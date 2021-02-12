---
title: Conectar máquinas híbridas ao Azure usando o PowerShell
description: Neste artigo, você aprende a instalar o agente e a conectar um computador ao Azure usando os servidores habilitados para Arc do Azure. Faça isso com o PowerShell.
ms.date: 10/28/2020
ms.topic: conceptual
ms.openlocfilehash: 0218235179e1a8a883360d0061e685c04079cbf4
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95492934"
---
# <a name="connect-hybrid-machines-to-azure-by-using-powershell"></a>Conectar máquinas híbridas ao Azure usando o PowerShell

Para servidores habilitados com o Azure Arc, você pode executar etapas manuais para habilitá-los para um ou mais computadores Windows ou Linux em seu ambiente. Como alternativa, você pode usar o cmdlet do PowerShell [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine) para baixar o agente do computador conectado, instalar o agente e registrar o computador com o Arc do Azure. O cmdlet baixa o pacote do agente do Windows (Windows Installer) do centro de download da Microsoft e o pacote do agente do Linux do repositório de pacotes da Microsoft.

Esse método exige que você tenha permissões de administrador no computador para instalar e configurar o agente. No Linux, usando a conta raiz, e no Windows, você é membro do grupo local de administradores. Você pode concluir esse processo interativamente ou remotamente em um servidor Windows usando a [comunicação remota do PowerShell](/powershell/scripting/learn/ps101/08-powershell-remoting).

Antes de começar, examine os [pré-requisitos](agent-overview.md#prerequisites) e verifique se sua assinatura e seus recursos atendem aos requisitos. Para obter informações sobre regiões com suporte e outras considerações relacionadas, consulte [regiões do Azure com suporte](overview.md#supported-regions).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Um computador com Azure PowerShell. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure](/powershell/azure/).

Use o PowerShell para gerenciar extensões de VM em seus servidores híbridos gerenciados por servidores habilitados para Arc do Azure. Antes de usar o PowerShell, instale o `Az.ConnectedMachine` módulo. Execute o seguinte comando no servidor habilitado com o arco do Azure:

```powershell
Install-Module -Name Az.ConnectedMachine
```

Quando a instalação for concluída, você verá a seguinte mensagem:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>Instalar o agente e conectar-se ao Azure

1. Abra um console do PowerShell com privilégios elevados.

2. Entre no Azure executando o comando `Connect-AzAccount` .

3. Para instalar o agente do computador conectado, use `Connect-AzConnectedMachine` com `-Name` os `-ResourceGroupName` parâmetros, `-Location` e. Use o `-SubscriptionId` parâmetro para substituir a assinatura padrão como resultado do contexto do Azure criado após a entrada. Execute um dos seguintes comandos:

    * Para instalar o agente do computador conectado no computador de destino que pode se comunicar diretamente com o Azure, execute:

        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region>
        ```
    
    * Para instalar o agente do computador conectado no computador de destino que se comunica por meio de um servidor proxy, execute:
        
        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -Proxy http://<proxyURL>:<proxyport>
        ```

Se o agente não for iniciado após a conclusão da instalação, verifique os logs para obter informações de erro detalhadas. No Windows, verifique este arquivo: *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log*. No Linux, verifique este arquivo: */var/opt/azcmagent/log/himds.log*.

## <a name="install-and-connect-by-using-powershell-remoting"></a>Instalar e conectar-se usando a comunicação remota do PowerShell

Veja como configurar um ou mais servidores Windows com servidores habilitados com o Azure Arc. Você deve habilitar a comunicação remota do PowerShell no computador remoto. Para fazer isso, use o cmdlet `Enable-PSRemoting`.

1. Abra um console do PowerShell como administrador.

2. Entre no Azure executando o comando `Connect-AzAccount` .

3. Para instalar o agente do computador conectado, use `Connect-AzConnectedMachine` com os `-ResourceGroupName` `-Location` parâmetros e. Os nomes de recursos do Azure usarão automaticamente o nome do host de cada servidor. Use o `-SubscriptionId` parâmetro para substituir a assinatura padrão como resultado do contexto do Azure criado após a entrada.

    * Para instalar o agente do computador conectado no computador de destino que pode se comunicar diretamente com o Azure, execute o seguinte comando:
    
        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```
    
    * Para instalar o agente do computador conectado em vários computadores remotos ao mesmo tempo, adicione uma lista de nomes de computadores remotos, cada um separado por uma vírgula.

        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName1, myMachineName2, myMachineName3
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```

    O exemplo a seguir mostra os resultados do comando que visa uma única máquina:
    
    ```azurepowershell
    time="2020-08-07T13:13:25-07:00" level=info msg="Onboarding Machine. It usually takes a few minutes to complete. Sometimes it may take longer depending on network and server load status."
    time="2020-08-07T13:13:25-07:00" level=info msg="Check network connectivity to all endpoints..."
    time="2020-08-07T13:13:29-07:00" level=info msg="All endpoints are available... continue onboarding"
    time="2020-08-07T13:13:50-07:00" level=info msg="Successfully Onboarded Resource to Azure" VM Id=f65bffc7-4734-483e-b3ca-3164bfa42941
    
    Name           Location OSName   Status     ProvisioningState
    ----           -------- ------   ------     -----------------
    myMachineName  eastus   windows  Connected  Succeeded
    ```

## <a name="verify-the-connection-with-azure-arc"></a>Verificar a conexão com o Azure Arc

Depois de instalar e configurar o agente para se registrar com os servidores habilitados para Arc do Azure, vá para o portal do Azure para verificar se o servidor foi conectado com êxito. Exiba o seu computador no [portal do Azure](https://portal.azure.com).

![Captura de tela do painel servidores, mostrando uma conexão de servidor bem-sucedida.](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Próximas etapas

* Se necessário, consulte o [guia solucionar problemas do agente do computador conectado](troubleshoot-agent-onboard.md).

* Saiba como gerenciar seu computador usando [Azure Policy](../../governance/policy/overview.md). Você pode usar a [configuração de convidado](../../governance/policy/concepts/guest-configuration.md)da VM, verificar se o computador está relatando para o espaço de trabalho esperado do log Analytics e habilitar o monitoramento com [Azure monitor com VMs](../../azure-monitor/insights/vminsights-enable-policy.md).

* Saiba mais sobre o [Agente do Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). O agente Log Analytics para Windows e Linux é necessário quando você deseja coletar dados de monitoramento de carga de trabalho e do sistema operacional, ou gerenciá-los usando runbooks ou recursos de automação do Azure, como Gerenciamento de Atualizações. Esse agente também é necessário para usar outros serviços do Azure, como a [central de segurança do Azure](../../security-center/security-center-introduction.md).
