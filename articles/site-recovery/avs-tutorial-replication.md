---
title: Configurar o Azure Site Recovery para VMs da Solução VMware no Azure
description: Saiba como configurar a recuperação de desastre do Azure para as VMs da Solução VMware no Azure com o Azure Site Recovery.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 3ac1f5bd3d44b7f98284cead60b34689f3d7be30
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395488"
---
# <a name="setup-azure-site-recovery-for-azure-vmware-solution-vms"></a>Configurar o Azure Site Recovery para VMs da Solução VMware no Azure

Este artigo descreve como habilitar a replicação de VMs da Solução VMware no Azure para VMs, para recuperação de desastres para o Azure usando o serviço [Azure Site Recovery](site-recovery-overview.md).

Este é o terceiro tutorial em uma série que mostra como configurar a recuperação de desastres para o Azure para VMs da Solução VMware no Azure. No tutorial anterior, [preparamos o ambiente da Solução VMware no Azure](avs-tutorial-prepare-avs.md) para recuperação de desastres para o Azure.


Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Definir as configurações de replicação de origem e um servidor de configuração do Site Recovery na nuvem privada da Solução VMware no Azure
> * Defina as configurações de destino de replicação.
> * Criar uma política de replicação.
> * Habilite a replicação para uma VM do VMware.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário. Eles usam opções padrão quando possível e não mostram todas as possíveis configurações e caminhos. Para obter instruções detalhadas, leia o artigo na seção Instruções do Sumário do Site Recovery.

## <a name="before-you-start"></a>Antes de começar

Conclua os tutoriais anteriores:

