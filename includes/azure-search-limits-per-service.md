---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: b298c87d802314bd865bd6f38c35e4361eb69f3f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172302"
---
O armazenamento é restrito por espaço em disco ou por um limite rígido no *número máximo* de índices, documentos ou outros recursos de alto nível, o que ocorrer primeiro. A tabela a seguir documenta os limites de armazenamento. Para obter limites máximos em índices, documentos e outros objetos, consulte [limites por recurso](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Resource | Grátis | Básico<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Serviço (SLA) contrato de nível<sup>3</sup>  |Não |sim |sim |sim |sim |sim |sim |Sim |
| Armazenamento por partição |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Partições por serviço |N/D |1 |12 |12 |12 |3 |12 |12 |
| Tamanho da partição |N/D |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Réplicas |N/D |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> Básico tem uma partição fixa. Nesse nível, as unidades de pesquisa adicional são usadas para alocar mais réplicas para cargas de trabalho de consulta maior.

<sup>2</sup> S3 HD tem um limite rígido de três partições, que é menor do que o limite de partição do S3. O limite de partição inferior é aplicado porque a contagem de índice de S3 HD é consideravelmente maior. Uma vez que há limites de serviço para os recursos de computação (processamento e armazenamento) e de conteúdo (índices e documentos), o limite de conteúdo é atingido primeiro.

<sup>3</sup> contratos de nível de serviço são oferecidos para serviços faturáveis em recursos dedicados. Serviços gratuitos e recursos de visualização não têm SLA. Para serviços faturáveis, os SLAs entram em vigor quando você fornece redundância suficiente para seu serviço. Duas ou mais réplicas são necessárias para SLAs de consulta (leitura). Três ou mais réplicas são necessárias para consulta e indexação SLAs (leitura-gravação). O número de partições não é uma consideração de SLA. 