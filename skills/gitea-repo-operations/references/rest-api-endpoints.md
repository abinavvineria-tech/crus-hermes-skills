# Gitea REST API endpoints used by this skill

Base URL: `http://localhost:3000/api/v1`
Auth header: `Authorization: token <tea-token>`

| Endpoint | Method | Purpose |
|---|---|---|
| `/user` | GET | Return the authenticated user (id, login, is_admin, email). Verify token works. |
| `/repos/{owner}/{repo}/issues` | GET | List issues. Query params: `state` (open/closed/all), `page`, `per_page`. |
| `/repos/{owner}/{repo}/pulls` | POST | Create a pull request. Body: title, head, base, body. |
| `/repos/{owner}/{repo}/pulls/{number}` | GET/PUT | Read or update a PR (state, title, body). |

Example: verify token:

```
curl -s -H "Authorization: token $TOKEN" http://localhost:3000/api/v1/user
```