# 6.LABORATORIJSKE VJEŽBE

## **Online and Offline Password Guessing**

Za svakog studenta je podignut račun na serveru. Cilj cježbe je pomoću online i offline pogađanaja lozinke preko lokalnog računala.

**Koristili smo:**

**Nmap(”Network Mapper”)—>**free and open-source tool used for network discovery and security auditing.

**Hydra—>** tool used for performing brute force attacks on a variety of protocols, including web forms, ssh, and smtp, to try to guess the login credentials of a remote system.

## Online password guessing

![slikaLLabb.JPG](6%20LABORATORIJSKE%20VJEZ%CC%8CBE%20559f11ad2d0b4cdcbf635c361da4a8f0/slikaLLabb.jpg)

Imamo informaciju da lozinka ima 4 - 6 lowercase letters—>Password space: 26^4+ 26^5+26^6

Za povezivanje i upravljanje poslužiteljem koristili smo naredbu:

`ssh mlinarevic_ivona@mlinarevicivona.local`

Za online password guessing koristimo hydru (naredbom `hydra -l doe_john -x 4:6:a doejohn.local -V -t 1 ssh` ) i pokušavamo pogoditi password pomoću brute-force-a (bez stvorenog dictionary-a). Za svaku pokušanu lozinku šalje se zahtjev na server koji onda radi usporedbu s lozinkom već upisanom u bazi podataka te u slučaju da se lozinke poklapaju dobivamo pristup računalu. Ovaj proces je iznimno spor(password space 26^6).

Lozinku bi brže dobili korištenjem unaprijed pripremljenog dictionary-a, kojeg smo uzeli naredbom `wget -r -nH -np --reject "index.html*" http://challenges.local/dictionary/g2/`

te ga koristimo naredbom:

 `hydra -l doe_john -P dictionary/g1/dictionary_online.txt doejohn.local -V -t 4 ssh`

U dictionary-u se nalaze najčešće korištene lozinke koje su već unaprijed hashirane te je ovaj napad puno brži od prethodnog iako bi nas vjerojatno sistem blokirao na neko vrijeme nakon nekoliko neuspješnih pokušaja što bi nas dosta usporilo.

## Offline password guessing

Ideja za offline guessing je da ne moramo za svaku pokušanu lozinku slati upit na server već da samo uspoređujemo hash vrijednosti lozinki koje pokušavamo s onim hash vrijednostima koje su zapisane u bazi(koje su nam poznate). U ovom prisupu je također bolja opcija koristiti pripremljeni dictionary.

Tako smo za ovaj zadatak znali da je se password stastoji od 6 lowercase slova  i koristili sljedeće naredbe:

`hashcat --force -m 1800 -a 3 hash.txt ?l?l?l?l?l?l --status --status-timer 10`

`hashcat --force -m 1800 -a 0 password_hash.txt dictionary/g2/dictionary_offline.txt --status --status-timer 10`

(*Hashcat —>*za probijanje hashirane lozinke iz različitih vrsta hash-eva)