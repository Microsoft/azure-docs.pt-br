---
title: Coletar métricas do disjuntor do Spring Cloud Resilience4J
description: Como coletar métricas de disjuntor do Spring Cloud Resilience4J.
author: MikeDodaro
ms.author: brendanm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: devx-track-java
ms.openlocfilehash: e44e7c5d04695d5bd65d2eedc5474889a707c8bd
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882137"
---
# <a name="collect-spring-cloud-resilience4j-circuit-breaker-metrics-preview"></a>Coletar métricas do disjuntor do Spring Cloud Resilience4J (versão prévia)

Este documento explica como coletar métricas de disjuntor do Spring Cloud Resilience4j com Application Insights agente em processo Java.  Com esse recurso, você pode monitorar as métricas do disjuntor de resilience4j de Application Insights.

Usamos o [Spring-Cloud-Circuit-disjuntor-demo](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) para mostrar como ele funciona.

## <a name="prerequisites"></a>Pré-requisitos

* Habilite o agente de In-Process Java do [Guia do agente de In-Process do Java para Application insights](./spring-cloud-howto-application-insights.md#enable-java-in-process-agent-for-application-insights). 

* Habilite a coleta de dimensão para métricas de resilience4j do [Guia de Application insights](../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).

* Instale o Git, o Maven e o Java, se ainda não estiver em uso pelo computador de desenvolvimento.

## <a name="build-and-deploy-apps"></a>Compilar e implantar aplicativos

O procedimento a seguir cria e implanta aplicativos.

1. Clone e crie o repositório de demonstração.

```bash
git clone https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo.git
cd spring-cloud-circuitbreaker-demo && mvn clean package -DskipTests
```

2. Criar aplicativos com pontos de extremidade

```azurecli
az spring-cloud app create --name resilience4j --is-public \
    -s ${asc-service-name} -g ${asc-resource-group}
az spring-cloud app create --name reactive-resilience4j --is-public \
    -s ${asc-service-name} -g ${asc-resource-group}
```

3. Implantar aplicativos.

```azurecli
az spring-cloud app deploy -n resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-resilience4j/target/spring-cloud-circuitbreaker-demo-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
az spring-cloud app deploy -n reactive-resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-reactive-resilience4j/target/spring-cloud-circuitbreaker-demo-reactive-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
```

> [!Note]
>
> * Inclua a dependência necessária para Resilience4j:
>
>   ```xml
>   <dependency>
>       <groupId>io.github.resilience4j</groupId>
>       <artifactId>resilience4j-micrometer</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-circuitbreaker-resilience4j</artifactId>
>   </dependency>
>   ```
> * O código do cliente deve usar a API do `CircuitBreakerFactory` , que é implementada como uma `bean` criada automaticamente quando você inclui um iniciador de disjuntor de nuvem Spring. Para obter detalhes, consulte [disjuntor de circuito de nuvem Spring](https://spring.io/projects/spring-cloud-circuitbreaker#overview).
>
> * As duas dependências a seguir têm conflitos com pacotes resilient4j acima.  Verifique se o cliente não os inclui.
>
>   ```xml
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-sleuth</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-zipkin</artifactId>
>   </dependency>
>   ```
>
>
> Navegue até a URL fornecida pelos aplicativos de gateway e acesse o ponto de extremidade de [Spring-Cloud-Circuit-disjuntor-demo](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) da seguinte maneira:
>
>   ```console
>   /get
>   /get/delay/{seconds}
>   /get/fluxdelay/{seconds}
>   ```

## <a name="locate-resilence4j-metrics-from-portal"></a>Localizar métricas do Resilence4j no portal

1. Selecione a folha **Application insights** no portal do Azure Spring Cloud e clique em **Application insights**.

   [![resilience4J 0](media/spring-cloud-resilience4j/resilience4J-0.png)](media/spring-cloud-resilience4j/resilience4J-0.PNG)

2. Selecione **métricas** na página **Application insights** .  Selecione **Azure. applicationinsights** do **namespace de métricas**.  Selecione também **resilience4j_circuitbreaker_buffered_calls** métricas com **média**.

   [![resilience4J 1](media/spring-cloud-resilience4j/resilience4J-1.png)](media/spring-cloud-resilience4j/resilience4J-1.PNG)

3. Selecione **resilience4j_circuitbreaker_calls** métricas e **média**.

   [![resilience4J 2](media/spring-cloud-resilience4j/resilience4J-2.png)](media/spring-cloud-resilience4j/resilience4J-2.PNG)

4. Selecione **resilience4j_circuitbreaker_calls**  métricas e **média**.  Clique em **Adicionar filtro** e selecione nome como **createNewAccount**.

   [![resilience4J 3](media/spring-cloud-resilience4j/resilience4J-3.png)](media/spring-cloud-resilience4j/resilience4J-3.PNG)

5. Selecione **resilience4j_circuitbreaker_calls**  métricas e **média**.  Em seguida, clique em **aplicar divisão** e selecione **tipo**.

   [![resilience4J 4](media/spring-cloud-resilience4j/resilience4J-4.png)](media/spring-cloud-resilience4j/resilience4J-4.PNG)

6. Selecione **resilience4j_circuitbreaker_calls**, '**resilience4j_circuitbreaker_buffered_calls** e **resilience4j_circuitbreaker_slow_calls** métricas com **média**.

   [![resilience4J 5](media/spring-cloud-resilience4j/resilience4j-5.png)](media/spring-cloud-resilience4j/resilience4j-5.PNG)

## <a name="see-also"></a>Confira também

* [Application Insights](./spring-cloud-howto-application-insights.md)
* [Rastreamento distribuído](spring-cloud-tutorial-distributed-tracing.md)
* [Painel do disjuntor](spring-cloud-tutorial-circuit-breaker.md)