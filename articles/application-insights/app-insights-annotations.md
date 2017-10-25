---
title: "Anotações de versão para o Application Insights | Microsoft Docs"
description: "Adicione uma implantação ou compile marcadores para seus gráficos do gerenciador de métricas no Application Insights."
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm
ms.assetid: 23173e33-d4f2-4528-a730-913a8fd5f02e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: bwren
ms.openlocfilehash: f7eb2f3cba535eb64db5544c498289c9e895987a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Anotações sobre gráficos de métricas no Application Insights
As anotações de versão nos gráficos do [Metrics Explorer](app-insights-metrics-explorer.md) mostram onde você implantou uma nova compilação ou outro evento relevante. Elas facilitam ver se suas alterações tinham qualquer efeito sobre o desempenho do aplicativo. Elas podem ser criadas automaticamente pelo [sistema de compilação do Visual Studio Team Services](https://www.visualstudio.com/en-us/get-started/build/build-your-app-vs). Você também pode criar anotações para sinalizar eventos desejados [criando-as no PowerShell](#create-annotations-from-powershell).

![Exemplo de anotações com correlação visível com tempo de resposta do servidor](./media/app-insights-annotations/00.png)



## <a name="release-annotations-with-vsts-build"></a>Anotações de versão com a compilação VSTS

As anotações de versão são um recurso de compilação baseado em nuvem e serviço de versão do Visual Studio Team Services. 

### <a name="install-the-annotations-extension-one-time"></a>Instalar a extensão Annotations (uma vez)
Para poder criar anotações de versão, você precisará instalar uma das muitas extensões de Team Service disponíveis no Visual Studio Marketplace.

1. Entre no seu projeto do [Visual Studio Team Services](https://www.visualstudio.com/en-us/get-started/setup/sign-up-for-visual-studio-online).
2. No Visual Studio Marketplace, [obtenha a extensão Anotações de Versão](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)e adicione-a à sua conta do Team Services.

![Na parte superior direita da página da Web do Team Services, abra o Marketplace. Selecione Visual Team Services e, em Compilação e Versão, escolha Ver mais.](./media/app-insights-annotations/10.png)

Você só precisa fazer isso uma vez em sua conta de serviços de equipe do Visual Studio. As anotações de versão agora podem ser configuradas para qualquer projeto na sua conta. 

### <a name="configure-release-annotations"></a>Criar anotações de versão

Você precisa obter uma chave de API separada para cada modelo de versão do VSTS.

1. Entre no [Portal do Microsoft Azure](https://portal.azure.com) e abra o recurso Application Insights que monitora seu aplicativo. (Ou [crie um agora](app-insights-overview.md), se você ainda não fez isso.)
2. Abra **Acesso à API**, **ID do Application Insights**.
   
    ![Em portal.azure.com, abra o recurso do Application Insights e escolha Configurações. Abra Acesso à API. Copie a ID do Aplicativo](./media/app-insights-annotations/20.png)

4. Em um janela de navegador separada, abra (ou crie) o modelo de versão que gerencia suas implantações do Visual Studio Team Services. 
   
    Adicione uma tarefa e selecione a tarefa de anotação de versão do Application Insights do menu.
   
    Cole a **ID do Aplicativo** que você copiou da folha Acesso à API.
   
    ![No Visual Studio Team Services, abra Versão, selecione uma definição de versão e escolha Editar. Clique em Adicionar Tarefa e selecione Anotação de Versão do Application Insights. Cole a ID do Application Insights.](./media/app-insights-annotations/30.png)
4. Defina o campo **APIKey** como uma variável `$(ApiKey)`.

5. De volta à janela do Azure, crie uma nova chave de API e faça uma cópia dela.
   
    ![Na folha de Acesso à API na janela do Azure, clique em Criar Chave de API. Forneça um comentário, marque Gravar anotações e clique em Gerar a Chave. Copie a nova chave.](./media/app-insights-annotations/40.png)

6. Abra a guia Configuração do modelo de versão.
   
    Crie uma definição de variável para `ApiKey`.
   
    Cole sua chave de API para a definição da variável ApiKey.
   
    ![Na janela Team Services, selecione a guia Configuração e clique em Adicionar Variável. Defina o nome para ApiKey e no Valor, cole a chave que você acabou de gerar e clique no ícone de bloqueio.](./media/app-insights-annotations/50.png)
7. Por fim, **Salve** a definição da versão.


## <a name="view-annotations"></a>Exibir anotações
Agora, sempre que você usar o modelo de versão para implantar uma nova versão, uma anotação será enviada ao Application Insights. As anotações aparecerão em gráficos no Metrics Explorer.

Clique em qualquer marcador de anotação para abrir os detalhes sobre a versão, incluindo o solicitante, a ramificação de controle do código-fonte, a definição da versão, o ambiente e muito mais.

![Clique em qualquer marcador de anotação de versão.](./media/app-insights-annotations/60.png)

## <a name="create-custom-annotations-from-powershell"></a>Criar anotações personalizadas no PowerShell
Você também pode criar anotações de qualquer processo que desejar (sem usar o VS Team System). 


1. Faça uma cópia local do [script do Powershell do GitHub](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

2. Obtenha a ID do aplicativo e crie uma chave de API na folha Acesso à API.

3. Chame o script assim:

```PS

     .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }
```

É fácil modificar o script, por exemplo, para criar as anotações para o que já passou.

## <a name="next-steps"></a>Próximas etapas

* [Criar um item de trabalho](app-insights-diagnostic-search.md#create-work-item)
* [Automação com o PowerShell](app-insights-powershell.md)
