---
title: Habilitar o State Configuration da Automação do Azure
description: Este artigo informa como configurar computadores para gerenciamento com o State Configuration da Automação do Azure.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: c0dc68bd7dacf0cd7f4be9732d45831e2dbb712c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896996"
---
# <a name="enable-azure-automation-state-configuration"></a>Habilitar o State Configuration da Automação do Azure

Este tópico descreve como você pode configurar seus computadores para gerenciamento com o State Configuration da Automação do Azure. Para obter detalhes desse serviço, confira [Visão geral do State Configuration da Automação do Azure](automation-dsc-overview.md).

## <a name="enable-azure-vms"></a>Habilitar VMs do Azure

O State Configuration da Automação do Azure permite habilitar facilmente as VMs do Azure para gerenciamento de configuração, usando o portal do Azure, modelos do Azure Resource Manager ou PowerShell. Nos bastidores, e sem que um administrador precise se conectar remotamente a uma VM, a extensão Desired State Configuration da VM do Azure registra a VM com o State Configuration da Automação do Azure. Como a extensão do Azure é executada de forma assíncrona, são fornecidas etapas para acompanhar seu progresso em [Verificar o status da instalação da VM](#check-status-of-vm-setup).

> [!NOTE]
>A implantação da DSC em um nó do Linux usa a pasta **/tmp**. Módulos como `nxautomation` são baixados temporariamente para verificação antes de serem instalados em seus locais apropriados. Para assegurar que os módulos sejam instalados corretamente, o agente do Log Analytics para Linux precisa de permissões de leitura/gravação na pasta **/tmp**.<br><br>
>O agente do Log Analytics para Linux é executado como o usuário `omsagent`. Para conceder permissão de gravação ao usuário `omsagent`, execute o comando `setfacl -m u:omsagent:rwx /tmp`.

### <a name="enable-a-vm-using-azure-portal"></a>Habilitar uma VM usando o Portal do Azure

Para habilitar uma VM do Azure para o State Configuration por meio do [portal do Azure](https://portal.azure.com/):

1. Navegue para a conta de automação do Azure na qual habilitar VMs. 

2. Na página State Configuration, selecione a guia **Nós** e, em seguida, clique em **Adicionar**.

3. Escolha uma VM para habilitar.

4. Se o computador não tiver a extensão de estado desejado do PowerShell instalada e o estado de energia estiver em execução, clique em **Conectar**.

5. Em **Registro**, insira os [valores do Local Configuration Manager da DSC do PowerShell](/powershell/scripting/dsc/managing-nodes/metaConfig) necessários para o seu caso de uso. Opcionalmente, você pode inserir uma configuração de nó para atribuir à VM.

![Habilitar VM](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="enable-a-vm-using-azure-resource-manager-templates"></a>Habilitar uma VM usando modelos do Azure Resource Manager

Você pode instalar e habilitar uma VM para State Configuration usando modelos do Azure Resource Manager. Confira [Servidor gerenciado pelo serviço Desired State Configuration](https://azure.microsoft.com/resources/templates/101-automation-configuration/) para obter um modelo de exemplo que habilita uma VM existente para o State Configuration. Se você estiver gerenciando um conjunto de dimensionamento de máquinas virtuais, confira o modelo de exemplo em [Configuração do conjunto de dimensionamento de máquinas virtuais gerenciada pela Automação do Azure](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="enable-machines-using-powershell"></a>Habilitar computadores usando o PowerShell

Você pode usar o cmdlet [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) no PowerShell para habilitar VMs para o State Configuration. 

> [!NOTE]
>O cmdlet `Register-AzAutomationDscNode` é implementado atualmente apenas para computadores que executam o Windows, pois ele dispara apenas a extensão do Windows.

### <a name="register-vms-across-azure-subscriptions"></a>Registrar VMs nas assinaturas do Azure

A melhor maneira de registrar VMs de outras assinaturas do Azure é usar a extensão DSC em um modelo de implantação do Azure Resource Manager. São fornecidos exemplos em [Extensão Desired State Configuration com modelos do Azure Resource Manager](../virtual-machines/extensions/dsc-template.md).

Para localizar a chave de registro e a URL de registro a serem usados como parâmetros no modelo, confira [Habilitar computadores com segurança usando o registro](#enable-machines-securely-using-registration).

## <a name="enable-physicalvirtual-windows-machines"></a>Habilitar computadores Windows físicos/virtuais

Você pode habilitar servidores Windows em execução no local ou em outros ambientes de nuvem (incluindo instâncias EC2 do AWS) no State Configuration da Automação do Azure. Os servidores devem ter [acesso de saída ao Azure](automation-dsc-overview.md#network-planning).

1. Verifique se a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) está instalada nos computadores para ativar o State Configuration. Além disso, o WMF 5 deve ser instalado no computador que você está usando para habilitar os computadores.
1. Siga as instruções em [Gerar metaconfigurações DSC](#generate-dsc-metaconfigurations) para criar uma pasta que contém as metaconfigurações DSC necessárias. 
1. Use o cmdlet a seguir para aplicar as metaconfigurações da DSC do PowerShell remotamente aos computadores a serem habilitados. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Se não for possível aplicar as metaconfigurações da DSC do PowerShell remotamente, copie a pasta de **metaconfigurações** nos computadores que você está habilitando. Em seguida, adicione o código para chamar [Set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager) localmente nos computadores.
1. Usando o portal ou cmdlets do Azure, verifique se as máquinas aparecem como nós do State Configuration registrados na sua conta de Automação do Azure.

## <a name="enable-physicalvirtual-linux-machines"></a>Habilitar computadores Linux físicos/virtuais

Você pode habilitar servidores Linux em execução no local ou em outros ambientes de nuvem para o State Configuration. Os servidores devem ter [acesso de saída ao Azure](automation-dsc-overview.md#network-planning).

1. Certifique-se de que a versão mais recente da [Desired State Configuration do PowerShell para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) esteja instalada nos computadores para habilitar o State Configuration.
2. Se os [padrões do Local Configuration Manager da DSC do PowerShell](/powershell/scripting/dsc/managing-nodes/metaConfig4) correspondem ao seu caso de uso, e você desejar habilitar os computadores para que eles sejam extraídos e se reportem ao State Configuration:

   - Em cada computador Linux a habilitar, use `Register.py` para habilitar o computador com os padrões do Local Configuration Manager da DSC do PowerShell.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Para localizar a chave de registro e a URL de registro da sua conta de Automação, confira [Habilitar computadores com segurança usando o registro](#enable-machines-securely-using-registration).

3. Se os padrões do Local Configuration Manager da DSC do PowerShell não corresponderem ao seu caso de uso ou se você deseja habilitar computadores que reportam apenas ao State Configuration da Automação do Azure, siga as etapas 4-7. Caso contrário, vá diretamente para a etapa 7.

4. Siga as instruções na seção [Gerar metaconfigurações DSC](#generate-dsc-metaconfigurations) para criar uma pasta que contém as metaconfigurações DSC necessárias.

5. Verifique se a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) está instalada no computador que está sendo usado para habilitar seus computadores para o State Configuration.

6. Adicione o código a seguir para aplicar as metaconfigurações da DSC do PowerShell remotamente aos computadores a serem habilitados.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Se você não pode aplicar as metaconfigurações do DSC do PowerShell remotamente, copie a metaconfiguração correspondente a esses computadores remotos a partir da pasta descrita na etapa 4 nos computadores Linux.

8. Adicione código para chamar `Set-DscLocalConfigurationManager.py` localmente em cada computador Linux para ativar o State Configuration.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Usando o portal do Azure ou os cmdlets, verifique se os computadores a habilitar agora aparecem como nós DSC registrados em sua conta de Automação do Azure.

## <a name="generate-dsc-metaconfigurations"></a>Gerar metaconfigurações DSC

Para habilitar qualquer computador para o State Configuration, você pode gerar uma [metaconfiguração DSC](/powershell/scripting/dsc/managing-nodes/metaConfig). Essa configuração informa ao agente de DSC para extrair e/ou reportar ao State Configuration da Automação do Azure. Você pode gerar uma metaconfiguração DSC para o State Configuration da Automação do Azure usando uma configuração DSC do PowerShell, ou os cmdlets do PowerShell de Automação do Azure.

> [!NOTE]
> Metaconfigurações DSC contêm os segredos necessários para habilitar um computador em uma conta de Automação para gerenciamento. Certifique-se de proteger corretamente quaisquer metaconfigurações de DSC que criar, ou exclua-as imediatamente após o uso.

O suporte a proxy para metaconfigurações é controlado pelo [Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig), que é o mecanismo de DSC do Windows PowerShell. O LCM é executado em todos os nós de destino e é responsável por chamar os recursos de configuração que estão incluídos em um script de metaconfiguração DSC. Você pode incluir suporte a proxy em uma configuração de metaconfiguração incluindo definições de propriedades `ProxyURL` e `ProxyCredential`, conforme necessário nos blocos `ConfigurationRepositoryWeb`, `ResourceRepositoryWeb` e `ReportServerWeb`. Um exemplo da configuração de URL é `ProxyURL = "http://172.16.3.6:3128";`. A propriedade `ProxyCredential` é definida como um objeto `PSCredential`, conforme descrito em [Gerenciar credenciais na Automação do Azure](shared-resources/credentials.md). 

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>Gerar metaconfigurações DSC usando uma configuração DSC

1. Abra o VSCode (ou seu editor favorito) como administrador em um computador no seu ambiente local. O computador também deve ter a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) instalada.
1. Compie o script a seguir localmente. Esse script contém uma configuração DSC do PowerShell para criar metaconfigurações e um comando para dar início à criação de metaconfigurações.

    > [!NOTE]
    > Os nomes de configuração do nó do State Configuration diferenciam maiúsculas de minúsculas no portal do Azure. Se o caractere for incompatível, o nó não aparecerá na guia **Nós**.

   ```powershell
   # The DSC configuration that will generate metaconfigurations
   [DscLocalConfigurationManager()]
   Configuration DscMetaConfigs
   {
        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl       = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
   }

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
   $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
   }

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   DscMetaConfigs @Params
   ```

1. Preencha a chave de registro e a URL para sua conta de Automação, bem como os nomes dos computadores a habilitar. Todos os outros parâmetros são opcionais. Para localizar a chave de registro e a URL de registro da sua conta de Automação, confira [Habilitar computadores com segurança usando o registro](#enable-machines-securely-using-registration).

1. Se você deseja que os computadores relatem informações de status de DSC ao State Configuration da Automação do Azure, mas não efetuem pull da configuração de recepção ou módulos do PowerShell, defina o parâmetro `ReportOnly` como verdadeiro.

1. Se o `ReportOnly` não estiver definido, os computadores reportarão as informações de status de DSC ao State Configuration da Automação do Azure e configuração de pull ou módulos do PowerShell. Defina os parâmetros adequadamente nos blocos `ConfigurationRepositoryWeb`, `ResourceRepositoryWeb` e `ReportServerWeb`.

1. Execute o script. Agora você deve ter uma pasta de diretório de trabalho chamada **DscMetaConfigs**, que contém as metaconfigurações de DSC do PowerShell para os computadores a habilitar (como um administrador).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Gerar metaconfigurações DSC usando cmdlets de Automação do Azure

Se os padrões do LCM de DSC do PowerShell corresponderem ao seu caso de uso e você desejar permitir que os computadores extraiam e reportem ao State Configuration de Automação do Azure, você poderá gerar as metaconfigurações necessárias do DSC mais simplesmente usando os cmdlets de Automação do Azure.

1. Abra o console do PowerShell ou o VSCode como administrador em um computador no seu ambiente local.
2. Conecte o Azure Resource Manager usando [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount).
3. Baixe, da conta de Automação na qual você está configurando nós, as metaconfigurações de DSC do PowerShell para as máquinas que você deseja habilitar.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. Agora você deve ter uma pasta **DscMetaConfigs** que contém as metaconfigurações de DSC do PowerShell para os computadores a habilitar (como um administrador).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="enable-machines-securely-using-registration"></a>Habilitar computadores com segurança usando o registro

Você pode habilitar computadores com segurança para uma conta de Automação do Azure por meio do protocolo de registro de DSC do WMF 5. Esse protocolo permite que um nó DSC autentique em um servidor de pull ou relatório DSC do PowerShell, incluindo o State Configuration da Automação do Azure. O nó é registrado no servidor na URL de registro e autenticado usando uma chave de registro. Durante o registro, o nó DSC e o servidor de pull/relatórios DSC negociam um certificado exclusivo para esse nó a ser usado para a autenticação do servidor após o registro. Esse processo impede que nós habilitados representem um ao outro, por exemplo, se um nó for comprometido e estiver se comportando de maneira mal-intencionada. Após o registro, a chave de registro não é usada para autenticação novamente e é excluída do nó.

Você pode obter as informações necessárias para o protocolo de registro da Configuração do Estado a partir das **Chaves** nas **Configurações de Conta** no portal do Azure. 

![Chaves de automação do Azure e a URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- A URL de registro é o campo de URL na página Chaves.
- A chave de registro é o valor do campo **Chave de acesso primário** ou **Chave de acesso secundário** na página Chaves. Qualquer chave pode ser usada.

Para maior segurança, você pode gerar novamente as chaves de acesso primário e secundário de uma conta de Automação a qualquer momento na página Chaves. A nova geração de chave impede que futuros registros de nós usem chaves anteriores.

## <a name="re-register-a-node"></a>Registrar um nó novamente

Depois de registrar um computador como um nó DSC no State Configuration da Automação do Azure, há vários motivos pelos quais você pode precisar registrar novamente esse nó no futuro.

- **Renovação de certificado.** Para versões do Windows Server anteriores ao Windows Server 2019, cada nó negocia automaticamente um certificado exclusivo para autenticação que expira após um ano. Se um certificado expirar sem renovação, o nó não poderá se comunicar com a Automação do Azure e será marcado como `Unresponsive`. Atualmente, o protocolo de registro DSC do PowerShell não pode renovar automaticamente certificados quando eles estão prestes a expirar, após um ano é preciso registrar os nós novamente. Antes de registrar novamente, verifique se cada nó está executando o WMF 5 RTM. 

    A realização de um novo registro a 90 dias ou menos do tempo de expiração do certificado, ou a qualquer momento após o tempo de expiração do certificado, resulta na geração e uso de um novo certificado. Uma solução para esse problema está incluída no Windows Server 2019 e posterior.

- **Alterações nos valores de LCM de DSC.** Pode ser necessário alterar os [valores de LCM de DSC do PowerShell](/powershell/scripting/dsc/managing-nodes/metaConfig4) definidos durante o registro inicial do nó, por exemplo, `ConfigurationMode`. Atualmente, você só pode alterar esses valores de agente de DSC através do novo registro. A única exceção é o valor Configuração do nó atribuído ao nó. Você pode alterar isso no DSC de Automação do Azure diretamente.

Você pode registrar novamente um nó, assim como o registrou inicialmente, usando qualquer um dos métodos descritos neste documento. Você não precisa cancelar o registro de um nó do State Configuration da Automação do Azure antes de registrá-lo novamente.

## <a name="check-status-of-vm-setup"></a>Verificar o status da instalação da VM

O State Configuration permite habilitar facilmente as VMs do Windows Azure para gerenciamento de configuração. Nos bastidores, a extensão de Configuração de Estado Desejado da VM do Azure é usada para registrar a VM com a Configuração do Estado de Automação do Azure. Como a extensão Desired State Configuration da VM do Azure é executada de forma assíncrona, pode ser importante acompanhar seu progresso e solucionar problemas de sua execução.

> [!NOTE]
> Qualquer método para habilitar VMs do Windows do Azure para o State Configuration que usa a extensão Desired State Configuration da VM do Azure pode levar até uma hora para que a Automação do Azure mostre as VMs como registradas. Esse atraso ocorre devido à instalação do WMF 5 na VM pela extensão Desired State Configuration da VM do Azure, necessária para habilitar as VMs para o State Configuration.

Para exibir o status da extensão Desired State Configuration da VM do Azure:

1. No portal do Azure, navegue até a VM que está sendo habilitada.
2. Clique em **Extensões**, em **Configurações**. 
3. Agora, selecione **DSC** ou **DSCForLinux** dependendo do seu sistema operacional. 
4. Para obter mais detalhes, você pode clicar em **Exibir status detalhado**.

## <a name="next-steps"></a>Próximas etapas

- Para começar, confira [Introdução ao State Configuration da Automação do Azure](automation-dsc-getting-started.md).
- Para saber como compilar configurações DSC para que possam ser atribuídas a nós de destino, confira [Compilar configurações DSC no State Configuration da Automação do Azure](automation-dsc-compile.md).
- Para obter uma referência de cmdlet do PowerShell, confira [Az.Automation](/powershell/module/az.automation).
- Para saber mais sobre preços, confira [Preços do State Configuration da Automação do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para ver um exemplo de uso do State Configuration da Automação do Azure em um pipeline de implantação contínua, confira [Configurar a implantação contínua com o Chocolatey](automation-dsc-cd-chocolatey.md).
- Para saber mais sobre a solução de problemas, confira [Solucionar problemas do State Configuration da Automação do Azure](./troubleshoot/desired-state-configuration.md).
