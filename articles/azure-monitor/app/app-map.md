---
title: Mapa de aplicativos no Azure Application Insights | Microsoft Docs
description: Monitorar topologias complexas de aplicativos com o mapa do aplicativo
ms.topic: conceptual
ms.date: 03/15/2019
ms.custom: devx-track-csharp
ms.reviewer: sdash
ms.openlocfilehash: db8c84334bfce52d34b9fadf73bb2b070fa93a70
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007101"
---
# <a name="application-map-triage-distributed-applications"></a>Mapa do aplicativo: aplicativos distribuídos por triagem

O mapa do aplicativo ajuda você a identificar gargalos de desempenho ou pontos de acesso com falha em todos os componentes dos seus aplicativos distribuídos. Cada nó do mapa representa um componente de aplicativo ou suas dependências e esses nós também têm KPIs de integridade e alertas de status. Você pode clicar em qualquer componente para obter diagnóstico mais detalhado, como eventos do Application Insights. Se seu aplicativo usar os serviços do Azure, você também poderá clicar no diagnóstico do Azure, como nas recomendações do Assistente do Banco de Dados SQL.

## <a name="what-is-a-component"></a>O que é um componente?

Os componentes são partes independentes dos aplicativos de microsserviços/distribuídos. As equipes de operações e desenvolvedores têm acesso ou visibilidade nível de código para telemetria gerada por esses componentes de aplicativos. 

* Os componentes são diferentes das dependências externas "observadas", como SQL, EventHub etc., a qual sua organização/equipe pode não ter acesso (código ou telemetria).
* Os componentes são executados em qualquer número de instâncias de contêiner/função/servidor.
* Os componentes podem ser chaves de instrumentação do Application Insights separadas (mesmo se as assinaturas forem diferentes) ou diferentes funções relatando para uma única chave de instrumentação do Application Insights. A experiência do mapa de visualização mostra os componentes independentemente de como eles estão configurados.

## <a name="composite-application-map"></a>Mapa do aplicativo composto

Você pode ver a topologia do aplicativo completa em vários níveis de componentes de aplicativos relacionados. Os componentes podem ser recursos diferentes do Application Insights ou funções diferentes em um único recurso. O mapa do aplicativo localiza os componentes seguindo qualquer chamada de dependência HTTP feita entre os servidores com o SDK do Application Insights instalado. 

Essa experiência começa com a descoberta progressiva dos componentes. Quando você carrega o mapa do aplicativo pela primeira vez, um conjunto de consultas é disparado para descobrir os componentes relacionados a esse componente. Um botão no canto superior esquerdo será atualizado com o número de componentes em seu aplicativo, conforme eles são descobertos. 

Ao clicar em "Atualizar componentes do mapa", o mapa será atualizado com todos os componentes descobertos até aquele momento. Dependendo da complexidade do seu aplicativo, isso pode levar um minuto para carregar.

Se todos os componentes são funções em um único recurso do Application Insights, essa etapa de descoberta não é necessária. A carga inicial para esse tipo de aplicativo terá todos os respectivos componentes.

![Captura de tela mostra um exemplo de um mapa de aplicativo.](media/app-map/app-map-001.png)

Um dos principais objetivos com essa experiência é ser capaz de visualizar topologias complexas com centenas de componentes.

Clique em qualquer componente para ver as respectivas informações e acesse a experiência de triagem de desempenho e falha desse componente.

