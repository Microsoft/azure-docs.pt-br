---
title: "Manual do Modelo de Solução do Cortana Intelligence para a previsão de demanda de energia | Microsoft Docs"
description: "Um Modelo de Solução com o Microsoft Cortana Intelligence que ajuda a prever a demanda para uma concessionária de energia elétrica."
services: cortana-analytics
documentationcenter: 
author: ilanr9
manager: ilanr9
editor: yijichen
ms.assetid: 8855dbb9-8543-45b9-b4c6-aa743a04d547
ms.service: cortana-analytics
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2016
ms.author: ilanr9;yijichen;garye
ms.openlocfilehash: 275e387878900154660d044b26ff5ac03a17a65a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="cortana-intelligence-solution-template-playbook-for-demand-forecasting-of-energy"></a>Manual do Modelo de Solução do Cortana Intelligence para a previsão de demanda de energia
## <a name="executive-summary"></a>Resumo executivo
Nos últimos anos, a Internet das Coisas (IoT), fontes de energia alternativas e Big Data foram mesclados para criar grandes oportunidades no domínio de serviços e de energia. Ao mesmo tempo, o setor de energia e o setor de serviços têm visto a estagnação do consumo, com os clientes exigindo maneiras melhores de controlar o uso da energia. Portanto, as empresas de rede inteligente e de serviços têm grande necessidade de inovar e de renovar a si mesmos. Além disso, muitas redes de energia e de serviços estão ficando desatualizadas e muito caras de manter e de gerenciar. No ano passado, a equipe trabalhou em vários compromissos no domínio da energia. Durante esses compromissos, encontramos muitos casos em que os ISVs (fornecedores independentes de software) ou as empresas de serviços procuravam previsões sobre a futura demanda de energia. Essas previsões desempenham um papel importante nos negócios atuais e futuros e tornaram-se a base para vários casos de uso. Elas incluem, a curto e a longo prazos, a previsão, comércio, balanceamento de carga e otimização da rede da carga de energia, entre outras. Os métodos de big data e de Análise Avançada (AA), como o Machine Learning, são os principais motivadores para a produção de previsões precisas e confiáveis.  

Neste manual, reunimos as diretrizes de negócios e analíticas necessárias para o desenvolvimento e a implantação bem-sucedidos de uma solução de previsão de demanda de energia. Essas diretrizes propostas podem ajudar empresas de serviços, cientistas de dados e engenheiros de dados no estabelecimento de soluções totalmente operacionalizadas, baseadas em nuvem, de previsão de demanda. Para as empresas que estejam começando sua jornada de big data e de análise avançada, essa solução poderá representar a semente da estratégia de rede inteligente a longo prazo.

> [!TIP]
> Para baixar um diagrama que fornece uma visão geral da arquitetura do modelo, consulte [Arquitetura do Modelo de Solução do Cortana Intelligence para previsão de demanda de energia](cortana-analytics-architecture-demand-forecasting-energy.md).  
> 
> 

## <a name="overview"></a>Visão geral
Este documento aborda os negócios, os dados e os aspectos técnicos do uso do Cortana Intelligence e, especialmente, o AML (Azure Machine Learning) para a implementação e implantação de Soluções de Previsão de Energia. O documento consiste em três partes principais:  

1. Noções básicas sobre negócios  
2. Noções básicas sobre dados  
3. Implementação técnica

A parte **Noções Básicas sobre Negócios** descreve o aspecto comercial que você precisa compreender e considerar antes de tomar uma decisão de investimento. Ela explica como qualificar o problema comercial existente para garantir que a análise preditiva e o aprendizado de máquina sejam realmente eficazes e aplicáveis. O documento explica ainda mais as noções básicas sobre o aprendizado de máquina e como ele é usado para resolver os problemas de previsão de energia. Descreve os pré-requisitos e os critérios de qualificação de um caso de uso. Alguns casos de uso e cenários de caso de uso de exemplo também são fornecidos.

Os dados são o principal ingrediente para qualquer solução de aprendizado de máquina. A parte **Noções Básicas de Dados** deste documento aborda alguns aspectos importantes dos dados. Ela descreve o tipo de dados necessário para a previsão de energia, requisitos de qualidade de dados e quais fontes de dados normalmente existem. Nós também explicamos como os dados brutos são usados para preparar recursos de dados que, na verdade, orientam a parte de modelagem.

A terceira parte do documento aborda o aspecto **Implementação Técnica** de uma solução. A engenharia e a modelagem de recursos são a essência do processo de ciência de dados e, portanto, serão discutidos em detalhes. Ela aborda o conceito de serviços Web, que são um importante veículo para a implantação de soluções de análise preditiva na nuvem. Nós também descrevemos uma arquitetura típica de uma solução operacionalizada completa.

Além disso, o documento inclui material de referência que você pode usar para obter mais conhecimentos sobre o domínio e a tecnologia.

