---
title: Azure AD Connect e federação | Microsoft Docs
description: Esta página é o local central para toda a documentação relativa às operações do AD FS que usam o Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/09/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a97142e0c512f4f95235ad08c94c852906d3efd8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92095849"
---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect e federação
O Azure Active Directory (Azure AD) Connect permite a você configurar a federação com os Serviços de Federação do Active Directory (AD FS) locais e o Azure AD. Com o logon federado, você pode habilitar os usuários a entrar em serviços baseados no Azure AD com suas senhas locais sem precisar digitar suas senhas novamente enquanto estiverem na rede corporativa. Usando a opção de federação com o AD FS, você pode implantar uma nova instalação do AD FS ou você pode especificar uma instalação existente em um farm do Windows Server 2012 R2.

Este tópico é a base das informações sobre funcionalidades relacionadas à Federação para o Azure AD Connect. Ele lista links para todos os tópicos relacionados. Para obter links para Azure AD Connect, consulte [integrando suas identidades locais com Azure Active Directory](whatis-hybrid-identity.md).

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect: tópicos sobre federação
| Tópico | O que ele abrange e quando deve ser lido |
|:--- |:--- |
| **Azure AD Connect opções de entrada do usuário** | |
| [Noções básicas sobre as opções de entrada do usuário](plan-connect-user-signin.md) |Aprenda sobre as diferentes opções de entrada do usuário e como elas afetam a experiência de entrada do usuário no Azure. |
| **Instalação do AD FS usando o Azure AD Connect** | |
| [Pré-requisitos](how-to-connect-install-custom.md#ad-fs-configuration-prerequisites) |Veja os pré-requisitos para uma instalação bem-sucedida do AD FS usando o Azure AD Connect. |
| [Configurar um farm do AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs) |Instale um novo farm do AD FS usando o Azure AD Connect. |
| [Federar com o Azure AD usando a ID de logon alternativa](how-to-connect-fed-management.md#alternateid) | Configurar a federação usando uma ID de logon alternativa  |
| **Modificar a configuração do AD FS** | |
| [Reparar a relação de confiança](how-to-connect-fed-management.md#repairthetrust) |Repare a relação de confiança atual entre o AD FS local e o Microsoft 365/Azure. |
| [Adicionar um novo servidor do AD FS](how-to-connect-fed-management.md#addadfsserver) |Expanda um farm do AD FS com um servidor do AD FS adicional após a instalação inicial. |
| [Adicionar um novo servidor WAP do AD FS](how-to-connect-fed-management.md#addwapserver) |Expanda um farm do AD FS com um servidor WAP (Proxy de Aplicativo Web) adicional após a instalação inicial. |
| [Adicionar um novo domínio federado](how-to-connect-fed-management.md#addfeddomain) |Adicione outro domínio para ser federado com o Azure AD. |
| [Atualizar o certificado TLS/SSL](how-to-connect-fed-ssl-update.md)| Atualize o certificado TLS/SSL para um farm de AD FS. |
| [Renovar certificados de Federação para Microsoft 365 e Azure AD](how-to-connect-fed-o365-certs.md)|Renovar seu certificado O365 com o Azure AD.|
| **Outra configuração de federação** | |
| [Federar várias instâncias do Azure AD com uma instância única do AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md) | Federar vários Azure ADs a um único farm do AD FS| 
| [Adicionar uma ilustração/logotipo personalizado da empresa](how-to-connect-fed-management.md#customlogo) |Modifique a experiência de entrada especificando o logotipo personalizado que é exibido na página de entrada do AD FS. |
| [Adicionar uma descrição de entrada](how-to-connect-fed-management.md#addsignindescription) |Altere a descrição de entrada na página de entrada do AD FS. |
| [Modificar AD FS regras de declaração](how-to-connect-fed-management.md#modclaims) |Modifique ou adicione regras de declaração do AD FS correspondentes à configuração de sincronização do Azure AD Connect. |


## <a name="additional-resources"></a>Recursos adicionais
* [Federando dois Azure ADs a um único AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md)
* [Implantação do AD FS no Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)
* [Implantação do AD FS de alta disponibilidade entre fronteiras geográficas no Azure com o Gerenciador de Tráfego do Azure](/windows-server/identity/ad-fs/deployment/active-directory-adfs-in-azure-with-azure-traffic-manager)