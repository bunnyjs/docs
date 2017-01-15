---
layout: example
id: examples_validation
title: Vanilla JavaScript Form Validation example
---

Bunny Validation with all sub-components is only **5.6kb gzipped** and it includes rich functionality support already like:
- Smooth scrolling to invalid element on form submit
- Ajax validation
- File validation
- Image validation

Without these components Validation Core itself is only **2kb**, which includes:
- Promises
- Form validation on submit and/or inline
- Manual section validation - useful for custom ajax forms with many steps where each step should be validated separately instead of whole form
- Manual input check
- Custom translations
- Custom validators
- Custom UI - by default Bootstrap 4 config is used.

Bunny Validation is an HTML5 native form validation library. It uses native attributes like `required`, `type="email"` and custom attributes like `data-ajax="/check-email/{value}"` or `maxfilesize="1"`.

Old `src/bunny.validate` is deprecated and will be removed in 1.0. Use newest version instead: `src/Validation`.

## Live demo

{% raw %}
<div class="card p-3">

    <form id="form1" method="POST">

        <div class="form-group">
            <label for="name">Name</label>
            <input type="text" name="name" id="name" class="form-control" placeholder="Name" required minlength="6" maxlength="18">
        </div>

        <div class="form-group">
            <label for="password">Password</label>
            <input type="password" name="password" id="password" class="form-control" placeholder="Password" required minlength="6" maxlength="18">
        </div>

        <div class="form-group">
            <label for="password_confirmation">Confirm Password</label>
            <input type="password" name="password_confirmation" id="password_confirmation" class="form-control" placeholder="Password again" required minlength="6" maxlength="18">
        </div>

        <div class="form-group">
            <label for="email">E-mail</label>
            <input type="email" name="email" id="email" class="form-control" placeholder="E-mail" required maxlength="18" data-ajax="/api/users/email-exists/?q={value}">
            <small>Try <i>test@test.com</i> for ajax validation</small>
        </div>

        <div class="form-group">
            <label for="select">Select</label>
            <select name="select" id="select" class="c-select form-control" required>
                <option value="" selected>Select option</option>
                <option value="1">One</option>
                <option value="2">Two</option>
                <option value="3">Three</option>
            </select>
        </div>

        <div class="form-group">
            <label for="photo">Photo</label>
            <input type="file" name="photo" accept="image/jpeg, image/png" id="photo" class="form-control" placeholder="Photo" required maxfilesize="1" mindimensions="500x500">
            <small><ul>
                <li>Try uploading non-images even if they have .png/.jpg extension</li>
                <li>Images < 500x500px will be invalid</li>
                <li>And try uploading images larger then 1MB</li>
            </ul></small>
        </div>

        <div class="form-group">
            <label for="about">About</label>
            <textarea name="about" id="about" class="form-control"></textarea>
        </div>

        <div class="form-group">
            <label class="c-input c-radio">
                <input name="agree" type="radio" value="1" required>
                <span class="c-indicator"></span>
                I agree with Terms of Service
            </label>
        </div>

        <input type="submit" class="btn btn-primary" value="Submit">

    </form>

</div>

<script src="https://bunnyjs.com/dist/ajax.min.js"></script>
<script src="https://bunnyjs.com/dist/file.min.js"></script>
<script src="https://bunnyjs.com/dist/image.min.js"></script>
<script src="https://bunnyjs.com/dist/element.min.js"></script>
<script src="https://bunnyjs.com/dist/validation.min.js"></script>

<script>
Validation.init(document.forms[0], true);
ValidationConfig.classError = 'form-control-feedback';
</script>

{% endraw %}


## 1. Including scripts

Because of modern web app development trends Bunny Validation for rich use-case support and UX uses by default:
- Bunny Ajax - to perform ajax calls, for example, to check if e-mail already is taken
- BunnyFile - to get uploaded file types by parsing file header (signature)
- BunnyImage - to work with uploaded images and get their dimensions
- BunnyElement - to make smooth scrolling for better UX

It is recommended to compile all the app scripts used on the page or a module into one file via `rollup.js` or anything else. All what you need to do is just to do one import:

```javascript
import { Validation } from 'bunnyjs/src/Validation';
```

