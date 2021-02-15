---
title: Criar um conjunto de habilidades
titleSuffix: Azure Cognitive Search
description: Defina as etapas de extração de dados, processamento de idioma natural ou análise de imagem para enriquecer e extrair informações estruturadas de seus dados para uso no Azure Pesquisa Cognitiva.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 39a7c92ca6c83684658cf767722698806ed994ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935442"
---
# <a name="how-to-create-a-skillset-in-an-ai-enrichment-pipeline-in-azure-cognitive-search"></a>Como criar um conconhecimento em um pipeline de enriquecimento de ia no Azure Pesquisa Cognitiva 

![estágios do indexador](media/cognitive-search-defining-skillset/indexer-stages-skillset.png "estágios do indexador")

Um qualificable define as operações que extraem e enriquecem os dados para torná-los pesquisáveis. Um configurador de habilidades é executado depois que o conteúdo do texto e da imagem é extraído dos documentos de origem e, depois de todos os campos do documento de origem, são mapeados para os campos de destino em um índice ou repositório de conhecimento.

Um grupo de qualificações contém uma ou mais *habilidades cognitivas* que representam uma operação de enriquecimento específica, como a conversão de texto, a extração de frases-chave ou a execução de reconhecimento óptico de caracteres de um arquivo de imagem. Para criar um qualificable, você pode usar [habilidades internas](cognitive-search-predefined-skills.md) da Microsoft ou habilidades personalizadas que contêm modelos ou lógica de processamento que você fornece (consulte [o exemplo: criando uma habilidade personalizada em um pipeline de enriquecimento de ia](cognitive-search-create-custom-skill-example.md) para obter mais informações).

Neste artigo, você aprenderá a criar um pipeline de enriquecimento para as habilidades que quiser usar. Um contratador de qualificações é anexado a um [indexador](search-indexer-overview.md)pesquisa cognitiva do Azure. Uma parte do design do pipeline, abordada neste artigo, é a criação do próprio conjunto de habilidades. 

