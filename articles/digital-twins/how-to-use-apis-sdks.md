---
title: Usar as APIs e os SDKs dos Gêmeos Digitais do Azure
titleSuffix: Azure Digital Twins
description: Veja como trabalhar com as APIs dos Gêmeos Digitais do Azure, incluindo por meio do SDK.
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f4f3fc8c928cd284088cc51120f1a7b485b4fac0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104595338"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>Usar as APIs e os SDKs dos Gêmeos Digitais do Azure

Os Gêmeos Digitais do Azure contam com **APIs de painel de controle** e **APIs de plano de dados** para gerenciar sua instância e os elementos dela. 
* As APIs do painel de controle são APIs do [ARM (Azure Resource Manager)](../azure-resource-manager/management/overview.md) e englobam operações de gerenciamento de recursos, por exemplo, criar e excluir a instância. 
* As APIs de plano de dados são APIs dos Gêmeos Digitais do Azure e são usadas para operações de gerenciamento de dados, por exemplo, gerenciar modelos, gêmeos e o grafo.

Este artigo fornece uma visão geral das APIs disponíveis, bem como os métodos para interagir com elas. Você pode usar as APIs REST diretamente com os Swaggers associados a elas (por meio de uma ferramenta como o [Postman](how-to-use-postman.md)) ou por meio de um SDK.

## <a name="overview-control-plane-apis"></a>Visão geral: APIs de painel de controle

As APIs de painel de controle são APIs do [ARM](../azure-resource-manager/management/overview.md) usadas para gerenciar sua instância dos Gêmeos Digitais do Azure como um todo, de modo que englobam operações como criar e excluir toda a sua instância. Você também as usará para criar e excluir pontos de extremidade.

A versão mais atual da API de painel de controle é _**2020-12-01**_.

