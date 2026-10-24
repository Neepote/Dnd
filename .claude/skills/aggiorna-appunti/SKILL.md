---
name: aggiorna-appunti
description: Riordina gli appunti di una campagna D&D dopo una sessione. Raggruppa le note nuove del diario in sessioni, aggiorna le schede di personaggi, luoghi, indizi e lore e l'indice della campagna, raddrizza le immagini nuove. Da usare quando l'utente ha scritto nuovi appunti, chiede di "aggiornare/sistemare gli appunti" o passa nuove informazioni sulla campagna (schede PG, lore, descrizioni).
argument-hint: "[cartella campagna, default The_Veil_Between_Dreams]"
---

# Aggiorna appunti della campagna

L'utente scrive gli appunti durante la sessione in fondo a `sessioni.md`, di getto. Il tuo compito è riordinarli e propagare le informazioni nelle altre schede, **senza riscrivere il suo testo**.

Campagna: `$ARGUMENTS` se indicata, altrimenti `The_Veil_Between_Dreams/`.

## Struttura della cartella campagna

| File | Contenuto |
|---|---|
| `README.md` | Indice: intro di 2 righe sul mondo, il gruppo, link ai file, **Prossimi passi** (checklist), tabella **Riassunto finora** (una riga per sessione) |
| `sessioni.md` | Diario. `## Sessione N — GG/MM/AAAA` → `> **In breve:** …` → sottosezioni `### Weekday GG/MM/AAAA HH:MM` scritte dall'utente |
| `personaggi.md` | Tabella **Il gruppo** (Personaggio · Giocatore · Note) + **PNG incontrati** (riga indice con stato tra parentesi, poi una `###` per PNG) |
| `<nome_pg>.md` | Scheda completa di un PG (es. `michele_foresta.md`), linkata dalla tabella del gruppo |
| `luoghi.md` | Regni · Città (sotto-luoghi con `####`) · Altri luoghi |
| `indizi.md` | Oggetti e indizi trovati + sezione finale **Domande aperte** |
| `mondo.md` | Lore: descrizione del mondo (riassunto IT + testo originale in citazione), **Cronologia**, fatti noti |
| `immagini/` | Immagini referenziate con percorso relativo `./immagini/…` |

Se un file manca, crealo seguendo lo stesso stile. Il `README.md` alla radice del repo elenca le campagne: aggiornalo solo se ne nasce una nuova.

## Procedura

1. **Trova le note nuove.** Controlla `git diff HEAD -- <campagna>/` e `git log -3 -- <campagna>/sessioni.md`. Sono nuove le sottosezioni `###` aggiunte dopo l'ultimo riordino: in genere stanno in fondo, sotto l'ultima `## Sessione` o senza un titolo di sessione.
   - Una sessione è una serata di gioco: le note con orari dalla sera fino alle prime ore del giorno dopo appartengono alla stessa sessione. Le note di un'altra serata aprono una nuova `## Sessione N`, con la data della serata.
   - Se l'utente scrive `#### ` o altri livelli di titolo diversi, adattali in silenzio a `###`.

2. **Riordina `sessioni.md`.**
   - Aggiungi `## Sessione N — data` e un `> **In breve:**` di 1–2 frasi. Aggiungi la sessione anche all'elenco in cima al file, e metti `<hr>` tra una sessione e l'altra.
   - Correggi **solo** i refusi evidenti e le maiuscole dei nomi propri. Tieni tono, battute e contenuti dell'utente così come sono, anche quelli volgari.
   - Alla prima menzione in ogni sottosezione, rendi link i PNG, i luoghi e gli indizi che hanno una scheda (es. `[Tavrin](./personaggi.md#tavrin-stonebell)`).
   - Nel diario l'utente usa i nomi dei **giocatori**: non sostituirli con quelli dei personaggi.

3. **Propaga nelle schede.** Per ogni PNG, luogo, oggetto o informazione nuova:
   - Crea o aggiorna la sua sezione nel file giusto, con fatti concisi e il riferimento alla sessione ("Sconfitta nella sessione 2").
   - Aggiorna gli stati (morto, sconfitto, scomparso, alleato…) nella riga indice dei PNG.
   - Aggiungi in `indizi.md → Domande aperte` le nuove domande. Segna come risolte quelle che hanno trovato risposta, barrandole con `~~…~~` e aggiungendo → la risposta, invece di cancellarle.
   - Distingui i fatti dalle ipotesi: le tue deduzioni vanno **solo** nelle Domande aperte, formulate come domanda.

4. **Aggiorna il `README.md` della campagna:** aggiungi la riga della sessione nel Riassunto finora, spunta i prossimi passi completati (`- [x]`) e aggiungi quelli nuovi che emergono dalle ultime note (di solito l'ultima sottosezione dice "l'idea è di…").

5. **Immagini nuove** in `immagini/`: aprile con Read. Se sono ruotate (testo verticale, nord non in alto), raddrizzale:
   ```bash
   S=<scratchpad>; pip3 install -q --target "$S/pylib" pillow
   PYTHONPATH="$S/pylib" python3 -c "from PIL import Image,ImageOps;import sys
   for f in sys.argv[1:]: ImageOps.exif_transpose(Image.open(f)).transpose(Image.Transpose.ROTATE_90).save(f,'JPEG',quality=95,subsampling=0)" <file>
   ```
   `ROTATE_90` = 90° antiorario, `ROTATE_270` = orario. Dopo la rotazione riapri l'immagine per verificarla. Ogni immagine va referenziata con `![nome](./immagini/file)` nel punto giusto del diario o della scheda.

6. **Verifica i link.** Ogni `#ancora` deve corrispondere a un titolo esistente. Le ancore vanno in minuscolo, la punteggiatura (anche parentesi, `/` e `:`) si toglie, gli spazi diventano `-` e le lettere accentate restano. Esempi: `## Sessione 3 — 24/10/2026` → `#sessione-3--24102026`, `#### Magazzino sud-est` → `#magazzino-sud-est`.

7. **Riepiloga e chiedi.** Mostra all'utente un elenco breve: cosa hai aggiunto, i nomi incerti (es. grafie diverse tra appunti e mappa) e le cose che non sei riuscito ad attribuire. Fai `git commit` **solo** se l'utente lo chiede. Non fare mai push.

## Altre richieste che passano da qui

- **Scheda PG** (link D&D Beyond `…/sheet-pdfs/….pdf`): scaricala con curl in scratchpad e leggila con Read. Crea o aggiorna `<nome_pg>.md` sul modello di `michele_foresta.md`: dati base, storia completa, statistiche, capacità, incantesimi, equipaggiamento. Linkala dalla tabella del gruppo.
- **Lore o descrizioni date dal master:** mettile in `mondo.md` con il testo originale in citazione e un riassunto in italiano. Aggiorna la Cronologia e le schede dei luoghi coinvolti.

## Il gruppo (The Veil Between Dreams)

| Personaggio | Giocatore | |
|---|---|---|
| Michele Foresta | Lorenzo (Neepote), l'utente: "io" negli appunti | Nano, mago abiuratore |
| Lanciolo | Marco (TehMark) | Nano, barbaro del Cammino del Gigante |
| Pantheon | Giacomo (GiarickL9) | Umano, paladino del Giuramento della Gloria |
| Piero | Massimo / Dutua (maxedo99) | Elfo, chierico della Tempesta |

Se cambia qualcosa nel gruppo, aggiorna sia questa tabella sia `personaggi.md`.
