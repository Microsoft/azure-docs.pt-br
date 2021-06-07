---
title: Implantação de DBMS de máquinas virtuais do Azure do IBM DB2 para carga de trabalho do SAP | Microsoft Docs
description: Implantação do DBMS de Máquinas Virtuais do IBM Db2 Azure para carga de trabalho do SAP
services: virtual-machines-linux,virtual-machines-windows
author: msjuergent
manager: bburns
tags: azure-resource-manager
keywords: Azure, DB2, SAP, IBM
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/20/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3f89f218c82505fd6bc261d41938d4619b32bf8a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101675971"
---
# <a name="ibm-db2-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Implantação do DBMS de Máquinas Virtuais do IBM Db2 Azure para carga de trabalho do SAP

Com o Microsoft Azure, você pode migrar seu aplicativo SAP existente em execução no IBM Db2 para LUW (Linux, UNIX e Windows) para máquinas virtuais do Azure. Com o SAP no IBM Db2 para LUW, os administradores e desenvolvedores ainda podem usar as mesmas ferramentas de desenvolvimento e administração que estão disponíveis localmente.
As informações gerais sobre como executar o SAP Business Suite no IBM Db2 para LUW podem ser encontradas na SCN (SAP Community Network) em <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>.

Para obter mais informações e atualizações sobre o SAP no Db2 para LUW no Azure, confira a Nota SAP [2233094]. 

Há vários artigos na carga de trabalho do SAP no Azure.  É recomendável iniciar em [carga de trabalho do SAP no Azure – Introdução](./get-started.md) e, em seguida, escolha a área de interesses

As seguintes notas SAP estão relacionadas ao SAP no Azure em relação à área de abordados neste documento:

| Número da observação |Title |
| --- |--- |
| [1928533] |Aplicativos SAP no Azure: Produtos suportados e tipos de VM do Azure |
| [2015553] |SAP no Microsoft Azure: Pré-requisitos de suporte |
| [1999351] |Solução de problemas de monitoramento aprimorado do Azure para SAP |
| [2178632] |Métricas-chave de monitoramento para SAP no Microsoft Azure |
| [1409604] |Virtualização no Windows: Monitoramento Avançado |
| [2191498] |SAP no Linux com o Azure: Monitoramento Avançado |
| [2233094] |DB6: Aplicativos SAP no Azure usando o IBM DB2 para Linux, UNIX e Windows - informações adicionais |
| [2243692] |Linux na VM (IaaS) do Microsoft Azure: Problemas de licença do SAP |
| [1984787] |SUSE Linux Enterprise Server 12: Notas de instalação |
| [2002167] |Red Hat Enterprise Linux 7.x: Instalação e atualização |
| [1597355] |Recomendação de troca de espaço para Linux |

Como uma pré-leitura para este documento, você deve ler o documento [Considerações para implantação de DBMS de Máquinas Virtuais do Azure para carga de trabalho do SAP](dbms_guide_general.md), bem como outros guias de [carga de trabalho do SAP na documentação do Azure](./get-started.md). 


## <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>Suporte de versão do IBM Db2 para Linux, UNIX e Windows
O SAP no IBM Db2 para LUW nos serviços de máquina virtual do Microsoft Azure é compatível desde a versão 10.5 do Db2.

Para obter informações sobre os tipos de VM do Azure e produtos SAP com suporte, consulte a Nota SAP [1928533].

## <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Diretrizes de configuração do IBM Db2 para Linux, UNIX e Windows para instalações do SAP em VMs do Azure
### <a name="storage-configuration"></a>Configuração de armazenamento
Para obter uma visão geral dos tipos de armazenamento do Azure para carga de trabalho do SAP, consulte o artigo [tipos de armazenamento do Azure para carga de trabalho do SAP](./planning-guide-storage.md) todos os arquivos de banco de dados devem ser armazenados em discos montados do armazenamento de blocos do Azure (Windows: NFFS, Linux: XFS, ext4 ou ext3). Qualquer tipo de unidade de rede ou compartilhamentos remotos como os seguintes serviços do Azure **não** têm suporte para arquivos de banco de dados: 

* [Serviço de arquivo Microsoft Azure](/archive/blogs/windowsazurestorage/introducing-microsoft-azure-file-service)

* [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)

Usando discos com base no Armazenamento de Blobs de Páginas do Azure ou nos Managed Disks, as declarações feitas em [Considerações para implantação de DBMS de máquinas virtuais do Azure para a carga de trabalho SAP](dbms_guide_general.md) se aplicarão também a implantações com o DBMS do Db2.

Conforme explicado anteriormente na parte geral do documento, existem cotas na taxa de transferência de IOPS para discos do Azure. As cotas exatas dependem do tipo de VM usado. Uma lista de tipos VM com suas cotas pode ser encontrada [aqui (Linux)][virtual-machines-sizes-linux] e [aqui (Windows)][virtual-machines-sizes-windows].

