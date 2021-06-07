---
title: Realizar amostragem de dados no SQL Server no Azure – processo de ciência de dados de equipe
description: Realize a amostragem de dados armazenados no SQL Server no Azure usando o SQL ou a linguagem de programação Python, então mova-os para o Azure Machine Learning.
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
ms.openlocfilehash: 7ac1fc5688dad3406041f36ff858e6fd27c7272f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93321870"
---
# <a name="sample-data-in-sql-server-on-azure"></a><a name="heading"></a>Dados de exemplo no SQL Server no Azure

Este artigo mostra como obter amostras de dados armazenados no SQL Server no Azure usando o SQL ou a linguagem de programação Python. Também mostra como mover dados de amostra para o Azure Machine Learning, salvando-os em um arquivo, carregando-os em um blob do Azure e, em seguida, lendo-os no Estúdio do Azure Machine Learning.

A amostragem de Python usa a biblioteca ODBC [pyodbc](https://code.google.com/p/pyodbc/) para se conectar ao SQL Server no Azure e a biblioteca [Pandas](https://pandas.pydata.org/) para fazer a amostragem.

> [!NOTE]
> O código de exemplo SQL neste documento pressupõe que os dados estejam em um SQL Server no Azure. Caso não estejam, consulte o tópico [Mover dados para o SQL Server no Azure](move-sql-server-virtual-machine.md) para obter instruções sobre como mover os dados para um SQL Server no Azure.
> 
> 

**Por que fazer amostragem dos dados?**
Se o conjunto de dados que você deseja analisar for grande, geralmente, é uma boa ideia reduzir os dados para um tamanho menor, mas representativo e mais gerenciável. A amostragem facilita a compreensão dos dados, a exploração e a engenharia de recursos. Sua função no [TDSP (Processo de Ciência de Dados de Equipe)](./index.yml) é permitir a rápida criação de protótipos de funções de processamento de dados e modelos de aprendizado de máquina.

Essa tarefa de amostragem é uma etapa do [TDSP (Processo de Ciência de Dados de Equipe)](./index.yml).

## <a name="using-sql"></a><a name="SQL"></a>Usando o SQL
Esta seção descreve vários métodos usando SQL para executar uma amostragem aleatória simples em relação aos dados no banco de dados. Escolha um método com base no tamanho e na distribuição dos seus dados.

Os dois itens a seguir mostram como usar `newid` no SQL Server para executar a amostra. O método escolhido depende do quão aleatório você deseja que o exemplo seja (pk_id no código de exemplo a seguir é considerado uma chave primária gerada automaticamente).

1. Menos rígido do exemplo aleatório

    ```sql
    select  * from <table_name> where <primary_key> in 
    (select top 10 percent <primary_key> from <table_name> order by newid())
    ```

2. Exemplo mais aleatório 

    ```sql
    SELECT * FROM <table_name>
    WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)
    ```

Tablesample pode ser usado para amostragem de dados também. Essa opção pode ser uma abordagem melhor se o tamanho dos dados for grande (supondo que os dados em diferentes páginas não estejam correlacionados) e para que a consulta seja concluída em um tempo razoável.

```sql
SELECT *
FROM <table_name> 
TABLESAMPLE (10 PERCENT)
```

> [!NOTE]
> Você pode explorar e gerar recursos por meio desses dados amostrados armazenando-os em uma nova tabela
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Conectando ao Azure Machine Learning
É possível usar diretamente as consultas de exemplo acima no módulo [Importar Dados][import-data] do Azure Machine Learning para buscar os dados dinamicamente e colocá-los em um teste do Azure Machine Learning. Uma captura de tela do uso do módulo leitor para ler os dados de amostra é mostrada aqui:

![sql leitor][1]

## <a name="using-the-python-programming-language"></a><a name="python"></a>Usando a linguagem de programação Python
Esta seção demonstra como usar a [biblioteca pyodbc](https://code.google.com/p/pyodbc/) para estabelecer uma conexão ODBC com um banco de dados do SQL Server no Python. A cadeia de conexão do banco de dados é a seguinte: (substitua ServerName, dbname, username e password por sua configuração):

```python
#Set up the SQL Azure connection
import pyodbc    
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

A biblioteca [Pandas](https://pandas.pydata.org/) no Python fornece um conjunto avançado de estruturas de dados e ferramentas de análise de dados para manipulação de dados em programação Python. O código a seguir lê uma amostra de 0,1% dos dados de uma tabela no banco de dados SQL do Azure em um dado pandas:

```python
import pandas as pd

# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select column1, column2... from <table_name> tablesample (0.1 percent)''', conn)
```

Agora você pode trabalhar com os dados de amostra no quadro de dados Pandas. 

### <a name="connecting-to-azure-machine-learning"></a><a name="python-aml"></a>Conectando ao Azure Machine Learning
Você pode usar o código de exemplo a seguir para salvar os dados convertidos em um arquivo e carregá-los para um blob do Azure. Os dados no blob podem ser lidos diretamente em um Teste do Azure Machine Learning usando o módulo [Importar Dados][import-data]. As etapas são as seguintes: 

1. Gravar o quadro de dados Pandas em um arquivo local

    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```

2. Carregar o arquivo local no blob do Azure

    ```python
    from azure.storage import BlobService
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    LOCALFILENAME= <local_file_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory

    try:

    #perform upload
    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)

    except:            
        print ("Something went wrong with uploading blob:"+BLOBNAME)
    ```

3. Leia dados do blob do Azure usando o módulo [Importar Dados][import-data] do Azure Machine Learning, como mostrado na captura de tela abaixo:

![blob de leitor][2]

## <a name="the-team-data-science-process-in-action-example"></a>Exemplo do Processo de Ciência de Dados de Equipe em ação
Para percorrer um exemplo do processo de ciência de dados de equipe usando um conjunto de dados público, confira [processo de ciência de dado de equipe em ação: usando SQL Server](sql-walkthrough.md).

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: /azure/machine-learning/studio-module-reference/import-data