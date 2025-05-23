# Blank Plugin Boilerplate

Minimalistisches Starter-Plugin für Gutenberg-Blockentwicklung mit WordPress.  
Ziel: **klare Trennung von Build-Assets, PHP-Logik und Block-Konfiguration** – ohne Ballast, aber vollständig funktionsfähig.

<pre>
ud-plugin-blank/

├── package.json              → Herzstück der JavaScript-Toolchain, steuert den gesamten Build-Prozess
├── webpack.config.js         → Erweiterte Build-Konfiguration. <strong>`package.json` muss auf diese Entrypunkte verweisen</strong>.
├── block.json                → Block-Metadaten: Name, Scripts, Styles, Attribute
├── ud-plugin-blank.php       → Haupt-Plugin-Datei – lädt alle includes/*

├── includes/                 
│   ├── api.php            → Generiert die Blockausgabe dynamisch mit PHP – z. B. durch Abfragen mit WP_Query
│   ├── block-register.php             → Block-Registrierung
│   ├── enqueue.php           → Nur nötig, wenn keine block.json vorhanden ist – oder für zusätzliches JS (z. B. Isotope).
│   ├── helpers.php           → Gemeinsame Hilfsfunktionen für Block-Logik, z. B. Kontextprüfung oder Teaser-Erkennung
│   ├── render.php            → Generiert die Blockausgabe dynamisch mit PHP – z. B. durch Abfragen mit WP_Query

├── src/
│   ├── css/
│   │   ├── editor.scss       → Gutenberg-spezifisches Styling
│   │   ├── frontend.scss     → Styles für das Frontend
│   ├── js/
│   │   ├── editor.js         → Einstiegspunkt für den Editor; lädt Block-Konfiguration und Logik
│   │   ├── edit.js           → Definiert die Editor-Oberfläche inkl. Inspector Controls
│   │   ├── save.js           → Liefert das HTML-Markup, das im Beitrag gespeichert wird
│   │   ├── frontend.js       → JS fürs Frontend (z. B. Breakpoints, DOM)
│   │   ├── libs/             → Externe Bibliotheken (z. B. Isotope – ungebundelt)
│   │   ├── utils/            → Eigene JS-Helferfunktionen

├── assets/                   → Statische Dateien (Fonts, Bilder, Icons – nicht gebundelt)
</pre>

## 🧱 Zusammenspiel: Build-System & Plugin-Core
<pre> 
+--------------------------------------------+
| package.json                               |
| Herzstück der JavaScript-Toolchain         |
| - steuert den gesamten Build-Prozess       |
+--------------------------------------------+
              |
              v
+--------------------------------------------+
| webpack.config.js                          |
| Herzstück des Asset-Buildings              |
| - definiert, wie Code & Styles zu          |
|   finalen Dateien verarbeitet werden       |
+--------------------------------------------+
              |
              v
+--------------------------------------------+
| block.json                                 |
| Block-Beschreibung für WordPress           |
| - definiert Name, Verhalten, Assets, etc.  |
| - Bindeglied zwischen JS, CSS und PHP      |
+--------------------------------------------+
              |
              v
+--------------------------------------------+
| ud-plugin-blank.php                        |
| Plugin-Initialisierung                     |
| Einstiegsdatei für WordPress               |
| - registriert Block via block.json         |
| - lädt PHP-Logik (render.php, enqueue.php) |
+--------------------------------------------+
</pre>

## 1. package.json
Muss in den scripts-Einträgen (build, start) explizit auf webpack.config.js verweisen:
<pre>
"scripts": {
  "build": "webpack --config webpack.config.js",
  "start": "webpack --watch --config webpack.config.js"
}
</pre>

## 2. webpack.config.js
<strong>Funktioniert nur, wenn Webpack (z. B. über `@wordpress/scripts`) in der `package.json` installiert ist.</strong>

* In `entry` stehen **auch `.scss`-Dateien** – das ist normal.
* Sie werden **trotzdem zu `.css` kompiliert**, nicht zu `.js`.


* Beispiel:

```js
  entry: {
    "editor-script": "src/js/editor.js",
    "editor-style": "src/css/editor.scss",
    "frontend-script": "src/js/frontend.js",
    "frontend-style": "src/css/frontend.scss"
  }
```

* Im Output entstehen:

  * `build/editor-script.js`
  * `build/editor-style.css`
  * `build/frontend-script.js`
  * `build/frontend-style.css`



## 3. block.json
Muss exakt dieselben Pfade zu JS/CSS referenzieren, die Webpack erzeugt (build/editor.js, etc.).

Aktuell:

```json
{
  "editorStyle": "file:./build/editor-style.css",
  "style": "file:./build/frontend-style.css",
  "editorScript": "file:./build/editor-script.js",
  "script": "file:./build/frontend-script.js"
}
```

## 4. ud-plugin-blank.php
Diese Datei ist der Einstiegspunkt des Plugins und wird von WordPress geladen.  
Sie selbst registriert keinen Block, sondern lädt alle Dateien im `includes/`-Ordner.





# 🚀 Schnellstart 

1. **Repository klonen oder herunterladen**

   ```bash
   git clone https://github.com/ulrich-digital/Blank-Plugin_Boilerplate.git
   cd dein-plugin
   ```

2. **Abhängigkeiten installieren**

   ```bash
   npm install
   ```

3. **Entwicklung starten (mit automatischem Build bei Änderungen)**

   ```bash
   npm start
   ```

4. **Produktionsbuild erstellen (wenn alles fertig ist)**

   ```bash
   npm run build
   ```