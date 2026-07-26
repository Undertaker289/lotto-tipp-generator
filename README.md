# Lotto-Tipp-Generator – 6 aus 45

Erzeugt Zufallstipps für das österreichische Lotto "6 aus 45", die statistisch **seltener von anderen Spielern gewählt werden** – reduziert also das Risiko, einen Gewinn teilen zu müssen.

**Live-Version:** https://undertaker289.github.io/lotto-tipp-generator/

## Die wichtigste Klarstellung zuerst

Nichts hier verändert deine **Gewinnwahrscheinlichkeit**. Jede der 8.145.060 möglichen Kombinationen ist exakt gleich wahrscheinlich (1 : 8.145.060). Was sich verändert, ist nur **wie viel** du bekommst, *falls* du gewinnst – weil der Gewinntopf jedes Rangs unter allen Gewinnern mit denselben Zahlen aufgeteilt wird.

## Warum manche Zahlen unpopulärer sind

Menschen tippen nicht zufällig. Dokumentierte Muster:

- **Geburtstage:** Zahlen 1–31 sind massiv übergewichtet, 1–12 doppelt (Tag *und* Monat als möglicher Treffer).
- **Glückszahlen:** 7 mit Abstand am beliebtesten, dann 3 und 9 (von den Österreichischen Lotterien selbst bestätigt).
- **Geometrische Muster** auf dem Tippschein: Reihen, Spalten, Diagonalen. Der gefährlichste Faktor – am 14. Jänner 1995 teilten sich in Großbritannien **133 Gewinner** einen Jackpot, weil die gezogenen Zahlen zufällig einem beliebten Schein-Muster entsprachen.
- **Arithmetische Folgen** (1-2-3-4-5-6, Vielfache von 5 oder 7): historisch die meistgespielten Kombinationen überhaupt.
- **Ironie der "klugen" Wahl:** Wer bewusst "zufällig aussehende" Zahlen sucht, meidet unbewusst Nachbarzahlen – dabei enthalten **~53 % aller echten Ziehungen mindestens ein Nachbarpaar**.

Realbeispiel: Am 10. April 1999 wurden in Deutschland 2-3-4-5-6-26 gezogen. Rund **38.000 Spieler** hatten fünf Richtige (2,3,4,5,6), weil zehntausende die Reihe 1–6 oder 2–7 getippt hatten – ein Fünferrang, der normalerweise fünfstellig auszahlt, brachte da nur ein paar hundert DM.

## Wie der Generator das umsetzt

Feste Regeln (reines Rejection-Sampling – die Ziehung selbst ist uniform, nicht gewichtet):

- höchstens 2 Zahlen im Geburtstagsbereich (≤ 31) – wird beim überschneidungsfreien Ziehen schrittweise gelockert (3, 4, … bis 6), falls der Pool an Zahlen > 31 sonst zu früh aufgebraucht wäre, damit mehr Tipps ohne Überschneidungen möglich bleiben
- Summe der 6 Zahlen ≥ 150 (Ziehungsmittel liegt bei 138, Geburtstagstipps typischerweise bei 100–120)
- mindestens ein Nachbarpaar (z. B. 43/44) – bewusstes Gegensignal zu "will zufällig aussehen"
- keine arithmetische 3er-Folge (z. B. 27-34-41)
- keine reinen Primzahlen-Tipps (Ausschluss nur, wenn alle 6 Zahlen Primzahlen sind – bis zu 5 Primzahlen sind weiterhin erlaubt; reine Primzahlen-Systeme sind selbst ein beliebtes Tippmuster)
- keine zwei exakt identischen Tipps
- so viele Tipps wie möglich überschneidungsfrei, danach mit möglichen Überschneidungen (aber weiterhin ohne Duplikate)

Die Zahlen 3, 7 und 9 werden dabei **nicht** ausgeschlossen oder bei der Ziehung benachteiligt – sie fließen nur in die rein informative Popularitätsschätzung unten ein (2,5× Faktor pro Zahl).

## "Popularität" und "erwarteter Topf-Anteil" im Interface

**Popularität** (z. B. "1,8× Durchschnitt") schätzt, wie oft andere Spieler genau diese 6 Zahlen wählen würden, verglichen mit einem reinen Zufallstipp. Das ist eine Schätzung auf Basis bekannter Tippmuster (siehe oben), **keine gemessene Verkaufszahl** – die Richtung ist verlässlicher als die exakte Zahl. "1,0× = durchschnittlich" im Interface ist als grober Orientierungspunkt gedacht, nicht als kalibrierter Mittelwert: In der aktuellen Formel bekommen Zahlen > 31 keinen Malus, aber auch keinen Bonus, wodurch ein tatsächlich uniform zufällig gezogener Tipp im Schnitt eher bei 6–7× landet statt bei 1×. Die relative Aussage – niedrige/beliebte Zahlen erhöhen den Faktor, hohe/unauffällige senken ihn – stimmt trotzdem.

