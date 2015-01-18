#Wstęp 
Realizacje zadań prowadziłem na PC z wykorzystaniem maszyn wirtualnych linuxa oraz windowsa 7.
PC host posiadał takie parametry:

Procesor: Intel Core i5 3470 4x3.20 GHz

RAM: DDR3 8GB 1600MHz Kingston HyperX XMP Beast (2x4GB, DualDDR, CL9)

Dysk Twardy: WD Red 1TB WD10EFRX (64MB, SATA/600)

System : Windows 7 Professional x64

#Zadanie 1a 
Usunięcie znaków nowych lini z pliku Train.csv aby poprawnie zaimportować plik do bazy danych.
Wykorzystanie tego skryptu.
```sh
	#! /bin/bash

if [ -z "$1" ] ; then
  echo "First replaces all the \\n with spaces then replaces all the \\r with \\n"
  echo "Replace header line with: _id,title,body,tags (for mongoDB)"
  echo "usage: $0 input.csv output.csv"
  exit 1;
fi

tr '\n' ' ' < "$1" | tr '\r' '\n' > "$2"
sed -i '$ d' "$2"

sed -i '1 c "_id","title","body","tags"' "$2"
```

Wykonanie komendy w bashu:
```sh
student@virtualbox:~/share/train$ time ./2unix.sh Train.csv train3.csv
real	27m45.500s
user	0m53.820s
sys	12m57.488s
student@virtualbox:~/share/train$
```

Zaimportowanie pliku csv na Winowsie.

```sh
	Measure-Command  {.\mongoimport.exe -d train -c train --type csv --headerline --file E:\train\train3.csv}
```

Wynik czasu z powershella:

```sh
	
Days              : 0
Hours             : 0
Minutes           : 20
Seconds           : 21
Milliseconds      : 255
Ticks             : 12212557648
TotalDays         : 0,0141349046851852
TotalHours        : 0,339237712444444
TotalMinutes      : 20,3542627466667
TotalSeconds      : 1221,2557648
TotalMilliseconds : 1221255,7648
```

#Zadanie 1b

```sh
	> db.train.count()
	6034195
```

#Zadanie 1c
Aby zrealizować to zadanie użyłem skrypt rozbijający za pomocą spacji string na tablice stringów dla pola tags.
```js
	var conn = new Mongo();
var db = conn.getDB('train');
var bazadanych = db.train.find();
bazadanych.forEach(function (record) {
	if (typeof record.tags === 'string') {
		var tableOfTags = record.tags.split(' ');
		db.train.update(
			{_id: record._id},
			{$set: {tags: tableOfTags}}
		)
	}
});

```

Wynik czasu z powershella:
```sh
> Measure-Command  {.\mongo.exe C:\temp\skrypt.js}


Days              : 0
Hours             : 1
Minutes           : 18
Seconds           : 50
Milliseconds      : 257
Ticks             : 47302570024
TotalDays         : 0,0547483449351852
TotalHours        : 1,31396027844444
TotalMinutes      : 78,8376167066667
TotalSeconds      : 4730,2570024
TotalMilliseconds : 4730257,0024
```