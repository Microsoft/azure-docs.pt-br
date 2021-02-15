---
title: Como criar uma oferta de serviço de consultoria no Marketplace comercial da Microsoft
description: Saiba como criar uma nova oferta de serviço de consultoria para o Microsoft AppSource ou o Azure Marketplace usando o programa comercial do Marketplace no Microsoft Partner Center.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: e9a0b2fe883fa46010fda74c58908128d05919e6
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754312"
---
# <a name="how-to-create-a-consulting-service-offer-in-the-commercial-marketplace"></a>Como criar uma oferta de serviço de consultoria no Marketplace comercial

Este artigo explica como criar uma oferta de serviço de consultoria para o Marketplace comercial da Microsoft usando o Partner Center. 

## <a name="before-you-begin"></a>Antes de começar

Para publicar uma oferta de serviço de consultoria, você deve atender a determinados requisitos de qualificação para demonstrar a experiência em seu campo. Se você ainda não fez isso, leia [planejar uma oferta de serviço de consultoria para o Marketplace comercial](./plan-consulting-service-offer.md). Ele descreve os pré-requisitos para um serviço de consultoria e os ativos que você precisará ao criar a oferta com o Partner Center.

## <a name="create-a-new-consulting-service-offer"></a>Criar uma nova oferta de serviços de consultoria

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).
2.  No menu de navegação à esquerda, selecione **Commercial Marketplace**  >  **visão geral** do Marketplace comercial.
3.  Na guia Visão geral, selecione **+ novo**  >  **serviço de consultoria** de oferta.

    ![Ilustra o menu de navegação à esquerda.](./media/new-offer-consulting-service.png)

4. Na caixa de diálogo **nova oferta** , insira uma **ID da oferta** . Essa ID é visível na URL da listagem do Marketplace comercial. Por exemplo, se você inserir Test-offer-1 nessa caixa, o endereço Web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .

    * Cada oferta em sua conta deve ter uma ID de oferta exclusiva.
    * Use apenas letras minúsculas e números. A ID da oferta pode incluir hifens e sublinhados, mas sem espaços, e está limitado a 50 caracteres.
    * A ID da oferta não pode ser alterada depois que você seleciona **criar** .

5. Insira um **Alias da oferta** . Esse é o nome usado para a oferta no Partner Center. Ele não está visível nas lojas online e é diferente do nome da oferta mostrado aos clientes.
6. Para gerar a oferta e continuar, selecione **criar** .

## <a name="configure-lead-management"></a>Configurar o gerenciamento de clientes potenciais

Conecte seu sistema de CRM (gerenciamento de relacionamento com o cliente) com sua oferta de Marketplace comercial para que você possa receber informações de contato do cliente quando um cliente expressar o interesse em seu serviço de consultoria. Você pode modificar essa conexão a qualquer momento durante ou depois de criar a oferta. Para obter diretrizes detalhadas, confira [leads do cliente de sua oferta do Marketplace comercial](./partner-center-portal/commercial-marketplace-get-customer-leads.md).

Para configurar o gerenciamento de leads no Partner Center:

1.  No Partner Center, vá para a guia **instalação da oferta** .
2.  Em **clientes potenciais do cliente** , selecione o link **conectar** .
3.  Na caixa de diálogo **detalhes da conexão** , selecione um destino de Lead na lista.
4.  Preencha os campos que aparecem. Para obter etapas detalhadas, consulte os seguintes artigos:

    * [Configurar a oferta para enviar clientes potenciais à tabela do Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [Configure sua oferta para enviar clientes potenciais ao engajamento do cliente do dynamics 365](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (anteriormente Dynamics CRM Online)
    * [Configurar sua oferta para enviar clientes potenciais ao ponto de extremidade HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [Configuração da oferta para enviar clientes potenciais para o Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [Configurar oferta para enviar clientes potenciais para o Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5.  Para validar a configuração que você forneceu, selecione o **link validar** .
6.  Quando você tiver configurado os detalhes da conexão, selecione **conectar** .
7.  Selecione **Salvar rascunho** .

Depois de enviar sua oferta para publicação no Partner Center, validaremos a conexão e enviaremos a você um líder de teste. Ao visualizar a oferta antes que ela fique ativa, teste sua conexão de cliente potencial tentando comprar a oferta por conta própria no ambiente de visualização.

> [!TIP]
> A conexão com o destino do cliente potencial deve permanecer atualizada para que você não perca nenhum cliente potencial.

## <a name="next-steps"></a>Próximas etapas

* [Como configurar suas propriedades de oferta de serviço de consultoria](./create-consulting-service-offer-properties.md)