---
title: Dimensionamento automático e Ambiente do Serviço de Aplicativo v1 – Azure
description: Dimensionamento automático e Ambiente de Serviço de Aplicativo
services: app-service
documentationcenter: ''
author: btardif
manager: erikre
editor: ''
ms.assetid: c23af2d8-d370-4b1f-9b3e-8782321ddccb
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 29a639142395c43fea06c1d6d18909b3c9f33b86
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769253"
---
# <a name="autoscaling-and-app-service-environment-v1"></a>Dimensionamento automático e Ambiente de Serviço de Aplicativo v1

> [!NOTE]
> Este artigo é sobre o Ambiente do Serviço de Aplicativo v1.  Há uma versão mais recente do Ambiente do Serviço de Aplicativo que é mais fácil de usar e é executada em infraestrutura mais avançada. Para saber mais sobre o novo início de versão com o [Introdução ao Ambiente do Serviço de Aplicativo](intro.md).
> 

Os ambientes de Serviço de Aplicativo do Azure dão suporte ao *dimensionamento automático*. Você pode dimensionar automaticamente pools de trabalho individuais com base em métricas ou na agenda.

![Opções de dimensionamento automático para um pool de trabalho.][intro]

O dimensionamento automático otimiza a utilização de recursos, escalar e reduzir verticalmente um ambiente de Serviço de Aplicativo de forma automática para ajustar seu orçamento e/ou perfil de carregamento.

## <a name="configure-worker-pool-autoscale"></a>Configurar o dimensionamento automático de pool de trabalho
Você pode acessar a funcionalidade de dimensionamento automático na guia **Configurações** do pool de trabalho.

![Guia de configurações do pool de trabalho.][settings-scale]

A partir daí, a interface deve ser bastante familiar, já que é a mesma experiência que você vê quando dimensiona um Plano do Serviço de Aplicativo. 

![Configurações manuais de escala.][scale-manual]

Você também pode configurar um perfil de dimensionamento automático.

![Configurações de dimensionamento automático.][scale-profile]

Os perfis de dimensionamento automático são úteis para definir limites na sua escala. Dessa forma, você pode ter uma experiência de desempenho consistente, definindo um valor de escala de limite inferior (1) e um limite de gastos previsível, definindo um limite superior (2).

![Configurações de escala no perfil.][scale-profile2]

Depois de definir um perfil, você pode adicionar regras de dimensionamento automático para aumentar ou reduzir o número de instâncias no pool de trabalho dentro dos limites definidos pelo perfil. As regras de dimensionamento automático se baseiam em métricas.

![Regra de escala.][scale-rule]

 Qualquer métrica de pool de trabalho ou de front-end pode ser usada para definir regras de dimensionamento automático. Essas métricas são as mesmas que você pode monitorar nos grafos de folha de recursos e para as quais pode definir alertas.

## <a name="autoscale-example"></a>Exemplo de dimensionamento automático
O dimensionamento automático de um ambiente de Serviço de Aplicativo pode ser melhor ilustrado examinando um cenário.

Este artigo explica todas as considerações necessárias ao configurar o dimensionamento automático. Este artigo aborda as interações que entram em jogo quando você fatora em ambientes de Serviço de Aplicativo de dimensionamento automático hospedados no Ambiente de Serviço de Aplicativo.

### <a name="scenario-introduction"></a>Introdução ao cenário
Matheus é um SysAdmin de uma empresa que migrou uma parte das cargas de trabalho que ele gerencia para um ambiente de Serviço de Aplicativo.

O ambiente do Serviço de Aplicativo está configurado para a escala manual da seguinte maneira:

* **Front-ends:** 3
* **Pool de trabalho 1**: 10
* **Pool de trabalho 2**: 5
* **Pool de trabalho 3**: 5

O pool de trabalho 1 é usado para cargas de trabalho de produção, embora o pool de trabalho 2 e o pool de trabalho 3 sejam usados para garantia de qualidade (QA) e cargas de trabalho de desenvolvimento.

Os Planos do Serviço de Aplicativo para controle de qualidade e desenvolvimento são configurados para escala manual. O Plano do Serviço de Aplicativo de produção é definido para dimensionamento automático para lidar com variações de carga e tráfego.

