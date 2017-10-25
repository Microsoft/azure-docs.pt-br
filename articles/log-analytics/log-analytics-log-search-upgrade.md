---
title: Atualizar o Azure Log Analytics para nova pesquisa de logs | Microsoft Docs
description: "A nova linguagem de consulta do Log Analytics está quase chegando e você pode participar da visualização pública.  Este artigo descreve as vantagens da nova linguagem e diz como converter seu espaço de trabalho."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 4a3ee3c4d1fa9b626a51f24997603adceed8311f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-your-azure-log-analytics-workspace-to-new-log-search"></a>Atualizar seu espaço de trabalho do Azure Log Analytics para uma nova pesquisa de logs

> [!NOTE]
> A atualização para a nova linguagem de consulta do Log Analytics é atualmente opcional, dando tempo a você para [familiarizar-se com a nova linguagem](https://go.microsoft.com/fwlink/?linkid=856078).  

A nova linguagem de consulta do Log Analytics chegou e você precisa atualizar seu espaço de trabalho para tirar proveito dela.  Este artigo descreve as vantagens da nova linguagem e diz como converter seu espaço de trabalho.  Se você não optar por atualizar agora, seu espaço de trabalho continuará a funcionar exatamente como sempre, mas ele será convertido automaticamente em uma data posterior.  Você receberá a devida notificação e um tempo significativo quando essa data for definida.

Este artigo fornece detalhes sobre a nova linguagem e o processo de atualização.

## <a name="why-the-new-language"></a>Por que a nova linguagem?
Compreendemos que há problemas em qualquer transição e não estamos apenas alterando linguagem de consulta por diversão.  Há várias razões pelas quais essa alteração fornecerá valor significativo para os clientes do Log Analytics.

- **Simples mas poderosa.** A nova linguagem é mais fácil de entender e é semelhante ao SQL, com construções mais semelhantes a linguagem natural do que a linguagem herdada.
- **Linguagem totalmente por piping.**  A nova linguagem tem recursos de piping mais amplos do que a linguagem herdada.  Virtualmente qualquer saída pode ser canalizada por pipe para outro comando para criar consultas mais complexas do que era possível anteriormente.
- **Extrações de campo de tempo de pesquisa.**  A nova linguagem dá suporte a campos calculados de tempo de execução mais avançados do que a linguagem herdada.  Você pode usar cálculos complexos para os campos estendidos e, em seguida, usar os campos calculados para comandos adicionais, incluindo junções e agregações.
- **Junções avançadas.**  A nova linguagem fornece junções mais avançadas do que a linguagem herdada, incluindo a capacidade de unir tabelas em vários campos, usar junções internas e externas fazer junções em campos estendidos.
- **Funções de data/hora.**  A nova linguagem tem funções de data/hora mais avançadas do que a linguagem herdada.
- **Análise inteligente.**  A nova linguagem tem algoritmos avançados para avaliar padrões em conjuntos de dados e comparar diferentes conjuntos de dados.
- **Portal Análise Avançada.**  O portal Análise Avançada oferece recursos de análise não disponíveis no portal do Log Analytics, incluindo edição de consultas multilinha, visualizações adicionais e diagnósticos avançados.
- **Consistência com outros aplicativos.**  A nova linguagem e o Portal Análise Avançada já são usados para análise no Application Insights.  Implementá-la para o Log Analytics proporciona consistência entre os serviços do Azure.
- **Melhor integração com o Power BI.** Consultas na nova linguagem podem ser exportadas para o Power BI Desktop, para que você possa utilizar seus recursos de transformação de dados avançados.
- **E muito mais.** Consulte o site [Linguagem de Consulta do Azure Log Analytics](https://docs.loganalytics.io) para obter detalhes completos e tutoriais sobre a nova linguagem.


## <a name="when-can-i-upgrade"></a>Quando posso fazer a atualização?
A atualização será lançada em todas as regiões do Azure, então ela pode estar disponível em algumas regiões antes de outras.  Você saberá quando seu espaço de trabalho está disponível para ser atualizado quando você vir a faixa roxa na parte superior do espaço de trabalho convidando para fazer a atualização.

![Atualização 1](media/log-analytics-log-search-upgrade/upgrade-01a.png)

## <a name="what-happens-when-i-upgrade"></a>O que acontece quando eu faço a atualização?
Quando você converte seu espaço de trabalho, todas as pesquisas salvas, regras de alerta e exibições que você criou com o Designer de Exibição são convertidos automaticamente para a nova linguagem.  Pesquisas incluídas em soluções não são convertidas automaticamente, mas em vez disso são convertidas em tempo real quando abertas por você.  Isso é completamente transparente para você.

## <a name="can-i-go-back-after-i-upgrade"></a>Posso voltar atrás depois de atualizar?
Quando você atualiza, é feito um backup completo de seu espaço de trabalho que inclui um instantâneo de todas as pesquisas, regras de alerta e exibições salvas.  Isso permite que você restaure seu espaço de trabalho antigo se posteriormente desejar.

Para restaurar seu espaço de trabalho herdado, vá para **Configurações** no espaço de trabalho e, em seguida, **Resumo da Atualização**.  Você pode então selecionar a opção **Restaurar o espaço de trabalho herdado**.  

![Restaurar herdado](media/log-analytics-log-search-upgrade/restore-legacy-b.png)

## <a name="how-do-i-perform-the-upgrade"></a>Como fazer para realizar a atualização?
Quando você vir a roxa faixa na parte superior do portal, você poderá atualizar seu espaço de trabalho.  

1.  Inicie o processo de atualização clicando na faixa roxa que diz **Saber mais e atualizar**.<br>![Atualização 2](media/log-analytics-log-search-upgrade/upgrade-01a.png)<br>
2.  Leia as informações adicionais sobre a atualização na página de informações de atualização.<br>![Atualização 2](media/log-analytics-log-search-upgrade/upgrade-03.png)<br>
3.  Clique em **Atualizar Agora** para iniciar a atualização.<br>![Atualização 4](media/log-analytics-log-search-upgrade/upgrade-04.png)<br>Uma caixa de notificação no canto direito superior mostra o status.<br>![Atualização 5](media/log-analytics-log-search-upgrade/upgrade-05.png)
4.  É isso!  Examine a página Pesquisa de Logs para dar uma olhada em seu espaço de trabalho atualizado.<br>![Atualização 6](media/log-analytics-log-search-upgrade/upgrade-06.png)<br>

Se você encontrar um problema que cause falha na atualização, você poderá ir para o [fórum de discussão](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) e postar sua pergunta ou [criar uma solicitação de suporte](../azure-supportability/how-to-create-azure-support-request.md) do Portal do Azure.

## <a name="how-do-i-learn-the-new-language"></a>Como fazer para aprender a nova linguagem?
Já que ela é usada por vários serviços, criamos um [site externo para hospedar a documentação](https://docs.loganalytics.io/) da nova linguagem.  Isso inclui uma referência completa para ajudá-lo a aprender, além de exemplos e tutoriais. Você pode percorrer um tutorial da nova linguagem em [Introdução a Consultas](https://go.microsoft.com/fwlink/?linkid=856078) e acessar a referência da linguagem em [Linguagem de consulta do Log Analytics](https://go.microsoft.com/fwlink/?linkid=856079).  

No entanto, se você já está familiarizado com a linguagem de consulta do Log Analytics herdada, você pode usar o conversor de idioma que é adicionado ao seu espaço de trabalho como parte da atualização.

Basta digitar sua consulta herdada e, em seguida, clique em **Converter** para ver a versão traduzida.  Você pode, em seguida, clicar no botão Pesquisar para executar a pesquisa ou copiar e colar a consulta convertida para usar em outro lugar, por exemplo, uma regra de alerta.

![Conversor de linguagem](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="next-steps"></a>Próximas etapas
- Confira um [tutorial sobre a nova linguagem](https://go.microsoft.com/fwlink/?linkid=856078).
- Percorra um [tutorial sobre como usar o portal de Pesquisa de Logs](log-analytics-log-search-log-search-portal.md) com a nova linguagem de consulta.
- Obtenha uma introdução ao novo [portal Análise Avançada](https://go.microsoft.com/fwlink/?linkid=856587).
