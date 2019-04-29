---
title: Configurar a recuperação de desastres para uma implantação do Citrix XenDesktop e XenApp de várias camadas usando o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como configurar a recuperação de desastres para implantações do Citrix XenDesktop e do XenApp usando o Azure Site Recovery.
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ponatara
ms.openlocfilehash: 68f12bb7335da0a996aeadd752f59db0aa360a8e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61038161"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-citrix-xenapp-and-xendesktop-deployment"></a>configurar a recuperação de desastres para uma implantação do Citrix XenApp e XenDesktop multicamada



O Citrix XenDesktop é uma solução de virtualização de área de trabalho que entrega aplicativos e áreas de trabalho como um serviço sob demanda a qualquer usuário, em qualquer lugar. Com tecnologia de entrega FlexCast, o XenDesktop pode fornecer aplicativos e áreas de trabalho aos usuários de forma rápida e com segurança.
Hoje, o Citrix XenApp não fornece qualquer capacidade de recuperação de desastre.

Uma boa solução de recuperação de desastres deve permitir a modelagem de planos de recuperação em torno das arquiteturas de aplicativo complexas indicadas acima, e também tem a capacidade de adicionar etapas personalizadas para lidar com mapeamentos de aplicativo entre as várias camadas, fornecendo uma solução certeira acionada com um único clique no caso de um desastre resultar em um RTO inferior.

Este documento fornece orientações passo a passo para criar uma solução de recuperação de desastre para suas implantações do Citrix XenApp no local nas plataformas Hyper-V e VMware vSphere. Este documento também descreve como executar um failover de teste (análise de recuperação de desastre) e o failover não planejado para o Azure usando planos de recuperação, as configurações com suporte e os pré-requisitos.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você precisa entender o seguinte:

