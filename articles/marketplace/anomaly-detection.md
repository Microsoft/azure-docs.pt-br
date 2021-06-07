---
title: Gerenciar anomalias de cobrança limitadas no Partner Center | Azure Marketplace
description: Saiba como a detecção automática de anomalias para cobrança limitada ajuda a garantir que seus clientes sejam cobrados corretamente para uso limitado de suas ofertas de mercado comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 2/18/2021
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 1ff6262a75261c575082f3fc48d588c868ad0b51
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101092515"
---
# <a name="manage-metered-billing-anomalies-in-partner-center"></a>Gerenciar anomalias de cobrança limitadas no Partner Center

A opção de cobrança limitada personalizada está disponível atualmente para ofertas de [software como serviço](plan-saas-offer.md) (SaaS) e [aplicativos do Azure](plan-azure-application-offer.md#types-of-plans) com um plano de aplicativo gerenciado.

Se você estiver usando a opção de cobrança limitada para criar ofertas no programa do Marketplace comercial que permite cobrar pelo uso com base em unidades não padrão, você precisa saber quando o cliente usou mais de um serviço do que o esperado.

## <a name="use-the-anomaly-detection-feature"></a>Usar o recurso de detecção de anomalias

A Microsoft conta com você, o parceiro, para reportar o uso excedente de seus clientes do SaaS ou das ofertas de aplicativos gerenciados pelo Azure antes da Microsoft faturar o cliente. Se o uso incorreto for relatado, o cliente poderá potencialmente receber uma fatura incorreta, submineração da Microsoft e da credibilidade do parceiro.

Para ajudar a garantir que seus clientes sejam cobrados corretamente, use o recurso de **detecção de anomalias** para aplicativos SaaS e planos de aplicativo gerenciados por aplicativo do Azure. Esse recurso monitora o uso em relação à cobrança limitada e prevê o valor esperado de uso dentro do intervalo esperado. Se o uso estiver fora do intervalo esperado, ele será tratado como inesperado (uma anomalia) e você receberá uma notificação de alerta em sua página de visão geral da oferta no programa do Marketplace comercial do Partner Center. Você pode acompanhar o uso de seus clientes diariamente para cada dimensão de medidor personalizado que você definiu.

## <a name="view-and-manage-metered-usage-anomalies"></a>Exibir e gerenciar anomalias de uso medido

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).
1. No menu de navegação à esquerda, selecione análise do **Marketplace comercial**  >  .
1. Selecione a guia **anomalias de uso medido** .

    [![Ilustra a guia anomalias de uso medido na página uso.](./media/anomaly-detection/metered-usage-anomalies.png)](./media/anomaly-detection/metered-usage-anomalies.png#lightbox)
    ***Figura 1: guia de anomalias de uso medido***

1. Para qualquer anomalia de uso detectada em relação à cobrança limitada, como um Publicador, você será solicitado a investigar e confirmar se a anomalia é verdadeira ou não. Selecione **Marcar como anomalia** para confirmar o diagnóstico.

     [![Ilustra a caixa de diálogo marcar como anomalia.](./media/anomaly-detection/mark-as-anomaly.png)](./media/anomaly-detection/mark-as-anomaly.png#lightbox)
    ***Figura 2: caixa de diálogo marcar como uma anomalia***

1. Se você acredita que a anomalia de uso excedente que detectamos não é original, você pode fornecer esses comentários selecionando **não uma anomalia** para a anomalia do Partner Center sinalizada no uso excedente específico.

    [![Ilustra a caixa de diálogo por que ele não é uma anomalia.](./media/anomaly-detection/why-is-it-not-an-anomaly.png)](./media/anomaly-detection/why-is-it-not-an-anomaly.png#lightbox)
    ***Figura 3: por que não é uma anomalia? caixa de diálogo***

1. Você pode rolar a página para ver uma lista de inventários de anomalias não confirmadas. A lista fornece um inventário de anomalias que você não confirmou. Você pode optar por marcar qualquer uma das anomalias sinalizadas pelo Partner Center como autêntico ou false.

   [![Ilustra a lista de anomalias não confirmadas do Partner Center na página uso.](./media/anomaly-detection/unacknowledged-anomalies.png)](./media/anomaly-detection/unacknowledged-anomalies.png#lightbox)
    ***Figura 4: lista de anomalias não confirmadas do Partner Center***

1. Você também verá um log de ações de anomalias que mostra as ações executadas por você nos usos excedentes. No log de ações, você poderá ver quais eventos de uso excedente foram marcados como autênticos ou falsos.

   [ ![ Ilustra o log de ações de anomalias na página uso.](./media/anomaly-detection/anomaly-action-log.png)](./media/anomaly-detection/anomaly-action-log.png#lightbox) 
    ***Figura 5: log de ações de anomalias***

1. A análise do centro de parceiros não dará suporte à reutilização de eventos de uso excedente nos relatórios de exportação. O Partner Center permite que você insira o uso excedente corrigido de uma anomalia e os detalhes são passados para as equipes da Microsoft para investigação. Com base na investigação, a Microsoft emitirá reembolsos de crédito para o cliente encarregado, conforme apropriado. Ao selecionar qualquer uma das anomalias sinalizadas, você pode selecionar **Marcar como anomalia** para marcar a anomalia de uso excedente como original.

   [ ![ Ilustra a caixa de diálogo marcar como uma anomalia.](./media/anomaly-detection/new-reported-usage.png)](./media/anomaly-detection/new-reported-usage.png#lightbox) 
    ***Figura: 6: caixa de diálogo marcar como anomalia***

Na primeira vez que um uso excedente é sinalizado como irregular no Partner Center, você obterá uma janela de 30 dias a partir dessa instância para marcar a anomalia como original ou falsa. Após o período de 30 dias, como o Publicador, você não poderá agir sobre as anomalias.

> [!IMPORTANT]
> As unidades de uso excedente informadas não são elegíveis para reestado e ajuste financeiro.

Você pode ver todas as anomalias (confirmadas ou de outra forma) para o período de computação selecionado. Os vários períodos de computação são os últimos 30 dias, os últimos 60 dias e os últimos 90 dias.

> [!IMPORTANT]
> Você é solicitado a agir sobre as anomalias sinalizadas dentro de 30 dias a partir do momento em que as anomalias são relatadas pela primeira vez no Partner Center.

O filtro modal no Widget permite que você selecione as ofertas individuais e os medidores personalizados individuais.

Depois de marcar um uso excedente como uma anomalia ou reconhecer um modelo que sinalizava uma anomalia como original, não é possível alterar a seleção para "não é uma anomalia".

> [!IMPORTANT]
> Você pode enviar novamente os usos excedentes no caso de situações de sobrecarga.

## <a name="see-also"></a>Veja também
- [Cobrança limitada para SaaS usando o serviço de medição do Marketplace comercial](./partner-center-portal/saas-metered-billing.md)
- [Cobrança limitada do aplicativo gerenciado](./partner-center-portal/azure-app-metered-billing.md)
- [Serviço de detecção de anomalias para cobrança limitada](./partner-center-portal/anomaly-detection-service-for-metered-billing.md)
