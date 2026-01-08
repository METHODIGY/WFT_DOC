# 04.X – Feldmatrix: Warum Felder (nicht) editierbar oder Pflicht sind

Dieses Kapitel beschreibt die **Regeln für Eingabebereitschaft (editierbar)** und **Pflichtfelder** im **Editor** und in der **Gruppenbearbeitung**.

Es gilt als Referenzkapitel: Wenn ein Benutzer an einer Stelle fragt „Warum kann ich das nicht ändern?“ oder „Warum ist das Pflicht?“, ist die Antwort hier zu finden.

---

## 1. Grundprinzip

Die Feldlogik hängt von zwei Dingen ab:

1) **Workflow-Status des Dokuments**  
2) **Rolle des aktuellen Benutzers** bezogen auf dieses Dokument (z. B. *Ersteller*, *Adressat*, ggf. *Fristverantwortlicher*)

### Feldzustände

- **Deaktiviert**: nicht editierbar, nicht Pflicht  
- **Editierbar**: editierbar, nicht Pflicht  
- **Pflichtfeld**: editierbar und muss befüllt sein

---

## 2. Rollen (UserRoleFlags)

Für die Matrix relevant sind:

- **Ersteller** (`WorkflowCreator`)
- **Adressat** (`WorkflowOrderAddressee`)
- **Fristverantwortlicher** (`SingleDeadlineResponsible`) – wird nur für bestimmte Fristfelder benötigt

---

## 3. Workflow-Status-Gruppen (vereinfachte Sicht)

| Workflowstatus (vereinfacht) | Technischer FieldState | Typische Kanban-Phase |
|---|---|---|
| *Warten / Posteingang* | `WaitingForOrder` | Posteingang / Klassifikation |
| *Frist erfassen* | `DeadlineEntriesOrdered` | Frist erfassen |
| *Fristen zugewiesen / fachlich „gesperrt“* | `DeadlinesAssigned` | Frist prüfen (Editor weitgehend read-only) |
| *Dokument ablegen (ohne Fristen)* | `StoreInDossierOrderedNoDeadlines` | Dokument ablegen (ohne Fristen) |
| *Sonstige / abgeschlossen* | `Default` | i. d. R. read-only (außer Kommentar/Dokument) |

---

# 4. Feldmatrix: Editor (Einzeldokument)

Legende:
- ✅ = editierbar
- ⭐ = Pflichtfeld
- — = deaktiviert

> Hinweis: In jeder Tabelle sind **alle Felder** aufgeführt (auch wenn sie deaktiviert sind), damit die Tabellen immer gleich aussehen.  
> Die Reihenfolge ist **an der Anordnung im Editor** orientiert.

## 4.1 Status: Warten / Posteingang (FieldState: WaitingForOrder)

| Feld | Ersteller | Adressat |
|---|---:|---:|
| Akte | ✅ | ✅ |
| Register | ✅ | ✅ |
| Ersteller | ⭐ | — |
| Adressat | ⭐ | — |
| Beschreibung | ✅ | ✅ |
| Dokument | ✅ | ✅ |
| Neuer Kommentar | ✅ | ✅ |

## 4.2 Status: Frist erfassen (FieldState: DeadlineEntriesOrdered)

| Feld | Ersteller | Adressat |
|---|---:|---:|
| Akte | ⭐ | ⭐ |
| Register | ⭐ | ⭐ |
| Ersteller | — | — |
| Adressat | — | — |
| Beschreibung | — | ✅ |
| Dokument | ✅ | ✅ |
| Neuer Kommentar | ✅ | ✅ |

## 4.3 Status: Frist prüfen (FieldState: DeadlinesAssigned)

> In diesem Status ist der Editor bewusst weitgehend **read-only**, um die fachliche Prüfung zu stabilisieren.

| Feld | Ersteller | Adressat |
|---|---:|---:|
| Akte | — | — |
| Register | — | — |
| Ersteller | — | — |
| Adressat | — | — |
| Beschreibung | — | — |
| Dokument | ✅ | ✅ |
| Neuer Kommentar | ✅ | ✅ |

## 4.4 Status: Dokument ablegen (ohne Fristen) (FieldState: StoreInDossierOrderedNoDeadlines)

| Feld | Ersteller | Adressat |
|---|---:|---:|
| Akte | — | ⭐ |
| Register | — | ⭐ |
| Ersteller | — | — |
| Adressat | — | — |
| Beschreibung | — | — |
| Dokument | ✅ | ✅ |
| Neuer Kommentar | ✅ | ✅ |

---

# 5. Gruppenbearbeitung (Gruppenkarte)

1) **Die Logik pro Dokument ist identisch** zur Editor-Matrix oben.  
2) Ein Feld ist in der Gruppenbearbeitung **editierbar**, wenn es **für mindestens ein Dokument der Gruppe** editierbar ist.

---

# 6. Ausblick: Fristenanlage & Aufgabenanlage

Diese Matrix-Struktur (Status × Rolle → enabled/required) wird später analog benötigt für:
- **Fristenanlage** (zusätzlich abhängig vom **Fristverantwortlichen**)
- **Aufgabenanlage** (zusätzliche Rollen/Verantwortlichkeiten)
