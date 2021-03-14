---
title: Usar scripts de implantação em modelos | Microsoft Docs
description: Use scripts de implantação em modelos do Azure Resource Manager.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 12/28/2020
ms.author: jgao
ms.openlocfilehash: 9d045fb75838ac016f3e9b04cd2519d8a8530a4b
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175644"
---
# <a name="use-deployment-scripts-in-arm-templates"></a>Usar scripts de implantação em modelos ARM

Saiba como usar scripts de implantação em modelos de recursos do Azure (modelos ARM). Com um novo tipo de recurso chamado `Microsoft.Resources/deploymentScripts` , os usuários podem executar scripts em implantações de modelo e examinar os resultados da execução. Esses scripts podem ser usados para executar etapas personalizadas, tais como:

- adicionar usuários a um diretório
- executar operações do plano de dados como, por exemplo, copiar blobs ou bancos de dados de semente
- pesquisar e validar uma chave de licença
- criar um certificado autoassinado
- criar um objeto no Azure Active Directory
- pesquisar blocos de endereços IP do sistema personalizado

Estes são os benefícios do script de implantação:

- Fácil de codificar, usar e depurar. Você pode desenvolver scripts de implantação em seus ambientes de desenvolvimento preferidos. Os scripts podem ser inseridos em modelos ou arquivos de script externos.
- Você pode especificar a linguagem de script e a plataforma. Atualmente, há suporte para scripts de implantação do Azure PowerShell e do CLI do Azure em ambiente Linux.
- Permita a passagem de argumentos de linha de comando para o script.
- É possível especificar saídas de script e passá-las de volta para a implantação.

O recurso de script de implantação só está disponível nas regiões em que a Instância de Contêiner do Azure está disponível.  Consulte [Disponibilidade de recursos para Instâncias de Contêiner do Azure nas regiões do Azure](../../container-instances/container-instances-region-availability.md).

