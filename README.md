# Engenharia de Dados Azure

## Arquitetura do Projeto

![novo azure meu](https://github.com/heliton1986/Engenharia_Dados_Azure/assets/45739569/c98500cd-b79c-45f8-a7f9-b828ff401c65)

## SQL Server

Criei uma Query para mostrar as tabelas do schema SalesLT

````sql
SELECT
	s.name as SchemaName,
	t.name as TableName
FROM sys.tables t
INNER JOIN sys.schemas s
ON t.schema_id = s.schema_id
WHERE s.name = 'SalesLT'
````

SchemaName |TableName
|----------|-------------------------------|
|SalesLT	  |Address                        |                               |
|SalesLT	  |Customer                       |                                          
|SalesLT	  |CustomerAddress                |                               
|SalesLT   |Product                        |                                
|SalesLT	  |ProductCategory                | 
|SalesLT	  |ProductDescription             | 
|SalesLT	  |ProductModel                   |
|SalesLT	  |ProductModelProductDescription | 
|SalesLT	  |SalesOrderDetail               | 
|SalesLT	  |SalesOrderHeader               |
--------------------------------------------

## Azure Data Factory

### Pipelines

Em Pipelines usei activies LookUP para ler a query acima para reconhecer o schema e suas tabelas. 

![image](https://github.com/heliton1986/Engenharia_Dados_Azure/assets/45739569/be2f3c10-9f64-44ce-9e36-7923fde495a6)

A partir disso pude usar um For Each para criar um loop para copiar todas as tabelas para a camada bronze do data lake em formato parquet.

![image](https://github.com/heliton1986/Engenharia_Dados_Azure/assets/45739569/928fa98c-12ca-4b9d-90e9-91c8326a676e)

![image](https://github.com/heliton1986/Engenharia_Dados_Azure/assets/45739569/fd500991-4170-4ac4-8d4e-a5dc9ec6fa4a)

Camada Bronze com diretorios de cada tabela

![image](https://github.com/heliton1986/Engenharia_Dados_Azure/assets/45739569/ae6d878e-55e6-42b8-ae65-d6e8b37c8b66)

Criei 3 notebooks, estes que estão acima no repositório
 - storage_mount: para o azure databricks se conectar com meu data lake.
 - bronze to silver: para fazer transformações das colunas de datas de todas tabelas que estavam datetime para date da camada bronze para silver em formato delta.
 - silver to gold: para fazer transformações renomeando os nomes das colunas inserindo "_" entre palavras da camada silver para gold em formato delta.

![image](https://github.com/heliton1986/Engenharia_Dados_Azure/assets/45739569/31bc5872-1bae-48e0-b3c4-101a7b8bb789)

## Synapse

Usando Get Metadata para reconhecer os itens filhos, da camada Gold no data lake.

![image](https://github.com/heliton1986/Engenharia_Dados_Azure/assets/45739569/1ccc7361-ffb5-4ac8-978c-08f2edef362e)

No Synapse criei uma store procedure para ser reconhecido no pipeline dentro do For each para criar views de todas as tabelas da camada Gold no banco serverless gold_db do Synapse.

![image](https://github.com/heliton1986/Engenharia_Dados_Azure/assets/45739569/ae11f978-a4be-4c8c-bb88-18727047bcc3)

Visualização das views criadas a partir do pipeline no Synapse.

![image](https://github.com/heliton1986/Engenharia_Dados_Azure/assets/45739569/b8692ac6-9008-4fb2-aa96-c605659f598d)

## Power BI

No Power BI, em "Obter dados", escolhi Azure Synapse Analytics SQL.

![pbi00](https://github.com/heliton1986/Engenharia_Dados_Azure/assets/45739569/217d4a87-fc1f-4c7e-b5a8-2c26c39a5d8c)

Para conectar o Synapse com Power BI, foi preciso ir no recurso do Synapse no Azure e buscar o endpoint do SQL Serverless.

![conexao synapse pbi](https://github.com/heliton1986/Engenharia_Dados_Azure/assets/45739569/fb96eea8-ac40-443b-bf68-cc43c7a585c0)

Dessa forma conectando ao POwer BI, todas as tabelas do banco escolhido aparecem no Power BI para carregamento ou transformação.
Como os dados já foram transformados, apenas carreguei.

![pbi01](https://github.com/heliton1986/Engenharia_Dados_Azure/assets/45739569/3edf83a0-17e2-4534-b20c-28461972c111)
![pbi02](https://github.com/heliton1986/Engenharia_Dados_Azure/assets/45739569/eef22873-f1f1-46a6-a643-4080a8038905)

Após carregados fiz o relacionamento entre as tabelas.

![pbi03](https://github.com/heliton1986/Engenharia_Dados_Azure/assets/45739569/99d3d153-08a9-4e80-8e48-9bc83e4d784e)

## Azure Active Directory

Para poder dar acesso à outros usuários, usei o Azure AD para criar um grupo e add um membro.

![AD1](https://github.com/heliton1986/Engenharia_Dados_Azure/assets/45739569/ce574901-d60a-4e10-b055-405b0b7bcf41)
![AD3](https://github.com/heliton1986/Engenharia_Dados_Azure/assets/45739569/e83286e5-f050-4036-81ff-361e34eb97d2)
![AD4](https://github.com/heliton1986/Engenharia_Dados_Azure/assets/45739569/78b70dcf-ca09-4ea1-84c8-b868b0354b8a)

No grupo de recursos em IAM adicione uma atribuição de função.

![gruporecurso1](https://github.com/heliton1986/Engenharia_Dados_Azure/assets/45739569/d3035811-23ec-4b58-b290-54bfdbd3f05e)
![gruporecurso2](https://github.com/heliton1986/Engenharia_Dados_Azure/assets/45739569/37ba5c84-5476-4f96-92c9-9517fcecd192)

Funções de administrador previlegiadas - contribuidor.

![gruporecurso3](https://github.com/heliton1986/Engenharia_Dados_Azure/assets/45739569/27d05f58-6073-4ad1-812a-d7fd2a034709)
![gruporecurso4](https://github.com/heliton1986/Engenharia_Dados_Azure/assets/45739569/ec27c78a-1e9e-43a0-a276-6acff4ac0ce9)

Clique em membros e adicione o grupo eng-dados-grupo.

![gruporecurso6](https://github.com/heliton1986/Engenharia_Dados_Azure/assets/45739569/5d150a19-105c-4fc6-9b1e-110995fd9db5)
![gruporecurso7](https://github.com/heliton1986/Engenharia_Dados_Azure/assets/45739569/a439221b-52a1-4e13-91db-e1c0336edcab)

Dessa forma o outro usuário terá acesso também ao grupo de recursos para fazer sua análises e relatórios.
