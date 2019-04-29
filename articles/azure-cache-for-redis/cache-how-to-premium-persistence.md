---
title: Como configurar a persistência de dados de um Cache Redis do Azure Premium
description: Saiba como configurar e gerenciar a persistência de dados para as instâncias da camada Premium do Cache Redis do Azure
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: b01cf279-60a0-4711-8c5f-af22d9540d38
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 08/24/2017
ms.author: yegu
ms.openlocfilehash: de0b2e3ef7b0268540ef4896ade132a297ee88ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60543291"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-cache-for-redis"></a>Como configurar a persistência de dados de um Cache Redis do Azure Premium
O Cache Redis do Azure apresenta diferentes ofertas de cache que fornecem flexibilidade na escolha do tamanho e dos recursos de cache, incluindo recursos da camada Premium como clustering, persistência e suporte à rede virtual. Este artigo descreve como configurar a persistência em uma instância Premium do Cache Redis do Azure.

Para saber mais sobre outros recursos Premium de cache, consulte [Introdução à camada Premium do Cache Redis do Azure](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>O que é a persistência de dados?
A [persistência do Redis](https://redis.io/topics/persistence) permite persistir os dados armazenados no Redis. Você também pode tirar instantâneos e fazer backup dos dados, que podem ser carregados em caso de falha de hardware. Essa é uma enorme vantagem em relação às camadas Básica ou Standard, em que todos os dados são armazenados na memória e pode haver uma possível perda de dados em caso de falha quando os nós do Cache estiverem inativos. 

O Cache Redis do Azure oferece a persistência de Redis usando os seguintes modelos:

* **Persistência de RDB**: quando a persistência de RDB (banco de dados do Redis) está configurada, o Cache Redis do Azure persiste um instantâneo do Cache Redis do Azure em um formato binário do Redis em disco com base em uma frequência de backup configurável. Se ocorrer um desastre que desabilite os caches primário e de réplica, o cache será reconstruído com o uso do instantâneo mais recente. Saiba mais sobre as [vantagens](https://redis.io/topics/persistence#rdb-advantages) e as [desvantagens](https://redis.io/topics/persistence#rdb-disadvantages) de persistência de RDB.
* **Persistência de AOF**: quando a persistência de AOF (Arquivo somente para anexação) está configurada, o Cache Redis do Azure salva todas as operações de gravação em um log salvo pelo menos uma vez por segundo em uma conta de Armazenamento do Azure. Se ocorrer um desastre que desabilite os caches primário e de réplica, o cache será reconstruído com as operações de gravação mais recentes. Saiba mais sobre as [vantagens](https://redis.io/topics/persistence#aof-advantages) e as [desvantagens](https://redis.io/topics/persistence#aof-disadvantages) da persistência de AOF.

A persistência é configurada na folha **Novo Cache Redis do Azure** durante a criação do cache e no menu **Recurso** dos caches Premium existentes.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Depois de selecionar um tipo de preço premium, clique em **Persistência do Redis**.

![Persistência do Redis][redis-cache-persistence]

As etapas na próxima seção descrevem como configurar a persistência do Redis em seu novo cache premium. Após configurar a persistência do Redis, clique em **Criar** para criar seu novo cache premium com persistência do Redis.

## <a name="enable-redis-persistence"></a>Habilitar a persistência de Redis

A persistência de Redis é habilitada na folha **Persistência de dados de Redis** escolhendo uma persistência de **RDB** ou **AOF**. Para novos caches, esta folha é acessada durante o processo de criação de cache, conforme descrito na seção anterior. Para os caches existentes, a folha **Persistência dos dados Redis** é acessada no **menu Recurso** do seu cache.

![Configurações do Redis][redis-cache-settings]


## <a name="configure-rdb-persistence"></a>Configurar a persistência de RDB

Para habilitar a persistência de RDB, clique em **RDB**. Para desabilitar a persistência de RDB em um cache premium habilitado anteriormente, clique em **Desabilitado**.

![Persistência de RDB Redis][redis-cache-rdb-persistence]

Para configurar o intervalo de backup, selecione uma **Frequência de Backup** na lista suspensa. As opções incluem **15 minutos**, **30 minutos**, **60 minutos**, **6 horas**, **12 horas** e **24 horas**. Esse intervalo inicia a contagem regressiva depois que a operação de backup anterior for concluída com êxito e quando ela expira, um novo backup é iniciado.

Clique em **Conta de Armazenamento** para selecionar a conta de armazenamento a ser usada e escolha a **Chave primária** ou **Chave secundária** a ser usada na lista suspensa **Chave de Armazenamento**. Você deve escolher uma conta de armazenamento na mesma região que o cache e uma conta do **Armazenamento Premium** é recomendada, pois o armazenamento premium tem uma maior taxa de transferência. 

> [!IMPORTANT]
> Se a chave de armazenamento para a sua conta de persistência for regenerada, você deverá reconfigurar a chave desejada no menu suspenso **Chave de Armazenamento**.
> 
> 

Clique em **OK** para salvar a configuração de persistência.

O próximo backup (ou primeiro backup para caches novos) será iniciado após decorrido o intervalo de frequência de backup.

## <a name="configure-aof-persistence"></a>Configurar a persistência de AOF

Para habilitar a persistência de AOF, clique em **AOF**. Para desabilitar a persistência de AOF em um cache premium habilitado anteriormente, clique em **Desabilitado**.

![Persistência de AOF de Redis][redis-cache-aof-persistence]

Para configurar persistência de AOF, especifique uma **Primeira Conta de Armazenamento**. Esta conta de armazenamento deve estar na mesma região que o cache, e uma conta do **Armazenamento Premium** é recomendada, pois o armazenamento premium tem uma taxa de transferência maior. Como opção, você pode configurar uma conta de armazenamento adicional denominada **Segunda Conta de Armazenamento**. Se uma segunda conta de armazenamento for configurada, as gravações no cache de réplica serão gravadas nessa segunda conta de armazenamento. Para cada conta de armazenamento configurada, escolha a **Chave primária** ou **Chave secundária** a ser usada na lista suspensa **Chave de Armazenamento**. 

> [!IMPORTANT]
> Se a chave de armazenamento para a sua conta de persistência for regenerada, você deverá reconfigurar a chave desejada no menu suspenso **Chave de Armazenamento**.
> 
> 

Quando a persistência de AOF estiver habilitada, as operações de gravação no cache serão salvas na conta de armazenamento designada (ou contas, se você tiver configurado uma segunda conta de armazenamento). No caso de uma falha catastrófica que desative o cache primário e de réplica, o log de AOF armazenado será usado para recriar o cache.

## <a name="persistence-faq"></a>Perguntas frequentes sobre persistência
A lista a seguir contém respostas para perguntas frequentes sobre a persistência do Cache Redis do Azure.

* [Posso habilitar a persistência em um cache criado anteriormente?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Posso habilitar a persistência de AOF e RDB ao mesmo tempo?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Qual modelo de persistência eu devo escolher?](#which-persistence-model-should-i-choose)
* [O que acontecerá se eu tiver dimensionado para um tamanho diferente e um backup for restaurado, que foi feito antes da operação de escala?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>Persistência de RDB
* [Posso alterar a frequência de backup de RDB depois de criar o cache?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Por que quando eu tenho uma frequência de backup de RDB de 60 minutos há mais de 60 minutos entre os backups?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [O que acontece com os backups de RDB antigos quando um backup novo é realizado?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>Persistência de AOF
* [Quando devo usar uma segunda conta de armazenamento?](#when-should-i-use-a-second-storage-account)
* [A persistência de AOF afeta a taxa de transferência, latência ou desempenho de meu cache?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Como posso remover a segunda conta de armazenamento?](#how-can-i-remove-the-second-storage-account)
* [O que é uma regravação e como ela afeta meu cache?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [O que devo esperar ao dimensionar um cache com o AOF habilitado?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Como os dados de AOF são organizados no armazenamento?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Posso habilitar a persistência em um cache criado anteriormente?
Sim, a persistência do Redis pode ser configurada na criação do cache e em caches premium existentes.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Posso habilitar a persistência de AOF e RDB ao mesmo tempo?

Não, você pode habilitar apenas RDB ou AOF, mas não ambos ao mesmo tempo.

### <a name="which-persistence-model-should-i-choose"></a>Qual modelo de persistência eu devo escolher?

A persistência de AOF salva cada gravação em um log, o que afeta um pouco a taxa de transferência, em comparação com a persistência de RDB que salva os backups com base no intervalo de backup configurado, com impacto mínimo no desempenho. Escolha a persistência de AOF se o seu principal objetivo for minimizar a perda de dados, e você poderá manipular uma diminuição na taxa de transferência de seu cache. Escolha a persistência de RDB se você quiser manter a taxa de transferência ideal em seu cache, mas ainda quiser um mecanismo para recuperação de dados.

* Saiba mais sobre as [vantagens](https://redis.io/topics/persistence#rdb-advantages) e as [desvantagens](https://redis.io/topics/persistence#rdb-disadvantages) de persistência de RDB.
* Saiba mais sobre as [vantagens](https://redis.io/topics/persistence#aof-advantages) e as [desvantagens](https://redis.io/topics/persistence#aof-disadvantages) da persistência de AOF.

Para saber mais sobre o desempenho ao usar a persistência de AOF, veja [A persistência de AOF afeta a taxa de transferência, latência ou desempenho de meu cache?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>O que acontecerá se eu tiver dimensionado para um tamanho diferente e um backup for restaurado, que foi feito antes da operação de escala?

Para a persistência de RDB e AOF:

* Se você tiver dimensionado para um tamanho maior, não haverá nenhum impacto.
* Se você tiver dimensionado para um tamanho menor e tem uma configuração personalizada dos [bancos de dados](cache-configure.md#databases) maior que o [limite dos bancos de dados](cache-configure.md#databases) para o novo tamanho, os dados nesses bancos de dados não serão restaurados. Para obter mais informações, consulte [A configuração dos meus bancos de dados personalizados é afetada durante o dimensionamento?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Se você tiver dimensionado para um tamanho menor e não houver espaço suficiente no menor tamanho para conter todos os dados do último backup, as chaves serão removidas durante o processo de restauração, normalmente usando a política de remoção [allkeys-lru](https://redis.io/topics/lru-cache) .

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Posso alterar a frequência de backup de RDB depois de criar o cache?
Sim, você pode alterar a frequência de backup para persistência de RDB na folha **Persistência de dados do Redis**. Para obter instruções, confira Configurar a persistência do Redis.

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Por que quando eu tenho uma frequência de backup de RDB de 60 minutos há mais de 60 minutos entre os backups?
O intervalo da frequência de backup da persistência de RDB não é iniciado até que o processo de backup anterior seja concluído com êxito. Se a frequência de backup for de 60 minutos e usar um processo de backup de 15 minutos para concluir com êxito, o próximo backup não será iniciado até 75 minutos após a hora de início do backup anterior.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>O que acontece com os backups de RDB antigos quando um backup novo é realizado?
Todos os backups da persistência de RDB, exceto pelo mais recente, serão excluídos automaticamente. Essa exclusão pode não acontecer imediatamente, mas os backups mais antigos não são persistidos por tempo indeterminado.


### <a name="when-should-i-use-a-second-storage-account"></a>Quando devo usar uma segunda conta de armazenamento?

Você deve usar uma segunda conta de armazenamento para a persistência de AOF quando acreditar que tem mais operações de conjunto do que o esperado no cache.  Configurar a conta de armazenamento secundária ajuda a garantir que o cache não atinja os limites de largura de banda de armazenamento.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>A persistência de AOF afeta a taxa de transferência, latência ou desempenho de meu cache?

A persistência de AOF afeta a taxa de transferência aproximadamente em 15 a 20% quando o cache estiver abaixo da carga máxima (CPU e carga do servidor ambos abaixo de 90%). Não deve haver problemas de latência quando o cache estiver dentro desses limites. No entanto, o cache atingirá esses limites antes com o AOF habilitado.

### <a name="how-can-i-remove-the-second-storage-account"></a>Como posso remover a segunda conta de armazenamento?

Você pode remover a conta de armazenamento secundária de persistência de AOF definindo a segunda conta de armazenamento como a mesma que a primeira conta de armazenamento. Para obter instruções, veja [Configurar a persistência de AOF](#configure-aof-persistence).

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>O que é uma regravação e como ela afeta meu cache?

Quando o arquivo AOF fica muito grande, uma regeneração é automaticamente enfileirada no cache. A regeneração redimensiona o arquivo AOF com o conjunto mínimo de operações necessárias para criar o conjunto de dados atual. Durante a regeneração, espere alcançar os limites de desempenho antes, especialmente ao lidar com grandes conjuntos de dados. As regravações ocorrem com menos frequência à medida que o arquivo de AOF fica maior, mas demorarão muito mais quando isso acontecer.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>O que devo esperar ao dimensionar um cache com o AOF habilitado?

Se o arquivo de AOF no momento do dimensionamento for muito grande, espere que a operação de dimensionamento demore mais do que o esperado, pois ela recarregará o arquivo após a conclusão do dimensionamento.

Para saber mais sobre dimensionamento, confira [O que acontecerá se eu tiver dimensionado para um tamanho diferente e um backup for restaurado, que foi feito antes da operação de escala?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Como os dados de AOF são organizados no armazenamento?

Os dados armazenados em arquivos AOF são divididos em vários blobs de página por nó a fim de aumentar o desempenho de gravação dos dados no armazenamento. A tabela a seguir exibe quantos blobs de página são usados para cada tipo de preço:

| Camada premium | Blobs |
|--------------|-------|
| P1           | 4 por fragmento    |
| P2           | 8 por fragmento    |
| P3           | 16 por fragmento   |
| P4           | 20 por fragmento   |

Quando o clustering estiver habilitado, cada fragmento no cache terá seu próprio conjunto de blobs de página, conforme indicado na tabela anterior. Por exemplo, um cache de P2 com três fragmentos distribui seu arquivo AOF em 24 blobs de páginas (oito blobs por fragmento, com três fragmentos).

Após uma regeneração, dois conjuntos de arquivos AOF existirão no armazenamento. As regenerações ocorrem em segundo plano e acrescentam ao primeiro conjunto de arquivos, enquanto as operações de conjunto que são enviadas ao cache durante a regeneração acrescentam ao segundo conjunto. Um backup é armazenado temporariamente durante regenerações no caso de falha, mas é excluída imediatamente após a conclusão de uma regeneração.


## <a name="next-steps"></a>Próximas etapas
Aprenda a usar mais recursos de cache premium.

* [Introdução à camada Premium do Cache Redis do Azure](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
