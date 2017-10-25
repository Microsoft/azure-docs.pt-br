---
title: "Integração do SDK do Android do Azure Mobile Engagement"
description: "Atualizações e procedimentos mais recentes para o SDK do Android do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 9ec3fab3-35ec-458e-bf41-6cdd69e3fa44
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 06/27/2016
ms.author: piyushjo
ms.openlocfilehash: 26ba47b19f3a503693d60d344ad39b9eba74fe99
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-integrate-engagement-reach-on-android"></a>Como integrar o Engagement Reach ao Android
> [!IMPORTANT]
> Você deve seguir o procedimento de integração descrito no documento Como Integrar o Engagement, antes de seguir este guia.
> 
> 

## <a name="standard-integration"></a>Integração padrão

Copie os arquivos de recursos do Reach por meio do SDK para seu projeto:

* Copie os arquivos da pasta `res/layout` fornecida com o SDK para a pasta `res/layout` do seu aplicativo.
* Copie os arquivos da pasta `res/drawable` fornecida com o SDK para a pasta `res/drawable` do seu aplicativo.

Edite seu arquivo `AndroidManifest.xml`:

* Adicione a seção a seguir (entre as marcas `<application>` e `</application>`):
  
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
              <category android:name="android.intent.category.DEFAULT" />
              <data android:mimeType="text/plain" />
            </intent-filter>
          </activity>
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
              <category android:name="android.intent.category.DEFAULT" />
              <data android:mimeType="text/html" />
            </intent-filter>
          </activity>
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
              <category android:name="android.intent.category.DEFAULT" />
            </intent-filter>
          </activity>
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
              <category android:name="android.intent.category.DEFAULT"/>
            </intent-filter>
          </activity>
          <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
            <intent-filter>
              <action android:name="android.intent.action.BOOT_COMPLETED"/>
              <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
              <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
            </intent-filter>
          </receiver>
          <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
            <intent-filter>
              <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
            </intent-filter>
          </receiver>
* Você precisa dessa permissão para exibir novamente as notificações de sistema que não foram clicadas na inicialização (caso contrário, elas serão mantidas em disco mas não serão mais exibidas; você realmente precisa incluir isso).
  
          <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
* Especifique um ícone usado para notificações (de aplicativo e de sistema), copiando e editando a seção a seguir (entre as marcas `<application>` e `</application>`):
  
          <meta-data android:name="engagement:reach:notification:icon" android:value="<name_of_icon_WITHOUT_file_extension_and_WITHOUT_'@drawable/'>" />

> [!IMPORTANT]
> Esta seção é **obrigatória** se você planeja usar notificações de sistema durante a criação de campanhas de Alcance. O Android impede que as notificações de sistema sem ícones sejam exibidas. Portanto, se você omitir esta seção, os usuários finais não poderão recebê-las.
> 
> 

