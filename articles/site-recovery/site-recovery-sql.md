---
title: Replicar aplicativos com o SQL Server e o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como replicar o SQL Server usando o Azure Site Recovery para as funcionalidades de desastre do SQL Server.
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: pratshar
ms.openlocfilehash: e53f60979e01a0eabe118d3ae6457a61bd4b0ded
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="protect-sql-server-using-sql-server-disaster-recovery-and-azure-site-recovery"></a>Proteger o SQL Server usando a recuperação de desastre do SQL Server e o Azure Site Recovery

Este artigo descreve como proteger o back-end do SQL Server de um aplicativo usando uma combinação de tecnologias BCDR (continuidade dos negócios e recuperação de desastre) do SQL Server e o [Azure Site Recovery](site-recovery-overview.md).

Antes de começar, verifique se você entendeu as funcionalidades de recuperação de desastre do SQL Server, incluindo o clustering de failover, grupos de disponibilidade do AlwaysOn, espelhamento de banco de dados e envio de log.


## <a name="sql-server-deployments"></a>Implantações do SQL Server

Muitas cargas de trabalho usam o SQL Server como base, e ele pode ser integrado com aplicativos como o SharePoint, Dynamics e SAP, para implementar os serviços de dados.  O SQL Server pode ser implantado de várias maneiras:

* **SQL Server autônomo**: o SQL Server e todos os bancos de dados são hospedados em um único computador (físico ou virtual). Quando virtualizado, o cluster de host é usado para alta disponibilidade local. A alta disponibilidade no nível de convidado não é implementada.
* **Instâncias de cluster de Failover (FCI do Always On) do SQL Server**: dois ou mais nós executando o SQL Server com instâncias de discos compartilhados são configurados em um cluster de Failover do Windows. Se um nó estiver inativo, o cluster poderá realizar o failover do SQL Server em outra instância. Essa configuração é usada normalmente para implementar a alta disponibilidade em um site primário. Essa implantação não protege contra falhas ou interrupção na camada de armazenamento compartilhada. Um disco compartilhado pode ser implementado usando ISCSI, Fiber Channel ou vhdx compartilhado.
* **Grupos de Disponibilidade AlwaysOn do SQL**: dois ou mais nós podem ser configurados em um cluster sem compartilhamento, com bancos de dados SQL Server configurados em um grupo de disponibilidade, com replicação síncrona e failover automático.

 Este artigo utiliza as seguintes tecnologias de recuperação de desastre nativas do SQL para recuperar bancos de dados em um local remoto:

* Grupos de Disponibilidade AlwaysOn do SQL, a fim de fornecer recuperação de desastres para SQL Server 2012 ou 2014 edições Enterprise.
* Espelhamento de banco de dados SQL no modo de alta segurança para SQL Server Standard Edition (qualquer versão), ou para o SQL Server 2008 R2.

## <a name="site-recovery-support"></a>Suporte do Site Recovery

### <a name="supported-scenarios"></a>Cenários com suporte
O Site Recovery pode proteger o SQL Server, como resumido na tabela.

**Cenário** | **Para um site secundário** | **Para o Azure**
--- | --- | ---
**Hyper-V** | Sim | Sim
**VMware** | Sim | Sim
**Servidor físico** | Sim | Sim

### <a name="supported-sql-server-versions"></a>Versões do SQL Server com suporte
Essas versões do SQL Server tem suporte nos cenários com suporte:

* SQL Server 2016 Enterprise e Standard
* SQL Server 2014 Enterprise e Standard
* SQL Server 2012 Enterprise e Standard
* SQL Server 2008 R2 Enterprise e Standard

### <a name="supported-sql-server-integration"></a>Integração do SQL Server com suporte

O Site Recovery pode ser integrado com tecnologias nativas de BCDR do SQL Server, resumidas na tabela a fim de fornecer uma solução de recuperação de desastres.

