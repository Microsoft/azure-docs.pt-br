---
title: Dependências do serviço de acesso condicional-Azure Active Directory
description: Saiba como as condições são usadas em Azure Active Directory acesso condicional para disparar uma política.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6592add5e33ba240c0f1d9fdbd23d82adfe5229
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91258601"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>O que são dependências de serviço no Azure Active Directory acesso condicional? 

Com as políticas de acesso condicional, você pode especificar os requisitos de acesso para sites e serviços. Por exemplo, seus requisitos de acesso podem incluir a exigência de autenticação multifator (MFA) ou de [dispositivos gerenciados](require-managed-devices.md). 

Quando você acessa um site ou serviço diretamente, o impacto de uma política relacionada normalmente é fácil de avaliar. Por exemplo, se você tiver uma política que exija o MFA (autenticação multifator) para o SharePoint Online configurado, a MFA será imposta para cada entrada no portal da Web do SharePoint. No entanto, nem sempre é um avanço direto para avaliar o impacto de uma política, pois há aplicativos de nuvem com dependências para outros aplicativos de nuvem. Por exemplo, o Microsoft Teams pode fornecer acesso a recursos no SharePoint Online. Portanto, ao acessar o Microsoft Teams em nosso cenário atual, você também está sujeito à política do SharePoint MFA. 

> [!TIP]
> Usar o aplicativo do [office 365](concept-conditional-access-cloud-apps.md#office-365) direcionará todos os aplicativos do Office para evitar problemas com dependências de serviço na pilha do Office.

## <a name="policy-enforcement"></a>Aplicação de políticas 

Se você tiver uma dependência de serviço configurada, a política poderá ser aplicada usando a aplicação de ligação antecipada ou de associação tardia. 

- A **imposição de diretiva de ligação antecipada** significa que um usuário deve satisfazer a política de serviço dependente antes de acessar o aplicativo de chamada. Por exemplo, um usuário deve satisfazer a política do SharePoint antes de entrar no MS Teams. 
- A **imposição de política de ligação tardia** ocorre depois que o usuário entra no aplicativo de chamada. A imposição é adiada ao chamar solicitações de aplicativo, um token para o serviço downstream. Exemplos incluem MS Teams acessando o Planner e o Office.com acessando o SharePoint. 

O diagrama a seguir ilustra as dependências do MS Teams Service. Setas sólidas indicam a imposição de ligação inicial a seta tracejada para o planejador indica a imposição de associação tardia. 

![Dependências de serviço do MS Teams](./media/service-dependencies/01.png)

Como prática recomendada, você deve definir políticas comuns entre aplicativos e serviços relacionados sempre que possível. Ter uma postura de segurança consistente fornece a melhor experiência do usuário. Por exemplo, a definição de uma política comum no Exchange Online, no SharePoint Online, no Microsoft Teams e no Skype for Business reduz significativamente as solicitações inesperadas que podem surgir de políticas diferentes sendo aplicadas aos serviços downstream. 

Uma ótima maneira de fazer isso com aplicativos na pilha do Office é usar o [aplicativo do office 365](concept-conditional-access-cloud-apps.md#office-365) , em vez de direcionar aplicativos individuais.

A tabela abaixo lista as dependências de serviço adicionais, onde os aplicativos cliente devem satisfazer  

| Aplicativos cliente         | Serviço downstream                          | Imposição |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Gerenciamento de Microsoft Azure (portal e API) | Associação antecipada |
| Sala de aula da Microsoft | Exchange                                    | Associação antecipada |
|                     | SharePoint                                  | Associação antecipada |
| Microsoft Teams     | Exchange                                    | Associação antecipada |
|                     | Planejador MS                                  | Associação tardia  |
|                     | Microsoft Stream                            | Associação tardia  |
|                     | SharePoint                                  | Associação antecipada |
|                     | Skype for Business Online                   | Associação antecipada |
| Portal do Office       | Exchange                                    | Associação tardia  |
|                     | SharePoint                                  | Associação tardia  |
| Grupos do Outlook      | Exchange                                    | Associação antecipada |
|                     | SharePoint                                  | Associação antecipada |
| PowerApps           | Gerenciamento de Microsoft Azure (portal e API) | Associação antecipada |
|                     | Windows Azure Active Directory              | Associação antecipada |
| Project             | Dynamics CRM                                | Associação antecipada |
| Skype for Business  | Exchange                                    | Associação antecipada |
| Visual Studio       | Gerenciamento de Microsoft Azure (portal e API) | Associação antecipada |
| Microsoft Forms     | Exchange                                    | Associação antecipada |
|                     | SharePoint                                  | Associação antecipada |
| Microsoft To-Do     | Exchange                                    | Associação antecipada |

## <a name="next-steps"></a>Próximas etapas

Para saber como implementar o Acesso Condicional em seu ambiente, confira [Planejar a implantação do Acesso Condicional no Azure Active Directory](plan-conditional-access.md).
