### Tema d'esame - 17 Luglio 2015 - Basi di Dati

---

#### Progettazione Parte A

----

**Sono date le seguenti relazioni (le chiavi primarie sono sottolineate):**

> STUDENTE (<u>MatrS</u>, NomeS, AnnoLaurea, TitoloStudio, VotoLaurea)
> DIPARTIMENTO (<u>CodD</u>, NomeD, Settore-Scientifico, NumDocenti)
> CONCORSO_DOTTORATO (<u>CodC</u>, <u>CodD</u>, DataPubblicazione, DataScadenza, NumPostiDisponibili) 
> STUDENTE_PARTECIPA_CONCORSO_DOTTORATO (<u>CodC</u>, <u>CodD</u>, <u>MatrS</u>, DataInvioDomanda)



Esprimere le seguenti interrogazioni:

**(b)** Per i dipartimenti in cui sono stati effettuati solo concorsi di dottorato aventi ciascuno un numero di posti disponibili superiore a 7, visualizzare il nome del dipartimento, il settore scientifico di afferenza e il numero di concorsi di dottorato con data di pubblicazione successiva a Marzo 2014.

``` sql
SELECT D.NomeD, D.Settore_Scientifico,
		COUNT(SELECT *
			  FROM CONCORSO_DOTTORATO CD2
			  WHERE CD2.CodD = CD.CodD
			  	  AND CD2.Data >= TO_DATE('01/04/2014', 'DD/MM/YYYY'))
FROM DIPARTIMENTO D, CONCORSO_DOTTORATO CD
WHERE CD.CodD = D.CodD AND CD.NumPostiDisponibili > 7;
```

**(c)** Per ciascuno studente che ha partecipato a concorsi di dottorato per almeno 3 settori scientifici diversi, visualizzare il nome dello studente e il settore scientifico per cui ha partecipato a tutti i concorsi.

``` sql
SELECT S.NomeS, D.Settore-Scientifico
FROM STUDENTE S, DIPARTIMENTO D, STUDENTE_PARTECIPA_CONCORSO_DOTTORATO SPCD
WHERE SPCD.CodD = D.CodD AND SPCD.MatrS = S.MatrS
	AND S.MatrS IN (SELECT SPCD2.MatrS
				    FROM DIPARTIMENTO D2, STUDENTE_PARTECIPA_CONCORSO_DOTTORATO SPCD2
				    WHERE D2.CodD = SPCD2.CodD
				    GROUP BY SPCD2.MatrS
				    HAVING COUNT(DISTINCT Settore-Scientifico) >= 3)
GROUP BY S.MatrS, D.Settore-Scientifico, S.NomeS
HAVING COUNT(*) = (SELECT COUNT(*)
		  		   FROM DIPARTIMENTO D3
		  		   WHERE D3.CodD = SPCD.CodD
                   	   AND D3.Settore-Scientifico = D.Settore-Scientifico);
```