> [!IMPORTANT]
> Uma conta de armazenamento e uma instância de contêiner são necessárias para executar o script e solucionar problemas. Você tem as opções necessárias para especificar uma conta de armazenamento existente, caso contrário, o serviço de script criará automaticamente a conta de armazenamento junto com a instância de contêiner. Os dois recursos criados automaticamente são geralmente excluídos pelo serviço de script quando a execução do script de implantação entra em um estado terminal. Você será cobrado pelos recursos até que eles sejam excluídos. Para saber mais, consulte [Limpar recursos do script de implantação](#clean-up-deployment-script-resources).

> [!IMPORTANT]
> A API de recurso deploymentScripts versão 2020-10-01 dá suporte a [OnBehalfofTokens (obo)](../../active-directory/develop/v2-oauth2-on-behalf-of-flow.md). Usando o OBO, o serviço de script de implantação usa o token da entidade de implantação para criar os recursos subjacentes para executar scripts de implantação, que incluem instância de contêiner do Azure, conta de armazenamento do Azure e atribuições de função para a identidade gerenciada. Na versão mais antiga da API, a identidade gerenciada é usada para criar esses recursos.
> A lógica de repetição para entrar no Azure agora está incorporada ao script de wrapper. Se você conceder permissões no mesmo modelo em que você executa scripts de implantação. O serviço de script de implantação tenta entrar por 10 minutos com intervalo de 10 segundos até que a atribuição de função de identidade gerenciada seja replicada.

## <a name="configure-the-minimum-permissions"></a>Configurar as permissões mínimas

Para a API de script de implantação versão 2020-10-01 ou posterior, a entidade de implantação é usada para criar recursos subjacentes necessários para que o recurso de script de implantação seja executado — uma conta de armazenamento e uma instância de contêiner do Azure. Se o script precisar se autenticar no Azure e executar ações específicas do Azure, é recomendável fornecer o script com uma identidade gerenciada atribuída pelo usuário. A identidade gerenciada deve ter o acesso necessário para concluir a operação no script.

Para configurar as permissões de privilégios mínimos, você precisará de:

- Atribua uma função personalizada com as seguintes propriedades à entidade de implantação:

  ```json
  {
    "roleName": "deployment-script-minimum-privilege-for-deployment-principal",
    "description": "Configure least privilege for the deployment principal in deployment script",
    "type": "customRole",
    "IsCustom": true,
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/storageAccounts/*",
          "Microsoft.ContainerInstance/containerGroups/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/deploymentScripts/*"
        ],
      }
    ],
    "assignableScopes": [
      "[subscription().id]"
    ]
  }
  ```

  Se o armazenamento do Azure e os provedores de recursos da instância de contêiner do Azure não tiverem sido registrados, você também precisará adicionar `Microsoft.Storage/register/action` e `Microsoft.ContainerInstance/register/action` .

- Se uma identidade gerenciada for usada, a entidade de implantação precisará da função de **operador de identidade gerenciada** (uma função interna) atribuída ao recurso de identidade gerenciada.

## <a name="sample-templates"></a>Modelos de exemplo

O JSON a seguir é um exemplo. Para obter mais informações, consulte o [esquema de modelo](/azure/templates/microsoft.resources/deploymentscripts)mais recente.

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2020-10-01",
  "name": "runPowerShellInline",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell", // or "AzureCLI"
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": "1",
    "containerSettings": {
      "containerGroupName": "mycustomaci"
    },
    "storageAccountSettings": {
      "storageAccountName": "myStorageAccount",
      "storageAccountKey": "myKey"
    },
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80",
    "arguments": "-name \\\"John Dole\\\"",
    "environmentVariables": [
      {
        "name": "UserName",
        "value": "jdole"
      },
      {
        "name": "Password",
        "secureValue": "jDolePassword"
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}. The username is {1}, the password is {2}.' -f $name,${Env:UserName},${Env:Password}
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ", // or "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> O exemplo é para fins de demonstração. As propriedades `scriptContent` e `primaryScriptUri` não podem coexistir em um modelo.

Detalhes do valor da propriedade:

- `identity`: Para a API de script de implantação versão 2020-10-01 ou posterior, uma identidade gerenciada atribuída pelo usuário é opcional, a menos que você precise executar ações específicas do Azure no script.  Para a versão de API 2019-10-01-Preview, uma identidade gerenciada é necessária, pois o serviço de script de implantação a usa para executar os scripts. No momento, somente há suporte para a identidade gerenciada atribuída pelo usuário.
- `kind`: especifica o tipo de script. Atualmente, há suporte para scripts Azure PowerShell e CLI do Azure. Os valores são **AzurePowerShell** e **AzureCLI**.
- `forceUpdateTag`: A alteração desse valor entre implantações de modelo força o script de implantação a ser executado novamente. Se você usar o `newGuid()` ou as `utcNow()` funções, ambas as funções só poderão ser usadas no valor padrão para um parâmetro. Para saber mais, confira [Executar script mais de uma vez](#run-script-more-than-once).
- `containerSettings`: Especifique as configurações para personalizar a instância de contêiner do Azure.  `containerGroupName` é para especificar o nome do grupo de contêineres. Se não for especificado, o nome do grupo será gerado automaticamente.
- `storageAccountSettings`: Especifique as configurações para usar uma conta de armazenamento existente. Se não for especificado, uma conta de armazenamento será criada automaticamente. Consulte [Usar uma conta de armazenamento existente](#use-existing-storage-account).
- `azPowerShellVersion`/`azCliVersion`: Especifique a versão do módulo a ser usada. Consulte uma lista de [versões de Azure PowerShell com suporte](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list). Consulte uma lista de [versões de CLI do Azure com suporte](https://mcr.microsoft.com/v2/azure-cli/tags/list).

  >[!IMPORTANT]
  > O script de implantação usa as imagens da CLI disponíveis do registro de contêiner da Microsoft (MCR). A certificação de uma imagem da CLI para o script de implantação leva cerca de um mês. Não use as versões da CLI que foram lançadas dentro de 30 dias. Para localizar as datas de lançamento das imagens, consulte as [notas de versão da CLI do Azure](/cli/azure/release-notes-azure-cli). Se uma versão sem suporte for usada, a mensagem de erro listará as versões com suporte.

- `arguments`: Especifique os valores de parâmetro. os valores são separados por espaços.

  Os scripts de implantação dividem os argumentos em uma matriz de cadeias de caracteres invocando a chamada do sistema [CommandLineToArgvW ](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw) . Essa etapa é necessária porque os argumentos são passados como uma [propriedade de comando](/rest/api/container-instances/containergroups/createorupdate#containerexec) para a instância de contêiner do Azure e a propriedade de comando é uma matriz de cadeia de caracteres.

  Se os argumentos contiverem caracteres de escape, use [JsonEscaper](https://www.jsonescaper.com/) para dobrar o escape dos caracteres. Cole a cadeia de caracteres de escape original na ferramenta e selecione **escape**.  A ferramenta gera uma cadeia de caracteres com escape duplo. Por exemplo, no modelo de exemplo anterior, o argumento é `-name \"John Dole\"` . A cadeia de caracteres de escape é `-name \\\"John Dole\\\"` .

  Para passar um parâmetro de modelo ARM do tipo Object como um argumento, converta o objeto em uma cadeia de caracteres usando a função [String ()](./template-functions-string.md#string) e, em seguida, use a função [replace ()](./template-functions-string.md#replace) para substituir qualquer `\"` `\\\"` . Por exemplo: 

  ```json
  replace(string(parameters('tables')), '\"', '\\\"')
  ```

  Para obter mais informações, consulte o [modelo de exemplo](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-jsonEscape.json).

- `environmentVariables`: Especifique as variáveis de ambiente para passar para o script. Para obter mais informações, consulte [Desenvolver scripts de implantação](#develop-deployment-scripts).
- `scriptContent`: especifique o conteúdo do script. Para executar um script externo, use `primaryScriptUri`. Para mais exemplos, consulte [Usar script embutido](#use-inline-scripts) e [Usar script externo](#use-external-scripts).
  > [!NOTE]
  > O portal do Azure não pode analisar um script de implantação com várias linhas. Para implantar um modelo com o script de implantação do portal do Azure, você pode encadear os comandos do PowerShell usando ponto e vírgula em uma linha ou usar a `primaryScriptUri` propriedade com um arquivo de script externo.

- `primaryScriptUri`: Especifique uma URL acessível publicamente para o script de implantação primário com as extensões de arquivo com suporte.
- `supportingScriptUris`: Especifique uma matriz de URLs acessíveis publicamente para dar suporte a arquivos que são chamados no `scriptContent` ou no `primaryScriptUri` .
- `timeout`: especifique o tempo de execução máximo permitido do script especificado no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). O valor padrão é **P1D**.
- `cleanupPreference`. Especifique a preferência de limpeza dos recursos de implantação quando a execução do script chegar a um estado terminal. A configuração padrão é **Sempre**, o que significa excluir os recursos, apesar do estado terminal (Êxito, Falha, Cancelado). Para saber mais, confira [Limpar recursos do script de implantação](#clean-up-deployment-script-resources).
- `retentionInterval`: Especifique o intervalo para o qual o serviço retém os recursos de script de implantação após a execução do script de implantação atingir um estado de terminal. Os recursos do script de implantação serão excluídos quando esse prazo expirar. A duração é baseada na [norma ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). O intervalo de retenção está entre 1 e 26 horas (PT26H). Essa propriedade é usada quando `cleanupPreference` está configurado como **OnExpiration**. A propriedade **Onexpiretion** não está habilitada no momento. Para saber mais, confira [Limpar recursos do script de implantação](#clean-up-deployment-script-resources).

### <a name="additional-samples"></a>Outros exemplos

- [Exemplo 1](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json): criar um cofre de chaves e usar o script de implantação para atribuir um certificado ao cofre de chaves.
- [Exemplo 2](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-subscription.json): Crie um grupo de recursos no nível da assinatura, crie um cofre de chaves no grupo de recursos e, em seguida, use o script de implantação para atribuir um certificado ao cofre de chaves.
- [Exemplo 3](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json): criar uma identidade gerenciada atribuída pelo usuário, atribuir a função de colaborador à identidade no nível do grupo de recursos, criar um cofre de chaves e usar o script de implantação para atribuir um certificado ao cofre de chaves.

## <a name="use-inline-scripts"></a>Usar scripts embutidos

O modelo a seguir tem um recurso definido com o tipo `Microsoft.Resources/deploymentScripts`. A parte realçada é o script embutido.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-44" highlight="24-30":::

> [!NOTE]
> Como os scripts de implantação embutidos são colocados entre aspas duplas, as cadeias de caracteres dentro dos scripts de implantação precisam ser precedidas usando uma barra invertida (**&#92;**) ou entre aspas simples. Você também pode considerar usar a substituição de cadeia de caracteres, como mostrado no exemplo de JSON anterior.

O script usa um parâmetro e a saída do valor do parâmetro. `DeploymentScriptOutputs` é usado para armazenar saídas. Na seção de saídas, a `value` linha mostra como acessar os valores armazenados. `Write-Output` é usado para fins de depuração. Para saber como acessar o arquivo de saída, consulte [monitorar e solucionar problemas de scripts de implantação](#monitor-and-troubleshoot-deployment-scripts). Para obter as descrições de propriedade, consulte [Modelos de exemplo](#sample-templates).

Para executar o script, selecione **Experimentar** para abrir o Cloud Shell, e cole o código a seguir no painel do Shell.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json"

Write-Host "Press [ENTER] to continue ..."
```

A saída se parece com isso:

![Saída “Olá, Mundo” do script de implantação do modelo do Resource Manager](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Usar scripts externos

Além dos scripts embutidos, você também pode usar arquivos de script externos. Há suporte somente para scripts primários do PowerShell com a extensão de arquivo _ps1_. Para os scripts da CLI, os scripts principais podem ter qualquer extensão (ou nenhuma extensão), desde que os scripts sejam scripts bash válidos. Para usar arquivos de script externos, substitua `scriptContent` por `primaryScriptUri`. Por exemplo:

```json
"primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Para obter mais informações, consulte o [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

Os arquivos de scripts externos devem estar acessíveis. Para proteger os arquivos de script armazenados nas contas de armazenamento do Azure, consulte [Implantar modelo de ARM privado com o token SAS](./secure-template-with-sas-token.md).

Você é responsável por garantir a integridade dos scripts que são referenciados pelo script de implantação, seja `primaryScriptUri` ou `supportingScriptUris` . Referencie somente scripts nos quais você confia.

## <a name="use-supporting-scripts"></a>Usar scripts de suporte

Você pode separar as lógicas complicadas em um ou mais arquivos de script de suporte. Se necessário, a propriedade `supportingScriptUris` permite que você forneça uma matriz de URIs para os arquivos de script de suporte:

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

Os arquivos de script de suporte podem ser chamados de scripts embutidos e arquivos de script primários. Os arquivos de script de suporte não têm restrições sobre a extensão do arquivo.

Os arquivos de suporte são copiados para `azscripts/azscriptinput` no tempo de execução. Use o caminho relativo para referenciar os arquivos de suporte de scripts embutidos e os arquivos de script primários.

## <a name="work-with-outputs-from-powershell-script"></a>Trabalhar com saídas do script do PowerShell

O modelo a seguir mostra como passar valores entre dois `deploymentScripts` recursos:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-68" highlight="30-31,50":::

No primeiro recurso, você define uma variável chamada `$DeploymentScriptOutputs` e a usa para armazenar os valores de saída. Para acessar o valor de saída de outro recurso dentro do modelo, use:

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>Trabalhar com saídas do script da CLI

Diferente do script de implantação do PowerShell, o suporte à CLI/bash não expõe uma variável comum para armazenar saídas de script, em vez disso, há uma variável de ambiente chamada `AZ_SCRIPTS_OUTPUT_PATH` que armazena o local onde o arquivo de saída de script reside. Se um script de implantação for executado a partir de um modelo do Resource Manager, essa variável de ambiente será definida automaticamente para você pelo shell Bash.

As saídas de script de implantação devem ser salvas no `AZ_SCRIPTS_OUTPUT_PATH` local e as saídas devem ser um objeto de cadeia de caracteres JSON válido. O conteúdo do arquivo deve ser salvo como um par chave-valor. Por exemplo, uma matriz de cadeias de caracteres é armazenada como `{ "MyResult": [ "foo", "bar"] }` .  Armazenar apenas os resultados da matriz, por exemplo `[ "foo", "bar" ]` , é inválido.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[jq](https://stedolan.github.io/jq/) foi usado no exemplo anterior. Ele vem com as imagens de contêiner. Consulte [Configurar o ambiente de desenvolvimento](#configure-development-environment).

## <a name="use-existing-storage-account"></a>Usar conta de armazenamento existente

Uma conta de armazenamento e uma instância de contêiner são necessárias para executar o script e solucionar problemas. Você tem as opções necessárias para especificar uma conta de armazenamento existente, caso contrário, o serviço de script criará automaticamente a conta de armazenamento junto com a instância de contêiner. Estes são os requisitos para usar uma conta de armazenamento existente:

- Os tipos de conta de armazenamento com suporte são:

    | SKU             | Tipo com suporte     |
    |-----------------|--------------------|
    | Premium_LRS     | FileStorage        |
    | Premium_ZRS     | FileStorage        |
    | Standard_GRS    | Armazenamento, StorageV2 |
    | Standard_GZRS   | StorageV2          |
    | Standard_LRS    | Armazenamento, StorageV2 |
    | Standard_RAGRS  | Armazenamento, StorageV2 |
    | Standard_RAGZRS | StorageV2          |
    | Standard_ZRS    | StorageV2          |

    Essas combinações dão suporte a compartilhamentos de arquivos. Para obter mais informações, consulte [criar um compartilhamento de arquivos do Azure](../../storage/files/storage-how-to-create-file-share.md) e [tipos de contas de armazenamento](../../storage/common/storage-account-overview.md).

- As regras de firewall da conta de armazenamento ainda não têm suporte. Para saber mais, consulte [Configurar Redes Virtuais e Firewalls de Armazenamento do Azure](../../storage/common/storage-network-security.md).
- A entidade de implantação deve ter permissões para gerenciar a conta de armazenamento, que inclui ler, criar e excluir compartilhamentos de arquivos.

Para especificar uma conta de armazenamento existente, adicione o JSON a seguir ao elemento de propriedade de `Microsoft.Resources/deploymentScripts` :

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- `storageAccountName`: especifique o nome da conta de armazenamento.
- `storageAccountKey`: especifique uma das chaves da conta de armazenamento. Você pode usar a função [listKeys ()](./template-functions-resource.md#listkeys) para recuperar a chave. Por exemplo:

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

Consulte [Modelos de exemplo](#sample-templates) para obter um exemplo de definição completo de `Microsoft.Resources/deploymentScripts`.

Quando uma conta de armazenamento existente é usada, o serviço de script cria um compartilhamento de arquivos com um nome exclusivo. Consulte [Limpar recursos de script de implantação](#clean-up-deployment-script-resources) para saber como o serviço de script limpa o compartilhamento de arquivos.

## <a name="develop-deployment-scripts"></a>Desenvolver scripts pós-implantação

### <a name="handle-non-terminating-errors"></a>Tratar de erros de não encerramento

Você pode controlar como o PowerShell responde a erros de não finalização usando a `$ErrorActionPreference` variável no script de implantação. Se a variável não estiver definida no script de implantação, o serviço de script usará o valor padrão **continue**.

O serviço de script define o estado de provisionamento de recursos como **falha** quando o script encontra um erro, apesar da configuração de `$ErrorActionPreference` .

### <a name="pass-secured-strings-to-deployment-script"></a>Passar cadeias de caracteres seguras para o script de implantação

Definir variáveis de ambiente (EnvironmentVariable) em suas instâncias de contêiner permite que você forneça a configuração dinâmica do aplicativo ou do script executado pelo contêiner. O script de implantação manipula variáveis de ambiente não seguras e protegidas da mesma maneira que a Instância de Contêiner do Azure. Para saber mais, consulte [Definir variáveis de ambiente em instâncias de contêiner](../../container-instances/container-instances-environment-variables.md#secure-values).

O tamanho máximo permitido para variáveis de ambiente é 64 KB.

## <a name="monitor-and-troubleshoot-deployment-scripts"></a>Monitorar e solucionar problemas de scripts de implantação

O serviço de script cria uma [conta de armazenamento](../../storage/common/storage-account-overview.md) (a menos que você especifique uma conta de armazenamento existente) e uma [instância de contêiner](../../container-instances/container-instances-overview.md) para execução de script. Se esses recursos forem criados automaticamente pelo serviço de script, ambos os recursos terão o `azscripts` sufixo nos nomes dos recursos.

![Nomes dos recursos de script de implantação de modelo do Resource Manager](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

O script do usuário, os resultados da execução e o arquivo stdout são armazenados nos compartilhamentos de arquivos da conta de armazenamento. Há uma pasta chamada `azscripts` . Na pasta, há mais duas pastas para a entrada e os arquivos de saída: `azscriptinput` e `azscriptoutput` .

A pasta de saída contém um _executionresult.json_ e o arquivo de saída de script. Você pode ver a mensagem de erro de execução do script em _executionresult.json_. O arquivo de saída é criado somente quando o script é executado com êxito. A pasta de entrada contém um arquivo de script do PowerShell do sistema e os arquivos de script da implantação do usuário. Você pode substituir o arquivo de script de implantação do usuário por um revisado e executar novamente o script de implantação da instância de contêiner do Azure.

### <a name="use-the-azure-portal"></a>Use o Portal do Azure

Depois de implantar um recurso de script de implantação, o recurso é listado no grupo de recursos no portal do Azure. A captura de tela a seguir mostra a página de **visão geral** de um recurso de script de implantação:

![Visão geral do portal de script de implantação de modelo do Resource Manager](./media/deployment-script-template/resource-manager-deployment-script-portal.png)

A página Visão geral exibe algumas informações importantes do recurso, como o **estado de provisionamento**, a **conta de armazenamento**, a **instância de contêiner** e **os logs**.

No menu à esquerda, você pode exibir o conteúdo do script de implantação, os argumentos passados para o script e a saída. Você também pode exportar um modelo para o script de implantação, incluindo o script de implantação.

### <a name="use-powershell"></a>Usar o PowerShell

Usando Azure PowerShell, você pode gerenciar scripts de implantação na assinatura ou no escopo do grupo de recursos:

- [Get-AzDeploymentScript](/powershell/module/az.resources/get-azdeploymentscript): Obtém ou lista os scripts de implantação.
- [Get-AzDeploymentScriptLog](/powershell/module/az.resources/get-azdeploymentscriptlog): Obtém o log de uma execução de script de implantação.
- [Remove-AzDeploymentScript](/powershell/module/az.resources/remove-azdeploymentscript): Remove um script de implantação e seus recursos associados.
- [Save-AzDeploymentScriptLog](/powershell/module/az.resources/save-azdeploymentscriptlog): salva o log de uma execução de script de implantação em disco.

A `Get-AzDeploymentScript` saída é semelhante a:

```output
Name                : runPowerShellInlineWithOutput
Id                  : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput
ResourceGroupName   : myds0618rg
Location            : centralus
SubscriptionId      : 01234567-89AB-CDEF-0123-456789ABCDEF
ProvisioningState   : Succeeded
Identity            : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/mydentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami
ScriptKind          : AzurePowerShell
AzPowerShellVersion : 3.0
StartTime           : 6/18/2020 7:46:45 PM
EndTime             : 6/18/2020 7:49:45 PM
ExpirationDate      : 6/19/2020 7:49:45 PM
CleanupPreference   : OnSuccess
StorageAccountId    : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Storage/storageAccounts/ftnlvo6rlrvo2azscripts
ContainerInstanceId : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.ContainerInstance/containerGroups/ftnlvo6rlrvo2azscripts
Outputs             :
                      Key                 Value
                      ==================  ==================
                      text                Hello John Dole

RetentionInterval   : P1D
Timeout             : PT1H
```

### <a name="use-azure-cli"></a>Usar a CLI do Azure

Usando CLI do Azure, você pode gerenciar scripts de implantação na assinatura ou no escopo do grupo de recursos:

- [AZ Deployment – scripts Delete](/cli/azure/deployment-scripts#az-deployment-scripts-delete): excluir um script de implantação.
- [AZ Deployment-lista de scripts](/cli/azure/deployment-scripts#az-deployment-scripts-list): lista todos os scripts de implantação.
- [AZ Deployment – scripts show](/cli/azure/deployment-scripts#az-deployment-scripts-show): recuperar um script de implantação.
- [AZ Deployment – scripts show-log](/cli/azure/deployment-scripts#az-deployment-scripts-show-log): Mostrar logs de script de implantação.

A saída do comando de lista é semelhante a:

```json
[
  {
    "arguments": "-name \\\"John Dole\\\"",
    "azPowerShellVersion": "3.0",
    "cleanupPreference": "OnSuccess",
    "containerSettings": {
      "containerGroupName": null
    },
    "environmentVariables": null,
    "forceUpdateTag": "20200625T025902Z",
    "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
    "identity": {
      "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
      "type": "userAssigned",
      "userAssignedIdentities": {
        "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
          "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF",
          "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF"
        }
      }
    },
    "kind": "AzurePowerShell",
    "location": "centralus",
    "name": "runPowerShellInlineWithOutput",
    "outputs": {
      "text": "Hello John Dole"
    },
    "primaryScriptUri": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myds0624rg",
    "retentionInterval": "1 day, 0:00:00",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "endTime": "2020-06-25T03:00:16.796923+00:00",
      "error": null,
      "expirationTime": "2020-06-26T03:00:16.796923+00:00",
      "startTime": "2020-06-25T02:59:07.595140+00:00",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts"
    },
    "storageAccountSettings": null,
    "supportingScriptUris": null,
    "systemData": {
      "createdAt": "2020-06-25T02:59:04.750195+00:00",
      "createdBy": "someone@contoso.com",
      "createdByType": "User",
      "lastModifiedAt": "2020-06-25T02:59:04.750195+00:00",
      "lastModifiedBy": "someone@contoso.com",
      "lastModifiedByType": "User"
    },
    "tags": null,
    "timeout": "1:00:00",
    "type": "Microsoft.Resources/deploymentScripts"
  }
]
```

### <a name="use-rest-api"></a>Usar API REST

Você pode obter as informações de implantação de recurso de script de implantação no nível do grupo de recursos e no nível da assinatura usando a API REST:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2020-10-01
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2020-10-01
```

O exemplo a seguir usa [ARMClient](https://github.com/projectkudu/ARMClient):

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2020-10-01
```

A saída deverá ser semelhante a:

```json
{
  "kind": "AzurePowerShell",
  "identity": {
    "type": "userAssigned",
    "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
        "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF",
        "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF"
      }
    }
  },
  "location": "centralus",
  "systemData": {
    "createdBy": "someone@contoso.com",
    "createdByType": "User",
    "createdAt": "2020-06-25T02:59:04.7501955Z",
    "lastModifiedBy": "someone@contoso.com",
    "lastModifiedByType": "User",
    "lastModifiedAt": "2020-06-25T02:59:04.7501955Z"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "forceUpdateTag": "20200625T025902Z",
    "azPowerShellVersion": "3.0",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "arguments": "-name \\\"John Dole\\\"",
    "retentionInterval": "P1D",
    "timeout": "PT1H",
    "containerSettings": {},
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts",
      "startTime": "2020-06-25T02:59:07.5951401Z",
      "endTime": "2020-06-25T03:00:16.7969234Z",
      "expirationTime": "2020-06-26T03:00:16.7969234Z"
    },
    "outputs": {
      "text": "Hello John Dole"
    },
    "cleanupPreference": "OnSuccess"
  },
  "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
  "type": "Microsoft.Resources/deploymentScripts",
  "name": "runPowerShellInlineWithOutput"
}

```

A API REST a seguir retorna o log:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2020-10-01
```

Ele só funciona antes de os recursos de script de implantação serem excluídos.

Para ver o recurso deploymentScripts no portal, selecione **Mostrar tipos ocultos**:

![Script de implantação de modelo do Resource Manager, mostrar tipos ocultos, portal](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Limpar recursos do script de implantação

Uma conta de armazenamento e uma instância de contêiner são necessárias para executar o script e solucionar problemas. Você tem as opções necessárias para especificar uma conta de armazenamento existente, caso contrário, uma conta de armazenamento, juntamente com uma instância de contêiner, será criada automaticamente pelo serviço de script. Os dois recursos criados automaticamente são excluídos pelo serviço de script quando a execução do script de implantação entra em um estado terminal. Você será cobrado pelos recursos até que eles sejam excluídos. Para obter as informações de preço, consulte os [Preços de Instâncias de Contêiner](https://azure.microsoft.com/pricing/details/container-instances/) e os [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

O ciclo de vida desses recursos é controlado pelas seguintes propriedades no modelo:

- `cleanupPreference`: Limpar preferência quando a execução do script chegar em um estado de terminal. Os valores com suporte são:

  - **Sempre**: Exclua os recursos criados automaticamente quando a execução do script chegar a um estado terminal. Se uma conta de armazenamento existente for usada, o serviço de script excluirá o compartilhamento de arquivos criado na conta de armazenamento. Como o `deploymentScripts` recurso ainda pode estar presente depois que os recursos são limpos, o serviço de script persiste os resultados da execução do script, por exemplo, stdout, saídas e valor de retorno antes que os recursos sejam excluídos.
  - **OnSuccess**: Exclua os recursos criados automaticamente somente quando a execução do script for bem-sucedida. Se uma conta de armazenamento existente for usada, o serviço de script removerá o compartilhamento de arquivos somente quando a execução do script for bem-sucedida. Você ainda pode acessar os recursos para localizar as informações de depuração.
  - **Onexpiretion**: exclua os recursos criados automaticamente somente quando a `retentionInterval` configuração tiver expirado. Se uma conta de armazenamento existente for usada, o serviço de script removerá o compartilhamento de arquivos, mas manterá a conta de armazenamento.

- `retentionInterval`: Especifique o intervalo de tempo que um recurso de script será retido e após o qual ele será expirado e excluído.

> [!NOTE]
> Não é recomendável usar a conta de armazenamento e a instância de contêiner que são geradas pelo serviço de script para outras finalidades. Os dois recursos podem ser removidos, dependendo do ciclo de vida do script.

A instância de contêiner e a conta de armazenamento são excluídas de acordo com o `cleanupPreference` . No entanto, se o script falhar e `cleanupPreference` não estiver definido como **Always**, o processo de implantação manterá automaticamente o contêiner em execução por uma hora. Você pode usar essa hora para solucionar o problema do script. Se você quiser manter o contêiner em execução após implantações bem-sucedidas, adicione uma etapa de suspensão ao seu script. Por exemplo, adicione [Start-Sleep](/powershell/module/microsoft.powershell.utility/start-sleep) ao final do seu script. Se você não adicionar a etapa de suspensão, o contêiner será definido como um estado de terminal e não poderá ser acessado mesmo que ainda não tenha sido excluído.

## <a name="run-script-more-than-once"></a>Executar script mais de uma vez

A execução do script de implantação é uma operação idempotente. Se nenhuma das `deploymentScripts` Propriedades de recurso (incluindo o script embutido) for alterada, o script não será executado quando você reimplantar o modelo. O serviço de script de implantação compara os nomes de recursos no modelo com os recursos existentes no mesmo grupo de recursos. Há duas opções se você quiser executar o mesmo script de implantação várias vezes:

- Altere o nome do `deploymentScripts` recurso. Por exemplo, use a função de modelo [utcNow](./template-functions-date.md#utcnow) como o nome do recurso ou como parte do nome do recurso. Alterar o nome do recurso cria um novo `deploymentScripts` recurso. É bom manter um histórico da execução do script.

    > [!NOTE]
    > A `utcNow` função só pode ser usada no valor padrão para um parâmetro.

- Especifique um valor diferente na propriedade do modelo `forceUpdateTag`. Por exemplo, use `utcNow` como o valor.

> [!NOTE]
> Grave os scripts de implantação idempotentes. Isso garante que, se eles forem executados novamente por acidente, não causarão alterações no sistema. Por exemplo, se o script de implantação for usado para criar um recurso do Azure, verifique se o recurso não existe antes de criá-lo, para que o script seja bem-sucedido e você não tenha que criar o recurso novamente.

## <a name="configure-development-environment"></a>Configurar o ambiente de desenvolvimento

Você pode usar uma imagem de contêiner pré-configurada como seu ambiente de desenvolvimento de script de implantação. Para obter mais informações, consulte [Configurar o ambiente de desenvolvimento para scripts de implantação em modelos](./deployment-script-template-configure-dev.md).

Depois que o script for testado com êxito, você poderá usá-lo como um script de implantação em seus modelos.

## <a name="deployment-script-error-codes"></a>Códigos de erro de script de implantação

| Código do erro | Descrição |
|------------|-------------|
| DeploymentScriptInvalidOperation | A definição de recurso de script de implantação no modelo contém nomes de propriedade inválidos. |
| DeploymentScriptResourceConflict | Não é possível excluir um recurso de script de implantação que está no estado não terminal e a execução não excedeu 1 hora. Ou não é possível executar novamente o mesmo script de implantação com o mesmo identificador de recurso (mesma assinatura, nome do grupo de recursos e nome do recurso), mas conteúdo de corpo de script diferente ao mesmo tempo. |
| DeploymentScriptOperationFailed | A operação de script de implantação falhou internamente. Contate o suporte da Microsoft. |
| DeploymentScriptStorageAccountAccessKeyNotSpecified | A chave de acesso não foi especificada para a conta de armazenamento existente.|
| DeploymentScriptContainerGroupContainsInvalidContainers | Um grupo de contêineres criado pelo serviço de script de implantação foi modificado externamente e contêineres inválidos foram adicionados. |
| DeploymentScriptContainerGroupInNonterminalState | Dois ou mais recursos de script de implantação usam o mesmo nome de instância de contêiner do Azure no mesmo grupo de recursos e um deles ainda não terminou sua execução. |
| DeploymentScriptStorageAccountInvalidKind | A conta de armazenamento existente do tipo BlobBlobStorage ou BlobStorage não dá suporte a compartilhamentos de arquivos e não pode ser usada. |
| DeploymentScriptStorageAccountInvalidKindAndSku | A conta de armazenamento existente não dá suporte a compartilhamentos de arquivos. Para obter uma lista de tipos de conta de armazenamento com suporte, consulte [usar conta de armazenamento existente](#use-existing-storage-account). |
| DeploymentScriptStorageAccountNotFound | A conta de armazenamento não existe ou foi excluída por um processo ou ferramenta externa. |
| DeploymentScriptStorageAccountWithServiceEndpointEnabled | A conta de armazenamento especificada tem um ponto de extremidade de serviço. Não há suporte para uma conta de armazenamento com um ponto de extremidade de serviço. |
| DeploymentScriptStorageAccountInvalidAccessKey | Chave de acesso inválida especificada para a conta de armazenamento existente. |
| DeploymentScriptStorageAccountInvalidAccessKeyFormat | Formato de chave da conta de armazenamento inválido. Consulte [gerenciar chaves de acesso da conta de armazenamento](../../storage/common/storage-account-keys-manage.md). |
| DeploymentScriptExceededMaxAllowedTime | O tempo de execução do script de implantação excedeu o valor de tempo limite especificado na definição de recurso de script de implantação. |
| DeploymentScriptInvalidOutputs | A saída do script de implantação não é um objeto JSON válido. |
| DeploymentScriptContainerInstancesServiceLoginFailure | A identidade gerenciada atribuída pelo usuário não conseguiu entrar após 10 tentativas com um intervalo de 1 minuto. |
| DeploymentScriptContainerGroupNotFound | Um grupo de contêineres criado pelo serviço de script de implantação foi excluído por uma ferramenta ou processo externo. |
| DeploymentScriptDownloadFailure | Falha ao baixar um script de suporte. Consulte [usar script de suporte](#use-supporting-scripts).|
| DeploymentScriptError | O script do usuário gerou um erro. |
| DeploymentScriptBootstrapScriptExecutionFailed | O script de inicialização gerou um erro. O script de inicialização é o script do sistema que orquestra a execução do script de implantação. |
| DeploymentScriptExecutionFailed | Erro desconhecido durante a execução do script de implantação. |
| DeploymentScriptContainerInstancesServiceUnavailable | Ao criar a instância de contêiner do Azure (ACI), o ACI gerou um erro de serviço indisponível. |
| DeploymentScriptContainerGroupInNonterminalState | Ao criar a instância de contêiner do Azure (ACI), outro script de implantação está usando o mesmo nome ACI no mesmo escopo (mesma assinatura, nome do grupo de recursos e nome do recurso). |
| DeploymentScriptContainerGroupNameInvalid | O nome da instância de contêiner do Azure (ACI) especificado não atende aos requisitos de ACI. Consulte [solucionar problemas comuns em instâncias de contêiner do Azure](../../container-instances/container-instances-troubleshooting.md#issues-during-container-group-deployment).|

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como usar os scripts de implantação. Para percorrer um tutorial de script de implantação:

> [!div class="nextstepaction"]
> [Tutorial: Usar scripts de implantação em modelos do Azure Resource Manager](./template-tutorial-deployment-script.md)

> [!div class="nextstepaction"]
> [Módulo Learn: estender modelos ARM usando scripts de implantação](/learn/modules/extend-resource-manager-template-deployment-scripts/)