**Recurso** | **Detalhes** | **SQL Server** |
--- | --- | ---
**Grupo de disponibilidade Sempre Ativo** | Várias instâncias autônomas do SQL Server cada uma executando em um cluster de failover com vários nós.<br/><br/>Os bancos de dados podem ser agrupados em grupos de failover, que podem ser copiados (espelhados) em instâncias do SQL Server, de modo que não exista a necessidade de armazenamento compartilhado.<br/><br/>Fornece a recuperação de desastres entre um site primário e um ou mais sites secundários. Dois nós podem ser configurados em um cluster sem compartilhamento, com bancos de dados do SQL Server configurados em um grupo de disponibilidade, com replicação síncrona e failover automático. | SQL Server 2014 & 2012 Enterprise Edition
**Clustering de failover (FCI AlwaysOn)** | O SQL Server aproveita o Clustering de Failover do Windows para proporcionar a alta disponibilidade de cargas de trabalho local do SQL Server.<br/><br/>Os nós que executam instâncias do SQL Server com discos compartilhados são configurados em um cluster de failover. Se uma instância estiver inoperante, o cluster realiza o failover para outro.<br/><br/>O cluster não protege contra falhas ou interrupções no armazenamento compartilhado. O disco compartilhado pode ser implementado com iSCSI, fiber channel ou VHDXs compartilhados. | Edições do SQL Server Enterprise<br/><br/>Edição SQL Server Standard (limitada somente a dois nós)
**Espelhamento de banco de dados (modo de alta segurança)** | Protege um único banco de dados para uma única cópia secundária. Disponível nos modos de replicação de alta segurança (síncrona) e de alto desempenho (assíncrono). Não requer um cluster de failover. | SQL Server 2008 R2<br/><br/>Todas as edições do SQL Server Enterprise
**SQL Server autônomo** | O SQL Server e o banco de dados estão hospedados em um único servidor (físico ou virtual). O clustering de host é usado para alta disponibilidade, se o servidor for virtual. Sem alta disponibilidade no nível do convidado. | Edição Enterprise ou Standard

## <a name="deployment-recommendations"></a>Recomendações de implantação

Esta tabela resume nossas recomendações para a integração de tecnologias de BCDR do SQL Server com a Recuperação de Site.

| **Versão** | **Edição** | **Implantação** | **Local para local** | **Local para o Azure** |
| --- | --- | --- | --- | --- |
| SQL Server 2014 ou 2012 |Enterprise |Instância do cluster de failover |Grupos de disponibilidade AlwaysOn |Grupos de disponibilidade AlwaysOn |
|| Enterprise |Grupos de disponibilidade AlwaysOn para alta disponibilidade |Grupos de disponibilidade AlwaysOn |Grupos de disponibilidade AlwaysOn | |
|| Standard |FCI (instância do cluster de failover) |Replicação de Recuperação de Site com espelhamento local |Replicação de Recuperação de Site com espelhamento local | |
|| Enterprise ou Standard |Autônomo |Replicação de recuperação de site |Replicação de recuperação de site | |
| SQL Server 2008 R2 ou 2008 |Enterprise ou Standard |FCI (instância do cluster de failover) |Replicação de Recuperação de Site com espelhamento local |Replicação de Recuperação de Site com espelhamento local |
|| Enterprise ou Standard |Autônomo |Replicação de recuperação de site |Replicação de recuperação de site | |
| SQL Server (qualquer versão) |Enterprise ou Standard |Instância do cluster de failover – aplicativo DTC |Replicação de recuperação de site |Sem suporte |

## <a name="deployment-prerequisites"></a>Pré-requisitos de implantação

