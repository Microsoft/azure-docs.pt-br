---
title: Perguntas frequentes sobre APIs de serviço de medição-Microsoft Commercial Marketplace
description: Perguntas frequentes sobre as APIs de serviço de medição para ofertas de SaaS no Microsoft AppSource e no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/01/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: ddec23f695396b8322d51da62158a97456096ae8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87319993"
---
# <a name="marketplace-metered-billing-apis---faq"></a>APIs de cobrança limitada do Marketplace-perguntas frequentes

Depois que um cliente assina um serviço SaaS ou Aplicativo Azure com um plano de aplicativos gerenciados, com cobrança limitada, você acompanhará o consumo de cada dimensão de cobrança que está sendo usada.  Se o consumo exceder as quantidades incluídas definidas para o termo selecionado pelo cliente, seu serviço emitirá eventos de uso para a Microsoft.

## <a name="for-both-saas-offers-and-managed-apps"></a>Para ofertas de SaaS e aplicativos gerenciados

### <a name="how-often-is-it-expected-to-emit-usage"></a>Com que frequência é esperado que ele emita o uso?

Idealmente, você deve emitir o uso a cada hora na última hora, somente se houver uso na hora anterior.

### <a name="is-there-a-maximal-period-between-one-emission-and-the-next-one"></a>Há um período máximo entre uma emissão e a próxima?

Não há essa limitação. Só emita o uso conforme ele ocorrer. Por exemplo, se você só precisa enviar uma unidade de uso por tempo de vida de assinatura, você pode fazer isso.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>Qual é o atraso máximo entre a hora em que um evento ocorre e a hora em que um evento de uso é emitido para a Microsoft?

O ideal é que o evento de uso seja emitido a cada hora para eventos que ocorreram na última hora. No entanto, os atrasos são esperados. O atraso máximo permitido é de 24 horas, após o qual os eventos de uso não serão aceitos. A prática recomendada é coletar o uso por hora e emitir como um evento no final da hora.

Por exemplo, se um evento de uso ocorrer em 1 PM em um dia, você terá até 1 PM no dia seguinte para emitir um evento de uso associado a esse evento.  Caso o sistema que está emitindo o uso esteja inativo, ele pode recuperar e, em seguida, enviar o evento de uso para o intervalo de horas em que o uso ocorreu, sem perda de fidelidade.

Se 24 horas tiverem passado após o uso real, você ainda poderá emitir as unidades consumidas com eventos de uso posteriores.  No entanto, essa prática pode prejudicar a credibilidade dos relatórios de eventos de cobrança para o cliente final.  Recomendamos que você evite enviar a emissão de medidores uma vez por dia/semana/mês.  Será mais difícil entender o uso real por um cliente e resolver problemas ou perguntas que podem ser geradas com relação a eventos de uso.

Outro motivo para enviar o uso a cada hora é evitar situações em que o usuário cancela a assinatura antes que o Publicador envie o evento de emissão diária/semanal/mensal.

### <a name="what-happens-when-you-send-more-than-one-usage-event-in-the-same-hour"></a>O que acontece quando você envia mais de um evento de uso na mesma hora?

Apenas um evento de uso é aceito para o intervalo de uma hora. O intervalo de horas começa no minuto 0 e termina em minuto 59.  Se mais de um evento de uso for emitido para a mesma hora, todos os eventos de uso subsequentes serão removidos como duplicatas.

### <a name="what-happens-when-the-customer-cancels-the-purchase-within-the-time-allowed-by-the-cancellation-policy"></a>O que acontece quando o cliente cancela a compra dentro do tempo permitido pela política de cancelamento?

O valor da taxa fixa não será cobrado, mas o uso excedente será.

### <a name="can-custom-meter-plans-be-used-for-one-time-payments"></a>Os planos de medidores personalizados podem ser usados para pagamentos de uma só vez?

Sim, você pode definir uma dimensão personalizada como uma unidade de pagamento ocasional e emiti-la apenas uma vez para cada cliente.

### <a name="can-custom-meter-plans-be-used-to-tiered-pricing-model"></a>Os planos de medidores personalizados podem ser usados para o modelo de preços em camadas?

