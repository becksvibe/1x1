# Einmaleins-Trainer

Eine einzelne Datei (`index.html`) ohne Server. Alle Daten bleiben im Browser des Geräts (`localStorage`).

Beim ersten Start fragt die App nach einem Namen (siehe Profile). Ablauf: Eine Runde hat 10 Aufgaben. Zu jeder gibt es ein Eingabefeld und sofortige Rückmeldung. Am Rundenende erscheinen Ergebnis, Level, Streak und die Einmaleins-Karte.

## Aufgabenauswahl

Die Aufgaben stammen aus dem Bereich 1 × 1 bis 10 × 10. Dabei zählen `3 × 7` und `7 × 3` als **dieselbe Aufgabe** (55 verschiedene), die Reihenfolge der Faktoren wird zufällig gezeigt. Pro Runde werden 10 verschiedene Aufgaben gewichtet gezogen, ohne Zurücklegen. Ab Level 3 kommen Lückenaufgaben dazu, ab Level 5 zusätzlich Geteilt-Aufgaben (siehe unten). Beide zusammen machen höchstens die Hälfte einer Runde aus. Die erste Aufgabe einer Runde ist immer eine Mal-Aufgabe, damit der Einstieg sanft bleibt.

Jede Aufgabe hat ein Gewicht. Je höher es ist, desto wahrscheinlicher kommt sie dran:

```
Gewicht = max(0,3 ; 1 + 2 · falsch − 0,3 · richtig + 1,5 · Langsamkeit)
```

- **Falsche Antworten** erhöhen das Gewicht kräftig.
- **Richtige Antworten** senken es langsam.
- **Langsame Aufgaben** erhöhen es ebenfalls (siehe Tempo).

## Lückenaufgaben

Ab **Level 3** kommen Aufgaben wie `4 × ? = 20` oder `? × 4 = 20` dazu. Gesucht ist der fehlende Faktor. Sie sind die Brücke zum Geteilt-Rechnen: Wer `4 × ? = 20` löst, hat die Umkehrung schon einmal gedacht.

- Pro Runde gibt es 1 bis 2 Lückenaufgaben. Die Lücke steht zufällig vorn oder hinten.
- Der gezeigte Faktor liegt zwischen 2 und 10, der fehlende zwischen 1 und 10.
- Zusammen mit den Geteilt-Aufgaben bleiben höchstens 5 von 10 Aufgaben übrig, die keine reinen Mal-Aufgaben sind. In den letzten beiden Levels sind es deshalb nur 0 bis 1 Lückenaufgaben.
- Wie bei Geteilt-Aufgaben gelten die gleiche Gewichtung (falsch und langsam kommt öfter dran) und **eigene Statistiken**. Die Basiszeit für "langsam" wird getrennt berechnet.
- Bei einem Fehler zeigt die App die ganze Rechnung, z. B. `5 × 3 = 15`.
- Beim Aufstieg auf Level 3 erscheint der Hinweis "Neu: Lückenaufgaben (4 × ? = 20)".
- Die Einmaleins-Karte zeigt weiterhin nur Mal-Aufgaben.

## Geteilt-Aufgaben

Ab **Level 5** kommen Aufgaben wie `20 : 4` dazu. Sie sind die Umkehrung der Mal-Aufgaben (`4 × 5 = 20`). Geteilt wird nur durch 2 bis 10, das Ergebnis liegt zwischen 1 und 10. Wie viele davon in einer Runde vorkommen, steigt mit dem Level (der Rest sind Mal-Aufgaben):

| Level | Geteilt-Aufgaben pro Runde |
|---|---|
| 1 bis 4 | keine |
| 5 und 6 | 1 bis 2 |
| 7 und 8 | 2 bis 3 |
| 9 und 10 | 3 bis 4 |
| 11 und 12 | 4 bis 5 (höchstens die Hälfte) |

- Für jede Runde wird die Anzahl zufällig innerhalb der Spanne gewählt. Die Geteilt-Aufgaben werden unter die Mal-Aufgaben gemischt.
- Es gilt dieselbe Gewichtung wie bei Mal-Aufgaben: Falsche und langsame Geteilt-Aufgaben kommen häufiger dran.
- Geteilt-Aufgaben haben **eigene Statistiken**. Wer `4 × 5` kann, kann nicht automatisch `20 : 4`, und umgekehrt.
- Die Basiszeit für "langsam" wird getrennt berechnet (Geteilt-Aufgaben dürfen länger dauern).
- Beim Aufstieg auf Level 5 erscheint der Hinweis "Neu: Geteilt-Aufgaben".
- Die Einmaleins-Karte zeigt nur Mal-Aufgaben. Die Geteilt-Statistik wird mitgespeichert.

