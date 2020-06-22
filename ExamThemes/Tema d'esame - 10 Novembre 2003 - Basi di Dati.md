### Tema d'esame - 10 Novembre 2003 - Basi di Dati

---

#### Progettazione Parte A

----

**Sono date le seguenti relazioni (le chiavi primarie sono sottolineate):**

> CONCERTO (<u>CodC</u>, NomeOrchestra, Durata)
> PROGRAMMA (<u>Data</u>, <u>OraInizio</u>, Sala, CodC)
> BIGLIETTO-VENDUTO (<u>NumPosto</u>, <u>Data</u>, <u>OraInizio</u>, <u>Sala</u>, TipoPosto, Prezzo)



Esprimere le seguenti interrogazioni:

**(a)** Trovare il codice dei concerti tenuti almeno due volte nella stessa sala e che non sono mai stati tenuti prima delle 20.

``` sql
SELECT P1.CodC
FROM PROGRAMMA P1, PROGRAMMA P2
WHERE P1.Sala = P2.Sala AND P1.CodC = P2.CodC
	AND (P1.Data <> P2.Data OR P1.OrarioInizio <> P2.OrarioInizio)
	AND P1.OrarioInizio > 20 AND P2.OrarioInizio > 20
GROUP BY P1.Sala, P1.CodC
HAVING COUNT(*) >= 2;
```

**(b)** Per gli spettacoli rappresentati in almeno cinque sale diverse, trovare codice del concerto e numero totale di biglietti venduti con un prezzo superiore al prezzo medio del biglietto per quel concerto.

``` sql
SELECT P.CodC, COUNT(*)
FROM PROGRAMMA P, BIGLIETTO-VENDUTO BV
WHERE AND P.Data = BV.Data AND P.OrarioInizio = BV.OrarioInizio
	AND P.Sala = BV.Sala AND P.CodC IN (SELECT CodC
										FROM PROGRAMMA
                                        GROUP BY CodC
                                        HAVING COUNT(DISTINCT Sala) >= 5)
	AND BV.Prezzo > (SELECT AVG(Prezzo)
					 FROM BIGLIETTO-VENDUTO
					 WHERE P.Data = Data AND P.OrarioInizio = OrarioInizio
                     	 AND P.Sala = Sala)
GROUP BY P.CodC;
```

