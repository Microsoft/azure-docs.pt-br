---
title: Examinar sua fatura do Contrato de Cliente da Microsoft – Azure
description: Saiba como examinar sua conta e o uso de recursos e como verificar cobranças em sua fatura do Contrato de Cliente da Microsoft.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: tutorial
ms.date: 12/02/2020
ms.author: banders
ms.openlocfilehash: b5de81f5eaf76c16c1ea8fc78075c4567c77aeee
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531965"
---
# <a name="tutorial-review-your-microsoft-customer-agreement-invoice"></a>Tutorial: Examinar sua fatura do Contrato de Cliente da Microsoft

Examine os encargos em sua fatura analisando as transações individuais. Na conta de cobrança de um Contrato de Cliente da Microsoft, uma fatura é gerada todos os meses para cada perfil de cobrança. A fatura inclui todos os encargos do mês anterior. Você pode exibir suas faturas no portal do Azure e comparar os encargos com o arquivo de detalhes de uso.

Este tutorial se aplica somente aos clientes do Azure com um Contrato de Cliente da Microsoft.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Examinar transações faturadas no portal do Azure
> * Examinar os encargos pendentes para estimar sua próxima fatura
> * Analisar os encargos de uso do Azure

## <a name="prerequisites"></a>Pré-requisitos

Você precisa ter uma conta de cobrança para um Contrato de Cliente da Microsoft.

