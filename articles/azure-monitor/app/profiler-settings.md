---
title: Usar o painel de configurações do Azure Application Insights Profiler | Microsoft Docs
description: Ver o status do Profiler e iniciar sessões de criação de perfil
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 9603c45443c6339a127f977600eeff2ba57a283f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61227935"
---
# <a name="configure-application-insights-profiler"></a>Configurar o Profiler do Application Insights

## <a name="profiler-settings-pane"></a>Painel de configurações do Profiler

Para abrir o painel de configurações do Azure Application Insights Profiler, vá para o painel de desempenho do Application Insights e, em seguida, selecione o botão **Profiler**.

![Configurar o painel do Profiler][configure-profiler-entry]

O painel **Configurar o Application Insights Profiler** contém quatro recursos: 
* **Criar Perfil Agora**: Inicia a criação de perfil de sessões para todos os aplicativos que estão vinculados a esta instância do Application Insights.
* **Aplicativos vinculados**: Lista os aplicativos que enviam os dados de criação de perfil para este recurso do Application Insights.
* **Sessões em Andamento**: Exibe o status da sessão quando você seleciona **Criar Perfil Agora**. 
* **Sessões de criação de perfil recentes**: Exibe informações sobre sessões de criação de perfil passadas.

![Profiler sob demanda][profiler-on-demand]

## <a name="app-service-environment"></a>Ambiente do Serviço de Aplicativo
Dependendo de como seu Ambiente do Serviço de Aplicativo do Azure está configurado, a chamada para verificar o status do agente pode estar bloqueada. O painel pode exibir uma mensagem que o agente não está em execução, mesmo quando ele está em execução. Para ter certeza, você pode verificar o trabalho Web em seu aplicativo. Se todos os valores de configurações de aplicativo estão corretos e a extensão de site do Application Insights está instalada no aplicativo, o Profiler está em execução. Se o aplicativo está recebendo tráfego suficiente, sessões recentes de criação de perfil devem ser exibidas em uma lista.

## <a id="profileondemand"></a>Disparar o Profiler manualmente

