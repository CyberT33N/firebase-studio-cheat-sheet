# firebase-studio-cheat-sheet

# Docs
- https://firebase.google.com/docs












<br><br>
________
________
<br><br>



# Projekte

## 🚀 GitHub-Projekt in Firebase Studio importieren
- https://firebase.google.com/docs/studio/get-started-import?hl=de&import_type=source

<details><summary>Click to expand..</summary>

### Schritte:

1. **Anmelden und Firebase Studio öffnen**  
   Melde dich mit deinem Google-Konto an und öffne [Firebase Studio](https://studio.firebase.google.com).

2. **Projekt importieren**  
   Klicke auf **„Projekt importieren“** – das Dialogfeld erscheint.

3. **Repo-URL eingeben**  
   Trage deine GitHub-Repository-URL in das Feld **„Repo-URL“** ein.

4. **Projektname festlegen**  
   Gib einen Namen für dein Projekt ein.

5. **Flutter-App auswählen (optional)**  
   - Wenn du ein Flutter-Projekt importierst, aktiviere: **„Dies ist eine Flutter-App“**  
   - Andernfalls: Haken weglassen.

6. **Import starten**  
   Klicke auf **„Importieren“**.

7. **Authentifizierung (falls erforderlich)**  
   Falls das Repository **privat** ist:
   - Du wirst aufgefordert, dich zu authentifizieren.
   - Für GitHub: Folge den Anweisungen und kopiere ein **Zugriffstoken**.

✅ That’s it.

</details>

















<br><br>
________
________
<br><br>

# dev.nix
Firebase Studio nutzt **Nix** zur Definition reproduzierbarer Entwicklungsumgebungen. Alle Anpassungen erfolgen über `.idx/dev.nix`.


<details><summary>Click to expand..</summary>


# 🛠 dev.nix – Aufbau

```nix
{ pkgs, ... }: {
  channel = "stable-23.11"; # oder "unstable"

  packages = [
    pkgs.nodejs_20
  ];

  env = {
    SOME_ENV_VAR = "hello";
  };

  idx.extensions = [
    "angular.ng-template"
  ];

  idx.previews = {
    enable = true;
    previews = {
      web = {
        command = [ "npm" "run" "start" "--" "--port" "$PORT" "--host" "0.0.0.0" "--disable-host-check" ];
        manager = "web";
        # cwd = "app/client"; # optional
      };
    };
  };
}
```

---

### 📦 Systemtools hinzufügen

- Verwende [search.nixos.org/packages](https://search.nixos.org/packages)
- Beispiel:
```nix
packages = [
  pkgs.nodejs_20
  pkgs.yarn
  pkgs.docker
];
```

---

### 🌍 Globale Umgebungsvariablen

```nix
env = {
  NODE_ENV = "development";
  API_URL = "http://localhost:3000";
};
```

---

### 🧩 IDE-Erweiterungen

Zwei Wege:
1. Manuell via UI (für persönliche Addons)
2. Automatisch in `dev.nix` (für projektspezifische Erweiterungen):
```nix
idx.extensions = [
  "angular.ng-template"
  "esbenp.prettier-vscode"
];
```
→ IDs via [open-vsx.org](https://open-vsx.org) finden.

---

### ⚙️ Dienste aktivieren

Firebase Studio unterstützt Services via `services.*`:

```nix
services.redis.enable = true;
services.mysql.enable = true;
services.pubsub.enable = true;
```

---

### ☁️ gcloud CLI + Komponenten

```nix
packages = [
  (pkgs.google-cloud-sdk.withExtraComponents [
    pkgs.google-cloud-sdk.components.cloud-datastore-emulator
  ])
];
```

---

### ⚡ Lokale Node-Binärdateien nutzen

- Terminal: `npx <tool>`
- Oder direkt, wenn `node_modules/.bin` im Pfad

---

### 🖼️ Arbeitsbereichs-Icon

- Lege `icon.png` in `.idx/` ab → wird im Dashboard angezeigt  
  Tipp: Unterschiedliche Icons für Branches (Dev/Prod)

---

### 🎯 Vorschau konfigurieren

- `idx.previews` steuert, wie Vorschauen gestartet werden  
- `$PORT` wird automatisch von Firebase Studio vergeben

---

### 📦 Projekt zippen

- Rechtsklick im Explorer → *Zip und herunterladen*  
- Oder Menü: Datei > Ordner öffnen → `/home/user` wählen → zippen

---

### 🧬 Reproduzierbarkeit

Dank Nix ist die dev.nix:
- **Deklarativ** – beschreibt exakt die Umgebung
- **Reproduzierbar** – jeder bekommt dieselbe Dev-Umgebung
- **Versionierbar** – via Git

---

### 🧰 Weitere Ressourcen

- 🔍 [Nix Packages Search](https://search.nixos.org/packages)
- 📚 [dev.nix Referenz](https://firebase.google.com/docs/studio/customize-workspace)
- 🧪 [Benutzerdefinierte Vorlagen](https://firebase.google.com/docs/studio/custom-templates)









<br><br>
<br><br>






# Firebase Studio (IDX) `dev.nix` Cheatsheet

Dies ist eine Übersicht über die Konfigurationsoptionen in der `dev.nix`-Datei, die von Firebase Studio zur Anpassung deiner Entwicklungsumgebung verwendet wird.

## Top-Level Attribute

Diese Attribute befinden sich direkt im Haupt-Attributsatz, der von der Nix-Funktion zurückgegeben wird.

### `channel`

*   **Zweck:** Definiert den Nixpkgs-Channel, der für die Paketverwaltung verwendet werden soll. Channels sind Sammlungen von Paketen in einem bestimmten Zustand (z.B. stabil oder aktuell).
*   **Type:** `String`
*   **Mögliche Werte:**
    *   `"stable-YY.MM"` (z.B. `"stable-24.05"`): Verwendet einen spezifischen stabilen Release-Zweig. Empfohlen für Konsistenz.
    *   `"unstable"`: Verwendet den neuesten Entwicklungszweig. Bietet die aktuellsten Pakete, kann aber weniger stabil sein.
*   **Beispiel:**
    ```nix
    channel = "stable-24.05";
    ```

### `packages`

*   **Zweck:** Liste der Softwarepakete, die in der Workspace-Umgebung installiert und verfügbar gemacht werden sollen. Du kannst Pakete über [Nix Package Search](https://search.nixos.org/packages) finden.
*   **Type:** `Liste von Nix-Paket-Derivationen` (typischerweise `pkgs.<paketname>`)
*   **Beispiel:**
    ```nix
    packages = [
      pkgs.nodejs_20 # Spezifische Node.js Version
      pkgs.zsh       # Z-Shell
      pkgs.starship  # Shell-Prompt-Anpassung
      pkgs.git
      pkgs.google-cloud-sdk
    ];
    ```

### `env`

*   **Zweck:** Definiert Umgebungsvariablen, die in der Workspace-Shell (Terminal) verfügbar sein sollen.
*   **Type:** `Attributsatz` (Schlüssel-Wert-Paare, wobei Schlüssel und Werte Strings sind)
*   **Beispiel:**
    ```nix
    env = {
      NODE_ENV = "development";
      API_KEY = "dein-super-geheimer-schluessel";
      # Kann auch auf andere Nix-Pakete verweisen
      GOOGLE_APPLICATION_CREDENTIALS = "${pkgs.google-cloud-sdk}/bin/gcloud";
    };
    ```

### `idx`

*   **Zweck:** Ein verschachtelter Attributsatz, der Konfigurationen speziell für die Firebase Studio (IDX) IDE-Funktionen enthält.
*   **Type:** `Attributsatz`

## `idx` Sub-Attribute

Diese Attribute befinden sich innerhalb des `idx`-Attributsatzes.

### `idx.extensions`

*   **Zweck:** Liste der VS Code-Erweiterungen, die automatisch im Workspace installiert werden sollen. Finde Erweiterungen und ihre IDs (Format: `publisher.extensionId`) auf dem [Open VSX Registry](https://open-vsx.org/).
*   **Type:** `Liste von Strings`
*   **Beispiel:**
    ```nix
    idx = {
      extensions = [
        "vscodevim.vim"           # Vim-Tastenbindungen
        "dbaeumer.vscode-eslint"  # ESLint Integration
        "esbenp.prettier-vscode"  # Prettier Code Formatter
      ];
      # ... andere idx Optionen
    };
    ```

### `idx.previews`

*   **Zweck:** Konfiguriert die Vorschau-Funktionalität in Firebase Studio, z.B. für Webserver oder andere Dienste.
*   **Type:** `Attributsatz`
*   **Sub-Attribute:**
    *   `enable`: (Boolean) Aktiviert oder deaktiviert die Vorschau-Funktion global. Standard ist oft `true`.
    *   `previews`: (Attributsatz) Definiert spezifische Vorschau-Konfigurationen. Der Schlüssel jedes Eintrags ist ein benutzerdefinierter Name für die Vorschau (z.B. `web`, `api`).

*   **Struktur einer einzelnen Vorschau (z.B. `idx.previews.previews.web`):**
    *   `command`: (Liste von Strings) Der Befehl, der ausgeführt werden soll, um den Vorschau-Server zu starten.
    *   `manager`: (String) Der Typ des Vorschau-Managers. `"web"` ist üblich für Web-Vorschauen im integrierten Browser-Panel.
    *   `env`: (Attributsatz) Spezifische Umgebungsvariablen für den Vorschau-Prozess. Die spezielle Variable `$PORT` wird von IDX bereitgestellt und sollte für den Server-Port verwendet werden.
    *   `rootDir`: (String, optional) Das Verzeichnis, von dem aus der `command` ausgeführt werden soll (relativ zum Workspace-Root).

*   **Beispiel:**
    ```nix
    idx = {
      previews = {
        enable = true;
        previews = {
          # Name der Vorschau: "web"
          web = {
            command = ["npm" "run" "dev"]; # Startet den Dev-Server via npm
            manager = "web";               # Nutzt die Web-Vorschau von IDX
            env = {
              PORT = "$PORT";              # Weist den von IDX verwalteten Port zu
              HOST = "0.0.0.0";            # Stellt sicher, dass der Server von außerhalb des Containers erreichbar ist
            };
          };
          # Beispiel für eine zweite Vorschau (z.B. API-Server)
          api = {
             command = ["npm" "run" "start:api" "--" "--port" "$PORT"]; # Startet API auf anderem Port
             manager = "web"; # Kann auch als Web-Vorschau angezeigt werden (zeigt die Root-URL)
             # Alternativ: manager = "process"; # Läuft nur als Hintergrundprozess ohne UI-Panel
             env = {
               PORT = "$PORT";
               DATABASE_URL = "deine-db-url";
             };
             rootDir = "services/api"; # Führt den Befehl im Unterverzeichnis aus
          };
        };
      };
      # ... andere idx Optionen
    };
    ```

### `idx.workspace`

*   **Zweck:** Definiert Shell-Befehle, die zu bestimmten Zeitpunkten im Lebenszyklus des Workspaces ausgeführt werden sollen.
*   **Type:** `Attributsatz`
*   **Sub-Attribute:**
    *   `onCreate`: (Attributsatz) Befehle, die *einmalig* ausgeführt werden, wenn der Workspace zum ersten Mal erstellt wird. Ideal für Setup-Aufgaben wie das Installieren von Abhängigkeiten. Jeder Schlüssel ist ein benutzerdefinierter Name für den Schritt, der Wert ist der auszuführende Shell-Befehl (String).
    *   `onStart`: (Attributsatz) Befehle, die *jedes Mal* ausgeführt werden, wenn der Workspace gestartet oder neu gestartet wird. Geeignet für das Starten von Hintergrundprozessen, Watchern etc. Struktur wie bei `onCreate`.

*   **Beispiel:**
    ```nix
    idx = {
      workspace = {
        # Wird nur beim ersten Erstellen ausgeführt
        onCreate = {
          install-deps = "npm install";
          init-db = "npm run db:migrate";
        };
        # Wird bei jedem Start/Neustart ausgeführt
        onStart = {
          start-dev-server = "npm run dev &"; # '&' startet im Hintergrund
          watch-files = "npm run watch &";
        };
      };
      # ... andere idx Optionen
    };
    ```

## Vollständiges Beispiel (`dev.nix`)

```nix
# Mehr Infos: https://firebase.google.com/docs/studio/customize-workspace
{ pkgs, ... }: {

  # 1. Nixpkgs Channel wählen
  channel = "stable-24.05";

  # 2. Systempakete installieren
  packages = [
    pkgs.zsh
    pkgs.starship
    pkgs.git
    pkgs.nodejs_20
    pkgs.google-cloud-sdk # Für Firebase/GCP CLI
  ];

  # 3. Umgebungsvariablen setzen
  env = {
    NODE_ENV = "development";
    # Beispiel: Pfad zur gcloud-Binary
    # PATH = pkgs.lib.makeBinPath [ pkgs.google-cloud-sdk ]; # Alternative Methode, um Binaries in PATH aufzunehmen
  };

  # 4. Firebase Studio (IDX) spezifische Konfigurationen
  idx = {

    # 4.1 VS Code Erweiterungen
    extensions = [
      "vscodevim.vim"
      "esbenp.prettier-vscode"
      "googlecloudtools.cloudcode" # Für GCP/Firebase Integration
    ];

    # 4.2 Vorschau-Konfigurationen
    previews = {
      enable = true;
      previews = {
        web = {
          command = ["npm" "run" "dev"];
          manager = "web";
          env = { PORT = "$PORT"; };
        };
      };
    };

    # 4.3 Workspace Lebenszyklus-Hooks
    workspace = {
      onCreate = {
        # Installiert Node.js Abhängigkeiten beim ersten Erstellen
        npm-install = "npm install";
      };
      onStart = {
        # Startet einen Watcher-Prozess im Hintergrund bei jedem Start
        # watch-backend = "npm run watch-backend &";
      };
    };
  };
}
```












</details>


















<br><br>
________
________
<br><br>

# Extensions

<details><summary>Click to expand..</summary>

# Marketplace
- https://open-vsx.org/


# 🧾Best Extensionms

| Name                  | Publisher ID                          | Link                                                                 |
|-----------------------|----------------------------------------|----------------------------------------------------------------------|
| Background            | `shalldie.background`                  | [🔗 Link](https://open-vsx.org/extension/shalldie/background)        |
| Docker                | `ms-azuretools.vscode-docker`          | [🔗 Link](https://open-vsx.org/extension/ms-azuretools/vscode-docker)|
| Dotenv                | `mikestead.dotenv`                     | [🔗 Link](https://open-vsx.org/extension/mikestead/dotenv)           |
| Error Lens           | `usernamehw.errorlens`                 | [🔗 Link](https://open-vsx.org/extension/usernamehw/errorlens)       |
| ESLint                | `dbaeumer.vscode-eslint`               | [🔗 Link](https://open-vsx.org/extension/dbaeumer/vscode-eslint)     |
| Fluent Icons          | `miguelsolorio.fluent-icons`           | [🔗 Link](https://open-vsx.org/extension/miguelsolorio/fluent-icons) |
| GitLens               | `eamodio.gitlens`                      | [🔗 Link](https://open-vsx.org/extension/eamodio/gitlens)            |
| Five Server           | `ritwickdey.LiveServer`                   | [🔗 Link](https://open-vsx.org/extension/ritwickdey/LiveServer         |
| Nuxt MDC              | `Nuxt.mdc`                             | [🔗 Link](https://open-vsx.org/extension/Nuxt/mdc)                   |
| PostCSS               | `csstools.postcss`                     | [🔗 Link](https://open-vsx.org/extension/csstools/postcss)           |
| PowerShell            | `ms-vscode.powershell`                 | [🔗 Link](https://open-vsx.org/extension/ms-vscode/powershell)       |
| Python                | `ms-python.python`                     | [🔗 Link](https://open-vsx.org/extension/ms-python/python)           |
| Symbols               | `castrogusttavo.symbols`               | [🔗 Link](https://open-vsx.org/extension/castrogusttavo/symbols)     |
| Animations            | `BrandonKirbyson.vscode-animations`    | [🔗 Link](https://open-vsx.org/extension/BrandonKirbyson/vscode-animations) |
| VSCode Pets           | `tonybaloney.vscode-pets`              | [🔗 Link](https://open-vsx.org/extension/tonybaloney/vscode-pets)    |

---

### 🛠️ dev.nix Ausschnitt (`idx.extensions`)

```nix
idx.extensions = [
  "shalldie.background"
  "ms-azuretools.vscode-docker"
  "mikestead.dotenv"
  "usernamehw.errorlens"
  "dbaeumer.vscode-eslint"
  "miguelsolorio.fluent-icons"
  "eamodio.gitlens"
  "yandeu.five-server"
  "Nuxt.mdc"
  "csstools.postcss"
  "ms-vscode.powershell"
  "ms-python.python"
  "castrogusttavo.symbols"
  "BrandonKirbyson.vscode-animations"
  "tonybaloney.vscode-pets"
];
```


   
</details>




