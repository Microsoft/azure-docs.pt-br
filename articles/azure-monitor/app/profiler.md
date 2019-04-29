---
title: Criar o perfil ativo de aplicativos do Serviço de Aplicativo do Azure com o Application Insights | Microsoft Docs
description: Crie o perfil ativo de aplicativos do Serviço de Aplicativo do Azure com o Application Insights Profiler.
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
ms.openlocfilehash: b8f6a2d12e1a9920421e6491432b516520ae110b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730120"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Criar o perfil ativo de aplicativos do Serviço de Aplicativo do Azure com o Application Insights

Você pode executar Profiler em ASP.NET e ASP.NET Core aplicativos que estão em execução no serviço de aplicativo do Azure usando a camada de serviço básico ou superior. Habilitar o Profiler no Linux somente é possível através [deste método](profiler-aspnetcore-linux.md).

## <a id="installation"></a> Habilitar o Profiler em seu aplicativo
Para habilitar o Profiler em um aplicativo, siga as instruções abaixo. Se você estiver executando um tipo diferente de serviço do Azure, aqui estão as instruções para habilitar o Profiler em outras plataformas com suporte:
* [Serviços de Nuvem](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Aplicativos do Service Fabric](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler previamente é instalado como parte do tempo de execução dos serviços de aplicativo. As etapas a seguir mostram como habilitar o serviço de aplicativo. Siga estas etapas, mesmo se você incluiu o SDK do App Insights em seu aplicativo no momento da compilação.

1. Habilite a configuração "Always On" para o serviço de aplicativo. Você pode atualizar a configuração na página de configuração do seu serviço de aplicativo em configurações gerais.
1. Vá ao painel **Serviços de Aplicativos** no portal do Azure.
1. Navegue até o painel **Configurações > Application Insights**.

   ![Habilitar Insights de Aplicativo no portal de Serviços de Aplicativos](./media/profiler/AppInsights-AppServices.png)

1. Siga as instruções no painel para criar um novo recurso ou selecione um recurso existente do App Insights para monitorar seu aplicativo. Verifique também o Profiler está **On**. Se o recurso do Application Insights está em uma assinatura diferente do seu serviço de aplicativo, será possível usar esta página para configurar o Application Insights. Você ainda pode fazer isso manualmente embora criando as configurações necessárias do aplicativo manualmente. [A próxima seção contém instruções para habilitar o Profiler manualmente.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Adicione a extensão de site do App Insights][Enablement UI]

1. O Profiler agora está ativado usando uma configuração de aplicativo do Serviço de Aplicativo.

    ![Configuração de aplicativo para o Profiler][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Habilitar o Profiler manualmente ou com o Azure Resource Manager
Application Insights Profiler pode ser habilitado com a criação de configurações de aplicativo para seu serviço de aplicativo do Azure. A página com as opções mostradas acima cria essas configurações de aplicativo para você. Mas você pode automatizar a criação dessas configurações usando um modelo ou outros meios. Essas configurações também funcionará se o recurso do Application Insights está em uma assinatura diferente do seu serviço de aplicativo do Azure.
Aqui estão as configurações necessárias para habilitar o criador de perfil:

|Configurações de Aplicativo    | Valor    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | iKey do recurso do Application Insights    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


Você pode definir esses valores usando [Azure Resource Manager Templates](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), [do Azure Powershell](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp), [CLI do Azure](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest).


## <a name="disable-profiler"></a>Desabilitar o Profiler

Para interromper ou reiniciar o Profiler de uma instância individual do aplicativo, em **Trabalhos Web**, acesse o recurso do aplicativo. Para excluir o Profiler, vá para **Extensões**.

![Desabilitar o Profiler para trabalhos da web][disable-profiler-webjob]

Recomendamos que você tenha o Profiler habilitado em todos os seus aplicativos para descobrir problemas de desempenho o mais rápido possível.

Arquivos do Profiler podem ser excluídos, ao usar WebDeploy para implantar as alterações ao seu aplicativo web. Você pode impedir a exclusão, excluindo a pasta App_Data da exclusão durante a implantação. 


## <a name="next-steps"></a>Próximas etapas

* [Trabalhar com o Application Insights no Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
