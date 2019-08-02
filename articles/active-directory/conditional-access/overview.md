---
title: O que é o Acesso Condicional no Azure Active Directory? | Microsoft Docs
description: Saiba como o Acesso Condicional no Azure Active Directory ajuda você a implementar decisões de acesso automatizado que não são baseadas apenas em quem tenta acessar um recurso, mas também em como um recurso é acessado.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 02/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99d17b354e267d003e23e507ca190b951e3ed4a0
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608086"
---
# <a name="what-is-conditional-access"></a>O que é Acesso Condicional?

Segurança é uma grande preocupação para organizações que usam a nuvem. Um aspecto importante da segurança em nuvem é a identidade e o acesso quando o assunto é gerenciar os recursos em nuvem. Em um mundo primeiro o dispositivo móvel e a nuvem, os usuários podem acessar os recursos da organização usando uma grande variedade de dispositivos e aplicativos de qualquer lugar. Por causa disso, concentrar-se apenas em quem pode acessar um recurso não é mais suficiente. Para dominar o equilíbrio entre segurança e produtividade, você também precisa considerar como um recurso é acessado em uma decisão de controle de acesso. Com Acesso Condicional do Azure AD (Azure Active Directory), você pode atender a esse requisito. O Acesso Condicional é um recurso do Azure Active Directory. Com o Acesso Condicional, você pode implementar decisões de controle de acesso automatizado para o acesso a aplicativos de nuvem com base em condições.

As políticas de Acesso Condicional são impostas após a conclusão da autenticação multifator. Portanto, o Acesso Condicional não funciona como uma primeira linha de defesa para cenários como ataques de DoS (ataque de negação de serviço), mas pode utilizar os sinais desses eventos (por exemplo, o nível de risco de entrada, a localização da solicitação e assim por diante) para determinar o acesso.  

![Controle](./media/overview/81.png)

Este artigo apresenta uma visão geral conceitual do Acesso Condicional no Azure AD.

## <a name="common-scenarios"></a>Cenários comuns

Em um mundo móvel e em nuvem, o Azure Active Directory permite o logon único para dispositivos, aplicativos e serviços de qualquer lugar. Com a proliferação de dispositivos (incluindo BYOD), trabalho fora de redes corporativas e aplicativos de SaaS de terceiros, você tem duas metas opostas:

- Capacitar os usuários para serem produtivos sempre e em qualquer lugar
- Proteger os ativos corporativos a qualquer momento

Usando políticas de Acesso Condicional, você pode aplicar os controles de acesso certos nas condições necessárias. O Acesso Condicional do AD Azure oferece segurança adicional quando necessário e fica fora do caminho do usuário quando não o é.

A seguir, são apresentadas algumas preocupações de acesso comuns em que o Acesso Condicional pode ajudar:

