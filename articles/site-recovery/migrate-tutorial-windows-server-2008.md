---
title: Migrar servidores Windows Server 2008 para o Azure com Migrações para Azure/Site Recovery
description: Este artigo descreve como migrar computadores locais do Windows Server 2008 para o Azure e recomenda Migrações para Azure.
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2020
ms.custom: MVC
ms.openlocfilehash: 492bcddaca3cbec2877343c37a665ae62fadcabb
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581259"
---
# <a name="migrate-servers-running-windows-server-2008-to-azure"></a>Migrar servidores que executam o Windows Server 2008 para o Azure

Este tutorial mostra como migrar servidores locais executando o Windows Server 2008 ou 2008 R2 para o Azure usando o Azure Site Recovery. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Preparar o ambiente local para migração.
> * Configure o ambiente de destino.
> * Configurar uma política de replicação.
> * Habilite a replicação.
> * Executar uma migração de teste para verificar se tudo está funcionando conforme o esperado.
> * Fazer failover para o Azure e concluir a migração.

## <a name="migrate-with-azure-migrate"></a>Migrar com as Migrações para Azure

Recomendamos que você migre computadores para o Azure usando o serviço [Migrações para Azure](../migrate/migrate-services-overview.md). 

- O recurso Migrações para Azure é criado especificamente para a migração do servidor.
- As Migrações para Azure oferecem um hub central para descoberta, avaliação e migração de computadores locais para o Azure. O Azure Site Recovery deve ser usado somente para recuperação de desastre, e não para migração.
- As Migrações para Azure dão suporte à migração de servidores que executam o Windows Server 2008.


## <a name="migrate-with-site-recovery"></a>Migrar com o Site Recovery

### <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis


|Sistema operacional  | Ambiente  |
|---------|---------|
|Windows Server 2008 SP2 – 32 e 64 bits (IA-32 e x86-64)</br>- Standard</br>- Enterprise</br>- Datacenter   |     VMs VMware, VMs Hyper-V e servidores físicos    |
|Windows Server 2008 R2 SP1 – 64 bits</br>- Standard</br>- Enterprise</br>- Datacenter     |     VMs VMware, VMs Hyper-V e servidores físicos|

> [!WARNING]
> - A migração de servidores que executam o Server Core não é um procedimento compatível.
> - Verifique se você tem o Service Pack e as atualizações do Windows mais recentes instalados antes de migrar.


### <a name="prerequisites"></a>Pré-requisitos

Antes de começar, vale a pena examinar a arquitetura do Azure Site Recovery para a [migração de servidores VMware e físicos](vmware-azure-architecture.md) ou a [migração de máquinas virtuais Hyper-V](hyper-v-azure-architecture.md) 

Para migrar máquinas virtuais Hyper-V que executam o Windows Server 2008 ou o Windows Server 2008 R2, siga as etapas no tutorial [migrar máquinas locais para o Azure](migrate-tutorial-on-premises-azure.md).

O restante deste tutorial mostra como você pode migrar máquinas virtuais de VMware locais e servidores físicos que executam o Windows Server 2008 ou 2008 R2.
> [!TIP]
> Procurando uma maneira sem uso de agente para migrar VMs VMware para o Azure? [Clique aqui](../migrate/tutorial-migrate-vmware.md)


### <a name="limitations-and-known-issues"></a>Limitações e problemas conhecidos

- O servidor de configuração, os servidores de processo adicionais e o serviço de mobilidade usados para migrar os servidores do Windows Server 2008 SP2 devem estar executando a versão 9.19.0.0 ou posterior do software Azure Site Recovery.

- Pontos de recuperação consistentes com o aplicativo e o recurso de consistência de várias VMs não são compatíveis com a replicação de servidores que executam o Windows Server 2008 SP2. Servidores do Windows Server 2008 SP2 devem ser migrados para um ponto de recuperação consistente com a falha. Os pontos de recuperação consistentes com a falha são gerados a cada cinco minutos por padrão. Usar uma política de replicação com uma frequência de instantâneos consistente com o aplicativo configurada fará com que a integridade de replicação se torne crítica devido à falta de pontos de recuperação consistentes com o aplicativo. Para evitar falsos positivos, defina a frequência de instantâneos consistentes com o aplicativo na política de replicação como "Desativada".

