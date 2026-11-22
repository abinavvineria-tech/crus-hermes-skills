# Termux/Android Flask Workarounds

These workarounds apply when running a Flask application on Android/Termux (Python 3.14).

## Werkzeug debug reloader crashes

Symptom: `ImportError: dlopen failed: cannot locate symbol "_PyType_AllocNoTrack"` in `array.cpython-314-aarch64-linux-android.so` when Werkzeug imports `multiprocessing`.

Cause: The Werkzeug debug/reloader imports `multiprocessing`, which is incompatible with this Termux Python 3.14 build.

Fix: Run the dev server with `debug=False`. The application itself works correctly; only the auto-reloader crashes.

```
app.run(host="0.0.0.0", port=int(os.environ.get("PORT", 5000)), debug=False)
```

Verify with Flask's test client instead of the reloader, and confirm live routes with `curl`.

## `uv venv` creates a venv without pip

Symptom: `.venv/bin/pip` does not exist after `uv venv .venv`.

Fix: Install dependencies with `uv pip install --python .venv -r requirements.txt`.

## /tmp is read-only

Symptom: Writing to `/tmp` fails with "Read-only file system".

Fix: Use the Hermes scratch directory for temporary files:
`/data/data/com.termux/files/home/.hermes/profiles/<profile>/cache/scratch`

## GitHub API requires authentication

Symptom: POST/PATCH to the GitHub issues API returns `401 Requires authentication`.

Fix: Do not attempt to post issue comments or close issues via the API unless a `GITHUB_TOKEN` is available. Document the summary for manual completion by the repository owner instead.
