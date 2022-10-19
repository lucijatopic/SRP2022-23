# Sigurnost računala i podataka

## Lab 1: Man-in-the-middle attack (ARP spoofing)

U prvoj laboratorijskoj vježbi analizirali smo ranjivost *Adress Resolution Protocol*-a *(ARP)* koja napadaču omogućava izvođenje *man in the middle (MitM)* i *denial of service (DoS)* napada na računala koja su dio iste lokalne mreže (LAN-a).

Testirali smo napad u virtualiziranoj Docker mreži koju čine tri virtualizirana Docker računala (eng. *container*) tj. dvije žrtve `station-1` i `station-2` te napadač `evil-station`.

Pokrenuli smo Windows terminal, napravili smo direktorij SRP naredbom mkdir SRP, ušli u taj direktorij naredbom cd SRP. U tom direktoriju napravili smo novi pod naredbom mkdir ltopic i naredbom cd ltopic pozicionirali se u taj direktorij.

 U odgovarajući direktorij klonirali smo GitHub repozitorij naredbom:

`git clone [https://github.com/mcagalj/SRP-2022-23](https://github.com/mcagalj/SRP-2022-23)`

Nakon toga naredbom cd (`cd SRP-2022-23/arp-spoofing/`) ušli smo u direktorij *arp-spoofing/* u kojem su se nalazile skripte [start.sh](http://start.sh) te [stop.sh](http://stop.sh) (one služe za pokretanje te zaustavljanje virtualiziranog mrežnog scenarija).

Pokretanje: `$ ./start.sh`

Zaustavljanje: `$ ./stop.sh`

Nakon toga pokrenuli smo shell station-1 naredbom  `$ docker exec -it station-1 bash` . Provjerili smo nalazi se station-2 na istoj mreži (`$ ping station-2`) te smo provjerili konfiguraciju mrežnog interface-a (`$ ifconfig -a`).

Zatim smo pokrenuli shell station-2 naredbom `$ docker exec -it station-2 bash` .  Prije toga split-ali smo terminal korištenjem shift alt +.

Kako bi povezali station-1 i station-2 koristili smo naredbe `$ netcat -l 8080` (u dijelu terminala gdje je otvoren station-2) te `$ netcat station-2 8080` (u dijelu terminala gdje je otvoren station-1). Nakon ovih naredbi omogućeno je slanje informacija između station-1 i station-2.

Kako bismo testirali napad, pokrenuli smo novi station (evil-station). Opet smo split-ali terminal korištenjem shift alt - (ovaj put smo se pozicionirali ispod station-2). Pokrenuli smo evil-station korištenjem naredbe `$ docker exec -it evil-station bash` .

Isprobavali smo:

- tcpdump (omogućava praćenje prometa) `tcpdump -qX host station-1 and not arp and not icmp`
- arpspoof (narušava integritet, lažno predstavljanje) `arpspoof -i eth0 -t station-1 station-2`

Na kraju testirali smo ovu naredbu: `evilstation echo 0 > /proc/sys/net/ipv4/ip_forward`. Korištenjem ove naredbe onemogućeno je razmjenjivanje poruka između station-1 te station-2.