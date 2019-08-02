---
title: Configure a recuperação de desastre em VMs locais do Hyper-V (sem o VMM) para o Azure com o Site Recovery | Microsoft Docs
description: Saiba como configurar a recuperação de desastre de VMs locais do Hyper-V (sem o VMM) para o Azure usando o Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: f4c9ddf608033f6b85268fe74b12861eb8c30082
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295148"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Configurar a recuperação de desastre de VMs locais do Hyper-V para o Azure

O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para sua estratégia de recuperação de desastre ao gerenciar e orquestrar a replicação, o failover e o failback de computadores locais e de VMs (máquinas virtuais) do Azure.

Este é o terceiro tutorial de uma série. Ele mostra como configurar a recuperação de desastre de VMs locais do Hyper-V para o Azure. Esse tutorial aplica-se a VMs do Hyper-V que não são gerenciadas pelo Microsoft System Center VMM (Virtual Machine Manager).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Selecionar a origem e o destino de replicação.
> * Configurar o ambiente de replicação de origem, incluindo componentes de Site Recovery locais e o ambiente de replicação de destino.
> * Criar uma política de replicação.
> * Habilitar a replicação para uma VM.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário. Eles usam opções padrão quando possível e não mostram todas as possíveis configurações e caminhos. Para obter instruções detalhadas, leia o artigo na seção **Guias de Instruções** da [documentação do Site Recovery](https://docs.microsoft.com/azure/site-recovery).

## <a name="before-you-begin"></a>Antes de começar

Este é o terceiro tutorial de uma série. Presume-se que você já tenha concluído as tarefas nos tutoriais anteriores:

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Preparar Hyper-V local](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>Selecione uma meta de replicação

1. No portal do Azure, vá até a folha de cofres dos **Serviços de Recuperação** e selecione o cofre. Preparamos o cofre **ContosoVMVault** no tutorial anterior.
2. No **Guia de Introdução**, selecione **Site Recovery** e, em seguida, selecione **Preparar a Infraestrutura**.
3. Em **Meta de proteção** > **Em que local os seus computadores estão?** , selecione **Local**.
4. Em **Para qual deseja replicar os seus computadores?** , selecione **Para o Azure**.
5. Em **Os seus computadores estão virtualizados?** , selecione **Sim, com o Hyper-V**.
6. Em **Você está usando o VMM do System Center para gerenciar seus hosts Hyper-V**, selecione **Não**.
7. Selecione **OK**.

    ![Meta de replicação](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Confirmar planejamento de implantação

1. Em **Planejamento de implantação**, se você estiver planejando uma implantação grande, baixe o Planejador de Implantações do Hyper-V usando o link na página. [Saiba mais](hyper-v-deployment-planner-overview.md) sobre o planejamento de implantação do Hyper-V.
2. Para este tutorial, não precisamos do Planejador de Implantações. Em **Você concluiu o planejamento de implantação?** , selecione **Farei isso mais tarde** e, em seguida, selecione **OK**.

    ![Planejamento de implantação](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Para configurar o ambiente de origem, crie um site do Hyper-V e adicione a ele hosts Hyper-V que contêm VMs que você deseja replicar. Em seguida, você baixa e instala o Provedor do Azure Site Recovery e o agente dos Serviços de Recuperação do Azure em cada host e registra o site do Hyper-V no cofre.

1. Em **Preparar a Infraestrutura**, selecione **Origem**.
2. Em **Preparar origem**, selecione **+ Site do Hyper-V**.
3. Em **Criar site do Hyper-V**, especifique o nome do site. Estamos usando **ContosoHyperVSite**.

    ![Site do Hyper-V](./media/hyper-v-azure-tutorial/hyperv-site.png)

4. Depois que o site for criado, em **Preparar origem** > **Etapa 1: Selecionar o site Hyper-V**, selecione o site que você criou.
5. Selecione **+ Servidor do Hyper-V**.

    ![Servidor Hyper-V](./media/hyper-v-azure-tutorial/hyperv-server.png)

6. Baixe o instalador do Provedor do Microsoft Azure Site Recovery.
7. Baixe a chave do registro do cofre. Você precisa dessa chave para instalar o Provedor. A chave é válida por cinco dias após ser gerada.

    ![Baixe o Provedor e chave de Registro](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Instale o Provedor

Instale o arquivo de instalação baixado (AzureSiteRecoveryProvider.exe) em cada host do Hyper-V que você deseja adicionar ao site do Hyper-V. A configuração instala o Provedor do Azure Site Recovery e o agente dos Serviços de Recuperação em cada host do Hyper-V.

1. Execute o arquivo de Instalação.
2. No assistente de Instalação do Provedor do Azure Site Recovery > **Microsoft Update**, opte por usar o Microsoft Update para verificar se há atualizações do provedor.
3. Em **Instalação**, aceite a localização de instalação padrão do Provedor e do agente e selecione **Instalar**.
4. Após a instalação, no Assistente de Registro do Microsoft Azure Site Recovery > **Configurações do Cofre**, selecione **Procurar** e, em **Arquivo de Chave**, selecione a chave do cofre do arquivo baixado.
5. Especifique a assinatura do Azure Site Recovery, o nome do cofre (**ContosoVMVault**) e o site de Hyper-V (**ContosoHyperVSite**) ao qual pertence o servidor Hyper-V.
6. Em **Configurações de Proxy**, selecione **Conectar diretamente ao Azure Site Recovery sem um proxy**.
7. Em **Registro**, depois que o servidor foi registrado no cofre, selecione **Concluir**.

Os metadados do servidor Hyper-V são recuperados pelo Azure Site Recovery e o servidor é exibido em **Infraestrutura do Site Recovery** > **Hosts Hyper-V**. Esse processo pode levar até 30 minutos.

#### <a name="install-the-provider-on-a-hyper-v-core-server"></a>Instalar o provedor em um servidor de núcleo do Hyper-V

Se você estiver executando um servidor de núcleo do Hyper-V, baixe o arquivo de instalação e siga estas etapas:

1. Extraia os arquivos do AzureSiteRecoveryProvider.exe para um diretório local executando este comando:

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2. Execute `.\setupdr.exe /i`. Os resultados são registrados em %Programdata%\ASRLogs\DRASetupWizard.log.

3. Registre o servidor executando este comando:

    ```
    cd  "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de origem

Selecione e verifique os recursos de destino:

1. Selecione **Preparar infraestrutura** > **Destino**.
2. Selecione a assinatura e o grupo de recursos **ContosoRG**, em que as VMs do Azure serão criadas após o failover.
3. Selecione o modelo de implantação do **Resource Manager**.

A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

1. Selecione **Preparar a Infraestrutura** > **Configurações de Replicação** >  **+Criar e associar**.
2. Em **Criar e associar política**, especifique um nome de política. Estamos usando **ContosoReplicationPolicy**.
3. Para este tutorial, vamos deixar as configurações padrão:
    - **Frequência de cópia** indica com que frequência os dados delta (após a replicação inicial) serão replicados. A frequência padrão é a cada cinco minutos.
    - **Retenção do ponto de recuperação** indica que os pontos de recuperação serão mantidos por duas horas.
    - **Frequência do instantâneo consistente com o aplicativo** indica que os pontos de recuperação contendo instantâneos consistentes com aplicativos serão criados a cada hora.
    - **Hora de início para replicação inicial** indica que a replicação inicial deve começar imediatamente.
4. Depois que a política for criada, selecione **OK**. Quando você cria uma nova política, ela é automaticamente associada ao site do Hyper-V especificado. Em nosso tutorial, isso equivale a **ContosoHyperVSite**.

    ![Política de replicação](./media/hyper-v-azure-tutorial/replication-policy.png)

## <a name="enable-replication"></a>Habilitar a replicação

1. Em **Replicar aplicativo**, selecione **Origem**.
2. Em **Origem**, selecione o site **ContosoHyperVSite**. Depois, selecione **OK**.
3. Em **Destino**, verifique o destino (Azure), a assinatura do cofre e o modelo de implantação do **Resource Manager**.
4. Se você estiver usando as configurações do tutorial, selecione a conta de armazenamento **contosovmsacct1910171607** criada no tutorial anterior para os dados replicados. Também selecione a rede **ContosoASRnet**, na qual as VMs do Azure estarão localizadas após o failover.
5. Em **Máquinas Virtuais** > **Selecionar**, selecione a VM que deseja replicar. Depois, selecione **OK**.

   É possível acompanhar o progresso da ação **Habilitar Proteção** em **Trabalhos** > **Trabalhos do Site Recovery**. Depois que o trabalho **Finalizar Proteção** for concluído, a replicação inicial será concluída e a VM estará pronta para failover.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Realizar uma simulação de recuperação de desastre](tutorial-dr-drill-azure.md)
