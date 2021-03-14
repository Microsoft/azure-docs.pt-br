---
title: Introdução à proteção de contas de serviço Azure Active Directory
description: Explicação dos tipos de contas de serviço disponíveis no Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 049025a5d871f1dd26e5dab498756aa44d2ebfe2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692985"
---
# <a name="introduction-to-securing-azure-service-accounts"></a>Introdução à proteção de contas de serviço do Azure

Há três tipos de contas de serviço nativas para Azure Active Directory: identidades gerenciadas, entidades de serviço e contas de serviço baseadas em usuário. As contas de serviço são um tipo especial de conta que pretende representar uma entidade não-humana, como um aplicativo, uma API ou outro serviço. Essas entidades operam dentro do contexto de segurança fornecido pela conta de serviço. 

## <a name="types-of-azure-active-directory-service-accounts"></a>Tipos de contas de serviço Azure Active Directory

Para serviços hospedados no Azure, é recomendável usar uma identidade gerenciada, se possível, e uma entidade de serviço, se não houver. Identidades gerenciadas não podem ser usadas para serviços hospedados fora do Azure. Nesse caso, recomendamos uma entidade de serviço. Se você puder usar uma identidade gerenciada ou uma entidade de serviço, faça isso. Recomendamos que você não use uma conta de usuário Azure Active Directory como uma entidade de serviço. Consulte a tabela a seguir para obter um resumo.
 

| Hospedagem do serviço| Identidade gerenciada| Entidade de serviço| Conta de usuário do Azure |
| - | - | - | - |
|O serviço está hospedado no Azure.| Sim. <br>Recomendado se o serviço <br>dá suporte a uma identidade gerenciada.| Sim.| Não recomendado. |
| O serviço não está hospedado no Azure.| Não| Sim. Recomendável.| Não recomendado. |
| O serviço é multilocatário| Não| Sim. Recomendável.| Não. |


## <a name="managed-identities"></a>Identidades gerenciadas

Identidades gerenciadas são identidades de Azure Active Directory seguras (Azure AD) criadas para fornecer identidades para recursos do Azure. Há [dois tipos de identidades gerenciadas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#managed-identity-types): 
 
* Identidades gerenciadas atribuídas pelo sistema podem ser atribuídas diretamente a uma instância de um serviço. 

* Identidades gerenciadas atribuídas pelo usuário podem ser criadas como um recurso autônomo. 

Para obter mais informações, consulte [protegendo identidades gerenciadas](service-accounts-managed-identities.md). Para obter informações gerais sobre identidades gerenciadas, consulte [o que são identidades gerenciadas para recursos do Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

## <a name="service-principals"></a>Entidades de serviço

Se você não puder usar uma identidade gerenciada para representar seu aplicativo, use uma entidade de serviço. As entidades de serviço podem ser usadas com aplicativos de locatário único e multilocatário. 

Uma entidade de serviço é a representação local de um objeto de aplicativo em um único locatário do Azure AD. Ele funciona como a identidade da instância do aplicativo, define quem pode acessar o aplicativo e quais recursos o aplicativo pode acessar. Uma entidade de serviço é criada em (local para) cada locatário em que o aplicativo é usado e faz referência ao objeto de aplicativo exclusivo globalmente. O locatário protege a entrada da entidade de serviço e o acesso aos recursos.

Há dois mecanismos para autenticação usando entidades de serviço — certificados de cliente e segredos de cliente. Os certificados são mais seguros: Use certificados de cliente, se possível. Ao contrário dos segredos do cliente, os certificados de cliente não podem ser inseridos acidentalmente no código.

Para obter informações sobre como proteger entidades de serviço, consulte Protegendo entidades de serviço.

 
## <a name="next-steps"></a>Próximas etapas


Para obter mais informações sobre como proteger contas de serviço do Azure, consulte:

[Protegendo identidades gerenciadas](service-accounts-managed-identities.md)

[Protegendo entidades de serviço](service-accounts-principal.md)

[Governando contas de serviço do Azure](service-accounts-governing-azure.md)



