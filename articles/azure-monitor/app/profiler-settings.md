---
title: Usar o painel de configurações do Azure Application Insights Profiler | Microsoft Docs
description: Ver o status do Profiler e iniciar sessões de criação de perfil
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 9d9cc377ead0c297e8334d34255bd2c7c7cd39fc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86499402"
---
# <a name="configure-application-insights-profiler"></a>Configurar o Profiler do Application Insights

## <a name="updated-profiler-agent"></a>Agente do criador de perfil atualizado
Os recursos do gatilho funcionam apenas com a versão 2,6 ou mais recente do agente do criador de perfil. Se você estiver executando um serviço de Azure App, o agente será atualizado automaticamente. Você pode ver qual versão do agente está executando se você vai para a URL do kudu para seu site e acrescentar \DiagnosticServices ao final dele, desta forma:  `https://yourwebsite.scm.azurewebsites.net/diagnosticservices` . A Application Insights Profiler Webjob deve ter a versão 2,6 ou mais recente. Você pode forçar uma atualização reiniciando seu aplicativo Web. 

Se você estiver executando o criador de perfil em um serviço de nuvem ou VM, precisará ter a extensão do Windows Diagnóstico do Azure (WAD) 16.0.4 ou mais recente instalada. Você pode verificar a versão do WAD fazendo logon em sua VM e procurando este diretório: C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.16.0.4. O nome do diretório é a versão do WAD que está instalada. O agente de VM do Azure atualizará o WAD automaticamente quando novas versões estiverem disponíveis.

## <a name="profiler-settings-page"></a>Página de configurações do criador de perfil

Para abrir o painel configurações do profiler do Aplicativo Azure insights, vá para o painel de desempenho Application Insights e, em seguida, selecione o botão **Configurar criador de perfil** .

![Link para abrir a página de configurações do criador de perfil][configure-profiler-entry]

Isso abre uma página parecida com esta:

![Página de configurações do criador de perfil][configure-profiler-page]

A página **configurar Application insights Profiler** tem estes recursos:

| Recurso | Descrição |
|-|-|
Perfil agora | Inicia a criação de perfil de sessões para todos os aplicativos que estão vinculados a esta instância do Application Insights.
Gatilhos | Permite configurar gatilhos que fazem com que o criador de perfil seja executado. 
Sessões de criação de perfil recentes | Exibe informações sobre sessões de criação de perfil passadas.

## <a name="profile-now"></a>Perfil agora
Essa opção permite que você inicie uma sessão de criação de perfil sob demanda. Quando você clica nesse link, todos os agentes do criador de perfil que estão enviando dados para esse Application Insights instância começarão a capturar um perfil. Após 5 a 10 minutos, a sessão de perfil será mostrada na lista abaixo.

Para que um usuário acione manualmente uma sessão de criador de perfil, ele precisa de pelo menos acesso de "gravação" em sua função para o componente de Application Insights. Na maioria dos casos, você obtém esse acesso automaticamente e nenhum trabalho adicional é necessário. Se você estiver tendo problemas, a função de escopo da assinatura a ser adicionada seria a função "colaborador do componente do Application Insights". [Veja mais sobre o controle de acesso de função com o monitoramento do Azure](./resources-roles-access-control.md).

## <a name="trigger-settings"></a>Configurações do gatilho
![Submenu configurações do gatilho][trigger-settings-flyout]

Clicar no botão gatilhos na barra de menus abre a caixa configurações do gatilho. Você pode configurar o gatilho para iniciar a criação de perfil quando a porcentagem de utilização de CPU ou memória atingir o nível definido.

| Configuração | Descrição |
|-|-|
Botão ligar/desligar | Em: o profiler pode ser iniciado por este gatilho; Desativada: o criador de perfil não será iniciado por este gatilho.
Limite de memória | Quando esse percentual de memória estiver em uso, o criador de perfil será iniciado.
Duration | Define o período de tempo que o profiler executará quando for disparado.
Resfriamento | Define o período de tempo que o profiler aguardará antes de verificar a memória ou o uso da CPU novamente depois que ele for disparado.

## <a name="recent-profiling-sessions"></a>Sessões de criação de perfil recentes
Esta seção da página mostra informações sobre as sessões de criação de perfil recentes. Uma sessão de criação de perfil representa o período de tempo em que o agente do criador de perfil estava usando um perfil em um dos computadores que hospedam seu aplicativo. Você pode abrir os perfis de uma sessão clicando em uma das linhas. Para cada sessão, mostramos:

| Configuração | Descrição |
|-|-|
Disparado por | Como a sessão foi iniciada por um gatilho, perfil agora ou amostragem padrão. 
Nome do Aplicativo | Nome do aplicativo cujo perfil foi criado.
Instância do computador | Nome do computador no qual o agente do criador de perfil foi executado.
Timestamp | Hora em que o perfil foi capturado.
Rastrear | Número de rastreamentos que foram anexados a solicitações individuais.
CPU (%) | Porcentagem da CPU que estava sendo usada enquanto o criador de perfil estava em execução.
Memória | Porcentagem de memória que estava sendo usada enquanto o criador de perfil estava em execução.

## <a name="use-web-performance-tests-to-generate-traffic-to-your-application"></a><a id="profileondemand"></a> Usar testes de desempenho da Web para gerar tráfego para seu aplicativo

Você pode disparar o Profiler manualmente com um único clique. Imagine que você está executando um teste de desempenho Web. Você precisará de rastreamentos para ajudá-lo a entender como seu aplicativo Web está sendo executado sob carga. Ter controle sobre quando os rastreamentos são capturados é crucial, pois você sabe quando o teste de carga será executado. Mas o intervalo de amostragem aleatória poderá não detectar isso.

As próximas seções ilustram como esse cenário funciona:

### <a name="step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>Etapa 1: gerar o tráfego para seu aplicativo Web iniciando um teste de desempenho da Web

Se seu aplicativo da Web já tiver tráfego de entrada ou se você quiser gerar tráfego manualmente, pule esta seção e continue na Etapa 2.

1. No portal de Application insights, selecione **Configurar**  >  **teste de desempenho**. 

1. Selecione o botão **Novo** para iniciar um novo teste de desempenho.

   ![Criar novo teste de desempenho][create-performance-test]

1. No painel **Novo teste de desempenho**, configure a URL de destino do teste. Aceite todas as configurações padrão e selecione **Executar teste** para começar a execução do teste de carga.

    ![Configurar o teste de carga][configure-performance-test]

    O novo teste é colocado na fila primeiro, seguido por um status *em andamento*.

    ![O teste de carga é enviado e colocado na fila][load-test-queued]

    ![O teste de carga está com a execução em andamento][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>Etapa 2: iniciar uma sessão sob demanda do profiler

1. Quando o teste de carga estiver em execução, inicie o Profiler para capturar rastreamentos no aplicativo Web enquanto ele recebe a carga.

1. Vá para o painel **Configurar o Profiler**.


### <a name="step-3-view-traces"></a>Etapa 3: Exibir rastreamentos

Depois que o Profiler terminar a execução, siga as instruções na notificação para ir para o painel Desempenho e exibir os rastreamentos.

## <a name="next-steps"></a>Próximas etapas
[Habilitar o Profiler e ver os rastreios](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[configure-profiler-page]: ./media/profiler-settings/configureBlade.png
[trigger-settings-flyout]: ./media/profiler-settings/CPUTrigger.png
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
