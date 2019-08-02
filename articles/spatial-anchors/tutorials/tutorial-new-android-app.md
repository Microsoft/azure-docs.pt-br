---
title: Tutorial – instruções passo a passo para criar um novo aplicativo Android usando Âncoras Espaciais do Azure | Microsoft Docs
description: Neste tutorial, você aprenderá como criar um novo aplicativo Android usando Âncoras Espaciais do Azure.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 04/03/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 499b08dbdc8e798a884b721bcba51be1f6973df6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562397"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>Tutorial: Instruções passo a passo para criar um novo aplicativo Android usando Âncoras Espaciais do Azure

Este tutorial mostra como criar um novo aplicativo Android que se integra à funcionalidade de ARCore com Âncoras Espaciais do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se você tem:

- Um computador Windows ou macOS com <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4+</a>.
- Um dispositivo Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">habilitado para desenvolvedor</a> e <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">compatível com ARCore</a>.

## <a name="getting-started"></a>Introdução

Inicie o Android Studio. Na janela **Bem-vindo ao Android Studio**, clique em **Iniciar um novo projeto do Android Studio**. Ou, se você já tiver um projeto aberto, selecione **Arquivo**->**Novo Projeto**.

Na janela **Criar Novo Projeto**, na seção **Telefone e Tablet**, escolha **Atividade Vazia** e clique em **Avançar**. Em seguida, em **Nível mínimo da API**, escolha `API 26: Android 8.0 (Oreo)` e verifique se a **Linguagem de programação** está definida como `Java`. Talvez você queira alterar a localização e o nome do projeto, bem como o nome do pacote. Deixe as outras opções como estão. Clique em **Concluir**. O **Instalador do Componente** será executado. Quando terminar, clique em **Concluir**. Depois de algum processamento, o Android Studio abrirá o IDE.

## <a name="trying-it-out"></a>Experimentá-lo

Para testar seu novo aplicativo, conecte o dispositivo de desenvolvedor habilitado para seu computador de desenvolvimento com um cabo USB. Clique em **Executar**->**Executar 'aplicativo'** . Na janela **Selecione o Destino de Implantação**, selecione seu dispositivo e clique em **OK**. Android Studio instala o aplicativo no dispositivo conectado e o inicia. Agora você deve ver "Olá, Mundo!" exibido no aplicativo em execução no seu dispositivo. Clique em **Executar**->**Interromper 'aplicativo'** .

## <a name="integrating-arcore"></a>Integração do _ARCore_

O <a href="https://developers.google.com/ar/discover/" target="_blank">_ARCore_</a> é a plataforma do Google para a criação de experiências de realidade aumentada, permitindo que seu dispositivo controle sua própria posição conforme ele se move e cria seu próprio entendimento do mundo real.

Modifique `app\manifests\AndroidManifest.xml` para incluir as entradas a seguir dentro do nó `<manifest>` da raiz. Este snippet de código faz algumas coisas:

- Ele permitirá que seu aplicativo acesse sua câmera do dispositivo.
- Ele também garantirá que seu aplicativo só seja visível no Google Play Store para dispositivos que deem suporte a ARCore.
- Ele também configurará o Google Play Store para baixar e instalar o ARCore, se ainda não estiver instalado, no ato da instalação do aplicativo.

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera.ar" />

<application>
    ...
    <meta-data android:name="com.google.ar.core" android:value="required" />
    ...
</application>
```

Modifique `Gradle Scripts\build.gradle (Module: app)` para incluir a entrada a seguir. Esse código assegurará que o aplicativo tenha o ARCore versão 1.8 como destino. Após essa alteração, você poderá receber uma notificação do Gradle solicitando que você sincronize: clique em **Sincronizar agora**.

```
dependencies {
    ...
    implementation 'com.google.ar:core:1.8.0'
    ...
}
```

## <a name="integrating-sceneform"></a>Integração do _Sceneform_

O <a href="https://developers.google.com/ar/develop/java/sceneform/" target="_blank">_Sceneform_</a> simplifica a renderização de cenas 3D realistas em aplicativos de realidade aumentada, sem precisar aprender OpenGL.

Modifique `Gradle Scripts\build.gradle (Module: app)` para incluir as entradas a seguir. Este código permitirá que seu aplicativo use constructos de linguagem do Java 8, exigidos pelo `Sceneform`. Também garantirá que aplicativo se destine ao `Sceneform` versão 1.8, já que ele deve corresponder à versão do ARCore que o aplicativo está usando. Após essa alteração, você poderá receber uma notificação do Gradle solicitando que você sincronize: clique em **Sincronizar agora**.

```
android {
    ...

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.google.ar.sceneform.ux:sceneform-ux:1.8.0'
    ...
}
```

Abra seu `app\res\layout\activity_main.xml` e substitua o elemento `<TextView>` de Olá Mundo existente com o seguinte ArFragment. Esse código fará com que o feed da câmera seja exibido na tela, permitindo que o ARCore acompanhe a posição do dispositivo conforme ele se move.

```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

