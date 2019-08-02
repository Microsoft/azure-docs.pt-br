---
title: 'Tutorial do Python: Chamar os Serviços Cognitivos em um pipeline de indexação – Azure Search'
description: Percorra um exemplo de extração de dados, linguagem natural e processamento de imagem por IA no Azure Search usando um notebook Jupyter Python. Os dados extraídos são indexados e podem ser acessados facilmente pela consulta.
manager: cgronlun
author: LisaLeib
services: search
ms.service: search
ms.devlang: python
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: v-lilei
ms.openlocfilehash: b7f1baa473ca28db696835a7b0895f1603c74770
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359398"
---
# <a name="python-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>Tutorial do Python: Chamar APIs de Serviços Cognitivos em um pipeline de indexação do Azure Search

Neste tutorial, você aprenderá a mecânica de programação enriquecimento de dados no Azure Search usando *habilidades cognitivas*. As habilidades são apoiadas pelos recursos de processamento de linguagem natural (PLN) e análise de imagem nos Serviços Cognitivos. Por meio da composição do conjunto de qualificações e configuração, você pode extrair texto e representações de texto de um arquivo de imagem ou documento digitalizado. Você também pode detectar a linguagem, entidades, frases-chave e muito mais. O resultado é um conteúdo mais avançado em um índice do Azure Search, criado com aprimoramentos por IA em um pipeline de indexação. 

Neste tutorial, você usará Python para realizar as seguintes tarefas:

> [!div class="checklist"]
> * Criar um pipeline de indexação que enriquece dados de exemplo em rota para um índice
> * Aplique as qualificações internas: o reconhecimento de entidade, a detecção de idioma, manipulação de texto e extração de frase-chave
> * Saiba como encadear habilidades mapeando entradas para saídas em um conjunto de qualificações
> * Executar solicitações e analisar resultados
> * Redefinir o índice e indexadores para desenvolvimento futuro

Saída é um índice de pesquisa de texto completo no Azure Search. Você pode aprimorar o índice com outros recursos padrão, como [sinônimos](search-synonyms.md), [perfis de pontuação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analisadores](search-analyzers.md), e [filtros](search-filters.md). 

Este tutorial é executado no serviço gratuito, mas o número de transações gratuitos é limitado a 20 documentos por dia. Se você quiser executar este tutorial mais de uma vez no mesmo dia, use um conjunto de arquivos menor para que você possa encaixar mais execuções.

