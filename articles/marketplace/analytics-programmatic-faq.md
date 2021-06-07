---
title: Acesso programático a perguntas comuns de dados de análise
description: Perguntas frequentes sobre o acesso programático a dados de análise no Partner Center para suas ofertas de mercado comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 393a718632138f4ffcf26e4875eea9ba3d886897
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583671"
---
# <a name="programmatic-access-of-analytics-data-common-questions"></a>Acesso programático a perguntas comuns de dados de análise

Este artigo aborda as perguntas mais frequentes sobre como acessar programaticamente dados de análise no Partner Center para suas ofertas de mercado comercial.

## <a name="api-responses"></a>Respostas de API

Quais são os diferentes cenários sob os quais posso receber uma resposta de API diferente de 200 (êxito)?

Esta tabela descreve as respostas da API e o que fazer se você as receber.

| Descrição do erro | Código do erro | Solucionar problemas |
| ------------ | ------------- | ------------- |
| Não Autorizado | 401 | Esta é uma exceção de autenticação. Verifique a exatidão do token do Azure Active Directory (AD do Azure). O token do Azure AD é válido por 60 minutos, após o qual você precisará regenerar o token do Azure AD. |
| Nome de tabela inválido | 400 | O nome do conjunto de uma está errado. Verifique novamente o nome do conjunto de valores chamando a API "obter todos os conjuntos de banco de todos". |
| Nome de coluna incorreto | 400| O nome da coluna na consulta está incorreto. Verifique novamente o nome da coluna chamando a API "obter todos os conjuntos de conjunto de os" ou consulte os nomes das colunas nas tabelas a seguir:<br><ul><li>[Tabela de detalhes de pedidos](orders-dashboard.md#orders-details-table)</li><li>[Tabela de detalhes de uso](usage-dashboard.md#usage-details-table)</li><li>[Tabela de detalhes do cliente](customer-dashboard.md#customer-details-table)</li><li>[Tabela de detalhes do Marketplace insights](insights-dashboard.md#marketplace-insights-details-table)</li></UL> |
| Valor nulo ou ausente | 400 | Você pode estar faltando parâmetros obrigatórios como parte da carga de solicitação da API. |
| Parâmetros de relatório inválidos | 400 | Verifique se os parâmetros do relatório estão corretos. Por exemplo, você pode estar fornecendo um valor inferior a 4 para o `RecurrenceInterval` parâmetro. |
| O intervalo de recorrência deve estar entre 4 e 90 | 400 | Verifique se o valor do `RecurrenceInterval` parâmetro de solicitação está entre 4 e 90. |
| Consultaid inválida | 400 | Verifique novamente o gerado `QueryId` . |
| Parâmetros de relatório inválidos para criação-a hora de início do relatório deve ter pelo menos 4 horas a partir da hora UTC atual- | 400 | O parâmetro de hora de início como parte da carga de solicitação não deve estar no passado. A hora de início do relatório deve ser pelo menos 4 horas a partir da hora UTC atual. |
| O valor solicitado ' String ' não foi encontrado | 400 | Verifique se você atualizou os parâmetros de solicitação `callbackurl` ou `format` . |
| Nenhum item encontrado com os filtros fornecidos. | 404 | Verifique o `reportID` parâmetro usado na API obter execuções de relatório. |
| Não há execuções que ocorreram para as condições de filtro fornecidas. Verifique novamente a ReportID ou ExecutionID e repita a API após o tempo de execução agendado do relatório | 404 | Verifique se o `reportId` está correto. Repita a API após o tempo de execução agendado do relatório, conforme especificado na carga de solicitação. |
| Encontrado um erro interno ao criar o relatório. ID de correlação <> | 500 | Verifique se o formato da data dos campos "StartTime", "QueryStartTime" e "QueryEndTime" estão corretos. |
| Serviço indisponível | 500 | Se você receber continuamente um serviço indisponível (erro 5xx), crie um [tíquete de suporte](support.md). |
||||

## <a name="no-records"></a>Nenhum registro

Eu recebi a resposta da API 200 quando faço o download do relatório do local seguro. Por que eu não recebo nenhum registro?

Verifique se a cadeia de caracteres na consulta tem um dos valores permitidos para o cabeçalho da coluna. Por exemplo, essa consulta retornará zero resultados:

`"SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paided' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"`

Neste exemplo, os valores permitidos para SKUBillingType são pagos ou gratuitos. Consulte as tabelas a seguir para todos os valores possíveis para as várias colunas:

- [Tabela de detalhes de pedidos](orders-dashboard.md#orders-details-table)
- [Tabela de detalhes de uso](usage-dashboard.md#usage-details-table)
- [Tabela de detalhes do cliente](customer-dashboard.md#customer-details-table)
- [Tabela de detalhes do Marketplace insights](insights-dashboard.md#marketplace-insights-details-table)

## <a name="next-steps"></a>Próximas etapas

- [APIs para acessar dados de análise do Marketplace comercial](analytics-available-apis.md)
