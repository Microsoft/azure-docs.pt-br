---
title: "Analisar padrões de uso da CDN do Azure | Microsoft Docs"
description: "O cliente pode habilitar a análise de log para a CDN do Azure."
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: v-semcev
ms.openlocfilehash: af396e9f8847421d529c32956216cfc47294edb2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analisar os padrões de uso da CDN do Azure

Após habilitar a CDN para seu aplicativo, será possível monitorar o uso da CDN, verificar a integridade da sua entrega e resolver eventuais problemas. A CDN do Azure fornece esses recursos de duas maneiras: 

## <a name="verizon-core-reports"></a>Relatórios de núcleo da Verizon

Como usuário da CDN do Azure com um perfil padrão ou premium da Verizon, é possível exibir os Relatórios de núcleo da Verizon no portal complementar Verizon. O portal complementar Verizon oferece uma variedade de grafos e modos de exibição e pode ser acessado por meio da opção **Gerenciar** do Portal do Azure. Para obter mais informações, consulte [Relatórios de núcleo da Verizon](cdn-analyze-usage-patterns.md).

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Análise de núcleo por meio de logs de diagnósticos do Azure

A análise de núcleo está disponível para todos os pontos de extremidade da CDN que pertencem a perfis CDN Verizon (Standard e Premium) e Akamai (Standard). Os logs de diagnóstico do Azure permitem que a análise de núcleo seja exportada para o armazenamento do Azure, para os hubs de eventos ou para o OMS (Operations Management Suite) Log Analytics. O OMS Log Analytics oferece uma solução com grafos configuráveis pelo usuário e personalizáveis. Para obter mais informações, consulte [Logs de diagnóstico do Azure](cdn-azure-diagnostic-logs.md).

