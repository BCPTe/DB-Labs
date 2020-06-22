### Tema d'esame - 15 Febbraio 2007 - Basi di Dati

---

#### Progettazione Parte A

----

**Sono date le seguenti relazioni (le chiavi primarie sono sottolineate):**

> LIBRO_PUBBLICATO (<u>ISBN</u>, Titolo, CodA, Genere)
> AUTORE (<u>CodA</u>, NomeAutore, DataNascita, Città)
> VENDITA (<u>CodVendita</u>, ISBN, Data, Ora, Prezzo)



Esprimere le seguenti interrogazioni:

**(b)** Visualizzare il nome degli autori che hanno pubblicato almeno un libro di genere “fantasy”, ma non hanno mai pubblicato un libro di genere “avventura” che è stato venduto in più di 5.000 copie.

``` sql
SELECT NomeAutore
FROM AUTORE
WHERE CodA IN (SELECT CodA
	           FROM LIBRO_PUBBLICATO
	           WHERE Genere = 'fantasy')
	AND NOT IN (SELECT LP.CodA
				FROM LIBRO_PUBBLICATO LP, VENDITA V
				WHERE V.ISBN = LP.ISBN
                	AND LP.Genere = 'avventura'
				GROUP BY LP.CodA, LP.ISBN
				HAVING COUNT(*) > 5000);
```

**(c)** Visualizzare il titolo e il genere dei libri per cui sono state vendute complessivamente almeno 4.000 copie, e che nel maggio 2006 hanno avuto un incasso superiore a quello avuto nel marzo 2006.

``` sql
SELECT Titolo, Genere
FROM LIBRO_PUBBLICATO
WHERE ISBN IN (SELECT ISBN
			   FROM VENDITA
			   WHERE Data >= TO_DATE('01/05/2006', 'DD/MM/YYYY')
				   AND Data <= TO_DATE('31/05/2006', 'DD/MM/YYYY')
			   GROUP BY ISBN
			   HAVING SUM(Prezzo) > (SELECT SUM(Prezzo)
				        			 FROM VENDITA
				        			 WHERE Data >= TO_DATE('01/03/2006', 'DD/MM/YYYY')
										 AND Data <= TO_DATE('31/03/2006', 'DD/MM/YYY')))
	AND ISBN IN (SELECT ISBN
		         FROM VENDITA
		         GROUP BY(ISBN)
		         HAVING COUNT(*) >= 4000);
```