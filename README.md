#!/bin/bash
# Skrypt archiwizujący cały katalog domowy użytkownika do pliku tar.gz
tar -czvf ~/archiwum_home.tar.gz ~/* 2>/dev/null
echo "Utworzono archiwum: ~/archiwum_home.tar.gz"


#!/bin/bash
# Skrypt rozpakowujący wcześniej utworzone archiwum do katalogu domowego
if [ -f ~/archiwum_home.tar.gz ]; then
    tar -xzvf ~/archiwum_home.tar.gz -C ~/
    echo "Archiwum rozpakowane."
else
    echo "Brak pliku archiwum_home.tar.gz!"
fi


#!/bin/bash
# Skrypt tworzący kopię zapasową pliku z kontrolą wersji
numer_indexsu="12345"
katalog_backup="$HOME/$numer_indexsu"
plik_zrodlowy="$1"

if [ -z "$plik_zrodlowy" ]; then
    echo "Użycie: $0 [nazwa_pliku]"
    exit 1
fi

mkdir -p "$katalog_backup"

if [ -f "$katalog_backup/$(basename "$plik_zrodlowy")" ] && [ "$plik_zrodlowy" -ot "$katalog_backup/$(basename "$plik_zrodlowy")" ]; then
    echo "Plik $(basename "$plik_zrodlowy") jest już aktualny."
else
    cp -uv "$plik_zrodlowy" "$katalog_backup/"
fi


#!/bin/bash
# Skrypt demonstrujący komunikację między procesami przez potok nienazwany
pipe=$(mktemp -u)
mkfifo "$pipe"

# Proces potomny
(
    while read line; do
        echo "Odebrano: $line"
    done <"$pipe"
) &

# Proces rodzica
read -p "Wpisz tekst: " tekst
echo "$tekst" >"$pipe"
rm "$pipe"



#!/bin/bash
# Skrypt serwera nasłuchującego na potoku nazwanym
potok="$1"
mkfifo "$potok"
echo "Serwer nasłuchuje na potoku: $potok"

while true; do
    sciezka=$(cat "$potok")
    ls "$sciezka" >"$potok"
done


#!/bin/bash
# Skrypt klienta wysyłającego ścieżkę katalogu do serwera
potok="$1"
sciezka="$2"

echo "$sciezka" >"$potok"
echo "Wynik ls dla $sciezka:"
cat "$potok"
