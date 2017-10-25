---
title: Migrar as VMs do AWS para o Azure com o Azure Site Recovery | Microsoft Docs
description: "Este artigo descreve como migrar VMs em execução no AWS (Amazon Web Services) para o Azure usando o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/22/2017
ms.author: raynew
ms.openlocfilehash: aa378c167c81617bc13baa65335335a749d13516
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Migrar das VMs do AWS (Amazon Web Services) para o Azure

Este tutorial ensina como migrar VMs (máquinas virtuais) do AWS (Amazon Web Services) para VMs do Azure usando o Site Recovery. Ao migrar as instâncias EC2 para o Azure, as VMs são tratadas como se fossem computadores locais físicos. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Preparar os recursos do Azure
> * Preparar as instâncias EC2 do AWS para migração
> * Implante um servidor de configuração
> * Habilite a replicação para VMs
> * Teste o failover para verificar se tudo está funcionando
> * Executar um failover avulso para o Azure

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prepare-azure-resources"></a>Preparar os recursos do Azure 

Você precisa ter alguns recursos prontos no Azure para que as instâncias migradas do EC2 seja utilizadas. Isso inclui uma conta de armazenamento, um cofre e uma rede virtual.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

As imagens de máquinas replicadas são mantidas no armazenamento do Azure. As VMs do Azure são criadas do armazenamento quando há failover do local para o Azure.

