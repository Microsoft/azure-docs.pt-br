---
title: "Considerações de design da identidade híbrida do Azure Active Directory - determinar os requisitos de gerenciamento de conteúdo | Microsoft Docs"
description: "Fornece informações sobre como determinar os requisitos de gerenciamento de conteúdo da sua empresa. Normalmente quando o usuário tem seu próprio dispositivo, ele terá também várias credenciais que serão alternadas de acordo com o aplicativo que usa. É importante diferenciar o conteúdo que foi criado usando credenciais pessoais em comparação com aquelas criadas usando credenciais corporativas. Sua solução de identidade deve ser capaz de interagir com os serviços de nuvem para fornecer uma experiência perfeita ao usuário final ao garantir a sua privacidade e aumentar a proteção contra a perda de dados."
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: dd1ef776-db4d-4ab8-9761-2adaa5a4f004
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 840de1e1fcba74285788d51d8f544375f0affa77
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Determinar os requisitos de gerenciamento de conteúdo para sua solução de identidade híbrida
Noções básicas sobre os requisitos de gerenciamento de conteúdo para a sua empresa direta podem afetar sua decisão sobre qual solução de identidade híbrida usar. Com a proliferação de vários dispositivos e a capacidade dos usuários para levar seus próprios dispositivos ([BYOD](http://aka.ms/byodcg)), a empresa precisa proteger seus próprios dados, mas também deve manter a privacidade do usuário intacta. Normalmente quando o usuário tem seu próprio dispositivo, ele terá também várias credenciais que serão alternadas de acordo com o aplicativo que usa. É importante diferenciar o conteúdo que foi criado usando credenciais pessoais em comparação com aquelas criadas usando credenciais corporativas. Sua solução de identidade deve ser capaz de interagir com os serviços de nuvem para fornecer uma experiência perfeita ao usuário final ao garantir a sua privacidade e aumentar a proteção contra a perda de dados. 

Sua solução de identidade será aproveitada por controles de técnicos diferentes para fornecer gerenciamento de conteúdo, como mostrado na figura a seguir:

![](./media/hybrid-id-design-considerations/securitycontrols.png)

**Controles de segurança que aproveitarão o sistema de gerenciamento de identidade**

Em geral, os requisitos de gerenciamento de conteúdo aproveitará o sistema de gerenciamento de identidade nas seguintes áreas:

* Privacidade: identificação do usuário que possui um recurso e aplica os controles apropriados para manter a integridade.
* Classificação de dados: identificar o usuário ou grupo e nível de acesso a um objeto de acordo com sua classificação. 
* Proteção contra vazamento de dados: controles de segurança responsáveis pela proteção de dados para evitar vazamento precisam interagir com o sistema de identidade para validar a identidade do usuário. Isso também é importante para a finalidade de trilha de auditoria.

> [!NOTE]
> Leitura [classificação de dados para preparação de nuvem](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) para obter mais informações sobre as práticas recomendadas e diretrizes para a classificação de dados.
> 
> 

Quando planejar sua solução de identidade híbrida, certifique-se de que as seguintes perguntas serão respondidas de acordo com os requisitos da sua organização:

* Sua empresa tem controles de segurança ativos para aplicar a privacidade dos dados?
  * Em caso positivo, esses controles de segurança poderão integrar com a solução de identidade híbrida que você vai adotar?
* Sua empresa usa a classificação de dados?
  * Em caso positivo, a solução atual é capaz de se integrar com a solução de identidade híbrida que você vai adotar?
* Sua empresa atualmente tem qualquer solução de vazamento de dados? 
  * Em caso positivo, a solução atual é capaz de se integrar com a solução de identidade híbrida que você vai adotar?
* Sua empresa precisa auditar o acesso a recursos?
  * Em caso afirmativo, que tipo de recursos?
  * Em caso afirmativo, qual nível de informação é necessário?
  * Em caso afirmativo, onde o log de auditoria deve residir? Local ou na nuvem?
* Sua empresa precisa criptografar qualquer email que contêm dados confidenciais (SSNs, números de cartão de crédito, etc)?
* Sua empresa precisa criptografar todos os documentos/conteúdos compartilhados com parceiros comerciais externos?
* Sua empresa precisa impor políticas corporativas em determinados tipos de emails (não responder a todos, não encaminhar)?

> [!NOTE]
> Certifique-se de fazer anotações de cada resposta e entender o raciocínio por trás de resposta. [Definir a estratégia de proteção de dados definir](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) ultrapassará as opções disponíveis e vantagens/desvantagens de cada opção.  Ao responder essas perguntas, você selecionará a opção que melhor se ajusta às necessidades da sua empresa.
> 
> 

## <a name="next-steps"></a>Próximas etapas
[Determinar requisitos de controle de acesso](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Consulte também
[Visão geral sobre as considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)

