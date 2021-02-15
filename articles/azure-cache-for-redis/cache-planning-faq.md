---
title: Perguntas frequentes de planejamento do cache do Azure para Redis
description: Conheça as respostas a perguntas comuns que ajudam a planejar o cache do Azure para Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 398b567e173691068e6dd932e075d6598dcfe601
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537398"
---
# <a name="azure-cache-for-redis-planning-faqs"></a>Perguntas frequentes de planejamento do cache do Azure para Redis

Este artigo fornece respostas a perguntas comuns sobre como planejar o cache do Azure para Redis.

## <a name="common-questions-and-answers"></a>Perguntas comuns e respostas
Esta seção aborda as seguintes perguntas frequentes:

* [Desempenho do Cache do Azure para Redis](#azure-cache-for-redis-performance)
* [Em que região posso localizar meu cache?](#in-what-region-should-i-locate-my-cache)
* [Onde residem os dados em cache?](#where-do-my-cached-data-reside)
* [Como eu sou cobrado pelo Cache do Azure para Redis?](#how-am-i-billed-for-azure-cache-for-redis)
* [Posso usar o cache do Azure para Redis com a nuvem do Azure governamental, nuvem do Azure China 21Vianet ou Microsoft Azure Alemanha?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany)

### <a name="azure-cache-for-redis-performance"></a>Desempenho do Cache do Azure para Redis
A tabela a seguir mostra os valores máximos de largura de banda observados durante o teste de vários tamanhos de caches Standard e Premium usando `redis-benchmark.exe` de uma VM de IaaS no ponto de extremidade do Cache do Azure para Redis. Para a taxa de transferência TLS, o redis-benchmark é usado com stunnel para se conectar ao ponto de extremidade do Cache do Azure para Redis.

>[!NOTE] 
>Esses valores não são garantidos e que não há nenhum SLA para esses números, mas eles devem ser típicos. Você deve realizar teste de carga em seu próprio aplicativo para determinar o tamanho de cache certo para ele.
>Esses números podem mudar à medida que postarmos resultados mais novos periodicamente.
>

Podemos tirar as seguintes conclusões desta tabela:

* A taxa de transferência para os caches são do mesmo tamanho é superior na camada Premium em comparação com a camada Standard. Por exemplo, com um Cache de 6 GB, a taxa de transferência de P1 será 180 mil RPS (solicitações por segundo), em comparação com 100 mil RPS para C3.
* Com o cluster Redis, a taxa de transferência aumenta linearmente à medida que o número de fragmentos (nós) no cluster aumenta. Por exemplo, se você criar um cluster P4 de 10 fragmentos, a taxa de transferência disponível será de 400.000 * 10 = 4 milhões de RPS.
* A taxa de transferência tamanhos de chave maiores é mais alta na camada Premium quando comparada à camada Standard.

| Tipo de preço | Tamanho | Núcleos de CPU | Largura de banda disponível | Tamanho do valor de 1 KB | Tamanho do valor de 1 KB |
| --- | --- | --- | --- | --- | --- |
| **Tamanhos de cache padrão** | | |**Megabits por segundo (Mb/s) / Megabytes por segundo (MB/s)** |**RPS (solicitações por segundo) não SSL** |**RPS (solicitações por segundo) SSL** |
| C0 | 250 MB | Compartilhado | 100 / 12,5  |  15,000 |   7\.500 |
| C1 |   1 GB | 1      | 500 / 62,5  |  38.000 |  20,720 |
| C2 | 2,5 GB | 2      | 500 / 62,5  |  41.000 |  37.000 |
| C3 |   6 GB | 4      | 1000 / 125  | 100.000 |  90.000 |
| C4 |  13 GB | 2      | 500 / 62,5  |  60.000 |  55.000 |
| C5 |  26 GB | 4      | 1\.000 / 125 | 102.000 |  93.000 |
| C6 |  53 GB | 8      | 2\.000 / 250 | 126.000 | 120.000 |
| **Tamanhos de cache Premium** | |**Núcleos de CPU por fragmento** | **Megabits por segundo (Mb/s) / Megabytes por segundo (MB/s)** |**RPS (solicitações por segundo) não SSL, por fragmento** |**RPS (solicitações por segundo) SSL, por fragmento** |
| P1 |   6 GB |  2 | 1\.500 / 187.5 | 180,000 | 172.000 |
| P2 |  13 GB |  4 | 3\.000 / 375   | 350.000 | 341.000 |
| P3 |  26 GB |  4 | 3\.000 / 375   | 350.000 | 341.000 |
| P4 |  53 GB |  8 | 6\.000 / 750   | 400.000 | 373.000 |
| P5 | 120 GB | 20 | 6\.000 / 750   | 400.000 | 373.000 |

Para obter instruções sobre como configurar o stunnel ou baixar as ferramentas do Redis como `redis-benchmark.exe` , consulte [como posso executar comandos do Redis?](cache-development-faq.md#how-can-i-run-redis-commands).

### <a name="in-what-region-should-i-locate-my-cache"></a>Em que região posso localizar meu cache?
Para melhor desempenho e menor latência, localize o Cache do Azure para Redis na mesma região que o aplicativo cliente de cache.

### <a name="where-do-my-cached-data-reside"></a>Onde residem os dados em cache?
O cache do Azure para Redis armazena os dados do aplicativo na RAM da VM ou das VMs, dependendo da camada, que hospeda o cache. Seus dados residem estritamente na região do Azure que você selecionou por padrão. Há dois casos em que seus dados podem sair de uma região:
* Quando você habilita a persistência no cache, o cache do Azure para Redis fará backup de seus dados para uma conta de armazenamento do Azure que você possui. Se a conta de armazenamento que você fornecer estiver em outra região, uma cópia dos dados acabará lá.
* Se você configurar a replicação geográfica e o cache secundário estiver em uma região diferente, que seria o caso normalmente, seus dados serão replicados para essa região.

Você precisará configurar explicitamente o cache do Azure para Redis para usar esses recursos. Você também tem controle total sobre a região em que a conta de armazenamento ou o cache secundário está localizado.

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Como eu sou cobrado pelo Cache do Azure para Redis?
O preço do Cache do Azure para Redis está [aqui](https://azure.microsoft.com/pricing/details/cache/). A página de preços lista os preços como uma taxa por hora e mensal. Os caches são cobrados em uma base por minuto do momento em que o cache é criado até o momento em que um cache é excluído. Não há nenhuma opção para parar ou pausar a cobrança de um cache.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany"></a>Posso usar o cache do Azure para Redis com a nuvem do Azure governamental, nuvem do Azure China 21Vianet ou Microsoft Azure Alemanha?
Sim, o Cache do Azure para Redis está disponível no Azure Governamental na Nuvem, Azure China 21Vianet Cloud e Microsoft Azure Alemanha. As URLs para acesso e gerenciamento do Cache do Azure para Redis são diferentes no Azure Governamental na Nuvem e no Azure China na Nuvem em comparação com a Nuvem Pública do Azure.

| Nuvem   | Sufixo DNS para Redis            |
|---------|---------------------------------|
| Público  | *.redis.cache.windows.net       |
| Gov dos EUA  | *.redis.cache.usgovcloudapi.net |
| Alemanha | *.redis.cache.cloudapi.de       |
| China   | *.redis.cache.chinacloudapi.cn  |

Para obter mais informações sobre considerações ao usar o Cache do Azure para Redis com outras nuvens, consulte os links a seguir.

- [Bancos de dados do Azure Governamental – Cache do Azure para Redis](../azure-government/compare-azure-government-global-azure.md)
- [Azure China 21Vianet Cloud – Cache do Azure para Redis](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/)

Para obter mais informações sobre como usar o Cache do Azure para Redis com o PowerShell no Azure Governamental na Nuvem, Azure China 21Vianet Cloud e no Microsoft Azure Alemanha, veja [Como se conectar a outras nuvens – PowerShell do Cache do Azure para Redis](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre outros [cache do Azure para perguntas frequentes](cache-faq.md)sobre o Redis.