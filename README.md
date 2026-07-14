# Previsione dell'insolvenza creditizia — Credit Card Default

Progetto di fine corso per **Introduzione al Pensiero Computazionale e alla Data Science** (a.a. 2025/26, Università di Bologna).

## Gruppo N. 1

| Membro | Matricola |
|---|---|
| Cota Miriam | 1216760 |
| Monopoli Alessandro | 1232093 |
| Ponissi Giuliana | 1216083 |

## Descrizione del progetto

L'obiettivo è prevedere se un cliente di carta di credito sarà **insolvente** (*default*) sul pagamento del mese successivo, a partire da caratteristiche demografiche, limite di credito, storico dei ritardi e importi fatturati/pagati. Il lavoro comprende: comprensione e pulizia del dataset, analisi esplorativa, confronto di tre modelli di classificazione, esperimenti di robustezza (soglia di decisione, numero di alberi, proporzione dello split) e valutazione critica dei risultati.

## Dataset

`Credit_Card_Default.csv`: 30.000 clienti di carte di credito taiwanesi, 23 variabili esplicative e una variabile target binaria (`default payment next month`: 1 = insolvente, 0 = solvente). La descrizione delle variabili è in `data/Credit_Card_Default_Description.txt`. Il dataset è il *Default of Credit Card Clients* dell'UCI Machine Learning Repository (Yeh & Lien, 2009).

Caratteristiche rilevanti emerse dall'analisi: classi sbilanciate (22,1% di insolventi), valori non documentati in `EDUCATION`, `MARRIAGE` e `PAY_*`, forte correlazione fra le variabili `BILL_AMT`.

## Modelli usati

1. **Regressione logistica** (modello lineare)
2. **k-Nearest Neighbors** con k=25 (modello non lineare)
3. **Random Forest** con 100 alberi (modello non lineare; la scelta di `n_estimators=100` è motivata da un esperimento dedicato nel notebook)

Preparazione dati: one-hot encoding delle variabili categoriche, train/test split 70/30 stratificato, standardizzazione delle feature (fit sul solo training set).

### Risultati principali (test set, 9.000 clienti)

| Modello | Accuracy | Precision (cl. 1) | Recall (cl. 1) | F1 (cl. 1) |
|---|---|---|---|---|
| Regressione logistica | 0,809 | 0,698 | 0,240 | 0,357 |
| k-NN (k=25) | 0,809 | 0,647 | 0,297 | 0,407 |
| Random Forest | **0,813** | 0,636 | **0,366** | **0,464** |

Baseline (classe maggioritaria): accuracy 0,779. A soglia standard il modello migliore è la Random Forest; la feature più predittiva è lo stato del pagamento più recente (`PAY_0`).

### Esperimenti di robustezza

- **Soglia di decisione (regressione logistica):** abbassando la soglia da 0,5 a 0,3 il recall sale da 0,240 a 0,456 e l'f1 raggiunge **0,503**, superando la Random Forest a soglia standard: in questo problema la calibrazione della soglia incide più della scelta dell'algoritmo.
- **Numero di alberi (Random Forest):** oltre ~50 alberi le metriche si stabilizzano; adottiamo 100 come miglior rapporto prestazioni/costo.
- **Split 70/30 vs 80/20:** i risultati sono stabili rispetto alla proporzione dello split (scostamenti entro circa ±0,02).

## Struttura del repository

```
/
├── data/        # dataset e descrizione delle variabili
├── notebooks/   # analisi_credit_default.ipynb (notebook eseguito, tutte le fasi)
├── figures/     # grafici esportati dal notebook
├── report/      # relazione LaTeX (sorgenti Overleaf + PDF finale)
└── README.md
```

## Istruzioni per l'esecuzione

1. Requisiti: Python ≥ 3.10 con `pandas`, `numpy`, `seaborn`, `matplotlib`, `scikit-learn` (`pip install pandas numpy seaborn matplotlib scikit-learn`).
2. Aprire `notebooks/analisi_credit_default.ipynb` (Google Colab o Jupyter).
3. Il notebook legge il dataset e salva i grafici nella cartella `sample_data/`: su **Colab** caricare `data/Credit_Card_Default.csv` nella cartella `sample_data/` del runtime (dal pannello File a sinistra); in **locale** creare una cartella `sample_data/` accanto al notebook e copiarvi il CSV.
4. Eseguire le celle dall'inizio alla fine. I risultati sono riproducibili (`random_state = 42`).

## Uso di assistenti LLM

Nel corso del progetto è stato utilizzato un assistente LLM (Claude) per la convalida del codice, il miglioramento della resa grafica e la risoluzione di errori, come documentato nel notebook. Tutto il codice è stato rivisto e compreso dai membri del gruppo.
