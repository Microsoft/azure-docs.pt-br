---
title: Desempenho do Oracle Database em volumes únicos do Azure NetApp Files | Microsoft Docs
description: Descreve os resultados do teste de desempenho de um Azure NetApp Files em volume único no Oracle Database.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: b-juche
ms.openlocfilehash: c6cdf2f6dada0aa4dea2f70f18237b7ee39e3ea1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91571269"
---
# <a name="oracle-database-performance-on-azure-netapp-files-single-volumes"></a>Desempenho do Oracle Database em volumes únicos do Azure NetApp Files

Este artigo aborda os tópicos a seguir sobre Oracle na nuvem. Esses tópicos podem ser de interesse particular para um administrador de banco de dados, arquiteto de nuvem ou de armazenamento:   

* Quando você gera uma carga de trabalho OLTP (processamento de transações online, principalmente, de E/S aleatória) ou uma carga de trabalho de OLAP (processamento analítico online, principalmente, de E/S sequencial), como fica o desempenho?   
* Qual é a diferença no desempenho entre o cliente regular kNFS (kernel NFS) do Linux e o cliente NFS direto próprio da Oracle?
* No que diz respeito à largura de banda, o desempenho de um volume único do Azure NetApp Files é o suficiente?

## <a name="testing-environment-and-components"></a>Ambiente e componentes de teste

O diagrama a seguir ilustra o ambiente usado para teste. Para consistência e simplicidade, os guias estratégicos de Ansible foram usados para implantar todos os elementos da base de testes.

![Ambiente de teste do Oracle](../media/azure-netapp-files/performance-oracle-test-environment.png)  

### <a name="virtual-machine-configuration"></a>Configuração de máquina virtual

Os testes usaram a seguinte configuração para a máquina virtual:
* Sistema operacional:   
    RedHat Enterprise Linux 7.8 (wle-ora01)
* Tipos de instância:   
    Dois modelos foram usados nos testes – D32s_v3 e D64s_v3
* Contagem de interface de rede:   
    Um (1) colocado na sub-rede 3  
* Discos:   
    O SO e os binários do Oracle foram colocados em um único disco Premium

### <a name="azure-netapp-files-configuration"></a><a name="anf_config"></a>Configuração do Azure NetApp Files
Os testes usaram a seguinte configuração do Azure NetApp Files:   

* Tamanho do pool de capacidade:  
    Foram configurados vários tamanhos de pool: 4 TiB, 8 TiB, 16 TiB, 32 TiB 
* Nível de serviço:  
    Ultra (128 MiB/s de largura de banda por 1 TiB de capacidade de volume alocado)
* Volumes:  
    Um e dois testes de volume foram avaliados

### <a name="workload-generator"></a>Gerador de carga de trabalho 

Os testes usaram um SLOB 2.5.4.2. gerador de cargas de trabalho. O SLOB (pequeno parâmetro de comparação da Oracle) é um gerador de carga de trabalho conhecido no espaço Oracle desenvolvido para enfatizar e testar o subsistema de E/S com uma carga de trabalho de E/S física de buffers da SGA.  

O SLOB 2.5.4.2 não dá suporte ao PDB (banco de dados conectável). Por isso, uma alteração foi adicionada aos `setup.sh` scripts e `runit.sh` para adicionar a ele suporte ao PDB.  

As variáveis SLOB usadas nos testes são descritas nas seções a seguir.

#### <a name="workload-80-select-20-update--random-io--slobconf-variables"></a>Carga de trabalho 80% SELECT, 20% UPDATE | E/S Aleatória – `slob.conf` variáveis   

`UPDATE_PCT=20`   
`SCAN_PCT=0`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

#### <a name="workload-100-select--sequential-io--slobconf-variables"></a>Carga de trabalho 100% SELECT | E/S Sequencial – `slob.conf` variáveis

`UPDATE_PCT=0`   
`SCAN_PCT=100`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

### <a name="database"></a>Banco de dados

A versão do Oracle usada para os testes é o Oracle Database Enterprise Edition 19.3.0.0.

