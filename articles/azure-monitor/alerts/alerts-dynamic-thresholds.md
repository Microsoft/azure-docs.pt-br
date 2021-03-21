---
title: Criação de alertas com limites dinâmicos no Azure Monitor
description: Criar alertas com limites dinâmicos baseados em aprendizado de máquina
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 01/04/2021
ms.openlocfilehash: 9cf4caf77d14dd1296276ca48827c170ef265e9b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101718056"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor"></a>Alertas de métrica com limites dinâmicos no Azure Monitor

O Alerta de Métrica com detecção de Limites Dinâmicos aproveita ML (aprendizado de máquina) avançado para aprender o comportamento histórico das métricas e identificar padrões e anomalias que indicam problemas de serviço possíveis. Ele fornece suporte tanto de uma interface do usuário simples quanto de operações em escala permitindo aos usuários configurar regras de alerta por meio da API do Azure Resource Manager de maneira totalmente automatizada.

Depois que uma regra de alerta tiver sido acionada, ela será acionada somente quando a métrica monitorada não se comportar conforme o esperado com base em seus limites personalizados.

Adoraríamos receber seus comentários; envie-os para <azurealertsfeedback@microsoft.com>.

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>Por que e quando é recomendado usar o tipo de condição dinâmica?

1. **Alertas escalonáveis** – as regras de alerta de limite dinâmico podem criar limites personalizados para centenas de séries métricas de cada vez, ainda fornecendo a mesma facilidade de definir uma regra de alerta em uma única métrica. Eles oferecem a você menos alertas para criar e gerenciar. Você pode usar portal do Azure ou a API Azure Resource Manager para criá-los. A abordagem escalonável é especialmente útil ao lidar com dimensões métricas ou ao se aplicar a vários recursos, como a todos os recursos de assinatura.  [Saiba mais sobre como configurar Alertas de Métrica com Limites Dinâmicos usando modelos](./alerts-metric-create-templates.md).

1. **Reconhecimento de padrão de métrica inteligente** – usando nossa tecnologia ml, podemos detectar automaticamente padrões de métricas e adaptar-se às alterações de métrica ao longo do tempo, o que pode incluir sazonalidade (por hora/dia/semana). Adaptar-se ao comportamento de métricas ao longo do tempo e o alerta com base em desvios de seu padrão alivia a carga de saber o limite "certo" para cada métrica. O algoritmo de ML usado em limites dinâmicos foi projetado para evitar ruído (baixa precisão) ou limites amplos (baixo recall) que não têm um padrão esperado.

1. **Configuração intuitiva** – limites dinâmicos permite configurar alertas de métricas usando conceitos de alto nível, aliviando a necessidade de ter amplo conhecimento de domínio sobre a métrica.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>Como configurar alertas com Limites Dinâmicos?

Alertas com Limites Dinâmicos podem ser configurados por meio de Alertas de Métrica no Azure Monitor. [Saiba mais sobre como configurar Alertas de Métrica](alerts-metric.md).

## <a name="how-are-the-thresholds-calculated"></a>Como os limites são calculados?

Os Limites Dinâmicos aprendem continuamente os dados da série de métrica e tentam modelá-los usando um conjunto de algoritmos e métodos. Ele detecta padrões nos dados, como periodicidade (por horária/diária/semanal) e é capaz de lidar com métricas ruidosas (como memória ou CPU do computador), bem como as métricas com baixa dispersão (como taxa de erro e disponibilidade).

Os limites são selecionados de modo que um desvio deles indique uma anomalia no comportamento da métrica.

> [!NOTE]
> Os limites dinâmicos podem detectar sazonalidade por horas, diariamente ou por padrões semanais. Outros padrões, como sazonalidade de bi ou quinzenal, podem não ser detectados. Para detectar sazonalidade semanais, pelo menos três semanas de dados históricos são necessárias. 

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>O que a configuração de 'Sensibilidade' em Limites Dinâmicos significa?

