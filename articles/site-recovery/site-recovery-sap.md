---
title: Configurar a recuperação de desastre do SAP NetWeaver com o Azure Site Recovery
description: Saiba como configurar a recuperação de desastres para SAP NetWeaver com Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: e639762cd1adb7bbbc3fb2ec31f4ce52710e46f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711936"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Configurar a recuperação de desastre para uma implantação de aplicativo do SAP NetWeaver de várias camadas

A maioria das implantações do SAP de grande e médio porte usam alguma forma de solução de recuperação de desastre. A importância de se ter soluções de recuperação de desastre sólidas foi aumentando à medida que os processos de negócios mais vitais foram movidos para aplicativos como o SAP. O Azure Site Recovery foi testado e integrado a aplicativos SAP. O Site Recovery excede os recursos da maioria das soluções de recuperação de desastre locais e a um custo total de propriedade mais baixo do que as soluções concorrentes.

Com o Site Recovery, você pode:
* Habilite a proteção de aplicativos SAP NetWeaver e não NetWeaver de produção em execução no local por meio da replicação de componentes no Azure.
* Habilite a proteção de aplicativos SAP NetWeaver e não NetWeaver de produção em execução no Azure por meio da replicação de componentes em outro datacenter do Azure.
* Simplifique a migração de nuvem usando o Site Recovery para migrar sua implantação do SAP para o Azure.
* Simplifique as atualizações, os testes e a criação de protótipos de projetos da SAP criando um clone de produção sob demanda para testar aplicativos SAP.

Você pode proteger implantações de aplicativo do SAP NetWeaver usando [Azure site Recovery](site-recovery-overview.md). Este artigo aborda as práticas recomendadas para proteger uma implantação do SAP NetWeaver de três camadas no Azure quando você replica para outro Datacenter do Azure usando Site Recovery. O artigo descreve os cenários e as configurações com suporte e como fazer failovers de teste (análises de recuperação de desastre) e failovers reais.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que você sabe fazer as tarefas a seguir:

