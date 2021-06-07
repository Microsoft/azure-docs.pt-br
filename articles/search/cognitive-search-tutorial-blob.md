---
title: 'Tutorial: REST e IA em blobs do Azure'
titleSuffix: Azure Cognitive Search
description: Veja um exemplo de extração de texto e processamento de linguagem natural do conteúdo no Armazenamento de Blobs usando o Postman e as APIs REST do Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/17/2020
ms.openlocfilehash: 21f0d141567f17c470732088c6a93a2ae7ed3c67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94738043"
---
# <a name="tutorial-use-rest-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Tutorial: Use o REST e a IA para gerar conteúdo pesquisável em blobs do Azure

Se você tiver um texto não estruturado ou imagens no Armazenamento de Blobs do Azure, um [pipeline de enriquecimento de IA](cognitive-search-concept-intro.md) poderá extrair informações e criar um conteúdo com base nos blobs que são úteis para cenários de pesquisa de texto completo ou mineração de conhecimento. Embora um pipeline possa processar imagens, este tutorial do REST concentra-se no texto, aplicando detecção de idioma e processamento de idioma natural para criar campos que você pode usar em consultas, facetas e filtros.

Este tutorial usa o Postman e as [APIs REST de Pesquisa](/rest/api/searchservice/) para executar as seguintes tarefas:

> [!div class="checklist"]
> * Configurar serviços e uma coleção do Postman.
> * Criar um pipeline de enriquecimento que extrai texto, detecta o idioma, reconhece entidades e detecta frases-chave.
> * Criar um índice para armazenar a saída (conteúdo bruto, além de pares nome-valor gerados pelo pipeline).
> * Executar o pipeline para executar transformações e a análise e para carregar o índice.
> * Explore os resultados usando a pesquisa de texto completo e uma sintaxe de consulta avançada.

Caso não tenha uma assinatura do Azure, abra uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="overview"></a>Visão geral

Este tutorial usa C# e as APIs REST do Azure Cognitive Search para criar uma fonte de dados, um índice, um indexador e um conjunto de habilidades. Você começará com documentos inteiros (texto não estruturado) como PDF, HTML, DOCX e PPTX no Armazenamento de Blobs do Azure e, em seguida, os executará por meio de um conjunto de habilidades para extrair entidades, frases-chave e outros tipos de texto nos arquivos de conteúdo.

Esse conjunto de habilidades usa habilidades internas baseadas na API de Serviços Cognitivos. As etapas no pipeline incluem a detecção de idioma em texto, extração de frases-chave e reconhecimento de entidades (organizações). Novas informações são armazenadas em novos campos que você pode aproveitar em consultas, facetas e filtros.

## <a name="prerequisites"></a>Pré-requisitos

