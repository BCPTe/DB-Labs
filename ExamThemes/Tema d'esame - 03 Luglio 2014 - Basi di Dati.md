### Tema d'esame - 03 Luglio 2014 - Basi di Dati

---

#### Progettazione Parte A

----

**Sono date le seguenti relazioni (le chiavi primarie sono sottolineate):**

> INGEGNERE (<u>CodI</u>, NomeI, Indirizzo, PartitaIVA, Città)
> AZIENDA (<u>CodA</u>, NomeA, TipologiaAzienda, Città, SitoWeb)
> SETTORE(<u>CodS</u>, NomeSettore, Descrizione)
> CONSULENZE EFFETTUATE(<u>CodI</u>, <u>CodA</u>, <u>Data</u>, CodS, Importo)
>
> _TipologiaAzienda = {microimpresa, piccolaimpresa, mediaimpresa, grandeimpresa}_



Esprimere le seguenti interrogazioni:

**(b)** Per ciascun ingegnere di Torino che non ha mai effettuato consulenze nel settore ”Edilizia”, visualizzare il nome dell’ingegnere e il numero complessivo di consulenze effettuate.

``` sql
SELECT I.NomeI, COUNT(*)
FROM INGEGNERE I, CONSULENZE_EFFETTUATE CE
WHERE I.Città = 'Torino' AND I.CodI = CE.CodI
	AND I.CodI NOT IN (SELECT CE2.CodI
                       FROM CONSULENZE_EFFETTUATE CE2, SETTORE S
					   WHERE CE2.CodS = S.CodS AND S.NomeSettore = 'Edilizia')
GROUP BY I.CodI, I.NomeI;
```

**(c)** Per ciascuna azienda, visualizzare il nome dell’azienda e il nome dell’ingegnere che ha effettuato il maggior numero di consulenze presso quell’azienda nell’anno 2013.

``` sql
SELECT A.NomeA, I.NomeI
FROM AZIENZA A, INGEGNERE I, CONSULENZE_EFFETTUATE CE
WHERE A.CodA = CE.CodA AND I.CodI = CE.CodI
GROUP BY A.CodA, I.CodI, A.NomeA, I.NomeI
HAVING COUNT(*) = (SELECT MAX(COUNT(*))
                   FROM CONSULENZE_EFFETTUATE CE2
                   WHERE CE2.Data >= TO_DATE('01/01/2013', 'DD/MM/YYYY')
                   	   AND CE2.Data <= TO_DATE('31/12/2013', 'DD/MM/YYYY')
                  	   AND CE2.CodA = CE.CodA
                   GROUP BY CE2.CodA, CE2.CodI);
```

