# Prediction of the number of goals

##	about the project
The idea of the project is to predict how many goals will be scored by a team in the next match based on the data collected on individual English Premier League matches. Data were collected from the Sports Tracking Website Resultati (http://www.rezultati.com/nogomet/engleska/premier-league-2015-2016/rezultati/). Matches from the 2015/16 season have been observed and some basic statistics extracted from those matches that are characteristic to a football match. How much some statistical parameters affect the number of goals a team will have in a match can be determined by linear regression and this is a method that is used in this project.
The whole project was implemented in the programming language R.

##	Linear regression
Linear regression is a method by which a so-called dependent variable can be predicted using one or more independent variables.Multiple independent variables were used in this project, so in this project it is not used single linear regression, but multiple. The number of goals was selected for the independent variable which should be predicted based on other variables. The following project work will outline what variables have proven to be a good indicator of the total number of goals a team has in a game. In the model which is a result of linear regression, there are as many coefficients as there are independent variables and another additional coefficient, a free member. The values of the coefficients near the independent variables can be positive, so the relation is directly proportional between that independent variable and the one predicted by the model, or inversely proportional if there is a negative sign in front of the independent variable.

The basic idea of linear regression is to provide a model that, based on some new data set, some specific values of independent variables, will give a prediction of the value of the observed dependent variable.

##	Data 
The data used was collected from Rezultati.com, one of the most popular real-time sports performance monitoring websites in Serbia. All data collected is stored in the file [Premier_train.csv](https://github.com/DanijelMisulic/Predvidjanje-broja-golova/blob/master/Premier_train.csv). Data were collected from all matches in the English Premier League 2015/16 season, the first 37 rounds. The idea is that, based on the model trained with the data from those first 37 rounds, the goals of the last 38th round will be predicted, and thus the model will tested in that way. The data of that last competition round is in the Premier_test.csv file. The collected data represent standard statistical parameters that are monitored on every football match such as: number of attempts on the target, percentage of possession, number of yellow cards, number of corners and more. An example of the data used in the analysis is given in Listing 1.

```
Team, Goals, Possession, TotalNumberOfAttempts, AttemptsOnTheTarget, AttemptsOfTheTarget, BlockedShots, FreeKicks, Corners, Offsides, GoalkeeperSaves, Fouls, YellowCards, RedCards, Odds, Winner
Manchester United,1,67,15,2,6,7,11,6,1,2,10,1,0,2.1,1
```
*Listing 1 - An example of the data used for analysis*

##	Project realization and interpretation of the results
The project was implemented in programming language R.The given programming language was chosen for ease of manipulation of CSV files and for
ease of making linear regression models. First, it is necessary to upload the Premier_train.csv file containing the information that has been collected. The **lm*** function is then called, which will make the required linear regression model. The initial idea is to set most of the variables contained in the training dataset as independent, and Goals as dependent. The result of this step alone will not be a perfect model, but it will be possible in several iterations to refine the model given the interpreted results.
```R
Premier = read.csv("Premier_train.csv")
model1 = lm(Goals ~ Possession + AttemptsOnTheTarget + BlockedShots + TotalNumberOfAttempts + AttemptsOfTheTarget + FreeKicks + Corners + Offsides + Odds +  Winner + RedCards, data = Premier)
summary(model1)
```
*Listing 2 - Regression model1*

In the initial solution model1 was obtained by calling the function lm. To the left of the ~ character is defined the dependent variable, which is in this case variable  *Goals*. To the right of the ~ character are independent variables that are combined with the + sign. Further, it is necessary to specify which dataset is being used, and the one that has been collected and loaded for the previous step will be passed as a parameter. The linear regression model itself is shown below by calling the method:

```R
summary(model1)

Residuals:
    Min      1Q  Median      3Q     Max 
-2.3524 -0.5397 -0.1026  0.4679  3.1676 

Coefficients:
                        Estimate Std. Error t value Pr(>|t|)    
(Intercept)             0.733277   0.337704   2.171 0.030479 *  
Possession             -0.006232   0.005044  -1.235 0.217383    
AttemptsOnTheTarget     0.234419   0.047317   4.954 1.07e-06 ***
BlockedShots -0.023064  0.048700  -0.474 0.636044    
TotalNumberOfAttempts   0.010440   0.043700   0.239 0.811309    
AttemptsOfTheTarget    -0.013583   0.042052  -0.323 0.746861    
FreeKicks               0.007883   0.011375   0.693 0.488699    
Corners                -0.064742   0.016553  -3.911 0.000108 ***
Offsides                0.009508   0.025654   0.371 0.711120    
Odds                   -0.039100   0.021199  -1.844 0.065850 .  
Winner                  1.015347   0.095957  10.581  < 2e-16 ***
RedCards               -0.034538   0.150987  -0.229 0.819177    
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 0.8237 on 408 degrees of freedom
Multiple R-squared:  0.5443,    Adjusted R-squared:  0.532 
F-statistic: 44.31 on 11 and 408 DF,  p-value: < 2.2e-16


```
*Listing 3 - Statistički parametri regresionog modela1*

U koloni *estimate* nalaze se koeficijenti koji stoje uz date nezavisne promenljive modela, a među njima se nalazi i slobodni član. 
Od najvećeg značaja su dve vrednosti: Multiple R-squared i Adjusted R- squared. One iznose 0.5443 i 0.532 i na osnovu toga se može 
zaključiti da je model za sada veoma pogodan za predvidđanje broja golova na utakmici. Najveća vrednost koju ova dva koeficijenta mogu uzeti je 1 i što je vrednost ove dve mere bliža jedinici to je model pogodniji za predviđanje. Osnovna mera koja se posmatra je Multiple R-squared koja predstavlja srednju kvadratnu grešku modela. Daljim podešavanjem modela se teži da mera Adjusted R–squared ima blisku vrednost Multiple R-squared. Koeficijent Multiple R-squared dolazi do izražaja kada se dodaju promenljive u model ili se izbacuju iz njega. Ukoliko dodavanjem neke promenljive vrednost Multiple R-squared ostane ista, a Adjusted R-squared se smanjuje onda dolazi do problema kada postoji previše promenljivih u modelu. Tada nastaje overfitting samog modela nad podacima koji su mu prosleđeni za treniranje i model se neće najbolje ponašati u radu sa podacima koji mu budu prosleđeni po prvi put – koji služe za testiranje.

Pošto model za sada deluje veoma složeno, nastojaće se da bude što više uprošćen, kako bi ostale izdvojene samo najznačajnije varijable makar i po cenu da se vrednosti R-squared koeficijenta neznatno smanje. Nekada je dobro imati jednostavniji model, jer se tada na lakši
način mogu uočiti varijable koje su stvarno od značaja za posmatrani problem.  

Dalje je neophodno protumačiti ako se pojave jedna, dve, tri zvezdice u produžetku korišćenih varijabli ili ako se te zvezdice uopšte i ne pojavljuju u tumačenju modela. Kada se u produžetku neke nezavisne promenljive nalaze 3 zvezdice(``***``), kao što je to slučaj sa *ŠuteviUOkvir*, *Pobednik* i *Korneri*, to znači da su ove promenljive jako značajne za model i da je poželjno zadržati ih. Tri zvezdice predstavljaju promenljive sa statističkim nivoom značajnosti 0, odnosno najvišim nivoom, a kako se broj zvezdica smanjuje i sam nivo značajnosti opada. Može se primetiti da trenutno postoji još samo jedna promenljive koja je od značaja za model, ali sa znatno manjim nivovom značajnosti od 0.1, to je promenljiva *Kvota*. Vrednost nivoa značajnosti promenljivih može varirati kroz iteracije modela, pa se zato teži da se model pojednostavljuje izbacivanjem jedne po jedne promenljive, a ne odjednom svih.

U svakoj iteraciji potrebno je protumačiti rezultate modela, pa se može desiti da neke promenljive koje u početku nisu bile statistički značajne postanu jako značajne. To se dešava usled problema multikolinearnosti, kada postoji velika korelacija između neke dve promenljive. Ova pojava se može javiti i u predloženom modelu zato što su neke promenljive izvedene iz drugih. Ideja je da na kraju kao rezultat ostane model dovoljno jasan i jednostavan, sa statistički veoma značajnim promenljivama i relativno velikom vrednošću oba koeficijenata R-squared.

Od promenljivih koje se u ovoj prvoj iteraciji nisu pokazale statistički značajne biće izabrana jedna koja će biti izbaćena iz modela.
Bira se promenljiva koja ima najveću vrednost u koloni *Pr(>|t|)*, a to je promenljiva *CrveniKartoni* sa vrednošću od 0.819177 u datoj
koloni. Zatim se poziva ista funkciju kao u prethodnoj iteraciji samo bez promenljive *CrveniKartoni* i formira se **model2**.

```R
model2 = lm(Goals ~ Possession + AttemptsOnTheTarget + BlockedShots + TotalNumberOfAttempts + AttemptsOfTheTarget + FreeKicks + Corners + Offsides + Odds +  Winner, data = Premier)
```
*Listing 4 - Regression model2*

In Model 2, the Multiple R-squared value is 0.5443 and the Adjusted R-squared value is 0.5331. Multiple R-squared value compared to
model1 remained the same, and Adjusted R-squared increased slightly, justifying the removal of the *RedCards*. variable.

```R
summary(model2)

Residuals:
    Min      1Q  Median      3Q     Max 
-2.3569 -0.5347 -0.1167  0.4615  3.1722 

Coefficients:
                        Estimate Std. Error t value Pr(>|t|)    
(Intercept)             0.715062   0.327801   2.181 0.029723 *  
Possession             -0.006093   0.005002  -1.218 0.223863    
AttemptsOnTheTarget     0.234396   0.047262   4.960 1.04e-06 ***
BlockedShots           -0.023103   0.048644  -0.475 0.635074    
TotalNumberOfAttempts   0.010488   0.043649   0.240 0.810234    
AttemptsOfTheTarget    -0.013129   0.041956  -0.313 0.754493    
FreeKicks               0.007941   0.011359   0.699 0.484919    
Corners                -0.064745   0.016534  -3.916 0.000106 ***
Offsides                0.009972   0.025544   0.390 0.696449    
Odds                   -0.038302   0.020886  -1.834 0.067402 .  
Winner                  1.019246   0.094322  10.806  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 0.8228 on 409 degrees of freedom
Multiple R-squared:  0.5443,    Adjusted R-squared:  0.5331 
F-statistic: 48.85 on 10 and 409 DF,  p-value: < 2.2e-16

```
*Listing 5 - Statistical parametres of regression model2*

The procedure is repeated further, the next variable to be removed is *TotalNumberOfAttempts*.

```R
model3 = lm(Goals ~ Possession + AttemptsOnTheTarget + BlockedShots + AttemptsOfTheTarget + FreeKicks + Corners + Offsides + Odds +  Winner, data = Premier)
```
*Listing 6 - Regression model3*

Multiple R-squared = 0.5442, Adjusted R-squared = 0.5342. The Adjusted R - squared measure increased again, representing an improvement in the regression model.

```R
summary(model3)

Residuals:
    Min      1Q  Median      3Q     Max 
-2.3462 -0.5354 -0.1185  0.4640  3.1702 

Coefficients:
                       Estimate Std. Error t value Pr(>|t|)    
(Intercept)            0.711408   0.327072   2.175   0.0302 *  
Possession            -0.005930   0.004950  -1.198   0.2316    
AttemptsOnTheTarget    0.244648   0.020303  12.050  < 2e-16 ***
BlockedShots          -0.012381   0.019340  -0.640   0.5224    
AttemptsOfTheTarget   -0.003788   0.015760  -0.240   0.8102    
FreeKicks              0.007920   0.011346   0.698   0.4855    
Corners               -0.064927   0.016498  -3.936 9.75e-05 ***
Offsides               0.010410   0.025450   0.409   0.6827    
Odds                  -0.038045   0.020835  -1.826   0.0686 .  
Winner                 1.020691   0.094022  10.856  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 0.8218 on 410 degrees of freedom
Multiple R-squared:  0.5442,    Adjusted R-squared:  0.5342 
F-statistic: 54.39 on 9 and 410 DF,  p-value: < 2.2e-16

```
*Listing 7 - Statistical parametres of regression model3*

The next variable to be thrown out of the model is  *AttemptsOfTheTarget*.

```R
model4 = lm(Goals ~ Possession + AttemptsOnTheTarget + BlockedShots + FreeKicks + Corners + Offsides + Odds +  Winner, data = Premier)
```
*Listing 8 - Regression model4*

Multiple R-squared = 0.5441, Adjusted R-squared = 0.5353. 

```R
summary(model4)
Residuals:
    Min      1Q  Median      3Q     Max 
-2.3733 -0.5314 -0.1135  0.4618  3.1804 

Coefficients:
                      Estimate Std. Error t value Pr(>|t|)    
(Intercept)           0.701734   0.324214   2.164   0.0310 *  
Possession           -0.006017   0.004931  -1.220   0.2231    
AttemptsOnTheTarget   0.244333   0.020238  12.073  < 2e-16 ***
BlockedShots         -0.013289   0.018946  -0.701   0.4835    
FreeKicks             0.008112   0.011305   0.718   0.4734    
Corners              -0.065608   0.016234  -4.041 6.34e-05 ***
Offsides              0.010421   0.025420   0.410   0.6821    
Odds                 -0.037948   0.020807  -1.824   0.0689 .  
Winner                1.020616   0.093913  10.868  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 0.8209 on 411 degrees of freedom
Multiple R-squared:  0.5441,    Adjusted R-squared:  0.5353 
F-statistic: 61.32 on 8 and 411 DF,  p-value: < 2.2e-16


```
*Listing 9 - Statistical parametres of regression model4*

The process is being repeated, now another variable will be removed which has been shown not to be statistically significant in the model: *Offsides*.

```R
model5 = lm(Goals ~ Possession + AttemptsOnTheTarget + BlockedShots + FreeKicks + Corners + Odds +  Winner, data = Premier)
```
*Listing 10 - Regression model5*

Multiple R-squared = 0.544, Adjusted R-squared 0.5362.  

```R
summary(model5)

Residuals:
    Min      1Q  Median      3Q     Max 
-2.3651 -0.5353 -0.1132  0.4609  3.1902 

Coefficients:
                      Estimate Std. Error t value Pr(>|t|)    
(Intercept)           0.729772   0.316598   2.305   0.0217 *  
Possession           -0.006100   0.004922  -1.239   0.2160    
AttemptsOnTheTarget   0.244570   0.020209  12.102  < 2e-16 ***
BlockedShots         -0.013125   0.018923  -0.694   0.4883    
FreeKicks             0.007791   0.011266   0.692   0.4896    
Corners              -0.065560   0.016217  -4.043 6.31e-05 ***
Odds                 -0.038441   0.020751  -1.853   0.0647 .  
Winner                1.021901   0.093766  10.898  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 0.8201 on 412 degrees of freedom
Multiple R-squared:  0.544,     Adjusted R-squared:  0.5362 
F-statistic:  70.2 on 7 and 412 DF,  p-value: < 2.2e-16

```
*Listing 11 - Statistical parametres of regression model5*

It can be noticed that the model is still improving, so iterations continue. The next variable that is being removed is *FreeKicks*.

```R
model6 = lm(Goals ~ Possession + AttemptsOnTheTarget + BlockedShots + Corners + Odds +  Winner, data = Premier)
```
*Listing 12 - Regression model6*

Multiple R-squared = 0.5434, Adjusted R-squared = 0.5368. It can be concluded that the model has improved slightly over several consecutive iterations, but has also simplified quite a bit, which is certainly desirable.

```R
summary(model6)

Residuals:
    Min      1Q  Median      3Q     Max 
-2.3303 -0.5452 -0.1030  0.4733  3.1746 

Coefficients:
                      Estimate Std. Error t value Pr(>|t|)    
(Intercept)           0.822941   0.286321   2.874  0.00426 ** 
Possession           -0.005867   0.004907  -1.196  0.23256    
AttemptsOnTheTarget   0.244366   0.020194  12.101  < 2e-16 ***
BlockedShots         -0.013024   0.018911  -0.689  0.49141    
Corners              -0.065911   0.016199  -4.069 5.66e-05 ***
Odds                 -0.039411   0.020691  -1.905  0.05750 .  
Winner                1.019287   0.093631  10.886  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 0.8195 on 413 degrees of freedom
Multiple R-squared:  0.5434,    Adjusted R-squared:  0.5368 
F-statistic: 81.93 on 6 and 413 DF,  p-value: < 2.2e-16

```
*Listing 13 - Statistical parametres of regression model6*

In the next iteration, the variable *BlockedShots* will be omitted from the model.

```R
model7 = lm(Goals ~ Possession + AttemptsOnTheTarget + Corners + Odds +  Winner, data = Premier)
```
*Listing 14 - Regression model7*

Multiple R-squared = 0.5429, Adjusted R-squared = 0.5374. 

```R
summary(model7)

Residuals:
    Min      1Q  Median      3Q     Max 
-2.3486 -0.5514 -0.1102  0.4581  3.1274 

Coefficients:
                     Estimate Std. Error t value Pr(>|t|)    
(Intercept)          0.828374   0.286031   2.896  0.00398 ** 
Possession          -0.006434   0.004835  -1.331  0.18403    
AttemptsOnTheTarget  0.243746   0.020161  12.090  < 2e-16 ***
Corners             -0.070095   0.015007  -4.671 4.06e-06 ***
Odds                -0.039188   0.020675  -1.895  0.05873 .  
Winner               1.023903   0.093331  10.971  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 0.819 on 414 degrees of freedom
Multiple R-squared:  0.5429,    Adjusted R-squared:  0.5374 
F-statistic: 98.34 on 5 and 414 DF,  p-value: < 2.2e-16

```
*Listing 15 - Statistical parametres of regression model7*

The next variable that won't stay in the end model is *Possesion*.

```R
model8 = lm(Goals ~ AttemptsOnTheTarget + Corners + Odds +  Winner, data = Premier)
```
*Listing 16 - Regression model8*

Multiple R-squared = 0.5409, Adjusted R-squared = 0.5365. The values of both coefficients decreased compared to the previous iteration, but a variable that was not statistically significant was dropped and thus a simpler model was obtained.
```R
summary(model8)

Residuals:
    Min      1Q  Median      3Q     Max 
-2.3352 -0.5520 -0.1211  0.4925  3.1250 

Coefficients:
                    Estimate Std. Error t value Pr(>|t|)    
(Intercept)          0.50127    0.14638   3.425 0.000677 ***
AttemptsOnTheTarget  0.24045    0.02003  12.006  < 2e-16 ***
Corners             -0.07569    0.01442  -5.250 2.44e-07 ***
Odds                -0.02646    0.01835  -1.442 0.149982    
Winner               1.03715    0.09289  11.166  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 0.8198 on 415 degrees of freedom
Multiple R-squared:  0.5409,    Adjusted R-squared:  0.5365 
F-statistic: 122.3 on 4 and 415 DF,  p-value: < 2.2e-16

```
*Listing 17 - Statistical parametres of regression model8*

When the model has shown satisfactory results it can be selected to stop with iterations or to remove another variable *Odds*.

```R
model9 = lm(Goals ~ AttemptsOnTheTarget + Corners +  Winner, data = Premier)
```
*Listing 18 - Regression model9*

```R
Residuals:
    Min      1Q  Median      3Q     Max 
-2.3581 -0.5566 -0.1177  0.4626  3.1630 

Coefficients:
                    Estimate Std. Error t value Pr(>|t|)    
(Intercept)          0.34641    0.09962   3.477  0.00056 ***
AttemptsOnTheTarget  0.24544    0.01975  12.426  < 2e-16 ***
Corners             -0.07017    0.01392  -5.041 6.92e-07 ***
Winner               1.06676    0.09071  11.761  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 0.8208 on 416 degrees of freedom
Multiple R-squared:  0.5386,    Adjusted R-squared:  0.5353 
F-statistic: 161.9 on 3 and 416 DF,  p-value: < 2.2e-16
```
*Listing 19 - Statistical parametres of regression model9*

Reziduali krajnjeg modela variraju izmedju -2.2581 i 3.1630. Ovaj statistički parametar predstavlja razliku izmedju ostvarenih vrednosti i onih koje je model predvideo. Samim tim što medijana ima vrednost blisku nuli, to znači da regresioni model daje dobra predviđanja bliska realnim vrednostima. Medijana u datom kontekstu predstavlja vrednost reziduala pre koje se nalazi 50% ostalih vrednosti reziduala, a posle koje se nalazi preostalih 50%. 

Za kranji model čitamo vrednost Fišerove statistike koja iznosi 161.9 uz 416 stepeni slobode. Stepeni slobode se dobijaju kada se od ukupnog broja opservacija u uzorku oduzme broj promenljivih koje ušestvuju u modelu. Što je veća vrednost F statistike od broja 1 to je bolje za model. Ova mera zavisi od veličine uzorka i broja promenljivih koje se predviđaju, pa se mora posmatrati relativno u odnosu na broj opservacija.  

Residual standard error predstavlja meru kvaliteta modela linerne regresije. To je prosečna vrednost koliko će promenljiva Golovi odstupati od prave regresione linije. 

Na kraju je dobijen model u kome su sve preostale promenljive značajne sa najvišim nivoom značajnosti, pa se staje sa iteracijama. Dati model linearne regresije sa velikom preciznošću samo na osnovu šuteva u okvir gola, broja
kornera i krajnjeg pobednika meča može predvideti broj golova jedne ekipe na utakmici.

##	Predlozi poboljšanja modela
Podaci na kojima se zasniva dobijen model dosta zavise od ofanzivne igre fudbalskog tima, a ne obraća se toliko pažnja na defanzivne sposobnosti. Možda bi od bilo od još većeg značaja bile odbrambene karakteristike protivničkog tima. Na primer, nije isto da li će tim poput Mančester Junajteda imati 10 šuteva u okvir gola protiv nekog nižerazrednog tima ili protiv nekog pretendenta na titulu. Takodje, ukoliko je neki tim dobio crveni karton veća je verovatnoća da će primiti gol, pa je za jedan tim poželjno voditi računa i o nekim osnovnim karakteristikama protivnika, a ne samo o sopstvenim karakteristikama. Tako bi na primer mogao da se iskoristi dodatni atribut: da li je tim u nekom trenutku imao igrača više u odnosu na protivnika ili ne. To opet samo po sebi ne bi bilo toliko precizno s obzirom na dužinu perioda sa igračem više i slično.

Predstavljen model daje solidne rezultate koristeći samo neke od osnovnih statističkih parametara koji su dostupni svima. Činjenica je da se model može poboljšati dodavanjem još nekih statističkih paramatera. Postoje statistički podaci koji nisu dostupni svima i koji se plaćaju, a mogu dati bolji uvid u neke aspekte igre fudbalskih timova, poput ukupne kilometraže koju igrači pretrče na meču ili ukupan broj startova koje načine.  

##	Literatura	
-	EDX kurs, Analytics Edge, link: https://courses.edx.org/courses/course-v1:MITx+15.071x_3+1T2016/courseware/f8d71d64418146f18a066d7f0379678c/6248c2ecbbcb40cfa613193e8f1873c1/ , datum pristupa: 12.07.2016
-	 Wikipedia, “Linear regression” link: https://en.wikipedia.org/wiki/Linear_regression, datum pristupa: 11.07.2016
-	 Quick guide:Interpreting simple linear model output in R, link: https://rstudio-pubs-static.s3.amazonaws.com/119859_a290e183ff2f46b2858db66c3bc9ed3a.html, datum pristupa 13.07.2016
