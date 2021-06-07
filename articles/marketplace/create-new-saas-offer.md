---
title: Como criar uma oferta de SaaS no marketplace comercial da Microsoft
description: Saiba como criar uma oferta de SaaS (software como serviço) para listagem ou venda no Microsoft AppSource, Azure Marketplace ou por meio do programa CSP (provedor de soluções na nuvem) usando o portal do marketplace comercial no Microsoft Partner Center.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: 74d30b7c42002c8f134520e0198774eba1519bcd
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553831"
---
# <a name="how-to-create-a-saas-offer-in-the-commercial-marketplace"></a>Como criar uma oferta de SaaS no marketplace comercial

Como editor do marketplace comercial, você pode criar uma oferta de SaaS (software como serviço) para que clientes potenciais possam comprar sua solução técnica baseada em SaaS. Este artigo explica o processo para criar uma oferta de SaaS para o marketplace comercial da Microsoft.

## <a name="before-you-begin"></a>Antes de começar

Caso ainda não tenha feito isso, leia [Planejar uma oferta de SaaS para o marketplace comercial](plan-saas-offer.md). Ele explicará os requisitos técnicos para seu aplicativo SaaS e as informações e os ativos de que você precisará ao criar sua oferta. A menos que você planeje publicar uma listagem simples (opção de listagem de **Entrar em contato comigo**) no Marketplace comercial, seu aplicativo SaaS precisa atender aos requisitos técnicos de autenticação.

> [!IMPORTANT]
> Recomendamos que você crie uma oferta de desenvolvimento/teste (DEV) separada e uma oferta de produção (PROD) separada. Este artigo descreve como criar uma oferta PROD. Para obter detalhes sobre como criar uma oferta DEV, confira [Criar uma oferta de desenvolvimento e teste](create-saas-dev-test-offer.md).

## <a name="create-a-new-saas-offer"></a>Criar uma oferta de SaaS

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).
1. No menu de navegação esquerdo, selecione **Marketplace Comercial** > **Visão geral**.
1. Na guia **Visão geral**, selecione **+ Nova oferta** > **Software como Serviço**.

   :::image type="content" source="media/new-offer-saas.png" alt-text="Ilustra o menu de navegação esquerdo e a lista Nova oferta.":::

1. Na caixa de diálogo **Nova oferta**, insira uma **ID da oferta**. Essa ID é visível na URL da listagem do marketplace comercial e nos modelos do Azure Resource Manager, se aplicável. Por exemplo, se você inserir **test-offer-1** nessa caixa, o endereço Web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
   + Cada oferta em sua conta precisa ter uma ID de oferta exclusiva.
   + Use apenas letras minúsculas e números. Ela pode incluir hifens e sublinhados, mas sem espaços, e está limitada a 50 caracteres.
   + A ID da oferta não poderá ser alterada depois que você selecionar a opção **Criar**.

1. Insira um **Alias da oferta**. Esse é o nome usado para a oferta no Partner Center.

   + Esse nome não é visível no marketplace comercial e é diferente do nome da oferta e de outros valores mostrados aos clientes.
   + O alias da oferta não poderá ser alterado depois que você selecionar a opção **Criar**.
1. Para gerar a oferta e continuar, selecione **Criar**.

## <a name="configure-your-saas-offer-setup-details"></a>Configurar os detalhes da instalação da oferta de SaaS

