---
title: "Introdução ao Android no Azure AD v2 – Instalação | Microsoft Docs"
description: Como um aplicativo do Android pode obter um token de acesso e chamar a API do Microsoft Graph ou APIs que exigem tokens de acesso por meio do ponto de extremidade do Azure Active Directory v2
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: a43d7e30a6f4176afba27f0de2c2c116df741080
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
## <a name="set-up-your-project"></a>Configurar o seu projeto

> Prefere baixar este projeto do Android Studio de exemplo? [Baixe um projeto](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip) e vá para a [Etapa de configuração](#create-an-application-express) para configurar o exemplo de código antes de executá-lo.


### <a name="create-a-new-project"></a>Criar um novo projeto 
1.  Abra o Android Studio e acesse: `File` > `New` > `New Project`
2.  Nomeie o aplicativo e clique em `Next`
3.  Lembre-se de selecionar *API 21 ou mais nova (Android 5.0)* e clique em `Next`
4.  Mantenha `Empty Activity`, clique em `Next` e, em seguida, em `Finish`


### <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>Adicionar a MSAL (Biblioteca de Autenticação da Microsoft) ao projeto
1.  No Android Studio, acesse: `Gradle Scripts` > `build.gradle (Module: app)`
2.  Copie e cole o seguinte código em `Dependencies`:

```ruby  
compile ('com.microsoft.identity.client:msal:0.1.+') {
    exclude group: 'com.android.support', module: 'appcompat-v7'
}
compile 'com.android.volley:volley:1.0.0'
```

<!--start-collapse-->
### <a name="about-this-package"></a>Sobre este pacote

O pacote acima instala a MSAL (Biblioteca de Autenticação da Microsoft). A MSAL manipula a aquisição, o armazenamento em cache e a atualização de tokens de usuário usados para acessar APIs protegidas pelo ponto de extremidade do Azure Active Directory v2.
<!--end-collapse-->

## <a name="create-your-applications-ui"></a>Criar a interface do usuário do aplicativo

1.  Abra `activity_main.xml` em `res` > `layout`
2.  Altere o layout de atividade de `android.support.constraint.ConstraintLayout` ou outro para `LinearLayout`
3.  Adicione a propriedade `android:orientation="vertical"` ao nó `LinearLayout`
4.  Copie e cole o seguinte código no nó `LinearLayout`, substituindo o conteúdo atual:

```xml
<TextView
    android:text="Welcome, "
    android:textColor="#3f3f3f"
    android:textSize="50px"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginLeft="10dp"
    android:layout_marginTop="15dp"
    android:id="@+id/welcome"
    android:visibility="invisible"/>

<Button
    android:id="@+id/callGraph"
    android:text="Call Microsoft Graph"
    android:textColor="#FFFFFF"
    android:background="#00a1f1"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginTop="200dp"
    android:textAllCaps="false" />

<TextView
    android:text="Getting Graph Data..."
    android:textColor="#3f3f3f"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginLeft="5dp"
    android:id="@+id/graphData"
    android:visibility="invisible"/>

<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="0dip"
    android:layout_weight="1"
    android:gravity="center|bottom"
    android:orientation="vertical" >

    <Button
        android:text="Sign Out"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="15dp"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:textAllCaps="false"
        android:id="@+id/clearCache"
        android:visibility="invisible" />
</LinearLayout>
```