![Submenu](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Investigar falhas

Selecione **investigar falhas** para iniciar o painel de falhas.

![Captura de tela do botão Investigar falhas](media/app-map/investigate-failures.png)

![Captura de tela da experiência de falhas](media/app-map/failures.png)

### <a name="investigate-performance"></a>Investigar o desempenho

Para solucionar problemas de desempenho, selecione **investigar desempenho**.

![Captura de tela do botão Investigar desempenho](media/app-map/investigate-performance.png)

![Captura de tela da experiência de desempenho](media/app-map/performance.png)

### <a name="go-to-details"></a>Acessar detalhes

Selecione **ir para detalhes** para explorar a experiência de transação de ponta a ponta, que pode oferecer exibições até o nível da pilha de chamadas.

![Captura de tela do botão Acessar detalhes](media/app-map/go-to-details.png)

![Captura de tela de detalhes da transação de ponta a ponta](media/app-map/end-to-end-transaction.png)

### <a name="view-logs-analytics"></a>Exibir logs (análise)

Para consultar e investigar ainda mais os dados de aplicativos, clique em **Exibir nos logs (análise)**.

![Captura de tela do botão Exibir na análise](media/app-map/view-logs.png)

![Captura de tela da experiência de análise. Gráfico de linhas Resumindo a duração média da resposta de uma solicitação nas últimas 12 horas.](media/app-map/log-analytics.png)

### <a name="alerts"></a>Alertas

Para exibir alertas ativos e as regras subjacentes que fazem com que os alertas sejam disparados, selecione **alertas**.

![Captura de tela do botão de alertas](media/app-map/alerts.png)

![Captura de tela da experiência de análise](media/app-map/alerts-view.png)

## <a name="set-or-override-cloud-role-name"></a>Definir ou substituir o nome da função de nuvem

O mapa do aplicativo usa a propriedade **nome da função de nuvem** para identificar os componentes no mapa. Para definir ou substituir manualmente o nome da função de nuvem e alterar o que é exibido no mapa do aplicativo:

> [!NOTE]
> O SDK ou agente do Application Insights adiciona automaticamente a propriedade nome da função de nuvem à telemetria emitida pelos componentes em um ambiente de serviço Azure App.

# <a name="netnetcore"></a>[.NET/. NetCore](#tab/net)

**Escreva personalizada telemetryinitializer personalizado como abaixo.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "Custom RoleName";
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

**Aplicativos ASP.NET: inicializador de carga para o TelemetryConfiguration ativo**

Em ApplicationInsights.config:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

Um método alternativo para aplicativos Web ASP.NET é instanciar o inicializador no código, por exemplo, em Global.aspx.cs:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

> [!NOTE]
> Adicionar inicializador usando `ApplicationInsights.config` ou usando `TelemetryConfiguration.Active` não é válido para aplicativos ASP.NET Core. 

**Aplicativos ASP.NET Core: inicializador de carga para o TelemetryConfiguration**

Para [aplicativos ASP.NET Core](asp-net-core.md#adding-telemetryinitializers), adicionar um novo `TelemetryInitializer` é feito adicionando-o ao contêiner de injeção de dependência, conforme mostrado abaixo. Isso é feito no método `ConfigureServices` de sua classe `Startup.cs`.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

# <a name="java"></a>[Java](#tab/java)

**Agente Java**

Para o [agente Java 3,0](./java-in-process-agent.md) , o nome da função de nuvem é definido da seguinte maneira:

```json
{
  "role": {
    "name": "my cloud role name"
  }
}
```

Você também pode definir o nome da função de nuvem usando a variável de ambiente ```APPLICATIONINSIGHTS_ROLE_NAME``` .

**Java SDK**

Se você estiver usando o SDK, começando com Application Insights SDK do Java 2.5.0, poderá especificar o nome da função de nuvem adicionando `<RoleName>` ao `ApplicationInsights.xml` arquivo, por exemplo,

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
   <RoleName>** Your role name **</RoleName>
   ...
</ApplicationInsights>
```

Se você usar o Spring Boot com o iniciador do Spring Boot do Application Insights, a única alteração necessária é definir seu nome personalizado para o aplicativo no arquivo application.properties.

`spring.application.name=<name-of-app>`

O iniciador do Spring boot atribuirá automaticamente o nome da função de nuvem ao valor inserido para a propriedade spring.application.name.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Método alternativo para o Node. js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
appInsights.queue.push(() => {
appInsights.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

# <a name="python"></a>[Python](#tab/python)

Para Python, os [processadores de telemetria do Python OpenCensus](api-filtering-sampling.md#opencensus-python-telemetry-processors) podem ser usados.

```python
def callback_function(envelope):
   envelope.tags['ai.cloud.role'] = 'new_role_name'
   
# AzureLogHandler
handler.add_telemetry_processor(callback_function)

# AzureExporter
exporter.add_telemetry_processor(callback_function)
```
---

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>Noções básicas sobre o nome da função de nuvem no contexto do mapa do aplicativo

No que diz respeito a como pensar sobre o **nome da função de nuvem**, pode ser útil examinar um mapa de aplicativo que tem vários nomes de função de nuvem presentes:

![Captura de tela do mapa do aplicativo](media/app-map/cloud-rolename.png)

No mapa do aplicativo acima, cada um dos nomes em caixas verdes são valores de nome de função de nuvem para diferentes aspectos desse aplicativo distribuído específico. Portanto, para esse aplicativo, suas funções consistem em: `Authentication` , `acmefrontend` , `Inventory Management` , a `Payment Processing Worker Role` . 

No caso desse aplicativo, cada um desses nomes de função de nuvem também representa um recurso de Application Insights exclusivo diferente com suas próprias chaves de instrumentação. Como o proprietário deste aplicativo tem acesso a cada um desses quatro recursos de Application Insights diferentes, o mapa de aplicativos é capaz de reunir um mapa das relações subjacentes.

Para as [definições oficiais](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93):

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

Como alternativa, a **instância de função de nuvem** pode ser útil para cenários em que o nome da função de **nuvem** informa que o problema está em algum lugar no front-end da Web, mas você pode estar executando seu front-end da Web em vários servidores com balanceamento de carga, de modo que seja capaz de analisar uma camada mais profunda por meio de consultas Kusto e saber se o problema está afetando todos os servidores/instâncias de front-end da Web, ou apenas um pode ser extremamente importante.

Um cenário em que você talvez queira substituir o valor para a instância de função de nuvem poderia ser se seu aplicativo estiver sendo executado em um ambiente em contêineres, onde apenas saber se o servidor individual pode não ser informações suficientes para localizar um determinado problema.

Para obter mais informações sobre como substituir a propriedade de nome da função de nuvem por inicializadores de telemetria, consulte [Adicionar propriedades: ITelemetryInitializer](api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Solução de problemas

Se você estiver tendo dificuldades para obter o Mapa do aplicativo para trabalhar conforme esperado, tente essas etapas:

### <a name="general"></a>Geral

1. Certifique-se que você está usando um SDK com suporte oficial. SDKs de comunidade/sem suporte podem não dar suporte à correlação.

    Consulte este [artigo](./platforms.md) para obter uma lista dos SDKs com suporte.

2. Atualize todos os componentes para a versão mais recente do SDK.

3. Se você estiver usando Azure Functions com C#, atualize para o [Functions v2](../../azure-functions/functions-versions.md).

4. Confirme se o [nome da função de nuvem](#set-or-override-cloud-role-name) está configurado corretamente.

5. Se estiver faltando uma dependência, verifique se ele está na lista de [dependências coletadas automaticamente](./auto-collect-dependencies.md). Se não, você ainda poderá acompanhá-lo manualmente com uma chamada [acompanhar dependência](./api-custom-events-metrics.md#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>Muitos nós no mapa

O mapa de aplicativos constrói um nó de aplicativo para cada nome de função de nuvem exclusivo presente na telemetria de solicitação e um nó de dependência para cada combinação exclusiva de tipo, destino e nome da função de nuvem em sua telemetria de dependência. Se houver mais de 10.000 nós em sua telemetria, o mapa do aplicativo não poderá buscar todos os nós e links, de modo que o mapa estará incompleto. Se isso acontecer, uma mensagem de aviso será exibida ao exibir o mapa.

Além disso, o mapa de aplicativos dá suporte apenas a até 1000 nós desagrupados separados ao mesmo tempo. O mapa do aplicativo reduz a complexidade visual agrupando dependências que têm o mesmo tipo e chamadores, mas se a telemetria tiver muitos nomes de função de nuvem exclusivos ou muitos tipos de dependência, esse agrupamento será insuficiente e o mapa não poderá ser renderizado.

Para corrigir isso, você precisará alterar sua instrumentação para definir corretamente o nome da função de nuvem, o tipo de dependência e os campos de destino de dependência.

* O destino de dependência deve representar o nome lógico de uma dependência. Em muitos casos, é equivalente ao nome do servidor ou do recurso da dependência. Por exemplo, no caso de dependências HTTP, ele é definido como o nome do host. Ele não deve conter IDs ou parâmetros exclusivos que se alteram de uma solicitação para outra.

* O tipo de dependência deve representar o tipo lógico de uma dependência. Por exemplo, HTTP, SQL ou BLOB do Azure são tipos de dependência típicos. Ele não deve conter IDs exclusivas.

* A finalidade do nome da função de nuvem é descrita na [seção acima](#set-or-override-cloud-role-name).

## <a name="portal-feedback"></a>Comentários do Portal

Para fornecer feedback, use a opção de feedback.

![Imagem de MapLink-1](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como a correlação funciona em Application Insights consulte o [artigo correlação de telemetria](correlation.md).
* A [experiência de diagnóstico de transação de ponta a ponta](transaction-diagnostics.md) correlaciona a telemetria do lado do servidor em todos os seus Application insights componentes monitorados em uma única exibição.
* Para cenários de correlação avançada em ASP.NET Core e ASP.NET, consulte o artigo [rastrear operações personalizadas](custom-operations-tracking.md) .
