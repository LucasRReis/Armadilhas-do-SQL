## Cálculo de idade

Para esta análise serão utilizadas duas variáveis que carregarão a data de nascimento e a data na qual se deseja saber a idade do individuo, que trataremos aqui como data de referência.
```sql
DECLARE 
  @DATA_NASCIMENTO DATE = '1990-12-11'
  , @DATA_REFERENCIA DATE = '2021-12-09'
```

##### Caso 1:
```sql
SELECT 
  DATEDIFF(YEAR, @DATA_NASCIMENTO, @DATA_REFERENCIA)
```
Este sem dúvida é o erro mais praticado no cálculo de idades, pois se trata da tradução direta do que se deseja obter: a diferença em anos, entre uma data e outra.
O problema neste caso é que apenas o ano das variáveis de data é considerado, uma vez que a função DATEDIFF despreza as frações de data menores que o argumento aplicado.

```sql
DECLARE 
  @DATA_NASCIMENTO DATE = '2020-12-31'
  , @DATA_REFERENCIA DATE = '2021-01-01'

SELECT 
  DATEDIFF(YEAR, @DATA_NASCIMENTO, @DATA_REFERENCIA) AS [IDADE ERRADA]
```

```sql
DECLARE 
  @DATA_NASCIMENTO DATE = '1990-12-11'
  , @DATA_REFERENCIA DATE = '2021-12-09'

SELECT 
	DATEDIFF(YEAR, @DATA_NASCIMENTO, @DATA_REFERENCIA) AS [IDADE ERRADA]
	, DATEDIFF(MONTH, @DATA_NASCIMENTO, @DATA_REFERENCIA) / 12 AS [IDADE ERRADA]
	, DATEDIFF(DAY, @DATA_NASCIMENTO, @DATA_REFERENCIA) / 365 AS [IDADE ERRADA]
	, FLOOR(DATEDIFF(DAY, @DATA_NASCIMENTO, @DATA_REFERENCIA) / 365.25) AS [IDADE ERRADA] --('2020-02-01')

	, DATEDIFF(YEAR, @DATA_NASCIMENTO, @DATA_REFERENCIA) + IIF(FORMAT(@DATA_REFERENCIA, 'dd/MM') < FORMAT(@DATA_NASCIMENTO, 'dd/MM'), -1, 0) AS [IDADE CORRETA]
```
