---
title: Azure Monitor pastas de trabalho e modelos de Azure Resource Manager
description: Simplifique relatórios complexos com pastas de trabalho Azure Monitor parametrizadas predefinidas e personalizadas implantadas por meio de modelos de Azure Resource Manager
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 77190b85da08d09cf05a02dcc5787f0c24229948
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100606456"
---
# <a name="programmatically-manage-workbooks"></a>Gerenciar pastas de trabalho programaticamente

Os proprietários de recursos têm a opção de criar e gerenciar suas pastas de trabalho programaticamente por meio de modelos do Resource Manager.

Isso pode ser útil em cenários como:
* Implantando relatórios de análise específicos da organização ou do domínio juntamente com implantações de recursos. Por exemplo, você pode implantar pastas de trabalho de desempenho e falha específicas da organização para seus novos aplicativos ou máquinas virtuais.
* Implantando relatórios padrão ou dashboards usando pastas de trabalho para recursos existentes.

A pasta de trabalho será criada no grupo de recursos/sub desejado e com o conteúdo especificado nos modelos do Resource Manager.

Há dois tipos de recursos de pasta de trabalho que podem ser gerenciados programaticamente:
* [Modelos de pasta de trabalho](#azure-resource-manager-template-for-deploying-a-workbook-template)
* [Instâncias da pasta de trabalho](#azure-resource-manager-template-for-deploying-a-workbook-instance)

## <a name="azure-resource-manager-template-for-deploying-a-workbook-template"></a>Modelo de Azure Resource Manager para implantar um modelo de pasta de trabalho

1. Abra uma pasta de trabalho que você deseja implantar programaticamente.
2. Alterne a pasta de trabalho para o modo de edição clicando no item _Editar_ barra de ferramentas.
3. Abra o _Editor avançado_ usando o _</>_ botão na barra de ferramentas.
4. Verifique se você está na guia _modelo da Galeria_ .

    ![Guia modelo da Galeria](./media/workbooks-automate/gallery-template.png)
1. Copie o JSON no modelo da galeria para a área de transferência.
2. Veja abaixo um modelo de Azure Resource Manager de exemplo que implanta um modelo de pasta de trabalho para Azure Monitor Galeria de pastas de trabalho. Cole o JSON que você copiou no lugar de `<PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>` . Um modelo de referência Azure Resource Manager que cria um modelo de pasta de trabalho pode ser encontrado [aqui](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/ARM-template-for-creating-workbook-template).

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "resourceName": {
                "type": "string",
                "defaultValue": "my-workbook-template",
                "metadata": {
                    "description": "The unique name for this workbook template instance"
                }
            }
        },
        "resources": [
            {
                "name": "[parameters('resourceName')]",
                "type": "microsoft.insights/workbooktemplates",
                "location": "[resourceGroup().location]",
                "apiVersion": "2019-10-17-preview",
                "dependsOn": [],
                "properties": {
                    "galleries": [
                        {
                            "name": "A Workbook Template",
                            "category": "Deployed Templates",
                            "order": 100,
                            "type": "workbook",
                            "resourceType": "Azure Monitor"
                        }
                    ],
                    "templateData": <PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>
                }
            }
        ]
    }
    ```
1. No `galleries` objeto, preencha as `name` chaves e `category` com seus valores. Saiba mais sobre os [parâmetros](#parameters) na próxima seção.
2. Implante esse modelo de Azure Resource Manager usando a [portal do Azure](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), a [interface de linha de comando](../../azure-resource-manager/templates/deploy-cli.md), o [PowerShell](../../azure-resource-manager/templates/deploy-powershell.md), etc.
3. Abra o portal do Azure e navegue até a Galeria de pastas de trabalho escolhida no modelo de Azure Resource Manager. No modelo de exemplo, navegue até a Galeria de pastas de trabalho do Azure Monitor:
    1. Abra o portal do Azure e navegue até Azure Monitor
    2. Abrir `Workbooks` do Sumário
    3. Localize seu modelo na Galeria, em categoria `Deployed Templates` (será um dos itens roxos).

### <a name="parameters"></a>Parâmetros

|Parâmetros                |Explicação                                                                                             |
|:-------------------------|:-------------------------------------------------------------------------------------------------------|
| `name`                   | O nome do recurso de modelo de pasta de trabalho no Azure Resource Manager.                                  |
|`type`                    | Sempre Microsoft. insights/workbooktemplates                                                            |
| `location`               | O local do Azure em que a pasta de trabalho será criada.                                               |
| `apiVersion`             | visualização de 2019-10-17                                                                                     |
| `type`                   | Sempre Microsoft. insights/workbooktemplates                                                            |
| `galleries`              | O conjunto de galerias para mostrar esse modelo de pasta de trabalho no.                                                |
| `gallery.name`           | O nome amigável do modelo de pasta de trabalho na galeria.                                             |
| `gallery.category`       | O grupo na galeria no qual o modelo será colocado.                                                     |
| `gallery.order`          | Um número que decide a ordem para mostrar o modelo dentro de uma categoria na galeria. A ordem inferior implica prioridade mais alta. |
| `gallery.resourceType`   | O tipo de recurso correspondente à galeria. Normalmente, essa é a cadeia de caracteres de tipo de recurso correspondente ao recurso (por exemplo, Microsoft. operationalinsights/Workspaces). |
|`gallery.type`            | Conhecido como tipo de pasta de trabalho, essa é uma chave exclusiva que diferencia a Galeria dentro de um tipo de recurso. Application Insights, por exemplo, têm tipos `workbook` e `tsg` correspondem a galerias de pastas de trabalho diferentes. |

### <a name="galleries"></a>Galerias

| Galeria                                        | Tipo de recurso                                      | Tipo de pasta de trabalho |
| :--------------------------------------------- |:---------------------------------------------------|:--------------|
| Pastas de trabalho no Azure Monitor                     | `Azure Monitor`                                    | `workbook`    |
| Informações de VM no Azure Monitor                   | `Azure Monitor`                                    | `vm-insights` |
| Pastas de trabalho no espaço de trabalho do log Analytics           | `microsoft.operationalinsights/workspaces`         | `workbook`    |
| Pastas de trabalho no Application Insights              | `microsoft.insights/component`                     | `workbook`    |
| Guias de solução de problemas no Application Insights | `microsoft.insights/component`                     | `tsg`         |
| Uso em Application Insights                  | `microsoft.insights/component`                     | `usage`       |
| Pastas de trabalho no serviço kubernetes                | `Microsoft.ContainerService/managedClusters`       | `workbook`    |
| Pastas de trabalho em grupos de recursos                   | `microsoft.resources/subscriptions/resourcegroups` | `workbook`    |
| Pastas de trabalho no Azure Active Directory            | `microsoft.aadiam/tenant`                          | `workbook`    |
| Informações de VM em máquinas virtuais                | `microsoft.compute/virtualmachines`                | `insights`    |
| Informações de VM em conjuntos de dimensionamento de máquinas virtuais      | `microsoft.compute/virtualmachinescalesets`        | `insights`    |

## <a name="azure-resource-manager-template-for-deploying-a-workbook-instance"></a>Modelo de Azure Resource Manager para implantar uma instância de pasta de trabalho

1. Abra uma pasta de trabalho que você deseja implantar programaticamente.
2. Alterne a pasta de trabalho para o modo de edição clicando no item _Editar_ barra de ferramentas.
3. Abra o _Editor avançado_ usando o _</>_ botão na barra de ferramentas.
4. No editor, alterne _tipo de modelo_ para _modelo do Resource Manager_.
5. O modelo do Resource Manager para a criação é mostrado no editor. Copie o conteúdo e use-o no estado em que se encontra ou mescle-o com um modelo maior que também implanta o recurso de destino.

    ![Imagem mostrando como obter o modelo do Resource Manager de dentro da interface do usuário da pasta de trabalho](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>Modelo de Azure Resource Manager de exemplo
Este modelo mostra como implantar uma pasta de trabalho simples que exibe um ' Olá, Mundo! '
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName":  {             
            "type":"string",
            "defaultValue": "My Workbook",
            "metadata": {
                "description": "The friendly name for the workbook that is used in the Gallery or Saved List. Needs to be unique in the scope of the resource group and source" 
            }
        },
        "workbookType":  {             
            "type":"string",
            "defaultValue": "tsg",
            "metadata": {
                "description": "The gallery that the workbook will been shown under. Supported values include workbook, `tsg`, Azure Monitor, etc." 
            }
        },
        "workbookSourceId":  {             
            "type":"string",
            "defaultValue": "<insert-your-resource-id-here>",
            "metadata": {
                "description": "The id of resource instance to which the workbook will be associated" 
            }
        },
        "workbookId": {
            "type":"string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "The unique guid for this workbook instance" 
            }
        }
    },    
    "resources": [
        {
            "name": "[parameters('workbookId')]",
            "type": "Microsoft.Insights/workbooks",
            "location": "[resourceGroup().location]",
            "kind": "shared",
            "apiVersion": "2018-06-17-preview",
            "dependsOn": [],
            "properties": {
                "displayName": "[parameters('workbookDisplayName')]",
                "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":\"{\\\"json\\\":\\\"Hello World!\\\"}\",\"conditionalVisibility\":null}],\"isLocked\":false}",
                "version": "1.0",
                "sourceId": "[parameters('workbookSourceId')]",
                "category": "[parameters('workbookType')]"
            }
        }
    ],
    "outputs": {
        "workbookId": {
            "type": "string",
            "value": "[resourceId( 'Microsoft.Insights/workbooks', parameters('workbookId'))]"
        }
    }
}
```

