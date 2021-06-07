---
title: 'Tutorial: Python e IA em blobs do Azure'
titleSuffix: Azure Cognitive Search
description: Veja um exemplo de extração de texto e processamento de linguagem natural do conteúdo no Armazenamento de Blobs usando um notebook do Jupyter Python e as APIs REST do Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 09/25/2020
ms.custom: devx-track-python
ms.openlocfilehash: ea1cc022cbea7dbf3d1fa12cb83cfe3084b28560
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92788072"
---
# <a name="tutorial-use-python-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Tutorial: Use o Python e a IA para gerar conteúdo pesquisável em blobs do Azure

Se você tiver um texto não estruturado ou imagens no Armazenamento de Blobs do Azure, um [pipeline de enriquecimento de IA](cognitive-search-concept-intro.md) poderá extrair informações e criar um conteúdo que seja útil para cenários de pesquisa de texto completo ou mineração de conhecimento. Embora um pipeline possa processar imagens, este tutorial do Python concentra-se no texto, aplicando detecção de idioma e processamento de idioma natural para criar campos que você pode usar em consultas, facetas e filtros.

Este tutorial usa o Python e as [APIs REST de Pesquisa](/rest/api/searchservice/) para executar as seguintes tarefas:

> [!div class="checklist"]
> * Comece com documentos inteiros (texto não estruturado), como PDF, HTML, DOCX e PPTX no Armazenamento de Blobs do Azure.
> * Defina um pipeline que extrai texto, detecta o idioma, reconhece entidades e detecta frases-chave.
> * Defina um índice para armazenar a saída (conteúdo bruto, além de pares nome-valor gerados pelo pipeline).
> * Execute o pipeline para começar com as transformações e a análise e para criar e carregar o índice.
> * Explore os resultados usando a pesquisa de texto completo e uma sintaxe de consulta avançada.

Caso não tenha uma assinatura do Azure, abra uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

