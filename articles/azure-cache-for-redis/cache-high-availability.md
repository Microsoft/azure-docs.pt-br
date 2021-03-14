---
title: Alta disponibilidade para o cache do Azure para Redis
description: Saiba mais sobre o cache do Azure para opções e recursos de alta disponibilidade do Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: ad6696fc4fe2af7047c25a3a9c260d3b12588ee2
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203293"
---
# <a name="high-availability-for-azure-cache-for-redis"></a>Alta disponibilidade para o cache do Azure para Redis

O cache do Azure para Redis tem alta disponibilidade interna. O objetivo de sua arquitetura de alta disponibilidade é garantir que sua instância Redis gerenciada esteja funcionando mesmo quando suas VMs (máquinas virtuais) subjacentes forem afetadas por interrupções planejadas ou não planejadas. Ele fornece taxas de percentual muito maiores do que o que é atingível ao hospedar Redis em uma única VM.

O cache do Azure para Redis implementa alta disponibilidade usando várias VMs, chamadas de *nós*, para um cache. Ele configura esses nós de modo que a replicação de dados e o failover ocorram no modos coordenado. Ele também orquestra operações de manutenção, como a aplicação de patches de software Redis. Várias opções de alta disponibilidade estão disponíveis nas camadas Standard, Premium e Enterprise:

