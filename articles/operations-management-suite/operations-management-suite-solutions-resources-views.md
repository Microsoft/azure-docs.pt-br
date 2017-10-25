---
title: "Exibições nas soluções de gerenciamento do OMS (Operations Management Suite) | Microsoft Docs"
description: "As soluções de gerenciamento no OMS (Operations Management Suite) normalmente incluirão uma ou mais exibições para visualizar os dados.  Este artigo descreve como exportar uma exibição criada pelo Designer de Exibição e incluí-la em uma solução de gerenciamento. "
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 570b278c-2d47-4e5a-9828-7f01f31ddf8c
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 533b5564a805e0b41f2b1a4ad92e12b133220952
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="views-in-operations-management-suite-oms-management-solutions-preview"></a>Exibições nas soluções de gerenciamento do OMS (Operations Management Suite) (Preview)
> [!NOTE]
> Esta é uma documentação preliminar para criar soluções de gerenciamento no OMS, que estão atualmente em visualização. Os esquemas descritos a seguir estão sujeitos a alterações.    
>
>

As [soluções de gerenciamento no OMS (Operations Management Suite)](operations-management-suite-solutions.md) normalmente incluirão uma ou mais exibições para visualizar os dados.  Este artigo descreve como exportar uma exibição criada pelo [Designer de Exibição](../log-analytics/log-analytics-view-designer.md) e incluí-la em uma solução de gerenciamento.  

> [!NOTE]
> Os exemplos neste artigo usam parâmetros e variáveis que são necessários ou comuns para as soluções de gerenciamento e estão descritos em [Creating management solutions in Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md) (Criando soluções de gerenciamento no OMS (Operations Management Suite))
>
>

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você já esteja familiarizado com o modo para [criar uma solução de gerenciamento](operations-management-suite-solutions-creating.md) e com a estrutura de um arquivo de solução.

## <a name="overview"></a>Visão geral
Para incluir uma exibição em uma solução de gerenciamento, crie um **recurso** no [arquivo de solução](operations-management-suite-solutions-creating.md).  O JSON que descreve a configuração detalhada da exibição geralmente é complexo e não algo que um autor típico de solução seria capaz de criar manualmente.  O método mais comum é criar a exibição usando o [Designer de Exibição](../log-analytics/log-analytics-view-designer.md), exportá-la e, em seguida, adicionar sua configuração detalhada na solução.

As etapas básicas para adicionar uma exibição a uma solução são da seguinte maneira.  Cada etapa é descrita mais detalhadamente nas seções a seguir.

1. Exportar a exibição para um arquivo.
2. Criar o recurso de exibição na solução.
3. Adicionar os detalhes da exibição.

## <a name="export-the-view-to-a-file"></a>Exportar a exibição para um arquivo
Siga as instruções em [Log Analytics View Designer](../log-analytics/log-analytics-view-designer.md) (Designer de exibição do Log Analytics) para exportar uma exibição para um arquivo.  O arquivo exportado será no formato JSON com os mesmos [elementos do arquivo de solução](operations-management-suite-solutions-solution-file.md).  

O elemento **resources** do arquivo de exibição terá um recurso com um tipo de **Microsoft.OperationalInsights/workspaces**, que representa o espaço de trabalho do OMS.  Esse elemento terá um subelemento com um tipo de **views**, que representa a exibição e contém a configuração detalhada.  Você copiará os detalhes desse elemento e, em seguida, vai copiá-lo em sua solução.

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

    "LogAnalyticsApiVersion": "2015-11-01-preview",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."


Observe que você poderá copiar todo o recurso de exibição do arquivo de exibição exportado, mas precisará fazer as seguintes alterações para que ele funcione em sua solução.  

* O **type** para o recurso de exibição precisa ser alterado de **views** para **Microsoft.OperationalInsights/workspaces**.
* A propriedade **name** para o recurso de exibição precisa ser alterada para incluir o nome do espaço de trabalho.
* A dependência no espaço de trabalho precisa ser removida, uma vez que o recurso workspace não foi definido na solução.
* A propriedade **DisplayName** precisa ser adicionada à exibição.  **Id**, **Name** e **DisplayName** devem ser compatíveis.
* Os nomes dos parâmetros devem ser alterados para coincidirem com o conjunto de parâmetros exigido.
* As variáveis devem ser definidas na solução e usadas nas propriedades adequadas.

## <a name="add-the-view-details"></a>Adicionar os detalhes de exibição
O recurso de exibição no arquivo de exibição exportado terá dois elementos no elemento **properties** chamados **Dashboard** e **OverviewTile**, que terão a configuração detalhada da exibição.  Copie esses dois elementos e seus conteúdos no elemento **properties** do recurso de exibição em seu arquivo de solução.

## <a name="example"></a>Exemplo
Por exemplo, a amostra a seguir apresenta um arquivo de solução simples com uma exibição.  As reticências (...) são mostradas para os conteúdos de **Dashboard** e **OverviewTile** por motivos de espaço.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
* Conheça os detalhes completos de criação das [soluções de gerenciamento](operations-management-suite-solutions-creating.md).
* Incluir os [runbooks de automação na solução de gerenciamento](operations-management-suite-solutions-resources-automation.md).
