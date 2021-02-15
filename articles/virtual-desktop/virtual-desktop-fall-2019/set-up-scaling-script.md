---
title: Dimensionar sessão hospeda área de trabalho virtual do Windows da automação do Azure (clássico) – Azure
description: Como dimensionar automaticamente hosts de sessão da área de trabalho virtual (clássica) do Windows com a automação do Azure.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fd14af6c95654708f339f4a68cd333d0e3162553
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89078173"
---
# <a name="scale-windows-virtual-desktop-classic-session-hosts-using-azure-automation"></a>Dimensionar hosts de sessão da área de trabalho virtual (clássica) do Windows usando a automação do Azure

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager.

É possível reduzir o custo total de implantação da Área de Trabalho Virtual do Windows dimensionando suas VMs (máquinas virtuais). Isso significa desligar e desalocar VMs de host da sessão fora do horário de pico de uso, voltar a ligá-las e realocá-las durante os horários de pico.

Neste artigo, você aprenderá sobre a ferramenta de dimensionamento criada com a conta de automação do Azure e o aplicativo lógico do Azure que dimensiona automaticamente as VMs do host de sessão no seu ambiente de área de trabalho virtual do Windows. Para ver como usar a ferramenta de dimensionamento, pule para [Pré-requisitos](#prerequisites).

## <a name="how-the-scaling-tool-works"></a>Como funciona a ferramenta de dimensionamento

A ferramenta de dimensionamento fornece uma opção de automação de baixo custo para os clientes que desejam otimizar os custos de VM dos hosts da sessão.

A ferramenta de dimensionamento pode ser usada para:

- Agendar o início e o término das VMs com base nos horários comerciais de pico e fora de pico.
- Distribuir as VMs com base no número de sessões por núcleo de CPU.
- Dimensionar as VMs fora dos horários de pico, deixando o mínimo de VMs de host da sessão em execução.

A ferramenta de dimensionamento usa uma combinação de uma conta de automação do Azure, um runbook do PowerShell, um webhook e o aplicativo lógico do Azure para funcionar. Quando a ferramenta é executada, o aplicativo lógico do Azure chama um webhook para iniciar o runbook de automação do Azure. O runbook então cria um trabalho.

Durante o período de uso de pico, o trabalho verifica o número atual de sessões e a capacidade da VM do host da sessão em execução atual para cada pool de hosts. Ele usa essas informações para calcular se as VMs do host da sessão em execução podem dar suporte a sessões existentes com base no parâmetro *SessionThresholdPerCPU* definido para o arquivo de **CreateOrUpdateAzLogicApp.ps1** . Se as VMs de host da sessão não derem conta das sessões existentes, o trabalho iniciará VMs de host da sessão adicionais no pool de hosts.

>[!NOTE]
>*SessionThresholdPerCPU* não restringe o número de sessões na VM. Esse parâmetro determina apenas quando novas VMs precisam ser iniciadas para equilibrar a carga das conexões. Para restringir o número de sessões, siga as instruções [Set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) para configurar adequadamente o parâmetro *MaxSessionLimit*.

Durante o tempo de uso fora do horário de pico, o trabalho determina quantas VMs do host de sessão devem ser desligadas com base no parâmetro *MinimumNumberOfRDSH* . Se você definir o parâmetro *LimitSecondsToForceLogOffUser* para um valor positivo diferente de zero, o trabalho definirá as VMs do host da sessão como o modo de descarga para impedir que novas sessões se conectem aos hosts. O trabalho, em seguida, notificará qualquer usuário conectado no momento para salvar seu trabalho, aguardará o período de tempo configurado e forçará os usuários a se desconectarem. Depois que todas as sessões de usuário na VM host de sessão forem desconectadas, o trabalho desligará a VM. Depois que a VM for desligada, o trabalho redefinirá seu modo de drenagem de host de sessão.

>[!NOTE]
>Se você definir manualmente a VM do host da sessão para o modo de descarga, o trabalho não gerenciará a VM do host da sessão. Se a VM host de sessão estiver em execução e definida como modo de descarga, ela será tratada como indisponível, o que fará com que o trabalho inicie VMs adicionais para lidar com a carga. Recomendamos que você marque todas as VMs do Azure antes de defini-las manualmente para o modo de descarga. Você pode nomear a marca com o parâmetro *MaintenanceTagName* ao criar o Agendador de aplicativos lógicos do Azure mais tarde. As marcas ajudarão você a distinguir essas VMs das gerenciadas pela ferramenta de dimensionamento. Definir a marca de manutenção também impede que a ferramenta de dimensionamento faça alterações na VM até que você remova a marca.

Se você definir o parâmetro *LimitSecondsToForceLogOffUser* como zero, o trabalho permitirá que a definição de configuração de sessão em políticas de grupo especificadas manipule a assinatura de sessões de usuário. Para ver essas políticas de grupo, vá para políticas de **configuração do computador**  >  **Policies**  >  **modelos administrativos**  >  **componentes do Windows**  >  **serviços de área de trabalho remota**  >  **host da sessão da área de trabalho remota**  >  **limites de tempo de sessão**. Se houver sessões ativas em uma VM de host da sessão, o trabalho deixará a VM de host da sessão em execução. Se não houver nenhuma sessão ativa, o trabalho desligará a VM do host da sessão.

Durante qualquer momento, o trabalho também usa o *MaxSessionLimit* do pool de hosts em conta para determinar se o número atual de sessões é mais de 90% da capacidade máxima. Se for, o trabalho iniciará VMs de host de sessão adicionais.

O trabalho é executado periodicamente com base em um intervalo de recorrência definido. Você pode alterar esse intervalo com base no tamanho do seu ambiente de área de trabalho virtual do Windows, mas lembre-se de que iniciar e desligar VMs pode levar algum tempo, portanto, lembre-se de considerar o atraso. É recomendável definir o intervalo de recorrência para ocorrer a cada 15 minutos.

No entanto, a ferramenta também tem as seguintes limitações:

- Essa solução se aplica somente a VMs de host de sessão de várias sessões em pool.
- Essa solução gerencia as VMs em qualquer região, mas só pode ser usada na mesma assinatura que a sua conta de automação do Azure e o aplicativo lógico do Azure.
- O tempo de execução máximo de um trabalho no runbook é de 3 horas. Se iniciar ou parar as VMs no pool de hosts demorar mais do que isso, o trabalho falhará. Para obter mais detalhes, consulte [recursos compartilhados](../../automation/automation-runbook-execution.md#fair-share).

>[!NOTE]
>A ferramenta de dimensionamento controla o modo de balanceamento de carga do pool de hosts que está sendo dimensionado no momento. A ferramenta usa o modo de balanceamento de carga em primeiro lugar para os horários de pico e fora do horário de pico.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a configurar a ferramenta de dimensionamento, verifique se estas opções estão prontas:

- Um [locatário e pool de host da Área de Trabalho Virtual do Windows](create-host-pools-arm-template.md)
- VMs de pool de hosts da sessão configuradas e registradas no serviço da Área de Trabalho Virtual do Windows
- Um usuário com [acesso de Colaborador](../../role-based-access-control/role-assignments-portal.md) na assinatura do Azure

O computador que você usar para implantar a ferramenta deverá ter:

- O Windows PowerShell 5.1 ou posterior
- O módulo do PowerShell Microsoft AZ

Se estiver tudo pronto, vamos começar.

## <a name="create-or-update-an-azure-automation-account"></a>Criar ou atualizar uma conta de automação do Azure

>[!NOTE]
>Se você já tiver uma conta de automação do Azure com um runbook executando uma versão mais antiga do script de dimensionamento, tudo o que você precisa fazer é seguir as instruções abaixo para verificar se ele está atualizado.

Primeiro, será preciso uma conta de Automação do Azure para executar o runbook do PowerShell. O processo que esta seção descreve é válido mesmo que você tenha uma conta de automação do Azure que você deseja usar para configurar o runbook do PowerShell. Veja como configurá-lo:

1. Abra o Windows PowerShell.

2. Execute o cmdlet a seguir para entrar em sua conta do Azure.

    ```powershell
    Login-AzAccount
    ```

    >[!NOTE]
    >Sua conta deve ter direitos de colaborador na assinatura do Azure em que você deseja implantar a ferramenta de dimensionamento.

3. Execute o seguinte cmdlet para baixar o script e criar a conta de Automação do Azure:

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzAutoAccount.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzAutoAccount.ps1"
    ```

4. Execute o cmdlet a seguir para executar o script e criar a conta de automação do Azure. Você pode preencher valores para os parâmetros ou comentá-los para usar seus padrões.

    ```powershell
    $Params = @{
         "AADTenantId"           = "<Azure_Active_Directory_tenant_ID>"   # Optional. If not specified, it will use the current Azure context
         "SubscriptionId"        = "<Azure_subscription_ID>"              # Optional. If not specified, it will use the current Azure context
         "ResourceGroupName"     = "<Resource_group_name>"                # Optional. Default: "WVDAutoScaleResourceGroup"
         "AutomationAccountName" = "<Automation_account_name>"            # Optional. Default: "WVDAutoScaleAutomationAccount"
         "Location"              = "<Azure_region_for_deployment>"
         "WorkspaceName"         = "<Log_analytics_workspace_name>"       # Optional. If specified, Log Analytics will be used to configure the custom log table that the runbook PowerShell script can send logs to
    }

    .\CreateOrUpdateAzAutoAccount.ps1 @Params
    ```

5. A saída do cmdlet incluirá um URI de webhook. Certifique-se de manter um registro do URI porque você o usará como um parâmetro ao configurar o agendamento de execução para o aplicativo lógico do Azure.

6. Se você tiver especificado o parâmetro **WorkspaceName** para log Analytics, a saída do cmdlet também incluirá a ID do espaço de trabalho log Analytics e sua chave primária. Lembre-se de se lembrar do URI porque você precisará usá-lo novamente mais tarde como um parâmetro ao configurar o agendamento de execução para o aplicativo lógico do Azure.

7. Depois de configurar sua conta de Automação do Azure, entre na sua assinatura do Azure e verifique se sua conta de Automação do Azure e o respectivo runbook apareceram no grupo de recursos especificado, conforme mostrado na seguinte imagem:

    >[!div class="mx-imgBorder"]
    >![Uma imagem da página de visão geral do Azure mostrando a conta de automação do Azure recém-criada e o runbook.](media/automation-account.png)

    Para verificar se o webhook está no local certo, selecione o nome dele. Em seguida, vá para a seção Recursos do seu runbook e selecione **Webhooks**.

## <a name="create-an-azure-automation-run-as-account"></a>Criar uma conta Executar como da Automação do Azure

Agora que você tem uma conta de automação do Azure, também precisará criar uma conta Executar como da automação do Azure, caso ainda não tenha uma. Essa conta permitirá que a ferramenta acesse seus recursos do Azure.

Uma [conta Executar como da automação do Azure](../../automation/manage-runas-account.md) fornece autenticação para gerenciar recursos no Azure com cmdlets do Azure. Quando você cria uma conta Executar como, ela cria um novo usuário de entidade de serviço no Azure Active Directory e atribui a função colaborador ao usuário da entidade de serviço no nível da assinatura. Uma conta Executar como do Azure é uma ótima maneira de autenticar com segurança com certificados e um nome da entidade de serviço sem a necessidade de armazenar um nome de usuário e senha em um objeto de credencial. Para saber mais sobre a autenticação da conta Executar como, consulte [limitar permissões da conta Executar como](../../automation/manage-runas-account.md#limit-run-as-account-permissions).

Qualquer usuário que seja membro da função Administradores de assinatura e coadministrador da assinatura pode criar uma conta Executar como.

Para criar uma conta Executar como em sua conta de automação do Azure:

1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, insira e selecione **contas de automação**.

2. Na página **contas de automação** , selecione o nome da sua conta de automação do Azure.

3. No painel no lado esquerdo da janela, selecione **contas Executar como** na seção **configurações de conta** .

4. Selecione **conta Executar como do Azure**. Quando o painel **adicionar conta Executar como do Azure** for exibido, examine as informações de visão geral e, em seguida, selecione **criar** para iniciar o processo de criação de conta.

5. Aguarde alguns minutos para que o Azure crie a conta Executar como. O progresso da criação pode ser acompanhado no menu Notificações.

6. Quando o processo for concluído, ele criará um ativo chamado **AzureRunAsConnection** na conta de automação do Azure especificada. Selecione **conta Executar como do Azure**. O ativo de conexão contém a ID do aplicativo, a ID do locatário, a ID da assinatura e a impressão digital do certificado. Lembre-se da ID do Aplicativo, pois você a usará mais tarde. Você também pode encontrar as mesmas informações na página **conexões** . Para ir para esta página, no painel no lado esquerdo da janela, selecione **conexões** na seção **recursos compartilhados** e clique no ativo de conexão denominado **AzureRunAsConnection**.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Criar uma atribuição de função na Área de Trabalho Virtual do Windows

Em seguida, você precisa criar uma atribuição de função para que o **AzureRunAsConnection** possa interagir com a área de trabalho virtual do Windows. Use o PowerShell para entrar com uma conta que tenha permissões para criar atribuições de função.

Primeiro, baixe e importe o [módulo do PowerShell da Área de Trabalho Virtual do Windows](/powershell/windows-virtual-desktop/overview/) para usá-lo na sessão do PowerShell, caso ainda não tenha feito isso. Execute os cmdlets do PowerShell a seguir para se conectar à Área de Trabalho Virtual do Windows e exibir seus locatários.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

# If your tenant is not in "Default Tenant Group", uncomment the following line and specify the name of your tenant group
# Set-RdsContext -TenantGroupName "<Tenant_Group_Name>"

Get-RdsTenant
```

Quando você encontrar o locatário com os pools de hosts que deseja dimensionar, siga as instruções em [criar uma conta Executar como de automação do Azure](#create-an-azure-automation-run-as-account) para coletar a ID do aplicativo **AzureRunAsConnection** e usar o nome do locatário da área de trabalho virtual do Windows obtido do cmdlet anterior no seguinte cmdlet para criar a atribuição de função:

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId "<applicationid>" -TenantName "<tenantname>"
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Criar o Aplicativo Lógico do Azure e o agendamento de execução

Por fim, será preciso criar o Aplicativo Lógico do Azure e configurar um agendamento de execução para sua nova ferramenta de dimensionamento.

1. Abra o Windows PowerShell.

2. Execute o cmdlet a seguir para entrar em sua conta do Azure.

    ```powershell
    Login-AzAccount
    ```

3. Execute o cmdlet a seguir para baixar o script para criar o aplicativo lógico do Azure.

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzLogicApp.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzLogicApp.ps1"
    ```

4. Execute o cmdlet a seguir para entrar na Área de Trabalho Virtual do Windows com uma conta que tenha permissões de Proprietário da RDS ou de Colaborador da RDS.

    ```powershell
    Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

    # If your tenant is not in "Default Tenant Group", uncomment the following line and specify the name of your tenant group
    # Set-RdsContext -TenantGroupName "<Tenant_Group_Name>"
    ```

5. Execute o seguinte script do PowerShell para criar o aplicativo lógico do Azure e o agendamento de execução para seu pool de hosts

    >[!NOTE]
    >Você precisará executar esse script para cada pool de hosts que deseja fazer o dimensionamento automático, mas precisa apenas de uma conta de automação do Azure.

    ```powershell
    $AADTenantId = (Get-AzContext).Tenant.Id

    $AzSubscription = Get-AzSubscription | Out-GridView -OutputMode:Single -Title "Select your Azure Subscription"
    Select-AzSubscription -Subscription $AzSubscription.Id

    $ResourceGroup = Get-AzResourceGroup | Out-GridView -OutputMode:Single -Title "Select the resource group for the new Azure Logic App"

    $RDBrokerURL = (Get-RdsContext).DeploymentUrl
    $WVDTenant = Get-RdsTenant | Out-GridView -OutputMode:Single -Title "Select your WVD tenant"
    $WVDHostPool = Get-RdsHostPool -TenantName $WVDTenant.TenantName | Out-GridView -OutputMode:Single -Title "Select the host pool you'd like to scale"

    $LogAnalyticsWorkspaceId = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Workspace ID returned by when you created the Azure Automation account, otherwise leave it blank"
    $LogAnalyticsPrimaryKey = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Primary Key returned by when you created the Azure Automation account, otherwise leave it blank"
    $RecurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"
    $BeginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"
    $EndPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"
    $TimeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"
    $SessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"
    $MinimumNumberOfRDSH = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"
    $MaintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"
    $LimitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, any session host VM that has user sessions, will be left untouched"
    $LogOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"
    $LogOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

    $AutoAccount = Get-AzAutomationAccount | Out-GridView -OutputMode:Single -Title "Select the Azure Automation account"
    $AutoAccountConnection = Get-AzAutomationConnection -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName | Out-GridView -OutputMode:Single -Title "Select the Azure RunAs connection asset"

    $WebhookURIAutoVar = Get-AzAutomationVariable -Name 'WebhookURI' -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName

    $Params = @{
         "AADTenantId"                   = $AADTenantId                             # Optional. If not specified, it will use the current Azure context
         "SubscriptionID"                = $AzSubscription.Id                       # Optional. If not specified, it will use the current Azure context
         "ResourceGroupName"             = $ResourceGroup.ResourceGroupName         # Optional. Default: "WVDAutoScaleResourceGroup"
         "Location"                      = $ResourceGroup.Location                  # Optional. Default: "West US2"
         "RDBrokerURL"                   = $RDBrokerURL                             # Optional. Default: "https://rdbroker.wvd.microsoft.com"
         "TenantGroupName"               = $WVDTenant.TenantGroupName               # Optional. Default: "Default Tenant Group"
         "TenantName"                    = $WVDTenant.TenantName
         "HostPoolName"                  = $WVDHostPool.HostPoolName
         "LogAnalyticsWorkspaceId"       = $LogAnalyticsWorkspaceId                 # Optional. If not specified, script will not log to the Log Analytics
         "LogAnalyticsPrimaryKey"        = $LogAnalyticsPrimaryKey                  # Optional. If not specified, script will not log to the Log Analytics
         "ConnectionAssetName"           = $AutoAccountConnection.Name              # Optional. Default: "AzureRunAsConnection"
         "RecurrenceInterval"            = $RecurrenceInterval                      # Optional. Default: 15
         "BeginPeakTime"                 = $BeginPeakTime                           # Optional. Default: "09:00"
         "EndPeakTime"                   = $EndPeakTime                             # Optional. Default: "17:00"
         "TimeDifference"                = $TimeDifference                          # Optional. Default: "-7:00"
         "SessionThresholdPerCPU"        = $SessionThresholdPerCPU                  # Optional. Default: 1
         "MinimumNumberOfRDSH"           = $MinimumNumberOfRDSH                     # Optional. Default: 1
         "MaintenanceTagName"            = $MaintenanceTagName                      # Optional.
         "LimitSecondsToForceLogOffUser" = $LimitSecondsToForceLogOffUser           # Optional. Default: 1
         "LogOffMessageTitle"            = $LogOffMessageTitle                      # Optional. Default: "Machine is about to shutdown."
         "LogOffMessageBody"             = $LogOffMessageBody                       # Optional. Default: "Your session will be logged off. Please save and close everything."
         "WebhookURI"                    = $WebhookURIAutoVar.Value
    }

    .\CreateOrUpdateAzLogicApp.ps1 @Params
    ```

    Depois de executar o script, o aplicativo lógico do Azure deve aparecer em um grupo de recursos, conforme mostrado na imagem a seguir.

    >[!div class="mx-imgBorder"]
    >![Uma imagem da página de visão geral de um Aplicativo Lógico do Azure de exemplo.](../media/logic-app.png)

    Para fazer alterações no agendamento de execução, como alterar o intervalo de recorrência ou o fuso horário, vá para o Agendador de dimensionamento automático do aplicativo lógico do Azure e selecione **Editar** para ir para o designer do aplicativo lógico do Azure.

    >[!div class="mx-imgBorder"]
    >![Uma imagem do designer de aplicativo lógico do Azure. Os menus de recorrência e webhook que permitem ao usuário editar os horários de recorrência e o arquivo de webhook estão abertos.](../media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Gerenciar a ferramenta de dimensionamento

Agora que você criou a ferramenta de dimensionamento, pode acessar a saída dela. Esta seção descreve alguns recursos que podem ser úteis.

### <a name="view-job-status"></a>Exibir status do trabalho

No portal do Azure, é possível exibir um status resumido de todos os trabalhos do runbook ou exibir um status mais detalhado de um trabalho específico do runbook.

À direita da conta de automação do Azure selecionada, em "estatísticas do trabalho", você pode exibir uma lista de resumos de todos os trabalhos de runbook. Abrir a página **Trabalhos** no lado esquerdo da janela mostrará os status do trabalho atual, os horários de início e de conclusão.

>[!div class="mx-imgBorder"]
>![Uma captura de tela da página de status de trabalho.](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Exibir logs e saída da ferramenta de dimensionamento

Você pode exibir os logs das operações de expansão e redução horizontal abrindo seu runbook e selecionando o trabalho.

Navegue até o runbook em seu grupo de recursos que hospeda a conta de automação do Azure e selecione **visão geral**. Na página Visão geral, selecione um trabalho em **trabalhos recentes** para exibir sua saída da ferramenta de dimensionamento, conforme mostrado na imagem a seguir.

>[!div class="mx-imgBorder"]
>![Uma imagem da janela de saída da ferramenta de dimensionamento.](media/tool-output.png)

### <a name="check-the-runbook-script-version-number"></a>Verificar o número de versão do script do runbook

Você pode verificar qual versão do script de runbook você está usando abrindo o arquivo de runbook em sua conta de automação do Azure e selecionando **Exibir**. Um script para o runbook será exibido no lado direito da tela. No script, você verá o número de versão no formato na `v#.#.#` `SYNOPSIS` seção. Você pode encontrar o número de versão mais recente [aqui](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/wvd-scaling-script/basicScale.ps1#L1). Se você não vir um número de versão no script de runbook, isso significa que você está executando uma versão anterior do script e deve atualizá-lo imediatamente. Se você precisar atualizar o script de runbook, siga as instruções em [criar ou atualizar uma conta de automação do Azure](#create-or-update-an-azure-automation-account).

### <a name="reporting-issues"></a>Problemas de relatórios

Ao relatar um problema, você precisará fornecer as seguintes informações para nos ajudar a solucionar problemas:

- Um log completo da guia **todos os logs** no trabalho que causou o problema. Para saber como obter o log, siga as instruções em [Exibir logs e saída da ferramenta de dimensionamento](#view-logs-and-scaling-tool-output). Se houver alguma informação confidencial ou privada no log, você poderá removê-la antes de enviar o problema para nós.

- A versão do script de runbook que você está usando. Para saber como obter o número de versão, consulte [verificar o número de versão do script do runbook](#check-the-runbook-script-version-number)

- O número de versão de cada um dos seguintes módulos do PowerShell instalados em sua conta de automação do Azure. Para encontrar esses módulos, abra a conta de automação do Azure, selecione **módulos** na seção **recursos compartilhados** no painel no lado esquerdo da janela e procure o nome do módulo.
    - Az.Accounts
    - Az.Compute
    - Az.Resources
    - Az.Automation
    - OMSIngestionAPI
    - Microsoft. RDInfra. RDPowershell

- A data de validade da sua [conta Executar como](#create-an-azure-automation-run-as-account). Para encontrar isso, abra sua conta de automação do Azure e, em seguida, selecione **contas Executar como** em **configurações de conta** no painel no lado esquerdo da janela. A data de validade deve estar na **conta Executar como do Azure**.

### <a name="log-analytics"></a>Log Analytics

Se você decidiu usar Log Analytics, poderá exibir todos os dados de log em um log personalizado chamado **WVDTenantScale_CL** em **logs personalizados** na exibição **logs** do espaço de trabalho log Analytics. Listamos algumas consultas de exemplo que podem ser úteis.

- Para ver todos os logs de um pool de hosts, insira a consulta a seguir

    ```Kusto
    WVDTenantScale_CL
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- Para exibir o número total de VMs de host de sessão em execução no momento e sessões de usuário ativas no pool de hosts, insira a consulta a seguir

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Number of running session hosts:"
         or logmessage_s contains "Number of user sessions:"
         or logmessage_s contains "Number of user sessions per Core:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- Para exibir o status de todas as VMs de host de sessão em um pool de hosts, insira a consulta a seguir

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Session host:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- Para exibir erros e avisos, insira a consulta a seguir

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "ERROR:" or logmessage_s contains "WARN:"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

## <a name="report-issues"></a>Relatar problemas

Os relatórios de problemas da ferramenta de dimensionamento estão sendo manipulados no momento por Suporte da Microsoft. Ao fazer um relatório de problemas, certifique-se de seguir as instruções em [problemas de relatório](#reporting-issues). Se você tiver comentários sobre a ferramenta ou se quiser solicitar novos recursos, abra um problema do GitHub rotulado "4-WVD-dimensionar-ferramenta" na [página do GitHub do RDS](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4-WVD-scaling-tool).
