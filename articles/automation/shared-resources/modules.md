---
title: Gerenciar os módulos na automação do Azure
description: Este artigo descreve como gerenciar os módulos na automação do Azure
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 06/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cd085164fc9804e0c1c822df1c72d3ef94093a07
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672799"
---
# <a name="manage-modules-in-azure-automation"></a>Gerenciar os módulos na automação do Azure

A automação do Azure fornece a capacidade para importar módulos do PowerShell para sua conta de automação a ser usada por runbooks baseados no PowerShell. Esses módulos podem ser módulos personalizados que você criou, da Galeria do PowerShell, ou os módulos AzureRM e Az para o Azure. Quando você cria uma conta de automação alguns módulos são importados por padrão.

## <a name="import-modules"></a>Importar módulos

Há várias maneiras que você pode importar um módulo para sua conta de automação. As seções a seguir mostram as diferentes maneiras de importar um módulo.

> [!NOTE]
> O caminho máximo de um arquivo em um módulo a ser usado na automação do Azure é 140 caracteres. Qualquer caminho com mais de 140 caracteres não poderão ser importados para a sessão do PowerShell com `Import-Module`.

### <a name="powershell"></a>PowerShell

Você pode usar o [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) para importar um módulo para sua conta de automação. O cmdlet usa uma url para um pacote de zip do módulo.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="azure-portal"></a>Portal do Azure

No portal do Azure, navegue até sua conta de automação e selecione **módulos** sob **recursos compartilhados**. Clique em **+ adicionar um módulo**. Selecione uma **. zip** arquivo que contém o módulo e clique em **Okey** para iniciar o processo de importação.

### <a name="powershell-gallery"></a>Galeria do PowerShell

