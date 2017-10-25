---
title: "Relatório de local para o SDK do Azure Mobile Engagement para Android"
description: "Descreve como configurar relatórios de local para o SDK do Azure Mobile Engagement para Android"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 6cab5ed1-b767-46ac-9f0b-48a4e249d88c
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 777d5719cce505b55dfb61c91dcac7e713b077a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="location-reporting-for-azure-mobile-engagement-android-sdk"></a>Relatório de local para o SDK do Azure Mobile Engagement para Android
> [!div class="op_single_selector"]
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

Este tópico descreve como fazer relatórios de local para seu aplicativo Android.

## <a name="prerequisites"></a>Pré-requisitos
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="location-reporting"></a>Relatórios de local
Se quiser que locais sejam informados, você precisa adicionar algumas linhas de configuração (entre as marcas `<application>` e `</application>`).

### <a name="lazy-area-location-reporting"></a>Relatórios de local de área lenta
O relatório de localização de área simples permite a indicação do país, da região e da localidade associados aos dispositivos. Esse tipo de relatório de local usa apenas os locais de rede (com base na ID da célula ou WIFI). A área de dispositivo é relatada no máximo uma vez por sessão. O GPS nunca é usado e, portanto, esse tipo de relatório de localização tem pouco impacto sobre a bateria.

As áreas relatadas são usadas para computar as estatísticas geográficas sobre usuários, sessões, eventos e erros. Elas também podem ser usadas como critério nas campanhas do Reach.

Habilite o relatório de localização de área simples usando a configuração mencionada anteriormente neste procedimento:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Também é necessário especificar uma permissão de localização. Este código usa a permissão ``COARSE`` :

    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Se seu aplicativo exigir, você poderá usar ``ACCESS_FINE_LOCATION`` em vez disso.

### <a name="real-time-location-reporting"></a>Relatórios de localização em tempo real
O relatório de localização em tempo real permite a indicação da latitude e da longitude associadas aos dispositivos. Por padrão, esse tipo de relatório de local usa apenas os locais de rede, com base na ID da célula ou WIFI. O relatório fica ativo apenas quando o aplicativo é executado em primeiro plano (por exemplo, durante uma sessão).

As localizações em tempo real *NÃO* são usadas para calcular estatísticas. Sua única finalidade é permitir o uso do critério de isolamento geográfico em tempo real \<Reach-Audience-geofencing\> em campanhas de alcance.

Para habilitar o relatório de localização em tempo real, adicione uma linha de código em que você defina a cadeia de conexão do Engagement na atividade de inicializador. O resultado se parece com o seguinte:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

        You also need to specify a location permission. This code uses ``COARSE`` permission:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

        If your app requires it, you can use ``ACCESS_FINE_LOCATION`` instead.

#### <a name="gps-based-reporting"></a>Relatórios com base em GPS
Por padrão, os relatórios de local em tempo real usam apenas locais com base em rede. Para habilitar o uso do GPS com base em locais (que são muito mais precisos), use o objeto de configuração:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Você também precisa adicionar a permissão a seguir, se estiver ausente:

    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Relatório de segundo plano
Por padrão, os relatórios de local em tempo real ficam ativos apenas quando o aplicativo é executado em primeiro plano (por exemplo, durante uma sessão). Para habilitar o relatório também em segundo plano, use este objeto de configuração:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [!NOTE]
> Quando o aplicativo é executado em segundo plano, somente locais baseados em rede são relatados, mesmo se você tiver habilitado o GPS.
> 
> 

Se o usuário reiniciar seu dispositivo, o relatório de localização do plano de fundo será interrompido. Para fazê-lo reiniciar automaticamente no momento da inicialização, adicione este código.

    <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
           android:exported="false">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
        </intent-filter>
    </receiver>

Você também precisa adicionar a permissão a seguir, se estiver ausente:

    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

## <a name="android-m-permissions"></a>Permissões do Android M
A partir do Android M, algumas permissões são gerenciadas em tempo de execução e precisam de aprovação do usuário.

Se você selecionar o nível 23 da API do Android, as permissões de tempo de execução serão desativadas por padrão para novas instalações de aplicativo. Caso contrário, elas serão ativadas por padrão.

Você pode habilitar/desabilitar essas permissões no menu de configurações do dispositivo. A desativação de permissões no menu de sistema interrompe os processos em segundo plano do aplicativo, que é um comportamento de sistema e não tem nenhum impacto na capacidade de receber push em segundo plano.

No contexto de relatório de localização do Mobile Engagement, as permissões que exigem aprovação em tempo de execução são:

* `ACCESS_COARSE_LOCATION`
* `ACCESS_FINE_LOCATION`

Solicite permissões do usuário usando uma caixa de diálogo padrão do sistema. Se o usuário aprovar, informe ``EngagementAgent`` para levar essa alteração em consideração em tempo real. Caso contrário, a alteração será processada na próxima vez que o usuário iniciar o aplicativo.

Aqui está um exemplo de código para usar em uma atividade do seu aplicativo para solicitar permissões e encaminhar o resultado se for positivo para ``EngagementAgent``:

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this doesn't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }
