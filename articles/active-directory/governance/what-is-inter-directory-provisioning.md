---
title: O que é provisionamento entre diretórios com o Azure Active Directory? | Microsoft Docs
description: Descreve a visão geral do provisionamento de identidade entre diretórios.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4246bc4c62fd8e5e73ff18e383b8bf115d25ede8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101643823"
---
# <a name="what-is-inter-directory-provisioning"></a>O que é provisionamento entre diretórios?

Um diretório é uma infraestrutura de informações compartilhadas usada para localizar, gerenciar, administrar e organizar itens e recursos de rede.  Exemplos de aplicativos que usam serviços de diretório são o Microsoft Active Directory e o Azure AD.  As identidades ajudam os sistemas de diretório a fazer determinações, como quem tem acesso a qual conteúdo e quem tem permissão para usar recursos específicos.

O provisionamento entre diretórios está provisionando uma identidade entre dois sistemas de serviços de diretório diferentes.   O cenário mais comum para o provisionamento entre diretórios é quando um usuário que já está no Active Directory é provisionado no Azure AD. Esse provisionamento pode ser realizado por agentes como a sincronização do Azure AD Connect ou o provisionamento de nuvem do Azure AD Connect.

O provisionamento entre diretórios nos permite criar ambientes de [identidade híbrida](../hybrid/whatis-hybrid-identity.md).


## <a name="what-types-of-inter-directory-provisioning-does-azure-ad-support"></a>A que tipos de provisionamento entre diretórios o Azure AD dá suporte

Atualmente, o Azure AD dá suporte a três métodos para realizar o provisionamento entre diretórios. Esses métodos são:

- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) – a ferramenta da Microsoft desenvolvida para atender à sua identidade híbrida e atingi-la, incluindo o provisionamento entre diretórios do Active Directory no Azure AD.

- O [Provisionamento de Nuvem do Azure AD Connect](../cloud-sync/what-is-cloud-sync.md) – um novo agente da Microsoft criado para atender às suas metas de identidade híbrida e atingi-las.  Ele fornece uma experiência de provisionamento entre diretórios leves entre o Active Directory e o Azure AD.

- [Microsoft Identity Manager](/microsoft-identity-manager/microsoft-identity-manager-2016) – solução de gerenciamento de acesso e identidade local da Microsoft que ajuda a gerenciar usuários, credenciais, políticas e acesso em sua organização. Além disso, o MIM fornece provisionamento entre diretórios avançado para obter ambientes de identidade híbrida para o Active Directory, o Azure AD e outros diretórios.

### <a name="key-benefits"></a>Principais benefícios

Essa funcionalidade de provisionamento entre diretórios oferece os seguintes benefícios de negócios significativos:

- [Sincronização de hash de senha](../hybrid/whatis-phs.md) – um método de entrada que sincroniza o hash da senha do AD local do usuário com o Azure AD.
- [Autenticação de passagem](../hybrid/how-to-connect-pta.md) – um método de entrada que permite que os usuários usem a mesma senha localmente e na nuvem, mas não exige a infraestrutura adicional de um ambiente federado.
- [Integração de federação](../hybrid/how-to-connect-fed-whatis.md) – pode ser usada para configurar um ambiente híbrido usando uma infraestrutura do AD FS local. Ela também fornece recursos de gerenciamento do AD FS, como renovação de certificado e implantações adicionais de servidor do AD FS.
- [Sincronização](../hybrid/how-to-connect-sync-whatis.md) – responsável pela criação de usuários, grupos e outros objetos.  Também é responsável por garantir que as informações de identidade dos usuários e grupos locais correspondam às da nuvem.  Essa sincronização também inclui os hashes de senha.
- [Monitoramento de Integridade](../hybrid/whatis-azure-ad-connect.md) – pode fornecer monitoramento robusto e fornecer um local central no portal do Azure para exibir essa atividade. 


## <a name="next-steps"></a>Próximas etapas 
- [O que é o gerenciamento do ciclo de vida de identidades](what-is-identity-lifecycle-management.md)
- [O que é provisionamento?](what-is-provisioning.md)
- [O que é provisionamento controlado por RH?](what-is-hr-driven-provisioning.md)
- [O que é provisionamento de aplicativos?](what-is-app-provisioning.md)