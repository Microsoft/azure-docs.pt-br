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
ms.openlocfilehash: 82b4c127f983f3133326bf7fb538e40713ef9655
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100580379"
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

