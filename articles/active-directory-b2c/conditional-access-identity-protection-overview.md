---
title: Identity Protection e acesso condicional no Azure AD B2C
description: Saiba como o Identity Protection fornece visibilidade sobre as entradas suspeitas e as detecções de risco. Descubra como o acesso condicional permite que você imponha políticas organizacionais com base em eventos de risco nos seus locatários do Azure AD B2C.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 03/03/2021
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2ec8af824d573493d1da743923956e587ded62e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033819"
---
# <a name="identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Identity Protection e acesso condicional do Azure AD B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Aprimore a segurança do Azure Active Directory B2C (Azure AD B2C) com o Azure AD Identity Protection e o acesso condicional. Os recursos de detecção de risco do Identity Protection, incluindo entradas e usuários suspeitos, são automaticamente detectados e exibidos no seu locatário do Azure AD B2C. Você pode criar políticas de acesso condicional que usam essas detecções de risco para determinar ações e impor políticas organizacionais. Juntas, essas funcionalidades oferecem aos proprietários de aplicativos do Azure AD B2C maior controle sobre políticas de acesso e autenticações suspeitas.
  
Caso você já esteja familiarizado com o [Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) e o [acesso condicional](../active-directory/conditional-access/overview.md) no Azure AD, o uso dessas funcionalidades com o Azure AD B2C será uma experiência conhecida, com as pequenas diferenças discutidas neste artigo.

![Acesso condicional em um locatário B2C](media/conditional-access-identity-protection-overview/conditional-access-b2c.png)

> [!NOTE]
> Para usar o acesso condicional, é necessário ter o Azure AD B2C Premium P2.

## <a name="benefits-of-identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Benefícios do Identity Protection e do acesso condicional do Azure AD B2C  

Ao emparelhar políticas de acesso condicional com a detecção de risco do Identity Protection, você pode responder às autenticações suspeitas com a ação de política apropriada.

- **Obtenha um novo nível de visibilidade dos riscos de autenticação para seus aplicativos e sua base de clientes**. Com sinais de bilhões de autenticações mensais no Azure AD e na conta Microsoft, os algoritmos de detecção de risco agora sinalizam as autenticações como de baixo, médio ou alto risco para suas autenticações de consumidor ou cidadão locais.
- **Resolva os riscos automaticamente configurando uma autenticação adaptável própria**. Para aplicativos especificados, você pode exigir que um conjunto específico de usuários forneça um segundo fator de autenticação, como ocorre na MFA (autenticação multifator). Ou, então, você pode bloquear o acesso com base no nível de risco detectado. Assim como acontece com outras experiências do Azure AD B2C, você pode personalizar a experiência do usuário final resultante com a voz, o estilo e a marca da sua organização. Você também pode exibir alternativas de mitigação caso o usuário não possa obter o acesso.
- **Controle o acesso com base na localização, em grupos e em aplicativos**.  O acesso condicional também pode ser usado para controlar situações não baseadas em risco. Por exemplo, você pode exigir a MFA para os clientes que acessam um aplicativo específico ou bloquear o acesso em geografias especificadas.
- **Integre-se aos fluxos dos usuários do Azure AD B2C e às políticas personalizadas do Identity Experience Framework**. Use as experiências personalizadas que você já tem e adicione os controles necessários para interagir com o acesso condicional. Você também pode implementar cenários avançados para permitir acesso, como o acesso baseado em conhecimento ou seu provedor de MFA preferencial.

## <a name="feature-differences-and-limitations"></a>Diferenças e limitações de recursos

O Identity Protection e o acesso condicional do Azure AD B2C geralmente funcionam da mesma forma que no Azure AD, com as seguintes exceções:

- A Central de Segurança não está disponível no Azure AD B2C.

- Não há suporte para o Identity Protection e o acesso condicional em fluxos de servidor para servidor ROPC em locatários do Azure AD B2C.

- Nos locatários do Azure AD B2C, as detecções de risco do Identity Protection estão disponíveis somente para contas locais do B2C, não para identidades sociais, como o Google ou o Facebook.

- Nos locatários do Azure AD B2C, um subconjunto das detecções de risco do Identity Protection está disponível. Confira [Investigar o risco com o Identity Protection](identity-protection-investigate-risk.md) e [Adicionar acesso condicional a fluxos de usuário](conditional-access-user-flow.md).

- O recurso de conformidade do dispositivo do acesso condicional não está disponível nos locatários do Azure AD B2C.


## <a name="integrate-conditional-access-with-user-flows-and-custom-policies"></a>Integrar o acesso condicional aos fluxos dos usuários e às políticas personalizadas

No Azure AD B2C, você pode disparar condições de acesso condicional em fluxos dos usuários internos. Você também pode incorporar o acesso condicional a políticas personalizadas. Assim como ocorre com outros aspectos do fluxo de usuário B2C, as mensagens de experiência do usuário final podem ser personalizadas de acordo com a voz, a marca e as alternativas de mitigação da sua organização. Confira [Adicionar o acesso condicional a fluxos de usuário](conditional-access-user-flow.md).

## <a name="microsoft-graph-api"></a>API do Microsoft Graph

Gerencie também as políticas de acesso condicional do Azure AD B2C com a API do Microsoft Graph. Para obter detalhes, confira a [documentação do acesso condicional](../active-directory/conditional-access/overview.md) e as [operações do Microsoft Graph](microsoft-graph-operations.md#conditional-access).

## <a name="next-steps"></a>Próximas etapas

- [Adicionar o acesso condicional a fluxos de usuário](conditional-access-user-flow.md)
- [Saiba mais sobre o Identity Protection no Azure AD](../active-directory/identity-protection/overview-identity-protection.md)
- [Saiba mais sobre o acesso condicional](../active-directory/conditional-access/overview.md)