---
title: Exibições em soluções de gerenciamento | Microsoft Docs
description: 'As soluções de gerenciamento normalmente incluem uma ou mais exibições para visualizar dados.  Este artigo descreve como exportar uma exibição criada pelo Designer de Exibição e incluí-la em uma solução de gerenciamento. '
services: monitoring
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 570b278c-2d47-4e5a-9828-7f01f31ddf8c
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/16/2018
ms.author: bwren
ms.openlocfilehash: cefb83d5336bb99fd09001b5ea369a0b8fc4b942
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596642"
---
# <a name="views-in-management-solutions-preview"></a>Exibições em soluções de gerenciamento (versão prévia)
> [!NOTE]
> Esta é uma documentação preliminar para criar soluções de gerenciamento que estão atualmente em versão prévia. Os esquemas descritos a seguir estão sujeitos a alterações.    


As [Soluções de gerenciamento](solutions.md) normalmente incluem uma ou mais exibições para visualizar dados.  Este artigo descreve como exportar uma exibição criada pelo [Designer de Exibição](../../azure-monitor/platform/view-designer.md) e incluí-la em uma solução de gerenciamento.  

> [!NOTE]
> Os exemplos neste artigo usam parâmetros e variáveis que são necessários ou comuns a soluções de gerenciamento e descritos em [Projetar e compilar uma solução de gerenciamento no Azure](solutions-creating.md)
>
>

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você já esteja familiarizado com o modo para [criar uma solução de gerenciamento](solutions-creating.md) e com a estrutura de um arquivo de solução.

## <a name="overview"></a>Visão geral
Para incluir uma exibição em uma solução de gerenciamento, crie um **recurso** no [arquivo de solução](solutions-creating.md).  O JSON que descreve a configuração detalhada da exibição geralmente é complexo e não algo que um autor típico de solução seria capaz de criar manualmente.  O método mais comum é criar a exibição usando o [Designer de Exibição](../../azure-monitor/platform/view-designer.md), exportá-la e, em seguida, adicionar sua configuração detalhada na solução.

As etapas básicas para adicionar uma exibição a uma solução são da seguinte maneira.  Cada etapa é descrita mais detalhadamente nas seções a seguir.

1. Exportar a exibição para um arquivo.
2. Criar o recurso de exibição na solução.
3. Adicionar os detalhes da exibição.

## <a name="export-the-view-to-a-file"></a>Exportar a exibição para um arquivo
Siga as instruções em [Log Analytics View Designer](../../azure-monitor/platform/view-designer.md) (Designer de exibição do Log Analytics) para exportar uma exibição para um arquivo.  O arquivo exportado será no formato JSON com os mesmos [elementos do arquivo de solução](solutions-solution-file.md).  

O elemento **resources** do arquivo de exibição terá um recurso com um tipo de **Microsoft.OperationalInsights/workspaces**, que representa o espaço de trabalho do Log Analytics.  Esse elemento terá um subelemento com um tipo de **views**, que representa a exibição e contém a configuração detalhada.  Você copiará os detalhes desse elemento e, em seguida, vai copiá-lo em sua solução.

## <a name="create-the-view-resource-in-the-solution"></a>Criar o recurso de exibição na solução
Adicione o seguinte recurso de exibição ao elemento **resources** do seu arquivo de solução.  Ele usa as variáveis descritas abaixo, que você também deverá adicionar.  Observe que as propriedades **Dashboard** e **OverviewTile** são espaços reservados que serão substituídos pelas propriedades correspondentes do arquivo de exibição exportado.

    {
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
        "type": "Microsoft.OperationalInsights/workspaces/views",
        "location": "[parameters('workspaceregionId')]",
        "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
        "dependson": [
            ],
        "properties": {
            "Id": "[variables('ViewName')]",
            "Name": "[variables('ViewName')]",
            "DisplayName": "[variables('ViewName')]",
            "Description": "",
            "Author": "[variables('ViewAuthor')]",
            "Source": "Local",
            "Dashboard": ,
            "OverviewTile":
        }
    }

Adicione as variáveis a seguir ao elemento de variáveis do arquivo de solução e substitua os valores pelos da solução.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

Observe que você poderá copiar todo o recurso de exibição do arquivo de exibição exportado, mas precisará fazer as seguintes alterações para que ele funcione em sua solução.  

