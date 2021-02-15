---
title: Como usar o armazenamento persistente no Azure Spring Cloud | Microsoft Docs
description: Como usar o armazenamento persistente no Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 9d769cb6e2cc33df259da46a82ad27a8bfd9e74b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90888531"
---
# <a name="use-persistent-storage-in-azure-spring-cloud"></a>Usar armazenamento persistente no Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java ✔️ C#

O Azure Spring Cloud fornece dois tipos de armazenamento para seu aplicativo: persistente e temporário.

Por padrão, o Azure Spring Cloud fornece armazenamento temporário para cada instância do aplicativo. O armazenamento temporário é limitado a 5 GB por instância com o caminho de montagem padrão/tmp.

> [!WARNING]
> Se você reiniciar uma instância do aplicativo, o armazenamento temporário associado será excluído permanentemente.

O armazenamento persistente é um contêiner de compartilhamento de arquivos gerenciado pelo Azure e alocado por aplicativo. Os dados armazenados no armazenamento persistente são compartilhados por todas as instâncias de um aplicativo. Uma instância do Azure Spring Cloud pode ter um máximo de 10 aplicativos com armazenamento persistente habilitado. Cada aplicativo é alocado de 50 GB de armazenamento persistente. O caminho de montagem padrão para o armazenamento persistente é/persistent.

> [!WARNING]
> Se você desabilitar o armazenamento persistente de um aplicativo, todo esse armazenamento será desalocado e todos os dados armazenados serão perdidos.

## <a name="use-the-azure-portal-to-enable-persistent-storage"></a>Usar o portal do Azure para habilitar o armazenamento persistente

1. Na **Home** Page do seu portal do Azure, selecione **todos os recursos**.

    >![Localize o ícone todos os recursos](media/portal-all-resources.jpg)

1. Selecione o recurso Azure Spring Cloud que precisa de armazenamento persistente. Neste exemplo, o aplicativo selecionado é chamado de **outspring**.

    > ![Selecione seu aplicativo](media/select-service.jpg)

1. No título **configurações** , selecione **aplicativos**.

1. Seus serviços de nuvem do Azure Spring aparecem em uma tabela.  Selecione o serviço ao qual você deseja adicionar o armazenamento persistente. Neste exemplo, o serviço de **Gateway** está selecionado.

    > ![Selecione o serviço](media/select-gateway.jpg)

1. Na página de configuração do serviço, selecione **configuração**

1. Selecione a guia **armazenamento persistente** e selecione **habilitar**.

    > ![Habilitar armazenamento persistente](media/enable-persistent-storage.jpg)

Depois que o armazenamento persistente estiver habilitado, seu tamanho e caminho serão mostrados na página de configuração.

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>Usar o CLI do Azure para modificar o armazenamento persistente

Se necessário, instale a extensão Spring Cloud para o CLI do Azure:

```azurecli
az extension add --name spring-cloud
```
Outras operações:

* Para criar um aplicativo com o armazenamento persistente habilitado:

    ```azurecli
    az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Para habilitar o armazenamento persistente para um aplicativo existente:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Para desabilitar o armazenamento persistente em um aplicativo existente:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
    ```

    > [!WARNING]
    > Se você desabilitar o armazenamento persistente de um aplicativo, todo esse armazenamento será desalocado e todos os dados armazenados serão permanentemente perdidos.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre as [cotas de serviço e aplicativo](spring-cloud-quotas.md).
* Saiba como [dimensionar manualmente seu aplicativo](spring-cloud-tutorial-scale-manual.md).
