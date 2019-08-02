---
title: APIs de preenchimento de SaaS-perguntas frequentes | Azure Marketplace
description: Experiências de descoberta e compra pelos clientes de uma oferta de SaaS no Azure Marketplace.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: b324d3b9dca710dca6f5f99ad50ce4d973a42d2a
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869541"
---
# <a name="saas-fulfillment-apis---faq"></a>APIs de cumprimento de SaaS – perguntas frequentes

Os requisitos de integração com o Azure Marketplace para permitir que os clientes do Azure assinem a oferta de SaaS estão listados.

## <a name="discovery-experience"></a>Experiência de descoberta

Depois que a oferta for publicada, os usuários do Azure poderão descobrir a oferta de SaaS no Azure Marketplace. Seus clientes poderão filtrar as ofertas com base no tipo de produto (SaaS) e descobrir os serviços SaaS em que estão interessados.

## <a name="purchase-experience"></a>Experiência de compra

Quando um usuário estiver interessado em um serviço SaaS específico, o usuário poderá assiná-lo do Azure Marketplace.

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>O que significa para um usuário do Azure assinar uma oferta de SaaS no Azure Marketplace?

Isso significa que um usuário pode exibir os termos de uso e a política de privacidade associada ao serviço SaaS e concordar em pagar por ele de acordo com os termos de cobrança definidos por você, o editor da oferta de SaaS, na fatura da Microsoft. Os usuários podem usar seu perfil de pagamento existente no Azure para pagar pelo consumo do serviço SaaS.

Isso é benéfico por muitos motivos. Agora, os clientes podem descobrir e assinar um único local usando Microsoft Cloud plataforma como fonte confiável, sem precisar examinaremos todos os softwares de ISV que pretendem usar. Além disso, os clientes podem usar seu perfil de pagamento existente sem ter que pagar explicitamente cada software ISV independentemente.

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>O usuário é cobrado automaticamente quando a oferta é assinada?

Ao assinar a oferta de SaaS, o usuário concordou em pagar pelo consumo do serviço SaaS por meio da plataforma Microsoft. No entanto, os encargos só serão iniciados quando a oferta for consumida. O usuário deve acessar sua oferta de SaaS e confirmar a criação da conta para começar a consumir a oferta. Em seguida, você notificará a Microsoft para iniciar a cobrança para essa assinatura de SaaS do cliente.

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>Como você é notificado quando um usuário assina sua oferta de SaaS?

Depois de assinar uma oferta, o usuário do Azure pode descobrir e gerenciar todas as suas ofertas no Azure. Por padrão, o estado de uma oferta de SaaS recém assinada é **' provisionamento, preenchimento pendente '** . Nesse estado, o usuário do Azure receberá uma ação para **' configurar conta '** , a fim de navegar até sua experiência de gerenciamento de assinatura de SaaS no portal do Azure.

Quando o usuário clicar em **' configurar conta '** , ele será redirecionado para o site do serviço SaaS. A URL para a qual eles são navegados é fornecida pelo Publicador no momento da publicação da oferta. Essa página é chamada de página de aterrissagem do editor. Os usuários do Azure devem ser capazes de fazer logon na página de aterrissagem de SaaS com base em suas credenciais do AAD existentes no Azure.

Quando o usuário do Azure é redirecionado para a página de aterrissagem, um token é adicionado à URL de consulta. Esse token é de vida curta e válido por um período de 24 horas. Em seguida, você pode detectar a presença desse token e chamar a API da Microsoft para obter mais contexto associado ao token.

![Fluxo de assinatura do cliente](media/saas-metering-service-integration-flow-a.png)

Consulte o documento [API de preenchimento de SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) para obter mais informações sobre o contrato de API para lidar com cenários de Transact no ciclo de vida de uma oferta de SaaS.

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>Como você sabe a oferta de SaaS para a qual o usuário se inscreve no Azure?

A resposta à `Resolve` API inclui a oferta e as informações de plano associadas à assinatura de SaaS.

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Como o usuário do Azure pode alterar o plano associado a esta assinatura do Azure?

* O usuário do Azure pode alterar o plano associado à assinatura de SaaS diretamente na experiência de SaaS ou por meio da plataforma Microsoft.

* As conversões podem ser feitas a qualquer momento no ciclo de cobrança. Você deve reconhecer qualquer conversão, que entrará em vigor depois de ser confirmada.

* As taxas do plano pré-pago (**mensal** ou **anual**) são classificadas pelo pro. Qualquer excedente emitido até o momento da conversão será cobrado na próxima fatura. Novas interrupções serão emitidas com base no novo plano.

>[!Note]
>Você poderá bloquear os downgrades se não quiser dar suporte a caminhos de conversão específicos.

A sequência a seguir captura o fluxo quando um cliente do Azure muda de plano na experiência de SaaS:

![Fluxo de alteração do plano do cliente](media/saas-metering-service-integration-flow-b.png)

A sequência a seguir captura o fluxo quando um cliente do Azure muda de plano na vitrine da Microsoft

![Fluxo de alteração do plano de vitrine do cliente](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Como o usuário do Azure pode cancelar a assinatura do plano associado à assinatura do Azure?

Um usuário do Azure pode cancelar a assinatura de uma oferta de SaaS adquirida diretamente na experiência de SaaS ou por meio da plataforma Microsoft. Depois que o usuário cancelar a assinatura, ele não será mais cobrado do próximo ciclo de cobrança.

A sequência a seguir captura o fluxo quando um cliente do Azure cancela a assinatura para a oferta de SaaS na experiência de SaaS:

![O cliente não assina a experiência de SaaS](media/saas-metering-service-integration-flow-d.png)

A sequência a seguir captura o fluxo quando o usuário do Azure cancela a assinatura na vitrine da Microsoft:

![O cliente não assina na vitrine da Microsoft](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>Próximas etapas

- Consulte [APIs do serviço de medição do Marketplace](./marketplace-metering-service-apis.md) para obter mais informações.
