---
title: Guia de início rápido – Adicionar uma chamada VOIP a um aplicativo Android usando os Serviços de Comunicação do Azure
description: Neste tutorial, você aprenderá a usar o SDK de Chamada dos Serviços de Comunicação do Azure para Android
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 38194ed7290d6cd9c4889d27ff458f950603c5be
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "106073000"
---
Neste guia de início rápido, você aprenderá a iniciar uma chamada usando o SDK de Chamada dos Serviços de Comunicação do Azure para Android.

[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-android-ios.md)]

> [!NOTE]
> Este documento usa a versão 1.0.0-beta.8 do SDK de Chamada.

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

Selecione o SDK Mínimo de "API 26: Android 8.0 (Oreo)" ou superior.

:::image type="content" source="../media/android/studio-calling-min-api.png" alt-text="Captura de tela que mostra a opção 'Atividade Vazia' selecionada na Tela do Modelo do Projeto 2.":::


### <a name="install-the-package"></a>Instalar o pacote

<!-- TODO: update with instructions on how to download, install and add package to project -->
Localize o nível de projeto build.gradle e certifique-se de adicionar `mavenCentral()` à lista de repositórios em `buildscript` e `allprojects`
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
Em seguida, no nível do módulo build.gradle, adicione as linhas a seguir às seções do Android e às dependências

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.8'
    ...
}
```

### <a name="add-permissions-to-application-manifest"></a>Adicionar permissões ao manifesto do aplicativo

Para solicitar as permissões necessárias para fazer uma chamada, elas precisam primeiro ser declaradas no Manifesto do Aplicativo (`app/src/main/AndroidManifest.xml`). Substitua o conteúdo do arquivo pelo seguinte:

```xml
    <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.contoso.acsquickstart">

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <!--Our Calling SDK depends on the Apache HTTP SDK.
When targeting Android SDK 28+, this library needs to be explicitly referenced.
See https://developer.android.com/about/versions/pie/android-9.0-changes-28#apache-p-->
        <uses-library android:name="org.apache.http.legacy" android:required="false"/>
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
    
```

### <a name="set-up-the-layout-for-the-app"></a>Configurar o layout do aplicativo

Duas entradas são necessárias: uma entrada de texto para a ID do receptor e um botão para realizar a chamada. Elas podem ser adicionadas por meio do designer ou por meio da edição do XML de layout. Crie um botão com uma ID de `call_button` e uma entrada de texto de `callee_id`. Navegue para (`app/src/main/res/layout/activity_main.xml`) e substitua o conteúdo do arquivo pelo seguinte:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/call_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="16dp"
        android:text="Call"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <EditText
        android:id="@+id/callee_id"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="Callee Id"
        android:inputType="textPersonName"
        app:layout_constraintBottom_toTopOf="@+id/call_button"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>Criar as associações e o scaffolding da atividade principal

Com o layout criado, as associações podem ser adicionadas, bem como o scaffolding básico da atividade. A atividade administra a solicitação de permissões de runtime, criando o agente de chamada e fazendo a chamada quando o botão estiver pressionado. Cada uma será abordada na própria seção. O método `onCreate` será substituído para invocar `getAllPermissions` e `createAgent`, bem como adicionar as associações do botão de chamada. Isso ocorrerá apenas uma vez quando a atividade for criada. Para obter mais informações sobre `onCreate`, confira o guia [Entender o ciclo de vida da atividade](https://developer.android.com/guide/components/activities/activity-lifecycle).

Navegue para **MainActivity.java** e substitua o conteúdo pelo seguinte código:

```java
package com.contoso.acsquickstart;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import com.azure.android.communication.common.CommunicationUserIdentifier;
import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.calling.CallAgent;
import com.azure.android.communication.calling.CallClient;
import com.azure.android.communication.calling.StartCallOptions;


