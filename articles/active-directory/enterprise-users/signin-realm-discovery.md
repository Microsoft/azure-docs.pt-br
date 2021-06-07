---
title: Pesquisa de nome de usuário durante a entrada – Azure Active Directory | Microsoft Docs
description: Como mensagens na tela refletem a pesquisa de nome de usuário durante a entrada no Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: overview
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ec804029919f0b9c4be26b044cd7a3eeddf6690
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96547824"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Descoberta de realm inicial para páginas de entrada do Azure Active Directory

Estamos alterando nosso comportamento de entrada do Azure AD (Azure Active Directory) para liberar espaço para novos métodos de autenticação e aprimorar a usabilidade. Durante a entrada, o Azure AD determina onde um usuário precisa se autenticar. O Azure AD toma decisões inteligentes lendo as configurações da organização e do usuário para o nome de usuário inserido na página de entrada. Este é um passo rumo a um futuro sem senha que permite credenciais adicionais como FIDO 2.0.

## <a name="home-realm-discovery-behavior"></a>Comportamento de descoberta de realm inicial

Historicamente, a descoberta de realm inicial foi regida pelo domínio que é fornecido na entrada ou por uma política de Descoberta de Realm Inicial para alguns aplicativos herdados. Por exemplo, em nosso comportamento de descoberta, um usuário do Azure Active Directory poderia digitar o nome de usuário incorretamente, mas ainda acessaria a tela de coleta de credenciais de sua organização. Isso ocorre quando o usuário fornece corretamente o nome de domínio da organização "contoso.com". Esse comportamento não permite a granularidade de personalizar experiências para um usuário individual.

Para dar suporte a uma variedade maior de credenciais e aumentar a usabilidade, o comportamento de pesquisa de nome de usuário do Azure Active Directory durante o processo de entrada foi atualizado. O novo comportamento toma decisões inteligentes lendo as configurações de nível da organização e do usuário com base no nome de usuário inserido na página de entrada. Para tornar isso possível, o Azure Active Directory verificará se o nome de usuário inserido na página de entrada existe no domínio especificado ou redireciona o usuário para fornecer as credenciais dele.

Um benefício adicional desse trabalho é o sistema de mensagens de erro aprimorado. Veja alguns exemplos do sistema de mensagens de erro aprimorado ao entrar em um aplicativo que dá suporte apenas a usuários do Azure Active Directory.

- O nome de usuário foi digitado incorretamente ou ainda não foi sincronizado com o Azure AD:
  
    ![O nome de usuário foi digitado incorretamente ou não foi encontrado](./media/signin-realm-discovery/typo-username.png)
  
- O nome de domínio foi digitado incorretamente:
  
    ![o nome de domínio foi digitado incorretamente ou não foi encontrado](./media/signin-realm-discovery/typo-domain.png)
  
- O usuário tenta entrar com um domínio de consumidor conhecido:
  
    ![entrar com um domínio de consumidor conhecido](./media/signin-realm-discovery/consumer-domain.png)
  
- A senha foi digitada incorretamente, mas o nome de usuário está correto:  
  
    ![a senha foi digitada incorretamente com um nome de usuário válido](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Esse recurso pode ter um impacto em domínios federados que dependem da Descoberta de Realm Inicial no nível de domínio antigo para forçar a federação. Para obter atualizações sobre quando o suporte a domínio federado será adicionado, confira [Descoberta de realm inicial durante a entrada para os serviços do Microsoft 365](https://azure.microsoft.com/updates/signin-hrd/). Enquanto isso, algumas organizações treinaram os funcionários para entrar com um nome de usuário que não existe no Azure Active Directory, mas contém o nome de domínio apropriado, pois os nomes de domínio roteiam os usuários no momento para o ponto de extremidade de domínio da organização. O novo comportamento de entrada não permite isso. O usuário é notificado para corrigir o nome de usuário e não tem permissão para entrar com um nome de usuário que não existe no Azure Active Directory.
>
> Se você ou sua organização tiver práticas que dependem do comportamento antigo, é importante que os administradores da organização atualizem a documentação de entrada e de autenticação de funcionários e os treinem para usar o nome de usuário do Azure Active Directory deles para entrar.
  
Se você tiver preocupações com o novo comportamento, deixe seus comentários na seção **Comentários** deste artigo.  

## <a name="next-steps"></a>Próximas etapas

[Personalizar sua identidade visual de entrada](../fundamentals/add-custom-domain.md)
