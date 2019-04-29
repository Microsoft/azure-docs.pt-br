---
title: Investigar os casos com Azure Sentinel Preview | Microsoft Docs
description: Use este tutorial para saber como investigar casos com Sentinel do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a493cd67-dc70-4163-81b8-04a9bc0232ac
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: 6b3357ec06c89645b9c41e9efdb582a18af40672
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614649"
---
# <a name="tutorial-investigate-cases-with-azure-sentinel-preview"></a>Tutorial: Investigar os casos com Azure Sentinel Preview

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este tutorial ajuda você a detectar ameaças com o Azure Sentinel.

Depois que você [suas fontes de dados conectadas](quickstart-onboard.md) ao Azure Sentinel, você deseja ser notificado quando acontece algo suspeito. Para que você possa fazer isso, Sentinel do Azure permite que você crie regras de alerta, que geram casos em que você pode atribuir e avançadas use profundamente investigar ameaças em seu ambiente e anomalias. 

> [!div class="checklist"]
> * Criar casos de
> * Investigue casos
> * Responda às ameaças

## <a name="investigate-cases"></a>Investigue casos

Um caso pode incluir vários alertas. É uma agregação de todos os as evidências relevantes para uma investigação específica. Um caso é criado com base em alertas definidos na **Analytics** página. As propriedades relacionadas a alertas, como severidade e o status são definidas no nível do caso. Depois que você informe Sentinel Azure quais tipos de ameaças que você está procurando e como encontrá-las, você pode monitorar as ameaças detectadas investigando os casos. 

1. Selecione **casos**. O **casos** página permite que você saiba quantos casos você tem, quantos são abrir, quantos você definiu **em andamento**, e quantos são fechados. Para cada caso, você pode ver a hora em que ele ocorreu e o status do caso. Examinar a severidade para decidir o que lidar com o primeiro. No **casos** , clique no **alertas** guia para ver todos os alertas relacionados a um caso. Entidades que você mapeou anteriormente como parte do caso de pode ser exibido na **entidades** guia.  Você pode filtrar os casos, conforme necessário, por exemplo, status ou gravidade. Quando você observa a **casos** guia, você verá casos abertos que contêm os alertas disparados por suas regras de detecção definidas no **Analytics**. Na parte superior verá seus casos ativos, os novos casos e, em casos de progresso. Você também pode ver uma visão geral de todos os seus casos por severidade.

   ![Painel de alerta](./media/tutorial-investigate-cases/cases.png)

2. Para começar uma investigação, clique em um caso específico. À direita, você pode ver informações detalhadas para o caso, incluindo sua severidade, resumida do número de entidades envolvidas (com base em seu mapeamento). Cada caso tem uma ID exclusiva. A severidade do caso é determinada de acordo com o alerta mais grave incluído no caso.  

1. Para exibir mais detalhes sobre os alertas e as entidades no caso, clique em **exibir detalhes completos** no caso de página e examine as guias relevantes que resumem as informações do caso.  Modo de exibição completo caso consolida todas as evidências no alerta, os alertas associados e entidades.

1. No **alertas** guia, examine o próprio - alerta quando ele foi acionado e por quanto excedeu os limites que você definir. Você pode ver todas as informações relevantes sobre o alerta – a consulta que disparou o alerta, o número de resultados retornados por consultas e a capacidade de executar Guias estratégicos nos alertas. Para fazer drill down ainda mais para o caso, clique no número de ocorrências. Isso abre a consulta que gerou os resultados e os resultados que dispararam o alerta no Log Analytics.

3. No **entidades** guia, você pode ver todas as entidades que você mapeou como parte da definição de regra de alerta. 

4. Se você estiver investigando ativamente um caso, ele é uma boa ideia definir o status do caso para **em andamento** até que você fechá-lo. Você também pode fechar o caso, onde **fechado resolvido** é o status de casos que indicam que um incidente foi tratado, enquanto **fechado ignorado** é o status de casos que não exigem tratamento. Explicações são necessárias explicando seu raciocínio para um caso de fechamento.

5. Casos podem ser atribuídos a um usuário específico. Para cada caso, você pode atribuir um proprietário, definindo o caso **proprietário** campo. Início de todos os casos como não atribuído. Você pode ir para os casos e filtrar por seu nome para ver todos os casos em que você possui. 

5. Clique em **investigar** para exibir o mapa de investigação e o escopo da violação com etapas de correção. 



## <a name="respond-to-threats"></a>Responda às ameaças

Sentinela do Azure oferece duas opções principais para responder às ameaças usando as guias estratégicos. Você pode definir um guia estratégico para serem executados automaticamente quando um alerta é disparado, ou você pode executar manualmente um guia estratégico em resposta a um alerta.

- Você pode definir um guia estratégico para serem executados automaticamente quando um alerta é disparado quando você configura o Guia estratégico. 

- Você pode executar manualmente um guia estratégico de dentro do alerta, clicando em **exibir guias estratégicos** e, em seguida, selecionando um guia estratégico para ser executado.




## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como começar a investigar os casos de uso do Azure Sentinel. Continue com o tutorial [como responder a ameaças usando as guias estratégicos automatizados](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Responder a ameaças](tutorial-respond-threats-playbook.md) para automatizar suas respostas às ameaças.

