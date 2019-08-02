---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/20/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ace42278269ff6af31902dbecead81329815af12
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172158"
---
## <a name="create-a-topic-using-the-azure-portal"></a>Criar um tópico usando o portal do Azure
1. Na página **Namespace de Barramento de Serviço**, selecione **Tópicos** no menu à esquerda.
2. Selecione **+ Tópico** na barra de ferramentas. 
4. Insira um **nome** para o tópico. Deixe as outras opções com os valores padrão.
5. Selecione **Criar**.

    ![Criar tópico](./media/service-bus-create-topics-subscriptions-portal/create-topic.png)

## <a name="create-subscriptions-to-the-topic"></a>Criar duas assinaturas do tópico
1. Selecione o **tópico** que você criou na seção anterior. 
    
    ![Selecionar tópico](./media/service-bus-create-topics-subscriptions-portal/select-topic.png)
2. Na página **Tópico de Barramento de Serviço**, selecione **Assinaturas** no menu à esquerda e em seguida, selecione **+ Assinatura** na barra de ferramentas. 
    
    ![Botão Adicionar assinatura](./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png)
3. Na página **Criar assinatura**, insira um **S1** para o **nome** da assinatura e selecione **Criar**. 

    ![Página Criar assinatura](./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png)
4. Repita a etapa anterior mais duas vezes, criando assinaturas nomeadas como **S2** e **S3**.