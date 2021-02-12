---
title: Sincronização de diretório com Azure Active Directory
description: Diretrizes arquitetônicas sobre a obtenção de sincronização de diretório com o Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 748f91b2fe77667969e9736f8084a9dd24018425
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172462"
---
# <a name="directory-synchronization"></a>Sincronização de diretório

Muitas organizações têm uma infraestrutura híbrida que abrange os componentes locais e na nuvem. A sincronização de identidades de usuários entre diretórios locais e de nuvem permite que os usuários acessem recursos com um único conjunto de credenciais. 

A sincronização é o processo de 

* Criando um objeto com base em determinadas condições
* mantendo o objeto atualizado
* removendo o objeto quando as condições não forem mais atendidas. 

O provisionamento local envolve o provisionamento de fontes locais (como Active Directory) para Azure Active Directory (Azure AD). 

## <a name="use-when"></a>Usar quando

Você precisa sincronizar os dados de identidade de seus ambientes de Active Directory locais com o Azure AD.

![diagrama arquitetônico](./media/authentication-patterns/dir-sync-auth.png)

## <a name="components-of-system"></a>Componentes do sistema

* **Usuário**: acessa um aplicativo usando o Azure AD.

* **Navegador da Web**: o componente com o qual o usuário interage para acessar a URL externa do aplicativo.

* **Aplicativo**: aplicativo Web que depende do uso do Azure ad para fins de autenticação e autorização.

* **Azure ad**: sincroniza informações de identidade do diretório local da organização por meio de Azure ad Connect. 

* **Azure ad Connect**: uma ferramenta para se conectar a infraestruturas de identidade locais para Microsoft Azure AD. O assistente e experiências guiadas ajudam você a implantar e configurar pré-requisitos e componentes necessários para a conexão, incluindo sincronização e logon de diretórios ativos para o Azure AD. 

* **Active Directory**: Active Directory é um serviço de diretório incluído na maioria dos sistemas operacionais Windows Server. Os servidores que executam o Active Directory Domain Services (AD DS) são chamados de controladores de domínio. Eles autenticam e autorizam todos os usuários e computadores no domínio.

## <a name="implement-directory-synchronization-with-azure-ad"></a>Implementar a sincronização de diretórios com o Azure AD

* [O que é provisionamento de identidade?](../cloud-provisioning/what-is-provisioning.md) 

* [Ferramentas de integração de diretório de identidade híbrida](../hybrid/plan-hybrid-identity-design-considerations-tools-comparison.md) 

* [Roteiro de instalação do Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md)