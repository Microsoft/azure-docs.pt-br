---
title: Azure Active Directory e ofertas de SaaS transactáveis no Marketplace comercial
description: Saiba como Azure Active Directory funciona com ofertas de SaaS transformadas no Microsoft Commercial Marketplace.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 674f267d3d99dd22c1ae06b6d32587761d5983ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93124910"
---
# <a name="azure-ad-and-transactable-saas-offers-in-the-commercial-marketplace"></a>Ofertas do Azure AD e SaaS transactáveis no Marketplace comercial

O serviço de gerenciamento de identidade e acesso baseado em nuvem da Microsoft, [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) ajuda os usuários a entrar e acessar recursos internos e externos. No Microsoft Commercial Marketplace, o Azure AD torna as ofertas de SaaS mais fáceis e seguras para todos, incluindo publicadores, compradores e usuários. Com o Azure AD, os editores podem automatizar o provisionamento de usuários para seus aplicativos SaaS (software como serviço), e os próprios compradores podem gerenciar esses usuários provisionados. 

Além disso, o SSO ( [logon único) do Azure ad](../active-directory/manage-apps/what-is-single-sign-on.md) fornece segurança e conveniência quando os usuários entram em aplicativos no Azure AD. O envolvimento mais rápido e experiências otimizadas também inspiram a confiança do comprador e do usuário da primeira interação com o aplicativo SaaS de um editor. Isso dá uma impressão positiva que cria visibilidade e incentiva a repetição de negócios.

