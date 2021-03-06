Toteutusdokumentti
====================
Yleisrakenne
------------
### Paketit ja luokat
Labyrintti
- Käynnistys: Käynnistää ohjelman

Labyrintti/osat
- Pohja: Kartta labyrintistä
- Ruutu: Kartta koostuu ruuduista

Labyrintti/sovellus
- Etsijä: Laskee reitin A*:lla
- Minimikeko: Oma toteutus minimikeosta

Labyrintti/gui
- Käyttöliittymä: Avaa sovelluksen ikkunaan
- Napin kuuntelija: Käynnistää reitin etsimisen
- Piirtoalusta: Piirtää kartan ja reitin

Saavutetut aika- ja tilavaativuudet
------------------------------------

## A*

talukko n riviä * m saraketta, m*n = V solmua, E kpl kaaria

	aStar()
		alustetaan minimikeko
		while ei olla päästy maaliin
			valitse keosta ruutu r, jolle lähtöön[r] + maaliin[r] on pienin
			aseta r käydyksi true
			käy läpi r:n naapurit
	
	alustus()
		for kaikille ruuduille r
			maaliin[r] = arvio r-->maali
			lähtöön[r] = ääretön
			reitti[r] = NIL
		lähtöön[lähtö] = 0
		lisää ruudut kekoon

	käyLäpiNaapurit(r)
		for kaikki r:n naapurit q
			// relax
			if(lähtöön[q] > lähtöön[r]+arvo[q])
				lähtöön[q] = lähtöön[r]+arvo[q]
				reitti[q] = r

###Aikavaativuus

aStarin aikavaativuus kaiken kaikkiaan on O((V+E) log V). Koska jokaisesta solmusta lähtee korkeintaan 4 kaarta, on kaarien lukumäärä E luokkaa O(4*V) = O(V), jolloin aikavaativuus voidaan kirjoittaa O((V+E) log V) = O((V + V) log V) = O(2V log V) = O(V log V).
		
	aStar()
		alustetaan minimikeko                   // O(V)
		while ei olla päästy maaliin            // ehdon tarkistaminen O(1)
                                               // while toistetaan O(V)
			r = poll(keko)	                  // O(log V)
			aseta r käydyksi                  // O(1)
			käy läpi r:n naapurit             // tutkitaan jokainen kaari, O(E log V)
	
	alustus()
		for kaikille ruuduille r		// O(V)
			maaliin[r] = arvio r-->m	// O(1)
			lähtöön[r] = ääretön		  // O(1)
			reitti[r] = NIL			   // O(1)
		lähtöön[lähtö] = 0			    // O(1)
		lisää ruudut kekoon			 // O(V)
	
	käyLäpiNaapurit(r)
		for kaikki r:n naapurit q		// O(4) = O(1)
			if(lähtöön[q] > lähtöön[r]+arvo[q]) // ehdon tarkistaminen O(1)
				lähtöön[q] = lähtöön[r]+arvo[q]	// O(1)
				reitti[q] = r			// O(1)
				päivitä keko	  		  // O(log V)

###Tilavaativuus

2-ulotteinen pohjataulukko, samoin kuin 1-ulotteinen minimikeko vie tilaa O(V), koska sinne tallennetaan kaikki solmut, siispä aStarin tilavaativuus on O(V).

##Minimikeko

Minimikeolla tärkeä metodi on Heapify, joka huolehtii siitä että kekoehto on voimassa koko ajan.

	Heapify(solmu)
		while vasen(solmu)<keonKoko
			v = vasen(solmu)
			o = oikea(solmu)
			if oikea < keonKoko //jos solmulla on oikea lapsi
				if ruudut[v]<ruudut[o] pienin = v
				else pienin = o
				if etäisyys(ruudut[solmu]) > etäisyys(ruudut[pienin])
					vaihda ruudut[solmu] ja ruudut[pieni]
					solmu = pienin
				else break
			else if v == keonKoko && etäisyys(ruudut[solmu]) > etäisyys(ruudut[v])
				vaihda ruudut[solmu] ja ruudut[v]
				break;
			else break