Os parâmetros do Oracle são os seguintes:  
* `sga_max_size`: 4096M
* `sga_target`: 4096
* `db_writer_processes`: 12
* `awr_pdb_autoflush_enabled`: true
* `filesystemio_options`: SETALL
* `log_buffer`: 134217728

Um PDB foi criado para o banco de dados do SLOB.

O diagrama a seguir mostra o espaço de tabela chamado PERFIO com o tamanho de 600 GB (20 arquivos de dados, 30 GB cada) criado para hospedar quatro esquemas de usuário SLOB. Cada esquema de usuário tinha o tamanho de 125 GB.

![Oracle Database](../media/azure-netapp-files/performance-oracle-tablespace.png)  

## <a name="performance-metrics"></a>Métricas de desempenho

O objetivo era relatar o desempenho de E/S como foi experimentado pelo aplicativo. Portanto, todos os diagramas neste artigo usam métricas relatadas pelo banco de dados do Oracle por meio de seus relatórios de AWR (repositório automático de carga de trabalho). As métricas usadas nos diagramas são essas:   

* **Média de Solicitações de E/S**   
    Corresponde à soma da média de Solicitações de E/S de Leitura por segundo e a média de Solicitações de E/S de Gravação por segundo da seção de perfil de carregamento
* **Média de MB de E/S por segundo**   
    Corresponde à soma da média de MB de E/S de Leitura por segundo e a média de MB de E/S de Gravação por segundo da seção de perfil de carregamento
* **Latência Média de leitura**   
    Corresponde à latência média do Evento de Espera do Oracle "leitura sequencial do arquivo de BD" em microssegundos
* **Número de conversas/esquemas**   
    Corresponde ao número de conversas SLOB por esquema de usuário

## <a name="performance-measurement-results"></a>Resultados de medição de desempenho  

Esta seção descreve os resultados da medição de desempenho.

### <a name="linux-knfs-client-vs-oracle-direct-nfs"></a>Cliente kNFS do Linux vs. Oracle Direct NFS

Esse cenário estava em execução em uma VM do Azure Standard_D32s_v3 (Intel E5-2673 v4 @ 2.30 GHz). A carga de trabalho é de 75% SELECT e 25% UPDATE, principalmente a E/S Aleatória e com uma ocorrência de buffer de banco de dados de ~ 7,5%. 

Conforme mostrado no diagrama a seguir, o cliente Oracle DNFS forneceu até 2.8x mais taxa de transferência do que um cliente regular kNFS do Linux:  

![Cliente kNFS do Linux comparado ao Oracle Direct NFS](../media/azure-netapp-files/performance-oracle-kfns-compared-dnfs.png)  

O diagrama a seguir mostra a curva de latência para as operações de leitura. Nesse contexto, o afunilamento do cliente kNFS é a conexão de soquete TCP de NFS única estabelecida entre o cliente e o servidor NFS (o volume Azure NetApp Files).  

![Cliente kNFS do Linux comparado a curva de latência do Oracle Direct NFS](../media/azure-netapp-files/performance-oracle-latency-curve.png)  

