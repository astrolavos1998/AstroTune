# AstroTune 1.5beta

**A settings advisor for DCS World — reads your real config, knows your hardware, tells you what to change.**
**Σύμβουλος ρυθμίσεων για το DCS World — διαβάζει το πραγματικό σου αρχείο, ξέρει το μηχάνημά σου, σου λέει τι να αλλάξεις.**

`Windows 10+` · `Python 3.10–3.13` · `no dependencies` · `GR / EN` · `dark / light`

🇬🇧 [English](#english) · 🇬🇷 [Ελληνικά](#ελληνικά)

> **Preview is the default. The graphics driver is never modified. Nothing is written to DCS unless you explicitly ask.**
> **Η προεπισκόπηση είναι η προεπιλογή. Ο driver δεν πειράζεται ποτέ. Τίποτα δεν γράφεται στο DCS αν δεν το ζητήσεις ρητά.**

---

## English

### What it is

AstroTune is a single-file Python desktop app. It reads your actual `options.lua` and your
graphics driver profile, detects what hardware you are running, and produces a per-setting
recommendation for the goal you choose — on a monitor or in VR.

It is not a "just set these" list. Every suggestion starts from the values you have *right
now*, weighted by your card, CPU, VRAM, resolution and whether you fly multiplayer. The same
setting gets a different answer on 8GB than on 24GB.

- **39 DCS settings** with a measurable cost (36 graphics, 3 cockpit)
- **4 goals** — smooth motion, maximum FPS, maximum graphics, balanced — plus switches for
  multiplayer and low-level flight
- **44 GPUs, 27 CPUs, 12 headsets** in the database, plus automatic detection
- **Driver checklist** — 13 items for NVIDIA, 11 for AMD, split into Global and per-application
- **Read-only comparison** of your three DCS Custom slots against your active settings
- Greek and English, dark and light

### Safety

| | Read | Write |
|---|:---:|---|
| Driver profile database (NVAPI) | ✔ | **never** |
| Windows registry | ✔ | **never** |
| `Config\OptionsPresets\Custom*.lua` | ✔ | **never** |
| `options.lua` | ✔ | on request only |
| Monitor refresh rate | ✔ | only via the button |

When it does write to `options.lua` it refuses to run while DCS is open, takes a timestamped
backup first, shows the exact list of changes and waits for a yes, then replaces **only** the
value text after verifying the bytes at that position are exactly what it read. Anything it
does not recognise with confidence, it leaves alone.

The driver database is shared across every game on the machine. A bad write there would not
only break DCS, and there is no good reason to take that risk when the tool can simply tell
you what to click — so the NVAPI calls are read-only, by design.

### Install

**Installer** — run `AstroTune_1.5beta_Setup.exe`. No administrator rights, nothing written
to the registry, installs per-user by default.

**From source** — with Python 3.10 or newer:

```bash
python AstroTune_1_5beta.py
```

Only `tkinter` is required, which ships with Python on Windows. Optional:
`pip install nvidia-ml-py` adds a live VRAM readout and the exact PCIe generation.

### How to use it

1. **Monitor or VR?** — asked on every launch, because in VR *which* settings matter, *what*
   they cost, and the whole frame-cap strategy all change.
2. **Tab 1 · PC build** — press *Detect this PC*. Fix anything it could not find. VRR is not
   detectable: tick it only if you can see G-Sync **active** in the driver.
3. **Tab 2 · Graphics card** — the driver checklist, Global and DCS, read-only.
4. **Tab 3 · DCS settings** — pick a goal, read the table, then either keep the preview or
   apply.

Rows in orange will change. Rows in red are low confidence and arrive **unticked** — you
decide. A value the program cannot recognise is shown as "unknown" and no change is
suggested for it.

### Command line

```bash
python AstroTune_1_5beta.py --report [smooth|fps|quality|balanced] [--sp] [--low] [--vr] [--en]
```

| Switch | Meaning |
|---|---|
| `--sp` | Single player (multiplayer is assumed otherwise) |
| `--low` | Low level / helicopters |
| `--vr` | Compute for VR instead of a monitor |
| `--en` | English output |

An `AstroTune.exe` built with `--noconsole` has nowhere to print this. Use the `.py`, or
build a second exe with `--console`.

### Files it writes

| Path | What |
|---|---|
| `%USERPROFILE%\.astrotune.json` | Theme, language, goal, saved build. Delete it to start fresh. |
| `%USERPROFILE%\.astrotune_exports\` | Driver profile exports, plain text. Safe to delete. |
| `…\Saved Games\DCS*\Config\AstroTune_backups\` | Copies of `options.lua` before every write. **Keep these.** |

### Building the release

Put `build.bat` next to `AstroTune_1_5beta.py` and double-click it. It finds Python,
installs PyInstaller if missing, creates the folders the Inno script expects, builds the
exe and tells you what is still missing. Or do it by hand:

```bash
pip install pyinstaller
pyinstaller --onefile --noconsole --name AstroTune ^
            --distpath build --workpath build\tmp --specpath build ^
            AstroTune_1_5beta.py
```

Then compile `AstroTune_Setup.iss` with Inno Setup 6.3+. The script expects:

```
AstroTune_Setup.iss
README.md
build\AstroTune.exe
docs\AstroTune_Manual.html
dist\                      <- the finished Setup.exe lands here
```

AstroTune uses only the standard library, so no `--hidden-import` is needed. `--onefile`
builds commonly trip antivirus heuristics because they unpack to a temp folder on every run;
`--onedir` does not.

The installer wizard is English-only unless `Greek.isl` is present, because Greek is an
*unofficial* Inno Setup translation and is not bundled. Drop it into a `languages\` folder
next to the `.iss` (or into Inno's own `Languages\`) and the script picks it up
automatically. This affects the wizard only — the application itself is bilingual either way.

### What it doesn't know yet

Value mappings were verified field by field against real config files compared with DCS
screenshots: 33 of 33 correct. Two remain open, and the program says so instead of guessing:

- **DLSS Perf/Quality** — DCS stores it as text in `options.lua` but as an integer in the
  Custom slot files. The integer mapping is unconfirmed, so it shows as "unknown" there.
- **Terrain Objects Shadows** — only `0 = Default` is verified; the remaining steps follow
  the dropdown order but are unconfirmed.

### Credits

AstroTune — DCS 2026© for **LOCK-ON GREECE** by **=GR= Astr0**.
DCS World is a product of Eagle Dynamics. This project is not affiliated with or endorsed by
Eagle Dynamics or NVIDIA.

---

## Ελληνικά

### Τι είναι

Το AstroTune είναι ένα πρόγραμμα Python σε ένα αρχείο. Διαβάζει το πραγματικό σου
`options.lua` και το προφίλ του driver, αναγνωρίζει τι υλικό έχεις, και βγάζει πρόταση ανά
ρύθμιση για τον στόχο που διαλέγεις — σε οθόνη ή σε VR.

Δεν είναι λίστα «βάλε αυτά». Κάθε πρόταση ξεκινάει από τις τιμές που έχεις *τώρα*,
σταθμισμένες με την κάρτα, τον επεξεργαστή, τη VRAM, την ανάλυση και το αν παίζεις
multiplayer. Η ίδια ρύθμιση παίρνει άλλη απάντηση σε 8GB και άλλη σε 24GB.

- **39 ρυθμίσεις** του DCS με μετρήσιμο κόστος (36 graphics, 3 κόκπιτ)
- **4 στόχοι** — ομαλή κίνηση, μέγιστα FPS, μέγιστα γραφικά, ισορροπία — συν διακόπτες για
  multiplayer και χαμηλή πτήση
- **44 κάρτες, 27 επεξεργαστές, 12 headsets** στη βάση, συν αυτόματη ανίχνευση
- **Λίστα ελέγχου για τον driver** — 13 σημεία για NVIDIA, 11 για AMD, χωρισμένα σε Global
  και ανά παιχνίδι
- **Σύγκριση μόνο για ανάγνωση** των τριών θέσεων Custom του DCS με τις ενεργές ρυθμίσεις
- Ελληνικά και αγγλικά, σκούρο και ανοιχτό θέμα

### Ασφάλεια

| | Ανάγνωση | Εγγραφή |
|---|:---:|---|
| Βάση προφίλ του driver (NVAPI) | ✔ | **ποτέ** |
| Μητρώο των Windows | ✔ | **ποτέ** |
| `Config\OptionsPresets\Custom*.lua` | ✔ | **ποτέ** |
| `options.lua` | ✔ | μόνο κατόπιν αιτήματος |
| Refresh rate οθόνης | ✔ | μόνο με το κουμπί |

Όταν γράφει στο `options.lua`, αρνείται να το κάνει όσο τρέχει το DCS, κρατάει πρώτα
αντίγραφο με χρονοσήμανση, δείχνει ακριβώς τι θα αλλάξει και περιμένει «ναι», και μετά
αντικαθιστά **μόνο** το κείμενο της τιμής, αφού επαληθεύσει ότι τα bytes σε εκείνη τη θέση
είναι ακριβώς αυτά που διάβασε. Ό,τι δεν αναγνωρίζει με βεβαιότητα, δεν το αγγίζει.

Η βάση του driver είναι κοινή για όλα τα παιχνίδια του μηχανήματος. Μια λάθος εγγραφή εκεί
δεν θα χαλούσε μόνο το DCS, και δεν υπάρχει καλός λόγος να το ρισκάρει ένα εργαλείο που
μπορεί απλώς να σου πει τι να πατήσεις — γι' αυτό οι κλήσεις NVAPI είναι αποκλειστικά
ανάγνωσης.

### Εγκατάσταση

**Με installer** — τρέξε το `AstroTune_1.5beta_Setup.exe`. Δεν ζητάει δικαιώματα
διαχειριστή, δεν γράφει στο μητρώο, εγκαθίσταται μόνο για τον λογαριασμό σου.

**Από τον κώδικα** — με Python 3.10 ή νεότερη:

```bash
python AstroTune_1_5beta.py
```

Χρειάζεται μόνο το `tkinter`, που έρχεται μαζί με την Python στα Windows. Προαιρετικά, το
`pip install nvidia-ml-py` προσθέτει ζωντανή ένδειξη VRAM και ακριβή γενιά PCIe.

### Πώς χρησιμοποιείται

1. **Οθόνη ή VR;** — ρωτάει σε κάθε άνοιγμα, γιατί σε VR αλλάζουν *ποιες* ρυθμίσεις μετράνε,
   *πόσο* κοστίζουν, και ολόκληρη η στρατηγική του frame cap.
2. **Καρτέλα 1 · Σύνθεση PC** — πάτα *Ανίχνευση αυτού του PC* και διόρθωσε ό,τι δεν βρήκε.
   Το VRR δεν ανιχνεύεται: τσέκαρέ το μόνο αν βλέπεις το G-Sync **ενεργό** στον driver.
3. **Καρτέλα 2 · Κάρτα γραφικών** — η λίστα ελέγχου, Global και DCS, μόνο για ανάγνωση.
4. **Καρτέλα 3 · Ρυθμίσεις DCS** — διάλεξε στόχο, διάβασε τον πίνακα, και μετά κράτα την
   προεπισκόπηση ή εφάρμοσε.

Οι πορτοκαλί γραμμές αλλάζουν. Οι κόκκινες είναι χαμηλής βεβαιότητας και έρχονται
**ξεμαρκαρισμένες** — αποφασίζεις εσύ. Τιμή που το πρόγραμμα δεν αναγνωρίζει εμφανίζεται ως
«άγνωστη» και δεν προτείνεται αλλαγή.

### Γραμμή εντολών

```bash
python AstroTune_1_5beta.py --report [smooth|fps|quality|balanced] [--sp] [--low] [--vr] [--en]
```

| Διακόπτης | Τι κάνει |
|---|---|
| `--sp` | Single player (αλλιώς υποθέτει multiplayer) |
| `--low` | Χαμηλή πτήση / ελικόπτερα |
| `--vr` | Υπολογισμός για VR αντί για οθόνη |
| `--en` | Έξοδος στα αγγλικά |

Ένα `AstroTune.exe` χτισμένο με `--noconsole` δεν έχει πού να τα τυπώσει. Χρησιμοποίησε το
`.py`, ή χτίσε δεύτερο exe με `--console`.

### Πού γράφει αρχεία

| Διαδρομή | Τι είναι |
|---|---|
| `%USERPROFILE%\.astrotune.json` | Θέμα, γλώσσα, στόχος, αποθηκευμένη σύνθεση. Σβήσ' το και ξεκινάει καθαρό. |
| `%USERPROFILE%\.astrotune_exports\` | Τα exports του driver, απλό κείμενο. Σβήνονται ελεύθερα. |
| `…\Saved Games\DCS*\Config\AstroTune_backups\` | Αντίγραφα του `options.lua` πριν από κάθε εγγραφή. **Μην τα σβήσεις.** |

### Χτίσιμο της έκδοσης

Βάλε το `build.bat` δίπλα στο `AstroTune_1_5beta.py` και κάνε διπλό κλικ. Βρίσκει την
Python, εγκαθιστά το PyInstaller αν λείπει, φτιάχνει τους φακέλους που περιμένει το Inno,
χτίζει το exe και σου λέει τι λείπει ακόμα. Ή με το χέρι:

```bash
pip install pyinstaller
pyinstaller --onefile --noconsole --name AstroTune ^
            --distpath build --workpath build\tmp --specpath build ^
            AstroTune_1_5beta.py
```

Μετά μεταγλώττισε το `AstroTune_Setup.iss` με Inno Setup 6.3+. Το script περιμένει:

```
AstroTune_Setup.iss
README.md
build\AstroTune.exe
docs\AstroTune_Manual.html
dist\                      <- εδώ βγαίνει το τελικό Setup.exe
```

Το AstroTune χρησιμοποιεί μόνο standard library, οπότε δεν χρειάζεται `--hidden-import`. Τα
`--onefile` πακέτα βγάζουν συχνά ψευδή συναγερμό σε antivirus επειδή αποσυμπιέζονται σε
προσωρινό φάκελο κάθε φορά· το `--onedir` όχι.

Ο οδηγός εγκατάστασης βγαίνει μόνο στα αγγλικά αν λείπει το `Greek.isl`, γιατί τα ελληνικά
είναι *ανεπίσημη* μετάφραση του Inno Setup και δεν έρχονται μαζί του. Βάλ' το σε φάκελο
`languages\` δίπλα στο `.iss` (ή στο `Languages\` του Inno) και το script το βρίσκει μόνο
του. Αφορά μόνο τον οδηγό — το ίδιο το πρόγραμμα είναι δίγλωσσο ούτως ή άλλως.

### Τι δεν ξέρει ακόμα

Οι αντιστοιχίσεις τιμών επαληθεύτηκαν πεδίο-πεδίο πάνω σε πραγματικά αρχεία, σε σύγκριση με
screenshots του DCS: 33 στις 33 σωστές. Δύο μένουν ανοιχτά, και το πρόγραμμα το λέει αντί να
μαντέψει:

- **DLSS Perf/Quality** — το DCS το αποθηκεύει ως κείμενο στο `options.lua` αλλά ως ακέραιο
  στα αρχεία των θέσεων Custom. Η αντιστοίχιση του ακεραίου δεν έχει επιβεβαιωθεί, οπότε
  εκεί εμφανίζεται ως «άγνωστη».
- **Terrain Objects Shadows** — επαληθεύτηκε μόνο ότι `0 = Default`· τα υπόλοιπα σκαλιά
  ακολουθούν τη σειρά του dropdown αλλά δεν έχουν επιβεβαιωθεί.

### Ευχαριστίες

AstroTune — DCS 2026© για το **LOCK-ON GREECE** από τον **=GR= Astr0**.
Το DCS World είναι προϊόν της Eagle Dynamics. Το έργο αυτό δεν σχετίζεται με, ούτε
υποστηρίζεται από, την Eagle Dynamics ή τη NVIDIA.

---

<sub>License / Άδεια: add a `LICENSE` file — βάλε ένα αρχείο `LICENSE` — and reference it here.</sub>
