---
title: Separar a telemetria do desenvolvimento, teste e lançamento no Azure Application Insights | Microsoft Docs
description: Direcione a telemetria para diferentes recursos para stamps de desenvolvimento, teste e produção.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 578e30f0-31ed-4f39-baa8-01b4c2f310c9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: mbullwin
ms.openlocfilehash: 2e9c599c12ed10327d352baee02500d2284d98d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60713389"
---
# <a name="separating-telemetry-from-development-test-and-production"></a>Separação da telemetria de desenvolvimento, teste e produção

Ao desenvolver a próxima versão de um aplicativo Web, não é bom misturar as telemetrias do da nova versão e da versão já lançada do [Application Insights](../../azure-monitor/app/app-insights-overview.md). Para evitar confusão, envie a telemetria de diferentes estágios de desenvolvimento a fim de separar os recursos do Application Insights, com chaves de instrumentação separadas (ikeys). Para facilitar a alteração da chave de instrumentação, quando uma versão muda de um estágio para outro, pode ser útil definir a ikey no código em vez de no arquivo de configuração. 

(Se o sistema for um Serviço de Nuvem do Azure, haverá [outro método de configuração de ikeys separados](../../azure-monitor/app/cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Sobre recursos e chaves de instrumentação

Ao configurar o monitoramento do Application Insights para seu aplicativo Web, você cria um *recurso* do Application Insights no Microsoft Azure. Abra esse recurso no portal do Azure para ver e analisar a telemetria coletada de seu aplicativo. O recurso é identificado por uma *chave de instrumentação* (ikey). Ao instalar o pacote do Application Insights para monitorar seu aplicativo, você o configura com a chave de instrumentação, assim ele sabe para onde enviar a telemetria.

Normalmente, você escolhe usar recursos separados ou um único recurso compartilhado em diversos cenários:

* Aplicativos independentes e diferentes – use um recurso separado e a ikey para cada aplicativo.
* Vários componentes ou funções de um aplicativo de negócios – use um [único recurso compartilhado](../../azure-monitor/app/app-map.md) para todos os aplicativos componentes. A telemetria pode ser filtrada ou segmentada pela propriedade cloud_RoleName.
* Desenvolvimento, Teste e Lançamento – usam um recurso e ikey separados para versões do sistema em 'stamp' ou estágio de produção.
* Teste A | B – use um único recurso. Crie um Inicializador de Telemetria para adicionar uma propriedade à telemetria que identifica as variantes.


## <a name="dynamic-ikey"></a> Chave de instrumentação dinâmica

Para facilitar a alteração da ikey à medida que o código percorre os estágios de produção, a defina no código em vez de no arquivo de configuração.

Defina a chave em um método de inicialização como global.aspx.cs em um serviço ASP.NET:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

Nesse exemplo, as ikeys para os diferentes recursos são colocadas em diferentes versões do arquivo de configuração da Web. Trocar o arquivo de configuração da Web, que pode ser realizado como parte do script versão, alternará o recurso de destino.

### <a name="web-pages"></a>Páginas da Web
A iKey também é usada nas páginas da Web do aplicativo, no [script que você obteve da folha de início rápido](../../azure-monitor/app/javascript.md). Em vez de codificá-la literalmente no script, gere-a a partir do estado do servidor. Por exemplo, em um aplicativo ASP.NET:

*JavaScript no Razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="create-additional-application-insights-resources"></a>Criar recursos adicionais do Application Insights
Para separar a telemetria de diferentes componentes do aplicativo ou de diferentes stamps (desenvolvimento/teste/produção) do mesmo componente, será necessário criar um novo recurso do Application Insights.

No [portal.azure.com](https://portal.azure.com), adicione um recurso do Application Insights:

![Clique em Novo, Application Insights](./media/separate-resources/01-new.png)

* **tipo de aplicativo** afeta o que você vê na folha de visão geral e as propriedades disponíveis no [explorador de métricas](../../azure-monitor/app/metrics-explorer.md)do Microsoft Azure. Se você não vir o tipo de aplicativo, escolha um dos tipos da Web para páginas da Web.
* **grupo de recursos** é uma conveniência para o gerenciamento de propriedades, como [controle de acesso](../../azure-monitor/app/resources-roles-access-control.md)do Microsoft Azure. Você pode usar grupos de recursos separados para desenvolvimento, teste e produção.
* **Assinatura** é a sua conta de pagamento no Azure.
* **Local** é onde podemos manter seus dados. Atualmente ele não pode ser alterado. 
* **Adicionar ao painel** coloca um bloco de acesso rápido para o recurso em sua Página Inicial do Azure. 

A criação do recurso leva alguns segundos. Quando estiver pronto, você verá um alerta.

(Você pode escrever um [script do PowerShell](../../azure-monitor/app/powershell-script-create-resource.md) para criar um recurso automaticamente.)

### <a name="getting-the-instrumentation-key"></a>Obter a chave de instrumentação
A chave de instrumentação identifica o recurso que você criou. 

![Clique em Essentials, clique na Chave de Instrumentação, CTRL+C](./media/separate-resources/02-props.png)

Você precisará das chaves de instrumentação de todos os recursos aos quais seu aplicativo enviará dados.

## <a name="filter-on-build-number"></a>Filtrar por número de compilação
Quando publicar uma nova versão do seu aplicativo, você desejará ser capaz de separar a telemetria das compilações diferentes.

Você pode definir a propriedade de versão do aplicativo para que possa filtrar resultados da [pesquisa](../../azure-monitor/app/diagnostic-search.md) e do [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md).

![Filtragem em uma propriedade](./media/separate-resources/050-filter.png)

Há vários métodos diferentes de definir a propriedade de Versão do aplicativo.

* Definir diretamente:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Defina a quebra automática de linha em um [inicializador de telemetria](../../azure-monitor/app/api-custom-events-metrics.md#defaults) para garantir que todas as instâncias de TelemetryClient sejam configuradas de forma consistente.
* [ASP.NET] Definir a versão em `BuildInfo.config`. O módulo da Web selecionará a versão do nó BuildLabel. Inclua esse arquivo no seu projeto e não se esqueça de definir a propriedade Copy Always no Gerenciador de Soluções.

    ```XML

    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] Gerar automaticamente BuildInfo.config no MSBuild. Para fazer isso, adicione algumas linhas ao seu arquivo `.csproj`:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Isso gera um arquivo chamado *nomedoSeuProjeto*.BuildInfo.config. O processo de Publicação renomeia o arquivo como BuildInfo.config.

    O rótulo da compilação contém um espaço reservado (AutoGen_...) quando você cria com o Visual Studio. Mas quando compilado com o MSBuild, ele é preenchido com o número de versão correta.

    Para permitir que o MSBuild gere números de versão, defina a versão como `1.0.*` em AssemblyReference.cs

## <a name="version-and-release-tracking"></a>Versão e controle de versão
Para controlar a versão do aplicativo, certifique-se de `buildinfo.config` é gerado pelo processo de Microsoft Build Engine. No arquivo. csproj, adicione:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

Quando ele tem as informações de compilação, o módulo da web Application Insights adiciona automaticamente **Versão do aplicativo** como uma propriedade para cada item de telemetria. Isso permite que você filtre por versão ao executar [pesquisas de diagnóstico](../../azure-monitor/app/diagnostic-search.md) ou ao [explorar métricas](../../azure-monitor/app/metrics-explorer.md).

No entanto, observe que o número de versão de compilação é gerado apenas pelo Microsoft Build Engine, não pela compilação de desenvolvedor no Visual Studio.

### <a name="release-annotations"></a>Anotações da versão
Se usar o Azure DevOps, você poderá [obter um marcador de anotação](../../azure-monitor/app/annotations.md) adicionado a seus gráficos sempre que lançar uma nova versão. A imagem a seguir mostra como esse marcador é exibido.

![Captura de tela de anotação de versão de exemplo em um gráfico](media/separate-resources/release-annotation.png)
## <a name="next-steps"></a>Próximas etapas

* [Recursos compartilhados para várias funções](../../azure-monitor/app/app-map.md)
* [Criar um Inicializador de Telemetria para distinguir variantes A | B](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
