---
title: Introdução às opções de listagem – Microsoft Commercial Marketplace
description: Este artigo descreve as opções de listagem para ofertas publicadas no Microsoft AppSource e no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 01/14/2021
ms.openlocfilehash: 2433e62fd1dbf35748483b40958fc3367532102a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100099227"
---
# <a name="introduction-to-listing-options"></a>Introdução às opções de listagem

Ao criar um tipo de oferta, você escolhe uma ou mais opções de listagem. Essas opções determinam os botões que os clientes veem na página de listagem de oferta nas lojas online. As opções de listagem incluem _avaliação gratuita_, _Test Drive_, _entre em contato comigo_ e _obtê-lo agora_.

Esta tabela mostra quais opções de listagem estão disponíveis para cada tipo de oferta.

| Tipo de oferta | Avaliação gratuita | Test drive | Entre em contato comigo | Obtenha agora `*` |
| ------------ | ------------- | ------------- | ------------- | ------------- |
| Aplicativo Azure (aplicativo gerenciado) |   | &#10004; |   | &#10004; |
| Aplicativo Azure (modelo de solução) |  |  |  | &#10004; |
| Serviço de consultoria |  |  | &#10004; |  |
| Contêiner do Azure |  |  |  | &#10004; |
| Dynamics 365 Business central | &#10004; | &#10004; | &#10004; | &#10004; |
| Compromisso com o cliente do Dynamics 365 & PowerApps | &#10004; | &#10004; | &#10004; | &#10004; |
| Dynamics 365 para operações | &#10004; | &#10004; | &#10004; | &#10004; |
| Módulo do IoT Edge |  |  |  | &#10004; |
| Serviço gerenciado |  |  |  | &#10004; |
| Aplicativo do Power BI |  |  |  | &#10004; |
| Máquina Virtual do Azure | &#10004; | &#10004; |  | &#10004; |
| Software como serviço | &#10004; | &#10004; | &#10004; | &#10004; |
||||||

&#42; a opção obter agora de listagem inclui obtê-lo agora (gratuito), traga sua própria licença (BYOL), assinatura e preços com base no uso. Para obter detalhes, consulte [obter agora](#get-it-now).

## <a name="changing-offer-type"></a>Alterando tipo de oferta

[!INCLUDE [change-offer-type](./includes/change-offer-type.md)]

## <a name="free-trial"></a>Avaliação gratuita

Use o Marketplace comercial para aprimorar a capacidade de descoberta e automatizar o provisionamento da experiência de avaliação da solução. Isso permite que os clientes potenciais usem seu SaaS (software como serviço), IaaS ou experiência no aplicativo da Microsoft sem custo de 30 dias a seis meses, dependendo do tipo de oferta.

Os clientes usam o botão _avaliação gratuita_ na página de listagem da sua oferta para experimentar sua oferta. Se você fornecer uma avaliação gratuita em vários planos na mesma oferta, os clientes poderão alternar para uma avaliação gratuita em outro plano, mas o período de avaliação não será reiniciado.

Para ofertas de máquina virtual, os clientes são cobrados pelos custos de infraestrutura do Azure para usar a oferta durante um período de avaliação. Após a expiração do período de avaliação, os clientes são cobrados automaticamente pelo último plano que tentaram com base nas taxas padrão, a menos que sejam cancelados antes do final do período de avaliação.

## <a name="test-drive"></a>Test drive

Os clientes usam o botão _Test Drive_ na página de listagem da sua oferta para obter acesso a um ambiente pré-configurado por um número fixo de horas. Para saber mais sobre as unidades de teste, consulte [o que é um test drive?](what-is-test-drive.md)

> [!TIP]
> Uma test drive é diferente de uma avaliação gratuita. Você pode oferecer um test drive, uma avaliação gratuita ou ambos. Ambos fornecem aos seus clientes sua solução por um período de tempo fixo. Mas um test drive também inclui um passeio prático e autoguiado dos principais recursos do produto e os benefícios que são demonstrados em um cenário de implementação real.

## <a name="contact-me"></a>Entre em contato comigo

Listagem simples de seu aplicativo ou serviço. Os clientes usam o botão _entrar em contato comigo_ na página de listagem da sua oferta para solicitar que você se conecte com eles sobre sua oferta.