> [!NOTE]
> À medida que expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, você precisará [anexar um recurso de Serviços Cognitivos faturável](cognitive-search-attach-cognitive-services.md). As cobranças são geradas ao chamar APIs nos Serviços Cognitivos e para a extração de imagem como parte do estágio de decodificação de documentos no Azure Search. Não há encargos para extração de texto em documentos.
>
> A execução de habilidades integradas é cobrada nos [preços pagos conforme o uso dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes. O preço de extração de imagem é descrito na [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Os serviços, as ferramentas e os dados a seguir são usados neste tutorial. 

+ [Criar uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) para armazenar os dados de exemplo. Verifique se que a conta de armazenamento está na mesma região do Azure Search.

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), que fornece o Python 3.x e os Notebooks Jupyter.

+ [Dados de exemplo](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) consistem em um conjunto de pequenos arquivos de tipos diferentes. 

+ [Crie um serviço Azure Search](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual. Você pode usar um serviço gratuito para este tutorial.

## <a name="get-a-key-and-url"></a>Obter uma chave e uma URL

Para interagir com o serviço do Azure Search, será necessária a URL de serviço e uma chave de acesso. Um serviço de pesquisa é criado com ambos, portanto, se você adicionou o Azure Search à sua assinatura, siga estas etapas para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha a URL. Um ponto de extremidade de exemplo pode parecer com `https://mydemo.search.windows.net`.

1. Em **Configurações** > **Chaves**, obtenha uma chave de administração para adquirir todos os direitos sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso seja necessário sobrepor uma. É possível usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

![Obter um ponto de extremidade HTTP e uma chave de acesso](media/search-get-started-postman/get-url-key.png "Obter um ponto de extremidade HTTP e uma chave de acesso")

Todas as solicitações requerem uma chave de api em cada pedido enviado ao serviço. Uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que a manipula.

## <a name="prepare-sample-data"></a>Preparar os dados de exemplo

O pipeline de enriquecimento extrai de fontes de dados do Azure. Fonte de dados deve originar-se de um tipo de fonte de dados com suporte de um [indexador do Azure Search](search-indexer-overview.md). O Armazenamento de Tabelas do Azure não tem suporte para a pesquisa cognitiva. Para este exercício, usamos o armazenamento de blob para apresentar múltiplos tipos de conteúdo.

1. [Entre no portal do Azure](https://portal.azure.com), navegue até sua conta de Armazenamento do Azure, clique em **Blobs** e, em seguida, clique em **+ Contêiner**.

1. [Crie um contêiner de Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) para conter dados de exemplo. Você pode definir o Nível de Acesso Público para qualquer um de seus valores válidos.

1. Após o contêiner ser criado, abra-o e selecione **Carregar** na barra de comandos para carregar os arquivos de exemplo que você baixou na etapa anterior.

   ![Arquivos de origem no armazenamento de blobs do Azure](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Depois que os arquivos de exemplo são carregados, obter o nome do contêiner e uma cadeia de caracteres de conexão para o armazenamento de Blob. Você pode fazer isso navegando até você conta de armazenamento no portal do Azure. Clique em **Chaves de acesso**e copie o campo **Cadeia de conexão**.

A cadeia de conexão terá este formato: `DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT-NAME>;AccountKey=<YOUR-STORAGE-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

Mantenha a cadeia de conexão em um lugar de fácil acesso. Você precisará dela em uma etapa posterior.

Existem outras maneiras de especificar a cadeia de caracteres de conexão, como fornecer uma assinatura de acesso compartilhado. Para saber mais sobre as credenciais de fonte de dados, consulte [indexação armazenamento de BLOBs do Azure](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="create-a-jupyter-notebook"></a>Criar um notebook Jupyter

> [!Note]
> Este artigo mostra como criar uma fonte de dados, um índice, um indexador e um conjunto de habilidades usando uma série de scripts Python. Para baixar o exemplo de notebook completo, vá para o [repositório Azure-Search-python-samples](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment-Jupyter-Notebook).

Use o Anaconda Navigator para iniciar o Jupyter Notebook e criar um novo notebook Python 3.

## <a name="connect-to-azure-search"></a>Conectar-se ao Azure Search

No notebook, execute este script a fim de carregar as bibliotecas usadas para trabalhar com JSON e formular solicitações HTTP.

```python
import json
import requests
from pprint import pprint
```

Em seguida, defina os nomes da fonte de dados, do índice, do indexador e do conjunto de habilidades. Execute este script a fim de configurar os nomes para este tutorial.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

> [!Tip]
> Em um serviço gratuito, você tem a limitação de três índices, indexadores e fontes de dados. Este tutorial cria um de cada. Verifique se há espaço para criar novos objetos antes de prosseguir.

No script a seguir, substitua os espaços reservados para seu serviço de pesquisa (YOUR-SEARCH-SERVICE-NAME) e para a chave da API de administração (YOUR-ADMIN-API-KEY) e execute-o para configurar o ponto de extremidade do serviço de pesquisa.

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2019-05-06'
}
```

## <a name="create-a-data-source"></a>Criar uma fonte de dados

Agora que os serviços e os arquivos de origem estão preparados, comece montando os componentes do pipeline de indexação. Comece com um objeto de fonte de dados que indica ao Azure Search como recuperar dados de origem externa.

No script a seguir, substitua o espaço reservado YOUR-BLOB-RESOURCE-CONNECTION-STRING pela cadeia de conexão do blob que você criou na etapa anterior. Em seguida, execute o script para criar uma fonte de dados chamada `cogsrch-py-datasource`.

```python
# Create a data source
datasourceConnectionString = "<YOUR-BLOB-RESOURCE-CONNECTION-STRING>"
datasource_payload = {
    "name": datasource_name,
    "description": "Demo files to demonstrate cognitive search capabilities.",
    "type": "azureblob",
    "credentials": {
        "connectionString": datasourceConnectionString
    },
    "container": {
        "name": "basic-demo-data-pr"
    }
}
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

A solicitação deve retornar um código de status 201 que confirma o êxito.

No portal do Azure, na página do painel do serviço de pesquisa, verifique se cogsrch-py-datasource aparece na lista **Fontes de dados**. Clique em **Atualizar** para atualizar a página.

![Fontes de dados lado a lado no portal do](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "lado a lado no portal de fontes de dados")

## <a name="create-a-skillset"></a>Criar um conjunto de habilidades

Nesta etapa, você deve definir um conjunto de etapas de aprimoramento para aplicar aos dados. Chamar cada etapa enriquecimento um *habilidade*e o conjunto de etapas de enriquecimento um *conjunto de qualificações*. Este tutorial usa [habilidades cognitivas internas](cognitive-search-predefined-skills.md) para o conjunto de habilidades:

+ [A detecção de idioma](cognitive-search-skill-language-detection.md) para identificar o idioma do conteúdo.

+ [Divisão de texto](cognitive-search-skill-textsplit.md) para dividir o conteúdo grande em partes menores antes de chamar a habilidade de extração de frase-chave. Extração de frase-chave aceita entradas de 50.000 caracteres ou menos. Alguns dos arquivos de exemplo precisam dividir para se ajustar dentro desse limite.

+ [Reconhecimento de entidade](cognitive-search-skill-entity-recognition.md) para extrair os nomes das organizações de conteúdo no contêiner de blobs.

+ [Extração de frase chave](cognitive-search-skill-keyphrases.md) para destacar as principais frases-chave. 

### <a name="python-script"></a>Script Python
Execute o script a seguir para criar um conjunto de habilidades chamado `cogsrch-py-skillset`.

```python
# Create a skillset
skillset_payload = {
    "name": skillset_name,
    "description":
    "Extract entities, detect language and extract key-phrases",
    "skills":
    [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": ["Organization"],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text", "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "organizations", "targetName": "organizations"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "inputs": [
                {
                    "name": "text", "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "languageCode"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "textSplitMode": "pages",
            "maximumPageLength": 4000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/pages/*",
            "inputs": [
                {
                    "name": "text", "source": "/document/pages/*"
                },
                {
                    "name": "languageCode", "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyPhrases"
                }
            ]
        }
    ]
}

r = requests.put(endpoint + "/skillsets/" + skillset_name,
                 data=json.dumps(skillset_payload), headers=headers, params=params)
print(r.status_code)
```

A solicitação deve retornar um código de status 201 que confirma o êxito.

A habilidade de extração de frase-chave é aplicada a cada página. A definição do contexto como `"document/pages/*"` faz com que esse aprimorador seja executado para cada membro da matriz de documento/páginas (para cada página do documento).

Cada uma delas executa no conteúdo do documento. Durante o processamento, o Azure Search danifique cada documento para ler o conteúdo de diferentes formatos de arquivo. O texto encontrado no arquivo de origem é colocado em um campo `content`, um para cada documento. Portanto, defina a entrada como `"/document/content"`.

Uma representação gráfica do conjunto de qualificações é mostrada abaixo.

![Entender um conjunto de qualificações](media/cognitive-search-tutorial-blob/skillset.png "entender um conjunto de qualificações")

As saídas podem ser mapeadas para um índice, usadas como entrada para uma habilidade posterior, ou ambos, como é o caso com o código de idioma. No índice, um código de idioma é útil para filtragem. Como uma entrada, o código de idioma é usado por habilidades de análise de texto para informar as regras linguísticas em torno de quebra de palavras.

Para obter mais informações sobre conceitos básicos do conjunto de qualificações, consulte [como definir um conjunto de qualificações](cognitive-search-defining-skillset.md).

## <a name="create-an-index"></a>Crie um índice

Nesta seção, você define o esquema de índice especificando os campos que serão incluídos no índice de pesquisa e definindo os atributos de pesquisa para cada campo. Campos têm um tipo e podem levar a atributos que determinam como o campo é usado (pesquisável, classificável e assim por diante). Nomes de campo em um índice não são necessários para identicamente correspondem aos nomes de campo na fonte. Em uma etapa posterior, você deve adicionar mapeamentos de campo em um indexador para conectar-se campos de origem-destino. Nesta etapa, defina o índice usando as convenções de nomenclatura de campo relevantes para o aplicativo de pesquisa.

Este exercício usa os seguintes campos e tipos de campo:

| nomes de campo: | ID         | conteúdo   | languageCode | keyPhrases         | organizações     |
|--------------|----------|-------|----------|--------------------|-------------------|
| nomes de campo: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

Execute este script para criar o índice chamado `cogsrch-py-index`.

```python
# Create an index
index_payload = {
    "name": index_name,
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": "true",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false",
            "sortable": "true"
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": "false",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "languageCode",
            "type": "Edm.String",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "keyPhrases",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "organizations",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "sortable": "false",
            "filterable": "false",
            "facetable": "false"
        }
    ]
}

r = requests.put(endpoint + "/indexes/" + index_name,
                 data=json.dumps(index_payload), headers=headers, params=params)
print(r.status_code)
```

A solicitação deve retornar um código de status 201 que confirma o êxito.

Para saber mais sobre como definir um índice, consulte [Criar Índice (API REST do Azure Search)](https://docs.microsoft.com/rest/api/searchservice/create-index).

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Crie um indexador, mapear os campos e executar transformações

Até agora, você criou uma fonte de dados, um conjunto de habilidades e um índice. Esses três componentes se tornam parte de um [indexador](search-indexer-overview.md) que reúne cada item em uma única operação de várias fases. Para formar um indexador com esses objetos, você deve definir mapeamentos de campo.

+ Os fieldMappings são processados antes do conjunto de habilidades, mapeando campos de origem da fonte de dados para campos de destino em um índice. Se os tipos e os nomes do campo forem os mesmos em ambas as extremidades, nenhum mapeamento será necessário.

+ Os outputFieldMappings são processados depois do conjunto de habilidades, fazendo referência a sourceFieldNames que não existem até a decodificação de documentos ou até que o enriquecimento os crie. O targetFieldName é um campo em um índice.

Além de conectar entradas a saídas, você também pode usar mapeamentos de campo para mesclar estruturas de dados. Para obter mais informações, consulte [Como mapear campos enriquecidos para um índice pesquisável](cognitive-search-output-field-mapping.md).

Execute este script para criar um indexador chamado `cogsrch-py-indexer`.

```python
# Create an indexer
indexer_payload = {
    "name": indexer_name,
    "dataSourceName": datasource_name,
    "targetIndexName": index_name,
    "skillsetName": skillset_name,
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction":
            {"name": "base64Encode"}
        },
        {
            "sourceFieldName": "content",
            "targetFieldName": "content"
        }
    ],
    "outputFieldMappings":
    [
        {
            "sourceFieldName": "/document/organizations",
            "targetFieldName": "organizations"
        },
        {
            "sourceFieldName": "/document/pages/*/keyPhrases/*",
            "targetFieldName": "keyPhrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "languageCode"
        }
    ],
    "parameters":
    {
        "maxFailedItems": -1,
        "maxFailedItemsPerBatch": -1,
        "configuration":
        {
            "dataToExtract": "contentAndMetadata",
            "imageAction": "generateNormalizedImages"
        }
    }
}

r = requests.put(endpoint + "/indexers/" + indexer_name,
                 data=json.dumps(indexer_payload), headers=headers, params=params)
print(r.status_code)
```

A solicitação deve retornar um código de status 201 rapidamente; no entanto, o processamento pode levar vários minutos para concluir. Embora o conjunto de dados seja pequeno, as habilidades analíticas, como análise de imagem, exigem computação intensa e levam tempo.

Use o script [Verificar status do indexador](#check-indexer-status) da próxima seção para determinar quando o processo do indexador foi concluído.

> [!TIP]
> Criar um indexador invoca o pipeline. Se houver um problema com o acesso aos dados, o mapeamento de entradas e saídas ou a ordem das operações, ele será exibido nesse estágio. Para executar novamente o pipeline com as alterações de código ou script, é preciso primeiro excluir objetos. Para saber mais, confira [Reiniciar e Reexecutar](#reset).

#### <a name="explore-the-request-body"></a>Explore o corpo da solicitação

O script define `"maxFailedItems"` como -1, que instrui o mecanismo de indexação para ignorar erros durante a importação de dados. Isso é útil porque há portanto alguns documentos na fonte de dados de demonstração. Para uma fonte de dados maior, você definirá o valor maior que 0.

Observe também o `"dataToExtract":"contentAndMetadata"` instrução nos parâmetros de configuração. Essa instrução manda o indexador extrair o conteúdo de formatos de arquivo diferentes e os metadados relacionados a cada arquivo.

Quando o conteúdo é extraído, você pode definir `imageAction` para extrair o texto da imagem foi encontrada na fonte de dados. A configuração `"imageAction":"generateNormalizedImages"`, combinada com a Habilidade de OCR e a Habilidade de Mesclagem de Texto, instrui o indexador a extrair o texto das imagens (por exemplo, a palavra "pare" de uma placa de trânsito "Pare") e inseri-la como parte do campo de conteúdo. Esse comportamento aplica-se tanto às imagens inseridas nos documentos (imagine uma imagem em um arquivo PDF) quanto às imagens encontradas na fonte de dados, por exemplo, um arquivo JPG.

<a name="check-indexer-status"></a>

## <a name="check-indexer-status"></a>Checar o status do indexador

Depois que o indexador for definido, ele é executado automaticamente quando você enviar a solicitação. Dependendo de quais habilidades cognitivas definida, a indexação pode demorar mais do que o esperado. Para descobrir se o processamento do indexador foi concluído, execute o script a seguir.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Na resposta, monitore os valores de "status" e "endTime" em “lastResult”. Execute o script periodicamente para verificar o status. Quando o indexador for concluído, o status será definido como "success", será especificado um "endTime" e a resposta incluirá todos os erros e avisos que ocorreram durante o aprimoramento.

![O indexador foi criado](./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "O indexador foi criado")

Os avisos são comuns com algumas combinações de arquivo e a habilidade de origem e sempre não indicam um problema. Neste tutorial, os avisos não são problemáticos. Por exemplo, um dos arquivos JPEG que não tem texto mostrará o aviso nesta captura de tela.

![Exemplo de aviso do indexador](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "Exemplo de aviso do indexador")

## <a name="query-your-index"></a>Consultar o índice

Depois de terminar de indexação, execute consultas que retornam o conteúdo dos campos individuais. Por padrão, o Azure Search retorna os 50 melhores resultados. Os dados de exemplo serão pequenos para que o padrão funciona bem. No entanto, ao trabalhar com grandes conjuntos de dados, você precisará incluir parâmetros na cadeia de caracteres de consulta para retornar mais resultados. Para obter instruções, consulte [como página de resultados do Azure Search](search-pagination-page-layout.md).

Como uma etapa de verificação, consulte o índice para todos os campos.

```python
# Query the index for all fields
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

O resultado deve ser semelhante ao exemplo a seguir. A captura de tela mostra apenas uma parte da resposta.

![Consultar índice em relação a todos os campos](./media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png "Consultar o índice em relação a todos os campos")

A saída é o esquema de índice, com o nome, tipo e atributos de cada campo.

Enviar uma segunda consulta para `"*"` para retornar todo o conteúdo de um único campo, como `organizations`.

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

O resultado deve ser semelhante ao exemplo a seguir. A captura de tela mostra apenas uma parte da resposta.

![Consultar índice em relação ao conteúdo das organizações](./media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png "Consultar o índice para retornar conteúdo das organizações")

Repita para campos adicionais: conteúdo, idioma, frases-chave e organizações neste exercício. Você pode retornar vários campos via `$select` usando uma lista delimitada por vírgulas.

Você pode usar GET ou POST, dependendo do tamanho e complexidade de cadeia de caracteres de consulta. Para obter mais informações, consulte a [Consulta usando a API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents).
it <a name="reset"></a>

## <a name="reset-and-rerun"></a>Redefinir e execute novamente

Nos primeiros estágios experimentais de desenvolvimento de pipeline, a abordagem mais prática para iterações de design é excluir os objetos do Azure Search e permitir que seu código para recriá-los. Nomes de recurso são exclusivos. Excluir um objeto permite que você recriá-la usando o mesmo nome.

Reindexar seus documentos com as novas definições:

1. Exclua o índice para remover dados persistentes. Exclua o indexador para recriá-la em seu serviço.
2. Modifique as definições do conjunto de habilidades e do índice.
3. Recrie um índice e um indexador no serviço para executar o pipeline.

Use o portal para excluir índices, indexadores e conjuntos de habilidades. Ao excluir o indexador, também é possível excluir o índice, o conjunto de habilidades e a fonte de dados ao mesmo tempo.

![Excluir objetos de pesquisa](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Excluir objetos de pesquisa no portal")

Você também pode excluí-los usando um script. O script a seguir excluirá o conjunto de habilidades que criamos. Você pode modificar a solicitação facilmente para excluir o índice, o indexador e a fonte de dados.

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Como seu código amadurece, talvez queira refinar uma estratégia de reconstrução. Para saber mais, confira [Como recompilar um índice](search-howto-reindex.md).

## <a name="takeaways"></a>Observações

Este tutorial demonstra as etapas básicas para a criação de um pipeline de indexação enriquecido durante a criação de componentes: uma fonte de dados, o conjunto de qualificações, o índice e o indexador.

Introduzimos [habilidades predefinidas](cognitive-search-predefined-skills.md), com definições de conjunto de habilidades e uma maneira de encadear habilidades por meio de entradas e saídas. Você também aprendeu que `outputFieldMappings` no indexador definição é necessária para roteamentos valores enriquecidos do pipeline em um índice de pesquisado em um serviço do Azure Search.

Por fim, você aprendeu a testar os resultados e a redefinir o sistema para ter novas iterações. Você aprendeu que emitir consultas em relação ao índice retorna a saída criada pelo pipeline de indexação enriquecido. Nesta versão, há um mecanismo para exibir as construções internas (enriquecidas documentos criados pelo sistema). Também aprendeu a verificar o status do indexador e quais objetos devem ser excluídos antes de executar novamente um pipeline.

## <a name="clean-up-resources"></a>Limpar recursos

A maneira mais rápida para limpar depois de um tutorial é excluindo o grupo de recursos que contém o serviço do Azure Search e o serviço de Blob do Azure. Supondo que tenha colocado os dois serviços no mesmo grupo, exclua o grupo de recursos para eliminar permanentemente todo o seu conteúdo, inclusive os serviços e o conteúdo armazenado que criou para este tutorial. No portal, o nome do grupo de recurso está na página de Visão geral de cada serviço.

## <a name="next-steps"></a>Próximas etapas

Personalizar ou estender o pipeline com qualificações personalizadas. Criando uma habilidade personalizada e adicionando-a a um conjunto de qualificações permite que você carregue texto ou análise de imagem que você escreve por conta própria.

> [!div class="nextstepaction"]
> [Exemplo: Como criar uma habilidade personalizada para a pesquisa cognitiva](cognitive-search-create-custom-skill-example.md)
