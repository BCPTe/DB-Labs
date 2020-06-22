### Tema d'esame - 19 Novembre 2004 - Basi di Dati

---

#### Progettazione Parte A

----

**Sono date le seguenti relazioni (le chiavi primarie sono sottolineate):**

> DIPENDENTE (<u>Matr</u>, NomeD)
> LINGUE-CONOSCIUTE (<u>Matr</u>, <u>Lingua</u>)
> PROGETTO (<u>CodP</u>, NomeP)
> LAVORA-IN (<u>Matr</u>, <u>CodP</u>, <u>DataInizio</u>, DataFine, Mansione)



Esprimere le seguenti interrogazioni:

**(a)** Visualizzare codice e nome dei progetti in cui lavora almeno un dipendente che conosce due o più lingue e non lavora nessun dipendente che conosce l'inglese o lo spagnolo.

``` sql
SELECT P.CodP, P.NomeP
FROM PROGETTO P, LINUE-CONOSCIUTE LC
WHERE P.CodP NOT IN (SELECT P2.CodP
               	     FROM PROGETTO P2, LAVORA-IN LI, LINGUE-CONOSCIUTE LC2
                     WHERE P2.CodP = LI.CodP AND LI.Matr = LC2.Matr
              	   	     AND (LC2.Lingua = 'Inglese' OR LC2.Lingua = 'Spagnolo')
GROUP BY LC.Matr, P.CodP, P.NomeP
HAVING COUNT(DISTINCT LC.Lingua) >= 2);
```

**(b)** Per i dipendenti che hanno lavorato complessivamente per più di 6 mesi nello stesso progetto, visualizzare matricola, nome e numero totale di progetti diversi in cui hanno lavorato.

``` sql
SELECT D.Matr, D.NomeD, COUNT(*)
FROM DIPENDENTE D, PROGETTO P, LAVORA-IN LI
WHERE D.Matr = LI.Matr AND LI.CodP = P.CodP
	AND D.Matr IN (SELECT D2.Matr
                   FROM DIPENDENTE D2, LAVORA-IN LI2, PROGETTO P2
                   WHERE D2.Matr = LI2.Matr AND LI2.CodP = P.CodP
                   GROUP BY D2.Matr, LI2.CodP, LI.DataInizio
                   HAVING SUM(DATEDIFF(month, LI.DataFine, LI.DataInizio)) > 6)
GROUP BY D.Matr, D.NomeD;
```

