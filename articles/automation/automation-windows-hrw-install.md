---
title: Implantar um Hybrid Runbook Worker do Windows na Automação do Azure
description: Este artigo informa como implantar um Hybrid Runbook Worker que você pode usar para executar runbooks em computadores baseados no Windows em seu datacenter local ou ambiente de nuvem.
services: automation
ms.subservice: process-automation
ms.date: 11/24/2020
ms.topic: conceptual
ms.openlocfilehash: f6858c7350e6c72a096b2f2bd5f4a4ff606bf023
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651350"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Implantar um Windows híbrido Runbook Worker

Você pode usar o recurso Hybrid Runbook Worker do usuário da automação do Azure para executar runbooks diretamente em um computador do Azure ou não Azure, incluindo servidores registrados com [servidores habilitados para Arc do Azure](../azure-arc/servers/overview.md). No computador ou servidor que está hospedando a função, você pode executar runbooks diretamente em relação a ele e em recursos no ambiente para gerenciar esses recursos locais.

A automação do Azure armazena e gerencia runbooks e, em seguida, os entrega a um ou mais computadores designados. Este artigo descreve como implantar um Hybrid Runbook Worker de usuário em um computador Windows, como remover o trabalho e como remover um grupo de Hybrid Runbook Worker.

Depois de implantar com êxito um trabalhador de runbook, revise [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para saber como configurar seus runbooks para automatizar processos em seu datacenter local ou em outro ambiente de nuvem.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem o seguinte.

### <a name="a-log-analytics-workspace"></a>Um workspace do Log Analytics

A função Hybrid Runbook Worker depende de um espaço de trabalho de Log Analytics de Azure Monitor para instalar e configurar a função. Você pode criá-lo por meio de [Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace), por meio do [PowerShell](../azure-monitor/logs/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json), ou no [portal do Azure](../azure-monitor/logs/quick-create-workspace.md).

Se você não tiver um Azure Monitor Log Analytics espaço de trabalho, examine as [diretrizes de design do Azure monitor log](../azure-monitor/logs/design-logs-deployment.md) antes de criar o espaço de trabalho.

### <a name="log-analytics-agent"></a>Agente do Log Analytics

A função Hybrid Runbook Worker requer o [agente de log Analytics](../azure-monitor/agents/log-analytics-agent.md) para o sistema operacional Windows com suporte. Para servidores ou computadores hospedados fora do Azure, você pode instalar o agente de Log Analytics usando os [servidores habilitados para Arc do Azure](../azure-arc/servers/overview.md).

### <a name="supported-windows-operating-system"></a>Sistema operacional Windows com suporte

O recurso Hybrid Runbook Worker dá suporte aos seguintes sistemas operacionais:

* Windows Server 2019 (incluindo Server Core)
* Windows Server 2016, versão 1709 e 1803 (exceto Server Core)
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enterprise (incluindo multissessão) e Pro
* Windows 8 Enterprise e Pro
* Windows 7 SP1

### <a name="minimum-requirements"></a>Requisitos mínimos

Os requisitos mínimos para um sistema Windows e Hybrid Runbook Worker de usuário são:

* Windows PowerShell 5,1 ([Baixe o WMF 5,1](https://www.microsoft.com/download/details.aspx?id=54616)). Não há suporte para o PowerShell Core.
* .NET Framework 4.6.2 ou posterior
* Dois núcleos
* 4 GB de RAM
* Porta 443 (saída)

### <a name="network-configuration"></a>Configuração de rede

Para os requisitos de rede para o Hybrid Runbook Worker, consulte [Configurando sua rede](automation-hybrid-runbook-worker.md#network-planning).

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Adicionando um computador a um grupo de Hybrid Runbook Worker

Você pode adicionar o computador de trabalho a um grupo de Hybrid Runbook Worker em uma de suas contas de automação. Para computadores que hospedam o Hybrid runbook Worker do sistema gerenciado pelo Gerenciamento de Atualizações, eles podem ser adicionados a um grupo de Hybrid Runbook Worker. Mas você deve usar a mesma conta de automação para Gerenciamento de Atualizações e a associação de grupo de Hybrid Runbook Worker.

>[!NOTE]
>A automação do Azure [Gerenciamento de atualizações](./update-management/overview.md) instala automaticamente o Hybrid runbook Worker do sistema em um computador Azure ou não Azure habilitado para gerenciamento de atualizações. No entanto, esse trabalhador não está registrado com nenhum grupo de Hybrid Runbook Worker em sua conta de automação. Para executar seus runbooks nesses computadores, você precisa adicioná-los a um grupo de Hybrid Runbook Worker. Siga a etapa 6 na seção [implantação manual](#manual-deployment) para adicioná-la a um grupo.

## <a name="enable-for-management-with-azure-automation-state-configuration"></a>Habilitar para gerenciamento com a configuração de estado de automação do Azure

Para obter informações sobre como habilitar computadores para gerenciamento com a configuração de estado de automação do Azure, consulte [habilitar máquinas para gerenciamento pela configuração de estado de automação do Azure](automation-dsc-onboarding.md).

> [!NOTE]
> Para gerenciar a configuração de computadores que dão suporte à função de Hybrid Runbook Worker com a DSC (configuração de estado desejado), você deve adicionar os computadores como nós DSC.

## <a name="installation-options"></a>Opções de instalação

Para instalar e configurar um usuário do Windows Hybrid Runbook Worker, você pode usar um dos métodos a seguir.

* Use um script do PowerShell fornecido para [automatizar](#automated-deployment) completamente o processo de configuração de um ou mais computadores Windows. Esse é o método recomendado para computadores em seu datacenter ou em outro ambiente de nuvem.
* Importe manualmente a solução de automação, instale o agente do Log Analytics para Windows e configure a função de trabalho no computador.

## <a name="automated-deployment"></a>Implantação automatizada

O método de implantação automatizado usa o **New-OnPremiseHybridWorker.ps1** de script do PowerShell para automatizar e configurar a função de Hybrid runbook Worker do Windows. Ele executa o seguinte:

* Instala os módulos necessários
* Entrar com sua conta do Azure
* Verifica a existência do grupo de recursos e da conta de automação especificados
* Cria referências a atributos de conta de automação
* Cria um Azure Monitor espaço de trabalho Log Analytics se não for especificado
* Habilitar a solução de automação do Azure no espaço de trabalho
* Baixe e instale o agente de Log Analytics para Windows
* Registrar o computador como Hybrid Runbook Worker

Execute as etapas a seguir para instalar a função em seu computador Windows usando o script.

1. Baixe o script **New-OnPremiseHybridWorker.ps1** na [Galeria do PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). Depois de baixar o script, copie-o ou execute-o no computador de destino. O script de **New-OnPremiseHybridWorker.ps1** usa os parâmetros a seguir durante a execução.

    | Parâmetro | Status | Descrição |
    | --------- | ------ | ----------- |
    | `AAResourceGroupName` | Obrigatório | O nome do grupo de recursos associado à sua conta de automação. |
    | `AutomationAccountName` | Obrigatório | O nome da sua conta de automação.
    | `Credential` | Opcional | As credenciais a serem usadas ao fazer logon no ambiente do Azure. |
    | `HybridGroupName` | Obrigatório | O nome de um grupo do Hybrid Runbook Worker que você especifica como um destino para os runbooks que dão suporte a esse cenário. |
    | `OMSResourceGroupName` | Opcional | O nome do grupo de recursos do espaço de trabalho do Log Analytics. Se esse grupo de recursos não for especificado, o valor de `AAResourceGroupName` será usado. |
    | `SubscriptionID` | Obrigatório | O identificador da assinatura do Azure associada à sua conta de Automação. |
    | `TenantID` | Opcional | O identificador da organização do locatário associado à sua conta de Automação. |
    | `WorkspaceName` | Opcional | O nome do espaço de trabalho do Log Analytics. Se você não tiver um espaço de trabalho do Log Analytics, o script cria e configura um. |

2. Abra um prompt de comando com privilégios elevados do PowerShell de 64 bits.

3. No prompt de comando do PowerShell, navegue até a pasta que contém o script que você baixou. Mude os valores dos parâmetros `AutomationAccountName`, `AAResourceGroupName`, `OMSResourceGroupName`, `HybridGroupName`, `SubscriptionID` e `WorkspaceName`. Em seguida, execute o script.

    Você é solicitado a autenticar com o Azure depois de executar o script. Você deve entrar com uma conta que seja membro da função Administradores de **assinatura** e coadministrador da assinatura.

    ```powershell-interactive
    $NewOnPremiseHybridWorkerParameters = @{
      AutomationAccountName = <nameOfAutomationAccount>
      AAResourceGroupName   = <nameOfResourceGroup>
      OMSResourceGroupName  = <nameOfResourceGroup>
      HybridGroupName       = <nameOfHRWGroup>
      SubscriptionID        = <subscriptionId>
      WorkspaceName         = <nameOfLogAnalyticsWorkspace>
    }
    .\New-OnPremiseHybridWorker.ps1 @NewOnPremiseHybridWorkerParameters
    ```

4. É preciso concordar com a instalação do NuGet e autenticar com suas credenciais do Azure. Se você não tiver a versão mais recente do NuGet, poderá baixá-la das [versões de distribuição do NuGet disponíveis](https://www.nuget.org/downloads).

5. Verifique a implantação após a conclusão do script. Na página **grupos de Hybrid runbook Worker** em sua conta de automação, na guia **grupo Hybrid runbook Workers do usuário** , ele mostra o novo grupo e o número de membros. Se for um grupo existente, o número de membros é incrementado. Você pode selecionar o grupo na lista na página, no menu à esquerda, escolher **Hybrid Workers** . Na página **Hybrid Workers** , você pode ver cada membro do grupo listado.

## <a name="manual-deployment"></a>Implantação manual

Para instalar e configurar um Hybrid Runbook Worker do Windows, execute as etapas a seguir.

1. Habilite a solução de automação do Azure em seu espaço de trabalho Log Analytics executando o seguinte comando em um prompt de comando do PowerShell com privilégios elevados ou em Cloud Shell no [portal do Azure](https://portal.azure.com).

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. Implante o agente de Log Analytics no computador de destino.

    * Para VMs do Azure, instale o agente Log Analytics para Windows usando a [extensão de máquina virtual para o Windows](../virtual-machines/extensions/oms-windows.md). A extensão instala o agente do Log Analytics em máquinas virtuais do Azure e registra máquinas virtuais em um espaço de trabalho do Log Analytics existente. Você pode usar um modelo de Azure Resource Manager, o PowerShell ou Azure Policy para atribuir a política de [implantação do agente de log Analytics para *Linux* ou VMS do *Windows*](../governance/policy/samples/built-in-policies.md#monitoring) . Depois que o agente é instalado, o computador pode ser adicionado a um grupo de Hybrid Runbook Worker em sua conta de automação.
    
    * Para computadores não Azure, você pode instalar o agente de Log Analytics usando os [servidores habilitados para Arc do Azure](../azure-arc/servers/overview.md). Os servidores habilitados para Arc dão suporte à implantação do agente de Log Analytics usando os seguintes métodos:
    
        - Usando a estrutura de extensões de VM.
        
            Esse recurso nos servidores habilitados para Arc do Azure permite que você implante a extensão de VM do agente de Log Analytics em um servidor Windows e/ou Linux que não seja do Azure. As extensões de VM podem ser gerenciadas usando os seguintes métodos em seus computadores híbridos ou servidores gerenciados por servidores habilitados para Arc:
        
            - O [Portal do Azure](../azure-arc/servers/manage-vm-extensions-portal.md)
            - A [CLI do Azure](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [PowerShell do Azure](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - Modelos do Azure [Resource Manager](../azure-arc/servers/manage-vm-extensions-template.md)
        
        - Usando Azure Policy.
        
            Usando essa abordagem, você usa o Azure Policy [implantar o agente log Analytics em](../governance/policy/samples/built-in-policies.md#monitoring) uma política interna do Windows Azure ARC para máquinas de arco para a auditoria se o servidor habilitado para Arc tiver o agente de log Analytics instalado. Se o agente não estiver instalado, ele o implantará automaticamente usando uma tarefa de correção. Como alternativa, se você planeja monitorar as máquinas com Azure Monitor para VMs, use a iniciativa [habilitar Azure monitor para VMs](../governance/policy/samples/built-in-initiatives.md#monitoring) para instalar e configurar o agente de log Analytics.

    É recomendável instalar o agente de Log Analytics para Windows ou Linux usando Azure Policy.

3. Verificar se o agente está subordinado ao espaço de trabalho

    O agente Log Analytics para Windows conecta computadores a um espaço de trabalho Azure Monitor Log Analytics. Quando você instala o agente em seu computador e o conecta ao seu espaço de trabalho, ele baixa automaticamente os componentes necessários para o Hybrid Runbook Worker.

    Quando o agente tiver se conectado ao seu workspace do Log Analytics após alguns minutos, você poderá executar a consulta a seguir para verificar se ele está enviando dados de pulsação para o workspace.

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    Nos resultados da pesquisa, você deve ver os registros de pulsação do computador, indicando que ele está conectado e relatando para o serviço. Por padrão, cada agente encaminha um registro de pulsação para seu workspace atribuído. Use as etapas a seguir para concluir a instalação e a configuração do agente.

4. Confirme a versão do Hybrid Runbook Worker no computador que hospeda o agente de Log Analytics, navegue até `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` e anote a subpasta **versão** . Essa pasta aparecerá no computador vários minutos depois que a solução for habilitada no espaço de trabalho.

5. Instale o ambiente de runbook e conecte-se à automação do Azure. Quando você configura um agente para relatar para um Log Analytics espaço de trabalho e importa a solução de **automação** , a solução envia por push o `HybridRegistration` módulo do PowerShell. Esse módulo contém o `Add-HybridRunbookWorker` cmdlet. Use este cmdlet para instalar o ambiente de runbook no computador e registrá-lo com a automação do Azure.

    Abra uma sessão do PowerShell no modo de Administrador e execute os comandos a seguir para importar o módulo.

    ```powershell-interactive
    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module .\HybridRegistration.psd1
    ```

6. Execute o `Add-HybridRunbookWorker` cmdlet especificando os valores para os parâmetros `Url` , `Key` e `GroupName` .

    ```powershell-interactive
    Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
    ```

    Você pode obter as informações necessárias para os parâmetros `Url` e `Key` a partir da página de **chaves** em sua conta de automação. Selecione **chaves** na seção **configurações de conta** do lado esquerdo da página.

    ![Página Gerenciar Chaves](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * Para o `Url` parâmetro, copie o valor para **URL**.

    * Para o `Key` parâmetro, copie o valor da **chave de acesso primária**.

    * Para o parâmetro `GroupName`, use o nome do grupo de Hybrid Runbook Worker. Se esse grupo já existir na conta de automação, o computador atual será adicionado a ele. Se esse grupo não existir, será adicionado.

    * Se necessário, defina o parâmetro `Verbose` para receber detalhes sobre a instalação.

7. Verifique a implantação após a conclusão do comando. Na página **grupos de Hybrid runbook Worker** em sua conta de automação, na guia **grupo Hybrid runbook Workers do usuário** , ele mostra o grupo novo ou existente e o número de membros. Se for um grupo existente, o número de membros é incrementado. Você pode selecionar o grupo na lista na página, no menu à esquerda, escolher **Hybrid Workers**. Na página **Hybrid Workers** , você pode ver cada membro do grupo listado.

## <a name="install-powershell-modules"></a>Instalar módulos do PowerShell

Os Runbooks podem usar qualquer uma das atividades e cmdlets definidos nos módulos instalados em seu ambiente de Automação do Azure. Como esses módulos não são implantados automaticamente em computadores locais, você deve instalá-los manualmente. A exceção é o módulo do Azure. Esse módulo é instalado por padrão e fornece acesso a cmdlets para todos os serviços e atividades do Azure para a Automação do Azure.

Como o objetivo principal do Hybrid Runbook Worker é gerenciar recursos locais, você provavelmente precisará instalar os módulos que permitem esses recursos, principalmente o módulo `PowerShellGet`. Para obter informações sobre como instalar módulos do Windows PowerShell, veja [Windows PowerShell](/powershell/scripting/developer/windows-powershell).

Os módulos instalados devem estar em um local referenciado pela variável de ambiente `PSModulePath`, para que o Hybrid Worker possa importá-los automaticamente. Para obter mais informações, veja [Instalar módulos no PSModulePath](/powershell/scripting/developer/module/installing-a-powershell-module).

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-windows-hybrid-runbook-worker"></a>Remover o Hybrid Runbook Worker

1. No portal do Azure, vá para sua conta de automação.

2. Em **Configurações da conta**, selecione **Chaves** e anote os valores para **URL** e **Chave de acesso primária**.

3. Abra uma sessão do PowerShell no modo de Administrador e execute o comando a seguir com a URL e os valores de chave de acesso primária. Use o parâmetro `Verbose` para obter um log detalhado do processo de remoção. Para remover computadores antigos do grupo do Hybrid Worker, use o parâmetro opcional `machineName`.

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Remover um grupo de Hybrid Worker

Para remover um grupo de Hybrid Runbook Worker, primeiro você precisa remover o Hybrid Runbook Worker de cada computador que seja membro do grupo. Em seguida, execute as seguintes etapas para remover o grupo:

1. Abra a conta de Automação no Portal do Azure.

2. Em **Automação de Processo**, selecione **Grupos de Trabalho Híbrido**. Selecione o grupo que você deseja excluir. A página de propriedades desse grupo é exibida.

   ![Página Propriedades](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Na página de propriedades do grupo selecionado, selecione **Excluir**. Uma mensagem pede para você confirmar esta ação. Selecione **Sim** se tiver certeza de que deseja continuar.

   ![Mensagem de confirmação](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Esse processo pode levar vários segundos para terminar. Você pode acompanhar o progresso em **Notificações** no menu.

## <a name="next-steps"></a>Próximas etapas

* Para saber como configurar os runbooks para automatizar processos no datacenter local ou em outro ambiente de nuvem, consulte [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

* Para saber como solucionar problemas de seus Hybrid Runbook Workers, veja [Solucionar Problemas do Hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#general).
