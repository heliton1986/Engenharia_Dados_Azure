# Engenharia de Dados Azure

## Arquitetura do Projeto

![novo azure meu](https://github.com/heliton1986/Engenharia_Dados_Azure/assets/45739569/c98500cd-b79c-45f8-a7f9-b828ff401c65)

## SQL Server

Criei uma Query para mostrar as tabelas do schema SalesLT

![image](https://github.com/heliton1986/Engenharia_Dados_Azure/assets/45739569/70735ba9-4340-4765-b57e-36af2e505010)

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

No Synapse criei uma store procedure para ser reconhecido no pipeline dentro do For each para criar views de todas as tabelas da camada Gold no banco serverless do Synapse.

![image](https://github.com/heliton1986/Engenharia_Dados_Azure/assets/45739569/ae11f978-a4be-4c8c-bb88-18727047bcc3)



