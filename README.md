# n8n Docker Stack - Vollständige Installationsanleitung

Ein vollständiger n8n-Stack mit PostgreSQL-Datenbank und n8n-MCP-Integration, bereitgestellt über Docker Compose.

## 📋 Was ist das?

- **n8n:** Eine Open-Source-Workflow-Automatisierungsplattform
- **Docker:** Container-Technologie zum einfachen Bereitstellen von Anwendungen
- **Claude Code:** Anthropics KI-basierte Entwicklungsumgebung
- **WSL2:** Windows Subsystem für Linux (ermöglicht Linux unter Windows)

## 🖥️ Systemvoraussetzungen

### Allgemeine Anforderungen
- Node.js 20+ (für Claude Code Installation)
- Docker und Docker Compose
- WSL2 (falls Windows verwendet wird)
- Mindestens 8 GB RAM (16 GB empfohlen)
- Mindestens 10 GB freier Festplattenspeicher

### Hardware-Virtualisierung
- CPU mit Virtualisierungsunterstützung (Intel VT-x oder AMD-V)
- Virtualisierung im BIOS/UEFI aktiviert

## 🚀 Schritt-für-Schritt Installation

### Schritt 1: WSL2 installieren (erforderlich für Windows)

#### 1.1 WSL aktivieren

Öffne die **PowerShell als Administrator** und führe aus:

```powershell
# WSL und Virtual Machine Platform aktivieren
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart

# Computer neu starten
shutdown /r /t 0
```

#### 1.2 WSL2 als Standard setzen

Nach dem Neustart öffnest du die PowerShell als Administrator:

```powershell
# WSL2 als Standard-Version setzen
wsl --set-default-version 2
```

#### 1.3 Linux-Distribution installieren

```powershell
# Ubuntu installieren (empfohlen)
wsl --install -d Ubuntu

# Oder über Microsoft Store:
# 1. Microsoft Store öffnen
# 2. Nach "Ubuntu" suchen
# 3. "Ubuntu 22.04 LTS" installieren
```

#### 1.4 Ubuntu einrichten

1. Ubuntu starten (aus dem Startmenü)
2. Benutzername und Passwort erstellen
3. System aktualisieren:

```bash
sudo apt update && sudo apt upgrade -y
```

### Schritt 2: Docker Desktop installieren

#### 2.1 Docker Desktop herunterladen

1. Besuche: https://www.docker.com/products/docker-desktop/
2. Klicke auf **"Download for Windows"**
3. Lade `Docker Desktop Installer.exe` herunter

#### 2.2 Docker Desktop installieren

1. **Führe den Installer als Administrator aus**
2. **Installationsoptionen:**
   - ✅ **"Use WSL 2 instead of Hyper-V"** aktivieren
   - ✅ **"Add shortcut to desktop"** (optional)
3. **Warte die Installation ab** (kann 10-15 Minuten dauern)
4. **Starte den Computer neu** wenn erforderlich

#### 2.3 Docker Desktop konfigurieren

1. **Starte Docker Desktop**
2. **Überspringe das Tutorial** oder gehe es durch
3. **Öffne die Einstellungen** (Zahnrad-Symbol)
4. **"Resources" > "WSL Integration":**
   - ✅ **"Enable integration with my default WSL distro"**
   - ✅ **Ubuntu** aktivieren
5. **Klicke auf "Apply & Restart"**

#### 2.4 Docker testen

In **WSL2 Ubuntu Terminal**:

```bash
# Docker-Version prüfen
docker --version

# Test-Container starten
docker run hello-world
```

### Schritt 3: Claude Code installieren

Claude Code wird über npm installiert. Du benötigst Node.js 20+ für die Installation.

#### Node.js installieren (falls nicht vorhanden)

**WSL2/Linux:**
```bash
# Über NodeSource (empfohlen)
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs

# Version prüfen
node --version
npm --version
```

