---
title: incluir arquivo
description: incluir arquivo
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: include
ms.date: 01/08/2021
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: 698219a446240950b9a661f923109fbb2f863556
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106178617"
---
Caso utilize o Armazenamento de Tabelas do Azure neste momento, você receberá os seguintes benefícios ao mudar para a API de Tabela do Azure Cosmos DB:

|Recurso | Armazenamento da tabela do Azure | API de Tabela do Azure Cosmos DB |
| --- | --- | --- |
| Latency | Rápido, mas não há limites superiores de latência. | Latência de milissegundo de dígito único para leituras e gravações, com suporte de leituras de latência de <10 ms e gravações de latência de <15 ms no 99º percentil, em qualquer escala, em qualquer lugar do mundo. |
| Produtividade | Modelo de taxa de transferência variável. As tabelas têm um limite de escalabilidade de 20.000 operações/s. | Altamente escalonável com [taxa de transferência reservada dedicada por tabela](../articles/cosmos-db/request-units.md), que é respaldada por SLAs. As contas não têm nenhum limite superior na taxa de transferência e dão suporte a mais de 10 milhões de operações/s por tabela (no modo de taxa de transferência provisionada). |
| Distribuição global | Região única com uma região de leitura de réplica secundária para leitura opcional para alta disponibilidade, que dá suporte a failover automático e manual de conta. | [Distribuição global turnkey](../articles/cosmos-db/distribute-data-globally.md) de 1 a 30 ou mais regiões. Suporte para [failovers automáticos e manuais](../articles/cosmos-db/high-availability.md) a qualquer momento, em qualquer lugar no mundo. |
| Indexação | Somente índice primário em PartitionKey e RowKey. Nenhum índice secundário. | Indexação automática e completa em todas as propriedades, sem gerenciamento de índice. |
| Consulta | A execução de consulta usa o índice para chave primária. Caso contrário, realiza a verificação. | As consultas podem aproveitar a indexação automática em propriedades para tempos rápidos de consulta. |
| Consistência | Forte na região primária. Eventual na região secundária. | [Cinco níveis de consistência bem definidos](../articles/cosmos-db/consistency-levels.md) para compensar a disponibilidade, latência, taxa de transferência e consistência com base nas necessidades do seu aplicativo. |
| Preços | Baseado em consumo. | Disponível nos modos [baseado em consumo](../articles/cosmos-db/serverless.md) e [capacidade provisionada](../articles/cosmos-db/set-throughput.md). |
| SLAs | disponibilidade de 99,99%. | 99,99% para todas as contas de região única e todas as contas de várias regiões com consistência amena e 99,999% de disponibilidade de leitura em todos os [SLAs abrangentes líderes do setor](https://azure.microsoft.com/support/legal/sla/cosmos-db/) de contas de banco de dados de várias regiões em disponibilidade geral. |