## <a name="get-it-now"></a>Obtenha agora

Essa opção de listagem inclui ofertas de transações (assinaturas e preços baseados em usuários), traga suas próprias ofertas de licença e obtenha-as agora (gratuito). As ofertas de transações são vendidas por meio do Marketplace comercial. A Microsoft é responsável pelas cobranças. Os clientes usam o _botão obter agora_ para obter a oferta.

A opção obter agora a lista pode incluir as seguintes opções de preço, dependendo do tipo de oferta:

- Obtenha agora (gratuito)
- BYOL (Traga sua própria licença)
- Subscription
- Preço com base no uso

Esta tabela mostra quais tipos de oferta dão suporte às opções de preços adicionais que estão incluídas com a opção obter agora a lista.

| Tipo de oferta | Obtenha agora (gratuito) | BYOL | Subscription | Preço com base no uso |
| ------------ | ------------- | ------------- | ------------- | ------------- |
| Aplicativo Azure (aplicativo gerenciado) |   |   | &#10004; | &#10004; |
| Aplicativo Azure (modelo de solução) | &#10004; |   |   |   |
| Serviço de consultoria |   |   |   |   |
| Contêiner do Azure | &#10004;<sup>1</sup> | &#10004;<sup>1</sup> |   |   |
| Dynamics 365 Business central | &#10004; |   |   |   |
| Compromisso com o cliente do Dynamics 365 & PowerApps | &#10004; |   |   |   |
| Dynamics 365 para operações | &#10004; |   |   |   |
| Módulo do IoT Edge | &#10004;<sup>1</sup> | &#10004;<sup>1</sup> |   |   |
| Serviço gerenciado |   | &#10004; |   |   |
| Aplicativo do Power BI | &#10004; |   |   |   |
| Máquina Virtual do Azure |   | &#10004; |   | &#10004;<sup>2</sup> |
| Software como serviço | &#10004; |   | &#10004; | &#10004; |
||||||

**Legenda**

<sup>1</sup> a coluna **modelo de preços** da guia **visão geral do plano** mostra _Free_ ou _BYOL_ , mas não é selecionável.

<sup>2</sup> com preço por hora e cobrado mensalmente.

### <a name="get-it-now-free"></a>Obtenha agora (gratuito)

Use esta opção de listagem para oferecer seu aplicativo gratuitamente. Os clientes usam o botão _obter agora_ para obter sua oferta gratuita.

