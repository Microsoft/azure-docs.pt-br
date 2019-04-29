---
title: Como agendar o Azure-SSIS Integration Runtime | Microsoft Docs
description: Este artigo descreve como agendar o início e o término do Azure-SSIS Integration Runtime usando o Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 1/9/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 54d7979f9fbe23e9372aa2702b46e42ca64496d2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60522509"
---
# <a name="how-to-start-and-stop-azure-ssis-integration-runtime-on-a-schedule"></a>Como iniciar e parar o Azure-SSIS Integration Runtime em um agendamento
Este artigo descreve como agendar o início e o término do Azure-SSIS IR (Integration Runtime) usando o ADF (Azure Data Factory). O Azure-SSIS IR é o recurso de computação do ADF dedicado à execução de pacotes do SSIS (SQL Server Integration Services). Executar o Azure-SSIS IR tem um custo associado. Portanto, você geralmente deseja executar o IR somente quando precisa executar pacotes do SSIS no Azure e parar o IR quando não precisar mais dele. Você pode usar o aplicativo/interface do usuário do ADF ou o Azure PowerShell para [iniciar ou parar manualmente o IR](manage-azure-ssis-integration-runtime.md)).

Como alternativa, você pode criar atividades da Web em pipelines do ADF para iniciar/parar o IR conforme um agendamento, por exemplo, começando na manhã antes da execução de suas cargas de trabalho de ETL diárias e parando à tarde após a conclusão.  Também é possível encadear uma atividade de Executar Pacote do SSIS entre duas atividades Web que iniciam e param seu IR, portanto, o IR vai iniciar/parar sob demanda, logo antes/depois da execução do pacote. Para obter mais informações sobre a atividade de Executar Pacote do SSIS, veja o artigo [Executar um pacote do SSIS usando a atividade de Executar Pacote do SSIS no pipeline do ADF](how-to-invoke-ssis-package-ssis-activity.md).

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="prerequisites"></a>Pré-requisitos
Se você ainda não tiver provisionado o Azure-SSIS IR, provisione-o seguindo as instruções no [tutorial](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="create-and-schedule-adf-pipelines-that-start-and-or-stop-azure-ssis-ir"></a>Criar e agendar pipelines do ADF que iniciam e/ou param o Azure-SSIS IR
Esta seção mostra como usar atividades da Web em pipelines do ADF para iniciar/parar o Azure-SSIS IR conforme um agendamento ou iniciar/parar esse recurso sob demanda. Vamos conduzi-lo na criação de três pipelines: 

1. O primeiro pipeline contém uma atividade da Web que inicia o Azure-SSIS IR. 
2. O segundo pipeline contém uma atividade da Web que para o Azure-SSIS IR.
3. O terceiro pipeline contém uma atividade Executar Pacote do SSIS encadeada entre duas atividades Web que iniciam/param o Azure-SSIS IR. 

Depois de criar e testar esses pipelines, você pode criar um gatilho de agendamento e associá-lo a qualquer pipeline. O gatilho de agendamento define uma agenda para executar o pipeline associado. 

Por exemplo, você pode criar dois gatilhos, o primeiro deles agendado para ser executado diariamente às 6h e associado ao primeiro pipeline, e o segundo agendado para ser executado diariamente às 18h e associado ao segundo pipeline.  Dessa forma, você tem um período das 6h às 18h diariamente em que o IR está em execução, pronto para executar suas cargas de trabalho ETL diárias.  

Se você criar um terceiro gatilho agendado para ser executado diariamente à meia-noite e associado ao terceiro pipeline, esse pipeline será executado todos os dias à meia-noite, iniciando o IR logo antes da execução de pacote, posteriormente executando o seu pacote e imediatamente parando o IR logo após a execução do pacote, portanto, o IR não será executado de modo ocioso.

### <a name="create-your-adf"></a>Criar seu ADF

1. Entre no [Portal do Azure](https://portal.azure.com/).    
2. Clique em **Novo** no menu à esquerda, clique em **Dados + Análise** e clique em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
   
3. Na página **Novo data factory**, insira **MyAzureSsisDataFactory** para **Nome**. 
      
   ![Página de novo data factory](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   O nome do ADF deve ser globalmente exclusivo. Se você receber o erro a seguir, altere o nome do ADF (por exemplo, yournameMyAzureSsisDataFactory) e tente criá-lo novamente. Veja o artigo [Data Factory – Regras de Nomenclatura](naming-rules.md) para saber mais sobre as regras de nomenclatura para artefatos do ADF.
  
   `Data factory name MyAzureSsisDataFactory is not available`
      
4. Selecione sua **Assinatura** do Azure sob a qual você deseja criar o ADF. 
5. Para **Grupo de Recursos**, realize uma das seguintes etapas:
     
   - Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa. 
   - Selecione **Criar novo**e insira o nome do novo grupo de recursos.   
         
   Para saber mais sobre grupos de recursos, veja o artigo [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).
   
6. Para **Versão**, selecione **V2**.
7. Para **Local**, selecione um dos locais compatíveis para a criação de ADF na lista suspensa.
8. Selecione **Fixar no painel**.     
9. Clique em **Criar**.
10. No painel do Azure, você verá o seguinte bloco com status: **Implantando Data Factory**. 

    ![implantando bloco data factory](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
   
11. Após a criação ter sido concluída, você poderá ver sua página do ADF conforme mostrado abaixo.
   
    ![Página inicial do data factory](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
   
12. Clique em **Criar e Monitorar** para iniciar a interface do usuário do AAD/aplicativo em uma guia separada.

### <a name="create-your-pipelines"></a>Crie seus pipelines

1. Na página **Introdução**, selecione **Criar pipeline**. 

   ![Página Introdução](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
   
2. Na caixa de ferramentas **Atividades**, expanda o menu **Geral** e arraste e solte uma atividade da **Web** na superfície do designer de pipeline. Na guia **Geral** da janela Propriedades da atividade, altere o nome da atividade para **startMyIR**. Alterne para a guia **Configurações** e realize as seguintes ações.

    1. Para **URL**, insira a seguinte URL para a API REST que inicia o Azure-SSIS IR, substituindo `{subscriptionId}`, `{resourceGroupName}`, `{factoryName}` e `{integrationRuntimeName}` pelos valores reais para o IR: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/start?api-version=2018-06-01` Como alternativa, você também pode copiar e colar a ID de recurso do seu IR da página de monitoramento no aplicativo/interface do usuário do AAD para substituir a seguinte parte do URL acima: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}`
    
       ![ID do Recurso de SSIS IR do ADF](./media/how-to-schedule-azure-ssis-integration-runtime/adf-ssis-ir-resource-id.png)
  
    2. Para **Método**, selecione **POSTAR**. 
    3. Para **Corpo**, insira `{"message":"Start my IR"}`. 
    4. Para **autenticação**, selecione **MSI** para usar a identidade gerenciada do ADF, consulte [identidade gerenciada para o Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) artigo para obter mais informações.
    5. Para **Recurso**, insira `https://management.azure.com/`.
    
       ![SSIS IR de Agendamento da Atividade Web do ADF](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-schedule-ssis-ir.png)
  
3. Clone o primeiro pipeline para criar um segundo, alterando o nome da atividade para **stopMyIR** e substituindo as propriedades a seguir.

    1. Para **URL**, insira a seguinte URL para a API REST que interrompe o Azure-SSIS IR, substituindo `{subscriptionId}`, `{resourceGroupName}`, `{factoryName}` e `{integrationRuntimeName}` pelos valores reais para o IR: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/stop?api-version=2018-06-01`
    
    2. Para **Corpo**, insira `{"message":"Stop my IR"}`. 

4. Crie um terceiro pipeline, arraste e solte uma atividade **Executar Pacote do SSIS** da caixa de ferramentas **Atividades** para a superfície do designer de pipeline e configure-a seguindo as instruções no artigo [Invocar um pacote SSIS usando a atividade Executar Pacote SSIS no ADF](how-to-invoke-ssis-package-ssis-activity.md).  Como alternativa, você pode usar uma atividade de **Procedimento Armazenado** em vez disso e configurá-la seguindo as instruções no artigo [Invocar um pacote SSIS usando a atividade de Procedimento Armazenado no ADF](how-to-invoke-ssis-package-stored-procedure-activity.md).  Em seguida, encadeie a atividade Executar Pacote SSIS/Procedimento Armazenado entre duas atividades Web que iniciam/param seu IR, semelhante às essas atividades da Web no primeiro/segundo pipeline.

   ![SSIS IR Sob Demanda da Atividade da Web do ADF](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-on-demand-ssis-ir.png)

5. Atribua à identidade gerenciada para seu ADF uma função de **Colaborador** de si mesma para que as atividades da Web em seus pipelines possam chamar a API REST para iniciar/parar Azure-SSIS IRs provisionados nele.  Na página do ADF no portal do Azure, clique em **Controle de acesso (IAM)**, clique em **+ Adicionar atribuição de função** e, em seguida, na folha **Adicionar atribuição de função**, realize as seguintes ações.

    1. Para **Função**, selecione **Colaborador**. 
    2. Para **Atribuir acesso a**, selecione **Usuário, grupo ou entidade de serviço do Azure AD**. 
    3. Para **Selecionar**, pesquise o nome do ADF e selecione-o. 
    4. Clique em **Salvar**.
    
   ![Atribuição de Função de Identidade Gerenciada do ADF](./media/how-to-schedule-azure-ssis-integration-runtime/adf-managed-identity-role-assignment.png)

6. Valide seu ADF e todas as configurações de pipeline clicando em **Validar tudo/Validar** na barra de ferramentas do pipeline/factory. Feche **Saída de Validação do Pipeline/Factory** clicando no botão **>>**.  

   ![Validar o pipeline](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-your-pipelines"></a>Realizar uma execução de teste de seus pipelines

1. Selecione **Execução de Teste** na barra de ferramentas para cada pipeline e veja a janela de **Saída** no painel inferior. 

   ![Execução de teste](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
    
2. Para testar o terceiro pipeline, inicie o SSMS (SQL Server Management Studio). Na janela **Conectar ao Servidor**, execute as seguintes ações. 

    1. Para **Nome do servidor**, insira **&lt;nome do servidor do Banco de Dados SQL do Azure&gt;.database.windows.net**.
    2. Selecione **Opções >>**.
    3. Para **Conectar um banco de dados**, selecione **SSISDB**.
    4. Selecione **Conectar**. 
    5. Expanda **Catálogo do Integration Services** -> **SSISDB** -> Sua pasta -> **Projetos** -> Seu projeto SSIS -> **Pacotes**. 
    6. Clique com o botão direito do mouse no pacote do SSIS especificado para executar e selecione **Relatórios** -> **Relatórios Padrão** -> **Todas as Execuções**. 
    7. Verifique se que ele foi executado. 

   ![Verificar a execução do pacote SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png)

### <a name="schedule-your-pipelines"></a>Agendar seus pipelines

Agora que os pipelines funcionam conforme o esperado, você pode criar gatilhos para executá-los em cadências especificadas. Para obter detalhes sobre como associar gatilhos de pipelines, veja o artigo [Disparar o pipeline conforme uma agenda](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

1. Na barra de ferramentas do pipeline, selecione **Disparar** e selecione **Novo/Editar**. 

   ![Disparar -> Novo/Editar](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)

2. No painel **Adicionar Gatilhos**, selecione **+ Novo**.

   ![Adicionar Gatilhos - Novo](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)

3. No painel **Novo Gatilho**, execute as seguintes ações: 

    1. Para **Nome**, insira um nome para o gatilho. No exemplo a seguir, **Executar diariamente** é o nome do gatilho. 
    2. Para **Tipo**, selecione **Agendamento**. 
    3. Para **Data de início (UTC)**, insira uma data e hora de início em UTC. 
    4. Para **Recorrência**, insira uma cadência do gatilho. No exemplo a seguir, é uma vez **Diariamente**. 
    5. Para **Término**, selecione **Sem Término** ou digite uma data e hora de término depois de selecionar **Na Data**. 
    6. Selecione **Ativado** para ativar o gatilho imediatamente depois de publicar todas as configurações do ADF. 
    7. Selecione **Avançar**.

   ![Disparar -> Novo/Editar](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
    
4. Na página **Parâmetros Execução de Gatilho**, examine qualquer aviso e selecione **Concluir**. 
5. Publique todas as configurações do ADF selecionando **Publicar Tudo** na barra de ferramentas do alocador. 

   ![Publicar Tudo](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-your-pipelines-and-triggers-in-azure-portal"></a>Monitorar seus pipelines e gatilhos no portal do Azure

1. Para monitorar execuções do gatilho e execuções do pipeline, use a guia **Monitorar** à esquerda do aplicativo/interface do usuário do ADF. Para obter etapas detalhadas, veja o artigo [Monitorar o pipeline](quickstart-create-data-factory-portal.md#monitor-the-pipeline).

   ![Execuções de pipeline](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)

2. Para exibir as atividades executadas associadas a uma execução do pipeline, selecione o primeiro link (**Exibir as Execuções da atividade**) na coluna **Ações**. Para o terceiro pipeline, você verá três execuções de atividade, uma para cada atividade encadeada no pipeline (atividade da Web para iniciar seu IR, atividade de Procedimento Armazenado para executar seu pacote e atividade da Web para interromper seu IR). Para exibir as execuções do pipeline novamente, selecione o link **Pipelines** na parte superior.

   ![Execuções de atividade](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)

3. Para exibir as execuções do gatilho, selecione **Execuções de Gatilho** na lista suspensa em **Execuções de Pipeline** na parte superior. 

   ![Execuções de gatilho](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-your-pipelines-and-triggers-with-powershell"></a>Monitorar seus pipelines e gatilhos com o PowerShell

Use scripts como os exemplos a seguir para monitorar seus pipelines e gatilhos.

1. Obtenha o status de uma execução de pipeline.

   ```powershell
   Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
   ```

2. Obter informações sobre um gatilho.

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
   ```

3. Obtenha o status de uma execução do gatilho.

   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
   ```

## <a name="create-and-schedule-azure-automation-runbook-that-startsstops-azure-ssis-ir"></a>Criar e agendar um runbook de Automação do Azure que inicie/pare o Azure-SSIS IR

Nesta seção, você aprenderá a criar runbook de Automação do Azure que executa o script do PowerShell, iniciando/parando o Azure-SSIS IR conforme um agendamento.  Isso é útil quando você quer executar scripts adicionais antes/depois de iniciar/parar o IR para pré/pós-processamento.

### <a name="create-your-azure-automation-account"></a>Criar sua conta da Automação do Azure

Se você ainda não tiver uma conta de Automação do Azure, crie uma seguindo as instruções nesta etapa. Para obter as etapas detalhadas, veja o artigo [Criar uma conta de Automação do Azure](../automation/automation-quickstart-create-account.md). Como parte dessa etapa, crie uma conta **Executar como do Azure** (uma entidade de serviço no seu Azure Active Directory) e atribua-a à função **Colaborador** da sua assinatura do Azure. Verifique se essa é a mesma assinatura que contém seu ADF com o Azure SSIS IR. A Automação do Azure usará essa conta para autenticar-se no Azure Resource Manager e operar em seus recursos. 

1. Iniciar o navegador da Web **Microsoft Edge** ou **Google Chrome**. Atualmente, o aplicativo/interface do usuário do ADF só tem suporte no Microsoft Edge e em navegadores da Web Google Chrome.
2. Entre no [Portal do Azure](https://portal.azure.com/).    
3. Selecione **Novo** no menu esquerdo, selecione **Monitoramento + Gerenciamento** e selecione **Automação**. 

   ![Novo -> Monitoramento + Gerenciamento -> Automação](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
    
2. No painel **Adicionar Conta de Automação**, execute as seguintes ações.

    1. Para **Nome**, insira um nome para sua conta de Automação do Azure. 
    2. Para **Assinatura**, selecione a assinatura que tenha o ADF com o Azure-SSIS IR. 
    3. Para **Grupo de recursos**, selecione **Criar novo** para criar um novo grupo de recursos ou **Usar existente** para selecionar um existente. 
    4. Para **Local**, selecione um local para sua conta de Automação do Azure. 
    5. Confirme **Criar Azure conta Executar Como** com **Sim**. Uma entidade de serviço será criada no Azure Active Directory e receberá uma função de **Colaborador** em sua assinatura do Azure.
    6. Selecione **Fixar no painel** para exibi-lo permanentemente no painel do Azure. 
    7. Selecione **Criar**. 

   ![Novo -> Monitoramento + Gerenciamento -> Automação](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
   
3. Você verá o status da implantação da sua conta de Automação do Azure em notificações e no painel do Azure. 
    
   ![Implantar automação](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
    
4. Você verá a home page da sua conta de Automação do Azure depois que ela tiver sido criada com sucesso. 

   ![Home page da Automação do Azure](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-adf-modules"></a>Importar módulos do ADF

1. Selecione **Módulos** na seção **SHARED RESOURCES** no menu esquerdo e verifique se você tem **AzureRM.DataFactoryV2** + **AzureRM.Profile** na lista de módulos.

   ![Verificar os módulos necessários](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Se você não tiver **AzureRM.DataFactoryV2**, vá para a Galeria do PowerShell para o [módulo AzureRM.DataFactoryV2](https://www.powershellgallery.com/packages/AzureRM.DataFactoryV2/), selecione **Implantar na Automação do Azure**, selecione sua conta de Automação do Azure e então selecione **OK**. Volte a exibir os **Módulos** na seção **SHARED RESOURCES** no menu à esquerda e aguarde até que você veja o **STATUS** do módulo **AzureRM.DataFactoryV2** alterado para **Disponível**.

    ![Verifique o módulo do Data Factory](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Se você não tiver **AzureRM.Profile**, vá para a Galeria do PowerShell para o [módulo AzureRM.Profile](https://www.powershellgallery.com/packages/AzureRM.profile/), selecione **Implantar na Automação do Azure**, selecione sua conta de Automação do Azure e então selecione **OK**. Volte a exibir os **Módulos** na seção **SHARED RESOURCES** no menu à esquerda e aguarde até que você veja o **STATUS** do módulo **AzureRM.Profile** alterado para **Disponível**.

    ![Verifique o módulo de perfil](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-your-powershell-runbook"></a>Criar seu runbook do PowerShell

A seção a seguir fornece as etapas para criar um runbook do PowerShell. O script associado ao runbook inicia/para um Azure-SSIS IR baseado no comando que você especificou para o parâmetro **OPERATION**. Esta seção não fornece todos os detalhes para criar um runbook. Para obter mais informações, consulte o artigo [Criar um runbook](../automation/automation-quickstart-create-runbook.md).

1. Alterne para a guia **Runbooks** e selecione **+ Adicionar um runbook** na barra de ferramentas. 

   ![Botão Adicionar um runbook](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
   
2. Selecione **Criar um novo runbook** e realize as seguintes ações: 

    1. Para **Nome**, insira **StartStopAzureSsisRuntime**.
    2. Para **Tipo de runbook**, selecione **PowerShell**.
    3. Selecione **Criar**.
    
   ![Botão Adicionar um runbook](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
   
3. Copie e cole o seguinte script do PowerShell para sua janela de script de runbook. Salve e, em seguida, publique seu runbook usando os botões **Salvar** e **Publicar** na barra de ferramentas. 

    ```powershell
    Param
    (
          [Parameter (Mandatory= $true)]
          [String] $ResourceGroupName,
    
          [Parameter (Mandatory= $true)]
          [String] $DataFactoryName,
    
          [Parameter (Mandatory= $true)]
          [String] $AzureSSISName,
    
          [Parameter (Mandatory= $true)]
          [String] $Operation
    )
    
    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
    
        "Logging in to Azure..."
        Connect-AzAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
            $ErrorMessage = "Connection $connectionName not found."
            throw $ErrorMessage
        } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
        }
    }
    
    if($Operation -eq "START" -or $operation -eq "start")
    {
        "##### Starting #####"
        Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

   ![Editar runbook do PowerShell](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
    
4. Teste seu runbook selecionando o botão **Iniciar** na barra de ferramentas. 

   ![Botão Iniciar runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
    
5. Na **iniciar Runbook** painel, execute as seguintes ações: 

    1. Para **RESOURCE GROUP NAME**, insira o nome do grupo de recursos que tem seu ADF com Azure-SSIS IR. 
    2. Para **DATA FACTORY NAME**, insira o nome do seu ADF com Azure-SSIS IR. 
    3. Para **AZURESSISNAME**, insira o nome do Azure-SSIS IR. 
    4. Para **OPERAÇÃO**, insira **INICIAR**. 
    5. Selecione **OK**.  

   ![Janela Iniciar runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
   
6. Na janela de trabalho, selecione o bloco **Saída**. Na janela de saída, aguarde a mensagem **##### Concluído #####** depois de ver **##### Iniciando #####**. Iniciar o Azure-SSIS IR leva aproximadamente 20 minutos. Feche a janela **Trabalho** e volte à janela **Runbook**.

   ![IR do Azure-SSIS - iniciado](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
    
7. Repita as duas etapas anteriores usando **STOP** como o valor para a **OPERATION**. Inicie seu runbook outra vez selecionando o botão **Iniciar** na barra de ferramentas. Insira os nomes de grupo de recursos, ADF e Azure-SSIS IR. Para **OPERAÇÃO**, insira **PARAR**. Na janela de saída, aguarde a mensagem **##### Concluído #####** depois de ver **##### Parando #####**. Parar o Azure-SSIS IR não leva tanto tempo quanto iniciá-lo. Feche a janela **Trabalho** e volte à janela **Runbook**.

## <a name="create-schedules-for-your-runbook-to-startstop-azure-ssis-ir"></a>Criar agendas para seu runbook para iniciar/parar o Azure-SSIS IR

Na seção anterior, você criou um runbook de Automação do Azure que pode iniciar ou parar o Azure-SSIS IR. Nesta seção, você criará dois agendamentos para seu runbook. Ao configurar o primeiro agendamento, você especifica **START** para **OPERATION**. Da mesma forma, ao configurar o segundo, especifique **STOP** para **OPERATION**. Para obter etapas detalhadas criar agendas, veja o artigo [Criar uma agenda](../automation/shared-resources/schedules.md#creating-a-schedule).

1. Na janela **Runbook**, selecione **Agendamentos**, e selecione **+ Adicionar um agendamento** na barra de ferramentas. 

   ![IR do Azure-SSIS - iniciado](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
   
2. No painel **Agendar Runbook**, execute as seguintes ações: 

    1. Selecione **Vincular um agendamento ao runbook**. 
    2. Selecione **Criar um novo agendamento**.
    3. No painel **Nova Agenda**, insira **Iniciar IR diariamente** para **Nome**. 
    4. Para **Inícios**, digite uma hora que seja alguns minutos após a hora atual. 
    5. Para **Recorrência**, selecione **Recorrente**. 
    6. Para **Repetir a cada**, insira **1** e selecione **Dia**. 
    7. Selecione **Criar**. 

   ![Agendar para o início do IR do Azure-SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
    
3. Alterne para a guia **Parâmetros e configurações de execução**. Especifique os nomes de grupo de recursos, ADF e Azure-SSIS IR. Para **OPERATION**, insira **START** e selecione **OK**. Selecione **OK** novamente para visualizar o agendamento na página **Agendamentos** de seu runbook. 

   ![Agendar para iniciar o IR do Azure-SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
    
4. Repita as duas etapas anteriores para criar um agendamento chamado **Parar IR diariamente**. Insira uma hora que seja pelo menos 30 minutos após a hora especificada para o agendamento **Iniciar IR diariamente**. Para **OPERATION**, insira **STOP** e selecione **OK**. Selecione **OK** novamente para visualizar o agendamento na página **Agendamentos** de seu runbook. 

5. Na janela **Runbook**, selecione **Trabalhos** no menu esquerdo. Você deve visualizar os trabalhos criados pelos agendamentos nos horários especificados e os status. Você pode ver os detalhes do trabalho, como sua saída, de modo semelhante ao que você viu depois de testar o runbook. 

   ![Agendar para iniciar o IR do Azure-SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
    
6. Depois de concluir o teste, desabilite as agendas editando-as. Selecione **Agendamentos** no menu esquerdo, selecione **Iniciar IR diariamente/Parar IR diariamente** e selecione **Não** para **Habilitado**. 

## <a name="next-steps"></a>Próximas etapas
Consulte a postagem blog a seguir:
-   [Modernizar e estender seus fluxos de trabalho ETL/ELT com atividades do SSIS nos pipelines do ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)

Consulte os artigos a seguir na documentação do SSIS: 

- [Implantar, executar e monitorar um pacote do SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Conecte-se ao catálogo do SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Agendar a execução do pacote no Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Conecte-se a fontes de dados locais com a autenticação do Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)
