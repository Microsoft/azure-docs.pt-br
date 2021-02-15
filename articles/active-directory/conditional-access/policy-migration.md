---
title: Migrar políticas de acesso condicional – Azure Active Directory
description: Veja o que você precisa saber para migrar as políticas clássicas no portal do Azure.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1811d5b9ae4d3e34b48e1cdc156438f2bad98d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90601647"
---
# <a name="conditional-access-classic-policy-migration"></a>Migração de política clássica de acesso condicional

O acesso condicional é a ferramenta usada pelo Azure Active Directory para reunir sinais, tomar decisões e impor políticas organizacionais. O acesso condicional está no centro do novo plano de controle controlado por identidade. Embora a finalidade ainda seja a mesma, o lançamento do novo portal do Azure introduziu melhorias significativas no funcionamento do acesso condicional.

Considere migrar as políticas que você não tiver criado no Portal do Azure porque:

- Agora você pode abordar cenários que você não podia manipular antes.
- Você pode consolidar as políticas e, dessa forma, reduzir o número de políticas a serem gerenciadas.
- Você pode gerenciar todas as políticas de acesso condicional em um local central.
- O portal clássico do Azure será desativado.

Este artigo explica o que você precisa saber para migrar suas políticas de acesso condicional existentes para a nova estrutura.

## <a name="classic-policies"></a>Políticas clássicas

No [portal do Azure](https://portal.azure.com), as políticas de acesso condicional podem ser encontradas em **Azure Active Directory**  >  **Security**  >  **acesso condicional**de segurança. Sua organização também pode ter políticas de acesso condicional mais antigas não criadas usando esta página. Essas políticas são conhecidas como *políticas clássicas*. As políticas clássicas são políticas de acesso condicional, que você criou em:

- Portal clássico do Azure
- Portal clássico do Intune
- Portal de Proteção de Aplicativo do Intune

Na página **acesso condicional** , você pode acessar suas políticas clássicas clicando em [**políticas clássicas**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) na seção **gerenciar** . 

![Acesso condicional no Azure AD mostrando a exibição de políticas clássicas](./media/policy-migration/71.png)

O modo de exibição **Políticas clássicas** fornece uma opção para:

- Filtrar suas políticas clássicas.
- Desativar políticas clássicas.
- Examine as configurações de uma política clássica e desabilite-a.

   ![Detalhes da política clássica, incluindo configuração de política existente](./media/policy-migration/74.png)

> [!WARNING]
> Depois de desabilitada, uma política clássica não pode ser reabilitada.

A exibição de detalhes de uma política clássica permite que você documente as configurações, modifique os grupos incluídos ou excluídos e desabilite a política.

![Detalhes da política-grupos a serem incluídos ou excluídos](./media/policy-migration/75.png)

Ao alterar os grupos selecionados ou excluindo grupos específicos, você pode testar o efeito de uma política clássica desabilitada para alguns usuários de teste antes de desabilitar a política para todos os usuários e grupos incluídos.
 
## <a name="migration-considerations"></a>Considerações sobre migração

Neste artigo, as políticas de acesso condicional do Azure AD também são conhecidas como *novas políticas*.
Suas políticas clássicas continuam funcionando paralelamente com as novas políticas até que você as desabilite ou exclua. 

Os seguintes aspectos são importantes no contexto de consolidação de uma política:

- Uma vez que as políticas clássicas são associadas a um aplicativo de nuvem específica, você pode selecionar quantos aplicativos de nuvem você precisar de uma nova política.
- Controles de uma política clássica e uma nova política para um aplicativo de nuvem exigem que todos os controles (*AND*) sejam atendidos. 
- Em uma nova política, você pode:
   - Combine várias condições se exigido por seu cenário. 
   - Selecione vários requisitos como acesso de controle e combine-os com uma lógica *OR* (requer um dos controles selecionados) ou com uma lógica *AND* (requer todos os controles selecionados).

### <a name="exchange-online"></a>Exchange Online

Se você quiser migrar políticas clássicas para o **Exchange Online** que incluem **Exchange Active Sync** como condição de aplicativos cliente, talvez não seja possível consolidá-las em uma nova política. 

Isso é, por exemplo, o caso se você deseja dar suporte a todos os tipos de aplicativo cliente. Em uma nova política que tem **Exchange Active Sync** como condição de aplicativos cliente, não será possível selecionar outros aplicativos cliente.

![Acesso condicional selecionando aplicativos cliente](./media/policy-migration/64.png)

A consolidação em uma nova política também não é possível se suas políticas clássicas possuem várias condições. Uma nova política que tem **Exchange Active Sync** como condição de aplicativos cliente configurada não oferece suporte para outras condições:   

![O Exchange ActiveSync não oferece suporte às condições selecionadas](./media/policy-migration/08.png)

Se você tiver uma nova política que tem **Exchange Active Sync** como condição de aplicativos cliente configurada, você precisa certificar-se de que todas as outras condições não estão configuradas. 

![Condições de acesso condicional](./media/policy-migration/16.png)
 
As políticas clássicas baseadas em aplicativo para o Exchange Online que incluem o **Exchange Active Sync** como condição de aplicativos cliente permitem plataformas de dispositivo **com** e sem **suporte** . Uma vez que você não pode configurar plataformas de dispositivos individuais em uma nova política relacionada, você pode limitar o suporte somente para [plataformas de dispositivo com suporte](concept-conditional-access-conditions.md#device-platforms). 

![Acesso condicional selecione Exchange ActiveSync](./media/policy-migration/65.png)

Você pode consolidar várias políticas clássicas que incluem **Exchange Active Sync** como condição de aplicativos cliente se eles tiverem:

- Somente **Exchange Active Sync** como condição 
- Vários requisitos para concessão de acesso configurado

Um cenário comum é a consolidação de:

- Uma política clássica baseada em dispositivo do portal clássico do Azure 
- Uma política clássica com base no aplicativo no portal de proteção do aplicativo do Intune 
 
Nesse caso, você pode consolidar suas políticas clássicas em uma nova política que tem dois requisitos selecionados.

![Controles de concessão de acesso condicional](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Plataformas de dispositivo

As políticas clássicas com controles baseados em aplicativo são pré-configurados com iOS e Android como condição de plataforma de dispositivo. 

Em uma nova política, você precisa selecionar as [plataformas de dispositivo](concept-conditional-access-conditions.md#device-platforms) que você deseja dar suporte individualmente.

![Seleção de plataformas de dispositivo de acesso condicional](./media/policy-migration/41.png)

## <a name="next-steps"></a>Próximas etapas

- [Use o modo somente de relatório para acesso condicional para determinar o impacto das novas decisões de política.](concept-conditional-access-report-only.md)
- Se você quiser saber como configurar uma política de acesso condicional, consulte [políticas comuns de acesso condicional](concept-conditional-access-policy-common.md).
- Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, consulte o artigo [como planejar sua implantação de acesso condicional em Azure Active Directory](plan-conditional-access.md). 