## Tempo

Gemessen wird die Zeit von der Anzeige der Aufgabe bis zum Abschicken, **nur bei richtigen Antworten**.

| Regel | Wert |
|---|---|
| Obergrenze pro Antwort | 15 Sekunden (gegen Ablenkung) |
| App im Hintergrund | Beim Zurückkommen startet die Zeit der Aufgabe neu |
| Gespeichert pro Aufgabe | gleitender Durchschnitt `ms` (alter Wert zu 50 %, neuer zu 50 %) und Anzahl der Messungen `tn` |
| Basis | Median aller Durchschnittszeiten, getrennt nach Aufgabenart (Mal: mindestens 3 Sekunden, ohne Daten 4; Geteilt: mindestens 4,5, ohne Daten 6; Lücke: mindestens 4, ohne Daten 5) |

**Langsamkeit** einer Aufgabe, für das Gewicht:

```
Langsamkeit = min(2 ; max(0 ; (ms − Basis) / Basis)) · min(1 ; tn / 2)
```

Der Maßstab ist also seine eigene typische Zeit. Mit nur einer Messung zählt die Langsamkeit nur halb.

**"Nicht flott"** gilt eine Aufgabe, wenn mindestens 2 Messungen vorliegen und der Durchschnitt über **5 Sekunden** und über dem **1,5-Fachen der Basis** liegt.

## Einmaleins-Karte

Ein 10 × 10-Raster, das am Rundenende gezeigt wird. Jedes Feld ist eine Aufgabe (gespiegelte Felder teilen sich dieselben Daten).

| Farbe | Bedeutung | Regel |
|---|---|---|
| dunkel | noch offen | noch nie geübt |
| blau | wackelt | höchstens so viele richtige wie falsche Antworten |
| türkis | noch nicht flott | mehr richtige als falsche, aber noch nicht sicher oder noch zu langsam |
| grün (leuchtend) | sicher und flott | mindestens 3 richtige, richtige mindestens 3-mal so oft wie falsche, und nicht "nicht flott" |

Felder, die in der letzten Runde einen Schritt weitergekommen sind (türkis oder grün), blinken kurz mit weißem Rand. Unter der Karte steht die Zahl der sicheren Felder.

## Diamanten und Level

- Eine richtige Antwort gibt 1 💎, eine Runde mit 10 von 10 zusätzlich 3 Bonus-Diamanten.
- Das Level hängt von der Zahl der Diamanten ab (Schwellen und Titel in der Konstante `LEVELS`). Am Rundenende steht nur, wie viele Diamanten bis zum nächsten Level fehlen. Der Titel wird erst beim Aufstieg verraten.

## Streak

Der Streak zählt, an wie vielen Tagen **hintereinander** geübt wurde. Er belohnt Regelmäßigkeit statt Menge.

**Was zählt:** Eine abgeschlossene Runde pro Kalendertag, auch am Wochenende. Weitere Runden am selben Tag ändern den Streak nicht, sie bringen nur Diamanten. Maßgeblich ist das Datum auf dem Gerät (lokale Zeit).

| Situation beim Abschluss einer Runde | Ergebnis |
|---|---|
| Heute schon gezählt | Nichts ändert sich |
| Letzte Runde war gestern | Streak +1 |
| Tage verpasst, genug Joker vorhanden | Pro verpasstem Tag wird 1 Joker verbraucht, der Streak läuft weiter (+1) |
| Tage verpasst, zu wenig Joker | Streak startet neu bei 1 |
| Erste Runde überhaupt | Streak = 1 |

Level, Diamanten, Karte und Rekord bleiben bei einem Neustart des Streaks erhalten.

**Joker:** Alle 7 Streak-Tage (7, 14, 21, …) gibt es 1 Joker, maximal 2 gleichzeitig. Sie werden automatisch eingesetzt.

**Meilensteine:**

| Streak-Tage | Bonus |
|---|---|
| 3 | +5 💎 |
| 7 | +10 💎 |
| 14 | +20 💎 |
| 30 | +50 💎 |

**Anzeige:** Oben steht `🔥` mit der aktuellen Zahl. Wäre der Streak ohne Joker gerissen, steht dort schon beim Öffnen `0`. Am Rundenende erscheinen der Streak (mit Rekord, falls höher) und Meldungen zu Joker, Meilenstein oder Rekord. "Neuer Rekord" kommt nur, wenn der Streak nach einem Abbruch einen früheren Bestwert übertrifft.

## Profile ("Wer übt?")

