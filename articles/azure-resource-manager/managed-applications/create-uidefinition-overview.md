---
title: CreateUiDefinition.jsno arquivo para o painel do portal
description: Descreve como criar definições de interface do usuário para o portal do Azure. Usado ao definir aplicativos gerenciados do Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 03/26/2021
ms.author: tomfitz
ms.openlocfilehash: 586237c6dd909312780163cf316220d2f3fddd8c
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641647"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>CreateUiDefinition.json para a experiência de criação do aplicativo gerenciado do Azure

Este documento apresenta os principais conceitos do **createUiDefinition.jsno** arquivo. O portal do Azure usa esse arquivo para definir a interface do usuário ao criar um aplicativo gerenciado.

O modelo é o seguinte

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
        "config": {
            "isWizard": false,
            "basics": { }
        },
        "basics": [ ],
        "steps": [ ],
        "outputs": { },
        "resourceTypes": [ ]
    }
}
```

Um `CreateUiDefinition` sempre contém três propriedades:

* handler
* version
* parameters

O manipulador deve sempre ser `Microsoft.Azure.CreateUIDef` , e a versão mais recente com suporte é `0.1.2-preview` .

O esquema da propriedade parameters depende da combinação do manipulador e da versão especificados. Para aplicativos gerenciados, as propriedades com suporte são `config` ,, `basics` `steps` e `outputs` . Você `config` só usa quando precisa substituir o comportamento padrão da `basics` etapa. As propriedades basic e steps contêm os [elementos](create-uidefinition-elements.md), como caixas de texto e listas suspensas, que serão exibidos no portal do Azure. A propriedade Outputs é usada para mapear os valores de saída dos elementos especificados para os parâmetros do modelo de Azure Resource Manager.

A inclusão de `$schema` é opcional, mas recomendada. Se especificado, o valor de `version` deve corresponder à versão dentro do URI `$schema`.

Você pode usar um editor de JSON para criar seu createUiDefinition e testá-lo na [área restrita do createUiDefinition](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) para visualizá-lo. Para obter mais informações sobre a área restrita, consulte [testar sua interface do portal para aplicativos gerenciados do Azure](test-createuidefinition.md).

## <a name="config"></a>Config

A propriedade `config` é opcional. Use-o para substituir o comportamento padrão da etapa básico ou para definir a interface como um assistente passo a passo. Se `config` for usado, será a primeira propriedade na **createUiDefinition.js** seção do arquivo `parameters` . O exemplo a seguir mostra as propriedades disponíveis.

```json
"config": {
    "isWizard": false,
    "basics": {
        "description": "Customized description with **markdown**, see [more](https://www.microsoft.com).",
        "subscription": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[not(contains(subscription().displayName, 'Test'))]",
                        "message": "Can't use test subscription."
                    },
                    {
                        "permission": "Microsoft.Compute/virtualmachines/write",
                        "message": "Must have write permission for the virtual machine."
                    },
                    {
                        "permission": "Microsoft.Compute/virtualMachines/extensions/write",
                        "message": "Must have write permission for the extension."
                    }
                ]
            },
            "resourceProviders": [
                "Microsoft.Compute"
            ]
        },
        "resourceGroup": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[not(contains(resourceGroup().name, 'test'))]",
                        "message": "Resource group name can't contain 'test'."
                    }
                ]
            },
            "allowExisting": true
        },
        "location": {
            "label": "Custom label for location",
            "toolTip": "provide a useful tooltip",
            "resourceTypes": [
                "Microsoft.Compute/virtualMachines"
            ],
            "allowedValues": [
                "eastus",
                "westus2"
            ],
            "visible": true
        }
    }
},
```

Para a `isValid` propriedade, escreva uma expressão que seja resolvida como true ou false. Para a `permission` propriedade, especifique uma das [ações do provedor de recursos](../../role-based-access-control/resource-provider-operations.md).

### <a name="wizard"></a>Assistente

A `isWizard` propriedade permite que você exija uma validação bem-sucedida de cada etapa antes de prosseguir para a próxima etapa. Quando a `isWizard` propriedade não é especificada, o padrão é **false** e a validação passo a passo não é necessária.

Quando `isWizard` está habilitado, definido como **true**, a guia **noções básicas** está disponível e todas as outras guias estão desabilitadas. Quando o botão **Avançar** é selecionado, o ícone da guia indica se a validação de uma guia foi aprovada ou falhou. Depois que os campos obrigatórios de uma guia são concluídos e validados, o botão **Avançar** permite a navegação para a próxima guia. Quando todas as guias são aprovadas na validação, você pode ir para a página **revisar e criar** e selecionar o botão **criar** para iniciar a implantação.

:::image type="content" source="./media/create-uidefinition-overview/tab-wizard.png" alt-text="Assistente de guia":::

### <a name="override-basics"></a>Conceitos básicos de substituição

A configuração de noções básicas permite que você personalize a etapa básico.

Para `description` o, forneça uma cadeia de caracteres habilitada para fins de redução que descreve seu recurso. Há suporte para o formato e os links de várias linhas.

Os `subscription` `resourceGroup` elementos e permitem que você especifique mais validações. A sintaxe para especificar validações é idêntica à validação personalizada para a [caixa de texto](microsoft-common-textbox.md). Você também pode especificar `permission` validações na assinatura ou no grupo de recursos.  

O controle de assinatura aceita uma lista de namespaces do provedor de recursos. Por exemplo, você pode especificar **Microsoft. Compute**. Ele mostra uma mensagem de erro quando o usuário seleciona uma assinatura que não dá suporte ao provedor de recursos. O erro ocorre quando o provedor de recursos não está registrado nessa assinatura e o usuário não tem permissão para registrar o provedor de recursos.  

O controle de grupo de recursos tem uma opção para `allowExisting` . Quando `true` , os usuários podem selecionar grupos de recursos que já têm recursos. Esse sinalizador é mais aplicável aos modelos de solução, em que o comportamento padrão exige que os usuários selecionem um grupo de recursos novo ou vazio. Na maioria dos outros cenários, a especificação dessa propriedade não é necessária.  

Para `location` , especifique as propriedades para o controle de localização que você deseja substituir. Todas as propriedades não substituídas são definidas com seus valores padrão. `resourceTypes` aceita uma matriz de cadeias de caracteres que contêm nomes de tipo de recurso totalmente qualificados. As opções de local são restritas a apenas regiões que dão suporte aos tipos de recursos.  `allowedValues`   aceita uma matriz de cadeias de caracteres de região. Somente essas regiões aparecem na lista suspensa.Você pode definir `allowedValues`   e  `resourceTypes` . O resultado é a interseção de ambas as listas. Por fim, a `visible` propriedade pode ser usada para desabilitar condicionalmente ou completamente a lista suspensa local.  

## <a name="basics"></a>Noções básicas

A etapa **básico** é a primeira etapa gerada quando o portal do Azure analisa o arquivo. Por padrão, a etapa básico permite que os usuários escolham a assinatura, o grupo de recursos e o local para implantação.

:::image type="content" source="./media/create-uidefinition-overview/basics.png" alt-text="Noções básicas padrão":::

Você pode adicionar mais elementos nesta seção. Quando possível, adicione elementos que consultam parâmetros de toda a implantação, como o nome de um cluster ou credenciais de administrador.

O exemplo a seguir mostra uma caixa de texto que foi adicionada aos elementos padrão.

```json
"basics": [
    {
        "name": "textBox1",
        "type": "Microsoft.Common.TextBox",
        "label": "Textbox on basics",
        "defaultValue": "my text value",
        "toolTip": "",
        "visible": true
    }
]
```

## <a name="steps"></a>Etapas

A propriedade Steps contém zero ou mais etapas a serem exibidas após noções básicas. Cada etapa contém um ou mais elementos. Considere adicionar etapas por função ou camada do aplicativo que está sendo implantado. Por exemplo, adicione uma etapa para entradas de nó primário e uma etapa para os nós de trabalho em um cluster.

```json
"steps": [
    {
        "name": "demoConfig",
        "label": "Configuration settings",
        "elements": [
          ui-elements-needed-to-create-the-instance
        ]
    }
]
```

## <a name="outputs"></a>Saídas

O portal do Azure usa a propriedade `outputs` para mapear os elementos de `basics` e `steps` para os parâmetros do modelo de implantação do Azure Resource Manager. As chaves do dicionário são os nomes dos parâmetros do modelo, e os valores são propriedades dos objetos da saída dos elementos referenciados.

Para definir o nome de recurso do aplicativo gerenciado, inclua um valor chamado `applicationResourceName` na propriedade outputs. Se você não definir esse valor, o aplicativo atribuirá um GUID para o nome. Você pode incluir uma caixa de texto na interface do usuário que solicita um nome de usuário.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="resource-types"></a>Tipos de recurso

Para filtrar os locais disponíveis somente para os locais que dão suporte aos tipos de recursos a serem implantados, forneça uma matriz dos tipos de recursos. Se você fornecer mais de um tipo de recurso, somente os locais que dão suporte a todos os tipos de recursos serão retornados. Essa propriedade é opcional.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
        "resourceTypes": ["Microsoft.Compute/disks"],
        "basics": [
          ...
```  

## <a name="functions"></a>Funções

O CreateUiDefinition fornece [funções](create-uidefinition-functions.md) para trabalhar com entradas e saídas de elementos e recursos como condicionais. Essas funções são semelhantes na sintaxe e na funcionalidade para Azure Resource Manager funções de modelo.

## <a name="next-steps"></a>Próximas etapas

O próprio arquivo createUiDefinition.json tem um esquema simples. A profundidade real dele é proveniente de todos os elementos e funções com suporte. Esses itens são descritos mais detalhadamente em:

- [Elementos](create-uidefinition-elements.md)
- [Funções](create-uidefinition-functions.md)

Um esquema JSON atual para createUiDefinition está disponível aqui: `https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json`.

Para ver um arquivo de interface do usuário de exemplo, consulte [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json).
