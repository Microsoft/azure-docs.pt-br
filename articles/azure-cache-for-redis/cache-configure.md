---
title: Como configurar o Cache do Azure para Redis
description: Entenda a configuração padrão Redis no Cache do Azure para Redis e aprenda a configurar as instâncias de Cache do Azure para Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: yegu
ms.openlocfilehash: ba021d280f71504ad5c1e1aea0dc1c8c6b507b1e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100581073"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Como configurar o Cache do Azure para Redis
Este tópico descreve as configurações disponíveis para suas instâncias de Cache do Azure para Redis. Este tópico também aborda a configuração padrão do servidor Redis para instâncias do Cache do Azure para Redis.

> [!NOTE]
> Para obter mais informações sobre como configurar e usar os recursos de cache premium, consulte [Como configurar a persistência](cache-how-to-premium-persistence.md), [Como configurar o clustering](cache-how-to-premium-clustering.md) e [Como configurar o suporte à Rede Virtual](cache-how-to-premium-vnet.md).
>
>

## <a name="configure-azure-cache-for-redis-settings"></a>Configurar o Cache do Azure para Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

As configurações do Cache do Azure para Redis são exibidas e definidas na folha **Cache do Azure para Redis** usando o **Menu de Recursos**.

![Configurações do Cache do Azure para Redis](./media/cache-configure/redis-cache-settings.png)

Você pode exibir e definir as seguintes configurações usando o **Menu recursos**.

