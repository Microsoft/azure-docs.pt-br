---
title: Exportando grupos de recursos do Azure que contêm extensões de VM
description: Exporte modelos do Resource Manager que incluem as extensões da máquina virtual.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 12/05/2016
ms.openlocfilehash: df1ae43b2c6a74448a6782a43fb86f8f4939b13a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559997"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Exportar Grupos de Recursos que contêm extensões de VM

Os Grupos de Recursos do Azure podem ser exportados para um novo modelo do Resource Manager que, depois, pode ser reimplantado. O processo de exportação interpreta os recursos existentes e cria um modelo do Resource Manager que, quando implantado, resulta em um Grupo de Recursos semelhante. Ao usar a opção de exportação do Grupo de Recursos em um Grupo de Recursos que contém extensões da máquina virtual, vários itens devem ser considerados, como a compatibilidade da extensão e configurações protegidas.

Este documento detalha como o processo de exportação do Grupo de Recursos funciona com relação às extensões da máquina virtual, incluindo uma lista de extensões com suporte e detalhes sobre a manipulação de dados protegidos.

## <a name="supported-virtual-machine-extensions"></a>Extensões da máquina virtual com suporte

Há muitas extensões da máquina virtual disponíveis. Nem todas as extensões podem ser exportadas para um modelo do Resource Manager usando o recurso "Script de Automação". Se não houver suporte para uma extensão da máquina virtual, será necessário colocá-la manualmente de volta no modelo exportado.

As extensões a seguir podem ser exportadas com o recurso de script de automação.

> Backup do Acronis, Acronis de backup do Linux, BG info, BMC CTM Agent Linux, BMC CTM Agent Windows, chefe Client, script personalizado, extensão de script personalizado, script personalizado para Linux, Datadog Linux Agent, Datadog Windows Agent, extensão do Docker, extensão de DSC, dynaTrace Linux, dynaTrace Windows, HPE Security Application defender, o antimalware de IaaS, diagnóstico de IaaS, cliente do Azure chefe, diagnóstico do Linux, patch do so para Linux, agente Puppet, site 24x7 , Site 24x7 Linux Server, local 24x7 Windows Server, Trend Micro DSA, Trend Micro DSA Linux, acesso à VM para Linux, acesso à VM para Linux, instantâneo de VM, instantâneo de VM Linux

## <a name="export-the-resource-group"></a>Exportar o Grupo de Recursos

Para exportar um Grupo de Recursos em um modelo reutilizável, execute as seguintes etapas:

1. Entre no Portal do Azure
2. No menu Hub, clique em Grupos de Recursos
3. Selecione o grupo de recursos de destino na lista
4. Na folha Grupo de Recursos, clique em Script de Automação

![Exportação de modelo](./media/export-templates/template-export.png)

O script de automação do Azure Resource Manager produz um modelo do Resource Manager, um arquivo de parâmetros e vários exemplos de scripts de implantação, como o PowerShell e a CLI do Azure. Neste ponto, o modelo exportado pode ser baixado usando o botão de download, adicionado como um novo modelo à biblioteca de modelos ou reimplantado usando o botão implantar.

## <a name="configure-protected-settings"></a>Definir as configurações protegidas

Muitas extensões da máquina virtual do Azure incluem uma definição de configurações protegidas que criptografa dados confidenciais, como credenciais e cadeias de configuração. As configurações protegidas não são exportadas com o script de automação. Se for necessário, as configurações protegidas precisam ser reinseridas no modelo exportado.

### <a name="step-1---remove-template-parameter"></a>Etapa 1: Remover o parâmetro de modelo

Após a exportação do Grupo de Recursos, um único parâmetro de modelo é criado para fornecer um valor às configurações protegidas exportadas. Esse parâmetro pode ser removido. Para remover o parâmetro, examine a lista de parâmetros e exclua o parâmetro semelhante a este exemplo de JSON.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Etapa 2: Obter as propriedades das configurações protegidas

Como cada configuração protegida tem um conjunto de propriedades exigidas, é preciso obter uma lista dessas propriedades. Cada parâmetro da definição de configurações protegidas pode ser encontrado no [Esquema do Azure Resource Manager no GitHub](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Esse esquema inclui apenas os conjuntos de parâmetro para as extensões listadas na seção de visão geral deste documento. 

De dentro do repositório de esquemas, pesquise a extensão desejada; para este exemplo é a `IaaSDiagnostics`. Após a localização do objeto `protectedSettings` das extensões, anote cada parâmetro. No exemplo da extensão `IaasDiagnostic`, os parâmetros exigidos são `storageAccountName`, `storageAccountKey` e `storageAccountEndPoint`.

```json
"protectedSettings": {
    "type": "object",
    "properties": {
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountKey": {
            "type": "string"
        },
        "storageAccountEndPoint": {
            "type": "string"
        }
    },
    "required": [
        "storageAccountName",
        "storageAccountKey",
        "storageAccountEndPoint"
    ]
}
```

### <a name="step-3---re-create-the-protected-configuration"></a>Etapa 3: Criar novamente a configuração protegida

No modelo exportado, procure `protectedSettings` e substitua o objeto de configuração protegida exportado por um novo que inclua os parâmetros de extensão obrigatórios e um valor para cada um.

No exemplo da extensão `IaasDiagnostic`, a nova definição de configuração protegida seria semelhante ao exemplo a seguir:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

O recurso de extensão final é semelhante ao exemplo de JSON a seguir:

```json
{
    "name": "Microsoft.Insights.VMDiagnosticsSettings",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "[variables('apiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "tags": {
        "displayName": "AzureDiagnostics"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Diagnostics",
        "type": "IaaSDiagnostics",
        "typeHandlerVersion": "1.5",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
        },
        "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]",
            "storageAccountEndPoint": "https://core.windows.net"
        }
    }
}
```

Se você usar parâmetros de modelo para fornecer valores de propriedade, será necessário criá-los. Ao criar parâmetros de modelo para valores de configuração protegida, use o tipo de parâmetro `SecureString` para que os valores confidenciais sejam protegidos. Para saber mais sobre como usar parâmetros, confira [Criação de modelos do Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md).

No exemplo da extensão `IaasDiagnostic`, os parâmetros a seguir seriam criados na seção de parâmetros do modelo do Resource Manager.

```json
"storageAccountName": {
    "defaultValue": null,
    "type": "SecureString"
},
"storageAccountKey": {
    "defaultValue": null,
    "type": "SecureString"
}
```

Neste ponto, o modelo pode ser implantado usando qualquer método de implantação de modelo.
