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

5c7482f0-c035-4009-83f4-bce8ca7367e1 aronnidea@gmail.com
a82c0fad-96f7-4afb-b436-21cec637de3f cedric@gmail.com
e7746cd0-a000-4c48-b8f2-b0d0ccc13b93 jessemina06@gmail.com

## Test results: expected vs actual

### Read tests (rows seen: books / members / loans / reviews)

| Test | Person    | Expected      | Actual |
|------|-----------|---------------|--------|
| R1   | Visitor   | 3 / 0 / 0 / 2 |        |
| R2   | Member A  | 3 / 1 / 2 / 2 |        |
| R3   | Member B  | 3 / 1 / 2 / 2 |        |
| R4   | Librarian | 3 / 2 / 4 / 2 |        |

### Write tests

| Test | Person    | Action                                    | Expected                                                              | Actual |
|------|-----------|-------------------------------------------|-----------------------------------------------------------------------|--------|
| W1   | Librarian | Insert a book                             | Succeeds, 1 row returned                                              |        |
| W2   | Member A  | Insert a book                             | Error: violates row-level security policy for "books"                 |        |
| W3   | Member A  | Update Member B's row                     | 0 rows, no error (blocked silently by USING)                          |        |
| W4   | Member A  | Change own `user_id` to another login     | Error: violates row-level security policy for "members" (WITH CHECK)  |        |
| W5   | Member A  | Insert a review with Member B's member_id | Error: violates row-level security policy for "reviews"               |        |
| W6   | Member A  | Insert a review with own member_id        | Succeeds, 1 row returned                                              |        |
| W7   | Member A  | Delete Member B's review                  | 0 rows, no error                                                      |        |
| W8   | Member A  | Insert a loan                             | Error: violates row-level security policy for "loans"                 |        |

### Forged-claim tests

Token claims: `user_metadata.role = librarian`, `app_metadata.role = member`.

| Test | Action                | Expected                                              | Actual |
|------|-----------------------|-------------------------------------------------------|--------|
| F1   | Read all four tables  | 3 / 1 / 2 / 2 (same as Member A)                      |        |
| F2   | Insert a book         | Error: violates row-level security policy for "books" |        |
| F3   | Update Member B's loan | 0 rows                                               |        |

1. Why does running a query in the SQL Editor as postgres not prove that a policy works?

The postgres role owns the tables and has the BYPASSRLS attribute, so Postgres never evaluates any policy for it. A query that returns every row only shows that the data exists, not that a real user is restricted. In our tests, postgres sees 2 member rows, while Member A sees only 1, which shows that policies only run when we switch to anon or authenticated and set the JWT claims.

2. What would happen to our policies if a request used the secret key, and why is that dangerous in a browser?

The secret key maps to the service_role, which bypasses RLS, so every policy would be skipped and the request could read, change, or delete any row in any table. That includes actions our rules forbid, such as deleting members or editing loans. In a browser, anyone can open DevTools and copy the key from the page’s code or network requests, giving them full access to the whole database. The secret key belongs only on a trusted server, and only the publishable key should be used in client code.,


