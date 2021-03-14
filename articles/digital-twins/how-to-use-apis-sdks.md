---
title: Usar as APIs e os SDKs dos Gêmeos Digitais do Azure
titleSuffix: Azure Digital Twins
description: Veja como trabalhar com as APIs do gêmeos digital do Azure, incluindo o por meio do SDK.
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: cab32a6c2835dc283a169f58c79ff54e7925467b
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554234"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>Usar as APIs e os SDKs dos Gêmeos Digitais do Azure

O Azure digital gêmeos vem equipado com APIs de **plano de controle** e APIs de **plano de dados** para gerenciar sua instância e seus elementos. 
* As APIs do plano de controle são APIs [de Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md) e abrangem operações de gerenciamento de recursos, como criar e excluir sua instância. 
* As APIs do plano de dados são APIs de gêmeos digitais do Azure e são usadas para operações de gerenciamento de dados, como gerenciar modelos, gêmeos e o grafo.

Este artigo fornece uma visão geral das APIs disponíveis e os métodos para interagir com elas. Você pode usar as APIs REST diretamente com seus swaggers associados (por meio de uma ferramenta como o [postmaster](how-to-use-postman.md)) ou por meio de um SDK.

## <a name="overview-control-plane-apis"></a>Visão geral: APIs do plano de controle

As APIs do plano de controle são APIs [ARM](../azure-resource-manager/management/overview.md) usadas para gerenciar sua instância do gêmeos digital do Azure como um todo, para que elas abranjam operações como criar ou excluir toda a sua instância. Você também vai usá-los para criar e excluir pontos de extremidade.

A versão mais atual da API do plano de controle é _**2020-12-01**_.

