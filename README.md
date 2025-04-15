# firebase-studio-cheat-sheet

# Docs
- https://firebase.google.com/docs












<br><br>
________
________
<br><br>



# Projekte

## 🚀 GitHub-Projekt in Firebase Studio importieren

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

# Workspace

<details><summary>Click to expand..</summary>


## 🔧 Firebase Studio – Arbeitsbereich anpassen

Firebase Studio nutzt **Nix** zur Definition reproduzierbarer Entwicklungsumgebungen. Alle Anpassungen erfolgen über `.idx/dev.nix`.

---

### 🛠 dev.nix – Aufbau

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
| Five Server           | `yandeu.five-server`                   | [🔗 Link](https://open-vsx.org/extension/yandeu/five-server)         |
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




