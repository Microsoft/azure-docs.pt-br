---
title: Usar scripts de implantação de modelo | Microsoft Docs
description: Saiba como usar scripts de implantação em modelos do ARM (modelos do Azure Resource Manager).
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 12/16/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 36fb54b4b6521d87c7461936c84a644bf22f7e31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97963956"
---
# <a name="tutorial-use-deployment-scripts-to-create-a-self-signed-certificate"></a>Tutorial: Usar scripts de implantação para criar um certificado autoassinado

Saiba como usar scripts de implantação em modelos do ARM (modelos do Azure Resource Manager). Os scripts de implantação podem ser usados para executar etapas personalizadas que não podem ser feitas por modelos do ARM. Por exemplo, criar um certificado autoassinado. Neste tutorial, você criará um modelo para implantar um Azure Key Vault e, em seguida, usará um recurso `Microsoft.Resources/deploymentScripts` no mesmo modelo para criar um certificado e adicioná-lo ao cofre de chaves. Para saber mais sobre o script de implantação, confira [Usar scripts de implantação em modelos do ARM](./deployment-script-template.md).

> [!IMPORTANT]
> Dois recursos de script de implantação, uma conta de armazenamento e uma instância de contêiner, são criados no mesmo grupo de recursos para execução de script e solução de problemas. Esses recursos geralmente são excluídos pelo serviço de script quando a execução de script entra em um estado terminal. Você será cobrado pelos recursos até que eles sejam excluídos. Para saber mais, confira [Limpar recursos do script de implantação](./deployment-script-template.md#clean-up-deployment-script-resources).

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Abrir um modelo de início rápido
> * Editar o modelo
> * Implantar o modelo
> * Depurar o script com falha
> * Limpar os recursos

Para ver um módulo do Microsoft Learn que aborda os scripts de implantação, confira [Estender modelos do ARM usando scripts de implantação](/learn/modules/extend-resource-manager-template-deployment-scripts/).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* **[Visual Studio Code](https://code.visualstudio.com/) com a extensão Ferramentas do Resource Manager**. Confira [Início Rápido: Criar modelos do ARM com o Visual Studio Code](./quickstart-create-templates-use-visual-studio-code.md).

* **Uma identidade gerenciada atribuída pelo usuário**. Essa identidade é usada para executar ações específicas do Azure no script. Para criar uma, confira [Identidade gerenciada atribuída por usuário](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). Você precisa da ID da identidade ao implantar o modelo. O formato da identidade é:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Use o script da CLI a seguir para obter a ID fornecendo o nome do grupo de recursos e o nome da identidade.

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  az identity list -g $resourceGroupName
  ```

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

Em vez de criar um modelo do zero, você pode abrir um modelo de [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/). Modelos de Início Rápido do Azure é um repositório de modelos do ARM.

O modelo usado neste início rápido é chamado de [Criar um Azure Key Vault e um segredo](https://azure.microsoft.com/resources/templates/101-key-vault-create/). O modelo cria um cofre de chaves e adiciona um segredo a ele.

1. No Visual Studio Code, escolha **Arquivo** > **Abrir Arquivo**.
2. Em **Nome do arquivo**, cole a seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-key-vault-create/azuredeploy.json
    ```

3. Escolha **Abrir** para abrir o arquivo.
4. Escolha **Arquivo** > **Salvar como** para salvar o arquivo como _azuredeploy.json_ em seu computador local.

## <a name="edit-the-template"></a>Editar o modelo

Faça as alterações a seguir no modelo:

### <a name="clean-up-the-template-optional"></a>Limpar o modelo (opcional)

O modelo original adiciona um segredo ao cofre de chaves. Para simplificar o tutorial, remova o seguinte recurso:

* `Microsoft.KeyVault/vaults/secrets`

Remova as duas seguintes definições de parâmetro:

* `secretName`
* `secretValue`

Se optar por não remover essas definições, você precisará especificar os valores de parâmetro durante a implantação.

### <a name="configure-the-key-vault-access-policies"></a>Configurar as políticas de acesso do cofre de chaves

O script de implantação adiciona um certificado ao cofre de chaves. Configure as políticas de acesso do cofre de chaves para fornecer a permissão à identidade gerenciada:

1. Adicione um parâmetro para obter a ID da identidade gerenciada:

    ```json
    "identityId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the ID of the user-assigned managed identity."
      }
    },
    ```

    > [!NOTE]
    > A extensão do modelo do Resource Manager do Visual Studio Code ainda não é capaz de formatar scripts de implantação. Não use Shift+Alt+F para formatar os recursos `deploymentScripts`, como o descrito a seguir.

1. Adicione um parâmetro para configurar as políticas de acesso do cofre de chaves para que a identidade gerenciada possa adicionar certificados ao cofre de chaves:

    ```json
    "certificatesPermissions": {
      "type": "array",
      "defaultValue": [
        "get",
        "list",
        "update",
        "create"
      ],
      "metadata": {
      "description": "Specifies the permissions to certificates in the vault. Valid values are: all, get, list, update, create, import, delete, recover, backup, restore, manage contacts, manage certificate authorities, get certificate authorities, list certificate authorities, set certificate authorities, delete certificate authorities."
      }
    }
    ```

1. Atualize as políticas de acesso do cofre de chaves existente a:

    ```json
    "accessPolicies": [
      {
        "objectId": "[parameters('objectId')]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      },
      {
        "objectId": "[reference(parameters('identityId'), '2018-11-30').principalId]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      }
    ],
    ```

    Há duas políticas definidas, uma para o usuário conectado e a outra para a identidade gerenciada. O usuário conectado precisa apenas da permissão de *lista* para verificar a implantação. Para simplificar o tutorial, o mesmo certificado é atribuído à identidade gerenciada e aos usuários conectados.

### <a name="add-the-deployment-script"></a>Adicionar o script de implantação

1. Adicione três parâmetros usados pelo script de implantação:

    ```json
    "certificateName": {
      "type": "string",
      "defaultValue": "DeploymentScripts2019"
    },
    "subjectName": {
      "type": "string",
      "defaultValue": "CN=contoso.com"
    },
    "utcValue": {
      "type": "string",
      "defaultValue": "[utcNow()]"
    }
    ```

1. Adicione um recurso `deploymentScripts`:

    > [!NOTE]
    > Como os scripts de implantação embutidos são colocados entre aspas duplas, as cadeias de caracteres dentro dos scripts de implantação precisam ser colocadas entre aspas simples. O [caractere de escape do PowerShell](/powershell/module/microsoft.powershell.core/about/about_quoting_rules#single-and-double-quoted-strings) é o acento grave (`` ` ``).

    ```json
    {
      "type": "Microsoft.Resources/deploymentScripts",
      "apiVersion": "2020-10-01",
      "name": "createAddCertificate",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
      ],
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[parameters('identityId')]": {
          }
        }
      },
      "kind": "AzurePowerShell",
      "properties": {
        "forceUpdateTag": "[parameters('utcValue')]",
        "azPowerShellVersion": "3.0",
        "timeout": "PT30M",
        "arguments": "[format(' -vaultName {0} -certificateName {1} -subjectName {2}', parameters('keyVaultName'), parameters('certificateName'), parameters('subjectName'))]", // can pass an argument string, double quotes must be escaped
        "scriptContent": "
          param(
            [string] [Parameter(Mandatory=$true)] $vaultName,
            [string] [Parameter(Mandatory=$true)] $certificateName,
            [string] [Parameter(Mandatory=$true)] $subjectName
          )

          $ErrorActionPreference = 'Stop'
          $DeploymentScriptOutputs = @{}

          $existingCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

          if ($existingCert -and $existingCert.Certificate.Subject -eq $subjectName) {

            Write-Host 'Certificate $certificateName in vault $vaultName is already present.'

            $DeploymentScriptOutputs['certThumbprint'] = $existingCert.Thumbprint
            $existingCert | Out-String
          }
          else {
            $policy = New-AzKeyVaultCertificatePolicy -SubjectName $subjectName -IssuerName Self -ValidityInMonths 12 -Verbose

            # private key is added as a secret that can be retrieved in the Resource Manager template
            Add-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName -CertificatePolicy $policy -Verbose

            $newCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

            # it takes a few seconds for KeyVault to finish
            $tries = 0
            do {
              Write-Host 'Waiting for certificate creation completion...'
              Start-Sleep -Seconds 10
              $operation = Get-AzKeyVaultCertificateOperation -VaultName $vaultName -Name $certificateName
              $tries++

              if ($operation.Status -eq 'failed')
              {
                throw 'Creating certificate $certificateName in vault $vaultName failed with error $($operation.ErrorMessage)'
              }

              if ($tries -gt 120)
              {
                throw 'Timed out waiting for creation of certificate $certificateName in vault $vaultName'
              }
            } while ($operation.Status -ne 'completed')

            $DeploymentScriptOutputs['certThumbprint'] = $newCert.Thumbprint
            $newCert | Out-String
          }
        ",
        "cleanupPreference": "OnSuccess",
        "retentionInterval": "P1D"
      }
    }
    ```

    O recurso `deploymentScripts` depende do recurso do cofre de chaves e do recurso de atribuição de função. Ele tem estas propriedades:

    * `identity`: o script de implantação usa uma identidade gerenciada atribuída pelo usuário para executar as operações no script.
    * `kind`: especifica o tipo de script. Atualmente, há suporte apenas para scripts do PowerShell.
    * `forceUpdateTag`: determine se o script de implantação deverá ser executado mesmo se a origem do script não tiver sido alterada. Pode ser o carimbo de data/hora atual ou um GUID. Para saber mais, confira [Executar script mais de uma vez](./deployment-script-template.md#run-script-more-than-once).
    * `azPowerShellVersion`: especifica a versão do módulo do Azure PowerShell a ser usada. No momento, o script de implantação é compatível com as versões 2.7.0, 2.8.0 e 3.0.0.
    * `timeout`: especifique o tempo de execução máximo permitido do script especificado no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). O valor padrão é **P1D**.
    * `arguments`: Especifique os valores de parâmetro. os valores são separados por espaços.
    * `scriptContent`: especifique o conteúdo do script. Para executar um script externo, use `primaryScriptURI`. Para obter mais informações, confira [Usar script externo](./deployment-script-template.md#use-external-scripts).
        Declarar `$DeploymentScriptOutputs` só é necessário ao testar o script em um computador local. Declarar a variável permite que o script seja executado em um computador local e em um recurso `deploymentScript` sem precisar fazer alterações. O valor atribuído a `$DeploymentScriptOutputs` está disponível como saídas na implantação. Para obter mais informações, confira [Trabalhar com saídas de scripts de implantação do PowerShell](./deployment-script-template.md#work-with-outputs-from-powershell-script) ou [Trabalhar com saídas de scripts de implantação da CLI](./deployment-script-template.md#work-with-outputs-from-cli-script).
    * `cleanupPreference`: especifique a preferência sobre quando excluir os recursos de script de implantação. O valor padrão é **Sempre**, o que significa que os recursos do script de implantação são excluídos apesar do estado terminal (Com êxito, Com falha, Cancelado). Neste tutorial, **OnSuccess** é usado para que você tenha a oportunidade de exibir os resultados da execução do script.
    * `retentionInterval`: especifique o intervalo para o qual o serviço reterá os recursos de script após atingir um estado terminal. Os recursos serão excluídos quando essa duração expirar. A duração é baseada no padrão ISO 8601. Este tutorial usa **P1D**, que significa um dia. Essa propriedade é usada quando `cleanupPreference` está configurado como **OnExpiration**. Essa propriedade não está habilitada no momento.

    O script de implantação usa três parâmetros: `keyVaultName`, `certificateName` e `subjectName`. Ele cria um certificado e o adiciona ao cofre de chaves.

    `$DeploymentScriptOutputs` é usado para armazenar o valor de saída. Para saber mais, confira [Trabalhar com saídas de scripts de implantação do PowerShell](./deployment-script-template.md#work-with-outputs-from-powershell-script) ou [Trabalhar com saídas de scripts de implantação da CLI](./deployment-script-template.md#work-with-outputs-from-cli-script).

    O modelo completo pode ser encontrado [aqui](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json).

1. Para ver o processo de depuração, insira um erro no código adicionando a seguinte linha ao script de implantação:

    ```powershell
    Write-Output1 $keyVaultName
    ```

    O comando correto é `Write-Output` em vez de `Write-Output1`.

1. Escolha **Arquivo** > **Salvar** para salvar o arquivo.

## <a name="deploy-the-template"></a>Implantar o modelo

1. Entrar no [Azure Cloud Shell](https://shell.azure.com)

1. Escolha seu ambiente preferencial selecionando **PowerShell** ou **Bash** (para a CLI) no canto superior esquerdo. Ao alternar, é necessário reiniciar o shell.

    ![Carregar arquivo do Cloud Shell no portal do Azure](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Escolha **Carregar/fazer o download dos arquivos** e, em seguida, escolha **Carregar**. Consulte a captura de tela anterior.  Selecione o arquivo que você salvou na seção anterior. Depois de carregar o arquivo, use os comandos `ls` e `cat` para verificar se o arquivo foi carregado com êxito.

1. Execute o script do PowerShell a seguir para implantar o modelo.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
    $identityId = Read-Host -Prompt "Enter the user-assigned managed identity ID"

    $adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
    $resourceGroupName = "${projectName}rg"
    $keyVaultName = "${projectName}kv"

    New-AzResourceGroup -Name $resourceGroupName -Location $location

    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -identityId $identityId -keyVaultName $keyVaultName -objectId $adUserId

    Write-Host "Press [ENTER] to continue ..."
    ```

    O serviço de script de implantação precisa criar outros recursos de script de implantação para a execução do script. Pode levar até um minuto para que a preparação e o processo de limpeza sejam concluídos, além do tempo de execução do script real.

    A implantação falhou devido ao comando inválido `Write-Output1` usado no script. Você receberá um erro dizendo:

    ```error
    The term 'Write-Output1' is not recognized as the name of a cmdlet, function, script file, or operable
    program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.
    ```

    O resultado da execução do script de implantação é armazenado nos recursos do script de implantação para a finalidade de solução de problemas.

## <a name="debug-the-failed-script"></a>Depurar o script com falha

1. Entre no [portal do Azure](https://portal.azure.com).
1. Abra o grupo de recursos. É o nome do projeto com **rg** acrescentado. Você verá dois recursos adicionais no grupo de recursos. Esses recursos são chamados de *recursos de script de implantação*.

    ![Recursos do script da implantação de modelo do Resource Manager](./media/template-tutorial-deployment-script/resource-manager-template-deployment-script-resources.png)

    Os dois arquivos têm o sufixo _azscripts_. Um é uma conta de armazenamento e o outro é uma instância de contêiner.

    Selecione **Mostrar tipos ocultos** para listar o recurso `deploymentScripts`.

1. Selecione a conta de armazenamento com o sufixo _azscripts_.
1. Selecione o bloco **Compartilhamentos de arquivo**. Você verá uma pasta _azscripts_ que contém os arquivos de execução do script de implantação.
1. Selecione _azscripts_. Você deverá ver duas pastas _azscriptinput_ e _azscriptoutput_. A pasta de entrada contém um arquivo de script do PowerShell do sistema e os arquivos de script da implantação do usuário. A pasta de saída contém um _executionresult.json_ e o arquivo de saída de script. Você pode ver a mensagem de erro em _executionresult.json_. O arquivo de saída não está lá porque a execução falhou.

Remova a linha de `Write-Output1` e reimplante o modelo.

Quando a segunda implantação for executada com êxito, os recursos do script de implantação serão removidos pelo serviço de script, porque a propriedade `cleanupPreference` foi definida como **OnSuccess**.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.  Você verá um total de seis recursos no grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar o script de implantação nos modelos do ARM. Para saber como implantar recursos do Azure com base em condições, veja:

> [!div class="nextstepaction"]
> [Condições de uso](./template-tutorial-use-conditions.md)
