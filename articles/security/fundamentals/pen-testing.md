---
title: Teste de penetração | Microsoft Docs
description: O artigo fornece uma visão geral do processo de teste de penetração e como executar um teste de caneta em seu aplicativo em execução na infraestrutura do Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: terrylan
ms.openlocfilehash: 9f65133f622c71189f35a1533e278ca09ab02085
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99550194"
---
# <a name="penetration-testing"></a>Teste de penetração

Um dos benefícios do uso do Azure para implantação e testes de aplicativos é que você pode criar rapidamente ambientes. Você não precisa se preocupar sobre requisição, aquisição e "posicionamento no rack e empilhamento" do seu próprio hardware local.

A criação rápida de ambientes é excelente, mas você ainda precisa ter certeza de que executou sua auditoria normal de segurança. Uma das coisas que você provavelmente quer fazer é o teste de penetração dos aplicativos implantados no Azure.

Não realizamos testes de penetração de seu aplicativo para você, mas entendemos que você deseja e precisa executar testes em seus próprios aplicativos. Isso é bom, porque ao aprimorar a segurança de seus aplicativos, você ajuda a tornar todo o ecossistema do Azure mais seguro.

Desde 15 de junho de 2017, a Microsoft não exige aprovação prévia para realizar testes de penetração com base nos recursos do Azure. Esse processo só está relacionado ao Microsoft Azure e não se aplica a outro serviço do Microsoft Cloud.

>[!IMPORTANT]
>Embora a notificação da Microsoft da caneta as atividades de teste não é mais necessário clientes ainda devem estar de acordo com o [Microsoft nuvem unificado penetração teste regras](https://technet.microsoft.com/mt784683).

Os testes padrão que você pode executar incluem:

* Testes em seus pontos de extremidade para revelar as [10 maiores vulnerabilidades do OWASP (Projeto de Segurança de Aplicativo Web Aberto)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Teste de fuzzing](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) de seus pontos de extremidade
* [Exame de portas](https://en.wikipedia.org/wiki/Port_scanner) de seus pontos de extremidade

Um tipo de teste de caneta que você não pode executar é qualquer tipo de ataque de [dos (negação de serviço)](https://en.wikipedia.org/wiki/Denial-of-service_attack) . Esse teste inclui o início de um ataque de DoS em si ou a execução de testes relacionados que podem determinar, demonstrar ou simular qualquer tipo de ataque de DoS.

>[!Note]
>A Microsoft firmou parceria com BreakingPoint Cloud para criar uma interface em que você pode gerar tráfego contra endereços IP públicos com a Proteção contra DDoS habilitada para fins de simulação. Para saber mais sobre a simulação de nuvem do BreakingPoint, confira [teste por meio de simulações](../../ddos-protection/test-through-simulations.md).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre as [regras de teste de penetração do Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2).
