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

Feste Regeln (Rejection-Sampling, gewichtete Ziehung):

- keine Zahl unter 14
- höchstens 2 Zahlen im Geburtstagsbereich (≤ 31)
- Summe der 6 Zahlen ≥ 150 (Ziehungsmittel liegt bei 138, Geburtstagstipps typischerweise bei 100–120)
- mindestens ein Nachbarpaar (z. B. 43/44) – bewusstes Gegensignal zu "will zufällig aussehen"
- keine arithmetische 3er-Folge (z. B. 27-34-41)
- keine der Zahlen 3, 7, 9
- höchstens 3 Primzahlen (reine Primzahlen-Systeme sind selbst ein beliebtes Tippmuster)
- keine zwei exakt identischen Tipps
- so viele Tipps wie möglich überschneidungsfrei, danach mit möglichen Überschneidungen (aber weiterhin ohne Duplikate)

## "Popularität" und "erwarteter Topf-Anteil" im Interface

**Popularität** (z. B. "1,8× Durchschnitt") schätzt, wie oft andere Spieler genau diese 6 Zahlen wählen würden, verglichen mit einem reinen Zufallstipp. 1,0× = durchschnittlich. Das ist eine Schätzung auf Basis bekannter Tippmuster (siehe oben), **keine gemessene Verkaufszahl** – die Richtung ist verlässlicher als die exakte Zahl.

**Erwarteter Topf-Anteil** ist die eigentlich relevante Größe: Falls der Tipp den Sechser trifft, welchen Anteil am Gewinntopf bekommst du im Schnitt, nachdem er mit eventuellen Mitgewinnern geteilt wurde?

Modell dahinter: ~7 Mio abgegebene Tipps pro Runde, davon geschätzt ~35 % reine Quicktipps (gleichmäßig über alle Kombinationen verteilt). Die Anzahl der Mitgewinner wird als Poisson-verteilt angenommen mit λ = N·p; der erwartete Anteil ergibt sich aus (1 − e⁻λ) / λ.

Die Quicktipp-Menge bildet einen Sockel, den keine Zahlenwahl unterschreiten kann – selbst der bestmögliche Tipp landet realistisch nie über ~85 % erwartetem Anteil. Größenordnungen im Modell:

| Tipp-Art | Popularitätsfaktor | erwarteter Topf-Anteil |
|---|---|---|
| optimierter Tipp | ~0,15× | ~82–85 % |
| Zufallstipp / Quicktipp | 1× | ~67 % |
| typischer Geburtstagstipp | ~5× | ~30 % |
| Muster-Tipp (Reihe/Diagonale) | ~100×+ | < 2 % |
| 1-2-3-4-5-6 | ~2000× | < 0,1 % |

## Grenzen der Methode

- Der Erwartungswert eines Lottotipps bleibt trotzdem klar negativ – nur rund die Hälfte des Einsatzes wird überhaupt ausgeschüttet. Diese Strategie optimiert den *bedingten* Gewinn, nicht die Verlustquote.
- Exakte Popularitätsverteilungen für Österreich sind nicht öffentlich einsehbar; das Modell stützt sich auf internationale Studien (u. a. Henze & Riedwyl zum deutschen 6 aus 49) plus offizielle Aussagen der Österreichischen Lotterien zu den meistgespielten Einzelzahlen.
- Der Nutzen skaliert mit der Jackpot-Größe: Je mehr Gelegenheitsspieler (= mehr Geburtstagstipps) mitspielen, desto mehr bringt die Strategie.

## Technik

Eine einzelne, abhängigkeitsfreie `index.html` (Vanilla JS, kein Build-Prozess). Läuft vollständig im Browser, keine Serverkomponente, keine Datenübertragung.

## Lizenz

MIT – siehe [LICENSE](LICENSE).
