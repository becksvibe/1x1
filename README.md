# Einmaleins-Trainer: Streak

Der Streak zählt, an wie vielen Tagen **hintereinander** geübt wurde. Er belohnt Regelmäßigkeit statt Menge.

## Regeln

**Was zählt:** Eine abgeschlossene Runde (10 Aufgaben) pro Kalendertag, auch am Wochenende. Weitere Runden am selben Tag ändern den Streak nicht, sie bringen nur Diamanten.

**Tageswechsel:** Maßgeblich ist das Datum auf dem Gerät (lokale Zeit, Wechsel um Mitternacht).

| Situation beim Abschluss einer Runde | Ergebnis |
|---|---|
| Heute schon gezählt | Nichts ändert sich |
| Letzte Runde war gestern | Streak +1 |
| Tage verpasst, genug Joker vorhanden | Pro verpasstem Tag wird 1 Joker verbraucht, der Streak läuft weiter (+1) |
| Tage verpasst, zu wenig Joker | Streak startet neu bei 1 |
| Erste Runde überhaupt | Streak = 1 |

Level, Diamanten, Landkarte und Rekord bleiben bei einem Neustart des Streaks erhalten.

## Joker

- Alle 7 Streak-Tage (7, 14, 21, …) gibt es 1 Joker.
- Es können maximal 2 Joker gleichzeitig gesammelt werden.
- Joker werden automatisch eingesetzt, wenn ein Tag verpasst wurde.

## Meilensteine

| Streak-Tage | Bonus |
|---|---|
| 3 | +5 💎 |
| 7 | +10 💎 |
| 14 | +20 💎 |
| 30 | +50 💎 |

## Anzeige

- **Oben:** `🔥` mit der aktuellen Zahl. Wäre der Streak ohne Joker gerissen, steht dort schon beim Öffnen `0`.
- **Rundenende:** aktueller Streak (mit Rekord, falls höher), dazu Meldungen wie Joker eingesetzt, Joker verdient, Meilenstein-Bonus oder neuer Rekord. "Neuer Rekord" erscheint nur, wenn der Streak nach einem Abbruch einen früheren Bestwert übertrifft.

## Datenspeicherung

Alles liegt im `localStorage` des Browsers unter dem Schlüssel `einmaleins-v1`, im Feld `streak`:

```json
{ "cur": 5, "best": 9, "last": "2026-11-20", "jokers": 1, "broke": false }
```

| Feld | Bedeutung |
|---|---|
| `cur` | aktueller Streak in Tagen |
| `best` | Rekord |
| `last` | Datum der letzten gezählten Runde (`JJJJ-MM-TT`) |
| `jokers` | vorhandene Joker (0 bis 2) |
| `broke` | war der Streak schon einmal gerissen (steuert die Meldung "Neuer Rekord") |

Der Streak gilt pro Gerät, Browser und Adresse. Wer die Browserdaten löscht oder ein anderes Gerät nutzt, beginnt bei null. Wird die Datei unter einer anderen Adresse veröffentlicht, ist der Fortschritt ebenfalls getrennt.

## Anpassen

Alle Streak-Regeln stehen im Skript von `index.html`:

| Was | Wo |
|---|---|
| Meilensteine und Boni | Konstante `MILE` |
| Joker-Regel (alle 7 Tage, maximal 2) | in `updateStreak()`, Zeile mit `st.cur % 7 === 0 && st.jokers < 2` |
| Anzeige oben (0 bei gerissenem Streak) | `shownStreak()` |
| Meldungen am Rundenende | `updateStreak()` |

## Bekannte Grenzen

- Die Zählung beruht auf der Gerätezeit. Wird die Uhr verstellt, wird der Streak entsprechend gewertet.
- Mehrere Geräte haben getrennte Streaks. Ein gemeinsamer Stand bräuchte ein Backend.
