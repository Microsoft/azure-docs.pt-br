---
title: Replicar VMs do Azure Stack no Azure usando Azure Site Recovery | Microsoft Docs
description: Saiba como configurar a recuperação de desastre para VMs do Azure Stack com o serviço do Azure Site Recovery.
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 300b239de74c7d21e03eb0c3b059663b5074964c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728378"
---
# <a name="replicate-azure-stack-vms-to-azure"></a>Replicar VMs do Azure Stack para Azure

Este artigo mostra como configurar a recuperação de desastre de VMs do Azure Stack para o Azure usando o [serviço do Azure Site Recovery](site-recovery-overview.md).

O Azure Site Recovery contribui para a estratégia de BCDR (continuidade dos negócios e recuperação de desastres). O serviço garante que as cargas de trabalho da VM permaneçam disponíveis quando ocorrerem interrupções esperadas e inesperadas.

- O Azure Site Recovery orquestra e gerencia a replicação de VMs para o armazenamento do Azure.
- Quando ocorre uma interrupção no site primário, o Azure Site Recovery é utilizado para fazer failover no Azure.
- No failover, as VMs do Azure são criadas a partir dos dados da VM armazenados e os usuários podem continuar acessando as cargas de trabalho em execução nessas VMs do Azure.
- Quando tudo estiver em execução novamente, você poderá fazer failback das VMs do Azure para seu site primário e começar a replicar para o armazenamento do Azure novamente.


Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * **Etapa 1: preparar VMs do Azure Stack para replicação**. Verifique se as VMs estão em conformidade com os requisitos do Azure Site Recovery e prepare para a instalação do serviço de Mobilidade do Site Recovery. Esse serviço é instalado em cada VM que você quer replicar.
> * **Etapa 2: configurar um cofre dos Serviços de Recuperação**. Configure um cofre do Site Recovery e especifique o que você quer replicar. Os componentes e ações do Site Recovery são configurados e gerenciados no cofre.
> * **Etapa 3: configure o ambiente de replicação de origem**. Configure um servidor de configuração do Site Recovery. O servidor de configuração é uma única VM do Azure Stack que executa todos os componentes necessários pelo Site Recovery. Após configurar o servidor de configuração, registre-o no cofre.
> * **Etapa 4: configurar o ambiente de replicação de destino**. Selecione a sua conta do Azure, a conta de armazenamento do Azure e a rede que deseja utilizar. Durante a replicação, os dados da VM são copiados para o armazenamento do Azure. Após o failover, as VMs do Azure são ingressadas na rede especificada.
> * **Etapa 5: habilitar replicação**. Defina as configurações de replicação e habilite a replicação para as VMs. O serviço de Mobilidade será instalado em uma VM quando a replicação estiver habilitada. O Site Recovery executa uma replicação inicial da VM e, em seguida, a replicação em andamento é iniciada.
> * **Etapa 6: realizar uma análise de recuperação de desastre**: depois que a replicação estiver ativa e em execução, você verificará se o failover funcionará conforme o esperado, realizando uma análise. Para iniciar a análise, você executa um failover de teste no Site Recovery. O failover de teste não afeta o ambiente de produção.

Com essas etapas concluídas, será possível executar um failover completo no Azure quando e conforme for necessário.

## <a name="architecture"></a>Arquitetura

![O diagrama mostra cofres dos Serviços de Recuperação para dois locatários em nuvens associadas a assinaturas de locatário em uma infraestrutura comum do Azure Stack.](./media/azure-stack-site-recovery/architecture.png)

**Localidade** | **Componente** |**Detalhes**
--- | --- | ---
**Servidor de configuração** | Funciona em uma única VM do Azure Stack. | Em cada assinatura, você configura uma VM do servidor de configuração. Essa VM executa os seguintes componentes do Site Recovery:<br/><br/> - Servidor de configuração: coordena as comunicações entre o local e o Azure e gerencia a replicação de dados. - Servidor de processo: atua como um gateway de replicação. Ele recebe dados de replicação, otimiza com cache, compactação e criptografia e envia para o armazenamento do Azure.<br/><br/> Se as VMs que você quer replicar excederem os limites indicados abaixo, você poderá configurar um servidor de processo independente separado. [Saiba mais](vmware-azure-set-up-process-server-scale.md).
**Serviço de mobilidade** | Instalado em cada VM que você quer replicar. | Nas etapas deste artigo, preparamos uma conta para que o serviço de Mobilidade seja instalado automaticamente em uma VM quando a replicação estiver habilitada. Se você não quiser instalar o serviço automaticamente, há vários outros métodos que poderão ser utilizados. [Saiba mais](vmware-azure-install-mobility-service.md).
**Azure** | No Azure, é necessário ter um cofre dos Serviços de Recuperação, uma conta de armazenamento e uma rede virtual. |  Os dados replicados são colocados na conta de armazenamento. VMs do Azure são adicionadas à rede do Azure quando ocorre um failover.


