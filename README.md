#  Secure Password Manager (Flask + SQLite + Encryption)

A simple, beginner-friendly password manager web app built with Python (Flask).
It lets users register, log in, generate strong passwords, and securely store
website credentials with **encryption** in a local SQLite database.

---

##  Features

- User registration & login (passwords hashed + salted with bcrypt)
- Encrypted storage of website passwords (using the `cryptography` library / Fernet)
- Strong password generator (length + symbols/numbers/uppercase options)
- Add / View / Edit / Delete saved credentials
- Each user can only see their own credentials
- Protection against SQL Injection (SQLAlchemy ORM), XSS (Jinja2 auto-escaping),
  and CSRF (Flask-WTF CSRF tokens on every form)

---

##  Project Structure

```
password_manager/
│
├── app.py                  <- Main Flask application (all routes & logic)
├── requirements.txt        <- Python packages needed
├── README.md                <- This file
│
├── templates/               <- HTML pages
│   ├── base.html
│   ├── register.html
│   ├── login.html
│   ├── dashboard.html
│   ├── add_credential.html
│   ├── view_credential.html
│   ├── edit_credential.html
│   └── generate_password.html
│
└── static/                  <- (empty, reserved for CSS/JS/images if you add any)
```

When you run the app for the first time, it will automatically create:
- `passwords.db` — the SQLite database file
- `secret.key` — the encryption key used to encrypt/decrypt stored passwords

**Important:** Never delete `secret.key` once you start saving passwords —
without it, your stored credentials cannot be decrypted.

---

##  Setup Instructions (Windows + VS Code)

### Step 1 — Install Python
1. Download Python (3.10 or newer) from https://www.python.org/downloads/
2. During installation, **tick the box "Add Python to PATH"**.
3. Confirm installation by opening Command Prompt and typing:
   ```
   python --version
   ```

### Step 2 — Open the Project in VS Code
1. Open **VS Code**.
2. Go to `File > Open Folder` and select the `password_manager` folder.
3. Open a new terminal inside VS Code: `Terminal > New Terminal`
   (This opens a terminal already pointed at your project folder.)

### Step 3 — Create a Virtual Environment (recommended)
In the VS Code terminal, run:
```
python -m venv venv
```
Activate it (Windows):
```
venv\Scripts\activate
```
You should now see `(venv)` at the start of your terminal line.

> If you get a "running scripts is disabled" error, run this once in
> PowerShell (as Administrator), then try activating again:
> ```
> Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
> ```

### Step 4 — Install Required Packages
With the virtual environment activated, run:
```
pip install -r requirements.txt
```

### Step 5 — Run the Application
```
python app.py
```
You should see output like:
```
 * Running on http://127.0.0.1:5000
```

### Step 6 — Open the App in Your Browser
Go to:
```
http://127.0.0.1:5000
```

---

##  How to Use the App

1. **Register** a new account (username, email, password).
2. **Login** with your new account.
3. Go to **Generate Password** to create a strong random password (optional).
4. Go to **Add New** to save a website credential (website name, username,
   password). Paste your generated password here if you used one.
5. On the **Dashboard**, you can:
   - Click **View** to see the decrypted password.
   - Click **Edit** to update a credential.
   - Click **Delete** to remove a credential.
6. Click **Logout** when finished.

---

##  How the Security Requirements Are Met

| Requirement                     | How it's implemented |
|----------------------------------|------------------------|
| Password hashing & salting       | `flask-bcrypt` automatically salts and hashes login passwords before storing |
| Encrypted credential storage     | `cryptography.Fernet` encrypts website passwords before saving to the DB, decrypts only when the owner views them |
| SQL Injection protection         | All database queries use SQLAlchemy ORM (no raw SQL string building) |
| XSS protection                   | Flask's Jinja2 templates auto-escape all user input by default |
| CSRF protection                  | `Flask-WTF`'s `CSRFProtect` requires a valid CSRF token on every form submission |
| Access control                   | Every credential lookup checks `user_id` so users can only see their own data |
| Session security                 | Login state stored in Flask's signed session cookie; cleared on logout |

---

##  Quick Test Checklist (for your demo video/document)

1. Register a new account → should redirect to login.
2. Try registering with the same username again → should show an error.
3. Login with correct/incorrect credentials → test both cases.
4. Generate a password → confirm it appears on screen.
5. Add a credential → confirm it appears on the Dashboard.
6. View it → confirm the correct password is shown (decrypted).
7. Edit it → confirm changes are saved.
8. Delete it → confirm it disappears from the Dashboard.
9. Open `passwords.db` in a SQLite viewer (e.g. "DB Browser for SQLite") and
   show that the stored password is **encrypted gibberish**, not plain text.
10. Logout → confirm you're redirected to the login page and dashboard is no
    longer accessible without logging in again.

---

##  Notes

- This project uses **SQLite**, so there is no database server to install —
  the `passwords.db` file is created automatically in the project folder.
- `debug=True` in `app.py` is fine for development and demos, but should be
  turned off (`debug=False`) for any real production deployment.
- The `SECRET_KEY` in `app.py` should ideally be set as an environment
  variable in a real deployment, rather than left as a hardcoded default.
