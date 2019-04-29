---
title: Configuração da conta de lote do Azure do projeto acústica
titlesuffix: Azure Cognitive Services
description: Estas instruções descrevem como configurar uma conta do lote do Azure para uso com o projeto acústica Unity e Unreal integrações de mecanismo.
services: cognitive-services
author: ashtat
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 7a7f5f6738b4bc96b6248deb062c7b3f63048148
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61335581"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Configuração da conta de lote do Azure do projeto acústica
Estas instruções descrevem como configurar uma conta do lote do Azure para uso com o projeto acústica Unity e Unreal integrações de mecanismo.

## <a name="get-an-azure-subscription"></a>Obter uma assinatura do Azure
Uma [assinatura do Azure](https://azure.microsoft.com/free/) é necessária antes de configurar contas do lote e armazenamento. Se você estiver se inscrevendo pela primeira vez, o Azure fornece alguns recursos gratuitos de tempo limitado e o crédito de US $200.

## <a name="create-azure-batch-and-storage-accounts"></a>Criar contas de lote e armazenamento do Azure
Em seguida, siga [estas instruções](https://docs.microsoft.com/azure/batch/batch-account-create-portal) para configurar o seu Azure Batch e as contas do Armazenamento do Azure associadas.

Escolha as opções padrão para as contas de lote e armazenamento:
  
  ![Captura de tela do lote do Azure novas contas mostrando as configurações padrão de opções](media/new-batch-account-create.png)

  ![Captura de tela do armazenamento do Azure novas contas mostrando as configurações padrão de opções](media/batch-storage-account-create.png)

Demora alguns minutos para o Azure implantar as contas. Procure uma notificação de conclusão no canto superior direito do portal.
  
  ![Contas de captura de tela do Azure implantado notificação](media/batch-accounts-deploy-notification.png)

Suas contas agora devem estar visíveis no seu painel.
  
  ![Painel do portal captura de tela do Azure mostrando uma conta de armazenamento e do lote](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Configurar a acústica da interface do usuário para assar com credenciais do Azure
Clique no link da conta do Lote no painel e, em seguida, clique no link **Chaves** na página da conta do Lote para acessar suas credenciais.
  
  ![Conta de captura de tela do lote do Azure com o link à página chaves realçada](media/batch-access-keys.png)

  ![Página de chaves de conta de captura de tela do lote do Azure com chaves de acesso](media/batch-keys-info.png)

Clique no link **Conta de armazenamento** na página para acessar suas credenciais da conta do Armazenamento do Azure.
  
  ![Página de chaves de conta de captura de tela do armazenamento do Azure com chaves de acesso](media/storage-keys-info.png)

Insira essas credenciais na [plug-in do Unity tortas](unity-baking.md) ou [plug-in de tortas Unreal](unreal-baking.md).

## <a name="node-types-and-region-support"></a>Tipos de nó e o suporte de região
Projeto acústica requer que e H-série Fsv2 computação otimizada de nós de VM do Azure que podem não ter suporte em todas as regiões do Azure. Por favor, verifique [esta tabela](https://azure.microsoft.com/global-infrastructure/services) para garantir que você está escolhendo o local certo para sua conta do Lote.
![Captura de tela mostrando a máquinas virtuais do Azure por região](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>Atualizando sua cota
As contas do Azure Batch são aprovisionadas na criação de contas com um limite de 20 núcleos de computação. Podemos aumentar esse limite para tortas rápidas, porque você pode paralelizar sua carga de trabalho acústica em muitos nós, até o número de pontos de teste na sua cena. Você pode solicitar um aumento de cota, clicando na **cota** link na sua página de portal de lote do Azure e, em seguida, clicando em **solicitação de aumento de cota**:

![Página de captura de tela de cota do Azure](media/azure-quotas.png)

## <a name="next-steps"></a>Próximos passos
* Integrar o plug-in do projeto acústica em seu [Unity](unity-integration.md) ou [Unreal](unreal-integration.md) projeto