- Os servidores que estão sendo migrados devem ter o .NET Framework 3.5 Service Pack 1 para que o serviço de mobilidade funcione.

- Se o servidor tem discos dinâmicos, você pode perceber, em determinadas configurações, que esses discos no servidor com falha são marcados como offline ou mostrados como discos externos. Você também pode observar que o status do conjunto espelhado para volumes espelhados em discos dinâmicos é marcado como "Falha de redundância". Você pode corrigir esse problema em diskmgmt.msc importando esses discos e reativando-os, manualmente.

- Os servidores que estão sendo migrados devem ter o driver vmstorfl.sys. O failover pode falhar se o driver não está presente no servidor que está sendo migrado. 
  > [!TIP]
  >Verifique se o driver está presente em "C:\Windows\system32\drivers\vmstorfl.sys". Se o driver não for encontrado, você poderá contornar o problema criando um arquivo fictício em lugar dele. 
  >
  > Abra o prompt de comando (Executar > cmd) e execute o seguinte: "copy nul c:\Windows\system32\drivers\vmstorfl.sys"

- Talvez você não seja capaz de usar o RDP para servidores do Windows Server 2008 SP2 que executem o sistema operacional de 32 bits imediatamente após fazer failover ou failover de teste deles para o Azure. Do portal do Azure, reinicie a máquina virtual cujo failover foi realizado e tente se conectar novamente. Se você ainda não consegue se conectar, verifique se o servidor está configurado para permitir conexões de área de trabalho remota e verifique se há alguma regra de firewall ou grupos de segurança de rede bloqueando a conexão. 
  > [!TIP]
  > Um failover de teste é altamente recomendável antes de migrar servidores. Verifique se você executou pelo menos um failover de teste bem-sucedido em cada servidor que você está migrando. Como parte do failover de teste, conecte-se ao computador em que o failover de teste foi realizado e assegure-se de que as coisas funcionem conforme o esperado.
  >
  >A operação de failover de teste não causa interrupções e ajuda a testar migrações, criando máquinas virtuais em uma rede isolada de sua escolha. Ao contrário a operação de failover, durante a operação de failover de teste, a replicação de dados continua em andamento. Antes de estar pronto para migrar, você pode executar quantos failovers de teste desejar. 
  >
  


### <a name="get-started"></a>Introdução

Execute as tarefas a seguir para preparar o ambiente de VMware/físico local e de assinatura do Azure:

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. Preparar o [VMware](vmware-azure-tutorial-prepare-on-premises.md) local


### <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

