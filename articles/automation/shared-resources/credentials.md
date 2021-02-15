---
title: Gerenciar credenciais na Automação do Azure
description: Este artigo explica como criar ativos de credenciais e usá-los em um runbook ou uma configuração DSC.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: 9b9e42d55a982aeb55d7c9e26f7b1a6cbca32e0a
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052797"
---
# <a name="manage-credentials-in-azure-automation"></a>Gerenciar credenciais na Automação do Azure

Um ativo de credencial de Automação contém um objeto que contém as credenciais de segurança, como um nome de usuário e uma senha. Os runbooks e as configurações DSC usam cmdlets que aceitam um objeto [PSCredential](/dotnet/api/system.management.automation.pscredential) para autenticação. Como alternativa, eles podem extrair o nome de usuário e a senha do objeto `PSCredential` para fornecer a um aplicativo ou serviço que exija autenticação.

>[!NOTE]
>Os ativos protegidos na Automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados na Automação do Azure usando uma chave exclusiva que é gerada para cada conta da Automação do Azure. A Automação do Azure armazena a chave no Key Vault gerenciado pelo sistema. Antes de armazenar um ativo seguro, a Automação do Azure carrega a chave do Key Vault e depois a usa para criptografar o ativo. 

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="powershell-cmdlets-used-to-access-credentials"></a>Cmdlets do PowerShell usados para acessar credenciais

Os cmdlets na tabela a seguir são usados para criar e gerenciar credenciais de Automação com o PowerShell. Eles são fornecidos como parte dos [módulos AZ](modules.md#az-modules).

| Cmdlet | Descrição |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential) |Recupera um objeto [CredentialInfo](/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo) que contém metadados sobre a credencial. O cmdlet não recupera o objeto `PSCredential` em si.  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential) |Cria uma nova credencial de Automação. |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential) |Remove uma credencial de Automação. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential) |Define as propriedades de uma credencial de Automação existente. |

## <a name="other-cmdlets-used-to-access-credentials"></a>Outros cmdlets usados para acessar credenciais

Os cmdlets na tabela a seguir são usados para acessar credenciais em seus runbooks ou em uma configuração DSC. 

