---
title: 'Perguntas frequentes: SRE e codificação | Microsoft Docs'
titleSuffix: Azure
description: 'Perguntas frequentes: compreendendo a relação entre SRE e codificação'
author: dnblankedelman
manager: ScottCa
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 09/14/2020
ms.author: dnb
ms.openlocfilehash: b8865fdd53f4947b17a3621a128fc83f3d93d3e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90089064"
---
# <a name="frequently-asked-questions-do-i-need-to-know-how-to-code-to-get-involved-with-sre"></a>Perguntas frequentes: é necessário saber como codificar para se envolver com o SRE?

Quando as pessoas estão considerando estar envolvidas no SRE e as equipes estão pensando em trazer as práticas SREs, uma pergunta comum que surge é "você precisa saber como codificar?"

A resposta curta: Sim. 

Mas a resposta completa é um pouco mais nuance. Vamos examinar três locais em que a codificação entra na engenharia de confiabilidade do site, juntamente com o nível de experiência de codificação necessário para cada um. Essa lista não está completa, mas esses cenários são alguns dos casos de uso mais comuns.

## <a name="scenario-1-removing-toil-through-automation"></a>Cenário 1: removendo toil por meio de automação

Engenheiros de confiabilidade do site e outros que usam práticas SREs tentam sempre que possível remover o toil. "Toil" significa uma coisa específica em SRE. Toil refere-se ao trabalho de operações que está sendo feito por um humano que tem determinadas características. O trabalho árduo não tem nenhum valor de resgate a longo prazo. Ele não promove o serviço de maneira significativa. Geralmente, ele é repetitivo e principalmente manual (mesmo que possa ser automatizado). Conforme o serviço ou os sistemas aumentarem ao longo do tempo, o número de solicitações para o sistema provavelmente também aumentará em quantidade em uma taxa proporcional e exigirá mais trabalho manual.

Por exemplo, se um serviço exigir que a equipe do SRE redefina algo a cada semana ou para provisionar novas contas e espaço em disco manualmente, ou reiniciá-la repetidamente por mão, essa será a carga operacional toil. A conclusão dessas ações não tornou o serviço melhor de nenhuma forma persistente e de longo prazo. Essas ações provavelmente precisarão ser repetidas várias vezes.

As SREs odeiam trabalho árduo. Elas funcionam para eliminá-lo sempre que possível e de forma adequada. Esse é um dos aspectos na qual a automação entra em cena na SRE. Se essas solicitações puderem ser tratadas automaticamente, isso liberará a equipe para trabalhar com mais recompensa e impacto.

*Experiência em codificação*: a automação requer alguma experiência em codificação, mas não precisa exigir habilidades completas de engenharia de software. Se você puder escrever scripts pequenos (talvez no PowerShell ou no Shell do Bourne) ou mesmo se você criar um [https://docs.microsoft.com/azure/logic-apps/logic-apps-overview](Azure Logic app) com mal qualquer código, esse aplicativo ainda poderá ajudar a eliminar o toil.

## <a name="scenario-2-control-through-apisdomain-specific-languages-dslstemplates"></a>Cenário 2: controle por meio de APIs/linguagens específicas de domínio (DSLs)/templates

Embora não seja estritamente necessário para o SRE Work, ser capaz de controlar ambientes por meio de APIs, DSLs e modelos (especialmente ambientes de nuvem) permite que o SREs Escale verticalmente seu trabalho. A infraestrutura de provisionamento/desprovisionamento, a configuração do monitoramento e a integração de vários serviços, tudo se torna muito mais eficiente por meio de codificação.

*Experiência em codificação*: como o cenário anterior, isso requer alguma experiência em codificação, mas não precisa exigir habilidades completas de engenharia de software. Além dos scripts e dos aplicativos lógicos mencionados antes, o [https://docs.microsoft.com/azure/azure-resource-manager/templates/overview](Azure Resource Manager Templates) também pode ser usado com a experiência de codificação mínima.

## <a name="scenario-3-fixing-the-code"></a>Cenário 3: corrigindo o código

Os engenheiros de confiabilidade do site procuram melhorar a confiabilidade de um sistema. Às vezes, essa meta requer a aprofundamento do código-fonte de um sistema, a determinação do problema e, muitas vezes, contribuindo com uma correção de volta para a base de código. Embora o nível de sofisticação desse trabalho possa variar amplamente com base na situação, a experiência de codificação é um requisito definitivo nesses casos.

*Experiência em codificação*: a experiência de engenharia de software completa geralmente é necessária neste cenário.


## <a name="next-steps"></a>Próximas etapas

Interessado em aprender mais sobre a engenharia de confiabilidade do site e o trabalho de baixo código? Confira nosso [Hub de engenharia de confiabilidade do site](../index.yml), a documentação do produto vinculada acima.
