---
title: Criar recursos no SQL Server usando o SQL e Python - Processo de ciência de dados de equipe
description: Gere recursos para dados armazenados em uma VM do SQL Server no Azure usando o SQL e Python – parte do Processo de ciência de dados de equipe.
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
ms.openlocfilehash: 3c20bf1c5856276c4c7ee0e37ed4ef2120d1d93d
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322044"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Criar recursos para dados no SQL Server usando o SQL e o Python
Este documento mostra como gerar recursos para os dados armazenados em uma VM do SQL Server no Azure que ajudam os algoritmos a aprender com mais eficiência com base nos dados. Você pode usar o SQL ou uma linguagem de programação como o Python para realizar esta tarefa. Ambas as abordagens são demonstradas aqui.

Essa tarefa é uma etapa no [TDSP (Processo de Ciência de Dados de Equipe)](./index.yml).

> [!NOTE]
> Para obter um exemplo prático, você poderá usar o [conjunto de dados NYC Taxi](https://www.andresmh.com/nyctaxitrips/) e consultar o IPNB intitulado [NYC Data wrangling using IPython Notebook and SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) (Realizar a disputa de dados de NYC usando o IPython Notebook e o SQL Server) para obter um passo a passo completo.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Este artigo supõe que você:

* Criou uma conta de armazenamento do Azure. Se precisar de instruções, confira [Criar uma conta de Armazenamento do Azure](../../storage/common/storage-account-create.md)
* Armazenou seus dados no SQL Server. Se não tiver feito isso, veja [Mover dados para um Banco de Dados SQL do Azure para o Azure Machine Learning](move-sql-azure.md) para obter instruções sobre como mover os dados para lá.

## <a name="feature-generation-with-sql"></a><a name="sql-featuregen"></a>Geração de recursos com o SQL
Nesta seção, descrevemos as maneiras de gerar recursos usando SQL:  

* [Geração de recursos baseada em contagem](#sql-countfeature)
* [Agrupamento da Geração de Recursos](#sql-binningfeature)
* [Propagar os recursos de uma única coluna](#sql-featurerollout)

> [!NOTE]
> Depois de gerar recursos adicionais, você pode adicioná-los como colunas à tabela existente ou criar uma nova tabela com os recursos adicionais e a chave primária, que pode ser unida com a tabela original.
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Geração de recursos baseada em contagem
Este documento demonstra duas maneiras de gerar recursos de contagem. O primeiro método usa soma condicional e o segundo usa a cláusula 'where'. Esses novos recursos podem então ser Unidos com a tabela original (usando colunas de chave primária) para ter recursos de contagem ao lado dos dados originais.

```sql
select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>
```

### <a name="binning-feature-generation"></a><a name="sql-binningfeature"></a>Agrupamento da Geração de Recursos
O exemplo a seguir mostra como gerar recursos compartimentalizados guardando (usando cinco compartimentos) uma coluna numérica que poderá ser usada como um recurso:

```sql
SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>
```


### <a name="rolling-out-the-features-from-a-single-column"></a><a name="sql-featurerollout"></a>Propagar os recursos de uma única coluna
Nesta seção, demonstraremos como propagar uma única coluna em uma tabela para gerar recursos adicionais. O exemplo presume que há uma coluna de latitude ou longitude na tabela da qual você está tentando gerar recursos.

Aqui está uma breve cartilha sobre os dados de localização de latitude/longitude (recursos de stackoverflow `https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`). Aqui estão algumas opções úteis para entender os dados de localização antes de criar recursos do campo:

* O sinal indica se estamos norte ou sul, leste ou oeste no globo.
* Um dígito de centena não zero indica longitude, não a latitude que está sendo usada.
* O dígito de dezena indica uma posição de cerca de 1.000 quilômetros. Ele fornece informações úteis sobre o continente ou o oceano em que estamos.
* O dígito e unidades (um grau decimal) oferece uma posição até 111 quilômetros (60 milhas náuticas, cerca de 69 milhas). Ele indica, aproximadamente, o estado grande ou país/região em que estamos.
* A primeira casa decimal representa até 11,1 km: ela pode distinguir a posição de uma cidade grande de uma cidade grande vizinha.
* A segunda casa decimal representa 1,1 km: ela pode separar um vila da próxima.
* A terceira casa decimal representa até 110 m: ela pode identificar um campo agrícola ou campus institucional grande.
* A quarta casa decimal representa até 11 m: ela pode identificar um lote de terreno. Ela é comparável à precisão típica de uma unidade GPS não corrigida sem interferência.
* A quinta casa decimal representa até 1,1 m: ela distingue as árvores umas das outras. Uma precisão desse nível com unidades GPS comerciais só pode ser obtida com a correção diferencial.
* A sexta casa decimal vale até 0,11 m: você pode usar esse nível para dispor estruturas em detalhes, para criar cenários, criando estradas. Ela é mais do que suficiente para acompanhar os movimentos de geleiras e rios. Essa meta pode ser obtida por meio de medidas criteriosas com GPS, como o GPS com uma diferença diferencial.

As informações de localização podem ser distinguidas separando a região, a localização e as informações da cidade. Uma vez também pode chamar um ponto de extremidade REST, como a API do Bing Maps (consulte `https://msdn.microsoft.com/library/ff701710.aspx` para obter as informações de região/distrito).

```sql
select
    <location_columnname>
    ,round(<location_columnname>,0) as l1        
    ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
    ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
    ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
    ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
    ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
    ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
from <tablename>
```

Os recursos baseados em localização podem ser usados ainda para gerar recursos adicionais de contagem, como descrito anteriormente.

> [!TIP]
> É possível inserir os registros com programação usando a linguagem de sua escolha. Talvez seja necessário inserir os dados em partes para melhorar a eficiência de gravação. [Aqui está um exemplo de como fazer isso usando pyodbc](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Outra alternativa é inserir dados no banco de dados usando o [utilitário BCP](/sql/tools/bcp-utility)
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Conectando ao Azure Machine Learning
O recurso recém-gerado pode ser adicionado como uma coluna a uma tabela existente ou armazenado em uma nova tabela e unido com a tabela original para o aprendizado de máquina. Recursos poderão ser gerados ou acessados se já foram criados, usando o módulo [Importar Dados](/azure/machine-learning/studio-module-reference/import-data) no AM do Azure conforme mostrado abaixo:

![Leitores de Azure ML](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Usando uma linguagem de programação como Python
O uso do Python para gerar recursos quando os dados estão no SQL Server é semelhante ao processamento de dados no Blob do Azure usando o Python. Para comparação, consulte os [Processar dados do Blob do Azure em seu ambiente de ciência de dados](data-blob.md). Carregue os dados do banco de dados em uma estrutura de dados de Pandas para processá-lo ainda mais. O processo de conexão ao banco de dados e o carregamento dos dados na estrutura de dados estão documentados nesta seção.

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

Agora, você pode trabalhar com o quadro de dados Pandas como abordado nos tópicos [Criar recursos para os dados de armazenamento de blobs do Azure usando o Panda](./explore-data-blob.md).