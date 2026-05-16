# SuperEnalotto — Archivio storico estrazioni

Archivio completo di tutte le estrazioni del SuperEnalotto dal **3 dicembre 1997** (primo concorso) a oggi.

I dati sono forniti in formato **CSV**, pronti per essere caricati in qualsiasi tool di analisi (Excel, Google Sheets, Python/pandas, R, BigQuery, ecc.).

## Contenuto

- [`superenalotto.csv`](./superenalotto.csv) — tutte le estrazioni, una riga per concorso.
- [`sestine_vincenti_6.csv`](./sestine_vincenti_6.csv) — elenco delle sole sestine che hanno realizzato un **"6"** (jackpot), con data, montepremi e regione.

## Schema CSV

| Colonna | Tipo | Descrizione |
|----------|------|--------------|
| `data` | `YYYY-MM-DD` | Data del concorso |
| `concorso` | int | Numero progressivo del concorso (resettato annualmente) |
| `n1` … `n6` | int (1-90) | Sei numeri principali estratti, ordine di estrazione del sito sorgente |
| `jolly` | int (1-90) | Numero Jolly (vuoto se non disponibile) |
| `superstar` | int (1-90) | Numero SuperStar (vuoto per i concorsi anteriori alla sua introduzione) |

### Esempio

```csv
data,concorso,n1,n2,n3,n4,n5,n6,jolly,superstar
1997-12-03,87,20,36,39,41,72,76,88,
2026-05-15,78,5,13,17,28,47,68,42,
```

## Statistiche rapide

- **Periodo coperto**: 1997-12-03 → ultima estrazione disponibile
- **Estrazioni totali**: ~4.180+
- **Range numeri**: 1-90

## Esempi di utilizzo

### Python (pandas)

```python
import pandas as pd

df = pd.read_csv('superenalotto.csv', parse_dates=['data'])

# Frequenza per numero
freq = pd.concat([df[f'n{i}'] for i in range(1,7)]).value_counts().sort_index()

# Numeri estratti negli ultimi 12 mesi
last_year = df[df['data'] >= df['data'].max() - pd.Timedelta(days=365)]
```

### SQL (BigQuery / SQLite)

```sql
SELECT n, COUNT(*) AS freq
FROM (
  SELECT n1 AS n FROM estrazioni UNION ALL
  SELECT n2 FROM estrazioni UNION ALL
  SELECT n3 FROM estrazioni UNION ALL
  SELECT n4 FROM estrazioni UNION ALL
  SELECT n5 FROM estrazioni UNION ALL
  SELECT n6 FROM estrazioni
)
GROUP BY n
ORDER BY freq DESC;
```

## Fonte dati

I dati sono stati raccolti dall'archivio pubblico [estrazionilotto.it](https://www.estrazionilotto.it/superenalotto/archivio-storico/) e normalizzati in CSV.

## Note metodologiche

- Vengono memorizzati i **6 numeri principali** + Jolly + SuperStar. Non sono inclusi i numeri delle quote secondarie introdotte successivamente (es. SuperStar è stato introdotto nel 2006, quindi i concorsi precedenti hanno il campo vuoto).
- Le date sono convertite in formato ISO (`YYYY-MM-DD`) a partire dal formato italiano della fonte (`lunedì 12 maggio 2026`).
- L'ordine dei sei numeri principali rispecchia quello mostrato dalla fonte e **non è ordinato in modo crescente**.

## Aggiornamento

Il dataset viene aggiornato periodicamente. Per riprodurre la procedura: vedi il progetto [superenalotto-checker](https://github.com/bwlab/superenalotto-checker) (parser e tool di analisi).

## Licenza

I dati sono di pubblico dominio (risultati di estrazioni ufficiali). Il presente repository è rilasciato sotto licenza **MIT**.

## Disclaimer

Questo archivio è fornito **a solo scopo informativo e statistico**. Il SuperEnalotto è un gioco d'azzardo: le estrazioni sono indipendenti e nessuna analisi statistica può prevedere i numeri futuri. Gioca con moderazione.
