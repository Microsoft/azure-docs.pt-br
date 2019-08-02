---
title: Limite de gastos do Azure | Microsoft Docs
description: Este artigo descreve como um limite de gastos do Azure funciona e como removê-lo.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: fb43f29827309fc8986ee6b4653f5edf303cc21d
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490607"
---
# <a name="azure-spending-limit"></a>Limite de gastos do Azure

O limite de gastos no Azure evita gastos ao longo de seu valor de crédito. Todos os novos clientes que se inscreverem para uma avaliação do Azure ou para ofertas que incluem créditos durante vários meses terão um limite de gastos ativado por padrão. O limite de gastos é de $0. Isso não pode ser alterado. O limite de gastos não está disponível para assinaturas em alguns planos de tais planos de compromisso e planos com preços pré-pagos. Consulte a [lista completa de ofertas do Azure e a disponibilidade do limite de gastos](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Atingir um limite de gastos

Quando seu uso resultar em encargos que consomem todas as quantias mensais incluídas em sua assinatura do Azure, os serviços que você implantou são desabilitados para o restante do período de cobrança.

Por exemplo, quando gastar todo o crédito incluído em sua assinatura, os recursos do Azure que você implantou são removidos da produção e suas máquinas virtuais do Azure são interrompidas e desalocadas. Os dados em suas contas de armazenamento estão disponíveis como somente leitura.

No início do próximo período de cobrança, se sua oferta de assinatura incluir créditos durante vários meses, sua assinatura será habilitada novamente automaticamente. Em seguida, você pode reimplantar seus recursos do Azure e têm acesso total a suas contas de armazenamento e os bancos de dados.

O Azure envia notificações quando atingir o gastos limitam para sua assinatura de email. Entrar para o [Centro de contas](https://account.windowsazure.com/Subscriptions) para ver as notificações sobre assinaturas que atingiram o limite de gastos.

Se você tiver uma assinatura de avaliação gratuita e atinge o limite de gastos, você pode atualizar para um plano com [pré-pago](billing-upgrade-azure-subscription.md) preços para remover os gastos limitar e habilita automaticamente a assinatura.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Remover o limite de gastos no Centro de Contas

Você pode remover o limite de gastos a qualquer momento, desde que haja um método de pagamento válido associado à sua assinatura do Azure. Para ofertas que têm crédito em vários meses, você também pode habilitar o limite de gastos no início do período de cobrança seguinte.

Para remover seu limite de gastos, execute estas etapas:

1. Entrar para o [Centro de contas](https://account.windowsazure.com/Subscriptions).
1. Selecione uma assinatura. Se a assinatura está desabilitada porque o limite de gasto foi atingido, clique na notificação: **Assinatura alcançou o limite de gastos e foi desabilitada para evitar encargos.** Caso contrário, clique em **Remover limite de gastos** na área **STATUS DA ASSINATURA**.
1. Selecione uma opção que é adequada para você.

![Selecionando uma opção para remover o limite de gastos](./media/billing-spending-limit/remove-spending-limit.PNG)

| Opção | Efeito |
| --- | --- |
| Remover o limite de gastos indefinidamente | Remove o limite de gastos sem ativá-lo automaticamente no início do próximo período de cobrança. |
| Remover limite de gastos para o período de cobrança atual | Remove o limite de gastos para que ele seja ativado automaticamente no início do próximo período de cobrança. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Por que você talvez queira remover o limite de gastos

O limite de gastos pode evitar que você implante ou use determinados serviços da Microsoft e de terceiros. Aqui estão as situações em que você deve remover o limite de gastos em sua assinatura.

-  Você planeja implantar imagens primárias como o Oracle e serviços como o Azure DevOps Services. Essa situação faz com que você exceda seu limite de gastos quase imediatamente e faz com que sua assinatura será desabilitada.
- Você tem serviços que você não deseja sofrer uma interrupção.
- Você tem serviços e recursos com configurações como endereços IP virtuais e que não deseja perder. Essas configurações são perdidas quando os serviços e os recursos são desalocados.

## <a name="turn-on-the-spending-limit-after-removing"></a>Ativar o limite de gastos após a remoção

Esse recurso está disponível somente quando o limite de gastos foi removido indefinidamente. Altere-o para ativá-lo automaticamente no início do próximo período de cobrança.

1. Entrar para o [Centro de contas](https://account.windowsazure.com/Subscriptions).
1. Clique na faixa amarela para alterar a opção de limite de gastos.
1. Escolha **Ativar o limite de gastos no próximo período de cobrança \<start date of billing period\>**

## <a name="custom-spending-limit"></a>Personalizar o limite de gastos

Limites de gastos personalizados não estão disponíveis.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>Um limite de gastos não impede que todos os encargos

[Alguns serviços externos publicados no Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) não pode ser usado com seus créditos de assinatura e podem causar cobranças separadas, mesmo quando o limite de gastos é definido. Exemplos incluem licenças do Visual Studio, o Azure Active Directory Premium, planos de suporte e a maior parte dos serviços de terceiros. Quando você provisiona um novo serviço externo, é mostrado um aviso informando que os serviços são cobrados separadamente:

![Aviso de compra no Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de Ajuda, [criar uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas
- Atualize para um plano com [pré-pago](billing-upgrade-azure-subscription.md) preços.