1. [Replicar uma máquina virtual no Azure](site-recovery-vmware-to-azure.md)
1. Como [criar uma rede de recuperação](site-recovery-network-design.md)
1. [Executar um failover de teste no Azure](site-recovery-test-failover-to-azure.md)
1. [Executar um failover no Azure](site-recovery-failover.md)
1. Como [replicar um controlador de domínio](site-recovery-active-directory.md)
1. Como [replicar o SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Padrões de implantação

Um farm Citrix XenApp e XenDesktop geralmente possui o seguinte padrão de implantação:

**Padrão de implantação**

A implantação do Citrix XenApp e XenDesktop com servidor DNS AD, servidor do banco de dados SQL, Controlador de entrega Citrix, servidor StoreFront, XenApp Master (VDA), servidor de licenças do Citrix XenApp

![Padrão de Implantação 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Suporte do Site Recovery

Com a finalidade deste artigo, as implantações do Citrix em máquinas virtuais VMware gerenciadas pelo vSphere 6.0/System Center VMM 2012 R2 foram usadas para configurar a recuperação de desastre.

### <a name="source-and-target"></a>Origem e destino

**Cenário** | **Para um site secundário** | **Para o Azure**
--- | --- | ---
**Hyper-V** | Não está no escopo | Sim
**VMware** | Não está no escopo | Sim
**Servidor físico** | Não está no escopo | Sim

### <a name="versions"></a>Versões
Os clientes podem implantar componentes do XenApp como máquinas virtuais em execução no Hyper-V ou VMware, ou como servidores físicos. O Azure Site Recovery pode proteger as implantações físicas e virtuais no Azure.
Como o XenApp 7,7 ou posterior tem suporte no Azure, somente implantações com essas versões podem fazer failover no Azure para recuperação de desastre ou migração.

### <a name="things-to-keep-in-mind"></a>Algumas coisas que se deve manter em mente

1. A proteção e a recuperação de implantações locais usando os computadores com o sistema operacional de servidor para entregar aplicativos publicados do XenApp e áreas de trabalho publicadas do XenApp.

2. A proteção e a recuperação de implantações locais usando os computadores com o sistema operacional de área de trabalho para entregar VDI da área de trabalho para as áreas de trabalho virtuais do cliente, incluindo o Windows 10, não é suportada. Isso ocorre porque o Site Recovery não suporta a recuperação de máquinas com sistemas operacionais de desktop.  Além disso, alguns sistemas operacionais de área de trabalho virtual do cliente (por exemplo, o Windows 7) ainda não têm suporte para licenciamento no Azure. [Saiba mais](https://azure.microsoft.com/pricing/licensing-faq/) sobre licenciamento para áreas de trabalho de cliente/servidor no Azure.

3.  O Azure Site Recovery não pode replicar e proteger clones MCS ou PVS locais existentes.
Você precisa recriar esses clones usando o provisionamento do Azure RM do Controlador de entrega.

4. O NetScaler não pode ser protegido usando o Azure Site Recovery conforme o NetScaler baseia-se em FreeBSD e o Azure Site Recovery não oferece suporte a proteção do sistema operacional do FreeBSD. Você precisa implantar e configurar um novo dispositivo NetScaler do Azure Marketplace após o failover no Azure.


## <a name="replicating-virtual-machines"></a>Replicação de máquinas virtuais

Os seguintes componentes da implantação do Citrix XenApp precisam ser protegidos para habilitar a replicação e a recuperação.

* Proteção do servidor DNS AD
* Proteção do servidor do banco de dados SQL
* Proteção do Controlador de entrega do Citrix
* Proteção do servidor StoreFront.
* Proteção do XenApp Master (VDA)
* Proteção do servidor de licença do Citrix XenApp


**Replicação do servidor DNS AD**

Consulte [Proteger o Active Directory e DNS com o Azure Site Recovery](site-recovery-active-directory.md) na orientação para a replicação e configurar de um controlador de domínio no Azure.

**Replicação do servidor do banco de dados SQL**

Consulte [Proteger o SQL Server com a recuperação de desastre do SQL Server e o Azure Site Recovery](site-recovery-sql.md) na orientação técnica detalhada das opções recomendadas para proteger os servidores SQL.

Siga [esta orientação](site-recovery-vmware-to-azure.md) para começar a replicar as outras máquinas virtuais de componente para o Azure.

![Proteção dos componentes do XenApp](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**Configurações de rede e de computação**

Depois que os computadores estiverem protegidos (o status mostra como "Protegido" em Itens replicados), as configurações de rede e de computação precisam ser definidas.
Em Computação e Rede > Propriedades de Computação, você pode especificar o nome da VM do Azure e o tamanho do destino.
Modifique o nome para que ele fique em conformidade com os requisitos do Azure, se for necessário. Você também pode exibir e adicionar as informações sobre a rede de destino, a sub-rede e o endereço IP que será atribuído à VM do Azure.

Observe o seguinte:

* Você pode definir o endereço IP de destino. Se você não fornecer um endereço, o computador com failover usará o DHCP. Se você definir um endereço que não esteja disponível no failover, o failover não funcionará. O mesmo endereço IP de destino poderá ser usado para failover de teste caso o endereço esteja disponível na rede de failover de teste.

* Para o servidor DNS/AD, manter o endereço local permite que você especifique o mesmo endereço como o servidor DNS para a rede Virtual do Azure.

O número de adaptadores de rede é determinado pelo tamanho especificado para a máquina virtual de destino, como a seguir:

*   Se o número de adaptadores de rede na máquina de origem for menor ou igual ao número de adaptadores permitido para o tamanho da máquina de destino, o destino terá o mesmo número de adaptadores que a origem.
*   Se o número de adaptadores para máquina virtual de origem exceder o número permitido para o tamanho de destino e o tamanho máximo de destino será usado.
* Por exemplo, se uma máquina de origem tiver dois adaptadores de rede e o tamanho da máquina de destino der suporte a quatro, a máquina de destino terá dois adaptadores. Se a máquina de origem tiver dois adaptadores, mas o tamanho de destino com suporte oferecer suporte apenas a uma máquina de destino, ela terá apenas um adaptador.
*   Se a máquina virtual tiver vários adaptadores de rede, todos eles se conectarão à mesma rede.
*   Se a máquina virtual tiver vários adaptadores de rede, o primeiro deles mostrado na lista se tornará o adaptador de rede Padrão na máquina virtual do Azure.


## <a name="creating-a-recovery-plan"></a>Criar um plano de recuperação

Após a replicação estar habilitada para as VMs de componente do XenApp, a próxima etapa é criar um plano de recuperação.
Uma plano de recuperação agrupa as máquinas virtuais com requisitos semelhantes para failover e recuperação.  

**Etapas para criar um plano de recuperação**

1. Adicione máquinas virtuais de componente do XenApp no Plano de recuperação.
2. Clique em Planos de Recuperação -> + Plano de Recuperação. Forneça um nome intuitivo para o plano de recuperação.
3. Para máquinas virtuais do VMware: Selecione a origem como o servidor de processo do VMware, o destino como o Microsoft Azure e o modelo de implantação como o Gerenciador de Recursos, e clique em Selecionar itens.
4. Para máquinas virtuais do Hyper-V: Selecione a origem como o servidor do VMM, o destino como o Microsoft Azure e o modelo de implantação como o Gerenciador de Recursos, e clique em Selecionar itens e, em seguida, selecione as VMs de implantação do XenApp.

### <a name="adding-virtual-machines-to-failover-groups"></a>Adicionar máquinas virtuais aos grupos de failover

Os planos de recuperação podem ser personalizados para adicionar grupos de failover para a ordem de inicialização específica, scripts ou ações manuais. Os grupos a seguir precisam ser adicionados ao plano de recuperação.

1. Grupo de failover 1: DNS do AD
2. Grupo de failover 2: VMs do SQL Server
2. Grupo de failover 3: VM de imagem do VDA Master
3. Grupo de failover 4: Controlador de entrega e VMs do servidor StoreFront


### <a name="adding-scripts-to-the-recovery-plan"></a>Adicionar scripts ao plano de recuperação

Os scripts podem ser executados antes ou depois de um grupo específico em um plano de recuperação. Ações manuais também podem ser incluídas e executadas durante o failover.

O plano de recuperação personalizado parece com o seguinte:

1. Grupo de failover 1: DNS do AD
2. Grupo de failover 2: VMs do SQL Server
3. Grupo de failover 3: VM de imagem do VDA Master

   >[!NOTE]     
   >As etapas 4, 6 e 7, que contém ações manuais ou de script, são aplicáveis apenas a um XenApp local > ambiente com catálogos MCS/PVS.

4. Ação de script ou manual do grupo 3: Desligar a VM do VDA Master.
A VM do VDA Master estará em um estado de execução quando houver failover. Para criar novos catálogos do MCS usando a hospedagem do Azure, é necessário que a VM mestre do VDA esteja no estado Parado (de alocado). Desligue a VM do portal do Azure.

5. Grupo de failover 4: Controlador de entrega e VMs do servidor StoreFront
6. Ação manual ou de script do grupo 3 1:

    ***Adicionar conexão do host do Azure RM***

    Crie uma conexão de host do Azure na máquina do Controlador de entrega para provisionar novos catálogos do MCS no Azure. Siga as etapas, conforme explicado neste [artigo](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/).

7. Ação manual ou de script do grupo 3 2:

    ***Recriar os catálogos MCS no Azure***

    Os clones MCS ou PVS existentes no site primário não serão replicados para o Azure. Você precisa recriar esses clones usando o provisionamento principal replicado VDA e Azure do controlador de entrega. Siga as etapas conforme explicado neste [artigo](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) para criar catálogos do MCS no Azure.

![Plano de recuperação para componentes do XenApp](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >Você pode usar scripts no [local](https://github.com/Azure/azure-quickstart-templates/tree/master/asr-automation-recovery/scripts) para atualizar o DNS com os novos IPs de failover > máquinas virtuais ou para anexar um balanceador de carga pela máquina virtual na qual o failover foi realizado, se necessário.


## <a name="doing-a-test-failover"></a>Executar um failover de teste

Siga [este guia](site-recovery-test-failover-to-azure.md) fazer um failover de teste.

![Plano de Recuperação](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>Executar um failover

Siga [este guia](site-recovery-failover.md) quando estiver realizando um failover.

## <a name="next-steps"></a>Próximas etapas

Você pode [saber mais](https://aka.ms/citrix-xenapp-xendesktop-with-asr) sobre replicação de implantações do Citrix XenApp e XenDesktop neste white paper. Examine as orientações para [replicar outros aplicativos](site-recovery-workload.md) usando o Site Recovery.
