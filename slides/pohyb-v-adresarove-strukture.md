---
class: 'text-center'
layout: cover
background: images/folders.jpg
---

# Pohyb v adresářové struktuře

---
layout: two-cols
---

## Speciální znaky

- `.`: Aktuální adresář
- `..`: Nadřazený adresář
- `~`: Domovaný adresář (tilda)
- `/path/to/somewhere`: Absolutní cesta začíná `/` 
- `path/to/somewhere`: Relativní cesta

::right::

```console
usr@com:/demo$ tree
.
├── dir1
│   ├── a
│   ├── b
│   └── c
└── dir2
    ├── a
    ├── x
    │   └── file
    └── y

8 directories, 1 file
usr@com:/demo$ ls /demo/dir1/a
usr@com:/demo$ ls dir2/x
file
usr@com:/demo$ ls dir2/x/../x/file
dir2/x/../x/file
```

---
layout: two-cols
---

## List

- `ls [PATH]`: Vypíše obsah adresáře
  - `-l`: Podrobný výpis
  - `-l -h`: Podrobný výpis s "lidsky čitelnými" jednotkami velikosti
  - `-a`: Vypíše "all", tedy vše včetně skrytých souborů (začínají `.`)
  - `-A`: Vypíše téměř vše, vynechá referenci na stávající a nadřazený adresář (`.` a `..`)

Můžeme se běžně setkat s `ll`, jedná se o alias pro `ls -al`. Některé distribuce ho mají by default povolený.
Bývá předepsaný, ale nepovolený v `.bashrc`.

::right::

```console
usr@com:/demo$ ls
dir1  dir2
usr@com:/demo$ ls -l
celkem 8
drwxrwxr-x 5 usr usr 4096 bře 11 17:50 dir1
drwxrwxr-x 5 usr usr 4096 bře 11 17:50 dir2
usr@com:/demo$ ls -l -h
celkem 8,0K
drwxrwxr-x 5 usr usr 4,0K bře 11 17:50 dir1
drwxrwxr-x 5 usr usr 4,0K bře 11 17:50 dir2
usr@com:/demo$ ls -l -a
celkem 16
drwxr-xr-x  4 usr  usr  4096 bře 11 17:50 .
drwxr-xr-x 21 root root 4096 bře 11 17:49 ..
drwxrwxr-x  5 usr  usr  4096 bře 11 17:50 dir1
drwxrwxr-x  5 usr  usr  4096 bře 11 17:50 dir2
-rw-rw-r--  1 usr  usr     0 bře 11 17:50 .hidden
usr@com:/demo$ ls -l -A
celkem 8
drwxrwxr-x 5 usr usr 4096 bře 11 17:50 dir1
drwxrwxr-x 5 usr usr 4096 bře 11 17:50 dir2
-rw-rw-r-- 1 usr usr    0 bře 11 17:50 .hidden
```

---
layout: two-cols
---

## Print Working Directory & Change Directory

- `pwd`: Vypíše aktuální adresář
  - `-L`: Pro logickou cestu
  - `-P`: Pro fyzickou cestu
- `cd PATH`: Změní aktivní adresář na zadanou cestu
- `cd` & `cd ~`: Změní aktivní adresář na domovský adresář
- `cd -`: Změní aktivní adresář na poslední aktivní

::right::

```console
usr@com:/demo$ pwd
/demo
usr@com:/demo$ cd dir1
usr@com:/demo/dir1$ cd ../dir2
usr@com:/demo/dir2$ pwd
/demo/dir2
usr@com:/demo/dir2$ cd -
/demo/dir1
usr@com:/demo/dir1$ pwd
/demo/dir1
usr@com:/demo/dir1$ cd ~/../../demo
usr@com:/demo$ pwd
/demo
usr@com:/demo$ echo $HOME
/home/usr
```

---
layout: two-cols
---

## Make Directories & Touch

- `touch PATH`: Aktualizuje timestamp souboru nebo adresáře, pokud soubor neexistuje pak se vytvoří
  - `-c`: Nevytvoří nový soubor
  - `-r FILE1 FILE2` aktualizuje timestamp dle zadaného souboru za přepínačem `-r`