| Opção | Descrição | Disponibilidade | Standard | Premium | Enterprise |
| ------------------- | ------- | ------- | :------: | :---: | :---: |
| [Replicação padrão](#standard-replication)| Configuração replicada de nó duplo em um único datacenter com failover automático | 99,9% |✔|✔|-|
| [Redundância de zona](#zone-redundancy) | Configuração replicada de vários nós em AZs, com failover automático | 99,95% (camada Premium), 99,99% (camadas Enterprise) |-|Visualização|Visualização|
| [Replicação geográfica](#geo-replication) | Instâncias de cache vinculadas em duas regiões, com failover controlado pelo usuário | 99,999% (camada Enterprise) |-|✔|Visualização|

## <a name="standard-replication"></a>Replicação padrão

Um cache do Azure para Redis na camada Standard ou Premium é executado em um par de servidores Redis por padrão. Os dois servidores são hospedados em VMs dedicadas. O Redis de código-fonte aberto permite que apenas um servidor manipule solicitações de gravação de dados. Esse servidor é o nó *primário* , enquanto a outra *réplica*. Depois de provisionar os nós de servidor, o cache do Azure para Redis atribui funções primárias e de réplica a eles. O nó primário geralmente é responsável por atender a gravação, bem como solicitações de leitura de clientes Redis. Em uma operação de gravação, ele confirma uma nova chave e uma atualização de chave para sua memória interna e responde imediatamente ao cliente. Ele encaminha a operação para a réplica de forma assíncrona.

:::image type="content" source="media/cache-high-availability/replication.png" alt-text="Configuração de replicação de dados":::
   
>[!NOTE]
>Normalmente, um cliente Redis se comunica com o nó primário em um cache Redis para todas as solicitações de leitura e gravação. Determinados clientes Redis podem ser configurados para leitura a partir do nó de réplica.
>
>

Se o nó primário em um cache Redis estiver indisponível, a réplica se promoverá para se tornar o novo primário automaticamente. Esse processo é chamado de *failover*. A réplica aguardará por tempo suficiente antes de assumir o caso de o nó primário se recuperar rapidamente. Quando ocorre um failover, o cache do Azure para Redis provisiona uma nova VM e a une ao cache como o nó de réplica. A réplica executa uma sincronização de dados completa com o primário para que ele tenha outra cópia dos dados de cache.

Um nó primário pode sair do serviço como parte de uma atividade de manutenção planejada, como Redis software ou atualização do sistema operacional. Ele também pode parar de funcionar devido a eventos não planejados, como falhas em hardware, software ou rede subjacentes. [Failover e aplicação de patch para o cache do Azure para Redis](cache-failover.md) fornece uma explicação detalhada sobre os tipos de failovers do Redis. Um cache do Azure para Redis passará por vários failovers durante seu tempo de vida. A arquitetura de alta disponibilidade foi projetada para tornar essas alterações dentro de um cache tão transparente para seus clientes quanto possível.

>[!NOTE]
>O seguinte está disponível como uma visualização.
>
>

Além disso, o cache do Azure para Redis permite nós de réplica adicionais na camada Premium. Um [cache de várias réplicas](cache-how-to-multi-replicas.md) pode ser configurado com até três nós de réplica. Ter mais réplicas geralmente melhora a resiliência devido aos nós adicionais que fazem backup do primário. Mesmo com mais réplicas, um cache do Azure para instância Redis ainda pode ser seriamente impactado por uma interrupção de nível de Datacenter ou AZ. Você pode aumentar a disponibilidade do cache usando várias réplicas em conjunto com a [redundância de zona](#zone-redundancy).

## <a name="zone-redundancy"></a>Redundância de zona

O cache do Azure para Redis dá suporte a configurações com redundância de zona nas camadas Premium e Enterprise. Um [cache com redundância de zona](cache-how-to-zone-redundancy.md) pode colocar seus nós entre diferentes [zonas de disponibilidade do Azure](../availability-zones/az-overview.md) na mesma região. Ele elimina o datacenter ou AZ a interrupção como um ponto único de falha e aumenta a disponibilidade geral do cache.

### <a name="premium-tier"></a>Camada premium

>[!NOTE]
>Isso está disponível como uma visualização.
>
>

O diagrama a seguir ilustra a configuração de zona redundante para a camada Premium:

:::image type="content" source="media/cache-high-availability/zone-redundancy.png" alt-text="Configuração de redundância de zona":::
   
O cache do Azure para Redis distribui os nós em um cache com redundância de zona em um modo Round Robin sobre o AZs que você selecionou. Ele também determina qual nó servirá como o primário inicialmente.

Um cache com redundância de zona fornece failover automático. Quando o nó primário atual não estiver disponível, uma das réplicas assumirá. Seu aplicativo poderá apresentar um tempo de resposta de cache maior se o novo nó primário estiver localizado em um AZ diferente. AZs estão geograficamente separados. Alternar de um AZ para outro altera a distância física entre onde o aplicativo e o cache estão hospedados. Essa alteração afeta as latências de rede de ida e volta de seu aplicativo para o cache. Espera-se que a latência extra se enquadrasse em um intervalo aceitável para a maioria dos aplicativos. Recomendamos que você teste seu aplicativo para garantir que ele possa funcionar bem com um cache com redundância de zona.

### <a name="enterprise-tiers"></a>Camadas Enterprise

Um cache em uma camada corporativa é executado em um cluster do Redis Enterprise. Ele requer um número ímpar de nós de servidor em todos os momentos para formar um quorum. Por padrão, ele é composto por três nós, cada um hospedado em uma VM dedicada. Um cache corporativo tem dois *nós de dados* de mesmo tamanho e um *nó de quorum* menor. Um cache flash corporativo tem três nós de dados de tamanho igual. O cluster corporativo divide os dados do Redis em partições internamente. Cada partição tem um *primário* e pelo menos uma *réplica*. Cada nó de dados contém uma ou mais partições. O cluster corporativo garante que as réplicas e primárias de qualquer partição nunca sejam colocalizadas no mesmo nó de dados. As partições replicam dados de forma assíncrona de primárias para suas réplicas correspondentes.

Quando um nó de dados fica indisponível ou ocorre uma divisão de rede, um failover semelhante ao descrito na [replicação padrão](#standard-replication) ocorre. O cluster corporativo usa um modelo baseado em quorum para determinar quais nós sobreviventes participarão de um novo quorum. Ele também promove partições de réplica dentro desses nós para os primários, conforme necessário.

## <a name="geo-replication"></a>Replicação geográfica

A [replicação geográfica](cache-how-to-geo-replication.md) é um mecanismo para vincular dois ou mais cache do Azure para instâncias Redis, normalmente abrangendo duas regiões do Azure. 

### <a name="premium-tier"></a>Camada premium

>[!NOTE]
>A replicação geográfica na camada Premium foi projetada principalmente para recuperação de desastres.
>
>

Duas instâncias de cache de camada Premium podem ser conectadas por meio da [replicação geográfica](cache-how-to-geo-replication.md) para que você possa fazer backup dos dados de cache em uma região diferente. Uma vez vinculada, uma instância é designada como o cache vinculado primário e a outra como o cache vinculado secundário. Somente o cache primário aceita solicitações de leitura e gravação. Os dados gravados no cache primário são replicados para o cache secundário. Um aplicativo acessa o cache por meio de pontos de extremidade separados para os caches primários e secundários. O aplicativo deve enviar todas as solicitações de gravação para o cache primário quando ele é implantado em várias regiões do Azure. Ele pode ler do cache primário ou secundário. Em geral, você deseja que as instâncias de computação do aplicativo leiam dos caches mais próximos para reduzir a latência. A transferência de dados entre as duas instâncias de cache é protegida por TLS.

A replicação geográfica não fornece failover automático devido a questões sobre o tempo de ida e volta da rede adicionado entre regiões se o restante do seu aplicativo permanecer na região primária. Você precisará gerenciar e iniciar o failover desvinculando o cache secundário. Isso irá promovê-lo para ser a nova instância primária.

### <a name="enterprise-tiers"></a>Camadas Enterprise

>[!NOTE]
>Isso está disponível como uma visualização.
>
>

As camadas empresariais oferecem suporte a uma forma mais avançada de replicação geográfica, chamada [replicação geográfica ativa](cache-how-to-active-geo-replication.md). Aproveitando os tipos de dados replicados sem conflitos, o software do Redis Enterprise dá suporte a gravações em várias instâncias de cache e cuida da mesclagem de alterações e da resolução de conflitos, se necessário. Duas ou mais instâncias de cache de camada empresarial em diferentes regiões do Azure podem ser Unidas para formar um cache com replicação geográfica ativa. Um aplicativo que usa esse cache pode ler e gravar em instâncias de cache distribuídas geograficamente por meio de pontos de extremidade correspondentes. Ele deve usar o que é mais próximo de cada instância de computação, que oferece a menor latência. O aplicativo também precisa monitorar as instâncias de cache e alternar para outra região se uma das instâncias ficar indisponível. Para obter mais informações sobre como funciona a replicação geográfica ativa, consulte [Active-active Geo-Distriubtion (baseado em CRDTs)](https://redislabs.com/redis-enterprise/technology/active-active-geo-distribution/).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como configurar o cache do Azure para opções de alta disponibilidade do Redis.

* [Cache do Azure para camadas de serviço do Redis Premium](cache-overview.md#service-tiers)
* [Adicionar réplicas ao cache do Azure para Redis](cache-how-to-multi-replicas.md)
* [Habilitar a redundância de zona para o cache do Azure para Redis](cache-how-to-zone-redundancy.md)
* [Configurar a replicação geográfica para o cache do Azure para Redis](cache-how-to-geo-replication.md)
