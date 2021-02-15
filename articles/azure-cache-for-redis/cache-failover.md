---
title: Failover e aplicação de patches – Cache Redis
description: Saiba mais sobre failover, aplicação de patch e o processo de atualização para o cache do Azure para Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: 7cfa7257e64421c30c359bb34044988bbb5af1dd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87093078"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Failover e aplicação de patch para o cache do Azure para Redis

Para criar aplicativos cliente resilientes e com êxito, é essencial entender o failover no contexto do cache do Azure para o serviço Redis. Um failover pode fazer parte de operações de gerenciamento planejadas ou pode ser causado por falhas de rede ou de hardware não planejadas. Um uso comum do failover de cache é quando o serviço de gerenciamento corrige o cache do Azure para binários Redis. Este artigo aborda o que é um failover, como ele ocorre durante a aplicação de patches e como criar um aplicativo cliente resiliente.

## <a name="what-is-a-failover"></a>O que é um failover?

Vamos começar com uma visão geral do failover para o cache do Azure para Redis.

### <a name="a-quick-summary-of-cache-architecture"></a>Um resumo rápido da arquitetura de cache

Um cache é construído de várias máquinas virtuais com endereços IP privados e separados. Cada máquina virtual, também conhecida como um nó, é conectada a um balanceador de carga compartilhado com um único endereço IP virtual. Cada nó executa o processo do servidor Redis e é acessível por meio do nome do host e das portas Redis. Cada nó é considerado um nó primário ou de réplica. Quando um aplicativo cliente se conecta a um cache, seu tráfego passa por esse balanceador de carga e é automaticamente roteado para o nó primário.

Em um cache básico, o único nó é sempre um primário. Em um cache Standard ou Premium, há dois nós: um é escolhido como o primário e o outro é a réplica. Como os caches padrão e Premium têm vários nós, um nó pode estar indisponível enquanto o outro continua a processar solicitações. Os caches clusterizados são compostos de muitos fragmentos, cada um com nós primários e de réplica distintos. Um fragmento pode estar inoperante enquanto os outros permanecem disponíveis.

> [!NOTE]
> Um cache básico não tem vários nós e não oferece um SLA (contrato de nível de serviço) para sua disponibilidade. Os caches básicos são recomendados apenas para fins de desenvolvimento e teste. Use um cache Standard ou Premium para uma implantação de vários nós, para aumentar a disponibilidade.

### <a name="explanation-of-a-failover"></a>Explicação de um failover

Um failover ocorre quando um nó de réplica se promove a se tornar um nó primário, e o nó primário antigo fecha as conexões existentes. Depois que o nó primário é revertido, ele observa a alteração nas funções e se rebaixa para se tornar uma réplica. Em seguida, ele se conecta ao novo primário e sincroniza os dados. Um failover pode ser planejado ou não planejado.

Um *failover planejado* ocorre durante atualizações do sistema, como aplicação de patch de Redis ou atualizações de so, e operações de gerenciamento, como dimensionamento e reinicialização. Como os nós recebem aviso prévio da atualização, eles podem trocar funções de maneira cooperativa e atualizar rapidamente o balanceador de carga da alteração. Um failover planejado geralmente termina em menos de 1 segundo.

Um *failover não planejado* pode ocorrer devido a falha de hardware, falha de rede ou outras interrupções inesperadas no nó primário. O nó de réplica se promove a primário, mas o processo leva mais tempo. Um nó de réplica deve primeiro detectar que seu nó primário não está disponível antes de poder iniciar o processo de failover. O nó de réplica também deve verificar se essa falha não planejada não é transitória ou local, para evitar um failover desnecessário. Esse atraso na detecção significa que um failover não planejado geralmente termina dentro de 10 a 15 segundos.

## <a name="how-does-patching-occur"></a>Como ocorre a aplicação de patch?

O cache do Azure para o serviço Redis atualiza regularmente seu cache com os recursos e correções mais recentes da plataforma. Para corrigir um cache, o serviço segue estas etapas:

1. O serviço de gerenciamento seleciona um nó para ser corrigido.
1. Se o nó selecionado for um nó primário, o nó de réplica correspondente se promoverá de forma cooperativa. Essa promoção é considerada um failover planejado.
1. O nó selecionado é reinicializado para realizar as novas alterações e é retornado como um nó de réplica.
1. O nó de réplica conecta-se ao nó primário e sincroniza os dados.
1. Quando a sincronização de dados é concluída, o processo de aplicação de patch se repete para os nós restantes.

Como a aplicação de patch é um failover planejado, o nó de réplica se promove rapidamente a se tornar um primário e começa a atender solicitações e novas conexões. Os caches básicos não têm um nó de réplica e ficam indisponíveis até que a atualização seja concluída. Cada fragmento de um cache clusterizado é corrigido separadamente e não fecha as conexões com outro fragmento.

