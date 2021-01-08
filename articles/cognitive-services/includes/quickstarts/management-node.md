---
title: 'Início Rápido: biblioteca de clientes de gerenciamento do Azure para Node.js'
description: Neste início rápido, comece a usar a biblioteca de clientes de gerenciamento do Azure para Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: eddee6bc0fdcd636b7b9ffb81b121052499251b2
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/23/2020
ms.locfileid: "97745678"
---
[Documentação de referência](/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices) | [Pacote (NPM)](https://www.npmjs.com/package/@azure/arm-cognitiveservices) | [Exemplos](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices#sample-code)

## <a name="javascript-prerequisites"></a>Pré-requisitos para usar o JavaScript

* Uma assinatura válida do Azure; [crie uma gratuitamente](https://azure.microsoft.com/free/).
* A versão atual do [Node.js](https://nodejs.org/)

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-nodejs-application"></a>Criar um novo aplicativo do Node.js

Em uma janela de console (como cmd, PowerShell ou Bash), crie um novo diretório para seu aplicativo e navegue até ele. 

```console
mkdir myapp && cd myapp
```

Execute o comando `npm init` para criar um aplicativo do Node com um arquivo `package.json`. 

```console
npm init
```

Crie um arquivo chamado _index.js_ antes de continuar.

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Instale os seguintes pacotes NPM:

```console
npm install @azure/arm-cognitiveservices
npm install @azure/ms-rest-js
npm install @azure/ms-rest-nodeauth
```

O arquivo `package.json` do seu aplicativo será atualizado com as dependências.

### <a name="import-libraries"></a>Importar bibliotecas

Abra o script _index.js_ e importe as bibliotecas a seguir.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Adicione os campos a seguir à raiz do script e preencha seus valores, usando a entidade de serviço que você criou e as informações de sua conta do Azure.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_constants)]

Em seguida, adicione a função `quickstart` a seguir para cuidar do trabalho principal do programa. O primeiro bloco de código constrói um objeto **CognitiveServicesManagementClient** usando as variáveis de credencial que você inseriu acima. Esse objeto é necessário para todas as operações de gerenciamento do Azure.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main_auth)]

## <a name="call-management-functions"></a>Chamar funções de gerenciamento

Adicione o código a seguir ao final de sua função `quickstart` para listar os recursos disponíveis, criar um recurso de exemplo, listar os recursos de sua propriedade e excluir o recurso de exemplo. Você definirá essas funções nas etapas a seguir.

## <a name="create-a-cognitive-services-resource"></a>Criar um recurso dos Serviços Cognitivos

Para criar e assinar um recurso dos Serviços Cognitivos, use a função **Criar**. Essa função adiciona um novo recurso que pode ser cobrado ao grupo de recursos que você passa. Ao criar o recurso, você precisará saber qual "tipo" de serviço deseja usar, bem como o tipo de preço (ou SKU) desejado e um local do Azure. A função a seguir usa todos esses argumentos e cria um recurso.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Escolher um serviço e um tipo de preço

Ao criar um recurso, você precisará saber qual "tipo" de serviço deseja usar, bem como o [tipo de preço](https://azure.microsoft.com/pricing/details/cognitive-services/) (ou SKU) desejado. Você usará essa e outras informações como parâmetros ao criar o recurso. A função a seguir lista os "tipos" de Serviços Cognitivos disponíveis.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Exibir os recursos

Para exibir todos os recursos em sua conta do Azure (em todos os grupos de recursos), use a seguinte função:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list)]

## <a name="delete-a-resource"></a>Excluir um recurso

A função a seguir exclui o recurso especificado do grupo de recursos fornecido.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_delete)]

## <a name="run-the-application"></a>Executar o aplicativo

Adicione o código a seguir à parte inferior do script para chamar sua função `quickstart` principal com tratamento de erro.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main)]

Em seguida, na janela do console, execute o aplicativo com o comando `node`.

```console
node index.js
```

## <a name="see-also"></a>Veja também

* [Documentação de referência do SDK de Gerenciamento do Azure](/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest)
* [O que são os Serviços Cognitivos do Azure?](../../what-are-cognitive-services.md)
* [Autenticar solicitações para os Serviços Cognitivos do Azure](../../authentication.md)
* [Criar um recurso usando o portal do Azure](../../cognitive-services-apis-create-account.md)