Na guia **Instalação da oferta**, em **Detalhes da instalação**, escolha se deseja vender sua oferta pela Microsoft ou gerenciar suas transações de modo independente. As ofertas vendidas pela Microsoft são chamadas de _ofertas transacionáveis_, o que significa que a Microsoft facilita a troca de dinheiro por uma licença de software em nome do editor. Para obter mais informações sobre essas opções, confira [Opções de listagem](plan-saas-offer.md#listing-options) e [Determinar sua opção de publicação](determine-your-listing-type.md).

1. Para vender pela Microsoft e para que nós possamos facilitar as transações para você, selecione **Sim**. Continue para [Habilitar um test drive](#enable-a-test-drive-optional).

1. Para listar sua oferta por meio do marketplace comercial e processar as transações de modo independente, selecione **Não** e siga um destes procedimentos:
   + Para fornecer uma assinatura gratuita para sua oferta, selecione **Obter agora (gratuito)** . Em seguida, na caixa **URL da oferta** exibida, insira a URL (começando com *http* ou *https*), em que os clientes podem obter uma avaliação por meio da [autenticação com um clique usando o Azure AD (Azure Active Directory)](azure-ad-saas.md). Por exemplo, `https://contoso.com/saas-app`.
   + Para fornecer uma avaliação gratuita de 30 dias, selecione **Avaliação gratuita** e, na caixa **URL de avaliação** exibida, insira a URL (começando com *http* ou *https*), em que os clientes podem acessar sua avaliação gratuita por meio da [autenticação com um clique usando o Azure AD (Azure Active Directory)](azure-ad-saas.md). Por exemplo, `https://contoso.com/trial/saas-app`.
   + Para que clientes potenciais entrem em contato com você a fim de comprar sua oferta, selecione **Entrar em contato comigo**.

## <a name="enable-a-test-drive-optional"></a>Habilitar um test drive (opcional)

Uma test drive é uma ótima forma de demonstrar sua oferta a clientes potenciais, dando a eles acesso a um ambiente pré-configurado por um número fixo de horas. A oferta de um test drive resulta em uma taxa de conversão maior e gera clientes potenciais altamente qualificados. Para saber mais sobre os test drives, confira [O que é um test drive?](./what-is-test-drive.md).

> [!TIP]
> Uma test drive é diferente de uma avaliação gratuita. Você pode oferecer um test drive, uma avaliação gratuita ou os dois. As duas opções fornecem aos clientes sua solução por um período fixo. Porém, um test drive também inclui um tour interativo e autoguiado sobre os principais recursos e benefícios do seu produto, demonstrados em um cenário de implementação real.

**Para habilitar um test drive**
1.  Em **Test drive**, marque a caixa de seleção **Habilitar um test drive**.
1.  Selecione o tipo de test drive na lista exibida.

## <a name="configure-lead-management"></a>Configurar o gerenciamento de clientes potenciais

Conecte seu sistema de CRM (gerenciamento de relacionamento com o cliente) com sua oferta de marketplace comercial para que você possa receber informações de contato do cliente quando ele expressar interesse ou implantar seu produto. Você pode modificar essa conexão a qualquer momento durante ou depois de criar a oferta.

> [!NOTE]
> Você precisará configurar o gerenciamento de clientes potenciais se estiver vendendo sua oferta pela Microsoft ou se tiver selecionado a opção de listagem **Entrar em contato comigo**. Para obter diretrizes detalhadas, confira [Vendas potenciais do cliente da sua oferta do marketplace comercial](partner-center-portal/commercial-marketplace-get-customer-leads.md).

### <a name="configure-the-connection-details-in-partner-center"></a>Configurar os detalhes de conexão no Partner Center

1.  Em **Vendas potenciais do cliente**, selecione o link **Conectar**.
1. Na caixa de diálogo **Detalhes da conexão**, selecione um destino do cliente potencial na lista.
1. Preencha os campos exibidos. Para obter as etapas, confira os seguintes artigos:

   - [Configurar a oferta para enviar clientes potenciais à tabela do Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Configurar sua oferta para enviar clientes potenciais para o Dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (antigo Dynamics CRM Online)
   - [Configurar a oferta para enviar clientes potenciais ao ponto de extremidade HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Configuração da oferta para enviar clientes potenciais para o Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Configurar oferta para enviar clientes potenciais para o Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Para validar a configuração que você forneceu, selecione o link **Validar**.
1. Para fechar a caixa de diálogo, selecione **OK**.

## <a name="configure-microsoft-365-app-integration"></a>Configurar a integração de aplicativos do Microsoft 365

Você pode iluminar a [descoberta unificada e a entrega](./plan-SaaS-offer.md) da sua oferta de SaaS e qualquer consumo de aplicativo do Microsoft 365 relacionado vinculando as duas.

### <a name="integrate-with-microsoft-api"></a>Fazer a integração com a API da Microsoft

1. Se sua oferta de SaaS não se integrar à API do Microsoft Graph, selecione **Não**. Continue para Vincular clientes de consumo de aplicativos publicados do Microsoft 365.  

1. Se sua oferta de SaaS se integrar à API do Microsoft Graph, selecione **Sim** e forneça a ID do aplicativo do Azure Active Directory que você criou e registrou para integrar à API do Microsoft Graph. 

### <a name="link-published-microsoft-365-app-consumption-clients"></a>Vincular clientes de consumo de aplicativos publicados do Microsoft 365

1. Se você não tiver publicado o suplemento do Office, o aplicativo Teams ou as soluções do SharePoint Framework que funcionam com sua oferta de SaaS, selecione **Não**.

1. Se você publicou o suplemento do Office, o aplicativo Teams ou as soluções do SharePoint Framework que funcionam com sua oferta de SaaS, selecione **Sim** e escolha **+Adicionar outro link do AppSource** para adicionar novos links.  

1. Forneça um link válido do AppSource.

1. Continue adicionando todos os links selecionando **+Adicionar outro link do AppSource** e forneça links válidos do AppSource.  

1. A ordem em que os produtos vinculados são mostrados na página de listagem da oferta de SaaS é indicada pelo valor de classificação. Para alterá-la, selecione, segure e mova o ícone = para cima e para baixo na lista. 

1. Você pode excluir um produto vinculado selecionando **Excluir** na linha de produtos.  


> [!IMPORTANT]
> Se você parar de vender um produto vinculado, ele não será desvinculado automaticamente na oferta de SaaS. Você precisa excluí-lo da lista de produtos vinculados e reenviar a oferta de SaaS.  

 

## <a name="next-steps"></a>Próximas etapas

- [Como configurar as propriedades da sua oferta de SaaS](create-new-saas-offer-properties.md)