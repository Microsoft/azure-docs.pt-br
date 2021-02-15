---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/29/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 25404837d5bc66ff415be8d8670eb6650475c30f
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99094570"
---
## <a name="warm-up-the-cache"></a>Aquecimento do cache

O disco com cache de host ReadOnly é capaz de fornecer IOPS maior do que o limite de disco. Para atingir esse desempenho máximo de leitura do cache de host, primeiramente você deve aquecer o cache desse disco. Isso faz com que as E/S de leitura que a ferramenta de parâmetros de comparação impulsionará no volume CacheReads realmente alcancem o cache, não o disco diretamente. Os acertos de cache resultam em mais IOPS do disco habilitado para cache único.

> [!IMPORTANT]
> Você deve iniciar o cache antes de executar os parâmetros de comparação toda vez que a VM for reinicializada.

## <a name="diskspd"></a>DISKSPD

[Baixe a ferramenta DISKSP](https://github.com/Microsoft/diskspd) na VM. DISKSPD é uma ferramenta que você pode personalizar para criar suas próprias cargas de trabalho sintéticas. Usaremos a mesma configuração descrita acima para executar testes de benchmark. Você pode alterar as especificações para testar cargas de trabalho diferentes.

Neste exemplo, usamos o seguinte conjunto de parâmetros de linha de base:

- -c200G: cria (ou recria) o arquivo de exemplo usado no teste. Ele pode ser definido em bytes, KiB, MiB, GiB ou blocos. Nesse caso, um arquivo grande do arquivo de destino 200-GiB é usado para minimizar o cache de memória.
- -W100: especifica a porcentagem de operações que são solicitações de gravação (-W0 é equivalente a 100% Read).
- -b4K: indica o tamanho do bloco em bytes, KiB, MiB ou GiB. Nesse caso, o tamanho do bloco de 4K é usado para simular um teste de e/s aleatório.
- -F4: define um total de quatro threads.
- -r: indica o teste de e/s aleatório (Substitui o parâmetro-s).
- -o128: indica o número de solicitações de e/s pendentes por destino por thread. Isso também é conhecido como a profundidade da fila. Nesse caso, 128 é usado para enfatizar a CPU.
- -W7200: especifica a duração do tempo de aquecimento antes do início das medições.
- -D30: especifica a duração do teste, sem incluir o aquecimento.
- -Sh: desabilitar o cache de gravação de software e hardware (equivalente a-Suw).

Para obter uma lista completa de parâmetros, consulte o [repositório GitHub](https://github.com/Microsoft/diskspd/wiki/Command-line-and-parameters).

### <a name="maximum-write-iops"></a>IOPS máxima de gravação
Usamos uma profundidade de fila alta de 128, um tamanho de bloco pequeno de 8 KB e quatro threads de trabalho para conduzir operações de gravação. Os trabalhos de gravação estão orientando o tráfego no volume "NoCacheWrites", que tem três discos com o cache definido como "None".

Execute o seguinte comando para 30 segundos de aquecimento e 30 segundos de medida:

`diskspd -c200G -w100 -b8K -F4 -r -o128 -W30 -d30 -Sh testfile.dat`

Os resultados mostram que a VM Standard_D8ds_v4 está fornecendo seu limite máximo de IOPS de gravação de 12.800.

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-write-io-per-second.png" alt-text="Para 3208642560 total de bytes, o total máximo de e/SS de 391680, com um total de 101,97 MiB/s e um total de e/s de 13052,65 por segundo.":::

### <a name="maximum-read-iops"></a>IOPS máxima de leitura

Usamos uma profundidade de fila alta de 128, um tamanho de bloco pequeno de quatro KB e quatro threads de trabalho para conduzir operações de leitura. Os trabalhos de leitura estão orientando o tráfego no volume "CacheReads", que tem um disco com cache definido como "ReadOnly".

Execute o seguinte comando por duas horas de aquecimento e 30 segundos de medida:

`diskspd -c200G -b4K -F4 -r -o128 -W7200 -d30 -Sh testfile.dat`

Os resultados mostram que o Standard_D8ds_v4 VM está fornecendo seu limite máximo de IOPS de leitura de 77.000.

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-read-io-per-second.png" alt-text="Para 9652785152 total de bytes, havia 2356637 e/SS total, às 306,72 MiB/s total e um total de 78521,23 I/os por segundo.":::

### <a name="maximum-throughput"></a>Taxa de transferência máxima

Para obter a taxa de transferência máxima de leitura e gravação, você pode mudar para um tamanho de bloco maior de 64 KB.
## <a name="fio"></a>FIO

FIO é uma ferramenta popular para o armazenamento de parâmetros de comparação em VMs Linux. Ela tem a flexibilidade para selecionar diferentes tamanhos de E/S, leituras e gravações sequenciais ou aleatórias. Ela gera threads ou processos de trabalho para executar as operações de E/S especificadas. Você pode especificar o tipo de operação de E/S que cada thread de trabalho deve executar usando arquivos de trabalho. Criamos um arquivo de trabalho por cenário ilustrado nos exemplos abaixo. É possível alterar as especificações nesses arquivos de trabalho para comparar diferentes cargas de trabalho em execução no Armazenamento Premium. Nos exemplos, estamos usando um Standard_D8ds_v4 executando o **Ubuntu**. Use a mesma configuração descrita no início da seção de benchmark e execute o cache antes de executar os testes de parâmetro de comparação.

Antes de começar, [baixe o FIO](https://github.com/axboe/fio) e instale-o em sua máquina virtual.

Execute o comando a seguir para o Ubuntu,

```
apt-get install fio
```

Usamos quatro threads de trabalho para impulsionar operações de gravação e quatro threads de trabalho para impulsionar operações de leitura nos discos. Os trabalhos de gravação estão orientando o tráfego no volume "NoCache", que tem três discos com o cache definido como "None". Os trabalhos de leitura estão orientando o tráfego no volume "readcache", que tem um disco com cache definido como "ReadOnly".

### <a name="maximum-write-iops"></a>IOPS máxima de gravação

 Crie o arquivo de trabalho com as especificações a seguir para obter IOPS máxima de gravação. Dê o nome de "fiowrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[writer1]
rw=randwrite
directory=/mnt/nocache
```

Observe os itens importantes a seguir que estão de acordo com as diretrizes de projeto abordadas nas seções anteriores. Estas especificações são essenciais para impulsionar a IOPS máxima:  

* Uma profundidade de fila alta de 256.  
* Um tamanho de bloco pequeno de 4 KB.  
* Vários threads que executam gravações aleatórias.

Execute o seguinte comando para iniciar o teste FIO por 30 segundos:  

```
sudo fio --runtime 30 fiowrite.ini
```

Enquanto o teste é executado, você pode ver o número de IOPS de gravação fornecido pela VM e pelos discos Premium. Conforme mostrado no exemplo abaixo, a VM Standard_D8ds_v4 está fornecendo seu limite máximo de IOPS de gravação de 12.800 IOPS.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-uncached-writes-1.jpg" alt-text="Número de VMs de gravação de IOPS e de SSDs Premium estão entregando, mostra que as gravações são 13.1 k IOPS.":::

### <a name="maximum-read-iops"></a>IOPS máxima de leitura

 Crie o arquivo de trabalho com as especificações a seguir para obter IOPS máxima de leitura. Dê o nome de "fioread.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache
```

Observe os itens importantes a seguir que estão de acordo com as diretrizes de projeto abordadas nas seções anteriores. Estas especificações são essenciais para impulsionar a IOPS máxima:

* Uma profundidade de fila alta de 256.  
* Um tamanho de bloco pequeno de 4 KB.  
* Vários threads que executam gravações aleatórias.

Execute o seguinte comando para iniciar o teste FIO por 30 segundos:

```
sudo fio --runtime 30 fioread.ini
```

Enquanto o teste é executado, você pode ver o número de IOPS de leitura fornecido pela VM e pelos discos Premium. Conforme mostrado no exemplo abaixo, a VM Standard_D8ds_v4 está fornecendo mais de 77.000 IOPS de leitura. Essa é uma combinação do desempenho do cache e do disco.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-cached-reads-1.jpg" alt-text="A captura de tela do número de VMs de gravação de IOPS e do SSDs Premium está sendo entregue, mostra que as leituras são 78.6 k.":::

### <a name="maximum-read-and-write-iops"></a>IOPS máxima de leitura e gravação

 Crie o arquivo de trabalho com as especificações a seguir para obter a IOPS Máxima de Leitura e Gravação. Dê o nome de "fioreadwrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=3200
```

Observe os itens importantes a seguir que estão de acordo com as diretrizes de projeto abordadas nas seções anteriores. Estas especificações são essenciais para impulsionar a IOPS máxima:

* Uma profundidade alta de fila de 128.  
* Um tamanho de bloco pequeno de 4 KB.  
* Vários threads que executam leituras e gravações aleatórias.

Execute o seguinte comando para iniciar o teste FIO por 30 segundos:

```
sudo fio --runtime 30 fioreadwrite.ini
```

Enquanto o teste é executado, você pode ver o número de IOPS de leitura e gravação combinadas fornecido pela VM e pelos discos Premium. Conforme mostrado no exemplo abaixo, a VM Standard_D8ds_v4 está fornecendo mais de 90.000 IOPS de leitura e gravação combinados. Essa é uma combinação do desempenho do cache e do disco.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-both-1.jpg" alt-text="IOPS combinados de leitura e gravação, mostra que as leituras são 78.3 k e gravações são 12.6 k IOPS.":::

### <a name="maximum-combined-throughput"></a>Taxa de transferência máxima combinada

 Para atingir a Taxa de Transferência máxima de Leitura e Gravação combinadas, use um tamanho de bloco maior e uma profundidade de fila grande com vários threads executando leituras e gravações. É possível usar um tamanho de bloco de 64 KB e uma profundidade de fila de 128.