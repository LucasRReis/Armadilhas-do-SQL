## Cálculo de idade

Para esta análise serão utilizadas duas variáveis que carregarão a data de nascimento e a data na qual se deseja saber a idade do indivíduo, que trataremos aqui como data de referência.
```sql
DECLARE 
  @DATA_NASCIMENTO DATE = '1990-12-11'
  , @DATA_REFERENCIA DATE = '2021-12-09'
```

##### Caso 1:
Este sem dúvida é o erro mais praticado no cálculo de idades, pois se trata da tradução direta do que se deseja obter: a diferença em anos, entre uma data e outra.

O problema neste caso é que apenas o ano das variáveis de data é considerado, uma vez que a função DATEDIFF despreza as frações de data menores que o argumento aplicado. Quando a data de referência for anterior à data de aniversário do indivíduo naquele ano, esta função irá calcular a idade de forma incorreta, já que o dia e o mês foram desprezados. 


```sql
-- Exemplo de cálculo incorreto de idade (Idade correta = 0)
DECLARE 
  @DATA_NASCIMENTO date = '2020-12-31'
  , @DATA_REFERENCIA date = '2021-01-01'

SELECT 
  DATEDIFF(YEAR, @DATA_NASCIMENTO, @DATA_REFERENCIA) AS [IDADE INCORRETA]

```
```
IDADE INCORRETA
---------------
1
```

##### Caso 2:
Tentando contornar a limitação apresentada no caso 1, utilizando o argumento de fração de data em dias ao invés de anos, e dividindo o resultado pela quantidade regular de dias do ano, conseguimos solucionar parcialmente o problema, mas a ocorrência de anos bissextos irão invalidar esta opção. 

```sql
-- Exemplo de cálculo incorreto de idade (Idade correta = 20)
DECLARE 
  @DATA_NASCIMENTO date = '2000-01-02'
  , @DATA_REFERENCIA date = '2021-01-01'
  
SELECT  
  DATEDIFF(DAY, @DATA_NASCIMENTO, @DATA_REFERENCIA) / 365 AS [IDADE INCORRETA]
```
```
IDADE INCORRETA
---------------
21
```
##### Caso 3:
Neste último caso, se considera o tratamento aplicado no caso 2, ajustando então a quantidade de dias do ano para 365 + 1/4 de dia para compensar a ocorrência dos anos bissextos e se utiliza a função FLOOR para retornar apenas a parte inteira dessa divisão.

Esta é a proposta de solução mais eficiente apresentada mas ainda assim não é totalmente eficaz, uma vez que a média de 365,25 dias por ano não é absoluta, variando entre 365 e 366 dias quando o intervalo as datas é de poucos anos, podendo ou não compreender anos bissextos, o que poderá causar um cálculo de idade incorreto.
```sql
-- Exemplo de cálculo incorreto de idade (Idade correta = 1)
DECLARE 
  @DATA_NASCIMENTO DATE = '2019-01-01'
  , @DATA_REFERENCIA DATE = '2020-01-01'

SELECT 
  FLOOR(DATEDIFF(DAY, @DATA_NASCIMENTO, @DATA_REFERENCIA) / 365.25) AS [IDADE INCORRETA]
```
```
IDADE INCORRETA
---------------
0
```

#### Solução:

O êxito do cálculo de idade pode ser alcançado utilizando o código apresentado no caso 1, combinado com uma validação condicional que se encarregará de tratar o resultado a partir da avaliação das frações de data desprezadas pela função DATEDIFF.

Foi utilizada a função IIF, que nada mais é do que a simplificação de uma expressão CASE. Nela comparamos as dadas de nascimento e referência formatadas sem o ano e caso a data de referência seja menor que a data de nascimento, se subtrai 1 ano do valor calculado na função DATEDIFF, pois o último aniversário não aconteceu.
 

```sql
-- Exemplo de cálculo correto de idade
DECLARE 
  @DATA_NASCIMENTO DATE = '2019-01-02'
  , @DATA_REFERENCIA DATE = '2021-01-01'
  
SELECT 
  DATEDIFF(YEAR, @DATA_NASCIMENTO, @DATA_REFERENCIA) + IIF(FORMAT(@DATA_REFERENCIA, 'dd/MM') < FORMAT(@DATA_NASCIMENTO, 'dd/MM'), -1, 0) AS [IDADE CORRETA]
```
```
IDADE CORRETA
-------------
1
```
Existem outras formas de garantir a eficiência do cálculo, como identificando quais e quantos anos bissextos ocorreram dentro do intervalo de tempo, mas são soluções que tornam o código mais extenso e menos performático.
