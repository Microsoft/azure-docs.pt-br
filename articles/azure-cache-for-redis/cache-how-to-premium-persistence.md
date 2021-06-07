---
title: Configurar a persistência de dados-cache Premium do Azure para Redis
description: Saiba como configurar e gerenciar a persistência de dados para as instâncias da camada Premium do Cache Redis do Azure
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 9d11d17f90dcd6335fcaf6bd48a44037777a087e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601377"
---
# <a name="configure-data-persistence-for-a-premium-azure-cache-for-redis-instance"></a>Configurar a persistência de dados para um cache do Azure Premium para a instância do Redis

A [persistência do Redis](https://redis.io/topics/persistence) permite persistir os dados armazenados no Redis. Você também pode tirar instantâneos e fazer backup dos dados, que podem ser carregados em caso de falha de hardware. Essa é uma enorme vantagem em relação às camadas Básica ou Standard, em que todos os dados são armazenados na memória e pode haver uma possível perda de dados em caso de falha quando os nós do Cache estiverem inativos. 

O Cache Redis do Azure oferece a persistência de Redis usando os seguintes modelos:

* **Persistência de RDB** – quando a persistência de RDB (banco de dados Redis) é configurada, o cache do Azure para Redis persiste um instantâneo do cache do Azure para Redis em um formato binário Redis para o disco (em uma conta de armazenamento do Azure) com base em uma frequência de backup configurável. Se ocorrer um desastre que desabilite os caches primário e de réplica, o cache será reconstruído com o uso do instantâneo mais recente. Saiba mais sobre as [vantagens](https://redis.io/topics/persistence#rdb-advantages) e as [desvantagens](https://redis.io/topics/persistence#rdb-disadvantages) de persistência de RDB.
* **Persistência de AOF**: quando a persistência de AOF (Arquivo somente para anexação) está configurada, o Cache Redis do Azure salva todas as operações de gravação em um log salvo pelo menos uma vez por segundo em uma conta de Armazenamento do Azure. Se ocorrer um desastre que desabilite os caches primário e de réplica, o cache será reconstruído com as operações de gravação mais recentes. Saiba mais sobre as [vantagens](https://redis.io/topics/persistence#aof-advantages) e as [desvantagens](https://redis.io/topics/persistence#aof-disadvantages) da persistência de AOF.

A persistência grava dados do Redis em uma conta de armazenamento do Azure que você possui e gerencia. Você pode configurar a partir da folha **novo cache do Azure para Redis** durante a criação do cache e no **menu de recursos** para os caches Premium existentes.

> [!NOTE]
> 
> O armazenamento do Azure criptografa automaticamente os dados quando eles são persistidos. Você pode usar suas próprias chaves para a criptografia. Para obter mais informações, consulte [chaves gerenciadas pelo cliente com Azure Key Vault](../storage/common/storage-service-encryption.md).
> 
> 

## <a name="set-up-data-persistence"></a>Configurar a persistência de dados

1. Para criar um cache Premium, entre no [portal do Azure](https://portal.azure.com) e selecione **criar um recurso**. Além de criar caches no portal do Azure, você também pode criá-los usando os modelos do Resource Manager, PowerShell ou CLI do Azure. Para obter mais informações sobre a criação de um Cache do Azure para Redis, consulte [Criar um cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Criar recurso.":::
   
2. Na página **Novo**, selecione **Bancos de dados** e, em seguida, **Cache do Azure para Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Selecionar o Cache do Azure para Redis.":::

3. Na página **novo cache Redis** , defina as configurações para seu novo cache Premium.
   
   | Configuração      | Valor sugerido  | DESCRIÇÃO |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nome DNS** | Insira um nome global exclusivo. | O nome do cache deve ser uma cadeia entre 1 e 63 caracteres que contenham apenas números, letras ou hifens. O nome precisa começar e terminar com um número ou uma letra e não pode conter hifens consecutivos. O *nome do host* de sua instância de cache será *\<DNS name>.redis.cache.windows.net*. | 
   | **Assinatura** | Na lista suspensa e selecione sua assinatura. | A assinatura na qual essa nova instância do Cache do Azure para Redis será criada. | 
   | **Grupo de recursos** | Menu suspenso e selecione um grupo de recursos ou selecione **criar novo** e insira um novo nome de grupo de recursos. | Nome do grupo de recursos no qual o cache e outros recursos serão criados. Ao colocar todos os seus recursos de aplicativos em um só grupo de recursos, você pode gerenciá-los ou excluí-los juntos com facilidade. | 
   | **Localidade** | Menu suspenso e selecione um local. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que usarão o cache. |
   | **Tipo de cache** | Menu suspenso e selecione um cache Premium para configurar os recursos premium. Para obter detalhes, veja [preços do cache do Azure para Redis](https://azure.microsoft.com/pricing/details/cache/). |  O tipo de preço determina o tamanho, o desempenho e os recursos disponíveis para o cache. Para obter mais informações, confira [Visão geral do Cache do Azure para Redis](cache-overview.md). |

4. Selecione a guia **Rede** ou clique no botão **Rede** na parte inferior da página.

5. Na guia **Rede**, escolha o método de conectividade. Para instâncias de cache Premium, você pode se conectar de forma pública, por meio de endereços IP públicos ou pontos de extremidade de serviço, ou, em particular, usando um ponto de extremidade privado.

6. Selecione **Próximo: Avançado** ou clique no botão **Próximo: Avançado** na parte inferior da página.

7. Na guia **avançado** de uma instância de cache Premium, defina as configurações para porta não TLS, clustering e persistência de dados. Para persistência de dados, você pode escolher a persistência de **RDB** ou **AoF** . 

8. Para habilitar a persistência de RDB, clique em **RDB** e defina as configurações. 
   
   | Configuração      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Frequência de backup** | Na lista suspensa e selecione um intervalo de backup, as opções incluem **15 minutos**, **30 minutos**, **60 minutos**, **6 horas**, **12 horas** e **24 horas**. | Esse intervalo inicia a contagem regressiva depois que a operação de backup anterior for concluída com êxito e quando ela expira, um novo backup é iniciado. | 
   | **Conta de armazenamento** | Na lista suspensa e selecione sua conta de armazenamento. | Você deve escolher uma conta de armazenamento na mesma região e assinatura que o cache, e uma conta de **armazenamento Premium** é recomendada porque o armazenamento Premium tem maior taxa de transferência.  | 
   | **Chave de Armazenamento** | Clique na lista suspensa e escolha a chave **primária** ou **secundária** a ser usada. | Se a chave de armazenamento para a sua conta de persistência for regenerada, você deverá reconfigurar a chave desejada no menu suspenso **Chave de Armazenamento**. | 

    O primeiro backup é iniciado quando o intervalo de frequência de backup expira.
    
   > [!NOTE]
   > Quando é feito backup dos arquivos RDB para o armazenamento, eles são armazenados na forma de blobs de páginas.

9. Para habilitar a persistência do AOF, clique em **AoF** e defina as configurações. 
   
   | Configuração      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Primeira conta de armazenamento** | Na lista suspensa e selecione sua conta de armazenamento. | Essa conta de armazenamento deve estar na mesma região e assinatura que o cache, e uma conta de **armazenamento Premium** é recomendada porque o armazenamento Premium tem maior taxa de transferência. | 
   | **Primeira chave de armazenamento** | Clique na lista suspensa e escolha a chave **primária** ou **secundária** a ser usada. | Se a chave de armazenamento para a sua conta de persistência for regenerada, você deverá reconfigurar a chave desejada no menu suspenso **Chave de Armazenamento**. | 
   | **Segunda conta de armazenamento** | Adicional Na lista suspensa e selecione sua conta de armazenamento secundária. | Opcionalmente, você pode configurar uma conta de armazenamento adicional. Se uma segunda conta de armazenamento for configurada, as gravações no cache de réplica serão gravadas nessa segunda conta de armazenamento. | 
   | **Segunda chave de armazenamento** | Adicional Clique na lista suspensa e escolha a chave **primária** ou **secundária** a ser usada. | Se a chave de armazenamento para a sua conta de persistência for regenerada, você deverá reconfigurar a chave desejada no menu suspenso **Chave de Armazenamento**. | 

    Quando a persistência de AOF estiver habilitada, as operações de gravação no cache serão salvas na conta de armazenamento designada (ou contas, se você tiver configurado uma segunda conta de armazenamento). No caso de uma falha catastrófica que desative o cache primário e de réplica, o log de AOF armazenado será usado para recriar o cache.

10. Selecione **Próximo: Marcas** ou clique no botão **Próximo: Botão** Categorias na parte inferior da página.

11. Opcionalmente, na guia **Marcas**, insira o nome e o valor caso deseje categorizar o recurso. 

12. Selecione **Examinar + criar**. Você será levado para a guia Examinar + criar, na qual o Azure validará a configuração.

13. Depois que a mensagem em verde Validação aprovada for exibida, selecione **Criar**.

A criação do cache demora um pouco. Monitore o progresso na página **Visão Geral** do Cache do Azure para Redis. Quando o **Status** for mostrado como **Em execução**, o cache estará pronto para uso. 

## <a name="persistence-faq"></a>Perguntas frequentes sobre persistência
A lista a seguir contém respostas para perguntas frequentes sobre a persistência do Cache Redis do Azure.

* [Posso habilitar a persistência em um cache criado anteriormente?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Posso habilitar a persistência de AOF e RDB ao mesmo tempo?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Qual modelo de persistência eu devo escolher?](#which-persistence-model-should-i-choose)
* [O que acontecerá se eu tiver dimensionado para um tamanho diferente e um backup for restaurado, que foi feito antes da operação de escala?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)
* [Posso usar a mesma conta de armazenamento para persistência em dois caches diferentes?](#can-i-use-the-same-storage-account-for-persistence-across-two-different-caches)
* [Serei cobrado pelo armazenamento que está sendo usado na persistência de dados](#will-i-be-charged-for-the-storage-being-used-in-data-persistence)

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

### <a name="can-i-use-the-same-storage-account-for-persistence-across-two-different-caches"></a>Posso usar a mesma conta de armazenamento para persistência em dois caches diferentes?
Sim, você pode usar a mesma conta de armazenamento para persistência em dois caches diferentes

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Posso alterar a frequência de backup de RDB depois de criar o cache?
Sim, você pode alterar a frequência de backup para persistência de RDB na folha de **persistência de dados** . Para obter instruções, confira Configurar a persistência do Redis.

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Por que quando eu tenho uma frequência de backup de RDB de 60 minutos há mais de 60 minutos entre os backups?
O intervalo da frequência de backup da persistência de RDB não é iniciado até que o processo de backup anterior seja concluído com êxito. Se a frequência de backup for de 60 minutos e usar um processo de backup de 15 minutos para concluir com êxito, o próximo backup não será iniciado até 75 minutos após a hora de início do backup anterior.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>O que acontece com os backups de RDB antigos quando um backup novo é realizado?
Todos os backups da persistência de RDB, exceto pelo mais recente, serão excluídos automaticamente. Essa exclusão pode não acontecer imediatamente, mas os backups mais antigos não são persistidos por tempo indeterminado.


### <a name="when-should-i-use-a-second-storage-account"></a>Quando devo usar uma segunda conta de armazenamento?

Você deve usar uma segunda conta de armazenamento para a persistência de AOF quando acreditar que tem mais operações de conjunto do que o esperado no cache.  Configurar a conta de armazenamento secundária ajuda a garantir que o cache não atinja os limites de largura de banda de armazenamento.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>A persistência de AOF afeta a taxa de transferência, latência ou desempenho de meu cache?

A persistência de AOF afeta a taxa de transferência aproximadamente em 15 a 20% quando o cache estiver abaixo da carga máxima (CPU e carga do servidor ambos abaixo de 90%). Não deve haver problemas de latência quando o cache estiver dentro desses limites. No entanto, o cache atingirá esses limites antes com o AOF habilitado.

### <a name="how-can-i-remove-the-second-storage-account"></a>Como posso remover a segunda conta de armazenamento?

Você pode remover a conta de armazenamento secundária de persistência de AOF definindo a segunda conta de armazenamento como a mesma que a primeira conta de armazenamento. Para os caches existentes, a folha **persistência de dados** é acessada no **menu de recursos** do seu cache. Para desabilitar a persistência do AOF, clique em **desabilitado**.

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

### <a name="will-i-be-charged-for-the-storage-being-used-in-data-persistence"></a>Serei cobrado pelo armazenamento que está sendo usado na persistência de dados?

Sim, você será cobrado pelo armazenamento que está sendo usado de acordo com o modelo de preços da conta de armazenamento que está sendo usada.


## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o cache do Azure para recursos do Redis.

* [Cache do Azure para camadas de serviço do Redis Premium](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
