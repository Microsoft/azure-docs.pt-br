---
title: Teste de Caneta | Microsoft Docs
description: O artigo fornece uma visão geral do processo (teste) dos testes de penetração e de como executar o teste de caneta em seus aplicativos em execução na infraestrutura do Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2018
ms.author: barclayn
ms.openlocfilehash: 8835c4534b6dab1e8dbfb3546257ae4bc3b9d7af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610919"
---
# <a name="penetration-testing"></a>Teste de penetração
Um dos benefícios do uso do Azure para implantação e testes de aplicativos é que você pode criar rapidamente ambientes. Você não precisa se preocupar sobre requisição, aquisição e "posicionamento no rack e empilhamento" do seu próprio hardware local.

Isso é ótimo, mas você ainda precisará certificar-se de executar a inspeção de segurança normal. Uma das coisas que você provavelmente desejará fazer é penetração teste os aplicativos que você implanta no Azure.

Talvez você já saiba que a Microsoft realiza [teste de penetração do nosso ambiente do Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Isso ajuda a gerar melhorias no Azure.

Nós não o teste de penetração seu aplicativo para você, mas entendemos que você deseja e precisa executar testes em seus próprios aplicativos. Isso é algo bom, porque quando você aumenta a segurança de seus aplicativos de ajudar a proteger melhor a todo o ecossistema do Azure.

A partir de 15 de junho de 2017, a Microsoft não exige aprovação prévia para realizar um teste de penetração com base nos recursos do Azure. Os clientes que desejam documentar formalmente futuros testes de penetração no Microsoft Azure são incentivados a preencher o [formulário Notificação de teste de penetração do Azure Service](https://portal.msrc.microsoft.com/en-us/engage/pentest). Esse processo só está relacionado ao Microsoft Azure e não se aplica a outro serviço do Microsoft Cloud.

>[!IMPORTANT]
>Embora a notificação da Microsoft da caneta as atividades de teste não é mais necessário clientes ainda devem estar de acordo com o [Microsoft nuvem unificado penetração teste regras](https://technet.microsoft.com/mt784683).

Os testes padrão que você pode executar incluem:

* Testes em seus pontos de extremidade para revelar as [10 maiores vulnerabilidades do OWASP (Projeto de Segurança de Aplicativo Web Aberto)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Teste de fuzzing](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) de seus pontos de extremidade
* [Exame de portas](https://en.wikipedia.org/wiki/Port_scanner) de seus pontos de extremidade

Um tipo de teste que você não pode executar é qualquer tipo de ataque [DoS (negação de serviço)](https://en.wikipedia.org/wiki/Denial-of-service_attack) . Isso inclui iniciar um ataque DoS em si ou a realização de testes relacionados que podem determinar, demonstrar ou simular qualquer tipo de ataque DoS.

## <a name="next-steps"></a>Próximas etapas

- Se você gostaria de documentar formalmente uma penetração futura testes em seus aplicativos hospedados no Microsoft Azure, vá diretamente para o [penetração teste regras de participação](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2) e preencha o formulário de notificação de teste.