**Windows:**
1. Besuche: https://nodejs.org/
2. Lade die LTS-Version herunter
3. Führe den Installer aus

#### Claude Code installieren

```bash
# Claude Code global installieren
npm install -g @anthropic-ai/claude-code

# Installation testen
claude-code --version
```

### Schritt 4: Git installieren (falls nicht vorhanden)

#### In WSL2:
```bash
sudo apt install git -y
git --version
```

#### In Windows:
1. Downloaden von: https://git-scm.com/download/win
2. Installer ausführen (Standard-Einstellungen OK)

## 📁 Projekt einrichten

### 1. Repository klonen

```bash
# Ins Projekte-Verzeichnis wechseln
cd ~
mkdir -p projects
cd projects

# Repository klonen (URL anpassen)
git clone https://github.com/betarepos/n8n n8n
cd n8n
```

### 2. Umgebungsvariablen konfigurieren

```bash
# Beispiel-Datei kopieren
cp .env.example .env

# Mit einem Editor bearbeiten (z.B. nano in WSL2)
nano .env
```

**Wichtige Einstellungen in der `.env`-Datei:**

```bash
# ---- Public URL / host settings ----
N8N_HOST=localhost
N8N_PROTOCOL=http
WEBHOOK_URL=http://localhost:5678/

# ---- n8n security ----
# WICHTIG: Eigene sichere Werte verwenden!
N8N_ENCRYPTION_KEY=hier_64_zeichen_langen_zufälligen_schlüssel_einfügen
N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER=admin
N8N_BASIC_AUTH_PASSWORD=dein_sicheres_passwort
N8N_API_KEY=dein_api_schlüssel

# ---- Postgres ----
POSTGRES_USER=n8n
POSTGRES_PASSWORD=sicheres_postgres_passwort
POSTGRES_DB=n8n
```

### 3. Sichere Schlüssel generieren

#### Automatisch generieren (Linux/WSL2):
```bash
# Verschlüsselungsschlüssel (64 Zeichen)
echo "N8N_ENCRYPTION_KEY=$(openssl rand -hex 32)" >> .env.local

# API-Schlüssel
echo "N8N_API_KEY=$(openssl rand -base64 32)" >> .env.local

# Werte aus .env.local in .env kopieren
```

#### Manuell generieren:
- Besuche: https://www.random.org/passwords/
- Generiere einen 64-Zeichen-String für `N8N_ENCRYPTION_KEY`
- Generiere einen 32-Zeichen-String für `N8N_API_KEY`

## 🐳 n8n Stack starten

### 1. Docker-Services starten

```bash
# Im Projekt-Verzeichnis
cd ~/projects/n8n

# Stack im Hintergrund starten
docker-compose up -d
```

### 2. Startup-Prozess verfolgen

```bash
# Logs aller Services anzeigen
docker-compose logs -f

# Nur n8n-Logs
docker-compose logs -f n8n

# Service-Status prüfen
docker-compose ps
```

**Erwartete Ausgabe:**
```
NAME          IMAGE                              STATUS
n8n           n8nio/n8n:latest                   Up
n8n-postgres  postgres:16-alpine                 Up (healthy)
n8n-mcp       ghcr.io/czlonkowski/n8n-mcp:latest Up
```

### 3. n8n öffnen

1. **Öffne deinen Webbrowser**
2. **Gib die URL ein:** http://localhost:5678
3. **Gib deine Anmeldedaten ein:**
   - Benutzername: Dein `N8N_BASIC_AUTH_USER` aus `.env`
   - Passwort: Dein `N8N_BASIC_AUTH_PASSWORD` aus `.env`

## 🎯 Erste Schritte mit n8n

1. **Richte dein Konto ein** (beim ersten Start)
2. **Erstelle deinen ersten Workflow:**
   - Klicke auf "New workflow"
   - Füge Nodes per Drag & Drop hinzu
   - Erstelle Verbindungen zwischen Nodes
