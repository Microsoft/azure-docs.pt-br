---
title: Criar assinaturas do Azure de maneira programática
description: Este artigo ajuda você a entender as opções disponíveis para criar assinaturas do Azure de maneira programática.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 01/13/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 546ed24b5f9e7892f40c9d425b668f60ad705f8f
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493881"
---
# <a name="create-azure-subscriptions-programmatically"></a>Criar assinaturas do Azure de maneira programática

Este artigo ajuda você a entender as opções disponíveis para criar assinaturas do Azure de maneira programática.

Usando várias APIs REST, você pode criar uma assinatura para os seguintes tipos de contrato do Azure:

- EA (Enterprise Agreement)
- MCA (Contrato de Cliente da Microsoft)
- MPA (Contrato de Parceiro da Microsoft)

Você não pode criar de maneira programática mais assinaturas para outros tipos de contrato com APIs REST.

Os requisitos e detalhes para criar assinaturas diferem para diferentes contratos e versões de API. Confira os seguintes artigos que se aplicam à sua situação:

APIs mais recentes:

- [Criar assinaturas de EA](programmatically-create-subscription-enterprise-agreement.md)
- [Criar assinaturas do MCA](programmatically-create-subscription-microsoft-customer-agreement.md)
- [Criar assinaturas do MPA](programmatically-create-subscription-microsoft-partner-agreement.md)

Se você ainda estiver usando as [APIs de versão prévia](programmatically-create-subscription-preview.md), poderá continuar criando assinaturas com elas. 

E você pode [criar assinaturas com um modelo do ARM](create-subscription-template.md). Um modelo do ARM ajuda a automatizar o processo de criação de assinaturas com APIs REST. 

## <a name="next-steps"></a>Próximas etapas

* Agora que você criou uma assinatura, conceda essa capacidade a outros usuários e entidades de serviço. Para saber mais, veja [Conceder acesso para criar assinaturas do Azure Enterprise (versão prévia)](grant-access-to-create-subscription.md).
* Para obter mais informações sobre como gerenciar um grande número de assinaturas usando grupos de gerenciamento, confira como [Organizar seus recursos com os grupos de gerenciamento do Azure](../../governance/management-groups/overview.md).
