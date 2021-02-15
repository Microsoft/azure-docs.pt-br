---
title: arquivo de inclusão
description: incluir arquivo
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: e208b52c67f173bd0d289715b63562df656b1ec9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009726"
---
1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto e selecione **Publicar**.

1. Na caixa de diálogo **publicar** , selecione **Azure** para **destino**e, em seguida, selecione **Avançar**. 

1. Selecione **Azure WebJobs** para **destino específico**e, em seguida, selecione **Avançar**.

1. Selecione **criar um novo WebJob do Azure**.

   ![Escolher destino de publicação](./media/webjobs-publish-netcore/pick-publish-target.png)

1. Na caixa de diálogo **serviço de aplicativo (Windows)** , use as configurações de hospedagem na tabela a seguir.

    | Configuração      | Valor sugerido  | Descrição                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Nome globalmente exclusivo | Nome que identifica seu novo aplicativo de funções de forma exclusiva. |
    | **Assinatura** | Escolha sua assinatura | A assinatura do Azure a utilizar. |
    | **[Grupo de recursos](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  Nome do grupo de recursos no qual criar o seu aplicativo de funções. Escolha **Novo** para criar um novo grupo de recursos.|
    | **[Plano de hospedagem](../articles/app-service/overview-hosting-plans.md)** | Plano do Serviço de Aplicativo | Um [plano do Serviço de Aplicativo](../articles/app-service/overview-hosting-plans.md) especifica o local, tamanho e recursos do farm de servidores Web que hospeda o aplicativo. Você pode economizar dinheiro ao hospedar vários aplicativos configurando os aplicativos Web para compartilhar um único plano do Serviço de Aplicativo. Os planos do serviço de aplicativo definem a região, o tamanho da instância, a contagem de escala e a SKU (gratuito, compartilhado, básico, Standard ou Premium). Escolha **novo** para criar um novo plano do serviço de aplicativo. |

    ![Criar a caixa de diálogo Serviço de Aplicativo](./media/webjobs-publish-netcore/app-service-dialog.png)

1. Selecione **criar** para criar um WebJob e recursos relacionados no Azure com essas configurações e implantar o código do projeto.