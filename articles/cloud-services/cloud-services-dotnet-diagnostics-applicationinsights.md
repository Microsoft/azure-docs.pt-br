---
title: "Solucionar problemas de Serviços de Nuvem usando o Application Insights | Microsoft Docs"
description: "Saiba como solucionar problemas do serviço de nuvem usando o Application Insights para processar dados do Diagnóstico do Azure."
services: cloud-services
documentationcenter: .net
author: sbtron
manager: timlt
editor: tysonn
ms.assetid: e93f387b-ef29-4731-ae41-0676722accb6
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: saurabh
ms.openlocfilehash: 4001ca908ff00b1a40829d687589080e9b07b18a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-cloud-services-using-application-insights"></a>Solucionar problemas de Serviços de Nuvem usando o Application Insights
Com o [Azure SDK 2.8](https://azure.microsoft.com/downloads/) e a extensão de diagnóstico do Azure 1.5, você pode enviar seus dados do Diagnóstico do Azure para seu serviço de nuvem diretamente para o Application Insights. Os logs coletados pelo Diagnóstico do Azure&mdash;incluindo logs de aplicativo, Logs de Eventos do Windows, Logs do ETW e contadores de desempenho&mdash;podem ser enviados ao Application Insights. Em seguida, você pode visualizar essas informações na interface do usuário do portal do Application Insights. Então, você pode usar o SDK do Application Insights para obter informações sobre as métricas e os logs que vêm do seu aplicativo, bem sobre dados em nível de sistema e infraestrutura que vêm do Diagnóstico do Azure.

## <a name="configure-azure-diagnostics-to-send-data-to-application-insights"></a>Configurar o Diagnóstico do Azure para enviar dados para o Application Insights
Siga estas etapas para configurar seu projeto de serviço de nuvem e enviar os dados do Diagnóstico do Azure para o Application Insights.

1. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse em uma função e selecione **Propriedades** para abrir o Designer de função.

    ![Propriedades da função Gerenciador de Soluções][1]

2. Na seção **Diagnóstico** do Designer de função, selecione a opção **Enviar dados de diagnóstico ao Application Insights**.

    ![O designer de função envia dados de diagnóstico para o application insights][2]

3. Na caixa de diálogo que abre em pop-up, selecione o recurso do Application Insights ao qual você gostaria de enviar os dados de diagnóstico do Azure. A caixa de diálogo permite selecionar um recurso do Application Insights existente na sua assinatura ou especificar manualmente uma chave de instrumentação para um recurso do Application Insights. Para saber mais sobre como criar um recurso do Application Insights, confira [Criar um novo recurso do Application Insights](../application-insights/app-insights-create-new-resource.md).

    ![selecionar recurso do application insights][3]

    Depois de adicionar o recurso do Application Insights, a chave de instrumentação para esse recurso fica armazenada como uma definição de configuração de serviço com o nome **APPINSIGHTS_INSTRUMENTATIONKEY**. Você pode alterar essa configuração para cada ambiente ou configuração de serviço. Para fazer isso, selecione uma configuração diferente na lista **Configuração do serviço** e especifique uma nova chave de instrumentação para essa configuração.

    ![selecionar configuração de serviço][4]

    A configuração **APPINSIGHTS_INSTRUMENTATIONKEY** é usada pelo Visual Studio para configurar a extensão de diagnóstico com as informações apropriadas do recurso do Application Insights durante a publicação. A configuração é uma maneira conveniente de definir chaves de instrumentação diferentes para configurações de serviço diferentes. O Visual Studio converterá essa configuração e a inserirá na configuração pública da extensão de diagnóstico durante o processo de publicação. Para simplificar o processo de configuração da extensão de diagnóstico com o PowerShell, a saída de pacote do Visual Studio também contém o XML de configuração pública com a chave de instrumentação do Application Insights adequada. Os arquivos de configuração públicos são criados na pasta Extensões e seguem o padrão *PaaSDiagnostics.&lt;RoleName&gt;.PubConfig.xml*. Quaisquer implantações baseadas em PowerShell podem usar esse padrão para mapear cada configuração para uma função.

4) Para configurar o diagnóstico do Azure para enviar todos os contadores de desempenho e logs em nível de erro coletados pelo agente de diagnóstico do Azure ao Application Insights, habilite a opção **Enviar dados de diagnóstico ao Application Insights**. 

    Se você quiser configurar em mais detalhes quais dados são enviados ao Application Insights, edite manualmente o arquivo *diagnostics.wadcfgx* para cada função. Confira [Configurar o Diagnóstico do Azure para enviar dados ao Application Insights](#configure-azure-diagnostics-to-send-data-to-application-insights) para saber mais sobre como atualizar a configuração manualmente.

Quando o serviço de nuvem estiver configurado para enviar dados de diagnóstico do Azure ao Application Insights, você poderá implantá-lo no Azure como faria normalmente, verificando se a extensão de diagnóstico do Azure está habilitada. Para saber mais, confira [Como publicar um serviço de nuvem usando o Visual Studio](../vs-azure-tools-publishing-a-cloud-service.md).  

## <a name="viewing-azure-diagnostics-data-in-application-insights"></a>Exibindo dados do Diagnóstico do Azure no Application Insights
A telemetria de diagnóstico do Azure aparecerá no recurso do Application Insights configurado para seu serviço de nuvem.

Os tipos de log de diagnóstico do Azure mapeiam para conceitos do Application Insights destas maneiras:

* Contadores de desempenho são exibidos como Métricas Personalizadas no Application Insights.
* Logs de Eventos do Windows são mostrados como Rastreamentos e Eventos Personalizados no Application Insights.
* Logs de Aplicativo, logs de ETW e logs de Infraestrutura de Diagnóstico são mostrados como Rastreamentos no Application Insights.

Para exibir dados de diagnóstico do Azure no Application Insights, siga um destes procedimentos:

* Use o [Metrics Explorer](../application-insights/app-insights-metrics-explorer.md) para visualizar quaisquer contadores de desempenho personalizados ou contagens de diferentes tipos de eventos do Log de Eventos do Windows.

    ![Métricas personalizadas no Metrics Explorer][5]

* Use [Pesquisar](../application-insights/app-insights-diagnostic-search.md) para pesquisar entre os logs de rastreamento enviados pelo Diagnóstico do Azure. Por exemplo, se uma exceção sem tratamento tiver feito uma função falhar e reciclar, informações sobre a exceção aparecerão no canal *Aplicativo* do *Log de Eventos do Windows*. Você pode usar a pesquisa para ver o erro do Log de Eventos do Windows e obter o rastreamento de pilha completo para a exceção para ajudar a encontrar a causa do problema.

    ![Pesquisar rastreamentos][6]

## <a name="next-steps"></a>Próximas etapas
* [Adicionar o SDK do Application Insights ao seu serviço de nuvem](../application-insights/app-insights-cloudservices.md) para enviar dados sobre solicitações, exceções, dependências e telemetria personalizada do seu aplicativo. Quando essas informações são combinadas aos dados do Diagnóstico do Azure, você pode obter uma exibição completa do seu aplicativo e do sistema no mesmo recurso do Application Insights.  

<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png
