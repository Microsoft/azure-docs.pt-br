---
title: Monitoramento-banco de dados do Azure para MySQL-servidor flexível
description: Este artigo descreve as métricas para monitoramento e alertas para o servidor flexível do banco de dados do Azure para MySQL, incluindo a CPU, o armazenamento e as estatísticas de conexão.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 5fe1d5a5a472b47abd364a89d1a65f1249c67c0d
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538656"
---
# <a name="monitor-azure-database-for-mysql-flexible-servers-with-built-in-metrics"></a>Monitorar servidores flexíveis do banco de dados do Azure para MySQL com métricas internas

> [!IMPORTANT] 
> O Banco de Dados do Azure para MySQL – Servidor Flexível está atualmente na versão prévia pública.

O banco de dados do Azure para MySQL servidor flexível fornece monitoramento de servidores por meio de Azure Monitor. Métricas são valores numéricos que descrevem alguns aspectos dos recursos do seu servidor em um determinado momento. Monitorar os recursos do seu servidor ajuda a solucionar problemas e otimizar sua carga de trabalho, permitindo que você monitore o que é mais importante para você. O monitoramento das métricas certas ajuda a manter o desempenho, a confiabilidade e a disponibilidade do servidor e dos aplicativos.

Neste artigo, você aprenderá sobre as várias métricas disponíveis para seu servidor flexível que fornecem informações sobre o comportamento do seu servidor.

## <a name="available-metrics"></a>Métricas disponíveis

O banco de dados do Azure para MySQL servidor flexível fornece várias métricas para entender como a carga de trabalho está sendo executada e baseada nesses dados, você pode entender o impacto em seu servidor e aplicativo. Por exemplo, em um servidor flexível, você pode monitorar **porcentagem de CPU do host** , **conexões ativas** , **porcentagem de e/s** e **porcentagem de memória do host** para identificar quando há um impacto no desempenho. A partir daí, talvez você precise otimizar sua carga de trabalho, dimensionar verticalmente alterando as camadas de computação ou dimensionar horizontalmente usando a réplica de leitura.

Todas as métricas do Azure têm uma frequência de um minuto e cada uma delas fornece 30 dias de histórico. É possível configurar alertas nas métricas. Para obter diretrizes passo a passo, consulte [como configurar alertas](./how-to-alert-on-metric.md). Outras tarefas incluem a configuração de ações automatizadas, execução de análises avançadas e arquivamento de histórico. Para obter mais informações, consulte a [Visão geral das métricas no Microsoft Azure](../../azure-monitor/platform/data-platform.md).

### <a name="list-of-metrics"></a>Lista de métricas
Essas métricas estão disponíveis para o Banco de Dados do Azure para MySQL:

|Nome de exibição da métrica|Métrica|Unit|Descrição|
|---|---|---|---|
|Porcentagem de CPU do host|cpu_percent|Porcentagem|A porcentagem de utilização da CPU no servidor, incluindo a utilização da CPU dos processos de carga de trabalho do cliente e MySQL do Azure|
|Rede de host em |network_bytes_ingress|Bytes|Tráfego de rede de entrada no servidor, incluindo o tráfego do banco de dados do cliente e recursos do Azure MySQL, como replicação, monitoramento, logs etc.|
|Saída da Rede do host|network_bytes_egress|Bytes|Tráfego de rede de saída no servidor, incluindo o tráfego dos recursos do banco de dados do cliente e do Azure MySQL, como replicação, monitoramento, logs etc.|
|Atraso de replicação|replication_lag|Segundos|O tempo desde a última transação reproduzida. Essa métrica está disponível somente para servidores de réplica.|
|Conexões ativas|active_connection|Contagem|O número de conexões ativas com o servidor.|
|Backup do Microsoft Azure|backup_storage_used|Bytes|A quantidade de armazenamento de backup usado.|
|Porcentagem de E/S|io_consumption_percent|Porcentagem|O percentual de E/S em uso.|
|Porcentagem de memória do host|memory_percent|Porcentagem|A porcentagem de memória em uso no servidor, incluindo a utilização de memória da carga de trabalho do cliente e dos processos do MySQL do Azure|
|Limite de armazenamento|storage_limit|Bytes|O armazenamento máximo para esse servidor.|
|Porcentagem de armazenamento|storage_percent|Porcentagem|O percentual de armazenamento usado fora do máximo do servidor.|
|Armazenamento usado|storage_used|Bytes|A quantidade de armazenamento em uso. O armazenamento usado pelo serviço pode incluir os arquivos de banco de dados, os logs de transação e os logs do servidor.|
|Total de conexões|total_connections|Contagem|O número total de conexões com o servidor|
|Conexões anuladas|aborted_connections|Contagem|O número de tentativas com falha para se conectar ao MySQL, por exemplo, falha na conexão devido a credenciais inadequadas.|
|Consultas|consultas|Contagem|O número de consultas por segundo|

## <a name="next-steps"></a>Próximas etapas
- Consulte [Como configurar alertas](./how-to-alert-on-metric.md) para obter orientação sobre como criar um alerta em uma métrica.
- Saiba mais sobre o [dimensionamento de IOPS](./concepts/../concepts-compute-storage.md#iops) para melhorar o desempenho.