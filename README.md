# Predviđanje broja golova

##	O projektu
Ideja projekta je da se osnovu prikupljenih podataka o pojedinačnim mečevima u okviru engleske Premijer lige u fudbalu predvidi koliko će golova postići neki tim ne sledećem meču. Podaci su prikupljeni sa sajta za praćenje sportskih takmičenja [Rezultati](http://www.rezultati.com/nogomet/engleska/premier-league-2015-2016/rezultati/). 
Posmatrani su mečevi iz sezone 2015/16 i iz tih mečeva izvučene neke osnovne statistike koje su karatkeristične fudbalskom meču. 
Koliko neki statistički parametri utiču na broj golova tima na utakmici može se utvrditi preko linearne regresije i to je metod koji je
korišćen u ovom projektu. Ceo projekat je realizovan u programskom jeziku R. 

##	Linerna regresija
Linearna regresija predstavlja metodu kojom se jedna takozvana zavisna promenljiva može predvideti korišćenjem jedne ili više nezavisnih promenljivih. U ovom projektu je korišćeno više nezavisnih promenljivih pa u pitanju nije jednostruka linearna regresija, već višestruka. Za nezavisnu promenljivu je odabran broj golova, koje je na osnovu ostalih promenljivih potrebno predvideti. U nastavku rada biće prikazano koje su to promenljive koje su se pokazale kao dobar pokazatelj ukupnog broja golova jednog tima na utakmici. 
U modelu koji je rezultat linearne regresije pojavljuje se onoliko koeficijenata koliko ima i nezavisnih promenljivih i još jedan koeficijent, odnosno slobodan član. Vrednosti koeficijenata uz nezavisne promenljive mogu biti pozitivne pa je onda veza direktno proporcionalna između te nezavisne promenljive i one koju model predviđa ili obrnuto proporcionalna ako je ispred nezavisne promenljive negativan predznak. 
Osnovna ideja linearne regresije je da pruži model koji će na osnovu nekog novog seta podataka, nekih konkretnih vrednosti nezavisnih promenljivih dati projekciju, odnosno predvideti vrednost posmatrane zavisne promenljive. 

##	Podaci 
Podaci koji su korišćeni su prikupljeni sa portala [Rezultati.com](http://www.rezultati.com/), jednog od najpopularnijih sajtova za 
praćenje rezultata sportskih dešavanja u realnom vremenu. Svi prikupljeni podaci su sačuvani u fajlu [Premier_train.csv](https://github.com/DanijelMisulic/Predvidjanje-broja-golova/blob/master/Premier_train.csv).
Prikupljeni su podaci svih mečeva u sezoni engleske Premijer lige 2015/16, odnosno prvih 37 kola. Ideja je da se na osnovu modela koji 
je istreniran sa podacima iz tih prvih 37 kola predvide brojevi golova poslednjeg 38. kola i na taj način sam model istestira. Podaci 
tog poslednjeg kola se nalaze u fajlu Premier_test.csv.  Sami podaci koji su prikupljeni predstavljaju standardne statističke parametre 
koji se prate na svakom fudbalskom meču poput: šuteva u okvir gola, procenat poseda lopte, broj žutih kartona, broj kornera i ostalo.
Primer podataka korišćenih u analizi dat je u Listingu 1.

```
Tim,Golovi,PosedLopte,UkupnoSuteva,SuteviUokvir,SuteviVanOkvira,BlokiraniSutevi,SlobodniUdarci,Korneri,Ofsajdi,OdbraneGolmana,Prekrsaji,ZutiKartoni,CrveniKartoni,Kvota,Pobednik
Manchester United,1,67,15,2,6,7,11,6,1,2,10,1,0,2.1,1
```
*Listing 1 - Primer podataka korišćenih za analizu*

##	Realizacija projekta i tumačenje rezultata
Projekat je realizovan u programskom jeziku R. Dati programski jezik je odabran zbog lakoće manipulacije CSV fajlovima i zbog 
jednostavnosti pravljenja modela linearne regresije. Prvo je neophodno učitati fajl Premier_train.csv u kome se nalaze podaci koji su prikupljeni. Zatim se poziva funkcija **lm***, koja će napraviti traženi model linearne regresije. Početna ideja je da se većina promenljivih koje se nalaze u training dataset-u postave kao nezavisne, a promeljiva Golovi da se postavi kao zavisna. Sam rezultat ovog koraka neće biti savršen model, ali će biti moguće u više iteracija model poboljšavati s obzirom na protumačene rezultate. 

```R
Premier = read.csv("Premier_train.csv")
model1 = lm(Golovi ~ PosedLopte + SuteviUokvir + BlokiraniSutevi + UkupnoSuteva + SuteviVanOkvira + SlobodniUdarci + Korneri + Ofsajdi + Kvota +  Pobednik + CrveniKartoni, data = Premier)
summary(model1)
```
*Listing 2 - Regresioni model1*

U inicijalnom rešenju dobijen je model1 pozivom funkcije lm. Sa leve strane znaka ~ definiše se zavisna promenljiva, a to je u ovom 
slučaju promenljiva *Golovi*. Sa desne strane znaka ~ nalaze se nezavisne promenljive koje se nadovezuju pomoću znaka +. Dalje se mora precizirati koji dataset se koristi, pa će biti prosleđen onaj koji je u ove svrhe prikupljen i učitan u prethodnom koraku. Kako izgleda sam model linearne regresije prikazano je u nastavku pozivom metode:

```R
summary(model1)

Residuals:
    Min      1Q  Median      3Q     Max 
-2.3524 -0.5397 -0.1026  0.4679  3.1676 

Coefficients:
                 Estimate Std. Error t value Pr(>|t|)    
(Intercept)      0.733277   0.337704   2.171 0.030479 *  
PosedLopte      -0.006232   0.005044  -1.235 0.217383    
SuteviUokvir     0.234419   0.047317   4.954 1.07e-06 ***
BlokiraniSutevi -0.023064   0.048700  -0.474 0.636044    
UkupnoSuteva     0.010440   0.043700   0.239 0.811309    
SuteviVanOkvira -0.013583   0.042052  -0.323 0.746861    
SlobodniUdarci   0.007883   0.011375   0.693 0.488699    
Korneri         -0.064742   0.016553  -3.911 0.000108 ***
Ofsajdi          0.009508   0.025654   0.371 0.711120    
Kvota           -0.039100   0.021199  -1.844 0.065850 .  
Pobednik         1.015347   0.095957  10.581  < 2e-16 ***
CrveniKartoni   -0.034538   0.150987  -0.229 0.819177    
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 0.8237 on 408 degrees of freedom
Multiple R-squared:  0.5443,    Adjusted R-squared:  0.532 
F-statistic: 44.31 on 11 and 408 DF,  p-value: < 2.2e-16


```
*Listing 3 - Statistički parametri regresionog modela1*

U koloni *estimate* nalaze se koeficijenti koji stoje uz date nezavisne promenljive modela, a mađu njima se nalazi i slobodni član. 
Od najvećeg značaja su dve vrednosti: Multiple R-squared i Adjusted R- squared. One iznose 0.5443 i 0.532 i na osnovu toga se može 
zaključiti da je model za sada veoma pogodan za predvidđanje broja golova na utakmici. Najveća vrednost koju ova dva koeficijenta mogu uzeti je 1 i što 
je vrednost ove dve mere bliža jedinici to je model pogodniji za predviđanje. Osnovna mera koja se posmatra je Multiple R-squared, a  predstavlja srednju kvadratnu grešku modela. Daljim podešavanjem modela se teži da mera Adjusted R – squared ima blisku vrednost Multiple R-squared. Koeficijent Multiple R-squared dolazi do izražaja kada se dodaju promenljive u model ili se izbacuju iz njega. Ukoliko dodavanjem neke promenljive vrednost Multiple R-squared ostane ista, a Adjusted R-squared se smanjuje onda dolazi do problema kada postoji previše promenljivih u modelu. Tada nastaje overfitting samog modela nad podacima koji su mu 
prosleđeni za treniranje i model se neće najbolje ponašati u radu sa podacima koji mu budu prosleđeni po prvi put – koji služe za testiranje. 
Pošto model za sada deluje veoma složeno, nastojaće se da bude što više uprošćen, kako bi ostale izdvojene samo najznačajnije varijable makar 
i po cenu da se vrednosti R-squared koeficijenta neznatno smanje. Nekada je dobro imati jednostavniji model, jer se tada na lakši
način mogu uočiti varijable koje su stvarno od značaja za posmatrani problem.  

Dalje je neophodno protumačiti ako se pojave jedna, dve, tri zvezdice u produžetku korišćenih varijabli ili ako se te zvezdice uopšte i ne pojavljuju u tumačenju modela. Kada se u produžetku neke nezavisne promenljive nalaze 3 zvezdice(``***``), kao što je to slučaj sa *ŠuteviUOkvir*, *Pobednik* i *Korneri*, to znači da su ove promenljive jako značajne za naš model i da je poželjno da ih zadržimo. Tri zvezdice predstavljaju promenljive sa statističkim nivoom značajnosti 0, odnosno najvišim nivoom, a kako se broj zvezdica smanjuje i sam nivo značajnosti opada. Može se primetiti da trenutno postoji još samo jedna promenljive koja je od značaja za model, ali sa znatno manjim nivovom značajnosti od 0.1, to je promenljiva *Kvota*. Vrednost nivoa značajnosti promenljivih može varirati kroz iteracije modela, pa se zato teži da se model pojednostavljuje izbacivanjem jedne po jedne promenljive, a ne odjednom svih.

Iz iteracije u iteraciju potrebno je protumačiti rezultate modela, pa se može desiti da neke promenljive koje u početku nisu bile statistički značajne postanu jako značajne. To se dešava usled problema multikolinearnosti, kada postoji velika korelacija između neke dve promenljive. Ova pojava se može javiti i u predloženom modelu zato što su neke promenljive izvedene iz drugih. Ideja je da na kraju kao rezultat ostane model dovoljno
jasan i jednostavan, sa statistički veoma značajnim promenljivama i relativno velikom vrednošću oba koeficijenata R-squared.

Od promenljivih koje se u ovoj prvoj iteraciji nisu pokazale statistički značajne biće izabrana jedna koja će biti izbaćena iz modela.
Bira se promenljiva koja ima najveću vrednost u koloni *Pr(>|t|)*, a to je promenljiva CrveniKartoni sa vrednošću od 0.819177 u datoj
koloni. Zatim se poziva ista funkciju kao u prethodnoj iteraciji samo bez promenljive *CrveniKartoni* i formira se **model2**.

```R
model2 = lm(Golovi ~ PosedLopte + SuteviUokvir + BlokiraniSutevi + UkupnoSuteva + SuteviVanOkvira + SlobodniUdarci + Korneri + Ofsajdi + Kvota +  Pobednik, data = Premier)
```
*Listing 4 - Regresioni model2*

U modelu 2 vrednost Multiple R-squared iznosi 0.5443, a Adjusted R-squared iznosi 0.5331. Vrednost Multiple R-squared u odnosu na 
model1 je ostala ista, a Adjusted R-squared se neznatno povećala što opravdava odstranjivanje promenljive CrveniKartoni. 

```R
summary(model2)

Residuals:
    Min      1Q  Median      3Q     Max 
-2.3569 -0.5347 -0.1167  0.4615  3.1722 

Coefficients:
                 Estimate Std. Error t value Pr(>|t|)    
(Intercept)      0.715062   0.327801   2.181 0.029723 *  
PosedLopte      -0.006093   0.005002  -1.218 0.223863    
SuteviUokvir     0.234396   0.047262   4.960 1.04e-06 ***
BlokiraniSutevi -0.023103   0.048644  -0.475 0.635074    
UkupnoSuteva     0.010488   0.043649   0.240 0.810234    
SuteviVanOkvira -0.013129   0.041956  -0.313 0.754493    
SlobodniUdarci   0.007941   0.011359   0.699 0.484919    
Korneri         -0.064745   0.016534  -3.916 0.000106 ***
Ofsajdi          0.009972   0.025544   0.390 0.696449    
Kvota           -0.038302   0.020886  -1.834 0.067402 .  
Pobednik         1.019246   0.094322  10.806  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 0.8228 on 409 degrees of freedom
Multiple R-squared:  0.5443,    Adjusted R-squared:  0.5331 
F-statistic: 48.85 on 10 and 409 DF,  p-value: < 2.2e-16

```
*Listing 5 - Statistički parametri regresionog modela2*

Dalje se postupak ponavlja, sledeća promenljiva koju će biti odstanjena je *UkupnoSuteva*. 

```R
model3 = lm(Golovi ~ PosedLopte + SuteviUokvir + BlokiraniSutevi + SuteviVanOkvira + SlobodniUdarci + Korneri + Ofsajdi + Kvota +  Pobednik, data = Premier)
```
*Listing 6 - Regresioni model3*

Multiple R-squared = 0.5442, Adjusted R-squared = 0.5342. Mera Adjusted R – squared se opet povećala što predstavalja poboljšanje regresionog modela. 

```R
summary(model3)

Residuals:
    Min      1Q  Median      3Q     Max 
-2.3462 -0.5354 -0.1185  0.4640  3.1702 

Coefficients:
                 Estimate Std. Error t value Pr(>|t|)    
(Intercept)      0.711408   0.327072   2.175   0.0302 *  
PosedLopte      -0.005930   0.004950  -1.198   0.2316    
SuteviUokvir     0.244648   0.020303  12.050  < 2e-16 ***
BlokiraniSutevi -0.012381   0.019340  -0.640   0.5224    
SuteviVanOkvira -0.003788   0.015760  -0.240   0.8102    
SlobodniUdarci   0.007920   0.011346   0.698   0.4855    
Korneri         -0.064927   0.016498  -3.936 9.75e-05 ***
Ofsajdi          0.010410   0.025450   0.409   0.6827    
Kvota           -0.038045   0.020835  -1.826   0.0686 .  
Pobednik         1.020691   0.094022  10.856  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 0.8218 on 410 degrees of freedom
Multiple R-squared:  0.5442,    Adjusted R-squared:  0.5342 
F-statistic: 54.39 on 9 and 410 DF,  p-value: < 2.2e-16

```
*Listing 7 - Statistički parametri regresionog modela3*

Sledeća promenljiva koja će biti izbačena iz modela je *ŠuteviVanOkvira*.

```R
model4 = lm(Golovi ~ PosedLopte + SuteviUokvir + BlokiraniSutevi + SlobodniUdarci + Korneri + Ofsajdi + Kvota +  Pobednik, data = Premier)
```
*Listing 8 - Regresioni model4*

Multiple R-squared = 0.5441, Adjusted R-squared = 0.5353. 

```R
summary(model4)
Residuals:
    Min      1Q  Median      3Q     Max 
-2.3733 -0.5314 -0.1135  0.4618  3.1804 

Coefficients:
                 Estimate Std. Error t value Pr(>|t|)    
(Intercept)      0.701734   0.324214   2.164   0.0310 *  
PosedLopte      -0.006017   0.004931  -1.220   0.2231    
SuteviUokvir     0.244333   0.020238  12.073  < 2e-16 ***
BlokiraniSutevi -0.013289   0.018946  -0.701   0.4835    
SlobodniUdarci   0.008112   0.011305   0.718   0.4734    
Korneri         -0.065608   0.016234  -4.041 6.34e-05 ***
Ofsajdi          0.010421   0.025420   0.410   0.6821    
Kvota           -0.037948   0.020807  -1.824   0.0689 .  
Pobednik         1.020616   0.093913  10.868  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 0.8209 on 411 degrees of freedom
Multiple R-squared:  0.5441,    Adjusted R-squared:  0.5353 
F-statistic: 61.32 on 8 and 411 DF,  p-value: < 2.2e-16


```
*Listing 9 - Statistički parametri regresionog modela4*

Postupak se ponavlja, sada će biti odstranjena još jedna promenljiva za koju se pokazalo da nije statistički značajna u modelu: *Ofsajdi*.

```R
model5 = lm(Golovi ~ PosedLopte + SuteviUokvir + BlokiraniSutevi + SlobodniUdarci + Korneri + Kvota +  Pobednik, data = Premier)
```
*Listing 10 - Regresioni model5*

Multiple R-squared = 0.544, Adjusted R-squared 0.5362.  

```R
summary(model5)

Residuals:
    Min      1Q  Median      3Q     Max 
-2.3651 -0.5353 -0.1132  0.4609  3.1902 

Coefficients:
                 Estimate Std. Error t value Pr(>|t|)    
(Intercept)      0.729772   0.316598   2.305   0.0217 *  
PosedLopte      -0.006100   0.004922  -1.239   0.2160    
SuteviUokvir     0.244570   0.020209  12.102  < 2e-16 ***
BlokiraniSutevi -0.013125   0.018923  -0.694   0.4883    
SlobodniUdarci   0.007791   0.011266   0.692   0.4896    
Korneri         -0.065560   0.016217  -4.043 6.31e-05 ***
Kvota           -0.038441   0.020751  -1.853   0.0647 .  
Pobednik         1.021901   0.093766  10.898  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 0.8201 on 412 degrees of freedom
Multiple R-squared:  0.544,     Adjusted R-squared:  0.5362 
F-statistic:  70.2 on 7 and 412 DF,  p-value: < 2.2e-16

```
*Listing 11 - Statistički parametri regresionog modela5*

Može se primetiti da se model i dalje poboljšava pa se nastavlja sa iteracijama. Sledeća se izbacuje promenljiva *SlobodniUdarci*.

```R
model6 = lm(Golovi ~ PosedLopte + SuteviUokvir + BlokiraniSutevi + Korneri + Kvota +  Pobednik, data = Premier)
```
*Listing 12 - Regresioni model6*

Multiple R-squared = 0.5434, Adjusted R-squared = 0.5368. Može se zaključiti da se model neznatno poboljšao kroz nekoliko uzastopnih iteracija, ali i dosta uprostio što je svakako poželjno. 

```R
summary(model6)

Residuals:
    Min      1Q  Median      3Q     Max 
-2.3303 -0.5452 -0.1030  0.4733  3.1746 

Coefficients:
                 Estimate Std. Error t value Pr(>|t|)    
(Intercept)      0.822941   0.286321   2.874  0.00426 ** 
PosedLopte      -0.005867   0.004907  -1.196  0.23256    
SuteviUokvir     0.244366   0.020194  12.101  < 2e-16 ***
BlokiraniSutevi -0.013024   0.018911  -0.689  0.49141    
Korneri         -0.065911   0.016199  -4.069 5.66e-05 ***
Kvota           -0.039411   0.020691  -1.905  0.05750 .  
Pobednik         1.019287   0.093631  10.886  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 0.8195 on 413 degrees of freedom
Multiple R-squared:  0.5434,    Adjusted R-squared:  0.5368 
F-statistic: 81.93 on 6 and 413 DF,  p-value: < 2.2e-16

```
*Listing 13 - Statistički parametri regresionog modela6*

U sledećoj iteraciji će iz modela biti izostavljena promenljiva *BlokiraniŠutevi*. 

```R
model7 = lm(Golovi ~ PosedLopte + SuteviUokvir + Korneri + Kvota +  Pobednik, data = Premier)
```
*Listing 14 - Regresioni model7*

Multiple R-squared = 0.5429, Adjusted R-squared = 0.5374. 

```R
summary(model7)

Residuals:
    Min      1Q  Median      3Q     Max 
-2.3486 -0.5514 -0.1102  0.4581  3.1274 

Coefficients:
              Estimate Std. Error t value Pr(>|t|)    
(Intercept)   0.828374   0.286031   2.896  0.00398 ** 
PosedLopte   -0.006434   0.004835  -1.331  0.18403    
SuteviUokvir  0.243746   0.020161  12.090  < 2e-16 ***
Korneri      -0.070095   0.015007  -4.671 4.06e-06 ***
Kvota        -0.039188   0.020675  -1.895  0.05873 .  
Pobednik      1.023903   0.093331  10.971  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 0.819 on 414 degrees of freedom
Multiple R-squared:  0.5429,    Adjusted R-squared:  0.5374 
F-statistic: 98.34 on 5 and 414 DF,  p-value: < 2.2e-16

```
*Listing 15 - Statistički parametri regresionog modela7*

Sledeća promenljiva koja se neće naći u krajnjem modelu je *PosedLopte*.

```R
model8 = lm(Golovi ~ SuteviUokvir + Korneri + Kvota +  Pobednik, data = Premier)
```

Multiple R-squared = 0.5409, Adjusted R-squared = 0.5365. vrednosti oba koeficijenta su se smanjile u odnosu na prethodnu iteraciju, ali je izbačena promenljiva koja nije bila statistički značajna i na taj način je dobijen jednostavniji model. Kada je model pokazao zadovoljavajuće rezultat može se odabrati da se stane sa iteracijama ili da se izbaci još jedna promenljiva *Kvota*. 

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

Na kraju je dobijen model u kome su sve preostale promenljive značajne sa najvišim nivoom značajnosti pa se staje sa iteracijama. Dati model linearne regresije sa velikom preciznošću samo na osnovu šuteva u okvir gola, broja
kornera i krajnjeg pobednika meča može predvideti broj golova jedne ekipe na utakmici.

##	Predlozi poboljšanja modela
Podaci na kojima se zasniva dobijen model dosta zavise od ofanzivne igre fudbalskog tima, a ne obraća se toliko pažnja na defanzivne sposobnosti. Možda bi od bilo od još većeg značaja bile odbrambene karakteristike protivničkog tima. Na primer, nije isto da li će tim poput Mančester Junajteda imati 10 šuteva u okvir gola protiv nekog nižerazrednog tima ili protiv nekog pretendenta na titulu. Takodje, ukoliko je neki tim dobio crveni karton veća je verovatnoća da će primiti gol, pa je za jedan tim poželjno voditi računa i o nekim osnovnim karakteristikama protivnika, a ne samo o sopstvenim karakteristikama. Tako bi na primer mogao da se iskoristi dodatni atribut: da li je tim u nekom trenutku imao igrača više u odnosu na protivnika ili ne. To opet samo po sebi ne bi bilo toliko precizno s obzirom na dužinu perioda sa igračem više i slično.

Predstavljen model daje solidne rezultate koristeći samo neke od osnovnih statističkih parametara koji su dostupni svima. Činjenica je da se model može poboljšati dodavanjem još nekih statističkih paramatera. Postoje statistički podaci koji nisu dostupni svima i koji se plaćaju, a mogu dati bolji uvid u neke aspekte igre fudbalskih timova, poput ukupne kilometraže koju igrači pretrče na meču ili ukupan broj startova koje načine.  

##	Literatura	
-	EDX kurs, Analytics Edge, link: https://courses.edx.org/courses/course-v1:MITx+15.071x_3+1T2016/courseware/f8d71d64418146f18a066d7f0379678c/6248c2ecbbcb40cfa613193e8f1873c1/ , datum pristupa: 12.07.2016
-	 Wikipedia, “Linear regression” link: https://en.wikipedia.org/wiki/Linear_regression, datum pristupa: 11.07.2016