* Se você criar campanhas com notificações de sistema usando a visão global, você precisa adicionar as seguintes permissões (após a marca `</application>` ) se estiverem faltando:
  
          <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
          <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
  
  * No Android M e se seu aplicativo direciona o nível de API de 23 do Android ou maior, a permissão do ``WRITE_EXTERNAL_STORAGE`` requer aprovação do usuário. Leia [esta seção](mobile-engagement-android-integrate-engagement.md#android-m-permissions).
* Para notificações de sistema, você também pode especificar na campanha do Reach se o dispositivo deve tocar e/ou vibrar. Para que isso funcione, você deve certificar-se de ter declarado a permissão a seguir (após a marca `</application>` ):
  
          <uses-permission android:name="android.permission.VIBRATE" />
  
  Sem essa permissão, o Android impede que as notificações de sistema seja mostradas se você marcou a opção para tocar ou vibrar Gerenciador de Campanha do Reach.

## <a name="native-push"></a>Push nativo
Agora que você configurou o módulo Reach, você precisa fazer a configuração nativa por push para poder receber as campanhas no dispositivo.

Oferecemos suporte a dois serviços no Android:

* Dispositivos Google Play: Use o [Google Cloud Messaging] seguindo a guia [Como integrar o GCM com a guia do Engagement](mobile-engagement-android-gcm-integrate.md).
* Dispositivos Amazon: Use o [Amazon Device Messaging] seguindo a guia [Como integrar o ADM com a guia do Engagement](mobile-engagement-android-adm-integrate.md).

Se você deseja ter como alvo tanto dispositivos Amazon quanto Google Play, é possível ter tudo dentro de 1 AndroidManifest.xml/APK (Android Application Package) para desenvolvimento. Mas, ao enviar para a Amazon, eles podem rejeitar seu aplicativo se encontrarem código do GCM (Google Cloud Messaging).

Nesse caso, você deve usar múltiplos APKs.

**Seu aplicativo agora está pronto para receber e exibir as campanhas de alcance!**

## <a name="how-to-handle-data-push"></a>Como lidar com o envio de dados
### <a name="integration"></a>Integração
Se desejar que seu aplicativo seja capaz de receber push de dados d Reach, você precisa criar uma subclasse de `com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver` e fazer referência a ela no arquivo `AndroidManifest.xml` (entre as marcas `<application>` e/ou `</application>`):

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

Em seguida, você pode substituir os retornos de chamada `onDataPushStringReceived` e `onDataPushBase64Received`. Aqui está um exemplo:

            public class MyDataPushReceiver extends EngagementReachDataPushReceiver
            {
              @Override
              protected Boolean onDataPushStringReceived(Context context, String category, String body)
              {
                Log.d("tmp", "String data push message received: " + body);
                return true;
              }

              @Override
              protected Boolean onDataPushBase64Received(Context context, String category, byte[] decodedBody, String encodedBody)
              {
                Log.d("tmp", "Base64 data push message received: " + encodedBody);
                // Do something useful with decodedBody like updating an image view
                return true;
              }
            }

### <a name="category"></a>Categoria
O parâmetro de categoria é opcional quando você criar uma campanha de envio de dados e permitir que você filtre o envio de dados. Isso é útil se você tiver vários receptores de difusão lidando com tipos diferentes de push de dados, ou se você deseja enviar por push diferentes tipos de dados `Base64` e deseja identificar o tipo dos mesmos antes de analisá-los.

### <a name="callbacks-return-parameter"></a>Parâmetro de retorno dos retornos de chamada
Aqui estão algumas diretrizes para lidar corretamente com o parâmetro de retorno de `onDataPushStringReceived` e `onDataPushBase64Received`:

* Um receptor de difusão deve retornar `null` no retorno de chamada se ele não souber como lidar com um push de dados. Você deve usar a categoria para determinar se seu receptor de difusão deve lidar com o push de dados ou não.
* Um receptor de difusão deve retornar `true` no retorno de chamada se ele aceita o push de dados.
* Um receptor de difusão deve retornar `false` no retorno de chamada se reconhece o push de dados, mas descarta por qualquer motivo. Por exemplo, retornar `false` quando os dados recebidos são inválidos.
* Se um receptor de difusão retorna `true` enquanto outro retorna `false` para o mesmo push de dados, o comportamento será indefinido; portanto, você nunca deve fazer isso.

O tipo de retorno é usado apenas para as estatísticas do Reach:

* `Replied` será incrementado se um dos receptores de difusão tiver retornado um `true` ou `false`.
* `Actioned` será incrementado apenas se um dos receptores de difusão tiver retornado `true`.

## <a name="how-to-customize-campaigns"></a>Como personalizar campanhas
Para personalizar campanhas, você pode modificar os layouts fornecidos no SDK do Reach.

Você deve manter todos os identificadores usados nos layouts e manter os tipos de modos de exibição que usam um identificador, especialmente para modos de exibição de texto e modos de exibição de imagem. Alguns modos de exibição são usados apenas para ocultar ou exibir determinadas áreas, para que seu tipo possa ser alterado. Se você pretende alterar o tipo de uma exibição nos layouts fornecidos, verifique o código-fonte.

### <a name="notifications"></a>Notificações
Há dois tipos de notificações: as de sistema e aquelas contidas no aplicativo, que usam arquivos de layout diferentes.

#### <a name="system-notifications"></a>Notificações de sistema
Para personalizar as notificações de sistema, você precisa usar as **categorias**. Você pode ir para [Categorias](#categories).

#### <a name="in-app-notifications"></a>Notificações no aplicativo
Por padrão, uma notificação no aplicativo é um modo de exibição que é adicionado dinamicamente à interface do usuário da atividade atual graças ao método Android `addContentView()`. Isso é chamado uma sobreposição de notificação. Sobreposições de notificação são ótimas para uma integração rápida, porque elas não exigem que você modifique nenhum layout em seu aplicativo.

Para modificar a aparência de suas sobreposições de notificação, você pode simplesmente modificar o arquivo `engagement_notification_area.xml` segundo as suas necessidades.

> [!NOTE]
> O arquivo `engagement_notification_overlay.xml` é aquele usado para criar uma sobreposição de notificação, e ele inclui o arquivo `engagement_notification_area.xml`. Você também pode personalizá-lo para adequar-se às suas necessidades (como para posicionar a área de notificação dentro da sobreposição).
> 
> 

##### <a name="include-notification-layout-as-part-of-an-activity-layout"></a>Incluir um layout de notificação como parte de um layout de atividade
As sobreposições são ótimas para uma integração rápida, mas podem ser inconvenientes ou ter efeitos colaterais em casos especiais. O sistema de sobreposição pode ser personalizado em nível de atividade, tornando fácil evitar efeitos colaterais para atividades especiais.

Você pode optar por incluir o layout de notificação ao seu layout existente graças à instrução **include** do Android. A seguir, temos um exemplo de um layout `ListActivity` modificado que contém apenas um `ListView`.

**Antes da integração do Engagement :**

            <?xml version="1.0" encoding="utf-8"?>
            <ListView
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@android:id/list"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent" />

**Após a integração de Engagement :**

            <?xml version="1.0" encoding="utf-8"?>
            <LinearLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <ListView
                android:id="@android:id/list"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:layout_weight="1" />

              <include layout="@layout/engagement_notification_area" />

            </LinearLayout>

Neste exemplo, adicionamos um contêiner pai porque o layout original usou um modo de exibição de lista como o elemento de nível mais alto. Também adicionamos `android:layout_weight="1"` para podermos adicionar um modo de exibição embaixo de um modo de exibição de lista configurado com `android:layout_height="fill_parent"`.

O SDK do Engagement Reach detecta automaticamente que o layout de notificação está incluído nesta atividade e não adicionará uma sobreposição para tal atividade.

> [!TIP]
> Se você usar um ListActivity em seu aplicativo, uma sobreposição de Reach visível impedirá que você continue a reagir a itens clicados na exibição de lista. Esse é um problema conhecido. Para contornar esse problema, sugerimos que você incorpore o layout de notificação em seu próprio layout de atividade de lista, como no exemplo anterior.
> 
> 

##### <a name="disabling-application-notification-per-activity"></a>Desabilitando a notificação de aplicativo por atividade
Se você não desejar que a sobreposição seja adicionada à sua atividade e se não incluir o layout de notificação em seu próprio layout, você pode desabilitar a sobreposição para essa atividade no `AndroidManifest.xml` pela adição de uma seção `meta-data`, como no exemplo a seguir:

            <activity android:name="SplashScreenActivity">
              <meta-data android:name="engagement:notification:overlay" android:value="false"/>
            </activity>

#### <a name="categories"></a> Categorias
Quando você modifica os layouts fornecidos, pode modificar também a aparência de todas as notificações. As categorias permitem que você defina várias aparências direcionadas (possíveis comportamentos) para as notificações. Uma categoria pode ser especificada quando você cria uma campanha de Reach. Tenha em mente que categorias também permitem personalizar anúncios e pesquisas, como está descrito mais adiante neste documento.

Para registrar um manipulador de categorias para suas notificações, você precisa adicionar uma chamada quando o aplicativo é inicializado.

> [!IMPORTANT]
> Leia o aviso sobre o atributo android: process \<android-sdk-engagement-process\> no tópico Como integrar contratos Android antes de continuar.
> 
> 

O exemplo a seguir pressupõe que você confirmou o recebimento do aviso anterior e utilizará uma subclasse de `EngagementApplication`:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), "myCategory");
              }
            }