Para usar as APIs do plano de controle:
* Você pode chamar as APIs diretamente referenciando a pasta Swagger mais recente no [repositório Swagger do plano de controle](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/stable). Essa pasta também inclui uma pasta de exemplos que mostram o uso.
* Atualmente, você pode acessar SDKs para APIs de controle no...
  - [**.Net (C#)**](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/) ([referência [gerada automaticamente]](/dotnet/api/overview/azure/digitaltwins/management)) ([origem](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins))
  - [**Java**](https://search.maven.org/search?q=a:azure-mgmt-digitaltwins) ([referência [gerada automaticamente]](/java/api/overview/azure/digitaltwins)) ([origem](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins))
  - [**JavaScript**](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([origem](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins))
  - [**Python**](https://pypi.org/project/azure-mgmt-digitaltwins/) ([origem](https://github.com/Azure/azure-sdk-for-python/tree/release/v3/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [**Ir**](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/digitaltwins/mgmt/2020-10-31/digitaltwins) para ([origem](https://github.com/Azure/azure-sdk-for-go/tree/master/services/digitaltwins/mgmt/2020-10-31/digitaltwins))

Você também pode exercitar as APIs do plano de controle interagindo com o gêmeos digital do Azure por meio do [portal do Azure](https://portal.azure.com) e da [CLI](how-to-use-cli.md).

## <a name="overview-data-plane-apis"></a>Visão geral: APIs do plano de dados

As APIs do plano de dados são as APIs do Azure digital gêmeos usadas para gerenciar os elementos em sua instância do gêmeos digital do Azure. Eles incluem operações como a criação de rotas, o carregamento de modelos, a criação de relações e o gerenciamento de gêmeos. Elas podem ser divididas em larga escala nas seguintes categorias:
* **DigitalTwinModels** -a categoria DigitalTwinModels contém APIs para gerenciar os [modelos](concepts-models.md) em uma instância do gêmeos digital do Azure. As atividades de gerenciamento incluem carregamento, validação, recuperação e exclusão de modelos criados no DTDL.
* **DigitalTwins** -a categoria DigitalTwins contém as APIs que permitem que os desenvolvedores criem, modifiquem e excluam [gêmeos digitais](concepts-twins-graph.md) e suas relações em uma instância do gêmeos digital do Azure.
* **Consulta** -a categoria de consulta permite que os desenvolvedores [encontrem conjuntos de gêmeos digitais no grafo de entrelaçamento](how-to-query-graph.md) entre relações.
* **Rotas de eventos** – a categoria rotas de eventos contém APIs para [rotear dados](concepts-route-events.md), por meio do sistema e para serviços downstream.

A versão mais atual da API do plano de dados é _**2020-10-31**_.

Para usar as APIs do plano de dados:
* Você pode chamar as APIs diretamente, por...
   - referenciando a pasta Swagger mais recente no [repositório Swagger do plano de dados](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Essa pasta também inclui uma pasta de exemplos que mostram o uso. 
   - exibindo a [documentação de referência da API](/rest/api/azure-digitaltwins/).
* Você pode usar o **SDK do .net (C#)**. Para usar o SDK do .NET...
   - Você pode exibir e adicionar o pacote do NuGet: [Azure. DigitalTwins. Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core). 
   - Você pode exibir a [documentação de referência do SDK](/dotnet/api/overview/azure/digitaltwins/client).
   - Você pode encontrar a origem do SDK, incluindo uma pasta de exemplos, no GitHub: [biblioteca de cliente do Azure IOT digital gêmeos para .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). 
   - Você pode ver informações detalhadas e exemplos de uso, continuando para a seção [*SDK (plano de dados) do .net (C#)*](#net-c-sdk-data-plane) deste artigo.
* Você pode usar o **SDK do Java**. Para usar o SDK do Java...
   - Você pode exibir e instalar o pacote do Maven: [`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)
   - Você pode exibir a [documentação de referência do SDK](/java/api/overview/azure/digitaltwins/client)
   - Você pode encontrar a origem do SDK no GitHub: [biblioteca de cliente do Azure IOT digital gêmeos para Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* Você pode usar o **SDK do JavaScript**. Para usar o SDK do JavaScript...
   - Você pode exibir e instalar o pacote em NPM: [biblioteca de cliente principal do gêmeos do Azure Azure digital para JavaScript](https://www.npmjs.com/package/@azure/digital-twins-core).
   - Você pode exibir a [documentação de referência do SDK](/javascript/api/@azure/digital-twins-core/).
   - Você pode encontrar a origem do SDK no GitHub: [biblioteca de cliente do Microsoft Azure digital gêmeos Core para JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)
* Você pode usar o **SDK do Python**. Para usar o SDK do Python...
   - Você pode exibir e instalar o pacote em PyPi: [biblioteca de cliente principal do gêmeos do Azure Azure digital para Python](https://pypi.org/project/azure-digitaltwins-core/).
   - Você pode exibir a [documentação de referência do SDK](/python/api/azure-digitaltwins-core/azure.digitaltwins.core).
   - Você pode encontrar a origem do SDK no GitHub: [biblioteca de cliente do Microsoft Azure digital gêmeos Core para Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* Você pode gerar um SDK para outro idioma usando o REST. Siga as instruções em [*como criar SDKs personalizados para o Azure digital gêmeos com o REST*](how-to-create-custom-sdks.md).

Você também pode exercitar as APIs do plano de datas interagindo com o gêmeos digital do Azure por meio da [CLI](how-to-use-cli.md).

## <a name="net-c-sdk-data-plane"></a>SDK do .NET (C#) (plano de dados)

O SDK do .NET do Azure digital gêmeos (C#) faz parte do SDK do Azure para .NET. Ele é de software livre e baseia-se nas APIs do plano de dados do gêmeos digital do Azure.

> [!NOTE]
> Para obter mais informações sobre o design do SDK, consulte os [princípios de design geral para SDKs do Azure](https://azure.github.io/azure-sdk/general_introduction.html) e as diretrizes de design específicas do [.net](https://azure.github.io/azure-sdk/dotnet_introduction.html).

Para usar o SDK, inclua o pacote NuGet **Azure. DigitalTwins. Core** com seu projeto. Você também precisará da versão mais recente do pacote **Azure. Identity** . No Visual Studio, você pode adicionar esses pacotes usando o Gerenciador de pacotes NuGet (acessado por meio de *ferramentas > Gerenciador de pacotes nuget > gerenciar pacotes NuGet para solução*). Como alternativa, você pode usar a ferramenta de linha de comando do .NET com os comandos encontrados nos links de pacote NuGet abaixo para adicioná-los ao seu projeto:
* [**Azure.DigitalTwins.Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core). Este é o pacote para o [SDK dos Gêmeos Digitais do Azure para .NET](/dotnet/api/overview/azure/digitaltwins/client). 
* [**Azure.Identity**](https://www.nuget.org/packages/Azure.Identity). Esta biblioteca fornece ferramentas para ajudar com a autenticação no Azure.

Para obter um passo a passo detalhado sobre como usar as APIs na prática, consulte o [*tutorial: codificar um aplicativo cliente*](tutorial-code.md). 

### <a name="net-sdk-usage-examples"></a>Exemplos de uso do SDK do .NET

Aqui estão alguns exemplos de código que ilustram o uso do SDK do .NET.

Autentique no serviço:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_basic":::

[!INCLUDE [Azure Digital Twins: local credentials note](../../includes/digital-twins-local-credentials-note.md)] 

Carregar um modelo:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

Listar modelos:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

Criar gêmeos:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

Gêmeos de consulta e loop pelos resultados:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

Consulte o [*tutorial: codificar um aplicativo cliente*](tutorial-code.md) para obter um passo a passo deste código de aplicativo de exemplo. 

Você também pode encontrar exemplos adicionais no [repositório do GitHub para o SDK do .net (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples).

#### <a name="serialization-helpers"></a>Auxiliares de serialização

Os auxiliares de serialização são funções auxiliares disponíveis no SDK para criar ou desserializar rapidamente dados de entrelaçamento para acesso a informações básicas. Como os métodos principais do SDK retornam dados de vertical como JSON por padrão, pode ser útil usar essas classes auxiliares para dividir os dados em cima.

As classes auxiliares disponíveis são:
* `BasicDigitalTwin`: Representa genericamente os dados principais de um teledigital
* `BasicDigitalTwinComponent`: Representa genericamente um componente nas `Contents` Propriedades de um `BasicDigitalTwin`
* `BasicRelationship`: Representa genericamente os dados principais de uma relação
* `DigitalTwinsJsonPropertyName`: Contém as constantes de cadeia de caracteres para uso em serialização JSON e desserialização para tipos de texto digital personalizados

##### <a name="deserialize-a-digital-twin"></a>Desserializar um r digital

Você sempre pode desserializar dados de entrelaçamento usando a biblioteca JSON de sua escolha, como `System.Text.Json` ou `Newtonsoft.Json` . Para acesso básico a um número de informações, as classes auxiliares podem torná-las mais convenientes.

A `BasicDigitalTwin` classe auxiliar também fornece acesso a propriedades definidas em o "My", por meio de a `Dictionary<string, object>` . Para listar as propriedades de myQuery, você pode usar:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin":::

> [!NOTE]
> `BasicDigitalTwin` usa `System.Text.Json` atributos. Para usar `BasicDigitalTwin` com seu [DigitalTwinsClient](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient?view=azure-dotnet&preserve-view=true), você deve inicializar o cliente com o construtor padrão ou, se desejar personalizar a opção de serializador, use o [JsonObjectSerializer](/dotnet/api/azure.core.serialization.jsonobjectserializer?view=azure-dotnet&preserve-view=true).

##### <a name="create-a-digital-twin"></a>Criar um teledigital

Usando a `BasicDigitalTwin` classe, você pode preparar dados para criar uma instância de entrelaçar:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

O código acima é equivalente à seguinte variante "manual":

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

##### <a name="deserialize-a-relationship"></a>Desserializar uma relação

Você sempre pode desserializar dados de relação para um tipo de sua escolha. Para acesso básico a uma relação, use o tipo `BasicRelationship` .

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

A `BasicRelationship` classe auxiliar também fornece acesso a propriedades definidas na relação, por meio de um `IDictionary<string, object>` . Para listar Propriedades, você pode usar:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="ListRelationshipProperties":::

##### <a name="create-a-relationship"></a>Criar uma relação

Usando a `BasicRelationship` classe, você também pode preparar dados para criar relações em uma instância de entrelaçar:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="CreateRelationship_short":::

##### <a name="create-a-patch-for-twin-update"></a>Criar um patch para atualização de atualizações

As chamadas de atualização para gêmeos e relações usam a estrutura de [patch JSON](http://jsonpatch.com/) . Para criar listas de operações de patch JSON, você pode usar o `JsonPatchDocument` conforme mostrado abaixo.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

## <a name="general-apisdk-usage-notes"></a>Notas gerais de uso de API/SDK

> [!NOTE]
> Observe que o Azure digital gêmeos atualmente não dá suporte a **CORS (compartilhamento de recursos entre origens)**. Para obter mais informações sobre as estratégias de impacto e resolução, consulte a seção [*CORS (compartilhamento de recursos entre origens)*](concepts-security.md#cross-origin-resource-sharing-cors) de *Concepts: Security for Azure digital gêmeos Solutions*.

A lista a seguir fornece detalhes adicionais e diretrizes gerais para o uso de APIs e SDKs.

* Você pode usar uma ferramenta de teste de REST HTTP como o postmaster para fazer chamadas diretas para as APIs do Azure digital gêmeos. Para obter mais informações sobre esse processo, consulte [*instruções: fazer solicitações com o postmaster*](how-to-use-postman.md).
* Para usar o SDK, crie uma instância da `DigitalTwinsClient` classe. O construtor requer credenciais que podem ser obtidas com uma variedade de métodos de autenticação no `Azure.Identity` pacote. Para obter mais informações `Azure.Identity` , consulte a [documentação do namespace](/dotnet/api/azure.identity). 
* Você pode achar o `InteractiveBrowserCredential` útil ao começar, mas há várias outras opções, incluindo credenciais para [identidade gerenciada](/dotnet/api/azure.identity.interactivebrowsercredential), que você provavelmente usará para autenticar as [Azure Functions configuradas com o MSI](../app-service/overview-managed-identity.md?tabs=dotnet) no Azure digital gêmeos. Para obter mais informações `InteractiveBrowserCredential` , consulte a [documentação](/dotnet/api/azure.identity.interactivebrowsercredential)de sua classe.
* Todas as chamadas à API de serviço são expostas como funções de membro na `DigitalTwinsClient` classe.
* Todas as funções de serviço existem em versões síncronas e assíncronas.
* Todas as funções de serviço geram uma exceção para qualquer status de retorno de 400 ou acima. Certifique-se de encapsular chamadas em uma `try` seção e capturar pelo menos `RequestFailedExceptions` . Para obter mais informações sobre esse tipo de exceção, consulte [aqui](/dotnet/api/azure.requestfailedexception).
* A maioria dos métodos de serviço retorna `Response<T>` ou ( `Task<Response<T>>` para as chamadas assíncronas), em que `T` é a classe de objeto de retorno para a chamada de serviço. A [`Response`](/dotnet/api/azure.response-1) classe encapsula o retorno de serviço e apresenta valores de retorno em seu `Value` campo.  
* Métodos de serviço com resultados paginados retornam `Pageable<T>` ou `AsyncPageable<T>` como resultados. Para obter mais informações sobre a `Pageable<T>` classe, consulte [aqui](/dotnet/api/azure.pageable-1); para obter mais informações `AsyncPageable<T>` , consulte [aqui](/dotnet/api/azure.asyncpageable-1).
* Você pode iterar por meio de resultados paginados usando um `await foreach` loop. Para obter mais informações sobre esse processo, consulte [aqui](/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8).
* O SDK subjacente é `Azure.Core` . Consulte a [documentação do namespace do Azure](/dotnet/api/azure) para obter referência sobre os tipos e a infraestrutura do SDK.

Os métodos de serviço retornam objetos fortemente tipados sempre que possível. No entanto, como o Azure digital gêmeos se baseia em modelos personalizados – configurados pelo usuário em tempo de execução (por meio de modelos de DTDL carregados para o serviço), muitas APIs de serviço levam e retornam dados de alto nível no formato JSON.

## <a name="monitor-api-metrics"></a>Monitorar métricas de API

As métricas de API, como solicitações, latência e taxa de falha podem ser exibidas no [portal do Azure](https://portal.azure.com/). 

Na página inicial do portal, pesquise sua instância do gêmeos digital do Azure para obter seus detalhes. Selecione a opção **métricas** no menu da instância do gêmeos digital do Azure para abrir a página de *métricas* .

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Captura de tela mostrando a página de métricas do Azure digital gêmeos":::

A partir daqui, você pode exibir as métricas para sua instância e criar exibições personalizadas.

## <a name="next-steps"></a>Próximas etapas

Veja como fazer solicitações diretas para as APIs usando o postmaster:
* [*Instruções: fazer solicitações com o postmaster*](how-to-use-postman.md)

Ou então, pratique usando o SDK do .NET Criando um aplicativo cliente com este tutorial:
* [*Tutorial: Codificar um aplicativo cliente*](tutorial-code.md)