* [Visão geral](#overview)
* [Log de atividades](#activity-log)
* [Controle de acesso (IAM)](#access-control-iam)
* [Marcas](#tags)
* [Diagnosticar e resolver problemas](#diagnose-and-solve-problems)
* [Configurações](#settings)
    * [Chaves de acesso](#access-keys)
    * [Configurações avançadas](#advanced-settings)
    * [Assistente do Cache do Azure para Redis](#azure-cache-for-redis-advisor)
    * [Escala](#scale)
    * [Tamanho do cluster](#cluster-size)
    * [Persistência de dados](#redis-data-persistence)
    * [Agende atualizações](#schedule-updates)
    * [Replicação geográfica](#geo-replication)
    * [Rede Virtual](#virtual-network)
    * [Firewall](#firewall)
    * [Propriedades](#properties)
    * [Bloqueios](#locks)
    * [Script de automação](#automation-script)
* Administração
    * [Importar dados](#importexport)
    * [Exportar dados](#importexport)
    * [Reboot](#reboot)
* [Monitoring](#monitoring)
    * [Métricas do Redis](#redis-metrics)
    * [Regras de alerta](#alert-rules)
    * [Diagnóstico](#diagnostics)
* Configurações de suporte e solução de problemas
    * [Integridade de recursos](#resource-health)
    * [Nova solicitação de suporte](#new-support-request)


## <a name="overview"></a>Visão geral

**A visão geral** fornece informações básicas sobre o cache como: nome, portas, tipo de preço e métricas de cache selecionadas.

### <a name="activity-log"></a>Log de atividades

Clique em **Log de auditoria** para exibir as ações executadas em seu cache. Você também pode usar a filtragem para expandir essa exibição a fim de incluir outros recursos. Para obter mais informações sobre como trabalhar com logs de auditoria, confira [Operações de auditoria com o Resource Manager](../azure-resource-manager/management/view-activity-logs.md). Para saber mais sobre como monitorar eventos do Cache do Azure para Redis, confira [Operações e alertas](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Controle de acesso (IAM)

A seção de **controle de acesso (iam)** fornece suporte para o controle de acesso baseado em função do Azure (RBAC do Azure) no portal do Azure. Essas configurações ajudam as organizações a atenderem seus requisitos de gerenciamento de acesso de maneira simples e precisa. Para obter mais informações, consulte [controle de acesso baseado em função do Azure no portal do Azure](../role-based-access-control/role-assignments-portal.md).

### <a name="tags"></a>Marcas

A seção **Marcas** o ajuda a organizar seus recursos. Para obter mais informações, veja [Usando marcas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md).


### <a name="diagnose-and-solve-problems"></a>Diagnosticar e resolver problemas

Clique em **Diagnosticar e solucionar problemas** para ver problemas comuns e estratégias para resolvê-los.



## <a name="settings"></a>Configurações
A seção **Configurações** permite acessar e definir as seguintes configurações para seu cache.

* [Chaves de acesso](#access-keys)
* [Configurações avançadas](#advanced-settings)
* [Assistente do Cache do Azure para Redis](#azure-cache-for-redis-advisor)
* [Escala](#scale)
* [Tamanho do cluster](#cluster-size)
* [Persistência de dados](#redis-data-persistence)
* [Agende atualizações](#schedule-updates)
* [Replicação geográfica](#geo-replication)
* [Rede Virtual](#virtual-network)
* [Firewall](#firewall)
* [Propriedades](#properties)
* [Bloqueios](#locks)
* [Script de automação](#automation-script)



### <a name="access-keys"></a>Chaves de acesso
Clique em **Teclas de acesso** para exibir ou regenerar as teclas de acesso para seu cache. Essas chaves são usadas pelos clientes que se conectam ao seu cache.

![Chaves de acesso do Cache do Azure para Redis](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Configurações avançadas
As configurações a seguir são definidas na folha **Configurações avançadas**.

* [Portas de acesso](#access-ports)
* [Políticas de memória](#memory-policies)
* [Notificações de Keyspace (configurações avançadas)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Portas de acesso
Por padrão, o acesso não TLS/SSL é desabilitado para novos caches. Para habilitar a porta não TLS, clique em **não** para **permitir acesso somente via SSL** na folha **Configurações avançadas** e, em seguida, clique em **salvar**.

> [!NOTE]
> O acesso TLS ao cache do Azure para Redis dá suporte a TLS 1,0, 1,1 e 1,2 no momento, mas as versões 1,0 e 1,1 estão sendo desativadas em breve.  Leia nossa [página remover TLS 1,0 e 1,1](cache-remove-tls-10-11.md) para obter mais detalhes.

![Portas de acesso do Cache do Azure para Redis](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Políticas de memória
As configurações **Política de Maxmemory**, **maxmemory-reserved** e **maxfragmentationmemory-reserved** na folha **Configurações avançadas** definem as políticas de memória do cache.

![Política Maxmemory do Cache do Azure para Redis](./media/cache-configure/redis-cache-maxmemory-policy.png)

A **política Maxmemory** configura a política de remoção do cache e permite escolher uma das seguintes políticas de remoção:

* `volatile-lru` - é a política de remoção padrão.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Para saber mais sobre políticas `maxmemory`, consulte [Políticas de remoção](https://redis.io/topics/lru-cache#eviction-policies).

A configuração **MaxMemory-reserved** configura a quantidade de memória, em MB por instância em um cluster, que é reservada para operações que não são de cache, como a replicação durante o failover. Definir esse valor permite que você tenha uma experiência mais consistente do servidor Redis quando sua carga varia. Esse valor deve ser definido como maior para cargas de trabalho que executam muitas operações de gravação. Quando a memória é reservada para essas operações, ela não fica disponível para o armazenamento de dados armazenados em cache.

A configuração **reservada maxfragmentationmemory** configura a quantidade de memória, em MB por instância em um cluster, que é reservada para acomodar a fragmentação de memória. A configuração desse valor permite ter uma experiência do servidor Redis mais consistente quando o cache está cheio ou quase cheio e a taxa de fragmentação é alta. Quando a memória é reservada para essas operações, ela não fica disponível para o armazenamento de dados armazenados em cache.

Uma coisa a ser considerada ao escolher um novo valor de reserva de memória (**maxmemory-reserved** ou **maxfragmentationmemory-reserved**) é como essa alteração pode afetar um cache que já está em execução com grandes quantidades de dados. Por exemplo, se você tiver um cache de 53 GB com 49 GB de dados, altere o valor de reserva para 8 GB; essa alteração reduzirá a memória máxima disponível para o sistema para 45 GB. Se os valores `used_memory` ou `used_memory_rss` atuais forem maiores que o novo limite de 45 GB, o sistema precisará remover os dados até que `used_memory` e `used_memory_rss` fiquem abaixo de 45 GB. A remoção pode aumentar a fragmentação de memória e carregamento do servidor. Para obter mais informações sobre métricas do cache, como `used_memory` e `used_memory_rss`, consulte [Métricas disponíveis e intervalos de relatórios](cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

> [!IMPORTANT]
> As configurações **maxmemory-reserved** e **maxfragmentationmemory-reserved** estão disponíveis somente para caches Standard e Premium.
>
>

#### <a name="keyspace-notifications-advanced-settings"></a>Notificações de Keyspace (configurações avançadas)
As notificações keyspace do Redis são configuradas na folha **Configurações avançadas** . As notificações keyspace permitem que clientes recebam notificações quando determinados eventos ocorrerem.

![Configurações avançadas do Cache do Azure para Redis](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> As notificações de Keyspace e a configuração **notify-keyspace-events** estão disponíveis apenas para os caches Standard e Premium.
>
>

Para obter mais informações, veja [Notificações de Keyspace do Redis](https://redis.io/topics/notifications). Para o código de exemplo, veja o arquivo [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) no exemplo [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Assistente do Cache do Azure para Redis
A folha **Assistente do Cache do Azure para Redis** exibe recomendações para seu cache. Durante as operações normais, nenhuma recomendação é exibida.

![Captura de tela que mostra onde as recomendações são exibidas.](./media/cache-configure/redis-cache-no-recommendations.png)

Se alguma condição ocorrer durante as operações de seu cache, por exemplo, alto uso de memória, de largura de banda de rede ou de carga do servidor, um alerta será exibido na folha **Cache do Azure para Redis**.

![Captura de tela que mostra onde os alertas são exibidos na seção cache do Azure para Redis.](./media/cache-configure/redis-cache-recommendations-alert.png)

É possível encontrar mais informações na folha **Recomendações** .

![Recomendações](./media/cache-configure/redis-cache-recommendations.png)

Você pode monitorar essas métricas nas seções [Gráficos de monitoramento](cache-how-to-monitor.md#monitoring-charts) e [Gráficos de uso](cache-how-to-monitor.md#usage-charts) da folha **Cache do Azure para Redis**.

Cada tipo de preço tem limites diferentes para conexões de cliente, memória e largura de banda. Se o cache se aproximar da capacidade máxima para essas métricas durante um período prolongado, uma recomendação será criada. Para obter mais informações sobre as métricas e os limites revisados pela ferramenta de **recomendações** , consulte a tabela a seguir:

| Métrica do Cache do Azure para Redis | Mais informações |
| --- | --- |
| Uso de largura de banda de rede |[Desempenho do cache - largura de banda disponível](cache-planning-faq.md#azure-cache-for-redis-performance) |
| Clientes conectados |[Configuração padrão do servidor Redis - maxclients](#maxclients) |
| Carga do servidor |[Gráficos de uso - carga do servidor Redis](cache-how-to-monitor.md#usage-charts) |
| Uso de memória |[Desempenho do cache - tamanho](cache-planning-faq.md#azure-cache-for-redis-performance) |

Para atualizar o cache, clique em **Atualizar agora** para alterar o tipo de preço e [dimensionar](#scale) o cache. Para obter mais informações sobre como escolher um tipo de preço, consulte [escolher a camada correta](cache-overview.md#choosing-the-right-tier)


### <a name="scale"></a>Escala
Clique em **Escala** para exibir ou alterar o tipo de preço do cache. Para obter mais informações sobre escala, veja [Como escalonar o Cache do Azure para Redis](cache-how-to-scale.md).

![Tipo de preço do Cache do Azure para Redis](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Tamanho do cluster Redis
Clique em **tamanho do cluster** para alterar o tamanho do cluster para um cache Premium em execução com clustering habilitado.

![Tamanho do cluster](./media/cache-configure/redis-cache-redis-cluster-size.png)

Para alterar o tamanho do cluster, use o controle deslizante ou digite um número entre 1 e 10 na caixa de texto **Contagem de fragmentos** e clique em **OK** para salvar.

> [!IMPORTANT]
> O clustering está disponível apenas para os Caches premium. Para saber mais, confira [Como configurar o clustering para um Cache do Azure para Redis Premium](cache-how-to-premium-clustering.md).
>
>


### <a name="redis-data-persistence"></a>Persistência de dados do Redis
Clique em **persistência de dados** para habilitar, desabilitar ou configurar a persistência de dados para seu cache Premium. O Cache do Azure para Redis oferece persistência Redis usando persistência RDB ou persistência AOF.

Para saber mais, confira [Como configurar a persistência para um Cache do Azure para Redis Premium](cache-how-to-premium-persistence.md).


> [!IMPORTANT]
> A persistência de dados do Redis só está disponível para os caches Premium.
>
>

### <a name="schedule-updates"></a>Agende atualizações
A folha **Agendar atualizações** permite designar uma janela de manutenção para atualizações do servidor Redis do seu cache.

> [!IMPORTANT]
> A janela de manutenção se aplica apenas às atualizações do servidor Redis e não a quaisquer atualizações do Azure ou atualizações do sistema operacional das VMs que hospedam o cache.
>
>

![Agende atualizações](./media/cache-configure/redis-schedule-updates.png)

Para especificar uma janela de manutenção, marque os dias desejados, especifique a hora de início da janela de manutenção para cada dia e clique em **OK**. O horário da janela de manutenção é em UTC.

Para obter mais informações e instruções, consulte [cache do Azure para administração de Redis – agendar atualizações](cache-administration.md#schedule-updates)

### <a name="geo-replication"></a>Replicação geográfica

A folha **Replicação geográfica** fornece um mecanismo para vincular duas instâncias de Cache do Azure para Redis de camada Premium. Um cache é designado como o cache vinculado primário e o outro como o cache vinculado secundário. O cache vinculado secundário se torna somente leitura e os dados gravados no cache primário são replicados para o cache vinculado secundário. Essa funcionalidade pode ser usada para replicar um cache entre regiões do Azure.

> [!IMPORTANT]
> A **replicação geográfica** está disponível somente para caches de camada Premium. Para obter mais informações e instruções, consulte [Como configurar a replicação geográfica no Cache do Azure para Redis](cache-how-to-geo-replication.md).
>
>

### <a name="virtual-network"></a>Rede Virtual
A seção **Rede Virtual** permite que você defina as configurações de rede virtual para o cache. Para saber mais sobre como criar um cache premium com suporte de rede virtual e atualizar suas configurações, confira [Como configurar o suporte de Rede Virtual para um Cache do Azure para Redis Premium](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> As configurações de rede virtual só estão disponíveis para os caches premium configurados com suporte para rede virtual durante a criação do cache.
>
>

### <a name="firewall"></a>Firewall

A configuração de regras de firewall está disponível para todas as camadas do Cache do Azure para Redis.

Clique em **Firewall** para exibir e configurar regras de firewall para o cache.

![Firewall](./media/cache-configure/redis-firewall-rules.png)

É possível especificar regras de firewall com um intervalo de endereços IP inicial e final. Quando regras de firewall são configuradas, apenas as conexões de cliente de intervalos de endereços IP especificados podem se conectar ao cache. Quando uma regra de firewall é salva, há um pequeno atraso antes que a regra entre em vigor. Normalmente, esse atraso é inferior a um minuto.

> [!IMPORTANT]
> Conexões dos sistemas de monitoramento do Cache do Azure para Redis serão sempre permitidas, mesmo se regras de firewall forem configuradas.
>
>

### <a name="properties"></a>Propriedades
Clique em **Propriedades** para exibir informações sobre seu cache, incluindo o ponto de extremidade e as portas do cache.

![Propriedades do Cache do Azure para Redis](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Locks
A seção de **bloqueios** permite que você bloqueie uma assinatura, grupo de recursos ou recurso para impedir que outros usuários em sua organização excluam ou modifiquem acidentalmente recursos críticos. Para obter mais informações, consulte [Bloquear recursos com Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).

### <a name="automation-script"></a>Script de automação

Clique em **Script de automação** para compilar e exportar um modelo de seus recursos implantados para implantações futuras. Para saber mais sobre como trabalhar com modelos, confira [Implantar recursos com modelos do Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="administration-settings"></a>Configurações de administração
As configurações na seção **Administração** permitem que você execute as tarefas administrativas a seguir para seu cache.

![Administração](./media/cache-configure/redis-cache-administration.png)

* [Importar dados](#importexport)
* [Exportar dados](#importexport)
* [Reboot](#reboot)


### <a name="importexport"></a>Importar/Exportar
A Importação/Exportação é uma operação de gerenciamento de dados do Cache do Azure para Redis que permite importar e exportar dados para o cache importando e exportando um instantâneo do RDB (Banco de Dados do Cache Redis) de um cache premium para um blob de páginas em uma Conta de Armazenamento do Azure. A Importação/Exportação permite migrar entre diferentes instâncias do Cache do Azure para Redis ou preencher o cache com dados antes do uso.

A importação pode ser usada para trazer arquivos RDB compatíveis com o Redis de qualquer servidor Redis em execução em qualquer nuvem ou ambiente, incluindo o Redis em execução no Linux, Windows ou qualquer provedor de nuvem como Amazon Web Services e similares. Importar os dados é uma maneira fácil de criar um cache com dados previamente populados. Durante o processo de importação, o Cache do Azure para Redis carrega os arquivos RDB do armazenamento do Azure na memória e insere as chaves no cache.

A exportação permite exportar os dados armazenados no Cache do Azure para Redis para um arquivo RDB compatível com Redis. É possível usar esse recurso para mover dados de uma instância do Cache do Azure para Redis para outro servidor Redis. Durante o processo de exportação, um arquivo temporário é criado na VM que hospeda a instância do servidor do Cache do Azure para Redis, e o arquivo é carregado na conta de armazenamento designada. Após a operação de exportação ser concluída com um status de êxito ou de falha, o arquivo temporário é excluído.

> [!IMPORTANT]
> A opção Importar/Exportar está disponível somente para caches do nível Premium. Para obter mais informações e instruções, confira [Importar e exportar dados no Cache do Azure para Redis](cache-how-to-import-export-data.md).
>
>

### <a name="reboot"></a>Reboot
A folha **Reinicializar** permite a reinicialização dos nós do cache. Essa funcionalidade de reinicialização permite que você teste seu aplicativo para garantir a resiliência caso ocorra uma falha de um nó de cache.

![Reboot](./media/cache-configure/redis-cache-reboot.png)

Se tiver um cache premium com clustering habilitado, você poderá selecionar quais fragmentos do cache serão reinicializados.

![Captura de tela que mostra onde selecionar quais fragmentos do cache deve ser reinicializado.](./media/cache-configure/redis-cache-reboot-cluster.png)

Para reinicializar um ou mais nós do cache, selecione os nós desejados e clique em **Reinicializar**. Se tiver um cache premium com clustering habilitado, selecione os fragmentos a serem reinicializados e clique em **Reinicializar**. Depois de alguns minutos, os nós selecionados são reinicializados e voltam a ficar online alguns minutos mais tarde.

> [!IMPORTANT]
> A reinicialização agora está disponível para todos os tipos de preço. Para obter mais informações e instruções, confira [Administração do Cache do Azure para Redis – Reinicialização](cache-administration.md#reboot).
>
>


## <a name="monitoring"></a>Monitoramento

A seção **Monitoramento** permite que você configure o diagnóstico e o monitoramento do Cache do Azure para Redis.
Para saber mais sobre diagnóstico e monitoramento do Cache do Azure para Redis, confira [Como monitorar o Cache do Azure para Redis](cache-how-to-monitor.md).

![Diagnósticos](./media/cache-configure/redis-cache-diagnostics.png)

* [Métricas do Redis](#redis-metrics)
* [Regras de alerta](#alert-rules)
* [Diagnóstico](#diagnostics)

### <a name="redis-metrics"></a>Métricas do Redis
Clique em **Métricas do Redis** para [exibir métricas](cache-how-to-monitor.md#view-cache-metrics) para seu cache.

### <a name="alert-rules"></a>Regras de alerta

Clique em **Regras de alerta** para configurar alertas com base na métrica do Cache do Azure para Redis. Para obter mais informações, consulte [Alertas](cache-how-to-monitor.md#alerts).

### <a name="diagnostics"></a>Diagnósticos

Por padrão, as métricas de cache no Azure Monitor são [armazenadas durante 30 dias](../azure-monitor/essentials/data-platform-metrics.md) e, em seguida, excluídas. Para manter suas métricas de cache por mais de 30 dias, clique em **Diagnóstico** para [configurar a conta de armazenamento](cache-how-to-monitor.md#export-cache-metrics) usada para armazenar o diagnóstico de cache.

>[!NOTE]
>Além de arquivar suas métricas de cache no armazenamento, você também pode [transmiti-las para um hub de eventos ou enviá-las para Azure monitor logs](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md).
>
>

## <a name="support--troubleshooting-settings"></a>Configurações de suporte e solução de problemas
As configurações na seção **Suporte + solução de problemas** fornecem opções para resolver problemas com o cache.

![Suporte + solução de problemas](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Integridade de recursos](#resource-health)
* [Nova solicitação de suporte](#new-support-request)

### <a name="resource-health"></a>Integridade de recursos
**Resource Health** observa seu recurso e informa se ele está sendo executado conforme o esperado. Para saber mais sobre o serviço Azure Resource Health, confira [Visão geral do Azure Resource Health](../service-health/resource-health-overview.md).

> [!NOTE]
> A integridade de recursos atualmente não consegue relatar a integridade de instâncias de Cache do Azure para Redis hospedadas em uma rede virtual. Para saber mais, confira [Todos os recursos de cache funcionam ao hospedar um cache em uma rede virtual?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network)
>
>

### <a name="new-support-request"></a>Nova solicitação de suporte
Clique em **Nova solicitação de suporte** para abrir uma solicitação de suporte para seu cache.





## <a name="default-redis-server-configuration"></a>Configuração padrão do servidor Redis
Novas instâncias de Cache do Azure para Redis são configuradas com os seguintes valores de configuração padrão Redis:

> [!NOTE]
> As configurações nesta seção não podem ser alteradas com o método `StackExchange.Redis.IServer.ConfigSet`. Se esse método for chamado com um dos comandos nesta seção, será gerada uma exceção similar ao exemplo a seguir:  
>
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>
> Todos os valores que podem ser configurados, como **max-memory-policy**, podem ser configurados por meio do Portal do Azure ou de ferramentas de gerenciamento de linha de comando, como a CLI do Azure ou o PowerShell.
>
>

| Configuração | Valor padrão | Descrição |
| --- | --- | --- |
| `databases` |16 |O número de bancos de dados padrão é 16, mas você pode configurar um número diferente com base no tipo de preço.<sup>1</sup> O banco de dados padrão é o DB 0; você poderá selecionar um diferente por conexão usando `connection.GetDatabase(dbid)`, em que `dbid` é um número entre `0` e `databases - 1`. |
| `maxclients` |Depende do tipo de preço<sup>2</sup> |Esse valor é o número máximo de clientes conectados permitidos ao mesmo tempo. Quando o limite é atingido o Redis fecha todas as novas conexões, retornando um erro de 'número máximo de clientes atingido'. |
| `maxmemory-policy` |`volatile-lru` |A política Maxmemory é a configuração de como o Redis seleciona o que remover quando `maxmemory` (o tamanho da oferta de cache que você selecionou quando criou o cache) é atingido. Com o Cache do Azure para Redis, a configuração padrão é `volatile-lru`, que remove as chaves com um conjunto de expiração usando um algoritmo LRU. Essa configuração pode ser definida no portal do Azure. Para obter mais informações, consulte [Políticas de memória](#memory-policies). |
| `maxmemory-samples` |3 |Para economizar memória, LRU e algoritmos TTL mínimos são algoritmos aproximados, em vez de algoritmos precisos. Por padrão, o Redis verificará três chaves e escolherá aquela que foi usada há mais tempo. |
| `lua-time-limit` |5\.000 |Tempo máximo de execução de um script Lua em milissegundos. Se o tempo de execução máximo for atingido, o Redis registrará em log que um script ainda está em execução depois do tempo máximo permitido e começará a responder a consultas com um erro. |
| `lua-event-limit` |500 |O tamanho máximo da fila de eventos de script. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8mb 60 |Os limites de buffer de saída do cliente podem ser usados para impor a desconexão de clientes que não estão lendo dados do servidor de forma rápida o suficiente, por algum motivo (uma razão comum é que um cliente Pub/Sub não consegue consumir mensagens de forma tão rápida quanto o editor consegue produzi-las). Para obter mais informações, confira [https://redis.io/topics/clients](https://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup>O limite para `databases` é diferente para cada tipo de preço do Cache do Azure para Redis e pode ser definido na criação do cache. Se nenhuma configuração `databases` for especificada durante a criação do cache, o padrão será 16.

* Caches Básico e Standard
  * Cache C0 (250 MB) - até 16 bancos de dados
  * Cache C1 (1 GB) - até 16 bancos de dados
  * Cache C2 (2,5 GB) - até 16 bancos de dados
  * Cache C3 (6 GB) - até 16 bancos de dados
  * Cache C4 (13 GB) - até 32 bancos de dados
  * Cache C5 (26 GB) - até 48 bancos de dados
  * Cache C6 (53 GB) - até 64 bancos de dados
* Caches Premium
  * P1 (6 GB - 60 GB) - até 16 bancos de dados
  * P2 (13 - 130 GB) - até 32 bancos de dados
  * P3 (26 GB - 260 GB) - até 48 bancos de dados
  * P4 (53 - 530 GB) - até 64 bancos de dados
  * Todos os caches premium com cluster Redis habilitado – o cluster Redis permite apenas o uso do banco de dados 0 para que o limite `databases` de qualquer cache premium com o cluster Redis habilitado seja efetivamente 1 e o comando [Select](https://redis.io/commands/select) não seja permitido. Para saber mais, confira [Preciso fazer alguma alteração no meu aplicativo cliente para usar clustering?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Para saber mais sobre bancos de dados, veja [O que são bancos de dados Redis?](cache-development-faq.md#what-are-redis-databases)

> [!NOTE]
> As configurações `databases` pode ser definida somente durante a criação do cache e apenas usando PowerShell, CLI ou outros clientes de gerenciamento. Para obter um exemplo de configuração `databases` durante a criação de cache usando o PowerShell, confira [New-AzRedisCache](cache-how-to-manage-redis-cache-powershell.md#databases).
>
>

<a name="maxclients"></a>
<sup>2</sup>`maxclients` é diferente para cada tipo de preço do Cache do Azure para Redis.

* Caches Básico e Standard
  * Cache C0 (250 MB) - até 256 conexões
  * Cache C1 (1 GB) - até 1.000 conexões
  * Cache C2 (2.5 GB) - até 2.000 conexões
  * Cache C3 (6 GB) - até 5.000 conexões
  * Cache C4 (13 GB) - até 10.000 conexões
  * Cache C5 (26 GB) - até 15.000 conexões
  * Cache C6 (53 GB) - até 20.000 conexões
* Caches Premium
  * P1 (6 GB - 60 GB) - até 7.500 conexões
  * P2 (13 GB - 130 GB) - até 15.000 conexões
  * P3 (26 GB - 260 GB) - até 30.000 conexões
  * P4 (53 GB - 530 GB) - até 40.000 conexões

> [!NOTE]
> Embora cada tamanho de cache permita *até* um determinado número de conexões, cada conexão com o Redis tem sobrecarga associadas. Um exemplo de tal sobrecarga seria o uso da CPU e de memória como resultado de criptografia TLS/SSL. O limite máximo de conexão para um tamanho de cache determinado pressupõe um cache com pouca carga. Se a carga da conexão de sobrecarga *mais* a carga de operações do cliente excederem a capacidade do sistema, o cache poderá enfrentar problemas de capacidade mesmo se não exceder o limite de conexão para o tamanho atual do cache.
>
>



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>Comandos Redis não têm suporte no Cache do Azure para Redis
> [!IMPORTANT]
> Como a configuração e o gerenciamento de instâncias do Cache do Azure para Redis são gerenciados pela Microsoft, os comandos a seguir são desabilitados. Se você tentar invocá-los, receberá uma mensagem de erro semelhante a `"(error) ERR unknown command"`.
>
> * BGREWRITEAOF
> * BGSAVE
> * CONFIG
> * DEBUG
> * MIGRATE
> * SAVE
> * SHUTDOWN
> * SLAVEOF
> * CLUSTER - Os comandos de gravação do cluster estão desabilitados, mas comandos do Cluster somente leitura são permitidos.
>
>

Para obter mais informações sobre comandos Redis, consulte [https://redis.io/commands](https://redis.io/commands) .

## <a name="redis-console"></a>Console do Redis
Você pode emitir comandos com segurança para suas instâncias do Cache do Azure para Redis usando o **Console do Redis**, que está disponível no portal do Azure para todas as camadas de cache.

> [!IMPORTANT]
> - O Console do Redis não funciona com [VNET](cache-how-to-premium-vnet.md). Quando o seu cache faz parte de uma VNET, somente os clientes na VNET podem acessar o cache. Como o Console do Redis é executado em seu navegador local, que está fora da VNET, ele não poderá se conectar ao seu cache.
> - Nem todos os comandos do Redis têm suporte no Cache do Azure para Redis. Para obter uma lista de comandos do Redis que estão desabilitados para o Cache do Azure para Redis, veja a seção anterior [Comandos do Redis sem suporte no Cache do Azure para Redis](#redis-commands-not-supported-in-azure-cache-for-redis). Para obter mais informações sobre comandos Redis, consulte [https://redis.io/commands](https://redis.io/commands) .
>
>

Para acessar o Console do Redis, clique em **Console** na folha **Cache do Azure para Redis**.

![Captura de tela que realça o botão do console.](./media/cache-configure/redis-console-menu.png)

Para emitir comandos em sua instância de cache, digite o comando desejado no console.

![A captura de tela thas mostra o console do Redis com o comando de entrada e os resultados.](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>Usando o Console Redis com um cache Premium clusterizado

Quando usar o Console do Redis com um cache Premium clusterizado, você pode emitir comandos para um único fragmento do cache. Para emitir um comando para um fragmento específico, primeiro conecte-se ao fragmento desejado clicando no seletor de fragmento.

![Console do Redis](./media/cache-configure/redis-console-premium-cluster.png)

Se você tentar acessar uma chave armazenada em um fragmento diferente do fragmento conectado, você receberá uma mensagem de erro semelhante à mensagem a seguir:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

No exemplo anterior, o fragmento 1 é o fragmento selecionado, mas `myKey` está localizado no fragmento 0, conforme indicado pela parte `(shard 0)` da mensagem de erro. Neste exemplo, para acessar `myKey`, selecione o fragmento 0 usando o seletor de fragmento e, em seguida, execute o comando desejado.


## <a name="move-your-cache-to-a-new-subscription"></a>Mover o cache para uma nova assinatura
Você pode mover o cache para uma nova assinatura clicando em **Mover**.

![Mover o Cache do Azure para Redis](./media/cache-configure/redis-cache-move.png)

Para saber mais sobre como mover os recursos de um grupo de recursos para outro, e de uma assinatura para outra, confira [Mover recursos para um novo grupo de recursos ou uma nova assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="next-steps"></a>Próximas etapas
* Para obter mais informações sobre como trabalhar com comandos Redis, consulte [como posso executar comandos do Redis?](cache-development-faq.md#how-can-i-run-redis-commands)
