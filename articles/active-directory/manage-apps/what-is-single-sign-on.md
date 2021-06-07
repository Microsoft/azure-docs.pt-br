---
title: O que é SSO (logon único) do Azure?
description: Saiba como o SSO (logon único) funciona com o Azure Active Directory. Use o SSO para que os usuários não precisem se lembrar das senhas de todos os aplicativos. Além disso, use o SSO para simplificar a administração do gerenciamento de contas.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: overview
ms.date: 12/03/2019
ms.author: iangithinji
ms.reviewer: arvindh, japere
ms.custom: contperf-fy21q1
ms.openlocfilehash: 65e4b32c7c41eea6a885a09dd7ad772980595a9e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379495"
---
# <a name="what-is-single-sign-on-sso"></a>O que é SSO (logon único)?

Logon único significa que o usuário não precisa entrar em todos os aplicativos que usa. O usuário faz logon uma vez e essa credencial também é usada para outros aplicativos.

Se você é um usuário final, provavelmente não se preocupa muito com os detalhes do SSO. Você só quer usar os aplicativos que aumentam sua produtividade sem precisar digitar tantas senhas. Você pode encontrar seus aplicativos em: https://myapps.microsoft.com.
 
Se você é um administrador ou profissional de TI, continue lendo para saber mais sobre o SSO e como ele é implementado no Azure.

## <a name="single-sign-on-basics"></a>Noções básicas sobre o logon único
O logon único representa um grande avanço na maneira como os usuários entram nos aplicativos e os utilizam. Os sistemas de autenticação baseados em logon único costumam ser chamados de "autenticação moderna". A autenticação moderna e o logon único se enquadram em uma categoria de computação chamada IAM (gerenciamento de identidade e acesso). Para entender o que torna o logon único possível, confira este vídeo.

Conceitos básicos de autenticação: Noções básicas | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/fbSVgC8nGz4]

## <a name="single-sign-on-with-web-applications"></a>Logon único com aplicativos Web
Os aplicativos Web são incrivelmente populares. Os aplicativos Web são hospedados por várias empresas e disponibilizados como um serviço. Alguns exemplos populares de aplicativos Web incluem Microsoft 365, GitHub e Salesforce, e há milhares de outros. As pessoas acessam aplicativos Web usando um navegador da Web no computador. O logon único possibilita que as pessoas naveguem entre os vários aplicativos Web sem precisar entrar várias vezes.

Para saber mais sobre como o logon único funciona com aplicativos Web, confira esses dois vídeos.

Conceitos básicos de autenticação: Aplicativos Web | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/tCNcG1lcCHY]

Conceitos básicos de autenticação: Logon único na Web | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/51B-jSOBF8U]

## <a name="cloud-versus-on-premises-hosted-apps"></a>Aplicativos hospedados na nuvem ou locais
A maneira como você implementa o logon único depende do local em que o aplicativo está hospedado. A hospedagem é importante devido à maneira como o tráfego de rede é roteado para acessar o aplicativo. Se um aplicativo é hospedado e acessado usando a rede local, ou seja, é um aplicativo local, não há necessidade de os usuários acessarem a Internet para utilizá-lo. Se o aplicativo é hospedado em outro lugar, ou seja, é um aplicativo hospedado na nuvem, os usuários precisam acessar a Internet para utilizá-lo.

> [!TIP]
> Os aplicativos hospedados na nuvem também são chamados de aplicativos SaaS (software como serviço). 

O logon único para aplicativos hospedados na nuvem é simples. Você permite que o provedor de identidade saiba que está sendo usado para o aplicativo. E, em seguida, você configura o aplicativo para confiar no provedor de identidade. Para saber como usar o Azure AD como um provedor de identidade para um aplicativo, consulte a [Série de guias de início rápido sobre Gerenciamento de Aplicativos](add-application-portal.md).

> [!TIP]
> Os termos nuvem e Internet costumam ser usados de maneira intercambiável. O motivo disso está relacionado aos diagramas de rede. É comum representar grandes redes de computadores com uma forma de nuvem em um diagrama, pois não é viável desenhar todos os componentes. A Internet é a rede mais conhecida e, portanto, é fácil usar os termos de maneira intercambiável. No entanto, qualquer rede de computadores pode ser chamada de nuvem.

Você também pode usar o logon único para aplicativos com base local. A tecnologia para fazer o SSO local acontecer é chamada de Proxy de Aplicativo. Para saber mais sobre isso, confira [Opções de logon único](sso-options.md).

## <a name="multiple-identity-providers"></a>Vários provedores de identidade
Quando você configura o logon único para trabalhar entre vários provedores de identidade, isso é chamado de federação. Para saber como a federação funciona, confira este vídeo.

Conceitos básicos de autenticação: Federação | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/CjarTgjKcX8]


## <a name="next-steps"></a>Próximas etapas
* [Série de Início Rápido no Gerenciamento de Aplicativos](view-applications-portal.md)
* [Opções de logon único](sso-options.md)
