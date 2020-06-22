### Tema d'esame - 17 Luglio 2014 - Basi di Dati

---

#### Progettazione Parte A

----

**Sono date le seguenti relazioni (le chiavi primarie sono sottolineate):**

> TURISTA (<u>CodFiscT</u>, Nome, DataNascita, CartaCredito, Nazione)
> VILLAGGIO_TURISTICO (<u>CodVT</u>, <u>NomeSocietà</u>, Nome, Città, #Stelle)
> SERVIZI_DISPONIBILI (<u>CodVT</u>, <u>NomeSocietà</u>, <u>NomeServizio</u>)
> PRENOTAZIONE_SOGGIORNO (<u>CodFiscT</u>, <u>DataInizioSoggiorno</u>, CodVT, NomeSocietà, DataFineSoggiorno, Importo, #Adulti, #Bambini, Acconto)



Esprimere le seguenti interrogazioni:

**(b)** Per ciascun turista nato dopo il 1980 che ha effettuato almeno 3 prenotazioni nello stesso villaggio turistico, visualizzare il nome e la nazione del turista, il numero complessivo di prenotazioni effettuale, il numero totale di adulti per cui sono state effettuate le prenotazioni e il corrispondente importo complessivo.

``` sql
SELECT T.Nome, T.Nazione, COUNT(*), SUM(#Adulti), SUM(Importo)
FROM TURISTA T, PRENOTAZIONE_SOGGIORNO PS
WHERE PS.CodFiscT = T.CodFiscT
	AND T.DataNascita >= TO_DATE('01/01/1981', 'DD/MM/YYYY')
GROUP BY T.CodFiscT, PS.CodVT
HAVING COUNT >= 3;
```

**(c)** Visualizzare il codice del villaggio turistico e il nome della società di afferenza per ciascun villaggio turistico 4 stelle che non ha mai ricevuto una prenotazione con un acconto pari all’importo, e per cui il numero complessivo di prenotazioni ricevute da turisti di nazionalità italiana sia superiore al numero complessivo di prenotazioni ricevute da turisti di nazionalità tedesca.

``` sql
SELECT VT.CodVT, VT.NomeSocietà
FROM VILLAGGIO_TURISTICO VT
WHERE VT.#Stelle = 4
	AND (VT.CodVT, VT.NomeSocietà)
			NOT IN (SELECT VT2.CodVT, VT2.NomeSocietà
					FROM VILLAGGIO_TURISTICO VT2, PRENOTAZIONE_SOGGIORNO PS
					WHERE VT2.CodVT = PS.CodVT AND PS.Acconto = PS.Importo)
	AND (SELECT COUNT(*)
	     FROM PRENOTAZIONE_SOGGIORNO PS
	     WHERE PS.CodVT = VT.CodVT, TURISTA T2
		   	 AND T2.Nazione = 'Italia')
	>
		(SELECT COUNT(*)
		 FROM PRENOTAZIONE_SOGGIORNO PS, TURISTA T3
	     WHERE PS.CodVT = VT.CodVT
			 AND T3.Nazione = 'Germania');
```