---
title: "Azure AD Connect e federação | Microsoft Docs"
description: "Esta página é o local central para toda a documentação relativa às operações do AD FS que usam o Azure AD Connect."
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: anandy
ms.openlocfilehash: 6822320c92d106d28607289a90f2f08a51e04070
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect e federação
O Azure Active Directory (Azure AD) Connect permite a você configurar a federação com os Serviços de Federação do Active Directory (AD FS) locais e o Azure AD. Com o logon federado, você pode habilitar os usuários a entrar em serviços baseados no Azure AD com suas senhas locais sem precisar digitar suas senhas novamente enquanto estiverem na rede corporativa. Usando a opção de federação com o AD FS, você pode implantar uma nova instalação do AD FS ou você pode especificar uma instalação existente em um farm do Windows Server 2012 R2.

Este tópico é a base das informações sobre funcionalidades relacionadas à Federação para o Azure AD Connect. Ele lista links para todos os tópicos relacionados. Para obter links para o Azure AD Connect, confira [Integrando suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect: tópicos sobre federação
| Tópico | O que ele abrange e quando deve ser lido |
|:--- |:--- |
| **Opções de entrada de usuário do Azure AD Connect** | |
| [Noções básicas sobre as opções de entrada do usuário](active-directory-aadconnect-user-signin.md) |Aprenda sobre as diferentes opções de entrada do usuário e como elas afetam a experiência de entrada do usuário no Azure. |
| **Instalação do AD FS usando o Azure AD Connect** | |
| [Pré-requisitos](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) |Veja os pré-requisitos para uma instalação bem-sucedida do AD FS usando o Azure AD Connect. |
| [Configurar um farm do AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) |Instale um novo farm do AD FS usando o Azure AD Connect. |
| [Federar ao Azure AD usando uma ID de logon alternativa ](active-directory-aadconnect-federation-management.md#alternateid) | Configurar a federação usando uma ID de logon alternativa  |
| **Modificar a configuração do AD FS** | |
| [Reparar a relação de confiança](active-directory-aadconnect-federation-management.md#repairthetrust) |Repare a confiança atual entre o AD FS local e o Office 365/Azure. |
| [Adicionar um novo servidor do AD FS](active-directory-aadconnect-federation-management.md#addadfsserver) |Expanda um farm do AD FS com um servidor do AD FS adicional após a instalação inicial. |
| [Adicionar um novo servidor WAP do AD FS](active-directory-aadconnect-federation-management.md#addwapserver) |Expanda um farm do AD FS com um servidor WAP (Proxy de Aplicativo Web) adicional após a instalação inicial. |
| [Adicionar um novo domínio federado](active-directory-aadconnect-federation-management.md#addfeddomain) |Adicione outro domínio para ser federado com o Azure AD. |
| [Atualizar o certificado SSL](active-directory-aadconnectfed-ssl-update.md)| Atualize o certificado SSL para um farm do AD FS. |
| **Outra configuração de federação** | |
| [Federar várias instâncias do Azure AD com uma única instância do AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md) | Federar vários Azure ADs a um único farm do AD FS| 
| [Adicionar uma ilustração/logotipo personalizado da empresa](active-directory-aadconnect-federation-management.md#customlogo) |Modifique a experiência de entrada especificando o logotipo personalizado que é exibido na página de entrada do AD FS. |
| [Adicionar uma descrição de entrada](active-directory-aadconnect-federation-management.md#addsignindescription) |Altere a descrição de entrada na página de entrada do AD FS. |
| [Modificar regras de declaração do AD FS](active-directory-aadconnect-federation-management.md#modclaims) |Modifique ou adicione regras de declaração do AD FS correspondentes à configuração de sincronização do Azure AD Connect. |


## <a name="additional-resources"></a>Recursos adicionais
* [Federando dois Azure ADs a um único AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md)
* [Implantação do AD FS no Azure](active-directory-aadconnect-azure-adfs.md)
* [Implantação do AD FS de alta disponibilidade entre fronteiras geográficas no Azure com o Gerenciador de Tráfego do Azure](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)
