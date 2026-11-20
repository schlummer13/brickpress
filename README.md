# BrickPress

**Automatische PDF-Bauanleitungen, Produktrenderings und Ambiente-Fotos aus LDraw-3D-Modellen – entwickelt für die Gründung von Brickton.**

BrickPress entsteht als zentrales Software-Projekt für die Gründung der neuen Firma **Brickton**. Ziel ist es, aus einem fertigen Klemmbausteinmodell im **LDraw-Format** (`.ldr`, `.mpd` oder Studio-`.io`-Export) vollautomatisch eine druckfertige PDF-Bauanleitung inklusive strukturierter Teileliste, fotorealistischer Studioansichten, maßstabsgetreuer Ambiente-Fotos und 360°-Drehvideo zu erzeugen.

Alle Bauschritte, Unterbaugruppen, Kamerawinkel und Explosionsansichten werden **ausschließlich deterministisch und geometrisch** berechnet – ohne generative KI.

> **Hinweis:** Dieses Repository dient als **Showcase** und enthält ausschließlich die 3D-Ausgangsdatei sowie die generierten Ergebnisse für das Beispielmodell **Apple Macintosh 1984** (Bauanleitung, Renderings, Vorschaubilder und strukturierte Schrittdaten). Der Quellcode der Engine ist hier bewusst nicht enthalten.

---

<p align="center">
  <img src="apple-macintosh-1984/Apple%20Macintosh%201984_produktbilder/ambiente_sideboard.jpg" alt="Apple Macintosh 1984 auf Sideboard" width="100%" />
</p>

---

## Was BrickPress automatisch erzeugt

Aus einer einzigen 3D-LDraw-Datei generiert die Pipeline:

1. **Druckfertige PDF-Bauanleitung**
   - Deckblatt, automatisch erkannte Abschnitte (z. B. Hauptmodell, Maus, Tastatur) mit eigenen Kapitelseiten, Abschlussseite und vollständiger Teileliste.
   - Deterministische Bauschritt-Reihenfolge auf Basis eines geprüften Verbindungsgraphen (Noppen, Bars, Pins, Achsen) und linearer Kollisionsprüfung entlang der Fügepfade.
   - Automatische Bildung stabiler Unterbaugruppen sowie optimierte Kamerawahl mit Explosionsansichten und Fügepfeilen.
   - Teileliste mit Vorschaubildern, Stückzahlen, BrickLink-Teilenummern, kompakten Teilenamen und Farben – geometrisch identische Teilevarianten werden automatisch zusammengefasst.
2. **Fotorealistische Produktbilder & Videos (Blender Cycles / AgX)**
   - **10 Studioansichten** auf weißem Hintergrund (vorne, hinten, links, rechts, schräg, oben, unten).
   - **3 Ambiente-Fotos** in maßstabsgetreuen, prozedural erzeugten Umgebungen (Sideboard, Glasvitrine, Bücherregal).
   - **360°-Drehvideo** in Full HD (`drehung.mp4`).
3. **Einzelbilder & Maschinenlesbare Anleitung (JSON)**
   - Alle Schritt-, Teile- und Baugruppen-Renderings als einzelne PNGs (`Apple Macintosh 1984_images/`).
   - Vollständige strukturierte Beschreibung aller Schritte, Teile, Kameras, Fügerichtungen und Validierungsdaten (`Apple Macintosh 1984.json`).

---

## Techniken & Programmiersprachen

### Programmiersprachen
- **Python (3.12+):** Hauptanwendung für LDraw-Parsing, Geometrie- und Verbindungsanalyse, Montageplanung, Kamera- und Explosions-Solver, PDF-Layout sowie Steuerung der Rendering-Pipeline.
- **Rust:** Native Performance-Erweiterung (eingebunden über **PyO3** und parallelisiert mit **rayon**) für rechenintensive Geometriekerne – insbesondere Pfad-Kollisionstests, Strahlbündel, räumliche Hash-Gitter und gerichtete Blockier-Graphen.

### Eingesetzte Verfahren & Technologien
- **Geometrieanalyse & Kollisionserkennung:**
  - Automatische Merkmalserkennung direkt aus der 3D-LDraw-Geometrie (Noppen, Anti-Noppen, Bars, Pins, Achsen und Clips) mittels Strahlparität, radialen Grip-Tests und SVD-Orthonormalisierung numerischer Transformationsmatrizen.
  - Exakte lineare Montagepfad-Kollisionsprüfung über das **Separating Axis Theorem (SAT)** auf orientierten Kollisionsquadern (OBB) sowie räumliche Hash-Gitter zur Beschleunigung.
  - Geometrische Tiefenkarten-Vergleiche zur automatischen Zusammenfassung formgleicher Teilevarianten in der Teileliste.
- **Deterministische Montageplanung:**
  - **Assembly-by-Disassembly** mit gerichteten Blockier-Graphen (nach Wilson & Latombe) sowie Graph-Analyse (**NetworkX**), Beam Search mit Forward Checking und Tiefensuche (DFS).
  - Physikalische Stabilitäts- und Überhangsprüfung, automatische Zerlegung mehrteiliger Modelle in logische Abschnitte sowie Erkennung wiederverwendbarer Unterbaugruppen.
