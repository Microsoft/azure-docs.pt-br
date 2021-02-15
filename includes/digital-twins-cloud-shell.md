---
author: baanders
description: arquivo de inclusão dos Gêmeos Digitais do Azure – configurar o Cloud Shell e a extensão de IoT
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: c1d1efce656bea02385cb20ac11ab90ca5664e60
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026609"
---
Para começar a trabalhar com os Gêmeos Digitais do Azure em uma janela aberta do [Azure Cloud Shell](https://shell.azure.com), faça logon e defina o contexto do shell da sua assinatura para esta sessão. Execute estes comandos no seu Cloud Shell:

```azurecli-interactive
az login
az account set --subscription "<your-Azure-subscription-ID>"
```
> [!TIP]
> Você também pode usar o nome da sua assinatura em vez da ID no comando acima. 

Se esta é a primeira vez que você usa essa assinatura com os Gêmeos Digitais do Azure, execute este comando para se registrar no namespace dos Gêmeos Digitais do Azure. (Se você não tiver certeza disso, não haverá problemas em executá-lo novamente mesmo que já tenha feito isso no passado.)

```azurecli-interactive
az provider register --namespace 'Microsoft.DigitalTwins'
```

Em seguida, você adicionará a [**Extensão de IoT do Microsoft Azure para a CLI do Azure**](/cli/azure/ext/azure-iot/iot?preserve-view=true&view=azure-cli-latest) ao seu Cloud Shell para habilitar comandos para interagir com os Gêmeos Digitais do Azure e com outros serviços de IoT. 

[!INCLUDE [digital-twins-cloud-shell-extensions.md](digital-twins-cloud-shell-extensions.md)]

Agora você está pronto para trabalhar com os Gêmeos Digitais do Azure no Cloud Shell.

É possível verificar isso executando `az dt -h` a qualquer momento para ver uma lista dos comandos dos Gêmeos Digitais do Azure de nível superior que estão disponíveis.