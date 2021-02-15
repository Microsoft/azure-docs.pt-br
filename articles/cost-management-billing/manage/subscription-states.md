---
title: Estados de assinatura do Azure
description: Este artigo descreve os diferentes estados e status de uma assinatura do Azure.
keywords: Estado status da assinatura do Azure
author: anuragdalmia
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: andalmia
ms.openlocfilehash: 5267b333e66a0ae7b2ad05399406fecc32af74b0
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430119"
---
# <a name="azure-subscription-states"></a>Estados de assinatura do Azure

Este artigo descreve os vários estados que uma assinatura do Azure pode ter. Você encontrará esses estados exibidos como **Status** nas áreas de assinatura do portal do Azure.

| Estado da assinatura | Descrição |
|-------------| ----------------|
| **Ativo**/**Habilitado** | Sua assinatura do Azure está ativa. Use a assinatura para implantar novos recursos e gerenciar os existentes.<br><br>Todas as operações (PUT, PATCH, DELETE, POST e GET) estão disponíveis para os provedores de recursos [registrados para a assinatura](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). |
| **Excluída** | Sua assinatura do Azure foi excluída juntamente com todos os recursos/dados subjacentes.<br><br>Nenhuma operação está disponível. |
| **Desabilitado** | Sua assinatura do Azure está desabilitada e não pode mais ser usada para criar ou gerenciar recursos do Azure. Enquanto estão nesse estado, suas máquinas virtuais são desalocadas, os endereços IP temporários são liberados, o armazenamento é somente leitura e outros serviços são desabilitados. Uma assinatura pode ser desabilitada devido aos seguintes motivos: Seu crédito pode ter expirado. Você pode ter atingido o limite de gastos. Você tem uma fatura vencida. O limite do seu cartão de crédito foi excedido. Ou então, ele foi explicitamente desabilitado ou cancelado. Dependendo do tipo de assinatura, uma assinatura pode permanecer desabilitada entre 1 e 90 dias. Após esse período, ela é excluída permanentemente. Para obter mais informações, confira [Reativar uma assinatura desabilitada do Azure](subscription-disabled.md).<br><br>As operações para criar ou atualizar recursos (PUT, PATCH) estão desabilitadas. As operações que realizam uma ação (POST) também estão desabilitadas. Você pode recuperar ou excluir recursos (GET, DELETE). Seus recursos ainda estão disponíveis. |
| **Expirada** | Sua assinatura do Azure expirou porque foi cancelada. Você pode reativar uma assinatura expirada. Para obter mais informações, confira [Reativar uma assinatura desabilitada do Azure](subscription-disabled.md).<br><br>As operações para criar ou atualizar recursos (PUT, PATCH) estão desabilitadas. As operações que realizam uma ação (POST) também estão desabilitadas. Você pode recuperar ou excluir recursos (GET, DELETE).|
| **Vencido** | Sua assinatura do Azure tem um pagamento pendente. Sua assinatura ainda está ativa, mas o não pagamento de dívidas pode resultar na desabilitação da assinatura. Para obter mais informações, confira [Resolver o saldo vencido da sua assinatura do Azure](resolve-past-due-balance.md).<br><br>Todas as operações estão disponíveis. |
| **Avisado** | Sua assinatura do Azure está com o estado avisado e será desabilitada em breve se o motivo de aviso não for resolvido. Uma assinatura pode ficar com o estado avisado após passar do prazo, ser cancelada pelo usuário, expirar etc.<br><br>Você poderá recuperar ou excluir recursos (GET/DELETE), mas não poderá criar nenhum recurso (PUT/PATCH/POST) |
