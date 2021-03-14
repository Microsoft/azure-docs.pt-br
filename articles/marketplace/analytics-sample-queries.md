---
title: Exemplos de consultas de análise programática
description: Use essas consultas de exemplo para acessar de forma programática os dados do Microsoft Commercial Marketplace Analytics.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 7d788448fb3f8a849f79e43fcb0737898f4c9e15
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583679"
---
# <a name="sample-queries-for-programmatic-analytics"></a>Exemplos de consultas de análise programática

Este artigo fornece exemplos de consultas para os relatórios de pedidos, uso e clientes do Microsoft Commercial Marketplace. Você pode usar essas consultas chamando o ponto de extremidade da API de [consulta criar relatório](analytics-programmatic-access.md#create-report-query-api) . Se necessário, a chamada de API de [consulta criar relatório](analytics-programmatic-access.md#create-report-query-api) pode ser modificada para adicionar mais colunas, ajustar o período de computação e adicionar condições de filtro. Os períodos de tempo com suporte são de seis meses (6 minutos), 12 meses (12M) e período de tempo personalizado.

Para obter detalhes sobre os nomes de coluna, atributos e descrições, consulte as seguintes tabelas:

- [Tabela de detalhes do cliente](customer-dashboard.md#customer-details-table)
- [Tabela de detalhes de pedidos](orders-dashboard.md#orders-details-table)
- [Tabela de detalhes de uso](usage-dashboard.md#usage-details-table)

## <a name="customers-report-queries"></a>Consultas de relatório de clientes

Essas consultas de exemplo se aplicam ao relatório de clientes.

| **Descrição da consulta** | **Consulta de exemplo** |
| --- | --- |
| Clientes ativos do parceiro até a data que você escolher | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 1` |
| Clientes com rotatividade do parceiro até a data que você escolher | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 0` |
| Lista de novos clientes de uma geografia específica nos últimos seis meses | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE DateAcquired <= ‘2020-06-30’ AND CustomerCountryRegion = ‘United States’` |
|||

## <a name="usage-report-queries"></a>Consultas de relatório de uso

Essas consultas de exemplo se aplicam ao relatório de uso.

| **Descrição da consulta** | **Consulta de exemplo** |
| --- | --- |
| Uso normalizado da VM (máquina virtual) para o tipo de licença do Marketplace "cobrado por meio do Azure" para o último 6 minutos | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| Uso bruto de VM para o tipo de licença do Marketplace "cobrado por meio do Azure" para o último 12M | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_1_YEAR` |
| Uso normalizado da VM para o tipo de licença do Marketplace "Traga sua própria licença" para o último 6 minutos | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| Uso bruto da VM para o tipo de licença do Marketplace "Traga sua própria licença" para o último 6 minutos | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| Com base na data de uso, no uso normalizado total diário e em "encargos estendidos estimados (PC/CC)" para os planos pagos do último mês | `SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST_MONTH` |
| Com base na data de uso, uso bruto total diário e "encargos estendidos estimados (PC/CC)" para os planos pagos do último mês | `SELECT UsageDate, RawUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST\_MONTH` |
| Para um nome de oferta específico, o uso normalizado da VM para o tipo de licença do Marketplace "cobrado por meio do Azure" para o último 6 minutos | `SELECT OfferName, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| Para um nome de oferta específico, uso medido para o último 6 minutos | `SELECT OfferName, MeteredUsage FROM ISVUsage WHERE OfferName = ‘Example Offer Name’ AND OfferType IN (‘SaaS’, ‘Azure Applications’) TIMESPAN LAST_6_MONTHS` |
|||

## <a name="orders-report-queries"></a>Consultas de relatório de pedidos

Essas consultas de exemplo se aplicam ao relatório de pedidos.

| **Descrição da consulta** | **Consulta de exemplo** |
| --- | --- |
| Relatório de pedidos para o tipo de licença do Azure como "Enterprise" para o último 6 minutos | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Enterprise’ TIMESPAN LAST\_6\_MONTHS` |
| Relatório de pedidos para o tipo de licença do Azure como "pré-pago" para o último 6 minutos | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Pay as You Go’ TIMESPAN LAST_6_MONTHS` |
| Relatório de pedidos para o nome da oferta específica para o último 6 minutos | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| Relatório de pedidos para pedidos ativos para o último 6 minutos | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Active’ TIMESPAN LAST_6_MONTHS` |
| Relatório de pedidos para pedidos cancelados para o último 6 minutos | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Cancelled’ TIMESPAN LAST_6_MONTHS` |
|||

## <a name="next-steps"></a>Próximas etapas

- [APIs para acessar dados de análise do Marketplace comercial](analytics-available-apis.md)
