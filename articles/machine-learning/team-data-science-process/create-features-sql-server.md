---
title: Criar recursos para dados no SQL Server usando o SQL e o Python | Microsoft Docs
description: Processar dados do SQL Azure
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: 
ms.assetid: bf1f4a6c-7711-4456-beb7-35fdccd46a44
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev;fashah;garye
ms.openlocfilehash: 06c165d25361694cf660f391b3d221ad1d63e95d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Criar recursos para dados no SQL Server usando o SQL e o Python
Este documento mostra como gerar recursos para os dados armazenados em uma VM do SQL Server no Azure que ajudam os algoritmos a aprender com mais eficiência com base nos dados. Isso pode ser feito usando o SQL ou usando uma linguagem de programação como o Python, ambos demonstrados aqui.

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

Este **menu** leva você até os tópicos que descrevem como criar recursos para dados em vários ambientes. Essa tarefa é uma etapa no [TDSP (Processo de Ciência de Dados de Equipe)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

> [!NOTE]
> Para obter um exemplo prático, você poderá usar o [conjunto de dados NYC Taxi](http://www.andresmh.com/nyctaxitrips/) e consultar o IPNB intitulado [NYC Data wrangling using IPython Notebook and SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) (Realizar a disputa de dados de NYC usando o IPython Notebook e o SQL Server) para obter um passo a passo completo.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Este artigo supõe que você:

* Criou uma conta de armazenamento do Azure. Se precisar de instruções, confira [Criar uma conta de Armazenamento do Azure](../../storage/common/storage-create-storage-account.md#create-a-storage-account)
* Armazenou seus dados no SQL Server. Se não tiver feito isso, veja [Mover dados para um Banco de Dados SQL do Azure para o Azure Machine Learning](move-sql-azure.md) para obter instruções sobre como mover os dados para lá.

## <a name="sql-featuregen"></a>Geração de recursos com o SQL
Nesta seção, descrevemos as maneiras de gerar recursos usando SQL:  

1. [Geração de recursos baseada em contagem](#sql-countfeature)
2. [Agrupamento da Geração de Recursos](#sql-binningfeature)
3. [Propagar os recursos de uma única coluna](#sql-featurerollout)

> [!NOTE]
> Depois de gerar recursos adicionais, você pode adicioná-los como colunas à tabela existente ou criar uma nova tabela com os recursos adicionais e a chave primária, que pode ser unida com a tabela original.
> 
> 

### <a name="sql-countfeature"></a>Geração de recursos baseada em contagem
Este documento demonstra duas maneiras de gerar recursos de contagem. O primeiro método usa soma condicional e o segundo usa a cláusula 'where'. Eles podem então ser unidos à tabela original (usando colunas de chave primária) para que os recursos de contagem fiquem junto com os dados originais.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="sql-binningfeature"></a>Agrupamento da Geração de Recursos
O exemplo a seguir mostra como gerar recursos compartimentalizados guardando (usando 5 compartimentos) uma coluna numérica que poderá ser usada como um recurso:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Propagar os recursos de uma única coluna
Nesta seção, demonstraremos como propagar uma única coluna em uma tabela para gerar recursos adicionais. O exemplo presume que há uma coluna de latitude ou longitude na tabela da qual você está tentando gerar recursos.

Aqui está uma breve cartilha sobre os dados de localização de latitude/longitude (recursos de stackoverflow `http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`). É útil entender isso antes de destacar o campo local:

* O sinal nos informa se estamos na parte norte ou sul, leste ou oeste do globo.
* Um dígito em centenas diferente de zero informa que estamos usando longitude, não latitude!
* O dígito de dezena indica uma posição de cerca de 1.000 quilômetros. Ele nos dá informações úteis sobre em qual continente ou oceano estamos.
* O dígito e unidades (um grau decimal) oferece uma posição até 111 quilômetros (60 milhas náuticas, cerca de 69 milhas). Ele pode indicar aproximadamente em qual grande estado ou país estamos.
* A primeira casa decimal representa até 11,1 km: ela pode distinguir a posição de uma cidade grande de uma cidade grande vizinha.
* A segunda casa decimal representa 1,1 km: ela pode separar um vila da próxima.
* A terceira casa decimal representa até 110 m: ela pode identificar um campo agrícola ou campus institucional grande.
* A quarta casa decimal representa até 11 m: ela pode identificar um lote de terreno. Ela é comparável à precisão típica de uma unidade GPS não corrigida sem interferência.
* A quinta casa decimal representa até 1,1 m: ela distingue as árvores mas das outras. Uma precisão desse nível com unidades GPS comerciais só pode ser obtida com a correção diferencial.
* A sexta casa decimal representa até 0,11 m: você pode usá-la para dispor estruturas detalhadamente, projetar paisagens e criar estradas. Ela é mais do que suficiente para acompanhar os movimentos de geleiras e rios. Isso pode ser obtido coletando medidas arduamente com o GPS, tais como GPS com correção diferencial.

As informações de localização podem pode ser destacadas da maneira indicada a seguir, separando informações de região, local e cidade. Observe que também é possível chamar um ponto de extremidade REST tal como a API do Bing Mapas, disponível em `https://msdn.microsoft.com/library/ff701710.aspx` para obter as informações de região/distrito.

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

Os recursos de localização acima podem ser usados ainda para gerar recursos adicionais de contagem, como descrito anteriormente.

> [!TIP]
> É possível inserir os registros com programação usando a linguagem de sua escolha. Talvez seja necessário inserir os dados em partes para melhorar a eficiência de gravação [Confira o exemplo de como fazer isso usando pyodbc aqui](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Outra alternativa é inserir dados no banco de dados usando o [utilitário BCP](https://msdn.microsoft.com/library/ms162802.aspx)
> 
> 

### <a name="sql-aml"></a>Conectando ao Azure Machine Learning
O recurso recém-gerado pode ser adicionado como uma coluna a uma tabela existente ou armazenado em uma nova tabela e unido com a tabela original para o aprendizado de máquina. Recursos poderão ser gerados ou acessados se já foram criados, usando o módulo [Importar Dados](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) no AM do Azure conforme mostrado abaixo:

![leitores de azureml](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="python"></a>Usando uma linguagem de programação como Python
Usar o Python para gerar recursos quando os dados estão no SQL Server é semelhante ao processamento de dados no blob do Azure usando o Python conforme documentado em [Processar dados de Blob do Azure em seu ambiente de ciência de dados](data-blob.md). Os dados precisam ser carregados do banco de dados para um quadro de dados pandas, quando então poderão ser processados. Documentamos o processo de conectar-se ao banco de dados e carregar os dados em um quadro de dados nesta seção.

O seguinte formato de cadeia de conexão pode ser usado para se conectar a um banco de dados do SQL Server do Python usando pyodbc (substitua servername, dbname, nome de usuário e senha pelos seus valores específicos):

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A [Biblioteca Pandas](http://pandas.pydata.org/) no Python fornece um conjunto avançado de estruturas de dados e ferramentas de análise de dados para manipulação de dados para a programação Python. O código a seguir lê os resultados retornados de um banco de dados do SQL Server em um quadro de dados Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Agora, você pode trabalhar com o quadro de dados Pandas como abordado nos tópicos [Criar recursos para os dados de armazenamento de blobs do Azure usando o Panda](create-features-blob.md).

