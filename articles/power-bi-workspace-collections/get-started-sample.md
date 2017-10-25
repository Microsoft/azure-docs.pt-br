---
title: Comece com uma amostra
description: "Neste artigo, apresentaremos a você o exemplo de introdução de Coleções de Espaços de Trabalho do Power BI."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: d8a9ef78-ad4e-4bc7-9711-89172dc5c548
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/25/2017
ms.author: asaxton
ms.openlocfilehash: 9049f95c9f81c0217c96469a45561b6cd0b33ae9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-power-bi-workspace-collections-sample"></a>Exemplo de introdução às Coleções de Espaços de Trabalho do Power BI

Com as **Coleções de Espaços de Trabalho do Microsoft Power BI**, você pode integrar relatórios do Microsoft Power BI diretamente a seus aplicativos móveis ou Web. Neste artigo, apresentamos a você o exemplo de introdução de **Coleções de Espaços de Trabalho do Power BI**.

> [!IMPORTANT]
> As Coleções do Espaço de Trabalho do Power BI foram preteridas e estarão disponíveis até junho de 2018 ou conforme a indicação do seu contrato. Recomendamos planejar a migração para o Power BI Embedded a fim de evitar interrupções em seu aplicativo. Para saber mais sobre como migrar seus dados para o Power BI Embedded, confira [Como migrar o conteúdo das Coleções do Espaço de Trabalho do Power BI para o Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Antes de continuarmos, você deseja salvar os seguintes recursos: eles ajudam você ao integrar relatórios do Power BI ao aplicativo de exemplo e seus próprios aplicativos também.

* [Exemplo de aplicativo Web do espaço de trabalho](http://go.microsoft.com/fwlink/?LinkId=761493)
* [Referência de API de Coleções de Espaços de Trabalho do Power BI](https://msdn.microsoft.com/library/azure/mt711507.aspx)
* [SDK do .NET do Power BI ](http://go.microsoft.com/fwlink/?LinkId=746472) (disponível via NuGet)
* [Amostra inserida de relatório JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo)

> [!NOTE]
> Antes que você possa configurar e executar o exemplo de introdução de Coleções de Espaços de Trabalho do Power BI, você precisa criar pelo menos uma **Coleção de Espaços de Trabalho** em sua assinatura do Azure. Para aprender a criar um **Coleção de Espaços de Trabalho** no Portal do Azure, consulte [Introdução às Coleções de Espaços de Trabalho do Power BI](get-started.md).

## <a name="configure-the-sample-app"></a>Configurar o aplicativo de exemplo

Vamos orientar você sobre como configurar seu ambiente de desenvolvimento do Visual Studio para acessar os componentes necessários para executar o aplicativo de exemplo.

1. Baixe e descompacte o exemplo [Coleções de Espaços de Trabalho do Power BI – Integrar um relatório a um aplicativo Web](http://go.microsoft.com/fwlink/?LinkId=761493) no GitHub.
2. Abra **PowerBI-embedded.sln** no Visual Studio. Talvez seja necessário executar o comando **Update-Package** no Console do Gerenciador de Pacotes do NuGet para atualizar os pacotes usados nesta solução.
3. Compilar a solução.
4. Execute o aplicativo de console **ProvisionSample** . No aplicativo de console de exemplo, você provisiona um espaço de trabalho e importa um arquivo PBIX.
5. Para provisionar um novo **Espaço de trabalho**, selecione a opção 1, **Gerenciamento de coleção** e selecione a opção 6, **Provisionar um novo espaço de trabalho**
6. Para importar um novo **Relatório**, selecione a opção 2, **Gerenciamento de relatório** e selecione a opção 3, **Importar arquivo PBIX da Área de Trabalho em um espaço de trabalho**.

7. Insira o nome da sua **Coleção de Espaços de Trabalho** e a sua **Chave de Acesso**. Você pode obtê-los no **Portal do Azure**. Para saber mais sobre como obter sua **Tecla de Acesso**, consulte [Exibir Chaves de Acesso de API do Power BI](get-started.md#view-power-bi-api-access-keys) na Introdução ao Microsoft Power BI Embedded.

    ![Chaves de acesso no Portal do Azure](media/get-started-sample/azure-portal.png)
8. Copie e salve a **ID do Espaço de Trabalho** recém-criada a usar posteriormente neste artigo. Depois que a **ID do Espaço de Trabalho** for criada, você poderá encontrá-la no **Portal do Azure**.

    ![ID do espaço de trabalho no Portal do Azure](media/get-started-sample/workspace-id.png)
9. Para importar um arquivo PBIX para o **Espaço de Trabalho**, selecione a opção **6. Importe o arquivo da Área de Trabalho PBIX em um espaço de trabalho existente**. Se você não tiver um arquivo PBIX prático, poderá baixar o [PBIX de Exemplo de Análise de Vendas](http://go.microsoft.com/fwlink/?LinkID=780547).
10. Se solicitado, insira um nome amigável para o **Conjunto de Dados**.

Você verá uma resposta semelhante a essa:

```
Checking import state... Publishing
Checking import state... Succeeded
```

> [!NOTE]
> Se o arquivo PBIX contiver quaisquer conexões de consulta direta, execute a opção 7 para atualizar as cadeias de conexão.

Neste ponto, um relatório PBIX do Power BI é importado para o seu **Espaço de Trabalho**. Agora, vejamos como executar aplicativo Web de exemplo de introdução de **Coleções de Espaços de Trabalho do Power BI**.

## <a name="run-the-sample-web-app"></a>Executar o aplicativo Web de exemplo

A amostra do aplicativo Web é um aplicativo de exemplo que renderiza relatórios importados para o seu **Espaço de Trabalho**. Aqui está como configurar o aplicativo Web de exemplo.

1. Na solução **PowerBI-embedded** do Visual Studio, clique com o botão direito do mouse no aplicativo Web **EmbedSample** e escolha **Definir como projeto de Inicialização**.
2. Em **web.config**, no aplicativo Web **EmbedSample** edite o nome de **appSettings**: **AccessKey**, **WorkspaceCollection** e a **WorkspaceId**.

    ```
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. Execute o aplicativo Web **EmbedSample**.

Depois que você executar o aplicativo Web **EmbedSample**, o painel de navegação esquerdo deve conter um menu **Relatórios**. Para exibir o relatório que você importou, expanda **Relatórios** e clique em um relatório. Se você tivesse importado o [PBIX de Exemplo de Análise de Vendas](http://go.microsoft.com/fwlink/?LinkID=780547), o aplicativo Web de exemplo teria esta aparência:

![Exemplo de navegação esquerda no aplicativo de exemplo](media/get-started-sample/sample-left-nav.png)

Depois de clicar em um relatório, o aplicativo Web **EmbedSample** deve ter essa aparência:

![Relatório de exemplo sendo exibido no aplicativo](media/get-started-sample/sample-web-app.png)

## <a name="explore-the-sample-code"></a>Explorar o código de exemplo

A amostra das **Coleções de Espaços de Trabalho do Microsoft Power BI** é um aplicativo Web de exemplo que mostra como integrar relatórios do **Power BI** ao seu aplicativo. Ele usa um MVC (Model-View-Controller) para demonstrar as práticas recomendadas. Esta seção destaca as partes do código de exemplo que você pode explorar dentro da solução de aplicativo Web **PowerBI-embedded**. O padrão Model-View-Controller (MVC) separa a modelagem de domínio, a apresentação e as ações com base na entrada do usuário em três classes separadas: Modelo, Exibição e Controle. Para saber mais sobre o MVC, consulte [Aprenda sobre o ASP.NET](http://www.asp.net/mvc).

O código de exemplo de **Coleções de Espaços de Trabalho do Microsoft Power BI** é separado conforme demonstrado a seguir. Cada seção inclui o nome do arquivo na solução PowerBI-embedded.sln para que você possa localizar facilmente o código no exemplo.

> [!NOTE]
> Esta seção é um resumo do código de exemplo que mostra como o código foi escrito. Para ver o exemplo completo, carregue a solução PowerBI-embedded.sln no Visual Studio.

### <a name="model"></a>Modelo

O exemplo conta com um **ReportsViewModel** e um **ReportViewModel**.

**ReportsViewModel.cs**: representa relatórios do Power BI.

    public class ReportsViewModel
    {
        public List<Report> Reports { get; set; }
    }

**ReportViewModel.cs**: representa um relatório do Power BI.

    public classReportViewModel
    {
        public IReport Report { get; set; }

        public string AccessToken { get; set; }
    }

### <a name="connection-string"></a>Cadeia de conexão

A cadeia de conexão deve estar no seguinte formato:

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

Usar atributos comuns de servidor e de banco de dados falhará. Por exemplo: Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### <a name="view"></a>Visualizar

A **Exibição** gerencia a exibição de **Relatórios** do Power BI e de um **Relatório** do Power BI.

**Reports.cshtml**: faça a iteração de **Model.Reports** para criar um **ActionLink**. O **ActionLink** é composto da seguinte maneira:

| Parte | Descrição |
| --- | --- |
| Title |Nome do Relatório. |
| QueryString |Um link para a ID de Relatório. |

    <div id="reports-nav" class="panel-collapse collapse">
        <div class="panel-body">
            <ul class="nav navbar-nav">
                @foreach (var report in Model.Reports)
                {
                    var reportClass = Request.QueryString["reportId"] == report.Id ? "active" : "";
                    <li class="@reportClass">
                        @Html.ActionLink(report.Name, "Report", new { reportId = report.Id })
                    </li>
                }
            </ul>
        </div>
    </div>

Report.cshtml: defina o **Model.AccessToken** e a expressão Lambda para **PowerBIReportFor**.

    @model ReportViewModel

    ...

    <div class="side-body padding-top">
        @Html.PowerBIAccessToken(Model.AccessToken)
        @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
    </div>

### <a name="controller"></a>Controller

**DashboardController.cs**: cria um PowerBIClient que passa um **token de aplicativo**. Um JWT (Token Web JSON) é gerado da **Chave de Assinatura** para obter as **Credenciais**. As **Credenciais** são usadas para criar uma instância de **PowerBIClient**. Quando tiver uma instância de **PowerBIClient**, você poderá chamar GetReports() e GetReportsAsync().

CreatePowerBIClient()

    private IPowerBIClient CreatePowerBIClient()
    {
        var credentials = new TokenCredentials(accessKey, "AppKey");
        var client = new PowerBIClient(credentials)
        {
            BaseUri = new Uri(apiUrl)
        };

        return client;
    }

ActionResult Reports()

    public ActionResult Reports()
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = client.Reports.GetReports(this.workspaceCollection, this.workspaceId);

            var viewModel = new ReportsViewModel
            {
                Reports = reportsResponse.Value.ToList()
            };

            return PartialView(viewModel);
        }
    }


Tarefa<ActionResult> Report(string reportId)

    public async Task<ActionResult> Report(string reportId)
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = await client.Reports.GetReportsAsync(this.workspaceCollection, this.workspaceId);
            var report = reportsResponse.Value.FirstOrDefault(r => r.Id == reportId);
            var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

            var viewModel = new ReportViewModel
            {
                Report = report,
                AccessToken = embedToken.Generate(this.accessKey)
            };

            return View(viewModel);
        }
    }

### <a name="integrate-a-report-into-your-app"></a>Integrar um relatório em seu aplicativo

Quando tiver um **Relatório**, você deverá usar um **IFrame** para inserir o **Relatório** do Power BI. Veja um trecho de código do powerbi.js na amostra de **Coleções de Espaços de Trabalho do Microsoft Power BI**.

```
init: function() {
    var embedUrl = this.getEmbedUrl();
    var iframeHtml = '<igrame style="width:100%;height:100%;" src="' + embedUrl + 
        '" scrolling="no" allowfullscreen="true"></iframe>';
    this.element.innerHTML = iframeHtml;
    this.iframe = this.element.childNodes[0];
    this.iframe.addEventListener('load', this.load.bind(this), false);
}
```

## <a name="filter-reports-embedded-in-your-application"></a>Filtrar relatórios inseridos no seu aplicativo

Você pode filtrar um relatório inserido usando uma sintaxe de URL. Para fazer isso, adicione um parâmetro de cadeia de caracteres de consulta **$filter** com um operador **eq** à sua URL de origem do iFrame com o filtro especificado. Aqui está a sintaxe de consulta de filtro:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [!NOTE]
> {tableName/fieldName} não pode incluir espaços ou caracteres especiais. O {fieldValue} aceita um único valor categórico.  

## <a name="see-also"></a>Consulte também

[Cenários comuns da Coleção de Espaços de Trabalho do Microsoft Power BI](scenarios.md)  
[Autenticando e autorizando em Coleções de Espaços de Trabalho do Power BI](app-token-flow.md)  
[Inserir um relatório](embed-report.md)  
[Criar um novo relatório de um conjunto de dados](create-report-from-dataset.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Amostra de inserção de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Mais perguntas? [Experimentar a comunidade do Power BI](http://community.powerbi.com/)