[Reimplante](#trying-it-out) seu aplicativo no dispositivo para validá-lo mais uma vez. Dessa vez, deve ser solicitado que você forneça permissões de câmera. Após a aprovação, você deverá ver a renderização do feed da câmera na tela.

## <a name="place-an-object-in-the-real-world"></a>Colocar um objeto no mundo real

Vamos criar e dispor um objeto usando o aplicativo. Primeiro, adicione as seguintes importações ao seu `app\java\<PackageName>\MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=23-33)]

Em seguida, adicione as seguintes variáveis de membro na classe `MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-57)]

Depois, adicione o código a seguir ao método `app\java\<PackageName>\MainActivity` `onCreate()`. Esse código conectará um ouvinte chamado `handleTap()`, que detectará quando o usuário toca a tela em seu dispositivo. Se o toque ocorrer em uma superfície do mundo real que já tiver sido reconhecida pelo acompanhamento do ARCore, o ouvinte será executado.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-74,85&highlight=6-7)]

Por fim, adicione o método `handleTap()` a seguir, que juntará tudo. Ele criará uma esfera e a colocará na localização tocada. A esfera inicialmente será preta, já que `this.recommendedSessionProgress` está definido como zero no momento. Esse valor será ajustado posteriormente.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-171,174-182,198-199)]

[Reimplante](#trying-it-out) seu aplicativo no dispositivo para validá-lo mais uma vez. Neste momento, você pode mover em torno de seu dispositivo para fazer com que o ARCore comece a reconhecer o seu ambiente. Em seguida, toque na tela para criar e colocar seu círculo preto sobre a superfície de sua escolha.

## <a name="attach-a-local-azure-spatial-anchor"></a>Anexar uma âncora espacial do Azure local

Modifique `Gradle Scripts\build.gradle (Module: app)` para incluir a entrada a seguir. Esse código assegurará que o aplicativo se destine às Âncoras Espaciais do Azure versão 1.3.0. Dito isso, fazer referência a qualquer versão recente das Âncoras Espaciais do Azure deve funcionar.

```
dependencies {
    ...
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_jni:[1.3.0]"
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_java:[1.3.0]"
    ...
}
```

Clique com o botão direito do mouse em `app\java\<PackageName>`->**Nova**->**Classe Java**. Defina o **Nome** como _MyFirstApp_ e a **Superclasse** como _android.app.Application_. Deixe as outras opções como estão. Clique em **OK**. Um arquivo chamado `MyFirstApp.java` será criado. Adicione a importação a seguir a ele:

```java
import com.microsoft.CloudServices;
```

Depois, adicione o código a seguir dentro da nova classe `MyFirstApp`, o que assegura que as Âncoras Espaciais do Azure sejam inicializadas com o contexto do aplicativo.

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

Agora, modifique `app\manifests\AndroidManifest.xml` para incluir a entrada a seguir dentro do nó `<application>` da raiz. Esse código se conectará à classe Aplicativo que você criou em seu aplicativo.

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

De volta em `app\java\<PackageName>\MainActivity`, adicione as seguintes importações a ele:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=33-40&highlight=2-8)]

Em seguida, adicione as seguintes variáveis de membro na classe `MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=57-60&highlight=3-4)]

Em seguida, adicionaremos o método `initializeSession()` a seguir dentro da classe `mainActivity`. Quando chamado, ele garantirá que uma sessão das Âncoras Espaciais do Azure seja criada e inicializada corretamente durante a inicialização do aplicativo.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-97,146)]

Agora, vamos conectar o método `initializeSession()` ao método `onCreate()`. Além disso, nós garantiremos que quadros do feed da câmera sejam enviados ao SDK das Âncoras Espaciais do Azure para processamento.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-85&highlight=9-17)]

Por fim, adicione o código a seguir ao método `handleTap()`. Ele anexará uma âncora espacial do Azure local à esfera preta que estamos colocando no mundo real.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-182,198-199&highlight=12-13)]

[Reimplante](#trying-it-out) o aplicativo mais uma vez. Mova seu dispositivo, toque na tela e posicione uma esfera preta. Neste momento, no entanto, seu código criará e anexará uma âncora espacial do Azure local à esfera.

Antes de Avançar, você precisará criar um identificador e chave de conta das Âncoras Espaciais do Azure, se ainda não os tiver. Siga a seção a seguir para obtê-los.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Carregar sua âncora local na nuvem

Depois que tiver seu identificador e chave de conta das Âncoras Espaciais do Azure, podemos voltar para `app\java\<PackageName>\MainActivity`. Adicione as seguintes importações a ele:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=40-45&highlight=3-6)]

Em seguida, adicione as seguintes variáveis de membro na classe `MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=60-65&highlight=3-6)]

