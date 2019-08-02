---
title: 'Carregar: SDK do Python de preparação de dados'
titleSuffix: Azure Machine Learning service
description: Aprenda sobre o carregamento de dados com o SDK de preparação de dados de aprendizado de máquina do Azure. Você pode carregar diferentes tipos de dados de entrada, especificar tipos e parâmetros de arquivos de dados ou usar a funcionalidade de leitura inteligente do SDK para detectar automaticamente o tipo de arquivo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: jmartens
ms.date: 07/12/2019
ms.custom: seodec18
ms.openlocfilehash: bd60d9f9bee55ef1342fe344e8b4f2f64e313331
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360980"
---
# <a name="load-and-read-data-with-the-azure-machine-learning-data-prep-sdk"></a>Carregar e ler dados com o SDK de preparação de dados Azure Machine Learning
Neste artigo, você aprende diferentes métodos de carregamento de dados usando o SDK do Azure Machine Learning data Prep.  O SDK suporta vários recursos de ingestão de dados, incluindo:

* Carregar de muitos tipos de arquivos com inferência de parâmetros de análise (codificação, separador, cabeçalhos)
* Conversão de tipos usando inferência durante o carregamento de arquivos
* Suporte de conexão para o MS SQL Server e o Azure Data Lake Storage

> [!Important]
> Se você estiver criando uma nova solução, experimente os [conjuntos](how-to-explore-prepare-data.md) de dados do Azure Machine Learning (versão prévia) para exploração e preparação de dado. Os conjuntos de dados são a próxima versão do data Prep SDK, oferecendo funcionalidade expandida para gerenciar conjuntos de dados em soluções de ia.


A tabela a seguir mostra uma seleção de funções usadas para carregar dados de tipos de arquivo comuns.

| Tipo de arquivo | Função | Link de referência |
|-------|-------|-------|
|Any|`auto_read_file()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#auto-read-file-path--filepath--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|
|Texto|`read_lines()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep#read-lines-path--filepath--header--azureml-dataprep-api-dataflow-promoteheadersmode----promoteheadersmode-none--0---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---skip-rows--int---0--skip-mode--azureml-dataprep-api-dataflow-skipmode----skipmode-none--0---comment--str---none--include-path--bool---false--verify-exists--bool---true-----azureml-dataprep-api-dataflow-dataflow)|
|CSV|`read_csv()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep#read-csv-path--filepath--separator--str--------header--azureml-dataprep-api-dataflow-promoteheadersmode----promoteheadersmode-constantgrouped--3---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---quoting--bool---false--inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--skip-mode--azureml-dataprep-api-dataflow-skipmode----skipmode-none--0---comment--str---none--include-path--bool---false--archive-options--azureml-dataprep-api--archiveoption-archiveoptions---none--infer-column-types--bool---false--verify-exists--bool---true-----azureml-dataprep-api-dataflow-dataflow)|
|Excel|`read_excel()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep#read-excel-path--filepath--sheet-name--str---none--use-column-headers--bool---false--inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--include-path--bool---false--infer-column-types--bool---false--verify-exists--bool---true-----azureml-dataprep-api-dataflow-dataflow)|
|Largura fixa|`read_fwf()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep#read-fwf-path--filepath--offsets--typing-list-int---header--azureml-dataprep-api-dataflow-promoteheadersmode----promoteheadersmode-constantgrouped--3---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--skip-mode--azureml-dataprep-api-dataflow-skipmode----skipmode-none--0---include-path--bool---false--infer-column-types--bool---false--verify-exists--bool---true-----azureml-dataprep-api-dataflow-dataflow)|
|JSON|`read_json()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-json-path--filepath--encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---flatten-nested-arrays--bool---false--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|

## <a name="load-data-automatically"></a>Carregar dados automaticamente

Para carregar dados automaticamente sem especificar o tipo de arquivo, use a função `auto_read_file()`. O tipo de arquivo e os argumentos necessários para lê-lo são inferidos automaticamente.

```python
import azureml.dataprep as dprep

dflow = dprep.auto_read_file(path='./data/any-file.txt')
```

Essa função é útil para detectar automaticamente o tipo de arquivo, a codificação e outros argumentos de análise, tudo usando um conveniente ponto de entrada. A função também executa automaticamente as etapas a seguir, normalmente executadas ao carregar dados delimitados:

* Inferir e definir o delimitador
* Ignorar registros vazios na parte superior do arquivo
* Inferir e definir a linha de cabeçalho

Como alternativa, se você souber o tipo de arquivo antecipadamente e quiser controlar explicitamente a maneira como ele é analisado, use as funções específicas do arquivo.