1. No menu [Portal do Azure](https://portal.azure.com), clique em **Novo** -> **Armazenamento** -> **Conta de armazenamento**.
2. Insira um nome para a conta de armazenamento. Para esses tutoriais usamos o nome **awsmigrated2017**. O nome deve ser exclusivo dentro do Azure e ter entre 3 e 24 caracteres, contendo apenas números e letras minúsculas.
3. Mantenha os padrões para **Modelo de implantação**, **Tipo de conta**, **Desempenho** e **Transferência segura exigida**.
5. Selecione o padrão **RA-GRS** para **Replicação**.
6. Selecione a assinatura que você deseja usar para este tutorial.
7. Para **Grupo de recursos**, selecione **Criar novo**. Neste exemplo, usamos **migrationRG** como o nome.
8. Selecione **Europa Ocidental** como a localização. 
9. Clique em **Criar** para criar a conta de armazenamento.

### <a name="create-a-vault"></a>Criar um cofre

1. No [Portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, clique em **Mais serviços**, pesquise e selecione os **Cofres dos Serviços de Recuperação**.
2. Na página de cofres dos Serviços de Recuperação, clique em **+ Adicionar** no canto superior esquerdo da página.
3. Em **Nome**, digite *myVault*. 
4. Em **Assinatura**, selecione a assinatura apropriada.
4. Para o **Grupo de recursos**, selecione **Usar existente** e selecione *migrationRG*. 
5. Em **Localização**, selecione *Europa Ocidental*. 
5. Para acessar rapidamente o novo cofre do painel, selecione **Fixar no painel**.
7. Quando terminar, clique em **Criar**.

O novo cofre é exibido no **Painel** > **Todos os recursos** e na página **Cofres dos Serviços de Recuperação** principal.

### <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

Quando as VMs do Azure são criadas após a migração (failover), elas são ingressadas nessa rede.

1. No [Portal do Azure](https://portal.azure.com), clique em **Novo** > **Rede** >
   **Rede Virtual**
3. Em **Nome**, digite *myMigrationNetwork*.
4. Deixe o valor padrão para o **Espaço de endereço**.
5. Em **Assinatura**, selecione a assinatura apropriada.
6. Em **Grupo de recursos**, selecione **Usar existente** e escolha *migrationRG* na lista suspensa.
7. Em **Localização**, selecione **Europa Ocidental**. 
8. Mantenha os padrões para a **Sub-rede**, tanto o **Nome** quanto o **Intervalo IP**.
9. Deixe os **Pontos de Extremidade de Serviço** desabilitados.
10. Quando terminar, clique em **Criar**.


## <a name="prepare-the-ec2-instances"></a>Preparar as instâncias de EC2

Você precisará de uma ou mais VMs que deseja migrar. Essas instância EC2 devem estar executando a versão de 64 bits do Windows Server 2008 R2 SP1 ou posterior, Windows Server 2012, Windows Server 2012 R2 ou Red Hat Enterprise Linux 6.7 (somente instâncias virtualizadas de HVM). O servidor deve ter somente os drivers Citrix PV ou AWS PV. Não há suporte para executar os drivers RedHat PV.

O serviço de Mobilidade deve ser instalado em cada VM que você deseja replicar. O Site Recovery instala esse serviço automaticamente quando você habilita a replicação para a VM. Para instalação automática, você precisa preparar uma conta na instância EC2 que o Site Recovery usará para acessar a VM.

Você pode usar uma conta local ou de domínio. Para VMs do Linux, a conta deve ser a raiz no servidor Linux de origem. Para VMs do Windows, se você não estiver usando uma conta de domínio, desabilite o Controle de Acesso de Usuários Remotos no computador local:

  - No registro, em **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, adicione a entrada DWORD **LocalAccountTokenFilterPolicy** e defina o valor para 1.
    
Você também precisa de uma instância de EC2 separada que pode usar como o servidor de configuração do Site Recovery. Essa instância deve estar em execução no Windows Server 2012 R2. 
    

## <a name="prepare-the-infrastructure"></a>Preparar a infraestrutura 

Na página do portal do seu cofre, selecione **Site Recovery** na seção **Introdução** e, em seguida, clique em **Preparar Infraestrutura**.

### <a name="1-protection-goal"></a>1 Meta de Proteção

Selecione os seguintes valores na página **Meta de Proteção**:
   
|    |  | 
|---------|-----------|
| Onde seus computadores estão localizados? | **Configuração local**|
| Para qual local deseja replicar as máquinas? |**Para o Azure**|
| Seus computadores são virtualizados? | **Não virtualizados / Outros**|

Quando terminar, clique em **OK** para mover para a próxima seção.

### <a name="2-source-prepare"></a>2 Preparo da Origem 

Na página **Preparar origem**, clique em **+ Servidor de configuração**. 

1. Use uma instância de EC2 em execução no Windows Server 2012 R2 para criar um servidor de configuração e registrá-lo com seu cofre de recuperação.

2. Configure o proxy na VM de instância EC2 que você está usando como servidor de configuração para que ela possa acessar as [URLs de Serviço](site-recovery-support-matrix-to-azure.md).

3. Baixe o programa [Configuração Unificada do Microsoft Azure Site Recovery](http://aka.ms/unifiedinstaller_wus). Você pode baixá-lo em seu computador local e copiá-lo na VM que está usando como o servidor de configuração.

4. Clique no botão **Baixar** para baixar a chave de registro do cofre. Copie o arquivo baixado para a VM que você está usando como o servidor de configuração.

5. Na VM, clique com botão direito do mouse no instalador baixado para a **Instalação Unificada do Microsoft Azure Site Recovery** e selecione **Executar como administrador**. 

    1. Em **Antes de começar**, selecione **Instalar o servidor de configuração e o servidor em processo** e, sem seguida, clique em **Avançar**.
    2. Em **Licença de Software de Terceiros**, selecione **Aceito o contrato de licença de terceiros**. e clique em **Próximo**.
    3. Em **Registro**, clique em procurar e navegue até o local em que você colocou o arquivo da chave de registro do cofre e, em seguida, clique em **Avançar**.
    4. Em **Configurações da Internet**, selecione **Conectar ao Azure Site Recovery sem um servidor proxy.** e clique em **Próximo**.
    5. Na página **Verificação de pré-requisitos**, ele executa verificações de vários itens. Quando ele estiver concluído, clique em **Avançar**. 
    6. Em **Configuração do MySQL**, forneça as senhas necessárias e, em seguida, clique em **Avançar**.
    7. Em **Detalhes do ambiente**, selecione **Não**, você não precisa proteger computadores VMware e, em seguida, clique em **Avançar**.
    8. Em **Local de instalação**, clique em **Avançar** para aceitar o padrão.
    9. Em **Seleção de Rede**, clique em **Avançar** para aceitar o padrão.
    10. Em **Resumo**, clique em **Instalar**.
    11. O **Progresso da Instalação** mostra informações sobre o lugar em que você está no processo de instalação. Quando ele estiver concluído, clique em **Concluir**. Você receberá um pop-up quanto à necessidade de uma possível reinicialização. Clique em **OK**. Você também receberá um pop-up sobre a Frase Secreta de Conexão do Servidor de Configuração. Copie a frase secreta para sua área de transferência e salve-a em um local seguro.
    
6. Na VM, execute **spsconfigtool.exe** para criar uma ou mais contas de gerenciamento no servidor de configuração. Verifique se as contas de gerenciamento tem permissões de administrador nas instâncias EC2 que você deseja migrar. 

Quando terminar de definir o servidor de configuração, volte ao portal e selecione o servidor que você acabou de criar para o **Servidor de Configuração** e clique em *OK** para passar para a etapa 3 Preparação do Destino.

### <a name="3-target-prepare"></a>3 Preparação do Destino 

Nesta seção, você vai inserir informações sobre os recursos que criou quando passou pela seção [Preparar recursos do Azure](#prepare-azure-resources), anteriormente neste tutorial.

1. Em **Assinatura**, selecione a assinatura do Azure que você usou no tutorial [Preparar o Azure](tutorial-prepare-azure.md).
2. Selecione **Gerenciador de Recursos** como o modelo de implantação.
3. A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis. Elas devem ser os recursos que você criou quando passou pela seção [Preparar recursos do Azure](#prepare-azure-resources), anteriormente neste tutorial
4. Quando terminar, clique em **OK**.


### <a name="4-replication-settings-prepare"></a>4 Preparação das Configurações de Replicação 

Você precisa criar uma política de replicação antes de habilitar a replicação

1. Clique em **+ Replicar e Associar**.
2. Em **Nome**, digite **myReplicationPolicy**.
3. Deixe o restante das configurações padrão e clique em **OK** para criar a política. A nova política é associada automaticamente ao servidor de configuração. 

### <a name="5-deployment-planning-select"></a>5 Seleção do planejamento de implantação 

Em **Você concluiu o planejamento da implantação?**, selecione **Farei isso mais tarde** na lista suspensa e, em seguida, clique em **OK**.

Quando terminar todas as 5 seções de **Preparar infraestrutura**, clique em **OK**.


## <a name="enable-replication"></a>Habilitar a replicação

Habilite a replicação para cada VM que você deseja migrar. Quando a replicação for habilitada, o Site Recovery instalará automaticamente o serviço de Mobilidade. 

1. Abra o [Portal do Azure](htts://portal.azure.com).
1. Na página do seu cofre, em **Introdução**, clique em **Site Recovery**.
2. Em **Para computadores locais e VMs do Azure**, clique em **Etapa 1: replicar aplicativo**. Complete as páginas do assistente com as seguintes informações e clique em **OK** em cada página quando terminar:
    - 1 Configuração da Origem:
      
    |  |  |
    |-----|-----|
    | Origem: | **Localmente**|
    | Local de origem:| O nome da instância EC2 do servidor da configuração.|
    |Tipo de computador: | **Computadores físicos**|
    | Servidor de processo: | Selecione o servidor de configuração na lista suspensa.|
    
    - 2 Configuração do Destino
        
    |  |  |
    |-----|-----|
    | Destino: | Deixar o padrão.|
    | Assinatura: | Selecione a assinatura que você usou.|
    | Grupo de recursos do failover de postagem:| Use o grupo de recursos que você criou na seção [Preparar recursos do Azure](#prepare-azure-resources).|
    | Modelo de implantação pós-failover: | Escolha **Resource Manager**|
    | Conta de armazenamento: | Escolha a conta de armazenamento criada na seção [Preparar recursos do Azure](#prepare-azure-resources).|
    | Rede do Azure: | Escolha **Configurar agora para os computadores selecionados**|
    | Rede do Azure pós-failover: | Escolha a rede criada na seção [Preparar recursos do Azure](#prepare-azure-resources).|
    | Sub-rede: | Selecione o **padrão** na lista suspensa.|
    
    - 3 Seleção de Computadores Físicos
        
        Clique em **+ Computador físico** e, em seguida, insira o **Nome**, o **Endereço IP** e **Tipo de SO** da instância EC2 que você deseja migrar e em seguida, clique em **OK**.
        
    - 4 Propriedades Configuram Propriedades
        
        Selecione a conta que você criou no servidor de configuração na lista suspensa e clique em **OK**.
        
    - 5 Configurações de Replicação definem Configurações de Replicação
    
        Verifique se a política de replicação selecionada na lista suspensa é **myReplicationPolicy** e, em seguida, clique em **OK**.
        
3. Quando o assistente for concluído, clique em **Habilitar replicação**.
        

Você pode acompanhar o progresso do trabalho **Habilitar Proteção** em **Monitoramento e relatórios** > **Trabalhos** > **Trabalhos de Recuperação de Site**. Após o trabalho de **Finalizar Proteção** ser executado, o computador estará pronto para failover.        
        
Quando você habilitar a replicação para uma VM, poderá levar 15 minutos ou mais para que as alterações entrem em vigor e apareçam no portal.

## <a name="run-a-test-failover"></a>Execute um teste de failover

Quando você executar um failover de teste, acontecerá o seguinte:

1. Uma verificação de pré-requisitos será executada para conferir se todas as condições exigidas para o failover foram cumpridas.
2. O failover processa os dados para que uma VM do Azure possa ser criada. Se o ponto de recuperação mais recente for selecionado, um ponto de recuperação será criado nos dados.
3. Uma VM do Azure é criada usando os dados processados na etapa anterior.

No portal, execute o failover de teste da seguinte maneira:

1. Na página do seu cofre, acesse **Itens protegidos** > **Itens replicados**> clique em VM > **+ Failover de Teste**.

2. Selecione um ponto de recuperação para usar no failover:
    - **Processado mais recente**: falha na VM para o ponto de recuperação único que foi processado pelo Site Recovery. A carimbo de data/hora é mostrado. Com esta opção, não há tempo gasto no processamento de dados, portanto, um RTO (Objetivo de Tempo de Recuperação) baixo é fornecido.
    - **Consistente com o aplicativo mais recente**: essa opção falha em todas as VMs para o ponto de recuperação consistente com o aplicativo mais recente. A carimbo de data/hora é mostrado.
    - **Personalizar**: selecione qualquer ponto de recuperação.
3. Em **Failover de Teste**, selecione a rede de destino do Azure à qual as VMs do Azure serão conectadas após o failover. Ela deverá ser a rede criada na seção [Preparar recursos do Azure](#prepare-azure-resources).
4. Clique em **OK** para iniciar o failover. Você pode acompanhar o andamento clicando na VM para abrir suas propriedades. Ou você pode clicar no trabalho **Failover de teste** na página de seu cofre em **Monitoramento e relatórios** > **Trabalhos** >
   **Trabalhos do Site Recovery**.
5. Após a conclusão do failover, a réplica da VM do Azure aparece no portal do Azure > **Máquinas Virtuais**. Verifique se a VM é do tamanho apropriado, se está conectada à rede correta e se está em execução.
6. Agora você certamente poderá se conectar à VM replicada no Azure.
7. Para excluir as VMs do Azure criadas durante o failover de teste, clique em **Limpar failover de teste** no plano de recuperação. Em **Observações**, registre e salve todas as observações associadas ao failover de teste.

Em alguns cenários, o failover requer um processamento adicional que leva cerca de oito a dez minutos para ser concluído. 


## <a name="migrate-to-azure"></a>Migrar para o Azure

Execute um failover real nas instância EC2 para migrá-las para VMs do Azure. 

1. Em **Itens protegidos** > **Itens replicados** clique nas instâncias do AWS > **Failover**.
2. Em **Failover**, selecione um **Ponto de Recuperação** para executar o failover. Selecione o ponto de recuperação mais recente.
3. Selecione **Desligue o computador antes do início do failover** se quiser que o Site Recovery tente realizar um desligamento das máquinas virtuais de origem antes de disparar o failover. O failover continuará mesmo o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos** .
4. Verifique se a VM é exibida em **Itens replicados**. 
5. Clique com botão direito do mouse em cada VM > **Concluir Migração**. Isso conclui o processo de migração, interrompe a replicação da VM do AWS e interrompe a cobrança do Site Recovery para a VM.

    ![Migração completa](./media/tutorial-migrate-aws-to-azure/complete-migration.png)

> [!WARNING]
> **Não cancelar um failover em andamento**: antes de iniciar o failover, a replicação da VM é interrompida. Se você cancelar um failover em andamento, o failover será interrompido, mas a VM não será replicada novamente.  


    

## <a name="next-steps"></a>Próximas etapas

Neste tópico, você aprendeu como migrar instâncias EC2 do AWS para VMs do Azure. Para saber mais sobre VMs do Azure, continue o tutorial para VMs do Windows.

> [!div class="nextstepaction"]
> [Tutoriais de máquina virtual do Windows Azure](../virtual-machines/windows/tutorial-manage-vm.md)
