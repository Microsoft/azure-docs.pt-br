---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 3c4ab8362b2a717a348a59c0baf829b61e1a8006
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808470"
---
![Gráfico mostrando as especificações D s v 3.](media/vm-disk-performance/dsv3-documentation.jpg)

- A taxa de transferência máxima do disco não *armazenado em cache* é o limite máximo de armazenamento padrão que a máquina virtual pode manipular.
- O limite máximo de taxa de transferência de armazenamento *em cache* é um limite separado quando você habilita o cache de host.

O cache de host funciona colocando o armazenamento mais próximo da VM que pode ser gravada ou lida com rapidez. A quantidade de armazenamento disponível para a VM para o cache de host está na documentação. Por exemplo, você pode ver que a Standard_D8s_v3 vem com 200 GiB de armazenamento em cache.

Você pode habilitar o cache de host ao criar sua máquina virtual e anexar discos. Você também pode ativar e desativar o cache de host em seus discos em uma VM existente.

![Captura de tela mostrando o cache de host.](media/vm-disk-performance/host-caching.jpg)

Você pode ajustar o cache do host para corresponder aos seus requisitos de carga de trabalho para cada disco. Você pode definir o cache do host como:

- **Somente leitura**: para cargas de trabalho que só fazem operações de leitura
- **Leitura/gravação**: para cargas de trabalho que fazem um saldo de operações de leitura e gravação

Se sua carga de trabalho não seguir nenhum desses padrões, não recomendamos que você use o cache de host.

Vamos executar alguns exemplos de diferentes configurações de cache de host para ver como ele afeta o fluxo de dados e o desempenho. Neste primeiro exemplo, veremos o que acontece com as solicitações de e/s quando a configuração de cache do host é definida como **somente leitura**.

**Instalação**

- Standard_D8s_v3
  - IOPS em cache: 16.000
  - IOPS não armazenado em cache: 12.800
- Disco de dados p30
  - IOPS: 5.000
  - Cache de host: **somente leitura**

Quando uma leitura é executada e os dados desejados estão disponíveis no cache, o cache retorna os dados solicitados. Não é necessário ler a partir do disco. Essa leitura é contada em relação aos limites em cache da VM.

![Diagrama mostrando um acesso de leitura de cache de host de leitura.](media/vm-disk-performance/host-caching-read-hit.jpg)

Quando uma leitura é executada e os dados desejados *não* estão disponíveis no cache, a solicitação de leitura é retransmitida para o disco. Em seguida, o disco a superfícies tanto para o cache quanto para a VM. Essa leitura é contada em relação ao limite não armazenado em cache da VM e ao limite em cache da VM.

![Diagrama mostrando um erro de leitura de cache de host de leitura.](media/vm-disk-performance/host-caching-read-miss.jpg)

Quando uma gravação é executada, a gravação precisa ser gravada no cache e no disco antes que ele seja considerado concluído. Essa gravação é contada em relação ao limite não armazenado em cache da VM e ao limite em cache da VM.

![Diagrama mostrando uma gravação de cache de host de leitura.](media/vm-disk-performance/host-caching-write.jpg)

Em seguida, vamos examinar o que acontece com as solicitações de e/s quando a configuração de cache do host é definida como **leitura/gravação**.

**Instalação**

- Standard_D8s_v3
  - IOPS em cache: 16.000
  - IOPS não armazenado em cache: 12.800
- Disco de dados p30
  - IOPS: 5.000
  - Cache de host: **leitura/gravação**

Uma leitura é tratada da mesma maneira que uma somente leitura. As gravações são a única coisa que é diferente com o cache de leitura/gravação. Quando a gravação com cache de host é definida como **leitura/gravação**, a gravação só precisa ser gravada no cache do host para ser considerada concluída. Em seguida, a gravação é gravada lentamente no disco como um processo em segundo plano. Isso significa que uma gravação é contada em relação a e/s em cache quando gravada no cache. Quando ele é gravado lentamente no disco, ele conta para a e/s não armazenada em cache.

![Diagrama mostrando gravação de cache de host de leitura/gravação.](media/vm-disk-performance/host-caching-read-write.jpg)

Vamos continuar com nossa máquina virtual de Standard_D8s_v3. Exceto desta vez, Habilitaremos o cache de host nos discos. Além disso, agora o limite de IOPS da VM é de 16.000 IOPS. Anexados à VM estão três discos p30 subjacentes que podem lidar com 5.000 IOPS.