## <a name="load-text-line-data"></a>Carregar dados da linha de texto

Para ler dados de texto simples em um fluxo de dados, use o `read_lines()` sem especificar parâmetros opcionais.

```python
dflow = dprep.read_lines(path='./data/text_lines.txt')
dflow.head(5)
```

||Linha|
|----|-----|
|0|Data \|\| temperatura mínima \|\| temperatura máxima|
|1|1-07-2015 \|\|  -4.1 \|\|  10.0|
|2|2-07-2015 \|\|  -0.8 \|\|  10.8|


Depois que os dados forem processados, execute o seguinte código para converter o objeto de fluxo de dados em um dataframe do Pandas.

```python
pandas_df = dflow.to_pandas_dataframe()
```

## <a name="load-csv-data"></a>Carregar dados CSV

Ao ler arquivos delimitados, o tempo de execução subjacente pode inferir os parâmetros de análise (separador, codificação, se usar cabeçalhos, etc.). Execute o seguinte código para tentar ler um arquivo CSV, especificando apenas sua localização.

```python
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
dflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|1|ALABAMA|1|101710|Condado de Hale|10171002158| |
|2|ALABAMA|1|101710|Condado de Hale|10171002162| |


Para excluir linhas durante o carregamento, defina o parâmetro `skip_rows`. Este parâmetro irá ignorar o carregamento de linhas descendentes no arquivo CSV (usando um índice baseado em um).

```python
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                       skip_rows=1)
dflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|
|0|ALABAMA|1|101710|Condado de Hale|10171002158|29|
|1|ALABAMA|1|101710|Condado de Hale|10171002162|40 |

Execute o código a seguir para exibir os tipos de dados da coluna.

```python
dflow.dtypes
```
Saída:

    stnam                     object
    fipst                     object
    leaid                     object
    leanm10                   object
    ncessch                   object
    schnam10                  object
    MAM_MTH00numvalid_1011    object
    dtype: object

Por padrão, o Azure Machine Learning Data Prep SDK não altera seu tipo de dados. A fonte de dados da qual você está lendo é um arquivo de texto, portanto, o SDK lê todos os valores como strings. Para este exemplo, colunas numéricas devem ser analisadas como números. Defina o `inference_arguments` parâmetro para `InferenceArguments.current_culture()` para inferir e converter automaticamente os tipos de colunas durante a leitura do arquivo.

```python
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1,
                          inference_arguments=dprep.InferenceArguments.current_culture())
dflow.dtypes
```
Saída:

    stnam                      object
    fipst                     float64
    leaid                     float64
    leanm10                    object
    ncessch                   float64
    schnam10                   object
    ALL_MTH00numvalid_1011    float64
    dtype: object


Várias das colunas foram corretamente detectadas como numéricas e seu tipo é definido como `float64`.

## <a name="use-excel-data"></a>Usar dados do Excel

O SDK inclui uma função `read_excel()` para carregar arquivos do Excel. Por padrão, a função carregará a primeira planilha na pasta de trabalho. Para definir uma folha específica para carregar, defina o parâmetro `sheet_name` com o valor da cadeia de caracteres do nome da folha.

```python
dflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2')
dflow.head(5)
```

| |Coluna1|Coluna2|Coluna3|Coluna4|Coluna5|Coluna6|Coluna7|Coluna8| | |
|-|-------|-------|-------|-------|-------|-------|-------|-------|-|-|
|0|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum| |
|1|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum| |
|2|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum| |
|3|Classificar|Título|Estúdio|Worldwide|Domestic / %|Coluna1|Overseas / %|Coluna2|Year^| |
|4|1|Avatar|Fox|2788|760.5|0.273|2027.5|0.727|2009^|5|

A saída mostra que os dados na segunda folha tinham três linhas vazias antes dos cabeçalhos. A função `read_excel()` contém parâmetros opcionais para pular linhas e usar cabeçalhos. Execute o seguinte código para pular as três primeiras linhas e use a quarta linha como os cabeçalhos.

```python
dflow = dprep.read_excel(path='./data/excel.xlsx',
                         sheet_name='Sheet2', use_column_headers=True, skip_rows=3)
```

||Classificar|Título|Estúdio|Worldwide|Domestic / %|Coluna1|Overseas / %|Coluna2|Year^|
|------|------|------|-----|------|-----|-------|----|-----|-----|
|0|1|Avatar|Fox|2788|760.5|0.273|2027.5|0.727|2009^|
|1|2|Titanic|Par.|2186,8|658. 7|0,301|1528,1|0. 699|1997^|

