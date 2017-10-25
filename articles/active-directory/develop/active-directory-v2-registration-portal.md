---
title: "Tópicos de ajuda do Portal de Registro de Aplicativos | Microsoft Docs"
description: "Uma descrição de vários recursos no Portal de Registro de Aplicativos da Microsoft."
services: active-directory
documentationcenter: 
author: lnalepa
manager: mbaldwin
editor: 
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2016
ms.author: lenalepa
ms.custom: aaddev
ms.openlocfilehash: c60499c425a7fd800f7ca9a5bac1fed5af73b801
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="app-registration-reference"></a>Referência de registro de aplicativo
Este documento fornece contexto e descrições de vários recursos encontrados no Portal de Registro de Aplicativos da Microsoft [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).

## <a name="my-applications"></a>Meus Aplicativos
Esta lista contém todos os aplicativos registrados para uso com o ponto de extremidade v2.0 do Azure AD.  Esses aplicativos permitem que usuários com contas pessoais da Microsoft e contas corporativas/de estudante do Azure Active Directory façam logon.  Para saber mais sobre o ponto de extremidade v2.0 do Azure AD, consulte nossa [Visão geral do v 2.0](active-directory-appmodel-v2-overview.md).  Esses aplicativos também podem ser usados para integração com o ponto de extremidade de autenticação da conta da Microsoft, `https://login.live.com`.

## <a name="live-sdk-applications"></a>Aplicativos do Live SDK
Esta lista contém todos os aplicativos registrados para uso exclusivo com uma conta da Microsoft.  Eles não são habilitados para uso com o Azure Active Directory ou outro.  Nessa lista você encontrará todos os aplicativos registrados anteriormente com o portal do desenvolvedor MSA em `https://account.live.com/developers/applications`.  Todas as funções executadas anteriormente em `https://account.live.com/developers/applications` podem ser executadas agora nesse novo portal, `https://apps.dev.microsoft.com`.  Se você tiver outras perguntas sobre seus aplicativos da conta da Microsoft, entre em contato conosco.

## <a name="application-secrets"></a>Segredos do aplicativo
Segredos do aplicativo são credenciais que permitem ao aplicativo executar a [autenticação de cliente](http://tools.ietf.org/html/rfc6749#section-2.3) confiável com o Azure AD.  No OAuth e no OpenID Connect, os segredos do aplicativo são conhecidos como `client_secret`.  No protocolo v 2.0, qualquer aplicativo que receba um token de segurança em um local endereçável da Web (usando um esquema `https` ) deve usar um segredo do aplicativo para se identificar ao Azure AD durante o resgate desse token de segurança.  Além disso, qualquer cliente nativo que receba os tokens em um dispositivo será proibido de usar o segredo do aplicativo para executar a autenticação do cliente, para evitar o armazenamento de segredos em ambientes inseguros.

Cada aplicativo pode conter dois segredos do aplicativo válidos em qualquer momento.  Mantendo dois segredos, você pode executar a substituição de chave periódica em todo o ambiente de seu aplicativo.  Depois de migrar todo o seu aplicativo para um novo segredo, você pode excluir o segredo antigo e provisionar um novo.

No momento, há permissão para apenas dois tipos de segredos do aplicativo no portal de registro do aplicativo.  Escolher **Gerar Nova Senha** vai gerar e armazenar um segredo compartilhado no armazenamento de dados respectivo, que você pode usar em seu aplicativo.  Escolher **Gerar Novo Par de Chaves** criará um novo par de chaves público/privado que pode ser baixado e usado para autenticação de cliente no Azure AD.

## <a name="profile"></a>Perfil
A seção de perfil do portal de registro de aplicativos pode ser usada para personalizar a página de entrada de seu aplicativo.  No momento, você pode alterar o logotipo do aplicativo, a URL dos termos de serviço e a política de privacidade da página de entrada.  O logotipo deve ser uma imagem transparente com 48 x 48 ou 50 x 50 pixels em um arquivo GIF, PNG ou JPEG com 15 KB ou menos.  Tente alterar os valores e veja a página de entrada resultante!

## <a name="live-sdk-support"></a>Suporte ao Live SDK
Quando você habilita o "Suporte ao Live SDK", qualquer segredo do aplicativo criado por você será provisionado nos repositórios de dados do Azure AD e da conta da Microsoft.  Isso permitirá que seu aplicativo se integre diretamente com o serviço de Conta da Microsoft (login.live.com).  Se você quiser criar um aplicativo usando a Conta da Microsoft diretamente (em vez de usar o ponto de extremidade v2.0 do Azure AD), certifique-se de que o Suporte do Live SDK esteja habilitado.

Desativar o suporte do Live SDK garantirá que o segredo do aplicativo seja gravado apenas nos armazenamentos de dados do Azure AD.  Os armazenamentos de dados do Azure AD incorporam regulamentos de nível empresarial que lhes permitem atender a determinados padrões, como a conformidade FISMA.  Se você habilitar o suporte ao Live SDK, talvez seu aplicativo não fique em conformidade com alguns desses padrões.

Se você planeja usar o ponto de extremidade v2.0 do Azure AD, desabilite com segurança o suporte ao Live SDK.