+ [Armazenamento do Azure](https://azure.microsoft.com/services/storage/)
+ [Aplicativo Postman para a área de trabalho](https://www.getpostman.com/)
+ [Criar](search-create-service-portal.md) ou [encontrar um serviço de pesquisa existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Use o serviço gratuito para este tutorial. Um serviço de pesquisa gratuito limita você a três índices, três indexadores e três fontes de dados. Este tutorial cria um de cada. Antes de começar, reserve um espaço no seu serviço para aceitar os novos recursos.

## <a name="download-files"></a>Baixar arquivos

1. Abra esta [pasta do OneDrive](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) e, no canto superior esquerdo, clique em **Baixar** para copiar os arquivos para o computador. 

1. Clique com o botão direito do mouse no arquivo zip e selecione **Extrair Tudo**. Há 14 arquivos de vários tipos. Você usará sete para este exercício.

Opcionalmente, você também pode baixar o código-fonte, um arquivo de coleção do Postman, para este tutorial. O código-fonte pode ser encontrado em [https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial).

## <a name="1---create-services"></a>1 – Criar serviços

Este tutorial usa o Azure Cognitive Search para indexação e consultas, os Serviços Cognitivos no back-end para enriquecimento de IA e o Armazenamento de Blobs do Azure para fornecer os dados. Este tutorial permanece sob a alocação gratuita de 20 transações por indexador por dia nos Serviços Cognitivos, de modo que os únicos serviços que você precisará criar são pesquisa e armazenamento.

Se possível, crie os dois na mesma região e no mesmo grupo de recursos para facilitar a proximidade e a capacidade de gerenciamento. Na prática, sua conta de armazenamento do Azure pode estar em qualquer região.

### <a name="start-with-azure-storage"></a>Começar com o Armazenamento do Azure

1. [Entre no portal do Azure](https://portal.azure.com/) e clique em **+ Criar Recurso**.

1. Pesquise *conta de armazenamento* e selecione a oferta Conta de Armazenamento da Microsoft.

   ![Criar conta de armazenamento](media/cognitive-search-tutorial-blob/storage-account.png "Criar Conta de Armazenamento")

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

   ![Carregar arquivos de exemplo](media/cognitive-search-tutorial-blob/sample-files.png "Carregar arquivos de exemplo")

1. Antes de sair do Armazenamento do Azure, obtenha uma cadeia de conexão, de modo que você possa formular uma conexão na Pesquisa Cognitiva do Azure. 

   1. Navegue novamente até a página Visão Geral de sua conta de armazenamento (usamos *blobstragewestus* como exemplo). 
   
   1. No painel de navegação à esquerda, selecione **Chaves de acesso** e copie uma das cadeias de conexão. 

   A cadeia de conexão é uma URL semelhante ao seguinte exemplo:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Salve a cadeia de conexão no Bloco de notas. Você precisará dela mais tarde ao configurar a conexão de fonte de dados.

### <a name="cognitive-services"></a>Serviços Cognitivos

O enriquecimento de IA é apoiado pelos Serviços Cognitivos, incluindo Análise de Texto e Pesquisa Visual Computacional para processamento de imagem e idioma natural. Caso seu objetivo seja concluir um protótipo ou um projeto real, neste ponto, você poderá provisionar os Serviços Cognitivos (na mesma região da Pesquisa Cognitiva do Azure), de modo que você possa anexá-lo às operações de indexação.

Para este exercício, no entanto, ignore o provisionamento de recursos, porque Pesquisa Cognitiva do Azure pode se conectar aos Serviços Cognitivos nos bastidores e fornecer a você 20 transações gratuitas por execução de indexador. Como este tutorial usa sete transações, a alocação gratuita é suficiente. Para projetos maiores, planeje o provisionamento dos Serviços Cognitivos no nível S0 pago conforme o uso. Para obter mais informações, confira [Anexar Serviços Cognitivos](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Pesquisa Cognitiva do Azure

O terceiro componente é a Pesquisa Cognitiva do Azure, que pode ser [criada no portal](search-create-service-portal.md). Use a Camada gratuita para concluir este passo a passo. 

Assim como o Armazenamento de Blobs do Azure, reserve um momento para coletar a chave de acesso. Além disso, quando começar a estruturar as solicitações, você precisará fornecer a chave de API de administração e o ponto de extremidade usados para autenticar cada solicitação.

### <a name="copy-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Copiar uma URL e uma chave de API de administração para o Azure Cognitive Search

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha o nome de seu serviço de pesquisa. Você pode confirmar o nome do serviço examinando a URL do ponto de extremidade. Se a URL do ponto de extremidade for `https://mydemo.search.windows.net`, o nome do serviço será `mydemo`.

2. Em **Configurações** > **Chaves**, obtenha uma chave de administração para adquirir todos os direitos sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso seja necessário sobrepor uma. É possível usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

   Obtenha a chave de consulta também. É uma melhor prática para emitir solicitações de consulta com acesso somente leitura.

   ![Obter o nome do serviço e as chaves de consulta e de administrador](media/search-get-started-javascript/service-name-and-keys.png)

Todas as solicitações exigem uma api-key no cabeçalho de cada solicitação enviada a seu serviço. Uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que a manipula.

## <a name="2---set-up-postman"></a>2 – Configurar o Postman

Inicie o Postman e configure uma solicitação HTTP. Se não estiver familiarizado com essa ferramenta, confira [Explorar APIs REST do Azure Cognitive Search](search-get-started-rest.md).

Os métodos de solicitação usados neste tutorial são **POST**, **PUT** e **GET**. Você usará os métodos para fazer quatro chamadas à API ao serviço de pesquisa: criar uma fonte de dados, um conjunto de habilidades, um índice e um indexador.

Em Cabeçalhos, defina "Content-Type" como `application/json` e `api-key` como a chave de API de administração do serviço da Pesquisa Cognitiva do Azure. Depois de definir os cabeçalhos, você poderá usá-los para cada solicitação neste exercício.

  ![URL e cabeçalho da solicitação do Postman](media/search-get-started-rest/postman-url.png "URL e cabeçalho da solicitação do Postman")

## <a name="3---create-the-pipeline"></a>3 – Criar o pipeline

No Azure Cognitive Search, o enriquecimento ocorre durante a indexação (ou a ingestão de dados). Esta parte do passo a passo cria quatro objetos: fonte de dados, definição de índice, conjunto de habilidades e indexador. 

### <a name="step-1-create-a-data-source"></a>Etapa 1: Criar uma fonte de dados

Um [objeto de fonte de dados](/rest/api/searchservice/create-data-source) fornece a cadeia de conexão ao contêiner de blob que contém os arquivos.

1. Use **POST** e a URL a seguir, substituindo NOME-DO-SERVIÇO pelo nome real do serviço.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/datasources?api-version=2020-06-30
   ```

1. No **Corpo** da solicitação, copie a definição JSON a seguir, substituindo a `connectionString` pela conexão real da conta de armazenamento. 

   Lembre-se de editar o nome do contêiner também. Sugerimos "cog-search-demo" para o nome do contêiner em uma etapa anterior.

    ```json
    {
      "name" : "cog-search-demo-ds",
      "description" : "Demo files to demonstrate cognitive search capabilities.",
      "type" : "azureblob",
      "credentials" :
      { "connectionString" :
        "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-ACCOUNT-KEY>;"
      },
      "container" : { "name" : "<YOUR-BLOB-CONTAINER-NAME>" }
    }
    ```
1. Enviar a solicitação. Você deverá ver um código de status 201 confirmando o êxito. 

Se você receber um erro 403 ou 404, verifique a construção da solicitação: `api-version=2020-06-30` deve ser no ponto de extremidade, `api-key` deve estar no cabeçalho após `Content-Type`, e seu valor deve ser válido para um serviço de pesquisa. É recomendável executar o documento JSON por meio de um validador JSON online para verificar se a sintaxe está correta. 

### <a name="step-2-create-a-skillset"></a>Etapa 2: Criar um conjunto de habilidades

Um [objeto de conjunto de habilidades](/rest/api/searchservice/create-skillset) é um conjunto de etapas de enriquecimento aplicado ao conteúdo. 

1. Use **PUT** e a URL a seguir, substituindo NOME-DO-SERVIÇO pelo nome real do serviço.

    ```http
    https://[YOUR-SERVICE-NAME].search.windows.net/skillsets/cog-search-demo-sd?api-version=2020-06-30
    ```

1. No **Corpo** da solicitação, copie a definição JSON abaixo. Esse conjunto de habilidades consiste nas habilidades internas a seguir.

   | Habilidade                 | Descrição    |
   |-----------------------|----------------|
   | [Reconhecimento de Entidade](cognitive-search-skill-entity-recognition.md) | Extrai os nomes de pessoas, organizações e localizações do conteúdo no contêiner de blobs. |
   | [Detecção de Idioma](cognitive-search-skill-language-detection.md) | Detecta o idioma do conteúdo. |
   | [Divisão de texto](cognitive-search-skill-textsplit.md)  | Divide um conteúdo grande em partes menores antes de chamar a habilidade de extração de frases-chave. Extração de frase-chave aceita entradas de 50.000 caracteres ou menos. Alguns dos arquivos de exemplo precisam dividir para se ajustar dentro desse limite. |
   | [Extração de Frases-chave](cognitive-search-skill-keyphrases.md) | Extrai as principais frases-chave. |

   Cada uma delas executa no conteúdo do documento. Durante o processamento, a Pesquisa Cognitiva do Azure abre cada documento para ler o conteúdo de diferentes formatos de arquivo. Encontrado um texto de origem no arquivo de origem é colocado em um campo gerado ```content```, uma para cada documento. Dessa forma, a entrada se torna ```"/document/content"```.

   Para a extração de frases-chave, como usamos a habilidade de divisor de texto para dividir arquivos maiores em páginas, o contexto para a habilidade de extração de frases-chave é ```"document/pages/*"``` (para cada página no documento) em vez de ```"/document/content"```.

    ```json
    {
      "description": "Extract entities, detect language and extract key-phrases",
      "skills":
      [
        {
          "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
          "categories": [ "Person", "Organization", "Location" ],
          "defaultLanguageCode": "en",
          "inputs": [
            { "name": "text", "source": "/document/content" }
          ],
          "outputs": [
            { "name": "persons", "targetName": "persons" },
            { "name": "organizations", "targetName": "organizations" },
            { "name": "locations", "targetName": "locations" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
          "inputs": [
            { "name": "text", "source": "/document/content" }
          ],
          "outputs": [
            { "name": "languageCode", "targetName": "languageCode" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
          "textSplitMode" : "pages",
          "maximumPageLength": 4000,
          "inputs": [
            { "name": "text", "source": "/document/content" },
            { "name": "languageCode", "source": "/document/languageCode" }
          ],
          "outputs": [
            { "name": "textItems", "targetName": "pages" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
          "context": "/document/pages/*",
          "inputs": [
            { "name": "text", "source": "/document/pages/*" },
            { "name":"languageCode", "source": "/document/languageCode" }
          ],
          "outputs": [
            { "name": "keyPhrases", "targetName": "keyPhrases" }
          ]
        }
      ]
    }
    ```
    Uma representação gráfica do conjunto de qualificações é mostrada abaixo. 

    ![Entender um conjunto de habilidades](media/cognitive-search-tutorial-blob/skillset.png "Entender um conjunto de habilidades")

1. Enviar a solicitação. O Postman deverá retornar um código de status 201 confirmando o êxito. 

> [!NOTE]
> Saídas podem ser mapeadas para um índice usado como entrada para uma habilidade de downstream, ou ambos, como é o caso com o código de idioma. No índice, um código de idioma é útil para filtragem. Como uma entrada, o código de idioma é usado por habilidades de análise de texto para informar as regras linguísticas em torno de quebra de palavras. Para obter mais informações sobre conceitos básicos do conjunto de qualificações, consulte [como definir um conjunto de qualificações](cognitive-search-defining-skillset.md).

### <a name="step-3-create-an-index"></a>Etapa 3: Crie um índice

Um [índice](/rest/api/searchservice/create-index) fornece o esquema usado para criar a expressão física do conteúdo em índices invertidos e outros constructos na Pesquisa Cognitiva do Azure. O maior componente de um índice é a coleção de campos, em que o tipo de dados e os atributos determinam o conteúdo e os comportamentos na Pesquisa Cognitiva do Azure.

1. Use **PUT** e a URL a seguir, substituindo NOME-DO-SERVIÇO pelo nome real do serviço, para nomear o índice.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?api-version=2020-06-30
   ```

1. No **Corpo** da solicitação, copie a definição JSON a seguir. O campo `content` armazena o documento propriamente dito. Os campos adicionais para `languageCode`, `keyPhrases` e `organizations` representam novas informações (campos e valores) criadas pelo conjunto de habilidades.

    ```json
    {
      "fields": [
        {
          "name": "id",
          "type": "Edm.String",
          "key": true,
          "searchable": true,
          "filterable": false,
          "facetable": false,
          "sortable": true
        },
        {
          "name": "metadata_storage_name",
          "type": "Edm.String",
          "searchable": false,
          "filterable": false,
          "facetable": false,
          "sortable": false
        },
        {
          "name": "content",
          "type": "Edm.String",
          "sortable": false,
          "searchable": true,
          "filterable": false,
          "facetable": false
        },
        {
          "name": "languageCode",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "facetable": false
        },
        {
          "name": "keyPhrases",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "filterable": false,
          "facetable": false
        },
        {
          "name": "persons",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        },
        {
          "name": "organizations",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        },
        {
          "name": "locations",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        }
      ]
    }
    ```

1. Enviar a solicitação. O Postman deverá retornar um código de status 201 confirmando o êxito. 

### <a name="step-4-create-and-run-an-indexer"></a>Etapa 4: Criar e executar um indexador

Um [Indexador](/rest/api/searchservice/create-indexer) conduz o pipeline. Os três componentes que você criou até o momento (fonte de dados, conjunto de habilidades e índice) são entradas para um indexador. A criação do indexador na Pesquisa Cognitiva do Azure é o evento que coloca todo o pipeline em movimento. 

1. Use **PUT** e a URL a seguir, substituindo NOME-DO-SERVIÇO pelo nome real do serviço, para nomear o indexador.

   ```http
   https://[servicename].search.windows.net/indexers/cog-search-demo-idxr?api-version=2020-06-30
   ```

1. No **Corpo** da solicitação, copie a definição JSON abaixo. Observe os elementos de mapeamento de campo; esses mapeamentos são importantes porque definem o fluxo de dados. 

   Os `fieldMappings` são processados antes do conjunto de habilidades, enviando o conteúdo da fonte de dados para os campos de destino em um índice. Você usará mapeamentos de campo para enviar o conteúdo existente e não modificado ao índice. Se os tipos e os nomes do campo forem os mesmos em ambas as extremidades, nenhum mapeamento será necessário.

   Os `outputFieldMappings` destinam-se aos campos criados por habilidades e, portanto, são processados após a execução do conjunto de habilidades. As referências a `sourceFieldNames` em `outputFieldMappings` não existirão enquanto a quebra de documento ou o enriquecimento não as criar. O `targetFieldName` é um campo em um índice, definido no esquema de índice.

    ```json
    {
      "name":"cog-search-demo-idxr",
      "dataSourceName" : "cog-search-demo-ds",
      "targetIndexName" : "cog-search-demo-idx",
      "skillsetName" : "cog-search-demo-ss",
      "fieldMappings" : [
        {
          "sourceFieldName" : "metadata_storage_path",
          "targetFieldName" : "id",
          "mappingFunction" :
            { "name" : "base64Encode" }
        },
        {
          "sourceFieldName" : "metadata_storage_name",
          "targetFieldName" : "metadata_storage_name",
          "mappingFunction" :
            { "name" : "base64Encode" }
        },
        {
          "sourceFieldName" : "content",
          "targetFieldName" : "content"
        }
      ],
      "outputFieldMappings" :
      [
        {
          "sourceFieldName" : "/document/persons",
          "targetFieldName" : "persons"
        },
        {
          "sourceFieldName" : "/document/organizations",
          "targetFieldName" : "organizations"
        },
        {
          "sourceFieldName" : "/document/locations",
          "targetFieldName" : "locations"
        },
        {
          "sourceFieldName" : "/document/pages/*/keyPhrases/*",
          "targetFieldName" : "keyPhrases"
        },
        {
          "sourceFieldName": "/document/languageCode",
          "targetFieldName": "languageCode"
        }
      ],
      "parameters":
      {
        "maxFailedItems":-1,
        "maxFailedItemsPerBatch":-1,
        "configuration":
        {
          "dataToExtract": "contentAndMetadata",
          "parsingMode": "default",
          "firstLineContainsHeaders": false,
          "delimitedTextDelimiter": ","
        }
      }
    }
    ```

1. Enviar a solicitação. O Postman deverá retornar um código de status 201 confirmando o processamento bem-sucedido. 

   Espere essa ação levar vários minutos para ser concluída. Embora o conjunto de dados é pequeno, capacidades analíticas são intensivas na computação. 

> [!NOTE]
> Criar um indexador invoca o pipeline. Se houver problemas ao acessar os dados, as entradas de mapeamento e saídas ou ordem de operações, eles aparecem neste estágio. Para executar novamente o pipeline com as alterações de código ou script, você precisará primeiro descarte de objetos. Para saber mais, confira [Reiniciar e Reexecutar](#reset).

#### <a name="about-indexer-parameters"></a>Sobre os parâmetros do indexador

O script define ```"maxFailedItems"``` como -1, que instrui o mecanismo de indexação para ignorar erros durante a importação de dados. Isso é aceitável porque há poucos documentos na fonte de dados de demonstração. Para uma fonte de dados maior, você definirá o valor maior que 0.

A instrução ```"dataToExtract":"contentAndMetadata"``` instrui o indexador a extrair automaticamente o conteúdo de diferentes formatos de arquivo, bem como os metadados relacionados a cada arquivo. 

Quando o conteúdo é extraído, você pode definir ```imageAction``` para extrair o texto da imagem foi encontrada na fonte de dados. A configuração ```"imageAction":"generateNormalizedImages"```, combinada com a Habilidade de OCR e a Habilidade de Mesclagem de Texto, instrui o indexador a extrair o texto das imagens (por exemplo, a palavra "pare" de uma placa de trânsito "Pare") e inseri-la como parte do campo de conteúdo. Esse comportamento se aplica a ambas as imagens inseridas nos documentos (imagine uma imagem dentro de um PDF), bem como imagens encontradas na fonte de dados, por exemplo um arquivo JPG.

## <a name="4---monitor-indexing"></a>4 – Monitorar a indexação

A indexação e o enriquecimento são iniciados assim que você envia a solicitação Criar Indexador. Dependendo das habilidades cognitivas definidas, a indexação pode levar algum tempo. Para descobrir se o indexador ainda está em execução, envie a solicitação a seguir para verificar o status do indexador.

1. Use **GET** e a URL a seguir, substituindo NOME-DO-SERVIÇO pelo nome real do serviço, para nomear o indexador.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr/status?api-version=2020-06-30
   ```

1. Examine a resposta para saber se o indexador está em execução ou para exibir informações de erro e aviso.  

Se você estiver usando a Camada gratuita, a seguinte mensagem será esperada: "Não foi possível extrair o conteúdo nem os metadados do documento. Texto extraído truncado para '32.768' caracteres". Essa mensagem é exibida porque a indexação de blob na Camada gratuita tem um[limite de 32 mil caracteres na extração de caracteres](search-limits-quotas-capacity.md#indexer-limits). Você não verá essa mensagem para esse conjunto de dados em camadas superiores. 

> [!NOTE]
> Os avisos são comuns em alguns cenários e nem sempre indicam um problema. Por exemplo, se um contêiner de blobs incluir arquivos de imagem e o pipeline não tratar imagens, você receberá um aviso informando que as imagens não foram processadas.

## <a name="5---search"></a>5 – Pesquisar

Agora que você criou campos e informações, vamos executar algumas consultas para entender o valor da pesquisa cognitiva, pois ela se relaciona a um cenário de pesquisa típico.

Lembre-se de que começamos com o conteúdo do blob, em que todo o documento é empacotado em um único campo `content`. Pesquise esse campo e encontre correspondências para as consultas.

1. Use **GET** e a URL a seguir, substituindo NOME-DO-SERVIÇO pelo nome real do serviço, para pesquisar instâncias de um termo ou uma frase, retornando o campo `content` e uma contagem dos documentos correspondentes.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$count=true&$select=content&api-version=2020-06-30
   ```
   
   Os resultados dessa consulta retornam o conteúdo do documento, que é o mesmo resultado que você obterá se usar o indexador de blob sem o pipeline da pesquisa cognitiva. Esse campo é pesquisável, mas não funcionará se você quiser usar facetas, filtros ou o Preenchimento automático.

   ![Saída do campo de conteúdo](media/cognitive-search-tutorial-blob/content-output.png "Saída do campo de conteúdo")
   
1. Para a segunda consulta, retorne alguns dos campos criados pelo pipeline (pessoas, organizações, localizações e languageCode). Estamos omitindo as keyPhrases por questões de brevidade, mas você deverá incluí-las se quiser ver esses valores.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$count=true&$select=metadata_storage_name,persons,organizations,locations,languageCode&api-version=2020-06-30
   ```
   Os campos na instrução $select contêm informações criadas com base nas funcionalidades de processamento de idioma natural dos Serviços Cognitivos. Como você pode esperar, há algum ruído nos resultados e na variação entre documentos, mas, em muitas instâncias, os modelos analíticos produzem resultados precisos.

   A imagem a seguir mostra os resultados da letra aberta de Satya Nadella depois de assumir o cargo de CEO na Microsoft.

   ![Saída do pipeline](media/cognitive-search-tutorial-blob/pipeline-output.png "Saída do pipeline")

1. Para ver como você pode aproveitar esses campos, adicione um parâmetro de faceta para retornar uma agregação de documentos correspondentes por localização.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&facet=locations&api-version=2020-06-30
   ``` 

   Neste exemplo, para cada localização, há duas ou três correspondências.

   ![Saída da faceta](media/cognitive-search-tutorial-blob/facet-output.png "Saída da faceta")
   

1. Neste exemplo final, aplique um filtro na coleção de organizações, retornando duas correspondências para critérios de filtragem com base na NASDAQ.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$filter=organizations/any(organizations: organizations eq 'NASDAQ')&$select=metadata_storage_name,organizations&$count=true&api-version=2020-06-30
   ```

Essas consultas ilustram algumas das maneiras pelas quais você pode trabalhar com a sintaxe de consulta e os filtros em campos criados pela pesquisa cognitiva. Para obter mais exemplos de consulta, confira [Exemplos em documentos de pesquisa da API REST](/rest/api/searchservice/search-documents#bkmk_examples), [Exemplos de consulta de sintaxe simples](search-query-simple-examples.md) e [Exemplos de consulta completas do Lucene](search-query-lucene-examples.md).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Redefinir e execute novamente

Nos primeiros estágios experimentais de desenvolvimento, a abordagem mais prática para a iteração de design é excluir os objetos do Azure Cognitive Search e permitir que o código os recompile. Nomes de recurso são exclusivos. Excluir um objeto permite que você recriá-la usando o mesmo nome.

Use o portal para excluir índices, indexadores, fontes de dados e conjuntos de habilidades. Ao excluir o indexador, também é possível excluir o índice, o conjunto de habilidades e a fonte de dados ao mesmo tempo.

![Excluir objetos de pesquisa](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Excluir objetos de pesquisa no portal")

Ou então, use **DELETE** e forneça URLs para cada objeto. O comando a seguir exclui um indexador.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr?api-version=2020-06-30
```

Código de status 204 é retornado na exclusão com êxito.

## <a name="takeaways"></a>Observações

Este tutorial demonstra as etapas básicas para a criação de um pipeline de indexação enriquecido durante a criação de componentes: uma fonte de dados, o conjunto de qualificações, o índice e o indexador.

[Habilidades internas](cognitive-search-predefined-skills.md) foram introduzidas, junto com a definição de conjunto de qualificações e a mecânica de encadear habilidades por meio de entradas e saídas. Você também aprendeu que `outputFieldMappings` na definição do indexador é necessário para encaminhar valores enriquecidos do pipeline em um índice pesquisável em um serviço da Pesquisa Cognitiva do Azure.

Por fim, você aprendeu como resultados de teste e reinicie o sistema para obter mais iterações. Você aprendeu que emitir consultas em relação ao índice retorna a saída criada pelo pipeline de indexação enriquecido. 

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você está trabalhando em sua própria assinatura, no final de um projeto, é uma boa ideia remover os recursos que já não são necessários. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Encontre e gerencie recursos no portal usando o link Todos os recursos ou Grupos de recursos no painel de navegação à esquerda.

## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com todos os objetos de um pipeline de enriquecimento de IA, vamos examinar mais de perto as definições de conjunto de habilidades e as habilidades individuais.

> [!div class="nextstepaction"]
> [Como criar um conjunto de habilidades](cognitive-search-defining-skillset.md)
