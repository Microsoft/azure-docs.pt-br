---
title: Tutorial de C# sobre o uso de IA nos Blobs do Azure
titleSuffix: Azure Cognitive Search
description: Veja um exemplo de extração de texto e processamento em idioma natural do conteúdo no Armazenamento de Blobs usando o C# e o SDK do .NET do Azure Cognitive Search.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/23/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 6cbfa01788f1897264b4197798aa5879cf724db3
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104770947"
---
# <a name="tutorial-use-net-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Tutorial: Usar o .NET e a IA para gerar um conteúdo pesquisável com base nos blobs do Azure

Se você tiver um texto não estruturado ou imagens no Armazenamento de Blobs do Azure, um [pipeline de enriquecimento de IA](cognitive-search-concept-intro.md) poderá extrair informações e criar conteúdo para cenários de pesquisa de texto completo ou mineração de conhecimento. 

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Configurar um ambiente de desenvolvimento
> * Definir um pipeline que usa OCR, detecção de idioma e reconhecimento de frases-chave e entidade.
> * Executar o pipeline para invocar transformações e para criar e carregar um índice de pesquisa.
> * Explore os resultados usando a pesquisa de texto completo e uma sintaxe de consulta avançada.

Caso não tenha uma assinatura do Azure, abra uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="overview"></a>Visão geral

Este tutorial usa C# e a [biblioteca de clientes **Azure.Search.Documents**](/dotnet/api/overview/azure/search.documents-readme) para criar uma fonte de dados, um índice, um indexador e um conjunto de habilidades.

O indexador se conecta a um contêiner de blobs especificado no objeto da fonte de dados e envia todo o conteúdo indexado para um índice de pesquisa existente.

