---
title: Práticas recomendadas no Microsoft Azure de desenvolvimento seguro
description: Práticas recomendadas para ajudá-lo a desenvolver código mais seguro e implantar um aplicativo mais seguro na nuvem.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: ee5c043038fbb747e90bca9530cbe2e94c8a95c2
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144458"
---
# <a name="secure-development-best-practices-on-azure"></a>Práticas recomendadas no Azure de desenvolvimento seguro
Esta série de artigos apresenta as atividades de segurança e controles a serem considerados ao desenvolver aplicativos para a nuvem. As fases do Microsoft Security Development Lifecycle (SDL) e perguntas de segurança e conceitos a serem considerados durante cada fase do ciclo de vida são abordadas. O objetivo é ajudá-lo a definir as atividades e serviços do Azure que você pode usar em cada fase do ciclo de vida para criar, desenvolver e implantar um aplicativo mais seguro.

As recomendações nos artigos vêm da nossa experiência com a segurança do Azure e com as experiências de nossos clientes. Você pode usar estes artigos como uma referência para o que você deve considerar durante uma fase específica de seu projeto de desenvolvimento, mas sugerimos que você leia também por meio de todos os artigos do início ao fim, pelo menos uma vez. Ler todos os artigos apresenta os conceitos que você pode ter perdido nas fases anteriores do seu projeto. Implementar esses conceitos antes de liberar seu produto pode ajudá-lo a criar softwares seguros, atender aos requisitos de conformidade de segurança e reduzir os custos de desenvolvimento.

Esses artigos devem ser um recurso para designers de software, os desenvolvedores, e os testadores em todos os níveis que criar e implantar aplicativos seguros do Azure.

## <a name="overview"></a>Visão geral

A segurança é um dos aspectos mais importantes de qualquer aplicativo, e não é uma solução simples para acertar. Felizmente, o Azure fornece muitos serviços que podem ajudar a proteger seu aplicativo na nuvem. Estes artigos abordam as atividades e serviços do Azure, você pode implementar em cada estágio do seu ciclo de vida de desenvolvimento de software para ajudá-lo a desenvolver código mais seguro e implantar um aplicativo mais seguro na nuvem.

## <a name="security-development-lifecycle"></a>Ciclo de vida de desenvolvimento de segurança

Seguir as práticas recomendadas para desenvolvimento de software seguro requer a integração de segurança em cada fase do ciclo de desenvolvimento de software, da análise de requisito para manutenção, independentemente da metodologia do projeto ([cascata](https://en.wikipedia.org/wiki/Waterfall_model), [agile](https://en.wikipedia.org/wiki/Agile_software_development), ou [DevOps](https://en.wikipedia.org/wiki/DevOps)). Em andamento de violações de dados de alto perfil e a exploração de falhas de segurança operacional, mais desenvolvedores estão Noções básicas sobre segurança precisa ser resolvido em todo o processo de desenvolvimento.

Depois de corrigir um problema em seu ciclo de vida de desenvolvimento, mais que corrigem irá custam. Problemas de segurança não são exceção. Se você ignorar problemas de segurança nas fases iniciais do seu desenvolvimento de software, cada fase que segue pode herdar as vulnerabilidades da fase anterior. O produto final serão acumulados vários problemas de segurança e a possibilidade de uma violação. Criação de segurança em cada fase do ciclo de vida de desenvolvimento ajuda você a capturar problemas antecipadamente e ajuda a reduzir os custos de desenvolvimento.

Seguimos as fases da Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) introduzir as atividades e os serviços do Azure que você pode usar para atender às práticas de desenvolvimento de software seguro em cada fase do ciclo de vida.

As fases do SDL são:

  - Treinamento
  - Requisitos
  - Design
  - Implementação
  - Verificação
  - Versão
  - Response

![Ciclo de vida de desenvolvimento de segurança](./media/secure-dev-overview/01-sdl-phase.png)

Nesses artigos, que agrupamos as fases do SDL em design, desenvolver e implantar.

## <a name="engage-your-organizations-security-team"></a>Entre em contato com equipe de segurança da sua organização

Sua organização pode ter um programa de segurança de aplicativo formal que ajuda você com as atividades de segurança do início ao fim do ciclo de vida de desenvolvimento. Se sua organização tiver equipes de conformidade e segurança, certifique-se de que envolva-o antes de começar a desenvolver seu aplicativo. Peça a eles em cada fase do SDL se há qualquer tarefa que você tenha perdido.

Entendemos que muitos leitores talvez não tenha uma equipe de segurança ou conformidade para envolver. Esses artigos podem ajudar a orientá-lo nas perguntas de segurança e as decisões que você precisa considerar em cada fase do SDL.

## <a name="resources"></a>Recursos

Para saber mais sobre como desenvolver aplicativos seguros e para ajudar a proteger seus aplicativos no Azure, use os seguintes recursos:

[Microsoft Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) – o SDL é um processo de desenvolvimento de software da Microsoft que ajuda os desenvolvedores a criarem softwares mais seguros. Ele ajuda você a atender a requisitos de conformidade de segurança, reduzindo os custos de desenvolvimento.

[Abra o Web Application Security OWASP (projeto)](https://www.owasp.org/index.php/Main_Page) – OWASP é uma comunidade online que produz artigos disponíveis gratuitamente, metodologias, documentação, ferramentas e tecnologias no campo da segurança do aplicativo web.

[Enviar por push para a esquerda, como um chefe](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) – uma série de artigos online que descreve os diferentes tipos de atividades de segurança de aplicativo que os desenvolvedores devem ser concluídas para criar código mais seguro.

[Plataforma de identidade Microsoft](https://docs.microsoft.com/azure/active-directory/develop/) – plataforma de identidade da Microsoft é uma evolução da plataforma de serviço e desenvolvedor de identidade do AD do Azure. É uma plataforma completa que consiste em um serviço de autenticação, bibliotecas de código-fonte aberto, registro de aplicativo e configuração, documentação do desenvolvedor completo, exemplos de código e outros conteúdos do desenvolvedor. A plataforma de identidade da Microsoft dá suporte a protocolos padrão do setor, como OAuth 2.0 e OpenID Connect.

[Práticas recomendadas de segurança para soluções do Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) – uma coleção de práticas recomendadas de segurança a ser usado ao criar, implantar e gerenciar soluções de nuvem usando o Azure. Este artigo é destinado a ser um recurso para profissionais de TI. Isso pode incluir designers, arquitetos, desenvolvedores e testadores que compilam e implantam soluções seguras do Azure.

[Segurança e planos gráficos de conformidade no Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) – planos gráficos de conformidade e de segurança do Azure são recursos que podem ajudar você a compilar e iniciam os aplicativos em nuvem que estão em conformidade com os padrões e normas rígidas.

## <a name="next-steps"></a>Próximas etapas
Os artigos a seguir, é recomendável que os controles de segurança e as atividades que podem ajudá-lo a projetarem, desenvolvem e implantar aplicativos seguros.

- [Design de aplicativos seguros](secure-design.md)
- [Desenvolver aplicativos seguros](secure-develop.md)
- [Implantar aplicativos seguros](secure-deploy.md)
