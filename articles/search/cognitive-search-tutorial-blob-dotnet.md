---
title: Tutorial do C# para chamar as APIs de Serviços Cognitivos em um pipeline de indexação – Azure Search
description: Neste tutorial, percorra um exemplo de extração de dados, o idioma natural e o processamento de imagem AI na indexação do Azure Search para transformação e extração de dados.
manager: eladz
author: MarkHeff
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: maheff
ms.openlocfilehash: 2c77d509a0e66fd02bd949e481c5f0316fdd9afb
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672017"
---
# <a name="c-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>Tutorial do C#: Chamar APIs de Serviços Cognitivos em um pipeline de indexação do Azure Search

Neste tutorial, você aprenderá a mecânica de programação enriquecimento de dados no Azure Search usando *habilidades cognitivas*. As habilidades são apoiadas pelos recursos de processamento de linguagem natural (PLN) e análise de imagem nos Serviços Cognitivos. Por meio da composição do conjunto de qualificações e configuração, você pode extrair texto e representações de texto de um arquivo de imagem ou documento digitalizado. Você também pode detectar a linguagem, entidades, frases-chave e muito mais. O resultado final é rico conteúdo adicional em um índice do Azure Search, criado por um pipeline de indexação com Inteligência Artificial.

Neste tutorial, você usa o SDK do .NET para executar as seguintes tarefas:

> [!div class="checklist"]
> * Criar um pipeline de indexação que enriquece dados de exemplo em rota para um índice
> * Aplicar habilidades internas: reconhecimento óptico de caracteres, fusão de texto, detecção de idioma, divisão de texto, reconhecimento de entidade, extração de frases-chave
> * Saiba como encadear habilidades mapeando entradas para saídas em um conjunto de qualificações
> * Executar solicitações e analisar resultados
> * Redefinir o índice e indexadores para desenvolvimento futuro

Saída é um índice de pesquisa de texto completo no Azure Search. Você pode aprimorar o índice com outros recursos padrão, como [sinônimos](search-synonyms.md), [perfis de pontuação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analisadores](search-analyzers.md), e [filtros](search-filters.md).

Este tutorial é executado no serviço gratuito, mas o número de transações gratuitos é limitado a 20 documentos por dia. Se você quiser executar este tutorial mais de uma vez no mesmo dia, use um conjunto de arquivos menor para que você possa encaixar mais execuções.

