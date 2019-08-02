---
title: O que é uma migração de política de acesso condicional do Azure Active Directory? | Microsoft Docs
description: Veja o que você precisa saber para migrar as políticas clássicas no portal do Azure.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 07/24/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7464546a78e1b54cdea3bd6dd66656f5b189bc02
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67506810"
---
# <a name="what-is-a-policy-migration-in-azure-active-directory-conditional-access"></a>O que é uma migração de política de acesso condicional do Azure Active Directory? 

[Acesso condicional](../active-directory-conditional-access-azure-portal.md) é um recurso do Azure Active directory (Azure AD) que permite que você controle como os usuários autorizados acessam seus aplicativos de nuvem. Enquanto a finalidade ainda é o mesmo, a versão do portal do Azure introduziu melhorias significativas para o funcionamento do acesso condicional.

Considere migrar as políticas que você não tiver criado no Portal do Azure porque:

- Agora você pode abordar cenários que você não podia manipular antes.
- Você pode consolidar as políticas e, dessa forma, reduzir o número de políticas a serem gerenciadas.   
- Você pode gerenciar todas as suas políticas de acesso condicional em um local central.
- O portal clássico do Azure será desativado.   

Este artigo explica o que você precisa saber para migrar as políticas de acesso condicional existentes para a nova estrutura.
 
## <a name="classic-policies"></a>Políticas clássicas

