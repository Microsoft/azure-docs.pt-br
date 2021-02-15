---
title: Explorar dados na máquina virtual do SQL Server – Processo de Ciência de Dados da Equipe
description: Como explorar dados armazenados em uma VM do SQL Server no Azure usando SQL ou uma linguagem de programação como Python.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 33b55afb7796b197f7130ec9288abb01cc115651
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86085643"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Explorar dados na Máquina Virtual do SQL Server no Azure

Este artigo aborda como explorar dados armazenados em uma VM do SQL Server no Azure. Use SQL ou Python para examinar os dados.

Esta tarefa é uma etapa no [Processo de Ciência de Dados da Equipe](overview.md).

> [!NOTE]
> As instruções SQL de exemplo neste documento pressupõem que os dados estão no SQL Server. Se não estiverem, consulte o mapa do processo de ciência de dados de nuvem para aprender a mover seus dados para o SQL Server.
> 
> 

## <a name="explore-sql-data-with-sql-scripts"></a><a name="sql-dataexploration"></a>Explorar dados de SQL com scripts SQL
Aqui estão alguns scripts de SQL de exemplo que podem ser usados para explorar armazenamentos de dados no SQL Server.

1. Obter a contagem de observações por dia
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Obter os níveis em uma coluna categórica 
   
    `select  distinct <column_name> from <databasename>`
3. Obter o número de níveis na combinação de duas colunas categóricas 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Obter a distribuição de colunas numéricas
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> Para ver um exemplo prático, é possível usar o [conjunto de dados de Táxis de NYC](https://www.andresmh.com/nyctaxitrips/) e consultar o IPNB intitulado[NYC Data wrangling using IPython Notebook and SQL Server (Realizar o wrangling de dados de NYC usando o IPython Notebook e SQL Server)](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) para obter um passo a passo de ponta a ponta.
> 
> 

## <a name="explore-sql-data-with-python"></a><a name="python"></a>Explorar dados de SQL com o Python
Usar o Python para explorar dados e gerar recursos quando os dados estão em SQL Server é semelhante a processar dados no blob do Azure usando o Python, conforme documentado em [processar dados de blob do Azure em seu ambiente de ciência de dados](data-blob.md). Carregue os dados do banco de dado em um dataframe do pandas e, em seguida, pode ser processado mais detalhadamente. Documentamos o processo de conectar-se ao banco de dados e carregar os dados em um DataFrame nesta seção.

O seguinte formato de cadeia de conexão pode ser usado para se conectar a um banco de dados do SQL Server do Python usando pyodbc (substitua servername, dbname, username e password pelos seus valores específicos):

```python
#Set up the SQL Azure connection
import pyodbc    
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

A [biblioteca pandas](https://pandas.pydata.org/) no Python fornece um rico conjunto de estruturas de dados e ferramentas de análise de dados para a manipulação de dados para a programação em Python. O código a seguir lê os resultados retornados de um banco de dados do SQL Server em um quadro de dados Pandas:

```python
# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)
```

Agora, você pode trabalhar com o DataFrame Pandas, como abordamos no tópico [Processar dados do Blob do Azure em seu ambiente de ciência de dados](data-blob.md).

## <a name="the-team-data-science-process-in-action-example"></a>Exemplo do Processo em ação de Ciência de Dados de Equipe
Para obter um exemplo passo a passo completo do Processo do Cortana Analytics usando um conjunto de dados público, confira [O Processo de Ciência de Dados de Equipe em ação: usando o SQL Server](sql-walkthrough.md).

