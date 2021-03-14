---
title: Azure VMs HA para SAP NW no Windows com Azure NetApp Files (SMB) | Microsoft Docs
description: Alta disponibilidade para SAP NetWeaver em VMs do Azure no Windows com Azure NetApp Files (SMB) para aplicativos SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: radeltch
ms.openlocfilehash: a4c4631a0a1263e5a5398c44a8570f92571102e8
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045829"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-netapp-filessmb-for-sap-applications"></a>Alta disponibilidade para SAP NetWeaver em VMs do Azure no Windows com Azure NetApp Files (SMB) para aplicativos SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Este artigo descreve como implantar, configurar as máquinas virtuais, instalar a estrutura de cluster e instalar um sistema SAP NetWeaver 7,50 altamente disponível em VMs do Windows, usando [SMB](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) em [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md).  

A camada de banco de dados não é abordada em detalhes neste artigo. Supomos que a [rede virtual](../../../virtual-network/virtual-networks-overview.md) do Azure já foi criada.  

Primeiro, leia os seguintes documentos e Notas SAP:

* [Documentação do Azure NetApp Files][anf-azure-doc] 
* Nota SAP [1928533][1928533], que contém:  
  * Uma lista de tamanhos de VM do Azure com suporte para a implantação de software SAP
  * Informações importantes sobre capacidade para tamanhos de VM do Azure
  * Software SAP e combinações de SO (sistema operacional) e banco de dados com suporte
  * Versão de kernel do SAP necessária para Windows em Microsoft Azure
