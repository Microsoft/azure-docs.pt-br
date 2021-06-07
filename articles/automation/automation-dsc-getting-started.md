---
title: Introdução ao State Configuration da Automação do Azure
description: Este artigo explica como fazer as tarefas mais comuns no State Configuration da Automação do Azure.
services: automation
ms.subservice: dsc
ms.date: 04/15/2019
ms.topic: conceptual
ms.openlocfilehash: 95072970004ae53663f130a78af36a84a6fab685
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99051491"
---
# <a name="get-started-with-azure-automation-state-configuration"></a>Introdução ao State Configuration da Automação do Azure

Este artigo fornece um guia passo a passo para realizar as tarefas mais comuns com o State Configuration da Automação do Azure, como criar, importar e compilar configurações, permitindo que os computadores gerenciem e exibam relatórios. Para uma visão geral do State Configuration, consulte [Visão geral do State Configuration](automation-dsc-overview.md). Para a documentação da DSC (Desired State Configuration), consulte [Visão geral da Desired State Configuration do Windows PowerShell](/powershell/scripting/dsc/overview/overview).

Se quiser um exemplo de ambiente já configurado sem seguir as etapas descritas neste artigo, você poderá usar o [modelo de nó gerenciado da Automação do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration). Este modelo configura um ambiente completo do State Configuration (DSC), incluindo uma VM do Azure gerenciada pelo State Configuration (DSC).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os exemplos neste artigo, são necessários:

