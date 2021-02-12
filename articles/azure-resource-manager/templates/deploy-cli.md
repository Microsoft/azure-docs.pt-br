---
title: Implantar recursos com CLI do Azure e modelo
description: Use Azure Resource Manager e CLI do Azure para implantar recursos no Azure. Os recursos são definidos em um modelo do Resource Manager.
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: f01409db36ca29deb2f6938ce3118cdcb20cd1a1
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881292"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Implantar recursos com modelos do Resource Manager e a CLI do Azure

Este artigo explica como usar CLI do Azure com modelos de Azure Resource Manager (modelos ARM) para implantar seus recursos no Azure. Se você não estiver familiarizado com os conceitos de implantação e gerenciamento de suas soluções do Azure, consulte [visão geral da implantação de modelo](overview.md).

Os comandos de implantação foram alterados no CLI do Azure versão 2.2.0. Os exemplos neste artigo exigem CLI do Azure versão 2.2.0 ou posterior.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Se você não tiver CLI do Azure instalado, poderá usar Azure Cloud Shell. Para obter mais informações, consulte [implantar modelos de ARM de Azure cloud Shell](deploy-cloud-shell.md).

## <a name="deployment-scope"></a>Escopo da implantação

Você pode direcionar sua implantação para um grupo de recursos, uma assinatura, um grupo de gerenciamento ou um locatário. Dependendo do escopo da implantação, você usará comandos diferentes.

