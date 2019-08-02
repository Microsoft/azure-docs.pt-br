---
title: Início rápido com o Azure Application Insights | Microsoft Docs
description: Fornece instruções para configurar rapidamente um aplicativo móvel para monitoramento com o Application Insights e o App Center
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.service: application-insights
ms.reviewer: daviste
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: b8ce08c662e1615b62160e0c681a2fe8dbcb2be1
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447034"
---
# <a name="start-analyzing-your-mobile-app-with-app-center-and-application-insights"></a>Começar a analisar seu aplicativo móvel com o App Center e o Application Insights

Este guia de início rápido percorre as etapas para conectar a instância do App Center do seu aplicativo ao Application Insights. Com o Application Insights, você pode consultar, segmentar, filtrar e analisar a telemetria com ferramentas mais avançadas que estão disponíveis no serviço [Análise](https://docs.microsoft.com/mobile-center/analytics/) do App Center.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa de:

- Uma assinatura do Azure.
- Um aplicativo iOS, Android, Xamarin, Universal do Windows ou React Native.
 
Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="onboard-to-app-center"></a>Integração ao App Center

Antes de usar o Application Insights com seu aplicativo móvel, você precisa integrar o aplicativo ao [App Center](https://docs.microsoft.com/mobile-center/). O Application Insights não recebe telemetria diretamente do aplicativo móvel. Em vez disso, o aplicativo envia telemetria de evento personalizado para o App Center. Em seguida, o App Center exporta cópias desses eventos personalizados continuamente para o Application Insights conforme eles vão sendo recebidos.

Para integrar seu aplicativo, siga o guia de início rápido do App Center para cada plataforma com suporte pelo seu aplicativo. Crie instâncias do App Center separadas para cada plataforma:

* [iOS](https://docs.microsoft.com/mobile-center/sdk/getting-started/ios).
* [Android](https://docs.microsoft.com/mobile-center/sdk/getting-started/android).
* [Xamarin](https://docs.microsoft.com/mobile-center/sdk/getting-started/xamarin).
* [Universal do Windows](https://docs.microsoft.com/mobile-center/sdk/getting-started/uwp).
* [React Native](https://docs.microsoft.com/mobile-center/sdk/getting-started/react-native).

## <a name="track-events-in-your-app"></a>Rastrear eventos no aplicativo

Depois que o aplicativo estiver integrado ao App Center, ele precisará ser modificado para enviar telemetria de evento personalizado usando o SDK do App Center. Eventos personalizados são o único tipo de telemetria do App Center que é exportado para o Application Insights.

Para enviar eventos personalizados de aplicativos iOS, use os métodos `trackEvent` ou `trackEvent:withProperties` no SDK do App Center. [Saiba mais sobre o rastreamento de eventos de aplicativos do iOS.](https://docs.microsoft.com/mobile-center/sdk/analytics/ios)

```Swift
MSAnalytics.trackEvent("Video clicked")
```

Para enviar eventos personalizados de aplicativos Android, use os métodos `trackEvent` no SDK do App Center. [Saiba mais sobre o rastreamento de eventos de aplicativos do Android.](https://docs.microsoft.com/mobile-center/sdk/analytics/android)

```Java
Analytics.trackEvent("Video clicked")
```

Para enviar eventos personalizados de outras plataformas de aplicativos, use os métodos `trackEvent` no SDK do App Center.

Para verificar se os eventos personalizados estão sendo recebidos, vá para a guia **Eventos** na seção **Análise** do App Center. Pode levar alguns minutos para que os eventos apareçam quando são enviados do aplicativo.

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Quando o aplicativo estiver enviando eventos personalizados e eles forem recebidos pelo App Center, você precisará criar um recurso do Application Insights do tipo App Center no portal do Azure:

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Criar um recurso** > **Ferramentas de desenvolvedor** > **Application Insights**.

    > [!NOTE]
    > Se esta for a primeira vez criando um recurso do Application Insights, você pode aprender mais no documento [Criar um recurso do Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

    Será exibida uma caixa de configuração. Use a tabela a seguir para preencher os campos de entrada.

    | Configurações        |  Valor           | DESCRIÇÃO  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Um valor globalmente exclusivo, como "myApp iOS" | Nome que identifica o aplicativo que você está monitorando |
     | **Grupo de recursos**     | Um novo grupo de recursos ou um existente no menu | O grupo de recursos no qual criar o novo recurso do Application Insights |
   | **Localidade** | Um local no menu | Escolher uma localização perto de você ou perto onde seu aplicativo está hospedado |

3. Clique em **Criar**.

Se seu aplicativo dá suporte a várias plataformas (iOS, Android, etc.), é melhor criar recursos do Application Insights separados, um para cada plataforma.

## <a name="export-to-application-insights"></a>Exportar para o Application Insights

Em seu novo recurso do Application Insights na página **Visão geral**. Copie a chave de instrumentação do seu recurso.

Na instância do [App Center](https://appcenter.ms/) para o aplicativo:

1. Na página **Configurações**, clique em **Exportar**.
2. Escolha **Nova Exportação**, escolha **Application Insights** e clique em **Personalizar**.
3. Cole a chave de instrumentação do Application Insights na caixa.
4. Autorize o aumento do uso da assinatura do Azure que contém o recurso do Application Insights. Cada recurso do Application Insights é gratuito para o primeiro 1 GB de dados recebidos por mês. [Saiba mais sobre o preço do Application Insights](https://azure.microsoft.com/pricing/details/application-insights/)

Lembre-se de repetir esse processo para cada plataforma com suporte pelo aplicativo.

Quando a [Exportação](https://docs.microsoft.com/mobile-center/analytics/export) estiver definida, cada evento personalizado recebido pelo App Center será copiado no Application Insights. Pode levar vários minutos para que os eventos cheguem ao Application Insights e, portanto, se eles não aparecem imediatamente, aguarde um pouco antes de fazer mais diagnósticos.

Para oferecer mais dados quando você se conecta pela primeira vez, as 48 horas de eventos personalizados mais recentes no App Center são exportadas para o Application Insights automaticamente.

## <a name="start-monitoring-your-app"></a>Iniciar o monitoramento do aplicativo

O Application Insights pode consultar, segmentar, filtrar e analisar a telemetria de evento personalizado de seus aplicativos além das ferramentas de análise que o App Center oferece.

1. **Consulte a telemetria de evento personalizado.** Na página **Visão geral** do Application Insights, escolha **Logs (Análise)** .

   O portal de logs (análise) do Application Insights associado ao recurso do Application Insights será aberto. O portal Logs (Análise) permite consultar diretamente os dados usando a linguagem de consulta do de Análise de logs, para que você possa fazer perguntas arbitrariamente complexas sobre seu aplicativo e seus usuários.
   
   Abra uma nova guia no portal Logs (Análise) e cole na consulta a seguir. Isso retorna uma contagem de usuários diferentes que enviaram cada evento personalizado do aplicativo nas últimas 24 horas, classificados por essas contagens distintas.

   ```AIQL
   customEvents
   | where timestamp >= ago(24h)
   | summarize dcount(user_Id) by name 
   | order by dcount_user_Id desc 
   ```

   ![Portal de Logs (análise)](./media/mobile-center-quickstart/analytics-portal-001.png)

   1. Selecione a consulta clicando em qualquer lugar na consulta no editor de texto.
   2. Em seguida, clique em **Ir** para executar a consulta. 

   Saiba mais sobre a [Análise do Application Insights](../../azure-monitor/app/analytics.md) e a [linguagem de consulta do Log Analytics](https://aka.ms/LogAnalyticsLanguageReference).


2. **Segmentar e filtrar sua telemetria de evento personalizado.** Na página **Visão geral** do Application Insights, escolha **Usuários** no sumário.

   ![Ícone da ferramenta Usuários](./media/mobile-center-quickstart/users-icon-001.png)

   A ferramenta Usuários mostra quantos usuários do seu aplicativo clicaram em determinados botões, visitaram determinadas telas ou executaram outra ação que você esteja rastreando como um evento com o SDK do App Center. Se você tiver procurando uma maneira de segmentar e filtrar seus eventos do App Center, a ferramenta Usuários é uma ótima opção.

   ![Ferramenta Usuários](./media/mobile-center-quickstart/users-001.png) 

   Por exemplo, segmente seu uso por área geográfica escolhendo **País ou região** no menu suspenso **Dividido por**.

3. **Analise padrões de conversão, retenção e navegação no aplicativo.** Na página **Visão geral** do Application Insights, escolha **Fluxos de Usuário** no sumário.

   ![Ferramenta Fluxos de usuário](./media/mobile-center-quickstart/user-flows-001.png)

   A ferramenta Fluxos de usuário visualiza os eventos que os usuários enviaram depois de algum evento inicial. É útil para obter uma visão geral de como os usuários navegam no aplicativo. Ele também pode revelar pontos em que muitos usuários estão cancelando seu aplicativo ou repetindo as mesmas ações sem parar.

   Além de Fluxos de Usuário, o Application Insights tem várias outras ferramentas de análise de comportamento para responder a perguntas específicas:

   * **Funis** para analisar e monitorar as taxas de conversão.
   * **Retenção** para analisar como seu aplicativo retém os usuários ao longo do tempo.
   * **Pastas de trabalho** para combinar visualizações e texto em um relatório que pode ser compartilhado.
   * **Colaboradores** para nomear e salvar grupos de usuários ou eventos específicos para que possam ser facilmente referenciados por outras ferramentas de análise.

## <a name="clean-up-resources"></a>Limpar recursos

Se você não quiser continuar usando o Application Insights com o App Center, desligue a exportação no App Center e exclua o recurso Application Insights. Isso impedirá outras cobranças pelo Application Insights para esse recurso.

Para desativar a exportação no App Center:

1. No App Center, vá para **Configurações** e escolha **Exportar**.
2. Clique na exportação do Application Insights que você deseja excluir, clique em **Excluir exportação** na parte inferior e confirme.

Para excluir o recurso Application Insights:

1. No menu esquerdo do portal do Azure, clique em **Grupos de recursos** e escolha o grupo de recursos no qual o recurso Application Insights foi criado.
2. Abra o recurso Application Insights que deseja excluir. Em seguida, clique em **Excluir** no menu superior do recurso e confirme. Isso excluirá permanentemente a cópia dos dados que foram exportados para o Application Insights.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Entender como os clientes estão usando seu aplicativo](../../azure-monitor/app/usage-overview.md)