O cliente DNFS foi capaz de enviar mais solicitações de E/S por segundo devido à sua capacidade de criar centenas de conexões de soquete TCP, aproveitando assim o paralelismo. Conforme descrito em [configuração de Azure NetApp Files](#anf_config), cada TiB adicional de capacidade alocada permite um 128MiB/s adicional de largura de banda. O DNFS com 1 GiB/s de taxa de transferência, que é o limite imposto pela seleção de capacidade de 8 TiB. Com mais capacidade, a taxa de transferência teria sido maior.

A taxa de transferência é apenas uma das considerações. Outra consideração é a latência, que tem o impacto principal na experiência do usuário. Como mostra o diagrama a seguir, podem ocorrer aumentos de latência mais rapidamente em kNFS do que em DNFS. 

![Cliente kNFS do Linux comparado a curva de leitura do Oracle Direct NFS](../media/azure-netapp-files/performance-oracle-read-latency.png)  

Os histogramas fornecem um ótimo insight sobre as latências de banco de dados. O diagrama a seguir fornece uma visão completa da perspectiva da "leitura sequencial do arquivo de banco de dados", ao usar DNFS no ponto de dados de simultaneidade mais alto (32 conversas/esquema). Conforme mostrado no diagrama a seguir, 47% de todas as operações de leitura foram respeitadas entre 512 microssegundos e 1000 microssegundos, enquanto 90% de todas as operações de leitura foram atendidas em uma latência abaixo de 2 ms.

![Cliente kNFS do Linux comparado aos histogramas do Oracle Direct NFS](../media/azure-netapp-files/performance-oracle-histogram-read-latency.png)  

Enfim, fica claro que o DNFS é necessário quando se trata de melhorar o desempenho de uma instância de banco de dados do Oracle em NFS.

### <a name="single-volume-performance-limits"></a>Limites de desempenho de volume único

Esta seção descreve os limites de desempenho de um único volume com E/S Aleatória e Sequencial. 

#### <a name="random-io"></a>E/S Aleatória

O DNFS é capaz de consumir muito mais largura de banda do que o fornecido por uma cota de desempenho do Azure NetApp Files de 8 TB. Ao aumentar a capacidade do volume de Azure NetApp Files para 16 TiB, que é uma alteração instantânea, a quantidade de largura de banda do volume aumentou de 1024 MiB/s por 2X para 2048 MiB/s. 

O diagrama a seguir mostra uma configuração de uma carga de trabalho de atualização de 80% selecionada e 20% e com uma taxa de acertos de buffer de banco de dados de 8%. O SLOB conseguiu impulsionar um volume único para solicitações de E/S de NFS de 200.000 por segundo. Considerando que cada operação tem o tamanho de 8 KiB, o sistema em teste foi capaz de entregar ~ 200.000 solicitações de E/S ou 1600 MiB por segundo.
 
![Taxa de transferência do Oracle DNFS](../media/azure-netapp-files/performance-oracle-dnfs-throughput.png)  

O diagrama de curva de latência de leitura a seguir mostra que conforme a taxa de transferência de leitura aumenta, a latência aumenta suavemente abaixo da linha de 1 ms e atinge a joelho da curva em ~165.000 média de solicitações de E/S de leitura por segundo na latência média de leitura de ~1.3 ms.  Esse valor é um valor de latência incrível para uma taxa de E/S inatingível em quase todas as tecnologias na nuvem do Azure. 

![Curva de latência do Oracle DNFS](../media/azure-netapp-files/performance-oracle-dnfs-latency-curve.png)  

#### <a name="sequential-io"></a>E/S Sequencial  

Conforme mostrado no diagrama a seguir, nem toda E/S é aleatória por natureza, considerando um backup do RMAN ou uma verificação de tabela completa, por exemplo, como cargas de trabalho que exigem tanta largura de banda quanto podem obter.  Usando a mesma configuração descrita anteriormente, mas com o volume redimensionado para 32 TiB, o diagrama a seguir mostra que uma única instância de Oracle DB pode impulsionar até 3.900 MB/s de taxa de transferência, muito perto da cota de desempenho do volume do Azure NetApp Files de 32 TB (128 MB/s * 32 = 4096 MB/s).

![E/S do Oracle DNFS](../media/azure-netapp-files/performance-oracle-dnfs-io.png)  

Resumindo, o Azure NetApp Files ajuda você a levar os seus bancos de dados Oracle para a nuvem. Ele oferece desempenho sempre que você precisar. Você pode redimensionar de forma dinâmica e sem interrupções sua cota de volume a qualquer momento.

## <a name="next-steps"></a>Próximas etapas

- [Recomendações do teste de parâmetros de comparação de desempenho para o Azure NetApp Files](azure-netapp-files-performance-metrics-volumes.md)
- [Parâmetros de comparação de desempenho para o Linux](performance-benchmarks-linux.md)