* Para implantar em um **grupo de recursos**, use [AZ Deployment Group Create](/cli/azure/deployment/group#az-deployment-group-create):

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>
  ```

* Para implantar em uma **assinatura**, use [AZ Deployment sub Create](/cli/azure/deployment/sub#az-deployment-sub-create):

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-template>
  ```

  Para saber mais sobre as implantações de nível de assinatura, confira [Criar grupos de recursos e recursos no nível da assinatura](deploy-to-subscription.md).

* Para implantar em um **grupo de gerenciamento**, use [AZ Deployment mg Create](/cli/azure/deployment/mg#az-deployment-mg-create):

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-template>
  ```

  Para saber mais sobre implantações de nível de grupo de gerenciamento, confira [Criar recursos no nível de grupo de gerenciamento](deploy-to-management-group.md).

* Para implantar em um **locatário**, use [AZ Deployment locatário Create](/cli/azure/deployment/tenant#az-deployment-tenant-create):

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-template>
  ```

  Para saber mais sobre implantações de nível de locatário, confira [Criar recursos no nível de locatário](deploy-to-tenant.md).

Para cada escopo, o usuário que está implantando o modelo deve ter as permissões necessárias para criar recursos.

## <a name="deploy-local-template"></a>Implantar o modelo local

Você pode implantar um modelo de seu computador local ou um que esteja armazenado externamente. Esta seção descreve a implantação de um modelo local.

Se você estiver implantando em um grupo de recursos que não existe, crie o grupo de recursos. O nome do grupo de recursos pode incluir somente caracteres alfanuméricos, pontos, sublinhados, hifens e parênteses. Pode ter até 90 caracteres. O nome não pode terminar com um ponto.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

Para implantar um modelo local, use o `--template-file` parâmetro no comando de implantação. O exemplo a seguir também mostra como definir um valor de parâmetro proveniente do modelo.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file azuredeploy.json \
  --parameters storageAccountType=Standard_GRS
```

A implantação pode levar alguns minutos para ser concluída. Quando ela for concluída, você verá uma mensagem que inclui o resultado:

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Implantar modelo remoto

Em vez de armazenar modelos de ARM em seu computador local, você pode preferir armazená-los em um local externo. É possível armazenar modelos em um repositório de controle de código-fonte (como o GitHub). Ou ainda armazená-los em uma conta de armazenamento do Azure para acesso compartilhado na sua organização.

Se você estiver implantando em um grupo de recursos que não existe, crie o grupo de recursos. O nome do grupo de recursos pode incluir somente caracteres alfanuméricos, pontos, sublinhados, hifens e parênteses. Pode ter até 90 caracteres. O nome não pode terminar com um ponto.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

Para implantar um modelo externo, use o parâmetro `template-uri`.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

O exemplo anterior requer um URI acessível publicamente para o modelo, que funciona para a maioria dos cenários porque seu modelo não deve incluir dados confidenciais. Se você precisar especificar dados confidenciais (como uma senha de administrador), passe esse valor como um parâmetro seguro. No entanto, se você quiser gerenciar o acesso ao modelo, considere o uso de [especificações de modelo](#deploy-template-spec).

Para implantar modelos vinculados remotos com o caminho relativo que são armazenados em uma conta de armazenamento, use `query-string` para especificar o token SAS:

```azurepowershell
az deployment group create \
  --name linkedTemplateWithRelativePath \
  --resource-group myResourceGroup \
  --template-uri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" \
  --query-string $sasToken
```

Para obter mais informações, consulte [usar caminho relativo para modelos vinculados](./linked-templates.md#linked-template).

## <a name="deployment-name"></a>Nome da implantação

Ao implantar um modelo do ARM, você pode dar um nome à implantação. Esse nome pode ajudá-lo a recuperar a implantação do histórico de implantação. Se você não fornecer um nome para a implantação, o nome do arquivo de modelo será usado. Por exemplo, se você implantar um modelo chamado `azuredeploy.json` e não especificar um nome de implantação, a implantação será nomeada `azuredeploy` .

Toda vez que você executa uma implantação, uma entrada é adicionada ao histórico de implantação do grupo de recursos com o nome da implantação. Se você executar outra implantação e fornecer o mesmo nome, a entrada anterior será substituída pela implantação atual. Se você quiser manter entradas exclusivas no histórico de implantação, dê a cada implantação um nome exclusivo.

Para criar um nome exclusivo, você pode atribuir um número aleatório.

```azurecli-interactive
deploymentName='ExampleDeployment'$RANDOM
```

Ou adicione um valor de data.

```azurecli-interactive
deploymentName='ExampleDeployment'$(date +"%d-%b-%Y")
```

Se você executar implantações simultâneas no mesmo grupo de recursos com o mesmo nome de implantação, somente a última implantação será concluída. Todas as implantações com o mesmo nome que não foram concluídas são substituídas pela última implantação. Por exemplo, se você executar uma implantação chamada `newStorage` que implanta uma conta de armazenamento denominada `storage1` e, ao mesmo tempo, executar outra implantação chamada `newStorage` que implanta uma conta de armazenamento denominada `storage2` , você implanta apenas uma conta de armazenamento. A conta de armazenamento resultante é denominada `storage2` .

No entanto, se você executar uma implantação chamada `newStorage` que implanta uma conta de armazenamento denominada `storage1` e imediatamente após a conclusão da execução de outra implantação chamada `newStorage` que implanta uma conta de armazenamento denominada `storage2` , você tem duas contas de armazenamento. Um é chamado `storage1` , e o outro é nomeado `storage2` . Mas, você tem apenas uma entrada no histórico de implantação.

Ao especificar um nome exclusivo para cada implantação, você pode executá-los simultaneamente sem conflitos. Se você executar uma implantação chamada `newStorage1` que implanta uma conta de armazenamento denominada `storage1` e, ao mesmo tempo, executar outra implantação chamada `newStorage2` que implanta uma conta de armazenamento denominada `storage2` , você tem duas contas de armazenamento e duas entradas no histórico de implantação.

Para evitar conflitos com implantações simultâneas e para garantir entradas exclusivas no histórico de implantação, dê a cada implantação um nome exclusivo.

## <a name="deploy-template-spec"></a>Implantar especificação de modelo

Em vez de implantar um modelo local ou remoto, você pode criar uma [especificação de modelo](template-specs.md). A especificação do modelo é um recurso em sua assinatura do Azure que contém um modelo do ARM. Ele facilita o compartilhamento seguro do modelo com usuários em sua organização. Use o controle de acesso baseado em função do Azure (RBAC do Azure) para conceder acesso à especificação do modelo. Este recurso está atualmente em visualização.

Os exemplos a seguir mostram como criar e implantar uma especificação de modelo.

Primeiro, crie a especificação do modelo fornecendo o modelo ARM.

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

Em seguida, obtenha a ID da especificação do modelo e implante-a.

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

Para obter mais informações, consulte [Azure Resource Manager de especificações de modelo (versão prévia)](template-specs.md).

## <a name="preview-changes"></a>Visualizar alterações

Antes de implantar seu modelo, você pode visualizar as alterações que o modelo fará no seu ambiente. Use a [operação What-If](template-deploy-what-if.md) para verificar se o modelo faz as alterações que você espera. O What-If também valida o modelo para erros.

## <a name="parameters"></a>Parâmetros

Para passar valores de parâmetros, você pode usar parâmetros inline ou um arquivo de parâmetros.

### <a name="inline-parameters"></a>Parâmetros embutidos

Para passar parâmetros embutidos, forneça os valores em `parameters`. Por exemplo, para passar uma cadeia de caracteres e uma matriz para um modelo em um shell bash, use:

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Se você estiver usando CLI do Azure com prompt de comando do Windows (CMD) ou PowerShell, passe a matriz no formato: `exampleArray="['value1','value2']"` .

Você também pode obter o conteúdo do arquivo e fornecer esse conteúdo como um parâmetro embutido.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Obtendo um valor de parâmetro de um arquivo é útil quando você precisa fornecer valores de configuração. Por exemplo, você pode fornecer [valores de cloud-init para uma máquina virtual Linux](../../virtual-machines/linux/using-cloud-init.md).

O _arrayContent.jsno_ formato é:

```json
[
    "value1",
    "value2"
]
```

Para passar um objeto, por exemplo, para definir marcas, use JSON. Por exemplo, seu modelo pode incluir um parâmetro como este:

```json
    "resourceTags": {
      "type": "object",
      "defaultValue": {
        "Cost Center": "IT Department"
      }
    }
```

Nesse caso, você pode passar uma cadeia de caracteres JSON para definir o parâmetro, conforme mostrado no seguinte script bash:

```bash
tags='{"Owner":"Contoso","Cost Center":"2345-324"}'
az deployment group create --name addstorage  --resource-group myResourceGroup \
--template-file $templateFile \
--parameters resourceName=abcdef4556 resourceTags="$tags"
```

Use aspas duplas em volta do JSON que você deseja passar para o objeto.

### <a name="parameter-files"></a>Arquivos de parâmetros

Em vez de passar parâmetros como valores embutidos no script, talvez seja mais fácil usar um arquivo JSON que contenha os valores de parâmetro. O arquivo de parâmetro deve ser um arquivo local. Não há suporte para arquivos de parâmetro externos com a CLI do Azure.

Para saber mais sobre o arquivo de parâmetro, confira [Criar arquivo de parâmetro do Resource Manager](parameter-files.md).

Para passar um arquivo de parâmetro local, use `@` para especificar um arquivo local chamado _storage.parameters.jsem_.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Manipular o formato JSON estendido

Para implantar um modelo com cadeias de caracteres de várias linhas ou comentários usando CLI do Azure com a versão 2.3.0 ou mais antiga, você deve usar a `--handle-extended-json-format` opção.  Por exemplo:

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

## <a name="next-steps"></a>Próximas etapas

- Para reverter para uma implantação bem-sucedida quando você receber um erro, confira [Reverter em caso de erro para uma implantação bem-sucedida](rollback-on-error.md).
- Para especificar como lidar com os recursos existentes no grupo de recursos, mas que não estão definidos no modelo, confira [Modos de implantação do Azure Resource Manager](deployment-modes.md).
- Para entender como definir parâmetros em seu modelo, consulte [entender a estrutura e a sintaxe de modelos ARM](template-syntax.md).
- Para dicas sobre como resolver erros de implantação, consulte [Solução de erros comuns de implantação do Azure com o Azure Resource Manager](common-deployment-errors.md).
