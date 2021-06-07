---
title: Tutorial – Usar o Painel do Circuit Breaker com o Azure Spring Cloud
description: Saiba como usar o Painel do Circuit Breaker com o Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 04/06/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 09acdc0c4ea2dbe90c0b7b037b6aec501cc1dd55
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223023"
---
# <a name="tutorial-use-circuit-breaker-dashboard-with-azure-spring-cloud"></a>Tutorial: Usar o Painel do Circuit Breaker com o Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java

O Spring [Cloud Netflix Turbine](https://github.com/Netflix/Turbine) é amplamente usado para agregar vários fluxos de métricas de [Hystrix](https://github.com/Netflix/Hystrix) para que os fluxos possam ser monitorados em uma só exibição usando o painel do Hystrix. Este tutorial demostra como usá-lo no Azure Spring Cloud.
> [!NOTE]
> O Netflix Hystrix é amplamente usado em muitos aplicativos do Spring Cloud, mas não está mais em desenvolvimento ativo. Se está desenvolvendo um novo projeto, use implementações do Spring Cloud Circuit Breaker, como [resilience4j](https://github.com/resilience4j/resilience4j). Diferente do Turbine mostrado neste tutorial, a nova estrutura de Disjuntor do Spring Cloud unifica todas as implementações do pipeline de dados de métricas no Micrometer, que também é compatível com o Azure Spring Cloud. [Saiba mais](https://docs.microsoft.com/azure/spring-cloud/how-to-circuit-breaker-metrics).

## <a name="prepare-your-sample-applications"></a>Preparar os aplicativos de exemplo
O exemplo é bifurcado deste [repositório](https://github.com/StackAbuse/spring-cloud/tree/master/spring-turbine).

Clone o repositório de exemplo para seu ambiente de desenvolvimento:
```
git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
cd Azure-Spring-Cloud-Samples/hystrix-turbine-sample
```

Crie os três aplicativos que serão usados neste tutorial:
* user-service: Um serviço REST simples que tem um ponto de extremidade de /personalized/{id}
* recommendation-service: Um serviço REST simples que tem um ponto de extremidade de /recommendations, que será chamado por user-service.
* hystrix-turbine: Um serviço de painel do Hystrix para exibir fluxos do Hystrix e um serviço do Turbine que agrega o fluxo de métricas do Hystrix de outros serviços.
```
mvn clean package -D skipTests -f user-service/pom.xml
mvn clean package -D skipTests -f recommendation-service/pom.xml
mvn clean package -D skipTests -f hystrix-turbine/pom.xml
```
## <a name="provision-your-azure-spring-cloud-instance"></a>Provisionar sua instância do Azure Spring Cloud
Siga o procedimento, [Provisionar uma instância de serviço na CLI do Azure](./spring-cloud-quickstart.md#provision-an-instance-of-azure-spring-cloud).

## <a name="deploy-your-applications-to-azure-spring-cloud"></a>Implantar seus aplicativos no Azure Spring Cloud
Esses aplicativos não usam o **Servidor de Configuração**, portanto, não há necessidade de configurar o **Servidor de Configuração** para o Azure Spring Cloud.  Crie e implante da seguinte maneira:
```azurecli
az spring-cloud app create -n user-service --assign-endpoint
az spring-cloud app create -n recommendation-service
az spring-cloud app create -n hystrix-turbine --assign-endpoint

az spring-cloud app deploy -n user-service --jar-path user-service/target/user-service.jar
az spring-cloud app deploy -n recommendation-service --jar-path recommendation-service/target/recommendation-service.jar
az spring-cloud app deploy -n hystrix-turbine --jar-path hystrix-turbine/target/hystrix-turbine.jar
```
## <a name="verify-your-apps"></a>Verificar seus aplicativos
Depois que todos os aplicativos estiverem em execução e detectáveis, acesse `user-service` com o caminho https://<username>-user-service.azuremicroservices.io/personalized/1 de seu navegador. Se user-service puder acessar `recommendation-service`, você deverá obter a saída a seguir. Atualize a página da Web algumas vezes se não funcionar.
```json
[{"name":"Product1","description":"Description1","detailsLink":"link1"},{"name":"Product2","description":"Description2","detailsLink":"link3"},{"name":"Product3","description":"Description3","detailsLink":"link3"}]
```
## <a name="access-your-hystrix-dashboard-and-metrics-stream"></a>Acessar o painel do Hystrix e o fluxo de métricas
Verifique usando pontos de extremidade públicos ou pontos de extremidade de teste privados.

### <a name="using-public-endpoints"></a>Usando pontos de extremidade públicos
Acesse hystrix-turbine com o caminho `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/hystrix` de seu navegador.  A figura a seguir mostra o painel do Hystrix em execução neste aplicativo.

![Painel do Hystrix](media/spring-cloud-circuit-breaker/hystrix-dashboard.png)

Copie a URL do fluxo do Turbine `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/turbine.stream?cluster=default` na caixa de texto e clique em **Monitorar Fluxo**.  Isso exibirá a o painel. Se nada aparecer no visualizador, pressione os pontos de extremidade `user-service` para gerar fluxos.

![Fluxo do Hystrix](media/spring-cloud-circuit-breaker/hystrix-stream.png) Agora, você pode fazer experimentos com o Painel do Circuit Breaker.
> [!NOTE] 
> Em produção, o painel do Hystrix e o fluxo de métricas não devem ser expostos à Internet.

### <a name="using-private-test-endpoints"></a>Usando pontos de extremidade de teste privados
Os fluxos de métricas do Hystrix também podem ser acessados de `test-endpoint`. Como um serviço de back-end, não atribuímos um ponto de extremidade público para `recommendation-service`, mas podemos mostrar suas métricas com test-endpoint em `https://primary:<KEY>@<SERVICE-NAME>.test.azuremicroservices.io/recommendation-service/default/actuator/hystrix.stream`

![Fluxo de test-endpoint do Hystrix](media/spring-cloud-circuit-breaker/hystrix-test-endpoint-stream.png)

Como um aplicativo Web, o painel do Hystrix deve estar funcionando em `test-endpoint`. Se ele não estiver funcionando corretamente, pode haver dois motivos: primeiro, o uso de `test-endpoint` alterou a URL base de `/ to /<APP-NAME>/<DEPLOYMENT-NAME>` ou, segundo, o aplicativo Web está usando um caminho absoluto para o recurso estático. Para fazê-lo funcionar em `test-endpoint`, talvez seja necessário editar manualmente o <base> nos arquivos de front-end.

## <a name="next-steps"></a>Próximas etapas
* [Provisionar uma instância de serviço na CLI do Azure](spring-cloud-quickstart.md#provision-an-instance-of-azure-spring-cloud)
* [Preparar um aplicativo Spring Java para implantação no Azure Spring Cloud](how-to-prepare-app-deployment.md)

