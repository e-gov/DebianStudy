# Debian pakendamise eneseõpe

Repos on kokkuvõtlikku teavet Debian tarkvaralevitussüsteemi kohta ja lihtne näide Debian paki koostamise ja paigaldamise kohta.

# Debian tarkvaralevitussüsteem

(kokkuvõtlikult olulisest)

**Pakk** (Debian _package_) on Debian pakiformaadile ja pakendusreeglitele vastav kogum tarkvara, kas lähte- või täidetava koodina. Lisaks koodile on pakis ka paigaldusskriptid ja metateave. Pakk moodustab faili (`.deb` või `.dsc`). Debian pakke saab kasutada Linux op-süsteemi mitmetes variantides, sh Ubuntu-s. Debian paki kohta vt lähemalt allpool jaotis "Debian pakk".

**Pakirepo** (ka: pakiarhiiv, _package repository_, _package archive_) on teenus, kust saab masinasse alla laadida pakke. Pakkide paigaldamisel on oluline, millistele pakirepodele masin ligi pääseb. Pakirepode nimekiri on masinas failis `/etc/apt/sources.list`.

**Pakihaldur** (_package manager_) on vahend pakkide allalaadimiseks, paigaldamiseks jm toiminguteks paki kogu elukaare ulatuses. Debian pakkide haldamiseks saab kasutada mitut erineva taseme vahendit: `dpkg` (Debian Package Management System) on peamine vahend, sellel on pealisvahend `apt` (APT, Advanced Package Tool), millel omakorda on pealisvahend `aptitude`.

## Debian pakk (ülevaade)

Tehniliselt on Debian pakk Unix `ar` arhiiv, milles omakorda on kaks `tar` arhiivi. Ühes neist `tar` arhiividest on ohjeteave, teises on paigaldatavad andmed. Lisaks on pakis pakiformaadi versiooni näitav fail:

- `debian-binary` - üherealine fail pakiformaadi versiooninumbriga
- `control.tar` - ohjearhiiv, sisaldab paki metateavet ja haldusskripte
- `data.tar` - andmearhiiv, sisaldab paigaldatavaid andmeid.

Ohjearhiivis on failid:

- `control` - kokkuvõtlik teave paki kohta, sh paki sõltuvused
- `md5sums` - kontrollsummad
- `conffiles` - konf-ifailidena käsitletavate failide nimekiri; neid faile ei kirjutata paki paigaldamisel üle
- `preinst`, `postinst`, `prerm`, `postrm` - enne/pärast paigaldamist v eemaldamist täidetavad skriptid
- `config` - valikuline skript, millega saab paigaldamist täiendavalt seadistada
- `shlibs` - ühiste teekide sõltuvuste nimekiri.

Pakk võib olla lähtekoodipakk (_source package_, `.dsc`)  või täidetava koodi pakk (_binary package_, `.deb`). Lähtekoodipakis on kood koos juhistega täidetava tehise ehitamiseks. Täidetava koodi pakis on valmisehitatud täidetav(ad) tehis(ed) - see (need) tuleb ainult masinasse paigaldada.

## Teave Debiani pakisüsteemi kohta

Debian pakisüsteemi kohta on mitmeid põhjalikke juhendit:

