---
title: "Alternar entre o modo de exibição e o modo de edição para relatórios nas Coleções de Espaços de Trabalho do Power BI | Microsoft Docs"
description: "Saiba como alternar entre o modo de exibição e o modo de edição para seus relatórios nas Coleções de Espaços de Trabalho do Power BI."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: e66778697f3f4f2f065d2757b3b60ac2699c0334
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="toggle-between-view-and-edit-mode-for-reports-in-power-bi-workspace-collections"></a>Alternar entre o modo de exibição e o modo de edição para relatórios nas Coleções de Espaços de Trabalho do Power BI

Saiba como alternar entre o modo de exibição e o modo de edição para seus relatórios nas Coleções de Espaços de Trabalho do Power BI.

> [!IMPORTANT]
> As Coleções do Espaço de Trabalho do Power BI foram preteridas e estarão disponíveis até junho de 2018 ou conforme a indicação do seu contrato. Recomendamos planejar a migração para o Power BI Embedded a fim de evitar interrupções em seu aplicativo. Para saber mais sobre como migrar seus dados para o Power BI Embedded, confira [Como migrar o conteúdo das Coleções do Espaço de Trabalho do Power BI para o Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="creating-an-access-token"></a>Criação de um token de acesso

Você precisa criar um token de acesso que lhe permita exibir e editar um relatório. Para editar e salvar um relatório, é necessária a permissão de token **Report.ReadWrite**. Para obter mais informações, consulte [Autenticando e autorizando nas Coleções de Espaços de Trabalho do Power BI](app-token-flow.md).

> [!NOTE]
> Isso permite que você edite e salve as alterações a um relatório existente. Se você também deseja a função de suporte a **Salvar Como**, precisa fornecer permissões adicionais. Para obter mais informações, consulte [Escopos](app-token-flow.md#scopes).

```
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Report.ReadWrite";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="embed-configuration"></a>Configuração de inserção

Você precisa fornecer permissões e um viewMode para ver o botão salvar no modo de edição. Para saber mais, confira [Detalhes de configuração do servidor de inserção](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

Por exemplo, no JavaScript:

```
   <div id="reportContainer"></div>

    // Get models. Models, it contains enums that can be used.
    var models = window['powerbi-client'].models;

    // Embed configuration used to describe the what and how to embed.
    // This object is used when calling powerbi.embed.
    // This also includes settings and options such as filters.
    // You can find more information at https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details.
    var config= {
        type: 'report',
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        id:  '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
        permissions: models.Permissions.ReadWrite /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.View,
        settings: {
            filterPaneEnabled: true,
            navContentPaneEnabled: true
        }
    };

    // Get a reference to the embedded report HTML element
    var reportContainer = $('#reportContainer')[0];

    // Embed the report and display it within the div container.
    var report = powerbi.embed(reportContainer, config);
```

Isso indica para inserir o relatório no modo de exibição com base na definição de **viewMode** como **models.ViewMode.View**.

## <a name="view-mode"></a>Modo de exibição

Você pode usar o seguinte JavaScript para mudar para o modo de exibição, caso esteja no modo de edição.

```
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to view mode.
report.switchMode("view");

```

## <a name="edit-mode"></a>Modo de edição

Você pode usar o JavaScript a seguir para mudar para o modo de edição, caso esteja no modo de exibição.

```
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to edit mode.
report.switchMode("edit");

```

## <a name="see-also"></a>Consulte também

[Introdução a exemplos](get-started-sample.md)  
[Inserir um relatório](embed-report.md)  
[Autenticando e autorizando em Coleções de Espaços de Trabalho do Power BI](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Amostra de inserção de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Repositório de Git PowerBI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)  
[Repositório de Git PowerBI-Node](https://github.com/Microsoft/PowerBI-Node)  

Mais perguntas? [Experimentar a comunidade do Power BI](http://community.powerbi.com/)
