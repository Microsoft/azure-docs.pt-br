---
title: incluir arquivo
description: incluir arquivo
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 02/17/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2f52e8a89ec9dd78a1951836053cb2c698310bbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100645311"
---
Os limites a seguir aplicam-se aos **tópicos** da Grade de Eventos do Azure (tópicos sistema, personalizado e parceiro). 

| Recurso | Limite |
| --- | --- |
| Tópicos personalizados por assinatura do Microsoft Azure | 100 |
| Assinaturas de Evento por tópico | 500 |
| Publicar a taxa de um tópico personalizado ou parceiro (entrada) | 5\.000 eventos/s ou 5 MB/s (o que for atingido primeiro) |
| Tamanho do evento | 1 MB  |
| Conexões do ponto de extremidade privado por tópico  | 64 | 
| Regras de firewall de IP por tópico | 16 | 

Os limites a seguir aplicam-se aos **domínios** da Grade de Eventos do Azure. 

| Recurso | Limite |
| --- | --- |
| Tópicos por domínio de eventos | 100.000 |
| Assinaturas de eventos por tópico em um domínio | 500 |
| Assinaturas de eventos no escopo do domínio | 50 |
| Publicar taxa para um domínio de eventos (entrada) | 5\.000 eventos/s ou 5 MB/s (o que for atingido primeiro) |
| Domínios de Eventos por Assinatura do Azure | 100 |
| Conexões do ponto de extremidade privado por domínio | 64 | 
| Regras de firewall de IP por domínio | 16 | 


