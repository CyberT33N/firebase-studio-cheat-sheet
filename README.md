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
- Firebase Studio nutzt **Nix** zur Definition reproduzierbarer Entwicklungsumgebungen. Alle Anpassungen erfolgen über `.idx/dev.nix`.
- https://github.com/CyberT33N/nix-cheat-sheet/blob/main/README.md

<details><summary>Click to expand..</summary>


# Firebase Studio (IDX) `dev.nix` Cheatsheet

Diese Datei (`.idx/dev.nix`) nutzt **Nix**, um **reproduzierbare und versionierbare** Entwicklungsumgebungen für Firebase Studio (ehemals Project IDX) zu definieren.

**Warum `dev.nix`?**

*   **Deklarativ:** Beschreibt den *gewünschten Zustand* der Umgebung, nicht die Schritte dorthin.
*   **Reproduzierbar:** Stellt sicher, dass jeder im Team exakt dieselbe Entwicklungsumgebung mit den gleichen Werkzeugen und Versionen erhält.
*   **Versionierbar:** Änderungen an der Umgebung können wie Code über Git verfolgt werden.

## Grundlegende Struktur

```nix
# Importiert den Nix-Paketsatz (pkgs) und erlaubt weitere Argumente (...)
{ pkgs, ... }: {

  # Hier kommen alle Konfigurationsoptionen rein
  # z.B. channel, packages, env, idx, services

}
```

---

## Hauptkonfigurationsoptionen (Top-Level)

Diese Attribute werden direkt im Haupt-Attributsatz definiert.

### `channel`

