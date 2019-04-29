---
title: Configurações de compilação na Configuração do Estado de Automação do Azure
description: Este artigo descreve como compilar as configurações da DSC (Configuração de Estado Desejado) para Automação do Azure.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 09/10/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f2c6f45081b78d330033570ff322f90cd06e50dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61074094"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Compilação das configurações DSC na Configuração do Estado de Automação do Azure

Você pode compilar as configurações DSC (Configuração de Estado Desejado) de duas maneiras com a Configuração do Automação do Azure: no portal do Azure e com o Windows PowerShell. A seguinte tabela ajuda você a determinar quando usar qual método com base nas características de cada um:

**Portal do Azure**

- Método mais simples com interface do usuário interativo
- Formulário para fornecer valores de parâmetro simples
- Acompanhar facilmente o estado do trabalho
- Acesso autenticado com o logon do Azure

**Windows PowerShell**

- Chame da linha de comando com os cmdlets do Windows PowerShell
- Pode ser incluído em uma solução automatizada com várias etapas
- Fornece valores de parâmetro simples e complexos.
- Acompanha o estado do trabalho
- É necessário um cliente para dar suporte a cmdlets do PowerShell
- Transmitir ConfigurationData
- Compilar configurações que usam credenciais

Depois de decidir sobre um método de compilação, é possível seguir os respectivos procedimentos abaixo para iniciar a compilação.

## <a name="compiling-a-dsc-configuration-with-the-azure-portal"></a>Compilando uma Configuração DSC com o portal do Azure