> [!NOTE]
> Obtenha agora (gratuito) ofertas não qualificadas para benefícios de recompensas do Marketplace para ofertas de transações. Como não há nenhuma transação por meio da vitrine, elas são categorizadas como "avaliação". Consulte as [recompensas do Marketplace](#marketplace-rewards) abaixo.

### <a name="bring-your-own-license-byol"></a>BYOL (Traga sua própria licença)

Use esta opção de listagem para permitir que os clientes implantem sua oferta usando uma licença comprada fora do Marketplace comercial. Essa opção é ideal para migrações locais para a nuvem. Os clientes usam o botão _obter agora_ para comprar sua oferta usando uma licença adquirida previamente de você.

> [!NOTE]
> As ofertas do BYOL não são elegíveis para benefícios de recompensas do Marketplace para ofertas de transações. Como eles exigem que um cliente adquira a licença do parceiro e não há transações por meio da vitrine do Marketplace comercial, elas são categorizadas como "lista". Consulte as [recompensas do Marketplace](#marketplace-rewards) abaixo.

### <a name="subscription"></a>Subscription

Você pode cobrar uma tarifa fixa por esses tipos de oferta:

- Aplicativo Azure (aplicativo gerenciado) oferece suporte a assinaturas mensais.
- O SaaS oferece suporte a assinaturas mensais e anuais.

### <a name="usage-based-pricing"></a>Preço com base no uso

Os seguintes tipos de oferta dão suporte a preços com base no uso:

- O Aplicativo Azure (aplicativo gerenciado) oferece suporte à cobrança limitada. Para obter mais detalhes, consulte [cobrança limitada do aplicativo gerenciado](partner-center-portal/azure-app-metered-billing.md).
- As ofertas de SaaS oferecem suporte à cobrança limitada e a preços por usuário (por estação). Para obter mais informações sobre cobrança limitada, consulte [cobrança limitada para SaaS usando o serviço de medição do Marketplace comercial](partner-center-portal/saas-metered-billing.md).
- A máquina virtual do Azure oferece suporte por núcleo, por tamanho de núcleo e por preço de mercado e de tamanho de núcleo. Essas opções de preços têm preços por hora e são cobradas mensalmente.

Ao criar uma oferta de transação, é importante entender as considerações sobre preços, cobrança, faturamento e pagamento antes de selecionar um tipo de oferta e criar sua oferta. Para saber mais, confira [lojas online do Marketplace comercial](overview.md#commercial-marketplace-online-stores).

## <a name="sample-offer"></a>Oferta de exemplo

Depois que sua oferta for publicada, as opções de listagem escolhidas aparecerão como um botão no canto superior esquerdo da página de listagem nas lojas online. Por exemplo, a tela a seguir mostra uma página de listagem de oferta na loja do Microsoft AppSource online com os botões **obter agora** e **testar unidade** :

:::image type="content" source="media/listing-options.png" alt-text="Ilustra a página de listagem de uma oferta com os botões contate-me e Test Drive.":::

## <a name="listing-and-pricing-options-by-online-store"></a>Lista e opções de preços por loja online

Com base em uma variedade de critérios, determinamos se sua oferta está listada no Azure Marketplace, Microsoft AppSource ou em ambas as lojas online. Para obter mais informações sobre as diferenças entre as duas lojas online, consulte [lojas online do Marketplace comercial](overview.md#commercial-marketplace-online-stores).

A tabela a seguir mostra as opções que estão disponíveis para diferentes tipos de oferta e suplementos e que armazena online sua oferta pode ser listada em.

| Tipos e suplementos de oferta | Entre em contato comigo | Avaliação gratuita | Obtenha agora (gratuito) | BYOL | Obtenha agora (Transact) |
| ------------ | ------------- | ------------- | ------------- | ------------- | ------------- |
| Máquina Virtual do Azure |   |   |   | Azure Marketplace | Azure Marketplace |
| Aplicativo do Azure |   |   | Azure Marketplace | Azure Marketplace | Azure Marketplace |
| Contêiner do Azure  |   |   | Azure Marketplace | Azure Marketplace |   |
| Módulo do IoT Edge |   |   | Azure Marketplace | Azure Marketplace |   |
| Serviço gerenciado |   |   |   | Azure Marketplace |   |
| Serviço de consultoria | Ambas as lojas online |   |   |   |   |
| SaaS  | Ambas as lojas online | Ambas as lojas online | Ambas as lojas online |   | Ambas as lojas online &#42; |
| Aplicativo do Microsoft 365 | AppSource | AppSource |   |   |  &#42;&#42; AppSource |
| Dynamics 365 Business central | AppSource | AppSource |   |   |   |
| Dynamics 365 para compromissos do cliente & PowerApps | AppSource | AppSource |   |   |   |
| Dynamics 365 para operações | AppSource | AppSource |   |   |   |
| Aplicativo do Power BI |   |   | AppSource |   |   |
|||||||

&#42; ofertas de SaaS que podem ser acessadas no AppSource são apenas o cartão de crédito.

&#42;&#42; os suplementos de Microsoft 365 são livres para instalação e podem ser monetizadas usando uma oferta de SaaS. Para obter mais informações, consulte [Monetizar seu suplemento do Office 365 por meio do marketplace comercial da Microsoft](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace).

## <a name="marketplace-rewards"></a>O Marketplace Rewards

As recompensas do Marketplace são diferenciadas com base na opção de listagem que você escolher. Para saber mais, confira [seus benefícios do Marketplace comercial](gtm-your-marketplace-benefits.md).

Se sua oferta for transformada, você ganhará benefícios ao aumentar suas vendas cobradas.

As ofertas não transacionais ganham benefícios com base em se uma avaliação gratuita está ou não conectada.

## <a name="next-steps"></a>Próximas etapas

- Para escolher um tipo de oferta a ser criado, consulte [Guia de publicação por tipo de oferta](publisher-guide-by-offer-type.md).
