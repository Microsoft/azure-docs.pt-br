---
title: 'Início Rápido: monitoramento de aplicativo Web Java'
description: Fornece instruções para configurar rapidamente um aplicativo Web Java para monitoramento com o Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.reviewer: lagayhar
ms.date: 07/15/2019
ms.service: application-insights
ms.custom: mvc, seo-java-july2019
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: 160c0bd90e2e733cd2d57798c0e80f2df569cf0a
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489747"
---
# <a name="start-monitoring-your-java-web-application"></a>Começar a monitorar o aplicativo Web Java

Com o Azure Application Insights, você pode monitorar facilmente o aplicativo Web quanto à sua disponibilidade, desempenho e uso. Você também pode identificar e diagnosticar erros rapidamente em seu aplicativo sem esperar que um usuário os relate. Com o SDK do Java do Application Insights você pode monitorar pacotes comuns de terceiros, incluindo MongoDB, MySQL e Redis.

Este guia de início rápido lhe ajuda a adicionar o SDK do Application Insights para um projeto Web dinâmico Java existente.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

- Instalar o JRE 1.7 ou 1.8
- Instale o [Eclipse IDE para desenvolvedores de Java EE gratuito](https://www.eclipse.org/downloads/). Este guia de início rápido usa Eclipse Oxygen (4.7)
- Você precisará de uma assinatura do Azure e de um projeto Web dinâmico Java existente
 
Se você não tiver um projeto Web dinâmico Java, você poderá criar um com o [guia de início rápido Criar um aplicativo Web Java](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-java).

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Se você preferir a estrutura Spring tente o [guia para configurar um aplicativo inicializador Spring Boot para usar o Application Insights](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-java-applicationinsights)

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Habilitar o Application Insights

O Application Insights pode coletar dados de telemetria de qualquer aplicativo conectado à Internet, independentemente de ele estar sendo executado localmente ou na nuvem. Use as etapas a seguir para começar a exibir esses dados.

1. Selecione **Criar um recurso** > **Ferramentas de desenvolvedor** > **Application Insights**.

   ![Adicionando um Recurso do Application Insights](./media/java-quick-start/1createresourseappinsights.png)

   Uma caixa de configuração é exibida. Use a tabela a seguir para preencher os campos de entrada.

    | Configurações        | Valor           | DESCRIÇÃO  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor Globalmente Exclusivo | Nome que identifica o aplicativo que você está monitorando |
   | **Grupo de recursos**     | myResourceGroup      | Nome para o novo grupo de recursos no qual hospedar dados do Application Insights |
   | **Localidade** | Leste dos EUA | Escolher uma localização perto de você ou perto onde seu aplicativo está hospedado |

2. Clique em **Criar**.

## <a name="install-app-insights-plugin"></a>Instalar o plug-in Application Insights

1. Inicie o **Eclipse** > Clique em **Ajuda** > Selecione **Instalar Novo Software**.

   ![Formulário de recursos do Application Insights novo](./media/java-quick-start/000-j.png)

2. Copie ```https://dl.microsoft.com/eclipse``` no campo de "Trabalhar Com" > Marque **Kit de Ferramentas do Azure para Java** > Selecione **plug-in Application Insights para Java** > **Desmarque** "Contatar todos os sites de atualização durante a instalação para localizar o software necessário."

3. Depois que a instalação for concluída, você receberá uma solicitação para **Reiniciar o Eclipse**.

## <a name="configure-app-insights-plugin"></a>Configurar o Plugin Application Insights

1. Inicie **Eclipse** > Abra seu **Projeto** > Clique com o botão direito do mouse no nome do projeto no **Explorador de Projeto** > selecione **Azure** > Clique em **Entrar**.

2. Selecione o método de autenticação **Interativo** > Clique em **Entrar** > Quando solicitado, insira as **Credenciais do Azure** > Selecione a **Assinatura do Azure**.

3. Clique com o botão direito do mouse o nome do projeto em **Explorador de Projeto** > Selecione **Azure** > Clique em **Configurar o Application Insights**.

4. Verifique **Habilitar a telemetria com o Application Insights** > selecione o recurso Application Insights e a **Chave de Instrumentação** associada que você deseja vincular ao seu aplicativo Java.

   ![Menu de Configuração do Eclipse do Azure](./media/java-quick-start/0007-j.png)

5. Depois de configurar o plug-in do Application Insights, será necessário [publicar/republicar](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-java#deploy-the-app) o aplicativo novamente, antes que ele seja capaz de iniciar o envio de telemetria.

> [!NOTE]
> O SDK do Application Insights para Java é capaz de capturar e visualizar as métricas em tempo real, mas quando você habilita coleção de telemetria pela primeira vez, pode levar alguns minutos até que os dados comecem a aparecer no portal. Se esse aplicativo é um aplicativo de teste de baixo tráfego, tenha em mente que a maioria das métricas só são capturadas quando há operações ou solicitações ativas.

## <a name="start-monitoring-in-the-azure-portal"></a>Iniciar o monitoramento no Portal do Azure

1. Agora você pode reabrir a página **Visão Geral** do Application Insights no portal do Azure para exibir detalhes sobre seu aplicativo em execução no momento.

   ![Menu Visão Geral do Application Insights](./media/java-quick-start/3overview.png)

2. Clique em **Mapa do aplicativo** para obter um layout visual das relações de dependência entre os componentes do aplicativo. Cada componente mostra KPIs como alertas, desempenho, falhas e carregamento.

   ![Mapa de aplicativo](./media/java-quick-start/4appmap.png)

3.  Clique no ícone **Análise do Aplicativo** ![ícone Mapa do Aplicativo](./media/java-quick-start/006.png) **Exibir no Analytics**.  Isso abre a **Análise do Application Insights**, que fornece uma linguagem de consulta avançada para analisar todos os dados coletados pelo Application Insights. Nesse caso, uma consulta que renderiza a contagem de solicitações como um gráfico é gerada para você. Você pode escrever suas próprias consultas para analisar outros dados.

   ![Grafo de análise de solicitações de usuário durante um período de tempo](./media/java-quick-start/5analytics.png)

4. Volte para a página **Visão geral** e examine os gráficos KPI. Esse painel fornece estatísticas sobre a integridade do aplicativo, incluindo o número de solicitações de entrada, a duração dessas solicitações e as falhas que ocorrem.

   ![Grafos de linha do tempo de Visão Geral de Integridade](./media/java-quick-start/6kpidashboards.png)

   Para habilitar o gráfico **Tempo de Carregamento da Exibição de Página** para popular usando dados de **telemetria do lado do cliente**, adicione esse script a cada página que você desejar acompanhar:

   ```HTML
   <!-- 
   To collect user behavior analytics about your application, 
   insert the following script into each page you want to track.
   Place this code immediately before the closing </head> tag,
   and before any other scripts. Your first data will appear 
   automatically in just a few seconds.
   -->
   <script type="text/javascript">
     var appInsights=window.appInsights||function(config){
     function i(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s="AuthenticatedUserContext",h="start",c="stop",l="Track",a=l+"Event",v=l+"Page",y=u.createElement(o),r,f;y.src=config.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(y);try{t.cookie=u.cookie}catch(p){}for(t.queue=[],t.version="1.0",r=["Event","Exception","Metric","PageView","Trace","Dependency"];r.length;)i("track"+r.pop());return i("set"+s),i("clear"+s),i(h+a),i(c+a),i(h+v),i(c+v),i("flush"),config.disableExceptionTracking||(r="onerror",i("_"+r),f=e[r],e[r]=function(config,i,u,e,o){var s=f&&f(config,i,u,e,o);return s!==!0&&t["_"+r](config,i,u,e,o),s}),t
    }({
        instrumentationKey:"<instrumentation key>"
    });

    window.appInsights=appInsights;
    appInsights.trackPageView();
   </script>
    ```

5. Clique em **Live Stream**. Aqui você encontra métricas em tempo real relacionadas ao desempenho do seu aplicativo Web Java. O **Live Metrics Stream** inclui dados relacionados ao número de solicitações de entrada, a duração dessas solicitações e as falhas que ocorrem. Você também pode monitorar as métricas de desempenho críticas, tais como processador e memória em tempo real.

   ![Grafos de métricas do servidor](./media/java-quick-start/7livemetrics.png)

Para saber mais sobre como monitorar o Java, confira a [documentação adicional do Java do Application Insights](./../../azure-monitor/app/java-get-started.md).

## <a name="clean-up-resources"></a>Limpar recursos

Quando você terminar de testar, poderá excluir o grupo de recursos e todos os recursos relacionados. Para isso, siga as etapas abaixo.

1. No menu à esquerda no Portal do Azure, clique em **Grupos de recursos** e clique em **myResourceGroup**.
2. Em sua página de grupo de recursos, clique em **Excluir**, digite **myResourceGroup** na caixa de texto e clique **Excluir**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Localizar e diagnosticar problemas de desempenho](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
