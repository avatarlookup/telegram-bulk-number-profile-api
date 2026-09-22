# Telegram Bulk Number Profile — `tg_profile_batch` | AvatarLookup

Bulk Telegram number profile: beyond the avatar it returns the username, user id, last-online time, active days and a set of appearance attributes the provider infers. Pick the country the numbers belong to when you upload.

This is the official AvatarLookup example repository for **one** bulk product, `tg_profile_batch`. It is an asynchronous task: upload a file, get a task id immediately, poll the task, and download the result file when it finishes.

- **Product page:** https://avatarlookup.com/products/tg_profile_batch
- **API documentation:** https://avatarlookup.com/api-docs
- **API base URL:** `https://avatarlookup.com`
- **Authentication:** `X-API-Key`
- **Get an API key:** https://avatarlookup.com/register

## What is it usually used for?

- List segmentation
- Contact data enrichment
- Audience review

## What does the result file contain?

| Column | Example | Meaning |
|---|---|---|
| `Phone` | `+17253100591` | The submitted number. |
| `activated` | `yes` | Whether the number is registered on Telegram. |
| `uid` | `123456789` | Telegram user id. |
| `username` | `alexkim` | Username, empty when the account has none. |
| `first_name` | `Alex` | Display first name. |
| `last_name` | `Kim` | Display last name. |
| `Last Online Time` | `2025/12/5 13:04` | The most recent online time observed. |
| `Active Days` | `252` | Days since that activity. |
| `Is Member` | `No` | Whether the account has Telegram Premium. |
| `Avatar` | `https://tg.waavatar.xyz/v/example.jpg` | Avatar URL. |
| `Gender` | `Male` | Gender estimated from the avatar. |
| `Age` | `30` | Age band estimated from the avatar. |
| `Ethnicity` | `White` | Ethnicity estimated from the avatar. |

The result is a **point-in-time signal**, not a verdict, and not identity data. It describes what the provider reported at the moment the task ran.

## How do I submit a task?

Upload a `.txt` or `.csv` with **one phone number per line**, 1,000–100,000 valid entries. A number list must come from a **single country**, given as `country` — the upstream uses it to expand bare national numbers.

```bash
curl -X POST 'https://avatarlookup.com/api/v1/bulk-tasks' \
  -H 'X-API-Key: YOUR_API_KEY' \
  -F 'product=tg_profile_batch' \
  -F 'country=US' \
  -F 'file=@numbers.txt'
```

The response returns the task id and `status=processing`, along with the server-side quote taken before processing starts.

## How do I poll a task and download the result?

```bash
curl -fsS 'https://avatarlookup.com/api/v1/bulk-tasks/TASK_ID' -H 'X-API-Key: YOUR_API_KEY'
```

`status` is `processing`, `success` or `failed` — there is no progress percentage to poll for. **Do not poll more often than once every 30 seconds.** When the task succeeds the response carries the result-file download link.

## Bulk task or realtime check?

`POST /api/v1/bulk-tasks` (this repository) takes a file of 1,000–100,000 entries and answers later — that is the shape for list cleaning, campaign preparation and enrichment runs. A **realtime** check (`POST /api/v1/check`, or `POST /api/v1/batch-check` for up to 100 identifiers) answers inside the same HTTP response, for a signup form or a live lookup. The two are separate endpoints and are not interchangeable; see the other repositories under [avatarlookup](https://github.com/avatarlookup).

## What about billing?

The whole file is reserved on submit. When the task finishes you are charged only for the entries that were actually checked and the remainder is returned. A failed task is refunded in full.

## What are the limits and error codes?

| Limit | Value |
|---|---|
| Entries per task | 1,000–100,000 |
| File formats | `.txt`, `.csv`, one entry per line |
| Products per task | 1 (`tg_profile_batch`) |
| Country | required, exactly one per task |
| Poll interval | no more than once per 30 seconds |

`40000`/`40001` are request or file errors; `40100` is an invalid key; `40200` is insufficient balance; `42900` is rate limited — honour `Retry-After`; `50300` is temporary maintenance. `GET /api/v1/balance` returns the current balance in `data.balance_micros`.

Keep the key on a trusted server and read it from `AVATARLOOKUP_API_KEY`. Never commit it or expose it in production browser code.

## Runnable examples in seven languages

| Language | Example |
|---|---|
| Python | [`examples/python`](examples/python) |
| Node.js | [`examples/nodejs`](examples/nodejs) |
| Go | [`examples/go`](examples/go) |
| Java | [`examples/java`](examples/java) |
| C# | [`examples/csharp`](examples/csharp) |
| PHP | [`examples/php`](examples/php) |
| Shell / curl | [`examples/shell`](examples/shell) |

## Official resources and responsible use

- **This product:** https://avatarlookup.com/products/tg_profile_batch
- **All products:** https://avatarlookup.com/products
- **Documentation:** https://avatarlookup.com/api-docs
- **Registration:** https://avatarlookup.com/register
- **Pricing:** https://avatarlookup.com/pricing
- **OpenAPI contract:** [`openapi.yaml`](openapi.yaml)
- **License:** [MIT](LICENSE) for the sample code

Submit only data you are authorized to process, and comply with applicable privacy laws and platform terms. Report the signal you received as a signal; do not relabel it downstream as a conclusion it does not support.

---

*Last reviewed: 2026-09-22 · Maintained by AvatarLookup. Canonical documentation: https://avatarlookup.com/api-docs*
