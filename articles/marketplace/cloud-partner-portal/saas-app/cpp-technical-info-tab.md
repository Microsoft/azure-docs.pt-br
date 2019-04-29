---
title: Configuração técnica da oferta de aplicativo SaaS do Azure | Microsoft Docs
description: Configure as Informações Técnicas para a oferta do aplicativo SaaS no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 891d9b7b34e3d30efb46b69ef1aa75566fe634c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60594327"
---
# <a name="saas-application-technical-info-tab"></a>Guia Informações Técnicas para aplicativo SaaS

A guia Informações Técnicas fornece o formulário de Configurações Técnicas. Use este formulário para escolher o tipo de aplicativo SaaS (aplicativo) que você está criando e configurar como seu aplicativo é fornecido aos clientes.

![Formulário de Configurações Técnicas](./media/saas-techinfo-techconfig.png)

## <a name="technical-configuration-form"></a>Formulário de Configurações Técnicas

Este formulário tem 2 campos: Produto e Chamada para ação.

### <a name="product-field"></a>Campo Produto

Você pode fornecer um aplicativo SaaS para ambas as vitrines a seguir:
- Para um usuário de negócios, selecionando a opção **Lista**.
- Para um usuário administrador de TI, selecionando **Vender por meio da Microsoft**.
Para ajudá-lo a decidir qual tipo de aplicativo SaaS que você está criando, leia [Entender a seleção de vitrine](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type#understand-storefront-selection).

#### <a name="sell-through-microsoft"></a>Vender por meio da Microsoft
Para criar essa experiência, você precisa configurar as seguintes partes:

- Conecte seu site do serviço SaaS usando as APIs SaaS da Microsoft. O artigo [Venda de SaaS por meio do Azure – APIs](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-saas-subscription-apis) explica como criar essa conexão.
- Habilite Vender por meio do Azure no Portal do Cloud Partner na no formulário Configurações Técnicas e forneça as informações solicitadas. Para obter mais informações sobre esse modelo de cobrança e como ele é implementado, confira [SaaS – Vender por meio do Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-saas-offer-subscriptions).

  ![Formulário Vender por meio da Microsoft](./media/saas-techinfo-sellthrough-ms.png)

A tabela a seguir descreve os campos obrigatórios para Venda por meio da Microsoft.

|  **Nome do campo**   |  **Descrição**  |
|  ---------------  |  ---------------  |
|    IDs de assinatura para versão prévia               |    Todos os identificadores de Assinatura do Azure usados para testar sua oferta na visualização antes que ela fique disponível publicamente.               |
|     Instruções de introdução              |   Orientações para compartilhar com seus clientes para ajudá-los a se conectar ao aplicativo SaaS. Marcas HTML básicas são permitidas, por exemplo: &lt;p&gt;, &lt;h1&gt;, &lt;li&gt;, etc.                |
|    URL da página de aterrissagem  |   A URL do site para o qual você direcionará seus clientes após a aquisição no portal do Azure. Essa URL também será o ponto de extremidade que receberá as APIs de conexão para facilitar o comércio com a Microsoft.                |
|  Webhook de conexão    |  Para todos os eventos assíncronos que a Microsoft precisa para enviar para você em nome do cliente (exemplo: Assinatura do Azure está inválida), exigimos que você forneça um webhook de conexão. Se você ainda não tiver um sistema de webhooks em vigor, a configuração mais simples é definir um Aplicativo de lógica de ponto de extremidade HTTP que atenderá todos os eventos que são postados nele e, em seguida, tratá-os adequadamente. Para saber mais, confira <a href="https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint">Chamar, disparar ou aninhar fluxos de trabalho com pontos de extremidade HTTP em aplicativos lógicos</a>                |
|  ID de locatário do Azure AD e ID do aplicativo      |   No portal do Azure, exigimos que você crie um Aplicativo do Active Directory para que possamos validar que a conexão entre nossos dois serviços está por trás de uma comunicação autenticada. Para esses campos, crie um Aplicativo do AD e cole a ID do locatário e a ID do aplicativo correspondentes necessárias. Observe que a ID do aplicativo é associada ao seu publisherID. Portanto, verifique a mesma ID de aplicativo em todos as ofertas.             |


Por fim, se você selecionar **Vender por meio da Microsoft**, há outra guia Nova oferta denominada **Planos**. 

A [guia de planos](./cpp-plans-tab.md) lista os planos específicos e os preços correspondentes compatíveis com seu aplicativo SaaS. A partir de hoje, permitimos preços mensais, com a capacidade de permitir um ou três meses de acesso gratuito. Esses planos e preços devem corresponder aos planos e preços exatos que você tem em seu próprio site do aplicativo SaaS.

>[!NOTE] 
>Os planos são necessários apenas se você escolher Venda por meio da Microsoft.

### <a name="call-to-action-field"></a>Campo Chamada para ação

O campo Chamada para ação permite escolher a mensagem que aparece no botão de aquisição da sua oferta. As seguintes opções estão disponíveis:

- Gratuito – se você escolher essa opção, você será solicitado a digitar uma URL de avaliação em que os clientes podem obter acesso ao seu aplicativo SaaS. Por exemplo: https://contoso.com/trial
- Avaliação gratuita – se você escolher essa opção, você será solicitado a digitar uma URL de avaliação em que os clientes podem obter acesso ao seu aplicativo SaaS. Por exemplo: https://contoso.com/trial
- Entrar em contato comigo

Para obter mais informações sobre as opções de Chamada para ação, consulte a opção Escolher uma publicação.

>[!Note]
>Canal do parceiro do Cloud Solution Providers (CSP) opt-in agora disponíveis.  Consulte [Cloud Solution Providers](../../cloud-solution-providers.md) para obter mais informações sobre sua oferta por meio do Microsoft CSP de marketing de parceiros canais.

## <a name="next-steps"></a>Próximas etapas

- [Guia Planos (opcional)](./cpp-plans-tab.md)
- [Guia Informações do Canal](./cpp-channel-info-tab.md)
