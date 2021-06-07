---
title: 'Início Rápido: biblioteca de clientes de gerenciamento do Azure para .NET'
description: Neste início rápido, comece a usar a biblioteca de clientes de gerenciamento do Azure para .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 3/22/2021
ms.author: pafarley
ms.openlocfilehash: 1ecfd58e961e70182bfc2e0cc8eaf77f5053eb77
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879613"
---
[Documentação de referência](/dotnet/api/overview/azure/cognitiveservices/management) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices) | [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Management.CognitiveServices/) | [Exemplos](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices/tests)

## <a name="c-prerequisites"></a>Pré-requisitos para usar C#

* Uma assinatura válida do Azure; [crie uma gratuitamente](https://azure.microsoft.com/free/).
* A versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-c-application"></a>Criar um aplicativo em C#

Criar um aplicativo .NET Core. Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `dotnet new` para criar um novo aplicativo do console com o nome `azure-management-quickstart`. Esse comando cria um projeto simples em C# do tipo "Olá, Mundo" com um arquivo de origem único: *program.cs*. 

```console
dotnet new console -n azure-management-quickstart
```

Altere o diretório para a pasta do aplicativo recém-criado. É possível criar o aplicativo com:

```console
dotnet build
```

A saída de compilação não deve conter nenhum aviso ou erro. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

No diretório do aplicativo, instale a biblioteca de clientes de Gerenciamento do Azure para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.Management.CognitiveServices
dotnet add package Microsoft.Azure.Management.Fluent
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

Se você estiver usando o IDE do Visual Studio, a biblioteca de clientes estará disponível como um pacote baixável do NuGet.

### <a name="import-libraries"></a>Importar bibliotecas

Abra *program.cs* e adicione as seguintes instruções `using` na parte superior do arquivo:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Adicione os campos a seguir à raiz de *program.cs* e preencha seus valores, usando a entidade de serviço que você criou e as informações de sua conta do Azure.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_constants)]

Em seguida, no método **Main**, use esses valores para construir um objeto **CognitiveServicesManagementClient**. Esse objeto é necessário para todas as operações de gerenciamento do Azure.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_assigns)]

## <a name="call-management-methods"></a>Chamar métodos gerenciamento

Adicione o código a seguir ao método **Main** para listar os recursos disponíveis, criar um recurso de exemplo, listar os recursos de sua propriedade e excluir o recurso de exemplo. Você definirá esses métodos nas etapas a seguir.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource-c"></a>Criar um recurso dos Serviços Cognitivos (C#)

Para criar e assinar um recurso dos Serviços Cognitivos, use o método **Criar**. Esse método adiciona um novo recurso que pode ser cobrado ao grupo de recursos que você passa. Ao criar o recurso, você precisará saber qual "tipo" de serviço deseja usar, bem como o tipo de preço (ou SKU) desejado e um local do Azure. O método a seguir usa todos esses argumentos e cria um recurso.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Escolher um serviço e um tipo de preço

Ao criar um recurso, você precisará saber qual "tipo" de serviço deseja usar, bem como o [tipo de preço](https://azure.microsoft.com/pricing/details/cognitive-services/) (ou SKU) desejado. Você usará essa e outras informações como parâmetros ao criar o recurso. Você pode encontrar uma lista dos "tipos" de Serviços Cognitivos disponíveis chamando o seguinte método em seu script:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Exibir os recursos

Para exibir todos os recursos em sua conta do Azure (em todos os grupos de recursos), use o seguinte método:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list)]

## <a name="delete-a-resource"></a>Excluir um recurso

O método a seguir exclui o recurso especificado do grupo de recursos fornecido.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_delete)]

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo do seu diretório de aplicativo com o comando `dotnet run`.

```dotnet
dotnet run
```

## <a name="see-also"></a>Veja também

* Confira **[Autenticar solicitações para os Serviços Cognitivos do Azure](../../authentication.md)** para saber como trabalhar com segurança com os Serviços Cognitivos.
* Confira **[O que são os Serviços Cognitivos do Azure?](../../what-are-cognitive-services.md)** para obter uma lista de categorias diferentes dentro dos Serviços Cognitivos.
* Confira **[Suporte para linguagem natural](../../language-support.md)** para ver a lista de linguagens naturais a que os Serviços Cognitivos dão suporte.
* Confira **[Usar os Serviços Cognitivos como contêineres](../../cognitive-services-container-support.md)** para saber como usar os Serviços Cognitivos no local.
* Confira **[Planejar e gerenciar custos para os Serviços Cognitivos](../../plan-manage-costs.md)** para estimar o custo de uso dos Serviços Cognitivos.
* Confira a **[documentação de referência do SDK de Gerenciamento dos Serviços Cognitivos](/dotnet/api/overview/azure/cognitiveservices/management)** para obter mais detalhes sobre o SDK de gerenciamento.
