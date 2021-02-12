---
title: Verificação em duas etapas do Azure AD MFA e ADFS-Azure Active Directory
description: Esta é a página de autenticação multifator do Azure AD que descreve como começar a usar o Azure AD MFA e AD FS.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0023d40fdc26fa1c42a67ce78a9259643098abb
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741398"
---
# <a name="getting-started-with-azure-ad-multi-factor-authentication-and-active-directory-federation-services"></a>Introdução à autenticação multifator do Azure AD e Serviços de Federação do Active Directory (AD FS)

<center>

![Azure AD MFA e introdução ao ADFS](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Se sua organização tiver federado suas Active Directory locais com Azure Active Directory usando AD FS, há duas opções para usar a autenticação multifator do Azure AD.

* Proteger recursos de nuvem usando a autenticação multifator do Azure AD ou Serviços de Federação do Active Directory (AD FS)
* Proteger os recursos de nuvem e locais usando o Servidor de Autenticação Multifator do Azure

A tabela a seguir resume a experiência de verificação entre a proteção de recursos com a autenticação multifator do Azure AD e o AD FS

| Experiência de verificação – Aplicativos baseados em navegador | Experiência de verificação – Aplicativos não baseados em navegador |
|:--- |:--- |
| Protegendo os recursos do Azure AD usando a autenticação multifator do Azure AD |<li>A primeira etapa de verificação é executada localmente usando o AD FS.</li> <li>A segunda etapa é um método baseado em telefone executado com a autenticação na nuvem.</li> |
| Protegendo recursos do AD do Azure usando os Serviços de Federação do Active Directory |<li>A primeira etapa de verificação é executada localmente usando o AD FS.</li><li>A segunda etapa é realizada localmente, cumprindo a declaração.</li> |

Advertências sobre senhas de aplicativo para usuários federados:

* Senhas de aplicativo são verificadas usando a autenticação na nuvem e, portanto, ignora as federações. A federação só é usada ativamente ao configurar a senha de aplicativo.
* As configurações do Controle de Acesso do Cliente local não são consideradas pela Senha de aplicativo.
* Você perde a capacidade de registro de autenticação local para senha de aplicativo.
* A desabilitação/exclusão da conta pode levar até três horas para a sincronização do diretório, atrasando a desabilitação/exclusão da senha de aplicativo na identidade da nuvem.

Para obter informações sobre como configurar a autenticação multifator do Azure AD ou o Servidor de Autenticação Multifator do Azure com o AD FS, consulte os seguintes artigos:

* [Proteger recursos de nuvem usando a autenticação multifator do Azure AD e o AD FS](howto-mfa-adfs.md)
* [Proteger recursos de nuvem e locais usando o Servidor de Autenticação Multifator do Azure com o AD FS do Windows Server 2012 R2](howto-mfaserver-adfs-2012.md)
* [Proteger recursos de nuvem e locais usando o Servidor de Autenticação Multifator do Azure com AD FS 2.0](howto-mfaserver-adfs-2.md)
