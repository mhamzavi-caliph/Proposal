# Deploying the web app

There are two web front-ends in this package — use either:

- `web.py` — a Flask app (custom upload page → review data → download). Recommended for hosting.
- `app.py` — a Streamlit app (same pipeline, quickest to put online for free).

Both call the same `extract.py` + `generate.py` pipeline.

---

## 1. Run on your own machine (browser, no hosting)

This already *is* a web app — it just runs locally and opens in your browser.

```bash
pip install -r requirements.txt
python web.py
```

Open **http://127.0.0.1:5000** . Drop in a PDF, review the data, download the proposal.
Only you can reach it (it's on your computer). To let others on your office Wi-Fi use it,
share your machine's local IP, e.g. `http://192.168.1.20:5000`.

Streamlit version instead:

```bash
pip install streamlit pdfplumber python-docx
streamlit run app.py
```

---

## 2. Put it on a public URL (hosted)

Pick one. All are standard; none require changing the code.

### Option A — Render.com (Flask, free tier)
1. Push this folder to a GitHub repo.
2. On Render: **New → Web Service**, connect the repo.
3. Build command: `pip install -r requirements.txt`
   Start command: `gunicorn web:app`
4. Deploy. Render gives you a `https://yourapp.onrender.com` URL.
   (`Procfile` is already included, so Render/Railway/Heroku auto-detect the start command.)

### Option B — Railway.app (Flask)
1. Push to GitHub → **New Project → Deploy from repo**.
2. Railway reads the `Procfile` automatically. Done.

### Option C — Streamlit Community Cloud (Streamlit, free, easiest)
1. Push to GitHub.
2. Go to share.streamlit.io → **New app**, point it at `app.py`.
3. It installs `requirements.txt` and gives you a public URL. Zero server config.

### Option D — Docker (any host: Fly.io, a VPS, Cloud Run)
A `Dockerfile` is included.
```bash
docker build -t proposal-app .
docker run -p 8080:8080 proposal-app
# open http://localhost:8080
```
Push the image to any container host (Google Cloud Run, Fly.io, AWS, etc.).

### Option E — PythonAnywhere (Flask, beginner-friendly, free tier)
Upload the files, set the WSGI file to import `app` from `web.py`, reload. Good if you'd
rather click through a dashboard than use the command line.

---

## Before you expose it publicly — a checklist

This app handles client illustration data, so if it's reachable from the internet:

- **Add a login / access control.** None is built in. On a public host, anyone with the
  URL can use it. Options: HTTP basic auth, a shared password, or your host's access controls.
- **Use HTTPS.** Render, Railway, Streamlit Cloud and Cloud Run give you HTTPS automatically.
- **Data handling.** The app reads each upload in memory and deletes the temp file right
  after; it does not store PDFs or proposals. Confirm your host doesn't log request bodies.
- **PDPA / privacy.** Illustrations can contain client personal data. Keep access internal,
  and prefer a private/internal deployment over a fully public one.
- **The output is a summary, not advice or a contract** — keep the disclaimer intact.

For most agents the simplest safe setup is: **run it locally (section 1)** or deploy to a
host with a password in front of it.
