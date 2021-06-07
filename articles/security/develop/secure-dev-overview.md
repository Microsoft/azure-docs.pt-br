---
title: Práticas recomendadas de desenvolvimento seguro no Microsoft Azure
description: Práticas recomendadas para ajudá-lo a desenvolver código mais seguro e a implantar um aplicativo mais seguro na nuvem.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: baaa311620f5c10948aa3494002ce359cc5dab28
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94517165"
---
# <a name="secure-development-best-practices-on-azure"></a>Práticas recomendadas de desenvolvimento seguro no Azure
Esta série de artigos apresenta as atividades e os controles de segurança a serem considerados ao desenvolver aplicativos para a nuvem. As fases do Microsoft Security Development Lifecycle (SDL) e das perguntas e conceitos de segurança a serem considerados durante cada fase do ciclo de vida são abordadas. O objetivo é ajudá-lo a definir atividades e serviços do Azure que você pode usar em cada fase do ciclo de vida para projetar, desenvolver e implantar um aplicativo mais seguro.

As recomendações nos artigos vêm de nossa experiência com a segurança do Azure e das experiências de nossos clientes. Você pode usar esses artigos como uma referência para o que deve ser considerado durante uma fase específica do seu projeto de desenvolvimento, mas sugerimos que você também Leia todos os artigos do início ao fim pelo menos uma vez. Ler todos os artigos apresenta os conceitos que você pode ter perdido nas fases anteriores do seu projeto. A implementação desses conceitos antes de você liberar seu produto pode ajudá-lo a criar software seguro, atender aos requisitos de conformidade de segurança e reduzir os custos de desenvolvimento.

Esses artigos destinam-se a ser um recurso para designers de software, desenvolvedores e testadores em todos os níveis que criam e implantam aplicativos seguros do Azure.

## <a name="overview"></a>Visão geral

A segurança é um dos aspectos mais importantes de qualquer aplicativo, e não é uma coisa simples de ficar certo. Felizmente, o Azure fornece muitos serviços que podem ajudá-lo a proteger seu aplicativo na nuvem. Esses artigos abordam as atividades e os serviços do Azure que você pode implementar em cada estágio do seu ciclo de vida de desenvolvimento de software para ajudá-lo a desenvolver um código mais seguro e a implantar um aplicativo mais seguro na nuvem.

## <a name="security-development-lifecycle"></a>Ciclo de vida de desenvolvimento de segurança

Seguir as práticas recomendadas para o desenvolvimento de software seguro requer a integração da segurança em cada fase do ciclo de vida do desenvolvimento de software, da análise de requisitos à manutenção, independentemente da metodologia do projeto ([cascata](https://en.wikipedia.org/wiki/Waterfall_model), [Agile](https://en.wikipedia.org/wiki/Agile_software_development)ou [DevOps](https://en.wikipedia.org/wiki/DevOps)). Na ativação de violações de dados de alto perfil e na exploração de falhas de segurança operacional, mais desenvolvedores estão compreendendo que a segurança precisa ser resolvida em todo o processo de desenvolvimento.

Quanto mais tarde você corrigir um problema no seu ciclo de vida de desenvolvimento, maior será o custo da correção. Problemas de segurança não são exceção. Se você ignorar problemas de segurança nas primeiras fases do desenvolvimento de software, cada fase a seguir poderá herdar as vulnerabilidades da fase anterior. Seu produto final terá acumulado vários problemas de segurança e a possibilidade de uma violação. A criação de segurança em cada fase do ciclo de vida de desenvolvimento ajuda a detectar problemas no início e ajuda a reduzir os custos de desenvolvimento.

Seguimos as fases do [SDL (ciclo de vida do desenvolvimento de segurança)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) da Microsoft para introduzir atividades e serviços do Azure que você pode usar para atender às práticas de desenvolvimento de software seguro em cada fase do ciclo de vida.

As fases do SDL são:

  - Treinamento
  - Requisitos
  - Design
  - Implementação
  - Verificação
  - Versão
  - Resposta

![Ciclo de vida de desenvolvimento de segurança](./media/secure-dev-overview/01-sdl-phase.png)

Nestes artigos, agrupamos as fases do SDL em projetar, desenvolver e implantar.

## <a name="engage-your-organizations-security-team"></a>Envolva a equipe de segurança de sua organização

Sua organização pode ter um programa de segurança de aplicativo formal que ajuda você com atividades de segurança do início ao fim durante o ciclo de vida de desenvolvimento. Se sua organização tiver equipes de segurança e conformidade, não se esqueça de envolvê-las antes de começar a desenvolver seu aplicativo. Pergunte a cada fase do SDL se há alguma tarefa que você perdeu.

Entendemos que muitos leitores podem não ter uma equipe de segurança ou conformidade para se envolver. Esses artigos podem orientá-lo nas perguntas e decisões de segurança que você precisa considerar em cada fase do SDL.

## <a name="resources"></a>Recursos

Use os recursos a seguir para saber mais sobre como desenvolver aplicativos seguros e para ajudar a proteger seus aplicativos no Azure:

[Microsoft Security Development Lifecycle (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) – o SDL é um processo de desenvolvimento de software da Microsoft que ajuda os desenvolvedores a criarem softwares mais seguros. Ele ajuda a resolver os requisitos de conformidade de segurança, reduzindo os custos de desenvolvimento.

[Abra o OWASP (projeto de segurança de aplicativos Web)](https://www.owasp.org/index.php/Main_Page) – o OWASP é uma comunidade online que produz artigos, metodologias, documentação, ferramentas e tecnologias disponíveis gratuitamente no campo de segurança de aplicativos Web.

[Enviando para a esquerda, como um chefe](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) – uma série de artigos online que descreve diferentes tipos de atividades de segurança de aplicativo que os desenvolvedores devem concluir para criar um código mais seguro.

[Plataforma de identidade da Microsoft](../../active-directory/develop/index.yml) – a plataforma de identidade da Microsoft é uma evolução do serviço de identidade do Azure AD e da plataforma de desenvolvedor. É uma plataforma completa que consiste em um serviço de autenticação, bibliotecas de software livre, registro e configuração de aplicativos, documentação completa para desenvolvedores, exemplos de código e outros conteúdos de desenvolvedores. A plataforma de identidade da Microsoft dá suporte a protocolos padrão do setor, como OAuth 2,0 e OpenID Connect.

[Práticas recomendadas de segurança para soluções do Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) – uma coleção de práticas recomendadas de segurança para usar ao projetar, implantar e gerenciar soluções de nuvem usando o Azure. Este documento destina-se a ser um recurso para os profissionais de ti. Isso pode incluir designers, arquitetos, desenvolvedores e testadores que compilam e implantam soluções seguras do Azure.

[Plantas de segurança e conformidade no Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) – plantas de segurança e conformidade do Azure são recursos que podem ajudá-lo a criar e iniciar aplicativos baseados em nuvem que estão em conformidade com regulamentos e padrões rígidos.

## <a name="next-steps"></a>Próximas etapas
Nos artigos a seguir, recomendamos os controles de segurança e as atividades que podem ajudá-lo a projetar, desenvolver e implantar aplicativos seguros.

- [Criar aplicativos seguros](secure-design.md)
- [Desenvolver aplicativos e segurança](secure-develop.md)
- [Implantar aplicativos seguros](secure-deploy.md)