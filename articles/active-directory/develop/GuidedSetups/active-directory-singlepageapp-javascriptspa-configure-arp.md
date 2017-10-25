---
title: "Instalação guiada do JS SPA no Azure AD v2 –Configurar (ARP) | Microsoft Docs"
description: Como aplicativos JavaScript SPA podem chamar uma API que exige tokens de acesso pelo ponto de extremidade do Azure Active Directory v2 (ARP)
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
ms.openlocfilehash: 708f4ff606d79639de979918a9cacd4ed75db311
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Adicionar as informações de registro do aplicativo ao seu aplicativo

Nesta etapa, é necessário configurar a URL de Redirecionamento das suas informações de registro do aplicativo e, em seguida, adicionar a Id do Aplicativo ao seu aplicativo JavaScript SPA.

### <a name="configure-redirect-url"></a>Configurar a URL de redirecionamento

Configure o campo `Redirect URL` acima com a URL para a sua página index.html com base em seu servidor Web, em seguida, clique em *Atualizar*.


> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>Instruções do Visual Studio para obter a URL de redirecionamento
> Para obter a URL de redirecionamento, siga as instruções abaixo:
> 1.    No *Gerenciador de Soluções*, selecione o projeto e examine a janela `Properties` (se uma janela Propriedades não for exibida, pressione `F4`)
> 2.    Copie o valor de `URL` para a área de transferência:<br/> ![Propriedades do projeto](media/active-directory-singlepageapp-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Cole o valor como um `Redirect URL` no topo desta página e, em seguida, clique em `Update`

<p/>

> #### <a name="setting-redirect-url-for-python"></a>Definir a URL de Redirecionamento para o Python
> Para Python, você pode definir a porta do servidor Web por meio da linha de comando. Esta instalação interativa usa a porta 8080 para referência, mas fique à vontade para usar qualquer outra porta disponível. Em qualquer caso, siga as instruções abaixo para configurar uma URL de redirecionamento nas informações de registro do aplicativo:<br/>
> Defina `http://localhost:8080/` como um `Redirect URL` na parte superior dessa página ou use `http://localhost:[port]/` se você estiver usando uma porta TCP personalizada (em que *[port]* é o número da porta TCP personalizada) e, em seguida, clique em 'Atualizar'

### <a name="configure-your-javascript-spa-application"></a>Configure seu aplicativo JavaScript SPA

1.  Crie um arquivo chamado `msalconfig.js` que contém as informações de registro do aplicativo. Se você estiver usando o Visual Studio, selecione o projeto (pasta raiz do projeto), clique com o botão direito do mouse e selecione: `Add` > `New Item` > `JavaScript File`. Nomeie-o `msalconfig.js`
2.  Adicione o seguinte código ao seu arquivo `msalconfig.js`:

```javascript
var msalconfig = {
    clientID: "[Enter the application Id here]",
    redirectUri: location.origin
};
``` 