## <a name="load-fixed-width-data-files"></a>Carregar arquivos de dados de largura fixa

Para carregar arquivos de largura fixa, você especifica uma lista de deslocamentos de caracteres. A primeira coluna é sempre assumida para iniciar no deslocamento zero.

```python
dflow = dprep.read_fwf('./data/fixed_width_file.txt',
                       offsets=[7, 13, 43, 46, 52, 58, 65, 73])
dflow.head(5)
```

||010000|99999|BOGUS NORUEGA|NÃO|NO_1|ENRS|Coluna7|Coluna8|Coluna9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010003|99999|BOGUS NORUEGA|NÃO|NÃO|ENSO||||
|1|010010|99999|JAN MAYEN|NÃO|JN|ENJA|+70933|-008667|+00090|


Para evitar a detecção de cabeçalho e analisar os dados corretos, passe `PromoteHeadersMode.NONE` para o `header` parâmetro.

```python
dflow = dprep.read_fwf('./data/fixed_width_file.txt',
                       offsets=[7, 13, 43, 46, 52, 58, 65, 73],
                       header=dprep.PromoteHeadersMode.NONE)
```

||Coluna1|Coluna2|Coluna3|Coluna4|Coluna5|Coluna6|Coluna7|Coluna8|Coluna9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010000|99999|BOGUS NORUEGA|NÃO|NO_1|ENRS|Coluna7|Coluna8|Coluna9|
|1|010003|99999|BOGUS NORUEGA|NÃO|NÃO|ENSO||||


## <a name="load-sql-data"></a>Carregar dados do SQL

