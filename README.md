# sveltekit-turnstile

This package includes SvelteKit components for rendering forms and validating form submissions that include cloudflare turnstile tokens for enhanced security.

## Set Up

Two environment variables must be set, either in .env or by some other method.  You can obtain these keys by creating an account at cloudflare.com.  Add your domain to cloudflare (for free), and then add a turnstile site (also free).  Make note of the public and private keys.

.env

```bash
PUBLIC_TURNSTILE_SITE_KEY=""
SECRET_TURNSTILE_KEY=""

```

## Usage

Import the `Turnstile` component on every page that will have a form with a turnstile element.  Loading the `Turnstile Element` component inside the html form element to generate a token when the form is rendered.  When the form is submitted, the token will be added to the submitted form data.

Loading turnstile from Cloudflare via the `Turnstile` component separately from the `TurnstileElement` component allows us to have multiple forms protected by turnstile loaded at the same time.  The `Turnstile` component will not attempt to load the turnstile script from Cloudflare if it is already loaded by the client, which is useful for SPA-style navigation.

The validateToken method should only be used on the server.  Storing your secret key as an environment variable without the PUBLIC_ prefix will prevent the key from being accidentally exported to the client.  An error will be thrown.  One logical place to include a server-side request to validate a token would be inside a form action.  Alternatively, you could create an api endpoint and submit the token to the endpoint for validation via a custom use:enhance function.

client-side (+page.svelte)

```svelte
<script>
   import { Turnstile, TurnstileElement } from 'sveltekit-turnstile'
   import { PUBLIC_TURNSTILE_SITE_KEY } from '$env/static/public'
</script>

<Turnstile />

<form action="/" method="POST" name="form1" ... >
   <TurnstileElement siteKey={PUBLIC_TURNSTILE_SITE_KEY}/>
   <!-- other form elements -->
</form>

```

server-side (+page.server.js)

```svelte
import { validateToken } from 'sveltekit-turnstile'
import { SECRET_TURNSTILE_KEY } from '$env/static/private'

export const actions = {

   default: async ({request}) => {

      const data = await request.formData()
      const token = data.get('token')
      console.log(token)

      const success = await validateToken(token, SECRET_TURNSTILE_KEY)
      console.log(success)

      // if they pass the turnstile, we do other stuff with the form data here
      // if they do not pass the turnstile, we return with a failure

   }
}

```

Errors from within the components will be caused by a missing site key.  Any errors are caught within a try/catch block and displayed in the console.  Be sure to check the browser console if you have difficulty.

## Acknowledgment

This package initally borrowed from the first svelte component available for turnstile, which was written by @ghostdevv.