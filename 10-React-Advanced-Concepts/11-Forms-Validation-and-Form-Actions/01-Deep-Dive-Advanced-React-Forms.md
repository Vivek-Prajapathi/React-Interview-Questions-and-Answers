# Advanced React Forms and Validation

## 1. Controlled and uncontrolled inputs

A controlled input gets its value from React state and updates through an event handler. An uncontrolled input keeps its current value in the DOM and can be read through a ref or `FormData`.

Controlled inputs are useful when the UI must react to every change, provide live validation, or coordinate fields. Uncontrolled forms can be simpler when values are only needed on submit.

## 2. Form submission

Use a semantic `<form>` and handle `onSubmit`. Call `preventDefault()` when the application handles submission without a full browser navigation.

```jsx
function LoginForm() {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");

  function handleSubmit(event) {
    event.preventDefault();
    // Validate and submit.
  }

  return (
    <form onSubmit={handleSubmit}>
      <label>
        Email
        <input
          type="email"
          value={email}
          onChange={event => setEmail(event.target.value)}
          required
        />
      </label>
      <label>
        Password
        <input
          type="password"
          value={password}
          onChange={event => setPassword(event.target.value)}
          required
        />
      </label>
      <button type="submit">Sign in</button>
    </form>
  );
}
```

## 3. Validation layers

- Browser validation: `required`, `type`, `minLength`, and other native constraints.
- Client validation: immediate feedback and user experience.
- Server validation: authoritative checks for security and business rules.

Never rely on client validation for security. The backend must validate submitted data independently.

## 4. Touched state and errors

A common form model tracks `values`, `errors`, and `touched`. Show an error after a field has been blurred or after submit, depending on the desired UX. Keep error messages associated with their inputs using `aria-describedby` and `aria-invalid`.

## 5. Async submission states

Track pending, success, and failure. Disable duplicate submission where appropriate and show clear feedback. Handle server field errors separately from general network errors.

## 6. React form Actions and useActionState

Modern React supports form Actions and `useActionState` for managing the result and pending state of an action. A form can use an action function, and `useActionState` returns state, a dispatch/action function, and pending status.

The exact server/client behavior depends on whether the application uses a framework and server functions. Do not assume a plain client-only Vite app has server actions automatically.

## 7. useFormStatus

`useFormStatus` reads the status of a parent form submission from a component rendered inside that form. It is useful for a submit button that displays a pending label or becomes disabled.

## 8. Common mistakes

- Missing `name` attributes when using `FormData` or a name-based change handler.
- Forgetting `event.preventDefault()` for client-side submit.
- Validating only on the client.
- Not disabling or guarding duplicate submissions.
- Showing errors without connecting them accessibly to fields.
- Mixing controlled and uncontrolled modes for the same input.