- Uma conta de Automação do Azure. Para saber mais sobre uma conta de automação e seus requisitos, consulte [visão geral da autenticação da conta de automação](./automation-security-overview.md).
- Uma VM do Azure Resource Manager (não clássica) executando um [sistema operacional suportado](automation-dsc-overview.md#operating-system-requirements). Para obter instruções sobre a criação de uma VM, consulte [Criar sua primeira máquina virtual do Windows no portal do Azure](../virtual-machines/windows/quick-create-portal.md)

## <a name="create-a-dsc-configuration"></a>Criar uma configuração de DSC

Crie uma [configuração de DSC](/powershell/scripting/dsc/configurations/configurations) simples que garante a presença ou a ausência do WindowsFeature do **Servidor Web** (IIS), dependendo de como os nós são atribuídos.

1. Inicie o [VSCode](https://code.visualstudio.com/docs) (ou qualquer editor de texto).
1. Digite o seguinte texto:

    ```powershell
    configuration TestConfig
    {
        Node IsWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true
            }
        }

        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'
            }
        }
    }
    ```
1. Salve o arquivo como **TestConfig.ps1**.

Essa configuração chama um recurso em cada bloco de nó, o [recurso WindowsFeature](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource). Esse recurso garante a presença ou a ausência do recurso **Servidor Web**.

## <a name="import-a-configuration-into-azure-automation"></a>Importar uma configuração na Automação do Azure

Em seguida, importe a configuração para a conta de Automação.

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página de conta da Automação, selecione **State Configuration (DSC)** em **Gerenciamento de configuração**.
1. Na página State Configuration (DSC), clique na guia **Configurações** e depois em **Adicionar**.
1. No painel Configuração de importação, vá para o arquivo `TestConfig.ps1` no seu computador.

   ![Captura de tela da folha **Importar Configuração**](./media/automation-dsc-getting-started/AddConfig.png)

1. Clique em **OK**.

## <a name="view-a-configuration-in-azure-automation"></a>Exibir uma configuração na Automação do Azure

Depois de importar uma configuração, você pode vê-la no Portal do Azure.

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página de conta da Automação, selecione **State Configuration (DSC)** em **Gerenciamento de configuração**.
1. Na página State Configuration (DSC), clique na guia **Configurações** e depois em **TestConfig**. Esse é o nome da configuração que você importou no procedimento anterior.
1. No painel de configuração de TestConfig, clique em **Exibir origem da configuração**.

   ![Captura de tela da folha TestConfig configuration (Configuração do TestConfig)](./media/automation-dsc-getting-started/ViewConfigSource.png)

   O painel de origem de configuração TestConfig abre e mostra o código do PowerShell para a configuração.

## <a name="compile-a-configuration-in-azure-automation"></a>Compilar uma configuração na Automação do Azure

Antes de aplicar um estado desejado a um nó, uma configuração DSC definindo esse estado deve ser compilada em uma ou mais configurações de nó (documento MOF) e colocada no servidor de pull do DSC de Automação. Para uma descrição mais detalhada das configurações de compilação no State Configuration (DSC), consulte [Compilar configurações no State Configuration da Automação do Azure](automation-dsc-compile.md).
Para obter mais informações sobre a compilação das configurações, consulte [Configurações da DSC](/powershell/scripting/dsc/configurations/configurations).

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página da conta da Automação, clique em **State Configuration (DSC)** em **Configuração**.
1. Na página State Configuration (DSC), clique na guia **Configurações** e depois em **TestConfig**. Este é o nome da configuração importada anteriormente.
1. No painel de configuração TestConfig, clique em **Compilar** e depois clique em **Sim**. Isso inicia um trabalho de compilação.

   ![Captura de tela da página de configuração de TestConfig realçando o botão de compilação](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Quando você compila uma configuração na Automação do Azure, ela implanta automaticamente quaisquer arquivos MOF de configuração de nó no servidor de pull.

## <a name="view-a-compilation-job"></a>Exibir um trabalho de compilação

Após iniciar uma compilação, você poderá visualizá-la no bloco **Trabalhos de Compilação** na página **Configuração**. O bloco **Trabalhos de Compilação** mostra os trabalhos atualmente em execução, concluídos e com falha. Quando você abre um painel de trabalho de compilação, ela mostra informações sobre esse trabalho, incluindo quaisquer erros ou avisos encontrados, parâmetros de entrada usados na configuração e logs de compilação.

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página da conta da Automação, clique em **State Configuration (DSC)** em **Configuração**.
1. Na página State Configuration (DSC), clique na guia **Configurações** e depois em **TestConfig**. Este é o nome da configuração importada anteriormente.
1. Em **Trabalhos de compilação**, selecione o trabalho de compilação para exibir. Um painel de trabalho de compilação abre com a data em que o trabalho de compilação foi iniciado.

   ![Captura de tela da página Trabalho de Compilação](./media/automation-dsc-getting-started/CompilationJob.png)

1. Clique em um bloco no painel de trabalho de compilação para ver mais detalhes sobre o trabalho.

## <a name="view-node-configurations"></a>Exibir configurações de nó

A conclusão com êxito de um trabalho de compilação cria uma ou mais novas configurações de nó. Uma configuração de nó é um documento MOF que é implantado no servidor de pull e está pronto para ser submetido ao pull e aplicado por um ou mais nós. É possível exibir as configurações de nós na conta de Automação na página State Configuration (DSC). Uma configuração de nó tem um nome com o formulário `ConfigurationName.NodeName`.

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página da conta da Automação, clique em **State Configuration (DSC)** em **Configuração**.
1. Na página State Configuration (DSC), clique na guia **Configurações compiladas**.

   ![Captura de tela da guia Configurações Compiladas](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="enable-an-azure-resource-manager-vm-for-management-with-state-configuration"></a>Habilitar uma VM do Azure Resource Manager para gerenciamento com o State Configuration

É possível usar o State Configuration para gerenciar VMs do Azure (tanto clássica quanto do gerenciador de recursos), VMs locais, computadores Linux, VMs de AWS e computadores físicos locais. Neste artigo, você aprenderá como habilitar somente VMs do Azure Resource Manager. Para obter mais informações sobre como habilitar outros tipos de computadores, consulte [Habilitar computadores para gerenciamento pelo State Configuration da Automação do Azure](automation-dsc-onboarding.md).

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página da conta da Automação, clique em **State Configuration (DSC)** em **Configuração**.
1. Na página State Configuration (DSC), selecione a guia **Nós** e, em seguida, clique em **+ Adicionar**.

   ![Captura de tela da página Nós DSC realçando o botão Adicionar VM do Azure](./media/automation-dsc-getting-started/OnboardVM.png)

1. No painel Máquinas Virtuais, selecione a VM.
1. No painel de detalhes da máquina virtual, clique em **+ Conectar**.

   > [!IMPORTANT]
   > A VM deve ser do Azure Resource Manager e executar um [sistema operacional compatível](automation-dsc-overview.md#operating-system-requirements).

2. Na página Registro, selecione o nome da configuração do nó para aplicar à VM no campo **Nome da configuração de nó**. Fornecer um nome neste ponto é opcional. Você pode alterar a configuração de nó atribuída após habilitar o nó.

3. Marque **Reinicialize o nó, se necessário** e clique em **OK**.

   ![Captura de tela da folha Registro](./media/automation-dsc-getting-started/RegisterVM.png)

   A configuração de nó especificada é aplicada à VM em intervalos especificados pelo valor fornecido para **Frequência do modo de configuração**. A VM verifica se há atualizações para a configuração de nó em intervalos especificados pelo valor de **Frequência de atualização**. Para obter mais informações sobre como esses valores são usados, consulte [Configurando o Gerenciador de Configuração Local](/powershell/scripting/dsc/managing-nodes/metaConfig).

O Azure inicia o processo de habilitar a VM. Quando estiver concluída, a VM aparecerá na guia **Nós** da página State Configuration (DSC) na conta de Automação.

## <a name="view-the-list-of-managed-nodes"></a>Exibir a lista de nós gerenciados

É possível exibir a lista de todos os computadores que foram habilitados para gerenciamento na conta de Automação na guia **Nós** da página do State Configuration (DSC).

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página da conta da Automação, clique em **State Configuration (DSC)** em **Configuração**.
1. Na página State Configuration (DSC),clique na guia **Nós**.

## <a name="view-reports-for-managed-nodes"></a>Exibir relatórios para nós gerenciados

Sempre que o State Configuration executar uma verificação de consistência em um nó gerenciado, o nó enviará um relatório de status de volta ao servidor de pull. Exiba esses relatórios na página do nó.

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página da conta da Automação, clique em **State Configuration (DSC)** em **Configuração**.
1. Na página State Configuration (DSC),clique na guia **Nós**. Aqui, é possível ver a visão geral do estado da Configuração e os detalhes de cada nó.

   ![Captura de tela da página de Nó](./media/automation-dsc-getting-started/NodesTab.png)

1. Na guia **Nós**, clique no registro do nó para abrir o relatório. Clique no relatório que você quer exibir os detalhes de relatórios adicionais.

   ![Captura de tela da folha Relatório](./media/automation-dsc-getting-started/NodeReport.png)

Na folha de um relatório individual, você pode ver as seguintes informações de status para a verificação de consistência correspondente:

- O status do relatório. Os valores possíveis são:
    * Conformidade - O nó está em conformidade com a verificação.
   * Falha - A configuração não foi aprovada na verificação.
   * Não conformidade - O nó está no modo `ApplyandMonitor` e o computador não está no estado desejado.
- A hora de início para a verificação de consistência.
- O runtime total para a verificação de consistência.
- O tipo de verificação de consistência.
- Quaisquer erros, incluindo o código de erro e a mensagem de erro.
- Todos os recursos do DSC usados na configuração e o estado de cada recurso (se o nó estiver no estado desejado para esse recurso). Você pode clicar em cada recurso para ver informações mais detalhadas sobre ele.
- O nome, o endereço IP e o modo de configuração do nó.

Você também pode clicar em **Exibir relatório bruto** para ver os dados reais que o nó envia para o servidor.
Para obter mais informações sobre como usar esses dados, consulte [Usando um servidor de relatório da DSC](/powershell/scripting/dsc/pull-server/reportserver).

Pode levar algum tempo depois de um nó ser habilitado até que o primeiro relatório esteja disponível. Talvez seja necessário aguardar até 30 minutos para o primeiro relatório após você habilitar um nó.

## <a name="reassign-a-node-to-a-different-node-configuration"></a>Reatribuir um nó a uma configuração de nó diferente

Você pode atribuir um nó para usar uma configuração de nó diferente daquela que inicialmente atribuída.

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página da conta da Automação, clique em **State Configuration (DSC)** em **Configuração**.
1. Na página State Configuration (DSC),clique na guia **Nós**.
1. Na guia **Nós** tab, clique no nome do nó que você deseja atribuir.
1. Na página desse nó, clique em **Atribuir configuração de nó**.

    ![Captura de tela da página de detalhes do Nó, realçando o botão Atribuir configuração de nó](./media/automation-dsc-getting-started/AssignNode.png)

1. Na página Atribuir configuração de nó, selecione a configuração de nó à qual você deseja atribuir o nó e, em seguida, clique em **OK**.

    ![Captura de tela da página Atribuir Configuração de Nó](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregister-a-node"></a>Cancelar o registro de um nó

Se não quiser mais que um nó seja gerenciado pelo State Configuration, você poderá cancelar seu registro.

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página da conta da Automação, clique em **State Configuration (DSC)** em **Configuração**.
1. Na página State Configuration (DSC),clique na guia **Nós**.
1. Na guia **Nós**, clique no nome do nó que você deseja desatribuir.
1. No painel desse nó, clique em **Cancelar registro**.

    ![Captura de tela da página de detalhes do Nó, realçando o botão Cancelar registro](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, consulte [Visão geral da State Configuration da Automação do Azure](automation-dsc-overview.md).
- Para habilitar o recurso para VMs em seu ambiente, consulte [Habilitar o State Configuration da Automação do Azure](automation-dsc-onboarding.md).
- Para obter informações sobre o PowerShell DSC, consulte [Visão geral das configurações de Desired State Configuration do Windows PowerShell](/powershell/scripting/dsc/overview/overview).
- Para obter informações sobre preços, veja [Preços do serviço State Configuration da Automação do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para obter uma referência de cmdlet do PowerShell, confira [Az.Automation](/powershell/module/az.automation).
