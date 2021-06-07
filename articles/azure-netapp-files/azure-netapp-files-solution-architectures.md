---
title: Arquiteturas da solução usando o Azure NetApp Files – Microsoft Docs
description: Fornece referências a melhores práticas para arquiteturas de solução usando o Azure NetApp Files.
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
ms.date: 04/06/2021
ms.author: b-juche
ms.openlocfilehash: d5ff0f2444aac153116e8e33a6ed2af74c93f057
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553508"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>Arquiteturas da solução usando o Azure NetApp Files
Este artigo fornece referências a melhores práticas que podem ajudar você a entender as arquiteturas de solução para usar o Azure NetApp Files.  

O seguinte diagrama resume as categorias de arquiteturas de solução que o Azure NetApp Files oferece:

![Categorias de arquitetura da solução](../media/azure-netapp-files/solution-architecture-categories.png)

## <a name="linux-oss-apps-and-database-solutions"></a>Aplicativos de software livre Linux e soluções de banco de dados

Esta seção fornece referências a soluções para bancos de dados e aplicativos de software livre do Linux. 

### <a name="oracle"></a>Oracle

* [Guia de melhores práticas de implantação do Oracle no Azure usando o Azure NetApp Files](https://www.netapp.com/us/media/tr-4780.pdf)
* [Imagens da VM Oracle e sua implantação no Microsoft Azure: opções de configuração de armazenamento compartilhado](../virtual-machines/workloads/oracle/oracle-vm-solutions.md#shared-storage-configuration-options)
* [Desempenho do Oracle Database em volumes únicos do Azure NetApp Files](performance-oracle-single-volumes.md)
* [Benefícios do uso do Azure NetApp Files com o Oracle Database](solutions-benefits-azure-netapp-files-oracle-database.md)

### <a name="machine-learning"></a>Machine Learning
*   [Cloudera Machine Learning](https://docs.cloudera.com/machine-learning/cloud/requirements-azure/topics/ml-requirements-azure.html)

## <a name="windows-apps-and-sql-server-solutions"></a>Aplicativos do Windows e soluções do SQL Server

Esta seção fornece referências para aplicativos do Windows e soluções do SQL Server.

### <a name="file-sharing-and-global-file-caching"></a>Compartilhamento de arquivos e cache de arquivos global

* [Compilar o próprio NFS do Azure? Compartilhamentos de arquivos de combate do Linux na nuvem](https://cloud.netapp.com/blog/ma-anf-blg-build-your-own-linux-nfs-file-shares)
* [Cache de arquivos global/implantação do Azure NetApp Files](https://youtu.be/91LKb1qsLIM)
* [Conformidade com a nuvem para o Azure NetApp Files](https://cloud.netapp.com/hubfs/Cloud%20Compliance%20for%20Azure%20NetApp%20Files%20-%20November%202020.pdf)

### <a name="sql-server"></a>SQL Server

* [Guia de implantação do SQL Server no Azure usando o Azure NetApp Files](https://www.netapp.com/pdf.html?item=/media/27154-tr-4888.pdf)
* [Benefícios de usar o Azure NetApp Files para implantação do SQL Server](solutions-benefits-azure-netapp-files-sql-server.md)
* [Implantar o SQL Server no SMB com o Azure NetApp Files](https://www.youtube.com/watch?v=x7udfcYbibs)
* [Implantar o cluster de failover do Always-On do SQL Server no SMB com o Azure NetApp Files](https://www.youtube.com/watch?v=zuNJ5E07e8Q) 
* [Implantar os grupos de disponibilidade do Always-On com o Azure NetApp Files](https://www.youtube.com/watch?v=y3VQmzzeyvc) 

## <a name="sap-on-azure-solutions"></a>Soluções SAP no Azure

Esta seção fornece referências às soluções SAP no Azure. 

### <a name="generic-sap-and-sap-netweaver"></a>SAP e SAP NetWeaver genéricos 

* [Aplicativos SAP no Microsoft Azure usando o Azure NetApp Files](https://www.netapp.com/us/media/tr-4746.pdf)
* [Alta disponibilidade do SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server com o Azure NetApp Files para aplicativos SAP](../virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files.md)
* [Alta disponibilidade do SAP NetWeaver em VMs do Azure no Red Hat Enterprise Linux com o Azure NetApp Files para aplicativos SAP](../virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files.md)
* [Alta disponibilidade do SAP NetWeaver em VMs do Azure no Windows com o Azure NetApp Files (SMB) para aplicativos SAP](../virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb.md)
* [Alta disponibilidade do SAP NetWeaver em VMs do Azure no Red Hat Enterprise Linux para guia multi-SID de aplicativos SAP](../virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid.md)

### <a name="sap-hana"></a>SAP HANA 

* [Configurações de armazenamento de máquina virtual do SAP HANA no Azure](../virtual-machines/workloads/sap/hana-vm-operations-storage.md)
* [Volumes NFS v4.1 no Azure NetApp Files para SAP HANA](../virtual-machines/workloads/sap/hana-vm-operations-netapp.md)
* [Alta disponibilidade de expansão SAP HANA em VMs com o Azure NetApp Files no Red Hat Enterprise Linux](../virtual-machines/workloads/sap/sap-hana-high-availability-netapp-files-red-hat.md)
* [Expansão SAP HANA com o nó em espera em VMs do Azure usando o Azure NetApp Files no SUSE Linux Enterprise Server](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse.md)
* [Expansão SAP HANA com o nó em espera em VMs do Azure usando o Azure NetApp Files no Red Hat Enterprise Linux](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel.md)
* [Expansão SAP HANA com HSR e o Pacemaker no RHEL – máquinas virtuais do Azure](../virtual-machines/workloads/sap/sap-hana-high-availability-scale-out-hsr-rhel.md)
* [Ferramenta de Instantâneo Consistente do Aplicativo Azure (AzAcSnap)](azacsnap-introduction.md)

### <a name="sap-anydb"></a>AnyDB SAP

* [Implantação do DBMS de Máquinas de Virtuais do Oracle do Azure para carga de trabalho do SAP – Máquinas virtuais do Azure](../virtual-machines/workloads/sap/dbms_guide_oracle.md#oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-linux)
* [Implantar o AnyDB SAP (Oracle 19c) com o Azure NetApp Files](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-anydb-oracle-19c-with-azure-netapp-files/ba-p/2064043)

### <a name="sap-iq-nls"></a>SAP IQ-NLS

*   [Implantar a solução de HA SAP IQ-NLS HA usando o Azure NetApp Files no SUSE Linux Enterprise Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172#.X2tDfpNzBh4.linkedin)
* [Como gerenciar a licença do SAP IQ no cenário de HA](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/how-to-manage-sap-iq-license-in-ha-scenario/ba-p/2052583)

### <a name="sap-tech-community-and-blog-posts"></a>Postagens no blog e comunidade tecnológica do SAP 

* [Azure NetApp Files – Backup do SAP HANA em segundos](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Azure NetApp Files – Restaurar o banco de dados do HANA de um backup de instantâneo](https://blog.netapp.com/azure-netapp-files-backup-sap-hana)
* [Azure NetApp Files – Backup de descarregamento do SAP HANA com sincronização de nuvem](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [Acelere suas cópias do sistema SAP HANA usando o Azure NetApp Files](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [Volumes de nuvem ONTAP e Azure NetApp Files: migração do sistema SAP HANA facilitada](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/)
* [Decisões arquitetônicas para maximizar o investimento no ANF na arquitetura de expansão HANA N+M – Parte 1](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2078737)
* [Decisões arquitetônicas para maximizar o investimento no ANF na arquitetura de expansão HANA N+M – Parte 2](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2117130)
* [Decisões arquitetônicas para maximizar o investimento no ANF na arquitetura de expansão HANA N+M – Parte 3](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2215948)
* [Dimensionamento de cenário SAP e consolidação de volume com o Azure NetApp Files](https://techcommunity.microsoft.com/t5/sap-on-microsoft/sap-landscape-sizing-and-volume-consolidation-with-anf/m-p/2145572/highlight/true#M14)

## <a name="azure-vmware-solutions"></a>Solução VMware no Azure

* [Azure NetApp Files com a Solução VMware no Azure – Montagens do SO convidado](../azure-vmware/netapp-files-with-azure-vmware-solution.md)

## <a name="virtual-desktop-infrastructure-solutions"></a>Soluções de Infraestrutura de Área de Trabalho Virtual

Esta seção fornece referências para soluções de infraestrutura de Área de Trabalho Virtual.

### <a name="windows-virtual-desktop"></a>Área de Trabalho Virtual do Windows

* [Benefícios de usar o Azure NetApp Files com a Área de Trabalho Virtual do Windows](solutions-windows-virtual-desktop.md)
* [Opções de armazenamento para contêineres de perfil FSLogix na Área de Trabalho Virtual do Windows](../virtual-desktop/store-fslogix-profile.md#azure-platform-details)
* [Criar um contêiner de perfil FSLogix para um pool de host usando o Azure NetApp Files](../virtual-desktop/create-fslogix-profile-container.md)
* [Área de Trabalho Virtual do Windows em escala corporativa](/azure/architecture/example-scenario/wvd/windows-virtual-desktop)
* [Microsoft FSLogix para empresa – Melhores práticas do Azure NetApp Files](/azure/architecture/example-scenario/wvd/windows-virtual-desktop-fslogix#azure-netapp-files-best-practices)
* [Como configurar o Azure NetApp Files para anexação de aplicativo MSIX](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/setting-up-azure-netapp-files-for-msix-app-attach-step-by-step/m-p/1990021)

## <a name="hpc-solutions"></a>Soluções de HPC

Esta seção fornece referências para soluções de HPC (computação de alto desempenho). 

### <a name="generic-hpc"></a>HPC genérico

* [Azure NetApp Files: como obter o máximo do seu armazenamento em nuvem](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf)
* [Executar cargas de trabalho de MPI com o Lote do Azure e o Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
* [Azure CycleCloud: ambientes de HPC CycleCloud no Azure NetApp Files](/azure/cyclecloud/overview)

### <a name="oil-and-gas"></a>Óleo e gás

* [HPC (computação de alto desempenho): petróleo e gás no Azure](https://techcommunity.microsoft.com/t5/azure-global/high-performance-computing-hpc-oil-and-gas-in-azure/ba-p/824926)
* [Executar o software de simulação de reservatório no Azure](/azure/architecture/example-scenario/infrastructure/reservoir-simulation)

### <a name="electronic-design-automation-eda"></a>EDA (automação de design eletrônico)

* [Benefícios de usar o Azure NetApp Files para automação de design eletrônico](solutions-benefits-azure-netapp-files-electronic-design-automation.md)
* [Azure CycleCloud: laboratório de HPC EDA com o Azure NetApp Files](https://github.com/Azure/cyclecloud-hands-on-labs/blob/master/EDA/README.md)
* [Azure para o setor de semicondutores](https://azurecomcdn.azureedge.net/cvt-f40f39cd9de2d875ab0c198a8d7b186350cf0bca161e80d7896941389685d012/mediahandler/files/resourcefiles/azure-for-the-semiconductor-industry/Azure_for_the_Semiconductor_Industry.pdf)

### <a name="analytics"></a>Análise

* [Azure NetApp Files: um sistema de arquivos compartilhado a ser usado com a grade SAS no Microsoft Azure](https://communities.sas.com/t5/Administration-and-Deployment/Azure-NetApp-Files-A-shared-file-system-to-use-with-SAS-Grid-on/m-p/705192)
* [Azure NetApp Files: um sistema de arquivos compartilhado a ser usado com a grade SAS no MS Azure – ATUALIZAÇÃO RHEL 8.3/nconnect](https://communities.sas.com/t5/Administration-and-Deployment/Azure-NetApp-Files-A-shared-file-system-to-use-with-SAS-Grid-on/m-p/722261#M21648)
* [Melhores práticas para usar o Microsoft Azure com SAS®](https://communities.sas.com/t5/Administration-and-Deployment/Best-Practices-for-Using-Microsoft-Azure-with-SAS/m-p/676833#M19680)

## <a name="azure-platform-services-solutions"></a>Soluções de serviços da plataforma Azure

Esta seção fornece soluções para os serviços da plataforma Azure. 

### <a name="azure-kubernetes-services-and-kubernetes"></a>Serviços de Kubernetes do Azure e Kubernetes

* [Integrar o Azure NetApp Files ao Serviço de Kubernetes do Azure](../aks/azure-netapp-files.md)
* [Desempenho fora de série do Kubernetes no Azure com o Azure NetApp Files](https://cloud.netapp.com/blog/ma-anf-blg-configure-kubernetes-openshift)
* [Azure NetApp Files + Trident = armazenamento dinâmico e persistente para Kubernetes](https://anfcommunity.com/2021/02/16/azure-netapp-files-trident-dynamic-and-persistent-storage-for-kubernetes/)
* [Trident – Orquestrador de armazenamento para contêineres](https://netapp-trident.readthedocs.io/en/stable-v20.04/kubernetes/operations/tasks/backends/anf.html)
* [Plataforma de comércio eletrônico Magento no AKS (Serviço de Kubernetes do Azure)](/azure/architecture/example-scenario/magento/magento-azure)

### <a name="azure-batch"></a>Lote do Azure

* [Executar cargas de trabalho de MPI com o Lote do Azure e o Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