O conjunto de habilidades é anexado ao indexador. Ele usa habilidades internas da Microsoft para encontrar e extrair informações. As etapas no pipeline incluem OCR (reconhecimento óptico de caracteres) em imagens, detecção de idioma em texto, extração de frases-chave e reconhecimento de entidades (organizações). Novas informações criadas pelo pipeline são armazenadas em novos campos em um índice. Depois que o índice for preenchido, você poderá usar os campos em consultas, facetas e filtros.

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio](https://visualstudio.microsoft.com/downloads/)
* [Pacote NuGet Azure.Search.Documents 11.x](https://www.nuget.org/packages/Azure.Search.Documents) 
* [Armazenamento do Azure](https://azure.microsoft.com/services/storage/)
* [Azure Cognitive Search](https://azure.microsoft.com/services/search/)

> [!Note]
> Use o serviço de pesquisa gratuito para este tutorial. Um serviço de pesquisa gratuito limita você a três índices, três indexadores e três fontes de dados. Este tutorial cria um de cada. Antes de começar, reserve um espaço no seu serviço para aceitar os novos recursos.

## <a name="download-sample-data"></a>Baixar dados de exemplo

Os dados de exemplo são compostos por 14 arquivos com tipo de conteúdo misto que você carregará no Armazenamento de Blobs do Azure em uma etapa posterior.

1. Abra esta [pasta do OneDrive](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) e, no canto superior esquerdo, clique em **Baixar** para copiar os arquivos para o computador. 

1. Clique com o botão direito do mouse no arquivo zip e selecione **Extrair Tudo**. Há 14 arquivos de vários tipos. Você usará sete para este exercício.

Você também pode baixar o código-fonte deste tutorial. O código-fonte está na pasta **tutorial-ai-enrichment/v11** no repositório [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="1---create-services"></a>1 – Criar serviços

Este tutorial usa o Azure Cognitive Search para indexação e consultas, os Serviços Cognitivos no back-end para enriquecimento de IA e o Armazenamento de Blobs do Azure para fornecer os dados. Este tutorial permanece sob a alocação gratuita de 20 transações por indexador por dia nos Serviços Cognitivos, de modo que os únicos serviços que você precisará criar são pesquisa e armazenamento.

Se possível, crie os dois na mesma região e no mesmo grupo de recursos para facilitar a proximidade e a capacidade de gerenciamento. Na prática, sua conta de armazenamento do Azure pode estar em qualquer região.

### <a name="start-with-azure-storage"></a>Começar com o Armazenamento do Azure

1. [Entre no portal do Azure](https://portal.azure.com/) e clique em **+ Criar Recurso**.

1. Pesquise *conta de armazenamento* e selecione a oferta Conta de Armazenamento da Microsoft.

   ![Criar conta de armazenamento](media/cognitive-search-tutorial-blob/storage-account.png "Criar Conta de Armazenamento")

1. Na guia Informações Básicas, os itens a seguir são obrigatórios. Aceite os padrões para todo o restante.

   * **Grupo de recursos**. Selecione um grupo existente ou crie um, mas use o mesmo grupo para todos os serviços, de modo que você possa gerenciá-los em conjunto.

   * **Nome da conta de armazenamento**. Se acreditar que possa ter vários recursos do mesmo tipo, use o nome para desfazer a ambiguidade por tipo e região, por exemplo, *blobstoragewestus*. 

   * **Local**. Se possível, escolha a mesma localização usada para a Pesquisa Cognitiva do Azure e os Serviços Cognitivos. Uma única localização anula os encargos de largura de banda.

   * **Tipo de Conta**. Escolha o padrão, *StorageV2 (Uso Geral v2)* .

1. Clique em **Examinar + Criar** para criar o serviço.

1. Após a criação, clique em **Ir para o recurso** para abrir a página Visão Geral.

1. Clique em serviço **Blobs**.

1. Clique em **+ Contêiner** para criar um contêiner e nomeie-o *cog-search-demo*.

1. Selecione *cog-search-demo* e, em seguida, clique em **Upload** para abrir a pasta em que você salvou os arquivos de download. Selecione todos os quatorze arquivos e clique em **OK** para carregá-los.

   ![Carregar arquivos de exemplo](media/cognitive-search-quickstart-blob/sample-data.png "Carregar arquivos de exemplo")

1. Antes de sair do Armazenamento do Azure, obtenha uma cadeia de conexão, de modo que você possa formular uma conexão na Pesquisa Cognitiva do Azure. 

   1. Navegue novamente até a página Visão Geral de sua conta de armazenamento (usamos *blobstoragewestus* como exemplo). 

   1. No painel de navegação à esquerda, selecione **Chaves de acesso** e copie uma das cadeias de conexão. 

   A cadeia de conexão é uma URL semelhante ao seguinte exemplo:

      ```http
      DefaultEndpointsProtocol=https;AccountName=blobstoragewestus;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Salve a cadeia de conexão no Bloco de notas. Você precisará dela mais tarde ao configurar a conexão de fonte de dados.

### <a name="cognitive-services"></a>Serviços Cognitivos

O enriquecimento de IA é apoiado pelos Serviços Cognitivos, incluindo Análise de Texto e Pesquisa Visual Computacional para processamento de imagem e idioma natural. Caso seu objetivo seja concluir um protótipo ou um projeto real, neste ponto, você poderá provisionar os Serviços Cognitivos (na mesma região da Pesquisa Cognitiva do Azure), de modo que você possa anexá-lo às operações de indexação.

Para este exercício, no entanto, ignore o provisionamento de recursos, porque Pesquisa Cognitiva do Azure pode se conectar aos Serviços Cognitivos nos bastidores e fornecer a você 20 transações gratuitas por execução de indexador. Como este tutorial usa 14 transações, a alocação gratuita é suficiente. Para projetos maiores, planeje o provisionamento dos Serviços Cognitivos no nível S0 pago conforme o uso. Para obter mais informações, confira [Anexar Serviços Cognitivos](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Pesquisa Cognitiva do Azure

O terceiro componente é o Azure Cognitive Search, que você pode [criar no portal](search-create-service-portal.md) ou [encontrar um serviço de pesquisa existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) em sua assinatura.

Use a Camada gratuita para concluir este passo a passo. 

### <a name="copy-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Copiar uma URL e uma chave de API de administração para o Azure Cognitive Search

Para interagir com o serviço da Pesquisa Cognitiva do Azure, será necessária a URL de serviço e uma chave de acesso. Um serviço de pesquisa é criado com ambos, portanto, se você adicionou a Pesquisa Cognitiva do Azure à sua assinatura, siga estas etapas para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha a URL. Um ponto de extremidade de exemplo pode parecer com `https://mydemo.search.windows.net`.

1. Em **Configurações** > **Chaves**, copie uma chave de administração para adquirir todos os direitos sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso seja necessário sobrepor uma. É possível usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

   Obtenha a chave de consulta também. É uma melhor prática para emitir solicitações de consulta com acesso somente leitura.

   ![Obter o nome do serviço e as chaves de consulta e de administrador](media/search-get-started-javascript/service-name-and-keys.png)

Ter uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que lida com ela.

## <a name="2---set-up-your-environment"></a>2 – Configurar o ambiente

Comece abrindo o Visual Studio e criando um novo projeto de aplicativo de console que possa ser executado no .NET Core.

### <a name="install-azuresearchdocuments"></a>Instalar Azure.Search.Documents

O [SDK do .NET do Azure Cognitive Search](/dotnet/api/overview/azure/search) consiste em uma biblioteca de clientes que permite que você gerencie seus índices, fontes de dados, indexadores e conjuntos de habilidades, carregue e gerencie documentos e execute consultas, sem precisar lidar com os detalhes de HTTP e JSON. Essa biblioteca de clientes é distribuída como um pacote NuGet.

Para este projeto, instale a versão 11 ou posterior do `Azure.Search.Documents` e a versão mais recente do `Microsoft.Extensions.Configuration`.

1. No Visual Studio, selecione **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Gerenciar Pacotes NuGet para a Solução...**

1. Procure por [Azure.Search.Document](https://www.nuget.org/packages/Azure.Search.Documents).

1. Selecione a versão mais recente e clique em **Instalar**.

1. Repita as etapas anteriores para instalar [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) e [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json).

### <a name="add-service-connection-information"></a>Adicionar informações de conexão de serviço

1. Clique com o botão direito do mouse no projeto no Gerenciador de Soluções e selecione **Adicionar** > **Novo Item...** . 

1. Nomeie o arquivo `appsettings.json` e selecione **Adicionar**. 

1. Inclua esse arquivo no diretório de saída.
    1. Clique com o botão direito do mouse em `appsettings.json` e selecione **Propriedades**. 
    1. Altere o valor de **Copiar para Diretório de Saída** para **Copiar se for o mais recente**.

1. Copie o JSON abaixo em seu novo arquivo JSON.

    ```json
    {
      "SearchServiceUri": "Put your search service URI here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```

Adicione as informações do seu serviço de pesquisa e da conta de armazenamento de blobs. Lembre-se de que você pode obter essas informações nas etapas de provisionamento do serviço indicadas na seção anterior.

Para **SearchServiceUri**, insira a URL completa.

### <a name="add-namespaces"></a>Adicionar namespaces

Em `Program.cs`, adicione os namespaces a seguir.

```csharp
using Azure;
using Azure.Search.Documents.Indexes;
using Azure.Search.Documents.Indexes.Models;
using Microsoft.Extensions.Configuration;
using System;
using System.Collections.Generic;
using System.Linq;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>Criar um cliente

Crie uma instância de um `SearchIndexClient` e um `SearchIndexerClient` em `Main`.

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    string searchServiceUri = configuration["SearchServiceUri"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];
    string cognitiveServicesKey = configuration["CognitiveServicesKey"];

    SearchIndexClient indexClient = new SearchIndexClient(new Uri(searchServiceUri), new AzureKeyCredential(adminApiKey));
    SearchIndexerClient indexerClient = new SearchIndexerClient(new Uri(searchServiceUri), new AzureKeyCredential(adminApiKey));
}
```

> [!NOTE]
> Os clientes se conectam ao serviço de pesquisa. Para evitar abrir um número excessivo de conexões, tente compartilhar uma instância única em seu aplicativo, se possível. Os métodos são thread-safe para habilitar esse compartilhamento.
> 

### <a name="add-function-to-exit-the-program-during-failure"></a>Adicionar uma função para sair do programa durante uma falha

Este tutorial pretende ajudar você a entender cada etapa do pipeline de indexação. Se houver um problema crítico que impeça o programa de criar a fonte de dados, o conjunto de habilidades, o índice ou o indexador, o programa produzirá a mensagem de erro e será fechado, de modo que o problema possa ser compreendido e resolvido.

Adicione `ExitProgram` a `Main` para processar cenários que exigem o fechamento do programa.

```csharp
private static void ExitProgram(string message)
{
    Console.WriteLine("{0}", message);
    Console.WriteLine("Press any key to exit the program...");
    Console.ReadKey();
    Environment.Exit(0);
}
```

## <a name="3---create-the-pipeline"></a>3 – Criar o pipeline

Na Pesquisa Cognitiva do Azure, o processamento de IA ocorre durante a indexação (ou a ingestão de dados). Esta parte do passo a passo cria quatro objetos: fonte de dados, definição de índice, conjunto de habilidades e indexador. 

### <a name="step-1-create-a-data-source"></a>Etapa 1: Criar uma fonte de dados

`SearchIndexerClient` tem uma propriedade [`DataSourceName`](/dotnet/api/azure.search.documents.indexes.models.searchindexer.datasourcename) que você pode definir para um objeto `SearchIndexerDataSourceConnection`. Esse objeto fornece todos os métodos necessários para criar, listar, atualizar ou excluir fontes de dados do Azure Cognitive Search.

Crie uma nova instância `SearchIndexerDataSourceConnection` chamando `indexerClient.CreateOrUpdateDataSourceConnection(dataSource)`. O código a seguir cria uma fonte de dados do tipo `AzureBlob`.

```csharp
private static SearchIndexerDataSourceConnection CreateOrUpdateDataSource(SearchIndexerClient indexerClient, IConfigurationRoot configuration)
{
    SearchIndexerDataSourceConnection dataSource = new SearchIndexerDataSourceConnection(
        name: "demodata",
        type: SearchIndexerDataSourceType.AzureBlob,
        connectionString: configuration["AzureBlobConnectionString"],
        container: new SearchIndexerDataContainer("cog-search-demo"))
    {
        Description = "Demo files to demonstrate cognitive search capabilities."
    };

    // The data source does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        indexerClient.CreateOrUpdateDataSourceConnection(dataSource);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Failed to create or update the data source\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without a data source");
    }

    return dataSource;
}
```

Para uma solicitação bem-sucedida, o método retornará a fonte de dados que foi criada. Se houver um problema com a solicitação, como um parâmetro inválido, o método gerará uma exceção.

Agora, adicione uma linha à `Main` para chamar a função `CreateOrUpdateDataSource` recém-adicionada.

```csharp
// Create or Update the data source
Console.WriteLine("Creating or updating the data source...");
SearchIndexerDataSourceConnection dataSource = CreateOrUpdateDataSource(indexerClient, configuration);
```

Compile e execute a solução. Como esta é sua primeira solicitação, verifique o portal do Azure para confirmar a fonte de dados foi criado na Pesquisa Cognitiva do Azure. Na página de visão geral do serviço de pesquisa, verifique se a lista de Fontes de Dados tem um novo item. Talvez seja necessário aguardar alguns minutos para que a página do portal atualizar.

  ![Bloco de fontes de dados no portal](./media/cognitive-search-tutorial-blob/data-source-tile.png "Bloco de fontes de dados no portal")

### <a name="step-2-create-a-skillset"></a>Etapa 2: Criar um conjunto de habilidades

Nesta seção, você deve definir um conjunto de etapas de enriquecimento que você deseja aplicar aos seus dados. Cada etapa de enriquecimento é chamada de *habilidade*, e o conjunto de etapas de enriquecimento é um *conjunto de habilidades*. Este tutorial usa [habilidades cognitivas internas](cognitive-search-predefined-skills.md) para o conjunto de habilidades:

* A qualificação [OCR (Reconhecimento Óptico de Caracteres)](cognitive-search-skill-ocr.md) reconhece textos impressos e manuscritos em arquivos de imagem.

* A qualificação [Fusão de Texto](cognitive-search-skill-textmerger.md) consolida o texto de uma coleção de campos em um único campo.

* [A detecção de idioma](cognitive-search-skill-language-detection.md) para identificar o idioma do conteúdo.

* A [Divisão de Texto](cognitive-search-skill-textsplit.md) divide o conteúdo grande em partes menores antes de chamar a habilidade de extração de frase-chave e a habilidade de reconhecimento de entidade. A extração de frase-chave e o reconhecimento de entidade aceitam entradas de 50 mil caracteres ou menos. Alguns dos arquivos de exemplo precisam dividir para se ajustar dentro desse limite.

* [Reconhecimento de entidade](cognitive-search-skill-entity-recognition.md) para extrair os nomes das organizações de conteúdo no contêiner de blobs.

* [Extração de frase chave](cognitive-search-skill-keyphrases.md) para destacar as principais frases-chave.

Durante o processamento inicial, o Azure Cognitive Search abre cada documento para extrair conteúdo de diferentes formatos de arquivo. O texto originário do arquivo de origem é colocado em um campo `content` gerado, um para cada documento. Assim, defina a entrada como `"/document/content"` para usar esse texto. O conteúdo de imagem é colocado em um campo `normalized_images` gerado, especificado em um conjunto de habilidades como `/document/normalized_images/*`.

Saídas podem ser mapeadas para um índice usado como entrada para uma habilidade de downstream, ou ambos, como é o caso com o código de idioma. No índice, um código de idioma é útil para filtragem. Como uma entrada, o código de idioma é usado por habilidades de análise de texto para informar as regras linguísticas em torno de quebra de palavras.

Para obter mais informações sobre conceitos básicos do conjunto de qualificações, consulte [como definir um conjunto de qualificações](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>Habilidade OCR

A habilidade **OCR** extrai o texto das imagens. Essa habilidade presume que um campo de imagens normalizado existe. Para gerar este campo, mais adiante no tutorial, definiremos a configuração ```"imageAction"``` na definição do indexador para ```"generateNormalizedImages"```.

```csharp
private static OcrSkill CreateOcrSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("image")
    {
        Source = "/document/normalized_images/*"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("text")
    {
        TargetName = "text"
    });

    OcrSkill ocrSkill = new OcrSkill(inputMappings, outputMappings)
    {
        Description = "Extract text (plain and structured) from image",
        Context = "/document/normalized_images/*",
        DefaultLanguageCode = OcrSkillLanguage.En,
        ShouldDetectOrientation = true
    };

    return ocrSkill;
}
```

### <a name="merge-skill"></a>Habilidade de mesclar

Nesta seção, você criará uma habilidade **Mesclar**, que mescla o campo de conteúdo do documento com o texto produzido pela habilidade do OCR.

```csharp
private static MergeSkill CreateMergeSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/content"
    });
    inputMappings.Add(new InputFieldMappingEntry("itemsToInsert")
    {
        Source = "/document/normalized_images/*/text"
    });
    inputMappings.Add(new InputFieldMappingEntry("offsets")
    {
        Source = "/document/normalized_images/*/contentOffset"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("mergedText")
    {
        TargetName = "merged_text"
    });

    MergeSkill mergeSkill = new MergeSkill(inputMappings, outputMappings)
    {
        Description = "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
        Context = "/document",
        InsertPreTag = " ",
        InsertPostTag = " "
    };

    return mergeSkill;
}
```

### <a name="language-detection-skill"></a>Habilidade de detecção de idioma

A habilidade **Detecção de Idioma** detecta o idioma de texto de entrada e os relatórios de um código de idioma único para cada documento enviado na solicitação. Usaremos a saída da habilidade **Detecção de Idioma** como parte da entrada para a habilidade **Divisão de Texto**.

```csharp
private static LanguageDetectionSkill CreateLanguageDetectionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/merged_text"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("languageCode")
    {
        TargetName = "languageCode"
    });

    LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(inputMappings, outputMappings)
    {
        Description = "Detect the language used in the document",
        Context = "/document"
    };

    return languageDetectionSkill;
}
```

### <a name="text-split-skill"></a>Habilidade de divisão de texto

A habilidade **Dividir** abaixo dividirá o texto por páginas e limitará o tamanho da página a quatro mil caracteres, conforme medido por `String.Length`. O algoritmo tentará dividir o texto em blocos com no máximo `maximumPageLength` de tamanho. Nesse caso, o algoritmo fará o melhor para quebrar a frase em um limite de orações, de modo que o tamanho da parte possa ser um pouco menor que `maximumPageLength`.

```csharp
private static SplitSkill CreateSplitSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/merged_text"
    });
    inputMappings.Add(new InputFieldMappingEntry("languageCode")
    {
        Source = "/document/languageCode"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("textItems")
    {
        TargetName = "pages",
    });

    SplitSkill splitSkill = new SplitSkill(inputMappings, outputMappings)
    {
        Description = "Split content into pages",
        Context = "/document",
        TextSplitMode = TextSplitMode.Pages,
        MaximumPageLength = 4000,
        DefaultLanguageCode = SplitSkillLanguage.En
    };

    return splitSkill;
}
```

### <a name="entity-recognition-skill"></a>Habilidade de reconhecimento de entidade

Esta instância `EntityRecognitionSkill` está configurada para reconhecer o tipo de categoria `organization`. A habilidade de **Reconhecimento de Entidade** também pode reconhecer os tipos de categoria `person` e `location`.

Observe que o campo "context" está definido como ```"/document/pages/*"``` com um asterisco, o que significa que a etapa de enriquecimento é chamada para cada página em ```"/document/pages"```.

```csharp
private static EntityRecognitionSkill CreateEntityRecognitionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/pages/*"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("organizations")
    {
        TargetName = "organizations"
    });

    EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(inputMappings, outputMappings)
    {
        Description = "Recognize organizations",
        Context = "/document/pages/*",
        DefaultLanguageCode = EntityRecognitionSkillLanguage.En
    };
    entityRecognitionSkill.Categories.Add(EntityCategory.Organization);

    return entityRecognitionSkill;
}
```

### <a name="key-phrase-extraction-skill"></a>Habilidade de extração de frase-chave

Como a instância `EntityRecognitionSkill` que acabou de ser criada, a habilidade **Extração de Frases-chave** é chamada para cada página do documento.

```csharp
private static KeyPhraseExtractionSkill CreateKeyPhraseExtractionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/pages/*"
    });
    inputMappings.Add(new InputFieldMappingEntry("languageCode")
    {
        Source = "/document/languageCode"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("keyPhrases")
    {
        TargetName = "keyPhrases"
    });

    KeyPhraseExtractionSkill keyPhraseExtractionSkill = new KeyPhraseExtractionSkill(inputMappings, outputMappings)
    {
        Description = "Extract the key phrases",
        Context = "/document/pages/*",
        DefaultLanguageCode = KeyPhraseExtractionSkillLanguage.En
    };

    return keyPhraseExtractionSkill;
}
```

### <a name="build-and-create-the-skillset"></a>Construir e criar o conjunto de habilidades

Construa o `Skillset` usando as habilidades que você criou.

```csharp
private static SearchIndexerSkillset CreateOrUpdateDemoSkillSet(SearchIndexerClient indexerClient, IList<SearchIndexerSkill> skills,string cognitiveServicesKey)
{
    SearchIndexerSkillset skillset = new SearchIndexerSkillset("demoskillset", skills)
    {
        Description = "Demo skillset",
        CognitiveServicesAccount = new CognitiveServicesAccountKey(cognitiveServicesKey)
    };

    // Create the skillset in your search service.
    // The skillset does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        indexerClient.CreateOrUpdateSkillset(skillset);
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to create the skillset\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without a skillset");
    }

    return skillset;
}
```

Adicione as linhas a seguir a `Main`.

```csharp
// Create the skills
Console.WriteLine("Creating the skills...");
OcrSkill ocrSkill = CreateOcrSkill();
MergeSkill mergeSkill = CreateMergeSkill();
EntityRecognitionSkill entityRecognitionSkill = CreateEntityRecognitionSkill();
LanguageDetectionSkill languageDetectionSkill = CreateLanguageDetectionSkill();
SplitSkill splitSkill = CreateSplitSkill();
KeyPhraseExtractionSkill keyPhraseExtractionSkill = CreateKeyPhraseExtractionSkill();

// Create the skillset
Console.WriteLine("Creating or updating the skillset...");
List<SearchIndexerSkill> skills = new List<SearchIndexerSkill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);
skills.Add(languageDetectionSkill);
skills.Add(splitSkill);
skills.Add(entityRecognitionSkill);
skills.Add(keyPhraseExtractionSkill);

SearchIndexerSkillset skillset = CreateOrUpdateDemoSkillSet(indexerClient, skills, cognitiveServicesKey);
```

### <a name="step-3-create-an-index"></a>Etapa 3: Crie um índice

Nesta seção, você define o esquema de índice especificando quais campos serão incluídos no índice de pesquisa e os atributos de pesquisa para cada campo. Campos têm um tipo e podem levar a atributos que determinam como o campo é usado (pesquisável, classificável e assim por diante). Nomes de campo em um índice não são necessários para identicamente correspondem aos nomes de campo na fonte. Em uma etapa posterior, você deve adicionar mapeamentos de campo em um indexador para conectar-se campos de origem-destino. Nesta etapa, defina o índice usando as convenções de nomenclatura de campo relevantes para o aplicativo de pesquisa.

Este exercício usa os seguintes campos e tipos de campo:

| Nomes de campo | Tipos de campo |
| --- | --- |
| `id` | Edm.String |
| `content` | Edm.String |
| `languageCode` | Edm.String |
| `keyPhrases` | List<Edm.String> |
| `organizations` | List<Edm.String> |

#### <a name="create-demoindex-class"></a>Criar classe DemoIndex

Os campos para este índice são definidos usando uma classe de modelo. Cada propriedade da classe de modelo tem atributos que determinam os comportamentos relacionados à pesquisa do campo de índice correspondente. 

Vamos adicionar a classe de modelo a um novo arquivo C#. Clique com o botão direito do mouse em seu projeto e selecione **Adicionar** > **Novo Item...** , selecione "Classe" e nomeie o arquivo `DemoIndex.cs`, depois selecione **Adicionar**.

Certifique-se de indicar que você deseja usar os tipos dos namespaces `Azure.Search.Documents.Indexes` e `System.Text.Json.Serialization`.

Adicione a definição de classe de modelo abaixo a `DemoIndex.cs` e inclua-a no mesmo namespace onde você criará o índice.

```csharp
using Azure.Search.Documents.Indexes;
using System.Text.Json.Serialization;

namespace EnrichwithAI
{
    // The SerializePropertyNamesAsCamelCase is currently unsupported as of this writing. 
    // Replace it with JsonPropertyName
    public class DemoIndex
    {
        [SearchableField(IsSortable = true, IsKey = true)]
        [JsonPropertyName("id")]
        public string Id { get; set; }

        [SearchableField]
        [JsonPropertyName("content")]
        public string Content { get; set; }

        [SearchableField]
        [JsonPropertyName("languageCode")]
        public string LanguageCode { get; set; }

        [SearchableField]
        [JsonPropertyName("keyPhrases")]
        public string[] KeyPhrases { get; set; }

        [SearchableField]
        [JsonPropertyName("organizations")]
        public string[] Organizations { get; set; }
    }
}
```

Agora que você definiu uma classe de modelo, em `Program.cs` você pode criar uma definição de índice com bastante facilidade. O nome desse índice será `demoindex`. Se já existir um índice com esse nome, ele será excluído.

```csharp
private static SearchIndex CreateDemoIndex(SearchIndexClient indexClient)
{
    FieldBuilder builder = new FieldBuilder();
    var index = new SearchIndex("demoindex")
    {
        Fields = builder.Build(typeof(DemoIndex))
    };

    try
    {
        indexClient.GetIndex(index.Name);
        indexClient.DeleteIndex(index.Name);
    }
    catch (RequestFailedException ex) when (ex.Status == 404)
    {
        //if the specified index not exist, 404 will be thrown.
    }

    try
    {
        indexClient.CreateIndex(index);
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to create the index\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without an index");
    }

    return index;
}
```

Durante os testes, você poderá constatar que está tentando criar o índice mais de uma vez. Por isso, verifique se o índice que você está prestes a criar já existe antes de tentar criá-lo.

Adicione as linhas a seguir a `Main`.

```csharp
// Create the index
Console.WriteLine("Creating the index...");
SearchIndex demoIndex = CreateDemoIndex(indexClient);
```

Adicione a instrução using a seguir para resolver a referência de desambiguação.

```csharp
using Index = Azure.Search.Documents.Indexes.Models;
```

Para saber mais sobre os conceitos de índice, confira [Criar índice (API REST)](/rest/api/searchservice/create-index).

### <a name="step-4-create-and-run-an-indexer"></a>Etapa 4: Criar e executar um indexador

Até agora, você criou uma fonte de dados, um conjunto de qualificações e um índice. Esses três componentes se tornam parte de um [indexador](search-indexer-overview.md) que reúne cada item em uma única operação de várias fases. Para unir esses em um indexador, você deve definir mapeamentos de campo.

* Os fieldMappings são processados antes do conjunto de habilidades, mapeando campos de origem da fonte de dados para campos de destino em um índice. Se os tipos e os nomes do campo forem os mesmos em ambas as extremidades, nenhum mapeamento será necessário.

* Os outputFieldMappings são processados depois do conjunto de habilidades, fazendo referência a sourceFieldNames que não existem até a decodificação de documentos ou até que o enriquecimento os crie. O targetFieldName é um campo em um índice.

Além de conectar entradas a saídas, você também pode usar mapeamentos de campo para mesclar estruturas de dados. Para obter mais informações, consulte [Como mapear campos enriquecidos para um índice pesquisável](cognitive-search-output-field-mapping.md).

```csharp
private static SearchIndexer CreateDemoIndexer(SearchIndexerClient indexerClient, SearchIndexerDataSourceConnection dataSource, SearchIndexerSkillset skillSet, SearchIndex index)
{
    IndexingParameters indexingParameters = new IndexingParameters()
    {
        MaxFailedItems = -1,
        MaxFailedItemsPerBatch = -1,
    };
    indexingParameters.Configuration.Add("dataToExtract", "contentAndMetadata");
    indexingParameters.Configuration.Add("imageAction", "generateNormalizedImages");

    SearchIndexer indexer = new SearchIndexer("demoindexer", dataSource.Name, index.Name)
    {
        Description = "Demo Indexer",
        SkillsetName = skillSet.Name,
        Parameters = indexingParameters
    };

    FieldMappingFunction mappingFunction = new FieldMappingFunction("base64Encode");
    mappingFunction.Parameters.Add("useHttpServerUtilityUrlTokenEncode", true);

    indexer.FieldMappings.Add(new FieldMapping("metadata_storage_path")
    {
        TargetFieldName = "id",
        MappingFunction = mappingFunction

    });
    indexer.FieldMappings.Add(new FieldMapping("content")
    {
        TargetFieldName = "content"
    });

    indexer.OutputFieldMappings.Add(new FieldMapping("/document/pages/*/organizations/*")
    {
        TargetFieldName = "organizations"
    });
    indexer.OutputFieldMappings.Add(new FieldMapping("/document/pages/*/keyPhrases/*")
    {
        TargetFieldName = "keyPhrases"
    });
    indexer.OutputFieldMappings.Add(new FieldMapping("/document/languageCode")
    {
        TargetFieldName = "languageCode"
    });

    try
    {
        indexerClient.GetIndexer(indexer.Name);
        indexerClient.DeleteIndexer(indexer.Name);
    }
    catch (RequestFailedException ex) when (ex.Status == 404)
    {
        //if the specified indexer not exist, 404 will be thrown.
    }

    try
    {
        indexerClient.CreateIndexer(indexer);
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to create the indexer\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without creating an indexer");
    }

    return indexer;
}
```

Adicione as linhas a seguir a `Main`.

```csharp
// Create the indexer, map fields, and execute transformations
Console.WriteLine("Creating the indexer and executing the pipeline...");
SearchIndexer demoIndexer = CreateDemoIndexer(indexerClient, dataSource, skillset, demoIndex);
```

Espere que o processamento do indexador leve algum tempo para ser concluído. Embora o conjunto de dados é pequeno, capacidades analíticas são intensivas na computação. Algumas técnicas, como a análise de imagem, são demoradas.

> [!TIP]
> Criar um indexador invoca o pipeline. Se houver problemas ao acessar os dados, as entradas de mapeamento e saídas ou ordem de operações, eles aparecem neste estágio.

### <a name="explore-creating-the-indexer"></a>Explorar a criação do indexador

O código define `"maxFailedItems"` como -1, que instrui o mecanismo de indexação para ignorar erros durante a importação de dados. Isso é útil porque há portanto alguns documentos na fonte de dados de demonstração. Para uma fonte de dados maior, você definirá o valor maior que 0.

Observe também que `"dataToExtract"` está definido como `"contentAndMetadata"`. Essa instrução informa o indexador para extrair automaticamente o conteúdo de diferentes formatos de arquivo, como também os metadados relacionados a cada arquivo.

Quando o conteúdo é extraído, você pode definir `imageAction` para extrair o texto da imagem foi encontrada na fonte de dados. A configuração `"imageAction"` definida como `"generateNormalizedImages"`, combinada com a habilidade de OCR e a habilidade de Mesclagem de Texto, instrui o indexador a extrair o texto das imagens (por exemplo, a palavra "pare" de uma placa de trânsito "Pare") e inseri-la como parte do campo de conteúdo. Esse comportamento se aplica a ambas as imagens inseridas nos documentos (imagine uma imagem dentro de um PDF), bem como imagens encontradas na fonte de dados, por exemplo um arquivo JPG.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 – Monitorar a indexação

Depois que o indexador for definido, ele é executado automaticamente quando você enviar a solicitação. Dependendo de quais habilidades cognitivas definida, a indexação pode demorar mais do que o esperado. Para descobrir se o indexador ainda está em execução, use o método `GetStatus`.

```csharp
private static void CheckIndexerOverallStatus(SearchIndexerClient indexerClient, SearchIndexer indexer)
{
    try
    {
        var demoIndexerExecutionInfo = indexerClient.GetIndexerStatus(indexer.Name);

        switch (demoIndexerExecutionInfo.Value.Status)
        {
            case IndexerStatus.Error:
                ExitProgram("Indexer has error status. Check the Azure Portal to further understand the error.");
                break;
            case IndexerStatus.Running:
                Console.WriteLine("Indexer is running");
                break;
            case IndexerStatus.Unknown:
                Console.WriteLine("Indexer status is unknown");
                break;
            default:
                Console.WriteLine("No indexer information");
                break;
        }
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to get indexer overall status\n Exception message: {0}\n", ex.Message);
    }
}
```

`demoIndexerExecutionInfo` representa o status atual e o histórico de execução de um indexador.

Os avisos são comuns com algumas combinações de arquivo e a habilidade de origem e sempre não indicam um problema. Neste tutorial, os avisos são benignos (por exemplo, nenhuma entrada de texto dos arquivos JPEG).

Adicione as linhas a seguir a `Main`.

```csharp
// Check indexer overall status
Console.WriteLine("Check the indexer overall status...");
CheckIndexerOverallStatus(indexerClient, demoIndexer);
```

## <a name="5---search"></a>5 – Pesquisar

Nos aplicativos de console dos tutoriais do Azure Cognitive Search, normalmente adicionamos um atraso de dois segundos antes de executar consultas que retornam resultados, mas como o enriquecimento leva vários minutos para ser concluído, fecharemos o aplicativo de console e usaremos outra abordagem.

A opção mais fácil é o [Gerenciador de pesquisa](search-explorer.md) no portal. Você pode executar primeiro uma consulta vazia que retorna todos os documentos ou uma pesquisa mais direcionada que retorna o conteúdo de campo criado pelo pipeline. 

1. No portal do Azure, na página de Visão geral da pesquisa, selecione **Índices**.

1. Localize **`demoindex`** na lista. Ele deve ter 14 documentos. Quando a contagem de documentos é zero, o indexador ainda está em execução ou a página ainda não foi atualizada. 

1. Selecione **`demoindex`**. O Gerenciador de pesquisa é a primeira guia.

1. O conteúdo fica pesquisável assim que o primeiro documento é carregado. Para verificar se o conteúdo existe, execute uma consulta não especificada clicando em **Pesquisar**. Essa consulta retorna todos os documentos indexados atualmente, dando a você uma ideia do que o índice contém.

1. Em seguida, cole a seguinte cadeia de caracteres para obter resultados mais gerenciáveis: `search=*&$select=id, languageCode, organizations`

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Redefinir e execute novamente

Nos primeiros estágios experimentais de desenvolvimento, a abordagem mais prática para a iteração de design é excluir os objetos do Azure Cognitive Search e permitir que o código os recompile. Nomes de recurso são exclusivos. Excluir um objeto permite que você recriá-la usando o mesmo nome.

O código de exemplo deste tutorial verifica se há objetos existentes e os exclui, de modo que você possa executar novamente o código. Use também o portal para excluir índices, indexadores, fontes de dados e conjuntos de habilidades.

## <a name="takeaways"></a>Observações

Este tutorial demonstrou as etapas básicas para a criação de um pipeline de indexação enriquecido durante a criação de partes dos componentes: uma fonte de dados, o conjunto de qualificações, o índice e o indexador.

[Habilidades internas](cognitive-search-predefined-skills.md) foram introduzidas, junto com a definição de conjunto de qualificações e a mecânica de encadear habilidades por meio de entradas e saídas. Você também aprendeu que `outputFieldMappings` na definição do indexador é necessário para encaminhar valores enriquecidos do pipeline em um índice pesquisável em um serviço da Pesquisa Cognitiva do Azure.

Por fim, você aprendeu como resultados de teste e reinicie o sistema para obter mais iterações. Você aprendeu que emitir consultas em relação ao índice retorna a saída criada pelo pipeline de indexação enriquecido. Você também aprendeu como verificar o status do indexador e quais objetos a serem excluídos antes de executar novamente um pipeline.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você está trabalhando em sua própria assinatura, no final de um projeto, é uma boa ideia remover os recursos que já não são necessários. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Encontre e gerencie recursos no portal usando o link Todos os recursos ou Grupos de recursos no painel de navegação à esquerda.

## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com todos os objetos de um pipeline de enriquecimento de IA, vamos examinar mais de perto as definições de conjunto de habilidades e as habilidades individuais.

> [!div class="nextstepaction"]
> [Como criar um conjunto de habilidades](cognitive-search-defining-skillset.md)