O objeto `MyNotifier` é a implementação do manipulador de categorias de notificação. Trata-se de uma implementação da interface `EngagementNotifier` ou então de uma subclasse da implementação padrão: `EngagementDefaultNotifier`.

Observe que o mesmo notificador pode manipular várias categorias e você pode registrá-las assim:

            reachAgent.registerNotifier(new MyNotifier(this), "myCategory", "myAnotherCategory");

Para substituir a implementação padrão de categoria, você pode registrar sua implementação como no exemplo a seguir:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), Intent.CATEGORY_DEFAULT); // "android.intent.category.DEFAULT"
              }
            }

A categoria atual usada em um manipulador é passada como um parâmetro na maioria dos métodos que você pode substituir em `EngagementDefaultNotifier`.

Ela é passada como um parâmetro `String` ou indiretamente em um objeto `EngagementReachContent` que tem um método `getCategory()`.

Você pode alterar a maior parte do processo de criação de notificação redefinindo métodos em `EngagementDefaultNotifier`. Para uma personalização mais avançada, fique à vontade para dar uma olhada na documentação técnica e no código-fonte.

##### <a name="in-app-notifications"></a>Notificações no aplicativo
Se você quiser apenas usar layouts alternativos para uma categoria específica, você pode implementar isso como no exemplo a seguir:

            public class MyNotifier extends EngagementDefaultNotifier
            {
              public MyNotifier(Context context)
              {
                super(context);
              }

              @Override
              protected int getOverlayLayoutId(String category)
              {
                return R.layout.my_notification_overlay;
              }


              @Override
              public Integer getOverlayViewId(String category)
              {
                return R.id.my_notification_overlay;
              }

              @Override
              public Integer getInAppAreaId(String category)
              {
                return R.id.my_notification_area;
              }
            }