Sim, ele pode ser implementado com cada dimensão personalizada que representa um único tipo de preço.

Por exemplo, a contoso deseja cobrar $0.05 por email pelos primeiros 1000 emails, $0.04 por email entre 1000 e 5000 emails e $0.02 por email para os emails acima da 5000. Eles podem definir três dimensões personalizadas que correspondem aos três tipos de preço por email. As unidades de emissão da primeira dimensão serão contanto que o número de emails permaneça abaixo de 1000 e as unidades da segunda dimensão quando o número de emails estiver entre 1000 e 5000 e, por fim, as unidades da terceira dimensão para os emails acima de 5000.

### <a name="what-happens-if-the-marketplace-metering-service-has-an-outage"></a>O que acontecerá se o serviço de medição do Marketplace tiver uma interrupção?

Se o ISV envia um medidor personalizado e recebe um erro, que pode ter sido causado por um problema no lado da Microsoft (normalmente, no caso de eventos semelhantes terem sido aceitos antes sem um erro), o ISV deve aguardar e tentar realizar a emissão novamente.

Se o erro persistir, reenvie esse medidor personalizado na próxima hora (acumule a quantidade). Continue esse processo até que uma resposta sem erro seja recebida.

## <a name="for-saas-offers-only"></a>Somente para ofertas de SaaS

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>O que acontece quando você emite o uso de uma assinatura de SaaS que já tinha o assinatura cancelada?

Qualquer evento de uso emitido para a plataforma do Marketplace não será aceito depois que uma assinatura de SaaS tiver sido excluída.

O uso pode ser emitido somente para assinaturas no status de assinatura (e não para assinaturas no `PendingFulfillmentStart` , `Suspended` ou `Unsubscribed` status).

A única exceção é o uso de relatórios para o tempo antes que a assinatura de SaaS tenha sido cancelada.

Por exemplo, o cliente cancelou a assinatura de SaaS hoje às 15:00. Agora é 5 PM, o Publicador ainda pode emitir o uso do período entre 6 PM ontem e 3 PM hoje para essa assinatura de SaaS.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>Você pode obter uma lista de todas as assinaturas de SaaS, incluindo assinaturas ativas e não assinadas?

Sim, quando você chama a [API obter a lista de assinaturas](pc-saas-fulfillment-api-v2.md#subscription-apis) , pois ela inclui uma lista de todas as assinaturas de SaaS. O campo status na resposta para cada assinatura de SaaS captura se a assinatura está ativa ou não assinada.

### <a name="are-the-start-and-end-dates-of-saas-subscription-term-and-overage-usage-emission-connected"></a>As datas de início e término do período de assinatura SaaS e a emissão de uso excedente estão conectadas?

Eventos excedentes podem ser emitidos a qualquer momento para a assinatura de SaaS existente no status *assinado* . É responsabilidade do editor emitir eventos de uso com base na política definida no plano de cobrança. O excedente deve ser calculado com base nas datas definidas no termo da assinatura de SaaS. 

Por exemplo, se o Publicador definir um plano de SaaS que inclua 1000 emails para $100 em taxa fixa mensal, cada email acima de 1000 será cobrado $1 por meio de dimensão personalizada.

Quando o cliente adquire e ativa a assinatura em 6 de Janeiro, o email 1000 incluído na taxa fixa será contado a partir deste dia. Portanto, se até 5 de fevereiro (fim do primeiro mês da assinatura), somente 900 emails são enviados, o cliente pagará a taxa fixa somente pelo primeiro mês dessa assinatura e nenhum evento de uso excedente será emitido pelo Publicador entre 6 de Janeiro e 5 de fevereiro. Em 6 de fevereiro, a assinatura será renovada automaticamente e a contagem será iniciada novamente. Se, em 15 de fevereiro, o cliente tiver atingido 1000 emails enviados, o restante dos emails enviados até 5 de março será cobrado como excedente ($1 por email) com base nos eventos de uso excedente emitidos pelo Publicador.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações, consulte [APIs de serviço de medição do Marketplace](./marketplace-metering-service-apis.md).
