---
title: Desempenho do banco de dados Oracle em Azure NetApp Files único volume | Microsoft Docs
description: Descreve os resultados do teste de desempenho de um Azure NetApp Files único volume no banco de dados Oracle.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91571269"
---
# <a name="oracle-database-performance-on-azure-netapp-files-single-volumes"></a>Desempenho do Oracle Database em volumes únicos do Azure NetApp Files

Este artigo aborda os seguintes tópicos sobre Oracle na nuvem. Esses tópicos podem ser de interesse particular para um administrador de banco de dados, arquiteto de nuvem ou arquiteto de armazenamento:   

* Quando você orienta uma carga de trabalho OLTP (processamento de transações online) (principalmente, e/s aleatória) ou uma carga de trabalho de processamento analítico online (OLAP) (principalmente, e/s sequencial), o desempenho se parece?   
* Qual é a diferença no desempenho entre o cliente normal do kNFS (Linux kernel NFS) e o próprio cliente NFS direto da Oracle?
* No que diz respeito à largura de banda, o desempenho de um único volume Azure NetApp Files é suficiente?

## <a name="testing-environment-and-components"></a>Ambiente e componentes de teste

O diagrama a seguir ilustra o ambiente usado para teste. Para fins de consistência e simplicidade, os guias estratégicos de Ansible foram usados para implantar todos os elementos da base de testes.

![Ambiente de teste Oracle](../media/azure-netapp-files/performance-oracle-test-environment.png)  

### <a name="virtual-machine-configuration"></a>Configuração de máquina virtual

Os testes usaram a seguinte configuração para a máquina virtual:
* Sistema operacional:   
    RedHat Enterprise Linux 7,8 (wle-ora01)
* Tipos de instância:   
    Dois modelos foram usados em testes – D32s_v3 e D64s_v3
* Contagem de interface de rede:   
    Um (1) colocado na sub-rede 3  
* Discos   
    OS binários e o sistema operacional Oracle foram colocados em um único disco Premium

### <a name="azure-netapp-files-configuration"></a><a name="anf_config"></a>Configuração de Azure NetApp Files
Os testes usaram a seguinte configuração de Azure NetApp Files:   

* Tamanho do pool de capacidade:  
    Vários tamanhos do pool foram configurados: 4 TiB, 8 TiB, 16 TiB, 32 TiB 
* Nível de serviço:  
    Ultra (128 MiB/s de largura de banda por 1 TiB de capacidade de volume alocado)
* Volumes:  
    Um e dois testes de volume foram avaliados

### <a name="workload-generator"></a>Gerador de carga de trabalho 

A carga de trabalho usada para testes gerou SLOB 2.5.4.2. SLOB (um pequeno benchmark da Oracle) é um gerador de carga de trabalho conhecido no espaço Oracle projetado para enfatizar e testar o subsistema de e/s com uma carga de trabalho de e/s física de SGA buffers.  

SLOB 2.5.4.2 não dá suporte ao PDB (banco de dados conectável). Como tal, uma alteração foi adicionada aos `setup.sh` scripts e `runit.sh` para adicionar suporte a PDB a ele.  

As variáveis SLOB usadas nos testes são descritas nas seções a seguir.

#### <a name="workload-80-select-20-update--random-io--slobconf-variables"></a>Carga de trabalho 80% SELECT, 20% UPDATE | E/s aleatória – `slob.conf` variáveis   

`UPDATE_PCT=20`   
`SCAN_PCT=0`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

#### <a name="workload-100-select--sequential-io--slobconf-variables"></a>Carga de trabalho 100% SELECT | E/s sequencial – `slob.conf` variáveis

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

A versão do Oracle usada para os testes é Oracle Database Enterprise Edition 19.3.0.0.

Os parâmetros do Oracle são os seguintes:  
* `sga_max_size`: 4096m
* `sga_target`: 4096
* `db_writer_processes`: 12
* `awr_pdb_autoflush_enabled`: true
* `filesystemio_options`: SETALL
* `log_buffer`: 134217728

Um PDB foi criado para o banco de dados SLOB.

O diagrama a seguir mostra o espaço de tabela chamado PERFIO com 600 GB de tamanho (20 arquivos de dados, 30 GB cada) criado para hospedar quatro esquemas de usuário SLOB. Cada esquema de usuário tinha 125 GB de tamanho.

![Oracle Database](../media/azure-netapp-files/performance-oracle-tablespace.png)  

## <a name="performance-metrics"></a>Métricas de desempenho

O objetivo era relatar o desempenho de e/s como foi experimentado pelo aplicativo. Portanto, todos os diagramas neste artigo usam métricas relatadas pelo banco de dados Oracle por meio de seus relatórios de AWR (repositório automático de carga de trabalho). As métricas usadas nos diagramas são as seguintes:   

* **Média de solicitações de e/s**   
    Corresponde à soma de média de solicitações de e/s de leitura/seg e média de solicitações de e/s de gravação por segundo da seção de perfil de carregamento
* **Média de MB de e/s**   
    Corresponde à soma de média de leitura MB de e/s e média de e/s de gravação por segundo da seção de perfil de carregamento
* **Latência média de leitura**   
    Corresponde à latência média do evento de espera do Oracle "leitura sequencial do arquivo de BD" em microssegundos
* **Número de threads/esquema**   
    Corresponde ao número de threads SLOB por esquema de usuário

## <a name="performance-measurement-results"></a>Resultados de medição de desempenho  

Esta seção descreve os resultados da medição de desempenho.

### <a name="linux-knfs-client-vs-oracle-direct-nfs"></a>Cliente Linux kNFS vs. NFS direto da Oracle

