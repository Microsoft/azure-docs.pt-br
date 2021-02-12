---
title: Criar o perfil ativo de aplicativos do Serviço de Aplicativo do Azure com o Application Insights | Microsoft Docs
description: Crie o perfil ativo de aplicativos do Serviço de Aplicativo do Azure com o Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: b1158a614da9ba32f628aba5dd2ed2cc71b4b455
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947032"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Criar o perfil ativo de aplicativos do Serviço de Aplicativo do Azure com o Application Insights

Você pode executar o Profiler em aplicativos ASP.NET e ASP.NET Core que estão em execução no Serviço de Aplicativo do Azure usando a camada de serviço básica ou superior. Habilitar o Profiler no Linux somente é possível através [deste método](profiler-aspnetcore-linux.md).

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a> Habilitar o Profiler em seu aplicativo
Para habilitar o Profiler em um aplicativo, siga as instruções abaixo. Se você estiver executando um tipo diferente de serviço do Azure, aqui estão as instruções para habilitar o Profiler em outras plataformas compatíveis:
* [Serviços de Nuvem](./profiler-cloudservice.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Aplicativos do Service Fabric](./profiler-servicefabric.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Máquinas virtuais](./profiler-vm.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

O Application Insights Profiler é pré-instalado como parte do runtime dos Serviços de Aplicativos. As etapas a seguir mostrarão como habilitá-lo para o Serviço de Aplicativo. Siga estas etapas mesmo se você tiver incluído o SDK do App Insights em seu aplicativo no momento da compilação.

1. Navegue até o painel de controle do Azure para seu serviço de aplicativo.
1. Habilite a configuração "Always On" para o serviço de aplicativo. Você pode encontrar essa configuração em **configurações**, página de **configuração** (consulte a captura de tela na próxima etapa) e clique na guia **configurações gerais** .
1. Navegue até **configurações > página Application insights** .

   ![Habilitar Insights de Aplicativo no portal de Serviços de Aplicativos](./media/profiler/AppInsights-AppServices.png)

1. Siga as instruções no painel para criar um novo recurso ou selecione um recurso existente do App Insights para monitorar seu aplicativo. Verifique também o Profiler está **On**. Se o recurso do Application Insights estiver em uma assinatura diferente do Serviço de Aplicativo, você não poderá usar essa página para configurar o Application Insights. Você ainda pode fazer isso manualmente criando as configurações de aplicativo necessárias. [A próxima seção contém instruções para habilitar manualmente o Profiler.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Adicione a extensão de site do App Insights][Enablement UI]

1. O Profiler agora está ativado usando uma configuração de aplicativo do Serviço de Aplicativo.

    ![Configuração de aplicativo para o Profiler][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Habilitar o Profiler manualmente ou com o Azure Resource Manager
O Application Insights Profiler pode ser habilitado criando configurações de aplicativo para seu Serviço de Aplicativo do Azure. A página com as opções mostradas acima cria essas configurações de aplicativo para você. Porém, você pode automatizar a criação dessas configurações usando um modelo ou outros meios. Essas configurações também funcionarão se o recurso do Application Insights estiver em uma assinatura diferente do Serviço de Aplicativo do Azure.
Aqui estão as configurações necessárias para habilitar o criador de perfil:

|Configurações de Aplicativo    | Valor    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | iKey para seu recurso do Application Insights    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


Você pode definir esses valores usando [modelos de Azure Resource Manager](./azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), [Azure PowerShell](/powershell/module/az.websites/set-azwebapp)  [CLI do Azure](/cli/azure/webapp/config/appsettings).

### <a name="enabling-profiler-for-other-clouds-manually"></a>Como habilitar o criador de perfil para outras nuvens manualmente

Se você quiser habilitar o criador de perfil para outras nuvens, poderá usar as configurações de aplicativo abaixo.

|Configurações de Aplicativo    | Valores do Governo dos EUA| Nuvem da China |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://profiler.monitor.azure.us`    | `https://profiler.monitor.azure.cn` |
|ApplicationInsightsEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

## <a name="disable-profiler"></a>Desabilitar o Profiler

Para parar ou reiniciar o Profiler para uma instância do aplicativo individual, em **WebJobs**, pare o webjob chamado ApplicationInsightsProfiler3. Mesmo que o criador de perfil esteja desabilitado usando a opção na página Application Insights, conforme descrito acima, o processo do criador de perfil ainda será executado. O criador de perfil verificará se ele está habilitado. Se ele estiver desabilitado, ele entrará em suspensão por um período antes de verificar novamente. Ele não fará nenhuma criação de perfil se estiver desabilitado. Se você desabilitar esse webjob, o processo do criador de perfil não será executado, mesmo para verificar se ele está habilitado.

  ![Desabilitar o Profiler para trabalhos da web][disable-profiler-webjob]

Recomendamos que você tenha o Profiler habilitado em todos os seus aplicativos para descobrir problemas de desempenho o mais rápido possível.

Os arquivos do criador de perfil podem ser excluídos ao usar o WebDeploy para implantar alterações em seu aplicativo Web. Você pode impedir a exclusão excluindo a pasta App_Data de ser excluída durante a implantação. 


## <a name="next-steps"></a>Próximas etapas

* [Trabalhar com o Application Insights no Visual Studio](./visual-studio.md)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png

