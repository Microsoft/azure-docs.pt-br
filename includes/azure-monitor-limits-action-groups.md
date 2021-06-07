---
title: incluir arquivo
description: incluir arquivo
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 10/01/2020
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 6f9405e1b402b029b628821824a1727dd825a031
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101734002"
---
| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| Push do aplicativo Azure | Dez ações de aplicativo do Azure por grupo de ações. | Igual ao padrão |
| Email | Mil ações de email em um grupo de ações.<br>não mais de 100 emails em uma hora.<br>Confira também as [informações de limitação da taxa](../articles/azure-monitor/alerts/alerts-rate-limiting.md). | Igual ao padrão |
| ITSM | Dez ações do ITSM em um grupo de ações. | Igual ao padrão | 
| Aplicativo lógico | Dez ações do aplicativo lógico em um grupo de ações. | Igual ao padrão |
| Runbook | Dez ações do runbook em um grupo de ações. | Igual ao padrão |
| SMS | Dez ações de SMS em um grupo de ações.<br>Não mais de uma mensagem SMS a cada cinco minutos.<br>Confira também as [informações de limitação da taxa](../articles/azure-monitor/alerts/alerts-rate-limiting.md). | Igual ao padrão |
| Voz | Dez ações de voz em um grupo de ações.<br>Não mais de uma chamada de voz a cada cinco minutos.<br>Confira também as [informações de limitação da taxa](../articles/azure-monitor/alerts/alerts-rate-limiting.md). | Igual ao padrão |
| webhook | Dez ações do webhook em um grupo de ações.  O número máximo de chamadas de webhook é 1.500 por minuto por assinatura. Outros limites estão disponíveis em [informações específicas da ação](../articles/azure-monitor/alerts/action-groups.md#action-specific-information).  | Igual ao padrão |