| Cmdlet | Descrição |
|:--- |:--- |
| `Get-AutomationPSCredential` |Obtém um objeto `PSCredential` a ser usado em uma configuração de DSC ou runbook. Geralmente, você deve usar esse [cmdlet interno](modules.md#internal-cmdlets) em vez do cmdlet `Get-AzAutomationCredential`, pois o último recupera apenas informações de credenciais. Normalmente, essas informações não são úteis para serem passadas para outro cmdlet. |
| [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) |Obtém uma credencial com um prompt para um nome de usuário e senha. Esse cmdlet faz parte do módulo padrão Microsoft.PowerShell.Security. Consulte [Módulos padrão](modules.md#default-modules).|
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential) | Cria um ativo de credencial. Esse cmdlet faz parte do módulo padrão do Azure. Consulte [Módulos padrão](modules.md#default-modules).|

Para recuperar objetos `PSCredential` em seu código, você deve importar o módulo `Orchestrator.AssetManagement.Cmdlets`. Para obter mais informações, confira [Gerenciar módulos na Automação do Azure](modules.md).

```powershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Evite usar variáveis no parâmetro `Name` de `Get-AutomationPSCredential`. Seu uso pode complicar a descoberta de dependências entre runbooks ou configurações DSC e ativos de credencial em tempo de design.

## <a name="python-functions-that-access-credentials"></a>Funções do Python que acessam credenciais

A função na tabela a seguir é usada para acessar credenciais em um runbook do Python 2 e 3. Atualmente, os runbooks do Python 3 estão em versão prévia.

| Função | Descrição |
|:---|:---|
| `automationassets.get_automation_credential` | Recupera informações sobre um ativo de credencial. |

> [!NOTE]
> É necessário importar o módulo `automationassets`, na parte superior do runbook do Python para acessar as funções do ativo.

## <a name="create-a-new-credential-asset"></a>Criar um novo ativo de credencial

Você pode criar um novo ativo de credencial usando o portal do Azure ou o Windows PowerShell.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Criar um novo ativo de credencial com o portal do Azure

1. Na sua conta de automação, no painel esquerdo, selecione **credenciais** em **recursos compartilhados**.
2. Na página **credenciais** , selecione **Adicionar uma credencial**.
3. No painel Nova Credencial, insira um nome de credencial apropriado de acordo com seus padrões de nomenclatura.
4. Digite sua ID de acesso no campo **Nome de usuário**.
5. Para ambos os campos de senha, insira sua chave de acesso secreta.

    ![Criar uma nova credencial](../media/credentials/credential-create.png)

6. Se a caixa autenticação multifator estiver marcada, desmarque-a.
7. Clique em **Criar** para salvar o novo ativo de credencial.

> [!NOTE]
> A Automação do Azure não tem suporte para as contas de usuário que usam autenticação multifator.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Criar um novo ativo de credencial com o Windows PowerShell

O exemplo a seguir mostra como criar um novo ativo de credencial de Automação. Um objeto `PSCredential` é criado pela primeira vez com o nome e a senha e, em seguida, é usado para criar o ativo de credencial. Em vez disso, você pode usar o cmdlet `Get-Credential` para solicitar que o usuário digite um nome e uma senha.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="get-a-credential-asset"></a>Obter um ativo de credencial

Uma configuração de DSC ou runbook recupera um ativo de credencial com o cmdlet `Get-AutomationPSCredential` interno. Esse cmdlet obtém um objeto `PSCredential` que você pode usar com um cmdlet que requer uma credencial. Você também pode recuperar as propriedades do objeto de credencial para usar individualmente. O objeto tem propriedades para o nome de usuário e a senha segura. 

> [!NOTE]
> O cmdlet `Get-AzAutomationCredential` não recupera um objeto `PSCredential` que pode ser usado para autenticação. Ele apenas fornece informações sobre a credencial. Se você precisar usar uma credencial em um runbook, deverá recuperá-la como um objeto `PSCredential` usando `Get-AutomationPSCredential`.

Como alternativa, você pode usar o método [GetNetworkCredential](/dotnet/api/system.management.automation.pscredential.getnetworkcredential) para recuperar um objeto do [NetworkCredential](/dotnet/api/system.net.networkcredential) que representa uma versão não segura da senha.

### <a name="textual-runbook-example"></a>Exemplo de runbook textual

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

O exemplo a seguir mostra como usar uma credencial do PowerShell em um runbook. Ele recupera a credencial e atribui seu nome de usuário e senha a variáveis.

```powershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Você também pode usar uma credencial para autenticar no Azure com [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Na maioria das circunstâncias, você deve usar uma [conta Executar Como](../automation-security-overview.md#run-as-accounts) e recuperar a conexão com [Get-AzAutomationConnection](../automation-connections.md).

```powershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$securePassword)

Connect-AzAccount -Credential $myPsCred
```

# <a name="python-2"></a>[Python 2](#tab/python2)

O exemplo a seguir mostra um exemplo de acesso a credenciais em runbooks do Python 2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

# <a name="python-3"></a>[Python 3](#tab/python3)

O exemplo a seguir mostra um exemplo de acesso a credenciais em runbooks do Python 3 (versão prévia).

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print (cred["username"])
print (cred["password"])
```

---

### <a name="graphical-runbook-example"></a>Exemplo de runbook gráfico

Para adicionar uma atividade para o cmdlet `Get-AutomationPSCredential` interno a um runbook gráfico, você pode clicar com o botão direito do mouse na credencial no painel Biblioteca do editor gráfico e selecionar **Adicionar à tela**.

![Adicionar cmdlet de credencial à tela](../media/credentials/credential-add-canvas.png)

A imagem a seguir mostra um exemplo do uso de uma credencial em um runbook gráfico. Nesse caso, a credencial fornece autenticação para um runbook para recursos do Azure, conforme descrito em [Usar o Azure Active Directory na Automação do Azure para autenticar no Azure](../automation-use-azure-ad.md). A primeira atividade recupera a credencial que tem acesso à assinatura do Azure. A atividade de conexão de conta usa essa credencial para fornecer autenticação para qualquer atividade que vier depois dela. Um [link de pipeline](../automation-graphical-authoring-intro.md#use-links-for-workflow) é usado aqui, pois `Get-AutomationPSCredential` está esperando um único objeto.  

![Exemplo de fluxo de trabalho de credenciais com link de pipeline](../media/credentials/get-credential.png)

## <a name="use-credentials-in-a-dsc-configuration"></a>Usar credenciais em uma configuração DSC

Embora as configurações de DSC na Automação do Azure possam trabalhar com ativos de credencial que usam `Get-AutomationPSCredential`, elas também podem passar ativos de credencial por meio de parâmetros. Para obter mais informações, veja [Compilando configurações na DSC de Automação do Azure](../automation-dsc-compile.md#credential-assets).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os cmdlets usados para acessar certificados, consulte [Gerenciar módulos na Automação do Azure](modules.md).
* Para obter informações gerais sobre runbooks, consulte [Execução de runbook na Automação do Azure](../automation-runbook-execution.md).
* Para obter detalhes sobre as configurações de DSC, consulte [visão geral do State Configuration da Automação do Azure](../automation-dsc-overview.md).