---
title: Exibir e baixar o preço do Azure de sua organização
description: Saiba como exibir e baixar o preço ou estimar os custos com o preço de sua organização.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 01/07/2021
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: d563907d3567607e537eebfc5c91be02e27fd758
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98014750"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>Exibir e baixar o preço do Azure de sua organização

Os clientes do Azure com um EA (Contrato Enterprise) do Azure, um MCA (Contrato de Cliente da Microsoft) ou um MPA (Contrato de Parceiro da Microsoft) podem exibir e baixar os preços no portal do Azure. [Saiba como verificar o tipo de sua conta de cobrança](#check-your-billing-account-type).

## <a name="download-pricing-for-an-enterprise-agreement"></a>Baixar preços de um Contrato Enterprise

Dependendo das políticas definidas para sua organização pelo Enterprise Admin, somente determinadas funções administrativas fornecem acesso às informações de preços do EA da organização. Para obter mais informações, consulte [Entendendo as funções administrativas do Azure Enterprise Agreement no Azure](understand-ea-roles.md).

1. Como admin corporativo, entre no [portal do Azure](https://portal.azure.com/).
1. Pesquise *Gerenciamento de Custos + Cobrança*.  
   ![Captura de tela que mostra a pesquisa do portal do Azure.](./media/ea-pricing/portal-cm-billing-search.png)
1. Na conta de faturamento, selecione **Usage + charges**.  
   ![Captura de tela que mostra o uso e as cobranças em Faturamento](./media/ea-pricing/ea-pricing-usage-charges-nav.png)
1. Selecione o ![ícone de Download.](./media/ea-pricing/download-icon.png) **Baixar** para o mês.
1. Sob **tabela de preços**, selecione **fazer o download do csv**.  
    :::image type="content" source="./media/ea-pricing/download-enterprise-agreement-price-sheet-01.png" alt-text="A captura de tela mostra as opções Baixar Uso + Encargos." :::

## <a name="download-pricing-for-an-mca-or-mpa-account"></a>Baixar preços de uma conta do MCA ou do MPA

Caso tenha um MCA, você precisará ser o proprietário, o colaborador, o leitor ou o gerenciador de faturas do perfil de cobrança para exibir e baixar os preços. Caso tenha um MPA, você precisará ter as funções Administrador Global e Agente Administrativo na organização parceira para exibir e baixar os preços.

### <a name="download-price-sheets-for-billed-charges"></a>Baixar planilhas de preços para encargos faturados

1. Entre no [portal do Azure](https://portal.azure.com).
1. Pesquise *Gerenciamento de Custos + Cobrança*.
1. Selecione um perfil de cobrança. Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança primeiro.
1. Selecione **Faturas**.
1. Na grade da fatura, encontre a linha da fatura correspondente à planilha de preços que deseja baixar.
1. Clique nas reticências (`...`) no final da linha.  
    ![Captura de tela que mostra as reticências selecionadas](./media/ea-pricing/billingprofile-invoicegrid-new.png)
1. Caso deseje ver os preços dos serviços na fatura selecionada, selecione **Planilha de preços da fatura**.
1. Caso deseje ver os preços de todos os serviços do Azure para o período de cobrança especificado, selecione **Planilha de preços do Azure**.  
    ![Captura de tela que mostra o menu de contexto com planilhas de preços](./media/ea-pricing/contextmenu-pricesheet01.png)

### <a name="download-price-sheets-for-the-current-billing-period"></a>Baixar as planilhas de preços para o período de cobrança atual

Caso tenha um MCA, você poderá baixar os preços para o período de cobrança atual.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Pesquise *Gerenciamento de Custos + Cobrança*.
1. Selecione um perfil de cobrança. Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança primeiro.
1. Na área **Visão Geral**, encontre os links de download abaixo dos encargos do mês atual.
1. Selecione a **planilha de preços do Azure**.  
    ![Captura de tela que mostra o download em Visão Geral](./media/ea-pricing/open-pricing01.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>Estimar os custos com a Calculadora de Preços do Azure

Você também pode usar o preço de sua organização para estimar os custos usando a Calculadora de Preços do Azure.

1. Vá para a [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator).
1. No canto superior direito, selecione **entrar**.
1. Em **Programas e Oferta** > **Programa de Licenciamento**, selecione **Enterprise Agreement (EA)** .
1. Em **Programas e Oferta** > **Contrato selecionado**, selecione **Nenhum selecionado**.  
    ![A captura de tela mostra os Programas e as Ofertas disponíveis.](./media/ea-pricing/ea-pricing-calculator-estimate.png)
1. Escolha a organização.
1. Escolha **Aplicar**.
1. Pesquise e, em seguida, adicione produtos à estimativa.
1. Os preços estimados apresentados são baseados no preço da organização selecionada.

## <a name="check-your-billing-account-type"></a>Verificar o tipo de conta de cobrança
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>Próximas etapas

Se você for cliente do EA, confira:

- [Gerenciar o acesso às informações de cobrança do EA para o Azure](manage-billing-access.md)
- [Exibir e baixar a fatura do Microsoft Azure](../understand/download-azure-invoice.md)
- [Exibir e baixar o uso e os encargos do Microsoft Azure](../understand/download-azure-daily-usage.md)
- [Entender sua fatura dos clientes do Contrato Enterprise](../understand/review-enterprise-agreement-bill.md)

Se você tiver um Contrato de Cliente da Microsoft, confira:

- [Entender os termos na planilha de preços de um Contrato de Cliente da Microsoft](mca-understand-pricesheet.md)
- [Exibir e baixar a fatura do Microsoft Azure](../understand/download-azure-invoice.md)
- [Exibir e baixar o uso e os encargos do Microsoft Azure](../understand/download-azure-daily-usage.md)
- [Exibir e baixar documentos de imposto do seu perfil de cobrança](../understand/mca-download-tax-document.md)
- [Noções básicas sobre os encargos na fatura do seu perfil de cobrança](../understand/review-customer-agreement-bill.md)
