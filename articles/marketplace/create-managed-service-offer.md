---
title: Como criar uma oferta de serviço gerenciado no Marketplace comercial da Microsoft
description: Saiba como criar uma nova oferta de serviço gerenciado para o Azure Marketplace usando o programa comercial do Marketplace no Microsoft Partner Center.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 46a9c9d953a2311d83b5fd18b83727d6765734fa
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918075"
---
# <a name="how-to-create-a-managed-service-offer-for-the-commercial-marketplace"></a>Como criar uma oferta de serviço gerenciado para o Marketplace comercial

Este artigo explica como criar uma oferta de serviço gerenciado para o Marketplace comercial da Microsoft usando o Partner Center.

Para publicar uma oferta de serviço gerenciado, você deve ter conquistado uma competência Gold ou prata da Microsoft na plataforma de nuvem. Se você ainda não tiver feito isso, leia [planejar uma oferta de serviço gerenciado para o Marketplace comercial](./plan-managed-service-offer.md). Ele ajudará você a preparar os ativos necessários ao criar a oferta no Partner Center.

## <a name="create-a-new-offer"></a>Criar uma oferta

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação à esquerda, selecione   >  **visão geral** do Marketplace comercial.
3. Na guia Visão geral, selecione **+ novo**  >  **serviço gerenciado** de oferta.

:::image type="content" source="./media/new-offer-managed-service.png" alt-text="Ilustra o menu de navegação à esquerda.":::

4. Na caixa de diálogo **nova oferta** , insira uma **ID da oferta**. Esse é um identificador exclusivo para cada oferta em sua conta. Essa ID é visível na URL da listagem e Azure Resource Manager modelos do Marketplace comercial, se aplicável. Por exemplo, se você inserir Test-offer-1 nessa caixa, o endereço Web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .

    * Cada oferta em sua conta deve ter uma ID de oferta exclusiva.
    * Use apenas letras minúsculas e números. Ela pode incluir hifens e sublinhados, mas sem espaços, e está limitada a 50 caracteres.
    * A ID da oferta não pode ser alterada depois que você seleciona **Criar**.

5. Insira um **Alias da oferta**. Esse é o nome usado para a oferta no Partner Center. Ele não está visível nas lojas online e é diferente do nome da oferta mostrado aos clientes.
6. Para gerar a oferta e continuar, selecione **criar**.

## <a name="configure-lead-management"></a>Configurar o gerenciamento de clientes potenciais

Conecte seu sistema de CRM (gerenciamento de relacionamento com o cliente) com sua oferta de Marketplace comercial para que você possa receber informações de contato do cliente quando um cliente expressar o interesse em seu serviço de consultoria. Você pode modificar essa conexão a qualquer momento durante ou depois de criar a oferta. Para obter diretrizes detalhadas, confira [leads do cliente de sua oferta do Marketplace comercial](./partner-center-portal/commercial-marketplace-get-customer-leads.md).

Para configurar o gerenciamento de leads no Partner Center:

1. No Partner Center, vá para a guia **instalação da oferta** .
2. Em **clientes potenciais do cliente**, selecione o link **conectar** .
3. Na caixa de diálogo **detalhes da conexão** , selecione um destino de Lead na lista.
4. Preencha os campos que aparecem. Para obter etapas detalhadas, consulte os seguintes artigos:

    * [Configurar a oferta para enviar clientes potenciais à tabela do Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [Configure sua oferta para enviar clientes potenciais ao engajamento do cliente do dynamics 365](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (anteriormente Dynamics CRM Online)
    * [Configurar sua oferta para enviar clientes potenciais ao ponto de extremidade HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [Configuração da oferta para enviar clientes potenciais para o Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [Configurar oferta para enviar clientes potenciais para o Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5. Para validar a configuração que você forneceu, selecione o **link validar**.
6. Quando você tiver configurado os detalhes da conexão, selecione **conectar**.
7. Selecione **Salvar rascunho**.

Depois de enviar sua oferta para publicação no Partner Center, validaremos a conexão e enviaremos a você um líder de teste. Ao visualizar a oferta antes que ela fique ativa, teste sua conexão de cliente potencial tentando comprar a oferta por conta própria no ambiente de visualização.

> [!TIP]
> A conexão com o destino do cliente potencial deve permanecer atualizada para que você não perca nenhum cliente potencial.

## <a name="configure-offer-properties"></a>Configurar propriedades da oferta

Na página Propriedades de sua oferta no Partner Center, você definirá as categorias aplicáveis à sua oferta e aos contratos legais. Essas informações garantem que o serviço gerenciado seja exibido corretamente na loja online e oferecido ao conjunto certo de clientes.

### <a name="select-a-category"></a>Selecionar uma categoria

Em **categorias**, selecione pelo menos uma e até cinco categorias para agrupar sua oferta nas áreas apropriadas de pesquisa do Marketplace comercial.

### <a name="provide-terms-and-conditions"></a>Fornecer termos e condições

Em **legal**, forneça seus termos e condições para esta oferta. Os clientes serão solicitados a aceitá-los antes de usar a oferta. Você também pode fornecer a URL na qual os termos e condições podem ser encontrados.

Selecione **Salvar rascunho** antes de continuar.

## <a name="next-step"></a>Próxima etapa

* [Configurar a listagem da oferta de serviço gerenciado](./create-managed-service-offer-listing.md)