---
title: Explorar dados na máquina virtual do SQL Server – Processo de Ciência de Dados da Equipe
description: Explorar dados e gerar recursos em uma máquina virtual do SQL Server no Azure
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/23/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7b3b4e0886f561cc66e2c02e4ea354c86b34453c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61044453"
---
# <a name="heading"></a>Processar dados na Máquina Virtual do SQL Server no Azure
Este documento aborda como explorar dados e gerar recursos dados armazenados em uma VM do SQL Server no Azure. Isso pode ser feito por disputa de dados usando SQL ou usando uma linguagem de programação como Python.

> [!NOTE]
> As instruções SQL de exemplo neste documento pressupõem que os dados estão no SQL Server. Se não estiverem, consulte o mapa do processo de ciência de dados de nuvem para aprender a mover seus dados para o SQL Server.
> 
> 

## <a name="SQL"></a>Usando o SQL
Descrevemos as seguintes tarefas wrangling de dados nesta seção usando SQL:

1. [Exploração de Dados](#sql-dataexploration)
2. [Geração de Recursos](#sql-featuregen)

### <a name="sql-dataexploration"></a>Exploração de Dados
Aqui estão alguns scripts de SQL de exemplo que podem ser usados para explorar armazenamentos de dados no SQL Server.

> [!NOTE]
> Para ver um exemplo prático, é possível usar o [conjunto de dados de Táxis de NYC](https://www.andresmh.com/nyctaxitrips/) e consultar o IPNB intitulado[NYC Data wrangling using IPython Notebook and SQL Server (Realizar o wrangling de dados de NYC usando o IPython Notebook e SQL Server)](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) para obter um passo a passo de ponta a ponta.
> 
> 

1. Obter a contagem de observações por dia
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Obter os níveis em uma coluna categórica
   
    `select  distinct <column_name> from <databasename>`
3. Obter o número de níveis na combinação de duas colunas categóricas 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Obter a distribuição de colunas numéricas
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="sql-featuregen"></a>Geração de Recursos
Nesta seção, descrevemos as maneiras de gerar recursos usando SQL:  

1. [Geração de recursos baseada em contagem](#sql-countfeature)
2. [Agrupamento da Geração de Recursos](#sql-binningfeature)
3. [Propagar os recursos de uma única coluna](#sql-featurerollout)

> [!NOTE]
> Depois de gerar recursos adicionais, você pode adicioná-los como colunas à tabela existente ou criar uma nova tabela com os recursos adicionais e a chave primária, que pode ser unida com a tabela original. 
> 
> 

### <a name="sql-countfeature"></a>Geração de recursos baseada em contagem
Os exemplos a seguir demonstram duas maneiras de gerar recursos de contagem. O primeiro método usa a soma condicional e o segundo usa a cláusula 'where'. Eles podem então ser unidos à tabela original (usando colunas de chave primária) para que os recursos de contagem fiquem junto com os dados originais.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

### <a name="sql-binningfeature"></a>Agrupamento da Geração de Recursos
O exemplo a seguir mostra como gerar recursos compartimentalizados guardando (usando cinco compartimentos) uma coluna numérica que poderá ser usada como um recurso:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Propagar os recursos de uma única coluna
Nesta seção, demonstraremos como propagar uma única coluna em uma tabela para gerar recursos adicionais. O exemplo presume que há uma coluna de latitude ou longitude na tabela da qual você está tentando gerar recursos.

Apresentamos aqui uma breve cartilha sobre dados de localização de latitude/longitude (extraídos de [How to measure the accuracy of latitude and longitude?](https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)[Como medir a precisão de latitude e longitude?] do StackOverflow). É útil entender isso antes de destacar o campo local:

* O sinal nos informa se estamos na parte norte ou sul, leste ou oeste do globo.
* Um dígito em centenas diferente de zero informa que estamos usando longitude, não latitude!
* O dígito de dezena indica uma posição de cerca de 1.000 quilômetros. Ele nos dá informações úteis sobre em qual continente ou oceano estamos.
* O dígito e unidades (um grau decimal) oferece uma posição até 111 quilômetros (60 milhas náuticas, cerca de 69 milhas). Ele pode indicar aproximadamente em qual grande estado ou país estamos.
* A primeira casa decimal representa até 11,1 km: ela pode distinguir a posição de uma cidade grande de uma cidade grande vizinha.
* A segunda casa decimal representa 1,1 km: ela pode separar um vila da próxima.
* A terceira casa decimal representa até 110 m: ela pode identificar um campo agrícola ou campus institucional grande.
* A quarta casa decimal representa até 11 m: ela pode identificar um lote de terreno. Ela é comparável à precisão típica de uma unidade GPS não corrigida sem interferência.
* A quinta casa decimal representa até 1,1 m: ela distingue as árvores umas das outras. Uma precisão desse nível com unidades GPS comerciais só pode ser obtida com a correção diferencial.
* A sexta casa decimal representa até 0,11 m: você pode usá-la para dispor estruturas detalhadamente, projetar paisagens e criar estradas. Ela é mais do que suficiente para acompanhar os movimentos de geleiras e rios. Isso pode ser obtido coletando medidas arduamente com o GPS, tais como GPS com correção diferencial.

As informações de local podem ser destacadas da maneira indicada a seguir, separando as informações de região, local e cidade. Observe que também é possível chamar um ponto de extremidade REST tal como a API do Bing Mapas disponível em [Find a Location by Point](https://msdn.microsoft.com/library/ff701710.aspx) (Encontrar um local por ponto) para obter as informações de região/distrito.

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

Os recursos baseados em localização podem ser usados ainda para gerar recursos adicionais de contagem, como descrito anteriormente. 

> [!TIP]
> É possível inserir os registros com programação usando a linguagem de sua escolha. Talvez seja necessário inserir os dados em partes para melhorar a eficiência de gravação (para obter um exemplo de como fazer isso usando o pyodbc, veja [A HelloWorld sample to access SQLServer with python](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)[Uma amostra do HelloWorld para acessar o SQL Server com o Python]). Outra alternativa é inserir dados no banco de dados usando o [utilitário BCP](https://msdn.microsoft.com/library/ms162802.aspx).
> 
> 

### <a name="sql-aml"></a>Conectando ao Azure Machine Learning
O recurso recém-gerado pode ser adicionado como uma coluna a uma tabela existente ou armazenado em uma nova tabela e unido com a tabela original para o aprendizado de máquina. Os recursos podem ser gerados ou acessados, se já foram criados, usando o módulo [Importar Dados][import-data] no Azure Machine Learning, conforme mostrado abaixo:

![leitores de azureml][1] 

## <a name="python"></a>Usando uma linguagem de programação como Python
Usar o Python para explorar dados e gerar recursos quando os dados estão no SQL Server é semelhante a processar dados no blob do Azure usando o Python, conforme documentado em [Processar dados de Blobs do Azure no ambiente de ciência de dados](data-blob.md). Os dados precisam ser carregados do banco de dados para um quadro de dados pandas, quando então poderão ser processados. Documentamos o processo de conectar-se ao banco de dados e carregar os dados em um quadro de dados nesta seção.

O seguinte formato de cadeia de conexão pode ser usado para se conectar a um banco de dados do SQL Server do Python usando pyodbc (substitua servername, dbname, username e password pelos seus valores específicos):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A [Biblioteca Pandas](https://pandas.pydata.org/) no Python fornece um conjunto avançado de estruturas de dados e ferramentas de análise de dados para manipulação de dados para a programação Python. O código a seguir lê os resultados retornados de um banco de dados do SQL Server em um quadro de dados Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Agora, você pode trabalhar com o quadro de dados do Pandas, como abordamos no artigo [Processar dados do Blob do Azure no ambiente de ciência de dados](data-blob.md).

## <a name="azure-data-science-in-action-example"></a>Exemplo da Ciência de Dados do Azure em Ação
Para obter um exemplo passo a passo e ponta a ponta do Processo de Ciência de Dados do Azure usando um conjunto de dados público, consulte [Processo de Ciência de Dados do Azure em Ação](sql-walkthrough.md).

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