O SDK também pode carregar dados de uma fonte de SQL. Atualmente, somente o Microsoft SQL Server é suportado. Para ler dados de um SQL Server, crie um [`MSSQLDataSource`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.mssqldatasource?view=azure-dataprep-py) objeto que contenha os parâmetros de conexão. O parâmetro password de `MSSQLDataSource` aceita um [`Secret`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#register-secret-value--str--id--str---none-----azureml-dataprep-api-engineapi-typedefinitions-secret) objeto. Você pode construir um objeto secreto de duas maneiras:

* Registre o segredo e seu valor no mecanismo de execução.
* Crie o segredo com apenas `id` (se o valor secreto já estiver registrado no ambiente de execução) usando `dprep.create_secret("[SECRET-ID]")`.

```python
secret = dprep.register_secret(value="[SECRET-PASSWORD]", id="[SECRET-ID]")

ds = dprep.MSSQLDataSource(server_name="[SERVER-NAME]",
                           database_name="[DATABASE-NAME]",
                           user_name="[DATABASE-USERNAME]",
                           password=secret)
```

Depois de criar um objeto de fonte de dados, você pode continuar a ler os dados da saída da consulta.

```python
dflow = dprep.read_sql(ds, "SELECT top 100 * FROM [SalesLT].[Product]")
dflow.head(5)
```

| |ProductID|Nome|ProductNumber|Cor|StandardCost|ListPrice|Tamanho|Peso|ProductCategoryID|ProductModelID|SellStartDate|SellEndDate|DiscontinuedDate|ThumbNailPhoto|ThumbnailPhotoFileName|rowguid|ModifiedDate| |
|-|---------|----|-------------|-----|------------|---------|----|------|-----------------|--------------|-------------|-----------|----------------|--------------|----------------------|-------|------------|-|
|0|680|HL Road Frame - Black, 58|FR-R92B-58|Preto|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00+00:00|Nenhum|Nenhum|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|43dd68d6-14a4-461f-9069-55309d90ea7e|2008-03-11 |0:01:36.827000+00:00|
|1|706|HL Road Frame - Red, 58|FR-R92R-58|Vermelho|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00+00:00|Nenhum|Nenhum|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|9540ff17-2712-4c90-a3d1-8ce5568b2462|2008-03-11 |10:01:36.827000+00:00|
|2|707|Sport-100 Helmet, Red|HL-U509-R|Vermelho|13.0863|34.99|Nenhum|Nenhum|35|33|2005-07-01 00:00:00+00:00|Nenhum|Nenhum|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|2e1ef41a-c08a-4ff6-8ada-bde58b64a712|2008-03-11 |10:01:36.827000+00:00|


## <a name="use-azure-data-lake-storage"></a>Usar o Azure Data Lake Storage

Existem duas maneiras de o SDK adquirir o token OAuth necessário para acessar o Armazenamento do Data Lake do Azure:

* Recuperar o token de acesso de uma sessão recente do login do CLI do Azure do usuário
* Use um serviço principal (SP) e um certificado como secreto

### <a name="use-an-access-token-from-a-recent-azure-cli-session"></a>Use um token de acesso de uma sessão recente do CLI do Azure

Na sua máquina local, execute o seguinte comando.

```azurecli
az login
az account show --query tenantId
dflow = read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', tenant='microsoft.onmicrosoft.com')) head = dflow.head(5) head
```

> [!NOTE]
> Se a sua conta de usuário for um membro de mais de um locatário do Azure, será necessário especificar o locatário no formulário de nome de host do URL do AAD.

### <a name="create-a-service-principal-with-the-azure-cli"></a>Crie uma entidade de serviço com a CLI do Azure

Use a CLI do Azure para criar uma entidade de serviço e o certificado correspondente. Essa entidade de serviço específica é configurada com a função `reader`, com seu escopo reduzido para apenas a conta de armazenamento de dados do Azure Data Lake 'dpreptestfiles'.

```azurecli
az account set --subscription "Data Wrangling development"
az ad sp create-for-rbac -n "SP-ADLS-dpreptestfiles" --create-cert --role reader --scopes /subscriptions/35f16a99-532a-4a47-9e93-00305f6c40f2/resourceGroups/dpreptestfiles/providers/Microsoft.DataLakeStore/accounts/dpreptestfiles
```

Este comando emite o `appId` e o caminho para o arquivo de certificado (geralmente na pasta pessoal). O arquivo .crt contém o certificado público e a chave privada no formato PEM.

```
openssl x509 -in adls-dpreptestfiles.crt -noout -fingerprint
```

Para configurar a ACL para o sistema de arquivos do Azure Data Lake Storage, use o objectId do usuário. Neste exemplo, o principal do serviço é usado.

```azurecli
az ad sp show --id "8dd38f34-1fcb-4ff9-accd-7cd60b757174" --query objectId
```

Para configurar o acesso `Read` e `Execute` para o sistema de arquivos do Azure Data Lake Storage, configure a ACL para pastas e arquivos individualmente. Isso é devido ao fato de que o modelo de ACL HDFS subjacente não dá suporte a herança.

```azurecli
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r-x" --path /
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r--" --path /farmers-markets.csv
```

```
certThumbprint = 'C2:08:9D:9E:D1:74:FC:EB:E9:7E:63:96:37:1C:13:88:5E:B9:2C:84'
certificate = ''
with open('./data/adls-dpreptestfiles.crt', 'rt', encoding='utf-8') as crtFile:
    certificate = crtFile.read()

servicePrincipalAppId = "8dd38f34-1fcb-4ff9-accd-7cd60b757174"
```

### <a name="acquire-an-oauth-access-token"></a>Adquirir um token de acesso do OAuth

Use o `adal` pacote (`pip install adal`) para criar um contexto de autenticação no locatário da MSFT e adquirir um token de acesso do OAuth. Para ADLS, o recurso na solicitação de token deve ser para "https:\//datalake.Azure.net", que é diferente da maioria dos outros recursos do Azure.

```python
import adal
from azureml.dataprep.api.datasources import DataLakeDataSource

ctx = adal.AuthenticationContext(
    'https://login.microsoftonline.com/microsoft.onmicrosoft.com')
token = ctx.acquire_token_with_client_certificate(
    'https://datalake.azure.net/', servicePrincipalAppId, certificate, certThumbprint)
dflow = dprep.read_csv(path=DataLakeDataSource(
    path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', accessToken=token['accessToken']))
dflow.to_pandas_dataframe().head()
```

||FMID|MarketName|Site|street|city|Município|
|----|------|-----|----|----|----|----|
|0|1012063|Associação do mercado dos fazendeiros de Caledonia - Danville|https://sites.google.com/site/caledoniafarmers... ||Danville|Caledonia|
|1|1011871|Stearns Homestead Fazendeiros ' mercado|http://Stearnshomestead.com |6975 Ridge Road|Parma|Cuyahoga|
|2|1011878|100 Mile Market|https://www.pfcmarkets.com |507 Harrison St|Kalamazoo|Kalamazoo|
|3|1009364|106 Mercado de Agricultores da Rua Principal|http://thetownofsixmile.wordpress.com/ |106 S. Rua principal|Seis milhas|||
|4|1010691|10th Street Community Farmers Market|https://agrimissouri.com/... |10ª Rua e Popular|Lamar|Barton|

## <a name="next-steps"></a>Próximas etapas

* Consulte o [tutorial](tutorial-data-prep.md) Azure Machine Learning data Prep SDK para obter um exemplo de como resolver um cenário específico
