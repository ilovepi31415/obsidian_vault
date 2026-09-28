---
tags:
  - HTML
---
A form is an [[HTML]] tag that allows for regular input from the user and handles validation and other like things

## Getting Information
### \<Input>
There are a variety of ways to get input from the user. Many can be formatted as `<input type=''>`
- `text`: A standard text input box
- `checkbox`: Like radio, but one or more can be chosen
- `email`
- `number`: Allows incrementing and decrementing
- `password`: All inputs show up as '\*'
- `radio`: Only one with a given `name` can be selected at a time

Additional properties of `<input>` elements include:
- `minlength`/`maxlength`: sets a minimum / maximum length for text inputs
- `placeholder`: provides hint text for what type of input should be used
- `required`: requires an input before form submission
- `value`: sets a default value for the input, that will be updated if the user inputs something
### Other input options
- `<textarea>`: A larger version of `<input type='text'>`, with adjustable dimensions

### Labels
To create a label, use the `<label>` element. The text between the opening and closing tags is displayed
```HTML
<form action="example.com/path" method="post">
  <label for="first_name">First Name:</label>
  <input type="text" id="first_name">
</form>
```
the `for` attribute in the label needs to match the `id` of the input it labels