Seguindo as orientações deste artigo, você ajudará a certificar sua oferta de SaaS no Marketplace comercial. Para obter mais detalhes sobre a certificação, leia as [políticas de certificação do Marketplace comercial](/legal/marketplace/certification-policies#100-general)detalhadas, incluindo aquelas [específicas para SaaS](/legal/marketplace/certification-policies#1000-software-as-a-service-saas).

## <a name="before-you-begin"></a>Antes de começar

Ao [criar sua oferta de SaaS](./create-new-saas-offer.md) no Partner Center, você escolhe um conjunto de opções de listagem específicas que serão exibidas na listagem de oferta. Sua escolha determina como sua oferta é transacionada no mercado comercial. As ofertas vendidas pela Microsoft são chamadas de ofertas de transações. Cobramos o cliente em seu nome para todas as ofertas de transações. Se você optar por vender pela Microsoft e tiver as transações de host dos EUA em seu nome (a opção **Sim** ), você optou por criar uma oferta proativa e este artigo é para você. É recomendável que você o leia em sua totalidade.

Se você optar por listar apenas sua oferta por meio do Marketplace comercial e processar as transações de forma independente (a opção **não** ), terá três opções de como os clientes potenciais acessarão sua oferta: Obtenha agora (gratuito), avaliação gratuita e entre em contato comigo. Se você selecionar **obter agora (gratuito)** ou **avaliação gratuita**, este artigo não será para você. Em vez disso, consulte [criar a página de aterrissagem para sua oferta de SaaS gratuita ou de avaliação no Marketplace comercial](./azure-ad-free-or-trial-landing-page.md) para obter mais informações. Se você selecionar **entrar em contato comigo**, não haverá responsabilidades diretas do editor. Continue criando sua oferta no Partner Center.

## <a name="how-azure-ad-works-with-the-commercial-marketplace-for-saas-offers"></a>Como o Azure AD funciona com o Marketplace comercial para ofertas de SaaS

O Azure AD permite a compra, o cumprimento e o gerenciamento contínuos de soluções do Marketplace comercial. A Figura 1 mostra como o Publicador, o comprador e o usuário interagem para comprar e ativar uma assinatura. Ele também mostra como os clientes usam e gerenciam aplicativos SaaS que eles obtêm do Marketplace comercial. Para os fins desta ilustração, o comprador é o usuário do aplicativo SaaS que inicia uma compra do Marketplace comercial.

Como mostra a Figura 1, quando um comprador seleciona sua oferta, ele aciona uma cadeia de fluxos de trabalho que inclui compra, assinatura e gerenciamento de usuários. Nessa cadeia, você como o Publicador é responsável por determinados requisitos, com a Microsoft fornecendo suporte em pontos-chave.

***Figura 1: usando o Azure AD para ofertas de SaaS no Marketplace comercial***

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow.png" alt-text="Ilustra as etapas de processo de gerenciamento de compra, gerenciamento de assinaturas e gerenciamento de usuário opcional.":::

As seções a seguir fornecem detalhes sobre os requisitos para cada etapa do processo.

## <a name="process-steps-for-purchase-management"></a>Etapas do processo para gerenciamento de compra

Esta figura mostra as quatro etapas do processo para o gerenciamento de compras.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-1-4.png" alt-text="Ilustra as quatro etapas de processo para o gerenciamento de compras.":::

Esta tabela fornece detalhes para as etapas do processo de gerenciamento de compras.

| Etapa do processo | Ação do Publicador | Recomendado ou necessário para Publicadores |
| ------------ | ------------- | ------------- |
| 1. o comprador entra no Marketplace comercial com sua identidade de ID do Azure e seleciona uma oferta de SaaS. | Nenhuma ação do Publicador necessária. | Não aplicável |
| 2. após a compra, o comprador seleciona **configurar conta** no Azure Marketplace ou **Configurar agora** em AppSource, que direciona o comprador para a página de aterrissagem do editor para esta oferta. O comprador deve ser capaz de entrar no aplicativo SaaS do Publicador com o SSO do Azure AD e deve ser solicitado apenas pelo mínimo de consentimento que não exija a aprovação do administrador do Azure AD. | Crie uma [página de aterrissagem](azure-ad-transactable-saas-landing-page.md) para a oferta para que ela receba um usuário com sua identidade do Azure AD ou conta Microsoft (MSA) e facilite qualquer provisionamento ou configuração adicional necessária. | Obrigatório |
| 3. o Publicador solicita detalhes de compra da API de preenchimento de SaaS. | Usando um [token de acesso](./partner-center-portal/pc-saas-registration.md) gerado a partir da ID do aplicativo da página de aterrissagem, [chame o ponto de extremidade de resolução](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) para recuperar informações específicas sobre a compra. | Obrigatório |
| 4. por meio do Azure AD e da API de Microsoft Graph, o Publicador reúne os detalhes da empresa e do usuário necessários para provisionar o comprador no aplicativo SaaS do editor.  | Decompote o token de usuário do Azure AD para localizar o nome e o email, ou [chamar a API de Microsoft Graph](/graph/use-the-api) e usar permissões delegadas para [recuperar informações](/graph/api/user-get) sobre o usuário que está conectado. | Obrigatório |
||||

## <a name="process-steps-for-subscription-management"></a>Etapas do processo para gerenciamento de assinatura

Esta figura mostra as duas etapas de processo para o gerenciamento de assinatura.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-5-6.png" alt-text="Ilustra as duas etapas de processo para o gerenciamento de assinatura.":::

Esta tabela descreve os detalhes sobre as etapas do processo de gerenciamento de assinaturas.

| Etapa do processo | Ação do Publicador | Recomendado ou necessário para Publicadores |
| ------------ | ------------- | ------------- |
| 5. o Publicador gerencia a assinatura para o aplicativo SaaS por meio da API de preenchimento de SaaS. | Manipule alterações de assinatura e outras tarefas de gerenciamento por meio das [APIs de preenchimento de SaaS](./partner-center-portal/pc-saas-fulfillment-api-v2.md).<br><br>Esta etapa requer um token de acesso, conforme descrito na etapa 3 do processo. | Obrigatório |
| 6. ao usar preços medidos, o Publicador emite eventos de uso para a API de serviço de medição. | Se seu aplicativo SaaS apresenta a cobrança baseada em uso, faça notificações de uso por meio das [APIs do serviço de medição do Marketplace](./partner-center-portal/marketplace-metering-service-apis.md).<br><br>Esta etapa requer um token de acesso, conforme descrito na etapa 3. | Necessário para a medição |
||||

## <a name="process-steps-for-user-management"></a>Etapas do processo para gerenciamento de usuários

Esta figura mostra as três etapas de processo para o gerenciamento de usuários.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-7-9.png" alt-text="Ilustra as três etapas de processo opcionais para o gerenciamento de usuários.":::

As etapas de processo 7 a 9 são etapas opcionais do processo de gerenciamento de usuário. Eles fornecem benefícios adicionais para Publicadores que dão suporte ao SSO (logon único) do Azure AD. Esta tabela descreve os detalhes sobre as etapas do processo de gerenciamento de usuário.

| Etapa do processo | Ação do Publicador | Recomendado ou necessário para Publicadores |
| ------------ | ------------- | ------------- |
| 7. os administradores do Azure AD na empresa do comprador podem, opcionalmente, gerenciar o acesso a usuários e grupos por meio do Azure AD. | Nenhuma ação do Publicador é necessária para habilitar isso se o SSO do Azure AD estiver configurado para os usuários (etapa 9). | Não aplicável |
| 8. o serviço de provisionamento do Azure AD comunica as alterações entre o Azure AD e o aplicativo SaaS do editor. | [Implemente um ponto de extremidade scim](../active-directory/app-provisioning/use-scim-to-provision-users-and-groups.md) para receber atualizações do Azure ad à medida que os usuários são adicionados e removidos. | Recomendadas |
| 9. depois que o aplicativo é permissão e provisionado, os usuários da empresa do comprador podem usar o SSO do Azure AD para fazer logon no aplicativo SaaS do editor. | [Use o SSO do Azure ad](../active-directory/manage-apps/what-is-single-sign-on.md) para permitir que os usuários entrem uma vez com uma conta para o aplicativo SaaS do editor. | Recomendadas |
||||

## <a name="next-steps"></a>Próximas etapas

- [Crie a página de aterrissagem para sua oferta de SaaS transactável no Marketplace comercial](azure-ad-transactable-saas-landing-page.md)
- [Crie a página de aterrissagem para sua oferta de SaaS gratuita ou de avaliação no Marketplace comercial](azure-ad-free-or-trial-landing-page.md)
- [Como criar uma oferta de SaaS no Marketplace comercial](create-new-saas-offer.md)