Desde que a cota de IOPS por disco atual seja suficiente, é possível armazenar todos os arquivos de banco de dados no único disco montado. Onde você sempre deve separar os arquivos de dados e arquivos de log de transações em discos/VHDs diferentes.

Para considerações sobre o desempenho, consulte também o capítulo “Data Safety and Performance Considerations for Database Directories” (Considerações sobre segurança de dados e desempenho para diretórios de banco de dados) nos guias de instalação SAP.

Como alternativa, você pode usar pools de armazenamento do Windows (disponíveis somente no Windows Server 2012 e superior) como considerações descritas [para implantação de DBMS de máquinas virtuais do Azure para carga de trabalho do SAP](dbms_guide_general.md) ou LVM ou mdadm no Linux para criar um dispositivo lógico grande em vários discos.

<!-- sapdata and saptmp are terms in the SAP and DB2 world and now spelling errors -->

Para os discos que contêm os caminhos de armazenamento do DB2 para seus `sapdata` `saptmp` diretórios e, você deve especificar um tamanho de setor de disco físico de 512 KB. Ao usar Pools de Armazenamento do Windows, você deve criá-los manualmente por meio da interface de linha de comando usando o parâmetro `-LogicalSectorSizeDefault`. Para obter mais informações, consulte <https://technet.microsoft.com/itpro/powershell/windows/storage/new-storagepool>.

Para a VM da série M do Azure, a latência de gravação nos logs de transação pode ser reduzida por fatores, comparados ao desempenho do armazenamento Premium do Azure, ao usar o Acelerador de gravação do Azure. Portanto, você deve implantar o Acelerador de Gravação do Azure para os VHDs que formam o volume para os logs de transação do Db2. Detalhes podem ser lidos no documento [Acelerador de Gravação](../../how-to-enable-write-accelerator.md).

## <a name="recommendation-on-vm-and-disk-structure-for-ibm-db2-deployment"></a>Recomendação sobre a estrutura de VM e disco para a implantação do IBM DB2

Os aplicativos IBM DB2 para SAP NetWeaver têm suporte em qualquer tipo de VM listado na nota de suporte do SAP [1928533].  As famílias de VMs recomendadas para executar o banco de dados IBM DB2 são Esd_v4/Eas_v4/Es_v3 e M/M_v2 Series para grandes bancos de dados de vários terabytes. O desempenho de gravação do disco de log de transações do IBM DB2 pode ser melhorado habilitando a Acelerador de Gravação da série M. 

Veja a seguir uma configuração de linha de base para vários tamanhos e usos do SAP em implantações do DB2 de pequeno a grande porte. A lista é baseada no armazenamento Premium do Azure. No entanto, o ultra Disk do Azure também tem suporte completo com DB2 e também pode ser usado. Basta usar os valores para capacidade, taxa de transferência de intermitência e IOPS de intermitência para definir a configuração de ultra Disk. Você pode limitar o IOPS para o/DB2/ <SID> /log_dir em cerca de 5000 IOPS. 

#### <a name="extra-small-sap-system-database-size-50---200-gb-example-solution-manager"></a>Sistema SAP extra pequeno: tamanho do banco de dados 50-200 GB: Gerenciador de soluções de exemplo
| Nome/tamanho da VM |Ponto de montagem do DB2 |Disco Premium do Azure |NR de discos |IOPS |Taxa de transferência [MB/s] |Tamanho [GB] |IOPS de intermitência |Aos de intermitência [GB] | Tamanho da distribuição | Cache |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E4ds_v4 |/db2 |P6 |1 |240  |50  |64  |3\.500  |170  ||  |
|vCPU: 4 |/DB2/ <SID> /sapdata |P10 |2 |1,000  |200  |256  |7.000  |340  |256 KB |ReadOnly |
|RAM: 32 GiB |/DB2/ <SID> /saptmp |P6 |1 |240  |50  |128  |3\.500  |170  | ||
| |/DB2/ <SID> /log_dir |P6 |2 |480  |100  |128  |7.000  |340  |64 KB ||
| |/DB2/ <SID> /offline_log_dir |P10 |1 |500  |100  |128  |3\.500  |170  || |

