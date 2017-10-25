---
title: "Manutenção preditiva no setor aeroespacial com o Azure - Modelo de solução de Cortana Intelligence | Microsoft Docs"
description: "Modelo de Solução com o Microsoft Cortana Intelligence para a manutenção preditiva no setor aeroespacial, de serviços públicos e de transporte."
services: cortana-analytics
documentationcenter: 
author: fboylu
manager: jhubbard
editor: cgronlun
ms.assetid: 2e8b66db-91eb-432b-b305-6abccca25620
ms.service: cortana-analytics
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: fboylu
ms.openlocfilehash: aafa395f8c0593d9597f74cd5cd2a41f26897c6f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="cortana-intelligence-solution-template-playbook-for-predictive-maintenance-in-aerospace-and-other-businesses"></a>Guia Estratégico do Modelo de Solução do Cortana Intelligence para a manutenção preditiva no setor aeroespacial e outras indústrias.
## <a name="executive-summary"></a>Resumo executivo
A manutenção preditiva é um dos usos mais exigidos de análise preditiva, com vantagens indiscutíveis, além de uma grande economia de custos. Este manual tem como objetivo fornecer uma referência para soluções de manutenção preditiva com ênfase em casos de uso importantes.
Ele está preparado para fornecer ao leitor um entendimento dos cenários mais comuns de manutenção preditiva, dos desafios de qualificar os problemas de negócios para essas soluções, dos dados necessários para resolver esses problemas de negócios, das técnicas de modelagem preditiva para criar soluções que usam esses dados e práticas recomendadas com arquiteturas de solução de exemplo.
Ele também descreve as especificidades dos modelos preditivos desenvolvidos, como engenharia de recursos, desenvolvimento de modelo e avaliação de desempenho. Basicamente, esse manual reúne as diretrizes de negócios e analíticas necessárias para o desenvolvimento e a implantação bem-sucedidos de soluções de manutenção preditiva. Essas diretrizes são preparadas para ajudar o público a criar uma solução inicial usando o Cortana Intelligence e, especificamente, o Azure Machine Learning como um ponto de partida em sua estratégia de manutenção preditiva de longo prazo. A documentação sobre o Cortana Intelligence Suite e o Azure Machine Learning pode ser encontrada nas páginas [Cortana Analytics](http://www.microsoft.com/server-cloud/cortana-analytics-suite/overview.aspx) e [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/).

> [!TIP]
> Para obter um guia técnico para implementar esse Modelo de Solução, confira o [Guia técnico para o Modelo de Solução do Cortana Intelligence para manutenção preditiva](cortana-analytics-technical-guide-predictive-maintenance.md).
> Para baixar um diagrama que fornece uma visão geral da arquitetura desse modelo, confira a [Arquitetura do Modelo de Solução do Cortana Intelligence para manutenção preditiva](cortana-analytics-architecture-predictive-maintenance.md).
> 
> 

## <a name="playbook-overview-and-target-audience"></a>Visão geral do guia estratégico e público-alvo
Este manual é organizado para auxiliar tanto o público-alvo técnico quanto o não técnico, com diferentes graus de experiência e interesse em manutenção preditiva. O manual aborda os aspectos de alto níveis de diferentes tipos de soluções de manutenção preditiva e detalhes sobre como implementá-las. O conteúdo é balanceado para atender tanto ao público que só está interessado em entender a solução e o tipo de aplicativo quanto aqueles que estão buscando implementar essas soluções e interessados nos detalhes técnicos.

A maior parte do conteúdo deste manual não pressupõe conhecimento de ciência de dados anterior ou experiência. No entanto, algumas partes do playbook exigirão um pouco de familiaridade com conceitos de ciência de dados para ser capaz de acompanhar os detalhes da implantação. São necessárias habilidades de ciência de dados em nível introdutório para o aproveitamento total do material nestas seções.

A primeira metade do playbook trata da introdução a aplicativos de manutenção preditiva, de como qualificar uma solução de manutenção preditiva, uma coleção de casos de uso comum com os detalhes do problema de negócios, os dados acerca desses casos de uso e os benefícios comerciais de implementar essas soluções de manutenção preditiva. Estas seções não exigem qualquer conhecimento técnico do domínio de análise preditiva.

Na segunda parte do playbook, tratamos dos tipos de técnicas de modelagem preditiva para manutenção preditiva e de como implantar esses modelos com exemplos dos casos de uso mencionados na primeira parte. Isso é ilustrado por meio de etapas de pré-processamento de dados, como rotulamento de dados e engenharia de recursos, seleção de modelo, treinamento/teste e melhores práticas de avaliação de desempenho. Essas seções são adequadas para um público-alvo técnico.

## <a name="predictive-maintenance-in-iot"></a>Manutenção preditiva no IoT
O impacto do tempo de inatividade não agendado de um equipamento pode ser extremamente nocivo para os negócios. É essencial manter os equipamentos de campo em execução para maximizar o uso e o desempenho e minimizar o tempo de inatividade não agendado e dispendioso. Aguardar a falha ocorrer simplesmente não é viável no panorama atual de operações de negócios. Para permanecerem competitivas, as empresas buscam novas formas de maximizar o desempenho de ativos usando dados coletados de vários canais. Uma maneira relevante de analisar essas informações é utilizar técnicas de análise preditivas que usam padrões históricos para prever resultados futuros. Uma das soluções mais populares entre essas é chamada de manutenção preditiva, que geralmente pode ser definida, dentre outras, como a possibilidade de falha de um ativo em um futuro próximo para que os ativos possam ser monitorados, identifiquem falhas de forma proativa e tomem providências antes que ocorram falhas. Essas soluções detectam padrões de falha para determinar os ativos que correm mais risco de falhar. Essa identificação antecipada de problemas ajuda a implantar recursos de manutenção limitados de maneira mais econômica e a melhorar a qualidade e os processos da cadeia de fornecimento.

Com o aumento dos aplicativos da Internet das coisas (IoT), a manutenção preditiva tem ganhando atenção cada vez maior do setor, já que a coleta de dados e o processamento de tecnologias evoluíram o suficiente para gerar, transmitir, armazenar e analisar todos os tipos de dados em lotes ou em tempo real. Essas tecnologias permitem o fácil desenvolvimento e implantação de soluções de ponta a ponta com soluções de análise avançada, sendo que as soluções de manutenção preditiva asseguram sem dúvida o maior benefício.

Os problemas de negócios no domínio da manutenção preditiva variam entre riscos operacionais altos devido a falhas inesperadas e percepção limitada da causa dos problemas em ambientes de negócios complexos. A maioria desses problemas pode ser categorizada nas seguintes perguntas de negócios:

* Qual é a probabilidade de um equipamento falhar em um futuro próximo?
* Qual é o tempo de vida restante do equipamento?
* Quais são as causas de falhas e quais ações de manutenção devem ser executadas para corrigir esses problemas?

Utilizando a manutenção preditiva para responder a essas perguntas, as empresas podem:

* Reduzir o risco operacional e aumentar a taxa de retorno de ativos, identificando as falhas antes que elas ocorram
* Reduzir operações de manutenção desnecessárias baseadas em tempo e controlar o custo de manutenção
* Melhorar a imagem geral da marca, eliminar a publicidade negativa e a perda de vendas resultante de desgaste dos clientes.
* Diminuir custos de inventário, reduzindo os níveis de estoque com a previsão do ponto de novo pedido
* Descobrir padrões conectados a vários problemas de manutenção

As soluções de manutenção preditiva podem permitir que as empresas tenham indicadores-chave de desempenho, como pontuações de integridade para monitorar a condição de um ativo em tempo real, estimativa do tempo de vida restante dos ativos, recomendação para atividades de manutenção proativa e datas estimadas de pedido de substituição de peças.

## <a name="qualification-criteria-for-predictive-maintenance"></a>Critérios de qualificação para a manutenção preditiva
É importante enfatizar que nem todos os casos de uso ou problemas de negócios podem ser efetivamente resolvidos pela manutenção preditiva. Importantes critérios de qualificação incluem se o problema é preditivo por natureza, se existe um caminho claro de ação para evitar falhas quando elas são detectadas com antecedência e, mais importante, se há dados com qualidade suficiente disponíveis para dar suporte ao caso de uso. Aqui, vamos focar nos requisitos de dados para criar uma solução bem-sucedida de manutenção preditiva.

Ao criar modelos preditivos, usamos os dados históricos para treinar o modelo que poderá reconhecer padrões ocultos e identificará esses padrões em dados futuros. Esses modelos são treinados com exemplos descritos por seus recursos e pelo destino da previsão. O modelo treinado deve fazer previsões sobre o alvo apenas observando as propriedades dos novos exemplos. É crucial que o modelo capture a relação entre os recursos e o alvo da previsão. Para treinar um modelo de aprendizado de máquina eficaz, precisamos de dados de treinamento que incluam recursos que realmente tenham capacidade preditiva quanto ao destino da previsão, significando que os dados devem ser relevantes para o alvo da previsão a fim de possibilitar previsões precisas.

Por exemplo, se o destino é prever falhas nas rodas dos vagões, os dados de treinamento devem conter recursos relativos às rodas (por exemplo, telemetria que reflita o status de integridade das rodas, a quilometragem, a carga etc.). No entanto, se o objetivo for prever falhas em motores de trens, provavelmente precisaremos de outro conjunto de dados de treinamento que tenha recursos relacionados ao motor. Antes de criar modelos preditivos, esperamos que o especialista de negócios compreenda o requisito de relevância de dados e forneça o conhecimento de domínio necessário para selecionar subconjuntos de dados relevantes para a análise.

Buscamos três fontes de dados essenciais para definir se um problema de negócios se qualifica para uma solução de manutenção preditiva:

1. Histórico de falha: normalmente, em usos de manutenção preditiva, os eventos de falha são muito raros. No entanto, ao criar modelos preditivos que vão prever falhas, o algoritmo precisa aprender o padrão de operação normal e o padrão de falha durante o processo de treinamento. Portanto, é essencial que os dados de treinamento contenham um número suficiente de exemplos em ambas as categorias para aprender esses dois padrões diferentes. Por esse motivo, é necessário que os dados tenham um número suficiente de eventos de falha. Os eventos de falha podem ser encontrados nos registros de manutenção, e o histórico de substituição de peças ou anomalias nos dados de treinamento também podem ser usados como falhas conforme identificado pelos especialistas do domínio.
2. Histórico de manutenção/reparo: uma fonte de dados essencial para soluções de manutenção preditiva é o histórico detalhado de manutenção do ativo que contém informações sobre os componentes substituídos, as manutenções preventivas realizadas, etc. É extremamente importante capturar esses eventos, já que eles afetam os padrões de degradação, e a ausência de informações levará a resultados incorretos.
3. Condições do computador: para prever quantos dias a mais (horas, milhas, transações etc.) uma máquina durará antes de falhar, supomos que o status de integridade do computador irá piorar com o tempo durante sua operação. Portanto, esperamos que os dados contenham recursos com variação de tempo que capturem esse padrão de vencimento e anomalias que levam à degradação. No uso de IoT, os dados de telemetria dos diferentes sensores representam um bom exemplo. Para prever se um computador irá falhar em um período de tempo, os dados deveriam capturar tendência de degradação durante esse período de tempo antes do evento de falha real.

Além disso, exigimos dados que estejam diretamente relacionados às condições operacionais do ativo que é o alvo de previsão. A decisão do alvo baseia-se tanto na disponibilidade de dados quanto nas necessidades de negócios. Usando a previsão de falha da roda do vagão como exemplo, podemos prever "se a roda terá uma falha" ou "se o vagão inteiro terá uma falha". A primeira enfoca mais um componente específico, ao passo que a segunda enfoca a falha no vagão. A segunda é uma pergunta mais geral que exige muito mais elementos de dados dispersos que a primeira, tornando mais difícil a criação de um modelo. Por outro lado, tentar prever falhas de roda apenas observando os dados de alto nível sobre a condição do vagão pode não ser viável, já que não contém informações no nível do componente. Em geral, é mais adequado prever eventos de falha específicos do que os gerais.

Uma pergunta comum normalmente feita sobre dados históricos de falha é "quantos eventos de falha são necessários para treinar um modelo e quantos são considerados “suficientes"? Não há uma resposta clara para essa pergunta, já que em vários cenários de análise preditiva, normalmente é a qualidade dos dados que dita o que é aceitável. Se o conjunto de dados não inclui recursos que são relevantes à previsão da falha, mesmo que haja muitos eventos de falha, a criação de um bom modelo pode não ser possível. No entanto, a regra geral é quanto mais eventos de falha, melhor será o modelo, e uma estimativa de quantos exemplos de falha são necessários é uma medida que depende bastante do contexto e dos dados. Esse problema será abordado na seção que ensina a lidar com conjuntos de dados desequilibrados, em que propomos métodos para lidar com o problema de não se ter falhas suficientes.

## <a name="sample-use-cases"></a>Casos de uso de exemplo
Esta seção se concentra em um conjunto de casos de uso de manutenção preditiva de vários setores, como Aeroespacial, Utilidades e Transporte. Cada subseção detalha os casos de uso coletados dessas áreas e discute o problema de negócios, os dados sobre o problema de negócios e as vantagens de uma solução de manutenção preditiva.

### <a name="aerospace"></a>Aeroespacial
#### <a name="use-case-1-flight-delay-and-cancellations"></a>Caso de uso 1: atraso de voo e cancelamentos
##### <a name="business-problem-and-data-sources"></a>*Problema de negócios e fontes de dados*
Um dos grandes problemas de negócios que as companhias aéreas enfrentam é o custo significativo associado a atrasos nos voos devido a problemas mecânicos. Se as falhas mecânicas não puderem ser reparadas, os voos ainda podem ser cancelados. Isso é extremamente dispendioso, já que atrasos podem criar problemas de planejamento e de operações, criam uma má reputação e levam à insatisfação do cliente, além de muitos outros problemas. As companhias aéreas tem interesse especial em prever essas falhas mecânicas com antecedência para que possam reduzir os atrasos ou cancelamentos dos voos. O objetivo da solução de manutenção preditiva para esses casos é prever a probabilidade de atrasos ou cancelamento de voos com base em fontes de dados relevantes, como o histórico de manutenção e as informações de rota de voo. As duas principais fontes de dados para esse caso de uso são os trechos de voo e os logs de página. Os dados sobre trechos de voo incluem dados sobre os detalhes de rota do voo, como a data e a hora de pouso e decolagem, aeroportos de partida e chegada, etc. Os dados de log de página incluem uma série de códigos de erro e de manutenção que são registrados pela equipe de manutenção.

##### <a name="business-value-of-the-predictive-model"></a>*Valor comercial do modelo preditivo*
Usando os dados históricos disponíveis, um modelo preditivo foi criado usando um algoritmo de várias classificações para prever o tipo de problema mecânico que resultará em um atraso ou cancelamento de um voo nas próximas 24 horas. Fazendo essa previsão, as ações de manutenção necessárias podem ser realizadas para reduzir o risco durante os reparos a uma aeronave, evitando possíveis atrasos ou cancelamentos. Usando o serviço Web do Azure Machine Learning, os modelos preditivos podem ser integrados perfeita e facilmente às plataformas operacionais existentes das companhias aéreas. 

#### <a name="use-case-2-aircraft-component-failure"></a>Caso de uso 2: falha de componente de aeronave
##### <a name="business-problem-and-data-sources"></a>*Problema de negócios e fontes de dados*
Os motores de aeronave são equipamentos muito sensíveis e caros, e a substituição de peças do motor está entre as tarefas de manutenção mais comuns no setor aéreo. As soluções de manutenção para companhias aéreas exigem o gerenciamento cuidadoso de disponibilidade, entrega e planejamento de estoque dos componentes. A capacidade de reunir inteligência sobre confiabilidade do componente leva a uma significativa redução nos custos de investimento. A fonte de dados principal desse caso de uso é os dados de telemetria coletados de uma quantidade de sensores na aeronave que fornecem informações sobre as condições dela. Registros de manutenção também foram usados para identificar quando ocorreram falhas de componente e substituições.

##### <a name="business-value-of-the-predictive-model"></a>Valor comercial do modelo preditivo
Foi criado um modelo de classificação multiclasse que prevê a probabilidade de uma falha devido a um determinado componente no próximo mês. Empregando essas soluções, as companhias aéreas podem reduzir os custos de reparo do componente, aprimorar a disponibilidade de estoque do componente, reduzir os níveis de estoque de ativos relacionados e melhorar o planejamento de manutenção.

### <a name="utilities"></a>Utilidades
#### <a name="use-case-1-atm-cash-dispense-failure"></a>Caso de uso 1: falha do caixa eletrônico
##### <a name="business-problem-and-data-sources"></a>*Problema de negócios e fontes de dados*
Executivos em setores com uso intensivo de ativos geralmente dizem que o principal risco operacional de seus negócios é falha inesperada dos ativos. Por exemplo, falha de máquinas, como caixas eletrônicos no setor bancário, é um problema muito comum que ocorre com frequência. Esses tipos de problemas tornam as soluções de manutenção preditiva muito interessantes para os operadores de tais máquinas. Nesse caso de uso, o problema de previsão é calcular a probabilidade de uma transação de saque no caixa eletrônico ser interrompida devido a uma falha na saída do dinheiro, como papel preso ou falha de alguma peça. As principais fontes de dados para esse caso são leituras de sensor que coletam medidas durante a liberação das notas e também os registros de manutenção coletados ao longo do tempo. Os dados de sensor incluíam as leituras do sensor de cada transação concluída e também as leituras do sensor para cada nota liberada. As leituras de sensor forneceram medidas como a folga entre as notas, a espessura, a distância de chegada da nota, etc. Os dados de manutenção incluíam códigos de erro e informações de reparo. Eles foram usados para identificar casos de falha.

##### <a name="business-value-of-the-predictive-model"></a>*Valor comercial do modelo preditivo*
Dois modelos preditivos foram criados para prever falhas nas transações de saque de dinheiro e nas notas individuais liberadas durante uma transação. Ao ser capaz de prever falhas de transação com antecedência, os caixas eletrônicos podem ser mantidos proativamente para impedir a ocorrência de falhas. Além disso, com a previsão de falha de nota, se uma transação tiver probabilidade de falha antes da conclusão devido a uma falha de geração de nota, pode ser melhor parar o processo e avisar ao cliente que a transação ficou incompleta em vez de aguardar a chegada do serviço de manutenção após a ocorrência do erro, o que pode levar a uma maior insatisfação do cliente.

#### <a name="use-case-2-wind-turbine-failures"></a>Caso de uso 2: falhas de turbinas eólicas
##### <a name="business-problem-and-data-sources"></a>*Problema de negócios e fontes de dados*
Com o aumento da consciência ambiental, as turbinas eólicas tornaram-se uma das principais fontes de geração de energia e normalmente custam milhões de dólares. Um dos principais componentes das turbinas eólicas é o motor do gerador, que é equipado com vários sensores que ajudam a monitorar o status e as condições da turbina. As leituras de sensor contêm informações importantes que podem ser usadas para criar um modelo preditivo para prever KPIs (indicadores-chave de desempenho), como tempo médio de falha para os componentes da turbina eólica. Os dados deste caso de uso vêm de várias turbinas eólicas situadas em três fazendas diferentes. Medidas de aproximadamente uma centena de sensores em cada turbina foram registradas por um ano a cada dez segundos. Essas leituras incluem medidas como temperatura, velocidade de gerador, potência da turbina e rotação do gerador.

##### <a name="business-value-of-the-predictive-model"></a>*Valor comercial do modelo preditivo*
Os modelos preditivos foram criados para estimar o tempo de vida restante para geradores e sensores de temperatura. Ao prever a probabilidade de falha, os técnicos de manutenção podem se concentrar em turbinas com suspeita de falha em breve e complementar os regimes de manutenção baseada em tempo.
Além disso, os modelos preditivos trazem percepções para o nível de contribuição de diferentes fatores para a probabilidade de uma falha que ajuda empresas a entender melhor a causa dos problemas.

#### <a name="use-case-3-circuit-breaker-failures"></a>Caso de uso 3: falhas de disjuntor
##### <a name="business-problem-and-data-sources"></a>*Problema de negócios e fontes de dados*
As operações de luz e gás que incluem a geração, a distribuição e a venda de energia elétrica exigem uma quantidade significativa de manutenção para garantir que as linhas de energia estejam funcionando o tempo todo para garantir o fornecimento de energia nas residências. A falha dessas operações é essencial, já que quase todas as entidades são afetadas por problemas de energia nas regiões em que ocorrem. Os disjuntores são essenciais para essas operações, já que são uma peça do equipamento que interrompe a corrente elétrica em caso de problemas e curto-circuitos para prevenir danos às linhas de energia. O problema de negócios desse caso de uso é prever falhas no disjuntor com logs de manutenção, histórico de comandos e especificações técnicas.

As três principais fontes de dados para esse caso são logs de manutenção que incluem ações corretivas, preventivas e sistemáticas, dados operacionais que incluem comandos automáticos e manuais enviados a disjuntores, como para ações abertas e fechadas, e dados de especificação técnica sobre as propriedades de cada disjuntor, como ano de fabricação, localização, modelo, etc.

##### <a name="business-value-of-the-predictive-model"></a>*Valor comercial do modelo preditivo*
As soluções de manutenção preditiva ajudam a reduzir os custos de reparo e a aumentar o ciclo de vida de equipamentos como disjuntores. Esses modelos também ajudam a melhorar a qualidade da rede de energia, já que os modelos dão indicações antes do tempo, o que irá reduzir a quantidade de falhas inesperadas e levar a menos interrupções ao serviço.

#### <a name="use-case-4-elevator-door-failures"></a>Caso de uso 4: falhas de porta de elevador
##### <a name="business-problem-and-data-sources"></a>*Problema de negócios e fontes de dados*
A maioria das grandes empresas de elevador normalmente têm milhões de elevadores em funcionamento no mundo todo. Para obter uma vantagem competitiva, eles se concentram na confiabilidade, que é o que mais importa para seus clientes. Com a ajuda do potencial da Internet das Coisas, ao conectar seus elevadores à nuvem e coletar dados de sensores e sistemas do elevador, eles podem transformar dados em business intelligence valioso, o que aprimora as operações com a oferta de manutenção preditiva e preemptiva que ainda não é disponibilizada pela concorrência. O requisito de negócios para esse caso é fornecer um aplicativo preditivo da base de conhecimento que irá prever as possíveis causas de falhas de porta. Os dados necessários para essa implementação consistem em três partes, que são recursos estáticos do elevador (por exemplo, identificadores, frequência de manutenção do contrato, tipo de construção, etc.), informações de uso (por exemplo, número de ciclos de porta, tempo médio de fechamento das portas, etc.) e histórico de falha (ou seja, registros do histórico de falha e suas causas).

Um modelo de regressão logística de multiclasse foi criado no Azure Machine Learning para solucionar o problema de previsão, com recursos estáticos integrados e dados de uso como recursos, e as causas dos registros históricos de falha como rótulos de classe. Esse modelo de previsão é consumido por um aplicativo em um dispositivo móvel usado por técnicos de campo para ajudar a melhorar a eficiência do trabalho. Quando um técnico vai ao local reparar um elevador, ele pode consultar esse aplicativo para obter causas recomendadas e melhores ações de manutenção para corrigir as portas do elevador o mais rapidamente possível.

### <a name="transportation-and-logistics"></a>Transporte e logística
#### <a name="use-case-1-brake-disc-failures"></a>Caso de Uso 1: falhas do disco de freio
##### <a name="business-problem-and-data-sources"></a>*Problema de negócios e fontes de dados*
As políticas de manutenção típicas para veículos incluem manutenção corretiva e preventiva. A manutenção corretiva implica que o veículo é reparado após uma falha que poderia causar uma grave inconveniência para o motorista como resultado de uma falha inesperada e do tempo gasto em uma visita à oficina. A maioria dos veículos também está sujeita a uma política de manutenção preventiva, que requer a execução de determinadas inspeções em uma agenda que não leva em conta a condição real dos subsistemas do carro. Nenhuma dessas abordagens é bem-sucedida na eliminação total dos problemas. O caso de uso específico aqui é a previsão de falha do disco de freio baseada nos dados coletados por meio de sensores instalados no sistema de pneus de um carro que controla os padrões de condução históricos e outras condições às quais o carro está exposto. A fonte de dados mais importante para esse caso são os dados do sensor que medem, por exemplo, as acelerações, padrões de frenagem, distâncias de deslocamento, velocidade etc. Essas informações, juntamente com outras informações estáticas como recursos de carro, ajudam a criar um bom conjunto de previsores que podem ser usados em um modelo preditivo. Outro conjunto de informações essenciais é os dados de falha que são inferidos do banco de dados de pedido da peça (usado para manter as datas e quantidades das peças de reposição durante o cuidado com os carros nas concessionárias).

##### <a name="business-value-of-the-predictive-model"></a>*Valor comercial do modelo preditivo*
O valor comercial de uma abordagem preditiva aqui é significativo. Um sistema de manutenção preditiva pode agendar uma visita ao revendedor com base em um modelo preditivo. O modelo pode se basear nas informações de sensores que estão representando a condição atual do carro e no histórico do carro. Essa abordagem pode minimizar o risco de falhas inesperadas, que também podem ocorrer antes da próxima manutenção periódica.
Ela também pode reduzir a quantidade de manutenção preventiva desnecessária. O motorista pode ser proativamente informado de que é necessário mudar algumas peças em poucas semanas e repassar a informação ao revendedor. O revendedor poderia preparar um pacote de manutenção individual para o motorista com antecedência.

#### <a name="use-case-2-subway-train-door-failures"></a>Caso de uso 2: falhas de porta do vagão do metrô
##### <a name="business-problem-and-data-sources"></a>*Problema de negócios e fontes de dados*
Um dos principais motivos de atrasos e problemas nas operações de metrô é a falha nas portas dos vagões. Prever se um vagão pode ter falha nas portas, ou ser capaz de prever a quantidade de dias até a próxima falha nas portas, é uma antecipação importante. Isso dá a oportunidade de otimizar a manutenção das portas dos vagões e de reduzir o tempo de inatividade da composição.

#### <a name="data-sources"></a>Fontes de dados
São três fontes de dados neste caso de uso 

* **dados de evento de treinamento**, que são os registros históricos de eventos de treinamento, 
* **dados de manutenção** , como tipos de manutenção, tipos de ordem de trabalho e códigos de prioridade,  
* **registros de falhas**.

##### <a name="business-value-of-the-predictive-model"></a>*Valor comercial do modelo preditivo*
Dois modelos foram criados para prever a probabilidade de falha no dia seguinte usando classificação binária, e os dias até a falha usando regressão Semelhante aos casos anteriores, os modelos criam uma enorme oportunidade de melhorar a qualidade do serviço e aumentar a satisfação do cliente complementando os regimes de manutenção regular.

## <a name="data-preparation"></a>Preparação dos dados
### <a name="data-sources"></a>Fontes de dados
Os elementos de dados comuns para problemas de manutenção preditiva podem ser resumidos da seguinte forma:

* Histórico de falhas: o histórico de falhas de uma máquina ou de um componente dentro da máquina.
* Histórico de manutenção: o histórico de reparo de uma máquina; por exemplo, códigos de erro, atividades de manutenção anteriores ou substituições de componentes.
* Condições e uso da máquina: as condições operacionais de uma máquina; por exemplo, dados coletados dos sensores.
* Recursos da máquina: os recursos de uma máquina, por exemplo, tamanho do motor, marca e modelo, local.
* Recursos do operador: os recursos do operador; por exemplo, sexo, experiência anterior.

É possível e normalmente é fato que o histórico de falha está contido no histórico de manutenção, na forma de códigos de erro especiais ou datas de pedido de peças de reposição. Nesses casos, as falhas podem ser extraídas dos dados de manutenção. Além disso, os domínios de negócios diferentes podem ter uma variedade de outras fontes de dados que influenciam os padrões de falha que não estão listados aqui exaustivamente. Eles devem ser identificados consultando-se os especialistas de domínio ao criar modelos preditivos.

Alguns exemplos dos elementos de dados acima vindos de casos de uso são:

Histórico de falhas: datas de atraso do voo, datas e tipos de falha do componente da aeronave, falhas de transação de saque em caixa eletrônico, falhas da porta do vagão/elevador, datas de pedido de substituição do disco de freio, datas de falha da turbina eólica e falhas de comando do disjuntor.

Histórico de manutenção: logs de erros de voo, logs de erros de transação no caixa eletrônico, registros de manutenção das composições, incluindo o tipo de manutenção, breve descrição, etc. e registros de manutenção do disjuntor.

Condições e uso de computador: rotas e horários de voo, dados de sensor coletados dos motores das aeronaves, leituras de sensor de transações de caixa eletrônico, dados de eventos com vagões, leituras dos sensores das turbinas eólicas, elevadores e carros conectados.

Recursos de máquina: especificações técnicas do disjuntor, como níveis de tensão, recursos de localização geográfica, ou propriedades de carro, como modelo, marca, tamanho do motor, tipos de pneu, instalações de produção, etc.

Considerando as fontes de dados acima, os dois tipos de dados principais que observamos no domínio de manutenção preditiva são dados temporais e dados estáticos.
Histórico de falha, condições da máquina, histórico de reparos e histórico de uso quase sempre são fornecidos com carimbos de data/hora que indicam a hora da coleta de cada dado. As propriedades da máquina e do operador em geral são estáticos, pois geralmente descrevem as especificações técnicas de máquinas ou propriedades do operador. É possível que essas propriedades mudem ao longo do tempo e, se for o caso, elas devem ser tratadas como fontes de dados com carimbo de data/hora.

### <a name="merging-data-sources"></a>Mesclagem das fontes de dados
Antes de entrar em qualquer tipo de processo de engenharia de recursos ou de rotulação, precisamos primeiro preparar os dados no formato necessário para criar recursos. O objetivo final é gerar um registro para cada unidade de tempo para cada ativo, e as propriedades e rótulos devem ser fornecidos ao algoritmo do aprendizado de máquina. Para preparar o conjunto final de dados limpo, algumas etapas de pré-processamento devem ser realizadas. A primeira etapa é dividir a duração da coleta de dados em unidades de tempo, em que cada registro pertence a uma unidade de tempo para um ativo. A coleta de dados também pode ser dividida em outras unidades, como ações; no entanto, para simplificar, usaremos unidade de tempo no resto das explicações.

A unidade de medida de tempo pode ser segundos, minutos, horas, dias, meses, ciclos, milhas ou transações, dependendo da eficiência da preparação de dados e das alterações observadas nas condições do ativo de uma unidade de tempo para outra ou outros fatores específicos do domínio. Em outras palavras, a unidade de tempo não precisa ser a mesma da frequência da coleta de dados, já que em muitos casos os dados podem não mostrar diferença de uma unidade para outra. Por exemplo, se os valores de temperatura tiverem sido sendo coletados a cada 10 segundos, a escolha de uma unidade de tempo de 10 segundos para toda a análise irá aumentar o número de exemplos sem fornecer informações adicionais. Uma melhor estratégia seria usar a média de uma hora como um exemplo.

Esquemas de dados genéricos de exemplo para as fontes de dados possíveis explicadas na seção anterior são:

Registros de manutenção: esses são os registros de ações de manutenção executadas. Os dados brutos de manutenção geralmente são fornecidos com uma ID do ativo e o carimbo de data/hora com informações sobre as atividades de manutenção que foram executadas naquele momento. No caso de tais dados brutos, as atividades de manutenção precisam ser convertidas em colunas categóricas, com cada categoria correspondendo a um tipo de ação de manutenção. O esquema de dados básico para registros de manutenção incluiria colunas de ID de ativo, hora e ação de manutenção.

Registros de falha: esses são os registros que pertencem ao alvo de previsão, que chamamos de falhas ou motivo da falha. Eles podem ser códigos de erro ou eventos de falha específicos definidos por condições de negócios específicas. Em alguns casos, os dados incluem vários códigos de erro, alguns dos quais correspondem a falhas de interesse. Nem todos os erros são alvo de previsão, então outros erros são normalmente usados para construir os recursos que podem se correlacionar com as falhas. O esquema de dados básico para registros de falha incluiria as colunas de ID do ativo, hora e falha ou motivo da falha, se o motivo estiver disponível.

Condições de máquina: são preferencialmente dados de monitoração em tempo real sobre as condições operacionais dos dados. Por exemplo, para falhas de porta, as horas de abertura e fechamento das portas são bons indicadores sobre a condição atual das portas. O esquema de dados básico para as condições de máquina incluiriam colunas de ID de ativo, hora e valor de condição.

Dados de máquina e operador: dados de máquina e operador podem ser mesclados em um esquema para identificar qual ativo foi operado por qual operador, junto com as propriedades de ativo e do operador. Por exemplo, um carro normalmente pertence a um motorista com atributos como idade, experiência de direção, etc. Se esses dados forem alterados ao longo do tempo, eles também devem incluir uma coluna de hora e devem ser tratados como dados com variação de tempo para a geração de recursos. O esquema de dados básico para as condições de máquina incluiriam colunas de ID de ativo, propriedades do ativo, ID do operador e propriedades do operador.

A tabela final antes da rotulação e da geração de recursos pode ser gerada unindo a tabela de condições da máquina com os registros de falha nos campos ID do ativo e hora. Essa tabela pode então ser unida aos registros de manutenção nos campos ID do ativo e Hora e, por fim, com as propriedades de máquina e operador no ID do ativo. A primeira união à esquerda deixa valores nulos na coluna de falhas quando a máquina estiver operando normalmente; eles podem ser imputados com um valor de indicador para operação normal. Esta coluna de falha é usada para criar rótulos para o modelo de previsão.

### <a name="feature-engineering"></a>Engenharia de recursos
A primeira etapa na modelagem é a engenharia de recursos. A ideia da geração de recursos é descrever conceitualmente e abstrair uma condição de integridade da máquina em dado momento usando dados históricos coletados até aquele momento. Na próxima seção, fornecemos uma visão geral do tipo de técnicas que pode ser usada para a manutenção preditiva e como o rotulamento é feito em cada técnica. A técnica exata que deve ser usada dependerá bastante do problema de negócios e dos dados. No entanto, os métodos de engenharia de recurso descritos abaixo podem ser usados como linha de base para a criação de recursos. Abaixo, discutiremos os recursos de retardo que devem ser construídos usando fontes de dados que vêm com carimbos de hora e também recursos estáticos criados usando fontes de dados estáticos, e forneceremos exemplos de casos de uso.

#### <a name="lag-features"></a>Recursos de retardo
Como mencionado anteriormente, na manutenção preditiva, os dados históricos geralmente são fornecidos com os carimbos de data/hora que indicam a hora da coleta de cada dado. Há várias maneiras de criar recursos usando os dados fornecidos com os dados com carimbo de data/hora. Nesta seção, discutiremos alguns desses métodos usados para manutenção preditiva. No entanto, não estamos limitados somente por esses métodos. Uma vez que a engenharia de recursos é considerada uma das áreas mais criativas de modelagem preditiva, pode haver muitas outras maneiras de se criar recursos. Eis algumas técnicas gerais.

##### <a name="rolling-aggregates"></a>*Agregações sem interrupção*
Para cada registro de um ativo, podemos escolher uma janela de tamanho "W” que é o número de unidades de tempo para o qual gostaríamos de calcular agregados históricos. Em seguida, calculamos recursos de agregação sem interrupção usando os períodos W antes da data de registro. Alguns exemplos de agregações sem interrupção podem ser contagem sem interrupção, média, desvios padrão, número de exceções baseado em desvio padrão, medidas CUMSUM, valores mínimo e máximo para a janela. Outra técnica interessante é capturar alterações de tendência, picos e alterações de nível usando algoritmos que detectam anomalias nos dados usando algoritmos de detecção de anomalias.

Para a demonstração, consulte a Figura 1 em que representamos valores de sensor registrados para um ativo para cada unidade de tempo com as linhas azuis e marcamos o cálculo de média do recurso sem interrupção como W = 3 para os registros em t<sub>1</sub> e t<sub>2</sub>, que são indicados por agrupamentos laranja e verde, respectivamente.

![Figura 1. Recursos de agregação sem interrupção](./media/cortana-analytics-playbook-predictive-maintenance/rolling-aggregate-features.png)

Figura 1. Recursos de agregação sem interrupção

Como exemplo, para falha de componente de aeronave, os valores de sensor da semana passada, dos últimos três dias e do último dia foram usados para criar os recursos de média, desvio padrão e soma. Da mesma forma, para falhas em caixas eletrônicos, foram usados os valores brutos e sem interrupção de sensor, valor mediano, intervalo, desvios padrão, número de exceções além de três desvios padrão, recursos CUMSUM superior e inferior.

Para previsão de atraso de voo, as contagens de erro da última semana os códigos foram usadas para criar recursos. Para falhas de porta de vagões, as contagens de eventos do último dia, as contagens de eventos nas duas semanas anteriores e a variação de contagens de eventos nos 15 dias anteriores foram usadas para criar recursos de retardo. A mesma contagem foi usada para eventos relacionados à manutenção.

Além disso, ao selecionar um W que é muito grande (por exemplo, anos), será possível examinar todo o histórico de um ativo, como a contagem de todos os registros de manutenção, falhas, etc. até o momento do registro. Esse método foi usado para a contagem de falhas do disjuntor nos últimos três anos. Também para falhas da composição, todos os eventos de manutenção foram contados criar um recurso a fim de capturar os efeitos de manutenção de longo prazo.

##### <a name="tumbling-aggregates"></a>*Agregações em cascata*
Para cada registro rotulado de um ativo, podemos escolher uma janela do tamanho "W-<sub>k</sub>", sendo que k é o número ou as janelas do tamanho "W" para os quais desejamos criar recursos de retardo. "k" pode ser selecionado como um número grande para capturar os padrões de degradação de longo prazo ou um número pequeno para capturar efeitos de curto prazo. Em seguida, usamos as janelas em cascata W -<sub>k</sub> , W -<sub>(k-1)</sub>, ..., W -<sub>2</sub> , W -<sub>1</sub> para criar recursos de agregação para os períodos antes da data e da hora do registro (veja a Figura 2). Também são janelas sem interrupção no nível de registro para uma unidade de tempo que não é capturada na Figura 2, mas a ideia é a mesma da Figura 1, em que t<sub>2</sub> também é usado para demonstrar o efeito sem interrupção.

![Figura 2. Recursos de agregação em cascata](./media/cortana-analytics-playbook-predictive-maintenance/tumbling-aggregate-features.png)

Figura 2. Recursos de agregação em cascata

Por exemplo, para turbinas eólicas, W = 1 e k = 3 meses foram usados para criar recursos de retardo para cada um dos últimos 3 meses usando exceções superior e inferior.

#### <a name="static-features"></a>Recursos estáticos
Essas são as especificações técnicas do equipamento, como data de fabricação, modelo, local, etc. Enquanto os recursos de retardo são principalmente numéricos por natureza, os recursos estáticos geralmente se tornam variáveis categóricas nos modelos. Como exemplo, as propriedades de disjuntor, como tensão, especificações de corrente e potência e os tipos de transformador, fontes de energia, etc. foram usados. Para as falhas do disco de freio, o tipo de roda, por exemplo, se é feita de liga ou aço, foi usado como alguns dos recursos estáticos.

Durante a geração de recurso, algumas etapas importantes, como a manipulação de valores ausentes e a normalização, devem ser executadas. Há vários métodos de imputação de valores ausentes e de normalização de dados, que não serão discutidos aqui. No entanto, é útil testar métodos diferentes para ver se um aumento no desempenho de previsão é possível.

A tabela final de recursos após as etapas de engenharia de recursos discutidas na seção anterior deve se parecer com o esquema de dados de exemplo abaixo, quando a unidade de tempo é um dia:

| ID do ativo | Hora | Colunas de recurso | Rótulo |
| --- | --- | --- | --- |
| 1 |Dia 1 | | |
| 1 |Dia 2 | | |
| ... |... | | |
| 2 |Dia 1 | | |
| 2 |Dia 2 | | |
| ... |... | | |

## <a name="modeling-techniques"></a>Técnicas de modelagem
A manutenção preditiva é um domínio muito avançado, geralmente empregando questões comerciais que podem ser abordadas em diferentes ângulos da perspectiva de modelagem preditiva. Nas próximas seções, apresentaremos as principais técnicas usadas para modelar questões de negócios diferentes que podem ser respondidas com soluções de manutenção preditiva. Embora existam semelhanças, cada modelo tem sua própria maneira de construir rótulos, o que será descrito em detalhes. Como um recurso que acompanha este artigo, você pode consultar o modelo de manutenção preditiva incluído nas experiências de exemplo fornecidas no Azure Machine Learning. Os links para o material online do modelo são fornecidos na seção de recursos. Você pode ver como algumas das técnicas de engenharia de recurso discutidas acima e a técnica de modelagem que será descrita nas seções a seguir são aplicadas para prever falhas no motor de uma aeronave usando o Azure Machine Learning.

### <a name="binary-classification-for-predictive-maintenance"></a>Classificação binária para a manutenção preditiva
A classificação binária para manutenção preditiva é usada para prever a probabilidade de o equipamento falhar em um período de tempo futuro. O período de tempo é determinado e baseado em regras de negócios e nos dados em questão. Alguns períodos de tempo comuns são o prazo mínimo necessário para comprar peças de reposição para substituir componentes com probabilidade de danos ou o tempo necessário para implantar recursos de manutenção para executar rotinas de manutenção e corrigir o problema que provavelmente ocorre nesse período de tempo. Chamamos esse período futuro no horizonte de "X".

Para usar classificação binária, precisamos identificar dois tipos de exemplos que chamamos de positivo e negativo. Cada exemplo é um registro que pertence a uma unidade de tempo para um ativo que descreve conceitualmente e abstrai suas condições operacionais até aquela unidade de tempo por meio de engenharia de recursos usando fontes históricas e outras fontes de dados descritas anteriormente. No contexto de classificação binária para manutenção preditiva, o tipo positivo indica falhas (rótulo 1) e o tipo negativo indica operações normais (rótulo = 0), em que os rótulos são do tipo categórico. O objetivo é encontrar um modelo que identificará cada novo exemplo como provável de falhar ou de operar normalmente nas próximas X unidades de tempo.

#### <a name="label-construction"></a>Construção do rótulo
A fim de criar um modelo preditivo para responder à pergunta "qual é a probabilidade de o ativo falhar nas próximas X unidades de tempo?”, o rotulamento é feito usando X registros anteriores à falha de um ativo e rotulando-os como "prestes a falhar" (rótulo = 1) e rotulando todos os outros registros como "normal" (rótulo = 0). Nesse método, os rótulos são variáveis categóricas (veja a Figura 3).

![Figura 3. Rotulação para classificação binária](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-binary-classification.png)

Figura 3. Rotulação para classificação binária

Para atrasos e cancelamentos de voo, X é escolhido como um dia para prever atrasos nas próximas 24 horas. Todos os voos que estão dentro do prazo de 24 horas antes das falhas foram rotulados como 1s. Para falhas nos caixas eletrônicos, dois modelos de classificação binária foram criados para prever a probabilidade de falha de uma transação nos próximos dez minutos e também para prever a probabilidade de falha nas próximas cem notas liberadas. Todas as transações que ocorreram nos 10 minutos antes da falha são rotuladas como 1 para o primeiro modelo. E todas as notas liberadas nas últimas 100 notas antes de uma falha foram rotuladas como 1 para o segundo modelo. Para falhas de disjuntor, a tarefa é prever a probabilidade de o próximo comando disjuntor falhar; nesse caso, X é escolhido para ser um comando futuro. Para falhas de porta de vagão, o modelo de classificação binária foi criado para prever falhas nos próximos sete dias. Para falhas de turbina eólica, X foi escolhido como três meses.

Os casos de turbina eólica e porta de vagão também são usados pela análise de regressão para prever o tempo de vida restante usando os mesmos dados, mas com uma estratégia de rotulamento diferente que é explicada na próxima seção.

### <a name="regression-for-predictive-maintenance"></a>Regressão para a manutenção preditiva
Os modelos de regressão na manutenção preditiva são usados para calcular o tempo de vida restante (RUL) de um ativo, que é definida como a quantidade de tempo em que o ativo estará operacional antes de ocorrer a próxima falha. Da mesma forma que a classificação binária, cada exemplo é um registro que pertence a uma unidade de tempo "Y" para um ativo. No entanto, no contexto de regressão, o objetivo é encontrar um modelo que calculará o tempo de vida restante de cada novo exemplo como um número contínuo, que é o período de tempo restante antes da falha. Chamamos esse período de tempo um múltiplo de Y. Cada exemplo também tem um tempo de vida restante que pode ser calculada medindo a quantidade de tempo que falta para esse exemplo antes da próxima falha.

#### <a name="label-construction"></a>Construção do rótulo
Dada a pergunta "Qual é o tempo de vida restante do equipamento?
", os rótulos para o modelo de regressão podem ser construídos usando cada registro antes da falha e rotulando-os ao calcular quantas unidades de tempo faltam antes da próxima falha. Nesse método, os rótulos são variáveis contínuas (consulte a Figura 4).

![Figura 4. Rotulação para a regressão](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-regression.png)

Figura 4. Rotulação para a regressão

Diferentemente da classificação binária, para a regressão, os ativos sem falhas nos dados não podem ser usados para modelagem, já que o rotulamento é feito em relação a um ponto de falha e seu cálculo não é possível sem se saber por quanto tempo o ativo sobreviveu antes da falha. Esse problema é mais bem resolvido por outra técnica estatística chamada análise de sobrevivência.
Não vamos abordar a Análise de Sobrevivência nesse playbook devido a complicações que podem surgir com a aplicação da técnica em casos de uso de manutenção preditiva que envolvem dados com variação de tempo e intervalos frequentes.

### <a name="multi-class-classification-for-predictive-maintenance"></a>Classificação multiclasse para a manutenção preditiva
A classificação multiclasse para manutenção preditiva pode ser usada para prever dois resultados futuros. O primeiro é atribuir um ativo a um dos vários períodos de tempo possíveis para fornecer um intervalo de tempo de falha para cada ativo. A segunda é identificar a probabilidade de falha em um período futuro devido a uma das várias causas principais. Isso permite que a equipe de manutenção que tenha esse conhecimento possa lidar com os problemas antecipadamente. Outra técnica de modelagem multiclasse tem mais enfoque na determinação da causa principal mais provável de determinada falha. Isso permite a criação de recomendações para as principais ações de manutenção a serem realizadas para corrigir uma falha.
Com uma lista classificada de causas raiz e ações de reparo associadas,  
os técnicos podem ser mais eficientes ao executar suas primeiras ações de reparo após as falhas.

#### <a name="label-construction"></a>Construção do rótulo
Dadas as duas perguntas que são "qual é a probabilidade de que um ativo falhe nas próximas unidades "aZ” de tempo, onde"a" é o número de períodos" e "qual é a probabilidade de que o ativo falhe nas próximas X unidades de tempo devido ao problema" P<sub>i</sub>", onde"i"é o número de causas possíveis, o rotulamento é feito da maneira abaixo para essas duas técnicas.

Para a segunda pergunta, o rotulamento é feito usando aZ registros antes da falha de um ativo, rotulando-os usando buckets de tempo (3Z, 2Z, Z) como rótulos e rotulando todos os outros registros como "normal" (rótulo = 0). Nesse método, o rótulo é variável categórica (veja a Figura 5).

![Figura 5. Rotulação da classificação multiclasse para a previsão da hora da falha](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-failure-time-prediction.png)

Figura 5. Rotulação da classificação multiclasse para a previsão da hora da falha

Para a segunda pergunta, o rotulamento é feito usando X registros antes da falha de um ativo, rotulando-os como "prestes a falhar devido ao problema P<sub></sub>" (rótulo = P<sub></sub>) e rotulando todos os outros registros como "normal" (rótulo = 0). Nesse método, os rótulos são variáveis categóricas (veja a Figura 6).

![Figura 6. Rotulação da classificação multiclasse para a previsão da causa-raiz](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-root-cause-prediction.png)

Figura 6. Rotulação da classificação multiclasse para a previsão da causa-raiz

O modelo atribui uma probabilidade de falha devido a cada P<sub>i</sub> e também uma probabilidade de não falha. Essas probabilidades podem ser ordenadas por magnitude para permitir a previsão dos problemas que têm maior probabilidade de ocorrer no futuro. O caso de uso de falha de componente de aeronave foi estruturado como um problema de multiclassificação. Isso permite a previsão das probabilidades de falha de dois componentes de válvula de pressão diferentes ocorrerem no próximo mês

Para recomendar ações de manutenção após falhas, o rotulamento não exige a seleção de um horizonte específico. Isso ocorre porque o modelo não está prevendo falha no futuro, somente prevendo a causa mais provável depois que a falha ocorrer. As falhas de porta do elevador se encaixam na terceira hipótese, em que o objetivo é prever a causa da falha levando em conta dados históricos sobre as condições operacionais. Em seguida, esse modelo será usado para prever as causas mais prováveis após uma falha. Um benefício importante desse modelo é que ele ajuda os técnicos sem experiência a diagnosticar e a corrigir facilmente problemas que exigiriam anos de experiência.

## <a name="training-validation-and-testing-methods-in-predictive-maintenance"></a>Métodos de treinamento, validação e teste na manutenção preditiva
Na manutenção preditiva, semelhante a qualquer outra solução que contém dados de data/hora, a rotina comum de treinamento e teste precisa considerar os aspectos de variação de tempo para generalizar melhor sobre dados futuros não vistos.

### <a name="cross-validation"></a>Validação cruzada
Muitos algoritmos de aprendizado de máquina dependem de um número de hiperparâmetros que podem alterar significativamente o desempenho do modelo. Os valores ideais desses hiperparâmetros não são calculados automaticamente durante o treinamento do modelo, mas devem ser especificados pelo cientista de dados. Há várias maneiras de localizar bons valores de hiperparâmetros. O mais comum é a “validação cruzada k vezes”, que divide os exemplos aleatoriamente em “k” dobras. Para cada conjunto de valores de hiperparâmetros, o algoritmo de aprendizado é executado k vezes. Em cada iteração, os exemplos na dobra atual são usados como um conjunto de validação; o restante dos exemplos é usado como um conjunto de treinamento. O algoritmo treina com exemplos de treinamento e as métricas de desempenho são calculadas sobre exemplos de validação. No final desse loop para cada conjunto de valores de hiperparâmetro, podemos calcular a média dos valores de métrica de desempenho k e escolher valores de hiperparâmetro que tenham o melhor desempenho médio.

Conforme mencionado anteriormente, em problemas de manutenção preditiva, os dados são gravados como uma série temporal de eventos que vêm de várias fontes de dados. Esses registros podem ser classificados de acordo com a hora de rotulação de um registro ou de um exemplo. Portanto, se dividirmos o conjunto de dados aleatoriamente em conjuntos de treinamento e validação, alguns dos exemplos de treinamento serão posteriores a alguns exemplos de validação. Isso resulta na estimativa de futuro desempenho de valores de hiperparâmetro baseada nos dados que chegaram antes de o modelo ser treinado. Essas estimativas podem ser muito otimistas, especialmente se a série temporal não for estacionária e alterar o comportamento ao longo do tempo. Como resultado, os valores de hiperparâmetro escolhidos podem estar abaixo do ideal.

Uma maneira melhor de encontrar bons valores de hiperparâmetro é dividir os exemplos em conjuntos de treinamento, validação e teste independentes de tempo, de forma que todos os exemplos de teste sejam posteriores no tempo em relação a todos os exemplos de treinamento. Em seguida, para cada conjunto de valores de hiperparâmetros, treinamos o algoritmo no conjunto de treinamento, medimos o desempenho do modelo no mesmo conjunto de validação e escolhemos valores de hiperparâmetro que mostram o melhor desempenho. Quando dados de série temporal não são estáticos e evoluem ao longo do tempo, os valores de hiperparâmetro escolhidos pela divisão treinamento/validação levam a um desempenho futuro do “modelo” melhor do que com os valores escolhidos aleatoriamente por validação cruzada.

O modelo final é gerado pelo treinamento de um algoritmo de aprendizado sobre dados inteiros usando os melhores valores de hiperparâmetro encontrados usando divisão treinamento/validação ou validação cruzada.

### <a name="testing-for-model-performance"></a>Testes para o desempenho do modelo
Depois de criar um modelo, precisamos calcular seu futuro desempenho sobre novos dados. A estimativa mais simples pode ser o desempenho do modelo em vez dos dados de treinamento. Mas essa estimativa é extremamente otimista, pois o modelo é personalizado para os dados que são usados na estimativa do desempenho. Uma melhor estimativa poderia ser a métrica de desempenho de valores de hiperparâmetro calculados sobre o conjunto de validação ou média da métrica de desempenho calculada com validação cruzada. Mas, pelas mesmas razões mencionadas anteriormente, essas estimativas são ainda extremamente otimistas. Precisamos de abordagens mais realistas para medir o desempenho do modelo.

Uma maneira é dividir os dados aleatoriamente em conjuntos de treinamento, validação e teste. Os conjuntos de treinamento e validação são usados para selecionar valores de hiperparâmetro e treinar o modelo com eles. O desempenho do modelo é medido no conjunto de teste.

Outra maneira relevante para a manutenção preditiva é dividir os exemplos em conjuntos de treinamento, validação e teste independentes de tempo, de forma que todos os exemplos de teste sejam posteriores no tempo em relação a todos os exemplos de treinamento e validação. Após a divisão, a geração de modelo e a medição do desempenho são feitas da mesma forma descrita anteriormente.

Quando a série temporal é estática e fácil de prever, ambas as abordagens geram semelhante estimativas de desempenho futuros. Mas quando a série temporal é dinâmica e/ou difícil de prever, a segunda abordagem gera estimativas mais realistas de desempenho futuro que a primeira.

### <a name="time-dependent-split"></a>Divisão dependente do tempo
Como uma prática recomendada, nesta seção vamos dar uma olhada mais detalhada em como implementar a divisão dependente de tempo. Nós descrevemos uma divisão dupla dependente de tempo entre conjuntos de treinamento e de teste; no entanto, a mesma lógica se aplica à divisão dependente de tempo para conjuntos de validação e de treinamento.

Suponha que tenhamos um fluxo de eventos com carimbo de data/hora, como medidas de vários sensores. Os recursos de treinamento e os exemplos de teste, bem como seus rótulos, são definidos por períodos de tempo que contêm vários eventos.
Por exemplo, para classificação binária, conforme descrito nas seções Engenharia de recursos e Técnicas de modelagem, os recursos são criados com base nos últimos eventos e os rótulos são criados com base em eventos futuros, no intervalo de "X" unidades de tempo no futuro. Assim, o período de tempo de rotulamento de um exemplo ocorre depois do período de tempo de seus recursos. Para a divisão dependente de tempo, escolhemos um ponto no tempo em que treinamos um modelo com hiperparâmetros ajustados usando dados históricos até aquele ponto. Para impedir o vazamento de futuros rótulos que estão além do corte de treinamento para os dados de treinamento, escolhemos o período de tempo mais recente para rotular exemplos de treinamento como X unidades antes da data de corte de treinamento. Na Figura 7, cada círculo sólido representa uma linha no conjunto de dados final do recurso para o qual os recursos e rótulos são calculados de acordo com o método descrito acima. Considerando que a figura mostra os registros que deveriam entrar nos conjuntos de treinamento e teste durante a implantação da divisão dependente de tempo para X = 2 e W = 3:

![Figura 7. Divisão dependente do tempo para classificação binária](./media/cortana-analytics-playbook-predictive-maintenance/time-dependent-split-for-binary-classification.png)

Figura 7. Divisão dependente do tempo para classificação binária

Os quadrados verdes representam os registros que pertencem às unidades de tempo que podem ser usadas para treinamento. Conforme explicado anteriormente, cada exemplo de treinamento na tabela final de recursos é gerado usando-se os últimos três períodos para a geração de recursos e dois períodos futuros para rotulamento antes do corte do dia do treinamento. Não usamos exemplos no conjunto de treinamento quando uma parte dos dois períodos futuros para tal exemplo está além do corte de treinamento, já que assumimos não ter visibilidade além do corte de treinamento. Devido a essa restrição, os exemplos em preto representam os registros do conjunto de dados rotulado final que não deve ser usado no conjunto de dados de treinamento. Esses registros também não serão usados no teste de dados, já que são de antes do corte do treinamento e seu período de tempo dependeria parcialmente do período de tempo do treinamento, o que não deve ocorrer, já que queremos separar totalmente os períodos de tempo de rotulamento para treinamento e teste a fim de evitar vazamento de informações de rótulo.

Essa técnica permite a sobreposição nos dados usados para a geração de recursos entre os exemplos de treinamento e teste que estão próximos do corte de treinamento. Dependendo da disponibilidade de dados, uma separação mais séria pode ser feita sem usar qualquer um dos exemplos no conjunto de teste que estejam a W unidades de tempo o ponto de corte de treinamento.

Em nosso trabalho, descobrimos que modelos de regressão usados para prever o restante do tempo de vida são afetados mais seriamente pelo problema de vazamento, e o uso de uma divisão aleatória leva a sobreajuste. Da mesma forma, em problemas de regressão, a divisão deve ser de modo que os registros que pertencem a ativos com falhas antes do corte de treinamento sejam usados para o conjunto de treinamento e os ativos que têm falhas após o corte sejam usados para o conjunto de teste.

Como um método geral, outra prática recomendada importante para dividir dados para treinamento e teste é usar uma ID distribuída por ativo para que nenhum dos ativos usados no treinamento sejam usados para teste, uma vez que o propósito do teste é verificar se, quando um novo ativo é usado para previsão, o modelo fornece resultados reais.

### <a name="handling-imbalanced-data"></a>Tratamento dos dados em desequilíbrio
Em problemas de classificação, se houver mais exemplos de uma classe que de outras, os dados serão considerados em desequilíbrio. Normalmente, gostaríamos de ter representantes suficientes de cada classe nos dados de treinamento para podermos diferenciar entre classes diferentes. Se uma classe for menor que 10% dos dados, podemos dizer que os dados são desequilibrados e chamamos o conjunto de dados pouco representado de classe minoritária. Drasticamente, em muitos casos encontramos conjuntos de dados em desequilíbrio, em que uma classe está seriamente sub-representada em comparação com outras, por exemplo, constituindo 0,001% dos pontos de dados. O desequilíbrio de classe é um problema em muitos domínios, incluindo detecção de fraudes, invasão de rede e manutenção preditiva onde as falhas são geralmente raras no tempo de vida dos ativos que compõem os exemplos de classe minoritária.

No caso de desequilíbrio de classe, o desempenho da maioria dos algoritmos de aprendizado padrão fica comprometido, já que eles tentam minimizar a taxa de erro geral. Por exemplo, para um conjunto de dados com exemplos de classe positiva de 1% e exemplos de classe negativa de 99%, podemos obter precisão de 99% simplesmente rotulando todas as instâncias como negativas. No entanto, isso é um erro de classificação de todos os exemplos positivos; portanto, o algoritmo não é um útil, embora a métrica de precisão seja muito alta. Consequentemente, as métricas de avaliação convencional, como a precisão geral na taxa de erro, não são suficientes em caso de aprendizado em desequilíbrio. Outras métricas, como precisão, cancelamento, pontuação F1 e curvas ROC ajustadas são usadas para avaliações em caso de conjuntos de dados em desequilíbrio, que serão abordados na seção Métricas de Avaliação.

No entanto, existem alguns métodos que ajudam a solucionar o problema de desequilíbrio de classe. Os dois principais são técnicas de amostragem e aprendizagem sensível ao custo.

#### <a name="sampling-methods"></a>Métodos de amostragem
O uso de métodos de amostragem no aprendizado em desequilíbrio consiste na modificação do conjunto de dados por mecanismos para fornecer um conjunto de dados equilibrado. Embora existam muitas técnicas diferentes de amostragem, as mais simples são sobreamostragem e subamostragem aleatórias.

Resumindo, a sobreamostragem aleatória é a seleção aleatória da classe minoritária, replicando os exemplos e adicionando-os ao conjunto de dados de treinamento. Isso aumenta o número total de exemplos na classe minoritária e eventualmente equilibra o número de exemplos de classes diferentes. Um dos riscos de sobreamostragem é que várias instâncias de determinados exemplos podem levar o classificador a se tornar muito específico, causando um sobreajuste.
Isso resultaria em precisão alta de treinamento, mas o desempenho em dados de teste nunca vistos pode ser fraco. Por outro lado, a subamostragem aleatória é a seleção de amostra aleatória da classe majoritária e a remoção dos exemplos do conjunto de dados de treinamento. No entanto, a remoção de exemplos de classe majoritária pode levar o classificador a perder conceitos importantes referentes à classe majoritária. A amostragem híbrida, em que a classe minoritária tem mais amostras e a classe majoritária tem menos, é outra abordagem viável. Há muitas outras técnicas de amostragem mais sofisticadas disponíveis, e métodos de amostragem para desequilíbrio de classe é uma área de pesquisa popular, com muita atenção e contribuições de vários canais. O uso de técnicas diferentes para decidir sobre as mais eficientes é normalmente testado e pesquisado pelo cientista de dados e depende bastante das propriedades de dados. Além disso, é importante ter certeza de que os métodos de amostragem são aplicados somente ao conjunto de treinamento, mas não ao conjunto de teste.

#### <a name="cost-sensitive-learning"></a>Aprendizagem sensível ao custo
Na manutenção preditiva, falhas que constituem a classe minoritária são mais interessantes do que exemplos normais; portanto, o foco normalmente é o desempenho do algoritmo. Isso é conhecido como perda desigual ou custos assimétricos de classificação incorreta de elementos de classes diferentes, no qual a previsão incorreta de positivo como negativo pode custar mais do que o contrário. O classificador desejado deve ser capaz de fornecer alta precisão de previsão sobre a classe minoritária, sem comprometer gravemente a precisão para a classe majoritária.

Há várias maneiras de se alcançar isso. Ao atribuir um alto custo de classificação incorreta da classe minoritária e tentar minimizar o custo geral, o problema das perdas desiguais pode ser efetivamente resolvido. Alguns algoritmos de aprendizado de máquina usam essa ideia inerentemente, como SVMs (máquinas de vetor de suporte), em que o custo de exemplos positivos e negativos pode ser incorporado durante o tempo de treinamento. Da mesma forma, métodos de estímulo são usados e geralmente mostram bom desempenho em caso de dados em desequilíbrio, como algoritmos de árvore decisória.

## <a name="evaluation-metrics"></a>Métricas da avaliação
Como mencionado anteriormente, o desequilíbrio de classe causa baixo desempenho, já que os algoritmos tendem a classificar exemplos da classe majoritária melhor do que os casos de classe minoritária, já que o erro de classificação é aprimorado quando a classe majoritária é rotulado corretamente. Isso leva a taxas de recuperação baixas e se torna um problema maior quando alarmes falsos custam caro aos negócios. A precisão é a métrica mais popular usada para descrever o desempenho do classificador. No entanto, conforme explicado acima, a precisão é ineficaz e não refletem o desempenho real da funcionalidade do classificador, já que ele é muito sensível a distribuições de dados. Em vez disso, outras métricas de avaliação são usadas para averiguar problemas de aprendizado em desequilíbrio. Nesses casos, precisão, recuperação e pontuações F1 devem ser as métricas iniciais a se observar ao avaliar o desempenho do modelo de manutenção preditiva. Na manutenção preditiva, as taxas de cancelamento denotam quantas falhas no conjunto de teste foram identificadas corretamente pelo modelo. Taxas mais altas de recuperação significam que o modelo foi bem-sucedida em capturar as falhas reais. A métrica de precisão está relacionada à taxa de alarmes falsos, em que taxas mais baixas de precisão correspondem a uma maior quantidade de alarmes falsos. A pontuação F1 considera as taxas de precisão e recuperação, com o melhor valor sendo 1 e o pior, 0.

Além disso, para classificação binária, as tabelas de decis e os gráficos de comparação de precisão são úteis para avaliar o desempenho. Eles focalizam somente na classe positiva (falhas) e fornecem uma visão mais complexa do desempenho do algoritmo que o que é visto examinando-se somente um ponto fixo operacional na curva ROC (característica operacional do receptor).
As tabelas de decis são obtidas classificando os exemplos de teste de acordo com suas probabilidades previstas de falhas calculadas pelo modelo antes do limite para decidir sobre o rótulo final. Os exemplos ordenados são agrupados em decis (ou seja, os exemplos de 10% com maior probabilidade e, em seguida, 20%, 30% e assim por diante). Calculando a razão entre a taxa positiva real de cada decil e sua linha de base aleatória (por exemplo, 0.1, 0.2...), é possível estimar como o desempenho do algoritmo é alterado em cada decil. Gráficos de comparação são usados para plotar valores de decis plotando taxas positivas reais de decil contra pares de taxa positiva real aleatórios para todos os decis. Normalmente, os primeiros decis são o foco dos resultados, já que é onde vemos os maiores ganhos. Os primeiros decis também podem ser vistos como representativos para "em risco" quando usados em manutenção preditiva.

## <a name="sample-solution-architecture"></a>Arquitetura da solução de exemplo
Ao implantar uma solução de manutenção preditiva, estamos interessados em uma solução de ponta a ponta que forneça um ciclo contínuo de ingestão de dados, de armazenamento de dados para treinamento do modelo, de geração de recursos, de previsão e visualização dos resultados juntamente com um mecanismo de geração de alertas, como um painel de monitoração de ativos. Queremos um pipeline de dados que forneça ideias futuras para o usuário de maneira contínua e automatizada. Uma arquitetura de manutenção preditiva de exemplo para um pipeline de dados IoT é ilustrada na Figura 8 abaixo. Na arquitetura, a telemetria em tempo real é coletada em um Hub de Evento que armazena dados de transmissão. Esses dados são ingeridos pelo Stream Analytics para o processamento em tempo real de dados como geração de recursos. Os recursos são então usados para chamar o serviço Web de modelo preditivo e os resultados são exibidos no painel. Ao mesmo tempo, os dados ingeridos também são armazenados em um banco de dados histórico e mesclados com fontes de dados externos, como bancos de dados locais para criar exemplos de treinamento para modelagem.
Os mesmos data warehouses podem ser usados para a pontuação por lote dos exemplos e o armazenamento dos resultados, que poderão ser usados novamente para fornecer relatórios preditivos no painel.

![Figura 8. Arquitetura de solução de exemplo para manutenção preditiva](./media/cortana-analytics-playbook-predictive-maintenance/example-solution-architecture-for-predictive-maintenance.png)

Figura 8. Arquitetura de solução de exemplo para manutenção preditiva

Para saber mais sobre cada um dos componentes da arquitetura, consulte a documentação do [Azure](https://azure.microsoft.com/) .

