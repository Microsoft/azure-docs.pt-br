---
title: Cancelar sua assinatura do Azure | Microsoft Docs
description: "Descreve como cancelar sua assinatura do Azure, como a assinatura de Avaliação Gratuita"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.author: genli
ms.openlocfilehash: 51fe2ab891e86ae4bd7402622231af47f35aa01d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="cancel-your-subscription-for-azure"></a>Cancelar sua assinatura do Azure

É possível cancelar sua assinatura do Azure como o [Administrador da Conta](billing-subscription-transfer.md#whoisaa). Depois de cancelar a assinatura, seu acesso aos recursos e serviços do Azure será encerrado.

Antes de cancelar sua assinatura:

* Faça backup dos dados. Por exemplo, se você estiver colocando dados no armazenamento do Azure ou SQL, baixe uma cópia. Se você tiver uma máquina virtual, salve uma imagem dela localmente.
* Finalize seus serviços. Vá para a [página de recursos no portal de gerenciamento](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources), e **Pare** quaisquer máquinas virtuais, aplicativos ou outros serviços em execução.
* Considere migrar seus dados. Consulte [Mover recursos para um novo grupo de recursos ou uma nova assinatura](../azure-resource-manager/resource-group-move-resources.md).

Se você cancelar um [plano de Suporte do Azure](https://azure.microsoft.com/support/plans/) pago, ainda será cobrado mensalmente no restante do termo de 6 meses.

## <a name="cancel-subscription-using-the-azure-portal"></a>Cancelar uma assinatura usando o portal do Azure

1. Selecione sua assinatura na [página Assinaturas no Portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione a assinatura que você deseja cancelar e clique em **Cancelar assinatura**.

    ![Captura de tela que mostra o botão Cancelar](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
1. Siga os prompts e conclua o cancelamento.

## <a name="what-happens-after-i-cancel-my-subscription"></a>O que acontecerá depois que eu cancelar minha assinatura?

Após o cancelamento, a cobrança será interrompida imediatamente. No entanto, pode levar até 10 minutos para que o cancelamento seja mostrado no portal.

Depois disso, os serviços são desabilitados. Isso significa que as máquinas virtuais são desalocadas, os endereços IP temporários são liberados e o armazenamento é somente leitura.

Se você cancelar no meio de um período de cobrança, enviaremos a cobrança final na data da fatura normal após o término do período. 

Aguardaremos 90 dias antes de excluir permanentemente seus dados caso você precise acessá-los ou mude de ideia. Você não é cobrado pela retenção dos dados. Para saber mais, consulte a [Central de Confiabilidade da Microsoft – Como gerenciamos seus dados](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Reativar a assinatura

Se você cancelou sua assinatura Pré-paga acidentalmente, poderá [reativá-la no Centro de Contas](billing-subscription-become-disable.md).

Caso sua assinatura não seja Pré-paga, contate o suporte em até 90 dias após o cancelamento para reativar a assinatura.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
