---
title: Implantar recursos com a CLI do Azure e o modelo | Microsoft Docs
description: Use Azure Resource Manager e CLI do Azure para implantar recursos no Azure. Os recursos são definidos em um modelo do Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/12/2019
ms.author: tomfitz
ms.openlocfilehash: 93b1b16776bac6cb24996d6fa08a547318802f32
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67853841"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Implantar recursos com modelos do Resource Manager e a CLI do Azure

Este artigo explica como usar a CLI do Azure com modelos do Resource Manager para implantar seus recursos no Azure. Se você não estiver familiarizado com os conceitos de implantação e gerenciamento de soluções do Azure, consulte [Visão geral do Azure Resource Manager](resource-group-overview.md).  

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Se você não tiver a CLI do Azure instalada, você pode usar o [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Escopo da implantação

Você pode direcionar sua implantação para uma assinatura do Azure ou um grupo de recursos em uma assinatura. Na maioria dos casos, você direcionará a implantação para um grupo de recursos. Use implantações de assinatura para aplicar políticas e atribuições de função na assinatura. Você também usa implantações de assinatura para criar um grupo de recursos e implantar recursos nele. Dependendo do escopo da implantação, você usará comandos diferentes.

Para implantar em um **grupo de recursos**, use [AZ Group Deployment Create](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create):

```azurecli
az group deployment create --resource-group <resource-group-name> --template-file <path-to-template>
```

Para implantar em uma **assinatura**, use [AZ Deployment Create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create):

```azurecli
az deployment create --location <location> --template-file <path-to-template>
```

Atualmente, as implantações de grupo de gerenciamento só têm suporte por meio da API REST. Consulte [implantar recursos com modelos do Resource Manager e a API REST do Gerenciador de recursos](resource-group-template-deploy-rest.md).

Os exemplos neste artigo usam implantações de grupo de recursos. Para obter mais informações sobre implantações de assinatura, consulte [criar grupos de recursos e recursos no nível da assinatura](deploy-to-subscription.md).

## <a name="deploy-local-template"></a>Implantar o modelo local

Ao implantar recursos no Azure, você:

1. Entre na sua conta do Azure
2. Crie um grupo de recursos que atue como o contêiner para os recursos implantados. O nome do grupo de recursos pode incluir somente caracteres alfanuméricos, pontos, sublinhados, hifens e parênteses. Pode ter até 90 caracteres. Ele não pode terminar em um período.
3. Implanta no grupo de recursos o modelo que define os recursos a serem criados

Um modelo pode incluir parâmetros que permitem personalizar a implantação. Por exemplo, você pode fornecer valores que são personalizados para um determinado ambiente (como desenvolvimento, teste e produção). O modelo de exemplo define um parâmetro para o SKU da conta de armazenamento. 

O exemplo a seguir cria um grupo de recursos e implanta um modelo do computador local:

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

A implantação pode levar alguns minutos para ser concluída. Quando ela for concluída, você verá uma mensagem que inclui o resultado:

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Implantar modelo remoto

Em vez de armazenar modelos do Resource Manager no computador local, talvez você prefira armazená-los em um local externo. É possível armazenar modelos em um repositório de controle de código-fonte (como o GitHub). Ou ainda armazená-los em uma conta de armazenamento do Azure para acesso compartilhado na sua organização.

Para implantar um modelo externo, use o parâmetro **template-uri**. Use o URI do exemplo para implantar o modelo de exemplo do GitHub.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

O exemplo anterior requer um URI acessível publicamente para o modelo, que funciona para a maioria dos cenários porque seu modelo não deve incluir dados confidenciais. Se você precisar especificar dados confidenciais (como uma senha de administrador), passe esse valor como um parâmetro seguro. No entanto, se você não quiser que seu modelo seja acessível publicamente, poderá protegê-lo armazenando-o em um contêiner de armazenamento particular. Para obter informações sobre como implantar um modelo que exige um token SAS (assinatura de acesso compartilhado), confira [Implantar modelo particular com o token SAS](resource-manager-cli-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

No Cloud Shell, use os seguintes comandos:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="redeploy-when-deployment-fails"></a>Reimplantar quando ocorrer falha na implantação

Esse recurso também é conhecido como *reversão em caso de erro*. Quando uma implantação falha, é possível reimplantar automaticamente uma implantação anterior bem-sucedida com base em seu histórico de implantações. Para especificar a reimplantação, use o parâmetro `--rollback-on-error` no comando de implantação. Essa funcionalidade será útil se você tiver um estado válido conhecido para sua implantação de infraestrutura e quiser reverter para esse estado. Há várias limitações e restrições:

- A reimplantação é executada exatamente como foi executada anteriormente com os mesmos parâmetros. Você não pode alterar os parâmetros.
- A implantação anterior é executada usando o [modo completo](./deployment-modes.md#complete-mode). Todos os recursos não incluídos na implantação anterior são excluídos e as configurações de recurso são definidas para o estado anterior. Certifique-se de compreender totalmente os [modos de implantação](./deployment-modes.md).
- A reimplantação afeta apenas os recursos, as alterações de dados não são afetadas.
- Esse recurso só tem suporte em implantações de grupo de recursos, não em implantações de nível de assinatura. Para obter mais informações sobre a implantação em nível de assinatura, consulte [criar grupos de recursos e recursos no nível da assinatura](./deploy-to-subscription.md).

Para usar essa opção, as implantações devem ter nomes exclusivos para que possam ser identificadas no histórico. Se você não tiver nomes exclusivos, a implantação atual com falha pode substituir a implantação bem-sucedida anteriormente no histórico. Você só pode usar essa opção com implantações de nível raiz. Implantações de um modelo aninhado não estão disponíveis para reimplantação.

Para reimplementar a última implantação bem-sucedida, adicione o parâmetro `--rollback-on-error` como um sinalizador.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Para reimplantar uma implantação específica, use o `--rollback-on-error` parâmetro e forneça o nome da implantação.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

A implantação especificada deve ter êxito.

## <a name="parameters"></a>parâmetros

Para passar valores de parâmetros, você pode usar parâmetros inline ou um arquivo de parâmetros. Os exemplos anteriores neste artigo mostram parâmetros inline.

### <a name="inline-parameters"></a>Parâmetros embutidos

Para passar parâmetros embutidos, forneça os valores em `parameters`. Por exemplo passar uma cadeia de caracteres e a matriz a um modelo é um shell Bash, use:

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Se você estiver usando CLI do Azure com prompt de comando do Windows (CMD) ou PowerShell, passe a matriz no formato `exampleArray="['value1','value2']"`:.

Você também pode obter o conteúdo do arquivo e fornecer esse conteúdo como um parâmetro embutido.

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Obtendo um valor de parâmetro de um arquivo é útil quando você precisa fornecer valores de configuração. Por exemplo, você pode fornecer [valores de cloud-init para uma máquina virtual Linux](../virtual-machines/linux/using-cloud-init.md).

O formato arrayContent.json é:

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>Arquivos de parâmetros

Em vez de passar parâmetros como valores embutidos no script, talvez seja mais fácil usar um arquivo JSON que contenha os valores de parâmetro. O arquivo de parâmetro deve ser um arquivo local. Não há suporte para arquivos de parâmetro externos com a CLI do Azure.

O arquivo de parâmetro deve estar no seguinte formato:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

Observe que a seção de parâmetros inclui um nome de parâmetro que corresponde ao parâmetro definido no seu modelo (storageAccountType). O arquivo de parâmetros contém um valor para o parâmetro. Esse valor é passado automaticamente ao modelo durante a implantação. Você pode criar mais de um arquivo de parâmetro e, em seguida, passar o arquivo de parâmetro apropriado para o cenário. 

Copie o exemplo anterior e salve-o como um arquivo chamado `storage.parameters.json`.

Para passar um arquivo de parâmetros local, use `@` para especificar um arquivo local chamado storage.parameters.json.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

### <a name="parameter-precedence"></a>Precedência de parâmetro

Você pode usar parâmetros embutidos e um arquivo de parâmetro local na mesma operação de implantação. Por exemplo, você pode especificar alguns valores no arquivo de parâmetro local e adicionar outros valores embutidos durante a implantação. Se você fornecer valores para um parâmetro no arquivo de parâmetros local e embutido, o valor embutido terá precedência.

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters @demotemplate.parameters.json \
  --parameters exampleArray=@arrtest.json
```

## <a name="test-a-template-deployment"></a>Testar uma implantação de modelo

Para testar os valores de parâmetro e o modelo sem realmente implantar os recursos, use [az group deployment validate](/cli/azure/group/deployment#az-group-deployment-validate). 

```azurecli-interactive
az group deployment validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Se nenhum erro for detectado, o comando retornará informações sobre a implantação de teste. Especificamente, observe que o valor de **erro** é null.

```azurecli
{
  "error": null,
  "properties": {
      ...
```

Se um erro for detectado, o comando retornará uma mensagem de erro. Por exemplo, passando um valor incorreto para a SKU da conta de armazenamento, retorna o seguinte erro:

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter 
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed 
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

Se seu modelo tem um erro de sintaxe, o comando retorna um erro indicando que ele não foi possível analisar o modelo. A mensagem indica o número da linha e a posição do erro de análise.

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

## <a name="next-steps"></a>Próximas etapas

- Os exemplos deste artigo implantam recursos em um grupo de recursos na sua assinatura padrão. Para usar outra assinatura, confira [Manage multiple Azure subscriptions](/cli/azure/manage-azure-subscriptions-azure-cli) (Gerenciar várias assinaturas do Azure).
- Para especificar como lidar com os recursos existentes no grupo de recursos, mas que não estão definidos no modelo, confira [Modos de implantação do Azure Resource Manager](deployment-modes.md).
- Para entender como definir parâmetros em seu modelo, confira [Noções básicas de estrutura e sintaxe dos modelos do Azure Resource Manager](resource-group-authoring-templates.md).
- Para dicas sobre como resolver erros de implantação, consulte [Solução de erros comuns de implantação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
- Para saber mais sobre como implantar um modelo que exija um token SAS, veja [Implantar o modelo particular com o token SAS](resource-manager-cli-sas-token.md).
- Para distribuir com segurança seu serviço para mais de uma região, consulte [Azure Deployment Manager](deployment-manager-overview.md).
