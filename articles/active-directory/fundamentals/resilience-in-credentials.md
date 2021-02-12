---
title: Crie resiliência com o gerenciamento de credenciais no Azure Active Directory
description: Um guia para arquitetos e administradores de ti na criação de uma estratégia de credenciais resiliente.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 399d2f71fa20d63dce89cf3be5c12ffd63264895
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724703"
---
# <a name="build-resilience-with-credential-management"></a>Crie resiliência com o gerenciamento de credenciais

Quando uma credencial é apresentada ao Azure AD em uma solicitação de token, há várias dependências que devem estar disponíveis para validação. O primeiro fator de autenticação depende da autenticação do Azure AD e, em alguns casos, na infraestrutura local. Para obter mais informações sobre arquiteturas de autenticação híbrida, consulte [criar resiliência em sua infraestrutura híbrida](resilience-in-hybrid.md). 

Se você implementar um segundo fator, as dependências do segundo fator serão adicionadas às dependências do primeiro. Por exemplo, se seu primeiro fator for via PTA e o segundo fator for o SMS, suas dependências serão:

* Serviços de autenticação do Azure AD

* Serviço do Azure MFA

* Infraestrutura local

* Operadora de telefone

* O dispositivo do usuário (não mostrado)

 
![Imagem de métodos de autenticação e dependências](./media/resilience-in-credentials/admin-resilience-credentials.png)

Sua estratégia de credenciais deve considerar as dependências de cada tipo de autenticação e provisionar métodos que evitam um ponto único de falha. 

Como os métodos de autenticação têm dependências diferentes, é uma boa ideia permitir que os usuários se registrem para o máximo possível de opções de fator de segundo. Lembre-se de incluir os outros fatores com dependências diferentes, se possível. Por exemplo, chamada de voz e SMS como fatores de segundo compartilham as mesmas dependências, portanto, tê-las como as únicas opções não reduzem o risco.

A estratégia de credencial mais resiliente é usar a autenticação com senha. As chaves de segurança do Windows Hello para empresas e do FIDO 2,0 têm menos dependências do que a autenticação forte com dois fatores separados. As chaves de segurança Microsoft Authenticator aplicativo, Windows Hello para empresas e Fido 2,0 são as mais seguras. 

Para os dois fatores, o aplicativo Microsoft Authenticator ou outros aplicativos autenticadores que usam a senha de uso único com base em tempo (TOTP) ou tokens de hardware OATH têm as menores dependências e, portanto, são mais resilientes.

## <a name="how-do-multiple-credentials-help-resilience"></a>Como várias credenciais ajudam a resiliência?

O provisionamento de vários tipos de credenciais fornece aos usuários opções que acomodam suas preferências e restrições ambientais. Como resultado, a autenticação interativa em que os usuários são solicitados a autenticação multifator será mais resiliente a dependências específicas estarem indisponíveis no momento da solicitação. Você pode [otimizar os prompts de reautenticação para autenticação multifator](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

Além da resiliência de usuário individual descrita acima, as empresas devem planejar as contingências para interrupções em larga escala, como erros operacionais que apresentam uma configuração incorreta, um desastre natural ou uma interrupção de recursos em toda a empresa para um serviço de federação local (especialmente quando usado para autenticação multifator). 

## <a name="how-do-i-implement-resilient-credentials"></a>Como fazer implementar credenciais resilientes?

* Implante [credenciais com senha](../authentication/howto-authentication-passwordless-deployment.md) como o Windows Hello para empresas, autenticação por telefone e chaves de segurança FIDO2 para reduzir as dependências.

* Implante o [aplicativo Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) como um segundo fator.

* Ative a [sincronização de hash de senha](../hybrid/whatis-phs.md) para contas híbridas que são sincronizadas do Windows Server Active Directory. Essa opção pode ser habilitada junto com serviços de Federação, como AD FS e fornece um retorno no caso de falha do serviço de Federação.

* [Analise o uso de métodos de autenticação multifator](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/) para melhorar a experiência dos usuários.

* [Implementar uma estratégia de controle de acesso resiliente](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>Próximas etapas
Recursos de resiliência para administradores e arquitetos
 
* [Crie resiliência com os Estados do dispositivo](resilience-with-device-states.md)

* [Criar resiliência usando a Continuous Access Evaluation (CAE)](resilience-with-continuous-access-evaluation.md)

* [Criar resiliência na autenticação de usuário externa](resilience-b2b-authentication.md)

* [Crie resiliência em sua autenticação híbrida](resilience-in-hybrid.md)

* [Crie resiliência no acesso do aplicativo com o proxy de aplicativo](resilience-on-premises-access.md)

Recursos de resiliência para desenvolvedores

* [Crie resiliência IAM em seus aplicativos](resilience-app-development-overview.md)

* [Crie resiliência em seus sistemas CIAM](resilience-b2c.md)