**Erwarteter Topf-Anteil** ist die eigentlich relevante Größe: Falls der Tipp den Sechser trifft, welchen Anteil am Gewinntopf bekommst du im Schnitt, nachdem er mit eventuellen Mitgewinnern geteilt wurde?

Modell dahinter: ~7 Mio abgegebene Tipps pro Runde, davon geschätzt ~35 % reine Quicktipps (gleichmäßig über alle Kombinationen verteilt). Die Anzahl der Mitgewinner wird als Poisson-verteilt angenommen mit λ = N·p; der erwartete Anteil ergibt sich aus (1 − e⁻λ) / λ.

Die Quicktipp-Menge bildet einen Sockel, den keine Zahlenwahl unterschreiten kann – selbst der bestmögliche vom Generator erzeugte Tipp landet realistisch nie über ~73 % erwartetem Anteil. Der Popularitätsfaktor ist im Code außerdem hart auf maximal 50× gedeckelt (`Math.min(50, …)`), auch wenn die rohe, ungedeckelte Rechnung für extreme Muster weit darüber liegen würde. Tatsächlich simulierte Größenordnungen (Median über mehrere tausend Stichproben je Kategorie):

| Tipp-Art | Popularitätsfaktor (typisch) | erwarteter Topf-Anteil |
|---|---|---|
| optimierter Tipp (Generator-Output) | ~0,7–3× (Median ~1,3×) | ~50–73 % |
| Zufallstipp / Quicktipp (uniform aus 45) | ~2–27× (Median ~6×) | ~5–50 % |
| typischer Geburtstagstipp (6 Zahlen aus 1–31) | ~7–50×, oft am Deckel | ~3–24 % |
| Muster-Tipp (Reihe/Diagonale/Vielfache) | ~5–18× | ~10–33 % |
| 1-2-3-4-5-6 | 50× (Deckel; ungedeckelt ≈ 85×) | ~3,5 % |

## Grenzen der Methode

- Der Erwartungswert eines Lottotipps bleibt trotzdem klar negativ – nur rund die Hälfte des Einsatzes wird überhaupt ausgeschüttet. Diese Strategie optimiert den *bedingten* Gewinn, nicht die Verlustquote.
- Exakte Popularitätsverteilungen für Österreich sind nicht öffentlich einsehbar; das Modell stützt sich auf internationale Studien (u. a. Henze & Riedwyl zum deutschen 6 aus 49) plus offizielle Aussagen der Österreichischen Lotterien zu den meistgespielten Einzelzahlen.
- Der Nutzen skaliert mit der Jackpot-Größe: Je mehr Gelegenheitsspieler (= mehr Geburtstagstipps) mitspielen, desto mehr bringt die Strategie.

## Technik

Eine einzelne, abhängigkeitsfreie `index.html` (Vanilla JS, kein Build-Prozess). Läuft vollständig im Browser, keine Serverkomponente, kein Tracking, keine eigene Datenerfassung. Einzige externe Anfrage ist der Google-Fonts-Ladevorgang (`fonts.googleapis.com`) für die Schriftarten.

### Reproduzierbarkeit mit Seed & Verlauf

Das **Seed-Feld** ermöglicht deterministische Tipp-Generierung: Dieselbe Seed-Zahl erzeugt immer dieselben Tipps.

**Wie es funktioniert:**
- **Leeres Feld:** Bei jedem Klick auf "Tipps generieren" wird ein neuer zufälliger Seed verwendet (aber nicht ins Feld eingetragen) – so liefert jeder Klick frische Tipps
- **Manuell:** Du kannst selbst eine Seed-Zahl eingeben (z.B. `12345`), um sie zu fixieren
- **Resultat:** Mehrfaches Klicken mit **gleichem, manuell eingetragenem Seed** = **identische Tipps**

**Verlauf:**
- Jede Generierung wird automatisch mit Seed, Tipps und Datum gespeichert (bis zu 50 Einträge)
- Der Verlauf wird in `localStorage` gespeichert und bleibt über Browser-Neuladen erhalten
- **Eintrag anklicken** = Tipps reproduzieren
- **Verlauf löschen** Button = Kompletten Verlauf löschen (mit Bestätigung)

**Praktische Anwendungen:**
- **Tipps archivieren:** Alle generierten Tipps sind im Verlauf → einfach klicken zum Reproduzieren
- **Mit Freunden teilen:** "Nutze Seed `9999`, dann hast du die gleichen Tipps wie ich"
- **Interessante Kombinationen:** Findest du einen guten Tipp? Eintrag klicken, um alle Infos zu sehen
- **Vergleichen:** Alte Tipps jederzeit verfügbar im Verlauf

**Technische Implementierung:** 
- Seed-Generator: Deterministischer Pseudozufallsgenerator (seeded RNG mit Math.sin-Basis), nicht kryptographisch sicher aber für Lotto-Tipps ausreichend
- Verlauf: Gespeichert in `localStorage` (max. 50 Einträge, älteste werden automatisch entfernt)

## Lizenz

MIT – siehe [LICENSE](LICENSE).
