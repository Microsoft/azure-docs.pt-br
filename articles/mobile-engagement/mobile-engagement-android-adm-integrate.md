---
title: "Integração do SDK do Android do Azure Mobile Engagement"
description: "Atualizações e procedimentos mais recentes para o SDK do Android do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a7d719ec-67b3-4be3-9d7f-0b61a57fe978
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 43987962ea2b7b825b88643d18b4db65f1f1670e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-integrate-adm-with-engagement"></a>Como integrar ADM ao Engagement
> [!IMPORTANT]
> Você deve seguir o procedimento de integração descrito no documento Como Integrar o Engagement, antes de seguir este guia.
> 
> Este documento será útil apenas se você já tiver integrado o módulo de Alcance e planeja enviar por push os dispositivos Amazon. Para integrar campanhas de Alcance em seu aplicativo, leia primeiro Como Integrar o Engagement Reach no Android.
> 
> 

## <a name="introduction"></a>Introdução
A integração do ADM permite que o seu aplicativo seja enviado por push ao direcionar dispositivos do Amazon Android.

As cargas de ADM enviadas para o SDK sempre contêm a chave `azme` no objeto de dados. Portanto, se você usar o ADM para outra finalidade em seu aplicativo, é possível filtrar os pushes com base nessa chave.

> [!IMPORTANT]
> Somente dispositivos Amazon Kindle executando Android 4.0.3 ou acima têm suporte pelo Amazon Device Messaging; no entanto, você pode integrar esse código com segurança em outros dispositivos.
> 
> 

## <a name="sign-up-to-adm"></a>Inscrever-se para ADM
Se ainda não tiver feito, você deve habilitar o ADM em sua conta da Amazon.

O procedimento é detalhado em: [<https://developer.amazon.com/sdk/adm/credentials.html>].

Depois de concluir o procedimento, você obtém:

* Credenciais OAuth (uma ID de cliente e um segredo do cliente) para o Engagement para poder enviar por push seus dispositivos.
* Uma chave de API que deve ser integrada ao seu aplicativo.

## <a name="sdk-integration"></a>Integração do SDK
### <a name="managing-device-registrations"></a>Gerenciando registros de dispositivo
Cada dispositivo deve enviar um comando de registro aos servidores do ADM, caso contrário eles não poderão ser alcançados.

Se você já usa a [biblioteca de cliente do ADM], e já tiver o [ADM integrado] você pode ir diretamente para o android-sdk-adm-receive.

Se você ainda não tiver o ADM integrado, o Engagement tem uma maneira mais simples para habilitá-lo em seu aplicativo:

Edite seu arquivo `AndroidManifest.xml` :

* Adicione o namespace Amazon, o arquivo deve começar como este:
  
      <?xml version="1.0" encoding="utf-8"?>
      <manifest xmlns:android="http://schemas.android.com/apk/res/android"
                xmlns:amazon="http://schemas.amazon.com/apk/res/android"
* Dentro da marca `<application/>` , adicione essa seção:
  
      <amazon:enable-feature
         android:name="com.amazon.device.messaging"
         android:required="false"/>
  
      <meta-data android:name="engagement:adm:register" android:value="true" />
* Depois de adicionar a marca da amazon, você poderá ter um erro de compilação se o Destino de Compilação do Projeto estiver abaixo da versão 2.1 do Android. É necessário usar um destino de compilação **Android 2.1+** (não se preocupe, você ainda pode ter um `minSdkVersion` definido como 4).
* Integre a chave de API do ADM como um ativo seguindo [este procedimento].

Siga as instruções das próximas seções.

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Comunicar a id de registro para o serviço de envio por push do Engagement e receber notificações
Para comunicar a ID de registro do dispositivo para o serviço de Envio por Push do Engagement e receber notificações, adicione o seguinte ao seu arquivo `AndroidManifest.xml`  dentro da marca `<application/>` (mesmo que você use ADM sem Engagement):

        <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
          android:exported="false">
          <intent-filter>
            <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
          </intent-filter>
        </receiver>

         <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
           android:permission="com.amazon.device.messaging.permission.SEND">
          <intent-filter>
            <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
            <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
            <category android:name="<your_package_name>"/>
          </intent-filter>
        </receiver>   

Certifique-se de ter as seguintes permissões em seu `AndroidManifest.xml` (antes da marca `</application>`).

        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
        <uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
        <permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

## <a name="grant-engagement-oauth-credentials"></a>Conceda as credenciais do Engagement OAuth
Envie suas credenciais OAuth (ID do Cliente e Segredo do Cliente) no Portal do Engagement.

[<https://developer.amazon.com/sdk/adm/credentials.html>]:https://developer.amazon.com/sdk/adm/credentials.html
[biblioteca de cliente do ADM]:https://developer.amazon.com/sdk/adm/setup.html
[ADM integrado]:https://developer.amazon.com/sdk/adm/integrating-app.html
[este procedimento]:https://developer.amazon.com/sdk/adm/integrating-app.html#Asset
