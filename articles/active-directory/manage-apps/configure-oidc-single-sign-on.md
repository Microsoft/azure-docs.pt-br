---
title: Entenda o SSO (logon único) baseado em OIDC para aplicativos no Azure Active Directory
description: Entenda o SSO (logon único) baseado em OIDC para aplicativos no Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2020
ms.author: kenwith
ms.reviewer: arajpathak7
ms.custom: contperf-fy21q2
ms.openlocfilehash: ffaa55d4aa482e8f0eda93b1b32db6310d17e2a3
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255242"
---
# <a name="understand-oidc-based-single-sign-on"></a>Entender o logon único baseado em OIDC
Na [série de guias de início rápido](view-applications-portal.md) sobre o gerenciamento de aplicativos, você aprendeu a usar o Azure ad como o IDP (provedor de identidade) para um aplicativo. Este artigo apresenta mais detalhes sobre os aplicativos que usam o padrão OpenID Connect para implementar o logon único. 

## <a name="before-you-begin"></a>Antes de começar
O processo de adicionar um aplicativo ao seu locatário de Azure Active Directory depende do tipo de logon único implementado pelo aplicativo. Para saber mais sobre as opções de logon único disponíveis para aplicativos que podem usar o Azure AD para gerenciamento de identidade, consulte [Opções de logon único](sso-options.md). Este artigo aborda os aplicativos baseados em OIDC.


## <a name="basic-oidc-configuration"></a>Configuração básica de OIDC
Na [série de início rápido](add-application-portal-setup-oidc-sso.md), há um artigo sobre como configurar o logon único. Nele, você aprende a adicionar um aplicativo baseado em OIDC ao seu locatário do Azure.

A boa coisa com a adição de um aplicativo que usa o padrão OIDC para logon único é que a configuração é mínima. Aqui está um breve vídeo que mostra como adicionar um aplicativo baseado em OIDC ao seu locatário.

Adicionar um aplicativo baseado em OIDC no Azure Active Directory

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

Para saber mais sobre o consentimento do usuário e do administrador, confira [Entender o consentimento do usuário e do administrador](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent).

## <a name="next-steps"></a>Próximas etapas

- [Série de Início Rápido no Gerenciamento de Aplicativos](add-application-portal-setup-oidc-sso.md)
- [Opções de logon único](sso-options.md)
- [Como: Entrar em qualquer usuário do Azure Active Directory usando o padrão de aplicativo multilocatário](../develop/howto-convert-app-to-be-multi-tenant.md)
