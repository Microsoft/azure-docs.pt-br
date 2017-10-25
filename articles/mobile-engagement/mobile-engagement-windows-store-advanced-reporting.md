---
title: "Relatórios avançados do Windows Universal com o Engagement para Aplicativos Móveis"
description: Como integrar o Azure Mobile Engagement com aplicativos do Windows Universal
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: ea5030bf-73ac-49b7-bc3e-c25fc10e945a
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: feac309db1ffce0945012e293bfc1df417aed876
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-reporting-with-the-windows-universal-apps-engagement-sdk"></a>Relatórios avançados com o SDK do Engagement para Aplicativos Universais do Windows
> [!div class="op_single_selector"]
> * [Universal do Windows](mobile-engagement-windows-store-advanced-reporting.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-reporting.md)
> 
> 

Este tópico descreve cenários de relatório adicionais em seu aplicativo universal do Windows. Estes cenários incluem opções que você pode optar para aplicar ao aplicativo criado no tutorial [Introdução](mobile-engagement-windows-store-dotnet-get-started.md) .

## <a name="prerequisites"></a>Pré-requisitos
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

Antes de iniciar este tutorial, você deve primeiro concluir o tutorial [Introdução](mobile-engagement-windows-store-dotnet-get-started.md) , que é deliberadamente direto e simples. Este tutorial aborda as opções adicionais que você pode escolher.

## <a name="specifying-engagement-configuration-at-runtime"></a>Como especificar a configuração do Engagement em tempo de execução
A configuração do Engagement é centralizada no arquivo `Resources\EngagementConfiguration.xml` de seu projeto, que é onde ele foi especificado no tópico [Introdução](mobile-engagement-windows-store-dotnet-get-started.md) .

Mas você também pode especificá-lo em tempo de execução: você pode chamar o método a seguir antes da inicialização do agente do Engagement:

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);



## <a name="recommended-method-overload-your-page-classes"></a>Método recomendado: sobrecarregar suas classes `Page`
Para ativar o relatório de todos os logs exigidos pelo Engagement para computar os usuários, sessões, atividades, falhas e das estatísticas técnicas, faça todas as suas subclasses `Page` herdarem das classes `EngagementPage`.

Este é um exemplo de uma página de seu aplicativo. Você pode fazer a mesma coisa em todas as páginas do seu aplicativo.

### <a name="c-source-file"></a>Arquivo de código-fonte C#
Modifique o arquivo `.xaml.cs` de paginação:

* Adicione às suas instruções `using`:
  
      using Microsoft.Azure.Engagement;
* Substituir `Page` por `EngagementPage`:

**Sem o Engagement:**

        namespace Example
        {
          public sealed partial class ExamplePage : Page
          {
            [...]
          }
        }

**Com o Engagement:**

        using Microsoft.Azure.Engagement;

        namespace Example
        {
          public sealed partial class ExamplePage : EngagementPage
          {
            [...]
          }
        }

> [!IMPORTANT]
> Se sua página substitui o método `OnNavigatedTo`, certifique-se de chamar `base.OnNavigatedTo(e)`. Caso contrário, a atividade não será registrada (a `EngagementPage` chama `StartActivity` dentro de seu método `OnNavigatedTo`).
> 
> 

### <a name="xaml-file"></a>Arquivo XAML
Modifique o arquivo `.xaml` de paginação:

* Adicione às suas declarações de namespaces:
  
      xmlns:engagement="using:Microsoft.Azure.Engagement"
* Substituir `Page` por `engagement:EngagementPage`:

**Sem o Engagement:**

        <Page>
            <!-- layout -->
            ...
        </Page>

**Com o Engagement:**

        <engagement:EngagementPage
            xmlns:engagement="using:Microsoft.Azure.Engagement">
            <!-- layout -->
            ...
        </engagement:EngagementPage >

### <a name="override-the-default-behaviour"></a>Substituir o comportamento padrão
Por padrão, o nome da classe da página é relatado como o nome da atividade, com e sem extras. Se a classe usar o sufixo "Page", o Engagement o removerá.

Para substituir o comportamento padrão para o nome, adicione este código:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

Para relatar informações extras com a sua atividade, adicione este código:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

Esses métodos são chamados de dentro do método `OnNavigatedTo` da página.

### <a name="alternate-method-call-startactivity-manually"></a>Método alternativo: chame `StartActivity()` manualmente
Se você não pode ou não quer sobrecarregar as classes `Page`, em vez disso, você pode iniciar suas atividades chamando os métodos `EngagementAgent` diretamente.

Recomendamos chamar `StartActivity` dentro de seu método `OnNavigatedTo` da sua Página.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [!IMPORTANT]
> Certifique-se de encerrar a sua sessão corretamente.
> 
> O SDK do Windows Universal chama automaticamente o método `EndActivity` quando o aplicativo é fechado. Portanto, será **ALTAMENTE** recomendável chamar o método `StartActivity` sempre que a atividade do usuário for alterada e **NUNCA** chamar o método `EndActivity`. Esse método notifica o servidor do Engagement de que o usuário atual deixou o aplicativo, o que afetará todos os logs de aplicativo.
> 
> 

## <a name="advanced-reporting"></a>Relatórios avançados
Opcionalmente, convém relatar eventos específicos do aplicativo, erros e trabalhos, para fazer isso, use os outros métodos encontrados na classe `EngagementAgent` . A API do Engagement permite usar todos os recursos avançados do Engagement.

Para obter mais informações, consulte [Como usar a API de marcação avançada do Mobile Engagement em seu aplicativo do Windows Universal](mobile-engagement-windows-store-use-engagement-api.md).

