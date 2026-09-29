
## What it does

a `compose.yml` file is that it acts as a **declarative blueprint** for an entire multi-container application.

Instead of opening a terminal and manually running multiple `docker network create`, `docker volume create`, and `docker run` commands in a specific order with dozens of flags, the desired end state is in one file. Running `docker compose up` tells Docker to read that file and build everything automatically.
### Phase 1: The 3-Service Stack (SQLite)

The database is just a file (SQLite) stored alongside the image uploads.

- **The Services:**
    
    1. **api:** Runs the backend code.
        
    2. **migrate:** A temporary container that runs the database setup script (`alembic upgrade head`) and then exits.
        
    3. **ui:** Runs the web server (nginx) for the frontend.
        
- **The Start Order:**
    
    - `migrate` starts and finishes.
        
    - `api` waits for `migrate` to finish successfully (`condition: service_completed_successfully`), then starts.
        
    - `ui` waits for the `api` health check to pass (`condition: service_healthy`), then starts.
        
- **Port Mapping Variable:**
    
    - `${UI_PORT:-80}:8080` tells Docker: "Look for `UI_PORT` in the `.env` file. If it is missing, default to port 80."
        
- **Data Storage:**
    
    - It uses one volume (`data`). Both the SQLite database file and the uploaded media go here.

```yml
# Sets the project name. Docker prefixes all containers/networks/volumes with this name (e.g., fieldnotes-api-1).
name: fieldnotes

services:
  api:
    image: gitlab.au-computing.org:5050/<project path>/api:1.0.0
    # Ensures the image architecture matches the VM, avoiding Apple Silicon architecture conflicts.
    platform: linux/amd64
    env_file:
      - .env      # Injects environment variables (like UI_PORT) from a local file.
      - jwt.env   # Injects the secret key needed for user authentication.
    volumes:
      # Mounts the 'data' named volume to /app/data. Saves the SQLite db file and media uploads permanently.
      - data:/app/data
    networks:
      - frontend  # Connects to the UI.
      - backend   # Connects to the migration tool.
    depends_on:
      migrate:
        # Waits until the migrate container finishes running successfully and exits before starting the API.
        condition: service_completed_successfully
    healthcheck:
      # Constantly pings the /readyz endpoint. The API is not considered "ready" until it gets a 200 OK.
      test: ["CMD", "python", "-c", "import urllib.request; urllib.request.urlopen('http://127.0.0.1:8000/readyz', timeout=2)"]
      interval: 10s       # Runs the test every 10 seconds.
      timeout: 3s         # Fails if it takes longer than 3 seconds to answer.
      retries: 3          # Marks container unhealthy after 3 consecutive failed tries.
      start_period: 20s   # Gives the server 20 seconds to boot up before failures start counting.
    # Automatically restarts if it crashes, unless you manually issued a stop command.
    restart: unless-stopped

  migrate:
    # Uses the exact same image as the API, but serves a different purpose.
    image: gitlab.au-computing.org:5050/<project path>/api:1.0.0
    platform: linux/amd64
    env_file:
      - .env
      - jwt.env
    volumes:
      - data:/app/data
    networks:
      - backend
    # Runs the database setup/update script and then exits. It does not stay running.
    command: alembic upgrade head

  ui:
    image: gitlab.au-computing.org:5050/<project path>/ui:1.0.0
    platform: linux/amd64
    ports:
      # Maps a port on the laptop to port 8080 inside the container.
      # ${UI_PORT:-80} means: "Use UI_PORT from .env, but if it is missing, default to 80."
      - "${UI_PORT:-80}:8080"
    networks:
      # Only on the frontend network. It cannot see the backend network.
      - frontend 
    depends_on:
      api:
        # Waits until the API's healthcheck passes (green light) before turning on the web server.
        condition: service_healthy
    restart: unless-stopped

# Declares the named volume so Docker knows to create it on the host machine.
volumes:
  data:

# Declares the isolated networks.
networks:
  frontend:
  backend:
```
        

### Phase 2: The 4-Service Stack (PostgreSQL)

