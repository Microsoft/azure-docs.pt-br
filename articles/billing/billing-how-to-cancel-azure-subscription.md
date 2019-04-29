---
title: Cancelar sua assinatura do Azure | Microsoft Docs
description: Descreve como cancelar sua assinatura do Azure, como a assinatura de Avaliação Gratuita
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/23/2019
ms.author: banders
ms.openlocfilehash: 374224663323e75a1d1413c47490bd3aacd00267
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60918347"
---
# <a name="cancel-your-subscription-for-azure"></a>Cancelar sua assinatura do Azure

É possível cancelar sua assinatura do Azure como o [Administrador da Conta](billing-subscription-transfer.md#whoisaa). Depois de cancelar a assinatura, seu acesso aos recursos e serviços do Azure será encerrado.

Antes de cancelar sua assinatura:

* Faça backup dos dados. Por exemplo, se você estiver colocando dados no armazenamento do Azure ou SQL, baixe uma cópia. Se você tiver uma máquina virtual, salve uma imagem dela localmente.
* Finalize seus serviços. Vá para a [página de recursos no portal de gerenciamento](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources), e **Pare** quaisquer máquinas virtuais, aplicativos ou outros serviços em execução.
* Considere migrar seus dados. Consulte [Mover recursos para um novo grupo de recursos ou uma nova assinatura](../azure-resource-manager/resource-group-move-resources.md).

Se você cancelar um plano de Suporte do Azure pago, ainda será cobrado pelo restante do período de assinatura. Para obter mais informações, consulte [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-using-the-azure-portal"></a>Cancelar uma assinatura usando o portal do Azure

1. Selecione sua assinatura na [página Assinaturas no Portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Selecione a assinatura que você quer cancelar.
3. Selecione **Visão Geral** e, em seguida, selecione **Cancelar assinatura**.

    ![Captura de tela que mostra o botão Cancelar](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. Siga os prompts e conclua o cancelamento.

## <a name="what-happens-after-i-cancel-my-subscription"></a>O que acontecerá depois que eu cancelar minha assinatura?

Após o cancelamento, a cobrança será interrompida imediatamente. No entanto, pode levar até 10 minutos para que o cancelamento seja mostrado no portal.

Depois disso, os serviços são desabilitados. Isso significa que as máquinas virtuais são desalocadas, os endereços IP temporários são liberados e o armazenamento é somente leitura.

Se você cancelar no meio de um período de cobrança, enviaremos a cobrança final na data da fatura normal após o término do período. 

Aguardaremos 90 dias antes de excluir permanentemente seus dados caso você precise acessá-los ou mude de ideia. Você não é cobrado pela retenção dos dados. Para saber mais, consulte a [Central de Confiabilidade da Microsoft – Como gerenciamos seus dados](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Reativar a assinatura

Se você cancelou sua assinatura Pré-paga acidentalmente, poderá [reativá-la no Centro de Contas](billing-subscription-become-disable.md).

Caso sua assinatura não seja Pré-paga, contate o suporte em até 90 dias após o cancelamento para reativar a assinatura.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de Ajuda, [criar uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