Matheus está familiarizado com o aplicativo. Ele sabe que as horas de pico de carga ocorrem entre 9:00 e 18:00, porque este é um aplicativo de linha de negócios (LOB) que os funcionários usam enquanto estão no escritório. O uso é reduzido depois disso, quando os usuários param de trabalhar naquele dia. Fora do horário de pico, ainda há alguma carga porque os usuários podem acessar o aplicativo remotamente, usando seus dispositivos móveis ou computadores domésticos. O plano do Serviço de Aplicativo de produção já está configurado para dimensionamento automático com base no uso de CPU com as seguintes regras:

![Configurações específicas para o aplicativo LOB.][asp-scale]

| **Perfil de dimensionamento automático – Dias da semana – plano do Serviço de Aplicativo** | **Perfil de dimensionamento automático – Finais de semana – plano do Serviço de Aplicativo** |
| --- | --- |
| **Nome:** Perfil de dia da semana |**Nome:** Perfil de final de semana |
| **Escalar por:** Regras de desempenho e agendamento |**Escalar por:** Regras de desempenho e agendamento |
| **Perfil:** Dias da semana |**Perfil:** Fim de Semana |
| **Tipo:** Recorrência |**Tipo:** Recorrência |
| **Intervalo de destino:** 5 a 20 instâncias |**Intervalo de destino:** 3 a 10 instâncias |
| **Dias:** segunda-feira, terça-feira, quarta-feira, quinta-feira, sexta-feira |**Dias:** sábado, domingo |
| **Hora de início:** 9h00 |**Hora de início:** 9h00 |
| **Fuso horário:** UTC-08 |**Fuso horário:** UTC-08 |
|  | |
| **Regra de dimensionamento automático (Escalar Verticalmente)** |**Regra de dimensionamento automático (Escalar Verticalmente)** |
| **Recurso:** Produção (Ambiente do Serviço de Aplicativo) |**Recurso:** Produção (Ambiente do Serviço de Aplicativo) |
| **Métrica:** CPU (%) |**Métrica:** CPU (%) |
| **Operação:** Maior que 60% |**Operação:** Maior que 80% |
| **Duração:** 5 Minutos |**Duração:** 10 minutos |
| **Agregação de tempo:** Média |**Agregação de tempo:** Média |
| **Ação:** Aumentar a contagem em 2 |**Ação:** Aumentar a contagem em 1 |
| **Tempo de resfriamento (minutos):** 15 |**Tempo de resfriamento (minutos):** 20 |
|  | |
| **Regra de dimensionamento automático (Reduzir Verticalmente)** |**Regra de dimensionamento automático (Reduzir Verticalmente)** |
| **Recurso:** Produção (Ambiente do Serviço de Aplicativo) |**Recurso:** Produção (Ambiente do Serviço de Aplicativo) |
| **Métrica:** CPU (%) |**Métrica:** CPU (%) |
| **Operação:** Menor que 30% |**Operação:** Menor que 20% |
| **Duração:** 10 minutos |**Duração:** 15 minutos |
| **Agregação de tempo:** Média |**Agregação de tempo:** Média |
| **Ação:** Diminuir contagem em 1 |**Ação:** Diminuir contagem em 1 |
| **Tempo de resfriamento (minutos):** 20 |**Tempo de resfriamento (minutos):** 10 |

### <a name="app-service-plan-inflation-rate"></a>Taxa de inflação do plano do Serviço de Aplicativo
Os planos do Serviço de Aplicativo configurados para dimensionamento automático o fazem a uma taxa máxima por hora. Essa taxa pode ser calculada com base nos valores fornecidos na regra de dimensionamento automático.

Compreender e calcular a *taxa de inflação do plano do Serviço de Aplicativo* é importante para o dimensionamento automático do ambiente de Serviço de Aplicativo, já que as alterações de escala em um pool de trabalho não são instantâneas.

A taxa de inflação do plano do Serviço de Aplicativo é calculada da seguinte maneira:

![Cálculo da taxa de inflação do plano do Serviço de Aplicativo.][ASP-Inflation]

