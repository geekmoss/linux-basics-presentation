---
---

# Obsah

- Úvod
- Pohyb v adresářové struktuře
- Adresářová struktura
- Oprávnění
- Práce s textem

---
layout: image-right
image: images/qr-prezentace.png
---

## Odkazy k prezentaci

- Prezentace: https://codecraft.cz/linux-basics
- "Wiki": https://wiki.quvy.eu/index.php/Linux:Basics
- Kontakt:
    - E-mail: `geekmoss@codecraft.cz`
    - Discord: `@geekmoss`

**Další odkazy**:

- https://devhints.io/bash
- https://bash-prompt-generator.org/
- https://crontab.guru/
- https://regex101.com/

---
---

## Pojmy a dojmy

- Příkaz vs Program
- Přepínače

---
---

### Příkazy a programy

V rámci terminálu typicky mluvíme o příkazech, ale většinou myslíme programy. Například `cat` nebo `test`.

Existují ale i klíčová slova, která dodává samotný shell například `if` nebo `while`.

*Taktéž se sekváme s pojem "skript" který může označovat jak shell skript (`.sh`, `.zsh`) tak i ale třeba
skript pro Python.*

---
---

### Přepínače

Přepínače, switch, a option jsou jedno a to samé. To co zapisujeme s `-` na začátku.

Konvence při používání přepínačů je `-` pro krátné a typicky jednoznakové přepínače, `--` pro víceznaké, tedy jak
zkratky tak celá slova a víceslovná spojení.

- `-h`
- `-help`
- `--help`

<!--
Krátký přínač -h může vracet kratší nápovědu, celoslovný pak delší. Ale není pravidlem.
-->

---
transition: fade
---

## Pár pomocníků na začátek

- <kbd>TAB</kbd>
- <kbd>↑</kbd> <kbd>↓</kbd>
- <kbd>^R</kbd> = <kbd>Ctrl</kbd> + <kbd>R</kbd>
- `-h` / `--help` / `-help`
- `man`
- `whatis`
- `which`
- `whereis`
- `whoami`

---
---

## Pár pomocníků na začátek

- <kbd>TAB</kbd>: Doplňování
- <kbd>↑</kbd> <kbd>↓</kbd>: Procházení historie
- <kbd>^R</kbd> = <kbd>Ctrl</kbd> + <kbd>R</kbd>: Rekurzivní hledání v historii
- `-h` / `--help` / `-help`: Nápověda příkaz
- `man`: Manuálová stránka
- `whatis`: Krátký popis příkazu
- `which`: Cesta k příkazu
- `whereis`: Cesty k příkazu, manuálu, atd.
- `whoami`: Přihlášený uživatel