**Exemplo de `my_notification_overlay.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <RelativeLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/my_notification_overlay"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <include layout="@layout/my_notification_area" />

            </RelativeLayout>

Como você pode ver, o identificador de exibição de sobreposição é diferente do padrão. É importante que cada layout use um identificador exclusivo para sobreposições.

**Exemplo de `my_notification_area.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <merge
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <RelativeLayout
                android:id="@+id/my_notification_area"
                android:layout_width="fill_parent"
                android:layout_height="64dp"
                android:layout_alignParentTop="true"
                android:background="#B000">

                <LinearLayout
                  android:orientation="horizontal"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent"
                  android:gravity="center_vertical">

                  <ImageView
                    android:id="@+id/engagement_notification_icon"
                    android:layout_width="48dp"
                    android:layout_height="48dp" />

                  <LinearLayout
                    android:id="@+id/engagement_notification_text"
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="fill_parent"
                    android:layout_weight="1"
                    android:gravity="center_vertical">

                    <TextView
                      android:id="@+id/engagement_notification_title"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:singleLine="true"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Medium" />

                    <TextView
                      android:id="@+id/engagement_notification_message"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:maxLines="2"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Small" />

                  </LinearLayout>

                  <ImageView
                    android:id="@+id/engagement_notification_image"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:adjustViewBounds="true" />

                  <ImageButton
                    android:id="@+id/engagement_notification_close_area"
                    android:visibility="invisible"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:src="@android:drawable/btn_dialog"
                    android:background="#0F00" />

                </LinearLayout>

                <ImageButton
                  android:id="@+id/engagement_notification_close"
                  android:layout_width="wrap_content"
                  android:layout_height="fill_parent"
                  android:layout_alignParentRight="true"
                  android:src="@android:drawable/btn_dialog"
                  android:background="#0F00" />

              </RelativeLayout>

            </merge>

Como você pode ver, o identificador de exibição da área de notificação é diferente do padrão. É importante que cada layout use um identificador exclusivo para áreas de notificação.

Esse exemplo simples de categoria faz com que as notificações de aplicativos (ou contidas em aplicativos) sejam exibidas na parte superior da tela. Não alteramos os identificadores padrão usados na área de notificação em si.

Se desejar alterá-los, você precisará redefinir o método `EngagementDefaultNotifier.prepareInAppArea` . Se quiser esse nível de personalização avançada, recomenda-se examinar a documentação técnica e o código-fonte de `EngagementNotifier` e `EngagementDefaultNotifier`.

##### <a name="system-notifications"></a>Notificações de sistema
Estendendo `EngagementDefaultNotifier`, você pode substituir `onNotificationPrepared` para alterar a notificação que foi preparada pela implementação padrão.

Por exemplo:

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content)
              throws RuntimeException
            {
              if ("ongoing".equals(content.getCategory()))
                notification.flags |= Notification.FLAG_ONGOING_EVENT;
              return true;
            }

Este exemplo cria um sistema de notificação para um conteúdo sendo exibido como um evento em andamento quando a categoria "em andamento" é usada.

Se deseja compilar o objeto `Notification` do zero, você pode retornar `false` para o método e chamar `notify` você mesmo no `NotificationManager`. Nesse caso, é importante que você mantenha um `contentIntent`, um `deleteIntent` e o identificador de notificação usado por `EngagementReachReceiver`.

