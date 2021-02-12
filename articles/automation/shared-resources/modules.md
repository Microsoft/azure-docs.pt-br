---
title: Gerenciar módulos na Automação do Azure
description: Este artigo informa como usar módulos do PowerShell para habilitar cmdlets em runbooks e recursos DSC em configurações DSC.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/01/2021
ms.topic: conceptual
ms.openlocfilehash: a784127cfd6019629f1c2714d0f36850406c3b9d
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99548757"
---
# <a name="manage-modules-in-azure-automation"></a>Gerenciar módulos na Automação do Azure

A Automação do Azure usa vários módulos do PowerShell para habilitar cmdlets em runbooks e recursos DSC em configurações DSC. Os módulos com suporte incluem:

* [Az.Automation do Azure PowerShell](/powershell/azure/new-azureps-module-az).
* [AzureRM.Automation do Azure PowerShell](/powershell/module/azurerm.automation/).
* Outros módulos do PowerShell.
* Módulo `Orchestrator.AssetManagement.Cmdlets` interno.
* Módulos Python 2.
* Módulos personalizados que você cria.

Quando você cria uma conta de Automação, a Automação do Azure importa alguns módulos por padrão. Consulte [Módulos padrão](#default-modules).

## <a name="sandboxes"></a>Áreas restritas

Quando a Automação executa trabalhos de runbook e compilação DSC, ela carrega os módulos para áreas restritas em que os runbooks podem ser executados e as configurações DSC podem ser compiladas. A Automação também insere automaticamente recursos DSC em módulos no servidor de pull DSC. Os computadores podem extrair os recursos quando aplicam as configurações DSC.

>[!NOTE]
>Importe apenas os módulos que seus runbooks e configurações DSC exigem. Não é recomendável importar o módulo Az raiz. Ele inclui muitos outros módulos talvez não sejam necessários, que podem causar problemas de desempenho. Importe módulos individuais, como Az.Compute.

A área restrita da nuvem dá suporte a um máximo de 48 chamadas do sistema e restringe todas as outras chamadas por motivos de segurança. Outras funcionalidades, como o gerenciamento de credenciais e algumas redes, não têm suporte na área restrita da nuvem.

Devido ao número de módulos e cmdlets incluídos, é difícil saber com antecedência quais dos cmdlets farão chamadas sem suporte. Em geral, vimos problemas com cmdlets que exigem acesso elevado, exigem uma credencial como parâmetro ou cmdlets relacionados à rede. Não há suporte para nenhum cmdlet que execute operações de rede de pilha completa na área restrita, incluindo [Connect-AipService](/powershell/module/aipservice/connect-aipservice) do módulo AipService PowerShell e [resolve-DnsName](/powershell/module/dnsclient/resolve-dnsname) do módulo dnsclient.

Essas são limitações conhecidas da área restrita. A solução alternativa recomendada é implantar um [Hybrid runbook Worker](../automation-hybrid-runbook-worker.md) ou usar [Azure Functions](../../azure-functions/functions-overview.md).

## <a name="default-modules"></a>Módulos padrão

A tabela a seguir lista os módulos que a Automação do Azure importa por padrão quando você cria sua conta de Automação. A Automação pode importar versões mais recentes desses módulos. No entanto, não é possível remover a versão original da sua conta de Automação, mesmo que você exclua uma versão mais recente. Observe que esses módulos padrão incluem vários módulos AzureRM.

Os módulos padrão também são conhecidos como módulos globais. No portal do Azure, a propriedade do **módulo global** será **true** ao exibir um módulo que foi importado quando a conta foi criada.

![Captura de tela da Propriedade do módulo global no portal do Azure](../media/modules/automation-global-modules.png)

A Automação não importa o módulo Az raiz automaticamente para nenhuma conta de automação nova ou existente. Para saber mais sobre como trabalhar com esses módulos, confira [Migrar para módulos Az](#migrate-to-az-modules).

> [!NOTE]
> Não recomendamos alterar módulos e runbooks em contas de Automação usadas para a implantação do recurso [Iniciar/Parar VMs fora do horário comercial](../automation-solution-vm-management.md).

|Nome do módulo|Versão|
|---|---|
| AuditPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0,2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostics |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| Orchestrator.AssetManagement.Cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1,1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="az-modules"></a>Az modules

Para Az.Automation, a maioria dos cmdlets tem os mesmos nomes que os usados para os módulos AzureRM, exceto que o prefixo `AzureRM` foi alterado para `Az`. Para obter uma lista de módulos Az que não seguem essa convenção de nomenclatura, confira a [lista de exceções](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

## <a name="internal-cmdlets"></a>Cmdlets internos

A Automação do Azure dá suporte ao módulo `Orchestrator.AssetManagement.Cmdlets` interno para o agente do Log Analytics para Windows, instalado por padrão. A tabela a seguir define os cmdlets internos. Esses cmdlets são criados para serem usados em vez de cmdlets do Azure PowerShell para interagir com recursos compartilhados. Eles podem recuperar segredos de variáveis criptografadas, credenciais e conexões criptografadas.

>[!NOTE]
>Os cmdlets internos só estão disponíveis quando você está executando runbooks no ambiente de área restrita do Azure ou em um Hybrid Runbook Worker do Windows. 

|Nome|Descrição|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Observe que os cmdlets internos diferem na nomenclatura dos cmdlets Az e AzureRM. Os nomes de cmdlet internos não contêm palavras como `Azure` ou `Az` no substantivo, mas usam a palavra `Automation`. Recomendamos o uso em vez do uso dos cmdlets Az ou AzureRM durante a execução do runbook em uma área restrita do Azure ou em um Hybrid Runbook Worker do Windows. Eles exigem menos parâmetros e são executados no contexto do trabalho que já está em execução.

Use os cmdlets Az ou AzureRM para manipular recursos de Automação fora do contexto de um runbook. 

## <a name="python-modules"></a>Módulos Python

Você pode criar runbooks Python 2 na Automação do Azure. Para obter informações sobre o módulo Python, confira [Gerenciar pacotes Python 2 na Automação do Azure](../python-packages.md).

## <a name="custom-modules"></a>Módulos personalizados

A Automação do Azure dá suporte a módulos personalizados do PowerShell que você cria para usar com seus runbooks e configurações DSC. Um tipo de módulo personalizado é um módulo de integração que, opcionalmente, contém um arquivo de metadados para definir a funcionalidade personalizada para os cmdlets do módulo. Um exemplo do uso de um módulo de integração é fornecido em [Adicionar um tipo de conexão](../automation-connections.md#add-a-connection-type).

A Automação do Azure pode importar um módulo personalizado para disponibilizar os cmdlets dele. Nos bastidores, ela armazena o módulo e o usa nas áreas restritas do Azure, assim como faz com outros módulos.

## <a name="migrate-to-az-modules"></a>Migrar para módulos Az

Esta ação informa como migrar para os módulos Az na Automação. Para obter mais informações, confira [Migrar o Azure PowerShell do AzureRM para o Az](/powershell/azure/migrate-from-azurerm-to-az).

Não recomendamos executar os módulos AzureRM e Az na mesma conta de Automação. Quando você tiver certeza de que deseja migrar do AzureRM para o Az, será melhor fazer commit completo para uma migração completa. A Automação geralmente reutiliza áreas restritas dentro da conta de Automação para economizar nos tempos de inicialização. Se você não fizer uma migração completa de módulo, poderá iniciar um trabalho que usa apenas módulos AzureRM e, em seguida, iniciar outro trabalho que usa apenas módulos Az. A área restrita logo falha e você recebe um erro informando que os módulos não são compatíveis. Essa situação resulta na ocorrência de falhas aleatoriamente para qualquer runbook ou configuração específica.

>[!NOTE]
>Quando você cria uma conta de Automação, mesmo após a migração para os módulos Az, a Automação instala os módulos AzureRM por padrão. Você ainda pode atualizar os runbooks do tutorial com os cmdlets AzureRM. No entanto, você não deve executar esses runbooks.

### <a name="test-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>Testar seus runbooks e configurações DSC antes da migração do módulo

Teste todos os runbooks e configurações DSC com cuidado, em uma conta de Automação separada, antes de migrar para os módulos Az. 

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Parar e desfazer o agendamento de todos os runbooks que usam os módulos AzureRM

Para verificar se você não executa nenhum runbook existente ou nenhuma configuração DSC que usa módulos AzureRM, você deve parar e cancelar o agendamento de todos os runbooks e configurações afetados. Primeiro, examine cada runbook ou configuração DSC e o agendamento separadamente para verificar se você pode reagendar o item no futuro, se necessário.

Quando você estiver pronto para remover seus agendamentos, poderá usar o portal do Azure ou o cmdlet [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule). Confira [Remover um agendamento](schedules.md#remove-a-schedule).

### <a name="remove-azurerm-modules"></a>Remover módulos AzureRM

É possível remover os módulos AzureRM antes de importar os módulos Az. No entanto, se você fizer isso, poderá interromper a sincronização do controle do código-fonte e causar a falha de todos os scripts que ainda estiverem agendados. Se você decidir remover os módulos, confira [Desinstalar AzureRM](/powershell/azure/migrate-from-azurerm-to-az#uninstall-azurerm).

### <a name="import-az-modules"></a>Importar módulos Az

Importar um módulo Az para sua conta de Automação não importa automaticamente o módulo para a sessão do PowerShell usada pelos runbooks. Os módulos são importados na sessão do PowerShell nas seguintes situações:

* Quando um runbook invoca um cmdlet de um módulo.
* Quando um runbook importa o módulo explicitamente com o cmdlet [Import-Module](/powershell/module/microsoft.powershell.core/import-module).
* Quando um runbook importa outro módulo dependente.

Você pode importar os módulos Az no portal do Azure. Lembre-se de importar apenas os módulos Az necessários, não todo o módulo Az.Automation. Como [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) é uma dependência para os outros módulos Az, importe esse módulo antes de qualquer outro.

1. Na sua conta de Automação, em **Recursos Compartilhados**, selecione **Módulos**.
2. Selecione **Procurar na Galeria**.  
3. Na barra de pesquisa, digite o nome do módulo (por exemplo, `Az.Accounts`).
4. Na página Módulo do PowerShell, selecione **Importar** para importar o módulo para a sua conta de Automação.

    ![Captura de tela da importação de módulos para sua conta de Automação](../media/modules/import-module.png)

Você também pode fazer isso por meio da [Galeria do PowerShell](https://www.powershellgallery.com) pesquisando o módulo a ser importado. Quando encontrar o módulo, selecione-o e escolha a guia **Automação do Azure**. Selecione **Implantar na Automação do Azure**.

![Captura de tela da importação de módulos diretamente da Galeria do PowerShell](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Testar seus runbooks

Depois de importar os módulos Az para a conta de Automação, você poderá começar a editar seus runbooks e configurações DSC para usar os novos módulos. Uma forma de testar a modificação de um runbook usar os novos cmdlets é usando o comando `Enable-AzureRmAlias -Scope Process` no início do runbook. Ao adicionar esse comando ao seu runbook, o script poderá ser executado sem alterações.

## <a name="author-modules"></a>Criar módulos

Recomendamos que você siga as considerações nesta seção ao criar um módulo do PowerShell personalizado para uso na Automação do Azure. Para preparar seu módulo para importação, você deve criar pelo menos um arquivo. psd1,. psm1 ou módulo **. dll** do PowerShell com o mesmo nome que a pasta do módulo. Em seguida, você pode compactar a pasta do módulo para que a Automação do Azure possa importá-lo como um único arquivo. O pacote **.zip** deve ter o mesmo nome da pasta de módulo contida.

Para saber mais sobre como criar um módulo do PowerShell, confira [Como gravar um módulo de script do PowerShell](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

### <a name="version-folder"></a>Pasta de versão

O controle de versão do módulo lado a lado do PowerShell permite que você use mais de uma versão de um módulo no PowerShell. Isso pode ser útil se você tiver scripts mais antigos que foram testados e funcionar apenas em uma determinada versão de um módulo do PowerShell, mas outros scripts exigem uma versão mais recente do mesmo módulo do PowerShell.

Para construir módulos do PowerShell para que eles contenham várias versões, crie a pasta de módulo e, em seguida, crie uma pasta dentro dessa pasta de módulo para cada versão do módulo que você deseja que seja utilizável. No exemplo a seguir, um módulo chamado *TestModule* fornece duas versões, 1.0.0 e 2.0.0.

```dos
TestModule
   1.0.0
   2.0.0
```

Em cada uma das pastas de versão, copie os arquivos **. dll** do PowerShell. psm1,. psd1 ou do PowerShell que compõem um módulo na respectiva pasta de versão. Recompacte a pasta do módulo para que a automação do Azure possa importá-la como um único arquivo. zip. Embora a automação mostre apenas a versão mais recente do módulo importada, se o pacote de módulo contiver versões lado a lado do módulo, todos estarão disponíveis para uso em seus runbooks ou configurações DSC.  

Embora a automação ofereça suporte a módulos que contêm versões lado a lado no mesmo pacote, ele não dá suporte ao uso de várias versões de um módulo em importações de pacote de módulo. Por exemplo, você importa o **módulo A**, que contém as versões 1 e 2 para sua conta de automação. Posteriormente, você atualiza o **módulo a** para incluir as versões 3 e 4, quando você importa para sua conta de automação, somente as versões 3 e 4 são utilizáveis em quaisquer runbooks ou configurações DSC. Se você precisar que todas as versões-1, 2, 3 e 4 estejam disponíveis, o arquivo. zip que sua importação deve conter as versões 1, 2, 3 e 4.

Se você pretende usar versões diferentes do mesmo módulo entre runbooks, você deve sempre declarar a versão que deseja usar em seu runbook usando o `Import-Module` cmdlet e incluir o parâmetro `-RequiredVersion <version>` . Mesmo que a versão que você deseja usar seja a versão mais recente. Isso ocorre porque os trabalhos do runbook podem ser executados na mesma área restrita. Se a área restrita já tiver carregado explicitamente um módulo de um determinado número de versão, porque um trabalho anterior nessa área restrita dizia fazer isso, os trabalhos futuros nessa área restrita não carregarão automaticamente a versão mais recente desse módulo. Isso ocorre porque alguma versão dela já está carregada na área restrita.

Para um recurso de DSC, use o seguinte comando para especificar uma versão específica:

```powershell
Import-DscResource -ModuleName <ModuleName> -ModuleVersion <version>
```

### <a name="help-information"></a>Informações de ajuda

Incluem uma sinopse, uma descrição e um URI de ajuda para cada cmdlet no seu módulo. No PowerShell, você pode definir informações de ajuda para cmdlets usando o cmdlet `Get-Help`. O exemplo a seguir mostra como definir um URI de sinopse e ajuda em um arquivo de módulo **.psm1**.

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  Fornecer essas informações mostra o texto de ajuda por meio do cmdlet `Get-Help` no console do PowerShell. Esse texto também é exibido no portal do Azure.

  ![Captura de tela da ajuda do módulo de integração](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Tipo de conexão

Se o módulo se conectar a um serviço externo, defina um tipo de conexão usando um [módulo de integração personalizado](#custom-modules). Cada cmdlet no módulo deve aceitar uma instância do mesmo tipo de conexão (objeto de conexão) que um parâmetro. Os usuários mapeiam parâmetros do ativo de conexão para os parâmetros correspondentes do cmdlet sempre que chamarem um cmdlet.

![Usar uma conexão personalizada no portal do Azure](../media/modules/connection-create-new.png)

O exemplo de runbook a seguir usa um ativo de conexão da Contoso chamado `ContosoConnection` para acessar recursos da Contoso e retornar dados do serviço externo. Neste exemplo, os campos são mapeados para as propriedades `UserName` e `Password` de um objeto `PSCredential` e, em seguida, passados para o cmdlet.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

Uma forma melhor e mais fácil de abordar esse comportamento é passando diretamente o objeto de conexão para o cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

Você pode habilitar um comportamento semelhante para seus cmdlets permitindo que eles aceitem um objeto de conexão diretamente como um parâmetro, em vez de apenas campos de conexão para parâmetros. Normalmente, convém ter um conjunto de parâmetros para cada um, para que um usuário que não esteja usando a Automação possa chamar seus cmdlets sem construir uma tabela de hash para agir como o objeto de conexão. O conjunto de parâmetros `UserAccount` é usado para passar as propriedades do campo de conexão. `ConnectionObject` permite que você passe a conexão diretamente.

### <a name="output-type"></a>Tipo de saída

Defina o tipo de saída para todos os cmdlets no módulo. A definição de um tipo de saída para um cmdlet permite o IntelliSense no tempo de design para ajudar você a determinar as propriedades de saída do cmdlet durante a criação. Essa prática é especialmente útil durante a criação do runbook gráfico, para o qual o conhecimento de tempo de design é fundamental para uma experiência do usuário fácil com seu módulo.

Adicione `[OutputType([<MyOutputType>])]`, em que `MyOutputType` é um tipo válido. Para saber mais sobre `OutputType`, confira [Sobre Funções OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). O seguinte código é um exemplo de como adicionar `OutputType` a um cmdlet:

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![Captura de tela do tipo de saída do runbook gráfico](../media/modules/runbook-graphical-module-output-type.png)

  Esse comportamento é semelhante à funcionalidade "preenchimento automático" da saída de um cmdlet no ambiente de serviço de integração do PowerShell, sem precisar executá-lo.

  ![Captura de tela de POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Estado do cmdlet

Torne todos os cmdlets em seu módulo sem estado. Vários trabalhos de runbook podem ser executados simultaneamente no mesmo `AppDomain` e no mesmo processo e área restrita. Se houver qualquer estado compartilhado nesses níveis, os trabalhos poderão afetar uns aos outros. Esse comportamento pode levar a problemas intermitentes e difíceis de diagnosticar. Veja um exemplo do que não fazer:

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

### <a name="module-dependency"></a>Dependência do módulo

Verifique se o módulo está totalmente contido em um pacote que pode ser copiado usando xcopy. Os módulos da Automação são distribuídos para as áreas restritas da Automação quando os runbooks são executados. Os módulos devem funcionar independentemente do host que os executa.

Você deve poder compactar e mover um pacote de módulo e ele deve funcionar normalmente quando importado para outro ambiente do PowerShell do host. Para que isso aconteça, verifique se o módulo não depende de nenhum arquivo fora da pasta do módulo que é compactada quando o módulo é importado para a Automação.

Seu módulo não deve depender de nenhuma configuração de Registro exclusiva em um host. Os exemplos são as configurações feitas quando um produto é instalado.

### <a name="module-file-paths"></a>Caminhos de arquivo do módulo

Verifique se todos os arquivos no módulo têm caminhos com menos de 140 caracteres. Caminhos com mais de 140 caracteres causam problemas com a importação runbooks. A Automação não pode importar um arquivo com tamanho de caminho maior que 140 caracteres para a sessão do PowerShell com `Import-Module`.

## <a name="import-modules"></a>Importar módulos

Esta seção define várias maneiras como você pode importar um módulo para sua conta de Automação.

### <a name="import-modules-in-the-azure-portal"></a>Importar módulos no portal do Azure

Para importar um módulo no portal do Azure:

1. Vá para sua conta de Automação.
2. Em **Recursos Compartilhados**, selecione **Módulos**.
3. Selecione **Adicionar um módulo**.
4. Selecione o arquivo **.zip** que contém o módulo.
5. Selecione **OK** para começar a importar o processo.

### <a name="import-modules-by-using-powershell"></a>Importar módulos usando o PowerShell

Você pode usar o cmdlet [New-AzAutomationModule](/powershell/module/az.automation/new-azautomationmodule) para importar um módulo para sua conta de Automação. O cmdlet usa a URL para um pacote .zip do módulo.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Você também pode usar o mesmo cmdlet para importar um módulo da Galeria do PowerShell diretamente. Pegue `ModuleName` e `ModuleVersion` da [Galeria do PowerShell](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-the-powershell-gallery"></a>Importar módulos da Galeria do PowerShell

Você pode importar módulos da [Galeria do PowerShell](https://www.powershellgallery.com) diretamente da Galeria ou da sua conta de Automação.

Para importar um módulo diretamente da Galeria do PowerShell:

1. Acesse https://www.powershellgallery.com e procure o módulo para importar.
2. Em **Opções de Instalação**, na guia **Automação do Azure**, selecione **Implantar na Automação do Azure**. Essa ação abre o portal do Azure. 
3. Na página Importar, selecione sua conta de Automação e **OK**.

![Captura de tela do módulo de importação da Galeria do PowerShell](../media/modules/powershell-gallery.png)

Para importar um módulo da Galeria do PowerShell diretamente da sua conta de Automação:

1. Em **Recursos Compartilhados**, selecione **Módulos**. 
2. Selecione **Procurar na galeria** e, em seguida, pesquise na Galeria um módulo. 
3. Selecione o módulo a ser importado e selecione **Importar**. 
4. Selecione **OK** para iniciar o processo de importação.

![Captura de tela de como importar um módulo da Galeria do PowerShell da portal do Azure](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Excluir módulos

Se você tiver problemas com um módulo ou precisar reverter para uma versão anterior de um módulo, poderá excluí-lo da sua conta de Automação. Não é possível excluir as versões originais dos [módulos padrão](#default-modules) importados quando você cria uma conta de Automação. Se o módulo a ser excluído for uma versão mais recente de um dos [módulos padrão](#default-modules), ele será revertido para a versão que foi instalada com sua conta de Automação. Caso contrário, qualquer módulo que você excluir da sua conta de Automação será removido.

### <a name="delete-modules-in-the-azure-portal"></a>Excluir módulos no portal do Azure

Para remover um módulo no portal do Azure:

1. Vá para sua conta de Automação. Em **Recursos Compartilhados**, selecione **Módulos**.
2. Selecione o módulo que você deseja remover.
3. Na página Módulo, selecione **Excluir**. Se esse módulo for um dos [módulos padrão](#default-modules), ele será revertido para a versão que existia quando a conta de Automação foi criada.

### <a name="delete-modules-by-using-powershell"></a>Excluir módulos usando o PowerShell

Para remover um módulo por meio do PowerShell, execute o seguinte comando:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre como usar módulos Azure PowerShell, consulte Introdução [ao Azure PowerShell](/powershell/azure/get-started-azureps).

* Para saber mais sobre a criação de módulos do PowerShell, consulte [escrevendo um módulo do Windows PowerShell](/powershell/scripting/developer/module/writing-a-windows-powershell-module).
