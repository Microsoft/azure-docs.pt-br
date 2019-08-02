---
title: Quais são as dependências de serviço no acesso condicional do Azure Active Directory? | Microsoft Docs
description: Saiba como as condições são usadas no acesso condicional do Azure Active Directory para disparar uma política.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9aca2e4ea5e107358ff72e83562057830ece2cc
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509360"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Quais são as dependências de serviço no acesso condicional do Azure Active Directory? 

Com as políticas de acesso condicional, você pode especificar os requisitos de acesso a sites e serviços. Por exemplo, os requisitos de acesso podem incluir a exigir a autenticação multifator (MFA) ou [dispositivos gerenciados](require-managed-devices.md). 

Quando você acessa um site ou serviço diretamente, o impacto de uma política relacionada é normalmente fácil avaliar. Por exemplo, se você tiver uma política que exige o MFA para o SharePoint Online configurada, a MFA é imposta para cada entrada no portal da web do SharePoint. No entanto, nem sempre é simples para avaliar o impacto de uma política porque há aplicativos de nuvem com dependências de outros aplicativos de nuvem. Por exemplo, Microsoft Teams pode fornecer acesso aos recursos no SharePoint Online. Assim, quando você acessar o Microsoft Teams em nosso cenário atual, você também está sujeito à política de MFA do SharePoint.   

## <a name="policy-enforcement"></a>Aplicação de políticas 

Se você tiver uma dependência do serviço configurada, a política pode ser aplicada usando imposição early bound ou associação tardia. 

- **Imposição de política de early bound** significa que um usuário deve satisfazer a política de serviço dependentes antes de acessar o aplicativo de chamada. Por exemplo, um usuário deve satisfazer a política do SharePoint antes de entrar no MS Teams. 
- **Imposição de política de associação tardia** ocorre após o usuário entra no aplicativo da chamada. Imposição é adiada para ao chamar as solicitações de aplicativo, um token para o serviço downstream. Os exemplos incluem o MS Teams acessando Planner e Office.com acessando o SharePoint. 

O diagrama a seguir ilustra as dependências de serviço do MS Teams. As setas sólidas indicam early bound imposição a seta tracejada Planejador indica a imposição de associação tardia. 

![Dependências de serviço do MS Teams](./media/service-dependencies/01.png)

Como prática recomendada, você deve definir políticas comuns em aplicativos relacionados e serviços sempre que possível. Ter uma postura de segurança consistente fornece a melhor experiência de usuário. Por exemplo, definir uma diretiva comum entre o Exchange Online, SharePoint Online, Microsoft Teams e Skype para empresas significativamente reduz prompts inesperados que podem surgir da políticas diferentes sendo aplicadas aos serviços downstream. 

A tabela a seguir lista as dependências de serviço adicionais, onde os aplicativos cliente devem satisfazer  

| Aplicativos cliente         | Serviço de downstream                          | Imposição |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Gerenciamento do Microsoft Azure (portal e API) | Early bound |
| Microsoft Classroom | Exchange                                    | Early bound |
|                     | SharePoint                                  | Early bound  |
| Equipes da Microsoft     | Exchange                                    | Early bound |
|                     | Planejador de MS                                  | Associação tardia  |
|                     | SharePoint                                  | Early bound |
|                     | Skype for Business Online                   | Early bound |
| Portal do Office       | Exchange                                    | Associação tardia  |
|                     | SharePoint                                  | Associação tardia  |
| Grupos do Outlook      | Exchange                                    | Early bound |
|                     | SharePoint                                  | Early bound |
| PowerApps           | Gerenciamento do Microsoft Azure (portal e API) | Early bound |
|                     | Windows Azure Active Directory              | Early bound |
| Project             | Dynamics CRM                                | Early bound |
| Skype for Business  | Exchange                                    | Early bound |
| Visual Studio       | Gerenciamento do Microsoft Azure (portal e API) | Early bound |

## <a name="next-steps"></a>Próximas etapas

Para saber como implementar o acesso condicional em seu ambiente, consulte [planejar a implantação de acesso condicional no Azure Active Directory](plan-conditional-access.md).