- **[Risco de entrada](conditions.md#sign-in-risk)** : O Azure AD Identity Protection detecta riscos de entrada. Como você restringe o acesso caso um risco de entrada detectado indique um mau ator? E se você quisesse obter prova mais sólida de que uma entrada foi realizada pelo usuário legítimo? E se a dúvida é forte o suficiente para até mesmo impedir que usuários específicos acessem um aplicativo?  
- **[Localização de rede](location-condition.md)** : O Azure AD está acessível de qualquer lugar. E se uma tentativa de acesso for realizada em um local de rede que não esteja sob o controle do departamento de TI? Uma combinação de nome de usuário e senha pode ser boa o suficiente como prova de identidade para tentativas de acesso da rede corporativa. E se você exigir uma prova de identidade mais forte para tentativas de acesso iniciadas de outros países ou regiões do mundo inesperados? E se você ainda quiser bloquear tentativas de acesso de determinados locais?  
- **[Gerenciamento de dispositivo](conditions.md#device-platforms)** : No Azure AD, os usuários podem acessar aplicativos em nuvem em uma grande variedade de dispositivos, inclusive dispositivos para dispositivos móveis e também pessoais. E se você exigir que as tentativas de acesso somente sejam realizadas com dispositivos gerenciados pelo departamento de TI? E se você ainda quiser bloquear determinados tipos de dispositivo de acessar aplicativos na nuvem no ambiente?
- **[Aplicativo cliente](conditions.md#client-apps)** : Atualmente, você pode acessar muitos aplicativos na nuvem usando tipos de aplicativos diferentes, como aplicativos baseados na Web, aplicativos móveis ou aplicativos da área de trabalho. E se uma tentativa de acesso for realizada usando-se um tipo de aplicativo cliente que causa problemas conhecidos? E se você precisar de um dispositivo gerenciado pelo departamento de TI para determinados tipos de aplicativo?

Essas perguntas e as respostas relacionadas representam cenários de acesso comuns para Acesso Condicional do Azure AD.
Acesso Condicional é um recurso do Azure Active Directory que permite processar cenários de acesso usando uma abordagem baseada em política.

> [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

## <a name="conditional-access-policies"></a>Políticas de Acesso Condicional

Política de Acesso Condicional é a definição de um cenário de acesso que usa o seguinte padrão:

![Controle](./media/overview/10.png)


**Quando isso acontece** define o motivo de disparar a política. Esse motivo é caracterizado por um grupo de condições que foram atendidas. No Acesso Condicional do Azure AD, as duas condições de atribuição desempenham um papel especial:

- **[Usuários](conditions.md#users-and-groups)** : Os usuários que realizam uma tentativa de acesso (**Quem**).
- **[Aplicativos de nuvem](conditions.md#cloud-apps-and-actions)** : Os destinos de uma tentativa de acesso (**O quê**).

Essas duas condições são obrigatórias em uma política de Acesso Condicional. Além das duas condições obrigatórias, você também pode incluir condições adicionais que descrevam como a tentativa de acesso é realizada. Os exemplos comuns estão usando dispositivos móveis ou locais que estejam fora da rede corporativa. Para saber mais, confira [Condições no Acesso Condicional do Azure Active Directory](conditions.md).

A combinação de condições e seus controles de acesso representa uma política de Acesso Condicional.

![Controle](./media/overview/51.png)

Com o Acesso Condicional do Azure AD, você pode controlar como os usuários autorizados podem acessar seus aplicativos na nuvem. O objetivo de uma política de Acesso Condicional é impor controles de acesso adicionais em uma tentativa de acesso a um aplicativo de nuvem baseado em como uma tentativa de acesso é realizada.

Uma abordagem baseada em política para proteger o acesso aos aplicativos de nuvem permite que você comece a esboçar os requisitos de política para o ambiente usando a estrutura descrita neste artigo sem se preocupar com a implementação técnica.

## <a name="azure-ad-conditional-access-and-federated-authentication"></a>Acesso Condicional do Azure AD e autenticação federada

As políticas de Acesso Condicional funcionam perfeitamente com [autenticação federada](../../security/fundamentals/choose-ad-authn.md#federated-authentication). Esse suporte inclui todas as condições com suporte e controles e visibilidade de como a política é aplicada às entradas do usuário por meio dos [relatórios do Azure AD](../reports-monitoring/concept-sign-ins.md).

*Autenticação federada com o Azure AD* significa que um serviço de autenticação confiável lida com a autenticação do usuário no Azure AD. Um serviço de autenticação confiável é, por exemplo, o AD FS (Serviços de Federação do Active Directory) ou qualquer outro serviço de federação. Nessa configuração, a autenticação de usuário principal é executada no serviço e, em seguida, o Azure AD é usado para entrar em aplicativos individuais. O Acesso Condicional do Azure AD é aplicado antes da concessão de acesso ao aplicativo que o usuário está acessando. 

Quando a política de Acesso Condicional configurada exige a autenticação multifator, o padrão do Azure AD é usar o Azure MFA. Se você usar o serviço de federação para o MFA, poderá configurar o Azure AD a fim de redirecionar para o serviço de federação quando o MFA for exigido definindo `-SupportsMFA` como `$true` no [PowerShell](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings). Essa configuração funciona para serviços de autenticação federada que dão suporte à solicitação de desafio do MFA emitida pelo Azure AD usando `wauth= http://schemas.microsoft.com/claims/multipleauthn`.

Depois que o usuário tiver se conectado ao serviço de autenticação federada, o Azure AD lidará com outros requisitos de política, como a conformidade do dispositivo ou um aplicativo aprovado.

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

Os clientes com [licenças do Microsoft 365 Business](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) também têm acesso a recursos de Acesso Condicional. 

## <a name="next-steps"></a>Próximas etapas

Para saber como implementar o Acesso Condicional em seu ambiente, confira [Planejar a implantação do Acesso Condicional no Azure Active Directory](plan-conditional-access.md).