> [!IMPORTANT]
> Os nós são corrigidos um de cada vez para evitar a perda de dados. Os caches básicos terão perda de dados. Os caches clusterizados são corrigidos em um fragmento por vez.

Vários caches no mesmo grupo de recursos e região também são corrigidos um de cada vez.  Os caches que estão em diferentes grupos de recursos ou regiões diferentes podem ser corrigidos simultaneamente.

Como a sincronização de dados completa ocorre antes de o processo ser repetido, a perda de dados provavelmente ocorrerá quando você usar um cache Standard ou Premium. Você pode proteger ainda mais contra a perda de dados [exportando](cache-how-to-import-export-data.md#export) dados e habilitando a [persistência](cache-how-to-premium-persistence.md).

## <a name="additional-cache-load"></a>Carregamento de cache adicional

Sempre que ocorre um failover, os caches padrão e Premium precisam replicar dados de um nó para o outro. Essa replicação causa um aumento de carga na memória do servidor e na CPU. Se a instância de cache já estiver muito carregada, os aplicativos cliente poderão enfrentar maior latência. Em casos extremos, os aplicativos cliente podem receber exceções de tempo limite. Para ajudar a reduzir o impacto dessa carga adicional, [defina](cache-configure.md#memory-policies) a configuração do cache `maxmemory-reserved` .

## <a name="how-does-a-failover-affect-my-client-application"></a>Como um failover afeta meu aplicativo cliente?

O número de erros vistos pelo aplicativo cliente depende de quantas operações estavam pendentes na conexão no momento do failover. Qualquer conexão que é roteada através do nó que fechou suas conexões verá erros. Muitas bibliotecas de cliente podem gerar diferentes tipos de erros quando as conexões são interrompidas, incluindo exceções de tempo limite, exceções de conexão ou exceções de soquete. O número e o tipo de exceções dependem de onde no caminho do código a solicitação é quando o cache fecha suas conexões. Por exemplo, uma operação que envia uma solicitação, mas não recebeu uma resposta quando o failover ocorrer pode receber uma exceção de tempo limite. Novas solicitações no objeto de conexão fechado recebem exceções de conexão até que a reconexão ocorra com êxito.

A maioria das bibliotecas de cliente tenta se reconectar ao cache se eles estiverem configurados para fazer isso. No entanto, os bugs imprevistos podem ocasionalmente posicionar os objetos de biblioteca em um estado irrecuperável. Se os erros persistirem por mais tempo do que um período pré-configurado, o objeto de conexão deverá ser recriado. No Microsoft.NET e em outras linguagens orientadas a objeto, a recriação da conexão sem reiniciar o aplicativo pode ser realizada usando [um \<T\> padrão lento](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#reconnecting-with-lazyt-pattern).

### <a name="how-do-i-make-my-application-resilient"></a>Como fazer tornar meu aplicativo resiliente?

Como não é possível evitar completamente os failovers, grave seus aplicativos cliente para obter resiliência para quebras de conexão e solicitações com falha. Embora a maioria das bibliotecas de cliente Reconecte-se automaticamente ao ponto de extremidade do cache, algumas delas tentam repetir solicitações com falha. Dependendo do cenário do aplicativo, pode fazer sentido usar a lógica de repetição com retirada.

Para testar a resiliência de um aplicativo cliente, use uma [reinicialização](cache-administration.md#reboot) como um gatilho manual para quebras de conexão. Além disso, recomendamos que você [agende as atualizações](cache-administration.md#schedule-updates) em um cache. Peça ao serviço de gerenciamento para aplicar patches de tempo de execução Redis durante as janelas semanais especificadas. Essas janelas normalmente são períodos em que o tráfego do aplicativo cliente é baixo, para evitar possíveis incidentes.

### <a name="client-network-configuration-changes"></a>Rede do cliente-alterações de configuração

Determinadas alterações de configuração de rede do lado do cliente podem disparar erros "nenhuma conexão disponível". Essas alterações podem incluir:

- Trocando o endereço IP virtual de um aplicativo cliente entre os slots de preparo e de produção.
- Dimensionando o tamanho ou o número de instâncias do seu aplicativo.

Essas alterações podem causar um problema de conectividade que dura menos de um minuto. Seu aplicativo cliente provavelmente perderá sua conexão com outros recursos de rede externa, além do cache do Azure para o serviço Redis.

## <a name="next-steps"></a>Próximas etapas

- [Agendar atualizações](cache-administration.md#schedule-updates) para seu cache.
- Teste a resiliência do aplicativo usando uma [reinicialização](cache-administration.md#reboot).
- [Configure](cache-configure.md#memory-policies) as reservas e as políticas de memória.
