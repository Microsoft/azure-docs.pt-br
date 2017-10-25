---
title: "Passar um objeto JSON para um runbook de Automação do Azure | Microsoft Docs"
description: "Como passar parâmetros para um runbook como um objeto JSON"
services: automation
documentationcenter: dev-center-name
author: eslesar
manager: carmonm
keywords: "powershell,  runbook, json, automação do azure"
ms.service: automation
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: TBD
ms.date: 06/15/2017
ms.author: eslesar
ms.openlocfilehash: eac0e95a46731b9d396ea0590e629d61ca6a7d70
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="pass-a-json-object-to-an-azure-automation-runbook"></a>Passar um objeto JSON para um runbook de Automação do Azure

Isso pode ser útil para armazenar os dados que você deseja passar para um runbook em um arquivo JSON.
Por exemplo, você pode criar um arquivo JSON que contém todos os parâmetros que você deseja passar para um runbook.
Para fazer isso, você precisa converter o JSON para uma cadeia de caracteres e, em seguida, converter a cadeia de caracteres em um objeto do PowerShell antes de passar o seu conteúdo para o runbook.

Neste exemplo, vamos criar um script do PowerShell que chama [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) para iniciar um runbook do PowerShell, passando o conteúdo de JSON para o runbook.
O runbook do PowerShell inicia uma VM do Azure, obtendo os parâmetros para a VM de JSON que foi passado.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você precisará do seguinte:

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou <a href="/pricing/free-account/" target="_blank">[inscrever-se em uma conta gratuita](https://azure.microsoft.com/free/).
* [Conta de automação](automation-sec-configure-azure-runas-account.md) para manter o runbook e se autenticar nos recursos do Azure.  Esta conta deve ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Paramos e iniciamos o computador, portanto, ele não deve ser uma VM de produção.
* Azure PowerShell instalado em um computador local. Consulte [Install and configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.1.0) (Instalar e configurar o Azure PowerShell) para obter informações sobre como obter o Azure PowerShell.

## <a name="create-the-json-file"></a>Criar o arquivo JSON

Digite o seguinte teste em um arquivo de texto e salve-o como `test.json` em algum lugar no seu computador local.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

## <a name="create-the-runbook"></a>Criar o runbook

Crie um novo runbook PowerShell chamado "Test-Json" na Automação do Azure.
Para saber como criar um novo runbook do PowerShell, consulte [Meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md).

Para aceitar os dados JSON, o runbook deve utilizar um objeto como um parâmetro de entrada.

O runbook pode, então, usar as propriedades definidas no JSON.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account   
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzureRmVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
 ```

 Salve e publique este runbook na sua conta de Automação.

## <a name="call-the-runbook-from-powershell"></a>Chamar o runbook do PowerShell

Agora você pode chamar o runbook de seu computador local usando o Azure PowerShell.
Execute os seguintes comandos do PowerShell:

1. Faça logon no Azure:
   ```powershell
   Login-AzureRmAccount
   ```
    Você recebe uma solicitação para inserir suas credenciais do Azure.
1. Obtenha o conteúdo do arquivo JSON e converta-o em uma cadeia de caracteres:
    ```powershell
    $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
    ```
    `JsonPath` é o caminho em que você salvou o arquivo JSON.
1. Converta o conteúdo da cadeia de caracteres do `$json` em um objeto do PowerShell:
   ```powershell
   $JsonParams = @{"json"=$json}
   ```
1. Crie uma tabela de hash para os parâmetros para `Start-AzureRmAutomstionRunbook`:
   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```
   Observe que você está definindo o valor de `Parameters` para o objeto do PowerShell que contém os valores do arquivo JSON. 
1. Iniciar o runbook
   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

O runbook usa os valores do arquivo JSON para iniciar uma VM.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como editar runbooks do PowerShell e do Fluxo de Trabalho do PowerShell com um editor de texto, consulte [Editando runbooks textuais na Automação do Azure](automation-edit-textual-runbook.md) 
* Para saber mais sobre a criação e a importação de runbooks, consulte [Criando ou importando um runbook na Automação do Azure](automation-creating-importing-runbook.md)


