# Análise do ITBI em São Paulo

## Estrutura

```
dados/brutos/     planilhas originais (fora do Git)
dados/tratados/   arquivos .parquet gerados pelo código
notebooks/        01_coleta, 02_limpeza, 03_carga, 04_eda
sql/              01_schema.sql, 02_analises.sql
dashboard/        arquivo .pbix e prints
```

## Como rodar

```
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
```
