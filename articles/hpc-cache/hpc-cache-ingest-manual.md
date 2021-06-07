---
title: Ingestão de dados do cache HPC do Azure-cópia manual
description: Como usar comandos CP para mover dados para um destino de armazenamento de BLOBs no cache HPC do Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: f96a0fa264124f9d050e667b003d98579da63b77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87092330"
---
# <a name="azure-hpc-cache-data-ingest---manual-copy-method"></a>Ingestão de dados do cache HPC do Azure-método de cópia manual

Este artigo fornece instruções detalhadas para copiar manualmente os dados para um contêiner de armazenamento de BLOBs para uso com o cache do HPC do Azure. Ele usa operações paralelas multi-threaded para otimizar a velocidade de cópia.

Para saber mais sobre como mover dados para o armazenamento de BLOBs para o cache do Azure HPC, leia [mover dados para o armazenamento de BLOBs do Azure](hpc-cache-ingest.md).

## <a name="simple-copy-example"></a>Exemplo de cópia simples

Você pode criar manualmente uma cópia com vários threads em um cliente executando mais de um comando de cópia ao mesmo tempo em segundo plano em relação a conjuntos predefinidos de arquivos ou caminhos.

O comando ``cp`` UNIX/Linux inclui o argumento ``-p`` para preservar a propriedade e os metadados mtime. Adicionar esse argumento aos comandos a seguir é opcional. (Adicionar o argumento aumenta o número de chamadas do sistema de arquivos enviadas do cliente para o sistema de arquivos de destino para modificação de metadados.)

Este exemplo simples copia dois arquivos em paralelo:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Depois de emitir esse comando, o comando `jobs` mostrará se os dois threads estão em execução.

## <a name="copy-data-with-predictable-file-names"></a>Copiar dados com nomes de arquivo previsíveis

Se os nomes de arquivo forem previsíveis, você poderá usar expressões para criar threads de cópia paralela.

Por exemplo, se seu diretório contiver 1000 arquivos que são numerados sequencialmente de `0001` para `1000` , você poderá usar as seguintes expressões para criar 10 threads paralelos que cada cópia 100 arquivos:

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination1/ & \
cp /mnt/source/file2* /mnt/destination1/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination1/ & \
cp /mnt/source/file5* /mnt/destination1/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination1/ & \
cp /mnt/source/file8* /mnt/destination1/ & \
cp /mnt/source/file9* /mnt/destination1/
```

## <a name="copy-data-with-unstructured-file-names"></a>Copiar dados com nomes de arquivo não estruturados

Se a estrutura de nomenclatura de arquivo não for previsível, você poderá agrupar arquivos por nomes de diretório.

Este exemplo coleta diretórios inteiros para enviar a comandos ``cp`` executados como tarefas em segundo plano:

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

Depois que os arquivos são coletados, você pode executar comandos de cópia paralela para copiar recursivamente as subpastas e todos os seus conteúdos:

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

## <a name="when-to-add-mount-points"></a>Quando adicionar pontos de montagem

Depois que você tiver threads paralelos suficientes indo para um único ponto de montagem do sistema de arquivos de destino, haverá um ponto em que a adição de mais threads não oferece mais taxa de transferência. (A taxa de transferência será medida em arquivos/segundo ou em bytes/segundo, dependendo do tipo de dados.) Ou pior, o excesso de threads pode, às vezes, causar uma degradação da taxa de transferência.

Quando isso acontece, você pode adicionar pontos de montagem do lado do cliente a outros endereços de montagem de cache do Azure HPC, usando o mesmo caminho de montagem do sistema de arquivos remoto:

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

Adicionar pontos de montagem do lado do cliente permite que você divida os comandos de cópia para pontos de montagem `/mnt/destination[1-3]` adicionais, obtendo ainda mais paralelismo.

Por exemplo, se os arquivos forem muito grandes, você poderá definir os comandos de cópia para usar caminhos de destino distintos, enviando mais comandos em paralelo do cliente que está executando a cópia.

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination2/ & \
cp /mnt/source/file2* /mnt/destination3/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination2/ & \
cp /mnt/source/file5* /mnt/destination3/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination2/ & \
cp /mnt/source/file8* /mnt/destination3/ & \
```

No exemplo acima, todos os três pontos de montagem de destino estão sendo direcionados pelos processos de cópia de arquivo do cliente.

## <a name="when-to-add-clients"></a>Quando adicionar clientes