+ [Armazenamento do Azure](https://azure.microsoft.com/services/storage/)
+ [Anaconda 3.7](https://www.anaconda.com/distribution/#download-section)
+ [Criar](search-create-service-portal.md) ou [encontrar um serviço de pesquisa existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Use o serviço gratuito para este tutorial. Um serviço de pesquisa gratuito limita você a três índices, três indexadores e três fontes de dados. Este tutorial cria um de cada. Antes de começar, reserve um espaço no seu serviço para aceitar os novos recursos.

## <a name="download-files"></a>Baixar arquivos

1. Abra esta [pasta do OneDrive](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) e, no canto superior esquerdo, clique em **Baixar** para copiar os arquivos para o computador. 

1. Clique com o botão direito do mouse no arquivo zip e selecione **Extrair Tudo**. Há 14 arquivos de vários tipos. Você usará sete para este exercício.

## <a name="1---create-services"></a>1 – Criar serviços

Este tutorial usa o Azure Cognitive Search para indexação e consultas, os Serviços Cognitivos no back-end para enriquecimento de IA e o Armazenamento de Blobs do Azure para fornecer os dados. Este tutorial permanece sob a alocação gratuita de 20 transações por indexador por dia nos Serviços Cognitivos, de modo que os únicos serviços que você precisará criar são pesquisa e armazenamento.

Se possível, crie os dois na mesma região e no mesmo grupo de recursos para facilitar a proximidade e a capacidade de gerenciamento. Na prática, sua conta de armazenamento do Azure pode estar em qualquer região.

### <a name="start-with-azure-storage"></a>Começar com o Armazenamento do Azure

1. [Entre no portal do Azure](https://portal.azure.com/) e clique em **+ Criar Recurso**.

1. Pesquise *conta de armazenamento* e selecione a oferta Conta de Armazenamento da Microsoft.

   :::image type="content" source="media/cognitive-search-tutorial-blob/storage-account.png" alt-text="Criar conta de armazenamento" border="false":::

1. Na guia Informações Básicas, os itens a seguir são obrigatórios. Aceite os padrões para todo o restante.

   + **Grupo de recursos**. Selecione um grupo existente ou crie um, mas use o mesmo grupo para todos os serviços, de modo que você possa gerenciá-los em conjunto.

   + **Nome da conta de armazenamento**. Se acreditar que possa ter vários recursos do mesmo tipo, use o nome para desfazer a ambiguidade por tipo e região, por exemplo, *blobstoragewestus*. 

   + **Local**. Se possível, escolha a mesma localização usada para a Pesquisa Cognitiva do Azure e os Serviços Cognitivos. Uma única localização anula os encargos de largura de banda.

   + **Tipo de Conta**. Escolha o padrão, *StorageV2 (Uso Geral v2)* .

1. Clique em **Examinar + Criar** para criar o serviço.

1. Após a criação, clique em **Ir para o recurso** para abrir a página Visão Geral.

1. Clique em serviço **Blobs**.

1. Clique em **+ Contêiner** para criar um contêiner e nomeie-o *cog-search-demo*.

1. Selecione *cog-search-demo* e, em seguida, clique em **Upload** para abrir a pasta em que você salvou os arquivos de download. Selecione todos os arquivos que não sejam de imagem. Você deve ter sete arquivos. Clique em **OK** para fazer upload deles.

   :::image type="content" source="media/cognitive-search-tutorial-blob/sample-files.png" alt-text="Carregar arquivos de exemplo" border="false":::

1. Antes de sair do Armazenamento do Azure, obtenha uma cadeia de conexão, de modo que você possa formular uma conexão na Pesquisa Cognitiva do Azure. 

   1. Navegue novamente até a página Visão Geral de sua conta de armazenamento (usamos *blobstragewestus* como exemplo). 
   
   1. No painel de navegação à esquerda, selecione **Chaves de acesso** e copie uma das cadeias de conexão. 

   A cadeia de conexão é uma URL semelhante ao seguinte exemplo:

      ```http
      DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Salve a cadeia de conexão no Bloco de notas. Você precisará dela mais tarde ao configurar a conexão de fonte de dados.

### <a name="cognitive-services"></a>Serviços Cognitivos

O enriquecimento de IA é apoiado pelos Serviços Cognitivos, incluindo Análise de Texto e Pesquisa Visual Computacional para processamento de imagem e idioma natural. Caso seu objetivo seja concluir um protótipo ou um projeto real, neste ponto, você poderá provisionar os Serviços Cognitivos (na mesma região da Pesquisa Cognitiva do Azure), de modo que você possa anexá-lo às operações de indexação.

Como este tutorial usa somente 7 transações, você pode ignorar o provisionamento de recursos, porque o Azure Cognitive Search pode se conectar aos Serviços Cognitivos em 20 transações gratuitas por execução do indexador. A alocação gratuita é suficiente. Para projetos maiores, planeje o provisionamento dos Serviços Cognitivos no nível S0 pago conforme o uso. Para obter mais informações, confira [Anexar Serviços Cognitivos](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Pesquisa Cognitiva do Azure

O terceiro componente é a Pesquisa Cognitiva do Azure, que pode ser [criada no portal](search-create-service-portal.md). Use a Camada gratuita para concluir este passo a passo. 

Assim como o Armazenamento de Blobs do Azure, reserve um momento para coletar a chave de acesso. Além disso, quando começar a estruturar as solicitações, você precisará fornecer a chave de API de administração e o ponto de extremidade usados para autenticar cada solicitação.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Obter uma URL e uma chave de API de administração para a Pesquisa Cognitiva do Azure

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha o nome de seu serviço de pesquisa. Você pode confirmar o nome do serviço examinando a URL do ponto de extremidade. Se a URL do ponto de extremidade for `https://mydemo.search.windows.net`, o nome do serviço será `mydemo`.

2. Em **Configurações** > **Chaves**, obtenha uma chave de administração para adquirir todos os direitos sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso seja necessário sobrepor uma. É possível usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

   Obtenha a chave de consulta também. É uma melhor prática para emitir solicitações de consulta com acesso somente leitura.

   :::image type="content" source="media/search-get-started-javascript/service-name-and-keys.png" alt-text="Obter o nome do serviço e as chaves de consulta e de administrador" border="false":::

Todas as solicitações exigem uma api-key no cabeçalho de cada solicitação enviada a seu serviço. Uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que a manipula.

## <a name="2---start-a-notebook"></a>2 – Iniciar um notebook

Crie o notebook usando as instruções a seguir ou baixe um notebook concluído do [repositório Azure-Search-python-samples](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment).

Use o Anaconda Navigator para iniciar o Jupyter Notebook e criar um novo notebook Python 3.

No notebook, execute este script a fim de carregar as bibliotecas usadas para trabalhar com JSON e formular solicitações HTTP.

```python
import json
import requests
from pprint import pprint
```

No mesmo notebook, defina os nomes da fonte de dados, do índice, do indexador e do conjunto de habilidades. Execute este script a fim de configurar os nomes para este tutorial.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

No script a seguir, substitua os espaços reservados para seu serviço de pesquisa (YOUR-SEARCH-SERVICE-NAME) e para a chave da API de administração (YOUR-ADMIN-API-KEY) e execute-o para configurar o ponto de extremidade do serviço de pesquisa.

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2020-06-30'
}
```

## <a name="3---create-the-pipeline"></a>3 – Criar o pipeline

Na Pesquisa Cognitiva do Azure, o processamento de IA ocorre durante a indexação (ou a ingestão de dados). Esta parte do passo a passo cria quatro objetos: fonte de dados, definição de índice, conjunto de habilidades e indexador. 

### <a name="step-1-create-a-data-source"></a>Etapa 1: Criar uma fonte de dados

Um [objeto de fonte de dados](/rest/api/searchservice/create-data-source) fornece a cadeia de conexão ao contêiner de blob que contém os arquivos.

No script a seguir, substitua o espaço reservado YOUR-BLOB-RESOURCE-CONNECTION-STRING pela cadeia de conexão do blob que você criou na etapa anterior. Substitua o texto do espaço reservado do contêiner. Em seguida, execute o script para criar uma fonte de dados chamada `cogsrch-py-datasource`.

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
        "name": "<YOUR-BLOB-CONTAINER-NAME>"
    }
}
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

A solicitação deve retornar um código de status 201 que confirma o êxito.

No portal do Azure, na página do painel do serviço de pesquisa, verifique se cogsrch-py-datasource aparece na lista **Fontes de dados**. Clique em **Atualizar** para atualizar a página.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-data-source-tile.png" alt-text="Bloco de fontes de dados no portal" border="false":::

### <a name="step-2-create-a-skillset"></a>Etapa 2: Criar um conjunto de habilidades

Nesta etapa, você deve definir um conjunto de etapas de aprimoramento para aplicar aos dados. Chamar cada etapa enriquecimento um *habilidade* e o conjunto de etapas de enriquecimento um *conjunto de qualificações*. Este tutorial usa [habilidades cognitivas internas](cognitive-search-predefined-skills.md) para o conjunto de habilidades:

+ [Reconhecimento de entidade](cognitive-search-skill-entity-recognition.md) para extrair os nomes das organizações de conteúdo no contêiner de blobs.

+ [A detecção de idioma](cognitive-search-skill-language-detection.md) para identificar o idioma do conteúdo.

+ [Divisão de texto](cognitive-search-skill-textsplit.md) para dividir o conteúdo grande em partes menores antes de chamar a habilidade de extração de frase-chave. Extração de frase-chave aceita entradas de 50.000 caracteres ou menos. Alguns dos arquivos de exemplo precisam dividir para se ajustar dentro desse limite.

+ [Extração de frase chave](cognitive-search-skill-keyphrases.md) para destacar as principais frases-chave. 

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
                    "name": "text", 
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "organizations", 
                    "targetName": "organizations"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "inputs": [
                {
                    "name": "text", 
                    "source": "/document/content"
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
                    "name": "text", 
                    "source": "/document/pages/*"
                },
                {
                    "name": "languageCode", 
                    "source": "/document/languageCode"
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

Cada uma delas executa no conteúdo do documento. Durante o processamento, a Pesquisa Cognitiva do Azure abre cada documento para ler o conteúdo de diferentes formatos de arquivo. O texto encontrado no arquivo de origem é colocado em um campo `content`, um para cada documento. Portanto, defina a entrada como `"/document/content"`.

Uma representação gráfica do conjunto de qualificações é mostrada abaixo.

:::image type="content" source="media/cognitive-search-tutorial-blob/skillset.png" alt-text="Entender um conjunto de habilidades" border="false":::

As saídas podem ser mapeadas para um índice, usadas como entrada para uma habilidade posterior, ou ambos, como é o caso com o código de idioma. No índice, um código de idioma é útil para filtragem. Como uma entrada, o código de idioma é usado por habilidades de análise de texto para informar as regras linguísticas em torno de quebra de palavras.

Para obter mais informações sobre conceitos básicos do conjunto de qualificações, consulte [como definir um conjunto de qualificações](cognitive-search-defining-skillset.md).

### <a name="step-3-create-an-index"></a>Etapa 3: Crie um índice

Nesta seção, você define o esquema de índice especificando os campos que serão incluídos no índice de pesquisa e definindo os atributos de pesquisa para cada campo. Campos têm um tipo e podem levar a atributos que determinam como o campo é usado (pesquisável, classificável e assim por diante). Nomes de campo em um índice não são necessários para identicamente correspondem aos nomes de campo na fonte. Em uma etapa posterior, você deve adicionar mapeamentos de campo em um indexador para conectar-se campos de origem-destino. Nesta etapa, defina o índice usando as convenções de nomenclatura de campo relevantes para o aplicativo de pesquisa.

Este exercício usa os seguintes campos e tipos de campo:

| nomes de campo: | ID         | content   | languageCode | keyPhrases         | organizações     |
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

Para saber mais sobre como definir um índice, consulte [Criar Índice (API REST da Pesquisa Cognitiva do Azure)](/rest/api/searchservice/create-index).

### <a name="step-4-create-and-run-an-indexer"></a>Etapa 4: Criar e executar um indexador

Um [Indexador](/rest/api/searchservice/create-indexer) conduz o pipeline. Os três componentes que você criou até o momento (fonte de dados, conjunto de habilidades e índice) são entradas para um indexador. A criação do indexador na Pesquisa Cognitiva do Azure é o evento que coloca todo o pipeline em movimento. 

Para formar um indexador com esses objetos, você deve definir mapeamentos de campo.

+ Os `"fieldMappings"` são processados antes do conjunto de habilidades, mapeando campos de origem da fonte de dados para campos de destino em um índice. Se os tipos e os nomes do campo forem os mesmos em ambas as extremidades, nenhum mapeamento será necessário.

+ Os `"outputFieldMappings"` são processados depois do conjunto de habilidades, fazendo referência a `"sourceFieldNames"` que não existem até a quebra de documento ou até que o enriquecimento os crie. O `"targetFieldName"` é um campo em um índice.

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

A solicitação deve retornar um código de status 201 em breve; no entanto, o processamento pode levar vários minutos para concluir. Embora o conjunto de dados seja pequeno, as habilidades analíticas, como análise de imagem, exigem computação intensa e levam tempo.

Você pode [monitorar o status do indexador](#check-indexer-status) para determinar quando o indexador está em execução ou concluído.

> [!TIP]
> Criar um indexador invoca o pipeline. Se houver um problema com o acesso aos dados, o mapeamento de entradas e saídas ou a ordem das operações, ele será exibido nesse estágio. Para executar novamente o pipeline com as alterações de código ou script, é preciso primeiro excluir objetos. Para saber mais, confira [Reiniciar e Reexecutar](#reset).

#### <a name="about-the-request-body"></a>Sobre o corpo da solicitação

O script define `"maxFailedItems"` como -1, que instrui o mecanismo de indexação para ignorar erros durante a importação de dados. Isso é útil porque há portanto alguns documentos na fonte de dados de demonstração. Para uma fonte de dados maior, você definirá o valor maior que 0.

Observe também o `"dataToExtract":"contentAndMetadata"` instrução nos parâmetros de configuração. Essa instrução manda o indexador extrair o conteúdo de formatos de arquivo diferentes e os metadados relacionados a cada arquivo.

Quando o conteúdo é extraído, você pode definir `imageAction` para extrair o texto da imagem foi encontrada na fonte de dados. A configuração `"imageAction":"generateNormalizedImages"`, combinada com a Habilidade de OCR e a Habilidade de Mesclagem de Texto, instrui o indexador a extrair o texto das imagens (por exemplo, a palavra "pare" de uma placa de trânsito "Pare") e inseri-la como parte do campo de conteúdo. Esse comportamento aplica-se tanto às imagens inseridas nos documentos (imagine uma imagem em um arquivo PDF) quanto às imagens encontradas na fonte de dados, por exemplo, um arquivo JPG.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 – Monitorar a indexação

Depois que o indexador for definido, ele é executado automaticamente quando você enviar a solicitação. Dependendo de quais habilidades cognitivas definida, a indexação pode demorar mais do que o esperado. Para descobrir se o processamento do indexador foi concluído, execute o script a seguir.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Na resposta, monitore o `"lastResult"` quanto a seus valores `"status"` e `"endTime"`. Execute o script periodicamente para verificar o status. Quando o indexador for concluído, o status será definido como "success", será especificado um "endTime" e a resposta incluirá todos os erros e avisos que ocorreram durante o aprimoramento.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png" alt-text="O indexador é criado" border="false":::

Os avisos são comuns com algumas combinações de arquivo e a habilidade de origem e sempre não indicam um problema. Muitos avisos são benignos. Por exemplo, se você indexar um arquivo JPEG sem texto, verá o aviso nesta captura de tela.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png" alt-text="Exemplo de aviso do indexador" border="false":::

## <a name="5---search"></a>5 – Pesquisar

Depois de terminar de indexação, execute consultas que retornam o conteúdo dos campos individuais. Por padrão, a Pesquisa Cognitiva do Azure retorna os 50 melhores resultados. Os dados de exemplo serão pequenos para que o padrão funciona bem. No entanto, ao trabalhar com grandes conjuntos de dados, você precisará incluir parâmetros na cadeia de caracteres de consulta para retornar mais resultados. Para obter instruções, consulte [Como paginar resultados da Pesquisa Cognitiva do Azure](search-pagination-page-layout.md).

Como uma etapa de verificação, obtenha a definição de índice mostrando todos os campos.

```python
# Query the service for the index definition
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

O resultado deve ser semelhante ao exemplo a seguir. A captura de tela mostra apenas uma parte da resposta.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png" alt-text="Índice de consulta para todos os campos" border="false":::

A saída é o esquema de índice, com o nome, tipo e atributos de cada campo.

Enviar uma segunda consulta para `"*"` para retornar todo o conteúdo de um único campo, como `organizations`.

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

O resultado deve ser semelhante ao exemplo a seguir. A captura de tela mostra apenas uma parte da resposta.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png" alt-text="Índice de consulta para o conteúdo das organizações" border="false":::

Repita para os campos adicionais: `content`, `languageCode`, `keyPhrases` e `organizations` neste exercício. Você pode retornar vários campos via `$select` usando uma lista delimitada por vírgulas.

Você pode usar GET ou POST, dependendo do tamanho e complexidade de cadeia de caracteres de consulta. Para obter mais informações, consulte a [Consulta usando a API REST](/rest/api/searchservice/search-documents).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Redefinir e execute novamente

Nos primeiros estágios experimentais de desenvolvimento, a abordagem mais prática para a iteração de design é excluir os objetos do Azure Cognitive Search e permitir que o código os recompile. Nomes de recurso são exclusivos. Excluir um objeto permite que você recriá-la usando o mesmo nome.

Use o portal para excluir índices, indexadores, fontes de dados e conjuntos de habilidades. Ao excluir o indexador, também é possível excluir o índice, o conjunto de habilidades e a fonte de dados ao mesmo tempo.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png" alt-text="Excluir objetos de pesquisa no portal" border="false":::

Você também pode excluí-los usando um script. O script a seguir mostra como excluir um conjunto de habilidades. 

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Código de status 204 é retornado na exclusão com êxito.

## <a name="takeaways"></a>Observações

Este tutorial demonstra as etapas básicas para a criação de um pipeline de indexação enriquecido durante a criação de componentes: uma fonte de dados, o conjunto de qualificações, o índice e o indexador.

Introduzimos [habilidades internas](cognitive-search-predefined-skills.md), com definições de conjunto de habilidades e uma maneira de encadear habilidades por meio de entradas e saídas. Você também aprendeu que `outputFieldMappings` na definição do indexador é necessário para encaminhar valores enriquecidos do pipeline em um índice pesquisável em um serviço da Pesquisa Cognitiva do Azure.

Por fim, você aprendeu a testar os resultados e a redefinir o sistema para ter novas iterações. Você aprendeu que emitir consultas em relação ao índice retorna a saída criada pelo pipeline de indexação enriquecido. Nesta versão, há um mecanismo para exibir as construções internas (enriquecidas documentos criados pelo sistema). Também aprendeu a verificar o status do indexador e quais objetos devem ser excluídos antes de executar novamente um pipeline.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você está trabalhando em sua própria assinatura, no final de um projeto, é uma boa ideia remover os recursos que já não são necessários. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Encontre e gerencie recursos no portal usando o link Todos os recursos ou Grupos de recursos no painel de navegação à esquerda.

## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com todos os objetos de um pipeline de enriquecimento de IA, vamos examinar mais de perto as definições de conjunto de habilidades e as habilidades individuais.

> [!div class="nextstepaction"]
> [Como criar um conjunto de habilidades](cognitive-search-defining-skillset.md)