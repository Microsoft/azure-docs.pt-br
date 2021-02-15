---
title: Como criar uma oferta de SaaS no Microsoft Commercial Marketplace
description: Saiba como criar uma nova oferta de SaaS (software como serviço) para listagem ou venda no Microsoft AppSource, no Azure Marketplace ou no programa CSP (provedor de soluções na nuvem) usando o portal do Marketplace comercial no Microsoft Partner Center.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: f689993ce56a1125a1d1de8f65ce05d01f776ea9
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130061"
---
# <a name="how-to-create-a-saas-offer-in-the-commercial-marketplace"></a>Como criar uma oferta de SaaS no Marketplace comercial

Como um editor de mercado comercial, você pode criar uma oferta de SaaS (software como serviço) para que clientes potenciais possam comprar sua solução técnica baseada em SaaS. Este artigo explica o processo para criar uma oferta de SaaS para o Marketplace comercial da Microsoft.

## <a name="before-you-begin"></a>Antes de começar

Se você ainda não tiver feito isso, leia [planejar uma oferta de SaaS para o Marketplace comercial](plan-saas-offer.md). Ele explicará os requisitos técnicos para seu aplicativo SaaS e as informações e os ativos que você precisará ao criar sua oferta. A menos que você planeje publicar uma listagem simples (opção de listagem de **contato comigo** ) no Marketplace comercial, seu aplicativo SaaS deve atender aos requisitos técnicos de autenticação.

## <a name="create-a-new-saas-offer"></a>Criar uma oferta de SaaS

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).
1. No menu de navegação à esquerda, selecione **Commercial Marketplace**  >  **visão geral** do Marketplace comercial.
1. Na guia **visão geral** , selecione **+ nova oferta**  >  **software como um serviço** .

   :::image type="content" source="media/new-offer-saas.png" alt-text="Ilustra o menu de navegação à esquerda e a nova lista de ofertas.":::

1. Na caixa de diálogo **nova oferta** , insira uma **ID da oferta** . Essa ID é visível na URL da listagem e Azure Resource Manager modelos do Marketplace comercial, se aplicável. Por exemplo, se você inserir **Test-offer-1** nessa caixa, o endereço Web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
   + Cada oferta em sua conta deve ter uma ID de oferta exclusiva.
   + Use apenas letras minúsculas e números. Ela pode incluir hifens e sublinhados, mas sem espaços, e está limitada a 50 caracteres.
   + A ID da oferta não pode ser alterada depois que você seleciona **criar** .

1. Insira um **Alias da oferta** . Esse é o nome usado para a oferta no Partner Center.

   + Esse nome não está visível no Marketplace comercial e é diferente do nome da oferta e de outros valores mostrados aos clientes.
   + O alias da oferta não pode ser alterado depois que você seleciona **criar** .
1. Para gerar a oferta e continuar, selecione **criar** .

## <a name="configure-your-saas-offer-setup-details"></a>Configurar os detalhes da instalação da oferta de SaaS

Na guia **instalação de oferta** , em **detalhes da instalação** , você escolherá se deseja vender sua oferta pela Microsoft ou gerenciar suas transações de forma independente. As ofertas vendidas pela Microsoft são chamadas de _ofertas de transações_ , o que significa que a Microsoft facilita a troca de dinheiro por uma licença de software em nome do editor. Para obter mais informações sobre essas opções, consulte [listando opções](plan-saas-offer.md#listing-options) e [determinar sua opção de publicação](determine-your-listing-type.md).

1. Para vender pela Microsoft e fazer com que possamos promovam transações para você, selecione **Sim** . Continue a [habilitar uma Test Drive](#enable-a-test-drive-optional).

1. Para listar sua oferta por meio do Marketplace comercial e processar as transações de forma independente, selecione **não** e siga um destes procedimentos:
   + Para fornecer uma assinatura gratuita para sua oferta, selecione **obtê-la agora (gratuito)** . Em seguida, na caixa **URL da oferta** exibida, insira a URL (começando com *http* ou *https* ), em que os clientes podem obter uma avaliação por meio de [autenticação com um clique usando Azure Active Directory (Azure AD)](azure-ad-saas.md). Por exemplo, `https://contoso.com/saas-app`.
   + Para fornecer uma avaliação gratuita de 30 dias, selecione **avaliação gratuita** e, em seguida, na caixa **URL de avaliação** exibida, insira a URL (começando com *http* ou *https* ), em que os clientes podem acessar sua avaliação gratuita por meio de [autenticação com um clique usando Azure Active Directory (Azure AD)](azure-ad-saas.md). Por exemplo, `https://contoso.com/trial/saas-app`.
   + Para que clientes potenciais entrem em contato com você para comprar sua oferta, selecione **entre em contato comigo** .

### <a name="enable-a-test-drive-optional"></a>Habilitar um test drive (opcional)

Uma test drive é uma ótima maneira de demonstrar sua oferta a clientes potenciais, concedendo a eles acesso a um ambiente pré-configurado por um número fixo de horas. A oferta de um test drive resulta em uma taxa de conversão maior e gera clientes potenciais altamente qualificados. Para saber mais sobre as unidades de teste, consulte [o que é um test drive?](./what-is-test-drive.md).

> [!TIP]
> Uma test drive é diferente de uma avaliação gratuita. Você pode oferecer um test drive, uma avaliação gratuita ou ambos. Ambos fornecem aos clientes sua solução por um período de tempo fixo. No entanto, um test drive também inclui um tour interativo prático dos principais recursos do produto e dos benefícios demonstrados em um cenário de implementação do mundo real.

**Para habilitar um test drive**
1.  Em **testar unidade** , marque a caixa de seleção **habilitar um test drive** .
1.  Selecione o tipo de test drive na lista que aparece.

### <a name="configure-lead-management"></a>Configurar o gerenciamento de clientes potenciais

Conecte seu sistema de CRM (gerenciamento de relacionamento com o cliente) com sua oferta de Marketplace comercial para que você possa receber informações de contato do cliente quando um cliente expressar o interesse ou implantar seu produto. Você pode modificar essa conexão a qualquer momento durante ou depois de criar a oferta.

> [!NOTE]
> Você deve configurar o gerenciamento de leads se estiver vendendo sua oferta pela Microsoft ou se tiver selecionado a opção de listagem **entre em contato comigo** . Para obter diretrizes detalhadas, confira [leads do cliente de sua oferta do Marketplace comercial](partner-center-portal/commercial-marketplace-get-customer-leads.md).

#### <a name="to-configure-the-connection-details-in-partner-center"></a>Para configurar os detalhes de conexão no Partner Center

1.  Em **clientes potenciais do cliente** , selecione o link **conectar** .
1. Na caixa de diálogo **detalhes da conexão** , selecione um destino de Lead na lista.
1. Preencha os campos que aparecem. Para obter etapas detalhadas, consulte os seguintes artigos:

   - [Configurar a oferta para enviar clientes potenciais à tabela do Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Configure sua oferta para enviar clientes potenciais ao engajamento do cliente do dynamics 365](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (anteriormente Dynamics CRM Online)
   - [Configurar sua oferta para enviar clientes potenciais ao ponto de extremidade HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Configuração da oferta para enviar clientes potenciais para o Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Configurar oferta para enviar clientes potenciais para o Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Para validar a configuração que você forneceu, selecione o link **validar** .
1. Para fechar a caixa de diálogo, selecione **OK** .

## <a name="next-steps"></a>Próximas etapas

- [Como configurar suas propriedades de oferta de SaaS](create-new-saas-offer-properties.md)