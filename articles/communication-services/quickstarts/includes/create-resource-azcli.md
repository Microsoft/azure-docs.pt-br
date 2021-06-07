---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 22a9cf3338f422341928a77f2bf14c497aa2ba31
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563762"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/dotnet/).
- Instalar a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?tabs=azure-cli) 

## <a name="create-azure-communication-resource"></a>Criar o recurso de comunicação do Azure

Para criar um recurso dos Serviços de Comunicação do Azure, [entre na CLI do Azure](/cli/azure/authenticate-azure-cli). Faça isso por meio do terminal usando o comando ```az login``` e fornecendo suas credenciais. Execute o seguinte comando para criar o recurso:

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

Se quiser selecionar uma assinatura específica, você também poderá especificar o sinalizador ```--subscription``` e fornecer a ID da assinatura.
```
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup> --subscription "<subscriptionID>"
```

Configure o recurso dos Serviços de Comunicação com as seguintes opções:

* O grupo de recursos
* O nome do recurso dos Serviços de Comunicação
* A geografia à qual o recurso será associado

Na próxima etapa, você pode atribuir marcas ao recurso. As marcas podem ser usadas para organizar os seus recursos do Azure. Confira a [documentação de marcação de recursos](../../../azure-resource-manager/management/tag-resources.md) para obter mais informações sobre marcas.

## <a name="manage-your-communication-services-resource"></a>Gerenciar o seu recurso dos Serviços de Comunicação

Para adicionar marcas ao recurso dos Serviços de Comunicação, execute os comandos a seguir. Você também pode direcionar uma assinatura específica.

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal1" --resource-group "<resourceGroup>"

az communication update --name "<communicationName>" --tags newTag="newVal2" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"
```

Para obter informações sobre comandos adicionais, confira [az communication](/cli/azure/ext/communication/communication).