A replicação funciona conforme a seguir:

1. No cofre, você especifica a origem e o destino da replicação, configura o servidor de configuração, cria uma política de replicação e habilita a replicação.
2. O serviço de Mobilidade é instalado na máquina (se você usou a instalação por push) e as máquinas iniciam a replicação de acordo com a política de replicação.
3. Uma cópia inicial dos dados do servidor é replicada para o armazenamento do Azure.
4. Após a conclusão da replicação inicial, a replicação de alterações delta para o Azure é iniciada. As alterações acompanhadas para uma máquina são mantidas em um arquivo .hrl.
5. O servidor de configuração orquestra o gerenciamento de replicação com o Azure (porta de saída HTTPS 443).
6. O servidor de processo recebe dados das máquinas de origem, otimiza e criptografa esses dados e os envia para o armazenamento do Azure (porta 443 de saída).
7. As máquinas replicadas comunicam-se com o servidor de configuração (porta HTTPS 443 de entrada, para gerenciamento de replicação. As máquinas enviam dados de replicação para o servidor de processo (porta HTTPS 9443 de entrada - pode ser modificada).
8. O tráfego é replicado para pontos de extremidade públicos do armazenamento do Azure, pela Internet. Como alternativa, é possível usar o emparelhamento público do Microsoft Azure ExpressRoute. Não há suporte para a replicação do tráfego através de uma VPN de site a site de um site local para o Azure.

## <a name="prerequisites"></a>Pré-requisitos

A seguir, o que é necessário para configurar esse cenário.

**Requisito** | **Detalhes**
--- | ---
**Conta de assinatura do Azure** | Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
**Permissões da conta do Azure** | A conta do Azure utilizada deverá ter permissões para:<br/><br/> - Criar um cofre do Serviço de recuperação<br/><br/> - Criar uma máquina virtual no grupo de recursos e na rede virtual usada para o cenário<br/><br/> - Gravar na conta de armazenamento que você especificar<br/><br/> Observe que:<br/><br/> - Se você criar uma conta, será o administrador da assinatura e poderá executar todas as ações.<br/><br/> - Se você usar uma assinatura existente e não for o administrador, será necessário trabalhar com o administrador para receber permissões de Proprietário ou de Colaborador.<br/><br/> - Se forem necessárias permissões mais granulares, revise [este artigo](site-recovery-role-based-linked-access-control.md).
**VM do Azure Stack** | É necessário ter uma VM do Azure Stack na assinatura do locatário, que será implantada como o servidor de configuração do Site Recovery.


### <a name="prerequisites-for-the-configuration-server"></a>Pré-requisitos para o servidor de configuração

[!INCLUDE [site-recovery-config-server-reqs-physical](../../includes/site-recovery-config-server-reqs-physical.md)]



## <a name="step-1-prepare-azure-stack-vms"></a>Etapa 1: preparar VMs do Azure Stack

### <a name="verify-the-operating-system"></a>Verificar o sistema operacional

Certifique-se de que as VMs estejam executando um dos sistemas operacionais resumidos na tabela.


**Sistema operacional** | **Detalhes**
--- | ---
**Windows de 64 bits** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 (a partir do SP1)
**CentOS** | 5.2 a 5.11, 6.1 a 6.9, 7.0 a 7.3
**Ubuntu** | 14.04 LTS Server, 16.04 LTS Server. Revisar [kernels com suporte](vmware-physical-azure-support-matrix.md#ubuntu-kernel-versions)

### <a name="prepare-for-mobility-service-installation"></a>Preparar para a instalação do serviço de mobilidade

Cada VM que você quer replicar deve ter o serviço de Mobilidade instalado. Para que o servidor de processo instale o serviço automaticamente na VM quando a replicação estiver habilitada, verifique as configurações da VM.

#### <a name="windows-machines"></a>Máquinas do Windows

- É necessário haver conectividade de rede entre a VM na qual quer habilitar a replicação e a máquina que está executando o servidor de processo (por padrão, essa é a VM do servidor de configuração).
- É necessário ter uma conta com direitos de administrador (domínio ou local) na máquina para a qual você habilita a replicação.
    - Você especifica essa conta ao configurar o Site Recovery. Em seguida, o servidor de processo usará essa conta para instalar o serviço de Mobilidade quando a replicação estiver habilitada.
    - Essa conta será usada apenas pelo Site Recovery para a instalação por push e para atualizar o serviço de Mobilidade.
    - Se você não estiver usando uma conta de domínio, precisará desabilitar o controle de acesso de Usuário Remoto na VM:
        - No registro, crie o valor DWORD **LocalAccountTokenFilterPolicy** em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System.
        - Defina o valor para 1.
        - Para fazer isso no prompt de comando, digite o seguinte: **REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1**.
- No Firewall do Windows na VM que você quer replicar, permita Compartilhamento de Arquivos e Impressoras e WMI.
    - Para fazer isso, execute **wf.msc** para abrir o console do Firewall do Windows. Clique com o botão direito em **Regras de Entrada** > **Nova Regra**. Selecione **Predefinida** e escolha **Compartilhamento de arquivos e impressoras** na lista. Conclua o assistente, selecione para permitir a conexão > **Concluir**.
    - Para computadores de domínio, é possível usar um GPO para fazer isso.


#### <a name="linux-machines"></a>Máquinas do Linux

- Verifique se há conectividade de rede entre o computador Linux e o servidor de processo.
- Na máquina para a qual você habilita a replicação, é necessário ter uma conta que seja um usuário raiz no servidor Linux de origem:
    - Você especifica essa conta ao configurar o Site Recovery. Em seguida, o servidor de processo usará essa conta para instalar o serviço de Mobilidade quando a replicação estiver habilitada.
    - Essa conta será usada apenas pelo Site Recovery para a instalação por push e para atualizar o serviço de Mobilidade.
- Verifique se o arquivo /etc/hosts no servidor Linux de origem contém entradas que mapeiam o nome do host local para os endereços IP associados a todos os adaptadores de rede.
- Instale os pacotes openssh, openssh-server e openssl mais recentes no computador que você deseja replicar.
- Verifique se o Secure Shell (SSH) está habilitado e em execução na porta 22.
- Habilite a autenticação de subsistema e senha SFTP no arquivo sshd_config:
    1. Para fazer isso, entre como raiz.
    2. Localize a linha que inicia com **PasswordAuthentication**, no arquivo /etc/ssh/sshd_config. Remova a marca de comentário da linha e altere o valor para **yes**.
    3. Localize a linha que começa com **Subsystem** e remova a marca de comentário existente nessa linha.

        ![Serviço de Mobilidade do Linux](./media/azure-stack-site-recovery/linux-mobility.png)

    4. Reinicie o serviço sshd.


### <a name="note-the-vm-private-ip-address"></a>Anote o endereço IP privado da VM

Para cada máquina que você quer replicar, localize o endereço IP:

1. No Portal do Azure Stack, clique na VM.
2. No menu **Recurso**, clique em **Interfaces de Rede**.
3. Anote o endereço IP privado.

    ![Endereço IP privado](./media/azure-stack-site-recovery/private-ip.png)


## <a name="step-2-create-a-vault-and-select-a-replication-goal"></a>Etapa 2: criar um cofre e selecionar uma meta de replicação

1. No Portal do Azure, selecione **Criar um recurso** > **Ferramentas de Gerenciamento** > **Backup e Site Recovery**.
2. Em **Nome**, digite um nome amigável para identificar o cofre.
3. Em **Grupo de recursos**, crie ou selecione um grupo de recursos. Nós estamos usando **contosoRG**.
4. Em **Localização**, insira a região do Azure. Estamos usando **Europa Ocidental**.
5. Para acessar rapidamente o cofre do painel, selecione **Fixar no painel** > **Criar**.

   ![Criar um novo cofre](./media/azure-stack-site-recovery/new-vault-settings.png)

   O novo cofre é exibido em **Painel** > **Todos os recursos** e na página principal **Cofres dos Serviços de Recuperação**.

### <a name="select-a-replication-goal"></a>Selecione uma meta de replicação

1. Em **Cofres dos Serviços de Recuperação** > especifique um nome do cofre. Nós estamos usando **ContosoVMVault**.
2. Em **Introdução**, selecione Site Recovery. Depois selecione **Preparar Infraestrutura**.
3. Em **Objetivo de proteção** > **Onde os seus computadores estão localizados**, selecione **local**.
4. Em **Para qual deseja replicar os seus computadores**, selecione **Para o Azure**.
5. Em **Os computadores são virtualizados**, selecione **Não virtualizado/outros**. Depois, selecione **OK**.

    ![Meta de proteção](./media/azure-stack-site-recovery/protection-goal.png)

## <a name="step-3-set-up-the-source-environment"></a>Etapa 3: configurar o ambiente de origem

Configure a máquina do servidor de configuração, registre-a no cofre e descubra as máquinas que você quer replicar.

1. Clique em **Preparar a Infraestrutura** > **Origem**.
2. Em **Preparar a origem**, clique em **+Servidor de configuração**.

    ![Captura de tela da caixa de diálogo +Servidor de Configuração com a mensagem "Clique em +Servidor de Configuração na barra de comandos acima para configurar um…".](./media/azure-stack-site-recovery/plus-config-srv.png)

3. Em **Adicionar Servidor**, verifique se **Servidor de Configuração** aparece em **Tipo de servidor**.
5. Baixe o arquivo de instalação Configuração Unificada da Recuperação de Site.
6. Baixe a chave do registro do cofre. Você precisa da chave de registro ao executar a Instalação Unificada. A chave é válida por cinco dias após ser gerada.

    ![Captura de tela da caixa de diálogo Adicionar Servidor com o Tipo de Servidor definido como Servidor de Configuração e o botão Baixar a chave de registro do cofre realçado.](./media/azure-stack-site-recovery/set-source2.png)


### <a name="run-azure-site-recovery-unified-setup"></a>Executar a Configuração Unificada do Azure Site Recovery

Para instalar e registrar o servidor de configuração, faça uma conexão RDP com a VM que você quer usar para o servidor de configuração e execute a Instalação Unificada.

Antes de começar, verifique se o relógio está [sincronizado com um servidor de horário](/windows-server/networking/windows-time-service/windows-time-service-top) na VM antes de iniciar. A instalação falhará se o tempo estiver mais de cinco minutos fora da hora local.

Agora instale o servidor de configuração:

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> O servidor de configuração também pode ser instalado a partir da linha de comando. [Saiba mais](physical-manage-configuration-server.md#install-from-the-command-line).
>
> Pode levar 15 minutos ou mais para que o nome da conta apareça no portal. Para atualizar imediatamente, selecione **Servidores de Configuração** > **_nome do servidor_ *_ > _* Atualizar Servidor**.

## <a name="step-4-set-up-the-target-environment"></a>Etapa 4: Configurar o ambiente de origem

Selecione e verifique os recursos de destino.

1. Em **Preparar infraestrutura** > **Destino**, selecione a assinatura do Azure que deseja usar.
2. Especifique o modelo de implantação de destino.
3. A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis. Se não localizá-las, será necessário criar pelo menos uma conta de armazenamento e uma rede virtual para concluir o assistente.


## <a name="step-5-enable-replication"></a>Etapa 5: habilitar a replicação

### <a name="create-a-replication-policy"></a>Criar uma política de replicação

1. Clique em **Preparar a Infraestrutura** > **Configurações de Replicação**.
2. Em **Criar política de replicação**, especifique um nome de política.
3. Em **Limite de RPO**, especifique o limite de RPO (objetivo de pontos de recuperação).
    - Os pontos de recuperação para dados replicados são criados de acordo com o tempo definido.
    - Essa configuração não afeta a replicação, que é contínua. Simplesmente, emitirá um alerta se o limite for atingido sem um ponto de recuperação sendo criado.
4. Em **Retenção de ponto de recuperação**, especifique quanto tempo cada ponto de recuperação é mantido. VMs replicadas podem ser recuperadas em qualquer ponto na janela de tempo especificada.
5. Em **Frequência de instantâneos consistente com o aplicativo**, especifique com que frequência os instantâneos consistentes com o aplicativo são criados.

    - Um instantâneo consistente com o aplicativo é um instantâneo pontual dos dados do aplicativo dentro da VM.
    - O VSS (Serviço de Cópias de Sombra de Volume) garante que os aplicativos na VM estejam em um estado consistente quando o instantâneo for criado.
6. Selecione **OK** para criar a política.


### <a name="confirm-deployment-planning"></a>Confirmar planejamento de implantação

Você pode ignorar essa etapa agora. Na lista suspensa **Planejamento de Implantação**, clique em **Sim, eu fiz isso**.



### <a name="enable-replication"></a>Habilitar a replicação

Certifique-se de ter concluído todas as tarefas na [Etapa 1: preparar a máquina](#step-1-prepare-azure-stack-vms). Em seguida, habilite a replicação conforme a seguir:

1. Selecione **Replicar aplicativo** > **Origem**.
2. Em **Origem**, selecione o servidor de configuração.
3. Em **Tipo de computador**, selecione **Máquinas físicas**.
4. Selecione o servidor de processo (servidor de configuração). Em seguida, clique em **OK**.
5. Em **Destino**, selecione a assinatura e o grupo de recursos no qual você quer criar as VMs após failover. Escolha o modelo de implantação que você quer usar para as VMs com failover.
6. Selecione a conta de armazenamento do Azure na qual deseja armazenar dados replicados.
7. Selecione a rede e a sub-rede do Azure às quais conectar as VMs do Azure quando elas forem criadas após o failover.
8. Selecione **Configurar agora para computadores selecionados** para aplicar a configuração de rede a todos os computadores selecionados para proteção. Selecione **Configurar mais tarde**, se quiser selecionar a rede do Azure separadamente para cada máquina.
9. Em **Máquinas físicas**, clique em **+Máquina física**. Especifique o nome, o endereço IP e o tipo de sistema operacional de cada computador a ser replicado.

    - Use o endereço IP interno da máquina.
    - Se você especificar o endereço IP público, a replicação talvez não funcione conforme o esperado.

10. Em **Propriedades** > **Configurar propriedades**, selecione a conta que o servidor de processo usará para instalar automaticamente o Serviço de Mobilidade na máquina.
11. Em **Configurações de replicação** > **Definir configurações de replicação**, verifique se a política de replicação correta está selecionada.
12. Clique em **Habilitar a Replicação**.
13. Acompanhe o progresso do trabalho **Habilitar proteção** em **Configurações** > **Trabalhos** > **Trabalhos do Site Recovery**. Após o trabalho de **Finalizar Proteção** ser executado, o computador estará pronto para failover.

> [!NOTE]
> O Site Recovery instala o Serviço de Mobilidade quando a replicação é habilitada para uma VM.
>
> Pode levar 15 minutos ou mais para que as alterações entrem em vigor e apareçam no portal.
>
> Para monitorar as VMs adicionadas, verifique o horário da última descoberta de VMs em **Servidores de Configuração** > **Último Contato Às**. Para adicionar VMs sem esperar pela descoberta agendada, realce o servidor de configuração (não o selecione) e selecione **Atualizar**.


## <a name="step-6-run-a-disaster-recovery-drill"></a>Etapa 6: realizar uma análise de recuperação de desastre

Você executa um failover de teste no Azure para garantir que tudo esteja funcionando conforme o esperado. Esse failover não afetará o ambiente de produção.

### <a name="verify-machine-properties"></a>Verificar as propriedades da máquina

Antes de executar um failover de teste, verifique as propriedades da máquina e certifique-se de que estão em conformidade com os [Requisitos do Azure](vmware-physical-azure-support-matrix.md#azure-vm-requirements). É possível exibir e modificar propriedades conforme a seguir:

1. Em **Itens Protegidos**, clique em **Itens Replicados** > VM.
2. No painel **Item Replicado**, há um resumo das informações da VM, o status de integridade e os últimos pontos de recuperação disponíveis. Clique em **Propriedades** para exibir mais detalhes.
3. Em **Computação e Rede**, modifique as configurações conforme necessário.

    - É possível modificar o nome da VM do Azure, grupo de recursos, tamanho de destino, [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md) e as configurações de disco gerenciado.
    - Também é possível exibir e modificar as configurações de rede. Isso inclui a rede/sub-rede à qual a VM do Azure é associada após o failover e o endereço IP que será atribuído à VM.
1. Em **Discos**, exiba as informações sobre o sistema operacional e os discos de dados na VM.


### <a name="run-a-test-failover"></a>Execute um teste de failover

Quando você executar um failover de teste, acontecerá o seguinte:

1. Uma verificação de pré-requisitos será executada para conferir se todas as condições exigidas para o failover foram cumpridas.
2. O failover processa os dados usando o ponto de recuperação especificado:
    - **Mais recente processado**: a máquina faz failover para o ponto de recuperação mais recente processado pelo Site Recovery. A carimbo de data/hora é mostrado. Com esta opção, não há tempo gasto no processamento de dados, portanto, um RTO (Objetivo de Tempo de Recuperação) baixo é fornecido.
    - **Mais recente consistente com o aplicativo**: o computador faz failover para o ponto de recuperação mais recente consistente com o aplicativo.
    - **Personalizado**: selecione o ponto de recuperação usado para failover.

3. Uma VM do Azure é criada usando os dados processados.
4. O failover de teste pode limpar automaticamente as VMs do Azure criadas durante a análise.

Execute um failover de teste para uma VM, conforme a seguir:

1. Em **Configurações** > **Itens Replicados**, clique na VM > **+Failover de Teste**.
2. Para este passo a passo, selecionaremos para usar o ponto de recuperação **Mais recente processado**.
3. No **Failover de Teste**, selecione a rede do Azure de destino.
4. Clique em **OK** para iniciar o failover.
5. Acompanhe o andamento, clicando na VM para abrir as propriedades. Ou clique no trabalho **Failover de Teste** no *nome do cofre* > **Configurações** > **Trabalhos** >**Trabalhos do Site Recovery**.
6. Após a conclusão do failover, a réplica da VM do Azure aparece no portal do Azure > **Máquinas Virtuais**. Verifique se a VM tem o tamanho adequado, está conectada à rede correta e está em execução.
7. Agora você certamente poderá se conectar à VM replicada no Azure. [Saiba mais](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
8. Para excluir as VMs do Azure criadas durante o failover de teste, clique em **Limpar failover de teste** na VM. Em **Observações**, salve quaisquer observações associadas ao failover de teste.

## <a name="fail-over-and-fail-back"></a>Failover e failback

Depois de configurar a replicação e executar uma análise para certificar-se de que tudo está funcionando, será possível fazer failover nas máquinas no Azure, conforme necessário.

Antes de executar um failover, se você quiser conectar a máquina no Azure após o failover, então [prepare para conectar](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) antes de inciar.

Em seguida, execute um failover conforme a seguir:


1. Em **Configurações** > **Itens Replicados**, clique na máquina > **Failover**.
2. Selecione o ponto de recuperação que você deseja usar.
3. No **Failover de Teste**, selecione a rede do Azure de destino.
4. Selecione **Desligar o computador antes do início do failover**. Com essa configuração, o Site Recovery tentará desligar a máquina de origem antes de iniciar o failover. No entanto, o failover continuará mesmo se o desligamento falhar.
5. Clique em **OK** para iniciar o failover. Você pode acompanhar o progresso do failover na página **Trabalhos** .
6. Após a conclusão do failover, a réplica da VM do Azure aparece no portal do Azure > **Máquinas Virtuais**. Se você preparou para conectar após o failover, verifique se a VM tem o tamanho apropriado, está conectada à rede correta e está em execução.
7. Após verificar a VM, clique em **Confirmar** para concluir o failover. Isso exclui todos os pontos de recuperação disponíveis.

> [!WARNING]
> Não cancele um failover em andamento: antes que o failover seja iniciado, a replicação de VM é interrompida. Se você cancelar um failover em andamento, o failover será interrompido, mas a VM não será replicada novamente.


### <a name="fail-back-to-azure-stack"></a>Failback do Azure Stack

Quando o site primário estiver ativo e em execução novamente, será possível fazer o fail back do Azure para o Azure Stack. Para fazer isso, siga as etapas listadas [aqui](/azure-stack/operator/site-recovery-failback).

## <a name="conclusion"></a>Conclusão

Neste artigo, replicamos as VMs do Azure Stack no Azure. Com a replicação em vigor, executamos uma análise de recuperação de desastre para garantir que o failover do Azure funcionasse conforme o esperado. O artigo também incluiu etapas para executar um failover completo no Azure e failback no Azure Stack.

## <a name="next-steps"></a>Próximas etapas

Após o failback, será possível proteger novamente a VM e começar a replicá-la no Azure novamente e, para fazer isso, repita as etapas deste artigo.