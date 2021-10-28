# teste

Exemplo de caso:

_A equipe de marketing solicita uma listagem com os clientes que não efetuaram nenhuma compra nos últimos 90 dias._

A listagem solicitada pode ser construida combinando o cadastro de clientes com os registros de venda da empresa, e para simular esta situação, serão criadas 2 tabelas temporárias com apenas os dados necessários para esta análise

```sql
-- Cria a tabela temporária de dados cadastrais de clientes
CREATE TABLE #DIM_CLIENTES
	(
	ID_CLIENTE int
	, NOME_CLIENTE varchar(10)
	)
  
-- Alimenta cadastro de clientes
INSERT INTO #DIM_CLIENTES VALUES
	(1, 'CLIENTE A')
	, (2, 'CLIENTE B')
	, (3, 'CLIENTE C')
	, (4, 'CLIENTE D')  
  
-- Cria a tabela temporária de vendas
CREATE TABLE #FAT_VENDAS
	(
	ID_CLIENTE int
	, DATA_VENDA date
	)  

-- Alimenta registros de vendas
INSERT INTO #FAT_VENDAS VALUES
	(1, '2021-03-08')
	, (2, '2021-03-21')
	, (2, '2021-07-03')
	, (2, '2021-09-12')
	, (3, '2021-10-03')
```
Para garantir que todos os clientes serão contemplados na nossa query, fazemos a junção da tabela de clientes com a tabela de vendas, aplicando um LEFT JOIN. 