* A Nota SAP [2015553][2015553] lista pré-requisitos para implantações de software SAP com suporte do SAP no Azure.
* A Nota SAP [2178632][2178632] contém informações detalhadas sobre todas as métricas de monitoramentos relatadas para o SAP no Azure.
* A Nota SAP [1999351][1999351] tem informações de solução de problemas adicionais para a Extensão de Monitoramento Avançado do Azure para SAP.
* O SAP Note [2287140](https://launchpad.support.sap.com/#/notes/2287140) lista os pré-requisitos para o recurso de autoridade de certificação compatível com SAP do protocolo SMB 3. x.
* O SAP Note [2802770](https://launchpad.support.sap.com/#/notes/2802770) tem informações de solução de problemas para o AL11 de transação SAP lento em execução no Windows 2012 e 2016.
* A observação do SAP [1911507](https://launchpad.support.sap.com/#/notes/1911507) tem informações sobre o recurso de failover transparente para um compartilhamento de arquivos no Windows Server com o protocolo SMB 3,0.
* O SAP Note [662452](https://launchpad.support.sap.com/#/notes/662452) tem recomendação (desativando a geração de nome 8,3) para resolver erros/desempenho do sistema de arquivos insatisfatórios durante acessos a dados.
* [Instalar alta disponibilidade do SAP NetWeaver em um cluster de failover do Windows e compartilhamento de arquivos para instâncias ASCS/SCS do SAP no Azure](./sap-high-availability-installation-wsfc-file-share.md) 
* [Arquitetura e Cenários de Alta disponibilidade de Máquinas Virtuais do Azure para SAP NetWeaver](./sap-high-availability-architecture-scenarios.md)
* [Adicionar porta de investigação na configuração do cluster ASCS](sap-high-availability-installation-wsfc-file-share.md)
* [Instalação de uma instância do (A) SCS em um cluster de failover](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [Criar um volume SMB para o Azure NetApp Files](../../../azure-netapp-files/create-active-directory-connections.md#requirements-for-active-directory-connections)
* [Aplicativos SAP NetApp no Microsoft Azure usando o Azure NetApp Files][anf-sap-applications-azure]

> [!IMPORTANT]
> Cuidado: Lembre-se de que a instalação de um sistema SAP com SWPM no compartilhamento SMB, hospedado no volume SMB [Azure NetApp files][anf-azure-doc] , pode falhar com erro de instalação para permissões insuficientes como "warningPerm não está definido". Para evitar o erro, o usuário no qual SWPM de contexto é executado, precisa de privilégio elevado "administrador de domínio" durante a instalação do sistema SAP.  

## <a name="overview"></a>Visão geral

SAP desenvolveu uma nova abordagem e alternativa para o cluster de discos compartilhados para fazedr cluster de instância SAP ASCS/SCS no Windows Failover Cluster. Em vez de usar discos compartilhados de cluster, é possível usar um compartilhamento de arquivos SMB para implantar arquivos de host global do SAP. Azure NetApp Files dá suporte a SMBv3 (junto com NFS) com ACL de NTFS usando Active Directory. O Azure NetApp Files é automaticamente altamente disponível (pois é um serviço de PaaS). Esses recursos tornam Azure NetApp Files ótima opção para hospedar o compartilhamento de arquivos SMB para SAP global.  
Há suporte para os [serviços de domínio Azure Active Directory (AD)](../../../active-directory-domain-services/overview.md) e [Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) . Você pode usar os controladores de domínio Active Directory existentes com Azure NetApp Files. Os controladores de domínio podem estar no Azure como máquinas virtuais ou localmente por meio de ExpressRoute ou VPN S2S. Neste artigo, usaremos o controlador de domínio em uma VM do Azure.  
A HA (alta disponibilidade) para serviços centrais do SAP NetWeaver requer armazenamento compartilhado. Para conseguir isso no Windows, até agora, era necessário criar um cluster SOFS ou usar o s/w do disco compartilhado do cluster como SIOS. Agora é possível obter a HA do SAP NetWeaver usando o armazenamento compartilhado, implantado no Azure NetApp Files. O uso de Azure NetApp Files para o armazenamento compartilhado elimina a necessidade de SOFS ou SIOS.  

> [!NOTE]
> Clustering de instâncias SAP ASCS/SCS com compartilhamento de arquivos tem suporte para produtos SAP NetWeaver 7.40 (e superior) com SAP Kernel 7.49 (e superior).  

![Arquitetura de alta disponibilidade do SAP ASCS/SCS com compartilhamento SMB](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

Os pré-requisitos para um compartilhamento de arquivos SMB são:
* Protocolo SMB 3.0 (ou posterior).
* Capacidade de definir Active Directory ACLs (listas de controle de acesso) para Active Directory grupos de usuários e o objeto Computer $ Computer.
* O compartilhamento de arquivos deve ser habilitado para HA.

O compartilhamento para os serviços centrais do SAP nessa arquitetura de referência é oferecido por Azure NetApp Files:

![Arquitetura de alta disponibilidade do SAP ASCS/SCS com detalhes de compartilhamento SMB](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>Criar e montar o volume SMB para Azure NetApp Files

Execute as etapas a seguir, como preparação para usar Azure NetApp Files.  

1. Siga as etapas para [registrar-se para Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-register.md)  
2. Criar uma conta do Azure NetApp, seguindo as etapas descritas em  [criar uma conta do NetApp](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)  
3. Configurar o pool de capacidade, seguindo as instruções em [configurar um pool de capacidade](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
4. Azure NetApp Files recursos devem residir em uma sub-rede delegada. Siga as instruções em [delegar uma sub-rede para Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md) para criar uma sub-rede delegada.  

   > [!IMPORTANT]
   > Você precisa criar conexões do Active Directory antes de criar um volume SMB. Examine os [requisitos de conexões de Active Directory](../../../azure-netapp-files/create-active-directory-connections.md#requirements-for-active-directory-connections).  

5. Crie Active Directory conexão, conforme descrito em [criar uma conexão de Active Directory](../../../azure-netapp-files/create-active-directory-connections.md#create-an-active-directory-connection)  
6. Crie um volume SMB Azure NetApp Files SMB, seguindo as instruções em [Adicionar um volume SMB](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#add-an-smb-volume)  
7. Monte o volume SMB em sua máquina virtual do Windows.

> [!TIP]
> Você pode encontrar as instruções sobre como montar o volume Azure NetApp Files, se você navegar no [portal do Azure](https://portal.azure.com/#home) para o objeto Azure NetApp files, clicar na folha **volumes** e, em seguida, **montar instruções**.  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>Preparar a infraestrutura para a alta disponibilidade do SAP usando um cluster de failover do Windows 

1. [Defina as regras de balanceamento de carga do ASCS/SCS para o balanceador de carga interno do Azure](./sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716).
2. [Adicione máquinas virtuais do Windows ao domínio](./sap-high-availability-infrastructure-wsfc-shared-disk.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c).
3. [Adicionar entradas de registro em ambos os nós de cluster da instância do SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-shared-disk.md#661035b2-4d0f-4d31-86f8-dc0a50d78158)
4. [Configurar um cluster de failover do Windows Server para uma instância do SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab)
5. Se você estiver usando o Windows Server 2016, recomendamos que configure a [testemunha de nuvem do Azure](/windows-server/failover-clustering/deploy-cloud-witness).


## <a name="install-sap-ascs-instance-on-both-nodes"></a>Instalar a instância do SAP ASCS em ambos os nós

Você precisa do seguinte software da SAP:
   * Ferramenta de instalação do SWPM (Gerenciador de provisionamento de software) SAP versão SPS25 ou posterior.
   * Kernel SAP 7,49 ou posterior
   * Crie um nome de host virtual (nome de rede do cluster) para a instância clusterizada do SAP ASCS/SCS, conforme descrito em [criar um nome de host virtual para a instância clusterizada do SAP ASCS/SCS](./sap-high-availability-installation-wsfc-shared-disk.md#a97ad604-9094-44fe-a364-f89cb39bf097).

> [!NOTE]
> Clustering de instâncias SAP ASCS/SCS com compartilhamento de arquivos tem suporte para produtos SAP NetWeaver 7.40 (e superior) com SAP Kernel 7.49 (e superior).  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>Instalar uma instância do ASCS/SCS no primeiro nó do cluster ASCS/SCS

1. Instale uma instância do SAP ASCS/SCS no primeiro nó do cluster. Inicie a ferramenta de instalação do SAP SWPM e navegue até: **produto**  >  **DBMS** > instalação > servidor de aplicativos ABAP (ou Java) > High-Availability sistema > instância do ASCS/SCS > primeiro nó de cluster.  

2. Selecione o **cluster de compartilhamento de arquivos** como a configuração de compartilhamento de cluster em SWPM.  
3. Quando solicitado na etapa **parâmetros do cluster do sistema SAP**, insira o nome do host para o compartilhamento SMB Azure NetApp files que você já criou como **nome de host do compartilhamento de arquivos**.  Neste exemplo, o nome do host de compartilhamento SMB é **anfsmb-9562**. 

   > [!IMPORTANT]
   > Se o verificador de pré-requisitos resultar em SWPM mostrar a condição de recurso de disponibilidade contínua não atendida, ela poderá ser resolvida seguindo as instruções em [mensagem de erro atrasada quando você tentar acessar uma pasta compartilhada que não existe mais no Windows](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l).  

   > [!TIP]
   > Se o verificador de pré-requisitos resultar em SWPM mostrar a condição de tamanho de permuta não atendida, você poderá ajustar o tamanho de permuta navegando até Meu Computador>Propriedades do sistema>configurações de desempenho> avançado> memória virtual> alteração.  

4. Configure um recurso de cluster do SAP, a `SAP-SID-IP` porta de investigação, usando o PowerShell. Execute essa configuração em um dos nós de cluster SAP ASCS/SCS, conforme descrito em [Configurar a porta de investigação](./sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761).

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>Instalar uma instância do ASCS/SCS no segundo nó do cluster ASCS/SCS

1. Instale uma instância do SAP ASCS/SCS no segundo nó do cluster. Inicie a ferramenta de instalação do SAP SWPM e, em seguida, navegue até **produto**  >  **DBMS** > instalação > servidor de aplicativos ABAP (ou Java) > High-Availability instância do sistema > ASCS/SCS > nó de cluster adicional.  

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>Instalar instância DBMS e servidores de aplicativos SAP

Conclua a instalação do SAP instalando:

   * Uma instância do DBMS  
   * Um servidor de aplicativos SAP primário  
   * Um servidor de aplicativos SAP adicional  

## <a name="test-the-sap-ascsscs-instance-failover"></a>Testar o failover da instância do SAP ASCS/SCS 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>Fazer failover do nó A do cluster para o nó B do cluster e voltar
Neste cenário de teste, iremos nos referir ao nó de cluster sapascs1 como nó A e ao nó de cluster sapascs2 como nó B.

1. Verifique se os recursos de cluster estão em execução no nó A. ![ Figura 1: recursos de cluster de failover do Windows Server em execução no nó A antes do teste de failover](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. Reinicie o nó A do cluster. Os recursos de cluster do SAP serão movidos para o nó B do cluster. ![ Figura 2: recursos de cluster de failover do Windows Server em execução no nó B após o teste de failover](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>Teste de entrada de bloqueio

1. Verifique se o servidor de replicação de enfileiramento do SAP (ERS) está ativo  
2. Faça logon no sistema SAP, execute a transação SU01 e abra uma ID de usuário no modo de alteração. Isso irá gerar a entrada de bloqueio do SAP.  
3. À medida que você estiver conectado ao sistema SAP, exiba a entrada de bloqueio, navegando até a transação ST12.  
4. Faça failover de recursos de ASCS do nó A do cluster para o nó B do cluster.  
5. Verifique se a entrada de bloqueio, gerada antes do failover dos recursos de cluster do SAP ASCS/SCS, é mantida.  

![Figura 3: a entrada de bloqueio é retida após o teste de failover](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

Para obter mais informações, consulte [solução de problemas de failover de enfileiramento em ASCS com ers](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS)
## <a name="next-steps"></a>Próximas etapas

* [Planejamento e implementação de Máquinas Virtuais do Azure para o SAP][planning-guide]
* [Implantação de Máquinas Virtuais do Azure para SAP][deployment-guide]
* [Implantação do DBMS de Máquinas Virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer alta disponibilidade e planejar a recuperação de desastre do SAP 
* HANA no Azure (instâncias grandes), consulte [SAP Hana (instâncias grandes) alta disponibilidade e recuperação de desastre no Azure](hana-overview-high-availability-disaster-recovery.md).
* Para saber como estabelecer a alta disponibilidade e o plano de recuperação de desastre do SAP HANA em VMs do Azure, confira [Alta disponibilidade do SAP HANA em VMs (Máquinas Virtuais) do Azure][sap-hana-ha]