- `mkdir PATH`: Vytvoří adresář
  - `-m UGO PATH`: Vytvoří adresář s právy dle `UGO`
  - `-p PATH`: Vytvoří celou adresářovou strukturu pokud je třeba

::right::

```console
usr@com:/demo$ mkdir dir3/abc
mkdir: adresář „dir3/abc“ nelze vytvořit: Adresář 
nebo soubor neexistuje
usr@com:/demo$ mkdir dir3
usr@com:/demo$ mkdir dir3/abc
usr@com:/demo$ touch dir3/abc/empty-file
usr@com:/demo$ mkdir -p dir4/abc
usr@com:/demo$ tree
.
├── dir1
│   ├── a
│   ├── b
│   └── c
├── dir2
│   ├── a
│   ├── x
│   │   └── file
│   └── y
├── dir3
│   └── abc
│       └── empty-file
└── dir4
    └── abc

12 directories, 2 files
```

---
layout: two-cols
---

## Move

- `mv PATH1 PATH2`: Přesouvá nebo přejmenovává jak adresáře tak soubory
  - `-b`: Vytvoří zálohu před přepsáním
  - `-i`: Před přepsáním se zeptá
  - `-f`: Naopak se neptá před přepsáním
  - `-n`: Nepřepíše pokud již soubor existuje
  - `-u`: Přemístí pouze starší soubory

::right::

```console
usr@com:/demo$ mv dir4 dir-x
usr@com:/demo$ ls
dir-x  dir1  dir2  dir3
usr@com:/demo$ mv -ib dir3/abc/empty-file dir2/x/file 
mv: přepsat 'dir2/x/file'? y
usr@com:/demo$ ls dir2/x/
file  file~
usr@com:/demo$ tree
.
├── dir-x
│   └── abc
├── dir1
│   ├── a
│   ├── b
│   └── c
├── dir2
│   ├── a
│   ├── x
│   │   ├── file
│   │   └── file~
│   └── y
└── dir3
    └── abc
```

---
layout: two-cols
---

## Remove

- `rm PATH`: Odstraní soubory
  - `-r`: Maže adresáře a soubory rekurzivně, tedy celou větev
  - `-i`: Ptá se před odstraněním
  - `-f`: Ignoruje neexistující soubory a argumenty, nikdy se neptá
  - `-d`: Maže prázdné adresáře

::right::

```console
usr@com:/demo$ rm -d dir-x
rm: nelze odstranit 'dir-x': Adresář není prázdný
usr@com:/demo$ rm -d dir-x/abc/
usr@com:/demo$ rm -d dir-x
usr@com:/demo$ rm dir3/abc
rm: nelze odstranit 'dir3/abc': je adresářem
usr@com:/demo$ rm -rf dir*
usr@com:/demo$ ls
```

---
layout: two-cols
---

## File

Pomocník na závěr, příkaz který dle hlavičky odhaduje co za soubor to je.

- `file FILE` prozradí drobné informace o souboru. Znaková sada, spustitelnost, ale i jsou možné i informace o programovacím jazyce, verze a další.
  - `-b` nevypíše název souboru na začátku výstupu.
  - `--mime-type` vrátí typ souboru dle MIME specifikace.

💡 _Pamatujme že Linux/Unix systémy nekoukají na koncovky. Když tak na hlavičku (první řádek či řádky souboru)._

::right::

```console
$ file --mime-type Obrázky/rss.jpeg
Obrázky/rss.jpeg: image/jpeg
$ file Obrázky/rss.jpeg
Obrázky/rss.jpeg: JPEG image data, 
JFIF standard 1.01, resolution (DPI), 
density 96x96, segment length 16, baseline, 
precision 8, 500x500, components 3
$ file Obrázky/DSC_0750.webp 
Obrázky/DSC_0750.webp: RIFF (little-endian) data, 
Web/P image
$ file kube-bulk.sh 
kube-plosne.sh: ASCII text
$ file .bin/bcrypt-hash 
.bin/bcrypt-hash: Python script, ASCII text executable
$ file .bin/run-hoi4
.bin/run-hoi4: Bourne-Again shell script, 
ASCII text executable
```