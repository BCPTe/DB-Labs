### Tema d'esame - 23 Gennaio 2014 - Basi di Dati

---

#### Progettazione Parte A

----

**Sono date le seguenti relazioni (le chiavi primarie sono sottolineate):**

> CORSA_CAMPESTRE (<u>CodCorsa</u>, NomeCorsa, Categoria, Città, Regione, Data, CodOrganizzatore)
> ORGANIZZATORE (<u>CodOrganizzatore</u>, NomeO)
> CONCORRENTE (<u>CodConcorrente</u>, NomeC, DataNascita, Indirizzo, Citt`a, Regione) CONCORRENTE_PARTECIPA_CORSA_CAMPESTRE(<u>CodConcorrente</u>, <u>CodCorsa</u>, Posizione)



Esprimere le seguenti interrogazioni:

**(b)** Visualizzare il nome di ciascuna persona che, nella regione Piemonte, ha organizzato almeno una corsa campestre per la categoria Under 14 ma nessuna corsa campestre per la categoria Under 20.

``` sql
SELECT NomeO
FROM ORGANIZZATORE
WHERE CodOrganizzatore IN (SELECT CodOrganizzatore
                           FROM CORSA_CAMPESTRE
                           WHERE Categoria = 'Under 14' AND Regione = 'Piemonte')
	 AND CodOrganizzatore NOT IN (SELECT CodOrganizzatore
                             	  FROM CORSA_CAMPESTRE
                             	  WHERE Categoria = 'Under 20' AND Regione = 'Piemonte');
```

**(c)** Visualizzare il nome di ciascun concorrente che ha partecipato a tutte le corse campestri organizzate nella propria regione, ma non ha mai conseguito la prima posizione in nessuna corsa campestre.

``` sql
SELECT C.NomeC
FROM CONCORRENTE C, CONCORRENTE_PARTECIPA_CORSA_CAMPESTRE CPCC, CORSA_CAMPESTRE CC
WHERE CPCC.CodConcorrente = C.CodConcorrente
	AND CPCC.CodCorsa = CC.CodCorsa AND CC.Regione = C.Regione
	AND CC.CodConcorrente NOT IN (SELECT CodConcorrente
                                  FROM CONCORRENTE_PARTECIPA_CORSA_CAMPESTRE
                                  WHERE Posizione = 1)
GROUP BY C.CodConcorrente, C.NomeC, C.Regione
HAVING COUNT(*) = (SELECT COUNT(*)
                   FROM CORSA_CAMPESTRE
                   WHERE Regione = C.Regione)
```

