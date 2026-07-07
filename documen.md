Dale, acá van los pasos completos y en orden, listos para copiar a tu Word:

---

**Instalación y autenticación de GitHub CLI (gh) en WSL/Ubuntu**

**1. Instalar GitHub CLI (versión oficial, no la de apt por defecto porque queda desactualizada)**

```bash
sudo apt update
type -p curl >/dev/null || sudo apt install curl -y
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
sudo chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null
sudo apt update
sudo apt install gh -y
```

**2. Verificar la instalación**

```bash
gh --version
```

**3. Instalar `wslu` (necesario en WSL para que `gh` pueda abrir el navegador de Windows automáticamente)**

```bash
sudo apt install wslu -y
```

**4. Autenticarse con GitHub**

```bash
gh auth login
```

Elegir en el asistente interactivo:

- Account: **GitHub.com**
- Protocolo preferido para Git: **HTTPS**
- Authenticate Git with GitHub credentials: **Yes**
- Método de autenticación: **Login with a web browser**

Copiar el código de un solo uso que aparece, presionar Enter y completar el login en el navegador que se abre.

**5. Verificar que quedó autenticado**

```bash
gh auth status
```

Debe mostrar `✓ Logged in to github.com` con los scopes `repo` y `workflow`.

**6. Notas importantes**

- Si el login falla por rate limit ("Too many requests..."), esperar unos minutos y reintentar.
- Si `gh auth status -a` (usado internamente por Claude Code) falla con `unknown shorthand flag`, es porque la versión de `gh` es vieja — repetir el paso 1 usando el repositorio oficial en vez de `apt install gh` directo.

---

¿Necesitás que arme algo similar para los pasos de `/install-github-app`?