- **3D-Rendering & Visualisierung:**
  - **Blender (headless)** mit **Cycles** und **AgX**-Farbmanagement für fotorealistische Studio-Renderings, prozedurale maßstabsgetreue Ambiente-Szenen (mit Light Linking und Tiefenschärfe) sowie 360°-Videos.
  - **ModernGL (OpenGL)** als schneller Fallback-Renderer für technische Schrittansichten.
  - Automatischer Kamera-Solver zur Vermeidung verdeckter Einbaustellen sowie Berechnung von Explosionsabständen und Fügepfeilen.
- **PDF-Erzeugung & Python-Ökosystem:**
  - **ReportLab** und **fontTools** für druckfertige PDF-Bauanleitungen mit eingebetteter Markenschrift (Subset-Embedding).
  - **NumPy**, **Pydantic**, **Pillow** und **Typer** für numerische Berechnungen, Datenmodelle, Bildverarbeitung und CLI.

---

## Beispielmodell: Apple Macintosh 1984

📄 **[Bauanleitung als PDF öffnen (60 Seiten, 5,6 MB)](apple-macintosh-1984/Apple%20Macintosh%201984.pdf)**
🧱 **[3D-Ausgangsdatei (LDraw – apple_macintosh_1984.ldr)](apple-macintosh-1984/apple_macintosh_1984.ldr)**
🎥 **[360°-Produktvideo (drehung.mp4)](apple-macintosh-1984/Apple%20Macintosh%201984_produktbilder/drehung.mp4)**
📋 **[Maschinenlesbare Anleitungsdaten (JSON)](apple-macintosh-1984/Apple%20Macintosh%201984.json)**

### Kennzahlen

| Eigenschaft | Wert |
|---|---|
| **Teile gesamt** | 242 |
| **Einzigartige Elemente** | 73 |
| **Bauschritte** | 98 |
| **PDF-Seiten** | 60 (A4) |
| **Erkannte Abschnitte** | 3 – Apple Macintosh 1984 (217 Teile), Mouse (6 Teile), Keyboard (19 Teile) |
| **Geprüfte Verbindungen** | 687 Noppen-Verbindungen, 2 Bar-Verbindungen |
| **Warnungen / Fehler** | 0 Warnungen, 0 Fehler, 0 Layoutprobleme |

---

## Galerie

### Ambiente-Renderings

Alle Möbel und Requisiten werden prozedural im echten Maßstab erzeugt, sodass die tatsächliche Größe des Klemmbausteinmodells realistisch wirkt.

| Glasvitrine | Bücherregal |
|---|---|
| ![Apple Macintosh 1984 in Glasvitrine](apple-macintosh-1984/Apple%20Macintosh%201984_produktbilder/ambiente_vitrine.jpg) | ![Apple Macintosh 1984 im Bücherregal](apple-macintosh-1984/Apple%20Macintosh%201984_produktbilder/ambiente_regal.jpg) |

### Studioansichten

| Vorne links oben | Frontalansicht | Rückansicht |
|---|---|---|
| ![Vorne links oben](apple-macintosh-1984/Apple%20Macintosh%201984_produktbilder/ansicht_05_vorne_links_oben.jpg) | ![Frontalansicht](apple-macintosh-1984/Apple%20Macintosh%201984_produktbilder/ansicht_01_vorne.jpg) | ![Rückansicht](apple-macintosh-1984/Apple%20Macintosh%201984_produktbilder/ansicht_02_hinten.jpg) |

Alle 10 Studioansichten (inkl. Seiten-, Schräg-, Drauf- und Unteransicht) befinden sich im Ordner [`apple-macintosh-1984/Apple Macintosh 1984_produktbilder/`](apple-macintosh-1984/Apple%20Macintosh%201984_produktbilder/).

### Bauanleitung: Deckblatt, Explosionsansicht & Unterbaugruppe

| Deckblatt-Rendering | Bauschritt 45 (Explosionsansicht) | Unterbaugruppe (Monitor) |
|---|---|---|
| ![Cover](apple-macintosh-1984/Apple%20Macintosh%201984_images/cover.png) | ![Schritt 45](apple-macintosh-1984/Apple%20Macintosh%201984_images/step_045.png) | ![Monitor-Baugruppe](apple-macintosh-1984/Apple%20Macintosh%201984_images/assembly_monitor.png) |

---

## Inhalt dieses Repositories

```text
.
├── README.md
└── apple-macintosh-1984/
    ├── apple_macintosh_1984.ldr              # LDraw-3D-Ausgangsdatei des Modells
    ├── Apple Macintosh 1984.pdf              # Druckfertige Bauanleitung (60 Seiten)
    ├── Apple Macintosh 1984.json             # Strukturierte Anleitungs- und Schrittdaten
    ├── Apple Macintosh 1984_produktbilder/   # 10 Studioansichten, 3 Ambiente-Fotos & 360°-Video
    └── Apple Macintosh 1984_images/          # Alle 98 Schritt-, 73 Teile-, 8 Baugruppen- & Cover-Renderings
```

---

## Hinweise

- Die verwendeten 3D-Teilegeometrien basieren auf der **LDraw Parts Library** (CCAL 2.0 / CC BY 4.0).
- Dieses Projekt steht in keiner Verbindung zu Apple Inc. oder der LEGO Group.
