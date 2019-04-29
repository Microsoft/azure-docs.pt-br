---
title: Migrar suas VMs Linux para o Armazenamento Premium do Azure com o Azure Site Recovery | Microsoft Docs
description: Migre máquinas virtuais existentes para o Armazenamento Premium do Azure usando o Site Recovery. O Armazenamento Premium dá suporte ao disco de alto desempenho e baixa latência para cargas de trabalho que usam muita E/S em execução em máquinas virtuais do Azure.
services: virtual-machines-linux,storage
cloud: Azure
author: luywang
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.topic: article
ms.date: 08/15/2017
ms.author: luywang
ms.subservice: disks
ms.openlocfilehash: ffcc2f46a30569979879ff302cde1e3b146d3b50
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60543305"
---
# <a name="migrate-to-premium-storage-by-using-azure-site-recovery"></a>Migrar para o Armazenamento Premium usando o Azure Site Recovery

Os [SSDs Premium do Azure](disks-types.md) oferecem suporte de disco de alto desempenho e baixa latência para máquinas virtuais (VMs) que estão executando cargas de trabalho intensivas para entradas e saídas. Este guia ajuda você a migrar os discos de VM de uma conta de armazenamento Standard para uma conta de armazenamento Premium usando o [Azure Site Recovery](../../site-recovery/site-recovery-overview.md).

O Site Recovery é um serviço do Azure que colabora com sua estratégia de continuidade dos negócios e de recuperação de desastre por meio da coordenação da replicação de servidores físicos locais e VMs na nuvem (Azure) ou em um datacenter secundário. Quando ocorrem paralisações em seu local primário, você realiza o failover em um local secundário a fim de manter aplicativos e cargas de trabalho disponíveis. Quando o local primário retoma as operações normais, você realiza o failback. 

O Site Recovery fornece failovers de teste para dar suporte à simulações de recuperação de desastre sem afetar os ambientes de produção. É possível executar failovers com perda mínima de dados (dependendo da frequência de replicação) para desastres inesperados. No cenário de migração para o Armazenamento Premium, é possível usar um [failover no Site Recovery](../../site-recovery/site-recovery-failover.md) para migrar os discos de destino para uma conta de armazenamento Premium.

É recomendável migrar para o armazenamento Premium usando o Site Recovery, porque essa opção fornece tempo de inatividade mínimo. Essa opção também evita a execução manual de copiar discos e criar novas VMs. O Site Recovery vai copiar os discos sistematicamente e criar novas VMs durante o failover. 

O Site Recovery oferece suporte a vários tipos de failover com pouco ou nenhum tempo de inatividade. Para planejar o tempo de inatividade e estimar a perda de dados, consulte os [tipos de failover no Site Recovery](../../site-recovery/site-recovery-failover.md). Se você se [preparar para se conectar a VMs do Azure após o failover](../../site-recovery/vmware-walkthrough-overview.md), você conseguirá se conectar à VM do Azure usando RDP após o failover.

![Simulação de recuperação de desastre][1]

## <a name="azure-site-recovery-components"></a>Componentes do Azure Site Recovery

Esses componentes do Site Recovery são relevantes para este cenário de migração:

* O **servidor de configuração** é uma VM do Azure que coordena a comunicação e gerencia os processos de recuperação e replicação de dados. Nesta VM, você executa um arquivo único de instalação para instalar o servidor de configuração e um componente adicional, chamado de servidor de processo, como um gateway de replicação. Leia sobre os [pré-requisitos do servidor de configuração](../../site-recovery/vmware-walkthrough-overview.md). Você define o servidor de configuração apenas uma vez e pode usá-lo em todas as migrações para a mesma região.

