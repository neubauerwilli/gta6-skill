# Der GTA-6-Skill

Elf Claude-Skills auf Deutsch, die aus GTA-6-Streams Kurzclips machen und dich zu Clipping-Kampagnen
bringen. GTA 6 erscheint am 19.11.2026 für PS5 und Xbox Series X|S. Die großen Streamer werden es tagelang
streamen. Wer in dieser Zeit schnell die guten Momente findet, sauber schneidet und regelkonform einreicht,
kann über Kampagnen Geld verdienen. Das Skill-Paket ist kostenlos und MIT-lizenziert. Ein nutzbarer
Claude-Code-Zugang ist separat erforderlich und kann kostenpflichtig sein. Das Paket verlangt keinen
eigenen API-Schlüssel; Claude Code braucht seine reguläre Anmeldung oder API-Abrechnung.

Einer sucht laufende Kampagnen. Einer findet, wer GTA 6 gerade streamt. Einer holt aus einem VOD die
stärksten Momente. Dann schneidet, betitelt und prüft das Paket den Clip. Und der Trend-Radar sagt dir, welcher
Moment gerade anzieht und zu welchem er noch keine Clips findet.

**Gepostet oder eingereicht wird nichts, bevor du Ja sagst.** Kein Skill loggt sich ein, lädt hoch oder reicht
etwas ein. Das machst du selbst. **Erfunden wird nichts:** keine Aufrufzahlen, keine Verdienste, keine Sätze.

## Wie das Geld fließt (nur das, was belegt ist)