* O **type** para o recurso de exibição precisa ser alterado de **views** para **Microsoft.OperationalInsights/workspaces**.
* A propriedade **name** para o recurso de exibição precisa ser alterada para incluir o nome do workspace.
* A dependência no espaço de trabalho precisa ser removida, uma vez que o recurso workspace não foi definido na solução.
* A propriedade **DisplayName** precisa ser adicionada à exibição.  **Id**, **Name** e **DisplayName** devem ser compatíveis.
* Os nomes dos parâmetros devem ser alterados para coincidirem com o conjunto de parâmetros exigido.
* As variáveis devem ser definidas na solução e usadas nas propriedades adequadas.

### <a name="log-analytics-api-version"></a>Versão da API do Log Analytics
Todos os recursos do Log Analytics definidos em um modelo do Resource Manager têm uma propriedade **apiVersion** que define a versão da API que o recurso deve usar.  Essa versão é diferente para os modos de exibição com filas que usam o [formato herdado e a linguagem de consulta atualizados](../../azure-monitor/log-query/log-query-overview.md).  

 A tabela a seguir especifica as versões de API de Log Analytics para modos de exibição em workspaces herdados e atualizados: 

| Versão do workspace | Versão da API | Consulta |
|:---|:---|:---|
| v1 (herdado)   | 2015-11-01-preview | Formato herdado.<br> Exemplo: Type=Event EventLevelName = Error  |
| v2 (atualizado) | 2015-11-01-preview | Formato herdado.  Convertido para o formato atualizado na instalação.<br> Exemplo: Type=Event EventLevelName = Error<br>Convertido para: Event &#124; where EventLevelName == "Error"  |
| v2 (atualizado) | 2017-03-03-versão prévia | Formato de atualização. <br>Exemplo: Event &#124; where EventLevelName == "Error"  |


## <a name="add-the-view-details"></a>Adicionar os detalhes de exibição
O recurso de exibição no arquivo de exibição exportado terá dois elementos no elemento **properties** chamados **Dashboard** e **OverviewTile**, que terão a configuração detalhada da exibição.  Copie esses dois elementos e seus conteúdos no elemento **properties** do recurso de exibição em seu arquivo de solução.

## <a name="example"></a>Exemplo
Por exemplo, a amostra a seguir apresenta um arquivo de solução simples com uma exibição.  As reticências (...) são mostradas para os conteúdos de **Dashboard** e **OverviewTile** por motivos de espaço.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string"
            },
            "accountName": {
                "type": "string"
            },
            "workspaceRegionId": {
                "type": "string"
            },
            "regionId": {
                "type": "string"
            },
            "pricingTier": {
                "type": "string"
            }
        },
        "variables": {
            "SolutionVersion": "1.1",
            "SolutionPublisher": "Contoso",
            "SolutionName": "Contoso Solution",
            "LogAnalyticsApiVersion": "2015-11-01-preview",
            "ViewAuthor":  "user@contoso.com",
            "ViewDescription":  "This is a sample view.",
            "ViewName":  "Contoso View"
        },
        "resources": [
            {
                "name": "[concat(variables('SolutionName'), '(' ,parameters('workspacename'), ')')]",
                "location": "[parameters('workspaceRegionId')]",
                "tags": { },
                "type": "Microsoft.OperationsManagement/solutions",
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspacename'), '/views/', variables('ViewName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspacename'))]",
                    "referencedResources": [
                    ],
                    "containedResources": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
                    ]
                },
                "plan": {
                    "name": "[concat(variables('SolutionName'), '(' ,parameters('workspaceName'), ')')]",
                    "Version": "[variables('SolutionVersion')]",
                    "product": "ContosoSolution",
                    "publisher": "[variables('SolutionPublisher')]",
                    "promotionCode": ""
                }
            },
            {
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
                "type": "Microsoft.OperationalInsights/workspaces/views",
                "location": "[parameters('workspaceregionId')]",
                "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
                "dependson": [
                ],
                "properties": {
                    "Id": "[variables('ViewName')]",
                    "Name": "[variables('ViewName')]",
                    "DisplayName": "[variables('ViewName')]",
                    "Description": "[variables('ViewDescription')]",
                    "Author": "[variables('ViewAuthor')]",
                    "Source": "Local",
                    "Dashboard": ...,
                    "OverviewTile": ...
                }
            }
          ]
    }




## <a name="next-steps"></a>Próximas etapas
* Conheça os detalhes completos de criação das [soluções de gerenciamento](solutions-creating.md).
* Incluir os [runbooks de automação na solução de gerenciamento](solutions-resources-automation.md).
