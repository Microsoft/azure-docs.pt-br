---
title: incluir arquivo
description: incluir arquivo
services: search
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/11/2020
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: fe5c8129434ddb4eec2dd25a3f123f28b4db221b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "83682626"
---
Um serviço de pesquisa é restrito pelo espaço em disco ou por um limite rígido do número máximo de índices ou indexadores, o que ocorrer primeiro. A tabela a seguir documenta os limites de armazenamento. Para obter os limites máximos de objeto, confira [Limites por recurso](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Recurso | Grátis | Básico<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| SLA (Contrato de Nível de Serviço)<sup>2</sup>  |Não |Sim |Sim |Sim |Sim |Sim |Sim |Sim |
| Armazenamento por partição |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Partições por serviço |N/D |1 |12 |12 |12 |3 |12 |12 |
| Tamanho da partição |N/D |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Réplicas |N/D |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> Básico tem uma partição fixa. Unidades de pesquisa adicionais podem ser usadas a fim de adicionar réplicas para volumes de consulta maiores.

<sup>2</sup> Os contratos de nível de serviço estão em vigor para serviços faturáveis em recursos dedicados. Serviços gratuitos e recursos de visualização não têm SLA. Para serviços faturáveis, os SLAs entram em vigor quando você fornece redundância suficiente para seu serviço. Duas ou mais réplicas são necessárias para os SLAs de consulta (leitura). Três ou mais réplicas são necessárias para consulta e indexação dos SLAs (leitura-gravação). O número de partições não é uma consideração de SLA. 