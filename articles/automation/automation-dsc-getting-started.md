---
title: "Introdução ao DSC de Automação do Azure | Microsoft Docs"
description: "Explicações e exemplos das tarefas mais comuns no DSC (Configuração de Estado Desejado) de Automação do Azure"
services: automation
documentationcenter: na
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: a3816593-70a3-403b-9a43-d5555fd2cee2
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/21/2016
ms.author: magoedte;eslesar
ms.openlocfilehash: 8a10d961ad7c107c68b57c64ee6c88544ff8832b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-azure-automation-dsc"></a>Introdução ao DSC de Automação do Azure
Este tópico explica como realizar as tarefas mais comuns com o DSC (Configuração de Estado Desejado) de Automação do Azure, como criar, importar e compilar configurações, máquinas de integração para gerenciar e exibir relatórios. Para obter uma visão geral do que o DSC de Automação do Azure é, consulte [Visão geral do DSC da Automação do Azure](automation-dsc-overview.md). Para obter a documentação da DSC, consulte [Visão Geral da Configuração de Estado Desejado do Windows PowerShell](https://msdn.microsoft.com/PowerShell/dsc/overview).

Este tópico fornece um guia passo a passo para usar o DSC de Automação do Azure. Se você quiser um ambiente de exemplo que já esteja configurado sem seguir as etapas descritas neste tópico, poderá usar [o seguinte modelo de ARM](https://github.com/azureautomation/automation-packs/tree/master/102-sample-automation-setup). Esse modelo define um ambiente completo do DSC de Automação do Azure, incluindo uma VM do Azure que é gerenciada pelo DSC de Automação do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir os exemplos neste tópico, são necessários:

* Uma conta de Automação do Azure. Para obter instruções sobre como criar uma conta Executar Como de Automação do Azure, consulte [Conta Executar Como do Azure](automation-sec-configure-azure-runas-account.md).
* Uma VM do Azure Resource Manager (não clássica) executando o Windows Server 2008 R2 ou posterior. Para obter instruções sobre a criação de uma VM, consulte [Criar sua primeira máquina virtual do Windows no portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>Criando uma configuração de DSC
Criaremos uma [configuração de DSC](https://msdn.microsoft.com/powershell/dsc/configurations) simples que garante a presença ou a ausência do WindowsFeature do **Servidor Web** (IIS), dependendo de como os nós são atribuídos.

1. Inicie o ISE do Windows PowerShell (ou qualquer editor de texto).
2. Digite o seguinte texto:
   
    ```powershell
    configuration TestConfig
    {
        Node WebServer
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
3. Salve o arquivo como `TestConfig.ps1`.

Esta configuração chama um recurso em cada bloco de nó, o [recurso WindowsFeature](https://msdn.microsoft.com/powershell/dsc/windowsfeatureresource), que garante a presença ou a ausência da funcionalidade **Servidor Web** .

## <a name="importing-a-configuration-into-azure-automation"></a>Importando uma configuração na Automação do Azure
Em seguida, importaremos a configuração para a conta de Automação.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu Hub, clique em **Todos os recursos** , em seguida, no nome da sua conta de Automação.
3. Na folha **Conta de Automação**, clique em **Configurações da DSC**.
4. Na folha **Configurações da DSC**, clique em **Adicionar uma configuração**.
5. Na folha **Importar Configuração**, navegue até o arquivo `TestConfig.ps1` em seu computador.
   
    ![Captura de tela da folha **Importar Configuração**](./media/automation-dsc-getting-started/AddConfig.png)
6. Clique em **OK**.

## <a name="viewing-a-configuration-in-azure-automation"></a>Exibindo uma configuração na Automação do Azure
Depois de importar uma configuração, você pode vê-la no Portal do Azure.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu Hub, clique em **Todos os recursos** , em seguida, no nome da sua conta de Automação.
3. Na folha **Conta de Automação**, clique em **Configurações da DSC**
4. Na folha **Configurações da DSC**, clique em **TestConfig** (este é o nome da configuração importada no procedimento anterior).
5. Na folha **Configuração do TestConfig**, clique em **Exibir fonte da configuração**.
   
    ![Captura de tela da folha TestConfig configuration (Configuração do TestConfig)](./media/automation-dsc-getting-started/ViewConfigSource.png)
   
    A folha **Fonte da Configuração do TestConfig** é aberta, exibindo o código do PowerShell para a configuração.

## <a name="compiling-a-configuration-in-azure-automation"></a>Compilando uma configuração na Automação do Azure
Antes de aplicar um estado desejado a um nó, uma configuração DSC definindo esse estado deve ser compilada em uma ou mais configurações de nó (documento MOF) e colocada no servidor de pull do DSC de Automação. Para obter uma descrição mais detalhada da compilação das configurações na DSC de Automação do Azure, consulte [Compilando as configurações na DSC de Automação do Azure](automation-dsc-compile.md). Para obter mais informações sobre a compilação das configurações, consulte [Configurações da DSC](https://msdn.microsoft.com/PowerShell/DSC/configurations).

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu Hub, clique em **Todos os recursos** , em seguida, no nome da sua conta de Automação.
3. Na folha **Conta de Automação**, clique em **Configurações da DSC**
4. Na folha **Configurações da DSC**, clique em **TestConfig** (o nome da configuração importada anteriormente).
5. Na folha **Configuração do TestConfig**, clique em **Compilar** e **Sim**. Isso inicia um trabalho de compilação.
   
    ![Captura de tela da folha TestConfig configuration (Configuração do TestConfig) realçando o botão de compilação](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Quando você compila uma configuração na Automação do Azure, ela implanta automaticamente quaisquer MOFs de configuração de nó no servidor de pull.
> 
> 

## <a name="viewing-a-compilation-job"></a>Exibindo um trabalho de compilação
Depois de iniciar uma compilação, você pode vê-la no bloco **Trabalhos de compilação** na folha **Configuração**. O bloco **Trabalhos de compilação** mostra os trabalhos atualmente em execução, concluídos e com falha. Quando você abre uma folha de trabalho de compilação, ela mostra informações sobre esse trabalho, incluindo quaisquer erros ou avisos encontrados, parâmetros de entrada usados na configuração e logs de compilação.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu Hub, clique em **Todos os recursos** , em seguida, no nome da sua conta de Automação.
3. Na folha **Conta de Automação**, clique em **Configurações da DSC**.
4. Na folha **Configurações da DSC**, clique em **TestConfig** (o nome da configuração importada anteriormente).
5. No bloco **Trabalhos de compilação** da folha **Configuração do TestConfig**, clique em qualquer um dos trabalhos listados. Uma folha **Trabalho de Compilação** é aberta, rotulada com a data em que o trabalho de compilação foi iniciado.
   
    ![Captura de tela da folha Trabalho de Compilação](./media/automation-dsc-getting-started/CompilationJob.png)
6. Clique em qualquer bloco na folha **Trabalho de Compilação** para ver mais detalhes sobre o trabalho.

## <a name="viewing-node-configurations"></a>Exibindo configurações de nó
A conclusão com êxito de um trabalho de compilação cria uma ou mais novas configurações de nó. Uma configuração de nó é um documento MOF que é implantado no servidor de pull e está pronto para ser submetido ao pull e aplicado por um ou mais nós. Você pode exibir as configurações do nó em sua conta de Automação na folha **Configurações do Nó DSC** . Uma configuração de nó tem um nome com o formato *NomeConfiguração*.*NomeNó*.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu Hub, clique em **Todos os recursos** , em seguida, no nome da sua conta de Automação.
3. Na folha **Conta de Automação**, clique em **Configurações do Nó DSC**.
   
    ![Captura de tela da folha DSC Node Configurations (Configurações de Nó DSC)](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-dsc"></a>Integrando uma VM do Azure para o gerenciamento com o DSC de Automação do Azure
Você pode usar o DSC de Automação do Azure para gerenciar VMs do Azure (Clássicas e Resource Manager), VMs locais, computadores Linux, VMs AWS e computadores físicos locais. Neste tópico, abordamos como integrar somente VMs do Azure Resource Manager. Para obter informações sobre a integração de outros tipos de computadores, consulte [Integrando computadores para o gerenciamento pela DSC de Automação do Azure](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-dsc"></a>Para integrar uma VM do Azure Resource Manager para o gerenciamento pelo DSC de Automação do Azure
1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu Hub, clique em **Todos os recursos** , em seguida, no nome da sua conta de Automação.
3. Na folha **Conta de Automação**, clique em **Nós DSC**.
4. Na folha **Nós DSC**, clique em **Adicionar VM do Azure**.
   
    ![Captura de tela da folha Nós DSC realçando o botão Adicionar VM do Azure](./media/automation-dsc-getting-started/OnboardVM.png)
5. Na folha **Adicionar VMs do Azure**, clique em **Selecionar máquinas virtuais para integração**.
6. Na folha **Selecionar VMs**, selecione a VM que você deseja integrar e clique em **OK**.
   
   > [!IMPORTANT]
   > Ela deve ser uma VM do Azure Resource Manager executando o Windows Server 2008 R2 ou posterior.
   > 
   > 
7. Na folha **Adicionar VMs do Azure**, clique em **Configurar dados de registro**.
8. Na folha **Registro**, insira o nome da configuração do nó que você deseja aplicar à VM na caixa **Nome de Configuração do Nó**. Isso deve corresponder exatamente ao nome de uma configuração de nó na conta de Automação. Fornecer um nome neste ponto é opcional. Você pode alterar a configuração de nó atribuída após integrar o nó.
   Marque **Reinicializar o Nó se Necessário** e clique em **OK**.
   
    ![Captura de tela da folha Registro](./media/automation-dsc-getting-started/RegisterVM.png)
   
    A configuração do nó especificada será aplicada à VM em intervalos especificados pela **Frequência do Modo de Configuração** e a VM verificará se há atualizações para a configuração do nó em intervalos especificados pela **Frequência de Atualização**. Para obter mais informações sobre como esses valores são usados, consulte [Configurando o Gerenciador de Configuração Local](https://msdn.microsoft.com/PowerShell/DSC/metaConfig).
9. Na folha **Adicionar VMs do Azure**, clique em **Criar**.

O Azure iniciará o processo de integração da VM. Quando for concluída, a VM será exibida na folha **Nós DSC** na conta de Automação.

## <a name="viewing-the-list-of-dsc-nodes"></a>Exibindo a lista de nós DSC
Você pode exibir a lista de todos os computadores que foram integrados para gerenciamento em sua conta de Automação na folha **Nós DSC** .

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu Hub, clique em **Todos os recursos** , em seguida, no nome da sua conta de Automação.
3. Na folha **Conta de Automação**, clique em **Nós DSC**.

## <a name="viewing-reports-for-dsc-nodes"></a>Exibindo relatórios para nós DSC
Cada vez que o DSC de Automação do Azure executa uma verificação de consistência em um nó gerenciado, o nó envia um relatório de status para o servidor de pull. Você pode exibir esses relatórios na folha do nó.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu Hub, clique em **Todos os recursos** , em seguida, no nome da sua conta de Automação.
3. Na folha **Conta de Automação**, clique em **Nós DSC**.
4. No bloco **Relatórios** , clique em qualquer um dos relatórios na lista.
   
    ![Captura de tela da folha Relatório](./media/automation-dsc-getting-started/NodeReport.png)

Na folha de um relatório individual, você pode ver as seguintes informações de status para a verificação de consistência correspondente:

* Status do relatório — se o nó é "Compatível", a configuração está "Com Falha" ou o nó "Não é Compatível" (quando o nó está no modo **applyandmonitor** e o computador não está no estado desejado).
* A hora de início para a verificação de consistência.
* O tempo de execução total para a verificação de consistência.
* O tipo de verificação de consistência.
* Quaisquer erros, incluindo o código de erro e a mensagem de erro. 
* Todos os recursos DSC usados na configuração e o estado de cada recurso (se o nó está no estado desejado para esse recurso) — você pode clicar em cada recurso para obter informações mais detalhadas para esse recurso.
* O nome, o endereço IP e o modo de configuração do nó.

Você também pode clicar em **Exibir relatório bruto** para ver os dados reais que o nó envia para o servidor. Para obter mais informações sobre como usar esses dados, consulte [Usando um servidor de relatório da DSC](https://msdn.microsoft.com/powershell/dsc/reportserver).

Pode levar algum tempo depois de um nó ser integrado até que o primeiro relatório esteja disponível. Talvez seja necessário aguardar até 30 minutos para o primeiro relatório após você integrar um nó.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Reatribuindo um nó a uma configuração de nó diferente
Você pode atribuir um nó para usar uma configuração de nó diferente daquela que inicialmente atribuída.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu Hub, clique em **Todos os recursos** , em seguida, no nome da sua conta de Automação.
3. Na folha **Conta de Automação**, clique em **Nós DSC**.
4. Na folha **Nós DSC** , clique no nome do nó que você deseja reatribuir.
5. Na folha do nó, clique em **Atribuir nó**.
   
    ![Captura de tela da folha Nó realçando o botão Assign Node (Atribuir Nó)](./media/automation-dsc-getting-started/AssignNode.png)
6. Na folha **Atribuir Configuração de Nó**, selecione a configuração de nó à qual você deseja atribuir o nó, em seguida, clique em **OK**.
   
    ![Captura de tela da folha Atribuir Configuração de Nó](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Cancelando o registro de um nó
Se você não desejar mais que um nó seja gerenciado pelo DSC de Automação do Azure, você poderá cancelar o registro dele.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu Hub, clique em **Todos os recursos** , em seguida, no nome da sua conta de Automação.
3. Na folha **Conta de Automação**, clique em **Nós DSC**.
4. Na folha **Nós DSC** , clique no nome do nó cujo registro você deseja cancelar.
5. Na folha desse nó, clique em **Cancelar Registro**.
   
    ![Captura de tela da folha Nó realçando o botão Cancelar Registro](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Artigos relacionados
* [Visão geral do DSC de Automação do Azure](automation-dsc-overview.md)
* [Máquinas de integração para o gerenciamento pelo DSC de Automação do Azure](automation-dsc-onboarding.md)
* [Visão Geral da Configuração de Estado Desejado do Windows PowerShell](https://msdn.microsoft.com/powershell/dsc/overview)
* [cmdlets da DSC de Automação do Azure](/powershell/module/azurerm.automation/#automation)
* [preço da DSC de Automação do Azure](https://azure.microsoft.com/pricing/details/automation/)

