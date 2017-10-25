---
title: Aplicativo Android v2.0 do Azure Active Directory | Microsoft Docs
description: "Como criar um aplicativo Android que conecte usuários com a conta pessoal e as contas corporativas ou de estudante da Microsoft e as chamadas à API do Graph usando bibliotecas de terceiros."
services: active-directory
documentationcenter: 
author: danieldobalian
manager: mbaldwin
editor: 
ms.assetid: 16294c07-f27d-45c9-833f-7dbb12083794
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: dadobali
ms.custom: aaddev
ms.openlocfilehash: c0a5a818c61f7af7ff04bf890b54e8364f3b21b1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-sign-in-to-an-android-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Adicionar entrada a um aplicativo Android usando uma biblioteca de terceiros com a API do Graph usando o ponto de extremidade v2.0
A plataforma de identidade da Microsoft usa padrões abertos, como OAuth2 e OpenID Connect. Os desenvolvedores podem usar qualquer biblioteca desejada para integrar aos nossos serviços. Para ajudar os desenvolvedores a usar nossa plataforma com outras bibliotecas, escrevemos alguns guias passo a passo como este para demonstrar como configurar bibliotecas de terceiros que se conectam à plataforma de identidade da Microsoft. A maioria das bibliotecas que implementa [a especificação RFC6749 do OAuth2](https://tools.ietf.org/html/rfc6749) pode se conectar à plataforma de identidade da Microsoft.

Com o aplicativo que esse passo a passo cria, os usuários podem entrar na respectiva organização e pesquisar a si mesmos na organização usando a API do Graph.

Se ainda não conhece o OAuth2 ou o OpenID Connect, grande parte desta configuração de exemplo pode não fazer muito sentido para você. É recomendável ler o artigo [Protocolos 2.0 – Fluxo de código de autorização do OAuth 2.0](active-directory-v2-protocols-oauth-code.md) para obter mais informações.

> [!NOTE]
> Alguns recursos da nossa plataforma que possuem uma expressão nos padrões OAuth2 ou OpenID Connect, como o Acesso Condicional e o gerenciamento de políticas do Intune, exigem que você use nossas Bibliotecas de Identidade do Microsoft Azure de software livre.
> 
> 

O ponto de extremidade v2.0 não dá suporte a todos os cenários e recursos do Azure Active Directory.

> [!NOTE]
> Para determinar se você deve usar o ponto de extremidade v2.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="download-the-code-from-github"></a>Baixar o código do GitHub
O código para este tutorial é mantido [no GitHub](https://github.com/Azure-Samples/active-directory-android-native-oidcandroidlib-v2).  Para acompanhar, você pode [baixar o esqueleto do aplicativo como um .zip](https://github.com/Azure-Samples/active-directory-android-native-oidcandroidlib-v2/archive/skeleton.zip) ou clonar o esqueleto:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

Você também pode apenas baixar o exemplo e começar imediatamente:

```
git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

## <a name="register-an-app"></a>Registrar um aplicativo
Crie um novo aplicativo no [Portal de registro de aplicativos](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) ou siga as etapas detalhadas em [Como registrar um aplicativo com o ponto de extremidade v 2.0](active-directory-v2-app-registration.md).  Não se esqueça de:

* Copiar a **ID do Aplicativo** atribuída ao seu aplicativo, pois você precisará dela em breve.
* Adicione a plataforma **Móvel** de seu aplicativo.

> Observação: o portal de registro de aplicativo fornece um valor de **URI de redirecionamento** . No entanto, neste exemplo, você deve usar o valor padrão de `https://login.microsoftonline.com/common/oauth2/nativeclient`.
> 
> 

## <a name="download-the-nxoauth2-third-party-library-and-create-a-workspace"></a>Baixar a biblioteca de terceiros NXOAuth2 e criar um espaço de trabalho
Para este passo a passo, você usará a OIDCAndroidLib do GitHub, que é uma biblioteca OAuth2 com base no código do OpenID Connect do Google. Ela implementa o perfil de aplicativo nativo e dá suporte ao ponto de extremidade de autorização do usuário. Isso é tudo de que você precisa para a integração à plataforma de identidade da Microsoft.

Clone o repositório OIDCAndroidLib em seu computador.

```
git@github.com:kalemontes/OIDCAndroidLib.git
```

![androidStudio](../media/active-directory-android-native-oidcandroidlib-v2/emotes-url.png)

## <a name="set-up-your-android-studio-environment"></a>Configurar o ambiente do Android Studio
1. Crie um novo projeto do Android Studio e aceite os padrões do assistente.
   
    ![Criar novo projeto no Android Studio](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample1.PNG)
   
    ![Dispositivos Android de destino](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample2.PNG)
   
    ![Adicionar uma atividade ao celular](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample3.PNG)
2. Para configurar os módulos de projeto, mova o repositório clonado para o local do projeto. Você também pode criar o projeto e cloná-lo diretamente no local do projeto.
   
    ![Módulos do projeto](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4_1.PNG)
3. Abra as configurações de módulos do projeto usando o menu de contexto ou usando o atalho Ctrl+Alt+Maj+S.
   
    ![Configurações dos módulos do projeto](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4.PNG)
4. Remova o módulo de aplicativo padrão, pois você quer apenas as configurações de contêiner do projeto.
   
    ![O módulo de aplicativo padrão](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample5.PNG)
5. Importe os módulos do repositório clonado para o projeto atual.
   
    ![Importar projeto gradle](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample6.PNG)![Página Criar novo módulo](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample7.PNG)
6. Repita essas etapas para o módulo `oidlib-sample` .
7. Verifique as dependências de oidclib no módulo `oidlib-sample` .
   
    ![dependências de oidclib no módulo de exemplo oidlib](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8.PNG)
8. Clique em **OK** e aguarde a sincronização do gradle.
   
    O settings.gradle deve se parecer com este:
   
    ![Captura de tela de settings.gradle](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8_1.PNG)
9. Crie o aplicativo de exemplo para garantir que o exemplo seja executado corretamente.
   
    Você ainda não poderá usá-lo com o Azure Active Directory. Você precisará configurar alguns pontos de extremidade primeiro. Isso é para garantir que você não tenha problemas no Android Studio antes de começar a personalizar o aplicativo de exemplo.
10. Compile e execute `oidlib-sample` como o destino no Android Studio.
    
    ![Progresso da criação do exemplo oidlib](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample9.png)
11. Exclua o diretório `app ` que foi deixado durante a remoção do módulo do projeto, já que o Android Studio não o exclui por segurança.
    
    ![Estrutura do arquivo que inclui o diretório de aplicativo](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample12.PNG)
12. Abra o menu **Editar Configurações** para remover a configuração de execução que também foi deixada quando você removeu o módulo do projeto.
    
    ![Menu Editar configurações](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample10.PNG)
    ![Executar configuração do aplicativo](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample11.PNG)

## <a name="configure-the-endpoints-of-the-sample"></a>Configurar os pontos de extremidade do exemplo
Agora que `oidlib-sample` está em execução com êxito, vamos editar alguns pontos de extremidade para que isso funcione com o Azure Active Directory.

### <a name="configure-your-client-by-editing-the-oidcclientconfxml-file"></a>Configurar o cliente editando o arquivo oidc_clientconf.xml
1. Já que você está usando somente fluxos do OAuth2 para obter um token e chamar a API do Graph, defina o cliente somente para OAuth2. O OIDC aparecerá em um exemplo mais adiante.
   
    ```xml
        <bool name="oidc_oauth2only">true</bool>
    ```
2. Configure a ID de cliente que você recebeu do portal de registro.
   
    ```xml
        <string name="oidc_clientId">86172f9d-a1ae-4348-aafa-7b3e5d1b36f5</string>
        <string name="oidc_clientSecret"></string>
    ```
3. Configure o URI de redirecionamento com o exemplo abaixo.
   
    ```xml
        <string name="oidc_redirectUrl">https://login.microsoftonline.com/common/oauth2/nativeclient</string>
    ```
4. Configure os escopos de que você precisa para acessar a API do Graph.
   
    ```xml
        <string-array name="oidc_scopes">
            <item>openid</item>
            <item>https://graph.microsoft.com/User.Read</item>
            <item>offline_access</item>
        </string-array>
    ```

O valor `User.Read` em `oidc_scopes` permite que você leia o perfil básico do usuário conectado.
É possível saber mais sobre todos os escopos disponíveis em [Escopos de permissão do Microsoft Graph](https://graph.microsoft.io/docs/authorization/permission_scopes).

Se você quiser explicações sobre `openid` ou `offline_access` como escopos no OpenID Connect, confira [Protocolos v2.0 – Fluxo de código de autorização do OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

### <a name="configure-your-client-endpoints-by-editing-the-oidcendpointsxml-file"></a>Configurar pontos de extremidade do cliente editando o oidc_endpoints.xml
* Abra o arquivo `oidc_endpoints.xml` e faça estas alterações:
  
    ```xml
    <!-- Stores OpenID Connect provider endpoints. -->
    <resources>
        <string name="op_authorizationEnpoint">https://login.microsoftonline.com/common/oauth2/v2.0/authorize</string>
        <string name="op_tokenEndpoint">https://login.microsoftonline.com/common/oauth2/v2.0/token</string>
        <string name="op_userInfoEndpoint">https://www.example.com/oauth2/userinfo</string>
        <string name="op_revocationEndpoint">https://www.example.com/oauth2/revoketoken</string>
    </resources>
    ```

Esses pontos de extremidade nunca deverão ser alterados se você estiver usando o OAuth2 como seu protocolo.

> [!NOTE]
> Os pontos de extremidade de `userInfoEndpoint` e `revocationEndpoint` atualmente não têm suporte do Azure Active Directory. Se deixá-los com o valor padrão de example.com, você será lembrado que eles não estão disponíveis no exemplo :-)
> 
> 

## <a name="configure-a-graph-api-call"></a>Configurar uma chamada à API do Graph
* Abra o arquivo `HomeActivity.java` e faça estas alterações:
  
    ```Java
       //TODO: set your protected resource url
        private static final String protectedResUrl = "https://graph.microsoft.com/v1.0/me/";
    ```

Aqui, uma chamada simples à API do Graph retorna nossas informações.

Essas são todas as alterações que você precisa fazer. Execute o aplicativo `oidlib-sample` e clique em **Entrar**.

Depois da autenticação bem-sucedida, escolha o botão **Solicitar Recurso Protegido** para testar sua chamada à API do Graph.

## <a name="get-security-updates-for-our-product"></a>Obter atualizações de segurança para nosso produto
É recomendável obter notificações sobre incidentes de segurança visitando a página [Segurança TechCenter](https://technet.microsoft.com/security/dd252948) e assinando os alertas do Security Advisory.