#### <a name="small-sap-system-database-size-200---750-gb-small-business-suite"></a>Sistema SAP pequeno: tamanho do banco de dados 200-750 GB: Small Business Suite
| Nome/tamanho da VM |Ponto de montagem do DB2 |Disco Premium do Azure |NR de discos |IOPS |Taxa de transferência [MB/s] |Tamanho [GB] |IOPS de intermitência |Aos de intermitência [GB] | Tamanho da distribuição | Cache |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E16ds_v4 |/db2 |P6 |1 |240  |50  |64  |3\.500  |170  || |
|vCPU: 16 |/DB2/ <SID> /sapdata |P15 |4 |4.400  |500  |1, 24  |14.000  |680  |256 KB |ReadOnly |
|RAM: 128 GiB |/DB2/ <SID> /saptmp |P6 |2 |480  |100  |128  |7.000  |340  |128 KB ||
| |/DB2/ <SID> /log_dir |P15 |2 |2.200  |250  |512  |7.000  |340  |64 KB ||
| |/DB2/ <SID> /offline_log_dir |P10 |1 |500  |100  |128  |3\.500  |170  ||| 

#### <a name="medium-sap-system-database-size-500---1000-gb-small-business-suite"></a>Sistema SAP médio: tamanho do banco de dados 500-1000 GB: Small Business Suite
| Nome/tamanho da VM |Ponto de montagem do DB2 |Disco Premium do Azure |NR de discos |IOPS |Taxa de transferência [MB/s] |Tamanho [GB] |IOPS de intermitência |Aos de intermitência [GB] | Tamanho da distribuição | Cache |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E32ds_v4 |/db2 |P6 |1 |240  |50  |64  |3\.500  |170  || |
|vCPU: 32 |/DB2/ <SID> /sapdata |P30 |2 |10.000  |400  |2, 48  |10.000  |400  |256 KB |ReadOnly |
|RAM: 256 GiB |/DB2/ <SID> /saptmp |P10 |2 |1,000  |200  |256  |7.000  |340  |128 KB ||
| |/DB2/ <SID> /log_dir |P20 |2 |4.600  |300  |1, 24  |7.000  |340  |64 KB ||
| |/DB2/ <SID> /offline_log_dir |P15 |1 |1.100  |125  |256  |3\.500  |170  ||| 

#### <a name="large-sap-system-database-size-750---2000-gb-business-suite"></a>Sistema SAP grande: tamanho do banco de dados 750-2000 GB: Business Suite
| Nome/tamanho da VM |Ponto de montagem do DB2 |Disco Premium do Azure |NR de discos |IOPS |Taxa de transferência [MB/s] |Tamanho [GB] |IOPS de intermitência |Aos de intermitência [GB] | Tamanho da distribuição | Cache |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E64ds_v4 |/db2 |P6 |1 |240  |50  |64  |3\.500  |170  || |
|vCPU: 64 |/DB2/ <SID> /sapdata |P30 |4 |20.000  |800  |4.096  |20.000  |800  |256 KB |ReadOnly |
|RAM: 504 GiB |/DB2/ <SID> /saptmp |P15 |2 |2.200  |250  |512  |7.000  |340  |128 KB ||
| |/DB2/ <SID> /log_dir |P20 |4 |9.200  |600  |2, 48  |14.000  |680  |64 KB ||
| |/DB2/ <SID> /offline_log_dir |P20 |1 |2\.300  |150  |512  |3\.500  |170  || |

#### <a name="large-multi-terabyte-sap-system-database-size-2-tb-global-business-suite-system"></a>Grande sistema SAP de vários terabytes: tamanho do banco de dados 2 TB +: Global Business Suite System
| Nome/tamanho da VM |Ponto de montagem do DB2 |Disco Premium do Azure |NR de discos |IOPS |Taxa de transferência [MB/s] |Tamanho [GB] |IOPS de intermitência |Aos de intermitência [GB] | Tamanho da distribuição | Cache |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|M128s |/db2 |P10 |1 |500  |100  |128  |3\.500  |170  || |
|vCPU: 128 |/DB2/ <SID> /sapdata |P40 |4 |30,000  |1, 0  |8,192  |30,000  |1, 0  |256 KB |ReadOnly |
|RAM: 2048 GiB |/DB2/ <SID> /saptmp |P20 |2 |4.600  |300  |1, 24  |7.000  |340  |128 KB ||
| |/DB2/ <SID> /log_dir |P30 |4 |20.000  |800  |4.096  |20.000  |800  |64 KB |WriteAccelerator |
| |/DB2/ <SID> /offline_log_dir |P30 |1 |5\.000  |200  |1, 24  |5\.000  |200  || |


### <a name="backuprestore"></a>Backup/restauração
A funcionalidade de backup/restauração para o IBM Db2 para LUW é compatível da mesma maneira que no Hyper-V e em sistemas operacionais Windows Server standard.

Verifique se você tem uma estratégia de backup de banco de dados válida em vigor. 

Como em implantações bare-metal, o desempenho de backup/restauração depende de quantos volumes podem ser lidos em paralelo e qual pode ser a taxa de transferência desses volumes. Além disso, o consumo de CPU usado pela compactação de backup pode desempenhar uma função significativa nas VMs com até oito threads por CPU. Portanto, é possível supor que:

