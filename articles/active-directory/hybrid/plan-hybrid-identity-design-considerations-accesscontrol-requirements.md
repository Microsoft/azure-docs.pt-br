---
title: Design de identidade híbrida - requisitos de controle de acesso do Azure | Microsoft Docs
description: Aborda os pilares da identidade e requisitos de acesso de identificação de recursos para usuários em um ambiente híbrido.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: e3b3b984-0d15-4654-93be-a396324b9f5e
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84b786a1701892823554a83fa2015ac88d6eff4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "60295136"
---
# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>Determina os requisitos de controle de acesso para sua solução de identidade híbrida
Quando uma organização está projetando sua solução de identidade híbrida, ela também pode usar essa oportunidade para examinar os requisitos de acesso para os recursos que pretende tornar disponível aos usuários. O acesso a dados cruza todos os quatro pilares da identidade, que são:

* Administração
* Autenticação
* Autorização
* Auditoria

As seções a seguir abordarão a autenticação e a autorização com mais detalhes; a administração e a auditoria são parte do ciclo de vida da identidade híbrida. Leia [Determinar as tarefas de gerenciamento de identidade híbrida](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) para saber mais sobre esses recursos.

> [!NOTE]
> Leia [Os quatro pilares da identidade - gerenciamento de identidade na era da TI híbrida](https://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) para saber mais sobre cada um desses pilares.
> 
> 

## <a name="authentication-and-authorization"></a>Autenticação e autorização
Há cenários diferentes para autenticação e autorização; esses cenários terão requisitos específicos que devem ser atendidos pela solução de identidade híbrida que a empresa adotar. Cenários que envolvem a comunicação entre empresas (B2B) podem representar um desafio adicional para administradores de TI, pois estes precisam garantir que os métodos de autenticação e autorização usados pela organização possam se comunicar com seus parceiros comerciais. Durante o processo de criação de requisitos de autenticação e autorização, verifique se as seguintes perguntas são respondidas:

* Sua organização irá autenticar e autorizar somente os usuários localizados em seu sistema de gerenciamento de identidade?
  * Há planos para cenários B2B?
  * Se sim, você já sabe quais protocolos (SAML, OAuth, Kerberos ou Certificados) serão usados para conectar as duas empresas?
* A solução de identidade híbrida que vocês vão adotar dá suporte a esses protocolos?

Outro ponto importante a considerar é onde o repositório de autenticação que será usado pelos usuários e parceiros estará localizado e o modelo administrativo que será usado. Considere as duas opções principais a seguir:

* Centralizado: nesse modelo, as políticas, a administração e as credenciais do usuário podem ser centralizadas localmente ou na nuvem.
* Híbrido: nesse modelo, as políticas, a administração e as credenciais do usuário serão centralizadas localmente e replicadas na nuvem.

Qual modelo sua organização adotará vai variar de acordo com suas necessidades de negócios. Responda às seguintes perguntas para identificar onde residirá o sistema de gerenciamento de identidade e o modo de uso administrativo:

* Sua organização tem atualmente um gerenciamento de identidade local?
  * Se sim, ela pretende mantê-lo?
  * Há algum requisito de normas ou de conformidade que sua organização deve seguir que defina onde deve residir o sistema de gerenciamento de identidade?
* Sua organização usa o logon único para aplicativos localizados no local ou na nuvem?
  * Em caso afirmativo, a adoção de um modelo de identidade híbrida afeta esse processo?

## <a name="access-control"></a>Controle de acesso
Embora a autenticação e a autorização sejam os principais elementos para habilitar o acesso a dados corporativos usando a validação do usuário, também é importante controlar o nível de acesso que esses usuários terão e o nível de acesso que os administradores terão sobre os recursos que gerenciam. Sua solução de identidade híbrida deve ser capaz de fornecer acesso granular aos recursos, à delegação e ao controle de acesso baseado em função. Verifique se as seguintes perguntas são respondidas sobre o controle de acesso:

* Sua empresa tem mais de um usuário com privilégio elevado para gerenciar seu sistema de identidade?
  * Em caso afirmativo, cada usuário precisa do mesmo nível de acesso?
* Sua empresa precisa delegar acesso aos usuários para gerenciar recursos específicos?
  * Em caso afirmativo, com que frequência isso acontece?
* Sua empresa precisa integrar os recursos de controle de acesso entre recursos locais e na nuvem?
* Sua empresa precisa limitar o acesso aos recursos de acordo com algumas condições?
* Sua empresa tem qualquer aplicativo que precise acessar alguns recursos de controle de acesso personalizado?
  * Em caso afirmativo, onde estão localizados os aplicativos (no local ou na nuvem)?
  * Em caso afirmativo, onde estão localizados os recursos de destino (no local ou na nuvem)?

> [!NOTE]
> Faça anotações de cada resposta e entenda o raciocínio por trás da resposta. [Definir a estratégia de proteção de dados definir](plan-hybrid-identity-design-considerations-data-protection-strategy.md) ultrapassará as opções disponíveis e vantagens/desvantagens de cada opção.  Depois de responder a essas perguntas, você selecionará a opção que melhor se ajusta às necessidades de sua empresa.
> 
> 

## <a name="next-steps"></a>Próximas etapas
[Determinar requisitos de resposta a incidentes](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Consulte Também
[Visão geral sobre as considerações de design](plan-hybrid-identity-design-considerations-overview.md)

