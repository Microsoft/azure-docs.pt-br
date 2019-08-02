---
title: APIs do serviço de medição do Marketplace-perguntas frequentes | Azure Marketplace
description: Emitir o uso de uma oferta de SaaS no Azure Marketplace.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: ccab7e59eaa925df4ba46447cef458111dc7e60a
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869567"
---
# <a name="marketplace-metering-service-apis---faq"></a>APIs de serviço de medição do Marketplace – perguntas frequentes

Depois que um usuário do Azure assina um serviço SaaS que inclui cobrança limitada, você acompanhará o consumo de cada dimensão de cobrança usada pelo cliente. Se o consumo exceder as quantidades incluídas definidas para o termo selecionado pelo cliente, seu serviço emitirá eventos de uso para a Microsoft.

## <a name="emit-usage-events"></a>Emitir eventos de uso

>[!Note]
>Esta seção é aplicável somente para ofertas de SaaS, nas quais pelo menos um dos planos tem dimensões de serviço de medição definidas no momento da publicação da oferta.

![Emitir eventos de uso](media/isv-emits-usage-event.png)

Consulte a [API de evento SaaS batch Usage](./marketplace-metering-service-apis.md#batch-usage-event) para obter informações sobre o contrato de API para emissão de eventos de uso.

### <a name="how-often-is-it-expected-to-emit-usage"></a>Com que frequência é esperado que ele emita o uso?

Idealmente, você deve emitir o uso a cada hora na última hora, somente se houver uso na hora anterior.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>Qual é o atraso máximo entre a hora em que um evento ocorre e a hora em que um evento de uso é emitido para a Microsoft?

O ideal é que o evento de uso seja emitido a cada hora para eventos que ocorreram na última hora. No entanto, os atrasos são esperados. O atraso máximo permitido é de 24 horas, após o qual os eventos de uso não serão aceitos.

Por exemplo, se um evento de uso ocorrer em 1 PM em um dia, você terá até 1 PM no dia seguinte para emitir um evento de uso associado a esse evento. Isso significa que, no caso do sistema que está emitindo o uso tem um tempo de inatividade, ele pode recuperar e, em seguida, enviar o evento de uso para o intervalo de hora em que o uso ocorreu, sem perda de fidelidade.

### <a name="what-happens-when-you-send-more-than-one-usage-event-on-the-same-hour"></a>O que acontece quando você envia mais de um evento de uso na mesma hora?

Apenas um evento de uso é aceito para o intervalo de horas. O intervalo de horas começa no minuto 0 e termina em minuto 59.  Se mais de um evento de uso for emitido para o mesmo intervalo de hora, todos os eventos de uso subsequentes serão removidos como duplicatas.

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>O que acontece quando você emite o uso de uma assinatura de SaaS que já tinha o assinatura cancelada?

Qualquer evento de uso emitido para a plataforma do Marketplace não será aceito depois que uma assinatura de SaaS tiver sido excluída.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>Você pode obter uma lista de todas as assinaturas de SaaS, incluindo assinaturas ativas e não assinadas?

Sim, quando você chama a `GET /saas/subscriptions` API, ela inclui uma lista de todas as assinaturas de SaaS. O campo status na resposta para cada assinatura de SaaS captura se a assinatura está ativa ou não assinada. A chamada para listar assinaturas retorna um máximo de 100 assinaturas no momento.

## <a name="next-steps"></a>Próximas etapas

- Consulte [APIs do serviço de medição do Marketplace](./marketplace-metering-service-apis.md) para obter mais informações.
