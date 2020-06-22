### Tema d'esame - 7 Settembre 2007 - Basi di Dati

---

#### Progettazione Parte A

----

**Sono date le seguenti relazioni (le chiavi primarie sono sottolineate):**

> SALA_RIUNIONI (<u>CodS</u>, NumeroMaxPosti, Proiettore)
> PRENOTAZIONE_SALA (<u>CodS</u>, <u>Data</u>, <u>OraInizio</u>, OraFine, CodDip)
> DIPENDENTE(<u>CodDip</u>, Nome, Cognome, DataNascita, Città)



Esprimere le seguenti interrogazioni:

**(b)** Visualizzare il codice e il numero massimo di posti delle sale dotate di proiettore che sono state prenotate almeno 15 volte per riunioni che iniziano prima delle ore 15:00, ma non sono mai state prenotate per riunioni che cominciano dopo le ore 20:00.

``` sql
SELECT CodS, NumeroMaxPosti
FROM SALA_RIUNIONI
WHERE CodS IN (SELECT CodS
               FROM PRENOTAZIONE_SALA
               WHERE OraInizio < TO_DATE('15:00', 'HH24:MI')
               GROUP BY CodS
               HAVING COUNT(*) >= 15)
    AND CodS NOT IN (SELECT CodS
                     FROM PRENOTAZIONE_SALA
                     WHERE OraInizio > TO_DATE('20:00', 'HH24:MI'))
	AND Proiettore = 1;
```

**(c)** Visualizzare per ogni sala il codice della sala, il numero massimo di posti e il numero di prenotazioni considerando solo l’ultima data in cui la sala è stata prenotata.

``` sql
SELECT S.CodS, S.NumeroMaxPosti, COUNT(*)
FROM SALA_RIUNIONI S, PRENOTAZIONE_SALA PS
WHERE S.CodS = PS.CodS
GROUP BY S.CodS, S.NumeroMaxPosti
HAVING PS.Data = (SELECT MAX(Data)
                  FROM PRENOTAZIONE_SALA PS2
                  WHERE PS2.CodS = PS.CodS);
```

