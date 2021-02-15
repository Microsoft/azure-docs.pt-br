---
title: Detecção de anomalias para cobrança limitada | Azure Marketplace
description: Saiba como a detecção automática de anomalias para cobrança limitada ajuda a garantir que seus clientes sejam cobrados corretamente para uso limitado da sua oferta de Marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 1/09/2021
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: d4fb88854359dcd6e383b47d2a8ce4e9c91f867a
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989280"
---
# <a name="anomaly-detection-for-metered-billing"></a>Detecção de anomalias para cobrança limitada

Este artigo fornece detalhes sobre o serviço de medição do Marketplace e a capacidade de detecção de anomalias automatizada associada para ajudar a garantir que possamos faturar os clientes corretamente para seu uso medido. A opção de cobrança limitada está disponível atualmente para ofertas de [software como serviço](plan-saas-offer.md) (SaaS) e [aplicativos do Azure](plan-azure-application-offer.md#types-of-plans) com um plano de aplicativo gerenciado. Essa opção permite que os parceiros criem ofertas no programa comercial do Marketplace que são cobrados de acordo com as unidades não padrão.

Os parceiros que têm medidores personalizados implantados para SaaS e aplicativos gerenciados podem ver o desvio do comportamento de uso esperado como anomalias para os _eventos excedentes_ em _medidores personalizados_ específicos no Partner Center. Para atenuar o risco, o Partner Center usa um serviço de detecção de anomalias que aplica os algoritmos de aprendizado de máquina para determinar o comportamento de cobrança medido normal, analisar o uso de cobrança medido e descobrir anomalias com pouca intervenção do usuário. Usando _modelos de detecção de anomalias_ nos conjuntos de dados de uso de cobrança medido, o Partner Center visa informar o Publicador quando o uso relatado excede o uso esperado.

## <a name="usability-experience"></a>Experiência de usabilidade

A Microsoft conta com o parceiro para reportar o uso excedente do seu cliente do SaaS ou das ofertas de aplicativos gerenciados pelo Azure antes da Microsoft faturar o cliente. Se o uso incorreto for relatado, o cliente poderá potencialmente receber uma fatura incorreta, submineração da Microsoft e da credibilidade do parceiro.

Para ajudar a mitigar isso, um recurso automatizado de detecção de anomalias é fornecido para aplicativos SaaS e planos de aplicativo gerenciados por aplicativos do Azure. Esse recurso é um modelo de aprendizado de máquina que monitora de forma proativa o uso em relação à cobrança limitada e prevê o valor esperado de uso dentro do intervalo esperado. Se o uso estiver fora do intervalo esperado, ele será tratado como uma anomalia e uma notificação de alerta será exibida para o parceiro na página Visão geral da oferta no programa do Marketplace comercial do Partner Center.

O modelo de aprendizado de máquina analisa o uso excedente diário. O Publicador pode ver todas as anomalias relatadas no uso excedente do seu cliente para as dimensões de medidor personalizado de cada oferta.

### <a name="view-and-manage-metered-usage-anomalies"></a>Exibir e gerenciar anomalias de uso medido

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

## <a name="see-also"></a>Consulte também
- [Cobrança limitada para SaaS usando o serviço de medição do Marketplace comercial](./partner-center-portal/saas-metered-billing.md)
- [Cobrança limitada do aplicativo gerenciado](./partner-center-portal/azure-app-metered-billing.md)