A sensibilidade do limite de alerta é um conceito de alto nível que controla a quantidade de desvio do comportamento da métrica necessária para disparar um alerta.
Essa opção não exige o conhecimento de domínio sobre a métrica, como limite estático. As opções disponíveis são:

- Alto – os limites serão rígidos e próximos do padrão da série da métrica. Uma regra de alerta será disparada no menor desvio, resultando em mais alertas.
- Médio – limites menos rígidos e mais equilibrados, menos alertas que com alta sensibilidade (padrão).
- Baixo – os limites serão flexíveis, mais afastados do padrão da série de métrica. Uma regra de alerta só será disparada em grandes desvios, resultando em menos alertas.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>Quais são as opções de configuração de 'Operator' em Limites Dinâmicos?

A regra de alerta de Limites Dinâmicos pode criar limites personalizados com base no comportamento da métrica para os limites superior e inferior usando a mesma regra de alerta.
Você pode escolher o alerta a ser disparado em uma das três condições a seguir:

- Maior que o limite superior ou menor que o limite inferior (padrão)
- Maior que o limite superior
- Menor do que o limite inferior.

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>O que as configurações avançadas em Limites Dinâmicos significam?

**Períodos com falha** -os limites dinâmicos também permitem que você configure "violações de número para disparar o alerta", um número mínimo de desvios necessários em uma determinada janela de tempo para o sistema gerar um alerta (a janela de tempo padrão é de quatro desvios em 20 minutos). O usuário pode configurar períodos de falha e escolher sobre o que ser alertado alterando os períodos de falha e a janela de tempo. Esse recurso reduz o ruído de alerta gerado por picos transitórios. Por exemplo:

Para disparar um alerta quando o problema for contínuo por 20 minutos, 4 vezes consecutivas em um determinado período de agrupamento de 5 minutos, use as seguintes configurações:

![Configurações de períodos de falha para problema contínuo de 20 minutos, 4 vezes consecutivas em um determinado agrupamento de período de 5 minutos](media/alerts-dynamic-thresholds/0008.png)

Para disparar um alerta quando houver uma violação de um Limite Dinâmico em 20 minutos dos últimos 30 minutos com período de 5 minutos, use as seguintes configurações:

![Configurações de períodos de falha para problema para 20 minutos dos últimos 30 minutos com o agrupamento de período de 5 minutos](media/alerts-dynamic-thresholds/0009.png)

**Ignorar dados antes de** – os usuários também podem definir uma data de início a partida da qual o sistema deve começar a calcular os limites. Um caso de uso típico pode ocorrer quando um recurso estava em execução em um modo de teste e agora será promovido para atender a uma carga de trabalho de produção, portanto, o comportamento de qualquer métrica durante a fase de teste deve ser desconsiderado.

## <a name="how-do-you-find-out-why-a-dynamic-thresholds-alert-was-triggered"></a>Como descobrir por que um alerta de limites dinâmicos foi disparado?

