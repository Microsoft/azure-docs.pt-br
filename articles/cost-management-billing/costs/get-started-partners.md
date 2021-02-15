---
title: Introdução ao Gerenciamento de Custos do Azure para parceiros
description: Este artigo explica como os parceiros usam os recursos do Gerenciamento de Custos do Azure e como eles habilitam o acesso do Gerenciamento de Custos para os clientes.
author: bandersmsft
ms.author: banders
ms.date: 01/27/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: aparnag
ms.custom: secdec18
ms.openlocfilehash: 40c683d98a13bf4810ec4575af3fba5ae64961f0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946992"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Introdução ao Gerenciamento de Custos do Azure para parceiros

O Gerenciamento de Custos do Azure está disponível nativamente para os parceiros diretos que integraram os clientes a um Contrato de Cliente da Microsoft e [compraram um plano do Azure](/partner-center/purchase-azure-plan). Este artigo explica como os parceiros usam os recursos do [Gerenciamento de Custos do Azure](../index.yml) para ver os custos de assinaturas no plano do Azure. Também descreve como os parceiros habilitam o acesso do Gerenciamento de Custos às taxas de varejo para seus clientes.

Para parceiros diretos e provedores indiretos, o administrador global e os agentes administradores podem acessar o Gerenciamento de Custos no locatário do parceiro e gerenciar custos aos preços faturados.

Os revendedores e os clientes podem acessar o Gerenciamento de Custos no locatário do cliente e ver os custos de consumo das assinaturas, em que os custos são calculados e mostrados em taxas de varejo. No entanto, eles precisam ter acesso do Azure RBAC à assinatura no locatário do cliente para exibir os custos. A política de visibilidade de custo precisa ser habilitada pelo provedor para o locatário do cliente.

Os clientes podem usar os recursos do Gerenciamento de Custos quando eles são habilitados pelo parceiro CSP.

Os parceiros CSP usam o Gerenciamento de Custos para:

