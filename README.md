# Predviđanje broja golova

##	O projektu
Ideja projekta je da se osnovu prikupljenih podataka o pojedinačnim mečevima u okviru engleske Premijer lige u fudbalu predvidi koliko će
golova postići neki tim ne sledećem meču. Podaci su prikupljeni sa sajta za praćenje sportskih takmičenja [Rezultati](http://www.rezultati.com/nogomet/engleska/premier-league-2015-2016/rezultati/). 
Posmatrani su mečevi iz sezone 2015/16 i iz tih mečeva izvučene neke osnovne statistike koje su karatkeristične fudbalskom meču. 
Koliko neki statistički parametri utiču na broj golova tima na utakmici može se utvrditi preko linearne regresije i to je metod koji je
korišćen u ovom projektu. Ceo projekat je realizovan u programskom jeziku R. 

##	Linerna regresija
Linearna regresija predstavlja metodu kojom se jedna takozvana zavisna promenljiva može predvideti korišćenjem jedne ili više nezavisnih promenljivih. U ovom projektu je korišćeno više nezavisnih promenljivih pa u pitanju nije jednostruka linearna regresija, već višestruka. Za nezavisnu promenljivu je odabran broj golova, koje na osnovu ostalih promeniljih želimo da predvidimo. U nastavku rada biće prikazano koje su to promenljive koje su se pokazale kao dobar pokazatelj ukupnog broja golova jednog tima na utakmici. 
U modelu koji je rezultat linearne regresije pojavljuje se onoliko koeficijenata koliko ima i nezavisnih promenljivih i još jedan koeficijent, odnosno slobodan član. Vrednosti koeficijenata uz nezavisne promeniljive mogu biti pozitivne pa je onda veza direktno proporcionalna između te nezavisne promenljive i one koju želimo da predvidimo ili obrnuto proporcionalna kako je ispred nezavisne promeniljive negativan predznak. 
Osnovna ideja linearne regresije je da da model koji će na osnovu nekog novog seta podataka, nekih konkretnih vrednosti nezavisnih promenljivih dati projekciju, odnosno predvideti vrednost posmatrane zavisne promenljive. 

##	Podaci 
Podaci koji su korićšćeni su prikupljeni sa portala [Rezultati.com](http://www.rezultati.com/), jednog od najpopularnijih sajtova za 
praćenje rezultata sportskih dešavanja u realnom vremenu. Svi prikupljeni podaci su sačuvani u fajlu [Premier_train.csv](https://github.com/DanijelMisulic/Predvidjanje-broja-golova/blob/master/Premier_train.csv).
Prikupljeni su podaci svih mečeva u sezoni engleske Premijer lige 2015/16, odnosno prvih 37 kola. Ideja je da se na osnovu modela koji 
je istreniran sa podacima iz tih prvih 37 kola predvide brojevi golova poslednjeg 38. kola i na taj način sam model istestira. Podaci 
tog poslednjeg kola se nalaze u fajlu Premier_test.csv.  Sami podaci koji su prikupljeni predstavljaju standardne statističke parametre 
koji se prate na svakom fudbalskom meču poput: šuteva u okvir gola, procenat poseda lopte, broj žutih kartona, broj kornera i slično.
Primer ispod pokazuje kako izgleda jedan red u datom csv fajlu:

```
Tim,Golovi,PosedLopte,UkupnoSuteva,SuteviUokvir,SuteviVanOkvira,BlokiraniSutevi,SlobodniUdarci,Korneri,Ofsajdi,OdbraneGolmana,Prekrsaji,ZutiKartoni,CrveniKartoni,Kvota,Pobednik
Manchester United,1,67,15,2,6,7,11,6,1,2,10,1,0,2.1,1
```

##	Realizacija projekta i tumačenje rezultata
Projekat je realizovan u programskom jeziku R. Dati programski jezik je odabran zbog lakoće manipulacije sa CSV fajlovima i zbog 
jednostavnosti pravljenja modela linearne regresije. Sam model se može napraviti pozivom jedne funkcije što će u nastavku i biti 
prikazano. U početku učitavamo fajl Premier_train.csv u kome se nalaze podaci koji su prikupljeni. Zatim se poziva funkcija **lm***,
koja će napraviti traženi model linearne regresije. Sami određujemo šta će biti zavisna promenljiva a koje promenljive će predstavljati
nezavisne. Moguće je napraviti jako puno modela linearne regresije u zavisnosti od toga koje promenljive želimo da iskoristimo.
Početna ideja će biti da se prosledi većina promenljivih koje se nalaze u training dataset-u. Sam rezultat ovog koraka neće biti neki
savršen model, ali će se onda korak po korak dati model poboljšavati s obzirom na protumačene rezultate koje nam R izbaci. 

```R
Premier = read.csv("Premier_train.csv")
model1 = lm(Golovi ~ PosedLopte + SuteviUokvir + BlokiraniSutevi + UkupnoSuteva + SuteviVanOkvira + SlobodniUdarci + Korneri + Ofsajdi + Kvota +  Pobednik + CrveniKartoni, data = Premier)
summary(model1)
```

U inicijalnom rešenju dobijamo model1 pozivom funkcije lm,  sa leve strane znaka ~ se precizira zavisna promenljiiva, a to je u našem 
slučaju promenljiva koju želimo da predvidimo Golovi. Sa desne strane znaka ~ nalaze se nezavisne promenljive koje nadovezujemo pomoću 
znaka +. Dalje se mora precizirati koji dataset koristimo, pa ćemo proslediti upravo ovaj koji smo malopre učitali. Kako izgleda sam 
model linearne regresije prikazano je u nastavku pozivom metode:

```R
summary(model1)

Coefficients:
                 		Estimate 	Std. Error t 		value Pr(>|t|)    
(Intercept)      		 0.733277   	0.337704  		 2.171 0.030479 *  
PosedLopte     		 -0.006232  	 0.005044  		-1.235 0.217383    
SuteviUokvir   	  0.234419   	0.047317   		4.954 1.07e-06 ***
BlokiraniSutevi 	 -0.023064   	0.048700  		-0.474 0.636044    
UkupnoSuteva    	 0.010440   	0.043700  		 0.239 0.811309    
SuteviVanOkvira 	-0.013583  	 0.042052  		-0.323 0.746861    
SlobodniUdarci   	 0.007883  	 0.011375   		 0.693 0.488699    
Korneri        		 -0.064742   	0.016553  		-3.911 0.000108 ***
Ofsajdi        		  0.009508  	 0.025654 		  0.371 0.711120    
Kvota          		 -0.039100  	 0.021199 		 -1.844 0.065850 .  
Pobednik       		  1.015347  	 0.095957 		 10.581  < 2e-16 ***
CrveniKartoni  	 -0.034538  	 0.150987  		-0.229 0.819177    
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 0.8237 on 408 degrees of freedom
Multiple R-squared:  0.5443,    Adjusted R-squared:  0.532 
F-statistic: 44.31 on 11 and 408 DF,  p-value: < 2.2e-16

```

U koloni estimate nam se nalaze koeficijenti koji stoje uz date nezavisne promenljive modela, a mađu njima se nalazi i slobodni član. 
Od najvećeg značaja su nam dve vrednosti: Multiple R-squared i Adjusted R- squared. One iznose 0.5443 i 0.532 i na osnovu toga možemo 
zaključiti da je naš model za sada veoma pogodon za predvidđanje broja golova na utakmici. Najveća vrednost koju mogu uzeti je 1 i što 
je vrednost ove dve mere bliža jedinici to je naš model pogodniji za predviđanje. Osnovna mera koju ćemo posmatrati je Multiple R-squared,
koja predstavlja ssrednju kvadratnu grešku našeg modela, a daljim podešavanjem modela ćemo težiti da mera Adjusted R – squared ima 
blisku vrednost Multiple R-squared. Koeficijent Multiple R-squared dolazi do izražaja kada dodajemo promenljive u model ili ih 
izbacujemo iz njega. Ukoliko dodavanjem neke promenljive vrednost Multiple R-squared ostaje ista, a Adjusted R-squared se smanjuje onda
može doći do problema kada imamo previše promenljivih u modelu i tada nastaje overfitting samog modela nad podacima koji su mu 
prosleđeni za treniranje, a sam model se neće najbolje ponašati sa npodacima koje vidi po prvi put – onim koji služe za testiranje. 
Pošto ovaj model za sada deluje veoma složeno, težićemo da ga što više uprostimo, kako bi izdvojili samo najznačajnije varijable makar 
i po cenu da nam se vrednosti R-squared koeficijenta neznatno smanje. Nekada je dobro imati jednostavniji model, da bismo na lakši
način uočili koje su to varijable koje su stvarno od značaja. U skadu sa ovim neophodno je da protumačimo šta znači ako se pojave jedna,
dve, tri zvezdice u produžetku korišćenih varijabli ili ako se te zvezdice uopšte i ne pojavljuju u tumačenju modela. Kada se u
produžetku neke nezavisne promenljive nalaze 3 zvezdice(``***``), kao što je to slučaj sa ŠuteviUOkvir, Pobednik i Korneri, to znači da su
ove promenljive jako značajne za naš model i da je poželjno da ih zadržimo. Tri zvezdice predstavljaju značaj signifikantnosti 0, a kako
se njihov broj smanjuje opada i signifikantnost. Tako vidimo da postoji još samo jedna promenljive koja je od značaja za model, ali sa
znatno manjim učešćem od 0.1 signifikantnosti, a to je promenljiva Kvota. Signifikantnost pormenljivih se može menjati kroz razlićčite
iteracije modela, pa će se zbog toga težiti da se model pojednostavljuje izbacivanjem jedne po jedne promenljive, a ne odjednom svih.
Zatim iz iteracije u iteraciju tumačimo dalje rezultate, pa se za neke promenljive koje su bile beznačajne u početku može ispostaviti
da su jako značajne. To se dešava usled problema multikolinearnosti, kada postoji velika korelacija između neke dve promenljive. To se
može desiti i u našem modelu zato što su neke promenljive izvedene iz drugih. Ideja je da na kraju kao rezultat dobijemo model dovoljno
jasan i jednostavan, sa samo značajnim promenljivama i velikom vrednošću koeficijenata R-squared.
Od promenljivih koje se u ovoj prvoj iteraciji nisu pokazale kao značajne moramo izabrati jednu koju ćemo izbaciti iz našeg modela.
Biramo promenljivu koja ima najveću vrednost u koloni *Pr(>|t|)*, a to je promenljiva CrveniKartoni sa vrednošću od 0.819177 u datoj
koloni. Pozivamo istu funkciju samo bez promenljive CrveniKartoni i pravimo **model2**.

```R
model2 = lm(Golovi ~ PosedLopte + SuteviUokvir + BlokiraniSutevi + UkupnoSuteva + SuteviVanOkvira + SlobodniUdarci + Korneri + Ofsajdi + Kvota +  Pobednik, data = Premier)
```

U modelu 2 vrednost Multiple R-squared iznosi 0.5443, a Adjusted R-squared iznosi 0.5331. Vrednost Multiple R-squared u odnosu na 
model1 je ostala ista, a Adjusted R-squared se neznatno povećala što opravdava odstranjivanje promenljive CrveniKartoni. Dalje 
ponavljamo postupak, sledeća promenljiva koju ćemo da odstranimo je UkupnoSuteva. 

```R
model3 = lm(Golovi ~ PosedLopte + SuteviUokvir + BlokiraniSutevi + SuteviVanOkvira + SlobodniUdarci + Korneri + Ofsajdi + Kvota +  Pobednik, data = Premier)
```

Multiple R-squared = 0.5442, Adjusted R-squared = 0.5342. Mera Adjusted R – squared se opet povećala što predstavalja poboljšanje našeg modela. Dalje izbacujemo ŠuteveVanOkvira.

```R
model4 = lm(Golovi ~ PosedLopte + SuteviUokvir + BlokiraniSutevi + SlobodniUdarci + Korneri + Ofsajdi + Kvota +  Pobednik, data = Premier)
```

Multiple R-squared = 0.5441, Adjusted R-squared = 0.5353. Nastavljamo postupak, sada izbacujemo još jednu promenljivu koja se pokazala da nije značajna u našem modelu-Ofsajdi.

```R
model5 = lm(Golovi ~ PosedLopte + SuteviUokvir + BlokiraniSutevi + SlobodniUdarci + Korneri + Kvota +  Pobednik, data = Premier)
```

Multiple R-squared = 0.544, Adjusted R-squared 0.5362. Primećujemo da se naš model i dalje poboljšava pa nastavljamo sa iteracijama. Izbacujemo promenljivu SlobodniUdarci. 

```R
model6 = lm(Golovi ~ PosedLopte + SuteviUokvir + BlokiraniSutevi + Korneri + Kvota +  Pobednik, data = Premier)
```

Multiple R-squared = 0.5434, Adjusted R-squared = 0.5368. Može se zaključiti da se model neznatno poboljšao kroz nekoliko uzastopnih iteracija,ali i dosta uprostio što je svakako i bilo poželjno. Sada izbacujemo BlokiraneŠuteve. 

```R
model7 = lm(Golovi ~ PosedLopte + SuteviUokvir + Korneri + Kvota +  Pobednik, data = Premier)
```

Multiple R-squared = 0.5429, Adjusted R-squared = 0.5374. Sada izbacujemo promenljivu PosedLopte. 

```R
model8 = lm(Golovi ~ SuteviUokvir + Korneri + Kvota +  Pobednik, data = Premier)
```

Multiple R-squared = 0.5409, Adjusted R-squared = 0.5365. vrednosti oba ova koeficijenta su se smanjile u odnosu na prošlu iteraciju, ali smo izbacili promenljivu koja nije bila statistički značajna i na taj način ojačali model. Sada može da odaberemo da stanemo sa iteracijama ili da izbacimo i promenljivu Kvota. 

```R
model9 = lm(Golovi ~ SuteviUokvir + Korneri +  Pobednik, data = Premier)
```

```R
Residuals:
    Min      1Q  Median      3Q     Max 
-2.3581 -0.5566 -0.1177  0.4626  3.1630 

Coefficients:
             Estimate Std. Error t value Pr(>|t|)    
(Intercept)   0.34641    0.09962   3.477  0.00056 ***
SuteviUokvir  0.24544    0.01975  12.426  < 2e-16 ***
Korneri      -0.07017    0.01392  -5.041 6.92e-07 ***
Pobednik      1.06676    0.09071  11.761  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 0.8208 on 416 degrees of freedom
Multiple R-squared:  0.5386,    Adjusted R-squared:  0.5353 
F-statistic: 161.9 on 3 and 416 DF,  p-value: < 2.2e-16
```

Sada imamo model u koji smo dosta uprostili i u kome su sve tri preostale promenljive statistički značajne sa najvišim stepenom 
signifikantnosti pa stajemo sa iteracijama. Dobili smo model koji sa velikom preciznošću samo na osnovu šuteva u okvir gola, broja
kornera i krajnjeg pobenika meča može da predvidi broj golova jedne ekipe na utakmici.

##	Predlozi poboljšanja modela
Sami podaci na kojima se zasniva dobijen model dosta zavise od ofanzivne igre tima, a ne obraća se toliko pažnja na defanzivne sposobnosti istog tima. Ono što bi možda bilo još od većeg značaja su odbrambene karakteristike protivničkog tima. Na primer, nije isto da li će tim poput Mančester Junajteda imati 10 šuteva u okvir gola protiv nekog nižerazrednog tima ili protiv nekog pretendenta na titulu. Takodje, ukoliko je neki tim dobio crveni karton veća je verovatnoća da će primiti gol, pa bi za jedan tim bilo poželjno voditi računa i o nekim osnovnim karakteristikama njegovog protivnika, a ne samo o sopstvenim karakteristikama. Tako bi na primer mogao da se iskoristi dodatni atribut: da li je tim u nekom trenutku imao igrača više u odnostu na protivnika ili ne. To opet samo po sebi ne bi bilo toliko precizno s obzirom dužinu perioda sa igračem više i slično. 
Predstavljen model daje solidne rezultate koristeći samo neke od osnovnih statističkih parametara koji su dostupni svima. Činjenica je da se model može poboljšati dodavanjem još nekih statističkih paramatera. Postoje statistički podaci koji nisu dostupni svima i koji se plaćaju a mogu dati bolji uvid u neke aspekte igre fudbalskih timova, poput ukupne kilometraže koju igrači pretrče na meču ili ukupan broj startova koje načine.  

##	Literatura	
-	EDX kurs, Analytics Edge, link: https://courses.edx.org/courses/course-v1:MITx+15.071x_3+1T2016/courseware/f8d71d64418146f18a066d7f0379678c/6248c2ecbbcb40cfa613193e8f1873c1/ , datum pristupa: 12.07.2016
-	 Wikipedia, “Linear regression” link: https://en.wikipedia.org/wiki/Linear_regression, datum pristupa: 11.07.2016
