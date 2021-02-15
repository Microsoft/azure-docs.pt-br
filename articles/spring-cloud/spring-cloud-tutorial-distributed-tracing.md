---
title: Usar o rastreamento distribuído com o Azure Spring Cloud
description: Saiba como usar o rastreamento distribuído do Spring Cloud por meio do Azure Application Insights
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: a78aec8c18f3b89629bbf696de3a097397ac59bc
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94337909"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>Usar o rastreamento distribuído com o Azure Spring Cloud

Com as ferramentas de rastreamento distribuído do Azure Spring Cloud, você pode depurar e monitorar problemas complexos com facilidade. O Azure Spring Cloud integra o [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) ao [Application Insights](../azure-monitor/app/app-insights-overview.md) do Azure. Essa integração fornece uma funcionalidade avançada de rastreamento distribuído no portal do Azure.

::: zone pivot="programming-language-csharp"
Neste artigo, você aprenderá a habilitar um aplicativo Steeltoe do .NET Core para usar o rastreamento distribuído.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir esses procedimentos, você precisa de um aplicativo Steeltoe que já esteja [preparado para implantação no Azure Spring Cloud](spring-cloud-tutorial-prepare-app-deployment.md).

## <a name="dependencies"></a>Dependências

Para Steeltoe 2.4.4, adicione os seguintes pacotes NuGet:

* [Steeltoe. Management. TracingCore](https://www.nuget.org/packages/Steeltoe.Management.TracingCore/)
* [Steeltoe. Management. ExporterCore](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/)

Para Steeltoe 3.0.0, adicione o seguinte pacote NuGet:

* [Steeltoe. Management. TracingCore](https://www.nuget.org/packages/Steeltoe.Management.TracingCore/)

## <a name="update-startupcs"></a>Atualizar Startup.cs

1. Para Steeltoe 2.4.4, chame `AddDistributedTracing` e `AddZipkinExporter` no `ConfigureServices` método.

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddDistributedTracing(Configuration);
       services.AddZipkinExporter(Configuration);
   }
   ```

   Para Steeltoe 3.0.0, chame `AddDistributedTracing` no `ConfigureServices` método.

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddDistributedTracing(Configuration, builder => builder.UseZipkinWithTraceOptions(services));
   }
   ```

1. Para Steeltoe 2.4.4, chame `UseTracingExporter` no `Configure` método.

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
        app.UseTracingExporter();
   }
   ```

   Para Steeltoe 3.0.0, nenhuma alteração é necessária no `Configure` método.

## <a name="update-configuration"></a>Atualizar configuração

Adicione as seguintes configurações à fonte de configuração que será usada quando o aplicativo for executado no Azure Spring Cloud:

1. Defina `management.tracing.alwaysSample` como true.

2. Se você quiser ver as extensões de rastreamento enviadas entre o servidor Eureka, o servidor de configuração e os aplicativos de usuário: defina `management.tracing.egressIgnorePattern` como "/API/v2/spans |/v2/apps/. */Permissions |/Eureka/.* | /oauth/.*".

Por exemplo, *appsettings.jsno* incluiria as seguintes propriedades:
 
```json
"management": {
    "tracing": {
      "alwaysSample": true,
      "egressIgnorePattern": "/api/v2/spans|/v2/apps/.*/permissions|/eureka/.*|/oauth/.*"
    }
  }
