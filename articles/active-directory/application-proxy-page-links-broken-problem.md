---
title: "Os links na página não funcionam para um aplicativo de Proxy de Aplicativo | Microsoft Docs"
description: Como solucionar problemas com links desfeitos em aplicativos de Proxy de Aplicativo integrados com o Azure AD
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
ms.openlocfilehash: 83c4261fab0498541591c01f9bb692b396c7b751
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>Os links na página não funcionam para um aplicativo de Proxy de Aplicativo

Este artigo ajuda-o a solucionar problemas porque links no seu aplicativo de Proxy de Aplicativo do Azure Active Directory não funcionam corretamente.

## <a name="overview"></a>Visão geral 
Após publicar um aplicativo de Proxy de Aplicativo, os únicos links que funcionam por padrão no aplicativo são links para destinos contidos na URL raiz publicada. Os links nos aplicativos não estão funcionando, a URL interna para o aplicativo provavelmente não inclui todos os destinos de links dentro do aplicativo.

**Por que isso acontece?** Ao clicar em um link em um aplicativo, o Proxy de aplicativo tenta resolver a URL como uma URL interna dentro do mesmo aplicativo ou como uma URL disponível externamente. Se o link aponta para uma URL interna que não está dentro do mesmo aplicativo, ele não pertence a nenhum um desses buckets e resulta em um erro não encontrado.

## <a name="ways-you-can-resolve-broken-links"></a>Maneiras de resolver links desfeitos

Há três maneiras de resolver esse problema. As escolhas abaixo estão listadas em crescente complexidade.

1.  Certifique-se de se que a URL interna é uma raiz que contém todos os links relevantes para o aplicativo. Isso permite que todos os links sejam resolvidos como conteúdo publicado no mesmo aplicativo.

    Se você alterar a URL interna, mas não quiser alterar a página de aterrissagem para usuários, altere a URL da home page para a URL interna publicada anteriormente. Isso pode ser feito indo para "Azure Active Directory" -&gt; Registros de aplicativo -&gt; selecione o aplicativo -&gt; Propriedades. Neste guia de propriedades, você verá o campo "URL da Home Page" que pode ser ajustado para a página de aterrissagem desejada.

2.  Se seus aplicativos usam nomes de domínio totalmente qualificados (FQDNs), use [domínios personalizados](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) para publicar seus aplicativos. Esse recurso permite que a mesma URL seja usada internamente e externamente.

    Essa opção garante que os links em seu aplicativo sejam acessíveis externamente através do Proxy de Aplicativo, pois os links dentro do aplicativo para URLs internas também são reconhecidos externamente. Observe que todos os links ainda precisam pertencer a um aplicativo publicado. No entanto, com essa opção os links não precisam pertencer ao mesmo aplicativo e podem pertencer a vários aplicativos.

3.  Se nenhuma dessas opções for viável, você ingressa na visualização para um novo recurso que faz conversão/regravação de URL. Com essa opção, as URLs internas ou os links existentes no corpo HTML de seus aplicativos serão convertidos ou “mapeados” para URLs de Proxy de Aplicativo externas publicadas. Isso só funciona para links em HTML ou CSS, e não irá ajudar se seu link for gerado através de JS. 

Como um resultado, é altamente recomendável usar a solução [domínios personalizados](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains), se possível. Se você deseja ingressar na visualização, envie um email <aadapfeedback@microsoft.com> com o applicationId(s).

## <a name="next-steps"></a>Próximas etapas
[Trabalhar com servidores proxy locais existentes](application-proxy-working-with-proxy-servers.md)

