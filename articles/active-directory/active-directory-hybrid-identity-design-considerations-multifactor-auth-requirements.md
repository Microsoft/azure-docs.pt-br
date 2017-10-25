---
title: "Considerações sobre design da identidade híbrida do Active Directory do Azure - determinar os requisitos da autenticação multifator"
description: "Com o controle de acesso condicional, o Active Directory do Azure verifica as condições específicas escolhidas para autenticação do usuário, antes de permitir o acesso ao aplicativo. Quando essas condições forem atendidas, o usuário é autenticado e autorizado a acessar o aplicativo."
documentationcenter: 
services: active-directory
author: femila
manager: billmath
editor: 
ms.assetid: 9c59fda9-47d0-4c7e-b3e7-3575c29beabe
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 5b3a8ce6e4203dfb3700f324e32687dd910118af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>Determinar os requisitos de autenticação multifator para sua solução de identidade híbrida
Em um mundo de mobilidade, com os usuários que acessam dados e aplicativos na nuvem e de qualquer dispositivo, proteger essas informações tornou imprescindível.  Todos os dias há um novo título sobre violação de segurança.  No entanto, não há nenhuma garantia contra falhas desse tipo, a autenticação multifator, fornece uma camada adicional de segurança para ajudar a evitar essas violações.
Comece pela avaliação das necessidades de organizações para autenticação multifator. Ou seja, o que a organização está tentando proteger.  Essa avaliação é importante para definir os requisitos técnicos para configurar e habilitar os usuários de organizações para autenticação multifator.

> [!NOTE]
> Se você não estiver familiarizado com o MFA e o que ele faz, é altamente recomendável que você leia o artigo [O que é o Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md) antes de continuar a ler esta seção.
> 
> 

Certifique-se de responder ao seguinte:

* Sua empresa está tentando proteger aplicativos Microsoft? 
* Como esses aplicativos são publicados?
* Sua empresa oferecer acesso remoto para permitir que os funcionários acessem aplicativos locais?

Em caso afirmativo, que tipo de acesso remoto? Você também precisa avaliar onde os usuários que estiverem acessando esses aplicativos estarão. Essa avaliação é outro passo importante para definir a estratégia de autenticação multifator adequada. Certifique-se de responder às seguintes perguntas:

* Onde os usuários estarão?
* Podem ser localizados em qualquer lugar?
* Sua empresa deseja estabelecer restrições de acordo com a localização do usuário?

Depois de compreender esses requisitos, é importante também avaliar os requisitos do usuário para autenticação multifator. Essa avaliação é importante que porque definirá os requisitos para implantaram uma autenticação multifator. Certifique-se de responder às seguintes perguntas:

* O s usuários estão familiarizados com a autenticação multifator?
* Alguns usos serão necessários para fornecer autenticação adicional?  
  * Em caso afirmativo, o tempo todo, quando se originar de redes externas ou acessar aplicativos específicos ou em outras condições?
* Os usuários exigirão treinamento sobre como configurar e implementar a autenticação multifator?
* Quais são os cenários principais que sua empresa quer habilitar a autenticação multifator para seus usuários?

Depois de responder às perguntas anteriores, você poderá determinar se há autenticação multifator já implementada localmente. Essa avaliação é importante para definir os requisitos técnicos para configurar e habilitar os usuários de organizações para autenticação multifator. Certifique-se de responder às seguintes perguntas:

* Sua empresa precisa proteger contas privilegiadas com MFA?
* Sua empresa precisa habilitar a MFA para determinado aplicativo por motivos de conformidade?
* Sua empresa precisa habilitar a MFA para todos os usuários qualificados desses aplicativos ou apenas os administradores?
* Você precisa ter o MFA sempre habilitado ou somente quando os usuários estão conectados fora da rede corporativa?

## <a name="next-steps"></a>Próximas etapas
[Definir uma estratégia de adoção de identidade híbrida](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## <a name="see-also"></a>Confira também
[Visão geral sobre as considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)

