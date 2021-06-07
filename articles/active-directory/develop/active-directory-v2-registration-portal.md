---
title: Referência do portal de registro de aplicativo | Azure
titleSuffix: Microsoft identity platform
description: Uma descrição dos recursos do portal de registro de aplicativo da Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: ryanwi
ms.reviewer: lenalepa
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 6a33da602eaa9bee20f155eaa550e558e5dcbeca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98755562"
---
# <a name="app-registration-reference"></a>Referência de registro de aplicativo

Este documento fornece contexto e descrições de vários recursos encontrados na experiência de [Registros de aplicativo](https://aka.ms/appregistrations) do portal do Azure.

## <a name="my-applications-or-converged-applications"></a>Meus aplicativos ou Aplicativos convergidos

Esta lista contém todos os seus aplicativos registrados para uso com a plataforma de identidade da Microsoft. Esses aplicativos permitem que usuários com contas pessoais da Microsoft e contas corporativas/de estudante do Azure Active Directory façam logon. Para saber mais sobre a plataforma de identidade da Microsoft, consulte a [visão geral v 2.0](./v2-overview.md). Esses aplicativos também podem ser usados para integração com o ponto de extremidade de autenticação da conta da Microsoft, `https://login.live.com`.

## <a name="azure-ad-only-applications"></a>Aplicativos somente do Azure AD

Esta lista contém todos os aplicativos registrados para uso com o ponto de extremidade do Azure AD v1.0. Esses aplicativos têm somente a capacidade de fazer logon dos usuários com contas corporativas/de estudante do Azure Active Directory. Esta lista inclui os aplicativos que foram registrados por meio da experiência de **Registros de aplicativo** no [portal do Azure](https://portal.azure.com).

## <a name="live-sdk-applications"></a>Aplicativos do Live SDK

Esta lista contém todos os aplicativos registrados para uso exclusivo com uma conta da Microsoft. Eles não são habilitados para uso com o Azure Active Directory. Nessa lista você encontrará todos os aplicativos registrados anteriormente com o portal do desenvolvedor MSA em `https://account.live.com/developers/applications`. Todas as funções executadas anteriormente em `https://account.live.com/developers/applications` agora podem ser executadas em [Registros de aplicativo](https://aka.ms/appregistrations).

## <a name="application-secrets"></a>Segredos do aplicativo

Os segredos do aplicativo são credenciais que permitem que seu aplicativo execute a [autenticação de cliente](https://tools.ietf.org/html/rfc6749#section-2.3) confiável com a plataforma de identidade da Microsoft. No OAuth e no OpenID Connect, o segredo do aplicativo é conhecido como `client_secret`. No protocolo v 2.0, qualquer aplicativo que recebe um token de segurança em um local endereçável da Web (usando um `https` esquema) deve usar um segredo de aplicativo para se identificar para a plataforma de identidade da Microsoft após o resgate desse token de segurança. Além disso, qualquer cliente nativo que receba os tokens em um dispositivo será proibido de usar o segredo do aplicativo para executar a autenticação do cliente. Isso desencoraja o armazenamento de segredos em ambientes inseguros.

Cada aplicativo pode conter dois segredos do aplicativo válidos em qualquer momento. Mantendo dois segredos, você pode executar a substituição de chave periódica em todo o ambiente de seu aplicativo. Depois de migrar todo o seu aplicativo para um novo segredo, você pode excluir o segredo antigo e provisionar um novo.

No momento, há permissão para apenas dois tipos de segredos do aplicativo no portal de registro do aplicativo. Escolher **Gerar Nova Senha** vai gerar e armazenar um segredo compartilhado no respectivo armazenamento de dados, que você pode usar em seu aplicativo. Escolher **gerar novo par de chaves** cria um novo par de chaves pública/privada que pode ser baixado e usado para autenticação de cliente na plataforma de identidade da Microsoft. Escolher **Carregar a chave pública** permite que você use seu próprio par de chaves públicas/privadas.
Você deve carregar um certificado que contenha uma chave pública.

## <a name="profile"></a>Perfil

A seção de perfil do portal de registro de aplicativos pode ser usada para personalizar a página de entrada de seu aplicativo. No momento, você pode alterar o logotipo do aplicativo, a URL dos termos de serviço e a URL da política de privacidade da página de entrada. O logotipo deve ser uma imagem transparente com 48 x 48 ou 50 x 50 pixels em um arquivo GIF, PNG ou JPEG com 15 KB ou menos. Tente alterar os valores e veja a página de entrada resultante!

## <a name="live-sdk-support"></a>Suporte ao Live SDK

Quando você habilita o "Suporte ao Live SDK", qualquer segredo do aplicativo criado por você será provisionado nos repositórios de dados do Azure AD e da conta da Microsoft. Isso permite que seu aplicativo se integre diretamente com o serviço de Conta da Microsoft (login.live.com). Caso deseje criar um aplicativo usando a conta Microsoft diretamente (em vez de usar o ponto de extremidade v2.0), verifique se o Suporte do Live SDK está habilitado.

Desativar o suporte do Live SDK garante que o segredo do aplicativo seja gravado apenas nos armazenamentos de dados do Azure AD. Os armazenamentos de dados do Azure AD incorporam regulamentos de nível empresarial que lhes permitem atender a determinados padrões, como a conformidade FISMA. Se você habilitar o suporte ao Live SDK, talvez seu aplicativo não fique em conformidade com alguns desses padrões.

Se você só pretende usar o ponto de extremidade v2.0, desabilite com segurança o suporte ao Live SDK.