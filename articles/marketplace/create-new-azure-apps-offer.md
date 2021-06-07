---
title: Como criar uma oferta de aplicativo do Azure no Marketplace comercial
description: Saiba como criar uma nova oferta de aplicativo do Azure para listagem ou venda no Azure Marketplace ou por meio do programa CSP (provedor de soluções na nuvem) usando o portal do Marketplace comercial no Microsoft Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 3cc5e5114b435965eee4aa096e5898538b0a56e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94369931"
---
# <a name="how-to-create-an-azure-application-offer-in-the-commercial-marketplace"></a>Como criar uma oferta de aplicativo do Azure no Marketplace comercial

Como um editor de mercado comercial, você pode criar uma oferta de aplicativo do Azure para que clientes potenciais possam comprar sua solução. Este artigo explica o processo para criar uma oferta de aplicativo do Azure para o Marketplace comercial da Microsoft.

Se você ainda não fez isso, leia [planejar uma oferta de aplicativo do Azure para o Marketplace comercial](plan-azure-application-offer.md). Ele fornecerá os recursos e ajudará você a coletar as informações e os ativos necessários ao criar sua oferta.

## <a name="create-a-new-offer"></a>Criar uma oferta

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).

1. No menu de navegação à esquerda, selecione   >  **visão geral** do Marketplace comercial.

1. Na página Visão geral, selecione **+ Nova oferta** > **Aplicativo do Azure**.

    ![Ilustra o menu de navegação à esquerda.](./media/create-new-azure-app-offer/new-offer-azure-app.png)

1. Na caixa de diálogo **nova oferta** , insira uma **ID da oferta**. Esse é um identificador exclusivo para cada oferta em sua conta. Essa ID é visível na URL da listagem e Azure Resource Manager modelos do Marketplace comercial, se aplicável. Por exemplo, se você inserir Test-offer-1 nessa caixa, o endereço Web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .

     * Cada oferta em sua conta deve ter uma ID de oferta exclusiva.
     * Use apenas letras minúsculas e números. Ela pode incluir hifens e sublinhados, mas sem espaços, e está limitada a 50 caracteres.
     * A ID da oferta não pode ser alterada depois que você seleciona **Criar**.

1. Insira um **Alias da oferta**. Esse é o nome usado para a oferta no Partner Center.

     * Esse nome só é visível no Partner Center e é diferente do nome da oferta e de outros valores mostrados aos clientes.
     * O Alias da oferta não poderá ser alterado depois que você selecionar a opção **Criar**.

1. Para gerar a oferta e continuar, selecione  **criar**.

## <a name="configure-your-azure-application-offer-setup-details"></a>Configurar os detalhes da instalação da oferta de aplicativo do Azure

Na guia **instalação de oferta** , em **detalhes da instalação**, você escolherá se deseja configurar um test drive. Você também conectará seu sistema CRM (gerenciamento de relacionamento com o cliente) com sua oferta de Marketplace comercial.

### <a name="enable-a-test-drive-optional"></a>Habilitar um test drive (opcional)

Uma test drive é uma ótima maneira de demonstrar sua oferta a clientes potenciais, concedendo a eles acesso a um ambiente pré-configurado por um número fixo de horas. A oferta de um test drive resulta em uma taxa de conversão maior e gera clientes potenciais altamente qualificados. Para saber mais sobre as unidades de teste, consulte [Test Drive](plan-azure-application-offer.md#test-drive).

#### <a name="to-enable-a-test-drive"></a>Para habilitar um test drive

- Em **testar unidade**, marque a caixa de seleção **habilitar um test drive** .

### <a name="customer-lead-management"></a>Gerenciamento de leads do cliente

Conecte seu sistema de CRM (gerenciamento de relacionamento com o cliente) com sua oferta de Marketplace comercial para que você possa receber informações de contato do cliente quando um cliente expressar o interesse ou implantar seu produto.

#### <a name="to-configure-the-connection-details-in-partner-center"></a>Para configurar os detalhes de conexão no Partner Center

1. Em **clientes potenciais do cliente**, selecione o link **conectar** .
1. Na caixa de diálogo **detalhes da conexão** , selecione um destino de Lead na lista.
1. Preencha os campos que aparecem. Para obter etapas detalhadas, consulte os seguintes artigos:

   - [Configurar a oferta para enviar clientes potenciais à tabela do Azure](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Configure sua oferta para enviar clientes potenciais ao engajamento do cliente do dynamics 365](partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (anteriormente Dynamics CRM Online)
   - [Configurar sua oferta para enviar clientes potenciais ao ponto de extremidade HTTPS](partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Configuração da oferta para enviar clientes potenciais para o Marketo](partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Configurar oferta para enviar clientes potenciais para o Salesforce](partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Para validar a configuração que você forneceu, selecione o link **validar** , se aplicável.
1. Para fechar a caixa de diálogo, selecione **conectar**.
1. Selecione **salvar rascunho** antes de continuar para a próxima guia: Propriedades.

> [!NOTE]
> Certifique-se de que a conexão com o destino do cliente potencial permaneça atualizada para que você não perca clientes potenciais. Certifique-se de atualizar essas conexões sempre que algo tiver mudado.

## <a name="next-steps"></a>Próximas etapas

- [Como configurar suas propriedades de oferta de Aplicativo Azure](create-new-azure-apps-offer-properties.md)
