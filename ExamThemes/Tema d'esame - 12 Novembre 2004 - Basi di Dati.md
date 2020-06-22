### Tema d'esame - 12 Novembre 2004 - Basi di Dati

---

#### Progettazione Parte A

----

**Sono date le seguenti relazioni (le chiavi primarie sono sottolineate):**

> UTENTE (<u>NumTel</u>, NomeU, Città)
> CHIAMATA (<u>NumTelChiamante</u>, <u>NumTelChiamato</u>, <u>Data</u>, <u>Ora</u>, Durata)



Esprimere le seguenti interrogazioni:

**(b)** Trovare il numero di telefono degli utenti che hanno chiamato solo utenti della stessa città.

``` sql
SELECT NumTel
FROM UTENTE
WHERE NumTel IN (SELECT U.NumTel
				 FROM UTENTE U, UTENTE U2, CHIAMATA C
				 WHERE U.NumTel = C.NumTelChiamante
					 AND U2.NumTel = C.NumtelChiamato
					 AND U.NumTel <> U2.NumTel AND U.Città = U2.Città)
```

**(c)** Per le città con più di 10.000 utenti, visualizzare numero di telefono e nome degli utenti della città, numero totale di telefonate effettuate da tali utenti nel giorno 11/11/04 e loro durata complessiva.

``` sql

```