Content Rewards (contentrewards.com, früher Whop Content Rewards) zahlt pro 1.000 freigegebene Aufrufe. Bei
Streamer-Kampagnen steht der Satz in jeder Kampagne; Content Rewards nennt als Median rund 1 Dollar je 1.000 (eigene Seite, Stand 13.11.2026), einzelne Kampagnen zahlen mehr oder weniger. Die Budgets sind
begrenzt, und eine Kampagne kann einzelne Clips ablehnen. Quellen:
[Whop-Doku](https://docs.whop.com/manage-your-business/growth-marketing/content-rewards) und
[Content-Rewards-Blog](https://contentrewards.com/blog/get-paid-clip-streamers).

> Keine Verdienstgarantie. Gerechnet wird mit deinen echten Zahlen. Clippe nur Material, das eine Kampagne oder
> der Rechteinhaber ausdrücklich erlaubt. Ohne Kampagne oder Erlaubnis schneidet der Skill nichts.

## Voraussetzungen

Am besten mit **Claude Code**, weil es auf deinem Rechner `yt-dlp` und `ffmpeg` ausführen kann. Im normalen
Chat laufen nur die Skills ohne Dateiarbeit (Kampagnen, Streams, Hook, Caption, Kanal und Uploadplan, Tracker).

| Was | Wofür | Pflicht |
| --- | --- | --- |
| Claude Code | Skills ausführen, Befehle starten | ja, für Schnitt und Momente |
| yt-dlp | VOD-Abschnitte und Untertitel holen | ja, für Momente und Schnitt |
| ffmpeg | Schneiden, Format 9:16, Untertitel einbrennen | ja, für Schnitt |
| Whisper (`faster-whisper` empfohlen, alternativ `openai-whisper` mit dem Befehl `whisper`) | Transkript, wenn keine Untertitel da sind | optional |
| Python 3.8 oder neuer | nur für Whisper und `tests/pruefen.py` | optional |

**Windows (PowerShell):**

```powershell
winget install yt-dlp.yt-dlp
winget install Gyan.FFmpeg
# optional, für Transkripte ohne Untertitel (lädt beim ersten Lauf ein Modell):
py -m pip install faster-whisper
```

Danach ein neues Terminal öffnen und prüfen: `yt-dlp --version` und `ffmpeg -version`.

**Mac (Terminal):**

```bash
brew install yt-dlp ffmpeg
# optional, für Transkripte ohne Untertitel (lädt beim ersten Lauf ein Modell):
pip3 install faster-whisper
```

Claude Code selbst: Anleitung unter https://docs.claude.com/en/docs/claude-code.

## Einrichten in Claude Code

Öffne Claude Code in einem neuen Projektordner. Nutze dort diese beiden Befehle nacheinander:

```
/plugin marketplace add neubauerwilli/gta6-skill
/plugin install gta6-skill@gta6-skill
```

Als Plugin bekommen die Skills einen Namensraum, sie heißen dann `/gta6-skill:kampagnen-finder` und so weiter.
Prüfe im Plugin-Menü, dass `gta6-skill` aktiviert ist. Falls die Befehle noch fehlen, starte Claude Code neu.
Im Terminal prüfst du die Installation mit `claude plugin list` und `claude plugin details gta6-skill`.

Starte dann zum Beispiel:

```text
/gta6-skill:kampagnen-finder Ich bin in Österreich und möchte deutschsprachige YouTube Shorts erstellen.
Suche aktuell zugängliche GTA-6-Clipping-Kampagnen. Prüfe Plattform, Erlaubnis, Vergütung und Restbudget
an der Originalseite. Fehlen öffentliche Daten, markiere sie offen. Erfinde keine Kampagne.
```

Ohne freigegebenes Material kannst du mit `/gta6-skill:kanal-bauer` bereits Namen, Beschreibung und
einen konkreten eigenen Kanalansatz planen. Vor dem Spielstart können passende Kampagnen oder VODs fehlen.

### Alternative: einzelne Skill-Ordner

Wenn du einfach `/kampagnen-finder` willst, kopiere die Ordner in ein neues Projekt. Bereits vorhandene
gleichnamige Skills zuerst vergleichen, nicht ungeprüft überschreiben:

```bash
git clone https://github.com/neubauerwilli/gta6-skill.git
mkdir -p .claude/skills
cp -Rn gta6-skill/skills/* .claude/skills/
```

Windows (PowerShell):

```powershell
git clone https://github.com/neubauerwilli/gta6-skill.git
New-Item -ItemType Directory -Force .claude/skills | Out-Null
Get-ChildItem gta6-skill/skills -Directory | ForEach-Object {
    $ziel = Join-Path .claude/skills $_.Name
    if (Test-Path -LiteralPath $ziel) { throw "Skill schon vorhanden: $ziel" }
    Copy-Item -LiteralPath $_.FullName -Destination $ziel -Recurse
}
```

Kein Claude Code? Füge den Inhalt einer einzelnen `SKILL.md` als Arbeitsanweisung in einen Claude-Chat ein.
Das installiert kein Plugin. Textaufgaben funktionieren dort mit dem vorhandenen Material; Webrecherche
braucht Webzugriff. Lokalen Videoschnitt mit ffmpeg führt der normale Chat dadurch nicht aus.

## Die elf

Die Tabelle zeigt die kurzen Namen. Bei der Plugin-Installation immer `gta6-skill:` voranstellen,
zum Beispiel `/gta6-skill:trend-radar`.

| Befehl | Rolle | Was er macht |
| --- | --- | --- |
| `/kampagnen-finder` | Kampagnen-Sucher | Sucht laufende Clipping-Kampagnen zu GTA 6 und Streamern: Satz je 1.000, Regeln, Restbudget, Link. |
| `/stream-finder` | Stream-Sucher | Findet, welche großen Streamer GTA 6 live oder als VOD haben (Twitch, YouTube, Kick), mit Link. |
| `/momente-finder` | Momente-Sucher | Holt Untertitel oder Transkript, sucht Lautstärke- und Chat-Spitzen und listet Momente mit Zeitstempel und Grund. |
| `/clip-schneider` | Schnitt | Schneidet auf 9:16, 15 bis 60 Sekunden, Gesicht oben und Spiel unten oder Spiel voll. |
| `/untertitel` | Untertitel | Brennt große Untertitel Wort für Wort ein, deutsch oder englisch. |
| `/hook-schreiber` | Hook | Text-Hook für die ersten Sekunden, dazu drei Varianten. |
| `/caption-und-hashtags` | Caption | Titel, Caption und Hashtags je Plattform, mit Pflicht-Tags der Kampagne. |
| `/regel-check` | Regel-Prüfer | Prüft jeden Clip gegen die Kampagnenregeln, bevor er rausgeht. |
| `/trend-radar` | Trend-Radar | Beobachtet, welche GTA-6-Momente anziehen, und meldet Momente, zu denen er noch keine Clips findet, mit Stream-Link und Zeitstempel. Sagt offen, was er messen kann und was nicht. |
| `/kanal-bauer` | Kanal-Bauer | Baut deinen YouTube-Shorts-Kanal auf (Name, Beschreibung, Banner-Text, Playlists) und plant, wann welcher Clip wo rausgeht, ohne Spam-Regeln zu verletzen. |
| `/auszahlungs-tracker` | Tracker | Tabelle je Clip mit Kampagne, Aufrufen, Satz und erwarteter Auszahlung. Rechnet nur mit Zahlen, die du einträgst. |

## Beispielablauf

1. `/kampagnen-finder`: Du siehst, welche Kampagnen laufen und was sie verlangen. Du trittst selbst bei.
2. `/stream-finder`: Er nennt die Streamer mit Kampagne, die gerade GTA 6 haben, mit Links.
3. `/momente-finder`: Er zieht Untertitel aus dem erlaubten VOD und listet die besten Stellen.
4. `/trend-radar`: Er prüft diese Momente gegen aktuelle Suchen und meldet Kandidaten ohne gefundene Clips.
5. `/clip-schneider`, dann `/untertitel`: Aus dem Moment wird ein 9:16-Clip mit Untertiteln.
6. `/hook-schreiber` und `/caption-und-hashtags`: Du wählst den Hook, dazu kommen die Texte je Plattform.
7. `/regel-check`: Der Clip geht gegen den Kampagnen-Brief. Erst bei `bereit` weiter.
8. `/kanal-bauer`: Er baut deinen Kanal auf und legt fest, wann was rausgeht. Posten und Einreichen machst du selbst.
9. `/auszahlungs-tracker`: Du trägst die echten Aufrufe ein, er rechnet.

Alles liegt im Ordner `gta6/` deines Projekts:

```
gta6/
├── kampagnen.md  ·  streams.md  ·  momente.md  ·  radar.md
├── kanal.md  ·  tracker.csv
├── quelle/                      (geladene Abschnitte und Untertitel)
└── clips/
    ├── 01-roh.mp4  ·  01-fertig.mp4
    └── 01-hook.md  ·  01-texte.md  ·  01-regelcheck.md
```

## Grenzen

- Der Trend-Radar sieht nur, was öffentlich sichtbar ist und was seine Suchen finden. Er sagt nicht voraus,
  was anzieht, und kann nicht beweisen, dass ein Moment noch frei ist.
- Der Radar läuft auf Aufruf. Das Paket enthält keinen Hintergrunddienst und überwacht Streams nicht von selbst.
  Nach deiner Auswahl führt er durch Schnitt, Untertitel und Prüfung. Einen fertigen Clip meldet er erst nach
  echter Dateierstellung und Kontrolle. Ohne Wortzeiten gibt es keine nachgewiesenen Wort-für-Wort-Untertitel.
- Der Kanal-Bauer erstellt Kanaltexte und einen Uploadplan. Er legt keinen YouTube-Kanal an und garantiert
  weder Partnerprogramm-Aufnahme noch Einnahmen. YouTube vergütet berechtigte Aufrufe nach seinen Regeln,
  nicht jeden Aufruf. Ein Streamer-Clip wird durch Schnitt und Untertitel allein nicht automatisch monetarisierbar.
- Kampagnen, Sätze und Budgets ändern sich. Jeder Skill liest sie bei jedem Lauf neu bei der Quelle.
- Seiten hinter einem Login kann Claude nicht lesen. Dann trägst du Regeln und Zahlen selbst ein.
- Bei den Zahlen im Tracker steht, was du eingibst. Eine Auszahlung ist damit nicht zugesagt.

## Datenschutz

Alles läuft in deinem Projektordner. Was du Claude zeigst, geht an deinen Claude-Zugang, wie jede andere
Unterhaltung auch. Gib keine Zugangsdaten von Plattformen oder Kampagnen in den Chat.

## Prüfen

```bash
python3 tests/pruefen.py
```

Prüft, dass alle elf Skills vorhanden sind, Name und Beschreibung stimmen, die Regeln drinstehen und keine
Gedankenstriche im Text sind. Unter Windows oft `py` statt `python3`.

Technischer Medien-Test mit eigenem synthetischem Material (benötigt ffmpeg und ffprobe):

```bash
python3 tests/test_media.py
```

Er erzeugt in einem temporären Ordner ein Testbild mit Ton, schneidet es proportional auf 1080x1920,
brennt Testuntertitel ein und prüft Dateiformat, Dauer, Ton und die tatsächlich geänderten Bildpixel.
Das ist kein Test von Streamer-Rechten, Live-Trends, Transkription oder Einnahmen.

Von Willi Neubauer · Lizenz: [MIT](LICENSE)