Módulos da Galeria do PowerShell podem ser importados o [da Galeria do PowerShell](https://www.powershellgallery.com) diretamente ou de sua conta de automação.

Para importar um módulo da Galeria do PowerShell, vá para https://www.powershellgallery.com e procure o módulo que você deseja importar. Clique em **implantar na automação do Azure** sobre o **automação do Azure** guia sob **opções de instalação**. Essa ação abre o portal do Azure. Sobre o **importação** , selecione sua conta de automação e clique em **Okey**.

![Módulo de importação da Galeria do PowerShell](../media/modules/powershell-gallery.png)

Você também pode importar os módulos da Galeria do PowerShell diretamente da sua conta de automação. Na sua conta de automação, selecione **módulos** sob **recursos compartilhados**. Na página módulos, clique em **procurar na galeria**. Isso abre o **procurar na galeria** página. Você pode usar essa página para pesquisar na Galeria do PowerShell para um módulo. Selecione o módulo que você deseja importar e clique em **importação**. Sobre o **importe** , clique em **Okey** para iniciar o processo de importação.

![Importação de galeria do PowerShell do portal do Azure](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Excluir módulos

Se você tiver problemas com um módulo ou se você precisa reverter para uma versão anterior de um módulo, você pode excluí-lo em sua conta de automação. Não é possível excluir a versão original do [padrão módulos](#default-modules) que são importados quando você cria uma conta de automação. Se o módulo que você deseja excluir é uma versão mais recente de um dos [padrão módulos](#default-modules) instalado, ele será reverter para a versão que foi instalada com sua conta de automação. Caso contrário, qualquer módulo que você excluir da sua conta de automação será removido.

### <a name="azure-portal"></a>Portal do Azure

No portal do Azure, navegue até sua conta de automação e selecione **módulos** sob **recursos compartilhados**. Selecione o módulo que você deseja remover. Sobre o **módulo** página, clcick **excluir**. Se esse módulo é um dos [padrão módulos](#default-modules) será possível revertê-lo para a versão que estava presente quando a conta de automação foi criada.

### <a name="powershell"></a>PowerShell

Para remover um módulo por meio do PowerShell, execute o seguinte comando:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Cmdlets internos

A seguir está uma lista dos cmdlets no interno `Orchestrator.AssetManagement.Cmdlets` módulo é importado para cada conta de automação. Esses cmdlets podem ser acessados em runbooks e configurações DSC e permitem que você interaja com seus ativos em sua conta de automação. Além disso, os cmdlets internos permitem que você recuperar segredos de criptografados **variável** valores, **credenciais**e criptografados **Conexão** campos. Os cmdlets do PowerShell do Azure não são capazes de recuperar esses segredos. Esses cmdlets não exigem que você implicitamente se conectar ao Azure, ao usá-los. Isso é útil para cenários nos quais você tem uma conexão, como uma conta executar como que você precisa usar a autenticação do Azure.

|Nome|DESCRIÇÃO|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Adicionar um tipo de conexão para seu módulo

Você pode fornecer um personalizado [tipo de conexão](../automation-connections.md) para uso em sua conta de automação, adicionando um arquivo opcional para seu módulo. Esse arquivo é um arquivo de metadados que especifica um tipo de conexão de automação do Azure a ser usado com os cmdlets do módulo na sua conta de automação. Para fazer isso, você primeiro deve saber como criar um módulo do PowerShell. Para obter mais informações sobre a criação do módulo, consulte [como escrever um módulo de Script do PowerShell](/powershell/developer/module/how-to-write-a-powershell-script-module).

![Usar uma conexão personalizada no portal do Azure](../media/modules/connection-create-new.png)

Para adicionar um tipo de conexão de automação do Azure, o módulo deve conter um arquivo com o nome `<ModuleName>-Automation.json` que especifica as propriedades de tipo de conexão. Esse é um arquivo json, que é colocado dentro da pasta do módulo do seu arquivo .zip compactado. Esse arquivo contém os campos de uma conexão que é necessário para se conectar ao sistema ou serviço que representa o módulo. Essa configuração acaba criando um tipo de conexão na Automação do Azure. Usando esse arquivo, você pode definir os nomes de campos, tipos, e se os campos devem ser criptografado ou opcional, para o tipo de conexão do módulo. O exemplo a seguir é um modelo no formato de arquivo json que define uma propriedade de nome de usuário e senha:

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="module-best-practices"></a>Práticas recomendadas do módulo

Os módulos PowerShell podem ser importados na Automação do Azure de modo a disponibilizar os cmdlets para uso em runbooks e recursos DSC para uso em configurações de DSC. Em segundo plano, a Automação do Azure armazena esses módulos e, no momento da execução do trabalho do runbook e do trabalho de compilação do DSC, os carrega nas áreas restritas da Automação do Azure, onde runbooks são executados e configurações de DSC compiladas. Todos os recursos DSC em módulos também são colocados automaticamente no servidor de pull de DSC de Automação. Eles podem ser obtidos por máquinas quando elas aplicam configurações de DSC.

Recomendamos que você considere o seguinte ao criar um módulo do PowerShell para uso na automação do Azure:

* Incluem uma sinopse, uma descrição e um URI de ajuda para cada cmdlet no módulo. No PowerShell, você pode usar o cmdlet **Get-Help** para definir certas informações de ajuda com relação aos cmdlets para que o usuário receba ajuda sobre como usá-los. O exemplo a seguir mostra como definir uma sinopse e ajudar a URI para em um arquivo de módulo. psm1:

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

  Fornecer essas informações mostra essa ajuda usando o cmdlet **Get-Help** no console do PowerShell. Além disso, essa descrição é exibida no portal do Azure.

  ![Ajuda do Módulo de Integração](../media/modules/module-activity-description.png)

* Se o módulo se conecta a um serviço externo, ela deve conter um [tipo de conexão](#add-a-connection-type-to-your-module). Cada cmdlet no módulo deve ser capaz de receber um objeto de conexão (uma instância desse tipo de conexão) como um parâmetro. Isso permite que os usuários mapeiem parâmetros do ativo de conexão para os parâmetros correspondentes do cmdlet sempre que chamarem um cmdlet. Com base no exemplo de runbook acima, ele usa um ativo de conexão da Contoso de exemplo chamado ContosoConnection para acessar recursos da Contoso e retornar dados de serviço externo.

  No exemplo a seguir, os campos são mapeados para as propriedades de nome de usuário e senha de um `PSCredential` do objeto e, em seguida, passado para o cmdlet.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Uma maneira mais fácil e adequada de abordar esse comportamento é passando diretamente o objeto de conexão para o cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Você pode habilitar um comportamento como o do exemplo anterior para seus cmdlets, permitindo que eles aceitem um objeto de conexão diretamente como um parâmetro, em vez de apenas campos de conexão para parâmetros. Normalmente, convém ter um conjunto de parâmetros para cada um, para que um usuário que não esteja usando a Automação do Azure possa chamar seus cmdlets sem construir uma tabela de hash para agir como o objeto de conexão. O conjunto de parâmetros `UserAccount`, é usado para passar a conexão de propriedades de campo. `ConnectionObject` permite que você passe a conexão diretamente.

* Defina o tipo de saída para todos os cmdlets no módulo. A definição de um tipo de saída para um cmdlet permite o IntelliSense no tempo de criação para ajudar você a determinar as propriedades de saída do cmdlet, para uso durante a criação. Isso é especialmente útil durante a criação gráfica do runbook da Automação, quando o conhecimento do tempo de design é fundamental para uma experiência de usuário facilitada com o seu módulo.

  Isso pode ser feito adicionando `[OutputType([<MyOutputType>])]` onde MyOutputType é um tipo válido. Para saber mais sobre o tipo de saída, consulte [sobre funções OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). O código a seguir está um exemplo de como adicionar `OutputType` para um cmdlet:

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

  ![Tipo de saída do runbook gráfico](../media/modules/runbook-graphical-module-output-type.png)

  Esse comportamento é semelhante à funcionalidade "preenchimento automático" da saída de um cmdlet no ISE do PowerShell sem precisar executá-lo.

  ![IntelliSense POSH](../media/modules/automation-posh-ise-intellisense.png)

* Torne todos os cmdlets no módulo cmdlets sem estado. Vários trabalhos de runbook podem executar simultaneamente no mesmo AppDomain e o mesmo processo e da área restrita. Se não houver nenhum estado compartilhado em um desses níveis, trabalhos podem afetar uns aos outros. Esse comportamento pode levar a intermitentes e difíceis de diagnosticar problemas.  Veja um exemplo do que não fazer:

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

* O módulo deve estar totalmente contido em um pacote capacitado para xcopy. Módulos de automação do Azure são distribuídos para as áreas restritas da automação quando os runbooks precisam ser executados. Os módulos precisam funcionar independentemente do host em que estão em execução. Você deve poder compactar e mover um pacote de módulo, e este deve funcionar normalmente quando importado para outro ambiente do PowerShell do host. Para que isso aconteça, o módulo não deve depender de qualquer arquivo fora da pasta do módulo. Essa pasta é a pasta que é compactada quando o módulo é importado para a Automação do Azure. O módulo também não deve depender de qualquer configuração de registro exclusiva em um host, como as configurações definidas quando um produto é instalado. Todos os arquivos no módulo devem ter um caminho de menos de 140 caracteres. Todos os caminhos com mais de 140 caracteres causará problemas de importação de seu runbook. Se essa melhor prática não for seguida, o módulo não poderá ser usado na Automação do Azure.  

* Se estiver fazendo referência aos [módulos Az do Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) em seu módulo, certifique-se de que não esteja fazendo referência também a `AzureRM`. O módulo `Az` não pode ser usado em conjunto com os módulos `AzureRM`. Há suporte para `Az` em runbooks, mas não são importados por padrão. Para saber mais sobre os módulos `Az` e considerações nas quais prestar atenção, confira [Suporte ao módulo Az na Automação do Azure](../az-modules.md).

## <a name="default-modules"></a>Módulos padrão

A tabela a seguir lista os módulos que são importados por padrão, quando uma conta de automação é criada. Os módulos listados abaixo podem ter versões mais recentes deles importadas, mas a versão original não pode ser removida da sua conta de automação, mesmo se você excluir uma versão mais recente deles.

|nome do módulo|Versão|
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
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como criar os Módulos do PowerShell, consulte [Escrevendo um Módulo do Windows PowerShell](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)