1. Verifique se você [configurou o Azure](avs-tutorial-prepare-azure.md) para recuperação de desastres no Azure.
2. Siga [estas etapas](avs-tutorial-prepare-avs.md) para preparar sua implantação da Solução VMware no Azure para recuperação de desastres para o Azure.
3. Neste tutorial, mostramos a você como replicar uma única VM. Se você estiver implantando várias VMs, use a [Ferramenta Planejador de Implantações](https://aka.ms/asr-deployment-planner). [Saiba mais](site-recovery-deployment-planner.md) sobre essa ferramenta.
4. Este tutorial usa várias opções que você talvez queira fazer de modo diferente:
    - O tutorial usa um modelo OVA para criar a VM do VMware do servidor de configuração. Se você não puder fazer isso por algum motivo, siga [estas instruções](physical-manage-configuration-server.md) para configurar o servidor de configuração manualmente.
    - Neste tutorial, o Site Recovery baixa e instala automaticamente o MySQL no servidor de configuração. Se você preferir, poderá configurá-lo manualmente em vez disso. [Saiba mais](vmware-azure-deploy-configuration-server.md#configure-settings).




## <a name="select-a-protection-goal"></a>Selecionar uma meta de proteção

1. Em **Cofres dos Serviços de Recuperação**, selecione o nome do cofre. Estamos usando **ContosoVMVault** para este cenário.
2. Em **Introdução**, selecione Site Recovery. Depois selecione **Preparar Infraestrutura**.
3. Em **Objetivo de proteção** > **Onde os seus computadores estão localizados**, selecione **local**.
4. Em **Para qual deseja replicar os seus computadores**, selecione **Para o Azure**.
5. Em **Os seus computadores estão virtualizados?** , selecione **Sim, com o Hipervisor do VMware vSphere**. Depois, selecione **OK**.



## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Em seu ambiente de origem, é necessário um computador local único e altamente disponível para hospedar esses componentes locais do Site Recovery:

- **Servidor de configuração**: O servidor de configuração coordena as comunicações entre a nuvem privada da Solução VMware no Azure e o Azure e gerencia a replicação de dados.
- **Servidor de processo**: O servidor de processo atua como um gateway de replicação. Ele recebe dados de replicação, otimiza-os com caching, compactação e criptografia e os envia para uma conta de armazenamento em cache no Azure. O servidor de processo também instala o agente do serviço Mobilidade nas VMs que você deseja replicar e executa a descoberta automática de VMs da Solução VMware no Azure.
- **Servidor de destino mestre**: O servidor de destino mestre lida com os dados de replicação durante o failback do Azure.


Todos esses componentes são instalados juntos em um computador único da Solução VMware no Azure, que é conhecido como *servidor de configuração*. Por padrão, para recuperação de desastre da Solução VMware no Azure, configuramos o servidor de configuração como uma VM VMware altamente disponível. Para fazer isso, baixe um modelo de OVA (Aplicativo de Virtualização Aberto) preparado e importe o modelo para o VMware para criar a VM.

- A versão mais recente do servidor de configuração está disponível no portal. Você também pode baixá-la diretamente do [Centro de Download da Microsoft](https://aka.ms/asrconfigurationserver).
- Se, por alguma razão, você não puder usar um modelo OVA para configurar uma VM, siga [estas instruções](physical-manage-configuration-server.md) para definir a configuração do servidor manualmente.
- A licença fornecida com o modelo OVF é uma licença de avaliação válida por 180 dias. O Windows em execução na VM precisa ser ativado com a licença necessária.


### <a name="download-the-vm-template"></a>Baixe o modelo de VM

1. No cofre, vá para **Preparar infraestrutura** > **Origem**.
2. Em **Preparar origem**, selecione **+Servidor de configuração**.
3. Em **Adicionar Servidor**, verifique se o **Servidor de configuração para VMware** aparece em **Tipo de servidor**.
4. Baixe o modelo de OVA para o servidor de configuração.



## <a name="import-the-template-in-vmware"></a>Importar o modelo para a VMware


1. Entre no VMware vCenter Server ou no host vSphere ESXi com o VMware vSphere Client.
2. No menu **Arquivo**, selecione **Implantar Modelo de OVF** para iniciar o **Implante o assistente de modelo de OVF**.

     ![Captura de tela do comando Implantar modelo de OVF no Cliente do VMWare vSphere.](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. Em **Selecionar origem**, insira o local do OVF baixado.
4. Em **Revisar detalhes**, selecione **Avançar**.
5. Em **Selecionar nome e pasta** e **Selecionar configuração**, aceite as configurações padrão.
6. Em **Selecionar armazenamento**, para obter o melhor desempenho, selecione **Thick Provision Eager Zeroed** em **Selecionar formato de disco virtual**.
7. No restante das páginas do assistente, aceite as configurações padrão.
8. Em **Pronto para concluir**, para configurar a VM com as configurações padrão, selecione **Ligar após a implantação** > **Concluir**.

   > [!TIP]
   > Se você quiser incluir um NIC adicional, limpe **Ligar após a implantação** > **Concluir**. Por padrão, o modelo contém um único NIC. É possível incluir NICs adicionais após a implantação.

## <a name="add-an-additional-adapter"></a>Adicionar mais um adaptador

Se você deseja adicionar mais uma NIC ao servidor de configuração, adicione-o antes de registrar o servidor no cofre. Adicionar outros adaptadores não é suportado após o registro.

1. No inventário vSphere Cliente, clique com o botão direito na VM e selecione **Editar configurações**.
2. Em **Hardware**, selecione **Adicionar** > **Adaptador Ethernet**. Em seguida, selecione **Avançar**.
3. Selecione um tipo de adaptador e uma rede.
4. Para se conectar à NIC virtual quando a VM estiver ativada, selecione **Conectar-se ao ligar**. Selecione **Avançar** > **Concluir**. Depois, selecione **OK**.


## <a name="register-the-configuration-server"></a>Registrar o servidor de configuração

Depois que o servidor de configuração estiver definido, registre-o no cofre.

1. No console do cliente VMware vSphere, ative a VM.
2. A VM será inicializada com uma experiência de instalação do Windows Server 2016. Aceite o contrato de licença e insira uma senha de administrador.
3. Após a conclusão da instalação, entre na VM como administrador.
4. Na primeira vez que você fizer logon, a Ferramenta de Configuração do Azure Site Recovery iniciará em alguns segundos.
5. Insira um nome que seja usado para registrar o servidor de configuração com o Site Recovery. Em seguida, selecione **Avançar**.
6. A ferramenta verifica se a VM pode se conectar ao Azure. Depois que a conexão for estabelecida, selecione **Entrar** para fazer logon na sua assinatura do Azure. As credenciais devem ter acesso ao cofre no qual você deseja registrar o servidor de configuração. Verifique se as [funções](vmware-azure-deploy-configuration-server.md#azure-active-directory-permission-requirements) necessárias estão atribuídas a esse usuário.
7. A ferramenta executa algumas tarefas de configuração e, em seguida, é reinicializada.
8. Entre novamente no computador. Em alguns segundos, o Assistente de Gerenciamento do Servidor de Configuração iniciará automaticamente.


### <a name="configure-settings-and-add-the-vmware-server"></a>Definir as configurações e adicionar o servidor do VMware

Conclua a configuração e o registro do servidor de configuração. Antes de continuar, garanta que todos os [pré-requisitos](vmware-azure-deploy-configuration-server.md#prerequisites) sejam atendidos para a configuração bem-sucedida do servidor de configuração.


1. No assistente de gerenciamento do servidor de configuração, selecione **Configurar conectividade**. Nas listas suspensas, primeiro selecione a NIC usada pelo servidor de processo interno para descoberta e instalação por push do serviço de mobilidade em computadores de origem e, em seguida, selecione a NIC que o servidor de configuração usa para conectividade com o Azure. Em seguida, selecione **Salvar**. Não é possível alterar essa configuração depois de ela ter sido definida.
2. Em **Selecionar cofre de Serviços de Recuperação**, selecione sua assinatura do Azure e o grupo de recursos e o cofre relevantes.
3. Em **Instalar software de terceiros**, aceite o contrato de licença. Selecione **Baixar e Instalar** para instalar o MySQL Server. Se você colocou o MySQL no caminho, esta etapa pode ser ignorada. Saiba [mais](vmware-azure-deploy-configuration-server.md#configure-settings)
4. Em **Validar configuração de dispositivo**, os pré-requisitos serão verificados antes de continuar.
5. Em **Configurar vCenter Server/servidor vSphere ESXi**, insira o FQDN ou endereço IP do vCenter Server ou o host vSphere, onde as VMs que deseja replicar estão localizadas. Insira a porta na qual o servidor está escutando. Insira um nome amigável a ser usado para o servidor VMware no cofre.
6. Insira as credenciais de usuário a serem usadas pelo servidor de configuração para se conectar ao servidor VMware. Garanta que o nome de usuário e a senha estão corretos e que o usuário é parte do grupo de Administradores da máquina virtual a ser protegida. O Site Recovery usa essas credenciais para descobrir automaticamente as VMs do VMware que estão disponíveis para replicação. Selecione **Adicionar** e depois **Continuar**.
7. Em **Configurar credenciais de máquina virtual**, insira o nome de usuário e senha que serão utilizados para instalar automaticamente o Serviço de Mobilidade nas VMs quando a replicação estiver habilitada.
    - Para computadores do Windows, a conta precisa de privilégios de administrador local nos computadores que você deseja replicar.
    - Para o Linux, forneça detalhes para a conta raiz.
8. Selecione **Finalizar configuração** para concluir o registro.
9. Após o término do registro, abra o portal do Azure e verifique se o servidor de configuração e o servidor VMware estão listados em **Cofre dos Serviços de Recuperação** > **Gerenciar** > **Infraestrutura do Site Recovery** > **Servidores de Configuração**.


Depois que o servidor de configuração estiver registrado, o Site Recovery se conectará aos servidores VMware usando as configurações especificadas e descobrirá VMs.

> [!NOTE]
> Pode levar 15 minutos ou mais para que o nome da conta apareça no portal. Para atualizar imediatamente, selecione **Servidores de Configuração** > **_nome do servidor_ *_ > _* Atualizar Servidor**.

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de origem

Selecione e verifique os recursos de destino.

1. Selecione **Preparar infraestrutura** > **Destino**. Selecione a assinatura do Azure que deseja usar. Estamos usando um modelo do Azure Resource Manager.
2. O Site Recovery verifica se você tem uma ou mais redes virtuais. É necessário tê-las ao configurar os componentes do Azure no [primeiro tutorial](tutorial-prepare-azure.md) nesta série de tutoriais.

   ![Captura de tela das opções em Preparar infraestrutura > Destino.](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>Criar uma política de replicação

1. Abra o [Portal do Azure](https://portal.azure.com). Pesquise e, em seguida, selecione **cofres dos Serviços de Recuperação**.
2. Selecione o cofre dos Serviços de Recuperação (**ContosoVMVault** neste tutorial).
3. Para criar uma política de replicação, selecione **Infraestrutura do Site Recovery** > **Políticas de Replicação** >  **+Política de Replicação**.
4. Em **Criar política de replicação**, insira o nome de política. Estamos usando o **VMwareRepPolicy**.
5. Em **Limite de RPO**, use o padrão de 60 minutos. Esse valor define a frequência em que são criados os pontos de recuperação. Um alerta será gerado se a replicação contínua exceder esse limite.
6. Em **Retenção do ponto de recuperação**, especifique quanto tempo cada ponto de recuperação será retido. Para este tutorial, estamos utilizando 72 horas. VMs replicadas podem ser recuperadas em qualquer ponto em uma janela de retenção.
7. Em **Frequência de instantâneos consistente com o aplicativo**, especifique com que frequência de instantâneos consistente com o aplicativo são criados. Estamos utilizando o padrão de 60 minutos. Selecione **OK** para criar a política.

   ![Captura de tela das opções em Criar política de replicação.](./media/vmware-azure-tutorial/replication-policy.png)

- A política é associada automaticamente ao servidor de configuração.
- Por padrão, uma política de correspondência é criada automaticamente para failback. Por exemplo, se a política de replicação for **rep-policy**, a política de failback será **rep-policy-failback**. Essa política não é usada até você iniciar um failback do Azure.

Observação: No cenário do VMware para o Azure, o instantâneo consistente com falhas é obtido em um intervalo de cinco minutos.

## <a name="enable-replication"></a>Habilitar a replicação

Habilite a replicação para VMs conforme demonstrado a seguir:

1. Selecione **Replicar aplicativo** > **Origem**.
2. Em **Origem**, selecione **Local**, e selecione o servidor de configuração no **Local de origem**.
3. Em **Tipo de máquina**, selecione **Máquinas Virtuais**.
4. Em **Hipervisor do vCenter/vSphere**, selecione o host vSphere ou o servidor vCenter que gerencia o host.
5. Selecione o servidor de processos (instalado por padrão na VM do servidor de configuração). Depois, selecione **OK**. O status de integridade de cada servidor de processo é indicado de acordo com os limites recomendados e outros parâmetros. Escolha um servidor de processo íntegro. Um servidor de processo [crítico](vmware-physical-azure-monitor-process-server.md#process-server-alerts) não pode ser escolhido. Você pode [solucionar problemas e resolver](vmware-physical-azure-troubleshoot-process-server.md) os erros **ou** configurar um [servidor de processo de expansão](vmware-azure-set-up-process-server-scale.md).
6. Em **Destino**, selecione a assinatura e o grupo de recursos em que deseja criar as VMs com failover. Estamos utilizando o Modelo de implantação do Azure Resource Manager.
7. Selecione a rede e a sub-rede do Azure às quais conectar as VMs do Azure quando elas forem criadas após o failover.
8. Selecione **Configurar agora para computadores selecionados** para aplicar a configuração de rede a todas as VMs nas quais você habilitar a replicação. Selecione **Configurar mais tarde** para selecionar a rede do Azure por computador.
9. Em **Máquinas Virtuais** > **Selecionar máquinas virtuais**, selecione cada máquina que você deseja replicar. Você só pode selecionar computadores para os quais a replicação pode ser habilitada. Depois, selecione **OK**. Se você não conseguir exibir/selecionar alguma máquina virtual específica, [saiba mais](./vmware-azure-troubleshoot-replication.md) sobre como resolver o problema.
10. Em **Propriedades** > **Configurar propriedades**, selecione a conta que será usada pelo servidor de processo para instalar automaticamente o Serviço de Mobilidade no computador.
11. Em **Configurações de replicação** > **Definir configurações de replicação**, verifique se a política de replicação correta está selecionada.
12. Selecione **Habilitar Replicação**. O Site Recovery instala o Serviço de Mobilidade quando a replicação é habilitada para uma VM.
13. Você pode acompanhar o progresso do trabalho **Habilitar Proteção** em **Configurações** > **Trabalhos** > **Trabalhos de Recuperação de Site**. Depois que o trabalho **Finalizar Proteção** for executado e uma geração de ponto de recuperação for concluída, o computador estará pronto para failover.
14. Pode levar 15 minutos ou mais para que as alterações entrem em vigor e apareçam no portal.
15. Para monitorar as VMs adicionadas, verifique o horário da última descoberta de VMs em **Servidores de Configuração** > **Último Contato Às**. Para adicionar VMs sem esperar pela descoberta agendada, realce o servidor de configuração (não o selecione) e selecione **Atualizar**.

## <a name="next-steps"></a>Próximas etapas
Após habilitar a replicação, execute uma simulação para verificar se tudo está funcionando conforme o esperado.
> [!div class="nextstepaction"]
> [Realizar uma simulação de recuperação de desastre](avs-tutorial-dr-drill-azure.md)
