---
title: Integrar computadores para gerenciamento por Configuração de Estado da Automação do Azure
description: Como configurar computadores para gerenciamento com a Configuração de Estado da Automação do Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.topic: conceptual
ms.date: 08/08/2018
manager: carmonm
ms.openlocfilehash: b81dccf3c71fbaff7b0cbb9ba00f13b458156e73
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61074458"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Integrar computadores para gerenciamento por Configuração de Estado da Automação do Azure

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Por que gerenciar máquinas com a Configuração do Estação de Automação do Azure?

Configuração de estado de automação do Azure é um serviço de gerenciamento de configuração para nós de DSC em qualquer nuvem ou data center local.
Ela permite a escalabilidade entre milhares de máquinas rápida e facilmente a partir de um local central e seguro.
Você pode, facilmente, integrar máquinas, atribuir a elas configurações declarativas e exibir relatórios que mostram a conformidade de cada computador com o estado desejado especificado.
O serviço de configuração de estado de automação do Azure é DSC o que são runbooks de automação do Azure para scripts do PowerShell.
Em outras palavras, da mesma forma que a Automação do Azure ajuda você a gerenciar os scripts do PowerShell, ela também ajuda a gerenciar configurações do DSC.
Para saber mais sobre os benefícios de usar a Configuração de Estado de Automação do Azure, consulte [Visão geral da Configuração de Estado de Automação do Azure](automation-dsc-overview.md).

A Configuração do Estado de Automação do Azure pode ser usada para gerenciar uma variedade de máquinas:

- Máquinas virtuais do Azure
- Máquinas virtuais do Azure (clássico)
- Instâncias de EC2 da Amazon Web Services (AWS)
- Máquinas físicas/virtuais locais do Windows, ou em uma nuvem diferente do Azure/AWS
- Máquinas físicas/virtuais locais do Linux, no Azure, ou em uma nuvem diferente do Azure

Além disso, se você não estiver pronto para gerenciar a configuração da máquina a partir da nuvem, a Configuração do Estado de Automação do Azure também poderá ser usado como um ponto de extremidade apenas para relatório.
Isso permite que você defina configurações de (push) por meio do DSC e exibir relatórios de detalhes na automação do Azure.

