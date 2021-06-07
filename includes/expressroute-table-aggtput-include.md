---
title: incluir arquivo
description: incluir arquivo
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 12/05/2019
ms.author: duau
ms.custom: include file
ms.openlocfilehash: b1465b9bf855d592d195277b616c860a23c179f6
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504662"
---
| SKU de gateway | Conexões por segundo | Número de fluxos | Megabits por segundo | Pacotes por segundo | Largura de banda do circuito | Número de rotas anunciadas pelo gateway (para MSEE) | Número de rotas aprendidas pelo gateway (do MSEE) | Número de VMs na rede virtual |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **SKU Básica (preterida)** | N/D | N/D | 500 | N/D | N/D | N/D | N/D | N/D |
| **SKU Standard/ErGw1AZ** | 7.000 | 400.000 | > 1.000 | > 100.000 | 1 Gbps |  500 | 4.000 | 2\.000 (reduzir para 1.000 durante a manutenção e restaurar posteriormente). | 
| **SKU de Alto Desempenho/ErGw2AZ** | 14.000 | 840.000 | > 2.000 | 250.000 | 1 Gbps | 500 | ~9.500 (reduzir para 4.000 se mais de 6.500 VMs estiverem na rede virtual). | 4\.500 |
| **SKU de Ultra Desempenho/ErGw3AZ** | 16.000 | 950.000 | ~10.000 | 1\.000.000 | 1 Gbps | 500 | ~9.500 | 11.000 |
