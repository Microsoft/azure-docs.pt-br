---
title: Cenários de uso da Loja de Consultas no Banco de Dados do Azure para o PostgreSQL
description: Este artigo descreve alguns cenários para o Query Store no Banco de Dados do Azure para PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/26/2018
ms.openlocfilehash: 873462354b70d13e56ca108c3257031ef34873f8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563172"
---
# <a name="usage-scenarios-for-query-store"></a>Cenários de uso de Store de consulta

**Aplica-se a:** Banco de Dados do Azure para PostgreSQL 9.6 e 10

Você pode usar o Query Store em uma ampla variedade de cenários, nos quais o acompanhamento e a manutenção do desempenho previsível da carga de trabalho são essenciais. Considere os exemplos a seguir: 
- Identificar e ajustar consultas caras superior 
- Testes de A/B 
- Mantendo o desempenho estável durante as atualizações 
- Identificando e melhorando as cargas de trabalho ad hoc 

## <a name="identify-and-tune-expensive-queries"></a>Identificar e ajustar consultas caras 

### <a name="identify-longest-running-queries"></a>Identificar as consultas de execução mais longas 
Use a exibição [Query Performance Insight](concepts-query-performance-insight.md) no portal do Azure para identificar rapidamente as consultas mais longas. Essas consultas normalmente tendem a consumir uma quantidade significativa de recursos. A otimização de suas perguntas mais longas pode melhorar o desempenho, liberando recursos para uso por outras consultas em execução no seu sistema. 

### <a name="target-queries-with-performance-deltas"></a>Consultas de destino com deltas de desempenho 
O Query Store divide os dados de desempenho em janelas de tempo, para que você possa acompanhar o desempenho de uma consulta ao longo do tempo. Isso ajuda a identificar exatamente quais consultas estão contribuindo para um aumento no tempo total gasto. Como resultado, você pode fazer uma solução de problemas direcionada de sua carga de trabalho.

### <a name="tuning-expensive-queries"></a>Ajuste de consultas caras 
Quando você identifica uma consulta com desempenho abaixo do ideal, a ação executada depende da natureza do problema: 
- Use [Recomendações de desempenho](concepts-performance-recommendations.md) para determinar se há algum índice sugerido. Se sim, crie o índice e use o Query Store para avaliar o desempenho da consulta depois de criar o índice. 
- Certifique-se de que as estatísticas estejam atualizadas para as tabelas subjacentes usadas pela consulta.
- Pense em reescrever as consultas caras. Por exemplo, aproveite a parametrização de consulta e reduza o uso de SQL dinâmico. Implemente a lógica ideal ao ler dados, como aplicar a filtragem de dados no lado do banco de dados, não no lado do aplicativo. 


## <a name="ab-testing"></a>Testes de A/B 
Use o Query Store para comparar o desempenho da carga de trabalho antes e depois de uma alteração de aplicativo que você planeja introduzir. Exemplos de cenários para usar o Query Store para avaliar o impacto do ambiente ou a alteração do aplicativo no desempenho da carga de trabalho: 
- Implantando uma nova versão de um aplicativo. 
- Adicionando recursos adicionais para o servidor. 
- Criando índices ausentes em tabelas referenciadas por consultas caras. 
 
Em qualquer um desses cenários, aplique o seguinte fluxo de trabalho: 
1. Execute sua carga de trabalho com o Query Store antes da mudança planejada para gerar uma linha de base de desempenho. 
2. Aplique mudanças de aplicação no momento controlado no tempo. 
3. Continue executando a carga de trabalho por tempo suficiente para gerar uma imagem de desempenho do sistema após a alteração. 
4. Compare os resultados de antes e depois da alteração. 
5. Decida se deseja manter a alteração ou reversão. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Identifique e melhore as cargas de trabalho ad hoc 
Algumas cargas de trabalho não possuem consultas dominantes que você pode ajustar para melhorar o desempenho geral do aplicativo. Essas cargas de trabalho normalmente são caracterizadas com um número relativamente grande de consultas exclusivas, cada uma consumindo uma parte dos recursos do sistema. Cada consulta exclusiva é executada com pouca frequência, portanto, individualmente, seu consumo em tempo de execução não é crítico. Por outro lado, como o aplicativo está gerando novas consultas o tempo todo, uma parte significativa dos recursos do sistema é gasta na compilação de consultas, o que não é o ideal. Normalmente, essa situação acontece se o aplicativo gerar consultas (em vez de usar procedimentos armazenados ou consultas parametrizadas) ou se depender de estruturas de mapeamento objeto-relacional que geram consultas por padrão. 
 
Se você estiver no controle do código do aplicativo, considere reescrever a camada de acesso a dados para usar procedimentos armazenados ou consultas parametrizadas. No entanto, essa situação também pode ser melhorada sem alterações de aplicativo, forçando a parametrização de consulta para todo o banco de dados (todas as consultas) ou para os modelos de consulta individuais com o mesmo hash de consulta. 

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o [práticas recomendadas para usar a consulta Store](concepts-query-store-best-practices.md)