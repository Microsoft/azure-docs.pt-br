---
title: Notas de versão para extensão de aplicativo Web do Azure-Application Insights
description: Notas de versões para a extensão de aplicativos Web do Azure para instrumentação de tempo de execução com Application Insights.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 06/26/2020
ms.openlocfilehash: b9da3b0647d5abe60dd8f6cb21163ff21f07205b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86539713"
---
# <a name="release-notes-for-azure-web-app-extension-for-application-insights"></a>Notas de versão para a extensão do aplicativo Web do Azure para Application Insights

Este artigo contém as notas de versões para a extensão de aplicativos Web do Azure para instrumentação de tempo de execução com Application Insights. Isso é aplicável somente a extensões pré-instaladas.

[Saiba](azure-web-apps.md) mais sobre a extensão do aplicativo Web do Azure para Application insights.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

- Como localizar a versão da extensão que estou no momento?
    - Acesse `https://<yoursitename>.scm.azurewebsites.net/ApplicationInsights`. Visite [o guia de solução de problemas passo a passo para o monitoramento baseado em extensão/agente](./azure-web-apps.md?tabs=net#troubleshooting) para obter mais informações.

- E se eu estiver usando extensões privadas?
    - Desinstale as extensões de site particulares, pois elas não são mais suportadas.

## <a name="release-notes"></a>Notas de versão

### <a name="2833"></a>2.8.33

- Agentes .NET, .NET Core, Java e Node.js e a extensão do Windows: suporte para nuvens soberanas. As cadeias de conexão podem ser usadas para enviar dados para nuvens soberanas.

### <a name="2831"></a>2.8.31

- Agente de ASP.NET Core: problema corrigido relacionado a uma das referências atualizadas do SDK do Application Insights (consulte problemas conhecidos do 2.8.26). Se a versão incorreta do `System.Diagnostics.DiagnosticSource.dll` já estiver carregada pelo tempo de execução, a extensão sem código agora não apresentará o aplicativo e fará o logoff. Para clientes afetados por esse problema, é aconselhável remover o `System.Diagnostics.DiagnosticSource.dll` da pasta bin ou usar a versão mais antiga da extensão, definindo "ApplicationInsightsAgent_EXTENSIONVERSION = 2.8.24"; caso contrário, o monitoramento de aplicativos não será habilitado.

### <a name="2826"></a>2.8.26

- Agente de ASP.NET Core: problema corrigido relacionado ao SDK do Application Insights atualizado. O agente não tentará carregar `AiHostingStartup` se o ApplicationInsights.dll já estiver presente na pasta bin. Isso resolve problemas relacionados à reflexão por meio do assembly \<AiHostingStartup\> . GetTypes ().
- Problemas conhecidos: a exceção `System.IO.FileLoadException: Could not load file or assembly 'System.Diagnostics.DiagnosticSource, Version=4.0.4.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51'` poderá ser lançada se outra versão da `DiagnosticSource` dll for carregada. Isso pode acontecer, por exemplo, se `System.Diagnostics.DiagnosticSource.dll` estiver presente na pasta de publicação. Como mitigação, use a versão anterior da extensão definindo as configurações do aplicativo nos serviços de aplicativos: ApplicationInsightsAgent_EXTENSIONVERSION = 2.8.24.

### <a name="2824"></a>2.8.24

- Versão reempacotada do 2.8.21.

### <a name="2823"></a>2.8.23

- Adicionado ASP.NET Core suporte a monitoramento sem código 3,0.
- Atualizado ASP.NET Core SDK para [2.8.0](https://github.com/microsoft/ApplicationInsights-aspnetcore/releases/tag/2.8.0) para versões de tempo de execução 2,1, 2,2 e 3,0. Os aplicativos destinados ao .NET Core 2,0 continuam a usar o 2.1.1 do SDK.

### <a name="2814"></a>2.8.14

- Atualizado ASP.NET Core versão do SDK do 2.3.0 para o mais recente (2.6.1) para aplicativos destinados ao .NET Core 2,1, 2,2. Os aplicativos destinados ao .NET Core 2,0 continuam a usar o 2.1.1 do SDK.

### <a name="2812"></a>2.8.12

- Suporte para aplicativos ASP.NET Core 2,2.
- Corrigido um bug na extensão ASP.NET Core causando injeção de SDK, mesmo quando o aplicativo já está instrumentado com o SDK. Para os aplicativos 2,1 e 2,2, a presença de ApplicationInsights.dll na pasta do aplicativo agora faz com que a extensão volte a ser desativada. Para os aplicativos 2,0, a extensão só fará a desativação se ApplicationInsights estiver habilitado com uma `UseApplicationInsights()` chamada.

- Correção permanente para resposta de HTML incompleta para aplicativos ASP.NET Core. Essa correção agora está estendida para funcionar para aplicativos .NET Core 2,2.

- Suporte adicionado para desativar a injeção de JavaScript para aplicativos ASP.NET Core ( `APPINSIGHTS_JAVASCRIPT_ENABLED=false appsetting` ). Para o ASP.NET Core, a injeção de JavaScript está no modo "recusar" por padrão, a menos que explicitamente desativada. (A configuração padrão é feita para manter o comportamento atual.)

- Foi corrigido um bug de extensão ASP.NET Core que causou injeção, mesmo que iKey não estivesse presente.
- Corrigido um bug na lógica de prefixo da versão do SDK que causou uma versão incorreta do SDK na telemetria.

- Prefixo de versão do SDK adicionado para aplicativos ASP.NET Core para identificar como a telemetria foi coletada.
- Correção da página SCM-ApplicationInsights para mostrar corretamente a versão da extensão pré-instalada.

### <a name="2810"></a>2.8.10

- Correção de resposta HTML incompleta para aplicativos ASP.NET Core.

## <a name="next-steps"></a>Próximas etapas

- Visite a [documentação do serviço Azure app](azure-web-apps.md) para obter mais informações sobre como configurar o monitoramento para Azure app Services. 
