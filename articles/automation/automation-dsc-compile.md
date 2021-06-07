---
title: Compilar configurações DSC na State Configuration da Automação do Azure
description: Este artigo descreve como compilar as configurações da DSC (Desired State Configuration) para a Automação do Azure.
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: b45aad71e04418c7c7dda4fc3f0c84a5fe99ecdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896335"
---
# <a name="compile-dsc-configurations-in-azure-automation-state-configuration"></a>Compilar configurações DSC na State Configuration da Automação do Azure

Você pode compilar as configurações DSC (Desired State Configuration) na State Configuration da Automação do Azure das seguintes maneiras:

- Serviço de compilação de State Configuration do Azure
  - Método para iniciantes com interface do usuário interativa
   - Acompanhar facilmente o estado do trabalho

- Windows PowerShell
  - Chamada do Windows PowerShell no serviço de build ou estação de trabalho local
  - Integrar com pipeline de teste de desenvolvimento
  - Fornece valores de parâmetro complexos
  - Trabalhar com elementos de nó e não nó em escala
  - Aprimoramento significativo do desempenho

Você também pode usar modelos de Azure Resource Manager com a extensão de DSC (Desired State Configuration) do Azure para enviar configurações por push para suas VMs do Azure. A extensão de DSC do Azure usa a estrutura do Agente de VM do Azure para entregar, aplicar e gerar relatórios sobre configurações da DSC executadas em VMs do Azure. Para obter detalhes de build usando modelos de Azure Resource Manager, confira [Extensão de Desired State Configuration com modelos do Azure Resource Manager](../virtual-machines/extensions/dsc-template.md#details). 

## <a name="compile-a-dsc-configuration-in-azure-state-configuration"></a>Compilar uma configuração DSC na State Configuration do Azure

### <a name="portal"></a>Portal

1. Em sua conta de Automação, clique em **DSC (State Configuration)** .
1. Clique na guia **Configurações** e, em seguida, clique no nome da configuração para compilar.
1. Clique em **Compilar**.
1. Se a configuração não tiver parâmetros, você será solicitado a confirmar se deseja compilá-la. Se a configuração tiver parâmetros, a folha **Compilar Configuração** será aberta para que você possa fornecer os valores de parâmetro.
1. A página Trabalho de Compilação é aberta para que você possa acompanhar o status do trabalho de compilação. Você também pode usar essa página para controlar as configurações de nó (documentos de configuração do MOF) colocadas no servidor de pull da State Configuration da Automação do Azure.

### <a name="azure-powershell"></a>Azure PowerShell

Você pode usar [Start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob) para iniciar a compilação com o Windows PowerShell. O código de exemplo a seguir inicia a compilação de uma configuração DSC chamada **SampleConfig**.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob` retorna um objeto de trabalho de compilação que você pode usar para acompanhar o status desse trabalho. Você pode usar esse objeto de trabalho de compilação com [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) para determinar o status do trabalho de compilação e [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) para exibir as transmissões dele (saída). O exemplo a seguir inicia a compilação da configuração SampleConfig, aguarda até a conclusão dela e exibe as respectivas transmissões.

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>Declarar parâmetros básicos

A declaração de parâmetro nas configurações DSC, incluindo tipos de parâmetros e propriedades, funciona da mesma maneira que os runbooks da Automação do Azure. Veja [Iniciando um runbook na Automação do Azure](./start-runbooks.md) para saber mais sobre os parâmetros de runbook.

O exemplo a seguir usa os parâmetros `FeatureName` e `IsPresent` para determinar os valores das propriedades na configuração de nó **ParametersExample.sample** gerada durante a compilação.

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

É possível compilar as configurações DSC que usam parâmetros básicos no portal de State Configuration da Automação do Azure ou com o Azure PowerShell.

#### <a name="portal"></a>Portal

No portal, é possível inserir valores de parâmetro depois de clicar em **Compilar**.

![Parâmetros de configuração de compilação](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

O PowerShell requer parâmetros em uma [tabela de hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) em que a chave corresponda ao nome do parâmetro e o valor seja igual ao valor do parâmetro.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Para obter informações sobre como transmitir objetos `PSCredential` como parâmetros, confira [Ativos de credencial](#credential-assets).

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Compilar configurações que contêm recursos de composição na Automação do Azure

O recurso **Recursos de Composição** permitem que você use as configurações DSC como recursos aninhados dentro de uma configuração. Esse recurso permite que a aplicação de várias configurações a um recurso. Confira [Recursos de composição: Usando uma configuração DSC como um recurso](/powershell/scripting/dsc/resources/authoringresourcecomposite) para saber mais sobre recursos de composição.

> [!NOTE]
> Para que as configurações que contêm recursos de composição sejam compiladas corretamente, você deve primeiro importar para a Automação do Azure os recursos de DSC dos quais as composições dependem. Adicionar um recurso de composição de DSC não é diferente de adicionar qualquer módulo do PowerShell à Automação do Azure. Esse processo está documentado em [Gerenciar módulos na Automação do Azure](./shared-resources/modules.md).

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Gerenciar ConfigurationData ao compilar configurações na Automação do Azure

`ConfigurationData` é um parâmetro DSC interno permite que você separe a configuração estrutural de qualquer configuração específica a um ambiente, usando simultaneamente a DSC do PowerShell. Para obter mais informações, confira [Separando "o que" de "onde" na DSC do PowerShell](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/).

> [!NOTE]
> Ao compilar no State Configuration da Automação do Azure, é possível usar `ConfigurationData` no Azure PowerShell, mas não no portal do Azure.

A configuração DSC de exemplo a seguir usa `ConfigurationData` por meio das palavras-chave `$ConfigurationData` e `$AllNodes`. O [módulo xWebAdministration](https://www.powershellgallery.com/packages/xWebAdministration/) também é necessário para este exemplo.

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

Você pode compilar a configuração da DSC acima com o Windows PowerShell. O seguinte script adiciona duas configurações de nó ao servidor de pull de State Configuration da Automação do Azure: **ConfigurationDataSample.MyVM1** e **ConfigurationDataSample.MyVM3**.

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

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>Trabalhar com ativos na Automação do Azure durante a compilação

As referências de ativos são as mesmas na State Configuration da Automação do Azure e nos runbooks. Para saber mais, consulte o seguinte:

- [Certificados](./shared-resources/certificates.md)
- [Conexões](automation-connections.md)
- [Credenciais](./shared-resources/credentials.md)
- [Variáveis](./shared-resources/variables.md)

#### <a name="credential-assets"></a>Ativos de credencial

Configurações de DSC na Automação do Azure podem fazer referência a ativos de credencial de automação usando `Get-AutomationPSCredential` experiência. Se uma configuração tem um parâmetro que especifica um objeto `PSCredential`, use `Get-AutomationPSCredential` passando o nome de cadeia de caracteres de um ativo de credencial de Automação do Azure para o cmdlet para recuperar a credencial. Em seguida, use esse objeto para o parâmetro que requer o objeto `PSCredential`. Em segundo plano, o ativo de credencial da Automação do Azure com esse nome será recuperado e passado para a configuração. O exemplo a seguir mostra esse cenário em ação.

Manter as credenciais seguras em configurações de nó (documentos de configuração do MOF) requer a criptografia das credenciais no arquivo MOF da configuração de nó. No momento, você precisa conceder permissão de DSC do PowerShell para gerar credenciais em texto sem formatação durante a geração de MOF de configuração de nó. O DSC do PowerShell não reconhece que a Automação do Azure criptografa todo o arquivo MOF após ele ser gerado por meio de um trabalho de compilação.

Você pode informar à DSC do PowerShell que não há problema nas credenciais serem exportadas em texto sem formatação nos MOFs gerados da configuração de nó usando ConfigurationData. Você deve transmitir `PSDscAllowPlainTextPassword = $true` por meio de `ConfigurationData` para o nome do bloco de cada nó que aparece na configuração DSC e que usa credenciais.

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

Você pode compilar a configuração da DSC acima com o PowerShell. O seguinte comando do PowerShell adiciona duas configurações de nó ao servidor de pull de State Configuration da Automação do Azure: **CredentialSample.MyVM1** e **CredentialSample.MyVM2**.

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

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> Quando a compilação for concluída, você poderá receber a mensagem de erro `The 'Microsoft.PowerShell.Management' module was not imported because the 'Microsoft.PowerShell.Management' snap-in was already imported.`. Você pode ignorar essa mensagem com segurança.

## <a name="compile-your-dsc-configuration-in-windows-powershell"></a>Compilar uma configuração DSC com o Windows PowerShell

O processo para compilar configurações DSC no Windows PowerShell está incluído na documentação do DSC do PowerShell [Gravar, compilar e aplicar uma configuração](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
Você pode executar esse processo em uma estação de trabalho de desenvolvedor ou em um serviço de build, como o [Azure DevOps](https://dev.azure.com). Em seguida, você pode importar os arquivos MOF produzidos compilando a configuração no serviço State Configuration do Azure.

A compilação no Windows PowerShell também fornece a opção de assinar o conteúdo de configuração. O agente DSC verifica uma configuração de nó assinada localmente em um nó gerenciado. A verificação garante que a configuração aplicada ao nó seja proveniente de uma fonte autorizada.

Você também pode importar as configurações de nó (arquivos MOF) que você compilou fora do Azure. A importação inclui a compilação de uma estação de trabalho de desenvolvedor ou em um serviço como o [Azure DevOps](https://dev.azure.com). Essa abordagem tem várias vantagens, incluindo desempenho e confiabilidade.

> [!NOTE]
> Um arquivo de configuração de nó não pode ultrapassar 1 MB para que possa ser importado pela Automação do Azure.

Para obter mais informações sobre como assinar configurações de nó, confira [Aprimoramentos no WMF 5.1 – Como assinar a configuração e o módulo](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Importar uma configuração de nó no portal do Azure

1. Na página Conta de Automação, clique em **DSC (State Configuration)** em **Gerenciamento de Configuração**.
1. Na página DSC (State Configuration), clique na guia **Configurações** e, em seguida, em **Adicionar**.
1. Na página Importar, clique no ícone de pasta ao lado do campo **Arquivo de Configuração de Nó** para procurar um arquivo MOF de configuração de nó no computador local.

   ![Procurar arquivo local](./media/automation-dsc-compile/import-browse.png)

1. Insira um nome no campo **Nome da Configuração**. Esse nome deve corresponder ao nome da configuração a partir da qual a configuração de nó foi compilada.
1. Clique em **OK**.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Importar uma configuração de nó com o Azure PowerShell

Use o cmdlet [Import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) para importar uma configuração de nó para a conta de Automação.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Próximas etapas

- Para começar, confira [Introdução ao State Configuration da Automação do Azure](automation-dsc-getting-started.md).
- Para saber como compilar configurações DSC para que possam ser atribuídas a nós de destino, confira [Compilar configurações DSC no State Configuration da Automação do Azure](automation-dsc-compile.md).
- Para obter uma referência de cmdlet do PowerShell, confira [Az.Automation](/powershell/module/az.automation).
- Para obter informações sobre preços, consulte [Preço da State Configuration da Automação do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para ver um exemplo de uso do State Configuration em um pipeline de implantação contínua, confira [Configurar a implantação contínua com o Chocolatey](automation-dsc-cd-chocolatey.md).
