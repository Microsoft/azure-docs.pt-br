---
title: Lista de consultas do sistema
description: Saiba mais sobre as consultas do sistema que você pode usar para obter dados de análise programaticamente sobre suas ofertas no Microsoft Commercial Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: f2b5f7eb559e349947f88067a3d2ea53d99b7cbf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583678"
---
# <a name="list-of-system-queries"></a>Lista de consultas do sistema

As consultas do sistema a seguir podem ser usadas na [API criar relatório](analytics-programmatic-access.md#create-report-api) diretamente com um `QueryId` . As consultas do sistema são como os relatórios de exportação no Partner Center por um período de computação de seis meses (6 minutos).

Para obter mais informações sobre os nomes de coluna, atributos e descrição, consulte estes artigos:

- [Painel de clientes na análise de Marketplace comercial](customer-dashboard.md#customer-details-table)
- [Painel de pedidos na análise do marketplace comercial](orders-dashboard.md#orders-details-table)
- [Painel de uso na análise do Marketplace comercial](usage-dashboard.md#usage-details-table)
- [Painel de Insights do Marketplace na análise do marketplace comercial](insights-dashboard.md#marketplace-insights-details-table)

As seções a seguir fornecem consultas de relatório para vários relatórios.

## <a name="customers-report-query"></a>Consulta de relatório de clientes

**Descrição do relatório**: relatório de clientes para o último 6 minutos

**QueryId**:  `b9df4929-073f-4795-b0cb-a2c81b11e28d`

**Consulta de relatório**:

`SELECT MarketplaceSubscriptionId,DateAcquired,DateLost,ProviderName,ProviderEmail,FirstName,LastName,Email,CustomerCompanyName,CustomerCity,CustomerPostalCode,CustomerCommunicationCulture,CustomerCountryRegion,AzureLicenseType,PromotionalCustomers,CustomerState,CommerceRootCustomer,CustomerId,BillingAccountId,ID  FROM ISVCustomer TIMESPAN LAST_6_MONTHS`

## <a name="orders-report-query"></a>Consulta de relatório de pedidos

**Descrição do relatório**: relatório de pedidos para o último 6 minutos

**QueryId**:  `fd0f299c-5a1c-4929-9f48-bfc6cc44355d`

**Consulta de relatório**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,Sku,CustomerCountry,IsPreviewSKU,OrderId,OrderQuantity,CloudInstanceName,IsNewCustomer,OrderStatus,OrderCancelDate,CustomerCompanyName,CustomerName,OrderPurchaseDate,OfferName,TrialEndDate,CustomerId,BillingAccountId FROM ISVOrder TIMESPAN LAST_6_MONTHS`

## <a name="usage-report-queries"></a>Consultas de relatório de uso

**Descrição do relatório**: relatório de uso de VM normalizado para o último 6 minutos

**QueryId**:  `2c6f384b-ad52-4aed-965f-32bfa09b3778`

**Consulta de relatório**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,NormalizedUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

**Descrição do relatório**: relatório de uso bruto de VM para o último 6 minutos

**QueryId**:  `3f19fb95-5bc4-4ee0-872e-cedd22578512`

**Consulta de relatório**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,RawUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

**Descrição do relatório**: relatório de uso medido para o último 6 minutos

**QueryId**:  `f0c4927f-1f23-4c99-be4a-1371a5a9a086`

**Consulta de relatório**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,MeteredUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('SaaS', 'Azure Applications') TIMESPAN LAST_6_MONTHS`

## <a name="marketplace-insights-report-query"></a>Consulta de relatório do Marketplace insights

**Descrição do relatório**: relatório do Marketplace insights para o último 6 minutos

**QueryId**:  `6fd7624b-aa9f-42df-a61d-67d42fd00e92`

**Consulta de relatório**:

`Date,OfferName,ReferralDomain,CountryName,PageVisits,GetItNow,ContactMe,TestDrive,FreeTrial FROM ISVMarketplaceInsights TIMESPAN LAST_6_MONTHS`

## <a name="next-steps"></a>Próximas etapas

- [APIs para acessar dados de análise do Marketplace comercial](analytics-available-apis.md)
- [Aplicativo de exemplo para acessar dados de análise do Marketplace comercial](analytics-sample-application.md)