Aqui está um exemplo correto de tal implementação:

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content) throws RuntimeException
            {
              /* Required fields */
              NotificationCompat.Builder builder = new NotificationCompat.Builder(mContext)
                .setSmallIcon(notification.icon)              // icon is mandatory
                .setContentIntent(notification.contentIntent) // keep content intent
                .setDeleteIntent(notification.deleteIntent);  // keep delete intent

              /* Your customization */
              // builder.set...

              /* Dismiss option can be managed only after build */
              Notification myNotification = builder.build();
              if (!content.isNotificationCloseable())
                myNotification.flags |= Notification.FLAG_NO_CLEAR;

              /* Notify here instead of super class */
              NotificationManager manager = (NotificationManager) mContext.getSystemService(Context.NOTIFICATION_SERVICE);
              manager.notify(getNotificationId(content), myNotification); // notice the call to get the right identifier

              /* Return false, we notify ourselves */
              return false;
            }

##### <a name="notification-only-announcements"></a>Anúncios exclusivamente de notificação
O gerenciamento do clique em um comunicado exclusivamente de notificação só pode ser personalizado substituindo-se `EngagementDefaultNotifier.onNotifAnnouncementIntentPrepared` para modificar o preparado `Intent`. Usar esse método permite ajustar os sinalizadores facilmente.

Por exemplo, para adicionar o sinalizador `SINGLE_TOP` :

            @Override
            protected Intent onNotifAnnouncementIntentPrepared(EngagementNotifAnnouncement notifAnnouncement,
              Intent intent)
            {
              intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
              return intent;
            }

Para usuários herdados do Engagement, observe que as notificações do sistema sem a ação do URL agora inicia o aplicativo se ele foi em segundo plano, para que esse método pode ser chamado com um comunicado sem a URL da ação. Você deve considerar isso ao personalizar a intenção.

Você também pode implementar o `EngagementNotifier.executeNotifAnnouncementAction` do zero.

##### <a name="notification-life-cycle"></a>Ciclo de vida de notificação
Ao usar a categoria padrão, alguns métodos de ciclo de vida são chamados no objeto `EngagementReachInteractiveContent` para relatar estatísticas e atualizar o estado de campanha:

* Quando a notificação é exibida no aplicativo ou colocada na barra de status, o método `displayNotification` (o qual reporta estatísticas) será chamado por `EngagementReachAgent` se `handleNotification` retornar `true`.
* Se a notificação é liberada, o método `exitNotification` é chamado, a estatística é relatada e as próximas campanhas agora podem ser processadas.
* Se a notificação é clicada, `actionNotification` é chamado, a estatística é relatada e a intenção associada é iniciada.

Se sua implementação de `EngagementNotifier` ignora o comportamento padrão, você precisa chamar esses métodos de ciclo de vida por conta própria. Os exemplos a seguir ilustram alguns casos em que o comportamento padrão é ignorado:

* Você não precisa estender `EngagementDefaultNotifier`, por exemplo, você implementou o tratamento de categoria a partir do zero.
* Para notificações do sistema, você substituiu o `onNotificationPrepared` e modificou `contentIntent` ou `deleteIntent` no objeto `Notification`.
* Para notificações no aplicativo, você substituiu `prepareInAppArea`, certifique-se de mapear pelo menos `actionNotification` para um de seus controles de IU.

> [!NOTE]
> Se `handleNotification` lança uma exceção, o conteúdo é excluído e `dropContent` é chamado. Isso é informado nas estatísticas e as próximas campanhas agora podem ser processadas.
> 
> 

### <a name="announcements-and-polls"></a>Anúncios e pesquisas
#### <a name="layouts"></a>Layouts
Você pode modificar os arquivos `engagement_text_announcement.xml`, `engagement_web_announcement.xml` e `engagement_poll.xml` para personalizar os anúncios de texto, anúncios da Web e pesquisas.

Esses arquivos compartilham dois layouts comuns para a área de título e a área do botão. O layout para o título é `engagement_content_title.xml` e usa o arquivo epônimo emitível para a tela de fundo. O layout dos botões de ação e de saída é `engagement_button_bar.xml` e usa o arquivo epônimo emitível para a tela de fundo.

Em uma pesquisa, o layout de pergunta e suas opções são dinamicamente infladas usando várias vezes o arquivo de layout `engagement_question.xml` para as perguntas e o arquivo `engagement_choice.xml` para as opções.

#### <a name="categories"></a>Categorias
##### <a name="alternate-layouts"></a>Layouts alternativos
Como as notificações, a categoria de campanha pode ser usada com layouts alternativos para seus anúncios e pesquisas.

Por exemplo, para criar uma categoria para um comunicado de texto, você pode estender `EngagementTextAnnouncementActivity` e fazer referência a ela no arquivo `AndroidManifest.xml`:

            <activity android:name="com.your_company.MyCustomTextAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>

