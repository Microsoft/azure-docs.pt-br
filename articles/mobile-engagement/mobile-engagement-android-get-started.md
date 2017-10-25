---
title: "Introdução ao Azure Mobile Engagement para Aplicativos Android"
description: "Aprenda a usar o Azure Mobile Engagement com análises e notificações por push para aplicativos Android."
services: mobile-engagement
documentationcenter: android
author: piyushjo
manager: erikre
editor: 
ms.assetid: 3c286c6d-cfef-4e3e-9b2c-715429fe82db
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: hero-article
ms.date: 08/10/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: dc255a930bf71e6ef6d964bc5e3472a38ce4e467
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-android-apps"></a>Introdução ao Azure Mobile Engagement para Aplicativos Android
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra como usar o Azure Mobile Engagement para entender o uso do aplicativo e como enviar notificações por push para usuários segmentados de um aplicativo Android.
Esse tutorial demonstra um cenário de transmissão simples usando o Mobile Engagement. Nele, você cria um aplicativo Android em branco que coleta dados básicos e recebe notificações por push usando o GCM (Google Cloud Messaging).

## <a name="prerequisites"></a>Pré-requisitos
A conclusão deste tutorial requer as [Ferramentas para desenvolvedores do Android](https://developer.android.com/sdk/index.html), que incluem o ambiente de desenvolvimento integrado do Android Studio e a plataforma Android mais recente.

Também é necessário baixar o [SDK do Mobile Engagement Android](https://aka.ms/vq9mfn).

> [!IMPORTANT]
> Para concluir este tutorial, você precisa de uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started).
>
>

## <a name="set-up-mobile-engagement-for-your-android-app"></a>Configurar o Mobile Engagement para seu aplicativo Android
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Conectar o aplicativo ao back-end do Mobile Engagement
Este tutorial apresenta uma "integração básica" que é o conjunto mínimo exigido para coletar dados e enviar uma notificação por push. Você cria um aplicativo básico com o Android Studio para demonstrar a integração.

A documentação de integração completa pode ser encontrada na [Integração do SDK do Mobile Engagement Android](mobile-engagement-android-sdk-overview.md).

### <a name="create-an-android-project"></a>Criar um projeto Android
1. Inicie o **Android Studio** e no pop-up, selecione **Iniciar um novo projeto do Android Studio**.

    ![][1]
2. Forneça um nome de aplicativo e um domínio da empresa. Anote o que você está preenchendo, porque precisará disso posteriormente. Clique em **Avançar**.

    ![][2]
3. Selecione o fator forma de destino e o nível de API e clique em **Avançar**.

   > [!NOTE]
   > O Mobile Engagement requer nível mínimo de API de 10 (Android 2.3.3).
   >
   >

    ![][3]
4. Selecione a **Atividade em Branco** aqui, que é a única tela para esse aplicativo e clique em **Próximo**.

    ![][4]
5. Finalmente, mantenha os padrões como estão e clique **Concluir**.

    ![][5]

O Android Studio agora criará o aplicativo de demonstração no qual integraremos o Mobile Engagement.

### <a name="include-the-sdk-library-in-your-project"></a>Inclua a biblioteca de SDK em seu projeto
1. Baixe o [SDK do Mobile Engagement Android](https://aka.ms/vq9mfn).
2. Extraia o file de arquivo para uma pasta no seu computador.
3. Identifique a biblioteca. jar para a versão atual desse SDK e copie-a para a área de transferência.

      ![][6]
4. Navegue para a seção **Projeto** (1) e cole o .jar na pasta de bibliotecas (2).

      ![][7]
5. Para carregar a biblioteca, sincronize o projeto.

      ![][8]

### <a name="connect-your-app-to-mobile-engagement-backend-with-the-connection-string"></a>Conecte seu aplicativo ao back-end do Mobile Engagement com a Cadeia de Conexão
1. Copie as seguintes linhas de código para a criação da atividade (deve ser feito apenas em um local do seu aplicativo, geralmente a atividade principal). Para este aplicativo de exemplo, abra a MainActivity em src -> main -> pasta java e adicione o seguinte:

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
        EngagementAgent.getInstance(this).init(engagementConfiguration);
2. Resolva as referências pressionando Alt + Enter ou adicionando as seguintes instruções de importação:

        import com.microsoft.azure.engagement.EngagementAgent;
        import com.microsoft.azure.engagement.EngagementConfiguration;
3. Volte para o Portal Clássico do Azure na página **Informações da Conexão** do seu aplicativo e copie a **Cadeia de Conexão**.

      ![][9]
4. Cole no parâmetro `setConnectionString`, substituindo a cadeia de caracteres inteira mostrada no código a seguir:

        engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

### <a name="add-permissions-and-a-service-declaration"></a>Adicionar permissões e uma declaração de serviço
1. Adicionar essas permissões ao Manifest.xml do projeto imediatamente anteriores ou posteriores à marca `<application>`:

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
2. Para declarar o serviço do agente, adicione este código entre as marcações `<application>` e `</application>`:

        <service
             android:name="com.microsoft.azure.engagement.service.EngagementService"
             android:exported="false"
             android:label="<Your application name>"
             android:process=":Engagement"/>
3. No código colado, substitua `"<Your application name>"` no rótulo, que é exibido no menu **Configurações** onde você pode ver os serviços em execução no dispositivo. Você pode adicionar a palavra "Serviço" desse rótulo por exemplo.

### <a name="send-a-screen-to-mobile-engagement"></a>Enviar uma tela ao Mobile Engagement
Para iniciar o envio dos dados e assegurar que os usuários estão ativos, você deve enviar pelo menos uma tela (Atividade) para o back-end do Mobile Engagement.

Vá para **MainActivity. Java** e adicione o seguinte para substituir a classe base de **MainActivity** por **EngagementActivity**:

    public class MainActivity extends EngagementActivity {

> [!NOTE]
> Se sua classe base não for *Activity*, consulte [Relatório Android Avançado](mobile-engagement-android-advanced-reporting.md) para saber como herdar de classes diferentes.
>
>

Comente (exclua) a linha a seguir para este cenário de exemplo simples:

    // setSupportActionBar(toolbar);

Se você quiser manter `ActionBar` em seu aplicativo, consulte [Relatório Avançado do Android](mobile-engagement-android-advanced-reporting.md).

## <a name="connect-app-with-real-time-monitoring"></a>Conectar o aplicativo com monitoramento em tempo real
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="enable-push-notifications-and-in-app-messaging"></a>Habilitar notificações por push e mensagens no aplicativo
Durante uma campanha, o Mobile Engagement permite interagir e acessar em REACH seus usuários com notificações por push e mensagens no aplicativo. Esse módulo é chamado de REACH no portal do Mobile Engagement.
A seção a seguir configura seu aplicativo para recebê-las.

### <a name="copy-sdk-resources-in-your-project"></a>Copiar recursos de SDK em seu projeto
1. Navegue de volta até o conteúdo baixado do SDK e copie a pasta **res** .

    ![][10]
2. Volte para o Android Studio, selecione o diretório **principal** dos arquivos de projeto e cole-o para adicionar os recursos ao projeto.

    ![][11]

[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[!INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

## <a name="next-steps"></a>Próximas etapas
Vá para [SDK do Android](mobile-engagement-android-sdk-overview.md) para ter um conhecimento detalhado sobre a integração do SDK.

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[10]: ./media/mobile-engagement-android-get-started/copy-resources.png
[11]: ./media/mobile-engagement-android-get-started/paste-resources.png
