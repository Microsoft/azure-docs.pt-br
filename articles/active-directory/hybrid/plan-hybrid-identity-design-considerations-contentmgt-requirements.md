---
title: Design de identidade híbrida - requisitos de gerenciamento de conteúdo do Azure | Microsoft Docs
description: Fornece informações sobre como determinar os requisitos de gerenciamento de conteúdo da sua empresa. Normalmente quando o usuário tem seu próprio dispositivo, ele terá também várias credenciais que serão alternadas de acordo com o aplicativo que usa. É importante diferenciar o conteúdo que foi criado usando credenciais pessoais em comparação com aquelas criadas usando credenciais corporativas. Sua solução de identidade deve ser capaz de interagir com os serviços de nuvem para fornecer uma experiência perfeita ao usuário final ao garantir a sua privacidade e aumentar a proteção contra a perda de dados.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: dd1ef776-db4d-4ab8-9761-2adaa5a4f004
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57990fda7475b95bd6582fa5a495ac8d24aa55d5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94408525"
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Determinar os requisitos de gerenciamento de conteúdo para sua solução de identidade híbrida
Noções básicas sobre os requisitos de gerenciamento de conteúdo para a sua empresa direta podem afetar sua decisão sobre qual solução de identidade híbrida usar. Com a proliferação de vários dispositivos e a capacidade dos usuários para levar seus próprios dispositivos ([BYOD](/mem/intune/fundamentals/byod-technology-decisions)), a empresa precisa proteger seus próprios dados, mas também deve manter a privacidade do usuário intacta. Normalmente quando o usuário tem seu próprio dispositivo, ele terá também várias credenciais que serão alternadas de acordo com o aplicativo que usa. É importante diferenciar o conteúdo que foi criado usando credenciais pessoais em comparação com aquelas criadas usando credenciais corporativas. Sua solução de identidade deve ser capaz de interagir com os serviços de nuvem para fornecer uma experiência perfeita ao usuário final ao garantir a sua privacidade e aumentar a proteção contra a perda de dados. 

Sua solução de identidade será aproveitada por controles de técnicos diferentes para fornecer gerenciamento de conteúdo, como mostrado na figura a seguir:

![controles de segurança](./media/plan-hybrid-identity-design-considerations/securitycontrols.png)

**Controles de segurança que aproveitarão o sistema de gerenciamento de identidade**

Em geral, os requisitos de gerenciamento de conteúdo aproveitará o sistema de gerenciamento de identidade nas seguintes áreas:

* Privacidade: identificação do usuário que possui um recurso e aplica os controles apropriados para manter a integridade.
* Classificação de dados: identificar o usuário ou grupo e nível de acesso a um objeto de acordo com sua classificação. 
* Proteção contra vazamento de dados: controles de segurança responsáveis pela proteção de dados para evitar vazamento precisam interagir com o sistema de identidade para validar a identidade do usuário. Isso também é importante para a finalidade de trilha de auditoria.

> [!NOTE]
> Leitura [classificação de dados para preparação de nuvem](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) para obter mais informações sobre as práticas recomendadas e diretrizes para a classificação de dados.
> 
> 

Quando planejar sua solução de identidade híbrida, verifique se as seguintes perguntas são respondidas de acordo com os requisitos da sua organização:

* Sua empresa tem controles de segurança ativos para aplicar a privacidade dos dados?
  * Em caso positivo, os controles de segurança poderão ser integrados à solução de identidade híbrida que você vai adotar?
* Sua empresa usa a classificação de dados?
  * Em caso positivo, a solução atual é capaz de se integrar com a solução de identidade híbrida que você vai adotar?
* Sua empresa atualmente tem qualquer solução de vazamento de dados? 
  * Em caso positivo, a solução atual é capaz de se integrar com a solução de identidade híbrida que você vai adotar?
* Sua empresa precisa auditar o acesso a recursos?
  * Em caso afirmativo, que tipo de recursos?
  * Em caso afirmativo, qual nível de informação é necessário?
  * Em caso afirmativo, onde o log de auditoria deve residir? Local ou na nuvem?
* Sua empresa precisa criptografar emails que contêm dados confidenciais (SSNs, números de cartão de crédito, etc)?
* Sua empresa precisa criptografar todos os documentos/conteúdos compartilhados com parceiros comerciais externos?
* Sua empresa precisa impor políticas corporativas em determinados tipos de emails (não responder a todos, não encaminhar)?

> [!NOTE]
> Faça anotações de cada resposta e entenda o raciocínio por trás da resposta. [Definir a estratégia de proteção de dados definir](plan-hybrid-identity-design-considerations-data-protection-strategy.md) ultrapassará as opções disponíveis e vantagens/desvantagens de cada opção.  Ao responder essas perguntas, você selecionará a opção que melhor se ajusta às necessidades da sua empresa.
> 
> 

## <a name="next-steps"></a>Próximas etapas
[Determinar requisitos de controle de acesso](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Consulte Também
[Visão geral sobre as considerações de design](plan-hybrid-identity-design-considerations-overview.md)