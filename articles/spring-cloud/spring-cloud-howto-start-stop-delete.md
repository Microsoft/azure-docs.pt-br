---
title: Iniciar, parar e excluir seu aplicativo do Azure Spring Cloud | Microsoft Docs
description: Como iniciar, parar e excluir seu aplicativo Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 46d79de612a17c7b21e7cc51dcf4904209e1625d
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102212935"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Iniciar, parar e excluir seu aplicativo do Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java ✔️ C#

Este guia explica como alterar o estado de um aplicativo no Azure Spring Cloud usando o portal do Azure ou o CLI do Azure.

## <a name="using-the-azure-portal"></a>Usando o portal do Azure

Depois de implantar um aplicativo, você pode iniciá-lo, pará-lo e excluí-lo usando o portal do Azure.

1. Acesse sua instância de serviço do Azure Spring Cloud no portal do Azure.
1. Selecione a guia **Painel do aplicativo**.
1. Selecione o aplicativo cujo estado você deseja alterar.
1. Na página **visão geral** do aplicativo, selecione **iniciar/parar**, **reiniciar** ou **excluir**.

## <a name="using-the-azure-cli"></a>Usando a CLI do Azure

> [!NOTE]
> Você pode usar parâmetros opcionais e configurar padrões com a CLI do Azure. Saiba mais sobre o CLI do Azure lendo [nossa documentação de referência](/cli/azure/ext/spring-cloud/spring-cloud).  

Primeiro, instale a extensão do Azure Spring Cloud para o CLI do Azure da seguinte maneira:

```azurecli
az extension add --name spring-cloud
```

Em seguida, selecione qualquer uma dessas operações de CLI do Azure:

* Para iniciar seu aplicativo:

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Para interromper seu aplicativo:

    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Para reiniciar seu aplicativo:

    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Para excluir seu aplicativo:

    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