> [!NOTE]
> À medida que você expande o escopo ao aumentar a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, você precisará anexar um recurso faturável dos Serviços Cognitivos. As cobranças são geradas ao chamar APIs nos Serviços Cognitivos e para a extração de imagem como parte do estágio de decodificação de documentos no Azure Search. Não há encargos para extração de texto em documentos.
>
> A execução das habilidades internas é cobrada com base no [preço de pagamento conforme o uso dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/). O preço de extração de imagem é descrito na [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Os serviços, as ferramentas e os dados a seguir são usados neste tutorial. 

+ [Criar uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) para armazenar os dados de exemplo. Verifique se que a conta de armazenamento está na mesma região do Azure Search.

+ [Dados de exemplo](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) consistem em um conjunto de pequenos arquivos de tipos diferentes. 

+ [Instalar o Visual Studio](https://visualstudio.microsoft.com/) para usar como o IDE.

+ [Crie um serviço Azure Search](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual. Você pode usar um serviço gratuito para este tutorial.

## <a name="get-a-key-and-url"></a>Obter uma chave e uma URL

Para interagir com o serviço do Azure Search, você precisará da URL de serviço e de uma chave de acesso. Um serviço de pesquisa é criado com ambos, portanto, se você adicionou o Azure Search à sua assinatura, siga estas etapas para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha a URL. Um ponto de extremidade de exemplo pode parecer com `https://mydemo.search.windows.net`.

1. Em **Configurações** > **Chaves**, obtenha uma chave de administração para adquirir todos os direitos sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso seja necessário sobrepor uma. É possível usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

   ![Obter um ponto de extremidade HTTP e uma chave de acesso](media/search-get-started-postman/get-url-key.png "Obter um ponto de extremidade HTTP e uma chave de acesso")

Ter uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que lida com ela.

## <a name="prepare-sample-data"></a>Preparar os dados de exemplo

O pipeline de enriquecimento extrai de fontes de dados do Azure. Fonte de dados deve originar-se de um tipo de fonte de dados com suporte de um [indexador do Azure Search](search-indexer-overview.md). O Armazenamento de Tabelas do Azure não tem suporte para a pesquisa cognitiva. Para este exercício, usamos o armazenamento de blob para apresentar múltiplos tipos de conteúdo.

1. [Entre no portal do Azure](https://portal.azure.com), navegue até sua conta de Armazenamento do Azure, clique em **Blobs** e, em seguida, clique em **+ Contêiner**.

1. [Crie um contêiner de Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) para conter dados de exemplo. Você pode definir o Nível de Acesso Público para qualquer um de seus valores válidos. Este tutorial presume que o nome do contêiner seja "basic-demo-data-pr".

1. Depois que o contêiner for criado, abra-o e selecione **Carregar** na barra de comandos para carregar os [dados de exemplo](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4).

   ![Arquivos de origem no armazenamento de blobs do Azure](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Depois que os arquivos de exemplo são carregados, obter o nome do contêiner e uma cadeia de caracteres de conexão para o armazenamento de Blob. Você pode fazer isso navegando para sua conta de armazenamento no portal do Azure, selecionando **Chaves de Acesso** e copiando o campo **Cadeia de Conexão**.

   A cadeia de conexão de armazenamento deve ser uma URL semelhante ao seguinte exemplo:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

Existem outras maneiras de especificar a cadeia de caracteres de conexão, como fornecer uma assinatura de acesso compartilhado. Para saber mais sobre as credenciais de fonte de dados, consulte [indexação armazenamento de BLOBs do Azure](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="set-up-your-environment"></a>Configure seu ambiente

Comece abrindo o Visual Studio e criando um novo projeto de aplicativo de console que possa ser executado no .NET Core.

### <a name="install-nuget-packages"></a>Instalar os pacotes NuGet

O [SDK do Azure Search .NET](https://aka.ms/search-sdk) consiste em algumas bibliotecas de clientes que permitem que você gerencie seus índices, fontes de dados, indexadores e conjuntos de qualificações, carregue e gerencie documentos e execute consultas, sem a necessidade de lidar com os detalhes de HTTP e JSON. Essas bibliotecas de cliente são distribuídas como pacotes NuGet.

Para este projeto, você precisará instalar a versão 9 do pacote `Microsoft.Azure.Search` NuGet e o pacote NuGet `Microsoft.Extensions.Configuration.Json` mais recente.

Instale o pacote `Microsoft.Azure.Search` NuGet usando o console do Gerenciador de Pacotes no Visual Studio. Para abrir o console do Gerenciador de Pacotes, selecione **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**. Para obter o comando a ser executado, navegue até a [página do pacote Microsoft.Azure.Search NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search), selecione a versão 9 e copie o comando do Gerenciador de Pacotes. No console do Gerenciador de Pacotes, execute este comando.

Para instalar o pacote `Microsoft.Extensions.Configuration.Json` NuGet no Visual Studio, selecione **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Gerenciar Pacotes NuGet para a solução...**. Selecione Procurar e procure o pacote `Microsoft.Extensions.Configuration.Json` NuGet. Depois de encontrá-lo, selecione o pacote, selecione seu projeto, confirme se a versão é a versão estável mais recente e selecione Instalar.

## <a name="add-azure-search-service-information"></a>Adicionar informações de serviço do Azure Search

Para se conectar ao seu serviço do Azure Search, você precisará adicionar as informações do serviço de pesquisa ao seu projeto. Clique com o botão direito do mouse no seu projeto no Gerenciador de Soluções e selecione **Adicionar** > **Novo Item...** . Nomeie o arquivo `appsettings.json` e selecione **Adicionar**. 

Este arquivo precisará ser incluído em seu diretório de saída. Para fazer isso, clique com o botão direito em `appsettings.json` e selecione **Propriedades**. Altere o valor de **Copiar para Diretório de Saída** para **Copiar do Mais Recente**.

Copie o JSON abaixo em seu novo arquivo JSON.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "SearchServiceQueryApiKey": "Put your query API key here",
  "AzureBlobConnectionString": "Put your Azure Blob connection string here",
}
```

Adicione as informações do seu serviço de pesquisa e da conta de armazenamento de blobs.

Você pode obter as informações do serviço de pesquisa na página da sua conta de pesquisa no portal do Azure. O nome da conta estará na página principal e as chaves podem ser encontradas selecionando **Chaves**.

Você pode obter a cadeia de caracteres de conexão do blob navegando para sua conta de armazenamento no Portal do Azure, selecionando **Chaves de Acesso** e copiando o campo **Cadeia de Conexão**.

## <a name="add-namespaces"></a>Adicionar namespaces

Este tutorial usa muitos tipos diferentes de vários namespaces. Para usar esses tipos, adicione o seguinte ao `Program.cs`.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;
```

## <a name="create-a-client"></a>Criar um cliente

Criar uma instância da classe `SearchServiceClient`.

```csharp
IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
IConfigurationRoot configuration = builder.Build();
SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient` cria um novo `SearchServiceClient` usando valores que estão armazenados no arquivo de configuração do aplicativo (appsettings.json).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string adminApiKey = configuration["SearchServiceAdminApiKey"];

   SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
   return serviceClient;
}
```

> [!NOTE]
> A classe `SearchServiceClient` gerencia conexões para o seu serviço de pesquisa. Para evitar abrir um número excessivo de conexões, você deve tentar compartilhar uma única instância de `SearchServiceClient` em seu aplicativo, se possível. Esses métodos são thread-safe para habilitar esse compartilhamento.
> 
> 

## <a name="create-a-data-source"></a>Criar uma fonte de dados

Crie uma nova instância `DataSource` chamando `DataSource.AzureBlobStorage`. `DataSource.AzureBlobStorage` requer que você especifique o nome da fonte de dados, a cadeia de caracteres de conexão e o nome do contêiner de blob.

Embora não conste neste tutorial, também é definida uma política de exclusão simples, que é usada para identificar os blobs excluídos com base no valor de uma coluna de exclusão simples. A política a seguir considerará que um blob foi excluído se tiver uma propriedade de metadados `IsDeleted` com o valor `true`.

```csharp
DataSource dataSource = DataSource.AzureBlobStorage(
    name: "demodata",
    storageConnectionString: configuration["AzureBlobConnectionString"],
    containerName: "basic-demo-data-pr",
    deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
        softDeleteColumnName: "IsDeleted",
        softDeleteMarkerValue: "true"),
    description: "Demo files to demonstrate cognitive search capabilities.");
```

Agora que você inicializou o objeto `DataSource`, crie a fonte de dados. `SearchServiceClient` tem uma propriedade `DataSources`. Essa propriedade fornece todos os métodos necessários para criar, listar, atualizar ou excluir fontes de dados do Azure Search.

Para uma solicitação bem-sucedida, o método retornará a fonte de dados que foi criada. Se houver um problema com a solicitação, como um parâmetro inválido, o método gerará uma exceção.

```csharp
try
{
    serviceClient.DataSources.CreateOrUpdate(dataSource);
}
catch (Exception e)
{
    // Handle the exception
}
```

Como esta é sua primeira solicitação, verifique o portal do Azure para confirmar a fonte de dados foi criado no Azure Search. Na página de painel do serviço de pesquisa, verifique se o bloco de fontes de dados tem um novo item. Talvez seja necessário aguardar alguns minutos para que a página do portal atualizar.

  ![Fontes de dados lado a lado no portal do](./media/cognitive-search-tutorial-blob/data-source-tile.png "lado a lado no portal de fontes de dados")

## <a name="create-a-skillset"></a>Criar um conjunto de habilidades

Nesta seção, você deve definir um conjunto de etapas de enriquecimento que você deseja aplicar aos seus dados. Cada etapa de enriquecimento é chamada de uma *qualificação* e o conjunto de etapas de enriquecimento um *conjunto de qualificações*. Este tutorial usa [predefinidos habilidades cognitivas](cognitive-search-predefined-skills.md) para o conjunto de qualificações:

+ A qualificação [OCR (Reconhecimento Óptico de Caracteres)](cognitive-search-skill-ocr.md) reconhece textos impressos e manuscritos em arquivos de imagem.

+ A qualificação [Fusão de Texto](cognitive-search-skill-textmerger.md) consolida o texto de uma coleção de campos em um único campo.

+ [A detecção de idioma](cognitive-search-skill-language-detection.md) para identificar o idioma do conteúdo.

+ A [Divisão de Texto](cognitive-search-skill-textsplit.md) divide o conteúdo grande em partes menores antes de chamar a habilidade de extração de frase-chave e a habilidade de reconhecimento de entidade. A extração de frase-chave e o reconhecimento de entidade aceitam entradas de 50 mil caracteres ou menos. Alguns dos arquivos de exemplo precisam dividir para se ajustar dentro desse limite.

+ [Reconhecimento de entidade](cognitive-search-skill-entity-recognition.md) para extrair os nomes das organizações de conteúdo no contêiner de blobs.

+ [Extração de frase chave](cognitive-search-skill-keyphrases.md) para destacar as principais frases-chave.

Durante o processamento inicial, o Azure Search quebra cada documento para ler conteúdo de diferentes formatos de arquivo. Encontrado um texto de origem no arquivo de origem é colocado em um campo gerado ```content```, uma para cada documento. Como tal, defina a entrada como ```"/document/content"``` para usar esse texto. 

Saídas podem ser mapeadas para um índice usado como entrada para uma habilidade de downstream, ou ambos, como é o caso com o código de idioma. No índice, um código de idioma é útil para filtragem. Como uma entrada, o código de idioma é usado por habilidades de análise de texto para informar as regras linguísticas em torno de quebra de palavras.

Para obter mais informações sobre conceitos básicos do conjunto de qualificações, consulte [como definir um conjunto de qualificações](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>Habilidade OCR

A habilidade **OCR** extrai o texto das imagens. Essa habilidade presume que um campo de imagens normalizado existe. Para gerar este campo, mais adiante no tutorial, definiremos a configuração ```"imageAction"``` na definição do indexador para ```"generateNormalizedImages"```.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "image",
    source: "/document/normalized_images/*"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "text",
    targetName: "text"));

OcrSkill ocrSkill = new OcrSkill(
    description: "Extract text (plain and structured) from image",
    context: "/document/normalized_images/*",
    inputs: inputMappings,
    outputs: outputMappings,
    defaultLanguageCode: OcrSkillLanguage.En,
    shouldDetectOrientation: true);
```

### <a name="merge-skill"></a>Habilidade de mesclar

Nesta seção, você criará uma habilidade **Mesclar** que mescla o campo de conteúdo do documento com o texto produzido pela habilidade de OCR.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/content"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "itemsToInsert",
    source: "/document/normalized_images/*/text"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "offsets",
    source: "/document/normalized_images/*/contentOffset"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "mergedText",
    targetName: "merged_text"));

MergeSkill mergeSkill = new MergeSkill(
    description: "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
    context: "/document",
    inputs: inputMappings,
    outputs: outputMappings,
    insertPreTag: " ",
    insertPostTag: " ");
```

### <a name="language-detection-skill"></a>Habilidade de detecção de idioma

A habilidade **Detecção de Idioma** detecta o idioma de texto de entrada e os relatórios de um código de idioma único para cada documento enviado na solicitação. Usaremos a saída da habilidade **Detecção de Idioma** como parte da entrada para a habilidade **Divisão de Texto**.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/merged_text"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "languageCode",
    targetName: "languageCode"));

LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(
    description: "Detect the language used in the document",
    context: "/document",
    inputs: inputMappings,
    outputs: outputMappings);
```

### <a name="text-split-skill"></a>Habilidade de divisão de texto

A habilidade **Dividir** abaixo dividirá o texto por páginas e limitará o tamanho da página a quatro mil caracteres, conforme medido por `String.Length`. O algoritmo tentará dividir o texto em blocos com no máximo `maximumPageLength` de tamanho. Nesse caso, o algoritmo fará o melhor para quebrar a frase em um limite de orações, de modo que o tamanho da parte possa ser um pouco menor que `maximumPageLength`.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/merged_text"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "languageCode",
    source: "/document/languageCode"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "textItems",
    targetName: "pages"));

