---
class: 'text-center'
layout: cover
background: images/mario-pipes.png
---

# Mario Magic
Pipelining, přesměrování, trubky, StdIn, StdOut, StdErr

---
layout: two-cols
---

# Standardní vstup, výstup a chybový výstup
StdIn, StdOut & StdErr

Se streamy jste se možná již setkali a možná i právě s těmito.

Každý program může číst stdin, výstup posílá na stdout.
Chyby by se měli propisovat na stderr, ale není vždy tomu tak.

Jako stdin může být i uživatelský vstup z klávesnice.

::right::

![streams](https://upload.wikimedia.org/wikipedia/commons/f/f6/Pipeline.svg)

---
---

## Přesměrování

Můžeme přesměrovávat vstup, i výstup aby se na místo na terminál zapisovalo do souboru.


```shell
ls > FILE    # Vytvoří nebo přepíše soubor
ls >> FILE   # Vytvoří soubor nebo přidá data na konec souboru
ls 2> FILE   # Přesměruje chybový výstup, taktéž můžeme použít 2>>
sort < FILE  # Přesměruje soubor na vstup příkazu, ekvivalent k `cat FILE | sort`

cmd > job.log 2> err.log
cmd >> job.log 2>&1
cmd 2>> job.log >&2

head -n 1 data.csv > header.data.csv

# Bullshittery
ls 3>&1 1>&2 2>&3 # Prohození stdout a stderr...

# File descriptors
exec 3>/tmp/file  # Nastavíme si file descriptor
echo ahoj >&3     # Můžeme do něj zapsat
exec 3>&-         # Uzavřeme file descriptor 
cat /tmp/file     # Můžeme si vypsat obsah
```

---
---

## Pipelining

Pipes, či občas i trubky, nám slouží k předávání výstupu jednoho programu pro vstup jiného programu.

Můžeme postupně vytvářet soubory a ty zpracovávat.

```shell
sort slovnik.txt > sorted.slovnik.txt            # Seřazení řádků
uniq -c sorted.slovnik.txt > uniq.slovnik.txt    # Unikátnění s počtem výskytů
sort -n -r uniq.slovnik.txt > count.slovnik.txt  # Seřazení dle počtů, od nejvyšího
rm -f sorted.slovnik.txt uniq.slovnik.txt        # Úklid
```

Na místo toho můžeme elegantně udělat toto:

```shell
sort slovnik.txt | uniq -c | sort -n -r > counter.slovnik.txt
```

Pokud bychom chtěli přeci jen mezikroky uložit, pak se může hodit `tee`, který je doslova T:

```shell
sort slovnik.txt | tee sorted.txt | uniq -c | sort -n -r > counted.txt
```