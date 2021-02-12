---
title: Exibir as suas contas de cobrança no portal do Azure
description: Saiba como exibir suas contas de cobrança no portal do Azure. Confira informações de escopo referente ao Contrato Enterprise, ao Contrato de Cliente da Microsoft e ao Contrato de Parceiro da Microsoft.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: banders
ms.openlocfilehash: a9e3297ad055157d552424ecc48db6801f43b220
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929948"
---
# <a name="billing-accounts-and-scopes-in-the-azure-portal"></a>Escopos do orçamento e contas de cobrança no portal do Azure

Uma conta de cobrança é criada quando você se inscreve para usar o Azure. Você usa a conta de cobrança para gerenciar faturas, pagamentos e acompanhar os custos. Você pode ter acesso a várias contas de cobrança. Por exemplo, você pode ter se inscrito no Azure para seus projetos pessoais. Você também pode ter acesso por meio do Contrato Enterprise de sua organização ou do Contrato de Cliente da Microsoft. Para cada um desses cenários, você terá uma conta de cobrança separada.

O portal do Azure dá suporte aos seguintes tipos de contas de cobrança:

- **Programa Microsoft Online Services**: Uma conta de cobrança para um Programa Microsoft Online Services é criada quando você se inscreve no Azure por meio do site do Azure. Por exemplo, quando você se inscreve em uma [conta gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), uma [conta com taxas pagas conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/) ou como [assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Contrato Enterprise**: Uma conta de cobrança para um Contrato Enterprise é criada quando sua organização assina um [EA (Contrato Enterprise)](https://azure.microsoft.com/pricing/enterprise-agreement/) para usar o Azure. Você pode ter um máximo de 2.000 assinaturas em um Contrato Enterprise.

- **Contrato de Cliente da Microsoft**: Uma conta de cobrança para um Contrato de Cliente da Microsoft é criada quando sua organização decide assinar um Contrato de Cliente da Microsoft por meio de um representante da Microsoft. Alguns clientes em regiões selecionadas, que se inscrevem por meio do site do Azure em uma [conta com taxas pagas conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/) ou uma [conta gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) também podem ter uma conta de cobrança para um Contrato de Cliente da Microsoft. Você pode ter, no máximo, 20 assinaturas em um Contrato de Cliente da Microsoft. Um Contrato de Cliente da Microsoft para uma empresa não tem um limite no número de assinaturas. Para obter mais informações, confira [Introdução à conta de cobrança para o Contrato de Cliente da Microsoft](../understand/mca-overview.md).

- **Contrato de Parceiro da Microsoft**: Uma conta de cobrança para um Contrato de Parceiro da Microsoft é criada para parceiros CSP (Provedor de Soluções na Nuvem) para gerenciar os clientes deles na nova experiência de comércio. Os parceiros precisam ter pelo menos um cliente com um [plano do Azure](/partner-center/purchase-azure-plan) para gerenciar as respectivas contas de cobrança no portal do Azure. Para obter mais informações, confira [Introdução à conta de cobrança para o Contrato de Parceiro da Microsoft](../understand/mpa-overview.md).

Para determinar o tipo de sua conta de cobrança, confira [Verificar o tipo de sua conta de cobrança](#check-the-type-of-your-account).

## <a name="scopes-for-billing-accounts"></a>Escopos para contas de cobrança
Um escopo é um nó em uma conta de cobrança que você usa para exibir e gerenciar a cobrança. É nele que você gerencia dados de cobrança, pagamentos, faturas e realiza o gerenciamento geral da conta.

Caso você não tenha acesso para ver nem gerenciar contas de cobrança, provavelmente, não tem permissão para acessá-las. Solicite ao administrador da conta de cobrança a permissão de acesso. Para obter mais informações, consulte os seguintes artigos:

- [Acesso ao Programa do Microsoft Online Services](manage-billing-access.md)
- [Acesso ao Contrato de Cliente da Microsoft](understand-mca-roles.md)
- [Acesso ao Contrato Enterprise](understand-ea-roles.md)

### <a name="microsoft-online-services-program"></a>Programa Microsoft Online Services

![Captura de tela que mostra a hierarquia MOSP](./media/view-all-accounts/mosp-hierarchy.png)

|Escopo  |Definição  |
|---------|---------|
|Conta de cobrança     | Representa um contrato que um cliente aceita para usar o Azure. Ele contém uma ou mais assinaturas.  |
|Subscription     |  Representa um agrupamento de recursos do Azure. A fatura é gerada nesse escopo. Outras informações de cobrança, como formas de pagamento e endereço de uso, estão associadas a esse escopo.|

### <a name="enterprise-agreement"></a>Contrato Enterprise

![Captura de tela que mostra a hierarquia de EA](./media/view-all-accounts/ea-hierarchy.png)

|Escopo  |Definição  |
|---------|---------|
|Conta de cobrança    | Representa um registro do Contrato Enterprise. Ele contém um ou mais departamentos e contas. A fatura é gerada nesse escopo. |
|department     |  Agrupamento opcional de contas para segmentar custos em agrupamentos lógicos e definir o orçamento.     |
|Conta     |  Representa um único proprietário da conta. Os proprietários da conta têm permissões para criar e gerenciar assinaturas do Azure que são cobradas para o registro. |

### <a name="microsoft-customer-agreement"></a>Contrato de Cliente da Microsoft

![Captura de tela que mostra a hierarquia de MCA](./media/view-all-accounts/mca-hierarchy.png)

|Escopo  |Tarefas  |
|---------|---------|
|Conta de cobrança     |   Representa um contrato que um cliente aceita para usar produtos e serviços Microsoft. Ele contém um ou mais perfis de cobrança. |
|Perfil de faturamento     |   Representa uma fatura e as informações de cobrança relacionadas, como métodos de pagamento e endereço para cobrança. Ele contém uma ou mais seções da fatura. |
|Seção da fatura     |   Representa um grupo de custos em uma fatura. As assinaturas do Azure e outras compras, como o Azure Marketplace e os produtos de origem do aplicativo, estão associadas a esse escopo.    |

### <a name="microsoft-partner-agreement"></a>Contrato de Parceiro da Microsoft

![Captura de tela que mostra a hierarquia de MPA](./media/view-all-accounts/mpa-hierarchy.png)

|Escopo  |Tarefas  |
|---------|---------|
|Conta de cobrança     |   Representa um contrato de parceiro para gerenciar os produtos e serviços da Microsoft dos clientes na nova experiência de comércio. Ele contém um ou mais perfis de cobrança e clientes.   |
|Perfil de faturamento     |   Representa uma fatura de uma moeda.     |
|Cliente    |   Representa um cliente para um parceiro CSP (Provedor de Soluções na Nuvem).  As assinaturas do Azure e outras compras, como o Azure Marketplace e os produtos de origem do aplicativo, estão associadas a esse escopo.  |
|Revendedor    |   Revendedor que fornece serviços a um cliente. Esse é um campo opcional para uma assinatura e é aplicável somente aos provedores indiretos no modelo de dois níveis do CSP.     |

## <a name="switch-billing-scope-in-the-azure-portal"></a>Alternar o escopo do orçamento no portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com).

2. Pesquise **Gerenciamento de Custos + Cobrança**.

   ![Captura de tela que mostra a pesquisa no portal do Azure de Gerenciamento de Custos e Cobrança.](./media/view-all-accounts/billing-search-cost-management-billing.png)

3. Na página de visão geral, selecione **Alternar escopo**.

   ![Captura de tela que mostra os escopos do orçamento.](./media/view-all-accounts/overview-select-scopes.png)

   > [!Note]
    >
    > Você não verá a opção Alternar escopo se você tiver acesso a apenas um escopo.

4. Selecione um escopo para exibir detalhes.

   ![Captura de tela que mostra escopos do orçamento, que você pode selecionar para obter detalhes.](./media/view-all-accounts/list-of-scopes.png)

## <a name="check-the-type-of-your-account"></a>Verifique o tipo de sua conta
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas
- Saiba como começar a [analisar os custos](../costs/quick-acm-cost-analysis.md).