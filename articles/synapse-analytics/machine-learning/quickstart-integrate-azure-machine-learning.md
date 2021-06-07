---
title: 'Início Rápido: Vincular um Workspace do Azure Machine Learning'
description: Vincular seu workspace do Azure Synapse a um Workspace do Azure Machine Learning
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 800cbf9b7a7fa415452f29b253347188c8917c52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98219446"
---
# <a name="quickstart-create-a-new-azure-machine-learning-linked-service-in-synapse"></a>Início Rápido: Criar um serviço vinculado do Azure Machine Learning no Azure Synapse

Neste início rápido, você vinculará um workspace do Azure Synapse Analytics a um Workspace do Azure Machine Learning. Vincular esses workspaces permite aproveitar o Azure Machine Learning de várias experiências no Azure Synapse.

Por exemplo, essa vinculação a um Workspace do Azure Machine Learning permite estas experiências:

- Execute seus pipelines do Azure Machine Learning como uma etapa em seus pipelines do Azure Synapse. Para saber mais, confira [Executar pipelines do Azure Machine Learning](../../data-factory/transform-data-machine-learning-service.md).

- Enriqueça seus dados com previsões usando um modelo de machine learning do registro de modelos do Azure Machine Learning e pontuando o modelo em pools de SQL do Synapse. Para obter mais detalhes, confira [Tutorial: Assistente de pontuação do modelo de machine learning para pools de SQL do Synapse](tutorial-sql-pool-model-scoring-wizard.md).

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/).
- [Workspace do Synapse Analytics](../get-started-create-workspace.md) com uma conta de armazenamento ADLS Gen2 configurada como o armazenamento padrão. Você precisa ser o **Colaborador de Dados do Blob de Armazenamento** do sistema de arquivos ADLS Gen2 com o qual trabalha.
- [Workspace do Azure Machine Learning](../../machine-learning/how-to-manage-workspace.md).
- Você precisa de permissões (ou solicitação de alguém que tenha permissões) para criar uma entidade de serviço e um segredo que você pode usar para criar o serviço vinculado. Observe que essa entidade de serviço precisa receber a função de colaborador no Workspace do Azure Machine Learning.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/)

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

Esta etapa criará uma entidade de serviço. Se você quiser usar uma entidade de serviço existente, pule esta etapa.
1. Abra o portal do Azure. 

1. Acesse **Azure Active Directory** -> **Registros de aplicativo**.

1. Clique em **Novo registro**. Em seguida, siga as instruções na interface do usuário para registrar um novo aplicativo.

1. Em seguida, o aplicativo será criado. Gere um segredo para o aplicativo. Acesse **Seu aplicativo** -> **Certificado e Segredo**. Clique em **Adicionar segredo do cliente** para gerar um segredo. Mantenha o segredo seguro e ele será usado posteriormente.

   ![Gerar segredo](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00a.png)

1. Crie uma entidade de serviço para o aplicativo. Acesse **Seu aplicativo** -> **Visão geral** e clique em **Criar entidade de serviço**. Em alguns casos, essa entidade de serviço é criada automaticamente.

   ![Criar uma entidade de serviço](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00b.png)

1. Adicione a entidade de serviço como "colaborador" do Workspace do Azure Machine Learning. Observe que isso exigirá ser um proprietário do grupo de recursos ao qual o Workspace do Azure Machine Learning pertence.

   ![Atribuir função de colaborador](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00c.png)

## <a name="create-a-linked-service"></a>Criar um serviço vinculado

1. No workspace do Azure Synapse no qual você deseja criar o serviço vinculado do Azure Machine Learning, vá para **Gerenciamento** -> **Serviço vinculado** e crie um serviço vinculado com o tipo "Azure Machine Learning".

   ![Criar serviço vinculado](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-create-linked-service-00a.png)

2. Preencha o formulário:

   - ID da entidade de serviço: Esta é a **ID do aplicativo (cliente)** do aplicativo.
  
     > [!NOTE]
     > NÃO é o nome da função de aplicativo. Localize a ID na página de visão geral do aplicativo. Ela deve ser uma cadeia de caracteres longa semelhante a "81707eac-ab38-406u-8f6c-10ce76a568d5".

   - Chave da entidade de serviço: O segredo que você gerou na seção anterior.

3. Clique em **Testar Conexão** para verificar a configuração está correta. Se o teste de conexão for aprovado, clique em **Salvar**.

   Se o teste de conexão falhar, verifique se a ID da entidade de serviço e o segredo estão corretos e tente novamente.

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Assistente de pontuação do modelo de machine learning – pool de SQL dedicado](tutorial-sql-pool-model-scoring-wizard.md)
- [Funcionalidades do Machine Learning no Azure Synapse Analytics](what-is-machine-learning.md)