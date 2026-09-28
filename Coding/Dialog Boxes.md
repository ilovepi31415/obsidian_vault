---
tags:
  - JS
---
The `<dialog>` [[HTML]] element allows you to create dialog boxes that pop up on screen. Optionally, [[JavaScript]] lets you prompt the user for information in a way more formally than the `prompt()` method.

## Using Dialogs
```HTML
<dialog open>
  <p>Greetings, one and all!</p>
  <form method="dialog">
    <button>OK</button>
  </form>
</dialog>
```
The `open` tag will cause the dialog to appear on page load, otherwise use the [[JavaScript]] `.show()` method.
### Modal vs Non-modal Dialog
A non-modal dialog box will not stop the user from interacting with the rest of the website. Any dialog without [[HTML]] is non-modal

Modal dialogs require the user to act before the rest of the webpage becomes available again.

To open a Modal dialog, use `.showModal()`. For non-modal, just use `show()`.

### Form Input
Often, a return value is wanted from a dialog. In order to do this, use a [[Forms|form]] and its input can be returned when the dialog is closed

```JS
document.getElementById("myForm").addEventListener("submit", function(event) {
    event.preventDefault(); // Prevent the form from refreshing the page
        
	// Get form data
	var name = document.getElementById("name").value;
	var email = document.getElementById("email").value;

	// Do something with the data (e.g., log it, send it to a server, etc.)
	console.log("Name:", name);
	console.log("Email:", email);

	// Close the dialog after submission
	dialog.close();

	// Optionally, reset the form
	document.getElementById("myForm").reset();
});
```

By default, the form will refresh the page when submitted. To keep this from happening, use `event.preventDefault()` 