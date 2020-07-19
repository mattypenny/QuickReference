---
title: "bash"
date: "2012-12-25"
draft: false
---
> The 'quick reference' pages here are 'crib files', which I typically use from the command line. They were originally plain text files which I grep-ed, or findstr-ed, or select-string-ed to look up bits of syntax
>
> More recently I've put some markdown tags around the text so that I could put them up here.


## awk & sed
````
awk -F= '{printf("%-20.20s  %-10.10sn", $2,  $1)}' *text

---

awk /Matt/ xx.txt        # same as grep

---

awk -F:            # use : as field delimiter

---

awk -f:            # use file

---

awk '/ystem/{print substr($0,10,6)}' xx

---

awk '/yst/||/racle/{print substr($0,10,6)}' xx     # either / or

---

awk '$1=="&gt;"{print $1}' xx

---

````
### files bigger than...
````

ls -Rl | awk '$5>109880{print $0}'
````

---

### think this replaces CRs with #

````
awk '{RS="#";FS="n";print $1,$2,$3,RS}' xx
````

---

### think this replaces # with CRs

````
awk -vRS="## '{print $0}'
````

---

````
awk 'substr($0, 43,9)!="None     "{print $0}' *
````

---

### deletes lines with Elephant

````
sed "/Elephant/d"
````

---
````

egrep -v "showlock|------|^$|Mode Reqd" * | awk 'substr($0, 50,9)!="None" {print $0}'
````

---

### Awk totalling:

````
BEGIN {TOT=0}

{

 TOT+=$1;

 print $1 }

END {print "bye " TOT}
````

---

````
/^Ck/{printf ("n%s ", $2)}

/free/{printf ("%dG" ,$1/(1024*1024*1024) )}

/ORA-/{printf ("n%14s",substr($0,1,14))}

psservice \ server1 | findstr   SERVICE_NAME STATE" | awk95 "

/SERV/{printf ORS} {printf $0} "

dir /B /S "N:folder1" | p:swarebingawk "{print "74a href=42" $0 "4276" $0 "7457a76" "74br76"  }" &gt; x.txt
````


#### Edit everything modified in October, possibly

````
vi $(ls -l $(locate *.log) | grep Oct | awk '{print substr($0, 52, 150)}')
````

#### Loops 

````
for i in 1 2 3 4 5; do  echo $i; done                    # bashloop

for i in "1 2 3 4 5"; do  echo $i; done                  # bashloop

for i in {1..10000}; do  echo $i; done                   # bashloop

for i in *; do file $i; done                             # bashloop

#!/bin/bash
x=1                                                      # bashloop
while [ $x -le 5 ]                                       # bashloop
do                                                       # bashloop
 echo "Welcome $x times"                                 # bashloop
 x=$(( $x + 1 ))                                         # bashloop
done                                                     # bashloop
````

