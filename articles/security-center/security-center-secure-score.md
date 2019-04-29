---
title: Pontuação segura no Azure Security Center | Microsoft Docs
description: " Priorize suas recomendações de segurança usando a pontuação segura na Central de Segurança do Azure. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: monhaber
ms.openlocfilehash: 22791fc43ff17d56e1f51e7f7737a10109f47c59
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60906123"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Melhorar sua pontuação segura na Central de segurança do Azure


Com tantos serviços oferecendo os benefícios de segurança, muitas vezes é difícil saber quais etapas seguir primeiro para proteger e otimizar sua carga de trabalho. A pontuação segura do Azure analisa suas recomendações de segurança e as prioriza para você, para que você saiba quais recomendações devem ser executadas primeiro. Isso ajuda a encontrar as vulnerabilidades de segurança mais sérias para que você possa priorizar a investigação. A pontuação segura é uma ferramenta que ajuda a avaliar sua postura de segurança de carga de trabalho.

## <a name="secure-score-calculation"></a>Proteger o cálculo de pontuação

A Central de Segurança reproduz o trabalho de um analista de segurança, revisa suas recomendações de segurança e aplica algoritmos avançados para determinar a importância de cada recomendação.
O Centro de Segurança do Azure analisa constantemente as recomendações ativas e calcula sua pontuação segura com base nelas. A pontuação de uma recomendação é derivada de sua gravidade e das práticas recomendadas de segurança que afetarão mais a segurança da sua carga de trabalho.

A Central de Segurança do Azure também fornece uma **pontuação geral segura**. 

**A pontuação geral segura** é uma acumulação de todas as suas pontuações de recomendação. Você pode exibir sua pontuação geral segura entre suas assinaturas ou grupos de gerenciamento, dependendo do que você selecionar. A pontuação varia de acordo com a assinatura selecionada e as recomendações ativas nessas assinaturas.

 
Para verificar quais recomendações impactam mais a sua pontuação segura, você pode visualizar as três principais recomendações mais impactantes no painel da Central de Segurança ou pode classificar as recomendações na lista de recomendações usando a coluna **Impacto da pontuação de impacto**.


Para visualizar sua pontuação geral segura:

1. No painel do Azure, clique em **Central de Segurança** e, em seguida, clique em **Classificação de segurança**.
2. Na parte superior, é possível ver os destaques da Classificação de Segurança:
   - A **Classificação de segurança geral** representa a classificação por políticas, por assinatura selecionada
   - **Classificação de segurança por categoria** mostra quais recursos precisam de mais atenção
   - **Principais recomendações por impacto da classificação de segurança** fornece uma lista das recomendações que mais melhorarão sua classificação de segurança se você implementá-las.
 
   ![Pontuação segura](./media/security-center-secure-score/secure-score-dashboard.png)

3. Na tabela abaixo, você pode ver cada uma das suas assinaturas e a classificação de segurança geral para cada uma.

   > [!NOTE]
   > A soma da classificação de segurança de cada assinatura não é igual à classificação de segurança geral. A classificação de segurança é um cálculo baseado na proporção entre seus recursos íntegros e seus recursos totais por recomendação, não uma soma das classificações de segurança de todas as assinaturas. 
   >
4. Clique em **Exibir recomendações** para ver as recomendações para essa assinatura que você pode corrigir para melhorar sua classificação de segurança.
4. Na lista de recomendações, você pode ver que, para cada recomendação, há uma coluna que representa o **impacto da classificação de segurança**. Esse número representa quanto sua pontuação geral segura melhorará se você seguir as recomendações. Por exemplo, na tela abaixo, se você **corrigir as vulnerabilidades nas configurações de segurança do contêiner**, sua pontuação segura aumentará em 35 pontos.

   ![Pontuação segura](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Pontuação segura individuais

Além disso, para visualizar pontuações seguras individuais, você pode encontrá-las dentro da área de recomendação individual.  

A **pontuação segura da recomendação** é um cálculo baseado na proporção entre seus recursos saudáveis e seus recursos totais. Se o número de recursos saudáveis for igual ao número total de recursos, você obtém a pontuação máxima segura da recomendação de 50. Para tentar obter sua pontuação segura mais próxima da pontuação máxima, corrija os recursos não íntegros seguindo as recomendações.

O **impacto recomendação** permite que você saiba o quanto melhora sua pontuação segura se você aplicar as etapas de recomendação. Por exemplo, se sua pontuação segura for 42 e o **impacto da recomendação** for +3, a execução das etapas descritas na recomendação melhora sua pontuação para se tornar 45.

A recomendação mostra quais ameaças sua carga de trabalho está exposta se as etapas de correção não forem executadas.

![pontuação de recomendação individual seguro](./media/security-center-secure-score/indiv-recommendation-secure-score.png)







## <a name="next-steps"></a>Próximas etapas
Este artigo mostrou como melhorar sua postura de segurança usando **Pontuação segura** na Central de Segurança do Azure. Para saber mais sobre a Central de Segurança, confira:

* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre perguntas frequentes sobre como usar o serviço.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md)– saiba como monitorar a integridade dos recursos do Azure.