SplitSkill splitSkill = new SplitSkill(
    description: "Split content into pages",
    context: "/document",
    inputs: inputMappings,
    outputs: outputMappings,
    textSplitMode: TextSplitMode.Pages,
    maximumPageLength: 4000);
```

### <a name="entity-recognition-skill"></a>Habilidade de reconhecimento de entidade

Esta instância `EntityRecognitionSkill` está configurada para reconhecer o tipo de categoria `organization`. A habilidade de **Reconhecimento de Entidade** também pode reconhecer os tipos de categoria `person` e `location`.

Observe que o campo "context" está definido como ```"/document/pages/*"``` com um asterisco, o que significa que a etapa de enriquecimento é chamada para cada página em ```"/document/pages"```.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/pages/*"));
    
List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "organizations",
    targetName: "organizations"));

List<EntityCategory> entityCategory = new List<EntityCategory>();
entityCategory.Add(EntityCategory.Organization);
    
EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(
    description: "Recognize organizations",
    context: "/document/pages/*",
    inputs: inputMappings,
    outputs: outputMappings,
    categories: entityCategory,
    defaultLanguageCode: EntityRecognitionSkillLanguage.En);
```

### <a name="key-phrase-extraction-skill"></a>Habilidade de extração de frase-chave

Como a instância `EntityRecognitionSkill` que acabou de ser criada, a habilidade **Extração de Frases-chave** é chamada para cada página do documento.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/pages/*"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "languageCode",
    source: "/document/languageCode"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "keyPhrases",
    targetName: "keyPhrases"));

KeyPhraseExtractionSkill keyPhraseExtractionSkill = new KeyPhraseExtractionSkill(
    description: "Extract the key phrases",
    context: "/document/pages/*",
    inputs: inputMappings,
    outputs: outputMappings);
```

### <a name="build-and-create-the-skillset"></a>Construir e criar o conjunto de habilidades

Construa o `Skillset` usando as habilidades que você criou.

```csharp
List<Skill> skills = new List<Skill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);
skills.Add(languageDetectionSkill);
skills.Add(splitSkill);
skills.Add(entityRecognitionSkill);
skills.Add(keyPhraseExtractionSkill);

Skillset skillset = new Skillset(
    name: "demoskillset",
    description: "Demo skillset",
    skills: skills);
```

Crie o conjunto de habilidades no seu serviço de pesquisa.

```csharp
try
{
    serviceClient.Skillsets.CreateOrUpdate(skillset);
}
catch (Exception e)
{
    // Handle exception
}
```

## <a name="create-an-index"></a>Crie um índice

Nesta seção, você define o esquema de índice especificando quais campos serão incluídos no índice de pesquisa e os atributos de pesquisa para cada campo. Campos têm um tipo e podem levar a atributos que determinam como o campo é usado (pesquisável, classificável e assim por diante). Nomes de campo em um índice não são necessários para identicamente correspondem aos nomes de campo na fonte. Em uma etapa posterior, você deve adicionar mapeamentos de campo em um indexador para conectar-se campos de origem-destino. Nesta etapa, defina o índice usando as convenções de nomenclatura de campo relevantes para o aplicativo de pesquisa.

Este exercício usa os seguintes campos e tipos de campo:

| nomes de campo: | `id`       | conteúdo   | languageCode | keyPhrases         | organizações     |
|--------------|----------|-------|----------|--------------------|-------------------|
| nomes de campo: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


### <a name="create-demoindex-class"></a>Criar classe DemoIndex

Os campos para este índice são definidos usando uma classe de modelo. Cada propriedade da classe de modelo tem atributos que determinam os comportamentos relacionados à pesquisa do campo de índice correspondente. 

Vamos adicionar a classe de modelo a um novo arquivo C#. Clique com o botão direito do mouse em seu projeto e selecione **Adicionar** > **Novo Item...**, selecione "Classe" e nomeie o arquivo `DemoIndex.cs`, depois selecione **Adicionar**.

Certifique-se de indicar que você deseja usar os tipos dos namespaces `Microsoft.Azure.Search` e `Microsoft.Azure.Search.Models`.

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
```

Adicione a definição de classe de modelo abaixo a `DemoIndex.cs` e inclua-a no mesmo namespace onde você criará o índice.

```csharp
// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public class DemoIndex
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsSearchable, IsSortable]
    public string Id { get; set; }

    [IsSearchable]
    public string Content { get; set; }

    [IsSearchable]
    public string LanguageCode { get; set; }

    [IsSearchable]
    public string[] KeyPhrases { get; set; }

    [IsSearchable]
    public string[] Organizations { get; set; }
}
```

Agora que você definiu uma classe de modelo, em `Program.cs` você pode criar uma definição de índice com bastante facilidade. O nome para esse índice será "demoindex".

```csharp
var index = new Index()
{
    Name = "demoindex",
    Fields = FieldBuilder.BuildForType<DemoIndex>()
};
```

Durante o teste, você pode constatar que está tentando criar o índice mais de uma vez. Por isso, verifique se o índice que você está prestes a criar já existe antes de tentar criá-lo.

```csharp
try
{
    bool exists = serviceClient.Indexes.Exists(index.Name);

    if (exists)
    {
        serviceClient.Indexes.Delete(index.Name);
    }

    serviceClient.Indexes.Create(index);
}
catch (Exception e)
{
    // Handle exception
}
```

Para saber mais sobre como definir um índice, consulte [Criar Índice (API REST do Azure Search)](https://docs.microsoft.com/rest/api/searchservice/create-index).

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Crie um indexador, mapear os campos e executar transformações

Até agora, você criou uma fonte de dados, um conjunto de qualificações e um índice. Esses três componentes se tornam parte de um [indexador](search-indexer-overview.md) que reúne cada item em uma única operação de várias fases. Para unir esses em um indexador, você deve definir mapeamentos de campo.

+ Os fieldMappings são processados antes do conjunto de habilidades, mapeando campos de origem da fonte de dados para campos de destino em um índice. Se os tipos e os nomes do campo forem os mesmos em ambas as extremidades, nenhum mapeamento será necessário.

+ Os outputFieldMappings são processados depois do conjunto de habilidades, fazendo referência a sourceFieldNames que não existem até a decodificação de documentos ou até que o enriquecimento os crie. O targetFieldName é um campo em um índice.

Além de conectar entradas a saídas, você também pode usar mapeamentos de campo para mesclar estruturas de dados. Para obter mais informações, consulte [Como mapear campos enriquecidos para um índice pesquisável](cognitive-search-output-field-mapping.md).

```csharp
IDictionary<string, object> config = new Dictionary<string, object>();
config.Add(
    key: "dataToExtract",
    value: "contentAndMetadata");
config.Add(
    key: "imageAction",
    value: "generateNormalizedImages");

List<FieldMapping> fieldMappings = new List<FieldMapping>();
fieldMappings.Add(new FieldMapping(
    sourceFieldName: "metadata_storage_path",
    targetFieldName: "id",
    mappingFunction: new FieldMappingFunction(
        name: "base64Encode")));
fieldMappings.Add(new FieldMapping(
    sourceFieldName: "content",
    targetFieldName: "content"));

List<FieldMapping> outputMappings = new List<FieldMapping>();
outputMappings.Add(new FieldMapping(
    sourceFieldName: "/document/pages/*/organizations/*",
    targetFieldName: "organizations"));
outputMappings.Add(new FieldMapping(
    sourceFieldName: "/document/pages/*/keyPhrases/*",
    targetFieldName: "keyPhrases"));
outputMappings.Add(new FieldMapping(
    sourceFieldName: "/document/languageCode",
    targetFieldName: "languageCode"));

Indexer indexer = new Indexer(
    name: "demoindexer",
    dataSourceName: dataSource.Name,
    targetIndexName: index.Name,
    description: "Demo Indexer",
    skillsetName: skillSet.Name,
    parameters: new IndexingParameters(
        maxFailedItems: -1,
        maxFailedItemsPerBatch: -1,
        configuration: config),
    fieldMappings: fieldMappings,
    outputFieldMappings: outputMappings);

try
{
    bool exists = serviceClient.Indexers.Exists(indexer.Name);

    if (exists)
    {
        serviceClient.Indexers.Delete(indexer.Name);
    }

    serviceClient.Indexers.Create(indexer);
}
catch (Exception e)
{
    // Handle exception
}
```

Espera-se que a criação do indexador leve algum tempo para ser concluída. Embora o conjunto de dados é pequeno, capacidades analíticas são intensivas na computação. Algumas técnicas, como a análise de imagem, são demoradas.

> [!TIP]
> Criar um indexador invoca o pipeline. Se houver problemas ao acessar os dados, as entradas de mapeamento e saídas ou ordem de operações, eles aparecem neste estágio.

### <a name="explore-creating-the-indexer"></a>Explorar a criação do indexador

O código define ```"maxFailedItems"``` como -1, que instrui o mecanismo de indexação para ignorar erros durante a importação de dados. Isso é útil porque há portanto alguns documentos na fonte de dados de demonstração. Para uma fonte de dados maior, você definirá o valor maior que 0.

Observe também que ```"dataToExtract"``` está definido como ```"contentAndMetadata"```. Essa instrução informa o indexador para extrair automaticamente o conteúdo de diferentes formatos de arquivo, como também os metadados relacionados a cada arquivo.

Quando o conteúdo é extraído, você pode definir `imageAction` para extrair o texto da imagem foi encontrada na fonte de dados. A configuração ```"imageAction"``` definida como ```"generateNormalizedImages"```, combinada com a habilidade de OCR e a habilidade de Mesclagem de Texto, instrui o indexador a extrair o texto das imagens (por exemplo, a palavra "pare" de uma placa de trânsito "Pare") e inseri-la como parte do campo de conteúdo. Esse comportamento se aplica a ambas as imagens inseridas nos documentos (imagine uma imagem dentro de um PDF), bem como imagens encontradas na fonte de dados, por exemplo um arquivo JPG.

## <a name="check-indexer-status"></a>Checar o status do indexador

Depois que o indexador for definido, ele é executado automaticamente quando você enviar a solicitação. Dependendo de quais habilidades cognitivas definida, a indexação pode demorar mais do que o esperado. Para descobrir se o indexador ainda está em execução, use o método `GetStatus`.

```csharp
try
{
    IndexerExecutionInfo demoIndexerExecutionInfo = serviceClient.Indexers.GetStatus(indexer.Name);

    switch (demoIndexerExecutionInfo.Status)
    {
        case IndexerStatus.Error:
            Console.WriteLine("Indexer has error status");
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
catch (Exception e)
{
    // Handle exception
}
```

`IndexerExecutionInfo` representa o status atual e o histórico de execução de um indexador.

Os avisos são comuns com algumas combinações de arquivo e a habilidade de origem e sempre não indicam um problema. Neste tutorial, os avisos são benignos (por exemplo, nenhuma entrada de texto dos arquivos JPEG).
 
## <a name="query-your-index"></a>Consultar o índice

Depois de terminar de indexação, você pode executar consultas que retornam o conteúdo dos campos individuais. Por padrão, o Azure Search retorna os 50 melhores resultados. Os dados de exemplo serão pequenos para que o padrão funciona bem. No entanto, ao trabalhar com grandes conjuntos de dados, você precisará incluir parâmetros na cadeia de caracteres de consulta para retornar mais resultados. Para obter instruções, consulte [como página de resultados do Azure Search](search-pagination-page-layout.md).

Como uma etapa de verificação, consulte o índice para todos os campos.

```csharp
DocumentSearchResult<DemoIndex> results;

ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*");
}
catch (Exception e)
{
    // Handle exception
}
```

`CreateSearchIndexClient` cria um novo `SearchIndexClient` usando valores que estão armazenados no arquivo de configuração do aplicativo (appsettings.json). Observe que a chave de API de consulta do serviço de pesquisa é usada, e não a chave do administrador.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

A saída é o esquema de índice, com o nome, tipo e atributos de cada campo.

Enviar uma segunda consulta para `"*"` para retornar todo o conteúdo de um único campo, como `organizations`.

```csharp
SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
}
catch (Exception e)
{
    // Handle exception
}
```

Repita para campos adicionais: conteúdo, idioma, frases-chave e organizações neste exercício. Você pode retornar vários campos via `$select` usando uma lista delimitada por vírgulas.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Redefinir e execute novamente

Nos primeiros estágios experimentais de desenvolvimento, a abordagem mais prática para iterações de design é excluir os objetos do Azure Search e permitir que seu código os reconstrua. Nomes de recurso são exclusivos. Excluir um objeto permite que você recriá-la usando o mesmo nome.

Este tutorial abordou a verificação de indexadores e índices existentes e sua exclusão, caso eles já existam, para que você possa executar novamente seu código.

Você também pode usar o portal para excluir índices, indexadores e conjuntos de qualificações.

Como seu código amadurece, talvez queira refinar uma estratégia de reconstrução. Para saber mais, confira [Como recompilar um índice](search-howto-reindex.md).

## <a name="takeaways"></a>Observações

Este tutorial demonstrou as etapas básicas para a criação de um pipeline de indexação enriquecido durante a criação de partes dos componentes: uma fonte de dados, o conjunto de qualificações, o índice e o indexador.

[Predefinidos habilidades](cognitive-search-predefined-skills.md) foram introduzidas, junto com a definição de conjunto de qualificações e a mecânica de encadear habilidades por meio de entradas e saídas. Você também aprendeu que `outputFieldMappings` no indexador definição é necessária para roteamentos valores enriquecidos do pipeline em um índice de pesquisado em um serviço do Azure Search.

Por fim, você aprendeu como resultados de teste e reinicie o sistema para obter mais iterações. Você aprendeu que emitir consultas em relação ao índice retorna a saída criada pelo pipeline de indexação enriquecido. Você também aprendeu como verificar o status do indexador e quais objetos a serem excluídos antes de executar novamente um pipeline.

## <a name="clean-up-resources"></a>Limpar recursos

A maneira mais rápida para limpar depois de um tutorial é excluindo o grupo de recursos que contém o serviço do Azure Search e o serviço de Blob do Azure. Supondo que você colocar ambos os serviços no mesmo grupo, exclua o grupo de recursos agora para excluir permanentemente todo o conteúdo, incluindo os serviços e qualquer conteúdo armazenado que você tenha criado para este tutorial. No portal, o nome do grupo de recurso está na página de Visão geral de cada serviço.

## <a name="next-steps"></a>Próximas etapas

Personalizar ou estender o pipeline com qualificações personalizadas. Criando uma habilidade personalizada e adicionando-a a um conjunto de qualificações permite que você carregue texto ou análise de imagem que você escreve por conta própria.

> [!div class="nextstepaction"]
> [Exemplo: Como criar uma habilidade personalizada para a pesquisa cognitiva](cognitive-search-create-custom-skill-example.md)
