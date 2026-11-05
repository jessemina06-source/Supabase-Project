### Supabase API Keys

Our project uses two Supabase API keys:

1. **Publishable Key** – This key allows our application to connect to Supabase. It can be used in browser or frontend code because it has limited privileges. Row Level Security (RLS) helps control which data users can access.

2. **Secret Key** – This key provides privileged access to our Supabase project and can bypass RLS. It must only be used on the server side. We must never expose it in browser code or upload it to a public GitHub repository.

**Security:** We do not include actual API key values or database passwords in this README. Secret keys must be stored securely using environment variables.

## Local PostgreSQL vs. Supabase Cloud

| Comparison | Local PostgreSQL | Supabase Project |
|---|---|---|
| Where it runs | Runs on our own computer. | Runs on Supabase cloud servers. |
| Backups | We are responsible for creating and managing backups. | Supabase provides backup features, depending on our plan and settings. |
| How we connect | We connect using local tools such as pgAdmin 4 and localhost. | We connect using the project's connection details through pgAdmin 4 or the Supabase Dashboard. |
| Who can access the tables | Users and applications with access to our local database can reach the tables. | Access depends on database permissions and Row Level Security (RLS) policies. |
| Maintenance | We manage PostgreSQL installation, updates, and server maintenance. | Supabase manages the cloud infrastructure, while we manage our database design and settings. |

**DBA Responsibility:** Even when using Supabase, we still need to manage database security, check access permissions, maintain the database structure, monitor performance, and make sure our data is properly protected.