*   **Zweck:** Wählt den [Nixpkgs](https://github.com/NixOS/nixpkgs) Channel (Paket-Sammlung).
*   **Typ:** `String`
*   **Werte:**
    *   `"stable-YY.MM"` (z.B. `"stable-24.05"`): Empfohlen für Stabilität.
    *   `"unstable"`: Neueste Pakete, potenziell weniger getestet.
*   **Beispiel:**
    ```nix
    channel = "stable-24.05";
    ```

### `packages`

*   **Zweck:** Installiert Systempakete und Werkzeuge in der Umgebung.
*   **Suche:** Finde Pakete auf [search.nixos.org/packages](https://search.nixos.org/packages).
*   **Typ:** `Liste von Nix-Paket-Derivationen`
*   **Beispiele:**
    ```nix
    packages = [
      # Basis-Tools
      pkgs.git
      pkgs.zsh
      pkgs.starship

      # Programmiersprachen / Runtimes
      pkgs.nodejs_20  # Spezifische Node.js Version
      pkgs.yarn
      # pkgs.python3
      # pkgs.go

      # Cloud Tools
      pkgs.google-cloud-sdk # Basis SDK

      # SDK mit zusätzlichen Komponenten (Beispiel)
      (pkgs.google-cloud-sdk.withExtraComponents [
        pkgs.google-cloud-sdk.components.gke-gcloud-auth-plugin
        pkgs.google-cloud-sdk.components.cloud-datastore-emulator
      ])

      # Andere Tools
      pkgs.docker
      pkgs.kubectl
    ];
    ```

### `env`

*   **Zweck:** Definiert globale Umgebungsvariablen für die Workspace-Shell.
*   **Typ:** `Attributsatz` (Key-Value-Paare von Strings)
*   **Beispiel:**
    ```nix
    env = {
      NODE_ENV = "development";
      API_URL = "http://localhost:3000";
      # Variable kann auf Pfade von installierten Paketen verweisen
      GOOGLE_APPLICATION_CREDENTIALS = "${pkgs.google-cloud-sdk}/bin/gcloud"; # Beispiel
    };
    ```

### `services`

*   **Zweck:** Aktiviert und konfiguriert von Firebase Studio verwaltete Hintergrunddienste.
*   **Typ:** `Attributsatz`
*   **Beispiele:**
    ```nix
    services = {
      # Aktiviert den Redis-Dienst
      redis.enable = true;

      # Aktiviert den MySQL/MariaDB-Dienst
      mysql.enable = true;
      # mysql.package = pkgs.mariadb; # Optional: Spezifisches Paket wählen
      # mysql.initialDatabases = [ { name = "mydb"; } ]; # Optional: DBs anlegen

      # Aktiviert den Pub/Sub Emulator
      pubsub.enable = true;
      # pubsub.port = 8085; # Optional: Port ändern
      # pubsub.host = "localhost"; # Optional: Host ändern

      # Aktiviert den Firestore Emulator (wenn 'google-cloud-sdk' installiert ist)
      # firestore.enable = true;
    };
    ```
    *Hinweis: Die Verfügbarkeit und Optionen können sich ändern. Siehe offizielle Doku für Details.*

---

## Firebase Studio (IDX) spezifische Konfiguration (`idx`)

Dieser verschachtelte Attributsatz steuert IDE-spezifische Funktionen.

### `idx.extensions`

*   **Zweck:** Installiert automatisch VS Code-Erweiterungen für das Projekt.
*   **Suche:** Finde IDs (`publisher.extensionId`) auf [open-vsx.org](https://open-vsx.org).
*   **Typ:** `Liste von Strings`
*   **Beispiel:**
    ```nix
    idx.extensions = [
      "vscodevim.vim"           # Vim Keybindings
      "dbaeumer.vscode-eslint"  # ESLint Linter
      "esbenp.prettier-vscode"  # Prettier Formatter
      "angular.ng-template"     # Angular Language Service
      "googlecloudtools.cloudcode" # Google Cloud / Firebase Tools
    ];
    ```
    *(Erweiterungen können auch manuell über die UI installiert werden, diese sind dann nur für deinen persönlichen Workspace aktiv.)*

### `idx.previews`

*   **Zweck:** Konfiguriert, wie Webserver oder andere Prozesse für die Vorschau gestartet werden.
*   **Typ:** `Attributsatz`
*   **Sub-Attribute:**
    *   `enable`: (`Boolean`, default: `true`) Schaltet die Vorschau-Funktion an/aus.
    *   `previews`: (`Attributsatz`) Definiert die einzelnen Vorschau-Konfigurationen. Jeder Key ist ein Name (z.B. `web`, `api`).

*   **Struktur einer einzelnen Vorschau (z.B. `idx.previews.previews.web`):**
    *   `command`: (`Liste von Strings`) Der auszuführende Befehl zum Starten des Servers/Prozesses.
    *   `manager`: (`String`) Typ der Vorschau.
        *   `"web"`: Öffnet die URL im integrierten Vorschau-Panel.
        *   `"process"`: Startet nur den Prozess ohne UI-Panel (nützlich für reine APIs oder Hintergrundtasks).
    *   `env`: (`Attributsatz`, optional) Zusätzliche Umgebungsvariablen für *diesen* Vorschau-Prozess.
        *   **Wichtig:** Verwende `$PORT`, um den von IDX zugewiesenen Port zu erhalten.
    *   `rootDir`: (`String`, optional, manchmal auch `cwd`) Verzeichnis, aus dem der `command` ausgeführt wird (relativ zum Workspace-Root).

*   **Beispiel:**
    ```nix
    idx.previews = {
      enable = true;
      previews = {
        # Einfacher Dev-Server
        web = {
          command = ["npm" "run" "dev"];
          manager = "web";
          env = { PORT = "$PORT"; HOST = "0.0.0.0"; };
        };
        # Komplexerer Startbefehl mit Argumenten und spezifischem Verzeichnis
        angular-app = {
          command = [ "npm", "run", "start", "--", "--port", "$PORT", "--host", "0.0.0.0", "--disable-host-check" ];
          manager = "web";
          rootDir = "my-angular-project"; # Führt npm im Unterordner aus
        };
        # Reiner Backend-Prozess ohne Web-UI
        api = {
          command = ["npm" "run" "start:api"];
          manager = "process";
          env = { PORT = "$PORT"; DATABASE_URL = "mysql://user:pass@localhost/mydb"; };
          rootDir = "server";
        };
      };
    };
    ```

### `idx.workspace`

*   **Zweck:** Definiert Befehle, die zu bestimmten Zeitpunkten im Workspace-Lebenszyklus ausgeführt werden.
*   **Typ:** `Attributsatz`
*   **Sub-Attribute:**
    *   `onCreate`: (`Attributsatz`) Befehle werden *einmalig* beim Erstellen des Workspaces ausgeführt (z.B. `npm install`). Keys sind Namen für die Schritte, Values die Shell-Befehle.
    *   `onStart`: (`Attributsatz`) Befehle werden *jedes Mal* beim Starten/Neustarten des Workspaces ausgeführt (z.B. Starten von Watchern, Hintergrunddiensten).

*   **Beispiel:**
    ```nix
    idx.workspace = {
      onCreate = {
        install-deps = "npm install";
        # setup-db = "npm run db:migrate";
      };
      onStart = {
        # Startet einen Watcher im Hintergrund (das '&' ist wichtig)
        # watch-files = "npm run watch &";
      };
    };
    ```

---

## Vollständiges Beispiel (`.idx/dev.nix`)

```nix
# Mehr Infos: https://firebase.google.com/docs/studio/customize-workspace
{ pkgs, ... }: {

  # 1. Nixpkgs Channel
  channel = "stable-24.05";

  # 2. Systempakete
  packages = [
    pkgs.git
    pkgs.zsh
    pkgs.starship
    pkgs.nodejs_20
    (pkgs.google-cloud-sdk.withExtraComponents [
      pkgs.google-cloud-sdk.components.cloud-datastore-emulator
    ])
    pkgs.docker
  ];

  # 3. Globale Umgebungsvariablen
  env = {
    NODE_ENV = "development";
    TZ = "Europe/Berlin"; # Zeitzone setzen
  };

  # 4. Verwaltete Dienste
  services = {
    redis.enable = true;
    # mysql.enable = true;
  };

  # 5. Firebase Studio (IDX) spezifische Konfiguration
  idx = {
    # 5.1 IDE-Erweiterungen
    extensions = [
      "esbenp.prettier-vscode"
      "dbaeumer.vscode-eslint"
      "googlecloudtools.cloudcode"
    ];

    # 5.2 Vorschau-Konfiguration
    previews = {
      enable = true;
      previews = {
        web = {
          command = ["npm", "run", "dev", "--", "--port", "$PORT", "--host", "0.0.0.0"];
          manager = "web";
          env = { NODE_OPTIONS = "--max-old-space-size=4096"; }; # Beispiel für Node-Option
        };
      };
    };

    # 5.3 Workspace Lebenszyklus-Hooks
    workspace = {
      onCreate = {
        install-dependencies = "npm install";
      };
      onStart = {
        # info = "echo 'Workspace started!'"; # Einfacher Info-Befehl
      };
    };
  };
}
```

---

## Nützliche Ressourcen

*   🔍 **Paketsuche:** [search.nixos.org/packages](https://search.nixos.org/packages)
*   🧩 **Erweiterungssuche:** [open-vsx.org](https://open-vsx.org)
*   📚 **Offizielle `dev.nix` Referenz:** [firebase.google.com/docs/studio/customize-workspace](https://firebase.google.com/docs/studio/customize-workspace)
*   🧪 **Benutzerdefinierte Vorlagen:** [firebase.google.com/docs/studio/custom-templates](https://firebase.google.com/docs/studio/custom-templates)

---

_Hinweis: Die `dev.nix`-Syntax und die verfügbaren Optionen können sich mit Updates von Firebase Studio weiterentwickeln._




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