3. **Teste den Workflow** mit "Test workflow"
4. **Aktiviere den Workflow** für automatische Ausführung

## 🛠️ Mit Claude Code arbeiten

### Projekt in Claude Code öffnen

```bash
cd ~/projects/n8n
claude-code .
```

### Nützliche Claude Code Befehle

```bash
# Docker-Status prüfen
docker-compose ps

# Logs anzeigen
docker-compose logs -f

# Services neu starten
docker-compose restart

# Stack stoppen
docker-compose down

# Stack mit Datenbank-Reset
docker-compose down -v
```

## 🔧 Wartung und Verwaltung

### Backup erstellen

```bash
# Datenbank-Backup
docker-compose exec db pg_dump -U n8n n8n > backup_$(date +%Y%m%d_%H%M%S).sql

# n8n-Daten backup
docker run --rm -v n8n_n8n_data:/data -v $(pwd):/backup alpine tar czf /backup/n8n_data_backup_$(date +%Y%m%d_%H%M%S).tar.gz -C /data .
```

### Updates durchführen

```bash
# Images aktualisieren
docker-compose pull

# Stack neu starten
docker-compose down
docker-compose up -d
```

### Container-Management

```bash
# In n8n-Container einloggen
docker-compose exec n8n sh

# Datenbank-Zugriff
docker-compose exec db psql -U n8n -d n8n

# Ressourcen-Verbrauch anzeigen
docker stats
```

## 🐛 Häufige Probleme und Lösungen

### Docker Desktop startet nicht

1. **Virtualisierung prüfen:**
   - BIOS/UEFI öffnen → Virtualization/VT-x aktivieren
   - Computer neu starten

2. **WSL2-Kernel aktualisieren:**
   ```powershell
   wsl --update
   ```

3. **Docker Desktop als Administrator starten**

### Port 5678 bereits belegt

```bash
# Port-Nutzung prüfen
sudo netstat -tulpn | grep 5678

# Prozess beenden (ID aus obigem Befehl)
sudo kill -9 <PID>
```

### n8n lädt nicht/Verbindungsfehler

1. **Container-Status prüfen:**
```bash
docker-compose ps
```

2. **Logs prüfen:**
```bash
docker-compose logs n8n
docker-compose logs db
```

3. **Firewall/Antivirus prüfen:**
   - Windows Defender Firewall → Erlaube Docker Desktop
   - Antivirus-Software → Füge eine Ausnahme hinzu

### Datenbank-Probleme

1. **DB-Container neu starten:**
```bash
docker-compose restart db
```

2. **Gesundheitsstatus prüfen:**
```bash
docker-compose exec db pg_isready -U n8n -d n8n
```

3. **Bei persistenten Problemen - DB zurücksetzen:**
```bash
docker-compose down -v
docker-compose up -d
```

## 📚 Hilfreiche Ressourcen

### Dokumentation
- [n8n Dokumentation](https://docs.n8n.io/)
- [Docker Desktop Dokumentation](https://docs.docker.com/desktop/)
- [Docker Compose Dokumentation](https://docs.docker.com/compose/)
- [WSL2 Dokumentation](https://docs.microsoft.com/en-us/windows/wsl/)

### Video-Tutorials
- [n8n YouTube Kanal](https://www.youtube.com/c/n8nio)
- [Docker Grundlagen](https://www.youtube.com/watch?v=fqMOX6JJhGo)

### Community
- [n8n Community Forum](https://community.n8n.io/)
- [n8n Discord](https://discord.gg/n8n)

## 🤝 Support und Hilfe

Bei Problemen:

1. **Logs prüfen:** `docker-compose logs`
2. **Status prüfen:** `docker-compose ps`
3. **Konfiguration validieren:** `docker-compose config`
4. **Docker Desktop neu starten**
5. **System neu starten** (als letztes Mittel)

---

**Viel Erfolg mit n8n! 🎉**