### <a name="template-parameters"></a>Parâmetros de modelo

| Parâmetro | Explicação |
| :------------- |:-------------|
| `workbookDisplayName` | O nome amigável da pasta de trabalho usada na galeria ou na lista salva. Precisa ser exclusivo no escopo do grupo de recursos e da origem |
| `workbookType` | A galeria na qual a pasta de trabalho será mostrada. Os valores com suporte incluem Workbook, `tsg` , Azure monitor, etc. |
| `workbookSourceId` | A ID da instância de recurso à qual a pasta de trabalho será associada. A nova pasta de trabalho aparecerá relacionada a essa instância de recurso, por exemplo, na tabela de conteúdo do recurso na _pasta de trabalho_. Se você quiser que sua pasta de trabalho apareça na Galeria de pastas de trabalho no Azure Monitor, use a cadeia de caracteres _Azure monitor_ em vez de uma ID de recurso. |
| `workbookId` | O GUID exclusivo para esta instância de pasta de trabalho. Use _[newGuid ()]_ para criar automaticamente um novo GUID. |
| `kind` | Usado para especificar se a pasta de trabalho criada é compartilhada ou privada. Use o valor _compartilhado_ para pastas de trabalho compartilhadas e para _usuários_ particulares. |
| `location` | O local do Azure onde a pasta de trabalho será criada. Use _[resourcegroup (). Location]_ para criá-lo no mesmo local que o grupo de recursos |
| `serializedData` | Contém o conteúdo ou a carga a ser usada na pasta de trabalho. Use o modelo do Resource Manager da interface do usuário de pastas de trabalho para obter o valor |

### <a name="workbook-types"></a>Tipos de pasta de trabalho
Tipos de pasta de trabalho especifique a Galeria da pasta de trabalho na qual a nova instância da pasta de trabalho será exibida. As opções incluem:

| Type | Localização da Galeria |
| :------------- |:-------------|
| `workbook` | O padrão usado na maioria dos relatórios, incluindo a Galeria de pastas de trabalho do Application Insights, Azure Monitor, etc.  |
| `tsg` | A Galeria de guias de solução de problemas no Application Insights |
| `usage` | A _mais_ galeria em _uso_ no Application insights |

### <a name="limitations"></a>Limitações
Por um motivo técnico, esse mecanismo não pode ser usado para criar instâncias da pasta de trabalho na Galeria de _pastas de trabalho_ do Application insights. Estamos trabalhando para resolver essa limitação. Enquanto isso, recomendamos que você use a Galeria de guias de solução de problemas (WorkbookType: `tsg` ) para implantar Application insights pastas de trabalho relacionadas.

## <a name="next-steps"></a>Próximas etapas

Explore como as pastas de trabalho estão sendo usadas para capacitar a nova [Azure monitor de experiência de armazenamento](../insights/storage-insights-overview.md).