Com base na regra Dimensionamento automático – Escalar verticalmente para o perfil de Dia da semana do Plano do Serviço de Aplicativo de produção:

![Taxa de inflação de plano do Serviço de Aplicativo para dias da semana com base em Dimensionamento automático – regra Escalar Verticalmente.][Equation1]

No caso da regra Dimensionamento automático - Escalar verticalmente do perfil Finais de semana do plano do Serviço de Aplicativo de produção, a fórmula resolveria para:

![Taxa de inflação de plano do Serviço de Aplicativo para finais de semana com base em Dimensionamento automático – regra Escalar Verticalmente.][Equation2]

Esse valor também pode ser calculado para operações de redução.

Com base na regra Dimensionamento automático - Reduzir verticalmente do perfil dia da semana perfil do plano do Serviço de Aplicativo de produção, seria semelhante ao seguinte:

![Taxa de inflação de plano do Serviço de Aplicativo para dias da semana com base em Dimensionamento automático – regra Reduzir Verticalmente.][Equation3]

No caso da regra Dimensionamento automático - Reduzir verticalmente do perfil Finais de semana do plano do Serviço de Aplicativo de produção, a fórmula resolveria para:  

![Taxa de inflação de plano do Serviço de Aplicativo para fins de semana com base em Dimensionamento automático – regra Reduzir Verticalmente.][Equation4]

O Plano do Serviço de Aplicativo de produção pode crescer em uma taxa máxima de oito instâncias/hora durante a semana e quatro instâncias/hora durante o fim de semana. Ele pode liberar instâncias em uma taxa máxima de quatro instâncias/hora durante a semana e seis instâncias/hora durante os finais de semana.

Se vários planos do Serviço de Aplicativo estiverem sendo hospedados em um pool de trabalho, você precisará calcular a *taxa total de inflação* como a soma da taxa de inflação de todos os planos do Serviço de Aplicativo hospedados nesse pool de trabalho.

