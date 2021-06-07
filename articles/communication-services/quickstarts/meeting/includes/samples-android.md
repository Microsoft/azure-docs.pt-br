---
title: Usando as equipes de serviços de comunicação do Azure incorporar para Android
description: Nesta visão geral, você aprenderá a usar a biblioteca de inserção de equipes dos serviços de comunicação do Azure para Android.
author: palatter
ms.author: palatter
ms.date: 24/02/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 711c738adaaad8aff1e1f56b537b41a846e528db
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803618"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Um recurso dos Serviços de Comunicação implantado. [Crie um recurso dos Serviços de Comunicação](../../create-communication-resource.md).
- Um `User Access Token` para habilitar o cliente de chamada. Para obter mais informações sobre [como obter um `User Access Token`](../../access-tokens.md)
- Conclua o guia de início rápido para [introdução ao acréscimo de equipes inseridas em seu aplicativo](../getting-started-with-teams-embed.md)

## <a name="teams-embed-events"></a>Eventos de inserção de equipes

Adicione o `MeetingEventListener` à sua classe.

```java
public class MainActivity extends AppCompatActivity implements MeetingEventListener {

    private MeetingUIClient meetingUIClient;
```

Defina o `MeetingEventListener` em `this` .

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    createMeetingClient();
}

private void createMeetingClient() {
    try {
        CommunicationTokenCredential credential = new CommunicationTokenCredential(tokenRefresher, true, ACS_TOKEN);
        meetingUIClient = new MeetingUIClient(credential);
        meetingUIClient.setMeetingEventListener(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

Implemente os métodos `onCallStateChanged` e `onRemoteParticipantCountChanged`.

```java
@Override
public void onCallStateChanged(CallState callState) {
    switch(callState) {
        case CONNECTING:
            System.out.println("Call state changed to 'Connecting'");
            break;
        case CONNECTED:
            System.out.println("Call state changed to 'Connected'");
            break;
        case WAITING_IN_LOBBY:
            System.out.println("Call state changed to 'Waiting in Lobby'");
            break;
        case ENDED:
            System.out.println("Call state changed to 'Ended'");
            break;
    }
}

@Override
public void onRemoteParticipantCountChanged(int newCount) {
    System.out.println("Remote participant count changed: " + newCount);
}
```

## <a name="assigning-avatars-for-users"></a>Atribuindo avatars para usuários

Adicione o `MeetingIdentityProvider` à sua classe.

```java
public class MainActivity extends AppCompatActivity implements MeetingIdentityProvider {

    private MeetingUIClient meetingUIClient;
```

Defina o `MeetingIdentityProvider` em `this` .

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    createMeetingClient();
}

private void createMeetingClient() {
    try {
        CommunicationTokenCredential credential = new CommunicationTokenCredential(tokenRefresher, true, ACS_TOKEN);
        meetingUIClient = new MeetingUIClient(credential);
        meetingUIClient.setMeetingIdentityProvider(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

Mapeie cada `userMri` para o Avatar correspondente.

```java
@Override
public void provideAvatarFor(String userIdentifier, MeetingIdentityProviderCallback meetingIdentityProviderCallback) {
    Drawable myAvatar = null;
    try {
        System.out.println("MicrosoftTeamsSDKIdentityProvider.requestForAvatar called for userIdentifier: " + userIdentifier);
        if (userIdentifier.startsWith("8:teamsvisitor:")) {
            // get and provide avatar picture asynchronously with long fetching/decoding delay.
            System.out.println("update avatar for Anonymous user");
            String imageUrl = "https://mlccdn.blob.core.windows.net/dev/LWA/qingy/doughboy_36x36.png";
            updateAvatarFromUrl(imageUrl, meetingIdentityProviderCallback);
        } else if (userIdentifier.startsWith("8:orgid:")) {
            System.out.println("update avatar for OrgID user");
            String imageUrl = "https://mlccdn.blob.core.windows.net/dev/LWA/qingy/qingy_120.jpg";
            updateAvatarFromUrl(imageUrl, meetingIdentityProviderCallback);
        } else if (userIdentifier.startsWith("8:acs:")) {
            System.out.println("update avatar for ACS user");
            String imageUrl = "https://mlccdn.blob.core.windows.net/dev/LWA/qingy/msudan.png";
            updateAvatarFromUrl(imageUrl, meetingIdentityProviderCallback);
        }
    } catch (Exception e) {
        System.out.println("MicrosoftTeamsSDKIdentityProvider: Exception while requestForAvatar for userIdentifier: " + userIdentifier + e.getMessage());
    }
}

/**
    * download and callback to set the Avatar image from web URL.
    * We have a few sample images on CDN
    *                 String imageUrl = "https://mlccdn.blob.core.windows.net/dev/LWA/qingy/doughboy_36x36.png";
    *                 String imageUrl = "https://mlccdn.blob.core.windows.net/dev/LWA/qingy/qingy_120.jpg";
    *                 String imageUrl = "https://mlccdn.blob.core.windows.net/dev/LWA/qingy/msudan.png";
    */
private void updateAvatarFromUrl(String url, MeetingIdentityProviderCallback meetingIdentityProviderCallback) {
    String urlImage = url;
    WeakReference<MeetingIdentityProviderCallback> weakReferenceObserver = new WeakReference<>(meetingIdentityProviderCallback);
    new AsyncTask<String, Integer, Drawable>(){
        @Override
        protected Drawable doInBackground(String... strings) {
            Bitmap bitmap = null;
            try {
                HttpURLConnection connection = (HttpURLConnection) new URL(urlImage).openConnection();
                connection.connect();
                InputStream input = connection.getInputStream();
                bitmap = BitmapFactory.decodeStream(input);
            } catch (IOException e) {
                e.printStackTrace();
            }
            Drawable d = new BitmapDrawable(getResources(), bitmap);
            return d;
        }
        protected void onPostExecute(Drawable myAvatar) {
            // provide avatar when it is available.
            MeetingIdentityProviderCallback callback = weakReferenceObserver.get();
            if (callback != null) {
                System.out.println("invoke the callback method with fetched avatar");
                callback.onAvatarAvailable(myAvatar);
            } else {
                System.out.println("callback observer are reclaimed, there is no need to update avatar in UI anymore");
            }
        }
    }.execute();
}
```
