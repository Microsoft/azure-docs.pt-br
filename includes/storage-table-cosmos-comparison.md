---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: include
ms.date: 04/06/2018
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: 93bb0a853199b8b1fc38b8cc903af0a434cab034
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172461"
---
Caso utilize o Armazenamento de Tabelas do Azure neste momento, você receberá os seguintes benefícios ao mudar para a API de Tabela do Azure Cosmos DB:

| | Armazenamento da tabela do Azure | API de Tabela do Azure Cosmos DB |
| --- | --- | --- |
| Latency | Rápido, mas não há limites superiores de latência. | Latência de milissegundo de dígito único para leituras e gravações, com suporte de leituras de latência de <10 ms e gravações de latência de <15 ms no 99º percentil, em qualquer escala, em qualquer lugar do mundo. |
| Produtividade | Modelo de taxa de transferência variável. As tabelas têm um limite de escalabilidade de 20.000 operações/s. | Altamente escalonável com [taxa de transferência reservada dedicada por tabela](../articles/cosmos-db/request-units.md), que é respaldada por SLAs. As contas não têm nenhum limite superior na taxa de transferência e dão suporte para >10 milhões de operações/s por tabela. |
| Distribuição global | Região única com uma região secundária legível opcional para alta disponibilidade. Você não pode iniciar o failover. | [Distribuição global turnkey](../articles/cosmos-db/distribute-data-globally.md) de 1 a 30 ou mais regiões. Suporte para [failovers automáticos e manuais](../articles/cosmos-db/high-availability.md) a qualquer momento, em qualquer lugar no mundo. |
| Indexação | Somente índice primário em PartitionKey e RowKey. Nenhum índice secundário. | Indexação automática e completa em todas as propriedades, sem gerenciamento de índice. |
| Consultar | A execução de consulta usa o índice para chave primária. Caso contrário, realiza a verificação. | As consultas podem aproveitar a indexação automática em propriedades para tempos rápidos de consulta. |
| Consistência | Forte na região primária. Eventual na região secundária. | [Cinco níveis de consistência bem definidos](../articles/cosmos-db/consistency-levels.md) para compensar a disponibilidade, latência, taxa de transferência e consistência com base nas necessidades do seu aplicativo. |
| Preços | Otimização de armazenamento. | Otimização de taxa de transferência. |
| SLAs | disponibilidade de 99,99%. | 99,99% para todas as contas de região única e todas as contas de várias regiões com consistência amena e 99,999% de disponibilidade de leitura em todos os [SLAs abrangentes líderes do setor](https://azure.microsoft.com/support/legal/sla/cosmos-db/) de contas de banco de dados de várias regiões em disponibilidade geral. |