Por fim, quando você tiver atingiu as funcionalidades do cliente, adicionar mais threads de cópia ou pontos de montagem não produzirá mais nenhum aumento de arquivos/s ou bytes/s. Nessa situação, você pode implantar outro cliente com o mesmo conjunto de pontos de montagem que vá executar os próprios conjuntos de processos de cópia de arquivo.

Exemplo:

```bash
Client1: cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
Client1: cp -R /mnt/source/dir2/dir2a /mnt/destination/dir2/ &
Client1: cp -R /mnt/source/dir3/dir3a /mnt/destination/dir3/ &

Client2: cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
Client2: cp -R /mnt/source/dir2/dir2b /mnt/destination/dir2/ &
Client2: cp -R /mnt/source/dir3/dir3b /mnt/destination/dir3/ &

Client3: cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ &
Client3: cp -R /mnt/source/dir2/dir2c /mnt/destination/dir2/ &
Client3: cp -R /mnt/source/dir3/dir3c /mnt/destination/dir3/ &

Client4: cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
Client4: cp -R /mnt/source/dir2/dir2d /mnt/destination/dir2/ &
Client4: cp -R /mnt/source/dir3/dir3d /mnt/destination/dir3/ &
```

## <a name="create-file-manifests"></a>Criar manifestos de arquivista

Depois de entender as abordagens acima (vários threads de cópia por destino, vários destinos por cliente, vários clientes por sistema de arquivos de origem acessível por rede), considere esta recomendação: Compilar manifestos de arquivo e usá-los com comandos de cópia entre vários clientes.

Esse cenário usa o comando ``find`` do UNIX para criar manifestos de arquivos ou diretórios:

```bash
user@build:/mnt/source > find . -mindepth 4 -maxdepth 4 -type d
./atj5b55c53be6-01/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-01/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-01/support/trace/rolling
./atj5b55c53be6-03/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-03/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-03/support/trace/rolling
./atj5b55c53be6-02/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-02/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-02/support/trace/rolling
```

Redirecione esse resultado para um arquivo: `find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`

Em seguida, você pode iterar pelo manifesto, usando comandos BASH para contar arquivos e determinar os tamanhos dos subdiretórios:

```bash
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l` `du -sh ${i}`"; done
244    3.5M    ./atj5b5ab44b7f-02/support/gsi/2018-07-18T00:07:03EDT
9      172K    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.8M    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T01:01:00UTC
131    13M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    6.2M    ./atj5b5ab44b7f-02/support/gsi/2018-07-20T21:59:41UTC
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
7      16K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:12:19UTC
8      83K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:17:17UTC
575    7.7M    ./atj5b5ab44b7f-02/support/cores/armada_main.2000.1531980253.gsi
33     4.4G    ./atj5b5ab44b7f-02/support/trace/rolling
281    6.6M    ./atj5b5ab44b7f-01/support/gsi/2018-07-18T00:07:03EDT
15     182K    ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-18T05:01:00UTC
244    17M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-19T01:01:01UTC
299    31M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T01:01:00UTC
256    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T21:59:41UTC_partial
889    7.7M    ./atj5b5ab44b7f-01/support/gsi/2018-07-20T21:59:41UTC
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
11     248K    ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:12:19UTC
11     88K     ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:17:17UTC
645    11M     ./atj5b5ab44b7f-01/support/cores/armada_main.2019.1531980253.gsi
33     4.0G    ./atj5b5ab44b7f-01/support/trace/rolling
244    2.1M    ./atj5b5ab44b7f-03/support/gsi/2018-07-18T00:07:03EDT
9      158K    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.3M    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T01:01:00UTC
131    12M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    8.4M    ./atj5b5ab44b7f-03/support/gsi/2018-07-20T21:59:41UTC
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_hpccache_catchup
7      159K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:12:19UTC
7      157K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:17:17UTC
576    12M     ./atj5b5ab44b7f-03/support/cores/armada_main.2013.1531980253.gsi
33     2.8G    ./atj5b5ab44b7f-03/support/trace/rolling
```

Por fim, você deve criar os comandos de cópia de arquivo reais para os clientes.

Se você tiver quatro clientes, use este comando:

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

Se você tiver cinco clientes, use algo parecido com isto:

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

E por seis... Extrapolar conforme necessário.

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

Você obterá *N* arquivos resultantes, um para cada um dos seus *N* clientes que têm os nomes de caminho para os diretórios de quatro níveis obtidos como parte da saída do comando `find`.

Use cada arquivo para criar o comando de cópia:

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

As opções acima gerará *N* arquivos, cada um com um comando de cópia por linha, que pode ser executado como um script BASH no cliente.

A meta é executar vários threads desses scripts simultaneamente por cliente em paralelo em vários clientes.
