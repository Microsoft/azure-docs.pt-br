---
title: Criar o perfil ativo de aplicativos do Serviço de Aplicativo do Azure com o Application Insights | Microsoft Docs
description: Crie o perfil ativo de aplicativos do Serviço de Aplicativo do Azure com o Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a53db9deb07863010c792943c71eb0af5d845af8
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026498"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Criar o perfil ativo de aplicativos do Serviço de Aplicativo do Azure com o Application Insights

Você pode executar o Profiler em aplicativos ASP.NET e ASP.NET Core que estão em execução no Serviço de Aplicativo do Azure usando a camada de serviço básica ou superior. Habilitar o Profiler no Linux somente é possível através [deste método](profiler-aspnetcore-linux.md).

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a> Habilitar o Profiler em seu aplicativo
Para habilitar o Profiler em um aplicativo, siga as instruções abaixo. Se você estiver executando um tipo diferente de serviço do Azure, aqui estão as instruções para habilitar o Profiler em outras plataformas compatíveis:
* [Serviços de Nuvem](./profiler-cloudservice.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Aplicativos do Service Fabric](./profiler-servicefabric.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Máquinas virtuais](./profiler-vm.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

O Application Insights Profiler é pré-instalado como parte do runtime dos Serviços de Aplicativos. As etapas a seguir mostrarão como habilitá-lo para o Serviço de Aplicativo. Siga estas etapas mesmo se você tiver incluído o SDK do App Insights em seu aplicativo no momento da compilação.

> [!NOTE]
> A instalação sem código do Application Insights Profiler segue a política de suporte do .NET Core.
> Para obter mais informações sobre tempos de execução com suporte, consulte [política de suporte do .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

1. Navegue até o painel de controle do Azure para seu serviço de aplicativo.
1. Habilite a configuração "Always On" para o serviço de aplicativo. Você pode encontrar essa configuração em **configurações**, página de **configuração** (consulte a captura de tela na próxima etapa) e selecione a guia **configurações gerais** .
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

## <a name="enable-profiler-for-other-clouds"></a>Habilitar o Profiler para outras nuvens

Atualmente, as únicas regiões que exigem modificações de ponto de extremidade são o [Azure governamental](../../azure-government/compare-azure-government-global-azure.md#application-insights) e o [Azure China](/azure/china/resources-developer-guide).

|Configurações de Aplicativo    | Nuvem do governo dos EUA | Nuvem da China |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://profiler.monitor.azure.us`    | `https://profiler.monitor.azure.cn` |
|ApplicationInsightsEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

## <a name="disable-profiler"></a>Desabilitar o Profiler

Para parar ou reiniciar o Profiler para uma instância do aplicativo individual, na barra lateral esquerda, selecione **webjobs** e interrompa o webjob chamado `ApplicationInsightsProfiler3` .

  ![Desabilitar o Profiler para trabalhos da web][disable-profiler-webjob]

Recomendamos que você tenha o Profiler habilitado em todos os seus aplicativos para descobrir problemas de desempenho o mais rápido possível.

Os arquivos do criador de perfil podem ser excluídos ao usar o WebDeploy para implantar alterações em seu aplicativo Web. Você pode impedir a exclusão excluindo a pasta App_Data de ser excluída durante a implantação. 


## <a name="next-steps"></a>Próximas etapas

* [Trabalhar com o Application Insights no Visual Studio](./visual-studio.md)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png