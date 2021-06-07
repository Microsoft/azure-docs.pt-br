---
title: incluir arquivo
description: incluir arquivo
services: azure-sentinel
author: yelevin
ms.service: azure-sentinel
ms.topic: include
ms.date: 06/28/2020
ms.author: yelevin
ms.custom: include file
ms.openlocfilehash: 63cb53dc60a718892d4bf86140e7fd51303bd61c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88761705"
---
### <a name="the-data-model-of-the-schema"></a>O modelo de dados do esquema

| Campo | Tipo de dados | Descrição |
| ---- | ---- | ---- |
| **AdditionalData** | dinâmico | Alertas contagem, contagem de indicadores, contagem de comentários, nomes de produtos de alerta e táticas |
| **AlertIds** | dinâmico | Alertas dos quais o incidente foi criado |
| **BookmarkIds** | dinâmico | Entidades com indicadores |
| **Classificação** | string | Classificação de fechamento de incidentes |
| **ClassificationComment** | string | Comentário de classificação de fechamento de incidente |
| **ClassificationReason** | string | Motivo da classificação de fechamento de incidentes |
| **Closedtime** | DATETIME | Timestamp (UTC) de quando o incidente foi fechado pela última vez |
| **Comentários** | dinâmico | Comentários do incidente |
| **CreatedTime** | DATETIME | Timestamp (UTC) de quando o incidente foi criado |
| **Descrição** | string | Descrição do incidente |
| **FirstActivityTime** | DATETIME | Hora do primeiro evento |
| **FirstModifiedTime** | DATETIME | Timestamp (UTC) de quando o incidente foi modificado pela primeira vez |
| **IncidentName** | string | GUID interna |
| **IncidentNumber** | INT |  |
| **IncidentUrl** | string | Link para o incidente |
| **Rótulos** | dinâmico | Marcas |
| **LastActivityTime** | DATETIME | Hora do último evento |
| **LastModifiedTime** | DATETIME | Timestamp (UTC) de quando o incidente foi modificado pela última vez <br>(a modificação descrita pelo registro atual) |
| **ModifiedBy** | string | Usuário ou sistema que modificou o incidente |
| **Proprietário** | dinâmico |  |
| **RelatedAnalyticRuleIds** | dinâmico | Regras das quais os alertas do incidente foram disparados |
| **Gravidade** | string | Severidade do incidente (alta/média/baixa/informativa) |
| **SourceSystem** | string | Constante (' Azure ') |
| **Status** | string |  |
| **TenantId** | string |  |
| **TimeGenerated** | DATETIME | Timestamp (UTC) de quando o registro atual foi criado <br>(após a modificação do incidente) |
| **Título** | string | 
| **Tipo** | string | Constante (' SecurityIncident ') |
|
