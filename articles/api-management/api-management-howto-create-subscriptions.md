---
title: Criar assinaturas no Gerenciamento de API do Azure | Microsoft Docs
description: Saiba como criar assinaturas no Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: bc791fea1dfd184749e84cb7b7a912972c6a9f12
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60657588"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Criar assinaturas no Gerenciamento de API do Azure

Quando você publica APIs por meio do Gerenciamento de API do Azure, é fácil e normal proteger o acesso a essas APIs usando chaves de assinatura. Os aplicativos cliente que precisam consumir as APIs publicadas devem incluir uma chave de assinatura válida em solicitações HTTP quando fazem chamadas a essas APIs. Para obter uma chave de assinatura para acessar APIs, é necessária uma assinatura. Para obter mais informações sobre as assinaturas do Azure, confira [Assinaturas no Gerenciamento de API do Azure](api-management-subscriptions.md).

Este artigo explica as etapas para criar assinaturas no portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para executar as etapas neste artigo, os pré-requisitos são da seguinte maneira:

+ [Crie uma instância de Gerenciamento de API](get-started-create-service-instance.md).
+ Compreenda as [assinaturas no Gerenciamento de API](api-management-subscriptions.md).

## <a name="create-a-new-subscription"></a>Criar uma nova assinatura

1. Selecione **Assinaturas** no menu à esquerda.
2. Selecione **Adicionar assinatura**.
3. Forneça um nome da assinatura e selecione o escopo.
4. Opcionalmente, escolha se a assinatura deve ser associada um usuário.
5. Clique em **Salvar**.

![Assinaturas flexíveis](./media/api-management-subscriptions/flexible-subscription.png)

Depois de criar a assinatura, duas chaves de API são fornecidas para acessar as APIs. Uma chave é primária e uma é secundária. 

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o Gerenciamento de API:

+ Conheça outros [conceitos](api-management-terminology.md) no Gerenciamento de API.
+ Siga nossos [tutoriais](import-and-publish.md) para saber mais sobre o Gerenciamento de API.
+ Verifique nossa [página de perguntas Frequentes](api-management-faq.md) para perguntas comuns.