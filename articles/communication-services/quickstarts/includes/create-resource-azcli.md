---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 1/28/2021
ms.author: mikben
ms.openlocfilehash: 32d869e6285e76b57a7b8e462e8110a43de82dd9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101656561"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/dotnet/).

## <a name="create-azure-communication-resource"></a>Criar o recurso de comunicação do Azure

Para criar um recurso dos Serviços de Comunicação do Azure, [entre na CLI do Azure](/cli/azure/authenticate-azure-cli) e execute o seguinte comando:

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

Configure o recurso dos Serviços de Comunicação com as seguintes opções:

* O grupo de recursos
* O nome do recurso dos Serviços de Comunicação
* A geografia à qual o recurso será associado

Na próxima etapa, você pode atribuir marcas ao recurso. As marcas podem ser usadas para organizar os seus recursos do Azure. Confira a [documentação de marcação de recursos](../../../azure-resource-manager/management/tag-resources.md) para obter mais informações sobre marcas.

## <a name="manage-your-communication-services-resource"></a>Gerenciar o seu recurso dos Serviços de Comunicação

Para adicionar marcas ao recurso dos Serviços de Comunicação, execute os seguintes comandos:

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"
```

Para obter informações sobre comandos adicionais, confira [az communication](/cli/azure/ext/communication/communication).