Or dists are available. Download them and put before `</body>`.

```html
<script src="https://bunnyjs.com/dist/ajax.min.js"></script>
<script src="https://bunnyjs.com/dist/file.min.js"></script>
<script src="https://bunnyjs.com/dist/image.min.js"></script>
<script src="https://bunnyjs.com/dist/element.min.js"></script>
<script src="https://bunnyjs.com/dist/validation.min.js"></script>
```

Finally, just add 1 line of code:

```javascript
// init form validation
Validation.init(document.form[0]);

// or set second argument to true to add also inline validation - inputs will be instantly validated on 'change' event
Validation.init(document.form[0], true);
```

## 2. HTML requirements and configuration

Configuration stored in `ValidationConfig` object and injected into `ValidationUI.config`. `ValidationUI` is an object which creates error messages and searches for DOM elements related to input, like input container (input group, form group) or label. `ValidationUI` injected into `Validation.ui`.

Configuration properties (by default Bootstrap 4 classes used):

```javascript
export const ValidationConfig = {

    // div/node class name selector which contains one label, one input, one help text etc.
    classInputGroup: 'form-group',
    // class to be applied on input group node if it has invalid input
    classInputGroupError: 'has-danger',

    // label to pick textContent from to insert field name into error message
    classLabel: 'form-control-label',

    // error message tag name
    tagNameError: 'small',
    // error message class
    classError: 'text-help',

    // query selector to search inputs within input groups to validate
    selectorInput: '[name]'

};
```

**The most important things to note here:**
- Each input MUST have "name" attribute even if they are not needed in server side, but validation required.
- Each input MUST be within container - input group. In Bootstrap there is `form-group` class and in Foundation - `input-group`. Input group should have only one input. By default error message is appended at the end of input group.

To change form group class, for example, just append a new value to `ValidationConfig.classInputGroup = 'app-input-container'`;

To change the behaviour of how to create, where to insert error messages and other DOM operations extend functions in `ValidationUI`. For example, to change place where to insert error message this method can be changed:

```javascript
    /**
     * DOM algorithm - where to insert error node/message
     *
     * @param {HTMLElement} inputGroup
     * @param {HTMLElement} errorNode
     */
    insertErrorNode(inputGroup, errorNode) {
        inputGroup.appendChild(errorNode);
    },
```

