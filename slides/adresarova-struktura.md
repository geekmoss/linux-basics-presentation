---
class: 'text-center'
layout: cover
background: images/folders.jpg
---

# Adresářová struktura

---
layout: two-cols
---

## Filesystem Hierarchy Standard

Linuxové distribuce se typicky řídí [FHS](https://cs.wikipedia.org/wiki/Filesystem_Hierarchy_Standard).



::right::

```console
$ tree -L 1
/
├── bin -> usr/bin
├── boot
├── cdrom
├── dev
├── etc
├── home
├── lib -> usr/lib
├── libx32 -> usr/libx32
├── lib32 -> usr/lib32
├── lib64 -> usr/lib64
├── lost+found
├── media
├── mnt
├── opt
├── proc
├── root
├── run
├── sbin -> usr/sbin
├── srv
├── sys
├── tmp
├── usr
└── var
```

---
---

## Systémové adresáře

- `/boot` - Bootloader, kernel
- `/dev` - Devices, jsou zde soubory reprezentující zařízení, rozhraní, drivery a další
- `/proc` - Virtual fs obsahující soubory obsahující informace o procesech, systémových zdrojů, atd.
  - `cat /proc/cpuinfo` - Můžete si vyzkoušet vypsat info o procesoru
- `/run` - Run-time data, obdoba proc, jen není virtuální, najdete zde up-time, přihlášené uživatele, atd.
- `/lib` - Knihovny pro `/bin` a `/sbin`, obdobné adresáře pak najdeme `/usr/lib`, `/usr/local/lib`

---
---

## Uživatelsky podstatné systémové adresáře

- `/bin`, `/usr/bin/`, `/usr/local/bin` - Binárky, spustitelné soubory
  - `/sbin`, `/usr/sbin`, `/usr/local/sbin`
- `/etc` - Ecetera, Editable Text Configuration, Extended Tool Chest
- `/home/<user>`, `/root` - domovské adresáře
- `/media` - Připojitelná zařízení (optické mechaniky, flashdisky, SD karty, atd)
  - `/cdrom` - Může se vyskytovat specifický adresář na optické mechaniky
- `/mnt` - Připojené adresáře (jiné disky, síťové adresáře, lokální adresáře)
- `/opt` - Optional, jde o dodatečný software mimo package managment
- `/srv` - Adresář pro serverová data, typicky sdílení ale i třeba data webů zde mohou být
- `/var` - Proměnné soubory - logy, cache, zálohy a podobně, sídlí zde ale i data pro weby (`/var/www`)
- `/tmp` - Dočasné soubory, při restartování se mažou, všichni zde mají práva

---
layout: two-cols
---

## Oprávnění

Rozlišujeme tři privilegie pro tři typy uživatelů. Jde o čtení `r`, zápis `w` a spuštění `x` u 
uživatelů typu vlastník `u`, skupina `g` a ostatní `o`.

### UGO/RWX

| Privilegium        | Bin mask | Oktalový zápis |
|:-------------------|----------|----------------|
| Čtení / Read       | `100`    | 4              |
| Zápis / Write      | `010`    | 2              |
| Spuštění / eXecute | `001`    | 1              |

::right::

![ugorwx](https://wiki.quvy.eu/images/1/13/Permissions.png)

---
---

## Úprava práv

- `chown USER[:GROUP] PATH`: Změní vlastníka, může měnit i skupinu při zápisu `usr:grp`
  - `-R`: Rekurzivní uplatnění
  - `--from=USER[:GROUP]`: Změní pouze pokud soubory které již vlastní zadaný uživatel
- `chgrp [-R] GROUP PATH`: Změní pouze skupinu
- `chmod [-R] UGO PATH`: Změní oprávnění
  - `chmod u=rwx,g+rw,o-rwx PATH`: Textový zápis, umožňuje i relativní přidání/odebrání
  - `chmod 760 PATH`: Kompletní přepsání oprávnění
  - `chmod +x PATH`: Jeden z nejčastějších příkazů, přidání spustitelnosti pro všechny

---
layout: two-cols
---

## Právo ~~sedět~~ být spuštěn


Příznak / právo spuštění může být matoucí, typicky protože skripty
spouštěníme třeba `bash run.sh` nebo `python run.py`.

Klíčové to ale je v momentě kdy máme binární soubory nebo chceme odbourat explicitní interpret nebo "spouštěč".

⚠️ _U skriptů je důležitý shebang, který definuje čím má být skript spuštěný._

::right::

```console
usr@com ~ $ ll pozdrav.sh 
-rw-rw---- 1 usr usr čen  7 19:56 pozdrav.sh
usr@com ~ $ bash pozdrav.sh 
Ahoj světe!
usr@com ~ $ ./pozdrav.sh
bash: ./pozdrav.sh: Operace zamítnuta
usr@com ~ $ chmod +x pozdrav.sh
usr@com ~ $ ll pozdrav.sh 
-rwxrwx--x 1 usr usr 32 čen  7 19:56 pozdrav.sh
usr@com ~ $ ./pozdrav.sh
Ahoj světe!
```

---
layout: two-cols
---

## Spouštění adresáře?

Při využití shrnutí z [vysvělení \[StackExchange\]](https://unix.stackexchange.com/a/21263):

**Read:** Můžeme číst položky ze seznamu souborů

**Write:** Můžeme přidávat, přejmenovávat a odstraňovat položky ze seznamu (nutný execute)

**Execute:** Můžeme mít adresář jako working directory

::right::

```console
$ mkdir dir; touch dir/file; ls -l  # Příprava
drwxrwxr-x 2 moss moss 4096 bře 15 17:22 dir
$ ls -l dir/
-rw-rw-r-- 1 moss moss 0 bře 15 17:22 file

$ chmod -x  # Bez práva ke spuštění
$ ls -l
drw-rw-r-- 2 moss moss 4096 bře 15 17:22 dir
$ ls -l dir
ls: nelze přistoupit k 'dir/file': Operace zamítnuta
-????????? ? ? ? ?            ? file
$ cat dir/file
cat: dir/file: Operace zamítnuta

$ chmod +x-r  # Bez práva ke čtení
$ ls -l
d-wx-wx--x 2 moss moss 4096 bře 15 17:22 dir
$ ls -l dir
ls: adresář 'dir' nelze otevřít: Operace zamítnuta
$ cat dir/file  # Funguje, soubor je prázdný
```

---
layout: two-cols
---

## Hardlink & Softlink

**Hardlink** dědí práva, přebírá obsah a zabírá stejné místo. Svým způsobem se jedná o kopii
s tím že se aktualizuje. Pokud původní soubor odstraníme zruší se i vazba, kopie zůstane. Pokud
se soubor znovu vytvoří, vazba se neobnoví.

**Softlink** či také **symlink** je odkaz a na disku zabíná pouze nejmenší alokovatelnou jednotku (typicky 4 bajty).
Po odstranění cíle nastane "brokenlink", který se ale obnoví pokud se cíl znovu objeví.

- `ln SOURCE LINK` pro hardlink
- `ln -s SOURCE LINK` pro softlink

::right::

```console
usr@com ~ $ echo "Ahoj světe" > file
usr@com ~ $ ln file file.link
usr@com ~ $ ln -s file link.symlink
usr@com ~ $ ls -l
-rw-r--r-- 2 usr usr 12 čen  8 07:57 file
-rw-r--r-- 2 usr usr 12 čen  8 07:57 file.link
lrwxrwxrwx 1 usr usr  4 čen  8 07:54 file.symlink -> file
usr@com ~ $ echo "123456789" > file
usr@com ~ $ rm -f file
usr@com ~ $ ls -l
-rw-r--r-- 1 usr usr 10 čen  8 08:00 file.link
lrwxrwxrwx 1 usr usr  4 čen  8 07:54 file.symlink -> file
usr@com ~ $ cat file.link
123456789
usr@com ~ $  cat file.symlink
cat: file.symlink: No such file or directory
usr@com ~ $ echo "Hello world" > file
usr@com ~ $ cat file
Hello world
usr@com ~ $ cat file.link
123456789
usr@com ~ $ cat file.symlink
Hello world
```