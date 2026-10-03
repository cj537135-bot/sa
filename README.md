# SAHARA — Production Hosting Package

This package is the production-hosting version of the SAHARA V6 Flask application.

## Included

- Flask application
- Separate **GET HELP** and **GIVE HELP** workflows
- SQLite database configured for a persistent Render disk
- Gunicorn production server
- Render Blueprint (`render.yaml`)
- Production secret configuration
- HTTPS cookie setting
- Basic security response headers
- `/healthz` health endpoint

## Recommended hosting: Render

This configuration uses a persistent disk because the application currently uses SQLite. Render's normal filesystem is ephemeral, while a persistent disk preserves the SQLite database across deploys and restarts.

### Deploy

1. Create a GitHub repository, for example `sahara-support-platform`.
2. Upload every file in this folder to the repository root.
3. Push the repository to GitHub.
4. In Render, choose **New → Blueprint** and connect the GitHub repository.
5. Render reads `render.yaml`.
6. When prompted for `ADMIN_PASSWORD`, enter a strong password. Do not put it in the code or GitHub.
7. Deploy.
8. Open the generated `https://....onrender.com` URL.

### Admin login

Username: `admin`

Password: the value you entered for the `ADMIN_PASSWORD` environment variable.

Important: the admin password is created when the database is initialized. Changing the environment variable later does not automatically change an existing admin account password. Change the admin password through a dedicated password-reset feature before treating the site as a public production system if password rotation is required.

## Local test

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
export SECRET_KEY="replace-with-a-long-random-secret"
export ADMIN_PASSWORD="replace-with-a-strong-admin-password"
python app.py
```

Then open `http://127.0.0.1:5000`.

For production, use Gunicorn instead of Flask's development server:

```bash
gunicorn --workers 1 --threads 4 --timeout 120 --bind 0.0.0.0:10000 app:app
```

## Database note

The current V6 application uses SQLite. The Render Blueprint therefore attaches one persistent disk and uses `/var/data/sahara.db`.

A persistent disk is intentionally configured with one web-service instance (`workers=1`). Do not horizontally scale this SQLite deployment. If SAHARA later needs multiple application instances or much higher concurrent traffic, migrate the data layer to PostgreSQL first.

## Security notes

- Keep `SECRET_KEY` private.
- Keep `ADMIN_PASSWORD` private.
- Never commit `.env` or a production database to GitHub.
- Use HTTPS in production.
- Back up important application data before major changes.
- Review privacy, consent, data-retention, and safeguarding requirements before opening the platform to the public.
