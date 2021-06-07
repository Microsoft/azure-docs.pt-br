---
title: Topologias e cenários com suporte de sincronização de nuvem Azure AD Connect
description: Saiba mais sobre as várias topologias locais e Azure Active Directory (Azure AD) que usam Azure AD Connect sincronização de nuvem.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fd14ed8d149cdc5296229c52ceb74afb2ce7b23
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98612971"
---
# <a name="azure-ad-connect-cloud-sync-supported-topologies-and-scenarios"></a>Topologias e cenários com suporte de sincronização de nuvem Azure AD Connect
Este artigo descreve várias topologias locais e Azure Active Directory (Azure AD) que usam Azure AD Connect sincronização de nuvem. Este artigo inclui apenas cenários e configurações com suporte.

> [!IMPORTANT]
> A Microsoft não dá suporte à modificação ou operação Azure AD Connect sincronização de nuvem fora das configurações ou ações documentadas formalmente. Qualquer uma dessas configurações ou ações pode resultar em um estado inconsistente ou sem suporte de Azure AD Connect sincronização de nuvem. Como resultado, a Microsoft não pode fornecer suporte técnico para essas implantações.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>Coisas a serem lembradas sobre todos os cenários e topologias
Veja a seguir uma lista de informações para ter em mente ao selecionar uma solução.

- Usuários e grupos devem ser identificados exclusivamente em todas as florestas
- A correspondência entre florestas não ocorre com a sincronização de nuvem
- Um usuário ou grupo deve ser representado apenas uma vez em todas as florestas
- A âncora de origem dos objetos é escolhida automaticamente.  Ele usa MS-DS-ConsistencyGuid, se presente, caso contrário, objectGUID será usado.
- Não é possível alterar o atributo usado para a âncora de origem.

## <a name="single-forest-single-azure-ad-tenant"></a>Floresta única, locatário único do Azure AD
![Diagrama que mostra a topologia de uma única floresta e um único locatário.](media/tutorial-single-forest/diagram-2.png)

A topologia mais simples é uma floresta local única, com um ou vários domínios, e um único locatário do Azure AD.  Para obter um exemplo desse cenário, consulte [tutorial: uma única floresta com um único locatário do Azure ad](tutorial-single-forest.md)


## <a name="multi-forest-single-azure-ad-tenant"></a>Locatário único do Azure AD de várias florestas
![Topologia para várias florestas e um único locatário](media/plan-cloud-provisioning-topologies/multi-forest-2.png)

Uma topologia comum é uma várias florestas do AD, com um ou vários domínios e um único locatário do Azure AD.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Floresta existente com Azure AD Connect, nova floresta com provisionamento de nuvem
![Diagrama que mostra a topologia de uma floresta existente e uma nova floresta.](media/tutorial-existing-forest/existing-forest-new-forest-2.png)

Esse cenário é a topologia semelhante ao cenário de várias florestas, no entanto, isso envolve um ambiente de Azure AD Connect existente e, em seguida, colocar uma nova floresta usando Azure AD Connect sincronização de nuvem.  Para obter um exemplo desse cenário, consulte [tutorial: uma floresta existente com um único locatário do Azure ad](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-sync-in-an-existing-hybrid-ad-forest"></a>Piloto Azure AD Connect sincronização de nuvem em uma floresta de AD híbrida existente
![Topologia para uma única floresta e um único locatário ](media/tutorial-migrate-aadc-aadccp/diagram-2.png) o cenário piloto envolve a existência de Azure ad Connect e Azure ad Connect sincronização de nuvem na mesma floresta e o escopo dos usuários e grupos de acordo. Observação: um objeto deve estar no escopo em apenas uma das ferramentas. 

Para obter um exemplo desse cenário, consulte [tutorial: piloto Azure ad Connect sincronização de nuvem em uma floresta do AD sincronizada existente](tutorial-pilot-aadc-aadccp.md)



## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é a sincronização de nuvem do Azure AD Connect?](what-is-cloud-sync.md)

