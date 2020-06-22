### Tema d'esame - 16 Settembre 2005 - Basi di Dati

---

#### Progettazione Parte A

----

**Sono date le seguenti relazioni (le chiavi primarie sono sottolineate):**

> DIPENDENTE (<u>Matr</u>, Nome, Mansione, CodD)
> DIPARTIMENTO (<u>CodD</u>, NomeD)
> FERIE (<u>Matr</u>, <u>DataInizio</u>, DurataInGiorni)



Esprimere le seguenti interrogazioni:

**(b)** Visualizzare matricola e nome per i dipendenti che hanno effettuato un solo periodo singolo di ferie con durata maggiore della durata media dei periodi singoli di ferie dei dipendenti dello stesso dipartimento con la stessa mansione.

``` sql
SELECT D.Matr, D.Nome
FROM DIPENDENTE D, FERIE F
WHERE D.Matr = F.Matr
	AND F.DurataInGiorni > (SELECT AVG(F2.DurataInGiorni)
                            FROM DIPENDENTE D2, FERIE F2
                            WHERE F2.Matr = D2.Matr
                            	AND D2.CodD = D.CodD
                           		AND D2.Mansione = D.Mansione)
GROUP BY D.Matr, D.Nome
HAVING COUNT(*) = 1;
```

**(c)** Visualizzare il codice dei dipartimenti che non hanno nessun dipendente che ha effettuato complessivamente più di 21 giorni di ferie.

``` sql
SELECT CodD
FROM DIPARTIMENTO
WHERE CodD NOT IN (SELECT DP.CodD
                   FROM DIPENDENTE DP, FERIE F
                   WHERE DP.Matr = F.Matr
                   GROUP BY DP.CodD, DP.Matr
                   HAVING SUM(F.DurataInGiorni) > 21);
```

