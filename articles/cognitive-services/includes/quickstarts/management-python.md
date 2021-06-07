---
title: 'Início Rápido: biblioteca de clientes de gerenciamento do Azure para Python'
description: Neste início rápido, comece a usar a biblioteca de clientes de gerenciamento do Azure para Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: fb908cdcf3e235654effc043de29e599a48179d4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879741"
---
[Documentação de referência](/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices) | [Pacote (PyPi)](https://pypi.org/project/azure-mgmt-cognitiveservices/) | [Amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices/tests)

## <a name="python-prerequisites"></a>Pré-requisitos para usar o Python

* Uma assinatura válida do Azure; [crie uma gratuitamente](https://azure.microsoft.com/free/).
* [Python 3.x](https://www.python.org/)

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

Crie um aplicativo Python no editor ou IDE de sua preferência e navegue até o projeto em uma janela de console.

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

É possível instalar a biblioteca de clientes com:

```console
pip install azure-mgmt-cognitiveservices
```

Se você estiver usando o IDE do Visual Studio, a biblioteca de clientes estará disponível como um pacote baixável do NuGet.

### <a name="import-libraries"></a>Importar bibliotecas

Abra o script de Python e importe as bibliotecas a seguir.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Adicione os campos a seguir à raiz do script e preencha seus valores, usando a entidade de serviço que você criou e as informações de sua conta do Azure.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_constants)]

Em seguida, adicione o código a seguir para construir um objeto **CognitiveServicesManagementClient**. Esse objeto é necessário para todas as operações de gerenciamento do Azure.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_auth)]

## <a name="create-a-cognitive-services-resource-python"></a>Criar um recurso dos Serviços Cognitivos (Python)

Para criar e assinar um recurso dos Serviços Cognitivos, use a função **Criar**. Essa função adiciona um novo recurso que pode ser cobrado ao grupo de recursos que você passa. Ao criar o recurso, você precisará saber qual "tipo" de serviço deseja usar, bem como o tipo de preço (ou SKU) desejado e um local do Azure. A função a seguir usa todos esses argumentos e cria um recurso.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Escolher um serviço e um tipo de preço

Ao criar um recurso, você precisará saber qual "tipo" de serviço deseja usar, bem como o [tipo de preço](https://azure.microsoft.com/pricing/details/cognitive-services/) (ou SKU) desejado. Você usará essa e outras informações como parâmetros ao criar o recurso. A função a seguir lista os "tipos" de Serviços Cognitivos disponíveis.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Exibir os recursos

Para exibir todos os recursos em sua conta do Azure (em todos os grupos de recursos), use a seguinte função:

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list)]

## <a name="delete-a-resource"></a>Excluir um recurso

A função a seguir exclui o recurso especificado do grupo de recursos fornecido.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_delete)]

## <a name="call-management-functions"></a>Chamar funções de gerenciamento

Adicione o código a seguir à parte inferior do script para chamar as funções acima. Esse código lista os recursos disponíveis, cria um recurso de exemplo, lista os recursos de sua propriedade e exclui o recurso de exemplo.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_calls)]

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo na linha de comando com o comando `python`.

```console
python <your-script-name>.py
```

## <a name="see-also"></a>Veja também

* Confira **[Autenticar solicitações para os Serviços Cognitivos do Azure](../../authentication.md)** para saber como trabalhar com segurança com os Serviços Cognitivos.
* Confira **[O que são os Serviços Cognitivos do Azure?](../../what-are-cognitive-services.md)** para obter uma lista de categorias diferentes dentro dos Serviços Cognitivos.
* Confira **[Suporte para linguagem natural](../../language-support.md)** para ver a lista de linguagens naturais a que os Serviços Cognitivos dão suporte.
* Confira **[Usar os Serviços Cognitivos como contêineres](../../cognitive-services-container-support.md)** para saber como usar os Serviços Cognitivos no local.
* Confira **[Planejar e gerenciar custos para os Serviços Cognitivos](../../plan-manage-costs.md)** para estimar o custo de uso dos Serviços Cognitivos.
* Confira a **[documentação de referência do SDK de Gerenciamento do Azure](/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices)** para obter mais detalhes sobre o SDK de gerenciamento.
