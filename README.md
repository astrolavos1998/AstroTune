# AstroTune 2.3

**A settings advisor for DCS World — reads your real config, knows your hardware, tells you what to change.**
**Σύμβουλος ρυθμίσεων για το DCS World — διαβάζει το πραγματικό σου αρχείο, ξέρει το μηχάνημά σου, σου λέει τι να αλλάξεις.**

`Windows 10+` · `Python 3.10–3.13` · `no dependencies` · `GR / EN` · `dark / light`

🇬🇧 [English](#english) · 🇬🇷 [Ελληνικά](#ελληνικά)

> **Read only. AstroTune changes no setting in Windows, in your graphics driver or in DCS — it shows you what to set, and you set it yourself.**
> **Μόνο ανάγνωση. Το AstroTune δεν αλλάζει καμία ρύθμιση σε Windows, driver ή DCS — σου δείχνει τι να βάλεις, και το βάζεις εσύ.**
>
> **Use of this program and of the settings it suggests is ENTIRELY AT THE USER'S OWN RISK.**
> **Η χρήση του προγράμματος και των ρυθμίσεων που προτείνει γίνεται με ΑΠΟΚΛΕΙΣΤΙΚΗ ΕΥΘΥΝΗ ΤΟΥ ΧΡΗΣΤΗ.**

---

## English

### What it is

AstroTune is a single-file Python desktop app. It reads your actual `options.lua` and your
graphics driver profile, detects what hardware you are running, and produces a per-setting
recommendation for the goal you choose — on a monitor or in VR.

It is not a "just set these" list. Every suggestion starts from the values you have _right
now_, weighted by your card, CPU, VRAM, resolution and whether you fly multiplayer. The same
setting gets a different answer on 8GB than on 24GB.

- **39 DCS settings** with a measurable cost (36 graphics, 3 cockpit)
- **Four choices, one path** — VR or Monitor → Multiplayer or Single Player → Low level or
  not → one of four goals
- **44 GPUs, 27 CPUs, 12 headsets** in the database, plus automatic detection
- **Driver checklist** — 13 items for NVIDIA, 11 for AMD, split into Global and per-application
- **Read-only comparison** of your three DCS Custom slots against your active settings
- Greek and English, dark and light

### Read only, and your responsibility

|                                           | Read | Write     |
| ----------------------------------------- | :--: | --------- |
| Windows settings (refresh rate, registry) |  ✔   | **never** |
| Driver profile database (NVAPI)           |  ✔   | **never** |
| `options.lua`                             |  ✔   | **never** |
| `Config\OptionsPresets\Custom*.lua`       |  ✔   | **never** |

This is not a promise made by the user interface — it is a property of the code. The
`options.lua` reader **has no write methods**: no `set`, no `save`. There is no
display-settings call. The NVAPI calls are read-only. A change cannot happen by accident,
because there is no path for it to happen.

The only files it creates are its own, in your user profile: `~/.astrotune.json` for theme,
language and the build you picked, and `~/.astrotune_exports/` for a copy of the driver
profiles it reads.

> **Use of this program and of the settings it suggests is ENTIRELY AT THE USER'S OWN
> RISK.** The suggestions are estimates based on measurement and experience, not
> guarantees. Check every value before you apply it, and keep a copy of your `options.lua`
> before making large changes.

### Install

**Installer** — run `AstroTune_2.3_Setup.exe`. No administrator rights, nothing written
to the registry, installs per-user by default.

**From source** — with Python 3.10 or newer:

```bash
python AstroTune_2_3.py
```

Only `tkinter` is required, which ships with Python on Windows. Optional:
`pip install nvidia-ml-py` adds a live VRAM readout and the exact PCIe generation.

### How to use it

Four choices, one path. Every combination produces a different set of settings — these are
not filters over one list, the numbers themselves change.

1. **VR or Monitor** — asked on every launch, because in VR _which_ settings matter, _what_
   they cost, and the whole frame-cap strategy all change.
2. **Multiplayer or Single Player** — on a full server, whatever costs CPU and VRAM comes
   down.
3. **Low level (helicopters) or not** — what you can see near the ground goes up; what only
   matters at altitude comes down.
4. **The goal** — Smooth motion with clarity · Maximum FPS · Maximum graphics · Balanced.

Along the way: **Tab 1** detects your hardware (VRR is not detectable — tick it only if you
can see G-Sync **active** in the driver). **Tab 2** is the driver checklist, Global and
per-application. **Tab 3** is the DCS list. There is no apply button anywhere; _List to
copy_ puts the rows on the clipboard.

Tab 2 reads your driver by itself, by two routes: first it asks NVAPI for each value
directly (no file, no encoding to get wrong), and if that does not work it exports the
whole profile database to a text file and parses that. If both fail you get a window with
exactly what the driver answered, a _Copy_ button, and whatever had already been read is
kept — a failed re-read no longer empties the list.

**Copy everything** in the bottom bar puts hardware, the driver checklist and all 39 DCS
settings into one text, with `=` for what already matches and `->` for what needs
changing. Every copy — this one, the driver list, the DCS list — starts with the context
of the suggestion: date, mode, goal, multiplayer, low level, FPS cap, machine and the
path of your `options.lua`. An empty DCS table is not a fault; it says everything is
already on target.

Tab 3 also shows your **Custom1/2/3 slots as columns**, next to Now and Target. Since 2.3
they are compared against the **Target**: `✓` where the slot is already on target, the
value where it is not, `—` where the key is not in that slot at all. A column full of `✓`
means "load this slot and you are done". If a slot is byte-for-byte identical to your
active `options.lua`, its column is a repeat of Now, so the header says **Now Custom2**
and the duplicate column is hidden — that is how you learn which saved slot you are
running. A line above the table gives each slot's save date and how many keys are missing
from which. Nothing is ever written to those files. "Only what changes" starts unticked,
so the tab opens on the full picture; your choice is remembered.

**Checking for a new version** is off until you say yes. When on, tab 1 has a _Check now_
button and the program asks GitHub Releases once a day, in the background, with a 5-second
timeout and no error popups. It only tells you when something newer exists — you download
and install it yourself. GitHub pre-releases are ignored, so a beta you publish notifies
nobody.

### Command line

```bash
python AstroTune_2_3.py --report [smooth|fps|quality|balanced] [--sp] [--low] [--vr] [--en]
```

| Switch  | Meaning                                          |
| ------- | ------------------------------------------------ |
| `--sp`  | Single player (multiplayer is assumed otherwise) |
| `--low` | Low level / helicopters                          |
| `--vr`  | Compute for VR instead of a monitor              |
| `--en`  | English output                                   |

An `AstroTune.exe` built with `--noconsole` has nowhere to print this. Use the `.py`, or
build a second exe with `--console`.

### Files it writes

| Path                                | What                                                                            |
| ----------------------------------- | ------------------------------------------------------------------------------- |
| `%USERPROFILE%\.astrotune.json`     | Theme, language, goal, saved build. Delete it to start fresh.                   |
| `%USERPROFILE%\.astrotune_exports\` | A copy of the driver profiles, so it can read them. Plain text. Safe to delete. |

Both are its own, in your user profile. Nothing inside DCS, nothing in the registry.

### Building the release

Put `build.bat` next to `AstroTune_2_3.py` and double-click it. It finds Python,
installs PyInstaller if missing, creates the folders the Inno script expects, builds the
exe and tells you what is still missing. Or do it by hand:

```bash
pip install pyinstaller
pyinstaller --onefile --noconsole --name AstroTune ^
            --distpath build --workpath build\tmp --specpath build ^
            AstroTune_2_3.py
```

Then compile `AstroTune_Setup.iss` with Inno Setup 6.3+. The script expects:

```
AstroTune_Setup.iss
README.md
build\AstroTune.exe
docs\AstroTune_Manual.html
dist\                      <- the finished Setup.exe lands here
```

…but it does not require that layout. Everything flat in one folder works too. Your exe
can be called anything: the script looks for `build\AstroTune.exe`, then `AstroTune.exe`,
then `AstroTune_<version>*.exe`, then any `AstroTune*.exe` that is not a `_Setup`, then
`build\AstroTune*.exe` — and installs whichever it found under the fixed name
`AstroTune.exe`, via `DestName`. That is why upgrades never leave old exes behind and
shortcuts never break. If you would rather the installed exe carry the version, one
commented line near the top of the .iss switches it, and it updates itself from
`MyAppVersion`. Keep only one `AstroTune*.exe` in the folder; the compiler output prints
which one it picked.

Two things to keep in mind when editing the .iss: it must stay **UTF-8 with BOM** (without
it Inno reads the Greek wizard messages as ANSI and they come out as mojibake), and
`Greek.isl` is an unofficial translation that does not ship with Inno Setup — see the
comments in section 5 of the script.

AstroTune uses only the standard library, so no `--hidden-import` is needed. `--onefile`
builds commonly trip antivirus heuristics because they unpack to a temp folder on every run;
`--onedir` does not.

The installer wizard is English-only unless `Greek.isl` is present, because Greek is an
_unofficial_ Inno Setup translation and is not bundled. Drop it into a `languages\` folder
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

Δεν είναι λίστα «βάλε αυτά». Κάθε πρόταση ξεκινάει από τις τιμές που έχεις _τώρα_,
σταθμισμένες με την κάρτα, τον επεξεργαστή, τη VRAM, την ανάλυση και το αν παίζεις
multiplayer. Η ίδια ρύθμιση παίρνει άλλη απάντηση σε 8GB και άλλη σε 24GB.

- **39 ρυθμίσεις** του DCS με μετρήσιμο κόστος (36 graphics, 3 κόκπιτ)
- **Τέσσερις επιλογές, μία διαδρομή** — VR ή Οθόνη → Multiplayer ή Single Player →
  Χαμηλή πτήση ή όχι → ένας από τους τέσσερις στόχους
- **44 κάρτες, 27 επεξεργαστές, 12 headsets** στη βάση, συν αυτόματη ανίχνευση
- **Λίστα ελέγχου για τον driver** — 13 σημεία για NVIDIA, 11 για AMD, χωρισμένα σε Global
  και ανά παιχνίδι
- **Σύγκριση μόνο για ανάγνωση** των τριών θέσεων Custom του DCS με τις ενεργές ρυθμίσεις
- Ελληνικά και αγγλικά, σκούρο και ανοιχτό θέμα

### Μόνο ανάγνωση, και δική σου ευθύνη

|                                              | Ανάγνωση | Εγγραφή  |
| -------------------------------------------- | :------: | -------- |
| Ρυθμίσεις των Windows (refresh rate, μητρώο) |    ✔     | **ποτέ** |
| Βάση προφίλ του driver (NVAPI)               |    ✔     | **ποτέ** |
| `options.lua`                                |    ✔     | **ποτέ** |
| `Config\OptionsPresets\Custom*.lua`          |    ✔     | **ποτέ** |

Δεν είναι υπόσχεση του περιβάλλοντος χρήστη — είναι ιδιότητα του κώδικα. Ο αναγνώστης του
`options.lua` **δεν έχει μεθόδους εγγραφής**: ούτε `set`, ούτε `save`. Δεν υπάρχει κλήση
αλλαγής ρυθμίσεων οθόνης. Οι κλήσεις NVAPI είναι αποκλειστικά ανάγνωσης. Μια αλλαγή δεν
μπορεί να συμβεί κατά λάθος, γιατί δεν υπάρχει διαδρομή για να συμβεί.

Τα μόνα αρχεία που δημιουργεί είναι δικά του, στο προφίλ σου: το `~/.astrotune.json` για
θέμα, γλώσσα και σύνθεση, και το `~/.astrotune_exports/` για το αντίγραφο των προφίλ του
driver που διαβάζει.

> **Η χρήση του προγράμματος και των ρυθμίσεων που προτείνει γίνεται με ΑΠΟΚΛΕΙΣΤΙΚΗ
> ΕΥΘΥΝΗ ΤΟΥ ΧΡΗΣΤΗ.** Οι προτάσεις είναι εκτιμήσεις βασισμένες σε μετρήσεις και εμπειρία,
> όχι εγγυήσεις. Έλεγξε κάθε τιμή πριν την εφαρμόσεις, και κράτα αντίγραφο του
> `options.lua` σου πριν από μεγάλες αλλαγές.

### Εγκατάσταση

**Με installer** — τρέξε το `AstroTune_2.3_Setup.exe`. Δεν ζητάει δικαιώματα
διαχειριστή, δεν γράφει στο μητρώο, εγκαθίσταται μόνο για τον λογαριασμό σου.

**Από τον κώδικα** — με Python 3.10 ή νεότερη:

```bash
python AstroTune_2_3.py
```

Χρειάζεται μόνο το `tkinter`, που έρχεται μαζί με την Python στα Windows. Προαιρετικά, το
`pip install nvidia-ml-py` προσθέτει ζωντανή ένδειξη VRAM και ακριβή γενιά PCIe.

### Πώς χρησιμοποιείται

Τέσσερις επιλογές, μία διαδρομή. Κάθε συνδυασμός δίνει διαφορετικό σύνολο ρυθμίσεων — δεν
είναι φίλτρα πάνω σε μια ενιαία λίστα, αλλάζουν τα ίδια τα νούμερα.

1. **VR ή Οθόνη** — ρωτιέται σε κάθε άνοιγμα, γιατί σε VR αλλάζουν _ποιες_ ρυθμίσεις
   μετράνε, _πόσο_ κοστίζουν, και ολόκληρη η στρατηγική του frame cap.
2. **Multiplayer ή Single Player** — σε γεμάτο server πέφτει ό,τι κοστίζει σε CPU και VRAM.
3. **Χαμηλή πτήση (ελικόπτερα) ή όχι** — ανεβαίνει ό,τι φαίνεται κοντά στο έδαφος, πέφτει
   ό,τι μετράει μόνο ψηλά.
4. **Ο στόχος** — Ομαλή κίνηση με ευκρίνεια · Μέγιστα FPS · Μέγιστα γραφικά · Ισορροπία.

Στον δρόμο: η **καρτέλα 1** ανιχνεύει το μηχάνημα (το VRR δεν ανιχνεύεται — τσέκαρέ το μόνο
αν βλέπεις το G-Sync **ενεργό** στον driver). Η **καρτέλα 2** είναι η λίστα ελέγχου του
driver, Global και ανά παιχνίδι. Η **καρτέλα 3** είναι η λίστα του DCS. Δεν υπάρχει πουθενά
κουμπί εφαρμογής· το _Λίστα για αντιγραφή_ βάζει τις γραμμές στο πρόχειρο.

Η καρτέλα 2 διαβάζει τον driver μόνη της, με δύο δρόμους: πρώτα ζητάει από το NVAPI κάθε
τιμή ξεχωριστά (χωρίς αρχείο, χωρίς κωδικοποίηση να πάει στραβά), κι αν αυτό δεν παίξει
γράφει ολόκληρη τη βάση προφίλ σε αρχείο κειμένου και το διαβάζει. Αν αποτύχουν και τα
δύο, βγαίνει παράθυρο με ό,τι ακριβώς απάντησε ο driver και κουμπί _Αντιγραφή_, ενώ ό,τι
είχε ήδη διαβαστεί κρατιέται — μια αποτυχημένη ξαναανάγνωση δεν αδειάζει πια τη λίστα.

Το **Αντιγραφή όλων** στην κάτω μπάρα βάζει σε ένα κείμενο το υλικό, τη λίστα του driver
και τις 39 ρυθμίσεις του DCS, με `=` σε όσες ήδη ταιριάζουν και `->` σε όσες θέλουν
αλλαγή. Κάθε αντιγραφή — αυτή, του driver και του DCS — ξεκινάει με το πλαίσιο της
πρότασης: ημερομηνία, λειτουργία, στόχος, Multiplayer, χαμηλή πτήση, όριο FPS, μηχάνημα
και διαδρομή του `options.lua`. Άδειος πίνακας DCS δεν είναι σφάλμα· σημαίνει ότι όλα
είναι ήδη στον στόχο.

Η καρτέλα 3 δείχνει επίσης τις **θέσεις Custom1/2/3 ως στήλες**, δίπλα στο «Τώρα» και τον
«Στόχο». Από την 2.3 συγκρίνονται με τον **Στόχο**: `✓` όπου η θέση είναι ήδη εκεί, η τιμή
όπου δεν είναι, `—` όπου το κλειδί δεν υπάρχει καθόλου στη θέση. Στήλη γεμάτη `✓` σημαίνει
«φόρτωσε αυτή τη θέση και τελείωσες». Αν μια θέση είναι ταυτόσημη με το ενεργό
`options.lua`, η στήλη της επαναλαμβάνει το «Τώρα», οπότε η κεφαλίδα γράφει **Τώρα
Custom2** και η διπλή στήλη κρύβεται — έτσι μαθαίνεις ποια αποθηκευμένη θέση τρέχεις. Μια
γραμμή πάνω από τον πίνακα δίνει την ημερομηνία αποθήκευσης της κάθε θέσης και πόσα
κλειδιά λείπουν από ποια. Ποτέ δεν γράφεται τίποτα σε εκείνα τα αρχεία. Το «Μόνο όσα
αλλάζουν» ξεκινάει ξετσεκαρισμένο, οπότε η καρτέλα ανοίγει στη συνολική εικόνα· η επιλογή
σου κρατιέται.

**Ο έλεγχος για νέα έκδοση** είναι κλειστός μέχρι να πεις ναι. Όταν είναι ανοιχτός, η
καρτέλα 1 έχει κουμπί _Έλεγχος τώρα_ και το πρόγραμμα ρωτάει το GitHub Releases μία φορά
την ημέρα, στο παρασκήνιο, με timeout 5 δευτερολέπτων και χωρίς παράθυρα σφαλμάτων. Σου
λέει μόνο όταν υπάρχει κάτι νεότερο — το κατεβάζεις και το εγκαθιστάς εσύ. Τα pre-release
του GitHub αγνοούνται, οπότε ένα beta που ανεβάζεις δεν ειδοποιεί κανέναν.

### Γραμμή εντολών

```bash
python AstroTune_2_3.py --report [smooth|fps|quality|balanced] [--sp] [--low] [--vr] [--en]
```

| Διακόπτης | Τι κάνει                                    |
| --------- | ------------------------------------------- |
| `--sp`    | Single player (αλλιώς υποθέτει multiplayer) |
| `--low`   | Χαμηλή πτήση / ελικόπτερα                   |
| `--vr`    | Υπολογισμός για VR αντί για οθόνη           |
| `--en`    | Έξοδος στα αγγλικά                          |

Ένα `AstroTune.exe` χτισμένο με `--noconsole` δεν έχει πού να τα τυπώσει. Χρησιμοποίησε το
`.py`, ή χτίσε δεύτερο exe με `--console`.

### Πού γράφει αρχεία

| Διαδρομή                            | Τι είναι                                                                               |
| ----------------------------------- | -------------------------------------------------------------------------------------- |
| `%USERPROFILE%\.astrotune.json`     | Θέμα, γλώσσα, στόχος, αποθηκευμένη σύνθεση. Σβήσ' το και ξεκινάει καθαρό.              |
| `%USERPROFILE%\.astrotune_exports\` | Αντίγραφο των προφίλ του driver, για να τα διαβάσει. Απλό κείμενο. Σβήνονται ελεύθερα. |

Και τα δύο δικά του, στο προφίλ σου. Τίποτα μέσα στο DCS, τίποτα στο μητρώο.

### Χτίσιμο της έκδοσης

Βάλε το `build.bat` δίπλα στο `AstroTune_2_3.py` και κάνε διπλό κλικ. Βρίσκει την
Python, εγκαθιστά το PyInstaller αν λείπει, φτιάχνει τους φακέλους που περιμένει το Inno,
χτίζει το exe και σου λέει τι λείπει ακόμα. Ή με το χέρι:

```bash
pip install pyinstaller
pyinstaller --onefile --noconsole --name AstroTune ^
            --distpath build --workpath build\tmp --specpath build ^
            AstroTune_2_3.py
```

Μετά μεταγλώττισε το `AstroTune_Setup.iss` με Inno Setup 6.3+. Το script περιμένει:

```
AstroTune_Setup.iss
README.md
build\AstroTune.exe
docs\AstroTune_Manual.html
dist\                      <- εδώ βγαίνει το τελικό Setup.exe
```

…αλλά δεν απαιτεί αυτή τη δομή. Όλα μαζί σε έναν φάκελο δουλεύει το ίδιο. Το exe σου
μπορεί να λέγεται ό,τι θέλει: το script ψάχνει `build\AstroTune.exe`, μετά
`AstroTune.exe`, μετά `AstroTune_<έκδοση>*.exe`, μετά ό,τι `AstroTune*.exe` δεν είναι
`_Setup`, και τέλος `build\AstroTune*.exe` — και εγκαθιστά ό,τι βρει με το σταθερό όνομα
`AstroTune.exe`, μέσω του `DestName`. Γι' αυτό οι αναβαθμίσεις δεν αφήνουν πίσω παλιά exe
και οι συντομεύσεις δεν σπάνε. Αν προτιμάς το εγκατεστημένο exe να έχει την έκδοση στο
όνομά του, μια σχολιασμένη γραμμή στην αρχή του .iss το αλλάζει και ενημερώνεται μόνη της
από το `MyAppVersion`. Κράτα ΕΝΑ `AstroTune*.exe` στον φάκελο· η έξοδος του compiler
γράφει ποιο διάλεξε.

Δύο πράγματα όταν πειράζεις το .iss: πρέπει να μείνει **UTF-8 ΜΕ BOM** (χωρίς αυτό το
Inno διαβάζει τα ελληνικά μηνύματα του οδηγού ως ANSI και βγαίνουν κινέζικα), και το
`Greek.isl` είναι ανεπίσημη μετάφραση που δεν έρχεται μαζί με το Inno Setup — δες τα
σχόλια στην ενότητα 5 του script.

Το AstroTune χρησιμοποιεί μόνο standard library, οπότε δεν χρειάζεται `--hidden-import`. Τα
`--onefile` πακέτα βγάζουν συχνά ψευδή συναγερμό σε antivirus επειδή αποσυμπιέζονται σε
προσωρινό φάκελο κάθε φορά· το `--onedir` όχι.

Ο οδηγός εγκατάστασης βγαίνει μόνο στα αγγλικά αν λείπει το `Greek.isl`, γιατί τα ελληνικά
είναι _ανεπίσημη_ μετάφραση του Inno Setup και δεν έρχονται μαζί του. Βάλ' το σε φάκελο
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
