---
title: Instalar o agente do computador conectado usando o DSC do Windows PowerShell
description: Neste artigo, você aprende a conectar computadores ao Azure usando os servidores habilitados para Arc do Azure usando o DSC do Windows PowerShell.
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: c0ae9c97afe14559aa36c1b8387f07897aa4c43b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100587650"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>Como instalar o agente do computador conectado usando o DSC do Windows PowerShell

Usando a DSC ( [configuração de estado desejado) do Windows PowerShell](/powershell/scripting/dsc/getting-started/winGettingStarted) , você pode automatizar a instalação e a configuração de software para um computador Windows. Este artigo descreve como usar a DSC para instalar o agente de máquina conectado de servidores habilitados para Arc do Azure em computadores Windows híbridos.

## <a name="requirements"></a>Requisitos

- Windows PowerShell versão 4,0 ou superior

- O módulo DSC do [AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc)

- Uma entidade de serviço para conectar os computadores aos servidores habilitados para Arc do Azure de forma não interativa. Siga as etapas na seção [criar uma entidade de serviço para integração em escala](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) se você ainda não criou uma entidade de serviço para servidores habilitados para Arc.

## <a name="install-the-connectedmachine-dsc-module"></a>Instalar o módulo DSC do ConnectedMachine

1. Para instalar o módulo manualmente, baixe o código-fonte e descompacte o conteúdo do diretório do projeto para o `$env:ProgramFiles\WindowsPowerShell\Modules folder` . Ou execute o seguinte comando para instalar a partir da galeria do PowerShell usando o PowerShellGet (no PowerShell 5,0):

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. Para confirmar a instalação, execute o comando a seguir e verifique se você vê os recursos de DSC do computador conectado do Azure disponíveis.

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   Na saída, você verá algo semelhante ao seguinte:

   ![Confirmação do exemplo de instalação do módulo DSC do computador conectado](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>Instalar o agente e conectar-se ao Azure

Os recursos neste módulo são projetados para gerenciar a configuração do agente de máquina conectado do Azure. Também está incluído um script `AzureConnectedMachineAgent.ps1` do PowerShell, encontrado na `AzureConnectedMachineDsc\examples` pasta. Ele usa recursos da Comunidade para automatizar o download e a instalação e estabelecer uma conexão com o arco do Azure. Esse script executa etapas semelhantes descritas no artigo [conectar máquinas híbridas ao Azure do portal do Azure](onboard-portal.md) .

Se o computador precisar se comunicar por meio de um servidor proxy para o serviço, depois de instalar o agente, você precisará executar um comando descrito [aqui](manage-agent.md#update-or-remove-proxy-settings). Isso define a variável de ambiente `https_proxy` do sistema do servidor proxy. Em vez de executar o comando manualmente, você pode executar essa etapa com a DSC usando o módulo [ComputeManagementDsc](https://www.powershellgallery.com/packages/ComputerManagementDsc) .

>[!NOTE]
>Para permitir que a DSC seja executada, o Windows precisa ser configurado para receber comandos remotos do PowerShell, mesmo quando você estiver executando uma configuração de localhost. Para configurar seu ambiente corretamente de forma simples, basta executar `Set-WsManQuickConfig -Force` em um Terminal do PowerShell elevado.
>

Os documentos de configuração (Arquivos MOF) podem ser aplicados ao computador usando o `Start-DscConfiguration` cmdlet.

A seguir estão os parâmetros que você passa para o script do PowerShell a ser usado.

- `TenantId`: O identificador exclusivo (GUID) que representa sua instância dedicada do Azure AD.

- `SubscriptionId`: A ID da assinatura (GUID) da sua assinatura do Azure na qual você deseja que os computadores.

- `ResourceGroup`: O nome do grupo de recursos ao qual você deseja que seus computadores conectados pertençam.

- `Location`: Consulte [regiões do Azure com suporte](overview.md#supported-regions). Essa localização pode ser a mesma ou uma diferente, assim como ocorre com a localização do grupo de recursos.

- `Tags`: Matriz de cadeia de caracteres de marcas que devem ser aplicadas ao recurso de computador conectado.

- `Credential`: Um objeto de credencial do PowerShell com o **ApplicationId** e a **senha** usados para registrar computadores em escala usando uma [entidade de serviço](onboard-service-principal.md).

1. Em um console do PowerShell, navegue até a pasta em que você salvou o `.ps1` arquivo.

2. Execute os seguintes comandos do PowerShell para compilar o documento MOF (para obter informações sobre compilação de configurações DSC, consulte [Configurações DSC](/powershell/scripting/dsc/configurations/configurations)):

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. Isso criará um `localhost.mof file` em uma nova pasta chamada `C:\dsc` .

Depois de instalar o agente e configurá-lo para se conectar aos servidores habilitados para Arc do Azure, vá para a portal do Azure para verificar se o servidor foi conectado com êxito. Exiba seus computadores no [portal do Azure](https://aka.ms/hybridmachineportal).

## <a name="adding-to-existing-configurations"></a>Adicionando a configurações existentes

Esse recurso pode ser adicionado às configurações de DSC existentes para representar uma configuração de ponta a ponta para um computador. Por exemplo, talvez você queira adicionar esse recurso a uma configuração que define as configurações de sistema operacional seguro.

O módulo [CompositeResource](https://www.powershellgallery.com/packages/compositeresource) da galeria do PowerShell pode ser usado para criar um [recurso de composição](/powershell/scripting/dsc/resources/authoringResourceComposite) da configuração de exemplo, para simplificar ainda mais a combinação de configurações.

## <a name="next-steps"></a>Próximas etapas

* Informações de solução de problemas podem ser encontradas no [guia solucionar problemas do agente do computador conectado](troubleshoot-agent-onboard.md).

* Saiba como gerenciar seu computador usando o [Azure Policy](../../governance/policy/overview.md) para itens como [configurar convidados](../../governance/policy/concepts/guest-configuration.md) de VM, verificar se o computador está relatando ao workspace do Log Analytics esperado, habilitar o monitoramento com o [Azure Monitor em VMs](../../azure-monitor/vm/vminsights-enable-policy.md) e muito mais.

* Saiba mais sobre o [Agente do Log Analytics](../../azure-monitor/agents/log-analytics-agent.md). O agente do Log Analytics para Windows e Linux é necessário quando você deseja monitorar proativamente o sistema operacional e as cargas de trabalho em execução no computador, o gerencia usando os runbooks de automação ou soluções como o Gerenciamento de Atualizações ou usa outros serviços do Azure como a [Central de Segurança do Azure](../../security-center/security-center-introduction.md).