1. Entre no [portal do Azure](https://portal.azure.com) > **Serviços de Recuperação**.
2. Clique em **Criar um recurso** > **Ferramentas de Gerenciamento** > **Backup e Site Recovery**.
3. Em **Nome**, especifique o nome amigável **W2K8-migration**. Se você tiver mais de uma assinatura, selecione uma delas.
4. Crie um grupo de recursos **w2k8migrate**.
5. Especifique uma região do Azure. Para verificar as regiões com suporte, confira a disponibilidade geográfica nos [Detalhes dos Preços de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Para acessar rapidamente o cofre no painel, clique em **Fixar no painel** e em **Criar**.

   ![Captura de tela mostrando as opções de criação de cofre.](media/migrate-tutorial-windows-server-2008/migrate-windows-server-2008-vault.png)

O novo cofre é adicionado ao **Painel** em **Todos os recursos** e na página principal **Cofres dos Serviços de Recuperação**.


### <a name="prepare-your-on-premises-environment-for-migration"></a>Preparar o ambiente local para a migração

- Para migrar máquinas virtuais do Windows Server 2008 em execução em VMware, [configure o servidor de configuração local no VMware](vmware-azure-tutorial.md#set-up-the-source-environment).
- Se o servidor de configuração não pode ser configurado como uma máquina virtual VMware, [configure o servidor de configuração local em uma máquina virtual ou servidor físico local](physical-azure-disaster-recovery.md#set-up-the-source-environment).

### <a name="set-up-the-target-environment"></a>Configurar o ambiente de origem

Selecione e verifique os recursos de destino.

1. Clique em **Preparar infraestrutura** > **Destino** e selecione a assinatura do Azure que você deseja usar.
2. Especifique o modelo de implantação do Gerenciador de Recursos.
3. A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis.


### <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

1. Para criar uma nova política de replicação, clique em **Infraestrutura de Site Recovery** > **Políticas de Replicação** >  **+Política de Replicação**.
2. Em **Criar política de replicação**, especifique um nome de política.
3. Em **Limite de RPO**, especifique o limite de RPO (objetivo de pontos de recuperação). Um alerta será gerado se o RPO de replicação exceder esse limite.
4. Em **Retenção do ponto de recuperação**, especifique qual será a duração (em horas) da janela de retenção para cada ponto de recuperação. Os servidores replicados podem ser recuperados para qualquer ponto nesta janela. Há suporte para retenção de até 24 horas para máquinas replicadas para armazenamento premium e 72 horas para o armazenamento padrão.
5. Em **Frequência de instantâneos consistentes com o aplicativo**, especifique **Desativado**. Clique em **OK** para criar a política.

A política é associada automaticamente ao servidor de configuração.

> [!WARNING]
> Verifique se você especificou **DESATIVADO** na definição frequência de instantâneos consistente com o aplicativo na política de replicação. Somente os pontos de recuperação consistentes com a falha são compatíveis ao replicar servidores que executam o Windows Server 2008. Especificar qualquer outro valor para a frequência de instantâneos consistentes com o aplicativo resultará em alertas falsos ao tornar crítica a integridade da replicação do servidor crítica devido à falta de pontos de recuperação consistentes com o aplicativo.

   ![Captura de tela mostrando as opções de criação da política de replicação.](media/migrate-tutorial-windows-server-2008/create-policy.png)

### <a name="enable-replication"></a>Habilitar a replicação

[Habilitar a replicação](physical-azure-disaster-recovery.md#enable-replication) para o servidor do Windows Server 2008 SP2/Windows Server 2008 R2 SP1 a ser migrado.
   
   ![Captura de tela mostrando as opções para adicionar computadores físicos.](media/migrate-tutorial-windows-server-2008/Add-physical-server.png)

   ![Captura de tela mostrando as opções para habilitar a replicação.](media/migrate-tutorial-windows-server-2008/Enable-replication.png)

### <a name="run-a-test-migration"></a>Execute um teste de migração

Você poderá executar um failover de teste de servidores em replicação após a replicação inicial ser concluída e o status do servidor mudar para **Protegido**.

Execute um [failover de teste](tutorial-dr-drill-azure.md) para o Azure, para verificar se tudo está funcionando conforme o esperado.

   ![Captura de tela mostrando o comando Failover de teste.](media/migrate-tutorial-windows-server-2008/testfailover.png)


### <a name="migrate-to-azure"></a>Migrar para o Azure

Execute um failover para as máquinas que você deseja migrar.

1. Em **Configurações** > **Itens replicados** clique no computador > **Failover**.
2. Em **Failover**, selecione um **Ponto de Recuperação** para executar o failover. Selecione o ponto de recuperação mais recente.
3. Selecione **Desligar o computador antes do início do failover**. O Site Recovery tentará desligar o servidor antes de disparar o failover. O failover continuará mesmo o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos** .
4. Verifique se a VM do Azure aparece no Azure, conforme o esperado.
5. Em **Itens replicados**, clique com o botão direito do mouse no servidor > **Concluir Migração**. Isso faz o seguinte:

    - Conclui o processo de migração, interrompe a replicação do servidor e a cobrança do Site Recovery para o servidor.
    - Esta etapa limpa os dados de replicação. Ela não exclui as VMs migradas.

   ![Captura de tela mostrando o comando Concluir migração.](media/migrate-tutorial-windows-server-2008/complete-migration.png)


> [!WARNING]
> **Não cancele um failover em andamento**: A replicação do servidor é interrompida antes do início do failover. Se você cancelar um failover em andamento, o failover será interrompido, mas o servidor não continuará replicando.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Veja perguntas comuns](../migrate/resources-faq.md) sobre as Migrações para Azure.