Você precisa ter acesso a um Contrato de Cliente da Microsoft. Você precisa ter um perfil de cobrança de Proprietário, Colaborador, Leitor ou Gerenciador de faturas para exibir informações e uso de cobrança. Para obter mais informações sobre funções de cobrança de Contratos de Cliente da Microsoft, confira [Funções e tarefas do perfil de cobrança](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

É necessário que você tenha assinado o Azure há mais de 30 dias. O Azure cobra você no final do período de faturamento.

## <a name="sign-in-to-azure"></a>Entrar no Azure

- Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft

Verifique o tipo de contrato para determinar se você tem acesso a uma conta de cobrança para um Contrato de Cliente da Microsoft.

Na portal do Azure, digite *gerenciamento de custos + cobrança* na caixa de pesquisa e, em seguida, selecione **Gerenciamento de Custos + Cobrança**.

![A captura de tela mostra a pesquisa de gerenciamento de custos e cobrança no portal do Azure.](./media/review-customer-agreement-bill/billing-search-cost-management-billing.png)

Caso você tenha acesso a apenas um escopo do orçamento, selecione **Propriedades** no lado esquerdo. Você tem acesso a uma conta de cobrança para um Contrato de Cliente da Microsoft se o tipo de conta de cobrança é **Contrato de Cliente da Microsoft**.

![Captura de tela mostrando o Contrato de Cliente da Microsoft na página de propriedades](./media/review-customer-agreement-bill/billing-mca-property.png)

Caso tenha acesso a vários escopos do orçamento, verifique o tipo na coluna da conta de cobrança. Você tem acesso a uma conta de cobrança para um Contrato de Cliente da Microsoft se o tipo de conta de cobrança para um dos escopos é **Contrato de Cliente da Microsoft**.

Na página de escopos do orçamento, selecione **Escopos do orçamento** e a conta de cobrança, que será usada no pagamento do uso das assinaturas. A conta de cobrança deve ser do tipo **Contrato de Cliente da Microsoft**.

![Captura de tela mostrando o Contrato de Cliente da Microsoft na página de lista de contas de cobrança](./media/review-customer-agreement-bill/billing-mca-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>Examinar transações faturadas no portal do Azure

No portal do Azure, selecione **Todas as transações** no lado esquerdo da página. Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança, um perfil de cobrança ou uma seção da fatura e, em seguida, selecionar **Todas as transações**.

A página Todas as transações exibe as seguintes informações:

![Captura de tela que mostra a lista de transações faturadas](./media/review-customer-agreement-bill/mca-billed-transactions-list.png)

|Coluna  |Definição  |
|---------|---------|
|Data     | A data da transação  |
|ID da fatura     | O identificador da fatura na qual a transação foi faturada. Se você enviar uma solicitação de suporte, compartilhe a ID com o Suporte do Azure para agilizá-la |
|Tipo de transação     |  O tipo de transação, como encargos de uso, compra e cancelamento  |
|Família de produtos     | A categoria do produto, como computação para máquinas virtuais ou banco de dados para o Banco de Dados SQL do Azure|
|SKU do produto     | Um código exclusivo que identifica a instância do produto |
|Amount     |  A quantidade de transações      |
|Seção da fatura     | A transação é exibida nesta seção da fatura do perfil de cobrança |
|Perfil de faturamento     | A transação é exibida nesta fatura do perfil de cobrança |

Pesquise uma ID da fatura para filtrar as transações da fatura.

### <a name="view-transactions-by-invoice-sections"></a>Exibir transações por seções da fatura

As seções da fatura ajudam você a organizar os custos da fatura de um perfil de cobrança. Para obter mais informações. Quando uma fatura é gerada, os encargos de todas as seções no perfil de cobrança são mostrados na fatura.

A imagem a seguir mostra os encargos da seção da fatura do Departamento de Contabilidade em uma fatura de exemplo.

![Imagem de exemplo mostrando as informações de detalhes por seção da fatura](./media/review-customer-agreement-bill/invoicesection-details.png)

Ao identificar os encargos de uma seção da fatura, você poderá exibir as transações no portal do Azure para entender os encargos.

Acesse a página Todas as transações no portal do Azure para exibir as transações de uma fatura.

Filtre pelo nome da seção da fatura para exibir as transações.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Examinar os encargos pendentes para estimar sua próxima fatura

Na conta de cobrança de um Contrato de Cliente da Microsoft, os encargos são estimados e considerados pendentes até que sejam faturados. Exiba os encargos pendentes no portal do Azure para estimar sua próxima fatura. Os encargos pendentes são estimados e não incluem impostos. Os encargos reais em sua próxima fatura serão diferentes dos encargos pendentes.

### <a name="view-summary-of-pending-charges"></a>Exibir um resumo dos encargos pendentes

Entre no [portal do Azure](https://portal.azure.com).

Selecione um perfil de cobrança. Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança. Na conta de cobrança, selecione **Perfis de cobrança** e, em seguida, selecione um perfil de cobrança.

Selecione a guia **Resumo** na parte superior da tela.

A seção de encargos exibe os encargos do mês atual e do mês passado.

![A captura de tela mostra um resumo do perfil de cobrança.](./media/review-customer-agreement-bill/mca-billing-profile-summary.png)

Os encargos do mês atual são os encargos pendentes do mês atual e são faturados quando a fatura é gerada para o mês. Se a fatura do mês passado ainda não foi gerada, os encargos do mês passado também ficam pendentes e são exibidos em sua próxima fatura.

### <a name="view-pending-transactions"></a>Exibir transações pendentes

Ao identificar encargos pendentes, você pode entender os encargos analisando as transações individuais que contribuíram para eles. Neste ponto, os encargos de uso pendentes não são exibidos na página Todas as transações. Exiba os encargos de uso pendentes na página de assinaturas do Azure.

Entre no [portal do Azure](https://portal.azure.com).

Na portal do Azure, digite *gerenciamento de custos + cobrança* na caixa de pesquisa e, em seguida, selecione **Gerenciamento de Custos + Cobrança**.

Selecione um perfil de cobrança. Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança. Na conta de cobrança, selecione **Perfis de cobrança** e, em seguida, selecione um perfil de cobrança.

Selecione **Todas as transações** no lado esquerdo da página.

Pesquise *pendentes*. Use o filtro **Intervalo de tempo** para exibir os encargos pendentes para o mês atual ou o mês passado.

![Captura de tela que mostra a lista de transações pendentes](./media/review-customer-agreement-bill/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Exibir encargos de uso pendentes

Entre no [portal do Azure](https://portal.azure.com).

Na portal do Azure, digite *gerenciamento de custos + cobrança* na caixa de pesquisa e, em seguida, selecione **Gerenciamento de Custos + Cobrança**.

Selecione um perfil de cobrança. Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança. Na conta de cobrança, selecione **Perfis de cobrança** e, em seguida, selecione um perfil de cobrança.

Selecione **Todas as assinaturas** no lado esquerdo da página.

A página de assinaturas do Azure exibe os encargos do mês atual e do mês passado para cada assinatura do perfil de cobrança. Os encargos do mês atual são os encargos pendentes do mês atual e são faturados quando a fatura é gerada para o mês. Se a fatura do mês passado ainda não foi gerada, os encargos do mês passado também ficam pendentes.

![A captura de tela mostra assinaturas com encargos do mês atual e encargos do mês passado.](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Analisar os encargos de uso do Azure

Use o arquivo CSV de uso e encargos do Azure para analisar os encargos com base no uso. Baixe o arquivo de uma fatura ou de encargos pendentes.

### <a name="download-your-invoice-and-usage-details"></a>Baixar a fatura e os detalhes de uso

Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança ou um perfil de cobrança no Gerenciamento de Custos + Cobrança. No menu esquerdo, selecione **Faturas** em **Cobrança**. Na grade da fatura, encontre a linha da fatura que você deseja baixar. Selecione o símbolo de download ou as reticências (...) no final da linha. Na caixa **Baixar**, baixe o arquivo de detalhes de uso e a fatura.

### <a name="view-detailed-usage-by-invoice-section"></a>Exibir o uso detalhado por seção da fatura

Filtre o arquivo de uso e encargos do Azure para reconciliar os encargos de uso nas seções da fatura.

As seguintes informações orientam você na reconciliação de encargos de computação na seção da fatura do Departamento de Contabilidade:

![Imagem de exemplo mostrando as informações de detalhes por seção da fatura](./media/review-customer-agreement-bill/invoicesection-details.png)

| PDF da fatura | CSV de uso e encargos do Azure |
| --- | --- |
|Departamento de contabilidade |invoiceSectionName |
|Encargos de uso – Plano do Microsoft Azure |productOrderName |
|Computação |serviceFamily |

Filtre a coluna **invoiceSectionName** no arquivo CSV para **Departamento de Contabilidade**. Em seguida, filtre a coluna **productOrderName** no arquivo CSV para **Plano do Microsoft Azure**. Em seguida, filtre a coluna **serviceFamily** no arquivo CSV para **Microsoft.Compute**.

![Captura de tela que mostra o arquivo de uso e encargos filtrado por seção da fatura](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-invoice-section.png)

### <a name="view-detailed-usage-by-subscription"></a>Exibir o uso detalhado por assinatura

Filtre o arquivo CSV de uso e encargos do Azure para reconciliar os encargos de uso em suas assinaturas. Ao identificar os encargos de uma assinatura, use o arquivo CSV de uso e encargos do Azure para analisá-los.

A imagem a seguir mostra a lista de assinaturas no portal do Azure.

![A captura de tela mostra a lista de assinaturas no portal do Azure com uma assinatura destacada.](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list-highlighted.png)

Filtre a coluna **subscriptionName** no arquivo CSV de uso e encargos do Azure para **WA_Subscription** para exibir os encargos de uso detalhado de WA_Subscription.

![Captura de tela que mostra o arquivo de uso e encargos filtrado por assinatura](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>Pagar sua fatura

As instruções para pagamento de sua fatura são mostradas na parte inferior da fatura. [Saiba como efetuar o pagamento](mca-understand-your-invoice.md#how-to-pay).

Se você já pagou a fatura, verifique o status do pagamento na página Faturas no portal do Azure.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Examinar transações faturadas no portal do Azure
> * Examinar os encargos pendentes para estimar sua próxima fatura
> * Analisar os encargos de uso do Azure

Conclua o guia de início rápido para começar a usar a análise de custo.

> [!div class="nextstepaction"]
> [Começar a analisar os custos](../costs/quick-acm-cost-analysis.md)