[compose.3-postgres.yml](https://gitlab.au-computing.org/andrews-university/courses/fall2026-cptr320/topics/topic-06-composing-the-stack-services-networks-and-the-database-tier/-/blob/main/compose/compose.3-postgres.yml?ref_type=heads)

This upgrades the stack to use a dedicated database server, improving performance and security.

- **The New Service (db):**
    
    - Pulls the official PostgreSQL image.
        
    - Reads passwords from the `.env` file. The syntax `${POSTGRES_PASSWORD:?set...}` is a safety check that crashes the startup and prints an error if you forgot to set the password.
        
- **The Updated Start Order:**
    
    - **db** starts.
        
    - **migrate** waits for the `db` health check to pass (`pg_isready`).
        
    - **api** waits for `migrate` to finish.
        
    - **ui** waits for `api` to be healthy.
        
- **Upgraded Storage (Two Volumes):**
    
    - `data`: Now strictly used by the API to store uploaded media.
        
    - `pgdata`: A new volume created solely for PostgreSQL to store database records permanently.
        
- **The Bind Mount:**
    
    - The `db` service maps `./db/init:/docker-entrypoint-initdb.d:ro`. This passes the local setup scripts into the database container as read-only (`ro`), so PostgreSQL creates the restricted `fieldnotes` user role upon booting.

```yml
name: fieldnotes

services:
  api:
    image: gitlab.au-computing.org:5050/<project path>/api:1.0.0
    platform: linux/amd64
    env_file:
      - .env
      - jwt.env
    volumes:
      # Still used, but now ONLY for media uploads. The database moved to pgdata.
      - data:/app/data 
    networks:
      - frontend
      - backend
    depends_on:
      migrate:
        condition: service_completed_successfully
    healthcheck:
      test: ["CMD", "python", "-c", "import urllib.request; urllib.request.urlopen('http://127.0.0.1:8000/readyz', timeout=2)"]
      interval: 10s
      timeout: 3s
      retries: 3
      start_period: 20s
    restart: unless-stopped

  migrate:
    image: gitlab.au-computing.org:5050/<project path>/api:1.0.0
    platform: linux/amd64
    env_file:
      - .env
      - jwt.env
    volumes:
      - data:/app/data
    networks:
      - backend
    depends_on:
      db:
        # NEW: Migrate must wait for the PostgreSQL database to be fully ready before trying to update tables.
        condition: service_healthy
    command: alembic upgrade head

  ui:
    image: gitlab.au-computing.org:5050/<project path>/ui:1.0.0
    platform: linux/amd64
    ports:
      - "${UI_PORT:-80}:8080"
    networks:
      - frontend
    depends_on:
      api:
        condition: service_healthy
    restart: unless-stopped

  db:
    # NEW SERVICE: Dedicated PostgreSQL database server.
    image: postgres:17
    environment:
      # The :? syntax is a safety check. If these variables are missing from .env,
      # Docker refuses to start and prints the warning message after the question mark.
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD:?set POSTGRES_PASSWORD in .env}
      FIELDNOTES_DB_PASSWORD: ${FIELDNOTES_DB_PASSWORD:?set FIELDNOTES_DB_PASSWORD in .env}
    volumes:
      # Named Volume: Permanently stores the database records.
      - pgdata:/var/lib/postgresql/data
      # Bind Mount: Mounts the local db/init folder as read-only (ro) into the container.
      # The postgres image automatically runs scripts in this folder when creating a brand new database.
      - ./db/init:/docker-entrypoint-initdb.d:ro
    networks:
      # Isolated. The UI cannot see or connect to this container because it is not on the frontend network.
      - backend 
    healthcheck:
      # CMD-SHELL runs a native postgres tool over TCP to verify the database is actively accepting connections.
      test: ["CMD-SHELL", "pg_isready -h localhost -U fieldnotes -d fieldnotes"]
      interval: 5s
      timeout: 3s
      retries: 10
    restart: unless-stopped

volumes:
  data:
  pgdata: # NEW: Declares the new named volume for PostgreSQL data.

networks:
  frontend:
  backend:
```