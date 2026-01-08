# 04.X – Feldmatrix: Warum Felder (nicht) editierbar oder Pflicht sind

Dieses Kapitel beschreibt die **Regeln für Eingabebereitschaft (editierbar)** und **Pflichtfelder** im **Editor** und in der **Gruppenbearbeitung**.

Es gilt als Referenzkapitel: Wenn ein Benutzer an einer Stelle fragt „Warum kann ich das nicht ändern?“ oder „Warum ist das Pflicht?“, ist die Antwort hier zu finden.

---

## 1. Grundprinzip

Die Feldlogik hängt von zwei Dingen ab:

1) **Workflow-Status des Dokuments** (z. B. *Posteingang/WaitingForOrder*, *Frist erfassen/DeadlineEntriesOrdered*, *Dokument ablegen ohne Fristen/StoreInDossierOrderedNoDeadlines*, …)  
2) **Rolle des aktuellen Benutzers** bezogen auf dieses Dokument (z. B. *Ersteller*, *Adressat*, ggf. *Fristverantwortlicher*)

### Feldzustände

- **Deaktiviert**: nicht editierbar, nicht Pflicht  
- **Editierbar**: editierbar, nicht Pflicht  
- **Pflichtfeld**: editierbar und muss befüllt sein

> Technisch wird das über `EditorFieldAttributes` gesteuert: `IsEnabled` und `IsRequired`.

---

## 2. Rollen (UserRoleFlags)

Für die Matrix relevant sind:

- **Ersteller** (`WorkflowCreator`)
- **Adressat** (`WorkflowOrderAddressee`)
- **Fristverantwortlicher** (`SingleDeadlineResponsible`) – wird nur für bestimmte Fristfelder benötigt

---

## 3. Workflow-Status-Gruppen (vereinfachte Sicht)

Die tatsächlichen `DocumentDeadlineWorkflowState` werden intern auf folgende Feld-Matrix-Zustände abgebildet:

| Workflowstatus (vereinfacht) | Technischer FieldState | Typische Kanban-Phase |
|---|---|---|
| *Warten / Posteingang* | `WaitingForOrder` | Posteingang / Klassifikation |
| *Frist erfassen* | `DeadlineEntriesOrdered` | Frist erfassen |
| *Fristen zugewiesen / fachlich „gesperrt“* | `DeadlinesAssigned` | Frist prüfen (Editor weitgehend read-only) |
| *Dokument ablegen (ohne Fristen)* | `StoreInDossierOrderedNoDeadlines` | Dokument ablegen (ohne Fristen) |
| *Sonstige / abgeschlossen* | `Default` | i. d. R. read-only (außer Kommentar/Dokument) |

---

## 4. Feldnamen und Beschriftungen

Die folgenden Felder werden in der Matrix verwendet (Label gemäß `EditorFieldLabelProvider`):

- **Akte** (`Dossier`)
- **Register** (`FilingTray`)
- **Ersteller** (`Creator`)
- **Adressat** (`Addressee`)
- **Beschreibung** (`Description`)
- **Textvorlage** (`TextTemplate`)
- **Dokument** (`Document`)
- **Neuer Kommentar** (`Comment`)
- **Fristen** (`DeadlineSearch`, `Deadlines`)

Nicht aufgeführte Felder gelten in den untenstehenden Zuständen als **deaktiviert**, sofern nicht anders angegeben.

---

# 5. Feldmatrix: Editor (Einzeldokument)

Legende in den Tabellen:
- ✅ = editierbar
- ⭐ = Pflichtfeld
- — = deaktiviert

## 5.1 Status: Warten / Posteingang (FieldState: WaitingForOrder)

| Feld | Ersteller | Adressat | Hinweis |
|---|---:|---:|---|
| Akte | ✅ | ✅ | editierbar für Ersteller **oder** Adressat |
| Register | ✅ | ✅ | editierbar für Ersteller **oder** Adressat |
| Adressat | ⭐ | — | Pflicht **nur** für Ersteller |
| Ersteller | ⭐ | — | Pflicht **nur** für Ersteller |
| Beschreibung | ✅ | ✅ | editierbar für Ersteller **oder** Adressat |
| Textvorlage | ✅ | — | editierbar nur für Ersteller |
| Dokument | ✅ | ✅ | immer editierbar |
| Neuer Kommentar | ✅ | ✅ | immer editierbar |

## 5.2 Status: Frist erfassen (FieldState: DeadlineEntriesOrdered)

| Feld | Ersteller | Adressat | Hinweis |
|---|---:|---:|---|
| Akte | ⭐ | ⭐ | Pflicht für Ersteller **oder** Adressat |
| Register | ⭐ | ⭐ | Pflicht für Ersteller **oder** Adressat |
| Beschreibung | — | ✅ | editierbar nur für Adressat |
| Fristen (Suche) | ⭐ | ⭐ | Pflicht für Ersteller **oder** Adressat |
| Fristen (Liste) | ⭐ | ⭐ | Pflicht für Ersteller **oder** Adressat |
| Textvorlage | — | ✅ | editierbar nur für Adressat |
| Dokument | ✅ | ✅ | immer editierbar |
| Neuer Kommentar | ✅ | ✅ | immer editierbar |

## 5.3 Status: Frist prüfen (FieldState: DeadlinesAssigned)

> In diesem Status ist der Editor bewusst weitgehend **read-only**, um die fachliche Prüfung zu stabilisieren.

| Feld | Ersteller | Adressat | Hinweis |
|---|---:|---:|---|
| Dokument | ✅ | ✅ | immer editierbar |
| Neuer Kommentar | ✅ | ✅ | immer editierbar |
| (alle anderen) | — | — | deaktiviert |

## 5.4 Status: Dokument ablegen (ohne Fristen) (FieldState: StoreInDossierOrderedNoDeadlines)

| Feld | Ersteller | Adressat | Hinweis |
|---|---:|---:|---|
| Akte | — | ⭐ | Pflicht nur für Adressat |
| Register | — | ⭐ | Pflicht nur für Adressat |
| Dokument | ✅ | ✅ | immer editierbar |
| Neuer Kommentar | ✅ | ✅ | immer editierbar |

## 5.5 Status: Default (FieldState: Default)

| Feld | Ersteller | Adressat | Hinweis |
|---|---:|---:|---|
| Dokument | ✅ | ✅ | immer editierbar |
| Neuer Kommentar | ✅ | ✅ | immer editierbar |
| (alle anderen) | — | — | deaktiviert |

---

# 6. Gruppenbearbeitung (Gruppenkarte)

In der Gruppenbearbeitung gelten zwei Regeln:

1) **Die Logik pro Dokument ist identisch** zur Editor-Matrix oben.  
2) Ein Feld ist in der Gruppenbearbeitung **editierbar**, wenn es **für mindestens ein Dokument der Gruppe** editierbar ist.  
   (Praktisch: *mindestens eine Tabellenzeile erlaubt Eingabe* → Gruppenfeld aktiv.)

### Konsequenz

- Sind alle Dokumente in der Gruppe für ein Feld deaktiviert → Feld bleibt in der Gruppenbearbeitung deaktiviert.
- Ist das Feld bei wenigstens einem Dokument aktiv → Feld wird in der Gruppenbearbeitung aktiv (und wirkt dann auf die betroffenen Dokumente).

> Hinweis: Pflichtlogik in der Gruppenbearbeitung ist in der Regel „mindestens ein Dokument benötigt es“.  
> In der UI sollte klar sein, *für welche Dokumente* eine Pflicht besteht.