Agora, adicione o código a seguir ao método `initializeSession()`. Primeiro, este código permitirá que seu aplicativo monitore o progresso que o SDK das Âncoras Espaciais do Azure faz conforme ele coleta quadros do feed da câmera. Conforme ele o faz, a cor de sua esfera começará a se alterar de seu preto original para cinza. Em seguida, ela se tornará branca depois que quadros suficientes tiverem sido coletados para enviar sua âncora para a nuvem. Em segundo lugar, esse código fornecerá as credenciais necessárias para se comunicar com o back-end de nuvem. É aqui que você configurará seu aplicativo para usar seu identificador e chave de conta. Você os copiou em um editor de texto ao [configurar o recurso Âncoras Espaciais](#create-a-spatial-anchors-resource).

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-120,142-146&highlight=11-36)]

Em seguida, adicione o método `uploadCloudAnchorAsync()` a seguir dentro da classe `mainActivity`. Quando chamado, esse método aguardará assincronamente até que quadros suficientes tenham sido coletados do seu dispositivo. Assim que isso acontecer, ele mudará a cor de sua esfera para amarelo e, em seguida, iniciará o upload de sua âncora espacial do Azure local para a nuvem. Depois que o upload for concluído, o código retornará um identificador da âncora.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

Por fim, vamos interligar tudo. Em seu método `handleTap()`, adicione o código a seguir. Ele invocará o método `uploadCloudAnchorAsync()` assim que a esfera for criada. Depois que o método retornar, o código a seguir executará uma atualização final à esfera, alterando sua cor para azul.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-199&highlight=24-37)]

[Reimplante](#trying-it-out) o aplicativo mais uma vez. Mova seu dispositivo, toque na tela e posicione a esfera. Neste momento, no entanto, a cor da esfera mudará de preto para branco, conforme os quadros da câmera forem coletados. Assim que tivermos quadros suficientes, a esfera ficará amarela e o upload para a nuvem começará. Depois que o upload for concluído, a esfera ficará azul. Opcionalmente, você também pode usar a janela `Logcat` dentro do Android Studio para monitorar as mensagens de log que o aplicativo está enviando. Por exemplo, o progresso da sessão durante capturas de quadro, bem como o identificador de âncora que a nuvem retorna uma vez que o upload é concluído.

## <a name="locate-your-cloud-spatial-anchor"></a>Localizar a âncora espacial de nuvem

Após a âncora ser carregada para a nuvem, estamos prontos para tentar localizá-la novamente. Primeiro, vamos adicionar as importações a seguir ao seu código.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=45-48&highlight=3-4)]

Depois, vamos adicionar o código a seguir ao método `handleTap()`. Esse código vai:

- Remover nossa esfera azul existente da tela.
- Inicializar nossa sessão de Âncoras Espaciais do Azure novamente. Esta ação assegurará que a âncora que vamos localizar venha da nuvem em vez de provir da âncora de local que criamos.
- Emita uma consulta para a âncora que é carregada para a nuvem.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

Agora, vamos conectar o código que será invocado quando a âncora que estamos consultando for localizada. Em seu método `initializeSession()`, adicione o código a seguir. Este snippet de código criará uma esfera verde e a posicionará quando a âncora espacial de nuvem for localizada. Ele também habilitará o toque na tela novamente, portanto, você poderá repetir o cenário completo mais uma vez: criar outra âncora local, fará o upload dela e a localizará novamente.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=34-53)]

É isso! [Reimplante](#trying-it-out) seu aplicativo uma última vez para experimentar o cenário completo de ponta a ponta. Mova seu dispositivo e posicione sua esfera preta. Em seguida, continuaremos movendo seu dispositivo para capturar quadros da câmera até que a esfera fique amarela. A âncora local será carregada e a esfera ficará azul. Por fim, toque em sua tela mais uma vez para que sua âncora local seja removida e, em seguida, consultaremos em busca de seu equivalente de nuvem. Continue movendo seu dispositivo até que a âncora espacial de nuvem seja localizada. Uma esfera verde deve aparecer na localização correta e você pode limpar e repetir o cenário completo.

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]
