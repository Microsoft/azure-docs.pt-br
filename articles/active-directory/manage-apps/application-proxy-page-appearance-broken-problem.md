---
title: A página do aplicativo não é exibida corretamente para o aplicativo de proxy de aplicativo | Microsoft Docs
description: Diretrizes quando a página não está sendo exibida corretamente em um Aplicativo de Proxy de Aplicativo que você integrou com o Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13e73f0ed56648ce162f00d6df5e7b86a922ca01
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381420"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Página de aplicativo não exibe corretamente para um aplicativo de Proxy de aplicativo

Este artigo te ajuda a solucionar problemas com aplicativos de Proxy de Aplicativo do Azure Active Directory quando você navegar para a página, mas algo na página não parece correto.

## <a name="overview"></a>Visão geral
Quando você publica um aplicativo de Proxy de aplicativo, apenas as páginas em sua raiz são acessíveis ao acessar o aplicativo. Se a página não estiver exibindo corretamente, a URL interna raiz usada para o aplicativo pode estar em falta de alguns recursos de página. Para resolver, certifique-se de ter publicado *todos* os recursos da página como parte do seu aplicativo.

Você pode verificar se os recursos faltantes são o problema, abrindo o rastreador de rede (como o Fiddler ou ferramentas F12 no Internet Explorer/Microsoft Edge), carregando a página e procurando por erros 404. Isso indica as páginas atualmente não podem ser encontradas e que você precisa publicá-las.

Como um exemplo desse caso, suponha que você tenha publicado um aplicativo de despesas usando a URL interna de `http://myapps/expenses`, mas o aplicativo usa a folha de estilos `http://myapps/style.css`. Nesse caso, a folha de estilo não é publicada em seu aplicativo, portanto, carregar o aplicativo de despesas gera um erro 404 ao tentar carregar style.css. Nesse exemplo, o problema é resolvido ao publicar o aplicativo com uma URL interna de `http://myapp/`.

## <a name="problems-with-publishing-as-one-application"></a>Problemas com a publicação como um aplicativo

Se não for possível publicar todos os recursos dentro do mesmo aplicativo, será necessário publicar vários aplicativos e habilitar links entre eles.

Para fazer isso, é recomendável usar a solução de [domínios personalizados](application-proxy-configure-custom-domain.md). No entanto, essa solução requer que você tenha o certificado para seu domínio e que seus aplicativos usem nomes de domínio totalmente qualificados (FQDNs). Para outras opções, consulte a [documentação solucionar problemas de links desfeitos](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>Próximas etapas
[Publicar aplicativos usando o Proxy de Aplicativo do AD do Azure](application-proxy-add-on-premises-application.md)
