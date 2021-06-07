---
title: Início Rápido – Adicionar o ingresso em uma reunião do Teams um aplicativo Android usando os Serviços de Comunicação do Azure
description: Neste início rápido, você aprenderá a usar a biblioteca de Inserção do Teams dos Serviços de Comunicação do Azure para Android.
author: palatter
ms.author: palatter
ms.date: 01/25/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 5ac4c53550468d33e9ed533303749d29e772d766
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108461"
---
Neste início rápido, você aprenderá a ingressar em uma reunião no Teams usando a biblioteca de Inserção do Teams dos Serviços de Comunicação do Azure para Android.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Android Studio](https://developer.android.com/studio), para criar o seu aplicativo Android.
- Um recurso dos Serviços de Comunicação implantado. [Crie um recurso dos Serviços de Comunicação](../../create-communication-resource.md).
- Um [Token de Acesso do Usuário](../../access-tokens.md) para o seu Serviço de Comunicação do Azure.

## <a name="setting-up"></a>Configurando

### <a name="create-an-android-app-with-an-empty-activity"></a>Criar um aplicativo Android com uma atividade vazia

No Android Studio, selecione Iniciar um novo projeto do Android Studio.

:::image type="content" source="../media/android/studio-new-project.png" alt-text="Captura de tela que mostra o botão 'Iniciar um novo Projeto do Android Studio' selecionado no Android Studio.":::

Selecione o modelo de projeto "Atividade Vazia" em "Telefone e Tablet".

:::image type="content" source="../media/android/studio-blank-activity.png" alt-text="Captura de tela que mostra a opção 'Atividade Vazia' selecionada na Tela do Modelo do Projeto.":::

Dê ao projeto o nome `TeamsEmbedAndroidGettingStarted`, defina a linguagem como Java e selecione o SDK Mínimo "API 21: Android 5.0 (Lollipop)" ou superior.

:::image type="content" source="../media/android/studio-calling-min-api.png" alt-text="Captura de tela que mostra a opção 'Atividade Vazia' selecionada na Tela do Modelo do Projeto 2.":::


### <a name="install-the-azure-package"></a>Instalar o pacote do Azure

No nível do aplicativo build.gradle, adicione as linhas a seguir às dependências e às seções do Android

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
}
```

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-common:1.0.0-beta.6'
    ...
}
```

### <a name="install-the-teams-embed-package"></a>Instalar o pacote de Inserção do Teams

Baixe o pacote `MicrosoftTeamsSDK`.

Em seguida, descompacte a pasta MicrosoftTeamsSDK para a pasta de aplicativo do projeto. Ex.: `TeamsEmbedAndroidGettingStarted/app/MicrosoftTeamsSDK`.

### <a name="add-teams-embed-package-to-your-buildgradle"></a>Adicionar o pacote de Inserção do Teams ao seu build.gradle

No nível do aplicativo `build.gradle`, adicione a seguinte linha ao final do arquivo:

```groovy
apply from: 'MicrosoftTeamsSDK/MicrosoftTeamsSDK.gradle'
```

Sincronize o projeto com os arquivos gradle.

### <a name="create-application-class"></a>Criar classe do aplicativo