import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {
    
    private CallAgent callAgent;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        getAllPermissions();
        createAgent();
        
        // Bind call button to call `startCall`
        Button callButton = findViewById(R.id.call_button);
        callButton.setOnClickListener(l -> startCall());
    }

    /**
     * Request each required permission if the app doesn't already have it.
     */
    private void getAllPermissions() {
        // See section on requesting permissions
    }

    /**
      * Create the call agent for placing calls
      */
    private void createAgent() {
        // See section on creating the call agent
    }

    /**
     * Place a call to the callee id provided in `callee_id` text input.
     */
    private void startCall() {
        // See section on starting the call
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
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO, Manifest.permission.CAMERA, Manifest.permission.WRITE_EXTERNAL_STORAGE, Manifest.permission.READ_PHONE_STATE};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 1);
    }
}
```

> [!NOTE]
> Ao projetar o seu aplicativo, leve em consideração quando essas permissões devem ser solicitadas. As permissões devem ser solicitadas quando elas forem necessárias e não com antecedência. Para obter mais informações, confira o [Guia de Permissões do Android.](https://developer.android.com/training/permissions/requesting)

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces cuidam de alguns dos principais recursos do SDK de Chamada da Interface do Usuário dos Serviços de Comunicação do Azure:

| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| O CallClient é o ponto de entrada principal para o SDK de Chamada.|
| CallAgent | O CallAgent é usado para iniciar e gerenciar chamadas. |
| CommunicationUserCredential | O CommunicationUserCredential é usado como a credencial de token para criar uma instância do CallAgent.|
| CommunicationIdentifier | O CommunicationIdentifier é usado como um tipo diferente de participante que poderia fazer parte de uma chamada.|

## <a name="create-an-agent-from-the-user-access-token"></a>Criar um agente do token de acesso do usuário

Com o token do usuário, um agente de chamada autenticado pode criar uma instância. Geralmente, esse token será gerado de um serviço com autenticação específica para o aplicativo. Para obter mais informações sobre tokens de acesso do usuário, confira o guia [Tokens de Acesso do Usuário](../../access-tokens.md). Para o guia de início rápido, substitua `<User_Access_Token>` por um token de acesso de usuário gerado para o recurso do Serviço de Comunicação do Azure.

```java

/**
 * Create the call agent for placing calls
 */
private void createAgent() {
    String userToken = "<User_Access_Token>";

    try {
        CommunicationTokenCredential credential = new CommunicationTokenCredential(userToken);
        callAgent = new CallClient().createCallAgent(getApplicationContext(), credential).get();
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create call agent.", Toast.LENGTH_SHORT).show();
    }
}

```

## <a name="start-a-call-using-the-call-agent"></a>Iniciar uma chamada usando o agente de chamada

A colocação da chamada pode ser feita por meio do agente de chamada e apenas requer a inclusão de uma lista de IDs de receptor da chamada e as opções de chamada. Para o guia de início rápido, as opções de chamada padrão sem vídeo e uma ID de receptor da entrada de texto serão usadas.

```java
/**
 * Place a call to the callee id provided in `callee_id` text input.
 */
private void startCall() {
    EditText calleeIdView = findViewById(R.id.callee_id);
    String calleeId = calleeIdView.getText().toString();
    
    StartCallOptions options = new StartCallOptions();

    callAgent.startCall(
        getApplicationContext(),
        new CommunicationUserIdentifier[] {new CommunicationUserIdentifier(calleeId)},
        options);
}
```


## <a name="launch-the-app-and-call-the-echo-bot"></a>Iniciar o aplicativo e chamar o bot de eco

Agora, o aplicativo pode ser iniciado usando o botão "Executar Aplicativo" na barra de ferramentas (Shift+F10). Chame `8:echo123` para verificar se você pode fazer chamadas. Uma mensagem previamente gravada será reproduzida e, em seguida, repetirá a sua mensagem de volta para você.

:::image type="content" source="../media/android/quickstart-android-call-echobot.png" alt-text="Captura de tela que mostra o aplicativo concluído.":::

## <a name="sample-code"></a>Exemplo de código

É possível baixar o aplicativo de exemplo do [GitHub](https://github.com/Azure-Samples/communication-services-android-quickstarts/tree/main/Add%20Voice%20Calling)
