# Familienrezepte
# Familienrezepte

Eine kleine Rezeptdatenbank für die ganze Familie. Läuft als statische Seite auf GitHub Pages und speichert alle Rezepte in Supabase, sodass jeder mit dem Link dieselben Daten sieht.

## Schritt 1: Supabase Projekt anlegen

1. Auf supabase.com kostenlos registrieren und ein neues Projekt erstellen.
2. Im Dashboard unter SQL Editor die Datei `schema.sql` öffnen und ausführen. Das legt die Tabelle `recipes` und die Zugriffsregeln an.
3. Unter Storage einen neuen Bucket namens `recipe-photos` anlegen und dabei "Public bucket" aktivieren.
4. Danach im SQL Editor die letzten vier Policies aus `schema.sql` ausführen (die für `storage.objects`), falls sie noch nicht mit ausgeführt wurden.
5. Unter Project Settings, dann API, die Werte "Project URL" und "anon public" Key kopieren.

## Schritt 2: App konfigurieren

In `index.html` ganz oben im Script Bereich folgende Zeilen anpassen:

```js
const SUPABASE_URL = "https://DEIN-PROJEKT.supabase.co";
const SUPABASE_ANON_KEY = "DEIN-ANON-KEY";
```

Mit den Werten aus Schritt 1 ersetzen. Der anon key ist zum öffentlichen Verwenden in einer statischen Seite gedacht, das ist bei Supabase so vorgesehen.

## Schritt 3: Auf GitHub veröffentlichen

1. Ein neues GitHub Repository erstellen, zum Beispiel `familienrezepte`.
2. Die Datei `index.html` in das Repository hochladen (im Wurzelverzeichnis).
3. Im Repository unter Settings, dann Pages, als Quelle den `main` Branch und den Ordner `/root` auswählen und speichern.
4. Nach kurzer Zeit ist die App unter `https://DEIN-BENUTZERNAME.github.io/familienrezepte` erreichbar. Diesen Link mit der Familie teilen.

## Schritt 4: Scan Funktion einrichten (optional)

Die Scan Funktion liest ein Foto eines Rezepts aus und füllt das Formular automatisch aus. Dafür wird ein Anthropic API Key benötigt, der aus Sicherheitsgründen nicht im Browser Code stehen darf, sondern über eine Supabase Edge Function läuft.

1. Einen Anthropic API Key unter console.anthropic.com erstellen (kostenpflichtig nach Nutzung, ein Scan kostet nur Bruchteile eines Cents).
2. Die Supabase CLI installieren, siehe supabase.com/docs/guides/cli.
3. Im Projektordner folgende Befehle ausführen:

```bash
supabase login
supabase link --project-ref DEIN-PROJEKT-REF
supabase secrets set ANTHROPIC_API_KEY=sk-ant-dein-key
supabase functions deploy scan-recipe
```

Den Projekt Ref findet man im Supabase Dashboard in der Projekt URL oder unter Project Settings.

Ohne diesen Schritt funktioniert die App ganz normal, nur der Kamera Button für den automatischen Scan zeigt dann eine Fehlermeldung.

## Sicherheitshinweis

Die Rezeptdatenbank ist mit dem anon key für jeden mit dem Link lesbar und beschreibbar, ähnlich wie eine geteilte Tabelle. Für eine private Familienrezeptsammlung ist das in der Regel unproblematisch. Wer zusätzlichen Schutz möchte, kann später Supabase Auth mit einem gemeinsamen Familienlogin ergänzen, das ist mit etwas Zusatzaufwand möglich.

## Dateien in diesem Projekt

- `index.html` die eigentliche App
- `schema.sql` Datenbank Tabelle und Zugriffsregeln
- `supabase/functions/scan-recipe/index.ts` Edge Function für die Scan Funktion