No [portal do Azure](https://portal.azure.com), o [acesso condicional - políticas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) página é o ponto de entrada para as políticas de acesso condicional. No entanto, em seu ambiente, você também pode ter políticas de acesso condicional, que você não tiver criado usando essa página. Essas políticas são conhecidas como *políticas clássicas*. As políticas clássicas são políticas de acesso condicional, você criou no:

- Portal clássico do Azure
- Portal clássico do Intune
- Portal de Proteção de Aplicativo do Intune

Sobre o **acesso condicional** página, você pode acessar suas políticas clássicas clicando [ **políticas clássicas (visualização)** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) no **gerenciar** seção. 

![Azure Active Directory](./media/policy-migration/71.png)

O modo de exibição **Políticas clássicas** fornece uma opção para:

- Filtrar suas políticas clássicas.
 
   ![Azure Active Directory](./media/policy-migration/72.png)

- Desativar políticas clássicas.

   ![Azure Active Directory](./media/policy-migration/73.png)
   
- Examine as configurações de uma política clássica (e para desabilitá-lo).

   ![Azure Active Directory](./media/policy-migration/74.png)

Se você tiver desabilitado uma política clássica, você não pode mais reverter essa etapa. Isso é porque você pode modificar a associação de grupo em uma política clássica com o modo de exibição **Detalhes**. 

![Azure Active Directory](./media/policy-migration/75.png)

Alterando os grupos selecionados ou excluindo grupos específicos, você pode testar o efeito de uma política clássica desabilitada para alguns usuários de teste antes de desabilitar a política para todos os usuários e grupos incluídos. 

## <a name="azure-ad-conditional-access-policies"></a>Políticas de acesso condicional do AD do Azure

Com acesso condicional no portal do Azure, você pode gerenciar todas as suas políticas em um local central. Como a implementação do acesso condicional foi alterado, você deve se familiarizar com os conceitos básicos antes de migrar suas políticas clássicas.

Consulte:

- [O que é acesso condicional no Azure Active Directory](../active-directory-conditional-access-azure-portal.md) para saber mais sobre os conceitos básicos e a terminologia.
- [Práticas recomendadas para acesso condicional no Azure Active Directory](best-practices.md) para obter orientação sobre como implantar o acesso condicional em sua organização.
- [Exigir MFA para aplicativos específicos com acesso condicional do Azure Active Directory](app-based-mfa.md) de se familiarizar com a interface do usuário no portal do Azure.
 
## <a name="migration-considerations"></a>Considerações sobre a migração

Neste artigo, as políticas de acesso condicional do Azure AD são também denominadas *novas políticas*.
Suas políticas clássicas continuam funcionando paralelamente com as novas políticas até que você as desabilite ou exclua. 

Os seguintes aspectos são importantes no contexto de consolidação de uma política:

- Uma vez que as políticas clássicas são associadas a um aplicativo de nuvem específica, você pode selecionar quantos aplicativos de nuvem você precisar de uma nova política.
- Controles de uma política clássica e uma nova política para um aplicativo de nuvem exigem que todos os controles (*AND*) sejam atendidos. 
- Em uma nova política, você pode:
   - Combine várias condições se exigido por seu cenário. 
   - Selecione vários requisitos como acesso de controle e combine-os com uma lógica *OR* (requer um dos controles selecionados) ou com uma lógica *AND* (requer todos os controles selecionados).

   ![Azure Active Directory](./media/policy-migration/25.png)

### <a name="office-365-exchange-online"></a>Office 365 Exchange online

Se você deseja migrar as políticas de clássicas para **Office 365 Exchange online** que incluem **Exchange Active Sync** como condição de aplicativos cliente, você não pode consolidá-las em uma nova política. 

Isso é, por exemplo, o caso se você deseja dar suporte a todos os tipos de aplicativo cliente. Em uma nova política que tem **Exchange Active Sync** como condição de aplicativos cliente, não será possível selecionar outros aplicativos cliente.

![Azure Active Directory](./media/policy-migration/64.png)

A consolidação em uma nova política também não é possível se suas políticas clássicas possuem várias condições. Uma nova política que tem **Exchange Active Sync** como condição de aplicativos cliente configurada não oferece suporte para outras condições:   

![Azure Active Directory](./media/policy-migration/08.png)

Se você tiver uma nova política que tem **Exchange Active Sync** como condição de aplicativos cliente configurada, você precisa certificar-se de que todas as outras condições não estão configuradas. 

![Azure Active Directory](./media/policy-migration/16.png)
 
As políticas clássicas [com base no aplicativo](technical-reference.md#approved-client-app-requirement) para o Office 365 Exchange online que incluem **Exchange Active Sync** como condição de aplicativos cliente permitem **plataformas de dispositivo** **com suporte** e [sem suporte](technical-reference.md#device-platform-condition). Uma vez que você não pode configurar plataformas de dispositivos individuais em uma nova política relacionada, você pode limitar o suporte somente para [plataformas de dispositivo com suporte](technical-reference.md#device-platform-condition). 

![Azure Active Directory](./media/policy-migration/65.png)

Você pode consolidar várias políticas clássicas que incluem **Exchange Active Sync** como condição de aplicativos cliente se eles tiverem:

- Somente **Exchange Active Sync** como condição 
- Vários requisitos para concessão de acesso configurado

Um cenário comum é a consolidação de:

- Uma política clássica baseada em dispositivo do portal clássico do Azure 
- Uma política clássica com base no aplicativo no portal de proteção do aplicativo do Intune 
 
Nesse caso, você pode consolidar suas políticas clássicas em uma nova política que tem dois requisitos selecionados.

![Azure Active Directory](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Plataformas de dispositivo

As políticas clássicas com [controles baseados em aplicativo](technical-reference.md#approved-client-app-requirement) são pré-configurados com iOS e Android como [condição de plataforma de dispositivo](technical-reference.md#device-platform-condition). 

Em uma nova política, você precisa selecionar as [plataformas de dispositivo](technical-reference.md#device-platform-condition) que você deseja dar suporte individualmente.

![Azure Active Directory](./media/policy-migration/41.png)

## <a name="next-steps"></a>Próximas etapas

- Se você quiser saber como configurar uma política de acesso condicional, consulte [exigir MFA para aplicativos específicos com acesso condicional do Azure Active Directory](app-based-mfa.md).
- Se você estiver pronto para configurar políticas de acesso condicional para o seu ambiente, consulte a [práticas recomendadas para acesso condicional no Azure Active Directory](best-practices.md). 