**Instalação**

- Standard_D8s_v3
  - IOPS em cache: 16.000
  - IOPS não armazenado em cache: 12.800
- Disco do sistema operacional p30
  - IOPS: 5.000
  - Cache de host: **leitura/gravação**
- Dois discos de dados p30 × 2
  - IOPS: 5.000
  - Cache de host: **leitura/gravação**

![Diagrama mostrando um exemplo de cache de host.](media/vm-disk-performance/host-caching-example-without-remote.jpg)

O aplicativo usa uma máquina virtual Standard_D8s_v3 com Caching habilitado. Ele faz uma solicitação de 15.000 IOPS. As solicitações são divididas como 5.000 IOPS para cada disco subjacente anexado. Nenhum capping de desempenho ocorre.

## <a name="combined-uncached-and-cached-limits"></a>Limites combinados não armazenados em cache e armazenados em cache

Os limites em cache de uma máquina virtual são separados dos limites não armazenados em cache. Isso significa que você pode habilitar o cache de host em discos anexados a uma VM e não habilitar o cache de host em outros discos. Essa configuração permite que suas máquinas virtuais obtenham uma e/s de armazenamento total do limite em cache mais o limite não armazenado em cache.

Vamos executar um exemplo para ajudá-lo a entender como esses limites funcionam juntos. Continuaremos com a máquina virtual Standard_D8s_v3 e a configuração anexada de discos Premium.

**Instalação**

- Standard_D8s_v3
  - IOPS em cache: 16.000
  - IOPS não armazenado em cache: 12.800
- Disco do sistema operacional p30
  - IOPS: 5.000
  - Cache de host: **leitura/gravação**
- Dois discos de dados p30 × 2
  - IOPS: 5.000
  - Cache de host: **leitura/gravação**
- Dois discos de dados p30 × 2
  - IOPS: 5.000
  - Cache de host: **desabilitado**

![Diagrama mostrando um exemplo de cache de host com armazenamento remoto.](media/vm-disk-performance/host-caching-example-with-remote.jpg)

Nesse caso, o aplicativo em execução em um Standard_D8s_v3 máquina virtual faz uma solicitação de 25.000 IOPS. A solicitação é dividida como 5.000 IOPS para cada um dos discos anexados. Três discos usam o cache de host e dois discos não usam o cache de host.

- Como os três discos que usam o cache de host estão dentro dos limites em cache de 16.000, essas solicitações são concluídas com êxito. Nenhum capping de desempenho de armazenamento ocorre.
- Como os dois discos que não usam o cache de host estão dentro dos limites não armazenados em cache de 12.800, essas solicitações também são concluídas com êxito. Não ocorre nenhum capping.

## <a name="disk-performance-metrics"></a>Métricas de desempenho de disco

Temos métricas no Azure que fornecem informações sobre como as máquinas virtuais e os discos estão sendo executados. Essas métricas podem ser exibidas por meio do portal do Azure. Eles também podem ser recuperados por meio de uma chamada à API. As métricas são calculadas em intervalos de um minuto. As métricas a seguir estão disponíveis para obter informações sobre e/s de disco e de VM e também sobre o desempenho da taxa de transferência:

- **Profundidade da fila de disco do so**: o número de solicitações de e/s pendentes atuais que estão aguardando para serem lidas ou gravadas no disco do sistema operacional.
- **Bytes de leitura do disco do so/s**: o número de bytes lidos em um segundo do disco do sistema operacional.
- **Operações de leitura de disco do so/s**: o número de operações de entrada que são lidas em um segundo do disco do sistema operacional.
- **Bytes de gravação de disco do sistema operacional/s**: o número de bytes gravados em um segundo do disco do sistema operacional.
- **Operações de gravação de disco do so/s**: o número de operações de saída que são gravadas em um segundo do disco do sistema operacional.
- **Profundidade da fila do disco de dados**: o número de solicitações de e/s pendentes atuais que estão aguardando para serem lidas ou gravadas nos discos de dados.
- **Bytes de leitura do disco de dados/s**: o número de bytes lidos em um segundo dos discos de dados.
- **Operações de leitura de disco de dados/s**: o número de operações de entrada que são lidas em um segundo de discos de dados.
- **Bytes de gravação do disco de dados/s**: o número de bytes gravados em um segundo dos discos de dados.
- **Operações de gravação do disco de dados/s**: o número de operações de saída que são gravadas em um segundo a partir de disco (s) de dados.
- **Bytes de leitura de disco/s**: o número total de bytes lidos em um segundo de todos os discos anexados a uma VM.
- **Operações de leitura de disco/s**: o número de operações de entrada que são lidas em um segundo de todos os discos anexados a uma VM.
- **Bytes de gravação de disco/s**: o número de bytes gravados em um segundo de todos os discos anexados a uma VM.
- **Operações de gravação de disco/s**: o número de operações de saída que são gravadas em um segundo de todos os discos anexados a uma VM.

