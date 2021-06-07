---
Título: Modelo do ARM da conta dos Serviços de Mídia: Descrição dos Serviços de Mídia do Azure: Este artigo mostra como usar um modelo do ARM para criar uma conta dos Serviços de Mídia.
services: media-services documentationcenter: '' author: IngridAtMicrosoft manager: femila editor: ''

ms.service: media-services ms.workload: ms.topic: guia de início rápido ms.date: 23/03/2021 ms.author: inhenkel ms.custom: subject-armqs

---

# <a name="quickstart-media-services-account-arm-template"></a>Início rápido: Modelo do ARM da conta dos Serviços de Mídia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo mostra como usar um modelo do ARM (Azure Resource Manager) para criar uma conta dos Serviços de Mídia.

## <a name="introduction"></a>Introdução

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Os leitores que têm experiência com modelos do ARM podem prosseguir para a [seção sobre implantação](#deploy-the-template).

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-media-services-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se você nunca implantou um modelo do ARM antes, é útil ler mais sobre os [modelos do ARM do Azure](../../azure-resource-manager/templates/index.yml) e acompanhar o [tutorial](../../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-powershell).

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-media-services-create/).

A sintaxe do limite de código JSON é:

:::code language="json" source="~/quickstart-templates/101-media-services-create/azuredeploy.json":::

Três tipos de recursos do Azure são definidos no modelo:

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): cria uma conta de armazenamento
- [Microsoft.Media/mediaservices](/azure/templates/microsoft.media/mediaservices): cria uma conta dos Serviços de Mídia

## <a name="set-the-account"></a>Definir a conta

```azurecli-interactive

az account set --subscription {your subscription name or id}

```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

```azurecli-interactive

az group create --name {the name you want to give your resource group} --location "{pick a location}"

```

## <a name="assign-a-variable-to-your-deployment-file"></a>Atribuir uma variável ao arquivo de implantação

Para maior conveniência, crie uma variável que armazena o caminho para o arquivo de modelo. Essa variável facilita a execução dos comandos de implantação porque você não precisa digitar o caminho toda vez que implantar.

```azurecli-interactive

templateFile="{provide the path to the template file}"

```

## <a name="deploy-the-template"></a>Implantar o modelo

Você precisará inserir o nome da conta dos Serviços de Mídia.

```azurecli-interactive

az deployment group create --name {the name you want to give to your deployment} --resource-group {the name of resource group you created} --template-file $templateFile

```

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Você verá uma resposta em JSON semelhante à mostrada abaixo:

```json
{
  "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Resources/deployments/amsarmquickstartdeploy",
  "location": null,
  "name": "amsarmquickstartdeploy",
  "properties": {
    "correlationId": "{correlationid}",
    "debugSetting": null,
    "dependencies": [
      {
        "dependsOn": [
          {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Storage/storageAccounts/storagey44cfdmliwatk",
            "resourceGroup": "amsarmquickstartrg",
            "resourceName": "storagey44cfdmliwatk",
            "resourceType": "Microsoft.Storage/storageAccounts"
          }
        ],
        "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/amsarmquickstartrg/providers/Microsoft.Media/mediaServices/{accountname}",
        "resourceGroup": "amsarmquickstartrg",
        "resourceName": "{accountname}",
        "resourceType": "Microsoft.Media/mediaServices"
      }
    ],
    "duration": "PT1M10.8615001S",
    "error": null,
    "mode": "Incremental",
    "onErrorDeployment": null,
    "outputResources": [
      {
        "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Media/mediaServices/{accountname}",
        "resourceGroup": "amsarmquickstartrg"
      },
      {
        "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Storage/storageAccounts/storagey44cfdmliwatk",
        "resourceGroup": "amsarmquickstartrg"
      }
    ],
    "outputs": null,
    "parameters": {
      "mediaServiceName": {
        "type": "String",
        "value": "{accountname}"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.Media",
        "registrationPolicy": null,
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "capabilities": null,
            "locations": [
              "eastus"
            ],
            "properties": null,
            "resourceType": "mediaServices"
          }
        ]
      },
      {
        "id": null,
        "namespace": "Microsoft.Storage",
        "registrationPolicy": null,
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "capabilities": null,
            "locations": [
              "eastus"
            ],
            "properties": null,
            "resourceType": "storageAccounts"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "templateHash": "{templatehash}",
    "templateLink": null,
    "timestamp": "2020-11-24T23:25:52.598184+00:00",
    "validatedResources": null
  },
  "resourceGroup": "amsarmquickstartrg",
  "tags": null,
  "type": "Microsoft.Resources/deployments"
}

```

No portal do Azure, confirme se os recursos foram criados.

![recursos do guia de início rápido criados](./media/media-services-arm-template-quickstart/quickstart-arm-template-resources.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se não pretender usar os recursos recém-criados, exclua o grupo de recursos.

```azurecli-interactive

az group delete --name {name of the resource group}

```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar um modelo do ARM seguindo o processo de criação de um modelo com parâmetros, variáveis e outro, experimente

> [!div class="nextstepaction"]
> [Tutorial: Criar e implantar seu primeiro modelo do Resource Manager](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)