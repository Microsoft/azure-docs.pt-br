---
title: Planejamento de capacidade para Azure Search | Microsoft Docs
description: "Ajusta os recursos de computador de partição e réplica no Azure Search, onde o preço de cada recurso é definido em unidades de pesquisa faturáveis."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 1dc16afe-56f9-439d-8874-1733ae1a2b74
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 02/08/2017
ms.author: heidist
ms.openlocfilehash: 26f5e71f3d00161a92de702209e224008ec8a5ae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="scale-resource-levels-for-query-and-indexing-workloads-in-azure-search"></a>Dimensionar os níveis de recursos para cargas de trabalho de consulta e indexação na Pesquisa do Azure
Depois que você [escolhe um tipo de preço](search-sku-tier.md) e [provisiona um serviço de pesquisa](search-create-service-portal.md), a próxima etapa é, como opção, aumentar o número de réplicas ou partições usadas pelo serviço. Cada camada oferece um número fixo de unidades de cobrança. Este artigo explica como alocar essas unidades para obter uma configuração ideal que equilibra os requisitos para execução da consulta, indexação e armazenamento.

A configuração de recursos fica disponível quando você configura um serviço na [camada Básica](http://aka.ms/azuresearchbasic) ou em uma das [camadas Standard](search-limits-quotas-capacity.md). Para os serviços faturáveis nessas camadas, a capacidade é comprada em incrementos de SUs (*unidades de pesquisa*), em que cada partição e réplica conta como uma SU. 

Usar menos SUs resulta em uma lista menor proporcionalmente. A cobrança fica em vigor durante o tempo de configuração do serviço. Se, no momento, você não estiver usando um serviço, a única maneira de evitar a cobrança será excluindo o serviço e o recriando quando precisar dele.

> [!Note]
> Excluir um serviço exclui tudo nele. Há um recurso no Azure Search para fazer backup e restaurar dados de pesquisa persistentes. Para reimplantar um índice existente em um novo serviço, você deverá executar o programa usado para criar e carregá-lo originalmente. 

## <a name="terminology-partitions-and-replicas"></a>Terminologia: partições e réplicas
Partições e réplicas são os recursos principais que apoiam um serviço de pesquisa.

| Recurso | Definição |
|----------|------------|
|*Partições* | Fornecem armazenamento de índice e E/S para operações de leitura/gravação (por exemplo, ao recompilar ou atualizar um índice).|
|*Réplicas* | Instâncias do serviço de pesquisa, usadas principalmente para equilibrar a carga das operações de consulta. Cada réplica sempre hospeda uma cópia de um índice. Se você tiver 12 réplicas, terá 12 cópias de cada índice carregadas no serviço.|

> [!NOTE]
> Não há uma maneira de manipular ou gerenciar diretamente quais índices são executados em uma réplica. Uma cópia de cada índice em cada réplica faz parte da arquitetura do serviço.
>

## <a name="how-to-allocate-partitions-and-replicas"></a>Como alocar partições e réplicas
Na Pesquisa do Azure, um serviço recebe inicialmente um nível mínimo de recursos compostos por uma partição e uma réplica. Para tipos que dão suporte a isso, você poderá ajustar de forma incremental a capacidade de recursos computacionais aumentando as partições, se precisar de mais armazenamento e E/S ou adicionar mais réplicas para volumes maiores de consulta ou melhor desempenho. Um único serviço deve ter recursos suficientes para manipular todas as cargas de trabalho (indexação e consultas). Você não pode subdividir cargas de trabalho entre vários serviços.

Para aumentar ou alterar a alocação de réplicas e partições, recomendamos o uso do portal do Azure. O portal aplica limites às combinações permitidas que permaneçam abaixo dos limites máximos:

1. Entre no [portal do Azure](https://portal.azure.com/) e selecione o serviço de pesquisa.
2. Em **Configurações**, abra a folha **Escala** e use os controles deslizantes para aumentar ou diminuir o número de partições e réplicas.

Uma alternativa para o portal é usar a [API REST de Gerenciamento](https://msdn.microsoft.com/library/azure/dn832687.aspx), caso você precise de uma abordagem de provisionamento baseada em script ou em código.

Em geral, os aplicativos de pesquisa precisam de mais réplicas do que partições, especialmente quando as operações de serviço são polarizadas para cargas de trabalho de consulta. A seção sobre [alta disponibilidade](#HA) , explica o motivo.

> [!NOTE]
> Após o provisionamento de um serviço, ele não pode ser atualizado para um SKU superior. Você precisará criar um serviço de pesquisa no novo tipo e recarregar os índices. Confira [Criar um serviço de Pesquisa do Azure no portal](search-create-service-portal.md) para obter ajuda com o provisionamento do serviço.
>
>

<a id="HA"></a>

## <a name="high-availability"></a>Alta disponibilidade
Uma vez que é relativamente fácil e rápido escalar verticalmente, recomendamos começar com uma partição e uma ou duas réplicas e então escalar verticalmente conforme os volumes de consulta se acumulam. Para vários serviços nas camadas Básica ou S1, uma única partição fornece armazenamento e E/S suficientes (com 15 milhões de documentos por partição).

As cargas de trabalho de consulta são executadas principalmente em réplicas. Se precisar de mais taxa de transferência ou alta disponibilidade, provavelmente, você precisará de mais réplicas.

Recomendações gerais para alta disponibilidade são:

* Duas réplicas para alta disponibilidade de cargas de trabalho somente leitura (consultas)
* Três ou mais réplicas para alta disponibilidade de cargas de trabalho de leitura/gravação (consultas e indexação à medida que documentos individuais são adicionados, atualizados ou excluídos)

Os SLAs (contratos de nível de serviço) do Azure Search são direcionados a operações de consulta e a atualizações de índice formadas pela adição, atualização ou exclusão de documentos.

### <a name="index-availability-during-a-rebuild"></a>Disponibilidade de índice durante uma recompilação

A alta disponibilidade para a Pesquisa do Azure pertence a consultas e atualizações de índice que não envolvem a recompilação de um índice. Se você excluir um campo, alterar um tipo de dados ou renomear um campo, será necessário recompilar o índice. Para recompilar o índice, é necessário excluir o índice, recriar o índice e recarregar os dados.

> [!NOTE]
> Você pode adicionar novos campos a um índice do Azure Search sem recompilar o índice. O valor do novo campo será nulo para todos os documentos já existentes no índice.

Para manter a disponibilidade do índice durante uma recompilação, é necessário ter uma cópia do índice com um nome diferente no mesmo serviço, ou uma cópia do índice com o mesmo nome em um serviço diferente e fornecer a lógica de redirecionamento ou de failover no código.

## <a name="disaster-recovery"></a>Recuperação de desastre
Atualmente, não há mecanismo integrado para recuperação de desastres. Adicionar partições ou réplicas seria a estratégia incorreta para atingir os objetivos de recuperação de desastres. A abordagem mais comum é adicionar redundância no nível de serviço configurando um segundo serviço de pesquisa em outra região. Assim como acontece com a disponibilidade durante uma recompilação de índice, o redirecionamento ou a lógica de failover deve vir de seu código.

## <a name="increase-query-performance-with-replicas"></a>Aumentar o desempenho de consulta com réplicas
A latência da consulta é um indicador da necessidade de réplicas adicionais. Em geral, uma primeira etapa para melhorar o desempenho de consulta é adicionar mais desse recurso. Conforme você adiciona réplicas, cópias adicionais do índice são colocadas online para oferecer maior suporte a cargas de trabalho de consulta e balancear a carga das solicitações em várias réplicas.

Não podemos fornecer estimativas fixas para QPS (consultas por segundo): o desempenho da consulta depende da complexidade da consulta e de cargas de trabalho concorrentes. Em média, uma réplica em SKUs Básico ou S1 pode atender cerca de 15 QPS, mas a taxa de transferência será um maior ou menor dependendo da complexidade da consulta (consultas facetadas são mais complexas) e da latência de rede. Além disso, é importante reconhecer que, embora a adição de réplicas definitivamente adicione escala e desempenho, o resultado não é estritamente linear: a adição de três réplicas não garante o triplo da taxa de transferência.

Para saber mais sobre QPS, incluindo métodos para calcular o QPS para suas cargas de trabalho, veja [Gerenciar o serviço da Pesquisa](search-manage.md).

## <a name="increase-indexing-performance-with-partitions"></a>Aumentar o desempenho de indexação com partições
Aplicativos de pesquisa que exigem atualização de dados quase em tempo real precisarão proporcionalmente de mais partições de réplicas. A adição de partições distribui as operações de leitura/gravação em uma quantidade maior de recursos de computação. Também oferece mais espaço em disco para armazenar documentos e índices adicionais.

Índices maiores levam mais tempo para consultar. Assim, você poderá perceber que cada aumento incremental em partições requer um aumento proporcional, mas menor, em réplicas. A complexidade de suas consultas e seu volume influenciarão a rapidez com que a execução da consulta é retornada.

## <a name="basic-tier-partition-and-replica-combinations"></a>Camada Básica: combinações de partição e réplica
Um serviço Básico pode ter exatamente uma partição e até três réplicas, para o limite máximo de três SUs. O único recurso ajustável são as réplicas. É necessário um mínimo de duas réplicas para alta disponibilidade em consultas.

<a id="chart"></a>

## <a name="standard-tiers-partition-and-replica-combinations"></a>Tipos Standard: combinações de partição e réplica
Esta tabela mostra as SUs necessárias para dar suporte a combinações de réplicas e partições, sujeito ao limite de 36 SUs, para todas as camadas Standard.

|   | **1 partição** | **2 partições** | **3 partições** | **4 partições** | **6 partições** | **12 partições** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 réplica** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 réplicas** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 réplicas** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 réplicas** |4 SU |8 SU |12 SU |16 SU |24 SU |N/D |
| **5 réplicas** |5 SU |10 SU |15 SU |20 SU |30 SU |N/D |
| **6 réplicas** |6 SU |12 SU |18 SU |24 SU |36 SU |N/D |
| **12 réplicas** |12 SU |24 SU |36 SU |N/D |N/D |N/D |

SUs, preço e capacidade são explicados detalhadamente no site do Azure. Para obter mais informações, consulte [Detalhes de Preço](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> O número de réplicas e partições divide de maneira uniforme em 12 (especificamente, 1, 2, 3, 4, 6 e 12). Isso ocorre porque a Pesquisa do Azure divide previamente cada índice em 12 fragmentos, para que possam ser distribuídos em partes iguais entre todas as partições. Por exemplo, se o serviço tiver três partições e você criar um índice, cada partição conterá quatro fragmentos do índice. A maneira como o Azure Search fragmenta um índice é um detalhe de implementação, sujeito a alterações em versões futuras. Embora o número seja 12 hoje, você não deve esperar que ele seja sempre 12 no futuro.
>
>

## <a name="billing-formula-for-replica-and-partition-resources"></a>Fórmula de cobrança para recursos de réplica e uma partição
A fórmula para calcular quantas SUs são usadas para combinações específicas é o produto de réplicas e partições ou (R X P = SU). Por exemplo, três réplicas multiplicadas por três partições são cobradas como nove SUs.

O custo por SU é determinado pela camada, com uma taxa de cobrança por unidade menor para Básico que para Standard. As taxas de cada camada podem ser encontradas em [Detalhes de Preço](https://azure.microsoft.com/pricing/details/search/).