É importante observar que, neste documento, não pretendemos abordar o processo de ciência de dados mais profundo, seus aspectos técnicos e matemáticos. Esses detalhes podem ser encontrados na documentação do [AM do Azure](http://azure.microsoft.com/services/machine-learning/) e em [blogs](http://blogs.microsoft.com/blog/tag/azure-machine-learning/).

### <a name="target-audience"></a>Público-alvo
O público-alvo deste documento é o pessoal comercial e técnico que deseja obter conhecimento e compreensão das soluções baseadas em Machine Learning e como elas estão sendo usadas especificamente no domínio da previsão de energia.

Os cientistas de dados também podem se beneficiar da leitura deste documento para obter uma melhor compreensão do processo de alto nível que orienta a implantação de uma solução de previsão de energia. Neste contexto, ele também pode ser usado para estabelecer uma boa linha de base e um ponto de partida para um material mais detalhado e mais avançado.

### <a name="industry-trends"></a>Tendências do Setor
Nos últimos anos, a IoT, fontes de energia alternativas e Big Data foram mesclados para criar grandes oportunidades no espaço dos serviços e de energia. Ao mesmo tempo, os setores de energia e de serviços têm visto o nivelamento de consumo, com os clientes exigindo maneiras melhores de controlar o uso de energia.

Muitas empresas de serviços e de energia inteligente têm sido pioneiras na [rede inteligente](https://en.wikipedia.org/wiki/Smart_grid) ao implantar vários casos de uso que utilizam os dados gerados pela rede. Muitos casos de uso giram em torno de características inerentes da produção de eletricidade: ela não pode ser acumulada nem armazenada separadamente como um estoque. Portanto, o que é produzido deve ser usado. As empresas de serviços que quiserem se tornar mais eficientes precisam prever o consumo de energia simplesmente porque isso dará a elas maior capacidade de **equilibrar o fornecimento e a demanda**, impedindo o desperdício de energia, **reduzir a emissão de gases de efeito estufa** e controlar os custos.

Quando falamos de custos, há outro aspecto importante, o preço. As novas capacidades de negociar a energia entre empresas de serviços geraram uma grande necessidade de **prever a demanda futura e o preço futuro da eletricidade**. Isso pode ajudar as empresas a determinar seus volumes de produção.

Quando usamos a palavra “inteligente”, na verdade, nos referimos a uma rede que pode aprender e então fazer previsões. Ela pode prever alterações sazonais no consumo, bem como **prever situações de sobrecarga temporária e ajustar-se automaticamente a elas**. Ao regular remotamente o consumo (com a ajuda desses medidores inteligentes), as situações de sobrecarga localizada poderão ser tratadas. **Ao prever primeiro e então agir**, a rede fica mais inteligente ao longo do tempo.

No restante deste documento, nos concentraremos em uma família específica de casos de uso que abrangem a previsão da demanda de energia futura, a curto e a longo prazos. Temos trabalhado nessas áreas há alguns meses e obtivemos conhecimentos e habilidades que nos permitem produzir resultados na rede do setor. Outros casos de uso também serão abordados no documento em um futuro próximo.

## <a name="business-understanding"></a>Noções básicas sobre negócios
### <a name="business-goals"></a>Metas de Negócios
A meta **Demonstração de Energia** serve para demonstrar uma análise preditiva e uma solução de aprendizado de máquina típica que podem ser implantadas em um período muito curto. Especificamente, nosso foco atual é habilitar as soluções de previsão de demanda de energia para que seu valor de negócios possa ser percebido e aproveitado com rapidez. As informações contidas neste manual podem ajudar o cliente cumprir as seguintes metas:

* Pouco tempo para avaliar a solução baseada em aprendizado de máquina
* Capacidade de expandir um caso de uso piloto para outros casos de uso ou para ampliar o escopo com base na necessidade de negócios
* Obter rapidamente o conhecimento sobre o produto Cortana Intelligence Suite

Com essas metas em mente, este manual tem como objetivo fornecer o conhecimento comercial e técnico que ajudará você a atingir essas metas.

### <a name="power-load-and-demand-forecasting"></a>Carga de Energia e Previsão de Demanda
No setor de energia, pode haver muitas maneiras nas quais a previsão de demanda pode ajudar a resolver problemas críticos de negócios. Na verdade, a previsão de demanda pode ser considerada a base para muitos dos principais casos de uso do setor. Em geral, consideramos dois tipos de previsões de demanda de energia: a curto prazo e a longo prazo. Cada um pode servir a uma finalidade diferente e utilizar uma abordagem diferente. A principal diferença entre os dois é o horizonte de previsão, que significa o intervalo de tempo no futuro para o qual podemos prever.

#### <a name="short-term-load-forecasting"></a>Previsão de Carga a Curto Prazo
No contexto de demanda de energia, a Previsão de Carga a Curto Prazo (STLF) é definida como a carga agregada que é prevista em um futuro próximo em várias partes da rede (ou a rede como um todo). Nesse contexto, a curto prazo é definido como o horizonte de tempo no intervalo de uma hora a 24 horas. Em alguns casos, um limite de 48 horas também é possível. Portanto, a STLF é muito comum em casos de uso operacional da rede. Veja alguns exemplos de casos de uso orientados pela STLF:

* Equilíbrio do fornecimento e da demanda
* Suporte à comercialização de energia
* Tomada de mercado (definição do preço da energia)
* Otimização operacional da rede
* [Resposta da demanda](https://en.wikipedia.org/wiki/Demand_response)
* Previsão da demanda de pico
* Gerenciamento do lado da demanda
* Balanceamento de carga e prevenção de sobrecarga
* Previsão de Carga a Longo Prazo
* Detecção de anomalias e de falhas
* Redução/nivelamento do pico 

O modelo STLF baseia-se principalmente no consumo de dados no passado próximo (último dia ou semana) e na temperatura prevista de uso como um indicador importante. A obtenção da previsão de temperatura precisa para a próxima hora e para até 24 horas tem se tornado menos desafiadora nos dias de hoje. Esses modelos são menos sensíveis aos padrões sazonais ou às tendências de consumo a longo prazo.

As soluções SLTF também têm a probabilidade de gerar um alto volume de chamadas de previsão (solicitações de serviço), já que estão sendo invocadas por hora e, em alguns casos, até com uma frequência maior. Também é muito comum ver a implantação onde cada subestação ou transformador individual representa um modelo autônomo. Sendo assim, o volume de solicitações de previsão é ainda maior.

#### <a name="long-term-load-forecasting"></a>Previsão de Carga a Longo Prazo
A meta Previsão de Carga a Longo Prazo (LTLF) serve para prever a demanda de energia com um prazo que varia de uma semana a vários meses (e, em alguns casos, alguns anos). Esse intervalo de horizonte é aplicável principalmente aos casos de uso de planejamento e de investimento.

Para cenários a longo prazo, é importante ter dados de alta qualidade que cubram um intervalo de vários anos (três anos, no mínimo). Esses modelos normalmente extrairão padrões de periodicidade dos dados históricos e usarão previsões externas, como padrões de tempo e de clima.

É importante esclarecer que quanto mais distante estiver o horizonte de previsão, menos precisa será a previsão. Portanto, é importante produzir alguns intervalos de confiança com a previsão real que permitam que os seres humanos considerem a variação possível no processo de planejamento.

Uma vez que o cenário de consumo para a LTLF é composto principalmente de planejamento, podemos esperar volumes de previsão muito menores (em comparação à STLF). Geralmente, veríamos essas previsões inseridas em ferramentas de visualização como o Excel ou o PowerBI e invocadas manualmente pelo usuário.

### <a name="short-term-vs-long-term-prediction"></a>Previsão a Curto Prazo versus a Longo Prazo
A tabela a seguir compara a STLF e a LTLF em relação aos atributos mais importantes:

| Atributo | Previsão de Carga a Curto Prazo | Previsão de Carga a Longo Prazo |
| --- | --- | --- |
| Horizonte de Previsão |De uma hora a 48 horas |De 1 a 6 meses ou mais |
| Granularidade de dados |Por hora |Por hora ou por dia |
| Casos de uso típicos |<ul><li>Balanceamento de demanda/fornecimento</li><li>Selecionar a hora de previsão</li><li>Resposta da demanda</li></ul> |<ul><li>Planejamento a longo prazo</li><li>Planejamento de ativos de grade</li><li>Planejamento de recursos</li></ul> |
| Indicadores típicos |<ul><li>Dia ou semana</li><li>Hora do dia</li><li>Temperatura por hora</li></ul> |<ul><li>Mês do ano</li><li>Dia do mês</li><li>Clima e temperatura a longo prazo</li></ul> |
| Intervalo de dados históricos |Dois a três anos de dados |Cinco a dez anos de dados |
| Precisão típica |MAPE* de 5% ou menos |MAPE* de 25% ou menos |
| Frequência de previsão |Produzida a cada hora ou a cada 24 horas |Produzida uma vez por mês, trimestre ou ano |

\*[MAPE](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error) – Erro Percentual Médio

Como pode ser visto nesta tabela, é muito importante distinguir entre os cenários de previsão a curto e a longo prazo, já que eles representam necessidades comerciais diferentes e podem ter padrões de implantação e de consumo diferentes.

### <a name="example-use-case-1-esmart-systems--overload-optimization"></a>Caso de Uso do Exemplo 1: Sistemas eSmart – otimização de sobrecarga
Uma função importante de uma [rede inteligente](https://en.wikipedia.org/wiki/Smart_grid) é otimizar e ajustar de forma dinâmica e constante de acordo com os padrões de consumo à medida que eles são alterados. O consumo de energia pode ser afetado por alterações a curto prazo causadas principalmente por flutuações de temperatura (*por exemplo*, mais energia é usada em sistemas de ar condicionado ou de aquecimento). Ao mesmo tempo, o consumo de energia também é influenciado pelas tendências a longo prazo. Elas podem incluir efeitos de periodicidade, feriados nacionais, crescimento do consumo a longo prazo e até fatores econômicos como o índice do consumidor, o preço do petróleo e o PIB.

Neste caso de uso, o [eSmart](http://www.esmartsystems.com/) queria implantar uma solução baseada em nuvem que permitia prever a propensão a uma situação de sobrecarga em uma determinada subestação da rede. Em particular, o eSmart queria identificar as subestações que provavelmente seriam sobrecarregadas na hora seguinte, para que uma ação imediata pudesse ser tomada para evitar ou resolver a situação.

Uma previsão de execução precisa e rápida exige a implementação de três modelos de previsão:

* O modelo a longo prazo que permite a previsão do consumo de energia em cada subestação durante as próximas semanas ou meses
* O modelo a curto prazo que permite a previsão de uma situação de sobrecarga em uma determinada subestação durante a próxima hora
* O modelo de temperatura que fornece a previsão de temperatura futura em vários cenários

O objetivo do modelo a longo prazo é classificar as subestações por sua propensão à sobrecarga (dada a capacidade de transmissão de energia delas) durante a semana ou o próximo mês. Isso permite a criação de uma pequena lista de subestações que serviria como uma entrada para a previsão a curto prazo. Como a temperatura é um importante indicador para o modelo a longo prazo, não há necessidade de produzir constantemente previsões de temperatura de vários cenários e adicioná-las como entradas no modelo a longo prazo. A previsão a curto prazo é então invocada para prever qual subestação provavelmente será sobrecarregada na próxima hora.

Os modelos a curto e a longo prazo são implantados individualmente por cada subestação. Portanto, a execução prática desses modelos requer uma orquestração abrangente. Para obter uma maior precisão da previsão a curto prazo, um modelo mais granular é dedicado a cada hora do dia. Todos esses modelos são executados a cada hora e sua execução é concluída em alguns minutos, para permitir que haja tempo suficiente para responder e tomar ações preventivas, se necessário. Essa coleção de modelos é mantida atualizada por meio da readaptação periódica com os dados mais recentes.

Mais informações sobre este caso de uso podem ser encontradas [aqui](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18945).

#### <a name="use-case-qualification-criteria--prerequisites"></a>Usar Critérios de Qualificação de Caso – Pré-requisitos
A principal força do Cortana Intelligence está em sua poderosa capacidade de implantar e escalar soluções centradas no aprendizado de máquina. Ele foi projetado para dar suporte a milhares de previsões executadas simultaneamente. Ele pode ser dimensionado automaticamente para atender a uma alteração no padrão de consumo. Portanto, o foco da solução é a precisão e o desempenho computacional. Por exemplo, uma empresa está interessada em produzir uma previsão precisa da demanda de energia para a próxima hora e para cada hora do dia. Por outro lado, estamos menos interessados em responder à pergunta sobre o motivo da demanda ser prevista dessa forma (o próprio modelo cuidará disso).

Portanto, é importante perceber que nem todos os casos de uso e os problemas de negócios podem ser efetivamente solucionados usando o aprendizado de máquina.

O Cortana Intelligence e o aprendizado de máquina podem ser altamente eficazes na resolução de um determinado problema de negócios quando os critérios a seguir forem atendidos:

* O problema de negócios em questão é **previsível** por natureza. Um exemplo de caso de uso previsível é o de uma empresa de serviços que gostaria de prever a carga de energia em uma determinada subestação durante a próxima hora. Por outro lado, a análise e a classificação de acionadores de demanda histórica seriam **descritivas** por natureza e, portanto, menos aplicáveis.
* Há um claro **caminho da ação** a ser tomado assim que a previsão estiver disponível. Por exemplo, prever uma sobrecarga em uma subestação durante a próxima hora pode disparar uma ação proativa de redução da carga associada à subestação, potencialmente impedindo uma sobrecarga.
* O caso de uso representa um **tipo de problema típico** de modo que, quando resolvido, pode preparar o caminho para solucionar outros casos de uso semelhantes.
* O cliente pode definir **metas quantitativas e qualitativas** para demonstrar a implementação de uma solução bem-sucedida. Por exemplo, uma boa meta quantitativa para a previsão da demanda de energia seria o limite de precisão necessária (*por exemplo*, um erro de até 5% é permitido) ou na previsão da sobrecarga da subestação, a precisão (taxa de verdadeiros positivos) e a recuperação (extensão de verdadeiros positivos) deveriam ficar acima de um determinado limite. Essas metas devem ser derivadas de metas de negócios do cliente.
* Há um claro **cenário de integração** com o fluxo de trabalho de negócios da empresa. Por exemplo, a previsão de carga da subestação pode ser integrada à central de controle da rede para permitir atividades de prevenção de sobrecarga.
* O cliente tem **dados com qualidade suficiente** prontos para uso para dar suporte ao caso de uso (veja mais na próxima seção, **Qualidade de Dados**, neste manual).
* O cliente adota a arquitetura de dados centrada na nuvem ou o **aprendizado de máquina baseado em nuvem**, incluindo o AM do Azure e outros componentes do Cortana Intelligence Suite.
* O cliente está disposto a estabelecer **um fluxo de dados completo** que facilite a entrega de dados na nuvem de forma contínua e está disposto a **operacionalizar** a solução.
* O cliente está pronto para **dedicar recursos** que serão ativamente envolvidos durante a implementação piloto inicial para que o conhecimento e a propriedade da solução possam ser transferidos para o cliente após a conclusão bem-sucedida.
* O recurso do cliente deve ser um **profissional de dados experiente**, preferencialmente um cientista de dados.

A qualificação de um caso de uso baseado nos critérios acima pode melhorar significativamente as taxas de êxito de um caso de uso e estabelecer uma boa base para a implementação de casos de uso futuros.

### <a name="cloud-based-solutions"></a>Soluções Baseadas em Nuvem
O Cortana Intelligence Suite no Azure é um ambiente integrado que reside na nuvem. A implantação de uma solução de análise avançada em um ambiente de nuvem contém benefícios substanciais para as empresas e, ao mesmo tempo, pode significar uma grande mudança para as empresas que ainda usam soluções de TI locais. No setor de energia, há uma clara tendência de migração gradual de operações para a nuvem. Essa tendência anda de mãos dadas com o desenvolvimento da rede inteligente, como discutido acima, em **Tendências do Setor**. Como este manual se concentra em uma solução baseada em nuvem no domínio de energia, é importante explicar os benefícios e outras considerações de implantação de uma solução baseada em nuvem.

Talvez a maior vantagem de uma solução baseada em nuvem seja o custo. Como as soluções utilizam componentes implantados na nuvem, não há custos iniciais ou COGS (Custo dos Bens Vendidos) associados a elas. Isso significa que não é necessário investir em hardware, software e na manutenção de TI e, portanto, há uma redução substancial dos riscos para o negócio.

Outra vantagem importante é a estrutura de custo pré-pago das soluções baseadas em nuvem. Os servidores baseados em nuvem para computação ou armazenamento podem ser implantados e dimensionados conforme o necessário. Isso representa a vantagem da eficiência de custo de uma solução baseada em nuvem.

Por fim, não há necessidade de investir em manutenção de TI ou em desenvolvimento de infraestrutura futura, já que tudo isso faz parte da oferta baseada em nuvem. Nesta medida, o Cortana Intelligence Suite inclui os melhores serviços e seu roteiro se mantém em evolução. Novos recursos e componentes são constantemente introduzidos e evoluem.

Para uma empresa que esteja iniciando sua transição para a nuvem, é altamente recomendável adotar uma abordagem gradual ao implementar um roteiro de migração na nuvem. Acreditamos que, para empresas de serviços e para as empresas no domínio de energia, os casos de uso abordados neste manual representam uma excelente oportunidade para a criação de pilotos de soluções de análise preditiva na nuvem.

#### <a name="business-case-justification-considerations"></a>Considerações sobre a Justificativa de Casos de Negócios
Em muitos casos, o cliente pode estar interessado em criar uma justificativa de negócios para um determinado caso de uso em que uma solução baseada em nuvem e o Machine Learning são componentes importantes. Ao contrário de uma solução local, no caso de uma solução baseada em nuvem, o componente de custo inicial é mínimo e a maioria dos elementos de custo está associada ao uso real. Quando se trata de implantar uma solução de previsão de energia no Cortana Intelligence Suite, vários serviços podem ser integrados a uma única estrutura de custo comum. Por exemplo, os bancos de dados (*por exemplo*, o SQL Azure) podem ser usados para armazenar os dados brutos e, para as previsões reais, o AM do Azure será usado para hospedar os serviços de previsão. Neste exemplo, a estrutura de custo poderia incluir componentes transacionais e de armazenamento.

Por outro lado, você deve ter uma boa compreensão do valor de negócio da operação de uma previsão de demanda de energia (a curto ou a longo prazo). Na verdade, é importante perceber o valor de negócio de cada operação de previsão. Por exemplo, prever uma carga de energia para as próximas 24 horas com precisão pode impedir a produção em excesso ou pode ajudar a impedir sobrecargas na rede, Isso pode ser quantificado em termos de economia financeira diariamente.

Uma fórmula básica para calcular as vantagens financeiras de demanda de previsão solução seria: ![Fórmula básica para calcular as vantagens financeiras da solução de previsão de demanda](media/cortana-analytics-playbook-demand-forecasting-energy/financial-benefit-formula.png)

Como o Cortana Intelligence Suite fornece um modelo de preços pré-pagos, não é necessário incorrer em um componente de custo fixo para essa fórmula. Esta fórmula pode ser calculada de forma diária, mensal ou anual.

Os planos de preços atuais do Cortana Intelligence Suite e do AM do Azure podem ser encontrados [aqui](http://azure.microsoft.com/pricing/details/machine-learning/).

### <a name="solution-development-process"></a>Processo de Desenvolvimento da Solução
O ciclo de desenvolvimento de uma solução de previsão de demanda de energia geralmente envolve quatro fases, em que usamos as tecnologias e os serviços baseados em nuvem do Cortana Intelligence Suite.

Isso é ilustrado no diagrama a seguir:

![Ciclo de Rede Inteligente](media/cortana-analytics-playbook-demand-forecasting-energy/smart-grid-cycle.png)

O parágrafo a seguir descreve esse processo da 4 etapas:

1. **Coleta de Dados** – qualquer solução baseada em análise avançada baseia-se em dados (veja **Noções básicas sobre dados**). Especificamente, quando se trata de análise preditiva e de previsão, nos baseamos em um fluxo de dados dinâmico e contínuo. No caso da previsão de demanda de energia, esses dados podem ser obtidos diretamente dos medidores inteligentes ou já podem estar agregados a um banco de dados local. Nós também utilizamos fontes de dados externas, como o clima e a temperatura. Esse fluxo contínuo de dados deve ser organizado, agendado e armazenado. [Data Factory do Azure](http://azure.microsoft.com/services/data-factory/) (ADF) é a nossa força de trabalho principal para realizar essa tarefa.
2. **Modelagem** – para previsões de energia precisas e confiáveis, você deve desenvolver (treinar) e manter um excelente modelo que use os dados históricos e extraia os padrões significativos e de previsão dos dados. A área do Machine Learning (ML) tem crescido rapidamente com algoritmos mais avançados, desenvolvidos de forma rotineira. O Estúdio AM do Azure fornece uma excelente experiência de usuário que ajuda a utilizar os algoritmos de AM mais avançados em um fluxo de trabalho completo. Esse fluxo de trabalho é ilustrado em um diagrama de fluxo intuitivo e inclui a preparação de dados, extração de recursos, modelagem e avaliação de modelos. O usuário pode reunir centenas de modelos variados que estão incluídos nesse ambiente. Até o final dessa fase, um cientista de dados terá um modelo de trabalho completamente avaliado e pronto para implantação.
   
   O diagrama a seguir é uma ilustração de um fluxo de trabalho típico:
   
   ![Fluxo de Trabalho de Modelagem](media/cortana-analytics-playbook-demand-forecasting-energy/modeling-workflow.png)
3. **Implantação** – com um modelo de trabalho em mãos, a próxima etapa será a implantação. Aqui, o modelo é convertido em um serviço Web que expõe uma API RESTful que pode ser invocada simultaneamente pela Internet de vários clientes de consumo. O AM do Azure fornece uma maneira simples de implantar um modelo diretamente do Estúdio AM do Azure com um único clique de botão. Todo o processo de implantação acontece nos bastidores. Essa solução pode ser automaticamente dimensionada para atingir o consumo necessário.
4. **Consumo** – nesta fase, podemos realmente usar o modelo de previsão para produzir previsões. O consumo pode ser gerado por um aplicativo do usuário (*por exemplo*, um painel) ou diretamente de um sistema operacional como um sistema de equilíbrio de demanda/fornecimento ou de uma solução de otimização de rede. Vários casos de uso podem ser obtidos de um único modelo.

## <a name="data-understanding"></a>Noções básicas sobre dados
Depois de abordar as considerações de negócios (veja **Noções Básicas sobre Negócios**) de uma solução de previsão de demanda de energia, agora estamos prontos para discutir a parte de dados. Qualquer solução de análise preditiva se baseia em dados confiáveis. Para a previsão de demanda de energia, podemos contar com dados históricos de consumo com vários níveis de granularidade. Esses dados históricos são usados como matéria-prima. Eles passarão por uma análise cuidadosa na qual o cientista de dados identificará indicadores (também conhecidos como recursos) que poderão ser colocados em um modelo que, eventualmente, gerará as previsões necessárias.

No restante desta seção, descreveremos as diversas etapas e considerações para entender os dados e como colocá-los em um formato utilizável.

### <a name="the-model-development-cycle"></a>O Ciclo de Desenvolvimento do Modelo
A produção de bons modelos requer uma preparação e um planejamento cuidadosos. A divisão do processo de modelagem em várias etapas e a concentração em uma etapa por vez pode aprimorar drasticamente o resultado de todo o processo.

O diagrama a seguir ilustra como o processo de modelagem pode ser dividido em várias etapas:

![Ciclo de Desenvolvimento do Modelo](media/cortana-analytics-playbook-demand-forecasting-energy/model-development-cycle.png)

Como se pode ver, o ciclo consiste em seis etapas:

* Formulação do problema
* Ingestão de dados e exploração de dados
* Preparação de dados e engenharia de recursos
* Modelagem
* Avaliação do modelo
* Desenvolvimento

No restante desta seção, descreveremos as etapas individuais e os itens a serem considerados em cada etapa.

### <a name="problem-formulation"></a>Formulação do problema
Podemos considerar a formulação do problema como a etapa mais crítica pela qual alguém precisa passar antes de implementar qualquer solução de análise preditiva. Aqui, transformaríamos o problema de negócios e o decomporíamos em elementos específicos, que poderiam ser solucionados com os dados e as técnicas de modelagem. É uma boa prática formular o problema como um conjunto de perguntas que gostaríamos de responder. Veja algumas perguntas possíveis que podem ser aplicáveis ao escopo de previsão da demanda de energia:

* Qual é a carga esperada em uma subestação individual na próxima hora ou no próximo dia?
* A que hora do dia minha rede sofrerá a demanda de pico?
* Qual é a probabilidade da minha rede manter a carga de pico esperada?
* Quanta energia a estação de energia deverá gerar durante cada hora do dia?

A formulação dessas perguntas permite que nos concentremos na obtenção dos dados corretos e na implementação de uma solução totalmente alinhada aos problemas de negócios em questão. Além disso, poderemos assim definir algumas métricas chave que nos permitirão avaliar o desempenho do modelo. Por exemplo, que grau de exatidão a previsão deverá ter e qual é o intervalo de erros que ainda seria aceitável para a empresa?

### <a name="data-sources"></a>Fontes de dados
A rede inteligente moderna coleta dados de várias partes e componentes da rede. Esses dados representam vários aspectos da operação e da utilização da rede de energia. No escopo da previsão da demanda de energia, estamos limitando a discussão a fontes de dados que reflitam o consumo de demanda real. Uma fonte do consumo de energia importante é composta pelos medidores inteligentes. As empresas de serviços em todo o mundo têm implantado medidores inteligentes para seus consumidores. Os medidores inteligentes registram o consumo de energia real e retransmitem constantemente esses dados à empresa de serviço. Os dados são coletados e enviados de volta em um intervalo fixo, desde a cada cinco minutos até a cada hora. Medidores inteligentes mais avançados podem ser programados remotamente para controlar e equilibrar o consumo real em uma casa. Os dados dos medidores inteligentes são relativamente confiáveis e incluem um carimbo de data/hora. Dessa maneira, é um ingrediente importante para a previsão da demanda. Os dados do medidor podem ser agregados (somados) em vários níveis na topologia da rede: transformador, subestação, região *etc*. Podemos então escolher o nível de agregação necessária para criar um modelo de previsão para ele. Por exemplo, se a empresa de serviço quiser prever a carga futura em cada uma das subestações da rede, então todos os dados dos medidores poderão ser agregados para cada subestação individual e usados como uma entrada para o modelo de previsão. Podemos nos referir aos medidores inteligentes como uma fonte de dados interna.

Uma previsão de demanda de energia confiável também se baseará em outras fontes de dados externas. Um importante fator que afeta o consumo de energia é o clima ou, mais precisamente, a temperatura. Os dados históricos mostram uma forte correlação entre a temperatura externa e o consumo de energia. Nos dias quentes de verão, os consumidores usarão seus aparelhos de ar-condicionado e, durante o inverno, usarão os sistemas de energia ou de aquecimento. Portanto, uma fonte confiável de temperaturas históricas no local da rede é fundamental. Além disso, também contamos com a previsão exata da temperatura como um indicador do consumo de energia.

Outras fontes de dados externas também podem ajudar na criação de modelos de previsão de demanda de energia. Elas podem incluir alterações de clima a longo prazo, índices econômicos (*por exemplo*, o PIB), entre outros. Neste documento, não incluiremos essas fontes de dados.

### <a name="data-structure"></a>Estrutura de Dados
Depois de identificar as fontes de dados necessárias, gostaríamos de garantir que os dados brutos coletados incluíssem os recursos de dados corretos. Para criar um modelo de previsão de demanda confiável, teríamos que garantir que os dados coletados incluíssem elementos de dados que poderiam ajudar a prever a demanda futura. Veja alguns requisitos básicos sobre a estrutura de dados (esquema) dos dados brutos.

Os dados brutos consistem em linhas e colunas. Cada medição é representada como uma única linha de dados. Cada linha de dados inclui várias colunas (também conhecidas como campos ou recursos).

1. **Carimbo de data/hora** – o campo de carimbo de data/hora representa a hora real em que a medição foi registrada. Ele deve ser compatível com um dos formatos de data/hora comuns. Ambas as partes de data e de hora devem ser incluídas. Na maioria dos casos, não é preciso que a hora seja registrada até o segundo nível de granularidade. É importante especificar o fuso horário no qual os dados são registrados.
2. **ID do medidor** - esse campo identifica o medidor ou o dispositivo de medição. É uma variável categórica e pode ser uma combinação de caracteres e de dígitos.
3. **Valor de consumo** – é o consumo real em uma determinada data/hora. O consumo pode ser medido em kWh (kilowatt-hora) ou em qualquer outra unidade de sua preferência. É importante observar que a unidade de medição deve permanecer consistente em todas as medições nos dados. Em alguns casos, o consumo pode ser fornecido em mais de três fases de energia. Nesse caso, será necessário coletar todas as fases independentes do consumo.
4. **Temperatura** – a temperatura normalmente é coletada de uma fonte independente. No entanto, ela deve ser compatível com os dados de consumo. Ela deve incluir um carimbo de data/hora conforme descrito acima, que permitirá sua sincronização com os dados de consumo real. O valor da temperatura pode ser especificado em graus Celsius ou Fahrenheit, mas deve permanecer consistente em todas as medições.
5. **Local –** o campo de local está geralmente associado ao local onde os dados de temperatura foram coletados. Ele pode ser representado em forma de um número de código postal ou no formato de latitude/longitude (lat/long).

As tabelas a seguir mostram exemplos de um bom formato de dados de consumo e de temperatura:

| **Data** | **Hora** | **ID de medidor** | **Fase 1** | **Fase 2** | **Fase 3** |
| --- | --- | --- | --- | --- | --- |
| 1/7/2015 |10:00:00 |ABC1234 |7.0 |2,1 |5,3 |
| 1/7/2015 |10:00:01 |ABC1234 |7.1 |2.2 |4.3 |
| 1/7/2015 |10:00:02 |ABC1234 |6,0 |2,1 |4,0 |

| **Data** | **Hora** | **Localidade** | **Temperatura** |
| --- | --- | --- | --- |
| 1/7/2015 |10:00:00 |11242 |24,4 |
| 1/7/2015 |10:00:01 |11242 |24,4 |
| 1/7/2015 |10:00:02 |11242 |24,5 |

Como pode ser visto acima, este exemplo inclui três valores diferentes para o consumo associado a três fases de energia. Além disso, observe que os campos de data e de hora estão separados, mas também podem ser combinados em uma única coluna. Nesse caso, a coluna de local é representada em formato de código postal de cinco dígitos e a temperatura em formato de graus Celsius.

### <a name="data-format"></a>Formato de Dados
O Cortana Intelligence Suite pode dar suporte à maioria dos formatos de dados comuns, como CSV, TSV, JSON *etc*. É importante que o formato de dados permaneça consistente durante todo o ciclo de vida do projeto.

### <a name="data-ingestion"></a>Ingestão de dados
Como a previsão da demanda de energia é prevista com constância e com frequência, devemos garantir que os dados brutos estejam fluindo por meio de um processo de ingestão de dados sólido e confiável. O processo de ingestão deve garantir que os dados brutos estejam disponíveis para o processo de previsão no tempo necessário. Isso significa que a frequência da ingestão de dados deve ser maior do que a frequência de previsão.

Por exemplo: se nossa solução de previsão de demanda gerasse uma nova previsão às 8:00 diariamente, precisaríamos garantir que todos os dados coletados durante as últimas 24 horas tivessem sido totalmente ingeridos até esse ponto e tivessem incluído até a última hora de dados.

Para fazer isso, o Cortana Intelligence Suite oferece várias maneiras de dar suporte a um processo de ingestão de dados confiável. Isso será discutido em mais detalhes na seção **Implantação** deste documento.

### <a name="data-quality"></a>Qualidade de Dados
A fonte de dados brutos necessária para executar a previsão de demanda precisa e confiável deve atender a alguns critérios básicos de qualidade de dados. Embora métodos estatísticos avançados possam ser usados para compensar algum problema de qualidade de dados possível, ainda será necessário garantir que estejamos cruzando algum limite de qualidade de dados de base ao ingerirmos novos dados. Veja algumas considerações em relação à qualidade dos dados brutos:

* **Valor ausente** – refere-se à situação quando a medição específica não foi coletada. O requisito básico aqui é que a taxa de valor ausente não deve ser maior do que 10% para qualquer período de tempo determinado. No caso em que um único valor esteja ausente, ele deverá ser indicado usando um valor predefinido (por exemplo: “9999”) e não “0”, que pode ser uma medição válida.
* **Precisão da medição** – o valor real de consumo ou de temperatura deve ser registrado com precisão. Medição imprecisas produzirão previsões imprecisas. Normalmente, o erro de medição deve ser inferior a 1% em relação ao valor verdadeiro.
* **Hora da medição** – é necessário que o carimbo de data/hora real dos dados coletados não desviem por mais de 10 segundos em relação ao horário da medição real.
* **Sincronização** – quando várias fontes de dados são usadas (*por exemplo*, temperatura e consumo), devemos garantir que não haja problemas de sincronização de horas entre elas. Isso significa que a diferença de tempo entre o carimbo de data/hora coletado de cada duas fontes de dados independentes não exceda 10 segundos.
* **Latência** - como discutido acima, em **Ingestão de Dados**, dependemos de um fluxo de dados e de um processo de ingestão confiáveis. Para controlarmos isso, devemos garantir que a latência de dados esteja sob controle. Isso é especificado como a diferença de tempo entre a hora em que a medição real foi feita e a hora na qual ela foi carregada no armazenamento do Cortana Intelligence Suite e está pronta para uso. Para a previsão de carga a longo prazo, a latência total não deverá ser maior do que 30 minutos. Para a previsão de carga a longo prazo, a latência total não deverá ser maior do que um dia.

### <a name="data-preparation-and-feature-engineering"></a>Preparação de dados e engenharia de recursos
Depois que os dados brutos tiverem sido ingeridos (veja **Ingestão de Dados**) e tiverem sido armazenados de forma segura, estarão prontos para o processamento. A fase de preparação de dados consiste basicamente em pegar os dados brutos e convertê-los (transformá-los, dar nova forma a eles) em um formulário para a fase de modelagem. Isso pode incluir operações simples, como o uso da coluna de dados brutos como ela está com seu valor medido real, valores padronizados, operações mais complexas como o [retardo de tempo](https://en.wikipedia.org/wiki/Lag_operator), entre outros. As colunas de dados recém-criadas são conhecidas como recursos de dados e o processo de gerá-las é chamado de engenharia de recursos. No final desse processo, teríamos um novo conjunto de dados derivado dos dados brutos que pode ser usado para modelagem. Além disso, a fase de preparação de dados precisa cuidar dos valores ausentes (veja **Qualidade de Dados**) e compensá-los. Em alguns casos, também seria preciso normalizar os dados para garantir que todos os valores fossem representados na mesma escala.

Nesta seção, listamos alguns dos recursos de dados comuns incluídos nos modelos de previsão de demanda de energia.

**Recursos controlados pelo tempo:** esses recursos são derivados dos dados de carimbo de data/hora. Eles são extraídos e convertidos em recursos categóricos como:

* Hora do dia – é a hora do dia que recebe valores de 0 a 23
* Dia da semana – representa o dia da semana e recebe valores de 1 (domingo) a 7 (sábado)
* Dia do mês – representa a data real e pode receber valores de 1 a 31
* Mês do ano – representa o mês e recebe valores de 1 (janeiro) a 12 (dezembro)
* Fim de semana – é um recurso de valor binário que recebe os valores 0 para dias da semana ou 1 para o fim de semana
* Feriado – é um recurso de valor binário que recebe os valores 0 para um dia comum ou 1 para um feriado
* Termos de Fourier – os termos de Fourier são pesos que derivam do carimbo de data/hora e são usados para capturar a periodicidade (ciclos) nos dados. Como podemos ter várias estações em nossos dados, talvez sejam necessários vários termos de Fourier. Por exemplo, os valores da demanda podem ter estações/ciclos anuais, semanais e diários, o que resultará em três termos de Fourier.

**Recursos de medição independentes:** os recursos independentes incluem todos os elementos de dados que gostaríamos de usar como indicadores em nosso modelo. Aqui, podemos excluir o recurso dependente que precisamos prever.

* Recurso de retardo – são valores de tempo deslocados da demanda real. Por exemplo, os recursos de retardo 1 conterão o valor de demanda na hora anterior (supondo que sejam os dados por hora) relativo ao carimbo de data/hora atual. Da mesma forma, podemos adicionar o retardo 2, o retardo 3 *etc*. A combinação real dos recursos de retardo usados é determinada durante a fase de modelagem por meio da avaliação dos resultados do modelo.
* Tendências a longo prazo – esse recurso representa o crescimento linear em demanda entre os anos.

**Recurso dependente:** recurso dependente é a coluna de dados que gostaríamos que nosso modelo previsse. Com o [aprendizado de máquina supervisionado](https://en.wikipedia.org/wiki/Supervised_learning), primeiro precisamos treinar o modelo usando os recursos dependentes (também conhecidos como rótulos). Isso permite que o modelo aprenda os padrões nos dados associados ao recurso dependente. Na previsão da demanda de energia, geralmente desejamos prever a demanda real e, portanto, poderíamos usá-la como o recurso dependente.

**Tratamento de valores ausentes:** durante a fase de preparação de dados, seria preciso determinar a melhor estratégia para lidar com os valores ausentes. Isso é feito principalmente usando as diversas estatísticas [métodos de imputação de dados](https://en.wikipedia.org/wiki/Imputation_\(statistics\)). No caso da previsão da demanda de energia, nós geralmente imputamos valores ausentes usando a média da movimentação de pontos de dados disponíveis anteriores.

**Normalização de Dados:** a normalização dos dados é outro tipo de transformação usado para colocar todos os dados numéricos, como a previsão de demanda, em uma escala semelhante. Normalmente, isso ajuda a melhorar a exatidão e a precisão do modelo. Geralmente, faríamos isso dividindo o valor real pelo intervalo dos dados.
Isso dimensionará o valor original para um intervalo menor, normalmente entre -1 e 1.

## <a name="modeling"></a>Modelagem
A fase de modelagem é onde ocorre a conversão de dados em um modelo. No centro desse processo, estão os algoritmos avançados que examinam os dados históricos (dados de treinamento), que extraem os padrões e que criam um modelo. Posteriormente, esse modelo poderá ser usado para prever novos dados que não tenham sido usados para criar o modelo.

Assim que tivermos um modelo confiável funcional, poderemos então usá-lo para pontuar novos dados estruturados para incluir os recursos necessários (X). O processo de pontuação usará o modelo persistente (objeto da fase de treinamento) e preverá a variável de destino indicada por Ŷ.

### <a name="demand-forecasting-modeling-techniques"></a>Técnicas de Modelagem da Previsão da Demanda
No caso da previsão da demanda, usamos os dados históricos ordenados por hora. Geralmente, nos referimos aos dados que incluem a dimensão de tempo como [série temporal](https://en.wikipedia.org/wiki/Time_series). A meta na modelagem da série temporal é encontrar tendências relacionadas ao tempo, a periodicidade, a correlação automática (correlação ao longo do tempo) e formular tudo em um modelo.

Nos últimos anos, algoritmos avançados foram desenvolvidos para acomodar a previsão da série temporal e aumentar a precisão da previsão. Discutiremos brevemente alguns deles aqui.

> [!NOTE]
> Esta seção não destina-se a ser usada como uma visão geral do aprendizado de máquina e da previsão, mas como uma pesquisa rápida de técnicas de modelagem comumente usadas para a previsão da demanda. Para saber mais e para obter material educativo sobre a previsão da série temporal, é altamente recomendável ler o livro online [Forecasting: principles and practice (Previsão: princípios e prática)](https://www.otexts.org/book/fpp).
> 
> 

#### <a name="ma-moving-averagehttpswwwotextsorgfpp62"></a>[**MA (Média de Movimentação)**](https://www.otexts.org/fpp/6/2)
A média de movimentação é uma das primeiras técnicas analíticas que foram usadas para a previsão da série temporal e ainda hoje é uma das técnicas mais comumente usadas. Também é a base para técnicas de previsão mais avançadas. Com a média de movimentação, estamos prevendo o próximo ponto de dados ao calcular a média nos pontos K mais recentes, onde K indica a ordem da média de movimentação.

A técnica da média de movimentação tem o efeito de suavização da previsão e, portanto, não pode manipular bem a grande volatilidade nos dados.

#### <a name="ets-exponential-smoothinghttpswwwotextsorgfpp75"></a>[**ETS (Suavização Exponencial)**](https://www.otexts.org/fpp/7/5)
A Suavização Exponencial (ETS) é uma família de vários métodos que usam a média ponderada de pontos de dados recentes para prever o próximo ponto de dados. A ideia é atribuir pesos mais altos a valores mais recentes e diminuir gradualmente esse peso para valores medidos mais antigos. Há vários métodos diferentes nessa família, alguns deles incluem o tratamento da periodicidade dos dados como o [Método Sazonal Holt-Winters](https://www.otexts.org/fpp/7/5).

Alguns desses métodos também consideram a periodicidade dos dados.

#### <a name="arima-auto-regression-integrated-moving-averagehttpswwwotextsorgfpp8"></a>[**ARIMA (Média de Movimentação Integrada de Regressão Automática)**](https://www.otexts.org/fpp/8)
A Média de Movimentação Integrada de Regressão Automática (ARIMA) é outra família de métodos que é normalmente usada na previsão da série temporal. Ela praticamente combina métodos de regressão automática à média de movimentação. Os métodos de regressão automática usam modelos de regressão ao receberem valores anteriores da série temporal para calcular o próximo ponto de data. Os métodos ARIMA também se aplicam a métodos de diferenciação, que incluem o cálculo da diferença entre os pontos de dados e o uso deles em vez do valor original medido. Por fim, a ARIMA também usa as técnicas de média de movimentação discutidas acima. A combinação de todos esses métodos de várias maneiras é o que constrói a família dos métodos ARIMA.

Atualmente, os métodos ETS e ARIMA são amplamente usados na previsão da demanda de energia e em muitos outros problemas de previsão. Em muitos casos, eles são combinados para oferecer resultados muito precisos.

**Regressão Múltipla Geral** os modelos de regressão podem ser a abordagem de modelagem mais importante no domínio de aprendizado de máquina e de estatísticas. No contexto de série temporal, usamos a regressão para prever os valores futuros (*por exemplo*, de demanda). Na regressão, pegamos uma combinação linear das previsões e aprendemos os pesos (também conhecidos como coeficientes) desses indicadores durante o processo de treinamento. A meta é produzir uma linha de regressão que preveja nosso valor previsto. Os métodos de regressão serão adequados quando a variável de destino for numérica e, portanto, também se adapte à previsão de série temporal. Há uma grande variedade de métodos de regressão, incluindo modelos de regressão muito simples, como a [Regressão Linear](https://en.wikipedia.org/wiki/Linear_regression), e mais avançados, como as árvores de decisão, as [Florestas Aleatórias](https://en.wikipedia.org/wiki/Random_forest), as [Redes Neurais](https://en.wikipedia.org/wiki/Artificial_neural_network) e as Árvores de Decisão Aumentadas.

A construção da previsão de demanda de energia como um problema de regressão nos dá muita flexibilidade para a seleção de nossos recursos de dados, que podem ser combinados a partir dos dados reais de série temporal e dos fatores externos, como a temperatura. Para saber mais sobre os recursos selecionados, confira a seção Engenharia de Recursos (confira **Preparação de Dados e Engenharia de Recursos**) deste manual.

Em nossa experiência com a implementação e a implantação do piloto de previsões de demanda de energia, descobrimos que os modelos de regressão avançados disponíveis no AM do Azure tendem a produzir os melhores resultados e por isso nós os utilizamos.

## <a name="model-evaluation"></a>Avaliação do modelo
A avaliação do modelo tem um papel fundamental no **Ciclo de Desenvolvimento do Modelo**. Nesta etapa, examinamos a validação do modelo e seu desempenho com dados reais. Durante a etapa de modelagem, usamos uma parte dos dados disponíveis para treinar o modelo. Durante a fase de avaliação, pegamos o restante dos dados para testar o modelo. Praticamente, isso significa que estamos alimentando o modelo com dados novos que foram restruturados e que contêm os mesmos recursos do conjunto de dados de treinamento. No entanto, durante o processo de validação, usamos o modelo para prever a variável de destino em vez de fornecer a variável de destino disponível. Normalmente, nos referimos a esse processo como pontuação de modelo. Usaríamos, então, os valores de destino verdadeiros e os compararíamos aos previstos. A meta aqui é medir e minimizar o erro de previsão, que significa a diferença entre as previsões e o valor verdadeiro. A quantificação da medição do erro é fundamental já que gostaríamos de ajustar o modelo e validar se o erro está realmente diminuindo. O ajuste do modelo pode ser feito por meio da modificação dos parâmetros do modelo que controlam o processo de aprendizagem ou por meio da adição ou da remoção dos recursos de dados (conhecido como [varredura de parâmetros](https://channel9.msdn.com/Blogs/Azure/Data-Science-Series-Building-an-Optimal-Model-With-Parameter-Sweep)). Praticamente, isso significa que talvez seja necessário iterar entre as fases de engenharia de recursos, de modelagem e de avaliação do modelo várias vezes até podermos reduzir o erro até o nível exigido.

É importante enfatizar que o erro de previsão nunca será zero, já que nunca haverá um modelo que possa prever com perfeição todos os resultados. No entanto, há uma determinada magnitude de erro que é aceitável para os negócios. Durante o processo de validação, queremos garantir que nosso erro do modelo de previsão seja igual ou que seja melhor do que o nível da tolerância do negócio. Portanto, é importante definir o nível de erro tolerável no início do ciclo durante a fase **Formulação do Problema** .

### <a name="typical-evaluation-techniques"></a>Técnicas de Avaliação Típicas
Há várias maneiras nas quais o erro de previsão pode ser medido e quantificado. Nesta seção, nos concentraremos na discussão sobre as técnicas de avaliação relevantes à série temporal e, especificamente, para a previsão da demanda de energia.

#### <a name="mapehttpsenwikipediaorgwikimeanabsolutepercentageerror"></a>[**MAPE**](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
MAPE significa Erro Médio de Porcentagem Absoluta. Com o MAPE, estamos calculando a diferença entre cada ponto previsto e o valor real desse ponto. Em seguida, quantificamos o erro por ponto ao calcularmos a proporção da diferença em relação ao valor real. Na última etapa, fazemos a média desses valores. A fórmula matemática usada para o MAPE é a seguinte:

![Fórmula MAPE](media/cortana-analytics-playbook-demand-forecasting-energy/mape-formula.png)
*Onde A<sub>t</sub> é o valor real, F<sub>t</sub> é o valor previsto e n é o horizonte de previsão.*

## <a name="deployment"></a>Implantação
Depois de concluirmos a fase de modelagem e de validarmos o desempenho do modelo, estaremos prontos para a fase de implantação. Nesse contexto, implantar significa permitir que o cliente consuma o modelo ao executar previsões reais nele em larga escala. O conceito de implantação é fundamental no AM do Azure, já que nossa meta principal é invocar constantemente as previsões, em vez de apenas obter as informações dos dados. A fase de implantação é a parte onde permitimos que o modelo seja consumido em larga escala.

No contexto da previsão da demanda de energia, nosso objetivo é invocar previsões periódicas e contínuas, garantindo que os dados atualizados estejam disponíveis para o modelo e que os dados previstos sejam enviados para o cliente consumidor.

### <a name="web-services-deployment"></a>Implantação de Serviços Web
O principal bloco de construção implantável no AM do Azure é o serviço Web. Essa é a maneira mais eficiente de habilitar o consumo de um modelo preditivo na nuvem. O serviço Web encapsula o modelo e o encerra com uma API (Application Programming Interface, Interface de Programação de Aplicativo) [RESTful](http://www.restapitutorial.com/) . A API pode ser usada como parte de qualquer código de cliente, como ilustrado no diagrama a seguir.

![Implantação e Consumo do Serviço Web](media/cortana-analytics-playbook-demand-forecasting-energy/web-service-deployment-and-consumption.png)

Como se pode ver, o serviço Web é implantado na nuvem do Cortana Intelligence Suite e pode ser invocado em seu ponto de extremidade de API REST exposto. Tipos diferentes de clientes em vários domínios podem invocar o serviço por meio da API Web de forma simultânea. O serviço Web também pode ser dimensionado para dar suporte a milhares de chamadas simultâneas.

### <a name="a-typical-solution-architecture"></a>Uma Arquitetura de Solução Típica
Ao implantarmos uma solução de previsão de demanda de energia, estamos interessados na implantação de uma solução completa que vá além do serviço Web de previsão e que facilite todo o fluxo de dados. No momento que invocamos uma nova previsão, precisamos garantir que o modelo seja alimentado com os recursos de dados atualizados. Isso implica em que os dados brutos recém-coletados sejam constantemente ingeridos, processados e transformados no conjunto de recursos necessário no qual o modelo foi criado. Ao mesmo tempo, gostaríamos de disponibilizar os dados previstos para os clientes consumidores finais. Um exemplo de ciclo de fluxo de dados (ou de pipeline de dados) é ilustrado no diagrama a seguir:

![Fluxo de Dados Completo de Previsão da Demanda de Energia](media/cortana-analytics-playbook-demand-forecasting-energy/energy-demand-forecase-end-data-flow.png)

Estas são as etapas que ocorrem como parte do ciclo de previsão da demanda de energia:

1. Milhões de medidores de dados implantados estão constantemente gerando dados de consumo de energia em tempo real.
2. Esses dados estão sendo coletados e carregados em um repositório da nuvem (*por exemplo*, o Blob do Azure).
3. Antes de serem processados, os dados brutos são agregados no nível da subestação ou no nível regional, conforme definido pela empresa.
4. O processamento de recursos (confira **Preparação de Dados e Processamento de Recursos**) ocorre e produz os dados necessários para o treinamento ou para a pontuação do modelo. Os dados do conjunto de recursos são armazenados em um banco de dados (*por exemplo*, o SQL Azure).
5. O serviço de readaptação é invocado para readaptar o modelo de previsão. A versão atualizada do modelo é mantida para que ele possa ser usado pelo serviço Web de pontuação.
6. O serviço Web de pontuação é invocado em uma agenda que se ajuste à frequência de previsão necessária.
7. Os dados previstos são armazenados em um banco de dados que pode ser acessado pelo cliente consumidor final.
8. O cliente consumidor recupera as previsões, as aplica novamente na rede e as consome de acordo com o caso de uso exigido.

É importante observar que todo esse ciclo é totalmente automatizado e executado em uma agenda. Toda a orquestração desse ciclo de dados pode ser concluída usando ferramentas como o [Data Factory do Azure](http://azure.microsoft.com/services/data-factory/).

### <a name="end-to-end-deployment-architecture"></a>Arquitetura de Implantação Completa
Para implantar de maneira prática uma solução de previsão de demanda de energia no Cortana Intelligence, precisamos garantir que os componentes necessários sejam estabelecidos e configurados corretamente.

O diagrama a seguir ilustra uma arquitetura típica do Cortana Intelligence que implementa e gerencia o ciclo de fluxo de dados descrito acima:

![Arquitetura de Implantação Completa](media/cortana-analytics-playbook-demand-forecasting-energy/architecture.png)

Para saber mais sobre cada um dos componentes e sobre a arquitetura inteira, veja o Modelo de Solução de Energia.

