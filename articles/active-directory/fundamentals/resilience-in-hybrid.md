---
title: Criar autenticação híbrida mais resiliente no Azure Active Directory
description: Um guia para arquitetos e administradores de ti na criação de uma infraestrutura híbrida resiliente.
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
ms.openlocfilehash: de01788c01d4d6dedd9563faaaac07bff30bbd97
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724752"
---
# <a name="build-resilience-in-your-hybrid-architecture"></a>Crie resiliência em sua arquitetura híbrida

A autenticação híbrida permite que os usuários acessem recursos baseados em nuvem com suas identidades mestras no local. Uma infraestrutura híbrida inclui componentes de nuvem e locais.

* Os componentes de nuvem incluem o Azure AD, os recursos e serviços do Azure, os aplicativos baseados em nuvem da sua organização e o SaaS.

* Os componentes locais incluem aplicativos locais, recursos como bancos de dados SQL e um provedor de identidade como o Windows Server Active Directory. 

> [!IMPORTANT]
> Conforme você planeja a resiliência em sua infraestrutura híbrida, é fundamental minimizar as dependências e pontos únicos de falha. 

A Microsoft oferece três mecanismos para autenticação híbrida. As opções são listadas em ordem de resiliência. Recomendamos que você implemente a sincronização de hash de senha, se possível.

* A [sincronização de hash de senha](../hybrid/whatis-phs.md) (PHS) usa Azure ad Connect para sincronizar a identidade e um hash do hash da senha para o Azure AD, permitindo que os usuários entrem em recursos baseados em nuvem com a senha Mestrada localmente. PHS tem dependências locais somente para sincronização, não para autenticação.

* A [autenticação de passagem](../hybrid/how-to-connect-pta.md) (PTA) redireciona os usuários para o Azure ad para entrada. Em seguida, o nome de usuário e a senha são validados em relação à Active Directory local, por meio de um agente que é implantado na rede corporativa. O PTA tem uma superfície local de seus agentes de PTA do Azure AD que residem em servidores locais.

* Os clientes de [Federação](../hybrid/whatis-fed.md) implantam um serviço de Federação como o AD FS e, em seguida, o Azure ad valida a declaração SAML produzida pelo serviço de Federação. A Federação tem a dependência mais alta na infraestrutura local e, portanto, mais pontos de falha. 

   
Você pode estar usando um ou mais desses métodos em sua organização. Para saber mais, veja [Escolher o método de autenticação certo para sua solução de identidade híbrida do Azure Active Directory | Microsoft Docs](../hybrid/choose-ad-authn.md). Este artigo contém uma árvore de decisão que pode ajudá-lo a decidir sobre sua metodologia.

## <a name="password-hash-synchronization"></a>Sincronização de hash de senha

A opção de autenticação híbrida mais simples e resiliente para o Azure AD é a [sincronização de hash de senha](../hybrid/whatis-phs.md) que não tem nenhuma dependência de infraestrutura de identidade local ao processar solicitações de autenticação. Depois que as identidades com hashes de senha são sincronizadas com o Azure AD, os usuários podem se autenticar em recursos de nuvem sem dependência nos componentes de identidade locais. 

![Diagrama de arquitetura do PHS](./media/resilience-in-hybrid/admin-resilience-password-hash-sync.png)

Se você escolher essa opção de autenticação, não haverá interrupção quando os componentes de identidade locais ficarem indisponíveis. A interrupção local pode ocorrer por vários motivos, incluindo falha de hardware, interrupções de energia, desastres naturais e ataques de malware. 

### <a name="how-do-i-implement-phs"></a>Como fazer implementar PHS?

Para implementar o PHS, consulte os seguintes recursos:

* [Implementar a sincronização de hash de senha com o Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md)

* [Permitir a sincronização de hash de senha](../hybrid/how-to-connect-password-hash-synchronization.md)

Se seus requisitos forem de modo que você não possa usar o PHS, use a autenticação de passagem.

## <a name="pass-through-authentication"></a>Autenticação de Passagem

