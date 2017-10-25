---
title: "Considerações de design de identidade híbrida do Azure Active Directory - determinar os requisitos de resposta a incidentes | Microsoft Docs"
description: "Determine recursos de monitoramento e emissão de relatórios que podem ser aproveitados pelo departamento de TI para realizar ações de identificação e redução de ameaças potenciais."
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: a3d2a459-599b-4b67-8e51-7369ee25082d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 536071ec61d093af243bfd42faa6bb404172fb8e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>Determinar requisitos de resposta a incidentes para sua solução de identidade híbrida
As organizações de médio ou grande porte provavelmente terão uma [resposta a incidentes de segurança](https://technet.microsoft.com/library/cc700825.aspx) para ajudar o departamento de TI a tomar as ações devidas de acordo com o nível do incidente. O sistema de gerenciamento de identidade é um componente importante do processo de resposta a incidentes, pois ele pode ser usado para ajudar a identificar quem executou uma ação específica em relação ao destino. A solução de identidade híbrida deve ser capaz de fornecer recursos de monitoramento e emissão de relatórios que podem ser aproveitados pelo departamento de TI para realizar ações de identificação e redução de ameaças potenciais. Um plano de resposta a incidentes comum terá as seguintes fases como parte do plano:

1. Avaliação inicial.
2. Comunicação do incidente.
3. Controle de danos e redução de riscos.
4. Identificação do que foi comprometido e a gravidade.
5. Preservação de provas.
6. Notificação às partes relevantes.
7. Recuperação do sistema.
8. Documentação.
9. Avaliação de danos e custos.
10. Revisão do processo e do plano.

Durante a identificação do que foi comprometido e da severidade, será necessário identificar os sistemas que foram comprometidos, os arquivos que foram acessados e determinar a confidencialidade desses arquivos. O sistema de identidade híbrida deve ser capaz de atender a esses requisitos para ajudá-lo a identificar o usuário que fez as alterações. 

## <a name="monitoring-and-reporting"></a>Monitoramento e emissão de relatórios
Muitas vezes, esse sistema de identidade também pode ajudar na fase de avaliação inicial, principalmente se o sistema tem recursos de auditoria e de emissão de relatórios interno. Durante a avaliação inicial, o administrador de TI deve ser capaz de identificar uma atividade suspeita ou o sistema deve ser capaz de acioná-la automaticamente baseado em uma tarefa pré-configurada. Várias atividades podem indicar um possível ataque, mas, em outros casos, um sistema mal configurado pode levar a um número de falsos positivos em um sistema de detecção de intrusão. 

O sistema de gerenciamento de identidade deve ajudar os administradores de TI a identificar e relatar as atividades suspeitas. Normalmente, esses requisitos técnicos podem ser atendidos monitorando-se todos os sistemas e tendo um recurso de relatório que possa realçar possíveis ameaças. Use as perguntas a seguir para ajudá-lo a desenvolver a sua solução de identidade híbrida levando em conta os requisitos de resposta a incidentes:

* Sua empresa tem uma resposta a incidentes de segurança em vigor?
  * Se sim, o sistema de gerenciamento de identidade atual é usado como parte do processo?
* Sua empresa precisa identificar tentativas de logon de usuários suspeitas em diferentes dispositivos?
* Sua empresa precisa detectar as credenciais do usuário potencialmente comprometido?
* Sua empresa precisa auditar o acesso e a ação do usuário?
* Sua empresa precisa saber quando um usuário redefine sua senha?

## <a name="policy-enforcement"></a>Aplicação de políticas
Durante o controle de danos e a fase de redução de riscos, é importante reduzir os efeitos reais e possíveis de um ataque rapidamente. Essa ação que você tomará agora pode fazer a diferença entre ataques de pequenas e grandes proporções. A resposta exata dependerá de sua organização e da natureza do ataque enfrentado. Se a avaliação inicial concluiu que uma conta foi comprometida, você precisará aplicar políticas para bloquear essa conta. Isso é apenas um exemplo em que o sistema de gerenciamento de identidade será utilizado. Use as perguntas a seguir para ajudá-lo a desenvolver a sua solução de identidade híbrida levando em conta como as políticas serão impostas na reação de um incidente em andamento:

* Sua empresa tem políticas in-loco para impedir que os usuários acessem a rede se necessário?
  * Em caso afirmativo, a solução atual integra o sistema de gerenciamento de identidade híbrida que vocês adotam?
* Sua empresa precisará garantir acesso condicional a usuários que estiverem em quarentena? 

> [!NOTE]
> Faça anotações de cada resposta e entenda o raciocínio por trás de resposta. [Definir a estratégia de proteção de dados](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) abordará as opções disponíveis e vantagens/desvantagens de cada opção.  Depois de responder a essas perguntas, você selecionará a opção que melhor se ajusta às necessidades da sua empresa.
> 
> 

## <a name="next-steps"></a>Próximas etapas
[Definir a estratégia de proteção de dados](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Consulte também
[Visão geral sobre as considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)

