# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

> Poznámka k jazyku: dokumentace v tomto projektu se píše česky, komunikace s uživatelem probíhá česky. Anglicky zůstává pouze vše code-related (kód, komentáře v kódu, názvy proměnných/funkcí, commit messages, PR popisy).

## Co to je

Jednostránkový statický prezentační web "Šárka Dolejšová – Dotace na pohodu" (česká poradna pro dotace). Nemá build step, žádný framework ani backend — celý web je ručně psané HTML/CSS servírované jako statické soubory. Vznikl migrací z WordPressu na statickou Bootstrap stránku (commit `9935c28`).

## Architektura

- **`public/`** je kořen pro deploy (adresář, který Firebase servíruje).
  - `public/index.html` — celý web. Jeden soběstačný soubor: veškeré custom CSS je v inline `<style>` bloku (řádky ~18–76) a tělo stránky je sekvence `<section>` bloků s kotvami `id` (`pro-vas-dum`, `na-co-dotace`, `proc-dotace`, `reference`, `kontakt`), na které odkazuje navbar.
  - `public/404.html` — vlastní "not found" stránka pro Firebase.
  - `public/img/` — loga a favicony, každé ve trojici `.avif` / `.webp` / `.png` pro fallback formátů přes `<picture>`/`<link>`, plus light/dark varianty favicony vybírané přes `prefers-color-scheme`.
- **Bootstrap 5.3.8** (CSS + JS bundle) a font **Figtree** z Google Fonts se načítají z CDN — nejsou vendorované. Theming se dělá přepsáním Bootstrap CSS custom properties (`--bs-primary` atd.) v inline `:root` bloku, ne úpravou samotného Bootstrapu.

## Konvence při úpravách

- Veškerý viditelný text je **česky** (`<html lang="cs">`) — jde o user-facing obsah, takže zůstává česky. Identifikátory na úrovni kódu, komentáře a commit messages jsou anglicky.
- Upřednostňuj Bootstrap utility třídy a komponentový markup před novým custom CSS. Když se custom stylům nelze vyhnout, přidej je do inline `<style>` bloku v `index.html`.
- Při přidávání obrázků vygeneruj sadu `.avif`/`.webp`/`.png`, aby odpovídala stávajícímu vzoru fallbacku formátů.

## Deploy & lokální náhled

Deploy probíhá automaticky přes GitHub Actions — neexistují žádné lokální build/lint/test příkazy.

- **Push do `master`** → `.github/workflows/firebase-hosting-merge.yml` nasadí na živý Firebase Hosting kanál (projekt `dotacenapohodu-cz`).
- **Pull requesty** → `firebase-hosting-pull-request.yml` nasadí dočasný preview kanál.
- **Lokální náhled:** `firebase emulators:start` servíruje `public/` na http://localhost:8080 (nastaveno v `firebase.json`).

`firebase.json` navíc přepisuje `/favicon.ico` → `/img/favicon.png`.
