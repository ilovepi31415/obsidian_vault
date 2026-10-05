[Topic 6 Link](https://gitlab.au-computing.org/andrews-university/courses/fall2026-cptr320/topics/topic-06-composing-the-stack-services-networks-and-the-database-tier/-/tree/main?ref_type=heads)

## Docker & Database Stack Cheat Sheet

### Key Vocabulary

- **Writable Layer:** The temporary "scratchpad" on top of a Docker image where a container writes new files. **If you delete the container, you delete this layer.**
    
- **Volume (Named Volume):** A permanent storage space managed by Docker that lives _outside_ the container. Safe from container deletion. Used for database files and media uploads.
    
- **Bind Mount:** A specific folder on your own laptop/computer that you link directly into a container. Great for passing in configuration scripts.
     
- **Docker Compose:** A tool that lets you manage multiple containers, networks, and volumes at the same time using a single file (`compose.yml`).
    
- **Service:** Docker Compose's word for a container setup (e.g., your `api`, your `ui`, your `db`).
    
- **Health Check:** A test Docker runs continuously to see if a container is actually ready to do its job (e.g., checking if the database is accepting connections, not just that it turned on).
    
- **Role (PostgreSQL):** A user account inside a database.
    
- **Least Privilege:** A security rule: give a service exactly the permissions it needs to do its job and _nothing more_.
    
- **Logical Backup:** A backup file made of pure text (SQL commands) that contains the exact instructions to rebuild your database and its data from scratch.
    
- **Migration:** A numbered script (like a version update) that changes the structure of your database (e.g., adding a new table).
    

###  Core Concepts

#### 1. Where Does Data Go?

- **The Problem:** If your database saves data inside the container's _Writable Layer_, typing `docker rm` will permanently delete your database.
    
- **The Solution:** You must map a [[#Key Vocabulary|volume]] to the container. When the database writes data, it saves to the volume. You can delete the container, start a brand new one, connect it to the same volume, and all your data will still be there.
    

#### 2. Orchestrating with Docker Compose

- Instead of typing out long `docker run` commands in a specific order, you define your entire "stack" (UI, API, Database, Networks, Volumes) in a single `compose.yml` [[Compose.yaml|file]].
    
- **Start Order:** You can't start a UI if the API is down, and you can't start the API if the Database is down. Compose uses `depends_on` and **Health Checks** to wait for one service to be fully ready before starting the next one in the chain.
    

#### 3. Network Security & Isolation

Docker creates isolated networks for your containers. You can use this for security:

- **Frontend Network:** The UI lives here. It talks to the outside world.
    
- **Backend Network:** The Database lives here.
    
- **The API** lives on _both_ networks so it can act as a bridge.
    
- **Why?** The UI literally cannot see or communicate with the Database. If a hacker takes over the UI, they can't directly access your database.
    

#### 4. Verified Backups

- Taking a backup (a "dump") isn't enough. A backup is only **verified** if you can do two things:

    1. Restore it into a _completely empty_ database.
        
    2. Check the row counts of your tables afterward to ensure they perfectly match the original database.
        

### Essential Commands Reference

#### Managing Containers (The old way)

- `docker stop <container>`: Stops the process. **Data is saved** because the writable layer is kept.
    
- `docker rm -f <container>`: Deletes the container. **Data is lost** (unless it was saved to a volume).
    
- `docker diff <container>`: Shows you every file that has been added or changed in the container's temporary writable layer.
    

#### Managing the Stack (The Docker Compose way)

- `docker compose up -d`: Reads your `compose.yml` file and starts your entire stack in the background (`-d` means detached).
    
- `docker compose ps`: Shows you the status of all running services in your stack, including whether they are "healthy" or not.
    
- `docker compose logs <service>`: Shows you the logs for a specific service (e.g., `docker compose logs db`).
    
- `docker compose exec <service> <command>`: Lets you run a command _inside_ a running container (e.g., to seed data or run database queries).
    
- `docker compose down`: Stops and removes all containers and networks. **Volumes (your data) are kept safe.**
    
- `docker compose down -v`: 🚨 **DANGER:** Removes containers, networks, AND volumes. This will permanently delete your database data!
    

#### Database Commands

- `pg_dump`: The PostgreSQL command used to create a logical backup (a text file of SQL commands).
    
- `psql`: The command-line tool used to interact with PostgreSQL databases (like listing tables with `\dt` or roles with `\du`).