- Beim ersten Start fragt die App nach einem Namen. Ohne Namen geht es nicht los.
- Jedes Profil hat einen **eigenen Stand** (Diamanten, Level, Streak, Karte, Statistik). Mehrere Kinder können sich also ein Gerät teilen.
- Unten rechts steht ein kleiner, unauffälliger Button mit dem aktuellen Namen. Ein Tipp öffnet die Auswahl, dort kann man das Profil wechseln oder ein neues anlegen. Eine laufende Runde wird dabei abgebrochen, bereits gegebene Antworten bleiben gespeichert.
- Die App merkt sich das zuletzt benutzte Profil und startet direkt damit.
- Die Namen bleiben im Browser des Geräts. Intern hat jedes Profil eine zufällige ID.
- **Update von einer Version ohne Profile:** Der bisherige Fortschritt wird dem ersten angelegten Namen zugeordnet. Der alte Datensatz (`einmaleins-v1`) bleibt als Sicherung im Browser bestehen und wird nicht mehr verwendet.

## Datenspeicherung

Pro Profil liegt ein Datensatz im `localStorage` unter dem Schlüssel `einmaleins-p-<Profil-ID>`:

```json
{
  "stats":  { "7x8": { "r": 5, "w": 1, "ms": 6400, "tn": 4 }, "d4x5": { "r": 2, "w": 1, "ms": 8100, "tn": 2 }, "l4x5": { "r": 3, "w": 0, "ms": 5200, "tn": 3 } },
  "log":    [ { "t": "2026-11-02T16:04:11.000Z", "q": "7x8", "a": 56, "ok": true, "ms": 5120 } ],
  "stars":  123,
  "streak": { "cur": 5, "best": 9, "last": "2026-11-20", "jokers": 1, "broke": false }
}
```

| Feld | Bedeutung |
|---|---|
| `stats` | pro Aufgabe (Mal: kleinerer Faktor zuerst, z. B. `7x8`; Geteilt: `d<Teiler>x<Ergebnis>`, z. B. `d4x5` für `20 : 4`; Lücke: `l<gezeigter Faktor>x<fehlender Faktor>`, z. B. `l4x5` für `4 × ? = 20`): `r` richtig, `w` falsch, `ms` durchschnittliche Zeit, `tn` Zahl der Zeitmessungen |
| `log` | jede einzelne Antwort mit Zeitpunkt, Aufgabe (`7x8`, `42:7` oder `4x?=20`), Eingabe, richtig oder falsch und Dauer in ms (die letzten 5000) |
| `stars` | gesammelte Diamanten |
| `streak` | `cur` aktueller Streak, `best` Rekord, `last` letztes gezähltes Datum, `jokers` vorhandene Joker, `broke` war der Streak schon einmal gerissen |

Die Profilliste liegt unter dem Schlüssel `einmaleins-profiles`:

```json
{ "list": [ { "id": "k3x9ab", "name": "Max" } ], "last": "k3x9ab" }
```

Die Daten gelten pro Profil, Gerät, Browser und Adresse. Wer die Browserdaten löscht, ein anderes Gerät nutzt oder die Seite unter anderer Adresse veröffentlicht, beginnt bei null.

## Anpassen

Alle Regeln stehen im Skript von `index.html`:

| Was | Wo |
|---|---|
| Aufgaben pro Runde | Konstante `ROUND` |
| Ab welchem Level Geteilt-Aufgaben kommen und wie viele | Konstante `DIV_FROM`, Funktion `divCount()` |
| Ab welchem Level Lückenaufgaben kommen und wie viele | Konstante `GAP_FROM`, Funktion `gapCount()` |
| Profile (Auswahl, Anlegen, Wechseln) | `showWho()`, `addProfile()`, `selectProfile()`, `start()` |
| Gewichtung der Aufgaben | Funktion `weight` in `newRound()` |
| Tempo-Messung (Obergrenze 15 s, Durchschnitt) | in `submit()` |
| Basiszeit und Langsamkeit | `baseMs()` und `slowness()` |
| Schwelle für "nicht flott" | `isSlow` |
| Regel für die Kartenfarben | `cellState()` |
| Level-Schwellen und Titel | Konstante `LEVELS` |
| Streak-Meilensteine | Konstante `MILE` |
| Joker-Regel | `updateStreak()`, Zeile mit `st.cur % 7 === 0 && st.jokers < 2` |

## Bekannte Grenzen

- Die Streak-Zählung beruht auf der Gerätezeit. Wird die Uhr verstellt, wird der Streak entsprechend gewertet.
- Mehrere Geräte haben getrennte Stände. Ein gemeinsamer Stand bräuchte ein Backend.
- Die Zeitmessung enthält auch die Tipp-Zeit. Das ist gewollt, denn sie gehört zum flotten Antworten.