* [Replicar uma máquina virtual no Azure](./azure-to-azure-tutorial-enable-replication.md)
* [Criar uma rede de recuperação](./azure-to-azure-about-networking.md)
* [Executar um failover de teste para o Azure](./azure-to-azure-tutorial-dr-drill.md)
* [Executar um failover para o Azure](site-recovery-failover.md)
* [Replicar um controlador de domínio](site-recovery-active-directory.md)
* [Replicar uma instância de SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Cenários com suporte

Você pode usar o Azure Site Recovery para implementar uma solução de recuperação de desastre nos seguintes cenários:
* Você tem sistemas SAP em execução em um datacenter do Azure e os está replicando para outro Datacenter do Azure (recuperação de desastre do Azure para o Azure). 
   Para saber mais, confira [Arquitetura de replicação do Azure para o Azure](./azure-to-azure-architecture.md).
* Você tem sistemas SAP em execução em servidores VMware (ou físicos) locais. Você também está replicando os sistemas SAP para um site de recuperação de desastre em um datacenter do Azure (recuperação de desastre do VMware para o Azure). 
   Este cenário requer alguns componentes adicionais. Para saber mais, confira [Arquitetura de replicação do VMware para o Azure](./vmware-azure-architecture.md).
* Você tem sistemas SAP em execução no Hyper-V local. Você também está replicando os sistemas SAP para um site de recuperação de desastre em um datacenter do Azure (recuperação de desastre do Hyper-V para o Azure).
   Este cenário requer alguns componentes adicionais. Para saber mais, confira [Arquitetura de replicação do Hyper-V para o Azure](./hyper-v-azure-architecture.md).

Neste artigo, usamos um cenário de recuperação de desastre **do Azure para o Azure** . O cenário mostra os recursos de recuperação de desastre do SAP da Site Recovery. Como a replicação do Site Recovery não é específica do aplicativo, o processo descrito deve também se aplicar a outros cenários.

### <a name="required-foundation-services"></a>Serviços básicos necessários
No cenário discutido neste artigo, os seguintes serviços de base são implantados:
* Gateway de VPN do Azure ou Azure ExpressRoute
* Pelo menos um Azure Active Directory controlador de domínio e servidor DNS, em execução no Azure

Recomendamos que você estabeleça essa infraestrutura antes de implantar o Site Recovery.

## <a name="reference-sap-application-deployment"></a>Implantação de referência do aplicativo SAP

Essa arquitetura de referência está executando o SAP NetWeaver em um ambiente do Windows no Azure com alta disponibilidade. Essa arquitetura é implantada com tamanhos específicos de VM (máquina virtual) que você pode alterar para atender às necessidades da sua organização.

![Diagrama de um padrão de implantação típico do SAP](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Considerações de recuperação de desastres

Para a recuperação de desastres, você deve ser capaz de fazer failover para uma região secundária. Cada camada usa uma estratégia diferente para fornecer proteção de recuperação de desastre.

#### <a name="vms-running-sap-web-dispatcher-pools"></a>VMs executando pools de Dispatcher da Web SAP

O componente Web Dispatcher funciona como um balanceador de carga para o tráfego SAP entre os servidores de aplicativos SAP. Para obter alta disponibilidade para o componente Web Dispatcher, Azure Load Balancer implementa a configuração do Dispatcher da Web paralela. O Web Dispatcher usa uma configuração Round Robin para a distribuição de tráfego HTTP (S) entre os expatchers da Web disponíveis no pool de balanceadores.

#### <a name="vms-running-application-servers-pools"></a>VMs executando pools de servidores de aplicativos
A transação SMLG gerencia grupos de logon para servidores de aplicativos ABAP. Ele usa a função de balanceamento de carga dentro do servidor de mensagens dos serviços centrais para distribuir a carga de trabalho entre pools do servidor de aplicativos SAP para o tráfego SAPGUIs e RFC. Você pode replicar esse gerenciamento usando Site Recovery.

#### <a name="vms-running-sap-central-services-clusters"></a>VMs executando clusters de serviços centrais SAP
Essa arquitetura de referência executa o Central Services em VMs na camada de aplicativo. Os serviços centrais são um possível ponto único de falha quando em uma única VM. A implantação típica e a alta disponibilidade não são requisitos.

Para implementar uma solução de alta disponibilidade, você pode usar um cluster de disco compartilhado ou um cluster de compartilhamento de arquivos. Para configurar VMs para um cluster de disco compartilhado, use Cluster de Failover do Windows Server. Recomendamos que você use a testemunha de nuvem como uma testemunha de quorum.

 > [!NOTE]
 > Como Site Recovery não Replica a testemunha de nuvem, recomendamos que você implante a testemunha de nuvem na região de recuperação de desastre.

Para dar suporte ao ambiente de cluster de failover, o [sios Datakeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) faz a função de volume compartilhado clusterizado. Na função, o grupo de SIOS datakeeper replica os discos independentes pertencentes aos nós do cluster. Como o Azure não dá suporte nativo a discos compartilhados, ele requer soluções fornecidas por SIOS.

Você também pode manipular o clustering implementando um cluster de compartilhamento de arquivos. Recentemente, a [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) modificou o padrão de implantação do Central Services para acessar os diretórios globais /sapmnt por meio de um caminho UNC. Ainda recomendamos que você verifique se o compartilhamento UNC/sapmnt está altamente disponível. Você pode verificar sua instância dos serviços centrais. Use o cluster de failover do Windows Server com o SOFS (servidor de arquivos Scale Out) e o recurso Espaços de Armazenamento Diretos (S2D) no Windows Server 2016.

 > [!NOTE]
 > Atualmente, Site Recovery dá suporte apenas à replicação de ponto consistente com falhas de máquinas virtuais que usam espaços de armazenamento diretos e o nó passivo do SIOS datakeeper.


## <a name="more-disaster-recovery-considerations"></a>Mais considerações sobre recuperação de desastre

Você pode usar Site Recovery para orquestrar o failover da implantação completa do SAP em regiões do Azure.
A seguir estão as etapas para configurar a recuperação de desastre:

1. Replicar máquinas virtuais
1. Criar uma rede de recuperação
1. Replicar um controlador de domínio
1. Replicar a camada de banco de dados
1. Execute um teste de failover
1. Execute um failover

A seguir, a recomendação para a recuperação de desastre de cada camada usada neste exemplo.

 **Camadas do SAP** | **Recomendação**
 --- | ---
**Pool do SAP Web Dispatcher** |  Replicar usando Site Recovery 
**Pool de servidores de aplicativo do SAP** |  Replicar usando Site Recovery 
**Cluster do SAP Central Services** |  Replicar usando Site Recovery 
**Máquinas virtuais do Active Directory** |  Usar a replicação do Active Directory 
**Servidores do Banco de Dados SQL** |  Usar replicação de Always On SQL Server

## <a name="replicate-virtual-machines"></a>Replicar máquinas virtuais

Para iniciar a replicação de todas as máquinas virtuais do aplicativo SAP para o datacenter de recuperação de desastre do Azure, siga as orientações em [Replicar uma máquina virtual no Azure](./azure-to-azure-tutorial-enable-replication.md).

* Para obter orientação sobre como proteger Active Directory e DNS, saiba [como proteger Active Directory e DNS](site-recovery-active-directory.md).

* Para obter orientação sobre como proteger a camada de banco de dados em execução no SQL Server, saiba [como proteger SQL Server](site-recovery-sql.md).

## <a name="networking-configuration"></a>Configuração de rede

Se você usar um endereço IP estático, poderá especificar o endereço IP que você deseja que a máquina virtual use. Para definir o endereço IP, vá para **configurações de computação e rede**  >  **placa de interface de rede**.

![Captura de tela que mostra como definir um endereço IP privado no painel de adaptador de rede do Site Recovery](./media/site-recovery-sap/sap-static-ip.png)


## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação

Um plano de recuperação dá suporte à sequência de várias camadas em um aplicativo de várias camadas durante um failover. O sequenciamento ajuda a manter a consistência do aplicativo. Quando você criar um plano de recuperação para um aplicativo Web de várias camadas, conclua as etapas descritas em [Criar um plano de recuperação usando o Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Adicionar máquinas virtuais aos grupos de failover

1. Crie um plano de recuperação adicionando as VMs servidor de aplicativos, Web Dispatcher e serviços SAP central.
1. Selecione **Personalizar** para agrupar as VMs. Por padrão, todas as VMs fazem parte do grupo 1.

### <a name="add-scripts-to-the-recovery-plan"></a>Adicionar scripts ao plano de recuperação
Para que seus aplicativos funcionem corretamente, talvez seja necessário realizar algumas operações nas máquinas virtuais do Azure. Execute essas operações após o failover ou durante um failover de teste. Você também pode automatizar algumas operações pós-failover. Por exemplo, atualize a entrada DNS e altere associações e conexões adicionando scripts correspondentes ao plano de recuperação.

Você pode implantar os scripts de Site Recovery mais usados em sua conta de automação do Azure selecionando **implantar no Azure**. Ao usar qualquer script publicado, siga as orientações no script.

[![Implantar no Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Adicione um script de ação prévia ao grupo 1 para fazer failover do grupo de disponibilidade SQL Server. Use o script ASR-SQL-FailoverAG publicado nos scripts de exemplo. Siga as orientações no script e faça as alterações necessárias no script adequadamente.
1. Adicione um script de ação post-action para anexar um balanceador de carga nas máquinas virtuais com failover da camada da Web (grupo 1). Use o script ASR-AddSingleLoadBalancer publicado nos scripts de exemplo. Siga as orientações no script e faça as alterações necessárias no script, conforme necessário.

![Plano de recuperação do SAP](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Execute um teste de failover

1. No Portal do Azure, selecione seu cofre de Serviços de Recuperação.
1. Selecione o plano de recuperação que você criou para aplicativos SAP.
1. Selecione **failover de teste**.
1. Para iniciar o processo de failover de teste, selecione o ponto de recuperação e a rede virtual do Azure.
1. Quando o ambiente secundário está ativo, faça validações.
1. Quando as validações forem concluídas, limpe o ambiente de failover selecionando **limpar failover de teste**.

Para obter mais informações, consulte [Failover de teste para Azure no Site Recovery ](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Executar um failover

1. No Portal do Azure, selecione seu cofre de Serviços de Recuperação.
1. Selecione o plano de recuperação que você criou para aplicativos SAP.
1. Selecione **Failover**.
1. Para iniciar o processo de failover, selecione o ponto de recuperação.

Para obter mais informações, consulte [Failover no Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre como criar uma solução de recuperação de desastre para implantações do SAP NetWeaver usando Site Recovery. Consulte o download white paper [SAP NetWeaver: criando uma solução de recuperação de desastres com site Recovery](/samples/browse/?redirectedfrom=TechNet-Gallery). O white paper discute recomendações para várias arquiteturas SAP. Você pode ver os aplicativos com suporte e os tipos de VM para SAP no Azure. Também há opções de plano para testar sua solução de recuperação de desastre.
* Saiba mais sobre [como replicar outras cargas de trabalho](site-recovery-workload.md) usando o Site Recovery.