Esse cenário estava em execução em uma VM do Azure Standard_D32s_v3 (Intel E5-2673 v4 @ 2,30 GHz). A carga de trabalho é de 75% SELECT e 25% UPDATE, principalmente a e/s aleatória e com uma queda de buffer de banco de dados de ~ 7,5%. 

Conforme mostrado no diagrama a seguir, o cliente Oracle DNFS forneceu até 2,8 x mais taxa de transferência do que o cliente kNFS do Linux regular:  

![Cliente Linux kNFS comparado com NFS direto da Oracle](../media/azure-netapp-files/performance-oracle-kfns-compared-dnfs.png)  

O diagrama a seguir mostra a curva de latência para as operações de leitura. Nesse contexto, o afunilamento do cliente kNFS é a conexão de soquete TCP de NFS única estabelecida entre o cliente e o servidor NFS (o volume Azure NetApp Files).  

![Cliente Linux kNFS comparado com a curva de latência NFS do Oracle Direct](../media/azure-netapp-files/performance-oracle-latency-curve.png)  

O cliente DNFS foi capaz de enviar mais solicitações de e/s por push devido à sua capacidade de criar centenas de conexões de soquete TCP, aproveitando assim o paralelismo. Conforme descrito em [configuração de Azure NetApp files](#anf_config), cada Tib adicional de capacidade alocada permite um 128MiB/s adicional de largura de banda. DNFS com 1 GiB/s de taxa de transferência, que é o limite imposto pela seleção de capacidade de 8 TiB. Dada mais capacidade, mais taxa de transferência teria sido orientada.

A taxa de transferência é apenas uma das considerações. Outra consideração é a latência, que tem o impacto principal na experiência do usuário. Como mostra o diagrama a seguir, as aumentos de latência podem ser muito mais rapidamente com kNFS do que com DNFS. 

![Cliente Linux kNFS comparado com a latência de leitura do NFS direto do Oracle](../media/azure-netapp-files/performance-oracle-read-latency.png)  

Os histogramas fornecem excelente insight sobre latências de banco de dados. O diagrama a seguir fornece uma visão completa da perspectiva da "leitura sequencial do arquivo de banco de dados", ao usar DNFS no ponto de data de simultaneidade mais alto (32 threads/esquema). Conforme mostrado no diagrama a seguir, 47% de todas as operações de leitura foram respeitadas entre 512 microssegundos e 1000 microssegundos, enquanto 90% de todas as operações de leitura foram atendidas em uma latência abaixo de 2 ms.

![Cliente Linux kNFS comparado com os histogramas NFS do Oracle Direct](../media/azure-netapp-files/performance-oracle-histogram-read-latency.png)  

Concluindo, fica claro que o DNFS é necessário quando se trata de melhorar o desempenho de uma instância de banco de dados Oracle em NFS.

### <a name="single-volume-performance-limits"></a>Limites de desempenho de volume único

Esta seção descreve os limites de desempenho de um único volume com e/s aleatória e e/s sequencial. 

#### <a name="random-io"></a>E/s aleatória

O DNFS é capaz de consumir muito mais largura de banda do que o fornecido por uma cota de desempenho de Azure NetApp Files de 8 TB. Ao aumentar a capacidade do volume de Azure NetApp Files para 16 TiB, que é uma alteração instantânea, a quantidade de largura de banda do volume aumentou de 1024 MiB/s por 2X para 2048 MiB/s. 

O diagrama a seguir mostra uma configuração de uma carga de trabalho de atualização de 80% Select e 20% e com uma taxa de acertos de buffer de banco de dados de 8%. O SLOB conseguiu gerar um único volume para solicitações de e/s de NFS de 200.000 por segundo. Considerando que cada operação tem o tamanho de 8 KiB, o sistema em teste foi capaz de entregar ~ 200.000 solicitações de e/s ou 1600 MiB/s.
 
![Produtividade do Oracle DNFS](../media/azure-netapp-files/performance-oracle-dnfs-throughput.png)  

O diagrama de curva de latência de leitura a seguir mostra que, à medida que a taxa de transferência de leitura aumenta, a latência aumenta suavemente abaixo da linha de 1 ms e atinge a joelho da curva em ~ 165.000 solicitações de e/s médias de leitura por segundo na latência média de leitura de ~ 1,3 ms.  Esse valor é um valor de latência incrível para uma taxa de e/s não atingível com quase qualquer outra tecnologia na nuvem do Azure. 

![Curva de latência do Oracle DNFS](../media/azure-netapp-files/performance-oracle-dnfs-latency-curve.png)  

#### <a name="sequential-io"></a>E/s sequencial  

Conforme mostrado no diagrama a seguir, nem toda e/s é aleatória por natureza, considerando um backup do RMAN ou uma verificação de tabela completa, por exemplo, como cargas de trabalho que exigem tanta largura de banda quanto podem ser obtidas.  Usando a mesma configuração descrita anteriormente, mas com o volume redimensionado para 32 TiB, o diagrama a seguir mostra que uma única instância de Oracle DB pode impulsionar até 3.900 MB/s de taxa de transferência, muito perto da cota de desempenho do volume Azure NetApp Files de 32 TB (128 MB/s * 32 = 4096 MB/s).

![Oracle DNFS e/s](../media/azure-netapp-files/performance-oracle-dnfs-io.png)  

Em resumo, Azure NetApp Files ajuda você a levar seus bancos de dados Oracle para a nuvem. Ele oferece desempenho quando o banco de dados exige isso. Você pode redimensionar de forma dinâmica e sem interrupções sua cota de volume a qualquer momento.

## <a name="next-steps"></a>Próximas etapas

- [Recomendações do teste de parâmetros de comparação de desempenho para o Azure NetApp Files](azure-netapp-files-performance-metrics-volumes.md)
- [Parâmetros de comparação de desempenho para o Linux](performance-benchmarks-linux.md)