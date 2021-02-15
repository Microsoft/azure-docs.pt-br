---
title: arquivo de inclusão
description: arquivo de inclusão
services: azure-sentinel
author: yelevin
ms.service: azure-sentinel
ms.topic: include
ms.date: 06/28/2020
ms.author: yelevin
ms.custom: include file
ms.openlocfilehash: 63cb53dc60a718892d4bf86140e7fd51303bd61c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88761705"
---
### <a name="the-data-model-of-the-schema"></a>O modelo de dados do esquema

| Campo | Tipo de dados | Descrição |
| ---- | ---- | ---- |
| **AdditionalData** | dinâmico | Alertas contagem, contagem de indicadores, contagem de comentários, nomes de produtos de alerta e táticas |
| **AlertIds** | dinâmico | Alertas dos quais o incidente foi criado |
| **BookmarkIds** | dinâmico | Entidades com indicadores |
| **Classificação** | cadeia de caracteres | Classificação de fechamento de incidentes |
| **ClassificationComment** | cadeia de caracteres | Comentário de classificação de fechamento de incidente |
| **ClassificationReason** | cadeia de caracteres | Motivo da classificação de fechamento de incidentes |
| **Closedtime** | DATETIME | Timestamp (UTC) de quando o incidente foi fechado pela última vez |
| **Comentários** | dinâmico | Comentários do incidente |
| **CreatedTime** | DATETIME | Timestamp (UTC) de quando o incidente foi criado |
| **Descrição** | cadeia de caracteres | Descrição do incidente |
| **FirstActivityTime** | DATETIME | Hora do primeiro evento |
| **FirstModifiedTime** | DATETIME | Timestamp (UTC) de quando o incidente foi modificado pela primeira vez |
| **IncidentName** | cadeia de caracteres | GUID interna |
| **IncidentNumber** | INT |  |
| **IncidentUrl** | cadeia de caracteres | Link para o incidente |
| **Rótulos** | dinâmico | Marcas |
| **LastActivityTime** | DATETIME | Hora do último evento |
| **LastModifiedTime** | DATETIME | Timestamp (UTC) de quando o incidente foi modificado pela última vez <br>(a modificação descrita pelo registro atual) |
| **ModifiedBy** | cadeia de caracteres | Usuário ou sistema que modificou o incidente |
| **Proprietário** | dinâmico |  |
| **RelatedAnalyticRuleIds** | dinâmico | Regras das quais os alertas do incidente foram disparados |
| **Gravidade** | cadeia de caracteres | Severidade do incidente (alta/média/baixa/informativa) |
| **SourceSystem** | cadeia de caracteres | Constante (' Azure ') |
| **Status** | cadeia de caracteres |  |
| **TenantId** | cadeia de caracteres |  |
| **TimeGenerated** | DATETIME | Timestamp (UTC) de quando o registro atual foi criado <br>(após a modificação do incidente) |
| **Título** | cadeia de caracteres | 
| **Tipo** | cadeia de caracteres | Constante (' SecurityIncident ') |
|