* Quanto menor o número de discos usados para armazenar os dispositivos de banco de dados, menor é a taxa de transferência geral na leitura
* Quanto menor o número de threads de CPU na VM, mais severo é o impacto da compactação de backup
* Quanto menos destinos (Diretórios de Faixa, discos) nos quais gravar o backup, menor é a taxa de transferência

Para aumentar o número de destinos nos quais gravar, é possível usar/combinar duas opções dependendo das suas necessidades:

* Como dividir o volume de destino de backup em vários discos para melhorar a taxa de transferência de IOPS nesse volume dividido
* Usando mais de um diretório de destino no qual gravar o backup

>[!NOTE]
>O Db2 no Windows não é compatível com a tecnologia VSS do Windows. Como resultado, o backup VM consistente de aplicativo do serviço de Backup do Azure não pode ser aproveitado para VMs DBMS Db2 no qual é implantado.

### <a name="high-availability-and-disaster-recovery"></a>Alta disponibilidade e recuperação de desastre

#### <a name="linux-pacemaker"></a>Pacemaker Linux

Há suporte para o HADR (recuperação de desastre de alta disponibilidade) do DB2 com pacemaker. Há suporte para os sistemas operacionais SLES e RHEL. Essa configuração permite a alta disponibilidade do IBM DB2 para SAP. Guias de implantação:
* SLES: [alta disponibilidade do IBM DB2 LUW em VMs do Azure em SuSE Linux Enterprise Server com pacemaker](dbms-guide-ha-ibm.md) 
* RHEL: [alta disponibilidade do IBM DB2 LUW em VMs do Azure no Red Hat Enterprise Linux Server](high-availability-guide-rhel-ibm-db2-luw.md)

#### <a name="windows-cluster-server"></a>Servidor de cluster do Windows

Não há suporte para o MSCS (Microsoft Cluster Server).

A HADR (alta disponibilidade e recuperação de desastre) do Db2 é compatível. Se as máquinas virtuais da configuração de HA tiverem uma resolução de nome funcionando, a configuração no Azure não será diferente de nenhuma configuração feita localmente. Não é recomendável confiar apenas na resolução de IP.

Não use a replicação geográfica para as contas de armazenamento que armazenam os discos de banco de dados. Para obter mais informações, consulte as [considerações de documento para a implantação de DBMS de máquinas virtuais do Azure para carga de trabalho do SAP](dbms_guide_general.md). 

### <a name="accelerated-networking"></a>Rede Acelerada
Para implantações do Db2 no Windows, é altamente recomendável usar a funcionalidade do Azure de Rede Acelerada, conforme descrito no documento [Rede Acelerada do Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Também considere as recomendações feitas em [Considerações para implantação de DBMS de Máquinas Virtuais do Azure para a carga de trabalho SAP](dbms_guide_general.md). 


### <a name="specifics-for-linux-deployments"></a>Informações específicas para implantações do Linux
Desde que a cota de IOPS por disco atual seja suficiente, é possível armazenar todos os arquivos de banco de dados no único disco. Onde você sempre deve separar os arquivos de dados e arquivos de log de transações em discos/VHDs diferentes.

Como alternativa, se a taxa de transferência IOPS ou e/s de um único VHD do Azure não for suficiente, você pode usar LVM (Logical Volume Manager) ou MDADM conforme descrito no documento [considerações de implantação de DBMS de máquinas virtuais do Azure para carga de trabalho do SAP](dbms_guide_general.md)para criar um grande dispositivo lógico em vários discos.
Para os discos que contém os caminhos de armazenamento do Db2 para os sapdata e saptmp, você precisa especificar um tamanho de setor do disco físico de 512 KB.

<!-- sapdata and saptmp are terms in the SAP and DB2 world and now spelling errors -->


### <a name="other"></a>Outros
Todas as outras áreas gerais, como o monitoramento do SAP ou Conjuntos de Disponibilidade do Azure se aplicam como descritas no documento [Considerações para a implantação DBMS de Máquinas Virtuais do Azure para carga de trabalho SAP](dbms_guide_general.md) para implantações de VMs com o Banco de Dados IBM também.

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692


## <a name="next-steps"></a>Próximas etapas
Leia o artigo 

- [Considerações para Implantação do DBMS de Máquinas Virtuais do Azure para carga de trabalho do SAP](dbms_guide_general.md)

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d 
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c 
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md 
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab 
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e 
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e 
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc 
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d 
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f 
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca 
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b 
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d 
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md 
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff 
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f 
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a 
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c 
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef 
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a 
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d 
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd 
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f 

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam 
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:../../../resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability-linux]:../../linux/manage-availability.md
[virtual-machines-manage-availability-windows]:../../windows/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md
[virtual-machines-sql-server-infrastructure-services]:../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md
[virtual-machines-sql-server-performance-best-practices]:../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/resources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md