### <a name="minimum-requirements"></a>Requisitos mínimos 
Para um usuário disparar manualmente uma sessão de criador de perfil, eles exigem no mínimo "gravação" acesso em suas funções para o componente do Application Insights. Na maioria dos casos você receberá esse acesso automaticamente e nenhum trabalho adicional é necessário. Se você estiver tendo problemas, a função de escopo de assinatura para adicionar seria a função "Colaborador de componente do Application Insights". [Veja mais informações sobre o controle de acesso de função no monitoramento do Azure](https://docs.microsoft.com/en-us/azure/azure-monitor/app/resources-roles-access-control).

Você pode disparar o Profiler manualmente com um único clique. Imagine que você está executando um teste de desempenho Web. Você precisará de rastreamentos para ajudá-lo a entender o desempenho de seu aplicativo Web sob carga. Ter controle sobre quando os rastreamentos são capturados é crucial, pois você sabe quando o teste de carga será executado. Mas o intervalo de amostragem aleatória poderá não detectar isso.

As próximas seções ilustram como esse cenário funciona:

### <a name="step-1-optional-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>Etapa 1: (Opcional) Gerar tráfego para o aplicativo Web iniciando um teste de desempenho Web

Se seu aplicativo da Web já tiver tráfego de entrada ou se você quiser gerar tráfego manualmente, pule esta seção e continue na Etapa 2.

1. Navegue até o portal do Application Insights, **Configurar** > **Teste de Desempenho**. 

1. Selecione o botão **Novo** para iniciar um novo teste de desempenho.

   ![Criar novo teste de desempenho][create-performance-test]

1. No painel **Novo teste de desempenho**, configure a URL de destino do teste. Aceite todas as configurações padrão e selecione **Executar teste** para começar a execução do teste de carga.

    ![Configurar o teste de carga][configure-performance-test]

    O novo teste é colocado na fila primeiro, seguido por um status *em andamento*.

    ![O teste de carga é enviado e colocado na fila][load-test-queued]

    ![O teste de carga está com a execução em andamento][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>Etapa 2: Iniciar uma sessão do Profiler sob demanda

1. Quando o teste de carga estiver em execução, inicie o Profiler para capturar rastreamentos no aplicativo Web enquanto ele recebe a carga.

1. Vá para o painel **Configurar o Profiler**.


### <a name="step-3-view-traces"></a>Etapa 3: Exibir rastreamentos

Depois que o Profiler terminar a execução, siga as instruções na notificação para ir para o painel Desempenho e exibir os rastreamentos.

## <a name="troubleshoot-the-profiler-on-demand-session"></a>Solucionar problemas da sessão sob demanda do Profiler

Após uma sessão sob demanda, você poderá ver uma mensagem de erro de tempo limite do Profiler:

![Erro de tempo limite do Profiler][profiler-timeout]

Você pode receber esse erro por qualquer um dos seguintes motivos:

* A sessão do Profiler sob demanda foi bem-sucedida, mas o Application Insights demorou mais do que o esperado para processar os dados coletados.  

  Se os dados não forem processados no prazo de 15 minutos, o portal exibirá uma mensagem de tempo limite. No entanto, após algum tempo, os rastreamentos do Profiler serão exibidos. Se receber uma mensagem de erro, você deverá ignorá-la por enquanto. Estamos trabalhando ativamente em uma correção.

* Seu aplicativo Web tem uma versão mais antiga do agente do Profiler que não tem o recurso sob demanda.  

  Se você tiver habilitado o Application Insights Profiler anteriormente, provavelmente precisará atualizar o agente do Profiler para começar a usar o recurso sob demanda.
  
Vá até o painel **Configurações de Aplicativo** dos Serviços de Aplicativos e verifique as seguintes configurações:
* **APPINSIGHTS_INSTRUMENTATIONKEY**: Substitua pela chave de instrumentação correta do Application Insights.
* **APPINSIGHTS_PORTALINFO**: ASP.NET
* **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

Se qualquer um dos valores anteriores não estiverem definidos, instale a extensão de site mais recente fazendo o seguinte:

1. Vá até o painel do **Application Insights** no portal dos Serviços de Aplicativos.

    ![Habilitar o Application Insights no portal dos Serviços de Aplicativos][enable-app-insights]

1. Se o painel **Application Insights** exibir um botão **Atualizar**, selecione-o para atualizar a extensão de site do Application Insights que instalará o agente mais recente do Profiler.

    ![Atualizar a extensão de site][update-site-extension]

1. Para garantir que o Profiler está ativado, selecione **Alterar** e, em seguida, selecione **OK** para salvar as alterações.

    ![Alterar e salvar o Application insights][change-and-save-appinsights]

1. Volte para o painel **Configurações do Aplicativo** do Serviço de Aplicativo para garantir que os valores a seguir estejam definidos:
   * **APPINSIGHTS_INSTRUMENTATIONKEY**: Substitua pela chave de instrumentação correta do Application Insights.
   * **APPINSIGHTS_PORTALINFO**: ASP.NET 
   * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

     ![Configurações do aplicativo para o Profiler][app-settings-for-profiler]

1. Opcionalmente, selecione **Extensões** e, em seguida, verifique a versão da extensão e determine se uma atualização está disponível.

    ![Verificar se há atualização da extensão][check-for-extension-update]

## <a name="next-steps"></a>Próximas etapas
[Habilitar o Profiler e ver os rastreios](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[create-performance-test]: ./media/profiler-settings/new-performance-test.png
[configure-performance-test]: ./media/profiler-settings/configure-performance-test.png
[load-test-queued]: ./media/profiler-settings/load-test-queued.png
[load-test-in-progress]: ./media/profiler-settings/load-test-inprogress.png
[enable-app-insights]: ./media/profiler-settings/enable-app-insights-blade-01.png
[update-site-extension]: ./media/profiler-settings/update-site-extension-01.png
[change-and-save-appinsights]: ./media/profiler-settings/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/profiler-settings/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/profiler-settings/check-extension-update-01.png
[profiler-timeout]: ./media/profiler-settings/profiler-timeout.png