```

Para obter mais informações sobre o rastreamento distribuído em aplicativos do .NET Core Steeltoe, consulte [rastreamento distribuído](https://steeltoe.io/docs/3/tracing/distributed-tracing) na documentação do Steeltoe.
::: zone-end
::: zone pivot="programming-language-java"
Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Habilitar o rastreamento distribuído no portal do Azure.
> * Adicionar o Spring Cloud Sleuth ao seu aplicativo.
> * Exibir mapas de dependências para seus aplicativos de microsserviço.
> * Pesquisar dados de rastreamento com diferentes filtros.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir estes procedimentos, você precisará de um serviço do Azure Spring Cloud que já esteja provisionado e em execução. Conclua o início rápido [implantar seu primeiro aplicativo do Azure Spring Cloud](spring-cloud-quickstart.md) para provisionar e executar um serviço de nuvem do Azure Spring.

## <a name="add-dependencies"></a>Adicionar dependências

1. Adicione a seguinte linha ao arquivo application.properties:

   ```xml
   spring.zipkin.sender.type = web
   ```

   Após essa alteração, o remetente Zipkin pode fazer o envio para a Web.

1. Ignore esta etapa caso tenha seguido nosso [guia para preparar um aplicativo do Azure Spring Cloud](spring-cloud-tutorial-prepare-app-deployment.md). Caso contrário, acesse o ambiente de desenvolvimento local e edite o arquivo pom.xml para incluir a seguinte dependência do Spring Cloud Sleuth:

    ```xml
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-sleuth</artifactId>
                <version>${spring-cloud-sleuth.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-sleuth</artifactId>
        </dependency>
    </dependencies>
    ```

1. Crie e implante novamente para o serviço do Azure Spring Cloud para refletir essas alterações.

## <a name="modify-the-sample-rate"></a>Modificar a taxa de amostragem

Modificando a taxa de amostragem, você pode alterar a taxa na qual a telemetria é coletada. Por exemplo, se você quiser reduzir a frequência da amostragem pela metade, abra o arquivo application.properties e altere a seguinte linha:

```xml
spring.sleuth.sampler.probability=0.5
```

Caso já tenha criado e implantado um aplicativo, modifique a taxa de amostra. Faça isso adicionando a linha anterior como uma variável de ambiente na CLI do Azure ou no portal do Azure.
::: zone-end

## <a name="enable-application-insights"></a>Habilitar o Application Insights

1. Acesse sua página de serviços do Azure Spring Cloud no portal do Azure.
1. Na página **Monitoramento** , selecione **Rastreamento Distribuído**.
1. Selecione **Editar configuração** para editar ou adicionar uma nova configuração.
1. Crie uma consulta do Application Insights ou selecione uma existente.
1. Escolha qual categoria de log deseja monitorar e especifique o tempo de retenção em dias.
1. Selecione **Aplicar** para aplicar o novo rastreamento.

## <a name="view-the-application-map"></a>Exibir o mapa do aplicativo

Retorne à página **Rastreamento Distribuído** e selecione **Exibir mapa do aplicativo**. Examine a representação visual do aplicativo das configurações de monitoramento. Para saber como usar o mapa do aplicativo, confira [Mapa do aplicativo: Triagem dos aplicativos distribuídos](../azure-monitor/app/app-map.md).

## <a name="use-search"></a>Usar a pesquisa

Use a função de pesquisa para consultar outros itens de telemetria específicos. Na página de **Rastreamento Distribuído** , selecione **Pesquisar**. Para obter mais informações sobre como usar a função de pesquisa, confira [Como usar a pesquisa no Application Insights](../azure-monitor/app/diagnostic-search.md).

## <a name="use-application-insights"></a>Usar o Application insights

O Application Insights fornece funcionalidades de monitoramento além do mapa do aplicativo e da função de pesquisa. Pesquise o nome do aplicativo no portal do Azure e, em seguida, abra uma página do Application Insights para encontrar informações de monitoramento. Para obter mais diretrizes sobre como usar essas ferramentas, confira [Consultas de log do Azure Monitor](/azure/data-explorer/kusto/query/).

## <a name="disable-application-insights"></a>Desabilitar o Application Insights

1. Acesse sua página de serviços do Azure Spring Cloud no portal do Azure.
1. Em **Monitoramento** , selecione **Rastreamento Distribuído**.
1. Selecione **Desabilitar** para desabilitar o Application Insights.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a habilitar e entender o rastreamento distribuído no Azure Spring Cloud. Para saber como associar serviços a um aplicativo, confira [Associar um banco de dados do Azure Cosmos DB a um aplicativo do Azure Spring Cloud](spring-cloud-tutorial-bind-cosmos.md).