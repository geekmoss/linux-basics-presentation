---
class: 'text-center'
layout: cover
background: images/bash.png
---

# Bash & Environment

---
layout: two-cols
---

# Bash
**B**ourne **a**gain **Sh**ell

Bash aby poskytl to co vidíte (a můžete využívat) při spuštění se musí nejdříve inicializovat a
nastavit. Jak přesně můžete vidět v diagramu vpravo.

_[Options & Settings](https://wiki.quvy.eu/index.php/Linux:Bash_Options)_


## Pojmy

**Interaktivní shell** je ten který očekává uživatelský vstup.

**Login shell** je první shell který se uživateli spustí při přihlášení.

::right::

![Bash diagram](https://wiki.quvy.eu/images/9/9e/Bash-startup-files.png)

---
---

## Expanze závorek

```console
usr@com: ~ $ ls 1.{png,gif,jpg}  # Expandovat rozsahy
1.gif   1.jpg   1.png
usr@com: ~ $ ls 1.png 1.gif 1.jpg  # Toto zařídí Bash za nás skrze expanzi

usr@com: ~ $ echo {"Hello world","Ahoj světe","Hallo Welt"}
Hello world Ahoj světe Hallo Welt

usr@com: ~ $ echo {1..10}  # Můžeme expandovat rozsahy
1 2 3 4 5 6 7 8 9 10
usr@com: ~ $ echo {1..10..2}
1 3 5 7 9
usr@com: ~ $ echo {a..h}  # Taky ASCII rozsahy
a b c d e f g h
usr@com: ~ $ echo {a..h..3}
a d g

usr@com: ~$ echo 0x{00..10}
0x00 0x01 0x02 0x03 0x04 0x05 0x06 0x07 0x08 0x09 0x10
```

Pamatujte na to že expanze se provadí již v shellu, a tedy program dostane jen více argumentů.
Pokud tedy program nepodporuje více argumentů, pak vám to nepomůže.

---
---

## Globbing

```console
$ ls ~/Pictures/*.gif    # Vypíše všechny soubory končící na .gif v adresáři
$ rm /tmp/*              # Vymaže vše z /tmp
$ cat /usr/share/*/doc   # Vypíše všechny soubory doc v libovolném adresáři, který je v /usr/share
$ cat /usr/share/*/*     # To co výše, jen vypíše všechny soubory ve všech adresářích v /usr/share

$ ls ~/Pictures/cat_?.png       # Taktéž ? pro jeden libovolný znak
$ ls ~/Pcitures/cat_[123].png   # Nebo množinu znaků
$ ls ~/Pcitures/cat_[1-9].png   # Případně ascii rozsah
$ ls ~/Pcitures/cat_[!1-9].png  # Unix-like systémy podporují typicky i negaci
```

Platí to samé co u expanze závorek, prvně se expanduje a poté se předá seznam argumentů.

---
---

## Aliasy

Bash nabízí možnost vytvářet si aliasy, které mohou obsahovat příkazy, hodnoty jako třeba cesty ale i celé 
sekvence příkazů.

```console
$ alias ls='ls --color=auto'
$ ls  # Pokud doteď nebyl, již bude obarvený výstup
$ ls -l  # Funguje i s dalšími parametry

$ alias ll='ls --color=auto -l'
$ ll  # Obarvený a podrobný výstup

$ alias c='cd '  # Mezera je důležitá aby se expandoval další alias
$ alias l='/var/log'
$ pwd
/home/usr
$ c l
$ pwd
/var/log
```

---
layout: two-cols
---

# Proměnné prostředí
Environment

Proměnné prostředí umožňuje přizpůsobovat prostředí pro každého uživatele zvlášť.
Zde se bere informace o domovském adresáři, jménu uživatele a tak podobně.

Vypsat si proměnné prostředí můžeme pomocí příkazu `env`.

*Proměnné prostředí existuje i u Windows, kde se složitěji nastavuje, ale můžete si vyzkoušet například `%USER%` nebo `%HOME%`.*

::right::

```console
$ echo $USER bydlí v $HOME
usr bydlí v /home/usr

$ echo $USER momentálně čumí do $PWD
usr momentálně čumí do /home/usr

$ cd /tmp; cd -
$ echo 'Příkaz "cd -" funguje díky proměnné $OLDPWD'
Příkaz "cd -" funguje díky proměnné $OLDPWD
$ echo "Předchozí adresář byl $OLDPWD"
Předchozí adresář byl /tmp
```

---
transition: fade
---

## PATH

Proměnná `PATH` je neskutečně důležitá. Proč? Zkusme si `PATH=""` a poté třeba jen `ls`.

---
---

## PATH

Proměnná `PATH` je neskutečně důležitá. Proč? Zkusme si `PATH=""` a poté třeba jen `ls`.

Proměnná PATH říká kde se mají hledat programy a skripty ke spuštění. Proto třeba i pokud máme
skript `hello.sh` tak musíme spouštět s relativní cestou `./hello.sh`.

```console
usr@com ~ $ echo $PATH
/home/usr/bin:/usr/local/bin:/usr/bin:/bin
```

Cesty v PATH jsou rozdělené `:` a prochází se postupně k prvnímu výskytu toho co požadujeme.
Proto se uvádí cesty typicky od specifických a bližích uživateli po společné a obecné.

Příklad rozšíření PATH, jedna z nich ale není dobrá volba:
```console
usr@com ~ $ export PATH='/path/to/my/bin:$PATH'
usr@com ~ $ export PATH='/path/to/my/bin'
```

---
layout: two-cols
---

## Proměnné

Bash nabízí proměnné které můžeme zjednodušeně označit za lokální a globální. Většinou se bavíme o 
proměnné a exportované proměnné.

**Lokální** vždy zůstanou ve stávajícím shellu a nepředávají se subshellům ani spuštěným programům.

**Globální** či **exportované** jsou exportované proměnné, které se předají subshellům a spuštěným programům.

Přoměnné se nikdy nepropagují o úroveň výše.

::right::

```console
$ echo 'echo "$VAR1 $VAR2"' > /tmp/skript.sh
$ VAR1='Ahoj'
$ export VAR2='světe'
$ bash /tmp/skript.sh
 světe
```