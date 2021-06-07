---
title: O que é o Cache do Azure para Redis?
description: Saiba mais sobre o Cache do Azure para Redis para habilitar cache-aside, armazenamento em cache de conteúdo, armazenamento em cache de sessão do usuário, enfileiramento de trabalhos e mensagens e transações distribuídas.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 02/08/2021
ms.openlocfilehash: 49a697e6c3a6a6c931f2bb9c545647e2d6f1322d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056650"
---
# <a name="about-azure-cache-for-redis"></a>Sobre o Cache Redis do Azure
O Cache do Azure para Redis fornece um armazenamento de dados na memória com base no software [Redis](https://redis.io/). O Redis melhora o desempenho e a escalabilidade de um aplicativo que usa muito armazenamentos de dados de back-end. Ele é capaz de processar grandes volumes de solicitação de aplicativo mantendo os dados acessados com frequência na memória do servidor, que pode ser gravada e lida rapidamente. O Redis traz uma solução de armazenamento de dados crítica de baixa latência e alta taxa de transferência para aplicativos modernos.

O Cache do Azure para Redis oferece o OSS Redis (software livre do Redis) e um produto comercializado da Redis Labs (Redis Enterprise) como serviço gerenciado. Ele fornece instâncias de servidor Redis seguras e dedicadas e compatibilidade total com a API do Redis. O serviço é operador pela Microsoft, hospedado no Azure e acessível para qualquer aplicativo dentro ou fora do Azure.

O Cache do Azure para Redis pode ser usado como um cache de dados ou conteúdo distribuído, um repositório de sessão, um agente de mensagem e muito mais. Ele pode ser implantado de modo autônomo ou junto com outros serviços de banco de dados do Azure, como o SQL do Azure ou Cosmos DB.

## <a name="key-scenarios"></a>Principais cenários
O Cache do Azure para Redis aprimora o desempenho do aplicativo dando suporte a padrões de arquitetura de aplicativo comuns. Alguns dos mais comuns incluem o seguinte:

| Padrão      | Descrição                                        |
| ------------ | -------------------------------------------------- |
| [Cache de dados](cache-web-app-cache-aside-leaderboard.md) | Os bancos de dados geralmente são grandes demais para serem carregados diretamente em um cache. É comum usar o padrão [cache-aside](/azure/architecture/patterns/cache-aside) para carregar dados no cache, somente conforme necessário. Quando o sistema faz alterações nos dados, ele também pode atualizar o cache, que será distribuído a outros clientes. Além disso, o sistema pode definir uma expiração em dados ou usar uma política de remoção para disparar atualizações de dados para o cache.|
| [Cache de conteúdo](cache-aspnet-output-cache-provider.md) | Muitas páginas da Web são geradas com base em modelos que usam conteúdo estático, como cabeçalhos, rodapés e faixas. Esses itens estáticos não devem ser alterados com frequência. O uso de um cache na memória fornece acesso rápido a conteúdo estático em comparação com armazenamentos de dados de back-end. Esse padrão reduz o tempo de processamento e a carga do servidor, permitindo que servidores Web sejam mais responsivos. Ele pode permitir que você reduza o número de servidores necessários para lidar com cargas. O Cache do Azure para Redis fornece o Provedor de Cache de Saída do Redis para dar suporte a esse padrão com o ASP.NET.|
| [Repositório de sessão](cache-aspnet-session-state-provider.md) | Esse padrão é geralmente usado com carrinhos de compras e outros dados de histórico do usuário que um aplicativo Web pode desejar associar aos cookies do usuário. O armazenamento de muitas informações em um cookie pode ter um impacto negativo no desempenho conforme o tamanho do cookie aumenta e é passado e validado com cada solicitação. Uma solução típica usa o cookie como uma chave para consultar os dados em um banco de dados. O uso de um cache na memória, como o Cache do Azure para Redis, para associar as informações a um usuário é muito mais rápido que a interação com um banco de dados relacional completo. |
| Enfileiramento de mensagens e trabalhos | Aplicativos geralmente adicionam tarefas a uma fila quando as operações associadas à solicitação levam tempo para serem executadas. As operações de execução mais longa são enfileiradas para serem processadas em sequência, muitas vezes por outro servidor.  Esse método de adiamento do trabalho é chamado de enfileiramento de tarefas. O Cache do Azure para Redis fornece uma fila distribuída para habilitar esse padrão em seu aplicativo.|
| Transações distribuídas | Às vezes, os aplicativos exigem uma série de comandos em relação a um armazenamento de dados de back-end para serem executados como uma única operação atômica. Todos os comandos devem ter êxito ou ser revertidos para o estado inicial. O Cache do Azure para Redis dá suporte à execução de um lote de comandos como uma única [transação](https://redis.io/topics/transactions). |

## <a name="redis-versions"></a>Versão do Redis

O Cache do Azure para Redis dá suporte ao software livre do Redis versão 4.x e, como versão prévia, 6.0. Tomamos a decisão de ignorar o Redis 5.0 para oferecer a você a última versão. Anteriormente, o Cache do Azure para Redis mantinha apenas uma só versão do Redis. Ele fornecerá uma atualização de versão principal mais recente e, pelo menos, uma versão estável mais antiga no futuro. Você pode [escolher qual versão](cache-how-to-version.md) funciona melhor para seu aplicativo.


## <a name="service-tiers"></a>Camadas de serviço
O Cache do Azure para Redis está disponível nas seguintes camadas:

| Camada | Descrição |
|---|---|
| Basic | Um cache do software livre do Redis em execução em uma VM. Essa camada não tem nenhum SLA (contrato de nível de serviço) e é ideal para o desenvolvimento/teste e cargas de trabalho não críticas. |
| Standard | Um cache do software livre do Redis em execução em duas VMs em uma configuração replicada. |
| Premium | Caches do software livre do Redis de alto desempenho. Essa camada oferece maior taxa de transferência, latência mais baixa, melhor disponibilidade e mais recursos. Os caches Premium são implantados em VMs mais avançadas em comparação com as VMs para caches Básico ou Standard. |
| Enterprise | Caches de alto desempenho fornecidos pelo software Redis Enterprise da Redis Labs. Essa camada dá suporte a módulos do Redis, incluindo RediSearch, RedisBloom e RedisTimeSeries. Além disso, ele oferece uma disponibilidade ainda maior do que a camada Premium. |
| Enterprise Flash | Caches grandes econômicos do software Redis Enterprise da Redis Labs. Essa camada estende o armazenamento de dados do Redis para a memória não volátil, que é mais barata que o DRAM em uma VM. Ele reduz o custo de memória geral por GB. |

### <a name="feature-comparison"></a>Comparação de recursos
O [Preço do Cache do Azure para Redis](https://azure.microsoft.com/pricing/details/cache/) fornece uma comparação detalhada de cada camada. A seguinte tabela ajuda a descrever alguns dos recursos compatíveis por camada:

| Descrição do recurso | Basic | Standard | Premium | Enterprise | Enterprise Flash |
| ------------------- | :-----: | :------: | :---: | :---: | :---: |
| [Contrato de nível de serviço (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |-|✔|✔|✔|✔|
| Criptografia de dados |✔|✔|✔|✔|✔|
| [Isolamento da rede](cache-how-to-premium-vnet.md) |✔|✔|✔|✔|✔|
| [Dimensionamento](cache-how-to-scale.md) |✔|✔|✔|✔|✔|
| [Cluster de software livre](cache-how-to-premium-clustering.md) |-|-|✔|✔|✔|
| [Persistência de dados](cache-how-to-premium-persistence.md) |-|-|✔|Visualização|Visualização|
| [Redundância de zona](cache-how-to-zone-redundancy.md) |-|-|Visualização|✔|✔|
| [Replicação geográfica](cache-how-to-geo-replication.md) |-|-|✔|Visualização|Visualização|
| [Módulos](https://redis.io/modules) |-|-|-|✔|✔|
| [Importar/exportar](cache-how-to-import-export-data.md) |-|-|✔|✔|✔|
| [Atualizações agendadas](cache-administration.md#schedule-updates) |✔|✔|✔|-|-|

### <a name="choosing-the-right-tier"></a>Escolher a camada correta
Você deve considerar o seguinte ao escolher uma camada do Cache do Azure para Redis:

* **Memória**: as camadas Básica e Standard oferecem 250 MB a 53 GB; a camada Premium 6 GB a 1,2 TB; as camadas Enterprise de 12 GB a 14 TB.  Para criar um cache de camada Premium maior que 120 GB, você pode usar o clustering do software livre do Redis. Para obter mais informações, consulte [Preço do Cache do Azure para Redis](https://azure.microsoft.com/pricing/details/cache/). Para saber mais, confira [Como configurar o clustering para um Cache do Azure para Redis Premium](cache-how-to-premium-clustering.md).
* **Desempenho**: os caches nas camadas Premium e Enterprise são implantados no hardware que tem processadores mais rápidos e que oferece um melhor desempenho quando comparado com as camadas Básica ou Standard. Os Caches da camada Premium têm a taxa de transferência mais alta e as latências mais baixas. Para obter mais informações, confira [Desempenho do Cache do Azure para Redis](cache-planning-faq.md#azure-cache-for-redis-performance).
* **Núcleo dedicado para o servidor do Redis**: todos os caches, exceto o C0 executam núcleos de VM dedicados. o Redis, por design, usa apenas um thread para processamento de comando. O Cache do Azure para Redis utiliza núcleos adicionais para processamento de E/S. Ter mais núcleos melhora o desempenho da taxa de transferência, embora possa não produzir dimensionamento linear. Além disso, tamanhos de VM maiores normalmente têm limites de largura de banda maiores do que os menores. Isso ajuda a evitar a saturação da rede, o que causará tempos limite em seu aplicativo.
* **Desempenho de rede**: se você tiver uma carga de trabalho que exija uma alta taxa de transferência, a camada Premium ou Enterprise oferece mais largura de banda quando comparada com as camadas Básica ou Standard. Também dentro de cada camada, caches de tamanhos maiores têm mais largura de banda, devido à VM subjacente que hospeda o cache. Para obter mais informações, confira [Desempenho do Cache do Azure para Redis](cache-planning-faq.md#azure-cache-for-redis-performance).
* **Quantidade máxima de conexões de cliente**: a camada Premium oferece o número máximo de clientes que podem se conectar ao Redis, com um número maior de conexões para caches de tamanhos maiores. O clustering não aumenta o número de conexões disponíveis para um cache em cluster. Para obter mais informações, confira [Preços do Cache do Azure para Redis](https://azure.microsoft.com/pricing/details/cache/).
* **Alta disponibilidade**: o Cache do Azure para Redis fornece várias opções de [alta disponibilidade](cache-high-availability.md). Ele garante que um cache Standard, Premium ou Enterprise esteja disponível conforme nosso [SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). o SLA abrange apenas a conectividade com os pontos de extremidade do cache. O SLA não abrange a proteção contra perda de dados. Recomendamos usar o recurso de persistência de dados do Redis nas camadas Premium e Enterprise para aumentar a resiliência contra a perda de dados.
* **Persistência de dados**: as camadas Premium e Enterprise permitem que você mantenha os dados do cache em uma conta do Armazenamento do Azure e em um Disco Gerenciado respectivamente. Problemas de infraestrutura subjacente podem resultar em uma possível perda de dados. Recomendamos usar o recurso de persistência de dados do Redis nessas camadas para aumentar a resiliência contra a perda de dados. O Cache do Azure para Redis oferece as opções RDB e AOF (versão prévia). A persistência de dados pode ser habilitada por meio do portal do Azure e da CLI. Para a camada Premium, confira [Como configurar a persistência para um Cache do Azure para Redis Premium](cache-how-to-premium-persistence.md).
* **Isolamento de rede**: as implantações de VNET (Rede Virtual) e do Link Privado do Azure fornecem segurança e isolamento de tráfego aprimorados para seu Cache do Azure para Redis. A VNET permite restringir ainda mais o acesso por meio de políticas de controle de acesso à rede. Para obter mais informações, confira o [Cache do Azure para Redis com o Link Privado do Azure](cache-private-link.md) e [Como configurar o suporte da Rede Virtual para um Cache do Azure para Redis Premium](cache-how-to-premium-vnet.md).
* **Extensibilidade**: as camadas Enterprise dão suporte a [RediSearch](https://docs.redislabs.com/latest/modules/redisearch/), [RedisBloom](https://docs.redislabs.com/latest/modules/redisbloom/) e [RedisTimeSeries](https://docs.redislabs.com/latest/modules/redistimeseries/). Esses módulos adicionam novos tipos de dados e funcionalidades ao Redis.

Você pode dimensionar o cache da camada Básica até a Premium após a criação dele. No momento, não há suporte para a redução para uma camada inferior. Para obter instruções sobre dimensionamento passo a passo, confira [Como dimensionar o Cache do Azure para Redis](cache-how-to-scale.md) e [Como automatizar uma operação de dimensionamento](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="special-considerations-for-enterprise-tiers"></a>Considerações especiais para as camadas Enterprise

As camadas Enterprise contam com o Redis Enterprise, uma versão comercial do Redis da Redis Labs. Os clientes obterão e pagarão por uma licença para esse software por meio de uma oferta do Azure Marketplace. O Cache do Azure para Redis facilitarão a aquisição da licença para que você não precisa fazer isso separadamente. Para fazer compras no Azure Marketplace, você precisa ter os seguintes pré-requisitos:
* Sua assinatura do Azure tem um meio de pagamento válido. Não há suporte para créditos Azure ou assinaturas do MSDN gratuitas.
* Sua organização permite [compras no Azure Marketplace](../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases).
* Se você usar um Marketplace particular, ele precisará conter a oferta do Redis Labs Enterprise.

> [!IMPORTANT]
> O Cache do Azure para Redis Enterprise requer Load Balancers de rede Standard que são cobrados separadamente das instâncias de cache propriamente ditas. Confira os [preços de Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/) para obter mais detalhes. Se um cache Enterprise estiver configurado para várias Zonas de Disponibilidade, a transferência de dados será cobrada com as [tarifas de largura de banda de rede Standard](https://azure.microsoft.com/pricing/details/bandwidth/) a partir de 1º de julho de 2021.
>
> Além disso, a persistência de dados adiciona Managed Disks. O uso desses recursos será gratuito durante a versão prévia pública da persistência de dados Enterprise. Isso poderá mudar quando o recurso entrar em disponibilidade geral.
>
>

## <a name="next-steps"></a>Próximas etapas
* [Criar um Cache Redis de software livre](quickstart-create-redis.md)
* [Criar um cache Redis Enterprise](quickstart-create-redis-enterprise.md)
* [Usar o Cache do Azure para Redis em um aplicativo Web ASP.NET](cache-web-app-howto.md)
* [Usar o Cache do Azure para Redis no .NET Core](cache-dotnet-core-quickstart.md)
* [Usar o Cache do Azure para Redis no .NET Framework](cache-dotnet-how-to-use-azure-redis-cache.md)
* [Usar o Cache do Azure para Redis no Node.js](cache-nodejs-get-started.md)
* [Usar o Cache do Azure para Redis no Java](cache-java-get-started.md)
* [Usar o Cache do Azure para Redis no Python](cache-python-get-started.md)