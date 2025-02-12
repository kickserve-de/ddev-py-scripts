# **Anleitung: Python-Webanwendung mit DDEV (Formularverarbeitung mit Flask)**
Diese Anleitung zeigt dir, wie du mit **DDEV und Flask** eine **Webanwendung mit einem HTML-Formular** erstellst, das vom **Python-Backend verarbeitet wird**.

---

## **1. Voraussetzungen**
Bevor du startest, installiere:
- **DDEV** 👉 [Installation](https://ddev.readthedocs.io/en/latest/users/install/)
- **Docker** 👉 [Download](https://www.docker.com/get-started/)
- **Python & Flask** Kenntnisse sind hilfreich, aber nicht zwingend.

---

## **2. Projekt mit DDEV einrichten**
1. **Neues Projektverzeichnis erstellen und betreten**:
   ```sh
   mkdir my-python-app && cd my-python-app
   ```

2. **DDEV-Projekt initialisieren**:
   ```sh
   ddev config --project-type=python --docroot=. --create-docroot
   ```

3. **DDEV-Projekt starten**:
   ```sh
   ddev start
   ```

---

## **3. Python-Backend mit Flask**
1. **Flask im DDEV-Container installieren**:
   ```sh
   ddev ssh
   pip install flask
   exit
   ```

2. **Flask-App erstellen (Backend)**  
   Erstelle die Datei `app.py`:
   ```python
   from flask import Flask, render_template, request

   app = Flask(__name__)

   @app.route("/", methods=["GET", "POST"])
   def index():
       name = None
       email = None

       if request.method == "POST":
           name = request.form.get("name")
           email = request.form.get("email")

           # Formulardaten verarbeiten (z.B. in Datei speichern)
           with open("data.txt", "a") as f:
               f.write(f"Name: {name}, Email: {email}\n")

       return render_template("index.html", name=name, email=email)

   if __name__ == "__main__":
       app.run(host="0.0.0.0", port=8080, debug=True)
   ```

---

## **4. HTML-Frontend mit Formular**
1. **Ordner für Templates anlegen**:
   ```sh
   mkdir templates
   ```

2. **Formular in `templates/index.html` erstellen**:
   ```html
   <!DOCTYPE html>
   <html lang="de">
   <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>Formular Beispiel</title>
       <link rel="stylesheet" href="{{ url_for('static', filename='styles.css') }}">
   </head>
   <body>
       <h1>Bitte Namen und E-Mail eingeben</h1>
       <form method="post">
           <label for="name">Name:</label>
           <input type="text" id="name" name="name" required>
           <br><br>
           <label for="email">E-Mail:</label>
           <input type="email" id="email" name="email" required>
           <br><br>
           <button type="submit">Absenden</button>
       </form>

       {% if name and email %}
           <h2>Empfangene Daten:</h2>
           <p><strong>Name:</strong> {{ name }}</p>
           <p><strong>E-Mail:</strong> {{ email }}</p>
       {% endif %}
   </body>
   </html>
   ```

---

## **5. CSS-Styles für das Formular**
Erstelle eine CSS-Datei `static/styles.css` mit folgenden Inhalten:

```css
/* Styles für das Formular */
body {
    font-family: Arial, sans-serif;
    background-color: #f4f4f4;
    text-align: center;
    margin: 50px;
}

h1 {
    color: #333;
}

form {
    background: white;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    display: inline-block;
}

label {
    font-weight: bold;
}

input {
    width: 100%;
    padding: 8px;
    margin: 10px 0;
    border: 1px solid #ccc;
    border-radius: 4px;
}

button {
    background-color: #007bff;
    color: white;
    padding: 10px 15px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
}

button:hover {
    background-color: #0056b3;
}

h2 {
    margin-top: 20px;
}
```

---

## **6. Flask-App in DDEV starten**
1. **DDEV-Container starten (falls nicht aktiv)**:
   ```sh
   ddev start
   ```

2. **Flask-App im DDEV-Container starten**:
   ```sh
   ddev ssh
   python app.py
   ```

3. **Webanwendung öffnen**:  
   👉 `http://127.0.0.1:8080`

---

## **7. Automatischer Start mit DDEV**
Damit Flask automatisch startet, erstelle die Datei `start.sh`:

```sh
#!/bin/bash
export FLASK_APP=app.py
flask run --host=0.0.0.0 --port=8080
```

Dann ausführbar machen:
```sh
chmod +x start.sh
```

In `.ddev/config.yaml` einfügen:
```yaml
hooks:
  post-start:
    - exec: "./start.sh"
```

---

## **8. Erweiterungen & Nächste Schritte**
✅ **Daten werden vom Python-Backend verarbeitet**  
✅ **Speicherung in Datei (`data.txt`) als Beispiel**  
✅ **Anzeige der Formulardaten nach Absenden im HTML-Template**  
✅ **Styling mit CSS für ein ansprechendes Design**  

### **Mögliche Erweiterungen**
- **Speicherung in SQLite oder PostgreSQL**
- **Daten validieren & Fehlerbehandlung**
- **Dynamische HTML-Ausgabe mit Jinja2**
