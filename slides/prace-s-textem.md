---
class: 'text-center'
layout: cover
background: images/typewriter.jpeg
---

# Práce s textem

---

- `cat FILE [FILE FILE...]` vypíše soubor, v případě více souborů vypíše všechny (concatenate)
- `head FILE` vypíše prvních 10 řádků
  - `-n LINES` vypíše prvních N řádků
- `tail FILE` vypíše posledních 10 řádků
  - `-n LINES` vypíše posledních N řádků
  - `-f` vypíše posledních 10 řádků a automaticky vypisuje změny, tedy nové řádky
- `less FILE` zobrazí soubor v laginátoru, můžeme procházet nahoru i dolů, ukončení <kbd>q</kbd>
- `more FILE` vypíše soubor na terminál, <kbd>Enter</kbd> pro řádek, <kbd>Mezerník</kbd> pro stranu

---
---

- `sort FILE` seřadí řádky a vypíše
    - `-r` reverzní řazení
    - `-R` náhodné řazení
    - `-n` číselné řazení
    - `-u` seřadí a odstraní duplicity
- `uniq FILE` odstraňuje duplicity, ale jen pokud jdou za sebou (proto kombinace se sort)
    - `-c` odstraňuje duplicity a počítá výskyty 
- `grep PATTERN FILE` vypíše řádky na kterých byla nalezena shoda s patternem
  - `-c` vypíše počet řádků odpovídající patternu
  - `-P` pro Perl regexp
- `wc FILE` vypíše počet řádků, slov a bajtů
  - `-l` počet řádků
  - `-m` počet znaků
  - `-w` počet slov
  - `-L` délku nejdelšího řádků
