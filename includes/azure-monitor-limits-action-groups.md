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
ms.openlocfilehash: 963e49ea0e5536be0fca6d565b439aef4a08793d
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98605200"
---
| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| Push do aplicativo Azure | Dez ações de aplicativo do Azure por grupo de ações. | Igual ao padrão |
| Email | Mil ações de email em um grupo de ações.<br>não mais de 100 emails em uma hora.<br>Confira também as [informações de limitação da taxa](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Igual ao padrão |
| ITSM | Dez ações do ITSM em um grupo de ações. | Igual ao padrão | 
| Aplicativo lógico | Dez ações do aplicativo lógico em um grupo de ações. | Igual ao padrão |
| Runbook | Dez ações do runbook em um grupo de ações. | Igual ao padrão |
| SMS | Dez ações de SMS em um grupo de ações.<br>Não mais de uma mensagem SMS a cada cinco minutos.<br>Confira também as [informações de limitação da taxa](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Igual ao padrão |
| Voz | Dez ações de voz em um grupo de ações.<br>Não mais de uma chamada de voz a cada cinco minutos.<br>Confira também as [informações de limitação da taxa](../articles/azure-monitor/platform/alerts-rate-limiting.md). | Igual ao padrão |
| webhook | Dez ações do webhook em um grupo de ações.  O número máximo de chamadas de webhook é 1.500 por minuto por assinatura. Outros limites estão disponíveis em [informações específicas da ação](../articles/azure-monitor/platform/action-groups.md#action-specific-information).  | Igual ao padrão |