![Cálculo da taxa total de inflação para vários planos do Serviço de Aplicativo hospedados em um pool de trabalho.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Usar a taxa de inflação do plano do Serviço de Aplicativo para definir regras de dimensionamento automático do pool de trabalho
Os pools de trabalho que hospedam Planos do Serviço de Aplicativo configurados para o dimensionamento automático precisam ter um buffer de capacidade alocado. O buffer permite que as operações de dimensionamento automático aumentem ou reduzam o plano do Serviço de Aplicativo, conforme necessário. O buffer mínimo seria a Taxa de Inflação Total do Plano do Serviço de Aplicativo calculada.

Como as operações de dimensionamento do ambiente de Serviço de Aplicativo demoram algum tempo para serem aplicadas, qualquer alteração deve levar em consideração outras alterações sob demanda que poderiam acontecer enquanto uma operação de escala está em andamento. Para acomodar essa latência, recomendamos que você use a Taxa de Inflação do Plano do Serviço de Aplicativo Total como o número mínimo de instâncias adicionadas para cada operação de dimensionamento automático.

Com essas informações, Matheus pode definir o seguinte perfil e regras de dimensionamento automático:

![Regras de perfil de dimensionamento automático para o exemplo LOB.][Worker-Pool-Scale]

| **Perfil de dimensionamento automático – Dias da semana** | **Perfil de dimensionamento automático – Finais de semana** |
| --- | --- |
| **Nome:** Perfil de dia da semana |**Nome:** Perfil de final de semana |
| **Escalar por:** Regras de desempenho e agendamento |**Escalar por:** Regras de desempenho e agendamento |
| **Perfil:** Dias da semana |**Perfil:** Fim de Semana |
| **Tipo:** Recorrência |**Tipo:** Recorrência |
| **Intervalo de destino:** 13 a 25 instâncias |**Intervalo de destino:** 6 a 15 instâncias |
| **Dias:** segunda-feira, terça-feira, quarta-feira, quinta-feira, sexta-feira |**Dias:** sábado, domingo |
| **Hora de início:** 7h00 |**Hora de início:** 9h00 |
| **Fuso horário:** UTC-08 |**Fuso horário:** UTC-08 |
|  | |
| **Regra de dimensionamento automático (Escalar Verticalmente)** |**Regra de dimensionamento automático (Escalar Verticalmente)** |
| **Recurso:** Pool de trabalho 1 |**Recurso:** Pool de trabalho 1 |
| **Métrica:** WorkersAvailable |**Métrica:** WorkersAvailable |
| **Operação:** Menor que 8 |**Operação:** Menor que 3 |
| **Duração:** 20 minutos |**Duração:** 30 minutos |
| **Agregação de tempo:** Média |**Agregação de tempo:** Média |
| **Ação:** Aumentar a contagem em 8 |**Ação:** Aumentar a contagem em 3 |
| **Tempo de resfriamento (minutos):** 180 |**Tempo de resfriamento (minutos):** 180 |
|  | |
| **Regra de dimensionamento automático (Reduzir Verticalmente)** |**Regra de dimensionamento automático (Reduzir Verticalmente)** |
| **Recurso:** Pool de trabalho 1 |**Recurso:** Pool de trabalho 1 |
| **Métrica:** WorkersAvailable |**Métrica:** WorkersAvailable |
| **Operação:** Maior que 8 |**Operação:** Maior que 3 |
| **Duração:** 20 minutos |**Duração:** 15 minutos |
| **Agregação de tempo:** Média |**Agregação de tempo:** Média |
| **Ação:** Diminuir contagem em 2 |**Ação:** Diminuir contagem em 3 |
| **Tempo de resfriamento (minutos):** 120 |**Tempo de resfriamento (minutos):** 120 |

O intervalo de Destino definido no perfil é calculado pelas instâncias do mínimas definidas no perfil para o plano do Serviço de Aplicativo + buffer.

O intervalo Máximo seria a soma de todos os intervalos máximos de todos os planos do Serviço de Aplicativo hospedados no pool de trabalho.

A contagem de Aumento das regras de escala vertical deve ser definida como, pelo menos, 1X a Taxa de Inflação do Plano do Serviço de Aplicativo para a escala vertical.

A contagem de redução pode ser ajustada para algo entre 1/2x ou 1x a taxa de inflação do plano do Serviço de Aplicativo para reduzir verticalmente.

### <a name="autoscale-for-front-end-pool"></a>Dimensionamento automático para o pool de front-end
As regras de dimensionamento automático de front-end são mais simples para pools de trabalho. Basicamente, você deve  
verifique se a duração da medição e se os temporizadores de resfriamento consideram que as operações de escala em um Plano do Serviço de Aplicativo não são instantâneas.

Para este cenário, Frank sabe que a taxa de erro aumenta depois que os front-ends alcançam 80% de utilização de CPU e define a regra de dimensionamento automático para aumentar o número de instâncias da seguinte maneira:

![Configurações de dimensionamento automático para o pool de front-end.][Front-End-Scale]

| **Perfil de dimensionamento automático – Front-ends** |
| --- |
| **Nome:** Dimensionamento automático – Front-ends |
| **Escalar por:** Regras de desempenho e agendamento |
| **Perfil:** Todos os dias |
| **Tipo:** Recorrência |
| **Intervalo de destino:** 3 a 10 instâncias |
| **Dias:** Todos os dias |
| **Hora de início:** 9h00 |
| **Fuso horário:** UTC-08 |
|  |
| **Regra de dimensionamento automático (Escalar Verticalmente)** |
| **Recurso:** Pool de front-end |
| **Métrica:** CPU (%) |
| **Operação:** Maior que 60% |
| **Duração:** 20 minutos |
| **Agregação de tempo:** Média |
| **Ação:** Aumentar a contagem em 3 |
| **Tempo de resfriamento (minutos):** 120 |
|  |
| **Regra de dimensionamento automático (Reduzir Verticalmente)** |
| **Recurso:** Pool de trabalho 1 |
| **Métrica:** CPU (%) |
| **Operação:** Menor que 30% |
| **Duração:** 20 minutos |
| **Agregação de tempo:** Média |
| **Ação:** Diminuir contagem em 3 |
| **Tempo de resfriamento (minutos):** 120 |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png