> [!NOTE]
> Outra parte do design do pipeline, abordada na [próxima etapa](#next-step), é a especificação de um indexador. Uma definição do indexador inclui uma referência ao conjunto de habilidades, além dos mapeamentos de campo usados para conectar as entradas às saídas no índice de destino.

Pontos principais a serem lembrados:

+ Você pode ter apenas um conjunto de habilidades por indexador.
+ Um conjunto de qualificações precisa ter pelo menos uma habilidade.
+ É possível criar várias habilidades do mesmo tipo (por exemplo, variantes de uma habilidade de análise de imagem).

## <a name="begin-with-the-end-in-mind"></a>Comece pensando no fim

Uma etapa inicial recomendada é decidir quais dados devem ser extraídos de seus dados brutos e como você deseja usar esses dados em uma solução de pesquisa. Criar uma ilustração de todo o pipeline de enriquecimento pode ajudar a identificar as etapas necessárias.

Suponha que você esteja interessado em processar um conjunto de comentários de um analista financeiro. Para cada arquivo, você deseja extrair os nomes das empresas e o sentimento geral dos comentários. Você também pode querer escrever um enriquecedor personalizado que usa serviço de Pesquisa de Entidade do Bing para encontrar informações adicionais sobre as empresas, como o tipo de negócios em que estão envolvidas. Essencialmente, você deseja extrair informações como as seguintes, indexadas para cada documento:

| texto de registro | empresas | sentimento | descrições das empresas |
|--------|-----|-----|-----|
|registro de amostra| ["Microsoft", "LinkedIn"] | 0,99 | ["A Microsoft Corporation é uma empresa de tecnologia multinacional americana...", "O LinkedIn é uma rede social concentrada em negócios e empregos..."]

O diagrama a seguir ilustra um pipeline de enriquecimento hipotético:

![Um pipeline de enriquecimento hipotético](media/cognitive-search-defining-skillset/sample-skillset.png "Um pipeline de enriquecimento hipotético")


Depois de ter uma boa ideia do que deseja conquistar com o pipeline, você poderá expressar o conjunto de habilidades que fornece estas etapas. Funcionalmente, o contratador de habilidades é expresso quando você carrega a definição do indexador no Azure Pesquisa Cognitiva. Para saber mais sobre como carregar o indexador, consulte a [documentação do indexador](/rest/api/searchservice/create-indexer).


No diagrama, a etapa *quebra de documento* acontece automaticamente. Essencialmente, o Azure Pesquisa Cognitiva sabe como abrir arquivos conhecidos e cria um campo de *conteúdo* que contém o texto extraído de cada documento. As caixas brancas são enriquecedores internos e a caixa pontilhada "Pesquisa de Entidade do Bing" representa um enriquecedor personalizado que você está criando. Conforme ilustrado, o conjunto de habilidades contém três habilidades.

## <a name="skillset-definition-in-rest"></a>Definição de conjunto de habilidades em RET

Um conjunto de habilidades é definido como uma matriz de habilidades. Cada uma delas define a origem de suas entradas e o nome das saídas produzidas. Usando a [API REST de Criação de Conjunto de Habilidades](/rest/api/searchservice/create-skillset), é possível definir um conjunto de habilidades correspondente ao diagrama anterior: 

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
```

```json
{
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
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
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "Calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
  ]
}
```

## <a name="create-a-skillset"></a>Criar um conjunto de habilidades

Quando cria um conjunto de habilidades, você pode fornecer uma descrição que ele seja autodocumentado. A descrição é opcional, mas é útil para você estar ciente do que o conjunto de habilidades faz. Como o conjunto de habilidades é um documento JSON, que não permite comentários, use um elemento `description` para fazer isso.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

A próxima parte do conjunto de habilidades é a matriz de habilidades. Você pode pensar em cada habilidade como um elemento primitivo de enriquecimento. Cada uma delas executa uma pequena tarefa no pipeline de enriquecimento. Cada uma usa uma entrada (ou um conjunto de entradas) e retorna algumas saídas. As próximas seções concentram-se em como especificar habilidades internas e personalizadas, encadeando habilidades em referências de entrada e saída. As entradas podem vir dos dados de origem ou de outra habilidade. As saídas podem ser mapeadas para um campo em um índice de pesquisa ou usadas como entrada de uma habilidade downstream.

## <a name="add-built-in-skills"></a>Adicionar habilidades internas

Vamos examinar a primeira habilidade, que é a habilidade interna de reconhecimento de [entidade](cognitive-search-skill-entity-recognition.md):

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
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
    }
```

* Todas as habilidades internas têm `odata.type` Propriedades, `input` e `output` . Propriedades específicas da habilidade fornecem informações adicionais aplicáveis a ela. Para o reconhecimento de entidade, `categories` é uma entidade em um conjunto fixo de tipos de entidade que o modelo pré-treinado pode reconhecer.

* Cada habilidade deve ter um ```"context"```. O contexto representa o nível no qual as operações ocorrem. Na habilidade acima, o contexto é o documento inteiro, o que significa que a habilidade de reconhecimento de entidade é chamada uma vez por documento. As saídas também são produzidas nesse nível. Mais especificamente, ```"organizations"``` são gerados como um membro de ```"/document"```. Nas habilidades downstream, você pode se referir a essas informações recém-criadas como ```"/document/organizations"```.  Se o campo ```"context"``` não for definido explicitamente, o contexto padrão será o documento.

* A habilidade tem uma entrada denominada "text", com a entrada de origem definida como ```"/document/content"```. A habilidade (reconhecimento de entidade) opera no campo de *conteúdo* de cada documento, que é um campo padrão criado pelo indexador de blob do Azure. 

* A habilidade tem uma saída chamada ```"organizations"```. As saídas existem somente durante o processamento. Para encadear essa saída à entrada de uma habilidade downstream, faça referência à saída como ```"/document/organizations"```.

* Para um documento específico, o valor de ```"/document/organizations"``` é uma matriz de organizações extraída do texto. Por exemplo:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

Em algumas situações, é necessário referenciar cada elemento de uma matriz separadamente. Por exemplo, suponha que você queira passar cada elemento de ```"/document/organizations"``` separadamente para outra habilidade (como o enriquecedor de Pesquisa de Entidade do Bing personalizado). Você pode referenciar cada elemento da matriz adicionando um asterisco ao caminho: ```"/document/organizations/*"``` 