* O **servidor de processo** é um gateway de replicação que: 

  1. Recebe dados de replicação de VMs de origem.
  2. Otimiza os dados com armazenamento em cache, compactação e criptografia.
  3. Envia os dados para uma conta de armazenamento. 

  Ele também manipula a instalação por push do serviço de mobilidade nas VMs de origem e executa a descoberta automática destas. O servidor de processo padrão é instalado no servidor de configuração. Você pode implantar servidores de processo autônomo adicionais para dimensionar sua implantação. Leia sobre [práticas recomendadas para implantação de servidor de processo](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) e [implantação de servidores de processo adicionais](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). Você define o servidor de processo apenas uma vez e pode usá-lo em todas as migrações para a mesma região.

* O **serviço de mobilidade** é um componente que é implantado em todas as VMs padrão que você deseja replicar. Ele captura gravações de dados na VM padrão e as encaminha ao servidor de processo. Leia sobre [pré-requisitos de máquinas replicadas](../../site-recovery/vmware-walkthrough-overview.md).

Este gráfico mostra como esses componentes interagem:

![Interação de componentes do Azure Site Recovery][15]

> [!NOTE]
> O Site Recovery não dá suporte à migração de discos dos espaços de armazenamento.

Para componentes adicionais para outros cenários, consulte [Arquitetura de cenário](../../site-recovery/vmware-walkthrough-overview.md).

## <a name="azure-essentials"></a>Conceitos básicos do Azure

Estes são os requisitos do Azure para esse cenário de migração:

* Uma assinatura do Azure.
* Uma conta de armazenamento Premium do Azure para armazenar os dados replicados.
* Uma rede virtual do Azure à qual as VMs serão conectadas quando forem criadas no failover. A rede virtual do Azure tem que estar na mesma região em que o Site Recovery é executado.
* Uma conta de armazenamento Standard do Azure para armazenar logs de replicação. Ela pode ser a mesma conta de armazenamento dos discos de VM que estão sendo migrados.

## <a name="prerequisites"></a>Pré-requisitos

* Compreenda os componentes relevantes do cenário de migração na seção anterior.
* Planeje o tempo de inatividade sabendo mais sobre o [Failover no Site Recovery](../../site-recovery/site-recovery-failover.md).

## <a name="setup-and-migration-steps"></a>Etapas de configuração e migração

Você pode usar o Site Recovery para migrar VMs IaaS do Azure entre regiões ou na mesma região. As instruções a seguir foram adaptadas para este cenário de migração com base no artigo [Replicar VMs VMware ou servidores físicos para o Azure](../../site-recovery/vmware-walkthrough-overview.md). Siga os links para obter as etapas detalhadas que complementam as instruções deste artigo.

### <a name="step-1-create-a-recovery-services-vault"></a>Etapa 1: Criar um cofre dos Serviços de Recuperação

