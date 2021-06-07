---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 493c674fa161bf33436e560fdcbb9196410db931
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102209454"
---
> [!TIP]
> Depois de entrar, você deve ver uma lista de assinaturas associadas à sua conta do Azure. A informação de assinatura com `isDefault: true` é a assinatura ativada no momento para os comandos da CLI do Azure. Essa assinatura precisa ser a mesma que contém o seu workspace do Azure Machine Learning. Encontre a ID da assinatura no [portal do Azure](https://portal.azure.com) acessando a página de visão geral do seu workspace. Use também o SDK para obter a ID da assinatura por meio do objeto de workspace. Por exemplo, `Workspace.from_config().subscription_id`.
> 
> Para selecionar outra assinatura, use o comando `az account set -s <subscription name or ID>` e especifique o nome ou a ID da assinatura para a qual deseja alternar. Para obter mais informações sobre a seleção da assinatura, confira [Usar várias assinaturas do Azure](/cli/azure/manage-azure-subscriptions-azure-cli).