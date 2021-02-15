---
title: Criar assinaturas no Gerenciamento de API do Azure | Microsoft Docs
description: Saiba como criar assinaturas no Gerenciamento de API do Azure. Uma assinatura é necessária para obter as chaves de assinatura que permitem o acesso às APIs.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 191323a4c150c00c93245be35c9c8af381e26b42
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87904846"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Criar assinaturas no Gerenciamento de API do Azure

Quando você publica APIs por meio do Gerenciamento de API do Azure, é fácil e normal proteger o acesso a essas APIs usando chaves de assinatura. Os aplicativos cliente que precisam consumir as APIs publicadas devem incluir uma chave de assinatura válida em solicitações HTTP quando fazem chamadas a essas APIs. É necessária uma assinatura para obter uma chave de assinatura para acessar APIs. Para obter mais informações sobre assinaturas, consulte [assinaturas no gerenciamento de API do Azure](api-management-subscriptions.md).

Este artigo explica as etapas para criar assinaturas no portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para executar as etapas neste artigo, os pré-requisitos são da seguinte maneira:

+ [Crie uma instância de gerenciamento de API](get-started-create-service-instance.md).
+ Compreenda as [assinaturas no Gerenciamento de API](api-management-subscriptions.md).

## <a name="create-a-new-subscription"></a>Criar uma nova assinatura

1. Selecione **Assinaturas** no menu à esquerda.
2. Selecione **Adicionar assinatura**.
3. Forneça um nome da assinatura e selecione o escopo.
4. Opcionalmente, escolha se a assinatura deve ser associada a um usuário.
5. Selecione **Salvar**.

![Assinaturas flexíveis](./media/api-management-subscriptions/flexible-subscription.png)

Depois de criar a assinatura, duas chaves de API são fornecidas para acessar as APIs. Uma chave é primária e uma é secundária. 

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o Gerenciamento de API:

+ Aprenda outros [conceitos](api-management-terminology.md) no gerenciamento de API.
+ Siga nossos [tutoriais](import-and-publish.md) para saber mais sobre o gerenciamento de API.
+ Confira nossa [página de perguntas frequentes](api-management-faq.md) para perguntas comuns.