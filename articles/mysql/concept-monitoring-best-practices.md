---
title: Práticas recomendadas de monitoramento-banco de dados do Azure para MySQL
description: Este artigo descreve as práticas recomendadas para monitorar o banco de dados do Azure para MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 59301e26f4d42056322ba5a7cdaff1555c531096
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96354925"
---
# <a name="best-practices-for-monitoring-azure-database-for-mysql--single-server"></a>Práticas recomendadas para monitorar o banco de dados do Azure para MySQL-servidor único

Saiba mais sobre as práticas recomendadas que podem ser usadas para monitorar suas operações de banco de dados e garantir que o desempenho não seja comprometido à medida que o tamanho dos mesmos cresce. À medida que adicionamos novos recursos à plataforma, continuaremos refinando as práticas recomendadas detalhadas nesta seção.

## <a name="layout-of-the-current-monitoring-toolkit"></a>Layout do kit de ferramentas de monitoramento atual

O banco de dados do Azure para MySQL fornece ferramentas e métodos que podem ser usados para monitorar o uso com facilidade, adicionar ou remover recursos (como CPU, memória ou e/s), solucionar problemas potenciais e ajudar a melhorar o desempenho de um banco de dados. Você pode [monitorar as métricas de desempenho](concepts-monitoring.md#metrics) regularmente para ver os valores médio, máximo e mínimo para uma variedade de intervalos de tempo.

Você pode [configurar alertas](howto-alert-on-metric.md#create-an-alert-rule-on-a-metric-from-the-azure-portal) para um limite de métrica, para que você seja informado se o servidor tiver atingido esses limites e tomar as devidas ações.  

Monitore o servidor de banco de dados para certificar-se de que os recursos atribuídos ao banco de dados possam lidar com a carga de trabalho do aplicativo. Se o banco de dados estiver atingindo os limites de recurso, considere:
    * Identificar e otimizar as principais consultas que consomem recursos. 
    * Adicionar mais recursos atualizando a camada de serviço.

### <a name="cpu-utilization"></a>Utilização da CPU
Monitore o uso da CPU e se o banco de dados estiver esgotando os recursos da CPU. Se o uso da CPU for 90% ou mais do que você deve escalar verticalmente sua computação aumentando o número de vCores ou escala para o próximo tipo de preço.  Verifique se a taxa de transferência ou a simultaneidade está conforme o esperado enquanto você dimensiona/reduz a CPU. 

### <a name="memory"></a>Memória 
A quantidade de memória disponível para o servidor de banco de dados é proporcional ao [número de vCores](concepts-pricing-tiers.md). Verifique se a memória é suficiente para a carga de trabalho. Teste de carga seu aplicativo para verificar se a memória é suficiente para operações de leitura e gravação. Se o consumo de memória do banco de dados costuma aumentar além de um limite definido, isso indica que você deve atualizar sua instância aumentando o nível de desempenho vCores ou superior. Use [repositório de consultas](concepts-query-store.md), [recomendações de desempenho de consulta](concepts-performance-recommendations.md) para identificar consultas com a duração mais longa, a mais executada. Explore oportunidades para otimizar. 

### <a name="storage"></a>Armazenamento 
A [quantidade de armazenamento](howto-create-manage-server-portal.md#scale-compute-and-storage) provisionada para o servidor MySQL determina o IOPS para o servidor. O armazenamento usado pelo serviço inclui os arquivos de banco de dados, os logs de transações, os logs de servidor e instantâneos de backup. Certifique-se de que o espaço em disco consumido não exceda constantemente acima de 85% do espaço em disco provisionado total. Se esse for o caso, você precisará excluir ou arquivar dados do servidor de banco de dado para liberar espaço. 

### <a name="network-traffic"></a>Tráfego de rede 

**Taxa de transferência de recebimento de rede, taxa de transferência de transmissão de rede** – a taxa de tráfego de rede de e para a instância do MySQL em megabytes por segundo. Você precisa avaliar o requisito de taxa de transferência para o servidor e monitorar constantemente o tráfego se a taxa de transferência for menor do que o esperado. 

### <a name="database-connections"></a>Conexões de banco de dados 
**Conexões de banco de dados** – o número de sessões de cliente que estão conectadas ao banco de dados do Azure para MySQL deve ser alinhado com os [limites de conexão para o tamanho de SKU selecionado](concepts-server-parameters.md#max_connections) . 


## <a name="next-steps"></a>Próximas etapas

- [Prática recomendada para o desempenho do banco de dados do Azure para MySQL](concept-performance-best-practices.md)
- [Prática recomendada para operações de servidor usando o banco de dados do Azure para MySQL](concept-operation-excellence-best-practices.md)
