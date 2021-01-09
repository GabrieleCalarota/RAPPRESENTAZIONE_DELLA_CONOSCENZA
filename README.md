# RAPPRESENTAZIONE_DELLA_CONOSCENZA
Repository per estrazione e classificazione non supervisionata di dataset di licenze software estrappolate da github.

## RIPRODUZIONE DEL LAVORO
### DOWNLOAD DATASET ORIGINALE
Il dataset di partenza è scaricabile a questo link: https://annex.softwareheritage.org/public/dataset/content-samples/2019-03-21-license-blobs/
Il dataset vero è proprio è contenuto nella cartella `license-blobs.tar` ed è strutturato come segue:

```
license-blobs/
|-00/
|-|-00/
|-|-|-sha1file.gz
|-|-|-sha1file2.gz
|-|-|-..
|-|-01/
|-|-..
|-01/
|-..
```
Sono presenti 256 cartelle nella cartella root `license_blobs` e in ognuna di queste altre 256 cartelle, contententi dai 60 ai 100 file ciascuna in formato testo compresso con gzip.

Per procedere con il lavoro, scaricare il progetto ed estrarre l'archivio license-blobs.tar

La struttura del progetto dovrebbe ora essere la seguente:
```
RAPPRESENTAZIONE_DELLA_CONOSCENZA/
|-license-blobs/
|-Clustering-v2.ipynb
|-Codice_estrazione.ipynb
|-online_vectorizers.py
|-Estrazione2_parsing_punctuation.ipynb
|-README.md
```
### ESTRAZIONE DEI FILE e DATA-PREPROCESSING
A questo punto possiamo runnare il notebook `Codice_estrazione.ipynb`.
Il notebook effettuerà le seguenti operazioni:
 - Downloaderà il dataset se non presente nella cartella del notebook
 - Estrarrà ogni singolo file .gz compresso e lo trasformerà in un file con estensione .tmp in foto non compresso (**! ATTENZIONE: si necessiterà di almeno 36 GB di memoria fisica**)
 - Andrà a creare una cartella chiamata `dataset_parsed` in cui andrà a scrivere 256 csv uno per ogni cartella root con il contenuto dei file di ogni sua sottocartella.
Il file csv avrà i seguenti header: `text`, `path`
```
> df.head()
	Unnamed: 0	text	path
0	0	<!DOCTYPE html> <html lang="pt-br" xmlns:og="h...	.\license-blobs\00\0a\000a799c27dfbad9a943dbf5...
1	1	Apache License Version 2.0, January 2004 http:...	.\license-blobs\00\20\0020f802a61ffa7d84e51dc4...
2	2	<!DOCTYPE html> <html> <head> <title></title> ...	.\license-blobs\00\22\00226daed73e0cb83a6292e3...
3	3	<!DOCTYPE html> <html lang="fr" prefix="fb: ht...	.\license-blobs\00\2b\002bc72c8a3daecbb6047ac5...
4	4	# work licensed Creative Commons Attribution-N...	.\license-blobs\00\2c\002c0a5bf56d20b864010404...
```
- Verranno rimosse tutte le parole appartenenti alle stopwords di lingua inglese, scaricabili da `ntlk.corpus.stopwords.download('english')`

#### ULTERIORE PRE-PROCESSING
Se si vuole rimuovere anche tutte le parole contenenti la punteggiatura, basta runnare il notebook `Estrazione2_parsing_punctuation.ipynb` che andrà a creare una nuova cartella `dataset_parsed_new` con la stessa struttura di file e cartelle, ma conterrà molte meno parole!

### CLUSTERING
Per generare il clustering, basterà runnare il notebook `Clustering-v2.ipynb` scegliendo con cura il valore della cartella base_dir, scegliendo tra `dataset_parsed` oppure `dataset_parsed_new` a seconda di con che livello di raffinatezza si vuole lavorare sul dataset.

Le operazioni che effettuerà sono:
 - Creazione del vocabolario
 - Creazione della matrice delle features con TFIDF
 - Salvataggio in formato `.pickle` del vectorizer
 - Salvataggio in formato `.npz` della matrice delle features per velocizzare il processo di re-training del cluster
 - Training del cluster `KMeans` con numero di cluster crescenti da 1 a 10, plottando il risultato con il cosiddetto `elbow-method`
 - Training del cluster con il numero di cluster adeguato (noi abbiamo scelto 6, ma si può cambiare settando oppurtunamente il valore della variabile `true_k`)
 - Stampa delle top 15 parole chiave per ogni cluster creato
 - Plot con PCA dimensionality reduction della disposizione dei documenti in un piano 2D, con un colore diverso per ogni cluster
 
> E' possibile settare le variabili `TELEGRAM_BOT_TOKEN` e `TELEGRAM_CHAT_ID` per essere notificati durante il completamento dei passaggi

### RISULTATI
Il dataset parsato (formato `dataset_parsed_new`), assieme ai risultati e ai modelli sono disponibili al seguente link in formato `.zip`:
https://casacalarota.onthewifi.com/wp-content/uploads/2020/10/RDC/Dataset_results.zip
