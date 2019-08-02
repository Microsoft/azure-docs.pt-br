---
title: Exibir suas contas de cobrança no portal do Azure | Microsoft Docs
description: Saiba como exibir suas contas de cobrança no portal do Azure.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2018
ms.author: banders
ms.openlocfilehash: 36430e9b0a4554761d53b537d3c32fa57068eabb
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490215"
---
# <a name="view-billing-accounts-in-azure-portal"></a>Exibir contas de cobrança no portal do Azure  

Uma conta de cobrança é criada quando você se inscreve para usar o Azure. Você pode usa sua conta de cobrança para gerenciar suas faturas, pagamentos e controlar os custos. Você pode ter acesso a várias contas de cobrança. Por exemplo, você pode ter entrado do Azure para seus projetos pessoais. Você também pode ter acesso por meio do Enterprise Agreement ou contrato de cliente da Microsoft da sua organização. Para cada um desses cenários, você teria uma conta de cobrança separada.

Portal do Azure atualmente suporta os seguintes tipos de contas de cobrança:

- **Programa Microsoft Online Services**: Uma conta de cobrança para um programa do Microsoft Online Services é criada quando você se inscrever para o Azure por meio do site do Azure. Por exemplo, quando você se inscrever para uma [conta gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [conta com as tarifas pré-pagas](https://azure.microsoft.com/offers/ms-azr-0003p/) ou como um [assinante do Visual studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Contrato Enterprise**: Uma conta de cobrança para um contrato Enterprise é criada quando sua organização assina uma [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) para usar o Azure.

- **Contrato do cliente Microsoft**: Uma conta de cobrança para um contrato de cliente da Microsoft é criada quando sua organização trabalha com um representante da Microsoft para assinar um contrato de cliente da Microsoft. Alguns clientes em regiões selecionadas, que se inscreverem por meio do site do Azure para um [conta com as tarifas pré-pagas](https://azure.microsoft.com/offers/ms-azr-0003p/) ou atualizar seus [conta gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) pode ter uma conta de cobrança para um Microsoft Customer Contrato também. Para obter mais informações, consulte [começar com sua conta de cobrança para o contrato do cliente Microsoft](billing-mca-overview.md).

<!--Todo Add section to identify the type of accounts -->

## <a name="scopes-for-billing-accounts"></a>Escopos para contas de cobrança
Um escopo é um nó dentro de uma conta de cobrança que os usuários usam para exibir e gerenciar a cobrança. É onde os usuários gerenciar dados de cobrança, pagamentos, faturas e conduzir o gerenciamento de conta geral. 

### <a name="microsoft-online-services-program"></a>Programa Microsoft Online Services

|Scope  |Definição  |
|---------|---------|
|Conta de cobrança     | Representa um único proprietário (administrador da conta) para um ou mais assinaturas do Azure. Um administrador da conta está autorizado a realizar várias tarefas de cobrança, como criar assinaturas, exibir faturas ou alterar a cobrança para assinaturas.  |
|Assinatura     |  Representa um agrupamento de recursos do Azure. Fatura é gerada neste escopo. Ele tem seus próprios métodos de pagamento que são usados para pagar sua fatura.|


### <a name="enterprise-agreement"></a>Contrato Enterprise

|Scope  |Definição  |
|---------|---------|
|Conta de cobrança    | Representa um registro do Enterprise Agreement. Fatura é gerada neste escopo. Ele é estruturado por meio de departamentos e contas de registro.  |
|department     |  Agrupamento opcional de contas de registro.      |
|Conta de registro     |  Representa um único proprietário da conta. As assinaturas do Azure são criadas sob este escopo.  |


### <a name="microsoft-customer-agreement"></a>Contrato de Cliente da Microsoft

|Scope  |Tarefas  |
|---------|---------|
|Conta de cobrança     |   Representa um contrato do cliente para vários produtos e serviços Microsoft. Ele é estruturado usando perfis de cobrança e seções de nota fiscal.   |
|Perfil de faturamento     |  Representa uma nota fiscal e seus métodos de pagamento. Fatura é gerada neste escopo. Ele pode ter várias seções de nota fiscal.      |
|Seção de nota fiscal     |   Representa um grupo de custos em uma nota fiscal. Assinaturas e outras compras estão associadas a esse escopo.    |


## <a name="switch-billing-scope-in-the-azure-portal"></a>Alternar o escopo de cobrança no portal do Azure


1. Entre no [Portal do Azure](https://portal.azure.com).

2. Pesquise **custo de gerenciamento + cobrança**.

   ![Captura de tela que mostra a pesquisa do portal do Azure](./media/billing-view-all-accounts/billing-search-cost-management-billing.png)

3. Selecione **todos os escopos de cobrança** do lado esquerdo.

   ![Captura de tela que mostra todos os escopos de cobrança](./media/billing-view-all-accounts/billing-list-of-accounts.png)

   \* * Você não verá **todos os escopos de cobrança** se você só tiver acesso a um escopo.

4. Selecione um escopo para exibir detalhes.



## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de Ajuda, [criar uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas
- Saiba como começar [analisando os custos](../cost-management/quick-acm-cost-analysis.md).