* Uma implantação local do SQL Server executando uma versão com suporte do SQL Server. Normalmente, também é necessário ter um Active Directory para o SQL Server.
* Os requisitos para o cenário que você deseja implantar. Saiba mais sobre os requisitos de suporte para [replicação no Azure](site-recovery-support-matrix-to-azure.md) e [no local](site-recovery-support-matrix.md) e [os pré-requisitos de implantação](site-recovery-prereq.md).
* Para configurar a recuperação no Azure, será necessário executar a ferramenta [Avaliação de preparação da máquina virtual do Azure](http://www.microsoft.com/download/details.aspx?id=40898) nas máquinas virtuais do SQL Server a fim de verificar se são compatíveis com o Azure e com o Site Recovery.

## <a name="set-up-active-directory"></a>Configurar o Active Directory

Configure o Active Directory no site de recuperação secundário para que o SQL Server seja executado corretamente.

* **Pequena empresa**— Com uma quantidade pequena de aplicativos e um único controlador de domínio para o site local, se você quiser realizar o failover de todo o site, recomendamos o uso da replicação do Site Recovery para replicar o controlador de domínio para o armazenamento de dados secundário ou para o Azure.
* **Empresa de médio e grande porte**— Se você tiver uma grande quantidade de aplicativos, uma floresta do Active Directory e quiser realizar o failover por aplicativo ou carga de trabalho, recomendamos a configuração de um controlador de domínio adicional no armazenamento de dados secundário ou no Azure. Se você estiver usando os grupos de disponibilidade AlwaysOn para recuperar em um site remoto, recomendamos a configuração de outro controlador de domínio adicional no site secundário ou no Azure, a fim de usá-lo para a instância do SQL Server recuperada.

As instruções neste artigo supõem que exista um controlador de domínio disponível no local secundário. [Ler mais](site-recovery-active-directory.md) sobre como proteger o Active Directory com o Site Recovery.


## <a name="integrate-with-sql-server-always-on-for-replication-to-azure"></a>Integrar com o SQL Server Always On para replicação no Azure

Você precisa saber do seguinte:

1. Importar scripts para sua conta da Automação do Azure. Contém os scripts de failover do grupo de disponibilidade do SQL em uma [máquina virtual do Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) e uma [máquina virtual clássica](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1).

    [![Implantar no Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. Adicione ASR-SQL-FailoverAG como uma ação prévia do primeiro grupo do plano de recuperação.

1. Siga as instruções disponíveis no script para criar uma variável de automação para fornecer o nome dos grupos de disponibilidade.

### <a name="steps-to-do-a-test-failover"></a>Etapas para fazer um failover de teste

O SQL AlwaysOn não dá suporte nativo ao failover de teste. Portanto, recomendamos o seguinte:

1. Configure o [Backup do Azure](../backup/backup-azure-vms.md) na máquina virtual que hospeda a réplica do grupo de disponibilidade no Azure.

1. Antes de disparar o failover de teste do plano de recuperação, recupere a máquina virtual no backup feito na etapa anterior.

    ![Restaurar do Backup do Azure ](./media/site-recovery-sql/restore-from-backup.png)

1. [Imponha um quorum](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) na máquina virtual restaurada do backup.

1. Atualize o IP do ouvinte para um IP disponível na rede de failover de teste.

    ![Atualizar o Ouvinte de IP](./media/site-recovery-sql/update-listener-ip.png)

1. Coloque o ouvinte online.

    ![Colocar o Ouvinte Online](./media/site-recovery-sql/bring-listener-online.png)

1. Crie um balanceador de carga com um IP criado no pool IP de front-end correspondente a cada ouvinte de grupo de disponibilidade e com a máquina virtual do SQL adicionada no pool de back-end.

     ![Criar balanceador de carga – pool IP de front-end ](./media/site-recovery-sql/create-load-balancer1.png)

    ![Criar balanceador de carga – pool de back-end ](./media/site-recovery-sql/create-load-balancer2.png)

1. Execute o failover de teste do plano de recuperação.

### <a name="steps-to-do-a-failover"></a>Etapas para fazer um failover

Depois de adicionar o script no plano de recuperação e validar o plano de recuperação seguindo um failover de teste, você pode fazer o failover do plano de recuperação.


## <a name="integrate-with-sql-server-always-on-for-replication-to-a-secondary-on-premises-site"></a>Integração com o SQL Server Always On para replicação em um site local secundário

Se o SQL Server estiver usando grupos de disponibilidade para alta disponibilidade (ou uma FCI), recomendamos também o uso dos grupos de disponibilidade no site de recuperação. Observe que isso se aplica a aplicativos que não usam transações distribuídas.

1. [Configure bancos de dados](https://msdn.microsoft.com/library/hh213078.aspx) em grupos de disponibilidade.
1. Crie uma rede virtual no site secundário.
1. Configure uma conexão de VPN site a site entre a rede virtual e o site primário.
1. Crie uma máquina virtual no site de recuperação e instale nela o SQL Server.
1. Estenda os grupos de disponibilidade Always On existentes para a nova VM do SQL Server. Configure essa instância do SQL Server como uma cópia de réplica assíncrona.
1. Crie um ouvinte do grupo de disponibilidade ou atualize o ouvinte existente para incluir a máquina virtual de réplica assíncrona.
1. Certifique-se de que o farm de aplicativos esteja configurado usando o ouvinte. Se a instalação tiver usado o nome de servidor do banco de dados, atualize-o para usar o ouvinte de modo que você não precise reconfigurá-lo após o failover.

Para aplicativos que usam transações distribuídas, recomendamos a implantação do Site Recovery com a [replicação site a site do VMware/servidor físico](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Considerações sobre o Plano de Recuperação
1. Adicione este exemplo de script à biblioteca do VMM nos sites primário e secundário.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

1. Ao criar um plano de recuperação para o aplicativo, adicione uma ação prévia à etapa com script Group-1, que invoca o script para fazer o failover de grupos de disponibilidade.

## <a name="protect-a-standalone-sql-server"></a>Proteger um SQL Server autônomo

Nesse cenário, recomendamos o uso da replicação de Site Recovery para proteger a máquina do SQL Server. As etapas exatas dependerão se o SQL Server é uma VM ou um servidor físico, e se você deseja replicar para o Azure ou para um site secundário local. Saiba mais sobre [cenários do Site Recovery](site-recovery-overview.md).

## <a name="protect-a-sql-server-cluster-standard-editionwindows-server-2008-r2"></a>Proteger um cluster do SQL Server (edição standard/Windows Server 2008 R2)

Para um cluster executando o SQL Server Standard ou o SQL Server 2008 R2, recomendamos o uso da replicação do Site Recovery para proteger o SQL Server.

### <a name="on-premises-to-on-premises"></a>Local para o próprio local

* Caso o aplicativo use transações distribuídas, recomendamos implantar o [Site Recovery com replicação SAN](site-recovery-vmm-san.md) para um ambiente Hyper-V ou [VMware/servidor físico para VMware](site-recovery-vmware-to-vmware.md) para um ambiente VMware.
* Para aplicativos não DTC, use a abordagem anterior para recuperar o cluster como um servidor autônomo, aproveitando um espelho de banco de dados local de segurança alta.

### <a name="on-premises-to-azure"></a>Local para o Azure

O Site Recovery não dá suporte a clusters convidados ao replicar para o Azure. O SQL Server também não fornece uma solução de recuperação de desastres de baixo custo para a edição Standard. Nesse cenário, recomendamos a proteção do cluster local do SQL Server para um SQL Server autônomo e sua recuperação no Azure.

1. Configure uma instância do SQL Server Autônomo adicional no site local.
1. Configure a instância para servir como um espelho para os bancos de dados que você quer proteger. Configure o espelhamento no modo de alta segurança.
1. Configure o Site Recovery no site local para [Hyper-V](site-recovery-hyper-v-site-to-azure.md) ou [VMs VMware/servidores físicos](site-recovery-vmware-to-azure-classic.md).
1. Use a replicação da Recuperação de Site para replicar a nova instância do SQL Server para o Azure. Como é uma cópia espelhada de alta segurança, ela será sincronizada com o cluster primário, mas será replicada para o Microsoft Azure usando a replicação do Site Recovery.


![Cluster padrão](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Considerações sobre failback

Para clusters do SQL Server Standard, a realização do failback após um failover não planejado exige um backup e restauração do SQL Server, a partir da instância de espelho para o cluster original com o restabelecimento do espelhamento.

## <a name="next-steps"></a>Próximas etapas
[Saiba mais](site-recovery-components.md) sobre a arquitetura do Site Recovery.
