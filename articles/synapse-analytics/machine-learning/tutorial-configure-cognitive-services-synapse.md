---
title: 'Tutorial: Pré-requisitos para Serviços Cognitivos no Azure Synapse Analytics'
description: Saiba como configurar os pré-requisitos para usar os Serviços Cognitivos no Azure Synapse.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 3ab861caca0ef6f58c2c1bc722412774deb725ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936674"
---
# <a name="tutorial-prerequisites-for-using-cognitive-services-in-azure-synapse-analytics"></a>Tutorial: Pré-requisitos para uso dos Serviços Cognitivos no Azure Synapse Analytics

Neste tutorial, você aprenderá como configurar os pré-requisitos para usar com segurança os Serviços Cognitivos do Azure no Azure Synapse Analytics.

Este tutorial abrange:
> [!div class="checklist"]
> - Criar um recurso dos Serviços Cognitivos, como a Análise de Texto ou o Detector de Anomalias.
> - Armazenar uma chave de autenticação para os recursos dos Serviços Cognitivos como segredos no Azure Key Vault e configurar o acesso para um workspace do Azure Synapse Analytics.
> - Criar um serviço vinculado do Azure Key Vault no workspace do Azure Synapse Analytics.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- [Workspace do Azure Synapse Analytics](../get-started-create-workspace.md) com uma conta de armazenamento do Azure Data Lake Storage Gen2 configurada como o armazenamento padrão. Você precisa ser o *Colaborador de Dados do Storage Blob* do sistema de arquivos do Azure Data Lake Storage Gen2 com o qual você trabalha.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-cognitive-services-resource"></a>Criar um recurso dos Serviços Cognitivos

Os [Serviços Cognitivos do Azure](../../cognitive-services/index.yml) incluem muitos tipos de serviços. A Análise de Texto e o Detector de Anomalias são dois exemplos nos tutoriais do Azure Synapse.

Você pode criar um recurso de [Análise de Texto](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) no portal do Azure:

![Captura de tela que mostra a Análise de Texto no portal, com o botão Criar.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00b.png)

Você pode criar um recurso de [Detector de Anomalias](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) no portal do Azure:

![Captura de tela que mostra a Detector de Anomalias no portal, com o botão Criar.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00a.png)

## <a name="create-a-key-vault-and-configure-secrets-and-access"></a>Criar um cofre de chaves e configurar segredos e acesso

1. Crie um [cofre de chaves](https://ms.portal.azure.com/#create/Microsoft.KeyVault) no portal do Azure.
2. Acesse **Key Vault** > **Políticas de acesso** e conceda ao [MSI do workspace do Azure Synapse](../security/synapse-workspace-managed-identity.md) as permissões para ler os segredos do Azure Key Vault.

   > [!NOTE]
   > Verifique se as alterações da política foram salvas. É fácil deixar essa etapa passar.

   ![Captura de tela que mostra seleções para adição de uma política de acesso.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00c.png)

3. Acesse seu recurso dos Serviços Cognitivos. Por exemplo, acesse **Detector de Anomalias** > **Chaves e Ponto de Extremidade**. Em seguida, copie uma das duas chaves para a área de transferência.

4. Acesse **Key Vault** > **Segredo** para criar um segredo. Especifique o nome do segredo e cole a chave da etapa anterior no campo **Valor**. Por fim, selecione **Criar**.

   ![Captura de tela que mostra seleções para criação de um segredo.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00d.png)

   > [!IMPORTANT]
   > Você precisará se lembrar do nome do segredo ou anotá-lo. Você o usará posteriormente quando se conectar aos Serviços Cognitivas do Azure Synapse Studio.

## <a name="create-an-azure-key-vault-linked-service-in-azure-synapse"></a>Criar um serviço vinculado do Azure Key Vault no Azure Synapse

1. Abra o workspace no Azure Synapse Studio. 
2. Acesse **Gerenciar** > **Serviços Vinculados**. Crie um serviço vinculado do **Azure Key Vault** apontando para o cofre de chaves que acabamos de criar. 
3. Verifique a conexão selecionando o botão **Testar conexão**. Se a conexão estiver verde, selecione **Criar** e escolha **Publicar tudo** para salvar sua alteração.

![Captura de tela que mostra o Azure Key Vault como um novo serviço vinculado.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00e.png)

Agora você está pronto para continuar com um dos tutoriais para usar a experiência dos Serviços Cognitivos do Azure no Azure Synapse Studio.

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Análise de sentimento com os Serviços Cognitivos do Azure](tutorial-cognitive-services-sentiment.md)
- [Tutorial: Detecção de anomalias com os Serviços Cognitivos do Azure](tutorial-cognitive-services-sentiment.md)
- [Tutorial: Pontuação do modelo de machine learning em Pools de SQL dedicados do Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md).
- [Funcionalidades do Machine Learning no Azure Synapse Analytics](what-is-machine-learning.md)