Você pode explorar as instâncias de alerta disparadas no modo de exibição de alertas clicando no link no email ou na mensagem de texto ou no navegador para ver a exibição de alertas no portal do Azure. [Saiba mais sobre a exibição de alertas](./alerts-overview.md#alerts-experience).

A exibição de alerta exibe:

- Todos os detalhes de métrica no momento em que o alerta de limites dinâmicos foi acionado.
- Um gráfico do período em que o alerta foi disparado, incluindo os limites dinâmicos usados nesse ponto no tempo.
- Capacidade de fornecer comentários sobre o alerta de limites dinâmicos e a experiência de exibição de alertas, que pode melhorar as detecções futuras.

## <a name="will-slow-behavior-changes-in-the-metric-trigger-an-alert"></a>As alterações de comportamento serão lentas no gatilho de métrica de um alerta?

Provavelmente, não. Limites Dinâmicos são bons para detectar desvios significativos, não problemas que se desenvolvem lentamente.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>Qual é a quantidade de dados usada para visualizar e então calcular os limites?

Quando uma regra de alerta é criada pela primeira vez, os limites que aparecem no gráfico são calculados com base em dados históricos suficientes para calcular a hora ou os padrões sazonais diários (10 dias). Depois que uma regra de alerta é criada, os limites dinâmicos usam todos os dados históricos necessários que estão disponíveis e irão aprender e se adaptar continuamente com base nos novos dados para tornar os limites mais precisos. Isso significa que, após esse cálculo, o gráfico também exibirá padrões semanais.

## <a name="how-much-data-is-needed-to-trigger-an-alert"></a>Qual é a quantidade de dados necessária para disparar um alerta?

Se você tiver um novo recurso ou dados de métrica ausentes, os limites dinâmicos não dispararão alertas antes de três dias e pelo menos 30 exemplos de dados de métrica estarão disponíveis para garantir limites precisos.
Para recursos existentes com dados de métrica suficientes, os limites dinâmicos podem disparar alertas imediatamente.

## <a name="dynamic-thresholds-best-practices"></a>Melhores práticas para Limites Dinâmicos

Os limites dinâmicos podem ser aplicados à maioria das métricas de plataforma e personalizadas no Azure Monitor e também foram ajustados para as métricas comuns de aplicativo e infraestrutura.
Os itens a seguir são melhores práticas sobre como configurar alertas em algumas dessas métricas usando Limites Dinâmicos.

### <a name="dynamic-thresholds-on-virtual-machine-cpu-percentage-metrics"></a>Limites dinâmicos em métricas de percentual de CPU da máquina virtual

1. Em [portal do Azure](https://portal.azure.com), clique em **Monitor**. A exibição do Monitor consolida todas as configurações e dados de monitoramento em uma exibição.

2. Clique em **Alertas** e, em seguida, clique em **+ Nova regra de alerta**.

    > [!TIP]
    > A maioria das folhas de recursos também tem **Alertas** no menu de recursos, em **Monitoramento**, e você pode criar alertas de lá também.

3. Clique em **Selecionar destino** no painel de contexto exibido e selecione um recurso de destino para o qual você quer criar um alerta. Use os menus suspensos **Assinatura** e **Tipo de recurso 'Máquinas Virtuais** para localizar o recurso que você quer monitorar. Também é possível usar a barra de pesquisa para localizar o recurso.

4. Após selecionar um recurso de destino, clique em **Adicionar condição**.

5. Selecione a **'Percentual de CPU'**.

6. Também é possível refinar a métrica ajustando o **Período** e a **Agregação**. Não é recomendável usar o tipo de agregação 'Máximo' para esse tipo de métrica, já que é menos representativo do comportamento. Para limite de agregação 'Máximo' do tipo estático, talvez seja mais apropriado.

7. Você verá um gráfico da métrica referente às últimas 6 horas. Defina os parâmetros de alerta:
    1. **Tipo de condição** - Escolha a opção 'Dinâmico'.
    1. **Confidencialidade** - Escolha a confidencialidade Média/Baixa para reduzir ruído de alerta.
    1. **Operador** - Escolha 'Maior que', a menos que o comportamento represente o uso do aplicativo.
    1. **Frequência** - Considere reduzir com base no impacto nos negócios do alerta.
    1. **Períodos com falhas** (Opção avançada) - A janela de retrocesso deve ter pelo menos 15 minutos. Por exemplo, se o período for definido como cinco minutos, os períodos com falha deverão ser pelo menos três ou mais.

8. O gráfico de métricas exibirá os limites calculados com base nos dados recentes.

9. Clique em **Concluído**.

10. Preencha **Detalhes do alerta** como **Nome da regra de alerta**, **Descrição** e **Severidade**.

11. Adicione um grupo de ações ao alerta selecionando um grupo de ações existente ou criar um novo.

12. Clique em **Concluído** para salvar a regra de alerta de métrica.

> [!NOTE]
> Regras de alerta de métrica criadas por meio do portal são criadas no mesmo grupo de recursos que o recurso de destino.

### <a name="dynamic-thresholds-on-application-insights-http-request-execution-time"></a>Limites Dinâmicos no tempo de execução da solicitação HTTP do Application Insights

1. Em [portal do Azure](https://portal.azure.com), clique em **Monitor**. A exibição do Monitor consolida todas as configurações e dados de monitoramento em uma exibição.

2. Clique em **Alertas** e, em seguida, clique em **+ Nova regra de alerta**.

    > [!TIP]
    > A maioria das folhas de recursos também tem **Alertas** no menu de recursos, em **Monitoramento**, e você pode criar alertas de lá também.

3. Clique em **Selecionar destino** no painel de contexto exibido e selecione um recurso de destino para o qual você quer criar um alerta. Use os menus suspensos **Assinatura** e **Tipo de recurso 'Application Insights'** para localizar o recurso que você quer monitorar. Também é possível usar a barra de pesquisa para localizar o recurso.

4. Após selecionar um recurso de destino, clique em **Adicionar condição**.

5. Selecione o **'Tempo de execução da solicitação de HTTP'**.

6. Também é possível refinar a métrica ajustando o **Período** e a **Agregação**. Não é recomendável usar o tipo de agregação 'Máximo' para esse tipo de métrica, já que é menos representativo do comportamento. Para limite de agregação 'Máximo' do tipo estático, talvez seja mais apropriado.

7. Você verá um gráfico da métrica referente às últimas 6 horas. Defina os parâmetros de alerta:
    1. **Tipo de condição** - Escolha a opção 'Dinâmico'.
    1. **Operador** - Escolha 'Maior que' para reduzir os alertas disparados na melhoria da duração.
    1. **Frequência** - Considere reduzir com base no impacto nos negócios do alerta.

8. O gráfico de métricas exibirá os limites calculados com base nos dados recentes.

9. Clique em **Concluído**.

10. Preencha **Detalhes do alerta** como **Nome da regra de alerta**, **Descrição** e **Severidade**.

11. Adicione um grupo de ações ao alerta selecionando um grupo de ações existente ou criar um novo.

12. Clique em **Concluído** para salvar a regra de alerta de métrica.

> [!NOTE]
> Regras de alerta de métrica criadas por meio do portal são criadas no mesmo grupo de recursos que o recurso de destino.

## <a name="interpreting-dynamic-threshold-charts"></a>Interpretando gráficos de limites dinâmicos

A seguir está um gráfico mostrando uma métrica, seus limites de limite dinâmico e alguns alertas acionados quando o valor estava fora dos limites permitidos.

![Saiba mais sobre como configurar alertas de métricas](media/alerts-dynamic-thresholds/threshold-picture-8bit.png)

Use as informações a seguir para interpretar o gráfico anterior.

- **Linha azul** – a métrica medida real ao longo do tempo.
- **Área sombreada azul** – mostra o intervalo permitido para a métrica. Desde que os valores de métrica permaneçam dentro desse intervalo, nenhum alerta ocorrerá.
- **Pontos azuis** -se você tiver clicado em parte do gráfico e passar o mouse sobre a linha azul, verá um ponto azul aparecendo no cursor mostrando um valor de métrica agregada individual.
- **Pop-up com ponto azul** – mostra o valor medido da métrica (o ponto azul) e os valores superior e inferior do intervalo permitido.  
- **Ponto vermelho com um círculo preto** – mostra o primeiro valor de métrica fora do intervalo permitido. Esse é o valor que dispara um alerta de métrica e o coloca em um estado ativo.
- **Pontos vermelhos**-indicam valores mediis adicionais fora do intervalo permitido. Eles não irão disparar alertas de métrica adicionais, mas o alerta permanece no modo ativo.
- **Área vermelha** – mostra a hora em que o valor da métrica estava fora do intervalo permitido. O alerta permanece no estado ativo, desde que os valores medidos subsequentes estejam fora do intervalo permitido, mas nenhum alerta novo será acionado.
- **Fim da área vermelha** – quando a linha azul está de volta dentro dos valores permitidos, a área vermelha é interrompida e a linha de valor medido fica azul. O status do alerta de métrica acionado no momento do ponto vermelho com contorno preto é definido como resolvido.