---
title: Como projetar sua implantação do Application Insights – Um vs. muitos recursos?
description: Direcione a telemetria para diferentes recursos para stamps de desenvolvimento, teste e produção.
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 9a60981e692a45dd3630073300b206289cfd2a30
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102424658"
---
# <a name="how-many-application-insights-resources-should-i-deploy"></a>Quantos recursos do Application Insights devo implantar?

Ao desenvolver a próxima versão de um aplicativo Web, não é bom misturar as telemetrias do da nova versão e da versão já lançada do [Application Insights](../../azure-monitor/app/app-insights-overview.md). Para evitar confusão, envie a telemetria de diferentes estágios de desenvolvimento a fim de separar os recursos do Application Insights, com chaves de instrumentação separadas (ikeys). Para facilitar a alteração da chave de instrumentação, quando uma versão muda de um estágio para outro, pode ser útil definir a ikey no código em vez de no arquivo de configuração.

(Se o sistema for um Serviço de Nuvem do Azure, haverá [outro método de configuração de ikeys separados](../../azure-monitor/app/cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Sobre recursos e chaves de instrumentação

Ao configurar o monitoramento do Application Insights para seu aplicativo Web, você cria um *recurso* do Application Insights no Microsoft Azure. Abra esse recurso no portal do Azure para ver e analisar a telemetria coletada de seu aplicativo. O recurso é identificado por uma *chave de instrumentação* (ikey). Ao instalar o pacote do Application Insights para monitorar seu aplicativo, você o configura com a chave de instrumentação, assim ele sabe para onde enviar a telemetria.

Cada recurso do Application Insights vem com métricas que estão disponíveis prontas para uso. Se componentes completamente separados reportarem ao mesmo recurso do Application Insights, essas métricas talvez não façam sentido para o painel/alerta.

### <a name="when-to-use-a-single-application-insights-resource"></a>Quando usar um único recurso do Application Insights

-   Para componentes de aplicativos implantados juntos. Normalmente desenvolvidos por uma única equipe, gerenciados pelo mesmo conjunto de usuários de DevOps/ITOps.
-   Se fizer sentido agregar KPIs (indicadores chave de desempenho), tais como durações de resposta, taxas de falha no painel, etc., em todos eles por padrão (você pode segmentar por nome de função na experiência do Metrics Explorer).
-   Se não houver necessidade de gerenciar o controle de acesso baseado em função do Azure (RBAC do Azure) de forma diferente entre os componentes do aplicativo.
-   Se critérios de alerta de métricas diferentes entre os componentes não forem necessários.
-   Se não for necessário gerenciar exportações contínuas de maneira diferente entre os componentes.
-   Se não for necessário gerenciar cobrança/cotas de maneira diferente entre os componentes.
-   Tenha o mesmo acesso aos dados de todos os componentes se não tiver problema em ter uma chave de API. 10 chaves de API são suficientes para as necessidades em todas elas.
-   Se não tiver problema em ter as mesmas configurações de detecção inteligente e de integração de itens de trabalho em todas as funções.

> [!NOTE]
> Se você quiser consolidar vários recursos de Application Insights, poderá apontar seus componentes de aplicativo existentes para um novo recurso de Application Insights consolidado. A telemetria armazenada no recurso antigo não será transferida para o novo recurso, portanto, exclua apenas o recurso antigo quando você tiver telemetria suficiente no novo recurso para continuidade dos negócios.

### <a name="other-things-to-keep-in-mind"></a>Outras coisas a ter em mente

-   Para garantir que os valores significativos sejam definidos no atributo [Cloud_RoleName](./app-map.md?tabs=net#set-or-override-cloud-role-name), talvez seja necessário adicionar um código personalizado. Sem valores significativos definidos para esse atributo, *NENHUMA* das experiências do portal funcionará.
- Para aplicativos do Service Fabric e serviços de nuvem clássicos, o SDK lê automaticamente do ambiente de função do Azure e os define. Provavelmente você precisará definir isso explicitamente em todos os outros tipos de aplicativos.
-   A experiência do Live Metrics não dá suporte à divisão por nome de função.

## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a> Chave de instrumentação dinâmica

Para facilitar a alteração do iKey à medida que o código se move entre os estágios de produção, referencie a chave dinamicamente no código em vez de usar um valor codificado/estático.

Defina a chave em um método de inicialização como global.aspx.cs em um serviço ASP.NET:

```csharp
protected void Application_Start()
{
  Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = 
      // - for example -
      WebConfigurationManager.AppSettings["ikey"];
  ...
```

Nesse exemplo, as ikeys para os diferentes recursos são colocadas em diferentes versões do arquivo de configuração da Web. Trocar o arquivo de configuração da Web, que pode ser realizado como parte do script versão, alternará o recurso de destino.

### <a name="web-pages"></a>Páginas da Web
A iKey também é usada nas páginas da Web do aplicativo, no [script que você obteve do painel de início rápido](../../azure-monitor/app/javascript.md). Em vez de codificá-la literalmente no script, gere-a a partir do estado do servidor. Por exemplo, em um aplicativo ASP.NET:

```javascript
<script type="text/javascript">
// Standard Application Insights web page script:
var appInsights = window.appInsights || function(config){ ...
// Modify this part:
}({instrumentationKey:  
  // Generate from server property:
  "@Microsoft.ApplicationInsights.Extensibility.
     TelemetryConfiguration.Active.InstrumentationKey"
  }
 )
//...
```

## <a name="create-additional-application-insights-resources"></a>Criar recursos adicionais do Application Insights

Para criar um recurso do Application Insights, siga o [guia de criação de recursos](./create-new-resource.md).

### <a name="getting-the-instrumentation-key"></a>Obter a chave de instrumentação
A chave de instrumentação identifica o recurso que você criou.

Você precisará das chaves de instrumentação de todos os recursos aos quais seu aplicativo enviará dados.

## <a name="filter-on-build-number"></a>Filtrar por número de compilação
Quando publicar uma nova versão do seu aplicativo, você desejará ser capaz de separar a telemetria das compilações diferentes.

Você pode definir a propriedade de versão do aplicativo para que possa filtrar resultados da [pesquisa](../../azure-monitor/app/diagnostic-search.md) e do [Metrics Explorer](../../azure-monitor/essentials/metrics-charts.md).

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
Para controlar a versão do aplicativo, certifique-se de `buildinfo.config` é gerado pelo processo de Microsoft Build Engine. No seu arquivo `.csproj`, adicione:  

```XML
<PropertyGroup>
  <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>
  <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
</PropertyGroup>
```

Quando ele tem as informações de compilação, o módulo da web Application Insights adiciona automaticamente **Versão do aplicativo** como uma propriedade para cada item de telemetria. Isso permite que você filtre por versão ao executar [pesquisas de diagnóstico](../../azure-monitor/app/diagnostic-search.md) ou ao [explorar métricas](../../azure-monitor/essentials/metrics-charts.md).

No entanto, observe que o número de versão de compilação é gerado apenas pelo Microsoft Build Engine, não pela compilação de desenvolvedor do Visual Studio.

### <a name="release-annotations"></a>Anotações da versão
Se usar o Azure DevOps, você poderá [obter um marcador de anotação](../../azure-monitor/app/annotations.md) adicionado a seus gráficos sempre que lançar uma nova versão. 

## <a name="next-steps"></a>Próximas etapas

* [Recursos compartilhados para várias funções](../../azure-monitor/app/app-map.md)
* [Criar um Inicializador de Telemetria para distinguir variantes A | B](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