- Entender os custos faturados e associá-los ao cliente, às assinaturas, aos grupos de recursos e aos serviços.
- Obter uma visão intuitiva dos custos do Azure na [análise de custo](quick-acm-cost-analysis.md) com funcionalidades para analisar os custos por cliente, assinatura, grupo de recursos, recurso, medidor, serviço e muitas outras dimensões.
- Ver os custos de recursos que têm o PEC (crédito ganho pelo parceiro) aplicado na análise de custo.
- Configurar as notificações e a automação usando [orçamentos](tutorial-acm-create-budgets.md) programáticos e alertas quando os custos excederem os orçamentos.
- Habilitar a política do Azure Resource Manager que fornece acesso do cliente aos dados do Gerenciamento de Custos. Em seguida, os clientes podem ver os dados de custo do consumo nas assinaturas usando [taxas pagas conforme o uso](https://azure.microsoft.com/pricing/calculator/).
- Exportar os dados de custo e uso para um blob de armazenamento com uma assinatura paga conforme o uso.

Veja um exemplo que mostra os custos de todos os clientes.

[![Exemplo mostrando os custos de todos os clientes](./media/get-started-partners/customer-costs1.png)](./media/get-started-partners/customer-costs1.png#lightbox)

Veja um exemplo que mostra os custos de um só cliente.

[![Exemplo mostrando os custos de um só cliente](./media/get-started-partners/customer-costs2.png)](./media/get-started-partners/customer-costs2.png#lightbox)

Todas as funcionalidades disponíveis no Gerenciamento de Custos do Azure também estão disponíveis nas APIs REST. Use as APIs para automatizar as tarefas de gerenciamento de custos.

## <a name="prerequisites"></a>Pré-requisitos

Como parceiro, o Gerenciamento de Custos do Azure está disponível nativamente apenas para as assinaturas que estão no plano do Azure.

Para habilitar o Gerenciamento de Custos do Azure no portal do Azure, você precisa ter confirmado a aceitação pelo cliente do Contrato de Cliente da Microsoft (em nome do cliente) e feito a transição do cliente para o plano do Azure. Somente os custos das assinaturas que foram migrados para o plano do Azure estão disponíveis no Gerenciamento de Custos do Azure.

O Gerenciamento de Custos do Azure exige acesso de leitura à sua conta de cobrança ou assinatura.

Para obter mais informações sobre como habilitar e atribuir acesso ao Gerenciamento de Custos do Azure em uma conta de cobrança, confira [Atribuir funções e permissões de usuários](/partner-center/permissions-overview). As funções **Administrador global** e **Agente administrativo** podem gerenciar os custos de uma conta de cobrança.

Para acessar o Gerenciamento de Custos do Azure no escopo da assinatura, qualquer usuário com acesso do Azure RBAC a uma assinatura pode exibir os custos com base em taxas de varejo (pagas conforme o uso). No entanto, a [política de visibilidade de custo do locatário do cliente](#enable-the-policy-to-view-azure-usage-charges) precisa ser habilitada. Para ver uma lista completa dos tipos de contas compatíveis, confira [Entender os dados do Gerenciamento de Custos](understand-cost-mgt-data.md).

Ao transferir os contratos de cobrança existentes para um novo parceiro, as funcionalidades de gerenciamento de custos só estão disponíveis para o relacionamento de cobrança atual com o parceiro. Os custos históricos gerados antes da transferência para o novo parceiro não são migrados para a nova conta de cobrança. No entanto, o histórico de custo permanece na conta de cobrança associada original.

## <a name="how-cost-management-uses-scopes"></a>Como o Gerenciamento de Custos usa os escopos

Os escopos são o local em que você gerencia os dados de cobrança, têm funções específicas para pagamentos, exibem faturas e realizam o gerenciamento geral da conta. As funções de cobrança e de conta são gerenciadas separadamente dos escopos usados para o gerenciamento de recursos, que usam o Azure RBAC. Para distinguir claramente a intenção dos escopos separados, incluindo as diferenças de controle de acesso, eles são chamados de escopos do orçamento e escopos do Azure RBAC, respectivamente.

Para entender os escopos de cobrança e os escopos do Azure RBAC e como o Gerenciamento de Custos funciona com os escopos, confira [Entender e trabalhar com escopos](understand-work-scopes.md).

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>Gerenciar os custos com escopos de cobrança no locatário do parceiro

Depois que você integrar seus clientes a um Contrato de Cliente da Microsoft, os _escopos de cobrança_ a seguir estarão disponíveis em seu locatário. Use os escopos para gerenciar os custos no Gerenciamento de Custos.

### <a name="billing-account-scope"></a>Escopo da conta de cobrança

Use o escopo da conta de cobrança para ver os custos antes da dedução do imposto em todos os perfis de cobrança e clientes. Os custos de fatura são mostrados apenas para os produtos baseados em consumo do cliente no Contrato de Cliente da Microsoft. No entanto, os custos de fatura são mostrados para os produtos baseados em compra dos clientes no Contrato de Cliente da Microsoft e na oferta do CSP. Atualmente, a moeda padrão para ver os custos no escopo é o dólar americano. Os orçamentos definidos para o escopo também estão em USD.

Independentemente das diferentes moedas cobradas, os parceiros usam o escopo da conta de cobrança para definir orçamentos e gerenciar os custos em dólares de clientes, assinaturas, recursos e grupos de recursos.

Os parceiros também filtram os custos em uma moeda de cobrança específica de clientes na exibição de análise de custo. Selecione a lista **Custo real** para ver os custos nas moedas de cobrança compatíveis.

![Exemplo mostrando a seleção de Custo real para moedas](./media/get-started-partners/actual-cost-selector.png)

Use a [exibição de custo amortizado](quick-acm-cost-analysis.md#customize-cost-views) nos escopos de cobrança para ver os custos amortizados da instância reservada em um prazo de reserva.

### <a name="billing-profile-scope"></a>Escopo do perfil de cobrança

Use o escopo do perfil de cobrança para ver os custos antes da dedução do imposto na moeda de cobrança em todos os clientes para todos os produtos e assinaturas incluídas em uma fatura. Filtre os custos em um perfil de cobrança para uma fatura específica usando o filtro **InvoiceID**. O filtro mostra os custos de consumo e compra de produto para uma fatura específica. Filtre também os custos de um cliente específico na fatura para ver os custos antes da dedução do imposto.

Depois de integrar os clientes a um Contrato de Cliente da Microsoft, você receberá uma fatura que inclui todos os preços de todos os produtos (consumo, compras e direitos) referente a esses clientes no Contrato de Cliente da Microsoft. Quando cobradas na mesma moeda, essas faturas também incluem os preços de produtos comprados e de direitos, como SaaS, Azure Marketplace e reservas para clientes que ainda estão na oferta CSP clássica agora no plano do Azure.

Para ajudar a reconciliar os preços na fatura do cliente, o escopo do perfil de cobrança permite que você veja todos os custos acumulados para uma fatura de seus clientes. Assim como a fatura, o escopo mostra os custos de cada cliente no novo Contrato de Cliente da Microsoft. O escopo também mostra cada preço para os produtos de direitos do cliente ainda na oferta atual do CSP.

Os escopos do perfil de cobrança e da conta de cobrança são os únicos escopos aplicáveis que mostram os preços para os produtos baseados em compra e em direito, como o Azure Marketplace e as compras de reserva.

Os perfis de cobrança definem as assinaturas que são incluídas em uma fatura. Os perfis de cobrança são o equivalente funcional de um registro do Contrato Enterprise. Um perfil de cobrança é o escopo no qual as faturas são geradas.

No momento, a moeda de cobrança é a moeda padrão na exibição dos custos no escopo do perfil de cobrança. Os orçamentos definidos no escopo do perfil de cobrança são apresentados na moeda de cobrança.

Os parceiros podem usar o escopo para reconciliação com as faturas. Além disso, eles usam o escopo para definir os orçamentos na moeda de cobrança para os seguintes itens:

- Fatura filtrada específica
- Cliente
- Subscription
- Resource group
- Recurso
- Serviço do Azure
- Medidor
- ResellerMPNID

### <a name="customer-scope"></a>Escopo do cliente

Os parceiros usam o escopo para gerenciar os custos associados aos clientes que estão integrados ao Contrato de Cliente da Microsoft. O escopo permite que os parceiros vejam os custos antes de dedução do imposto para um cliente específico em uma moeda de cobrança. Filtre também os custos antes da dedução do imposto de uma assinatura, um grupo de recursos ou um recurso específico.

O escopo do cliente não inclui os clientes que estão na oferta atual do CSP. O escopo inclui apenas os clientes que têm um Contrato de Cliente da Microsoft. Os custos de direitos, não o uso do Azure, para os clientes da oferta atual do CSP estão disponíveis nos escopos da conta de cobrança e do perfil de cobrança quando você aplica o filtro do cliente. Os orçamentos definidos nesse escopo são apresentados na moeda de cobrança.

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Acesso do parceiro aos escopos do orçamento no Gerenciamento de Custos

Somente os usuários com as funções **Administrador global** e **Agente administrativo** podem gerenciar e ver os custos de contas de cobrança, perfis de cobrança e clientes diretamente no locatário do Azure do parceiro. Para obter mais informações sobre as funções do Partner Center, confira [Atribuir funções e permissões de usuários](/partner-center/permissions-overview).

## <a name="enable-cost-management-for-customer-tenant-subscriptions"></a>Habilitar o gerenciamento de custos para assinaturas de locatário do cliente

Os parceiros poderão habilitar o acesso ao Gerenciamento de Custos depois que os clientes forem integrados a um Contrato de Cliente da Microsoft. Em seguida, os parceiros poderão habilitar uma política que permite aos clientes ver os custos calculados dos serviços consumidos do Azure com base em taxas de varejo pagas conforme o uso. Os custos são mostrados na moeda de cobrança do cliente para o uso consumido nos escopos dos grupos de recursos e da assinatura do Azure RBAC.

Quando a política de visibilidade de custo é habilitada pelo parceiro, qualquer usuário com acesso ao Azure Resource Manager na assinatura pode gerenciar e analisar os custos com base em taxas pagas conforme o uso. Efetivamente, os revendedores e os clientes que têm o acesso do Azure RBAC apropriado às assinaturas do Azure podem exibir o custo.

Independentemente da política, os administradores globais e os agentes de administração do provedor poderão ver os custos de assinatura se tiverem acesso à assinatura e ao grupo de recursos.

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>Habilitar a política para ver os preços de uso do Azure

Você precisa ser membro do grupo do **agente de administração** para ver e atualizar a política. Use as informações a seguir para habilitar a política e permitir que os clientes vejam os preços de uso do Azure.

No portal do Azure, entre no *locatário do parceiro* e selecione **Gerenciamento de Custos e Cobrança**. Selecione o escopo do orçamento relevante na área Escopo do Orçamento e escolha **Clientes**. A lista de clientes está associada à conta de cobrança. *Se você entrar por engano no locatário do cliente, não verá a lista **Clientes**.*

Na lista de clientes, selecione o cliente que deseja permitir para ver os custos.

[![Selecionar clientes no Gerenciamento de Custos](./media/get-started-partners/customer-list.png)](./media/get-started-partners/customer-list.png#lightbox)

Em **Configurações**, selecione **Políticas**.

A política de visibilidade de custo atual é mostrada para os preços de **Uso do Azure** associados às assinaturas do cliente selecionado.
![Política para permitir que os clientes vejam os preços pagos conforme o uso](./media/get-started-partners/cost-management-billing-policies.png)

Quando a política é definida como **Não**, o Gerenciamento de Custos do Azure não está disponível para os usuários da assinatura associados ao cliente. A menos que seja habilitado por um parceiro, a política de visibilidade de custo é desabilitada por padrão para todos os usuários da assinatura.

Quando a política de custo é definida como **Sim**, os usuários da assinatura associados ao locatário do cliente podem ver os preços de uso com as taxas pagas conforme o uso.

Quando a política de visibilidade de custo é habilitada, todos os serviços que tenham o uso da assinatura mostram os custos com base nas taxas pagas conforme o uso. O uso de reserva é exibido com preços sem nenhum valor para os custos reais e amortizados. As compras e os direitos não estão associados a uma assinatura específica. Portanto, as compras não são exibidas no escopo da assinatura. O administrador global/agente de administração de um parceiro direto ou um provedor indireto também pode usar a [API de Atualização de Cliente](/rest/api/billing/2019-10-01-preview/policies/updatecustomer) para definir a política de visibilidade de custos de cada cliente em escala.

### <a name="view-subscription-costs-in-the-customer-tenant"></a>Exibir custos de assinatura no locatário do cliente

Para ver os custos de uma assinatura, abra **Gerenciamento de Custos + Cobrança** no locatário do Azure do cliente. Selecione **Análise de custo** na assinatura necessária para começar a examinar os custos. Você pode ver os custos de consumo para cada assinatura individualmente no locatário do cliente.

[![Exibir a análise de custo como um cliente ](./media/get-started-partners/subscription-costs.png)](./media/get-started-partners/subscription-costs.png#lightbox)

A análise de custo, os orçamentos e os alertas estão disponíveis para os escopos do Azure RBAC do grupo de recursos e da assinatura em custos com base em taxas pagas conforme o uso.

As exibições amortizadas e os custos reais das instâncias reservadas nos escopos do Azure RBAC não mostram encargos. Os custos de compra para direitos, como valores de Instâncias Reservadas e do Marketplace, são mostrados apenas nos escopos do orçamento no locatário do parceiro em que as compras foram feitas.

As taxas de varejo usadas para computar os custos mostrados na exibição são iguais aos preços mostrados na Calculadora de Preços do Azure para todos os clientes. Os custos mostrados não incluem descontos nem créditos que o parceiro possa ter, como Créditos Ganhos do Parceiro, Descontos por Nível e descontos de Serviço Global.

 

## <a name="analyze-costs-in-cost-analysis"></a>Analisar os custos na análise de custo

Os parceiros com acesso aos escopos de cobrança no locatário do parceiro podem explorar e analisar os custos faturados na análise de custos em clientes para um cliente específico ou para uma fatura. Na exibição [Análise de custo](quick-acm-cost-analysis.md), você também pode [salvar exibições](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) e exportar dados para [arquivos CSV e PNG](quick-acm-cost-analysis.md#download-usage-data).

Os usuários do Azure RBAC com acesso à assinatura no locatário do cliente também podem analisar os custos de varejo para as assinaturas no locatário do cliente, salvar exibições e exportar dados para arquivos CSV e PNG.

Use os recursos Filtrar e Agrupar por na análise de custo para analisar os custos por vários campos. Os campos específicos do parceiro serão mostrados na próxima seção.

## <a name="data-fields"></a>Campos de dados

Os campos de dados a seguir são encontrados em arquivos de detalhes de uso e nas APIs do Gerenciamento de Custos. Quando disponíveis, as informações equivalentes do Partner Center são mostradas. Para os campos em negrito a seguir, os parceiros podem usar recursos Filtrar e Agrupar por na análise de custo para analisar os custos por vários campos. Ambos os campos em negrito só se aplicam aos Contratos de Cliente da Microsoft com suporte dos parceiros.

| **Nome do campo** | **Descrição** | **Equivalente do Partner Center** |
| --- | --- | --- |
| invoiceId | A ID da fatura mostrada na fatura para a transação específica. | Número da fatura em que a transação é mostrada. |
| previousInvoiceID | Referência a uma fatura original se há um reembolso (custo negativo). Populado somente quando há um reembolso. | N/D |
| billingAccountName | Nome da conta de cobrança que representa o parceiro. Ela acumula todos os custos dos clientes que se integraram a um Contrato de Cliente da Microsoft e os clientes do CSP que fizeram compras de direitos como SaaS, Azure Marketplace e reservas. | N/D |
| billingAccountID | Identificador da conta de cobrança que representa o parceiro. | ID Raiz do Comércio do Parceiro do MCAPI. Usada em uma solicitação, mas não incluída em uma resposta.|
| billingProfileID | Identificador do perfil de cobrança que agrupa os custos das faturas em uma só moeda de cobrança dos clientes que se integraram a um Contrato de Cliente da Microsoft e os clientes do CSP que fizeram compras de direitos como SaaS, Azure Marketplace e reservas. | ID do Grupo de Cobrança do Parceiro do MCAPI. Usada em uma solicitação, mas não incluída em uma resposta. |
| billingProfileName | Nome do perfil de cobrança que agrupa os custos das faturas em uma só moeda de cobrança dos clientes que se integraram a um Contrato de Cliente da Microsoft e os clientes do CSP que fizeram compras de direitos como SaaS, Azure Marketplace e reservas. | N/D |
| invoiceSectionName | Nome do projeto que está sendo cobrado na fatura. Não aplicável aos Contratos de Cliente da Microsoft integrados por parceiros. | N/D |
| invoiceSectionID | Identificador do projeto que está sendo cobrado na fatura. Não aplicável aos Contratos de Cliente da Microsoft integrados por parceiros. | N/D |
| **CustomerTenantID** | Identificador do locatário do Azure Active Directory da assinatura do cliente. | ID organizacional do cliente: a TenantID do Azure Active Directory do cliente. |
| **CustomerName** | Nome do locatário do Azure Active Directory para a assinatura do cliente. | Nome da organização do cliente, conforme mostrado no Partner Center. Importante para reconciliar a fatura com as informações do sistema. |
| **CustomerTenantDomainName** | Nome de domínio do locatário do Azure Active Directory da assinatura do cliente. | Domínio do locatário do Azure Active Directory do cliente. |
| **PartnerTenantID** | Identificador do locatário do Azure Active Directory do parceiro. | ID do Locatário do Azure Active Directory do parceiro chamada ID de Parceiro, no formato GUID. |
| **PartnerName** | Nome do locatário do Azure Active Directory do parceiro. | Nome do parceiro. |
| **ResellerMPNID** | MPNID do revendedor associado à assinatura. | ID do MPN do revendedor registrado para a assinatura. Não disponível para a atividade atual. |
| costCenter | Centro de custo associado à assinatura. | N/D |
| billingPeriodStartDate | Data de início do período de cobrança, conforme mostrado na fatura. | N/D |
| billingPeriodEndDate | Data de término do período de cobrança, conforme mostrado na fatura. | N/D |
| servicePeriodStartDate | Data de início do período de classificação em que a utilização do serviço foi classificada para cobranças. Os preços dos serviços do Azure são determinados para o período de classificação. | ChargeStartDate no Partner Center.  Data de início do ciclo de cobrança, exceto ao apresentar datas de dados de uso latente anteriormente não cobrados (do ciclo de cobrança anterior). A hora sempre é o início do dia, 0h00. |
| servicePeriodEndDate | Data de término do período em que a utilização do serviço foi classificada para cobranças. Os preços dos serviços do Azure são determinados com base no período de classificação. | N/D |
| date | Para os dados de consumo do Azure, ele mostra a data de uso como classificado. Para a instância reservada, ele mostra a data de compra. Para os preços recorrentes e os preços avulsos, como Marketplace e suporte, ele mostra a data da compra. | N/D |
| productID | Identificador do produto que tem preços acumulados por consumo ou compra. É a chave concatenada de productID e SKuID, conforme mostrado no Partner Center. | A ID do produto. |
| product | Nome do produto que tem preços acumulados por consumo ou compra, conforme mostrado na fatura. | O nome do produto no catálogo. |
| serviceFamily | Mostra a família de serviços para o produto adquirido ou cobrado. Por exemplo, Armazenamento ou Computação. | N/D |
| productOrderID | O identificador do ativo ou o nome do plano do Azure ao qual a assinatura pertence. Por exemplo, plano do Azure. | CommerceSubscriptionID |
| productOrderName | O nome do plano do Azure ao qual a assinatura pertence. Por exemplo, plano do Azure. | N/D|
| consumedService | Serviço consumido (taxonomia herdada), conforme usado nos detalhes de uso do EA herdado. | Serviço mostrado no Partner Center. Por exemplo, Microsoft.Storage, Microsoft.Compute e microsoft.operationalinsights. |
| meterID | Identificador limitado para o consumo medido. | A ID do medidor usado. |
| meterName | Identifica o nome do medidor para o consumo medido. | O nome do medidor consumido. |
| meterCategory | Identifica o serviço de nível superior para uso. | O serviço de nível superior para o uso. |
| meterSubCategory | Define o tipo ou a subcategoria de serviço do Azure que pode afetar a taxa. | O tipo de serviço do Azure que pode afetar a taxa.|
| meterRegion | Identifica o local do datacenter para determinados serviços que são cobrados com base no local do datacenter. | A localização regional de um data center para serviços, quando aplicável e populado. |
| ID da assinatura | Identificador exclusivo gerado pela Microsoft para a assinatura do Azure. | EntitlementID |
| subscriptionName | Nome da assinatura do Azure. | N/D |
| Termo | Exibe o prazo de validade da oferta. Por exemplo, as instâncias reservadas mostram 12 meses de um prazo anual da instância reservada. Para compras avulsas ou recorrentes, o prazo exibe um mês para SaaS, Azure Marketplace e suporte. Não aplicável para consumo do Azure. | N/D |
| publisherType (firstParty, thirdPartyReseller, thirdPartyAgency) | Tipo de distribuidor que identifica o distribuidor como interno, revendedor de terceiros ou agência de terceiros. | N/D |
| partNumber | Número de peça da instância reservada não utilizada e dos serviços do Azure Marketplace. | N/D |
| publisherName | Nome do distribuidor do serviço, incluindo a Microsoft ou fornecedores de terceiros. | O nome do distribuidor do produto.|
| reservationId | Identificador da compra de instância reservada. | N/D |
| reservationName | Nome da instância reservada. | N/D |
| reservationOrderId | OrderID da instância reservada. | N/D |
| frequência | Frequência de pagamento para uma instância reservada. | N/D |
| resourceGroup | Nome do grupo de recursos do Azure usado para o gerenciamento de recursos do ciclo de vida. | Nome do grupo de recursos. |
| instanceID (ou) ResourceID | Identificador da instância de recurso. | Mostrado como um ResourceURI que inclui propriedades de recurso completas. |
| resourceLocation | Nome da localização do recurso. | A localização do recurso. |
| Location | Localização normalizada do recurso. | N/D |
| effectivePrice | O preço unitário efetivo do serviço, na moeda do preço. Exclusivo para um produto, uma família de serviços, um medidor e uma oferta. Usado com os preços na tabela de preços da conta de cobrança. Quando há preços em camadas ou uma quantidade incluída, ele mostra o preço combinado do consumo. | O preço unitário após os ajustes serem feitos. |
| Quantidade | Quantidade medida comprada ou consumida. O valor do medidor usado durante o período de cobrança. | Número de unidades. Verifique se ele corresponde às informações no sistema de cobrança durante a reconciliação. |
| unitOfMeasure | Identifica a unidade em que o serviço é cobrado. Por exemplo, GB e horas. | Identifica a unidade em que o serviço é cobrado. Por exemplo, GB, horas e 10.000s. |
| pricingCurrency | A moeda que define o preço unitário. | A moeda na lista de preços.|
| billingCurrency | A moeda que define o custo cobrado. | A moeda definida como a moeda cobrada na fatura. |
| chargeType | Define o tipo de preço que o custo representa no Gerenciamento de Custos do Azure como compra e reembolso. | O tipo de preço ou ajuste. Não disponível para a atividade atual. |
| costinBillingCurrency | ExtendedCost ou o custo combinado antes da dedução do imposto na moeda cobrada. | N/D |
| costinPricingCurrency | ExtendedCost ou o custo combinado antes do imposto na moeda do preço a ser correlacionado com os preços. | N/D |
| **costinUSD** | ExtendedCost estimado ou o custo combinado antes da dedução do imposto em USD. | N/D |
| **paygCostInBillingCurrency** | Mostra os custos se os preços estão em preços de varejo. Mostra os preços pagos conforme o uso na moeda de cobrança. Disponível somente em escopos do Azure RBAC. | N/D |
| **paygCostInUSD** | Mostra os custos se os preços estão em preços de varejo. Mostra os preços pagos conforme o uso em USD. Disponível somente em escopos do Azure RBAC. | N/D |
| exchangeRate | Taxa de câmbio usada para conversão da moeda do preço na moeda de cobrança. | Conhecido como PCToBCExchangeRate no Partner Center. A moeda do preço na taxa de câmbio da moeda de cobrança.|
| exchangeRateDate | A data da taxa de câmbio usada para conversão da moeda do preço na moeda de cobrança. | Conhecido como PCToBCExchangeRateDat no Partner Center. A moeda do preço na data da taxa de câmbio da moeda de cobrança.|
| isAzureCreditEligible | Indica se o custo é qualificado para pagamento com créditos Azure. | N/D |
| serviceInfo1 | Um campo herdado que captura os metadados específicos do serviço opcional. | Metadados interno de serviço do Azure. |
| serviceInfo2 | Um campo herdado que captura os metadados específicos do serviço opcional. | Informações do serviço. Por exemplo, um tipo de imagem para uma máquina virtual e o nome do ISP para o ExpressRoute.|
| additionalInfo | Metadados específicos ao serviço. Por exemplo, um tipo de imagem para uma máquina virtual. | Informações adicionais não abordadas em outras colunas. Os metadados específicos do serviço. Por exemplo, um tipo de imagem para uma máquina virtual.|
| marcas | Marca atribuída ao medidor. Use marcas para agrupar registros de cobrança. Por exemplo, você pode usar marcas para distribuir os custos entre os departamentos que usam o medidor. | Marcas adicionadas pelo cliente.|
| **partnerEarnedCreditRate** | Taxa de desconto aplicada se há um PEC (crédito ganho pelo parceiro) com base no acesso de link do administrador do parceiro. | A taxa do PEC (crédito ganho pelo parceiro). Por exemplo, 0% ou 15%. |
| **partnerEarnedCreditApplied** | Indica se o crédito ganho pelo parceiro foi aplicado. | N/D |

## <a name="view-partner-earned-credit-pec-resource-costs"></a>Exibir os custos de recursos do PEC (crédito ganho pelo parceiro)

No Gerenciamento de Custos do Azure, os parceiros podem usar a análise de custo para ver os custos que receberam os benefícios do PEC.

No portal do Azure, entre no locatário do parceiro e selecione **Gerenciamento de Custos + Cobrança**. Em **Gerenciamento de Custos**, selecione **Análise de custo**.

A exibição Análise de custo mostra os custos da conta de cobrança do parceiro. Selecione **Escopo**, conforme necessário para o parceiro, um cliente específico ou um perfil de cobrança para reconciliar faturas.

Em um gráfico de rosca, selecione a lista suspensa e selecione **PartnerEarnedCreditApplied** para analisar os custos do PEC.

![Exemplo mostrando como exibir o crédito ganho pelo parceiro](./media/get-started-partners/cost-analysis-pec1.png)

Quando a propriedade **PartnerEarnedCreditApplied** é _True_, o custo associado tem o benefício do acesso de administrador obtido pelo parceiro.

Quando a propriedade **PartnerEarnedCreditApplied** é _False_, isso indica que o custo associado não atendeu à qualificação necessária para o crédito. Ou o serviço comprado não está qualificado para o crédito ganho pelo parceiro.

Os dados de utilização do serviço normalmente levam de 8 a 24 horas para serem exibidos no Gerenciamento de Custos. Para obter mais informações, confira [Atualizações de dados de custo e de uso e retenção](understand-cost-mgt-data.md#cost-and-usage-data-updates-and-retention). Os créditos do PEC são exibidos em até 48 horas após a hora de acesso no Gerenciamento de Custos do Azure.


Você também pode fazer um agrupamento e aplicar um filtro pela propriedade **PartnerEarnedCreditApplied** usando as opções **Agrupar por**. Use as opções para examinar os custos que têm e não têm o PEC.

![Agrupar ou filtrar pelo crédito ganho pelo parceiro](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="export-cost-data-to-azure-storage"></a>Exportar os dados de custo para o Armazenamento do Azure

Os parceiros com acesso aos escopos de cobrança em um locatário do parceiro podem exportar os dados de custo e de uso para um Azure Storage Blob. O blob precisa estar em uma assinatura no locatário do parceiro que não seja uma [assinatura de serviço compartilhado](/partner-center/shared-services) nem uma assinatura do cliente. Para habilitar a exportação de dados de custo, recomendamos que você configure uma assinatura independente paga conforme o uso no locatário do parceiro para hospedar os dados de custo exportados. A conta de armazenamento de exportação é criada no Azure Storage Blob hospedado na assinatura paga conforme o uso. Com base no escopo em que o parceiro cria a exportação, os dados associados são exportados para a conta de armazenamento automaticamente de modo recorrente.

Os usuários com acesso do Azure RBAC à assinatura também podem exportar os dados de custo para um Azure Storage Blob hospedado em qualquer assinatura no locatário do cliente.

### <a name="create-an-export-in-a-partner-tenant-or-customer-tenant"></a>Criar uma exportação em um locatário do parceiro ou um locatário do cliente

No portal do Azure, entre no locatário do parceiro ou no locatário do cliente e selecione **Gerenciamento de Custos + Cobrança**. Selecione um escopo apropriado, por exemplo, uma conta de cobrança do Contrato de Parceiro da Microsoft e, em seguida, selecione **Análise de Custo**. Quando a página for carregada, selecione **Exportar**. Selecione **Exibir todas as exportações** em Agendar Exportação.

![Selecionar Exportar e Exibir todas as exportações](./media/get-started-partners/export01.png)

Em seguida, selecione **Adicionar**, digite o nome e selecione um tipo de exportação. Selecione a guia **Armazenamento** e insira as informações necessárias.

![Adicionar uma nova exportação e selecionar a guia Armazenamento](./media/get-started-partners/export02.png)

Ao criar uma exportação no locatário do parceiro, selecione a assinatura paga conforme o uso no locatário do parceiro. Crie uma conta de Armazenamento do Azure usando essa assinatura.

Para os usuários do Azure RBAC no locatário do cliente, selecione uma assinatura no locatário do cliente. Crie uma conta de Armazenamento do Azure usando a assinatura.

Examine o conteúdo e, em seguida, selecione **Criar** para agendar uma exportação.

Para verificar os dados na lista de exportação, selecione o nome da conta de armazenamento. Na página da conta de armazenamento, selecione **Contêineres** e, em seguida, o contêiner. Navegue até a pasta correspondente e selecione o arquivo CSV. Selecione **Baixar** para obter o arquivo CSV e abra-o. Os dados exportados se assemelham aos dados de custo, de modo semelhante aos detalhes de uso no portal do Azure.

![Exemplo de dados exportados](./media/get-started-partners/example-export-data.png)

## <a name="cost-management-rest-apis"></a>APIs REST do Gerenciamento de Custos

Os parceiros e os clientes podem usar as APIs do Gerenciamento de Custos descritas nas seções a seguir para tarefas comuns.

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>APIs do Gerenciamento de Custos do Azure: provedores diretos e indiretos

Os parceiros com acesso aos escopos de cobrança em um locatário do parceiro podem usar as APIs a seguir para ver os custos faturados.

As APIs no escopo da assinatura podem ser chamadas por um parceiro, seja qual for a política de custo, caso tenham acesso à assinatura. Outros usuários com acesso à assinatura, como o cliente ou o revendedor, poderão chamar as APIs somente depois que o parceiro habilitar a política de custo para o locatário do cliente.


#### <a name="to-get-a-list-of-billing-accounts"></a>Para obter uma lista de contas de cobrança

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-customers"></a>Para obter uma lista de clientes

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-subscriptions"></a>Para obter uma lista de assinaturas

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-invoices-for-a-period-of-time"></a>Para obter uma lista de faturas por um período

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/invoices?api-version=2019-10-01-preview&periodStartDate={periodStartDate}&periodEndDate={periodEndDate}
```

A chamada à API retorna uma matriz de faturas que tem elementos semelhantes ao código JSON a seguir.

```
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/{billingAccountID}/billingProfiles/{BillingProfileID}/invoices/{InvoiceID}",
      "name": "{InvoiceID}",
      "properties": {
        "amountDue": {
          "currency": "USD",
          "value": x.xx
        },
        ...
    }
```

Use o valor do campo ID retornado anteriormente e substitua-o no exemplo a seguir como o escopo para consulta dos detalhes de uso.

```
GET https://management.azure.com/{id}/providers/Microsoft.Consumption/UsageDetails?api-version=2019-10-01
```

O exemplo retorna os registros de uso associados à fatura específica.


#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>Para obter a política para os clientes verem os custos

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>Para definir a política para os clientes verem os custos

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>Para obter a utilização do serviço do Azure para uma conta de cobrança

```
GET https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>Para baixar a utilização do serviço do Azure de um cliente

A chamada GET a seguir é uma operação assíncrona.

```
GET https://management.azure.com/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

Chame o URI de `Location` retornado na resposta para verificar o status da operação. Quando o status for *Concluído*, a propriedade `downloadUrl` conterá um link que poderá ser usado para baixar o relatório gerado.


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>Para obter ou baixar a lista de preços dos serviços do Azure consumidos

Primeiro, use o POST a seguir.

```
POST https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

Em seguida, chame o valor da propriedade de operação assíncrona. Por exemplo:

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheetDownloadOperations/{operation}?sessiontoken=0:11186&api-version=2019-10-01-preview
```
A chamada GET anterior retorna o link de download que contém a lista de preços.


#### <a name="to-get-aggregated-costs"></a>Para obter custos agregados

```
POST https://management.azure.com/providers/microsoft.billing/billingAccounts/{billingAccountName}/providers/microsoft.costmanagement/query?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-partner"></a>Criar um orçamento para um parceiro

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-customer"></a>Criar um orçamento para um cliente

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2019-10-01
```

#### <a name="delete-a-budget"></a>Excluir um orçamento

```
DELETE
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/budgets/{budgetName}?api-version=2019-10-01
```


## <a name="next-steps"></a>Próximas etapas
- [Começar a analisar os custos](quick-acm-cost-analysis.md) no Gerenciamento de Custos
- [Criar e gerenciar orçamentos](tutorial-acm-create-budgets.md) no Gerenciamento de Custos
