---
title: Usar portal do Azure para implantar o aplicativo de catálogo de serviços
description: Mostra aos consumidores de Aplicativos Gerenciados como implantar um aplicativo de catálogo de serviço por meio do portal do Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: ce58fc69496f54c078b0a0a55a8a3c7cad82a051
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "81391704"
---
# <a name="quickstart-deploy-service-catalog-app-through-azure-portal"></a>Início rápido: implantar o aplicativo de catálogo de serviços por meio do portal do Azure

No [início rápido anterior](publish-service-catalog-app.md), você publicou uma definição de aplicativo gerenciado. Neste início rápido, você pode criar um aplicativo de catálogo de serviço usando essa definição.

## <a name="create-service-catalog-app"></a>Criar aplicativo de catálogo de serviços

No portal do Azure, use as seguintes etapas:

1. Selecione **Criar um recurso**.

   ![Criar um recurso](./media/deploy-service-catalog-quickstart/create-new.png)

1. Pesquise **Aplicativo Gerenciado de Catálogo de Serviço** e selecione-o entre as opções disponíveis.

   ![Pesquisar aplicativo de catálogo de serviços](./media/deploy-service-catalog-quickstart/select-service-catalog.png)

1. Você verá uma descrição do serviço de Aplicativo Gerenciado. Selecione **Criar**.

   ![Selecione criar](./media/deploy-service-catalog-quickstart/create-service-catalog.png)

1. O portal mostra as definições de aplicativo gerenciado às quais você tem acesso. Entre as definições disponíveis, selecione aquela que você deseja implantar. Neste início rápido, use a definição de **Conta de Armazenamento Gerenciado** que você criou no início rápido anterior. Selecione **Criar**.

   ![Selecione a definição a ser implantada](./media/deploy-service-catalog-quickstart/select-definition.png)

1. Forneça valores para a guia **noções básicas** . Selecione a assinatura do Azure para implantar seu aplicativo de catálogo de serviços. Crie um novo grupo de recursos denominado **applicationGroup**. Selecione um local para o aplicativo. Ao terminar, selecione **OK**.

   ![Forneça valores para o básico](./media/deploy-service-catalog-quickstart/provide-basics.png)

1. Forneça um prefixo para o nome da conta de armazenamento. Selecione o tipo de conta de armazenamento a ser criada. Ao terminar, selecione **OK**.

   ![Fornecer valores para o armazenamento](./media/deploy-service-catalog-quickstart/provide-storage.png)

1. Examine o resumo. Se a validação for bem-sucedida, selecione **OK** para iniciar a implantação.

   ![Exibir resumo](./media/deploy-service-catalog-quickstart/view-summary.png)

## <a name="view-results"></a>Exibir os resultados

Depois que o serviço de aplicativo do catálogo tiver sido implantado, você terá dois novos grupos de recursos. Um grupo de recursos contém o app de catálogo de serviços. O outro grupo de recursos contém os recursos para o app de catálogo de serviços.

1. Exiba o grupo de recursos denominado **applicationGroup** para ver o app de catálogo de serviços.

   ![Exibir o aplicativo](./media/deploy-service-catalog-quickstart/view-managed-application.png)

1. Exiba o grupo de recursos denominado **applicationGroup{hash-characters}** para ver os recursos do app de catálogo de serviços.

   ![Exibir recursos](./media/deploy-service-catalog-quickstart/view-resources.png)

## <a name="next-steps"></a>Próximas etapas

* Para saber como criar os arquivos de definição de um aplicativo gerenciado, confira [Criar e publicar uma definição de aplicativo gerenciado](publish-service-catalog-app.md).
* Para a CLI do Azure, confira [Implantar app de catálogo de serviços com a CLI do Azure](./scripts/managed-application-cli-sample-create-application.md).
* Para o PowerShell, confira [Implantar app de catálogo de serviços com o PowerShell](./scripts/managed-application-poweshell-sample-create-application.md).
