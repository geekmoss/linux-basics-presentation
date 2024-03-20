---
class: 'text-center'
layout: cover
background: images/tux-scripting.jpeg
---

# Skriptování

---
layout: two-cols
---

## Shebang

Shebang je hlavička, která shellu řekne čím má daný kód interpretovat.

**Vždy začíná** `!#` a **musí obsahovat absolutní cestu**.

Pokud využijeme prvně `/usr/bin/env`, pak se hledá námi zadaný program v PATH.

Například v příápadě Pythonu se pak právě hodí pokud uživatel například využívá nesystémovou verzi Pythonu.
Buď jde o aktuálnější verze, testovací verze nebo jen lokální prostředí pro závislosti.

::right::

```sh
#!/bin/bash

# Bash code
```

```sh
#!/usr/bin/env bash

# Bash code
```

```py
#!/usr/bin/python

# Python code
```

```py
#!/usr/bin/env python

# Python code
```

---
---

## Speciální proměnné

- `$0` program/skript/funkce samotný
- `$1..$9` jednotlivé argumenty
- `$#` Počet argumentů
- `$@` Všechny argumenty, dílčí řetězce, pokud se použije `"$@"` je stejné jako `$*`
- `$*` Všechny argumenty, jako jeden řetězec
- `$?` Návratový kód posledního příkazu (exit, return, result code)
- `$$` Proces ID aktuálního skriptu

- `$USER` & `$HOSTNAME` proměné z env
- `$SECONDS` Délka běhu skriptu v sekundách
- `$LINENO` Číslo řádku v aktuálním skriptu

---
---

## Zřetězení a návratové kódy

Můžeme řetězit příkazy, různými způsoby a chováním.

```sh
# Doslova jen fronta příkazů
mkdir /tmp/dir; touch /tmp/dir/file

# Pokud první příkaz dopadne dobře, pak se stane druhý
mkdir /tmp/dir && touch /tmp/dir/file

# Pokud první příkaz dopadne dobře, druhý se neprovede
echo ahoj || echo hello
```

Zda příkaz proběhne v pořádku se vyhodnocuje na základě návratového kódu.
Ten by měl být v rozsahu 0-255. Kdy `0` se vždy bere jako úspěšné provedení. Vše ostatní je problém.

Specificky některé programy a skripty mohou mít řečeno že kód `1-9` jsou jen warningy, ale to musíte
pak řešit vy. Shell k tomu vždy přistupuje jako k problému.

```console
$ echo ahoj; echo $?
$ ls /non-existing/path 2> /dev/null; echo $?
```

---
---

## Samotné skriptování - příklad funkce

Dobrý pomocník je https://devhints.io/bash, zde najdete popis všeho v základu podstatného.

```bash
function pypi-search() {
    if [[ -z "${INDEX}" ]]; then
        _INDEX="https://pypi.repo/simple/"
    else
        _INDEX="${INDEX}"
    fi

    if [[ "$1" == "--old-index" ]]; then
        _INDEX="https://pypi.old/simple/"
        shift
    elif [[ "$2" == "--old-index" ]]; then
        _INDEX="https://pypi.old/simple/"
    fi
    # Stáhne se HTML, vygerou se jen odkazy, sedem se vyparsují názvy balíkčů a nad nimi se pustí grep
    curl -s "${_INDEX}" | grep -P '<a href=".*">.*<\/a>$' | sed -E 's/.*<a href=".*">(.*)<\/a>$/\1/g' | grep -P "$1"

    unset -v _INDEX
}
```

---
---

## Příklad skriptu

```sh
#!/bin/bash

export HADOOP_USER_NAME=my-user
YEAR=2023

for topic in `cat topic_to_check` ; do
  for month in {01..12}; do
    # Když ne, tak zkontrolovat diff listu
    hadoop fs -ls "webhdfs://old-host/$topic/$YEAR/$month/*/*/*" | tr -s " " | cut -d " " -f5 > orig
    hadoop fs -ls "hdfs://new-host/$topic/$YEAR/$month/*/*/*" | tr -s " " | cut -d " " -f5 > new
    diff orig new > /dev/null 2>&1
    DIFF_EXIT=$?
    # Kontrola. Diff nutně nemusí znamenat totální fail
    # Tady taky může dojít k tomu že wildcard maska nebude odpovídat
    echo "${topic} - ${month}: result $DIFF_EXIT"
  done
done
```

---
---

## Příklad entrypointu / wrapperu

Občas potřebujeme nějaký příkaz obalit třeba fixními parametry nebo je třeba udělat něco před a/nebo potom.
Například v k8s právě dělají entrypoint skripty.

V k8s můžeme mít `args: [ "-x", "job1" ]`, toto se předá skrze `$@` na konci skriptu.

```sh
# něco předtím ...

RUN_CONFIGS=""
for FILENAME in $(find /opt/conf -type f -name "*.conf" -o -name "*.properties" | sort -z); do
    if [[ "${FILENAME##*/}" == "main.conf" || "${FILENAME##*/}" == "main.properties" ]]; then
        RUN_CONFIGS="-f ${FILENAME} ${RUN_CONFIGS}"
    else
        RUN_CONFIGS="${RUN_CONFIGS} -f ${FILENAME}"
    fi
done

exec ${APP_HOME}/bin/agent -c ${APP_HOME}/conf ${RUN_CONFIGS} \
      -XX:+UseParNewGC \
      ${JMX_OPTS} ${METRICS_OPTS} $@  # Zde se předají předají parametry které dostal entrypoint
```