Crie um arquivo de classe Java chamado `TeamsEmbedAndroidGettingStarted`. Essa será a classe de aplicativo que deve estender `TeamsSDKApplication`. [Documentação do Android](https://developer.android.com/reference/android/app/Application)

:::image type="content" source="../media/android/application-class-location.png" alt-text="Captura de tela mostrando onde criar a classe do aplicativo no Android Studio":::

```java
package com.microsoft.teamsembedandroidgettingstarted;

import com.microsoft.teamssdk.app.TeamsSDKApplication;

public class TeamsEmbedAndroidGettingStarted extends TeamsSDKApplication {
}
```

### <a name="update-themes"></a>Atualizar temas

Defina o nome do estilo como `AppTheme` em seus arquivos `theme.xml` e `theme.xml (night)`.

:::image type="content" source="../media/android/theme-settings.png" alt-text="Captura de tela mostrando os arquivos theme.xml no Android Studio":::

```xml
<style name="AppTheme" parent="Theme.AppCompat.DayNight.DarkActionBar">
```

```xml
<style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
```

### <a name="add-permissions-to-application-manifest"></a>Adicionar permissões ao manifesto do aplicativo

Adicione a permissão `RECORD_AUDIO` ao Manifesto do Aplicativo (`app/src/main/AndroidManifest.xml`):  


```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.yourcompany.TeamsEmbedAndroidGettingStarted">
    <uses-permission android:name="android.permission.RECORD_AUDIO"/>
    <application
```

### <a name="add-app-name-and-theme-to-application-manifest"></a>Adicionar nome do aplicativo e tema ao manifesto do aplicativo

Adicione `xmlns:tools="http://schemas.android.com/tools" ao manifesto.

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    package="com.yourcompany.TeamsEmbedAndroidGettingStarted">
```

Adicione `.TeamsEmbedAndroidGettingStarted` a `android:name`, `android:name` a `tools:replace` e altere o `android:theme` para `@style/AppTheme`

```xml
<application
    android:name=".TeamsEmbedAndroidGettingStarted"
    tools:replace="android:name"
    android:theme="@style/AppTheme"
    android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:roundIcon="@mipmap/ic_launcher_round"
    android:supportsRtl="true">
```

### <a name="set-up-the-layout-for-the-app"></a>Configurar o layout do aplicativo

Crie um botão com a ID `join_meeting`. Navegue para (`app/src/main/res/layout/activity_main.xml`) e substitua o conteúdo do arquivo pelo seguinte:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/join_meeting"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Join Meeting"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>Criar as associações e o scaffolding da atividade principal

Com o layout criado, o scaffolding básico da atividade, bem como as associações necessárias, podem ser adicionados. A atividade administra a solicitação de permissões de runtime, a criação do cliente da reunião e o ingresso na reunião quando o botão for pressionado. Cada uma será abordada na própria seção. 

O método `onCreate` será substituído para invocar `getAllPermissions` e `createAgent`, bem como adicionar as associações do botão `Join Meeting`. Isso ocorrerá apenas uma vez quando a atividade for criada. Para obter mais informações sobre `onCreate`, confira o guia [Entender o ciclo de vida da atividade](https://developer.android.com/guide/components/activities/activity-lifecycle).

Navegue para **MainActivity.java** e substitua o conteúdo pelo seguinte código:

```java
package com.yourcompany.teamsembedandroidgettingstarted;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.Toast;

import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.common.CommunicationTokenRefreshOptions;
import com.azure.android.communication.ui.meetings.MeetingJoinOptions;
import com.azure.android.communication.ui.meetings.MeetingUIClient;

import java.util.ArrayList;
import java.util.concurrent.Callable;

public class MainActivity extends AppCompatActivity {

    private final String displayName = "John Smith";

    private MeetingUIClient meetingUIClient;
    private MeetingJoinOptions meetingJoinOptions;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        meetingJoinOptions = new MeetingJoinOptions(displayName);
        
        getAllPermissions();
        createMeetingClient();

        Button joinMeeting = findViewById(R.id.join_meeting);
        joinMeeting.setOnClickListener(l -> joinMeeting());
    }

    private void createMeetingClient() {
        // See section on creating meeting client
    }

    private void joinMeeting() {
        // See section on joining a meeting
    }

    private void getAllPermissions() {
        // See section on getting permissions
    }
}
```

### <a name="request-permissions-at-runtime"></a>Solicitar permissões no runtime

Para o Android 6.0 e superior (nível da API 23) e `targetSdkVersion` 23 ou superior, as permissões são concedidas no runtime em vez de quando o aplicativo é instalado. Para dar suporte a isso, `getAllPermissions` pode ser implementado para chamar `ActivityCompat.checkSelfPermission` e `ActivityCompat.requestPermissions` para cada permissão necessária.

```java
/**
 * Request each required permission if the app doesn't already have it.
 */
private void getAllPermissions() {
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 202);
    }
}
```

> [!NOTE]
> Ao projetar o seu aplicativo, leve em consideração quando essas permissões devem ser solicitadas. As permissões devem ser solicitadas quando elas forem necessárias e não com antecedência. Para obter mais informações, confira o [Guia de Permissões do Android.](https://developer.android.com/training/permissions/requesting)

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e as interfaces administram alguns dos principais recursos da biblioteca de Inserção do Teams dos Serviços de Comunicação do Azure:

| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| MeetingUIClient| O MeetingUIClient é o principal ponto de entrada na biblioteca de Inserção do Teams. |
| MeetingJoinOptions | MeetingJoinOptions são usadas para opções configuráveis, como nome de exibição. |
| CallState | O CallState é usado para relatar alterações no estado da chamada. As opções são: `connecting`, `waitingInLobby`, `connected` e `ended`. |

## <a name="create-a-meetingclient-from-the-user-access-token"></a>Criar um MeetingClient do token de acesso do usuário

É possível criar uma instância do cliente de reunião autenticado com um token de acesso do usuário. Esse token geralmente é gerado por um serviço com autenticação específica para o aplicativo. Para saber mais sobre os tokens de acesso do usuário, confira o guia [Tokens de Acesso do Usuário](../../access-tokens.md). Para o guia de início rápido, substitua `<USER_ACCESS_TOKEN>` por um token de acesso de usuário gerado para o recurso do Serviço de Comunicação do Azure.

```java
private void createMeetingClient() {
    try {
        CommunicationTokenRefreshOptions refreshOptions = new CommunicationTokenRefreshOptions(tokenRefresher, true, "<USER_ACCESS_TOKEN>");
        CommunicationTokenCredential credential = new CommunicationTokenCredential(refreshOptions);
        meetingUIClient = new MeetingUIClient(credential);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="setup-token-refreshing"></a>Configurar a atualização do token

Crie um método `tokenRefresher` que pode ser chamado. Em seguida, crie um método `fetchToken` para obter o token do usuário. [Encontre instruções de como fazer isso aqui](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens?pivots=programming-language-java)

```java
Callable<String> tokenRefresher = () -> {
    return fetchToken();
};

public String fetchToken() {
    // Get token
    return USER_ACCESS_TOKEN;
}
```

## <a name="get-the-teams-meeting-link"></a>Obter o link de reunião do Teams

O link de reunião do Teams pode ser recuperado usando APIs do Graph. Isso é detalhado na [documentação do Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true).
O SDK de Chamada de Serviços de Comunicação aceita um link de reunião completo do Teams. Esse link é retornado como parte do recurso `onlineMeeting`, acessível na propriedade [`joinWebUrl`](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) Você também pode obter as informações da reunião necessárias da URL **Ingressar na Reunião** no próprio convite da reunião do Teams.

## <a name="start-a-meeting-using-the-meeting-client"></a>Iniciar uma reunião usando o cliente de reunião

O ingresso em uma reunião pode ser feito por meio do `MeetingClient` e requer apenas um `meetingURL` e o `JoinOptions`. Substitua `<MEETING_URL>` por uma URL de reunião do Teams.

```java
/**
 * Join a meeting with a meetingURL.
 */
private void joinMeeting() {
    try {
        meetingUIClient.join("<MEETING_URL>", meetingJoinOptions);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="launch-the-app-and-join-a-meeting"></a>Iniciar o aplicativo e ingressar em uma reunião

Agora, o aplicativo pode ser iniciado usando o botão "Executar Aplicativo" na barra de ferramentas (Shift+F10). 

:::image type="content" source="../media/android/quickstart-android-join-meeting.png" alt-text="Captura de tela que mostra o aplicativo concluído.":::

:::image type="content" source="../media/android/quickstart-android-joined-meeting.png" alt-text="Captura de tela mostrando o aplicativo concluído após o ingresso na reunião.":::

## <a name="sample-code"></a>Exemplo de código

É possível baixar o aplicativo de exemplo do [GitHub](https://github.com/Azure-Samples/teams-embed-android-getting-started)