> [!NOTE]
> Gerenciar VMs do Azure com a Configuração do Estado está incluído sem custo adicional, se a extensão de DSC da máquina virtual instalada for maior que 2.70. Consulte a [**página de preços de Automação**](https://azure.microsoft.com/pricing/details/automation/) para obter mais detalhes.

As seções a seguir descrevem como você pode integrar cada tipo de máquina à Configuração do Estado de Automação do Azure.

## <a name="azure-virtual-machines"></a>Máquinas virtuais do Azure

A Configuração do Estado de Automação do Azure permite que você integre facilmente máquinas virtuais do Azure para o gerenciamento de configuração, usando o portal do Azure, os modelos do Azure Resource Manager ou o PowerShell. Nos bastidores e sem um administrador precisar acessar remotamente a VM, a extensão de Configuração de Estado Desejado da VM do Azure registra a VM com a Configuração do Estado de Automação do Azure.
Como a extensão da Configuração de Estado Desejado da VM do Azure é executada de forma assíncrona, as etapas para acompanhar seu andamento ou para resolver problemas nele são fornecidas na seção [**Solução de problemas de integração de máquina virtual do Azure**](#troubleshooting-azure-virtual-machine-onboarding) a seguir.

### <a name="azure-portal"></a>Portal do Azure

No [Portal do Azure](https://portal.azure.com/), navegue até a conta da Automação do Azure na qual você deseja carregar as máquinas virtuais. Na página de Configuração Estado e na guia **Nós**, clique em **+ Adicionar**.

Selecione uma máquina virtual do Azure para carregar.

Se o computador não tiver a extensão de estado desejado do PowerShell instalada e o estado de energia estiver em execução, clique em **Conectar**.

Em **Registro**, digite os [valores do Gerenciador de Configuração Local de DSC do PowerShell](/powershell/dsc/metaconfig4) necessários para o seu caso de uso e, opcionalmente, uma configuração de nó para atribuir à VM.

![integração](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

Máquinas virtuais do Azure podem ser implantadas e integradas à Configuração do Estado de Automação do Azure por meio de modelos do Gerenciador do Azure Resource Manager. Ver [servidor gerenciado pelo serviço de Desired State Configuration](https://azure.microsoft.com/en-us/resources/templates/101-automation-configuration/) para um modelo de exemplo que integra uma VM existente para a configuração de estado de automação do Azure.
Se você estiver gerenciando um conjunto de dimensionamento de máquinas virtuais, consulte o modelo de exemplo [configuração de conjunto de dimensionamento de VM gerenciada pela automação do Azure](https://azure.microsoft.com/en-us/resources/templates/201-vmss-automation-dsc/).

### <a name="powershell"></a>PowerShell

O cmdlet [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) pode ser usado para integrar máquinas virtuais ao Portal do Azure por meio do PowerShell.

### <a name="registering-virtual-machines-across-azure-subscriptions"></a>Registrando as máquinas virtuais entre assinaturas do Azure

A melhor maneira de registrar as máquinas virtuais a partir de outras assinaturas do Azure é usar a extensão de DSC em um modelo de implantação do Azure Resource Manager.
Exemplos são fornecidos nas [extensão de Desired State Configuration com modelos do Azure Resource Manager](https://docs.microsoft.com/en-us/azure/virtual-machines/extensions/dsc-template).
Para localizar a chave de registro e a URL de registro para usar como parâmetros no modelo, consulte o seguinte [ **proteger o registro** ](#secure-registration) seção.

## <a name="azure-virtual-machines-classic"></a>Máquinas virtuais do Azure (clássico)

Com a Configuração do Estado de Automação do Azure, você pode facilmente carregar máquinas virtuais do Azure (clássico) para gerenciamento de configuração usando o portal do Azure ou o PowerShell. Nos bastidores e sem um administrador precisar acessar remotamente a VM, a extensão de Configuração de Estado Desejado da VM do Azure registra a VM com a Configuração do Estado de Automação do Azure.
Etapas para acompanhar o progresso ou resolver problemas nele são fornecidas a seguir [ **integração de máquina virtual do Azure de solução de problemas** ](#troubleshooting-azure-virtual-machine-onboarding) seção.

### <a name="azure-portal-classic-virtual-machines"></a>Portal do Azure (máquinas virtuais clássicas)

No [portal do Azure](https://portal.azure.com/), clique em **Procurar** -> **Máquinas virtuais (clássicas)**. Selecione a VM do Windows que você deseja integrar. Na folha do painel da máquina virtual, clique em **Todas as configurações** -> **Extensões** -> **Adicionar** -> **DSC de Automação do Azure** -> **Criar**.
Insira o [valores do Gerenciador de configurações de Local de DSC do PowerShell](/powershell/dsc/metaconfig4) para chave de registro de sua conta da automação e URL de registro e, opcionalmente, uma configuração de nó atribuir à VM.

![Extensões de VM para DSC](./media/automation-dsc-onboarding/DSC_Onboarding_1.png)

Para encontrar a URL de registro e a chave da conta da Automação a ser integrada ao computador, confira a seção [**Proteger o registro**](#secure-registration) a seguir:

### <a name="powershell-classic-virtual-machines"></a>PowerShell (máquinas virtuais clássicas)

```powershell
# log in to both Azure Service Management and Azure Resource Manager
Add-AzureAccount
Connect-AzureRmAccount

# fill in correct values for your VM/Automation account here
$VMName = ''
$ServiceName = ''
$AutomationAccountName = ''
$AutomationAccountResourceGroup = ''

# fill in the name of a Node Configuration in Azure Automation State Configuration, for this VM to conform to
# NOTE: DSC Node Configuration names are case sensitive in the portal.
$NodeConfigName = ''

# get Azure Automation State Configuration registration info
$Account = Get-AzureRmAutomationAccount -ResourceGroupName $AutomationAccountResourceGroup -Name $AutomationAccountName
$RegistrationInfo = $Account | Get-AzureRmAutomationRegistrationInfo

# use the DSC extension to onboard the VM for management with Azure Automation State Configuration
$VM = Get-AzureVM -Name $VMName -ServiceName $ServiceName

$PublicConfiguration = ConvertTo-Json -Depth 8 @{
    SasToken = ''
    ModulesUrl = 'https://eus2oaasibizamarketprod1.blob.core.windows.net/automationdscpreview/RegistrationMetaConfigV2.zip'
    ConfigurationFunction = 'RegistrationMetaConfigV2.ps1\RegistrationMetaConfigV2'

# update these PowerShell DSC Local Configuration Manager defaults if they do not match your use case.
# See https://docs.microsoft.com/powershell/dsc/metaConfig for more details
    Properties = @{
        RegistrationKey = @{
            UserName = 'notused'
            Password = 'PrivateSettingsRef:RegistrationKey'
        }
        RegistrationUrl = $RegistrationInfo.Endpoint
        NodeConfigurationName = $NodeConfigName
        ConfigurationMode = 'ApplyAndMonitor'
        ConfigurationModeFrequencyMins = 15
        RefreshFrequencyMins = 30
        RebootNodeIfNeeded = $False
        ActionAfterReboot = 'ContinueConfiguration'
        AllowModuleOverwrite = $False
    }
}

$PrivateConfiguration = ConvertTo-Json -Depth 8 @{
    Items = @{
        RegistrationKey = $RegistrationInfo.PrimaryKey
    }
}

$VM = Set-AzureVMExtension `
    -VM $vm `
    -Publisher Microsoft.Powershell `
    -ExtensionName DSC `
    -Version 2.76 `
    -PublicConfiguration $PublicConfiguration `
    -PrivateConfiguration $PrivateConfiguration `
    -ForceUpdate

$VM | Update-AzureVM
```

> [!NOTE]
> Nomes de Configuração de Nó do Estado diferenciam maiúsculas de minúsculas no portal. Se o caractere for incompatível o nó não aparecerá na guia **Nós**.

## <a name="amazon-web-services-aws-virtual-machines"></a>Máquinas virtuais do AWS (Amazon Web Services)

Você pode facilmente integrar máquinas virtuais do Amazon Web Services para o gerenciamento de configuração pela Configuração do Estado de Automação do Azure usando o Kit de Ferramentas AWS DSC. Você pode saber mais sobre esse kit de ferramentas [aqui](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/).

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>Máquinas físicas/virtuais locais do Windows, ou em uma nuvem diferente do Azure/AWS

Servidores Windows em execução no local ou em outros ambientes de nuvem também pode ser integrado à configuração de estado de automação do Azure, desde que eles tenham acesso de saída para o Azure:

1. Verifique se a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) está instalada nos computadores que você deseja integrar à Configuração do Estado de Automação do Azure.
1. Siga as instruções na seção [**Gerando metaconfigurações DSC**](#generating-dsc-metaconfigurations) a seguir para gerar uma pasta com as metaconfigurações de DSC necessárias.
1. Aplique-se remotamente a metaconfiguração do DSC do PowerShell às máquinas que você deseja carregar. **O computador no qual este comando é executado deve ter a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) instalada**:

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Se você não pode aplicar as metaconfigurações do DSC do PowerShell remotamente, copie a pasta de metaconfigurações da etapa 2 em cada computador para carregar. Ligue para **Set-DscLocalConfigurationManager** localmente em cada computador para carregar.
1. Usando o portal do Azure ou os cmdlets, verifique se as máquinas para carregar agora aparecem como nós da Configuração do Estado registrados em sua conta de Automação do Azure.

## <a name="physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>No local, de máquinas físicas/virtuais Linux ou em uma nuvem diferente do Azure

Servidores Linux em execução no local ou em outros ambientes de nuvem também pode ser integrado à configuração de estado de automação do Azure, desde que eles tenham acesso de saída para o Azure:

1. Certifique-se de que a versão mais recente da [PowerShell Desired State Configuration for Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) está instalada nos computadores que você deseja integrar à Configuração do Estado de Automação do Azure.
1. Se o [padrões do Gerenciador de Configurações Local do DSC do PowerShell](/powershell/dsc/metaconfig4) corresponde a seu caso de uso e você deseja integrar computadores de modo que como que eles **ambos** efetuem pull e gerem relatório para a Configuração do Estado de Automação do Azure:

   - Em cada computador Linux em que será carregada a Configuração de Estado de Automação do Azure, use `Register.py` para carregar usando os padrões do Gerenciador de Configurações Local do DSC do PowerShell:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Para encontrar a chave de registro e a URL de registro de sua conta da Automação, confira a seção [**Proteger o registro**](#secure-registration) a seguir.

     Se o Gerenciador de configurações de Local de DSC do PowerShell padrão é **não** corresponde ao seu caso de uso, ou você deseja integrar computadores, de modo que eles só reportem para configuração de estado de automação do Azure, siga as etapas 3 a 6. Caso contrário, vá diretamente para a etapa 6.

1. Siga as instruções na seção [**Gerando metaconfigurações DSC**](#generating-dsc-metaconfigurations) a seguir para gerar uma pasta com as metaconfigurações de DSC necessárias.
1. Aplique remotamente a metaconfiguração do DSC do PowerShell às máquinas que você deseja carrega:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

O computador no qual este comando é executado deve ter a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) instalada.

1. Se você não pode aplicar as metaconfigurações de DSC do PowerShell remotamente, copie a metaconfiguração correspondente a esse computador a partir da pasta na etapa 5 no computador Linux. Em seguida, chame `SetDscLocalConfigurationManager.py` localmente em cada computador com Linux que deseja integrar à Configuração do Estado de Automação do Azure:

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. Usando o portal do Azure ou os cmdlets, verifique se as máquinas para carregar agora aparecem como nós DSC registrados em sua conta de Automação do Azure.

## <a name="generating-dsc-metaconfigurations"></a>Gerando metaconfigurações DSC

Para carregar genericamente qualquer computador para a configuração de estado de automação do Azure, uma [metaconfiguração de DSC](/powershell/dsc/metaconfig) pode ser gerado que informa o agente de DSC para efetuar pull de e/ou relatar para configuração de estado de automação do Azure. As metaconfigurações da Configuração do Estado para o DSC de Automação do Azure podem ser geradas usando uma configuração de DSC do PowerShell, ou os cmdlets do PowerShell de Automação do Azure.

> [!NOTE]
> Metaconfigurações DSC contêm os segredos necessários para carregar um computador em uma conta de Automação para gerenciamento. Certifique-se de proteger corretamente quaisquer metaconfigurações de DSC que criar, ou exclua-as imediatamente após o uso.

### <a name="using-a-dsc-configuration"></a>Usando uma Configuração de DSC

1. Abra o VSCode (ou seu editor favorito) como administrador em um computador no seu ambiente local. O computador também deve ter a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) instalada.
1. Compie o script a seguir localmente. Esse script contém uma configuração DSC do PowerShell para criar metaconfigurações e um comando para dar início à criação de metaconfigurações.

> [!NOTE]
> Nomes de Configuração de Nó do Estado diferenciam maiúsculas de minúsculas no portal. Se o caractere for incompatível o nó não aparecerá na guia **Nós**.

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

1. Preencha a chave de registro e a URL para sua conta de Automação, bem como os nomes dos computadores a carregar. Todos os outros parâmetros são opcionais. Para encontrar a chave de registro e a URL de registro de sua conta da Automação, confira a seção [**Proteger o registro**](#secure-registration) a seguir.
1. Se você deseja que as máquinas relatem informações de status de DSC à Configuração do Estado de Automação do Azure, mas não efetue pull da configuração de recepção ou módulos do PowerShell, defina o parâmetro **ReportOnly** como verdadeiro.
1. Execute o script. Agora você deve ter uma pasta chamada **DscMetaConfigs** no diretório de trabalho, que contém as metaconfigurações de DSC do PowerShell para os computadores a carregar (como um administrador):

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Usando os cmdlets da Automação do Azure

Se o padrões do Gerenciador de Configurações Local do DSC do PowerShell correspondem a seu caso de uso e você deseja integrar computadores de modo que eles ambos efetuem pull e gerem relatório para a Configuração do Estado de Automação do Azure, os cmdlets de Automação do Azure fornecem um método simplificado para gerar as metaconfigurações de DSC necessárias:

1. Abra o console do PowerShell ou o VSCode como administrador em uma máquina no seu ambiente local.
2. Conecte-se ao Azure Resource Manager usando `Connect-AzureRmAccount`
3. Baixe, da conta de Automação da qual você deseja carregar nós, as metaconfigurações do DSC do PowerShell para as máquinas que você deseja carregar:

   ```powershell
   # Define the parameters for Get-AzureRmAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation Account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzureRmAutomationDscOnboardingMetaconfig @Params
   ```

1. Agora você deve ter uma pasta chamada ***DscMetaConfigs***que contém as metaconfigurações de DSC do PowerShell para os computadores a carregar (como um administrador):

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Proteger o registro

Os computadores podem ser carregados com segurança a uma conta de Automação do Azure por meio do protocolo de registro de WMF 5 DSC, que permite a um nó DSC se autenticar a um servidor de Recepção ou de Relatórios do DSC do PowerShell (incluindo a Configuração do Estado de Automação do Azure). O nó é registrado no servidor em uma **URL de Registro**, autenticando por meio de uma **Chave de registro**. Durante o registro, o nó de DSC e o servidor de Recepção/Relatórios DSC negociam um certificado exclusivo para este nó a ser usado para a autenticação do servidor após o registro. Esse processo impede que nós integrados representem um ao outro, por exemplo, se um nó for comprometido e estiver se comportando de maneira mal-intencionada. Após o registro, a Chave de registro não é usada para autenticação novamente e é excluída do nó.

Você pode obter as informações necessárias para o protocolo de registro da Configuração do Estado a partir das **Chaves** nas **Configurações de Conta**no portal do Azure. Abra a folha clicando no ícone de chave no painel **Noções Básicas** da conta da Automação.

![Chaves de automação do Azure e a URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- A URL de registro é o campo de URL na folha Gerenciar chaves.
- A chave de registro é a Chave de Acesso Primária ou Chave de Acesso Secundária na folha Gerenciar chaves. Qualquer chave pode ser usada.

Para aumentar a segurança, as chaves de acesso primária e secundária de uma conta de Automação podem ser regeneradas a qualquer momento (na página **Gerenciar chaves** ) para impedir que os registros de nós futuros usem chaves anteriores.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Solução de problemas de integração de máquina virtual do Azure

O Configuração do Estado de Automação do Azure permite a fácil integração de VMs do Microsoft Azure ao gerenciamento de configuração. Nos bastidores, a extensão de Configuração de Estado Desejado da VM do Azure é usada para registrar a VM com a Configuração do Estado de Automação do Azure. Como a extensão de Configuração de Estado Desejado da VM do Azure é executada de forma assíncrona, acompanhar o seu progresso e solucionar os problemas de sua execução pode ser importante.

> [!NOTE]
> Qualquer método de integração de uma VM do Azure à Configuração do Estado de Automação do Azure que usa a extensão de Configuração de Estado Desejado da VM do Azure pode levar até uma hora para o nó mostrar como registrado na Automação do Azure. Isso se deve à instalação do Windows Management Framework 5.0 na VM pela extensão DSC da VM do Azure, que precisa integrar a VM à Configuração do Estado de Automação do Azure.

Para solucionar problemas ou exibir o status da extensão de Configuração de Estado Desejado da VM do Azure, no portal do Azure, navegue até a VM que está sendo integrada e clique em **Extensões** em **Configurações**. Em seguida, clique em **DSC** ou **DSCForLinux** dependendo do seu sistema operacional. Para obter mais detalhes, você pode clicar em **Exibir status detalhado**.

## <a name="certificate-expiration-and-reregistration"></a>Expiração do certificado e um novo registro

Depois de registrar uma máquina como um nó DSC na Configuração do Estado de Automação do Azure, há vários motivos para você precisar registrar novamente o nó no futuro:

- Após o registro, cada nó negocia automaticamente um certificado exclusivo para autenticação, que expira depois de um ano. Atualmente, o protocolo de registro DSC do PowerShell não pode renovar automaticamente certificados quando eles estão prestes a expirar, então você precisa registrar novamente os nós após um ano. Antes de registrar novamente, certifique-se de que cada nó está executando o Windows Management Framework 5.0 RTM. Se o certificado de autenticação de um nó expirar e o nó não for registrado novamente, o nó não será capaz de se comunicar com a Automação do Azure e será marcado como ‘Sem resposta’. A realização de um novo registro a 90 dias ou menos do tempo de expiração do certificado, ou a qualquer momento após o tempo de expiração do certificado, vai resultar na geração e uso de um novo certificado.
- Para alterar quaisquer [valores do Gerenciador de Configuração Local do PowerShell DSC](/powershell/dsc/metaconfig4) que foram definidos durante o registro inicial do nó, como ConfigurationMode. Atualmente, esses valores de agente do DSC só podem ser alterados por meio de um novo registro. A única exceção é a Configuração de Nó atribuída ao nó, isso pode ser alterado diretamente no DSC de Automação do Azure.

Um novo registro pode ser executado da mesma maneira que você registrou o nó inicialmente, usando qualquer um dos métodos de integração descritos neste documento. Você não precisa cancelar o registro de um nó da Configuração do Estado de Automação do Azure antes de registrá-lo novamente.

## <a name="next-steps"></a>Próximas etapas

- Para começar, consulte [Introdução à Configuração de Estado da Automação do Azure](automation-dsc-getting-started.md)
- Para saber como compilar configurações de DSC para que possam ser atribuídas a nós de destino, consulte [Compilar configurações na Configuração de Estado da Automação do Azure](automation-dsc-compile.md)
- Para referência de cmdlet do PowerShell, consulte [Cmdlets da Configuração de Estado da Automação do Azure](/powershell/module/azurerm.automation/#automation)
- Para obter informações sobre preços, consulte [Preço da Configuração de Estado da Automação do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo de uso da Configuração de Estado da Automação do Azure em um pipeline de implantação contínua, consulte [Implantação contínua usando Configuração de Estado da Automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md)