A autenticação de passagem tem uma dependência nos agentes de autenticação que residem localmente nos servidores. Uma conexão persistente, ou barramento de serviço, está presente entre o Azure AD e os agentes PTA locais. O firewall, os servidores que hospedam os agentes de autenticação e o Active Directory do Windows Server local (ou outro provedor de identidade) são todos os pontos de falha potenciais. 

![Diagrama de arquitetura do PTA](./media/resilience-in-hybrid/admin-resilience-pass-through-authentication.png)

### <a name="how-do-i-implement-pta"></a>Como fazer implementar PTA?

Para implementar a autenticação de passagem, consulte os recursos a seguir.

* [Como funciona a autenticação de passagem](../hybrid/how-to-connect-pta-how-it-works.md)

* [Aprofundamento em segurança na Autenticação de Passagem](../hybrid/how-to-connect-pta-security-deep-dive.md)

* [Instalar a autenticação de passagem do Azure AD](../hybrid/how-to-connect-pta-quick-start.md)

* Se você estiver usando o PTA, defina uma [topologia altamente disponível](../hybrid/how-to-connect-pta-quick-start.md).

 ## <a name="federation"></a>Federação

A Federação envolve a criação de uma relação de confiança entre o Azure AD e o serviço de Federação, que inclui a troca de pontos de extremidade, certificados de assinatura de token e outros metadados. Quando uma solicitação chega ao Azure AD, ela lê a configuração e redireciona o usuário para os pontos de extremidade configurados. Nesse ponto, o usuário interage com o serviço de Federação, que emite uma Asserção SAML validada pelo Azure AD. 

O diagrama a seguir mostra uma topologia de um Serviços de Federação do Active Directory (AD FS) empresarial (AD FS), implantação que inclui a Federação redundante e servidores proxy de aplicativo Web em vários data centers locais. Essa configuração depende de componentes de infraestrutura de rede corporativa, como DNS, balanceamento de carga de rede com recursos de afinidade geográfica, firewalls, etc. Todos os componentes e conexões locais são suscetíveis a falhas. Visite a [documentação de planejamento de capacidade AD FS](/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity) para obter mais informações.

> [!NOTE]
>  A Federação tem o número mais alto de dependências locais e, portanto, os pontos de falha mais potenciais. Embora este diagrama mostre AD FS, outros provedores de identidade local estão sujeitos a considerações de design semelhantes para obter alta disponibilidade, escalabilidade e failover.

![Diagrama de arquitetura da Federação](./media/resilience-in-hybrid/admin-resilience-federation.png)

 ### <a name="how-do-i-implement-federation"></a>Como fazer implementar a Federação?

Se você estiver implementando uma estratégia de autenticação federada ou quiser torná-la mais resiliente, consulte os recursos a seguir.

* [O que é autenticação federada](../hybrid/whatis-fed.md)

* [Como funciona a federação](../hybrid/how-to-connect-fed-whatis.md)

* [Lista de compatibilidade de federação do AD do Azure](../hybrid/how-to-connect-fed-compatibility.md)

* Siga a [documentação de planejamento de capacidade AD FS](/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity)

* [Implantando AD FS no Azure IaaS](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)

* [Habilitar o PHS](../hybrid/tutorial-phs-backup.md) juntamente com sua Federação

## <a name="next-steps"></a>Próximas etapas
Recursos de resiliência para administradores e arquitetos
 
* [Crie resiliência com o gerenciamento de credenciais](resilience-in-credentials.md)

* [Crie resiliência com os Estados do dispositivo](resilience-with-device-states.md)

* [Criar resiliência usando a Continuous Access Evaluation (CAE)](resilience-with-continuous-access-evaluation.md)

* [Criar resiliência na autenticação de usuário externa](resilience-b2b-authentication.md)

* [Crie resiliência no acesso do aplicativo com o proxy de aplicativo](resilience-on-premises-access.md)

Recursos de resiliência para desenvolvedores

* [Crie resiliência IAM em seus aplicativos](resilience-app-development-overview.md)

* [Crie resiliência em seus sistemas CIAM](resilience-b2c.md)