---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/11/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e5148ff9e92a2e550a3117356a4e77cbac8fc6f4
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673219"
---
*Aquecendo o cache*  
O disco com o cache de host ReadOnly pode oferecer IOPS mais alta do que o limite do disco. Para atingir esse desempenho máximo de leitura do cache de host, primeiramente você deve aquecer o cache desse disco. Isso faz com que as E/S de leitura que a ferramenta de parâmetros de comparação impulsionará no volume CacheReads realmente alcancem o cache, não o disco diretamente. Os acertos no cache resultam em IOPS adicional do único disco habilitado para cache.

> [!IMPORTANT]
> você deve aquecer o cache antes de executar os parâmetros de comparação, toda vez que a VM é reinicializada.

## <a name="tools"></a>Ferramentas

### <a name="iometer"></a>Iometer

[Baixe a ferramenta Iometer](https://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) na VM.

#### <a name="test-file"></a>Arquivo de teste

O Iometer usa um arquivo de teste que fica armazenado no volume no qual você executa o teste de parâmetros de comparação. Ele orienta as leituras e gravações nesse arquivo de teste para avaliar a IOPS e a Taxa de Transferência do disco. O Iometer criará esse arquivo de teste caso você não tenha fornecido um. Crie um arquivo de teste de 200 GB chamado iobw.tst nos volumes CacheReads e NoCacheWrites.

#### <a name="access-specifications"></a>Especificações de acesso

As especificações, o tamanho da E/S de solicitação, a % de leitura/gravação, a % aleatória/sequencial são configurados usando a guia "Especificações de Acesso" no Iometer. Crie uma especificação de acesso para cada um dos cenários descritos abaixo. Crie as especificações de acesso e "Salve" com um nome apropriado como – RandomWrites\_8K, RandomReads\_8K. Selecione a especificação correspondente ao executar o cenário de teste.

Um exemplo de especificações de acesso para o cenário de IOPS de gravação máxima é mostrado abaixo,  
    ![Exemplo de especificações de acesso para a IOPS de gravação máxima](../articles/virtual-machines/linux/media/premium-storage-performance/image8.png)

#### <a name="maximum-iops-test-specifications"></a>Especificações de teste de IOPS máxima

Para demonstrar a IOPS máxima, use o tamanho de solicitação menor. Use o tamanho de solicitação de 8 K e crie especificações para gravações e leituras aleatórias.

| Especificação de acesso | Tamanho da solicitação | Aleatório % | Leitura % |
| --- | --- | --- | --- |
| RandomWrites\_8K |8 K |100 |0 |
| RandomReads\_8K |8 K |100 |100 |

#### <a name="maximum-throughput-test-specifications"></a>Especificações de teste de taxa de transferência máxima

Para demonstrar a Taxa de Transferência máxima, use o tamanho de solicitação maior. Use o tamanho de solicitação de 64K e crie especificações para gravações e leituras aleatórias.

| Especificação de acesso | Tamanho da solicitação | Aleatório % | Leitura % |
| --- | --- | --- | --- |
| RandomWrites\_64K |64 k |100 |0 |
| RandomReads\_64K |64 k |100 |100 |

#### <a name="run-the-iometer-test"></a>Executar o teste Iometer

Execute as etapas abaixo para aquecer o cache

1. Crie duas especificações de acesso com os valores mostrados abaixo:

   | Nome | Tamanho da solicitação | Aleatório % | Leitura % |
   | --- | --- | --- | --- |
   | RandomWrites\_1MB |1 MB |100 |0 |
   | RandomReads\_1MB |1 MB |100 |100 |
1. Execute o teste Iometer para inicializar o disco do cache com os parâmetros a seguir. Use três threads de trabalho para o volume de destino e uma profundidade de fila de 128. Defina a duração do teste "Tempo de execução" como 2 horas na guia "Configuração do teste".

   | Cenário | Volume de destino | Nome | Duração |
   | --- | --- | --- | --- |
   | Inicializar disco do cache |CacheReads |RandomWrites\_1MB |2 horas |
1. Execute o teste Iometer para aquecer o disco do cache com os parâmetros a seguir. Use três threads de trabalho para o volume de destino e uma profundidade de fila de 128. Defina a duração do teste "Tempo de execução" como 2 horas na guia "Configuração do teste".

   | Cenário | Volume de destino | Nome | Duração |
   | --- | --- | --- | --- |
   | Aquecer o disco do cache |CacheReads |RandomReads\_1MB |2 horas |

Depois de aquecer o disco do cache, prossiga com os cenários de teste listados abaixo. Para executar o teste Iometer, use pelo menos três threads de trabalho para **cada** volume de destino. Para cada thread de trabalho, selecione o volume de destino, defina a profundidade da fila e selecione uma das especificações de teste salvas, conforme mostrado na tabela a seguir, para executar o cenário de teste correspondente. A tabela também mostra os resultados esperados para IOPS e Taxa de Transferência ao executar esses testes. Todos os cenários usam um tamanho pequeno de E/S, de 8 KB, e uma profundidade de fila alta de 128.

| Cenário de teste | Volume de destino | Nome | Resultado |
| --- | --- | --- | --- |
| Máx. IOPS de leitura |CacheReads |RandomWrites\_8K |50.000 IOPS |
| Máx. IOPS de gravação |NoCacheWrites |RandomReads\_8K |64.000 IOPS |
| Máx. IOPS combinada |CacheReads |RandomWrites\_8K |100.000 IOPS |
| NoCacheWrites |RandomReads\_8K | &nbsp; | &nbsp; |
| Máx. MB/s de leitura |CacheReads |RandomWrites\_64K |524 MB/s |
| Máx. MB/s de gravação |NoCacheWrites |RandomReads\_64K |524 MB/s |
| MB/s combinado |CacheReads |RandomWrites\_64K |1000 MB/s |
| NoCacheWrites |RandomReads\_64K | &nbsp; | &nbsp; |

Veja abaixo as capturas de tela dos resultado do teste Iometer para cenários combinados de IOPS e Taxa de Transferência.

#### <a name="combined-reads-and-writes-maximum-iops"></a>IOPS máxima de leituras e gravações combinadas

![IOPS máxima de leituras e gravações combinadas](../articles/virtual-machines/linux/media/premium-storage-performance/image9.png)

#### <a name="combined-reads-and-writes-maximum-throughput"></a>Taxa de transferência máxima de leituras e gravações combinadas

![Taxa de transferência máxima de leituras e gravações combinadas](../articles/virtual-machines/linux/media/premium-storage-performance/image10.png)

### <a name="fio"></a>FIO

FIO é uma ferramenta popular para o armazenamento de parâmetros de comparação em VMs Linux. Ela tem a flexibilidade para selecionar diferentes tamanhos de E/S, leituras e gravações sequenciais ou aleatórias. Ela gera threads ou processos de trabalho para executar as operações de E/S especificadas. Você pode especificar o tipo de operação de E/S que cada thread de trabalho deve executar usando arquivos de trabalho. Criamos um arquivo de trabalho por cenário ilustrado nos exemplos abaixo. É possível alterar as especificações nesses arquivos de trabalho para comparar diferentes cargas de trabalho em execução no Armazenamento Premium. Nos exemplos, estamos usando uma VM DS14 padrão executando o **Ubuntu**. Use a mesma configuração descrita no início da seção de parâmetros de comparação e passiva do cache antes de executar os testes de benchmark.

Antes de começar, [baixe o FIO](https://github.com/axboe/fio) e instale-o em sua máquina virtual.

Execute o comando a seguir para o Ubuntu,

```
apt-get install fio
```

Usamos quatro threads de trabalho para impulsionar operações de gravação e quatro threads de trabalho para impulsionar operações de leitura nos discos. Os trabalhos de gravação orientam o tráfego no volume "nocache", que tem 10 discos com o cache definido como "None". Os trabalhos de leitura orientam o tráfego no volume "readcache", que tem um disco com o cache definido como "ReadOnly".

#### <a name="maximum-write-iops"></a>IOPS máxima de gravação

Crie o arquivo de trabalho com as especificações a seguir para obter IOPS máxima de gravação. Dê o nome de "fiowrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

Observe os itens importantes a seguir que estão de acordo com as diretrizes de projeto abordadas nas seções anteriores. Estas especificações são essenciais para impulsionar a IOPS máxima:  

* Uma profundidade de fila alta de 256.  
* Um tamanho de bloco pequeno de 8 KB.  
* Vários threads que executam gravações aleatórias.

Execute o seguinte comando para iniciar o teste FIO por 30 segundos:  

```
sudo fio --runtime 30 fiowrite.ini
```

Enquanto o teste é executado, você pode ver o número de IOPS de gravação fornecido pela VM e pelos discos Premium. Como mostrado no exemplo abaixo, a VM DS14 está fornecendo seu limite máximo de IOPS de gravação, isto é, 50.000 IOPS.  
    ![Quantidade de IOPS de gravação que a VM e os discos Premium estão fornecendo](../articles/virtual-machines/linux/media/premium-storage-performance/image11.png)

#### <a name="maximum-read-iops"></a>IOPS máxima de leitura

Crie o arquivo de trabalho com as especificações a seguir para obter IOPS máxima de leitura. Dê o nome de "fioread.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

Observe os itens importantes a seguir que estão de acordo com as diretrizes de projeto abordadas nas seções anteriores. Estas especificações são essenciais para impulsionar a IOPS máxima:

* Uma profundidade de fila alta de 256.  
* Um tamanho de bloco pequeno de 8 KB.  
* Vários threads que executam gravações aleatórias.

Execute o seguinte comando para iniciar o teste FIO por 30 segundos:

```
sudo fio --runtime 30 fioread.ini
```

Enquanto o teste é executado, você pode ver o número de IOPS de leitura fornecido pela VM e pelos discos Premium. Conforme mostrado no exemplo abaixo, a VM DS14 está fornecendo mais de 64.000 IOPS de leitura. Essa é uma combinação do desempenho do cache e do disco.  
    ![](../articles/virtual-machines/linux/media/premium-storage-performance/image12.png)

#### <a name="maximum-read-and-write-iops"></a>IOPS máxima de leitura e gravação

Crie o arquivo de trabalho com as especificações a seguir para obter a IOPS Máxima de Leitura e Gravação. Dê o nome de "fioreadwrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Observe os itens importantes a seguir que estão de acordo com as diretrizes de projeto abordadas nas seções anteriores. Estas especificações são essenciais para impulsionar a IOPS máxima:

* Uma profundidade alta de fila de 128.  
* Um tamanho de bloco pequeno de 4 KB.  
* Vários threads que executam leituras e gravações aleatórias.

Execute o seguinte comando para iniciar o teste FIO por 30 segundos:

```
sudo fio --runtime 30 fioreadwrite.ini
```

Enquanto o teste é executado, você pode ver o número de IOPS de leitura e gravação combinadas fornecido pela VM e pelos discos Premium. Como mostrado no exemplo abaixo, a VM DS14 está fornecendo mais de 100.000 IOPS de leitura e gravação combinadas. Essa é uma combinação do desempenho do cache e do disco.  
    ![IOPS de gravação e leitura combinadas](../articles/virtual-machines/linux/media/premium-storage-performance/image13.png)

#### <a name="maximum-combined-throughput"></a>Taxa de transferência máxima combinada

Para atingir a Taxa de Transferência máxima de Leitura e Gravação combinadas, use um tamanho de bloco maior e uma profundidade de fila grande com vários threads executando leituras e gravações. É possível usar um tamanho de bloco de 64 KB e uma profundidade de fila de 128.
