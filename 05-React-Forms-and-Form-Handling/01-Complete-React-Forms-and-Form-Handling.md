# Chapter 5 -- React Forms and Form Handling: Complete Concepts

> A complete learning and reference guide to building accessible,
> validated, maintainable forms in React, from native HTML forms to
> advanced form libraries.

## Table of Contents

-   [1. Introduction to Forms in
    React](#1-introduction-to-forms-in-react)
-   [2. HTML Forms Refresher](#2-html-forms-refresher)
-   [3. Controlled Components](#3-controlled-components)
-   [4. Uncontrolled Components](#4-uncontrolled-components)
-   [5. Controlled vs Uncontrolled
    Components](#5-controlled-vs-uncontrolled-components)
-   [6. Handling Text Inputs](#6-handling-text-inputs)
-   [7. Handling Different Input
    Types](#7-handling-different-input-types)
-   [8. Handling Textareas and Select
    Elements](#8-handling-textareas-and-select-elements)
-   [9. Handling Checkboxes and Radio
    Buttons](#9-handling-checkboxes-and-radio-buttons)
-   [10. Handling Multiple Inputs with One
    Handler](#10-handling-multiple-inputs-with-one-handler)
-   [11. Form Submission and Preventing Default
    Behavior](#11-form-submission-and-preventing-default-behavior)
-   [12. Form Validation](#12-form-validation)
-   [13. HTML5 Built-in Validation](#13-html5-built-in-validation)
-   [14. Custom Validation in React](#14-custom-validation-in-react)
-   [15. Validation Timing and Error
    Handling](#15-validation-timing-and-error-handling)
-   [16. Form State: Values, Errors, Touched, and
    Submission](#16-form-state-values-errors-touched-and-submission)
-   [17. Dynamic Forms and Field
    Arrays](#17-dynamic-forms-and-field-arrays)
-   [18. Conditional Form Fields](#18-conditional-form-fields)
-   [19. File Uploads](#19-file-uploads)
-   [20. Form Reset and Default
    Values](#20-form-reset-and-default-values)
-   [21. Form Accessibility](#21-form-accessibility)
-   [22. Form Libraries: Why and When](#22-form-libraries-why-and-when)
-   [23. React Hook Form](#23-react-hook-form)
-   [24. Formik](#24-formik)
-   [25. Schema-Based Validation with Yup and
    Zod](#25-schema-based-validation-with-yup-and-zod)
-   [26. React Form Actions and
    useActionState](#26-react-form-actions-and-useactionstate)
-   [27. Form Submission with APIs](#27-form-submission-with-apis)
-   [28. Security Considerations](#28-security-considerations)
-   [29. Performance Optimization](#29-performance-optimization)
-   [30. Common Form Patterns](#30-common-form-patterns)
-   [31. Common Mistakes](#31-common-mistakes)
-   [32. Best Practices](#32-best-practices)
-   [33. Practical Projects](#33-practical-projects)
-   [34. Recommended Learning Order](#34-recommended-learning-order)

------------------------------------------------------------------------

## 1. Introduction to Forms in React

Forms collect information from users and allow an application to process
it. Common examples include: - Login and registration forms. - Search
forms and filters. - Contact forms. - Checkout and payment forms. -
Profile-editing forms. - File-upload forms.

React forms combine native HTML form elements with React state, event
handling, validation, and submission logic.

### What makes React forms different?

In a traditional HTML form, the browser manages the input values and
submits the form using native browser behavior. In React, we often
connect input values to component state so React can control and respond
to changes.

A form workflow usually contains:

1.  Display input fields.
2.  Capture user input.
3.  Store or read field values.
4.  Validate the values.
5.  Show helpful validation messages.
6.  Submit valid data.
7.  Display loading, success, or error feedback.
8.  Reset or preserve the form as appropriate.

## 2. HTML Forms Refresher

A basic HTML form uses a `<form>` element, labels, inputs, and a submit
button.

``` html
<form>
  <label for="email">Email</label>
  <input id="email" name="email" type="email" required />

  <button type="submit">Submit</button>
</form>
```

Important attributes:

  -----------------------------------------------------------------------
  Attribute                           Purpose
  ----------------------------------- -----------------------------------
  `name`                              Identifies a field in native form
                                      submission and `FormData`

  `id`                                Identifies an element and connects
                                      it to a label

  `type`                              Defines the kind of input

  `value`                             Sets the current/default value
                                      depending on the element and usage

  `required`                          Marks a field as required

  `placeholder`                       Displays a hint inside an empty
                                      field

  `min`, `max`                        Sets numeric or date bounds where
                                      supported

  `minLength`, `maxLength`            Sets text-length constraints

  `pattern`                           Defines a regular-expression
                                      constraint for supported text
                                      inputs

  `autoComplete`                      Hints the browser about the
                                      expected value

  `disabled`                          Prevents interaction and excludes
                                      the field from native form
                                      submission

  `readOnly`                          Prevents editing while keeping the
                                      control focusable and submitted
                                      normally
  -----------------------------------------------------------------------

In JSX, use `htmlFor` instead of `for` on a `<label>`. Use camelCase for
many DOM properties such as `autoComplete` and `onChange`.

## 3. Controlled Components

A controlled input is an input whose current value is controlled by
React state. The input receives a `value` prop, and an event handler
updates that state.

### Example

``` jsx
import { useState } from "react";

function NameForm() {
  const [name, setName] = useState("");

  return (
    <form>
      <label htmlFor="name">Name</label>
      <input
        id="name"
        name="name"
        value={name}
        onChange={(event) => setName(event.target.value)}
      />
      <p>Entered name: {name}</p>
    </form>
  );
}
```

### How it works

1.  React state provides the input's value.
2.  The user types into the input.
3.  `onChange` runs.
4.  The handler reads `event.target.value`.
5.  The setter updates state.
6.  React renders the input with the latest value.

### Important rules

-   A controlled text input should receive a string value throughout its
    lifetime.
-   A controlled checkbox should use `checked`, not `value`, to
    represent whether it is selected.
-   Do not switch an input from uncontrolled to controlled during its
    lifetime.
-   Initialize text values with `""` rather than `undefined` or `null`
    when you intend to control them.

## 4. Uncontrolled Components

An uncontrolled input stores its current value in the DOM rather than in
React state. React can read the value when needed, commonly using a ref
or `FormData`.

### Using a ref

``` jsx
import { useRef } from "react";

function SearchForm() {
  const inputRef = useRef(null);

  function handleSubmit(event) {
    event.preventDefault();
    console.log(inputRef.current.value);
  }

  return (
    <form onSubmit={handleSubmit}>
      <input ref={inputRef} name="search" defaultValue="" />
      <button type="submit">Search</button>
    </form>
  );
}
```

### Using FormData

``` jsx
function ContactForm() {
  function handleSubmit(event) {
    event.preventDefault();

    const formData = new FormData(event.currentTarget);
    const values = Object.fromEntries(formData.entries());

    console.log(values);
  }

  return (
    <form onSubmit={handleSubmit}>
      <input name="name" />
      <input name="email" type="email" />
      <button type="submit">Send</button>
    </form>
  );
}
```

Use `defaultValue` or `defaultChecked` to specify an initial value for
an uncontrolled input. These props do not keep the input synchronized
with React state after the initial render.

## 5. Controlled vs Uncontrolled Components

  -----------------------------------------------------------------------
  Controlled                          Uncontrolled
  ----------------------------------- -----------------------------------
  Value is held in React state        Current value is held by the DOM

  Uses `value` or `checked`           Uses `defaultValue`,
                                      `defaultChecked`, refs, or
                                      `FormData`

  React receives each change          React reads the value when needed

  Easy to validate and conditionally  Can involve less state wiring for
  render from current values          simple forms

  Useful for interactive forms        Useful for simple forms or
                                      integration with DOM-based tools
  -----------------------------------------------------------------------

Neither approach is universally best. Choose based on how much the UI
needs to react to the field values.

## 6. Handling Text Inputs

Use `value` and `onChange` for a controlled text input.

``` jsx
const [email, setEmail] = useState("");

<input
  type="email"
  name="email"
  value={email}
  onChange={(event) => setEmail(event.target.value)}
/>
```

The `onChange` event in React fires as the input value changes. For text
fields, `event.target.value` is a string.

## 7. Handling Different Input Types

### Number

The DOM input's `value` is still a string. Convert it when you need a
number.

``` jsx
const [age, setAge] = useState("");

<input
  type="number"
  value={age}
  onChange={(event) => setAge(event.target.value)}
/>

const numericAge = age === "" ? "" : Number(age);
```

Be careful with empty strings and invalid numeric conversions.

### Password

``` jsx
const [password, setPassword] = useState("");

<input
  type="password"
  autoComplete="current-password"
  value={password}
  onChange={(event) => setPassword(event.target.value)}
/>
```

Use `new-password` for a new password field and `current-password` for
an existing password where appropriate.

### Email

``` jsx
<input type="email" name="email" required />
```

The browser can perform basic email-format constraint validation, but
the server must still validate submitted data.

### Date

``` jsx
const [date, setDate] = useState("");

<input
  type="date"
  value={date}
  onChange={(event) => setDate(event.target.value)}
/>
```

Date input values are commonly represented as `YYYY-MM-DD` strings.

## 8. Handling Textareas and Select Elements

### Textarea

In JSX, a controlled `<textarea>` uses `value` and `onChange`.

``` jsx
const [message, setMessage] = useState("");

<textarea
  name="message"
  value={message}
  onChange={(event) => setMessage(event.target.value)}
/>
```

### Select

``` jsx
const [country, setCountry] = useState("");

<select
  name="country"
  value={country}
  onChange={(event) => setCountry(event.target.value)}
>
  <option value="">Select a country</option>
  <option value="india">India</option>
  <option value="canada">Canada</option>
</select>
```

For a multiple select, the controlled `value` is an array of selected
option values.

``` jsx
const [skills, setSkills] = useState([]);

<select
  multiple
  value={skills}
  onChange={(event) => {
    const selected = Array.from(
      event.target.selectedOptions,
      (option) => option.value
    );
    setSkills(selected);
  }}
>
  <option value="react">React</option>
  <option value="node">Node.js</option>
  <option value="sql">SQL</option>
</select>
```

## 9. Handling Checkboxes and Radio Buttons

### Checkbox

Use `checked` to control whether a checkbox is selected.

``` jsx
const [accepted, setAccepted] = useState(false);

<label>
  <input
    type="checkbox"
    checked={accepted}
    onChange={(event) => setAccepted(event.target.checked)}
  />
  I accept the terms
</label>
```

`event.target.checked` is a boolean.

### Multiple checkboxes

``` jsx
const [selectedSkills, setSelectedSkills] = useState([]);

function handleSkillChange(event) {
  const { value, checked } = event.target;

  setSelectedSkills((previous) =>
    checked
      ? [...previous, value]
      : previous.filter((skill) => skill !== value)
  );
}
```

### Radio buttons

Radio buttons in the same group share a `name`. Their values identify
the selected option.

``` jsx
const [gender, setGender] = useState("");

<label>
  <input
    type="radio"
    name="gender"
    value="female"
    checked={gender === "female"}
    onChange={(event) => setGender(event.target.value)}
  />
  Female
</label>

<label>
  <input
    type="radio"
    name="gender"
    value="male"
    checked={gender === "male"}
    onChange={(event) => setGender(event.target.value)}
  />
  Male
</label>
```

## 10. Handling Multiple Inputs with One Handler

For forms with multiple fields, use a single state object and a reusable
change handler.

``` jsx
import { useState } from "react";

function RegisterForm() {
  const [form, setForm] = useState({
    name: "",
    email: "",
    password: ""
  });

  function handleChange(event) {
    const { name, value } = event.target;

    setForm((previous) => ({
      ...previous,
      [name]: value
    }));
  }

  return (
    <form>
      <input name="name" value={form.name} onChange={handleChange} />
      <input name="email" value={form.email} onChange={handleChange} />
      <input
        name="password"
        type="password"
        value={form.password}
        onChange={handleChange}
      />
    </form>
  );
}
```

The `name` attribute connects each input to the matching property in the
state object. The computed property `[name]` updates the correct field.

For checkboxes, use `checked` rather than `value` when updating boolean
fields:

``` jsx
const { name, type, value, checked } = event.target;
const nextValue = type === "checkbox" ? checked : value;

setForm((previous) => ({ ...previous, [name]: nextValue }));
```

## 11. Form Submission and Preventing Default Behavior

A browser form normally performs a navigation or request when submitted.
In a client-side React form, you often handle submission in JavaScript.

``` jsx
function LoginForm() {
  function handleSubmit(event) {
    event.preventDefault();

    const formData = new FormData(event.currentTarget);
    const values = Object.fromEntries(formData.entries());

    console.log(values);
  }

  return (
    <form onSubmit={handleSubmit}>
      <input name="email" type="email" required />
      <input name="password" type="password" required />
      <button type="submit">Login</button>
    </form>
  );
}
```

### Why use `preventDefault()`?

It prevents the browser's default form submission behavior, allowing the
React handler to process the values without a full-page navigation.

### Button types

-   `type="submit"` submits the form.
-   `type="button"` performs an ordinary button action without
    submitting.
-   `type="reset"` resets controls to their default values.

Always specify the button type when a button is inside a form,
especially for buttons that should not submit.

## 12. Form Validation

Validation checks whether user input meets the application's
requirements before processing it.

Common validation rules: - Required fields. - Valid email format. -
Minimum and maximum length. - Password complexity. - Matching password
and confirmation. - Numeric range. - Accepted terms. - Valid file type
and size.

### Client-side vs server-side validation

Client-side validation improves usability and provides quick feedback.
Server-side validation is essential because client-side checks can be
bypassed. The server must independently validate all untrusted input.

## 13. HTML5 Built-in Validation

HTML provides built-in constraint validation attributes.

``` jsx
<form>
  <input name="email" type="email" required />
  <input name="username" minLength={3} maxLength={20} required />
  <input name="age" type="number" min={18} max={100} />
  <button type="submit">Submit</button>
</form>
```

Useful attributes: - `required` - `type="email"` - `min`, `max` -
`minLength`, `maxLength` - `pattern` - `step`

The browser blocks a normal form submission when a constraint fails and
may show a native validation message. Use `noValidate` on the form if
you want to bypass automatic browser UI and implement custom validation
behavior.

The `checkValidity()` and `reportValidity()` methods can be used when
programmatic validation is needed.

## 14. Custom Validation in React

Custom validation is useful when requirements go beyond basic HTML
constraints.

``` jsx
function validate(values) {
  const errors = {};

  if (!values.name.trim()) {
    errors.name = "Name is required";
  }

  if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(values.email)) {
    errors.email = "Enter a valid email address";
  }

  if (values.password.length < 8) {
    errors.password = "Password must contain at least 8 characters";
  }

  if (values.password !== values.confirmPassword) {
    errors.confirmPassword = "Passwords do not match";
  }

  return errors;
}
```

Use the returned errors to display messages next to their fields and
prevent invalid submission.

A regular expression can check a basic email shape, but it cannot prove
that an address exists or can receive mail.

## 15. Validation Timing and Error Handling

Validation can happen at different times:

-   **On change:** validate as the user edits.
-   **On blur:** validate after the user leaves a field.
-   **On submit:** validate when the form is submitted.
-   **After submit:** update messages as the user corrects errors.

A common user-friendly approach is to avoid showing errors before the
user has interacted with a field, then validate on blur and submit. The
best timing depends on the form.

### Touched fields

A field is often considered touched after the user has focused and then
left it. Track touched state so errors can be displayed at an
appropriate time.

### Error summary

For long forms, consider showing a summary at the top that links to
invalid fields, in addition to inline messages.

## 16. Form State: Values, Errors, Touched, and Submission

A robust form often tracks several categories:

  Form state       Meaning
  ---------------- -------------------------------------------
  `values`         Current field values
  `errors`         Validation errors by field
  `touched`        Whether a field has been visited
  `isSubmitting`   Whether submission is in progress
  `isSubmitted`    Whether a submission has occurred
  `status`         General success or server response status

These can be managed manually with React state or by a form library.

### Example form state shape

``` jsx
const initialValues = {
  name: "",
  email: ""
};

const [values, setValues] = useState(initialValues);
const [errors, setErrors] = useState({});
const [touched, setTouched] = useState({});
const [isSubmitting, setIsSubmitting] = useState(false);
```

## 17. Dynamic Forms and Field Arrays

Dynamic forms allow users to add or remove repeated fields, such as
multiple phone numbers, education records, or addresses.

``` jsx
function PhoneNumbersForm() {
  const [phones, setPhones] = useState([""]);

  function updatePhone(index, value) {
    setPhones((previous) =>
      previous.map((phone, i) => i === index ? value : phone)
    );
  }

  function addPhone() {
    setPhones((previous) => [...previous, ""]);
  }

  function removePhone(index) {
    setPhones((previous) =>
      previous.filter((_, i) => i !== index)
    );
  }

  return (
    <div>
      {phones.map((phone, index) => (
        <div key={index}>
          <input
            value={phone}
            onChange={(event) => updatePhone(index, event.target.value)}
          />
          <button type="button" onClick={() => removePhone(index)}>
            Remove
          </button>
        </div>
      ))}
      <button type="button" onClick={addPhone}>Add phone</button>
    </div>
  );
}
```

For production field arrays, use stable unique IDs as React keys rather
than array indexes when items can be inserted, removed, or reordered.

## 18. Conditional Form Fields

Conditional fields appear based on another field's value.

``` jsx
function AccountForm() {
  const [accountType, setAccountType] = useState("personal");

  return (
    <form>
      <label>
        Account type
        <select
          value={accountType}
          onChange={(event) => setAccountType(event.target.value)}
        >
          <option value="personal">Personal</option>
          <option value="business">Business</option>
        </select>
      </label>

      {accountType === "business" && (
        <label>
          Company name
          <input name="companyName" required />
        </label>
      )}
    </form>
  );
}
```

Decide what should happen to a field's value when it becomes hidden:
preserve it, clear it, or omit it from submission. Make this behavior
explicit.

## 19. File Uploads

File inputs are special: their value is controlled by the browser for
security reasons. You generally read selected files from the input's
`files` property rather than setting a file path through React state.

``` jsx
function FileUpload() {
  const [file, setFile] = useState(null);

  function handleFileChange(event) {
    setFile(event.target.files?.[0] ?? null);
  }

  async function handleSubmit(event) {
    event.preventDefault();
    if (!file) return;

    const formData = new FormData();
    formData.append("document", file);

    await fetch("/api/upload", {
      method: "POST",
      body: formData
    });
  }

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="file"
        accept=".pdf,.png,.jpg,.jpeg"
        onChange={handleFileChange}
      />
      <button type="submit">Upload</button>
    </form>
  );
}
```

### Multiple files

Add the `multiple` attribute and read `event.target.files`, which is a
`FileList`.

### Important upload considerations

-   `accept` is a file-picker hint, not a security boundary.
-   Validate file size and type on the server.
-   Do not trust the filename or MIME type supplied by the client.
-   Use `FormData` for typical multipart uploads.
-   Do not manually set the multipart `Content-Type` header when using
    browser `fetch`; the browser must add the boundary.
-   For previews, create object URLs with `URL.createObjectURL(file)`
    and revoke them with `URL.revokeObjectURL(url)` when no longer
    needed.

## 20. Form Reset and Default Values

### Reset controlled state

``` jsx
const initialValues = { name: "", email: "" };
const [values, setValues] = useState(initialValues);

function handleReset() {
  setValues(initialValues);
}
```

### Native reset

A form's reset button restores controls to their default values. For
controlled inputs, React state remains the source of truth, so update
that state as part of the reset behavior.

``` jsx
<button type="button" onClick={() => setValues(initialValues)}>
  Clear form
</button>
```

For a native form reset event, use `onReset` if you need to coordinate
the reset with React state.

## 21. Form Accessibility

Accessible forms are easier for everyone to use, including people using
assistive technologies.

Best practices: - Give every input a programmatic label using
`<label htmlFor>` and a matching `id`. - Group related controls with
`<fieldset>` and `<legend>`. - Use clear instructions and error
messages. - Associate help or error text using `aria-describedby`. - Set
`aria-invalid="true"` when a field has an error. - Do not communicate
errors using color alone. - Ensure all controls can be reached and
operated by keyboard. - Use a visible focus indicator. - Use appropriate
input types and autocomplete attributes. - Move focus to an error
summary or the first invalid field when appropriate after failed
submission.

``` jsx
<label htmlFor="email">Email address</label>
<input
  id="email"
  name="email"
  type="email"
  aria-invalid={Boolean(error)}
  aria-describedby={error ? "email-error" : "email-help"}
/>
<p id="email-help">Use the email associated with your account.</p>
{error && <p id="email-error">{error}</p>}
```

## 22. Form Libraries: Why and When

Manual React state is suitable for small forms. Larger forms may need: -
Field registration and value tracking. - Validation and schema
integration. - Touched and dirty tracking. - Dynamic field arrays. -
Submission lifecycle handling. - Performance optimizations. - Consistent
error display.

Popular libraries include React Hook Form and Formik. Schema validation
libraries such as Zod and Yup can define reusable validation rules.

Choose a library based on project requirements, team familiarity,
ecosystem support, and form complexity.

## 23. React Hook Form

React Hook Form is a form library that provides form registration,
validation, submission handling, and state tracking. It commonly uses
uncontrolled inputs through refs to reduce unnecessary re-renders.

Install:

``` bash
npm install react-hook-form
```

### Basic example

``` jsx
import { useForm } from "react-hook-form";

function LoginForm() {
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting }
  } = useForm();

  async function onSubmit(data) {
    console.log(data);
  }

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <label htmlFor="email">Email</label>
      <input
        id="email"
        {...register("email", {
          required: "Email is required",
          pattern: {
            value: /^[^\s@]+@[^\s@]+\.[^\s@]+$/,
            message: "Enter a valid email"
          }
        })}
      />
      {errors.email && <p>{errors.email.message}</p>}

      <label htmlFor="password">Password</label>
      <input
        id="password"
        type="password"
        {...register("password", {
          required: "Password is required",
          minLength: {
            value: 8,
            message: "Password must be at least 8 characters"
          }
        })}
      />
      {errors.password && <p>{errors.password.message}</p>}

      <button disabled={isSubmitting}>
        {isSubmitting ? "Submitting..." : "Login"}
      </button>
    </form>
  );
}
```

### Important React Hook Form APIs

-   `useForm`: initializes and configures form handling.
-   `register`: connects an input to the form.
-   `handleSubmit`: validates and calls the submit callback.
-   `formState`: exposes errors, touched fields, dirty state, and
    submission status.
-   `watch`: observes field values.
-   `setValue`: programmatically changes a field value.
-   `getValues`: reads current values without subscribing a component to
    changes.
-   `reset`: resets values and form state.
-   `Controller`: integrates controlled third-party components.
-   `useFieldArray`: manages arrays of fields.

### Controller example

Use `Controller` when integrating a controlled component that does not
work directly with `register`.

``` jsx
import { Controller, useForm } from "react-hook-form";

function ControlledExample() {
  const { control, handleSubmit } = useForm({
    defaultValues: { role: "user" }
  });

  return (
    <form onSubmit={handleSubmit(console.log)}>
      <Controller
        name="role"
        control={control}
        render={({ field }) => (
          <select {...field}>
            <option value="user">User</option>
            <option value="admin">Admin</option>
          </select>
        )}
      />
      <button type="submit">Save</button>
    </form>
  );
}
```

## 24. Formik

Formik is a form library that helps manage form values, validation,
errors, touched fields, and submission.

Install:

``` bash
npm install formik
```

### Basic example

``` jsx
import { Formik, Form, Field, ErrorMessage } from "formik";

function SignupForm() {
  return (
    <Formik
      initialValues={{ email: "", password: "" }}
      validate={(values) => {
        const errors = {};

        if (!values.email) {
          errors.email = "Email is required";
        }

        if (values.password.length < 8) {
          errors.password = "Password must be at least 8 characters";
        }

        return errors;
      }}
      onSubmit={async (values, actions) => {
        try {
          console.log(values);
        } finally {
          actions.setSubmitting(false);
        }
      }}
    >
      {({ isSubmitting }) => (
        <Form>
          <label htmlFor="email">Email</label>
          <Field id="email" name="email" type="email" />
          <ErrorMessage name="email" component="p" />

          <label htmlFor="password">Password</label>
          <Field id="password" name="password" type="password" />
          <ErrorMessage name="password" component="p" />

          <button type="submit" disabled={isSubmitting}>
            Submit
          </button>
        </Form>
      )}
    </Formik>
  );
}
```

### Important Formik concepts

-   `initialValues`: initial form values.
-   `values`: current values.
-   `handleChange`: updates a field.
-   `handleSubmit`: handles form submission.
-   `errors`: validation messages.
-   `touched`: fields the user has interacted with.
-   `isSubmitting`: submission status.
-   `Field`: connects an input to Formik.
-   `FieldArray`: helps manage repeated fields.
-   `resetForm`: resets the form.

## 25. Schema-Based Validation with Yup and Zod

Schema validation defines validation rules separately from the UI.

### Yup

Yup is a JavaScript schema validation library often used with Formik and
React Hook Form.

``` jsx
import * as yup from "yup";

const schema = yup.object({
  name: yup.string().required("Name is required"),
  email: yup
    .string()
    .email("Enter a valid email")
    .required("Email is required"),
  password: yup
    .string()
    .min(8, "Minimum 8 characters")
    .required("Password is required")
});
```

### Zod

Zod is a TypeScript-friendly schema validation library that can validate
data at runtime and infer TypeScript types.

``` jsx
import { z } from "zod";

const signupSchema = z.object({
  name: z.string().min(1, "Name is required"),
  email: z.string().email("Enter a valid email"),
  password: z.string().min(8, "Password must be at least 8 characters")
});

const result = signupSchema.safeParse({
  name: "Asha",
  email: "asha@example.com",
  password: "password123"
});

if (result.success) {
  console.log(result.data);
} else {
  console.log(result.error.flatten());
}
```

### Yup vs Zod

  -----------------------------------------------------------------------
  Yup                                 Zod
  ----------------------------------- -----------------------------------
  Widely used in Formik projects      Strong TypeScript type inference

  Chainable schema API                TypeScript-oriented schema API

  Commonly integrated with form       Integrates with React Hook Form
  libraries                           through a resolver

  Good choice for existing Yup-based  Good choice for many
  codebases                           TypeScript-first projects
  -----------------------------------------------------------------------

A schema validates data on the client for user experience, but the
server must validate submitted data independently.

## 26. React Form Actions and useActionState

Modern React supports form Actions. A function can be supplied to a
form's `action` prop to handle submission. React's `useActionState` Hook
can manage the result of an Action and expose a pending flag.

This is especially useful in React applications using Actions and, where
supported, server-side form workflows. Exact server Action behavior
depends on the framework and runtime.

### Basic client-side example

``` jsx
import { useActionState } from "react";

async function submitName(previousState, formData) {
  const name = formData.get("name");

  if (!name || !String(name).trim()) {
    return { message: "Name is required" };
  }

  return { message: `Hello, ${name}!` };
}

function NameForm() {
  const [state, formAction, isPending] = useActionState(
    submitName,
    { message: "" }
  );

  return (
    <form action={formAction}>
      <input name="name" />
      <button disabled={isPending}>
        {isPending ? "Submitting..." : "Submit"}
      </button>
      <p>{state.message}</p>
    </form>
  );
}
```

### What does useActionState return?

It returns: 1. The current state returned by the Action. 2. A dispatch
function to use as the form Action. 3. A boolean indicating whether the
Action is pending.

The Action receives the previous state first, followed by the form data
or dispatched payload.

## 27. Form Submission with APIs

A common application flow is: 1. Read form values. 2. Validate them on
the client. 3. Set a loading state. 4. Send the request to the backend.
5. Handle success or failure. 6. Display feedback. 7. Reset or preserve
the form as appropriate.

``` jsx
import { useState } from "react";

function ContactForm() {
  const [status, setStatus] = useState("idle");
  const [error, setError] = useState("");

  async function handleSubmit(event) {
    event.preventDefault();

    const formData = new FormData(event.currentTarget);
    const payload = Object.fromEntries(formData.entries());

    setStatus("loading");
    setError("");

    try {
      const response = await fetch("/api/contact", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(payload)
      });

      if (!response.ok) {
        throw new Error("Unable to submit the form");
      }

      setStatus("success");
      event.currentTarget.reset();
    } catch (error) {
      setStatus("error");
      setError(error.message);
    }
  }

  return (
    <form onSubmit={handleSubmit}>
      <input name="name" required />
      <input name="email" type="email" required />
      <button disabled={status === "loading"}>
        {status === "loading" ? "Sending..." : "Send"}
      </button>

      {status === "success" && <p>Submitted successfully.</p>}
      {status === "error" && <p>{error}</p>}
    </form>
  );
}
```

For controlled inputs, reset the React state as well; resetting only the
DOM does not change the state that controls those inputs.

In production code, handle server validation errors, network failures,
duplicate submissions, and cancellation where appropriate. Do not assume
every response contains valid JSON.

## 28. Security Considerations

-   Never trust client-side validation alone.
-   Validate and sanitize untrusted data on the server according to the
    data's intended use.
-   Enforce authorization on the server; hiding a form field does not
    secure an operation.
-   Protect state-changing requests against CSRF when using cookie-based
    authentication, according to the application's architecture.
-   Avoid logging passwords, tokens, or sensitive form data.
-   Do not expose secrets in frontend code.
-   Validate uploaded files on the server; do not trust the browser's
    `accept` attribute or client-provided MIME type.
-   Use HTTPS for sensitive submissions.
-   Avoid rendering untrusted input as HTML.

## 29. Performance Optimization

-   Keep form state close to the fields that need it.
-   Avoid placing every keystroke into broad global state without a
    reason.
-   Use a form library when its registration and subscription model
    helps with large forms.
-   Avoid expensive validation on every keystroke if it harms
    responsiveness.
-   Debounce expensive remote checks such as username availability, and
    cancel or ignore stale responses.
-   Use stable keys for dynamic fields.
-   Avoid unnecessary controlled/uncontrolled conversions.
-   Profile before adding memoization.
-   Consider splitting very large forms into sections while preserving a
    clear overall submission model.

## 30. Common Form Patterns

### Search form

Use a controlled input or `FormData`; debounce remote search when
needed.

### Login form

Use email/username and password fields, appropriate autocomplete values,
validation, pending feedback, and safe server-side authentication.

### Multi-step form

Track the current step, preserve values between steps, validate the
current step before advancing, and validate the complete payload on the
server.

### Dependent dropdowns

When one selection controls another (for example, country and city),
update or clear the dependent value when its parent selection changes.
Handle async option loading and stale responses.

### Password confirmation

Compare password and confirmation values during validation. Do not treat
matching client-side values as proof of account security.

## 31. Common Mistakes

1.  **Forgetting `event.preventDefault()`:** The browser may navigate or
    reload during a client-handled submission.
2.  **Using `value` without `onChange`:** A controlled input becomes
    read-only unless it is intentionally read-only.
3.  **Using `value` instead of `checked` for checkboxes:** Checkbox
    selection is represented by `checked`.
4.  **Missing `name` attributes:** `FormData` and native form submission
    rely on field names.
5.  **Using array indexes as keys for reorderable fields:** This can
    cause field state to attach to the wrong item.
6.  **Switching between controlled and uncontrolled:** Keep the input's
    control mode consistent.
7.  **Only validating on the client:** The server must validate
    independently.
8.  **Not handling async errors:** Display meaningful failure feedback
    and allow recovery.
9.  **Submitting multiple times:** Disable or otherwise guard submission
    while a request is pending when appropriate.
10. **Resetting only the DOM for controlled fields:** Reset the React
    state that supplies their values.
11. **Trusting file input restrictions:** Validate file content and size
    on the server.
12. **Showing errors too early:** Use touched/submission state to
    provide useful feedback.

## 32. Best Practices

-   Use semantic HTML form elements.
-   Associate labels with controls.
-   Choose controlled or uncontrolled inputs intentionally.
-   Give every submitted field a meaningful `name`.
-   Use the correct input type and autocomplete hints.
-   Keep validation logic organized and reusable.
-   Show clear inline errors and submission feedback.
-   Handle loading, success, server validation, and network failure
    states.
-   Use stable IDs for dynamic field lists.
-   Make forms keyboard accessible.
-   Validate all untrusted data on the server.
-   Use React Hook Form or Formik when form complexity justifies a
    library.
-   Use Yup or Zod when reusable schema validation is useful.
-   Keep sensitive values out of logs and unnecessary persistent
    storage.

## 33. Practical Projects

### Project 1: Login Form

Requirements: - Email and password fields. - Required-field and format
validation. - Show/hide password. - Loading, success, and error
states. - Accessible labels and messages.

### Project 2: Registration Form

Requirements: - Name, email, phone, password, confirm password. - Terms
checkbox. - Custom validation. - Field-level error messages. - Prevent
duplicate submission.

### Project 3: Dynamic Profile Form

Requirements: - Add/remove education records. - Add multiple phone
numbers. - Conditional fields based on user type. - Edit and reset
functionality.

### Project 4: Multi-Step Application Form

Requirements: - Personal details, education, and review steps. -
Preserve values when moving between steps. - Validate each step. - Show
a final summary before submission.

### Project 5: File Upload Form

Requirements: - Select one or multiple files. - Display file names and
sizes. - Validate allowed types and file size. - Preview supported
images. - Upload using `FormData`. - Display progress or pending status
and server errors.

### Project 6: React Hook Form Registration

Requirements: - Use `useForm`, `register`, and `handleSubmit`. - Add
validation rules. - Display `errors`. - Use `Controller` for a
controlled custom input. - Reset the form after successful submission.

### Project 7: API-Connected Admin Form

Requirements: - Create and edit a record. - Load existing values for
editing. - Submit data to an API. - Display server-side validation
errors. - Disable submission while saving. - Refresh or update the
relevant list after success.

## 34. Recommended Learning Order

1.  HTML form elements and attributes.
2.  Controlled components.
3.  Uncontrolled components and refs.
4.  Text, number, password, date, textarea, select, checkbox, and radio
    inputs.
5.  Managing multiple fields with one state object.
6.  Form submission and `preventDefault`.
7.  HTML5 validation.
8.  Custom validation and error messages.
9.  Touched, dirty, and submission state.
10. Dynamic and conditional fields.
11. File uploads and `FormData`.
12. Reset behavior and default values.
13. Accessibility.
14. React Hook Form.
15. Formik.
16. Yup and Zod.
17. React form Actions and `useActionState`.
18. API submission, security, performance, and practical projects.

------------------------------------------------------------------------

**End of Chapter 5 -- React Forms and Form Handling.**
