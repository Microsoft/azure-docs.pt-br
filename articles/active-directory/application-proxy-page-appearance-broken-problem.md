---
title: "Página de aplicativo não exibe corretamente para um aplicativo de Proxy de aplicativo | Microsoft Docs"
description: "Diretrizes quando a página não está sendo exibida corretamente em um Aplicativo de Proxy de Aplicativo que você integrou com o Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: cac4c333e59ef9a0f28a2f93a7afee22eeafd54e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Página de aplicativo não exibe corretamente para um aplicativo de Proxy de aplicativo

Este artigo irá ajudá-lo a solucionar problemas com aplicativos de Proxy de Aplicativo do Azure Active Directory quando você navegar para a página, mas algo na página não parece correto.

## <a name="overview"></a>Visão geral
Quando você publica um aplicativo de Proxy de aplicativo, apenas as páginas em sua raiz são acessíveis ao acessar o aplicativo. Se a página não estiver exibindo corretamente, a URL interna raiz usada para o aplicativo pode estar em falta de alguns recursos de página. Para resolver, certifique-se de ter publicado *todos* os recursos da página como parte do seu aplicativo.

Você pode verificar se esse é o problema, abrindo o rastreador de rede (como o Fiddler ou ferramentas F12 no Internet Explorer/Edge), carregando a página e procurando por erros 404. Esses indicam as páginas que atualmente não podem ser encontradas e ainda podem ser publicadas.

Como um exemplo desse caso, suponha que você tenha publicado um aplicativo de despesas usando uma URL interna de <http://myapps/expenses>, but the app uses the stylesheet <http://myapps/style.css>. Nesse caso, a folha de estilo não é publicada em seu aplicativo, portanto, carregar o aplicativo de despesas gera um erro 404 ao tentar carregar style.css. Nesse exemplo, o problema é resolvido ao publicar o aplicativo com uma URL interna de <http://myapp/>.

## <a name="problems-with-publishing-as-one-application"></a>Problemas com a publicação como um aplicativo

Se não for possível publicar todos os recursos dentro do mesmo aplicativo, será necessário publicar vários aplicativos e habilitar links entre eles.

Para fazer isso, é recomendável usar a solução de [domínios personalizados](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains). No entanto, essa solução requer que você tenha o certificado para seu domínio e que seus aplicativos usem nomes de domínio totalmente qualificados (FQDNs). Para outras opções, consulte a [documentação solucionar problemas de links desfeitos](https://microsoft-my.sharepoint.com/personal/harshja_microsoft_com/_layouts/15/guestaccess.aspx?guestaccesstoken=IxuG3mFVbnPWI3Yn4Qi7wCNi8VIfHS5mwPt5quh8DMw%3d&docid=2_14558cd6ddea34c1c9887dc640feb5831&rev=1).

## <a name="next-steps"></a>Próximas etapas
[Publicar aplicativos usando o Proxy de Aplicativo do AD do Azure](application-proxy-publish-azure-portal.md)
