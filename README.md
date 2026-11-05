### Supabase API Keys

Our project uses two Supabase API keys:

1. **Publishable Key** – This key allows our application to connect to Supabase. It can be used in browser or frontend code because it has limited privileges. Row Level Security (RLS) helps control which data users can access.

2. **Secret Key** – This key provides privileged access to our Supabase project and can bypass RLS. It must only be used on the server side. We must never expose it in browser code or upload it to a public GitHub repository.

**Security:** We do not include actual API key values or database passwords in this README. Secret keys must be stored securely using environment variables.