A segunda habilidade de extração de sentimento segue o mesmo padrão que o primeiro enriquecedor. Ela usa ```"/document/content"``` como entrada e retorna uma pontuação de sentimento para cada instância de conteúdo. Como você não definiu o ```"context"``` campo explicitamente, a saída (mySentiment) agora é um filho de ```"/document"```.

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
```

## <a name="add-a-custom-skill"></a>Adicionar uma habilidade personalizada

Lembre-se da estrutura do enriquecedor de Pesquisa de Entidade do Bing personalizado:

```json
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "This skill calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
```

Essa definição é uma [habilidade personalizada](cognitive-search-custom-skill-web-api.md) que chama uma API da Web como parte do processo de enriquecimento. Para cada organização identificada pelo reconhecimento de entidade, essa habilidade chama uma API da Web para localizar a descrição dessa organização. A orquestração de quando chamar a API Web e como transmitir as informações recebidas é feita internamente pelo mecanismo de enriquecimento. No entanto, a inicialização necessária para chamar essa API personalizada deve ser fornecida no JSON (como URI, httpHeaders e as entradas esperadas). Para obter diretrizes sobre a criação de uma API Web personalizada para o pipeline de enriquecimento, confira [Como definir uma interface personalizada](cognitive-search-custom-skill-interface.md).

Observe que o campo "context" está definido como ```"/document/organizations/*"``` com um asterisco, o que significa que a etapa de enriquecimento é chamada *para cada* organização em ```"/document/organizations"```. 

A saída, nesse caso a descrição de uma empresa, é gerada para cada organização identificada. Ao fazer referência à descrição em uma etapa downstream (por exemplo, na extração de frases-chave), você usaria o caminho ```"/document/organizations/*/description"``` para fazer isso. 

## <a name="add-structure"></a>Adicionar estrutura

O conjunto de habilidades gera informações estruturadas usando dados não estruturados. Considere o seguinte exemplo:

*"Em seu quarto trimestre, a Microsoft registrou $1100000000 em receita do LinkedIn, a empresa de rede social comprada no ano passado. A aquisição permite que a Microsoft Combine recursos do LinkedIn com seus recursos do CRM e do Office. Os acionistas estão empolgados com o progresso até agora. "*

Um resultado provável seria uma estrutura gerada semelhante à ilustração a seguir:

![Estrutura de saída de exemplo](media/cognitive-search-defining-skillset/enriched-doc.png "Estrutura de saída de exemplo")

Até agora, essa estrutura tem sido somente interno, somente de memória e usada somente nos índices de Pesquisa Cognitiva do Azure. A adição de uma loja de conhecimento oferece uma maneira de salvar os aprimoramentos moldados para uso fora da pesquisa.

## <a name="add-a-knowledge-store"></a>Adicionar uma loja de conhecimento

O [repositório de conhecimento](knowledge-store-concept-intro.md) é um recurso do Azure pesquisa cognitiva para salvar seu documento aprimorado. Uma loja de conhecimento que você cria, apoiada por uma conta de armazenamento do Azure, é o repositório no qual seus dados aprimorados ficam. 

Uma definição de repositório de conhecimento é adicionada a um conconhecedor. Para obter uma explicação do processo inteiro, consulte [criar uma loja de conhecimento em REST](knowledge-store-create-rest.md).

```json
"knowledgeStore": {
  "storageConnectionString": "<an Azure storage connection string>",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
          "storageContainer": "containername",
          "source": "/document/EnrichedShape/",
          "key": "/document/Id"
        }
      ]
    }
  ]
}
```

Você pode optar por salvar os documentos aprimorados como tabelas com relações hierárquicas preservadas ou como documentos JSON no armazenamento de BLOBs. A saída de qualquer uma das habilidades no conferent pode ser originada como a entrada para a projeção. Se você pretende projetar os dados em uma forma específica, a [habilidade](cognitive-search-skill-shaper.md) atualizada do modelador agora pode modelar tipos complexos para uso. 

<a name="next-step"></a>

## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com o pipeline de enriquecimento e com os conjuntos de habilidades, continue com [Como referenciar anotações em um conjunto de habilidades](cognitive-search-concept-annotations-syntax.md) ou [Como mapear saídas para campos em um índice](cognitive-search-output-field-mapping.md).