Browse source code on [GitHub](https://github.com/Mevrael/bunny/blob/master/src/Validation.js#L319) to view all the methods.


## 3. Translations and AJAX error message

Each validator has own validation error message and all lang string are stored in `ValidationLang` object:

```javascript
/**
 * Bunny Form Validation default Translations (EN)
 *
 * object key = validator method name
 * may use additional parameters in rejected (invalid) Promise
 * each invalid input will receive {label} parameter anyway
 * ajax error message should be received from server via JSON response in "message" key
 */
export const ValidationLang = {

    required: "'{label}' ir required!",
    email: "'{label}' should be a valid e-mail address!",
    tel: "'{label}' is not a valid telephone number!",
    maxLength: "'{label}' length must be < '{maxLength}'",
    minLength: "'{label}' length must be > '{minLength}'",
    maxFileSize: "Max file size must be < {maxFileSize}MB, uploaded {fileSize}MB",
    image: "'{label}' should be an image (JPG or PNG)",
    minImageDimensions: "'{label}' must be > {minWidth}x{minHeight}, uploaded {width}x{height}",
    maxImageDimensions: "'{label}' must be < {maxWidth}x{maxHeight}, uploaded {width}x{height}",
    requiredFromList: "Select '{label}' from list",
    confirmation: "'{label}' is not equal to '{originalLabel}'",
    minOptions: "Please select at least {minOptionsCount} options"

};
```

`ValidatorLang` is injected into `Validation.lang` property.

The easiest way to change lang string on some page/controller/route is to assign a new value to `Validation.lang.required = 'new value'`.

For complete translations new lang object can be created, imported and injected via `Validation.lang = CustomValidationLang`.

For AJAX validations - server should return a response in JSON format. If response contains `message` root key and it is NOT empty then input will be invalid and message from response will be used. To translate AJAX validation server should handle it itself.


## 4. Validator list

{% raw %}
<div class="table">
{% endraw %}

| Validator key      | How to use                                                     | Additional params for error message        | Comment                                                                                                                                                  |
|--------------------|----------------------------------------------------------------|--------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| required           | add "required" attribute                                       |                                            | Works also with files, selects and checkboxes                                                                                                            |
| email              | type="email"                                                   |                                            |                                                                                                                                                          |
| tel                | type="tel"                                                     |                                            | Input must be a telephone number                                                                                                                         |
| maxLength          | maxlength="N"                                                  | {maxLength}                                | N - number of characters                                                                                                                                 |
| minLength          | minlength="N"                                                  | {minLength}                                | N - number of characters                                                                                                                                 |
| maxFileSize        | maxfilesize="N"                                                | {maxFileSize}, {fileSize}                  | N - number in MBs, can be float                                                                                                                          |
| image              | accept="image/jpeg, image/png"                                 | {signature}                                | If accept has "image" somewhere, file input is image                                                                                                     |
| minImageDimensions | mindimensionx="XxY"                                            | {width}, {height}, {minWidth}, {minHeight} | X - width, Y - height                                                                                                                                    |
| maxImageDimensions | maxdimensions="XxY"                                            | {width}, {height}, {maxWidth}, {maxHeight} | X - width, Y - height                                                                                                                                    |
| requiredFromList   | requiredfromlist="ID" OR there is input with ID = name + "_id" |                                            | ID - hidden input ID, this validator is useful for  Google Place and other autocompletes                                                                 |
| minOptions         | minoptions="N" on custom autocompletes, tag selectors, etc.    |                                            | N - number of min options to select from autocomplete, tag selector, etc. It looks for non-empty hidden inputs within same input container (input group) |
| confirmation       | ID = original input.name + "_confirmation"                     |                                            |                                                                                                                                                          |
| ajax               | data-ajax="URL"                                                |                                            | URL - ajax URL where {value} will be replaced with input.value, server should return JSON. If there is non-empty "message" key - input is invalid        |

{% raw %}
</div>
{% endraw %}

## 5. Custom validators

Each validator is a simple function which:
- accepts only one argument - input
- and SHOULD return a `Promise` object with both `resolve` and `reject`.

All validators are stored in `ValidatorValidators` object and injected into `Validator.validators`.

Each ValidatorValidators object property name = validator name = ValidatorLang property name.

The **order** of properties defined in ValidatorValidators is important. For example, first one `required` is defined. Each input is checked against each validator in defined order. When validator was rejected, input is marked as invalid and further validators on this input are not executed.

**Example with custom validator** which uses `BunnyDate` to check for valid date:

```javascript
Validation.lang.date = 'Invalid date';

Validation.validators.date = input => {
    return new Promise((valid, invalid) => {
        if (input.id === 'day') {
            const date = BunnyDate.create(
                document.getElementById('year').value,
                document.getElementById('month').value,
                input.value
            );
            if (date) {
                document.getElementById('birth_date').value = BunnyDate.toSqlDate(date);
                valid();
            } else {
                invalid();
            }
        } else {
            valid();
        }
    });
};
```

## 6. Manual section validation and input check

In modern web apps and complicated forms with sections, tabs and steps there is a need of validating not the whole form but only part of it.

`Validation.validateSection(node)` does this job. It returns Promise with resolve only which accepts only one argument - true if all inputs within this section are valid or array of invalid inputs otherwise.

When form validation is in progress - validateSection() should not be called during this time or Error will be thrown.

Because of that and from UX point of view all the buttons which should validate section - MUST be disabled and maybe some spinner added. Example below is taken from [init()](https://github.com/Mevrael/bunny/blob/master/src/Validation.js#L521) src:

```javascript
Validation.validateSection(node).then(result => {
    [].forEach.call(submitBtns, submitBtn => {
        submitBtn.disabled = false;
    });
    if (result === true) {
        form.submit();
    } else {
        // section invalid, result is array of invalid inputs
        Validation.focusInput(result[0]);
    }
})
```

`Validation.focusInput()` can be used to focus invalid input, to scroll to it and also put cursor to the end if it is text input.
