---
title: "Instalação guiada do JS SPA no Azure AD v2 – Configurar | Microsoft Docs"
description: Como aplicativos JavaScript SPA podem chamar uma API que exige tokens de acesso pelo ponto de extremidade do Azure Active Directory v2
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/01/2017
ms.author: andret
ms.openlocfilehash: adff529bfdc40006666cc643d49a302d7f1d09ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
## <a name="register-your-application"></a>Registre seu aplicativo

Há várias maneiras de criar um aplicativo, selecione um deles:

### <a name="option-1-register-your-application-express-mode"></a>Opção 1: Registrar seu aplicativo (modo Expresso)
Agora você precisa registrar seu aplicativo no *Portal de Registro de Aplicativos da Microsoft*:

1.  Registre o aplicativo por meio do [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure)
2.  Insira um nome para o aplicativo e seu email
3.  Certifique-se de que a opção *Instalação Guiada* está marcada
4.  Siga as instruções para obter a ID do aplicativo e colá-lo no código

### <a name="option-2-register-your-application-advanced-mode"></a>Opção 2: Registrar seu aplicativo (modo Avançado)

1. Acesse o [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/portal/register-app) para registrar um aplicativo
2. Insira um nome para o aplicativo e seu email 
3. Certifique-se de que a opção *Instalação Guiada* está desmarcada
4.  Clique em `Add Platform` e selecione `Web`
5. Adicione o `Redirect URL` que corresponde à URL do aplicativo com base no servidor Web. Veja as seções abaixo para obter instruções sobre como definir/obter a URL de redirecionamento no Visual Studio ou Python.
6. Clique em *Salvar*

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>Instruções do Visual Studio para obter a URL de redirecionamento
> Siga as instruções para obter a URL de redirecionamento:
> 1.    No *Gerenciador de Soluções*, selecione o projeto e examine a janela `Properties` (se uma janela Propriedades não for exibida, pressione `F4`)
> 2.    Copie o valor de `URL` para a área de transferência:<br/> ![Propriedades do projeto](media/active-directory-singlepageapp-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Volte para o *Portal de Registro de Aplicativos* e cole o valor como um `Redirect URL` e clique em 'Salvar'

<p/>

> #### <a name="setting-redirect-url-for-python"></a>Definir a URL de Redirecionamento para o Python
> Para Python, você pode definir a porta do servidor Web por meio da linha de comando. Esta instalação interativa usa a porta 8080 para referência, mas fique à vontade para usar qualquer outra porta disponível. Em qualquer caso, siga as instruções abaixo para configurar uma URL de redirecionamento nas informações de registro do aplicativo:<br/>
> - Volte para o *Portal de Registro de Aplicativos* e defina `http://localhost:8080/` como um `Redirect URL` ou use `http://localhost:[port]/` se estiver usando uma porta TCP personalizada (em que *[porta]* é o número da porta TCP personalizada) e clique em 'Salvar'


#### <a name="configure-your-javascript-spa"></a>Configurar o JavaScript SPA

1.  Crie um arquivo chamado `msalconfig.js` que contém as informações de registro do aplicativo. Se você estiver usando o Visual Studio, selecione o projeto (pasta raiz do projeto), clique com o botão direito do mouse e selecione: `Add` > `New Item` > `JavaScript File`. Nomeie-o `msalconfig.js`
2.  Adicione o seguinte código ao seu arquivo `msalconfig.js`:

```javascript
var msalconfig = {
    clientID: "Enter_the_Application_Id_here",
    redirectUri: location.origin
};
```
<ol start="3">
<li>
Substitua <code>Enter_the_Application_Id_here</code> pela ID do Aplicativo que você acabou de registrar
</li>
</ol>
