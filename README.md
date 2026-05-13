# /server-info

```text
Server Version:  GitHub.com (Linux)
Server MPM:      event
Server Built:    When you weren't looking
Current Time:    Right now, your local time
Restart Time:    Never, by design

Notice:
  This endpoint would normally hand a stranger your entire
  server configuration. By design, on this domain, it does not.
```

---

## What this account is

If you typed `github.com/server-info` hoping to find a juicy GitHub internals page — sorry, and also, you're welcome. GitHub does not expose a `mod_info` endpoint. It would be a very bad day for everyone if it did.

This account exists to give a friendly nudge to anyone running Apache on the open internet: **`mod_info` is a debugging tool, not a public endpoint.** Every repo here is a file or path that should never be reachable through a browser. Each one has a README walking through what the file is, how it leaks, and how to make sure the real version on your server returns a polite `403`.

## Loaded modules

| Module       | Repo                                                       | What it leaks                       |
|--------------|------------------------------------------------------------|-------------------------------------|
| `mod_access` | [`.htaccess`](https://github.com/server-info/.htaccess)    | Per-directory Apache configuration  |
| `mod_auth`   | [`.htpasswd`](https://github.com/server-info/.htpasswd)    | Usernames and password hashes       |

More to come. If there's a path on the internet that gets scanned a million times a day and shouldn't, it probably belongs here.

## Check your own server right now

From a machine that isn't your server:

```bash
curl -I https://your-domain.example/server-info
curl -I https://your-domain.example/server-status
```

A `403` or `404` is the right answer. Anything else is your weekend.

The fix, in case you need it, is one `<Location>` block:

```apache
<Location "/server-info">
    SetHandler server-info
    Require ip 127.0.0.1
    Require ip ::1
</Location>
```

Repeat for `/server-status`. Reload Apache. Re-run the curl. Sleep better.

## Why this exists

`/server-info` and `/server-status` sit in the top tier of internet-wide scanned endpoints. The configurations that leave them open are almost always inherited — a tutorial from 2014, a forgotten staging vhost, a `LoadModule info_module` line that nobody dared remove. The fix takes thirty seconds. The cost of not fixing it is a stranger reading your entire Apache configuration over a cup of tea, and then your `.htpasswd`, and then your database.

If this account got you to run `curl -I` against your own domain this afternoon, it did its job.

---

*A public-interest curiosity. Not affiliated with GitHub, the Apache Software Foundation, or any project referenced here. Issues and PRs welcome on individual repos.*