- [Debian New Maintainers' Guide](https://www.debian.org/doc/manuals/maint-guide/index.en.html)
- [Ubuntu and Debian Package Management Essentials](https://www.digitalocean.com/community/tutorials/ubuntu-and-debian-package-management-essentials)
- [Debian Policy Manual](https://www.debian.org/doc/debian-policy/index.html) - Debiani tarkvaralevitussüsteemi omadused, reeglid ja nõuded Debian pakkidele.

Neist oma kasutusjuhu jaoks vajaliku ülesleidmine siiski võib olla keeruline. Mõned praktilisemad juhised on:

- [What is the simplest Debian Packaging Guide?](
https://askubuntu.com/questions/1345/what-is-the-simplest-debian-packaging-guide) 


Konkreetse paki ehitamisel on vahenditest võib-olla kõige kasulikumad:

`dpkg-deb`
- "packs, unpacks and provides information about Debian archives" - 
- [juhend](https://manpages.debian.org/stretch/dpkg/dpkg-deb.1.en.html)

`dpkg`
- package manager for Debian
- [juhend](https://manpages.debian.org/stretch/dpkg/dpkg.1.en.html)

## Rakenduse "Hello, World!" (Bash skript) pakendamine

(lihtne näide Debian paki koostamise ja paigaldamise kohta)

Vastavalt juhendile: https://stackoverflow.com/questions/1382569/how-do-i-do-debian-packaging-of-a-python-package/25275227#25275227. 

1  Tegin kausta `HelloWorld-1.0`

Kaust saab sisaldama rakendust ja Debian pakendamise abiteavet (ohjefaile).
`HelloWorld` on rakenduse nimi ja `1.0` on rakenduse versiooninumber (Debiani paki mõistes).

2  Tegin skripti

- alamkausta `usr/bin`
- ja sinna skripti `HelloWorld.sh`

````
#!/bin/bash

echo 'Hello, World!'
````

Kaustastruktuur peegeldab skripti ettenähtud asukohta paigaldusmasinas.

3  Tegin DEBIAN ohjefaili:

- alamkausta `DEBIAN`
- faili `DEBIAN/control`

````
Package: HelloWorld
Version: 1.0
Architecture: all
Maintainer: Priit Parmakson <priit.parmakson@gmail.com>
Depends: 
Installed-Size: 1
Homepage: http://example.com
Description: "Hello, World!" Bash skript
 "Hello, World!" Bash skript Debian pakendamise õppimiseks.
````

Vajadusel saab samass kausta luua ka ohjefailid `changelog`, `rules` jt.

4  Seadsin omaniku ja õigused:

- `sudo chown root:root -R HelloWorld-1.0`
- `sudo chmod 0755 HelloWorld-1.0/usr/bin/HelloWorld.sh`

Seadmised on vajalikud selleks, et Debian pakikoostamisvahend saaks `control` faili kasutada ja paki paigaldamise järel saaks skripti käivitada.

5  Käivitasin paki ehitamise:

`dpkg --build HelloWorld-1.0`

Annab teate: `dpkg-deb: building package 'helloworld' in 'HelloWorld-1.0.deb'`

6  Kontrollisin ehitatud pakki:

- `dpkg --info HelloWorld-1.0.deb` (annab teabe paki kohta)
- `dpkg --contents HelloWorld-1.0.deb` (annab pakis olevate failide nimekirja)

7  Paigaldasin paki:

`dpkg --install HelloWorld-1.0.deb`

Märkus. `dpkg --install` on nn madalataseme paigaldusmeetod. Sellega ei paigaldata autommaatselt sõltuvusi. Kõrgema taseme meetodid (millega paigaldatakse ka sõltuvused) on:

- `sudo apt-get install -f` (lipp `-f` käsib vajadusel üritada parandada katkist paigaldust) ja
- `sudo apt install`. Vt: https://unix.stackexchange.com/questions/159094/how-to-install-a-deb-file-by-dpkg-i-or-by-apt.

8  Igaks juhuks vaatasin, et paigaldatud pakkide "andmebaas" (dpkg andmebaas) on olemas ja paki paigaldamisega on seal toimunud muutusi (kuupäevade järgi otsustades):

`ll /var/lib/dpkg`

9  Kontrollisin, et pakk `helloworld` on paigaldatud. Kõigepealt kõrgetaseme vahendi `apt` abil:

`sudo apt list --installed` (annab pika nimekirja)

`sudo apt list --installed | grep helloworld` (filtreerisin huvipakkuva paki)

Seejärel madalataseme vahendiga `dpkg-query`, saades detailsemat teavet paki kohta:

`dpkg-query -l | grep helloworld`

10  Kontrollisin, et pakis sisalduv fail (`HelloWorld.sh`) tõesti on kantud ettenähtud kausta (`/usr/bin`):

`ll /usr/bin/HelloWorld.sh`

11  Kontrollisin, et ülekandmisel on seatud ka ettenähtud failiomanik ja õigused.

12  Käivitasin paigaldatud faili (`/user/bin/HelloWorld.sh`):

`source /usr/bin/HelloWorld.sh`

ja kontrollisin, et skript töötab ettenähtud viisil.