Para usar as APIs de painel de controle:
* Você pode chamar as APIs diretamente referenciando a pasta mais recente do Swagger no [repositório Swagger do painel de controle](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/stable). Essa pasta também inclui outra pasta de exemplos de uso.
* Atualmente, você pode acessar os SDKs das APIs de controle em...
  - [ **.NET (C#)** ](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/) ([referência [gerada automaticamente]](/dotnet/api/overview/azure/digitaltwins/management)) ([origem](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins))
  - [**Java**](https://search.maven.org/search?q=a:azure-mgmt-digitaltwins) ([referência [gerada automaticamente]](/java/api/overview/azure/digitaltwins)) ([origem](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins))
  - [**JavaScript**](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([origem](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins))
  - [**Python**](https://pypi.org/project/azure-mgmt-digitaltwins/) ([origem](https://github.com/Azure/azure-sdk-for-python/tree/release/v3/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [**Go**](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/services/digitaltwins/mgmt) ([origem](https://github.com/Azure/azure-sdk-for-go/tree/master/services/digitaltwins/mgmt))

Você também pode experimentar as APIs de painel de controle interagindo com os Gêmeos Digitais do Azure por meio do [portal do Azure](https://portal.azure.com) e da [CLI](how-to-use-cli.md).

## <a name="overview-data-plane-apis"></a>Visão geral: APIs de plano de dados

As APIs de plano de dados são as APIs dos Gêmeos Digitais do Azure usadas para gerenciar os elementos dentro de sua instância dos Gêmeos Digitais do Azure. Elas incluem operações como criar rotas, carregar modelos, criar relações e gerenciar gêmeos. Elas podem ser divididas, de modo geral, nas seguintes categorias:
* **DigitalTwinModels** – a categoria DigitalTwinModels contém APIs para gerenciar os [modelos](concepts-models.md) em uma instância dos Gêmeos Digitais do Azure. As atividades de gerenciamento incluem carregamento, validação, recuperação e exclusão de modelos criados na DTDL.
* **DigitalTwins** – a categoria DigitalTwins contém as APIs que permitem que os desenvolvedores criem, modifiquem e excluam [gêmeos digitais](concepts-twins-graph.md) e as relações deles em uma instância dos Gêmeos Digitais do Azure.
* **Consulta** – a categoria Consulta permite que os desenvolvedores [encontrem conjuntos de gêmeos digitais no grafo de gêmeos](how-to-query-graph.md) entre diferentes relações.
* **Rotas de Eventos** – a categoria Rotas de Eventos contém APIs para [rotear dados](concepts-route-events.md) pelo sistema e para serviços downstream.

A versão mais atual da API de plano de dados é _**2020-10-31**_.

Para usar as APIs de plano de dados:
* É possível chamar as APIs diretamente...
   - referenciando a pasta do Swagger mais recente no [repositório Swagger do plano de dados](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Essa pasta também inclui outra pasta de exemplos de uso. 
   - conferindo a [documentação de referência da API](/rest/api/azure-digitaltwins/).
* Você pode usar o **SDK do .NET (C#)** . Para usar o SDK do .NET...
   - você pode exibir e adicionar o pacote do NuGet: [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core). 
   - você pode conferir a [documentação de referência do SDK](/dotnet/api/overview/azure/digitaltwins/client).
   - você pode encontrar a origem do SDK, incluindo uma pasta de exemplos, no GitHub: [biblioteca de clientes dos Gêmeos Digitais do Azure IoT para .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). 
   - você pode ver informações detalhadas e exemplos de uso prosseguindo para a seção [*SDK do .NET (C#) (plano de dados)* ](#net-c-sdk-data-plane) deste artigo.
* Você pode usar o **SDK do Java**. Paras usar o SDK do Java...
   - você pode exibir e instalar o pacote do Maven: [`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)
   - você pode conferir a [documentação de referência do SDK](/java/api/overview/azure/digitaltwins/client)
   - você pode encontrar a origem do SDK no GitHub: [biblioteca de clientes dos Gêmeos Digitais do Azure IoT para Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* Você pode usar o **SDK do JavaScript**. Para usar o SDK do JavaScript...
   - você pode exibir e instalar o pacote do npm: [Biblioteca de clientes principal dos Gêmeos Digitais do Azure para JavaScript](https://www.npmjs.com/package/@azure/digital-twins-core).
   - você pode conferir a [documentação de referência do SDK](/javascript/api/@azure/digital-twins-core/).
   - você pode encontrar a origem do SDK no GitHub: [Biblioteca de clientes principal dos Gêmeos Digitais do Azure para JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)
* Você pode usar o **SDK do Python**. Para usar o SDK do Python...
   - você pode exibir e instalar o pacote do PyPi: [Biblioteca de clientes principal dos Gêmeos Digitais do Azure para Python](https://pypi.org/project/azure-digitaltwins-core/).
   - você pode conferir a [documentação de referência do SDK](/python/api/azure-digitaltwins-core/azure.digitaltwins.core).
   - você pode encontrar a origem do SDK no GitHub: [Biblioteca de clientes principal dos Gêmeos Digitais do Azure para Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* Você pode gerar um SDK para outra linguagem usando o AutoRest. Siga as instruções em [*Como criar SDKs personalizados para os Gêmeos Digitais do Azure com AutoRest*](how-to-create-custom-sdks.md).

Você também pode experimentar as APIs de plano de dados interagindo com os Gêmeos Digitais do Azure por meio da [CLI](how-to-use-cli.md).

## <a name="net-c-sdk-data-plane"></a>SDK do .NET (C#) (plano de dados)

O SDK do .NET (C#) dos Gêmeos Digitais do Azure faz parte do SDK do Azure para .NET. Ele é de software livre e é baseado nas APIs de plano de dados dos Gêmeos Digitais do Azure.

> [!NOTE]
> Para saber mais sobre o design de SDKs, confira os [princípios gerais de design para SDKs do Azure](https://azure.github.io/azure-sdk/general_introduction.html) e as [diretrizes de design específicas do .NET](https://azure.github.io/azure-sdk/dotnet_introduction.html).

Para usar o SDK, inclua o pacote NuGet **Azure.DigitalTwins.Core** com o projeto. Você também precisará da última versão do pacote **Azure.Identity**. No Visual Studio, você pode adicionar esses pacotes usando o Gerenciador de Pacotes NuGet (acessado por meio de *Ferramentas > Gerenciador de Pacotes NuGet > Gerenciar Pacotes NuGet para a Solução*). Como alternativa, você pode usar a ferramenta de linha de comando do .NET com os comandos encontrados nos links do pacote NuGet abaixo para adicioná-los ao projeto:
* [**Azure.DigitalTwins.Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core). Este é o pacote para o [SDK dos Gêmeos Digitais do Azure para .NET](/dotnet/api/overview/azure/digitaltwins/client). 
* [**Azure.Identity**](https://www.nuget.org/packages/Azure.Identity). Esta biblioteca fornece ferramentas para ajudar com a autenticação no Azure.

Para obter um passo a passo detalhado de como usar as APIs na prática, confira o [*Tutorial: codificar um aplicativo cliente*](tutorial-code.md). 

### <a name="net-sdk-usage-examples"></a>Exemplos de uso do SDK do .NET

Veja alguns exemplos de código que ilustram o uso do SDK do .NET.

Autenticar-se no serviço:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_basic":::

[!INCLUDE [Azure Digital Twins: local credentials note](../../includes/digital-twins-local-credentials-note.md)] 

Carregar um modelo:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

Listar modelos:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

Criar gêmeos:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

Consultar gêmeos e percorrer os resultados em loop:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

Confira o [*Tutorial: Codificar um aplicativo cliente*](tutorial-code.md) para ver um passo a passo desse código de aplicativo de exemplo. 

Você também pode encontrar exemplos adicionais no [Repositório do GitHub para o SDK do .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples).

#### <a name="serialization-helpers"></a>Auxiliares de serialização

Auxiliares de serialização são funções auxiliares disponíveis no SDK para criar ou desserializar rapidamente dados de gêmeos para ter acesso a informações básicas. Como os métodos principais do SDK retornam dados de gêmeos como JSON por padrão, pode ser útil usar essas classes auxiliares para dividir mais esses dados.

As classes auxiliares disponíveis são:
* `BasicDigitalTwin`: representa genericamente os dados principais de um gêmeo digital
* `BasicDigitalTwinComponent`: representa genericamente um componente nas propriedades `Contents` de um `BasicDigitalTwin`
* `BasicRelationship`: representa genericamente os dados principais de uma relação
* `DigitalTwinsJsonPropertyName`: contém as constantes de cadeia de caracteres para uso na serialização e na desserialização JSON para tipos de gêmeos digitais personalizados

##### <a name="deserialize-a-digital-twin"></a>Desserializar um gêmeo digital

Você sempre pode desserializar dados de gêmeos usando a biblioteca JSON de sua escolha, como `System.Text.Json` ou `Newtonsoft.Json`. As classes auxiliares podem tornar mais conveniente o acesso básico a um gêmeo.

A classe auxiliar `BasicDigitalTwin` também fornece acesso a propriedades definidas no gêmeo por meio de um `Dictionary<string, object>`. Para listar as propriedades do gêmeo, você pode usar:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin":::

> [!NOTE]
> `BasicDigitalTwin` usa atributos `System.Text.Json`. Para usar `BasicDigitalTwin` com o [DigitalTwinsClient](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient?view=azure-dotnet&preserve-view=true), inicialize o cliente com o construtor padrão ou, se desejar personalizar a opção do serializador, use o [JsonObjectSerializer](/dotnet/api/azure.core.serialization.jsonobjectserializer?view=azure-dotnet&preserve-view=true).

##### <a name="create-a-digital-twin"></a>Criar um gêmeo digital

Usando a classe `BasicDigitalTwin`, você pode preparar dados para criar uma instância de gêmeo:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

O código acima é equivalente à seguinte variante "manual":

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

##### <a name="deserialize-a-relationship"></a>Desserializar uma relação

Você sempre pode desserializar dados de relação para um tipo de sua escolha. Para ter acesso básico a uma relação, use o tipo `BasicRelationship`.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

A classe auxiliar `BasicRelationship` também fornece acesso a propriedades definidas na relação por meio de um `IDictionary<string, object>`. Para listar propriedades, você pode usar:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="ListRelationshipProperties":::

##### <a name="create-a-relationship"></a>Criar uma relação

Usando a classe `BasicRelationship`, você também pode preparar dados para criar relações em uma instância de gêmeo:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="CreateRelationship_short":::

##### <a name="create-a-patch-for-twin-update"></a>Criar um patch para atualização de um gêmeo

As chamadas de atualização para gêmeos e relações usam a estrutura de [Patch JSON](http://jsonpatch.com/). Para criar listas de operações de Patch JSON, você pode usar o `JsonPatchDocument` conforme mostrado abaixo.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

## <a name="general-apisdk-usage-notes"></a>Notas de uso gerais sobre a API/SDK

> [!NOTE]
> Observe que, atualmente, os Gêmeos Digitais do Azure não dão suporte ao **CORS (compartilhamento de recursos entre origens)** . Para obter mais informações sobre o impacto e as estratégias de resolução, confira a seção [*CORS (compartilhamento de recursos entre origens)*](concepts-security.md#cross-origin-resource-sharing-cors) em *Conceitos: segurança para soluções dos Gêmeos Digitais do Azure*.

A lista a seguir fornece detalhes adicionais e diretrizes gerais para o uso de APIs e SDKs.

* Você pode usar uma ferramenta de testes de HTTP REST, como o Postman, para fazer chamadas diretas para as APIs dos Gêmeos Digitais do Azure. Para saber mais sobre esse processo, confira [*Como fazer solicitações com o Postman*](how-to-use-postman.md).
* Para usar o SDK, crie uma instância da classe `DigitalTwinsClient`. O construtor requer credenciais que podem ser obtidas com uma variedade de métodos de autenticação no pacote `Azure.Identity`. Para saber mais sobre `Azure.Identity`, confira a [documentação do namespace](/dotnet/api/azure.identity). 
* Você pode considerar o `InteractiveBrowserCredential` útil para começar, mas há várias outras opções, incluindo credenciais para [identidade gerenciada](/dotnet/api/azure.identity.interactivebrowsercredential), que você provavelmente usará para autenticar as [funções do Azure configuradas com o MSI](../app-service/overview-managed-identity.md?tabs=dotnet) nos Gêmeos Digitais do Azure. Para saber mais sobre `InteractiveBrowserCredential`, confira a [documentação da classe](/dotnet/api/azure.identity.interactivebrowsercredential).
* Todas as chamadas à API de serviço são expostas como funções de membro na classe `DigitalTwinsClient`.
* Todas as funções de serviço existem em versões síncronas e assíncronas.
* Todas as funções de serviço geram uma exceção para qualquer status retornado como 400 ou acima. Encapsule as chamadas em uma seção `try` e capture pelo menos `RequestFailedExceptions`. Confira [aqui](/dotnet/api/azure.requestfailedexception) mais informações sobre esse tipo de exceção.
* A maioria dos métodos de serviço retorna `Response<T>` ou (`Task<Response<T>>` para as chamadas assíncronas), em que `T` é a classe do objeto retornado para a chamada de serviço. A classe [`Response`](/dotnet/api/azure.response-1) encapsula o retorno do serviço e apresenta valores retornados no campo `Value`.  
* Métodos de serviço com resultados paginados retornam `Pageable<T>` ou `AsyncPageable<T>` como resultados. Para saber mais sobre a classe `Pageable<T>`, confira [aqui](/dotnet/api/azure.pageable-1). Para saber mais sobre `AsyncPageable<T>`, confira [aqui](/dotnet/api/azure.asyncpageable-1).
* Você pode iterar em resultados paginados usando um loop `await foreach`. Saiba mais sobre esse processo [aqui](/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8).
* O SDK subjacente é `Azure.Core`. Confira a [documentação do namespace do Azure](/dotnet/api/azure) para obter a referência sobre os tipos e a infraestrutura do SDK.

Os métodos de serviço retornam objetos fortemente tipados sempre que possível. No entanto, como os Gêmeos Digitais do Azure se baseiam em modelos configurados de maneira personalizada pelo usuário no runtime (por meio de modelos de DTDL carregados para o serviço), muitas APIs de serviço usam e retornam dados de gêmeos no formato JSON.

## <a name="monitor-api-metrics"></a>Monitorar métricas da API

As métricas da API, como solicitações, latência e taxa de falha, podem ser exibidas no [portal do Azure](https://portal.azure.com/). 

Na página inicial do portal, procure sua instância dos Gêmeos Digitais do Azure para obter os detalhes. Selecione a opção **Métricas** no menu da instância dos Gêmeos Digitais do Azure para abrir a página de *Métricas*.

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Captura de tela que mostra a página de métricas dos Gêmeos Digitais do Azure":::

Nela, você pode ver as métricas de sua instância e criar exibições personalizadas.

## <a name="next-steps"></a>Próximas etapas

Veja como fazer solicitações diretas para as APIs usando o Postman:
* [*Como fazer solicitações com o Postman*](how-to-use-postman.md)

Ou pratique o uso do SDK do .NET criando um aplicativo cliente com este tutorial:
* [*Tutorial: Codificar um aplicativo cliente*](tutorial-code.md)