---
title: Módulos bicep
description: Descreve como definir e consumir um módulo e como usar escopos de módulo.
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 7a680e8aa0fa4d5ef9cac7f9e7ba07a3aa4ee1e2
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105611728"
---
# <a name="use-bicep-modules-preview"></a>Usar módulos bicep (versão prévia)

O bicep permite dividir uma solução complexa em módulos. Um módulo bicep é um conjunto de um ou mais recursos a serem implantados juntos. Os módulos abstraem detalhes complexos da declaração de recursos brutos, o que pode aumentar a legibilidade. Você pode reutilizar esses módulos e compartilhá-los com outras pessoas. Combinado com as [especificações de modelo](./template-specs.md), ele cria uma maneira de modularidade e reutilização de código. Para obter um tutorial, consulte [tutorial: Adicionar módulos bicep](./bicep-tutorial-add-modules.md).

## <a name="define-modules"></a>Definir módulos

Todo arquivo bicep pode ser consumido como um módulo. Um módulo expõe apenas parâmetros e saídas como contrato para outros arquivos bicep. Os parâmetros e as saídas são opcionais.

O arquivo bicep a seguir pode ser implantado diretamente para criar uma conta de armazenamento ou ser usado como um módulo.  A próxima seção mostra como consumir os módulos:

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_RAGRS'
  'Standard_ZRS'
  'Premium_LRS'
  'Premium_ZRS'
  'Standard_GZRS'
  'Standard_RAGZRS'
])
param storageSKU string = 'Standard_LRS'
param location string

var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'

resource stg 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

A saída é usada para passar valores para os arquivos bicep pai.

## <a name="consume-modules"></a>Consumir módulos

Use a palavra-chave _Module_ para consumir um módulo. O seguinte arquivo bicep implanta o recurso definido no arquivo de módulo que está sendo referenciado:

```bicep
@minLength(3)
@maxLength(11)
param namePrefix string
param location string = resourceGroup().location

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

- **módulo**: palavra-chave.
- **nome simbólico** (stgModule): identificador para o módulo.
- **arquivo de módulo**: o caminho para o módulo neste exemplo é especificado usando um caminho relativo (./storageAccount.bicep). Todos os caminhos no Bicep devem ser especificados usando o separador de diretório de barra (/) para garantir a compilação consistente multiplataforma. Não há suporte para o caractere de barra invertida () do Windows \\ .
- A propriedade **_Name_** (storageDeploy) é necessária ao consumir um módulo. Quando bicep gera o IL do modelo, esse campo é usado como o nome do recurso de implantação aninhado, que é gerado para o módulo:

    ```json
    ...
    ...
    "resources": [
      {
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2020-10-01",
        "name": "storageDeploy",
        "properties": {
          ...
        }
      }
    ]
    ...
    ```

Para obter um valor de saída de um módulo, recupere o valor da propriedade com sintaxe como: `stgModule.outputs.storageEndpoint` onde `stgModule` é o identificador do módulo.

## <a name="configure-module-scopes"></a>Configurar escopos de módulo

Ao declarar um módulo, você pode fornecer uma propriedade de _escopo_ para definir o escopo no qual implantar o módulo:

```bicep
module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: resourceGroup('someOtherRg') // pass in a scope to a different resourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}
```

A propriedade de _escopo_ pode ser omitida quando o escopo de destino do módulo e o escopo de destino do pai são os mesmos. Quando a Propriedade Scope não é fornecida, o módulo é implantado no escopo de destino do pai.

O arquivo bicep a seguir mostra como criar um grupo de recursos e implantar um módulo no grupo de recursos:

```bicep
// set the target scope for this file
targetScope = 'subscription'

@minLength(3)
@maxLength(11)
param namePrefix string

param location string = deployment().location

var resourceGroupName = '${namePrefix}rg'
resource myResourceGroup 'Microsoft.Resources/resourceGroups@2020-01-01' = {
  name: resourceGroupName
  location: location
  scope: subscription()
}

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: myResourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

## <a name="next-steps"></a>Próximas etapas

- Para percorrer um tutorial, consulte [tutorial: Adicionar módulos bicep](./bicep-tutorial-add-modules.md).