###Aikavaativuus

Minimikekoon alustetaan koko pohjakartta, siispä keon koko on aluksi (ja suurimmillaan) V, joka on kartan solmujen lukumäärä.

- Heapify O(log V)
- Build-heap O(V log V)
- Solmun päivittäminen O(log V)
- Poll O(log V)

Aikavaativuuden analysointi pseudokoodista:
	
	Heapify(solmu)
		while vasen(solmu)<keonKoko					// O(log V)
			v = vasen(solmu)						// O(1)
			o = oikea(solmu)						// O(1)
			if oikea < keonKoko						// O(1)
				if ruudut[v]<ruudut[o] pienin = v	// O(1)
				else pienin = o						// O(1)
				if etäisyys(ruudut[solmu]) > etäisyys(ruudut[pienin]) // O(1)
					vaihda ruudut[solmu] ja ruudut[pieni]	// O(1)	
					solmu = pienin					// O(1)
				else break							// O(1)
			else if v == keonKoko && etäisyys(ruudut[solmu]) > etäisyys(ruudut[v]) // O(1)
				vaihda ruudut[solmu] ja ruudut[v]	// O(1)
				break								// O(1)
			else break								// O(1)

	Build-heap()
		for i = keonKoko / 2 to 1		// O(V)
			heapify(i)					// O(log V)

	PäivitäRuutu(päivitettävä)
		solmu = indeksi(päivitettävä)						// O(1)
		while solmu > 1 && etäisyys(ruudut[vanhempi(solmu)]) > etäisyys(ruudut[solmu]) // O(log V)
			vaihda ruudut[solmu] ja ruudut[vanhempi(solmu)]	// O(1)
			solmu = vanhempi(solmu)							// O(1)

	Poll()
		pienin = ruudut[1]			// O(1)
		ruudut[1] = ruudut[keonKoko]// O(1)
		keonKoko--					// O(1)
		heapify(1)					// O(log V)
		return pienin				// O(1)
			
###Tilavaativuus

Minimikeon tilavaativuus on O(V), koska talukossa on kaikki kartan ruudut.

Puutteet ja parannusehdotukset
------------------------------
- Käyttäjälle suuren kartan syöttäminen on aika vaivalloista, tähän olisi voinut ehkä keksiä jonkun toisen ratkaisun.
- Taulukon koko on rajattu 30 x 30 ruutua, koska GUI:n aukeavassa ikkunassa ruudun pikseleiksi on määritelty 25, suurempi kartta ei mahtuisi tällöin näytölle. Vaihtoehtona tähän olisi voinut tehdä kiinteänkokoisen ikkunan, joka skaalaa ruutujen koon käyttäjän antamien leveyden ja korkeuden mukaan. Halusin kuitenkin, että ruudut ovat neliönmuotoisia, joten päädyin tähän tapaan.
- Kaynnistysluokan testaus on puutteellinen käyttäjän syötteiden osalta luokan toteutustavan takia, mutta kattavat käyttöohjeet korvaavat osittain tämän.
- Reitin etenemisen visualisoinnin olisi voinut ajastaa sen sijaan että ohjelma piirtää kerralla koko lopputuloksen.
- Ohjelma hyväksyy ruudun arvoksi nollan. Tämä aiheuttaa reitin laskuun virheitä, koska ohjelma ei ymmärrä, että nolla-reittiä pitkin pääsisi "kevyemmin", vaikka etäisyysarvio loppuun olisikin nolla-ruudussa suurempi kuin virheellisen reitin ruuduilla. Ongelma on ratkaistu niin, että vaikka käyttäjä syöttäisi nollan, ohjelma muuttaa sen ykköseksi.

Esim.

	//kartta	// virheellinen		// oikea		// kartan reitti käyttäen ykkösiä
	L0000			Lxxxx			Lxxxx			Lxxxx
	33330			3333x			3333x			3333x
	00000			0000x			xxxxx			1111x
	03333			0333x			x3333			1333x
	0000M			0000M			xxxxM			1111M

Lähteet
---------
http://www.cs.helsinki.fi/u/floreen/tira2014/tira.pdf

