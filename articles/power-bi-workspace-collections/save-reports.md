---
title: Salvar relatórios em Coleções de Workspaces do Power BI | Microsoft Docs
description: Saiba como salvar relatórios nas Coleções de Workspaces do Power BI. Isso requer as permissões apropriadas para trabalhar com êxito.
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 1e23bd22f2b89ef42f4b27f5e4ca2590be7fe5a7
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67668891"
---
# <a name="save-reports-in-power-bi-workspace-collections"></a>Salvar relatórios nas Coleções de Workspaces do Power BI

Saiba como salvar relatórios nas Coleções de Workspaces do Power BI. Salvar relatórios requer as permissões apropriadas para trabalhar com êxito.

> [!IMPORTANT]
> As Coleções de Workspaces do Power BI foram preteridas e só estarão disponíveis até junho de 2018 ou conforme a indicação do seu contrato. Recomendamos planejar a migração para o Power BI Embedded a fim de evitar interrupções em seu aplicativo. Para saber mais sobre como migrar seus dados para o Power BI Embedded, confira [Como migrar o conteúdo das Coleções de Workspaces do Power BI para o Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Nas Coleções de Workspaces do Power BI, você pode editar os relatórios existentes e salvá-los. Você também pode criar um novo relatório e salvar como um novo relatório para criar um.

Para salvar um relatório, você precisa primeiro criar um token para o relatório específico com os escopos certos:

* Para habilitar salvar, o escopo Report.ReadWrite é necessário
* Para habilitar salvar como, os escopos Report.Read e Workspace.Report.Copy são necessários
* Para habilitar as ações salvar e salvar como, os escopos Report.ReadWrite e Workspace.Report.Copy são necessários

Para habilitar os botões de salvar/salvar como certos no menu Arquivo, respectivamente, você precisa fornecer a permissão certa na configuração ao inserir o relatório:

* models.Permissions.ReadWrite
* models.Permissions.Copy
* models.Permissions.All

> [!NOTE]
> O token de acesso também precisa dos escopos apropriados. Para obter mais informações, consulte [Escopos](app-token-flow.md#scopes).

## <a name="embed-report-in-edit-mode"></a>Inserir o relatório no modo de edição

Digamos que você deseja inserir um relatório em modo de edição dentro de seu aplicativo, portanto, basta passar as propriedades certas na configuração de inserção e chamar powerbi.embed(). Forneça permissões e um viewMode para ver os botões salvar e salvar como em modo de edição. Para saber mais, confira [Detalhes de configuração do servidor de inserção](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

Por exemplo, no JavaScript:

```html
   <div id="reportContainer"></div>

    <script>
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
        permissions: models.Permissions.All /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.Edit,
        settings: {
            filterPaneEnabled: true,
            navContentPaneEnabled: true
        }
    };

    // Get a reference to the embedded report HTML element
    var reportContainer = $('#reportContainer')[0];

    // Embed the report and display it within the div container.
    var report = powerbi.embed(reportContainer, config);
    </script>
```

Agora, um relatório é inserido em seu aplicativo no modo de edição.

## <a name="save-report"></a>Salvar relatório

Depois de inserir o relatório no modo de edição com o token e permissões certos, você pode salvar o relatório do menu Arquivo ou do JavaScript:

```javascript
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);

 // Save report
    report.save();
```

## <a name="save-as"></a>Salvar como

```javascript
// Get a reference to the embedded report.
    report = powerbi.get(reportContainer);
    
    var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);
```

> [!IMPORTANT]
> Um novo relatório é criado somente depois do uso de *salvar como*. Depois de salvar, a tela ainda mostra o antigo relatório no modo de edição e não o novo relatório. Insira o novo relatório que foi criado. Inserir o novo relatório requer um novo token de acesso, uma vez que eles são criados por relatório.

Em seguida, você precisará carregar o novo relatório após um *salvar como*. O carregamento do novo relatório é semelhante à inserção de qualquer relatório.

```html
<div id="reportContainer"></div>
<script>
var embedConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MJ',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: '5dac7a4a-4452-46b3-99f6-a25915e0fe54',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
</script>
```

## <a name="see-also"></a>Consulte também

[Introdução a exemplos](get-started-sample.md)  
[Inserir um relatório](embed-report.md)  
[Criar um novo relatório de um conjunto de dados](create-report-from-dataset.md)  
[Autenticando e autorizando em Coleções de Workspaces do Power BI](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Amostra de inserção de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Mais perguntas? [Experimentar a comunidade do Power BI](https://community.powerbi.com/)