1. Abra o [Portal do Azure](https://portal.azure.com).
2. Selecione **Criar um recurso** > **Gerenciamento** > **Backup** e **Site Recovery (OMS)**. Como alternativa, você pode selecionar **Procurar** > **Cofre dos Serviços de Recuperação** > **Adicionar**. 
3. Especifique uma região para a qual as VMs serão replicadas. Para fins de migração na mesma região, selecione a região em que estão as VMs e as contas de armazenamento de origem. 

### <a name="step-2-choose-your-protection-goals"></a>Etapa 2: Escolher as metas de proteção 

1. Na VM em que você deseja instalar o servidor de configuração, abra o [portal do Azure](https://portal.azure.com).
2. Vá para **Cofres dos Serviços de Recuperação** > **Configurações** > **Site Recovery** > **Etapa 1: Preparar a Infraestrutura** > **Meta de proteção**.

   ![Navegando até o painel de meta de proteção][2]

3. Em **Objetivo de proteção**, na primeira lista suspensa, selecione **Para o Azure**. Na segunda lista suspensa, selecione **Não virtualizados / outros** e selecione **OK**.

   ![Painel de meta de proteção com caixas preenchidas][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>Etapa 3: Configure o ambiente de origem (servidor de configuração)

1. Baixe a **Configuração unificada do Azure Site Recovery** e a chave de registro do cofre indo para os painéis **Preparar infraestrutura** > **Preparar origem** > **Adicionar servidor**. 
 
   Você vai precisar da chave de registro do cofre para executar a instalação unificada. A chave é válida por cinco dias após ser gerada.

   ![Navegando até o painel Adicionar Servidor][4]

2. No painel **Adicionar Servidor**, adicione um servidor de configuração.

   ![Painel Adicionar Servidor com Servidor de Configuração selecionado][5]

3. Na VM que você está usando como servidor de configuração, execute a Instalação Unificada para instalar o servidor de configuração e o servidor de processo. Você pode [percorrer as capturas de tela](../../site-recovery/vmware-walkthrough-overview.md) para concluir a instalação. Você pode consultar as capturas de tela a seguir para ver as etapas especificadas para este cenário de migração.

   1. Em **Antes de começar**, selecione **Instalar o servidor de configuração e o servidor em processo**.

      ![Página Antes de Começar][6]

   2. Em **Registro**, procure e selecione a chave de registro que você baixou do cofre.

      ![Página de registro][7]

   3. Em **Detalhes do Ambiente**, selecione se você replicará as VMs VMware. Para esse cenário de migração, escolha **Não**.

      ![Página Detalhes do Ambiente][8]

4. Quando a instalação estiver concluída, faça o seguinte na janela **Servidor de Configuração do Microsoft Azure Site Recovery**:
 
   1. Use a guia **Gerenciar contas** para criar a conta que o Site Recovery pode usar para descoberta automática. (No cenário sobre proteção de computadores físicos, configurar a conta não é relevante, mas você precisa de pelo menos uma conta para habilitar uma das etapas a seguir. Nesse caso, você pode nomear a conta e a senha como qualquer.) 
   2. Use a guia **Registro do cofre** para carregar o arquivo de credencial de cofre.

      ![Guia Registro do Cofre][9]

### <a name="step-4-set-up-the-target-environment"></a>Etapa 4: Configurar o ambiente de origem

Selecione **Preparar infraestrutura** > **Destino** e especifique o modelo de implantação que você deseja usar para as VMs após o failover. Você pode escolher **Clássico** ou **Gerenciador de recursos**, dependendo do cenário.

![Painel Destino][10]

A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis. 

> [!NOTE]
> Se estiver usando uma conta de armazenamento Premium para os dados replicados, você precisará configurar uma conta de armazenamento Standard adicional para armazenar os logs de replicação.

### <a name="step-5-set-up-replication-settings"></a>Etapa 5: Definir as configurações de replicação

Para verificar se o seu servidor de configuração está corretamente associado à política de replicação que você criou, siga [Definir configurações de replicação](../../site-recovery/vmware-walkthrough-overview.md).

### <a name="step-6-plan-capacity"></a>Etapa 6: Planejar a capacidade

1. Use o [planejador de capacidade](../../site-recovery/site-recovery-capacity-planner.md) para estimar com precisão a largura de banda de rede, o armazenamento e outros requisitos para atender às suas necessidades de replicação. 
2. Quando terminar, selecione **Sim, eu fiz isso** na caixa **Você concluiu o planejamento da capacidade?**.

   ![para confirmar que você concluiu o planejamento de capacidade][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>Etapa 7: Instalar o serviço de mobilidade e habilitar a replicação

1. Você pode optar pela [instalação por push](../../site-recovery/vmware-walkthrough-overview.md) para suas VMs de origem ou [instalação manual do serviço de mobilidade](../../site-recovery/site-recovery-vmware-to-azure-install-mob-svc.md) nas suas VMs de origem. Você pode encontrar a solicitação de instalação por push e o caminho do instalador do manual no link fornecido. Se você estiver fazendo uma instalação manual, será preciso usar um endereço IP interno para localizar o servidor de configuração.

   ![Página Detalhes do Servidor de Configuração][12]

   A VM que sofreu o failover terá dois discos temporários: um da VM primária e outro criado durante o provisionamento da VM na região de recuperação. Para excluir o disco temporário antes da replicação, instale o serviço de mobilidade antes de habilitar a replicação. Para saber mais sobre como excluir o disco temporário, veja [Excluir discos da replicação](../../site-recovery/vmware-walkthrough-overview.md).

2. Habilite a replicação da seguinte maneira:
   1. Selecione **Replicar Aplicativo** > **Origem**. Depois de habilitar a replicação pela primeira vez, selecione **+Replicar** no cofre para habilitar a replicação para máquinas virtuais adicionais.
   2. Na etapa 1, configure **Origem** como o servidor de processo.
   3. Na etapa 2, especifique o modelo de implantação pós-failover, uma conta de armazenamento Premium para a migração, uma conta de armazenamento padrão para salvar os logs e uma rede virtual para falhas.
   4. Na etapa 3, adicione VMs protegidas por endereço IP. (Talvez seja necessário um endereço IP interno para localizá-las.)
   5. Na etapa 4, configure as propriedades, selecionando as contas que você configurou anteriormente no servidor de processo.
   6. Na etapa 5, escolha a política de replicação que você criou anteriormente em "Etapa 5: Defina as configurações de replicação."
   7. Selecione **OK**.

   > [!NOTE]
   > Quando uma VM do Azure é desalocada e reiniciada, não há nenhuma garantia de que ela receberá o mesmo endereço IP. Se houver alteração no endereço IP do servidor de processo/configuração ou nas VMs do Azure protegidas, a replicação pode não funcionar corretamente neste cenário.

   ![Habilitar o painel de replicação com a Origem selecionada][13]

Ao criar seu ambiente de Armazenamento do Azure, é recomendável usar contas de armazenamento separadas para cada VM em um conjunto de disponibilidade. É recomendável que você siga a melhor prática na camada de armazenamento para [usar várias contas de armazenamento para cada conjunto de disponibilidade](../linux/manage-availability.md). A distribuição de discos VM para várias contas de armazenamento ajuda a melhorar a disponibilidade de armazenamento e distribui a E/S em toda a infraestrutura de armazenamento do Azure.

Se suas VMs estiverem em um conjunto de disponibilidade, em vez de replicar os discos de todas elas em uma conta de armazenamento, é altamente recomendável migrar várias VMs várias vezes. Desse modo, as VMs em um mesmo conjunto de disponibilidade não compartilham uma única conta de armazenamento. Use o painel **Habilitar Replicação** para configurar uma conta de armazenamento de destino para cada VM, uma de cada vez.
 
Você pode escolher um modelo de implantação pós-failover de acordo com suas necessidades. Se você escolher o Azure Resource Manager como seu modelo de implantação pós-failover, você poderá fazer failover de uma VM (Resource Manager) para uma VM (Resource Manager) ou você poderá fazer failover de uma VM (clássica) para uma VM (Resource Manager).

### <a name="step-8-run-a-test-failover"></a>Etapa 8: Execute um teste de failover

Para verificar se a replicação foi concluída, selecione sua instância do Site Recovery e, em seguida, selecione **Configurações** > **Itens Replicados**. Você verá o status e a porcentagem do processo de replicação. 

Após a conclusão da replicação inicial, execute o failover de teste para validar a estratégia de replicação. Para obter as etapas detalhadas de um failover de teste, consulte [Executar um failover de teste no Site Recovery](../../site-recovery/vmware-walkthrough-overview.md). 

> [!NOTE]
> Antes de você executar qualquer failover, verifique se as VMs e a estratégia de replicação atendem aos requisitos. Para obter mais informações e instruções sobre a execução de um failover de teste, consulte [Failover de teste para o Azure no Site Recovery](../../site-recovery/site-recovery-test-failover-to-azure.md).

Você pode ver o status do failover de teste em **Configurações** > **Trabalhos** > *NOME_DO_SEU_PLANO_DE_FAILOVER*. No painel, você pode ver uma divisão das etapas e os resultados de êxito/falha. Se o failover de teste falhar em alguma etapa, selecione-a para verificar a mensagem de erro. 

### <a name="step-9-run-a-failover"></a>Etapa 9: Executar um failover

Após a conclusão do failover de teste, execute um failover para migrar os discos para o Armazenamento Premium e replicar as instâncias de VM. Siga as etapas detalhadas em [Executar um failover](../../site-recovery/site-recovery-failover.md#run-a-failover). 

Verifique se você selecionou **Desligar VMs e sincronizar os dados mais recentes**. Essa opção especifica que o Site Recovery deve tentar desligar as VMs protegidas e sincronizar os dados para que ocorra o failover da versão mais recente dos dados. Se você não selecionar essa opção ou se tentar e não tiver êxito, o failover será do último ponto de recuperação da VM disponível. 

O Site Recovery vai criar uma instância VM cujo tipo é igual ou semelhante ou de uma VM compatível com armazenamento Premium. Para verificar o desempenho e o preço de várias instâncias de VM, vá para [Preços de Máquinas Virtuais Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) ou [Preços de Máquinas Virtuais Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>Etapas pós-migração

1. **Configurar as VMs replicadas para o conjunto de disponibilidade, se aplicável**. O Site Recovery não dá suporte à migração de VMs junto com o conjunto de disponibilidade. Dependendo da implantação da VM replicada, siga um dos seguintes procedimentos:
   * Para uma VM criada por meio do modelo de implantação clássico: Adicione a VM ao conjunto de disponibilidade no portal do Azure. Para obter as etapas detalhadas, vá para [Adicionar uma máquina virtual existente ao conjunto de disponibilidade](../linux/classic/configure-availability-classic.md).
   * Para uma VM criada por meio do modelo de implantação do Resource Manager: Salve a configuração da VM e, em seguida, exclua e recrie as VMs no conjunto de disponibilidade. Para fazer isso, use o script em [Definir Conjunto de Disponibilidade de VM do Azure Resource Manager](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4). Antes de executar esse script, verifique as limitações dele e planeje o tempo de inatividade.

2. **Exclua VMs e discos antigos**. Verifique se os discos Premium são consistentes com os discos de origem e se as novas VMs realizam a mesma função que as VMs de origem. Exclua a VM e exclua os discos das contas de armazenamento de origem no Portal do Azure. Se houver um problema no qual o disco não é excluído, mesmo que você exclua a VM, consulte [Solucionar problemas de erros de exclusão de recursos de armazenamento](storage-resource-deletion-errors.md).

3. **Limpe a infraestrutura do Azure Site Recovery**. Se o Site Recovery não for mais necessário, você poderá limpar a infraestrutura dele. Exclua itens duplicados, o servidor de configuração e a política de recuperação, então exclua o cofre do Azure Site Recovery.

## <a name="troubleshooting"></a>solução de problemas

* [Monitorar e solucionar problemas de proteção para máquinas virtuais e sites físicos](../../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [Fórum do Microsoft Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>Próximas etapas

Para cenários específicos de migração de máquinas virtuais, consulte as seguintes fontes:

* [Migrar Máquinas Virtuais do Azure entre as Contas de Armazenamento](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Fazer upload de um disco rígido virtual do Linux](upload-vhd.md)
* [Migrando Máquinas Virtuais do Amazon AWS para o Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Confira também as fontes a seguir para saber mais sobre o Armazenamento do Azure e as Máquinas Virtuais do Azure:

* [Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Máquinas Virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Selecione um tipo de disco para VMs IaaS](disks-types.md)

[1]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-1.png
[2]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-2.png
[3]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-3.png
[4]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-4.png
[5]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-5.png
[6]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-6.PNG
[7]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-7.PNG
[8]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-8.PNG
[9]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-9.PNG
[10]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-10.png
[11]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-11.PNG
[12]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-12.PNG
[13]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-13.png
[14]:../site-recovery/media/site-recovery-vmware-to-azure/v2a-architecture-henry.png
[15]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-14.png