1. Em sua conta de Automação, clique em **Configuração de estado (DSC)**.
1. Clique na guia **Configurações** e, em seguida, clique no nome da configuração para compilar.
1. Clique em **Compilar**.
1. Se a configuração não tiver parâmetros, você será solicitado a confirmar se deseja compilá-la. Se a configuração tiver parâmetros, a folha **Compilar Configuração** será aberta para que você possa fornecer os valores de parâmetro. Consulte a seguinte seção de [**Parâmetros Básicos**](#basic-parameters) para obter mais detalhes sobre os parâmetros.
1. A página **Trabalho de Compilação** é aberta para que você possa acompanhar o status do trabalho de compilação e as configurações de nó (documentos de configuração do MOF) que ele colocou no Servidor de Recepção de Configuração do Estado de Automação do Azure.

## <a name="compiling-a-dsc-configuration-with-windows-powershell"></a>Compilando uma Configuração DSC com o Windows PowerShell

Você pode usar [`Start-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) para iniciar a compilação com o Windows PowerShell. O código de exemplo a seguir inicia a compilação de uma configuração DSC chamada **SampleConfig**.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzureRmAutomationDscCompilationJob` retorna um objeto de trabalho de compilação que você pode usar para acompanhar seu status. Você pode usar esse objeto de trabalho de compilação com [`Get-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob)
para determinar o status do trabalho de compilação, e [`Get-AzureRmAutomationDscCompilationJobOutput`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput)
para exibir seus fluxos (saída). O código de exemplo a seguir inicia a compilação da configuração **SampleConfig** , aguarda até a sua conclusão e exibe suas transmissões.

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

## <a name="basic-parameters"></a>Parâmetros básicos

A declaração de parâmetro nas configurações DSC, incluindo tipos de parâmetros e propriedades, funciona da mesma maneira que os runbooks da Automação do Azure. Veja [Iniciando um runbook na Automação do Azure](automation-starting-a-runbook.md) para saber mais sobre os parâmetros de runbook.

O exemplo a seguir usa dois parâmetros denominados **FeatureName** e **IsPresent** para determinar os valores das propriedades na configuração de nó **ParametersExample.sample** gerada durante a compilação.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]
        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = 'Present'
    if($IsPresent -eq $false)
    {
        $EnsureString = 'Absent'
    }

    Node 'sample'
    {
        WindowsFeature ($FeatureName + 'Feature')
        {
            Ensure = $EnsureString
            Name   = $FeatureName
        }
    }
}
```

É possível compilar as Configurações DSC que usam parâmetros básicos no portal de Configuração do Estado de Automação do Azure ou com o Azure PowerShell:

### <a name="portal"></a>Portal

No portal, é possível inserir valores de parâmetro depois de clicar em **Compilar**.

![Parâmetros de configuração de compilação](./media/automation-dsc-compile/DSC_compiling_1.png)

### <a name="powershell"></a>PowerShell

O PowerShell requer parâmetros em uma [tabela de hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) em que a chave corresponda ao nome do parâmetro e o valor seja igual ao valor do parâmetro.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Para obter informações sobre como transmitir PSCredentials como parâmetros, confira [Ativos de credencial](#credential-assets) abaixo.

## <a name="composite-resources"></a>Recursos de composição

**Recursos de Composição** permitem que você use as configurações DSC como recursos aninhados dentro de uma configuração. Isso permite que você aplique várias configurações para um único recurso. Confira [Recursos de composição: Usando uma configuração DSC como um recurso](/powershell/dsc/authoringresourcecomposite) para saber mais sobre **Recursos de Composição**.

> [!NOTE]
> Para que os **Recursos de Composição** sejam compilados corretamente, primeiro você deverá garantir que todos os Recursos DSC em que a composição confie sejam instalados primeiro no repositório dos Módulos de Conta de Automação do Azure ou eles não serão importados corretamente.

Para adicionar um **Recursos de Composição** DSC, você deverá adicionar o módulo de recurso a um arquivo (*.zip). Acesse o repositório de Módulos na sua Conta de Automação do Azure. Em seguida, clique no botão 'Adicionar um Módulo'.

![Adicionar módulo](./media/automation-dsc-compile/add_module.png)

Navegue até o diretório onde seu arquivo está localizado. Selecione o arquivo e clique em OK.

![Selecione Módulo](./media/automation-dsc-compile/select_dscresource.png)

Você é levado de volta ao diretório de módulos, onde é possível monitorar o status do seu **Recurso de Composição** enquanto ele desempacota e se registra com a Automação do Azure.

![Importar Recursos de Composição](./media/automation-dsc-compile/register_composite_resource.png)

Quando o módulo estiver registrado, você poderá clicar nele para validar que os **Recursos de Composição** estão disponíveis para serem utilizados em uma configuração.

![Validar Recursos de Composição](./media/automation-dsc-compile/validate_composite_resource.png)

Então, será possível chamar os **Recursos de Composição** na sua configuração, desse modo:

```powershell
Node ($AllNodes.Where{$_.Role -eq 'WebServer'}).NodeName
{
    DomainConfig myCompositeConfig
    {
        DomainName = $DomainName
        Admincreds = $Admincreds
    }

    PSWAWebServer InstallPSWAWebServer
    {
        DependsOn = '[DomainConfig]myCompositeConfig'
    }
}
```

## <a name="configurationdata"></a>ConfigurationData

**ConfigurationData** permite que você separe a configuração estrutural de qualquer configuração específica do ambiente usando a DSC do PowerShell. Veja [Separar “O que” de “Onde” na DSC do PowerShell](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) para saber mais sobre **ConfigurationData**.

> [!NOTE]
> Você pode usar **ConfigurationData** ao copilar na Configuração do Estado de Automação do Azure usando o Microsoft Azure PowerShell, mas não no portal do Azure.

A seguinte configuração de exemplo DSC usa **ConfigurationData** por meio das palavras-chave **$ConfigurationData** e **$AllNodes**. Também é necessário o[**módulo** xWebAdministration](https://www.powershellgallery.com/packages/xWebAdministration/) para este exemplo:

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq 'WebServer'}.NodeName
    {
        xWebsite Site
        {
            Name         = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure       = 'Present'
        }
    }
}
```

Você pode compilar a configuração da DSC acima com o PowerShell. O seguinte comando do PowerShell adiciona duas configurações de nó ao Servidor de Pull de Configuração de Estado da Automação do Azure: **ConfigurationDataSample.MyVM1** e **ConfigurationDataSample.MyVM3**:

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'MyVM1'
            Role = 'WebServer'
        },
        @{
            NodeName = 'MyVM2'
            Role = 'SQLServer'
        },
        @{
            NodeName = 'MyVM3'
            Role = 'WebServer'
        }
    )

    NonNodeData = @{
        SomeMessage = 'I love Azure Automation State Configuration and DSC!'
    }
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

## <a name="assets"></a>Ativos

As referências de ativos são as mesmas na Configuração do Estado de Automação do Azure e runbooks. Veja o seguinte para obter mais informações:

- [Certificados](automation-certificates.md)
- [Conexões](automation-connections.md)
- [Credenciais](automation-credentials.md)
- [Variáveis](automation-variables.md)

### <a name="credential-assets"></a>Ativos de credencial

Configurações de DSC na Automação do Azure podem fazer referência a ativos de credencial de automação usando `Get-AutomationPSCredential` experiência. Se uma configuração tem um parâmetro que tem um tipo **PSCredential**, em seguida, você pode usar o cmdlet `Get-AutomationPSCredential`, passando o nome de cadeia de caracteres de um ativo de credencial de Automação do Azure para o cmdlet para recuperar a credencial. Você pode usar esse objeto para o parâmetro que exige o objeto **PSCredential**. Em segundo plano, o ativo de credencial da Automação do Azure com esse nome será recuperado e passado para a configuração. O exemplo a seguir mostra isso em ação.

Manter as credenciais seguras em configurações de nó (documentos de configuração do MOF) requer a criptografia das credenciais no arquivo MOF da configuração de nó. No entanto, atualmente, você deve informar a DSC do PowerShell que as credenciais podem ser exportadas em texto sem formatação durante a geração do MOF da configuração de nó, pois a DSC do PowerShell não está ciente de que a Automação do Azure criptografará todo o arquivo MOF após sua geração por meio de um trabalho de compilação.

Você pode informar à DSC do PowerShell que não há problema nas credenciais serem exportadas em texto sem formatação nos MOFs gerados da configuração de nó usando [**ConfigurationData**](#configurationdata). Você deve transmitir `PSDscAllowPlainTextPassword = $true` por meio do **ConfigurationData** para o nome do bloco de cada nó que aparece na configuração DSC e que usa credenciais.

O exemplo a seguir mostra uma configuração DSC que usa um ativo de credencial da Automação.

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential 'SomeCredentialAsset'

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath      = '\\Server\share\path\file.ext'
            DestinationPath = 'C:\destinationPath'
            Credential      = $Cred
        }
    }
}
```

Você pode compilar a configuração da DSC acima com o PowerShell. O seguinte comando do PowerShell adiciona duas configurações de nó ao Servidor de Pull de Configuração de Estado da Automação do Azure: **CredentialSample.MyVM1** e **CredentialSample.MyVM2**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = '*'
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = 'MyVM1'
        },
        @{
            NodeName = 'MyVM2'
        }
    )
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> Quando a compilação for concluída, você poderá receber um erro indicando: **O módulo 'Microsoft.PowerShell.Management' não foi importado porque o snap-in 'Microsoft.PowerShell.Management' já foi importado.** Esse aviso pode ser ignorado sem problemas.

## <a name="partial-configuration"></a>Configuração parcial

Configuração de estado de automação do Azure dá suporte ao uso de [configurações parciais](https://docs.microsoft.com/en-us/powershell/dsc/pull-server/partialconfigs).
Nesse cenário, DSC está configurado para gerenciar várias configurações de forma independente e cada configuração é retreieved da automação do Azure.
No entanto, somente uma configuração pode ser atribuída a um nó por conta de automação.
Isso significa que se você estiver usando duas configurações para um nó exigirá duas contas de automação.
Para obter mais informações sobre como as equipes podem trabalhar juntos para gerenciar os servidores de modo colaborativo usando a configuração como código ver [a função da compreensão DSC em um Pipeline de CI/CD](https://docs.microsoft.com/en-us/powershell/dsc/overview/authoringadvanced).

## <a name="importing-node-configurations"></a>Como importar configurações de nó

Você também pode importar as configurações de nó (MOFs) que você compilou fora do Azure. Uma vantagem disso é que as configurações dos nós podem ser assinadas. Uma configuração de nó assinada é verificada localmente em um nó gerenciado pelo agente de DSC, garantindo que a configuração aplicada ao nó seja proveniente de uma origem autorizada.

> [!NOTE]
> Você pode importar configurações assinadas em sua conta de Automação do Azure, mas a Automação do Azure não oferece suporte à compilação de configurações assinadas.

> [!NOTE]
> Um arquivo de configuração de nó não deve ultrapassar 1 MB, para poder ser importado na Automação do Azure.

Para obter mais informações sobre como assinar configurações de nó, consulte [melhorias no WMF 5.1 - como a configuração de entrada e o módulo](/powershell/wmf/5.1/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Importar uma configuração de nó no Portal do Azure

1. Na página Conta de Automação, clique em **Configuração de estado (DSC)** em **Gerenciamento de Configuração**.
1. Na página **Configuração de estado (DSC)**, clique na guia **Configurações** e, em seguida, em **+ Adicionar**.
1. Na página **Importar**, clique no ícone de pasta próximo à caixa de texto **Arquivo de Configuração de Nó** para procurar um arquivo de configuração de nó (MOF) no computador local.

   ![Procurar arquivo local](./media/automation-dsc-compile/import-browse.png)

1. Insira um nome na caixa de texto **Nome da Configuração**. Esse nome deve corresponder ao nome da configuração a partir da qual a configuração de nó foi compilada.
1. Clique em **OK**.

### <a name="importing-a-node-configuration-with-powershell"></a>Importar uma configuração de nó com o PowerShell

Use o cmdlet [Import-AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) para importar uma configuração de nó para a conta de automação.

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Próximas etapas

- Para começar, consulte [Introdução à Configuração de Estado da Automação do Azure](automation-dsc-getting-started.md)
- Para saber como compilar configurações de DSC para que possam ser atribuídas a nós de destino, consulte [Compilar configurações na Configuração de Estado da Automação do Azure](automation-dsc-compile.md)
- Para referência de cmdlet do PowerShell, consulte [Cmdlets da Configuração de Estado da Automação do Azure](/powershell/module/azurerm.automation/#automation)
- Para obter informações sobre preços, consulte [Preço da Configuração de Estado da Automação do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo de uso da Configuração de Estado da Automação do Azure em um pipeline de implantação contínua, consulte [Implantação contínua usando Configuração de Estado da Automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md)
