---
title: "Máquinas de integração para o gerenciamento pelo DSC de Automação do Azure | Microsoft Docs"
description: "Como configurar computadores para o gerenciamento com o DSC de Automação do Azure"
services: automation
documentationcenter: dev-center-name
author: eslesar
manager: carmonm
ms.assetid: da13e1f5-2a1c-443b-8e3b-9f0d6f9e4810
ms.service: automation
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: TBD
ms.date: 12/13/2016
ms.author: eslesar
ms.openlocfilehash: 1a6355c18aed8a4040121e5af482328b70294b83
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="onboarding-machines-for-management-by-azure-automation-dsc"></a>Máquinas de integração para o gerenciamento pelo DSC de Automação do Azure

## <a name="why-manage-machines-with-azure-automation-dsc"></a>Por que gerenciar máquinas com o DSC de Automação do Azure?

Como a [Configuração de Estado Desejado do PowerShell](https://technet.microsoft.com/library/dn249912.aspx), a Configuração de Estado Desejado da Automação do Azure é um serviço de gerenciamento de configuração simples, mas poderoso, para nós DSC (máquinas físicas e virtuais) em qualquer datacenter de nuvem ou no local. Ela permite a escalabilidade entre milhares de máquinas rápida e facilmente a partir de um local central e seguro. Você pode, facilmente, integrar máquinas, atribuir a elas configurações declarativas e exibir relatórios que mostram a conformidade de cada computador com o estado desejado especificado. A camada de gerenciamento do DSC de Automação do Azure é para o DSC o que a camada de gerenciamento de Automação do Azure é para o script do PowerShell. Em outras palavras, da mesma forma que a Automação do Azure ajuda você a gerenciar os scripts do PowerShell, ela também ajuda a gerenciar configurações do DSC. Para saber mais sobre os benefícios do uso do DSC de Automação do Azure, consulte [Visão geral do DSC de Automação do Azure](automation-dsc-overview.md).

O DSC de Automação do Azure pode ser usado para gerenciar uma variedade de máquinas:

* Máquinas virtuais do Azure (clássico)
* Máquinas virtuais do Azure
* Máquinas virtuais do AWS (Amazon Web Services)
* Máquinas físicas/virtuais locais do Windows, ou em uma nuvem diferente do Azure/AWS
* Máquinas físicas/virtuais locais do Linux, no Azure, ou em uma nuvem diferente do Azure

Além disso, se você não estiver pronto para gerenciar a configuração da máquina a partir da nuvem, o DSC da Automação do Azure também poderá ser usado como um ponto de extremidade apenas para relatório. Isso permite que você defina a configuração desejada (push) por meio do DSC local e exiba relatórios detalhados sobre a conformidade de nó com o estado desejado na Automação do Azure.

> [!NOTE]
> Gerenciar VMs do Azure com DSC é incluído sem custo adicional, se a extensão de DSC da máquina virtual instalada é maior que 2.7.  Consulte a [**página de preços de Automação**](https://azure.microsoft.com/en-us/pricing/details/automation/) para obter mais detalhes.


As seções a seguir descrevem como você pode integrar cada tipo de máquina ao DSC de Automação do Azure.

## <a name="azure-virtual-machines-classic"></a>Máquinas virtuais do Azure (clássico)

Com o DSC de Automação do Azure, você pode facilmente carregar máquinas virtuais do Azure (clássico) para gerenciamento de configuração usando o portal do Azure ou o PowerShell. Nos bastidores e sem um administrador precisar acessar remotamente a VM, a extensão de Configuração de Estado Desejado da VM do Azure registra a VM com o DSC de Automação do Azure. Como a extensão da Configuração de Estado Desejado da VM do Azure é executada de forma assíncrona, as etapas para acompanhar seu andamento ou para resolver problemas nele são fornecidas na seção [**Solução de problemas de integração de máquina virtual do Azure**](#troubleshooting-azure-virtual-machine-onboarding) abaixo.

### <a name="azure-portal"></a>Portal do Azure

No [portal do Azure](http://portal.azure.com/), clique em **Procurar** -> **Máquinas virtuais (clássicas)**. Selecione a VM do Windows que você deseja integrar. Na folha do painel da máquina virtual, clique em **Todas as configurações** -> **Extensões** -> **Adicionar** -> **DSC de Automação do Azure** -> **Criar**. Insira os [Valores do Gerenciador de Configurações Local de DSC do PowerShell](https://msdn.microsoft.com/powershell/dsc/metaconfig4) necessários para o seu caso de uso, a chave de registro e a URL de registro de sua conta da Automação e, opcionalmente, uma configuração de nó para atribuir à VM.

![](./media/automation-dsc-onboarding/DSC_Onboarding_1.png)

Para encontrar a URL de registro e a chave da conta da Automação a ser integrada ao computador, confira a seção [**Proteger o registro**](#secure-registration) abaixo.

### <a name="powershell"></a>PowerShell

```powershell
# log in to both Azure Service Management and Azure Resource Manager
Add-AzureAccount
Add-AzureRmAccount

# fill in correct values for your VM/Automation account here
$VMName = ""
$ServiceName = ""
$AutomationAccountName = ""
$AutomationAccountResourceGroup = ""

# fill in the name of a Node Configuration in Azure Automation DSC, for this VM to conform to
$NodeConfigName = ""

# get Azure Automation DSC registration info
$Account = Get-AzureRmAutomationAccount -ResourceGroupName $AutomationAccountResourceGroup -Name $AutomationAccountName
$RegistrationInfo = $Account | Get-AzureRmAutomationRegistrationInfo

# use the DSC extension to onboard the VM for management with Azure Automation DSC
$VM = Get-AzureVM -Name $VMName -ServiceName $ServiceName

$PublicConfiguration = ConvertTo-Json -Depth 8 @{
    SasToken = ""
    ModulesUrl = "https://eus2oaasibizamarketprod1.blob.core.windows.net/automationdscpreview/RegistrationMetaConfigV2.zip"
    ConfigurationFunction = "RegistrationMetaConfigV2.ps1\RegistrationMetaConfigV2"

# update these PowerShell DSC Local Configuration Manager defaults if they do not match your use case.
# See https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396 for more details
    Properties = @{
    RegistrationKey = @{
        UserName = 'notused'
        Password = 'PrivateSettingsRef:RegistrationKey'
    }
    RegistrationUrl = $RegistrationInfo.Endpoint
    NodeConfigurationName = $NodeConfigName
    ConfigurationMode = "ApplyAndMonitor"
    ConfigurationModeFrequencyMins = 15
    RefreshFrequencyMins = 30
    RebootNodeIfNeeded = $False
    ActionAfterReboot = "ContinueConfiguration"
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
    -Version 2.19 `
    -PublicConfiguration $PublicConfiguration `
    -PrivateConfiguration $PrivateConfiguration `
    -ForceUpdate

$VM | Update-AzureVM
```

## <a name="azure-virtual-machines"></a>Máquinas virtuais do Azure

O DSC de Automação do Azure permite que você integre facilmente máquinas virtuais do Azure para o gerenciamento de configuração, usando o portal do Azure, os modelos do Gerenciador de Recursos do Azure ou o PowerShell. Nos bastidores e sem um administrador precisar acessar remotamente a VM, a extensão de Configuração de Estado Desejado da VM do Azure registra a VM com o DSC de Automação do Azure. Como a extensão da Configuração de Estado Desejado da VM do Azure é executada de forma assíncrona, as etapas para acompanhar seu andamento ou para resolver problemas nele são fornecidas na seção [**Solução de problemas de integração de máquina virtual do Azure**](#troubleshooting-azure-virtual-machine-onboarding) abaixo.

### <a name="azure-portal"></a>Portal do Azure

No [Portal do Azure](https://portal.azure.com/), navegue até a conta da Automação do Azure na qual você deseja carregar as máquinas virtuais. No painel da conta de Automação, clique em **Nós DSC** -> **Adicionar VM do Azure**.

Selecione uma máquina virtual do Azure para carregar.

Se o computador não tiver a extensão de estado desejado do PowerShell instalada e o estado de energia estiver em execução, clique em **Conectar**.

Em **Registro**, digite os [valores do Gerenciador de Configuração Local de DSC do PowerShell](https://msdn.microsoft.com/powershell/dsc/metaconfig4) necessários para o seu caso de uso e, opcionalmente, uma configuração de nó para atribuir à VM.

![](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Modelos do Gerenciador de Recursos do Azure

Máquinas virtuais do Azure podem ser implantadas e integradas ao DSC de Automação do Azure por meio de modelos do Gerenciador de Recursos do Azure. Veja [Configurar uma VM por meio da extensão de DSC e da DSC de Automação do Azure](https://azure.microsoft.com/documentation/templates/dsc-extension-azure-automation-pullserver/) para obter um modelo de exemplo que integra uma VM existente à DSC de Automação do Azure. Para encontrar a chave de registro e a URL de registro adotadas como entrada neste modelo, confira a seção [**Proteger o registro**](#secure-registration) abaixo.

### <a name="powershell"></a>PowerShell

O cmdlet [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) pode ser usado para integrar máquinas virtuais ao Portal do Azure por meio do PowerShell.

## <a name="amazon-web-services-aws-virtual-machines"></a>Máquinas virtuais do AWS (Amazon Web Services)

Você pode facilmente integrar máquinas virtuais do Amazon Web Services para o gerenciamento de configuração pelo DSC de Automação do Azure usando o Kit de Ferramentas AWS DSC. Você pode saber mais sobre esse kit de ferramentas [aqui](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/).

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>Máquinas físicas/virtuais locais do Windows, ou em uma nuvem diferente do Azure/AWS

As máquinas do Windows locais e as máquinas do Windows em nuvens que não são do Azure (como o Amazon Web Services) também podem ser integradas ao DSC de Automação do Azure, desde que eles tenham acesso de saída à Internet, através de algumas etapas simples:

1. Verifique se a versão mais recente do [WMF 5](http://aka.ms/wmf5latest) está instalada nos computadores que você deseja integrar ao DSC de Automação do Azure.
2. Siga as instruções na seção [**Gerando metaconfigurações DSC**](#generating-dsc-metaconfigurations) abaixo para gerar uma pasta com as metaconfigurações de DSC necessárias.
3. Aplique-se remotamente a metaconfiguração do DSC do PowerShell às máquinas que você deseja carregar. **O computador no qual este comando é executado deve ter a versão mais recente do [WMF 5](http://aka.ms/wmf5latest) instalada**:

    ```powershell
    Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
    ```

4. Se você não pode aplicar as metaconfigurações do DSC do PowerShell remotamente, copie a pasta de metaconfigurações da etapa 2 em cada computador para carregar. Ligue para **Set-DscLocalConfigurationManager** localmente em cada computador para carregar.
5. Usando o portal do Azure ou os cmdlets, verifique se as máquinas para carregar agora aparecem como nós DSC registrados em sua conta de Automação do Azure.

## <a name="physicalvirtual-linux-machines-on-premises-in-azure-or-in-a-cloud-other-than-azure"></a>Máquinas físicas/virtuais locais do Linux, no Azure, ou em uma nuvem diferente do Azure

Os computadores com Linux locais, computadores com Linux no Azure e os computadores com Linux em nuvens que não são do Azure também podem ser integrados ao DSC de Automação do Azure, desde que tenham acesso de saída à Internet, por meio de algumas etapas simples:

1. Certifique-se de que a versão mais recente da [PowerShell Desired State Configuration for Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) está instalada nos computadores que você deseja integrar à DSC de Automação do Azure.
2. Se o [padrões do Gerenciador de Configurações Local do DSC do PowerShell](https://msdn.microsoft.com/powershell/dsc/metaconfig4) corresponde a seu caso de uso e você deseja integrar computadores de modo que como que eles **ambos** efetuem pull e gerem relatório para o DSC de Automação do Azure:

   + Em cada computador Linux para carregar ao DSC de Automação do Azure, use Register.py para carregar usando os padrões do Gerenciador de Configuração Local do DSC do PowerShell:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   + Para encontrar a chave de registro e a URL de registro de sua conta da Automação, confira a seção [**Proteger o registro**](#secure-registration) abaixo.

     Se os padrões do Gerenciador de Configuração Local do DSC do PowerShell **não** **correspondem** a seu caso de uso, ou você deseja carregar computadores, de modo que eles só reportem para o DSC de Automação do Azure, mas não façam configuração pull nem módulos do PowerShell com base nela, siga as etapas 3-6. Caso contrário, vá diretamente para a etapa 6.

3. Siga as instruções na seção [**Gerando metaconfigurações DSC**](#generating-dsc-metaconfigurations) abaixo para gerar uma pasta com as metaconfigurações de DSC necessárias.
4. Aplique remotamente a metaconfiguração do DSC do PowerShell às máquinas que você deseja carrega:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String "<root password>" -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential "root", $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard

    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

O computador no qual este comando é executado deve ter a versão mais recente do [WMF 5](http://aka.ms/wmf5latest) instalada.

1. Se você não pode aplicar as metaconfigurações do DSC do PowerShell remotamente, para cada computador Linux para carregar, copie a metaconfiguração correspondente a esse computador a partir da pasta na etapa 5 no computador Linux. Em seguida, chame `SetDscLocalConfigurationManager.py` localmente em cada computador com Linux que deseja integrar à DSC de Automação do Azure:

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

2. Usando o portal do Azure ou os cmdlets, verifique se as máquinas para carregar agora aparecem como nós DSC registrados em sua conta de Automação do Azure.

## <a name="generating-dsc-metaconfigurations"></a>Gerando metaconfigurações DSC

Para carregar genericamente qualquer computador ao DSC de Automação do Azure, uma [metaconfiguração de DSC](https://msdn.microsoft.com/en-us/powershell/dsc/metaconfig) pode ser gerada de modo que, quando aplicada, informa o agente do DSC no computador para efetuar pull de e/ou relatar para o DSC de Automação do Azure. As metaconfigurações de DSC para o DSC de Automação do Azure podem ser geradas usando uma configuração de DSC do PowerShell, ou os cmdlets do PowerShell de Automação do Azure.

> [!NOTE]
> Metaconfigurações DSC contêm os segredos necessários para carregar um computador em uma conta de Automação para gerenciamento. Certifique-se de proteger corretamente quaisquer metaconfigurações de DSC que criar, ou exclua-as imediatamente após o uso.

### <a name="using-a-dsc-configuration"></a>Usando uma Configuração de DSC

1. Abra o PowerShell ISE como administrador em um computador no seu ambiente local. O computador também deve ter a versão mais recente do [WMF 5](http://aka.ms/wmf5latest) instalada.
2. Compie o script a seguir localmente. Esse script contém uma configuração DSC do PowerShell para criar metaconfigurações e um comando para dar início à criação de metaconfigurações.

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

            [String]$ConfigurationMode = "ApplyAndMonitor",

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = "ContinueConfiguration",

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq "")
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
        $RefreshMode = "PUSH"
        }
        else
        {
        $RefreshMode = "PULL"
        }

        Node $ComputerName
        {

            Settings
            {
                RefreshFrequencyMins = $RefreshFrequencyMins
                RefreshMode = $RefreshMode
                ConfigurationMode = $ConfigurationMode
                AllowModuleOverwrite = $AllowModuleOverwrite
                RebootNodeIfNeeded = $RebootNodeIfNeeded
                ActionAfterReboot = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationDSC
                {
                    ServerUrl = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationDSC
                {
                ServerUrl = $RegistrationUrl
                RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationDSC
            {
                ServerUrl = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
    }

    # Create the metaconfigurations
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

3. Preencha a chave de registro e a URL para sua conta de Automação, bem como os nomes dos computadores a carregar. Todos os outros parâmetros são opcionais. Para encontrar a chave de registro e a URL de registro de sua conta da Automação, confira a seção [**Proteger o registro**](#secure-registration) abaixo.
4. Se você deseja que as máquinas relatem informações de status de DSC ao DSC de Automação do Azure, mas não efetue pull da configuração de recepção ou módulos do PowerShell, defina o parâmetro **ReportOnly** como verdadeiro.
5. Execute o script. Agora você deve ter uma pasta chamada **DscMetaConfigs** no diretório de trabalho, que contém as metaconfigurações de DSC do PowerShell para os computadores a carregar (como um administrador):

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Usando os cmdlets da Automação do Azure

Se o padrões do Gerenciador de Configurações Local do DSC do PowerShell correspondem a seu caso de uso e você deseja integrar computadores de modo que eles ambos efetuem pull e gerem relatório para o DSC de Automação do Azure, os cmdlets de Automação do Azure fornecem um método simplificado para gerar as metaconfigurações de DSC necessárias:

1. Abra o console do PowerShell ou o PowerShell ISE como administrador em uma máquina no seu ambiente local.
2. Conecte-se ao Gerenciador de Recursos do Azure usando **Add-AzureRmAccount**
3. Baixe, da conta de Automação da qual você deseja carregar nós, as metaconfigurações do DSC do PowerShell para as máquinas que você deseja carregar:

    ```powershell
    # Define the parameters for Get-AzureRmAutomationDscOnboardingMetaconfig using PowerShell Splatting
    $Params = @{

        ResourceGroupName = 'ContosoResources'; # The name of the ARM Resource Group that contains your Azure Automation Account
        AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
        ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
        OutputFolder = "$env:UserProfile\Desktop\";
    }
    # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
    # For more info about splatting, run: Get-Help -Name about_Splatting
    Get-AzureRmAutomationDscOnboardingMetaconfig @Params
    ```
    
4. Agora você deve ter uma pasta chamada ***DscMetaConfigs***que contém as metaconfigurações de DSC do PowerShell para os computadores a carregar (como um administrador):
    
    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Proteger o registro

Os computadores podem ser carregados com segurança a uma conta de Automação do Azure por meio do protocolo de registro de WMF 5 DSC, que permite a um nó DSC se autenticar a um servidor de Recepção ou de Relatórios do DSC V2 do PowerShell (incluindo o DSC de Automação do Azure). O nó é registrado no servidor em uma **URL de Registro**, autenticando por meio de uma **Chave de registro**. Durante o registro, o nó de DSC e o servidor de Recepção/Relatórios DSC negociam um certificado exclusivo para este nó a ser usado para a autenticação do servidor após o registro. Esse processo impede que nós integrados representem um ao outro, por exemplo, se um nó for comprometido e estiver se comportando de maneira mal-intencionada. Após o registro, a Chave de registro não é usada para autenticação novamente e é excluída do nó.

Você pode obter as informações necessárias para o protocolo de registro do DSC na folha **Gerenciar chaves** no portal de visualização do Azure. Abra a folha clicando no ícone de chave no painel **Noções Básicas** da conta da Automação.

![](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

* A URL de registro é o campo de URL na folha Gerenciar chaves.
* A chave de registro é a Chave de Acesso Primária ou Chave de Acesso Secundária na folha Gerenciar chaves. Qualquer chave pode ser usada.

Para aumentar a segurança, as chaves de acesso primária e secundária de uma conta de Automação podem ser regeneradas a qualquer momento (na folha **Gerenciar chaves** ) para impedir que os registros de nós futuros usem chaves anteriores.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Solução de problemas de integração de máquina virtual do Azure

O DSC de Automação do Azure permite a fácil integração de VMs do Microsoft Azure ao gerenciamento de configuração. Nos bastidores, a extensão de Configuração de Estado Desejado da VM do Azure é usada para registrar a VM com o DSC de Automação do Azure. Como a extensão de Configuração de Estado Desejado da VM do Azure é executada de forma assíncrona, acompanhar o seu progresso e solucionar os problemas de sua execução pode ser importante.

> [!NOTE]
> Qualquer método de integração de uma VM do Azure ao DSC de Automação do Azure que usa a extensão de Configuração de Estado Desejado da VM do Azure pode levar até uma hora para o nó mostrar como registrado na Automação do Azure. Isso se deve à instalação do Windows Management Framework 5.0 na VM pela extensão DSC da VM do Azure, que precisa integrar a VM ao DSC de Automação do Azure.

Para solucionar problemas ou exibir o status da extensão de Configuração de Estado Desejado da VM do Azure, no portal do Azure, navegue até a VM que está sendo integrada e clique em -> **Todas as configurações** -> **Extensões** -> **DSC**. Para obter mais detalhes, você pode clicar em **Exibir status detalhado**.

[![](./media/automation-dsc-onboarding/DSC_Onboarding_5.png)](https://technet.microsoft.com/library/dn249912.aspx)

## <a name="certificate-expiration-and-reregistration"></a>Expiração do certificado e um novo registro

Depois de registrar uma máquina como um nó DSC no DSC de Automação do Azure, há vários motivos para você precisar registrar novamente o nó no futuro:

* Após o registro, cada nó negocia automaticamente um certificado exclusivo para autenticação, que expira depois de um ano. Atualmente, o protocolo de registro DSC do PowerShell não pode renovar automaticamente certificados quando eles estão prestes a expirar, então você precisa registrar novamente os nós após um ano. Antes de registrar novamente, certifique-se de que cada nó está executando o Windows Management Framework 5.0 RTM. Se o certificado de autenticação de um nó expirar e o nó não for registrado novamente, o nó não será capaz de se comunicar com a Automação do Azure e será marcado como "Sem resposta". A realização de um novo registro a 90 dias ou menos do tempo de expiração do certificado, ou a qualquer momento após o tempo de expiração do certificado, vai resultar na geração e uso de um novo certificado.
* Para alterar quaisquer [valores do Gerenciador de Configuração Local do PowerShell DSC](https://msdn.microsoft.com/powershell/dsc/metaconfig4) que foram definidos durante o registro inicial do nó, como ConfigurationMode. Atualmente, esses valores de agente do DSC só podem ser alterados por meio de um novo registro. A única exceção é a Configuração de Nó atribuída ao nó, isso pode ser alterado diretamente no DSC de Automação do Azure.

Um novo registro pode ser executado da mesma maneira que você registrou o nó inicialmente, usando qualquer um dos métodos de integração descritos neste documento. Você não precisa cancelar o registro de um nó do DSC de Automação do Azure antes de registrá-lo novamente.

## <a name="related-articles"></a>Artigos relacionados

* [Visão geral do DSC de Automação do Azure](automation-dsc-overview.md)
* [cmdlets da DSC de Automação do Azure](/powershell/module/azurerm.automation/#automation)
* [preço da DSC de Automação do Azure](https://azure.microsoft.com/pricing/details/automation/)