## <a name="storage-io-utilization-metrics"></a>Métricas de utilização de e/s de armazenamento
As métricas a seguir ajudam a diagnosticar afunilamento na sua combinação de disco e máquina virtual. Essas métricas só estão disponíveis ao usar a VM habilitada Premium. Essas métricas estão disponíveis para todos os tipos de disco, exceto para ultra. 

Métricas que ajudam a diagnosticar a e/s de disco com limitação:

- **Percentual de IOPS consumido de disco de dados**: a porcentagem calculada pelo IOPS de disco de dados foi concluída sobre o IOPS de disco de dados provisionado. Se esse valor for em 100%, seu aplicativo em execução será e/s limitada do limite de IOPS do disco de dados.
- **Porcentagem consumida da largura de banda do disco de dados**: a porcentagem calculada pela taxa de transferência do disco de dados foi concluída na taxa de transferência do disco de dados provisionado Se esse valor for em 100%, seu aplicativo em execução será e/s limitada do limite de largura de banda do disco de dados.
- **Porcentagem consumida de IOPS de disco do so**: a porcentagem calculada pelo IOPS de disco do sistema operacional concluída em relação à IOPS de disco do sistema operacional provisionado. Se esse valor for em 100%, seu aplicativo em execução será e/s limitada do limite de IOPS do disco do sistema operacional.
- **Porcentagem consumida da largura de banda do disco do so**: a porcentagem calculada pela taxa de transferência do disco do so concluída sobre a taxa de transferência do disco do so provisionado Se esse valor for em 100%, seu aplicativo em execução será e/s limitada do limite de largura de banda do disco do sistema operacional.

Métricas que ajudam a diagnosticar a e/s de VM com limitação:

- **Porcentagem consumida de IOPS em cache da VM**: a porcentagem calculada pelo total de IOPS concluída em relação ao limite de IOPS máximo em cache da máquina virtual. Se esse valor for em 100%, seu aplicativo em execução será e/s limitada do limite de IOPS em cache da VM.
- **Porcentagem consumida da largura de banda em cache da VM**: a porcentagem calculada pela taxa de transferência total do disco concluída na taxa de transferência máxima de máquina virtual em cache. Se esse valor for em 100%, seu aplicativo em execução será e/s limitada do limite de largura de banda em cache da VM.
- **Percentual de IOPS consumido de VM não armazenado em cache**: a porcentagem calculada pelo IOPS total em uma máquina virtual foi concluída com o limite máximo de IOPS de máquina virtual sem cache. Se esse valor for em 100%, seu aplicativo em execução será e/s limitada do limite de IOPS sem cache da sua VM.
- **Porcentagem consumida da largura de banda não armazenada em cache da VM**: a porcentagem calculada pela taxa de transferência total do disco em uma máquina virtual foi concluída com a taxa de transferência máxima da máquina virtual provisionada. Se esse valor for em 100%, seu aplicativo em execução será e/s limitada do limite de largura de banda sem cache da sua VM.

## <a name="storage-io-utilization-metrics-example"></a>Exemplo de métricas de utilização de e/s de armazenamento

Vamos executar um exemplo de como usar essas novas métricas de utilização de e/s de armazenamento para nos ajudar a depurar onde há um afunilamento em nosso sistema. A configuração do sistema é igual ao exemplo anterior, exceto que, desta vez, o disco do sistema operacional anexado *não* é armazenado em cache.

**Instalação**

- Standard_D8s_v3
  - IOPS em cache: 16.000
  - IOPS não armazenado em cache: 12.800
- Disco do sistema operacional p30
  - IOPS: 5.000
  - Cache de host: **desabilitado**
- Dois discos de dados p30 × 2
  - IOPS: 5.000
  - Cache de host: **leitura/gravação**
- Dois discos de dados p30 × 2
  - IOPS: 5.000
  - Cache de host: **desabilitado**