Observe que a categoria no filtro de intenção é usada para fazer a diferença com a atividade de comunicado padrão.

O SDK do Reach usa o sistema de intenção para resolver a atividade correta para uma categoria específica, e ele retorna para a categoria padrão se a resolução tiver falhado.

Em seguida, você precisa implementar `MyCustomTextAnnouncementActivity`. Se quiser apenas alterar o layout (mas manter os mesmos identificadores de exibição), você só precisa definir a classe como no exemplo a seguir:

            public class MyCustomTextAnnouncementActivity extends EngagementTextAnnouncementActivity
            {
              @Override
              protected String getLayoutName()
              {
                return "my_text_announcement";  // tell super class to use R.layout.my_text_announcement
              }
            }

Para substituir a categoria padrão de anúncios de texto, basta substituir `android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"` pela sua implementação.

Pesquisas e anúncios da Web podem ser personalizadas de modo semelhante.

Para anúncios da Web, você pode estender `EngagementWebAnnouncementActivity` e declarar sua atividade no `AndroidManifest.xml`, como no exemplo a seguir:

            <activity android:name="com.your_company.MyCustomWebAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/html" />    <!-- only difference with text announcements in the intent is the data mime type -->
              </intent-filter>
            </activity>

Para pesquisas, você pode estender `EngagementPollActivity` e declarar sua atividade no `AndroidManifest.xml`, como no exemplo a seguir:

            <activity android:name="com.your_company.MyCustomPollActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="my_category" />
              </intent-filter>
            </activity>

##### <a name="implementation-from-scratch"></a>Implementação do zero
Você pode implementar categorias para suas atividades de comunicado (e pesquisa) sem estender uma das classes `Engagement*Activity` fornecidas pelo SDK do Reach. Isso é útil, por exemplo, se desejar definir um layout que não use as mesmas exibições que os layouts padrão.

Como para personalização da notificação avançada, é recomendável examinar o código-fonte da implementação do padrão.

Aqui estão algumas coisas que devem ser consideradas: o Reach iniciará a atividade com um propósito específico (correspondente ao filtro intencional), além de um parâmetro extra que é o identificador de conteúdo.

Para recuperar o objeto de conteúdo que contém os campos que você especificou ao criar a campanha no site da Web, você pode fazer isso:

            public class MyCustomTextAnnouncement extends EngagementActivity
            {
              private EngagementAnnouncement mContent;

              @Override
              protected void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);

                /* Get content */
                mContent = EngagementReachAgent.getInstance(this).getContent(getIntent());
                if (mContent == null)
                {
                  /* If problem with content, exit */
                  finish();
                  return;
                }

                setContentView(R.layout.my_text_announcement);

                /* Configure views by querying fields on mContent */
                // ...
              }
            }

Para estatísticas, você deve relatar o conteúdo que é exibido no evento `onResume`:

            @Override
            protected void onResume()
            {
             /* Mark the content displayed */
             mContent.displayContent(this);
             super.onResume();
            }

Em seguida, não se esqueça de chamar `actionContent(this)` ou `exitContent(this)` no objeto de conteúdo antes que a atividade entre em segundo plano.

Se você não chamar `actionContent` ou `exitContent`, as estatísticas não serão enviadas (ou seja, não haverá análise da campanha) e, mais importante, as próximas campanhas não serão notificadas até o processo do aplicativo ser reiniciado.

A orientação ou outras alterações de configuração podem mudar o código, tornando difícil determinar se a atividade entra em segundo plano ou não. A implementação padrão garante que o conteúdo seja relatado como encerrado se o usuário sair da atividade (pressionando `HOME` ou `BACK`), mas isso não ocorre se a orientação for alterada.

Aqui está a parte interessante da implementação:

            @Override
            protected void onUserLeaveHint()
            {
              finish();
            }

            @Override
            protected void onPause()
            {
              if (isFinishing() && mContent != null)
              {
                /*
                 * Exit content on exit, this is has no effect if another process method has already been
                 * called so we don't have to check anything here.
                 */
                mContent.exitContent(this);
              }
              super.onPause();
            }

Como você pode ver, se tiver chamado `actionContent(this)` e então terminado a atividade, `exitContent(this)` pode ser chamado com segurança sem ter qualquer efeito.

[here]:http://developer.android.com/tools/extras/support-library.html#Downloading
[Google Cloud Messaging]:http://developer.android.com/guide/google/gcm/index.html
[Amazon Device Messaging]:https://developer.amazon.com/sdk/adm.html
