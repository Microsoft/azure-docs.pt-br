
### <a name="update-manifest-file-to-enable-notifications"></a>Atualizar o arquivo de manifesto para habilitar as notificações
Copie os recursos de mensagens no aplicativo a seguir para o Manifest.xml entre as marcas `<application>` e `</application>`.

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

### <a name="specify-an-icon-for-notifications"></a>Especifique um ícone para notificações
Cole o trecho XML a seguir no Manifest.xml entre as marcas `<application>` e `</application>`.

        <meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

Isso define o ícone que é exibido no sistema e notificações no aplicativo. Embora ele seja opcional para notificações no aplicativo, é obrigatório para notificações do sistema. O Android rejeita as notificações do sistema com ícones inválidos.

Certifique-se de estar usando um ícone que exista em uma das pastas **desenháveis** (como ``engagement_close.png``). **mipmap** .

> [!NOTE]
> Você não deve usar o ícone **iniciador** . Ele tem uma resolução diferente e normalmente está nas pastas de mipmap, para as quais não há suporte.
> 
> 

Para os aplicativos reais, você poderá usar um ícone adequado para notificações conforme as [diretrizes de design do Android](http://developer.android.com/design/patterns/notifications.html).

> [!TIP]
> Para garantir o uso das resoluções de ícone corretas, analise [estes exemplos](https://www.google.com/design/icons).
> Role para baixo até a seção **Notificação**, clique em um ícone e clique em `PNGS` para baixar o conjunto desenhável de ícones. Você pode ver quais pastas sorteáveis usar com qual resolução para cada versão de ícone.
> 
> 

### <a name="enable-your-app-to-receive-gcm-push-notifications"></a>Habilitar seu aplicativo para receber notificações por push do GCM
1. Cole o conteúdo apresentado a seguir em seu Manifest.xml, entre as marcas `<application>` e `</application>` depois de substituir a **ID do Remetente** obtido do console do projeto do Firebase. O \n é intencional, assim, verifique se o número do projeto termina com ele.
   
        <meta-data android:name="engagement:gcm:sender" android:value="************\n" />
2. Cole o código a seguir em seu arquivo Manifest.xml, entre as marcas `<application>` e `</application>`. Substitua o nome do pacote <Your package name>.
   
        <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
        android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
            </intent-filter>
        </receiver>
   
        <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<Your package name>" />
            </intent-filter>
        </receiver>
3. Adicione o último conjunto de permissões destacadas antes da marca `<application>` . Substitua `<Your package name>` pelo nome real do pacote do seu aplicativo.
   
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
        <permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

