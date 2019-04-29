---
title: Configurar Java APM e ferramentas de monitoramento no Linux – Serviço de Aplicativo do Azure
description: Saiba como enviar logs e métricas para seus aplicativos Java em execução no Serviço de Aplicativo do Azure Linux para provedores de NewRelic e o aplicativo Dynamics APM
services: app-service\web
author: rloutlaw
manager: angerobe
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 03/21/2019
ms.author: astay;routlaw
ms.custom: seodec18
ms.openlocfilehash: e6a22258266bda18c9ff79590d88e70d512f6c77
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60849958"
---
# <a name="how-to-application-performance-monitoring-tools-with-java-apps-on-azure-app-service-on-linux"></a>Instruções: ferramentas do monitoramento do desempenho de aplicativos com aplicativos Java no Serviço de Aplicativo do Azure no Linux

Este artigo mostra como conectar aplicativos Java implantados no Serviço de Aplicativo do Azure no Linux com o desempenho do aplicativo NewRelic e da AppDynamics monitoram as plataformas (APM).

## <a name="configure-new-relic"></a>Configurar o New Relic

1. Criar uma conta de NewRelic em [NewRelic.com](https://newrelic.com/signup)
2. Fazer o download do agente de Java do NewRelic, ele terá um nome de arquivo semelhante ao `newrelic-java-x.x.x.zip`.
3. Copie sua chave de licença, você precisará dela para configurar o agente mais tarde.
4. [SSH em sua instância do Serviço de Aplicativo](/azure/app-service/containers/app-service-linux-ssh-support) e crie um novo diretório `/home/site/wwwroot/apm`.
5. Carregue os arquivos do agente NewRelic Java descompactados em um diretório em `/home/site/wwwroot/apm`. Os arquivos para seu agente devem estar em `/home/site/wwwroot/apm/newrelic`.
6. Modifique o arquivo YAML em `/home/site/wwwroot/apm/newrelic/newrelic.yml` e substitua o valor de licença de espaço reservado com sua própria chave de licença.
7. No portal do Azure, navegue até seu aplicativo no serviço de aplicativo e criar uma nova configuração de aplicativo.
    - Se seu aplicativo estiver usando **Java SE**, crie uma variável de ambiente denominada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se você estiver usando **Tomcat**, crie uma variável de ambiente denominada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se você estiver usando **WildFly**, consulte a documentação do New Relic [aqui](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) para obter orientação sobre como instalar o agente de Java e a configuração do JBoss.
    - Se você já tiver uma variável de ambiente para `JAVA_OPTS` ou `CATALINA_OPTS`, acrescente o `javaagent` opção ao final do valor atual.

## <a name="configure-appdynamics"></a>Configurar o AppDynamics

1. Criar uma conta do AppDynamics em [AppDynamics.com](https://www.appdynamics.com/community/register/)
1. Fazer o download do agente Java do site do AppDynamics, o nome do arquivo será semelhante a `AppServerAgent-x.x.x.xxxxx.zip`
1. [SSH em sua instância do Serviço de Aplicativo](/azure/app-service/containers/app-service-linux-ssh-support) e crie um novo diretório `/home/site/wwwroot/apm`.
1. Carregue os arquivos do agente Java descompactados em um diretório em `/home/site/wwwroot/apm`. Os arquivos para seu agente devem estar em `/home/site/wwwroot/apm/appdynamics`.
1. No portal do Azure, navegue até seu aplicativo no serviço de aplicativo e criar uma nova configuração de aplicativo.
    - Se seu aplicativo estiver usando **Java SE**, crie uma variável de ambiente denominada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` quando `<YOUR_SITE_NAME>` for o nome do seu Serviço de Aplicativo.
    - Se você estiver usando **Java SE**, crie uma variável de ambiente denominada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` quando `<YOUR_SITE_NAME>` for o nome do seu Serviço de Aplicativo.
    - Se você estiver usando **WildFly**, consulte a documentação do AppDynamics [aqui](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) para obter orientação sobre como instalar o agente de Java e a configuração do JBoss.
