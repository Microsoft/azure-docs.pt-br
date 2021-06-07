---
title: Guia de IA do Azure para soluções de manutenção preditiva - Processo de Ciência de dados da equipe
description: Uma descrição completa da ciência de dados que oferece soluções de manutenção preditiva em vários setores verticais.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 1e939b86eeadfee276378488cfcb40c07f28684d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880651"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Guia de IA do Azure para soluções de manutenção preditiva

## <a name="summary"></a>Resumo

Manutenção preditiva (**PdM**) é um aplicativo popular de análise preditiva que pode ajudar as empresas em vários setores a alcançar alta utilização de ativos e economia em custos operacionais. Este guia reúne os negócios e as diretrizes analíticas e práticas recomendadas para desenvolver e implantar soluções de PdM usando com êxito a tecnologia da [plataforma Microsoft Azure AI](https://azure.microsoft.com/overview/ai-platform).

Para começar, este guia apresenta cenários comerciais específicos do setor e o processo de qualificação desses cenários para PdM. Os requisitos de dados e as técnicas de modelagem para compilar soluções de PdM também são fornecidos. O conteúdo principal do guia  está em processo de ciência de dados - incluindo as etapas de preparação de dados, engenharia de recurso, criação de modelo e operacionalização do modelo. Para complementar esses conceitos principais, este guia lista um conjunto de modelos de solução para ajudar a acelerar o desenvolvimento de aplicativos PdM. O guia também aponta para os recursos de treinamento para o profissional saber mais sobre o IA por trás de ciência de dados. 

### <a name="data-science-guide-overview-and-target-audience"></a>Visão geral do guia de ciência de Dados e público-alvo
A primeira metade deste guia descreve problemas típicos de negócios, os benefícios da implementação de PdM para resolver esses problemas e lista alguns casos de uso comuns. Os Tomadores de decisões comerciais (BDMs) se beneficiarão deste conteúdo. A segunda metade explica a ciência de dados por trás da PdM e fornece uma lista de soluções de PdM criadas usando os princípios descritos neste guia. Também fornece caminhos de aprendizagem e ponteiros para o material de treinamento. Os tomadores de decisões técnicas (TDMs) acharão esse conteúdo útil.

| Começar com... | Se você for… |
|:---------------|:---------------|
| [Caso de negócios para manutenção preventiva](#business-case-for-predictive-maintenance) |um tomador de decisões comerciais (BDM) procurando para reduzir os custos operacionais e tempo de inatividade e melhorar a utilização do equipamento |
| [Ciência de Dados para manutenção preventiva](#data-science-for-predictive-maintenance) |um tomador de decisões técnicas (TDM) avaliando as tecnologias de PdM para entender o processamento de dados exclusivo e requisitos de IA para manutenção preditiva |
| [Modelos de solução para manutenção preventiva](#solution-templates-for-predictive-maintenance)|um arquiteto de software ou o desenvolvedor de IA procurando para preparar rapidamente uma demonstração ou uma prova de conceito |
| [Recursos de treinamento para manutenção preditiva](#training-resources-for-predictive-maintenance) | qualquer ou todos os itens acima e quiser aprender os conceitos fundamentais por trás da ciência de dados, ferramentas e técnicas.

### <a name="prerequisite-knowledge"></a>Pré-requisito de conhecimento
O conteúdo BDM não espera que o leitor tenha qualquer conhecimento de ciência de dados anteriores. Para o conteúdo TDM, um conhecimento básico de ciência de dados e de estatísticas é útil. Recomenda-se o conhecimento de dados do Azure e serviços IA, Python, R, XML e JSON. As técnicas de IA são implementadas em pacotes Python e R. Os modelos da solução são implementados usando serviços do Azure, ferramentas de desenvolvimento e SDKs.

## <a name="business-case-for-predictive-maintenance"></a>Caso de negócios para manutenção preventiva

As empresas exigem que os equipamentos críticos estejam executando com eficiência e utilização máximas para realizar seu retorno sobre os investimentos de capital. Esses ativos podem variar de mecanismos de aeronave, turbinas, elevadores ou resfriadores industriais - que custam milhões - até dispositivos diários como fotocopiadoras, máquinas de café ou coolers de água.
- Por padrão, a maioria das empresas dependem da _manutenção corretiva_, onde as partes são substituídas como e quando falharem. A manutenção corretiva garante os usos das partes completamente (portanto, sem desperdiçar a vida útil do componente), mas custos de negócios em tempo de inatividade, serviços e requisitos de manutenção agendada (horas desativado ou locais inconvenientes).
- No próximo nível, manutenção preventiva _prática de negócios_, onde determinam o tempo de vida útil de uma peça e realizam a manutenção ou substituem antes da falha. A manutenção preventiva evita falhas catastróficas e não programadas. Mas os altos custos do tempo de inatividade agendado, a subutilização do componente durante seu tempo de vida útil e o trabalho ainda permanecem.
- O objetivo da _manutenção preditiva_ é otimizar o equilíbrio entre a manutenção corretiva e preventiva, habilitando a substituição de componentes _no momento_ certo. Esta abordagem substitui apenas os componentes quando estiverem perto de uma falha. Ao aumentar a vida útil do componente (comparado à manutenção preventiva) e reduzindo os custos de mão de obra e manutenção agendada (pela manutenção corretiva), as empresas podem obter economias de custo e vantagens competitivas.

## <a name="business-problems-in-pdm"></a>Problemas de negócios em PdM
As empresas enfrentam alto risco operacional devido a falhas inesperadas e têm insight limitado na causa raiz dos problemas em sistemas complexos. Algumas das perguntas importantes das empresas são:

- Detectar anomalias nos equipamentos ou sistema de desempenho ou funcionalidade.
- Prever se um ativo pode falhar em um futuro próximo.
- Estimar a vida útil restante de um ativo.
- Identifica as principais causas de falha de um ativo.
- Identificar quais as ações de manutenção precisam ser realizadas, quando, em um ativo.

As instruções de meta típicas de PdM são:

- Reduzir o risco operacional de equipamentos de missão crítica.
- Aumentar a taxa de retorno de ativos prevendo as falhas antes que elas ocorram.
- Controlar o custo de manutenção permitindo que as operações de manutenção ocorram em tempo hábil.
- Reduzir o desgaste dos clientes, melhorar a imagem de marca e perda de vendas.
- Diminuir custos de inventário, reduzindo os níveis de estoque com a previsão do ponto de novo pedido
- Descobrir padrões conectados a vários problemas de manutenção
- Fornecer KPIs (indicadores chave de desempenho), como a pontuação de integridade para condições de ativos.
- Estimar o tempo de vida restante dos ativos.
- Recomendar as atividades de manutenção em tempo hábil.
- Habilitar apenas no inventário de tempo, estimando datas de ordem de substituição de peças.

Esses objetivos de meta são os pontos de partida para:

- _cientistas de dados_ para analisar e solucionar problemas específicos de previsão.
- _arquitetos e desenvolvedores de nuvem_ para juntar uma solução de ponta a ponta.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Qualificação de problemas para manutenção preditiva
É importante enfatizar que nem todos os casos ou problemas de empresas podem ser efetivamente resolvidos pela PdM. Há três critérios de qualificação importantes que devem ser considerados durante a seleção do problema:

- O problema deve ser preventivo por natureza; ou seja, deve haver um destino ou a um resultado para prever. O problema também deve ter um caminho claro de ação para evitar falhas quando são detectadas.
- O problema deve ter um registro do histórico operacional do equipamento que contém _bons e maus resultados_. O conjunto de ações tomadas para reduzir os resultados incorretos também deve estar disponível como parte desses registros. Relatórios de erro, logs de manutenção de degradação de desempenho, reparo e logs de substituição também são importantes. Além disso, os reparos assumidos para melhorar e os registros de substituição também são úteis.
- O histórico gravado deve ser refletido nos _dados_ relevantes que seja de qualidade _suficiente_ para ser compatível com o caso de uso. Para obter mais informações sobre a relevância dos dados e suficiência, consulte [Requisitos de dados para manutenção preventiva](#data-requirements-for-predictive-maintenance).
- Por fim, a empresa deve ter especialistas de domínio que tenham uma compreensão clara do problema. Devem estar cientes dos processos internos e práticas recomendadas para ajudar o analista a entender e interpretar os dados. Também devem ser capazes de fazer as alterações necessárias para processos de negócios existentes para ajudar a coletar os dados certos problemas, se necessário.

## <a name="sample-pdm-use-cases"></a>Casos de uso da PdM modelo
Esta seção se concentra em um conjunto de casos de uso de PdM de vários setores, como Aeroespacial, Utilidades e Transporte. Cada seção começa com um problema de negócios e aborda os benefícios da PdM, os dados relevantes com relação ao problema e, finalmente, os benefícios de uma solução PdM.

| Problema de Negócios | Benefícios da PdM |
|:-----------------|-------------------|
|**Aviação**      |                   |
|_Atraso e cancelamentos de voo_ devido a problemas mecânicos. Falhas que não podem ser reparadas no tempo podem fazem com que voos seja cancelado e interrompem o agendamento e as operações. |Soluções PdM podem prever a probabilidade de um avião ser atrasado ou cancelado devido a falhas mecânicas.|
|_Falha de peças do motor da aeronave_: substituições de parte do motor de aeronaves estão entre as tarefas mais comuns de manutenção no setor de viagens. As soluções de manutenção para companhias aéreas exigem o gerenciamento cuidadoso de disponibilidade, entrega e planejamento de estoque dos componentes|A capacidade de reunir inteligência sobre confiabilidade do componente leva a uma significativa redução nos custos de investimento.|
|**Finanças** |                         |
|_Falha de caixa eletrônico_ é um problema comum no setor de serviços bancários. O problema aqui é relatar a probabilidade que uma transação de saque em caixa eletrônico pode ser interrompida devido a um atolamento de papel ou falha em peça no caso de caixa eletrônico. Com base na previsão de falhas de transação, a manutenção pode ser feita nos caixas eletrônicos para evitar proativamente que ocorram falhas.| Em vez de permitir que o computador falhe no meio de uma transação, a alternativa desejável é programar a máquina par anegar o serviço com base na previsão.|
|**Energia** |                          |
|_Falhas na turbina do vento_: as turbinas do vento são a fonte de energia principal em países/regiões responsáveis por ambiente e envolvem altos custos de capital. Um componente-chave em turbinas de vento é o motor gerador, cuja falha renderiza a turbina ineficaz. Também é altamente caro corrigir.|KPIs preventivos como MTTF (tempo médio de falha) podem ajudar as empresas de energia a evitar falhas de turbina e a verificar o tempo de inatividade mínimo. As probabilidades de falha informam aos técnicos para monitorar turbinas que provavelmente falharão em breve e agendam regimes com base em tempo de manutenção. Modelos de previsão fornecem insights sobre diferentes fatores que contribuem com a falha, o que ajuda a técnicos entender melhor as causas raizes dos problemas.|
|_Falhas de disjuntor_: a distribuição de eletricidade para casas e empresas requer linhas de energia estejam operacionais em todos os momentos para garantir o fornecimento de energia. Os disjuntores ajudam a limitar ou evitar danos às linhas de energia durante sobrecarga ou condições climáticas adversas. O problema da empresa aqui é prever falhas do disjuntor.| As soluções de PdM ajudam a reduzir os custos de reparo e a aumentar o ciclo de vida de equipamentos como disjuntores. Ajudam a melhorar a qualidade da rede de energia reduzindo falhas inesperadas e interrupções de serviço.|
|**Transporte e logística** |    |
|_Falhas de porta do elevador_: grandes empresas de elevador fornecem um serviço de pilha completa para milhões de elevadores funcionais em todo o mundo. Segurança, Tempo de atividade, confiabilidade e segurança elevador são a principal preocupação de  seus clientes. Essas empresas rastreiam esses e vários outros atributos por meio de sensores, para ajudá-los com manutenção corretiva e preventiva. Em um elevador, o problema mais importante do cliente é o mal funcionamento das portas do elevador. O problemas das empresas neste caso é fornecer um aplicativo preditivo da base de conhecimento que irá prever as possíveis causas de falhas de porta.| Os elevadores são investimentos de capital para potencialmente um tempo de vida de 20 a 30 anos. Para que cada venda potencial possa ser altamente competitiva; portanto, as expectativas para o serviço de suporte são altas. A manutenção preditiva pode fornecer essas empresas com uma vantagem sobre seus concorrentes em seus produtos e as ofertas de serviço.|
|_Falhas de roda_: as falhas de roda correspondem à metade dos descarrilamentos de trem e custam bilhões para o setor ferroviário global. Falhas de disco também causam deterioração dos trilho, muitas vezes fazendo com que o trilho quebre prematuramente. As quebras de trilho geram eventos catastróficos, como descarrilamentos. Para evitar esses problemas, ferrovias monitoram o desempenho da rodas e as substituem de forma preventiva. O problema da empresa é a previsão de falhas de roda.| A manutenção preditiva das rodas ajudará com substituição das rodas just-in-time |
|_Falhas das portas do trem do metrô_: um dos principais motivos para atrasos nas operações do metrô são as falhas de porta dos carros dos trens. O problema da empresa aqui é prever falhas das portas do trem.|Conhecimento antecipado de uma falha de porta ou o número de dias até a falha da porta, ajudam a otimizar as programações de manutenção das portas dos trens.|

A seção a seguir oferece detalhes de como obter os benefícios de PdM discutidos acima.

## <a name="data-science-for-predictive-maintenance"></a>Ciência de Dados para manutenção preventiva

Esta seção fornece diretrizes gerais de princípios de ciência de dados e prática para PdM. Se destina a ajudar um TDM, arquiteto de soluções, ou um desenvolvedor compreender os pré-requisitos e o processo de criação de aplicativos de IA de ponta a ponta para PdM. Você pode ler esta seção e analisar as demonstrações e modelos de prova de conceito listados em [Modelos de Solução para manutenção preditiva](#solution-templates-for-predictive-maintenance). Em seguida, você pode usar esses princípios e práticas recomendadas para implementar sua solução PdM no Microsoft Azure.

> [!NOTE]
> Este guia NÃO se destina a ensinar o leitor de Ciência de Dados. Várias fontes úteis são fornecidas para ler mais na seção de [recursos de treinamento para manutenção preventiva](#training-resources-for-predictive-maintenance). Os [modelos de solução](#solution-templates-for-predictive-maintenance) listados na guia demonstram algumas dessas técnicas de IA para problemas PdM específicos.

## <a name="data-requirements-for-predictive-maintenance"></a>Requisitos de dados para manutenção preventiva

O sucesso de qualquer aprendizado depende da (a) qualidade do que está sendo ensinado e da (b) capacidade do aprendiz. Modelos de previsão aprendem os padrões dos dados históricos e preveem resultados futuros com certa probabilidade com base nesses padrões observados. A precisão da previsão do modelo depende a relevância, suficiência e qualidade dos dados de treinamento e teste. Os novos dados que são 'classificados' usando este modelo devem ter os mesmos recursos e esquema como os dados de treinamento e teste. As características de recurso (tipo, densidade, distribuição e assim por diante) de dados novos devem coincidir com um dos conjuntos de dados de treinamento e teste. O foco desta seção é esses requisitos de dados.

### <a name="relevant-data"></a>Dados relevantes

Em primeiro lugar, os dados precisam ser _pertinentes ao problema_. Considere o caso de uso de _falha de roda_ discutido acima - os dados de treinamento devem conter recursos relacionados a operações de roda. Se o problema ocorreu ao prever a falha do _sistema de tração_, os dados de treinamento devem abranger todos os componentes do sistema de tração. O primeiro caso tem como alvo um componente específico, enquanto o segundo caso tem como alvo a falha de um subsistema maior. A recomendação geral é criar sistemas de previsão sobre componentes específicos em vez de subsistemas maiores, uma vez que o último terá mais dados dispersados. O especialista de domínio (consulte [Problemas de qualificação para manutenção preditiva](#qualifying-problems-for-predictive-maintenance)) deve ajudar na seleção de subconjuntos de dados para a análise mais relevante. As fontes de dados relevantes são discutidas com mais detalhes na [Preparação de dados para manutenção preditiva](#data-preparation-for-predictive-maintenance).

### <a name="sufficient-data"></a>Dados suficientes
Duas perguntas geralmente são feitas com relação aos dados de histórico da falha: (1) "Quantos eventos de falha são necessários para treinar um modelo?" (2) "quantos registros são considerados" suficientes "? Não há nenhuma resposta definitiva, mas apenas regras de Thumb. Para (1), quanto mais o número de eventos de falha, melhor o modelo. Para (2), e o número exato de eventos de falha depende dos dados e o contexto do problema que está sendo resolvido. Mas, por outro lado, se uma máquina falhar com muita frequência, em seguida, a empresa a substituirá, o que irá reduzir instâncias de falha. Aqui, novamente, a orientação do domínio especialista é importante. No entanto, há métodos para lidar com o problema de _eventos raros_. Eles são discutidos na seção [Tratamento dos dados em desequilíbrio](#handling-imbalanced-data).

### <a name="quality-data"></a>Dados de qualidade
A qualidade dos dados é essencial - cada valor de atributo de previsão deve ser _preciso_ em conjunto com o valor da variável de destino. A qualidade de dados é uma área bem estudada em estatísticas e gerenciamento de dados e, portanto, fora do escopo deste guia.

> [!NOTE]
> Há vários recursos e produtos empresariais para fornecer dados de qualidade. Um exemplo de referências é fornecido abaixo:
> - Dasu, T, Johnson, T., Exploratory Data Mining and Data Cleaning, Wiley, 2003.
> - [Análise de dados exploratórios, Wikipedia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Hellerstein, J, Quantitative Data Cleaning for Large Databases](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de Jonge, E, van der loo, M, Introduction to Data Cleaning with R](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

## <a name="data-preparation-for-predictive-maintenance"></a>Preparação de dados para manutenção preditiva

### <a name="data-sources"></a>Fontes de dados

As fontes de dados relevantes para manutenção preditiva incluem, mas não estão limitadas a:
- Histórico de falha
- Histórico de manutenção/reparo
- Condições operacionais da máquina
- Metadados de equipamento

#### <a name="failure-history"></a>Histórico de falha
Eventos de falha são raros em aplicativos PdM. No entanto, ao criar modelos de previsão, o algoritmo precisa saber o padrão operacional normal de um componente, bem como seus padrões de falha. Assim, os dados de treinamento devem conter um número suficiente de exemplos de ambas as categorias. Registros de manutenção e histórico de substituição de peças são boas fontes para encontrar eventos de falha. Com a ajuda de algum conhecimento de domínio, anomalias nos dados de treinamento também podem ser definidas como falhas.

#### <a name="maintenancerepair-history"></a>Histórico de manutenção/reparo
O histórico de manutenção de um ativo contém detalhes sobre os componentes substituídos, as atividades de reparo executadas etc. Esses eventos registram padrões de degradação. A ausência dessas informações cruciais nos dados de treinamento pode levar a resultados incorretos de modelo. O histórico de falha também pode ser encontrado no histórico de manutenção, como códigos de erro especial, ou datas de ordem para peças. Fontes de dados adicionais que influenciam os padrões de falha devem ser investigados e fornecidos por especialistas de domínio.

#### <a name="machine-operating-conditions"></a>Condições operacionais da máquina
Dados de fluxo de sensor com base (ou outros) do equipamento na operação são uma fonte de dados importantes. Uma suposição principal no PdM é que o status de integridade de um computador degrada ao longo do tempo durante sua operação de rotina. Portanto, esperamos que os dados contenham recursos com variação de tempo que capturem esse padrão de vencimento e anomalias que levam à degradação. O aspecto temporal dos dados é necessário para o algoritmo aprender a falha e padrões de não falha ao longo do tempo. Com base nesses pontos de dados, o algoritmo aprende prever quantos mais unidades de tempo que uma máquina pode continuar a trabalhar antes de falhar.

#### <a name="static-feature-data"></a>Dados de recursos estatísticos
Recursos estáticos são metadados sobre o equipamento. Os exemplos são a marca do equipamento, modelo, data fabricada, data de início do serviço, local do sistema e outras especificações técnicas.

Exemplos de dados relevantes para o [casos de uso do exemplo PdM](#sample-pdm-use-cases) estão tabulados abaixo:

| Caso de uso | Exemplos de dados relevantes |
|:---------|---------------------------|
|_Atraso e cancelamentos de voo_ | Informações de rota de voo na forma de segmentos de voo e logs de página. Os dados do segmento de voo incluem detalhes de roteamento, como data de partida/chegada, hora, aeroporto, layovers, etc. O log de página inclui uma série de códigos de erro e manutenção registrados pela equipe de manutenção de aterramento.|
|_Falha de pelas do motor da aeronave_ | Dados coletados de sensores em aeronave que fornece informações sobre a condição de várias peças. Registros de manutenção ajudam a identificar quando as falhas de componente ocorreram e quando foram substituídas.|
|_Falha do caixa eletrônico_ | Leituras de sensor para cada transação (depositando dinheiro/cheque) e entregando dinheiro. Informações sobre a medição de lacunas entre notas, espessura da nota, nota de chegada de observação, atributos de cheque, etc. Registros de manutenção que fornecem códigos de erro, informações de reparo, última vez que o dispensador de caixa foi recarregado.|
|_Falhas de turbinas eólicas_ | Sensores de turbina monitoram condições como temperatura, direção do vento, geração de energia, velocidade do gerador, etc. Os dados são coletados de várias turbinas de ventos dos farms de vento localizados em várias regiões. Normalmente, cada turbina terá várias leituras de sensor retransmitindo medidas em um intervalo de tempo fixo.|
|_Falhas de disjuntor_ | Logs de manutenção que incluem ações corretivas, preventivas e sistemáticas. Dados operacionais que incluem comandos automáticos e manuais enviados ao disjuntores como para ações de abertura e fechamento. Metadados de dispositivo, como data de fabricação, localização, modelo, etc. Especificações de disjuntor, como níveis de tensão, geolocalização, condições de ambiente.|
|_Falhas de porta de elevador_| Metadados de elevador como tipo de elevador, data fabricada, frequência de manutenção, tipo de construção e assim por diante. Informações operacionais como número de ciclos de porta, tempo médio de fechamento de porta. Histórico de falha com causas.|
|_Falhas de roda_ | Dados de sensor que medem a aceleração da roda, instâncias frenagem, distância de condução, velocidade etc. Informações estáticas em rodas como fabricante, data de fabricação. Dados da falha inferidos a partir do banco de dados que rastreia as datas e quantidades de solicitação.|
|_Falhas de porta do vagão do metrô_ | Abertura de porta e tempos de fechamento, outros dados operacionais, como a condição atual das portas do trem. Os dados estáticos incluiriam as colunas de identificador, hora e valor de condição.|

### <a name="data-types"></a>Tipos de dados
Considerando as fontes de dados acima, os dois tipos de dados principais observados no domínio PdM são:

- _Dados temporais_: telemetria operacional, condições de máquina, tipos de ordem de trabalho, códigos de prioridade que terão os carimbos de data/hora no momento da gravação. Falha, manutenção/reparo e histórico de uso também terão carimbos de data/hora associados a cada evento.
- _Dados estatísticos_: as propriedades da máquina e do operador em geral são estáticos, pois descrevem as especificações técnicas de máquinas ou propriedades do operador. Se esses recursos podem mudar ao longo do tempo, também devem ter os carimbos de data/hora associados a eles.

Variáveis de previsão e de destino devem ser pré-processados/transformados em [tipos de dados numéricos, categórica e outras](https://www.statsdirect.com/help/basics/measurement_scales.htm) dependendo do algoritmo que está sendo usado.

### <a name="data-preprocessing"></a>Pré-processamento de dados
Como um pré-requisito para _engenharia de recursos_, preparar os dados de vários fluxos para compor um esquema a partir do qual é fácil criar recursos. Visualize os dados pela primeira vez como uma tabela de registros. Cada linha na tabela representa uma instância de treinamento e as colunas representam recursos _preditores_ (também chamados de variáveis ou atributos independentes). Organizar os dados de forma que a última coluna seja o _destino_ (variável dependente). Para cada instância de treinamento, atribua um _rótulo_ como valor dessa coluna.

Para dados temporais, divida a duração dos dados de sensor em unidades de tempo. Cada registro deve pertencer a uma unidade de tempo para um ativo, _e deve oferecer informações distintas_. As unidades de tempo são definidas com base nas necessidades de negócios em múltiplos de segundos, minutos, horas, dias, meses, e assim por diante. A unidade de tempo _não precisa ser a mesmo que a frequência da coleta de dados_. Se a frequência for alta, os dados não podem mostrar nenhuma diferença significativa de uma unidade para o outra. Por exemplo, suponha que a temperatura ambiente foi coletada a cada 10 segundos. Usar o mesmo intervalo para os dados de treinamento apenas infla o número de exemplos sem fornecer informações adicionais. Nesse caso, uma estratégia melhor seria usar a média dos dados em 10 minutos ou uma hora com base na justificativa de negócios.

Para dados estáticos,
- _Registros de manutenção_: os dados brutos de manutenção tem um identificador de ativo e o carimbo de data/hora com informações sobre as atividades de manutenção que foram executadas em um determinado ponto no tempo. Transformar as atividades de manutenção em _colunas_ categóricas, onde cada descritor de categoria exclusivamente mapeia uma ação de manutenção específica. O esquema de dados básico para registros de manutenção incluiria colunas de ID de ativo, hora e ação de manutenção.

- _Registros de falha_: falhas ou motivos de falha podem ser registrados como definidos por condições de negócios específico de eventos de falha ou de códigos de erro específicos. Em casos onde o equipamento tem vários códigos de erro, a especialista de domínio deve ajudar a identificar aqueles que são relevantes para a variável de destino. Use as condições ou códigos de erro restantes para construir os recursos do _preditor_ que se correlacionam com essas falhas. O esquema de dados básico para registros de falha incluiria as colunas de ID do ativo, hora e falha ou motivo da falha, se o motivo estiver disponível.

- _Metadados de máquina e operador_: mesclar os dados de máquina e operador em um esquema para associar um ativo ao seu operador, junto com seus respectivos atributos. O esquema para condições da máquina incluirá identificador de ativo, recursos de ativo, identificador do operador e recursos do operador.

Outros etapas de pré-processamento de dados incluem _lidar com valores ausentes_ e _normalização_ de valores de atributo. Uma discussão detalhada está além do escopo deste guia - consulte a próxima seção para algumas referências úteis.

Com as fontes de dados pré-processados acima no local, as transformação final mediante a engenharia do recurso é unir as tabelas acima com base no identificador do ativo e carimbo de data/hora. A tabela resultante teria valores nulos para a coluna de falha quando a máquina estivesse em operação normal. Esses valores nulos podem ser inseridos por um indicador para operação normal. Use essa coluna de falha para criar _rótulos para o modelo de previsão_. Para obter mais informações, consulte a seção sobre a [modelagem técnica para manutenção preditiva](#modeling-techniques-for-predictive-maintenance).

## <a name="feature-engineering"></a>Engenharia de recursos
A engenharia de recursos é a primeira etapa antes da modelagem de dados. Sua função no processo de ciência de dados [é descrita aqui](./create-features.md). Um _recurso_ é um atributo de previsão para o modelo - como a temperatura, pressão, vibração e assim por diante. Para o PdM, a engenharia de recursos envolve a abstração da integridade de um computador sobre os dados históricos coletados durante uma duração ajustável. Nesse sentido, é diferente de seus colegas, como monitoramento remoto, detecção de anomalias e detecção de falha. 

### <a name="time-windows"></a>Janelas de tempo
O monitoramento remoto implica relatar os eventos que acontecem a partir dos _pontos no tempo_. Modelos de detecção de anomalias avaliam fluxos de entrada (pontuação) de dados para sinalizar anomalias a partir de pontos no tempo. A detecção de falha classifica as falha como tipos específicos conforme ocorrem pontos no tempo. Em contraste, a PdM envolve prever falhas em um _período futuro_, com base nos recursos que representam o comportamento da máquina em _período de tempo históricos_. Para PdM, os dados de recurso de pontos individuais de tempo são muito barulhentos para serem preditivos. Assim, os dados para cada recurso precisam ser _suavizados_ por pontos de dados de agregação em janelas de tempo.

### <a name="lag-features"></a>Recursos de retardo
Os requisitos de negócios definem quanto o modelo deve prever no futuro. Por sua vez, a duração ajuda a definir  “a distância de retrospectiva do modelo” para fazer essas previsões. Este período de “fazer retrospectiva” é chamado de _retardo_, e os recursos projetados nesse período de retardo são chamados de _recurso de retardo_. Esta seção discute os recursos de retardo que podem ser construídos a partir de fontes de dados com os carimbos de data/hora e a criação do recurso de fontes de dados estáticos. Recursos de retardo são normalmente _numéricos_ por natureza.

> [!IMPORTANT]
> O tamanho da janela é determinado por meio de experimentação e deve ser finalizado com a ajuda de uma especialista de domínio. A mesma advertência mantém a seleção e definição de recursos de latência, suas agregações e o tipo de janelas.

#### <a name="rolling-aggregates"></a>Agregações sem interrupção
Para cada registro de um ativo, uma janela sem interrupção de tamanho "W" é escolhida como o número de unidades de tempo para calcular as agregações. Os recursos de retardo são então computados usando os períodos W _antes da data_ do registro. Na Figura 1, as linhas azuis mostram valores de sensor registrados para um ativo para cada unidade de tempo. Indicam uma média dos valores de recurso em uma janela de tamanho W = 3. A média móvel é calculada em todos os registros com carimbos de data/hora no intervalo t<sub>1</sub> (em laranja) para t<sub>2</sub> (em verde). O valor para W é normalmente em minutos ou horas, dependendo da natureza dos dados. Mas para determinados problemas, escolhendo um grande W (digamos 12 meses) podem fornecer todo o histórico de um ativo até o momento do registro.

![Figura 1. Recursos de agregação sem interrupção](./media/predictive-maintenance-playbook/rolling-aggregate-features.png)

Figura 1. Recursos de agregação sem interrupção

Exemplos de agregações sem interrupção em uma janela de tempo são contagem, média, médias CUMESUM (soma acumulada), valores mín./máx. Além disso, a variação, desvio padrão e contagem de exceções além dos desvios de padrão N são geralmente usadas. Exemplos de agregações que podem ser aplicadas para os [casos de uso](#sample-pdm-use-cases) neste guia estão listados abaixo. 
- _Atraso de voo_: contagem de códigos de erro sobre o último dia/semana.
- _Falha na parte do mecanismo de aeronave_: média, desvio padrão e soma durante o dia anterior, semana, etc. Essa métrica deve ser determinada junto com o especialista em domínio de negócios.
- _Falhas de caixa eletrônico_: sem interrupção significa, mediana, intervalo, desvios padrão, a contagem de exceções, além dos três desvios, CUMESUM superior e inferior.
- _Falhas de porta do trem do metrô_: contagem de eventos anteriores do dia, semana, duas semanas etc.
- _Falhas de disjuntor_: contagens de falha na semana anterior, ano, três anos etc.

Outra técnica interessante é capturar alterações de tendência, picos e alterações de nível usando algoritmos que detectam anomalias nos dados usando algoritmos de detecção de anomalias.

#### <a name="tumbling-aggregates"></a>Agregações em cascata
Para cada registro rotulado de um ativo, uma janela de tamanho _w-<sub>k</sub>_ é definida, em que _k_ é o número de janelas de tamanho _w_. As agregações são então criadas sobre _k_ _em cascata Windows_ _W-k, w-<sub>(k-1)</sub>,..., w-<sub>2</sub>, w-<sub>1</sub>_ para os períodos antes do carimbo de data/hora de um registro. _k_ pode ser um número pequeno para capturar efeitos de curto prazo ou um número grande para capturar padrões de degração de longo prazo. (veja Figura 2).

![Figura 2. Recursos de agregação em cascata](./media/predictive-maintenance-playbook/tumbling-aggregate-features.png)

Figura 2. Recursos de agregação em cascata

Por exemplo, recursos de retardo para o caso de uso de turbinas eólicas podem ser criados com W = 1 e k = 3. Isso gera o retardo para cada um dos últimos três meses usando exceções superior e inferior.

### <a name="static-features"></a>Recursos estáticos

As especificações técnicas do equipamento, como data de fabricação, número de modelo, local são alguns dos exemplos de recursos estáticos. São tratados como variáveis _categóricas_ para modelagem. Alguns exemplos para o caso de uso do disjuntor são tensão, atual, capacidade de energia, tipo de transformador e fonte de energia. Para falhas de disco, o tipo de rodas do pneu (liga vs aço) é um exemplo.

Os esforços de preparação de dados discutidos até aqui devem gerar os dados sendo organizados conforme mostrado abaixo. Dados de treinamento, teste e validação devem ter esse esquema lógico (este exemplo mostra o tempo em unidades de dias).

| ID do ativo | Hora | \<Feature Columns> | Label |
| ---- | ---- | --- | --- |
| A123 |Dia 1 | . . . | . |
| A123 |Dia 2 | . . . | . |
| ...  |...   | . . . | . |
| B234 |Dia 1 | . . . | . |
| B234 |Dia 2 | . . . | . |
| ...  |...   | . . . | . |

A última etapa na engenharia de recursos é a **rotulação** da variável de destino. Este processo é dependente da técnica de modelagem. Por sua vez, a técnica de modelagem depende do problema de negócios e a natureza dos dados disponíveis. A rotulação é discutida na próxima seção.

> [!IMPORTANT]
> Preparação de dados e de engenharia de recursos são tão importantes quanto técnicas para chegar a soluções de PdM bem-sucedida de modelagem. O especialista de domínio e o profissional devem investir tempo em que chegam a recursos corretos e os dados para o modelo. Uma pequena amostra de muitos livros de engenharia de recurso abaixo:
> - Pyle, D. Data Preparation for Data Mining (The Morgan Kaufmann Series in Data Management Systems), 1999
> - Zheng, A., Casari, A. Feature Engineering for Machine Learning: Principles and Techniques for Data Scientists, O'Reilly, 2018.
> - Dong, G. Liu, H. (editores), engenharia de recursos para análise de dados e Machine Learning (Chapman & Hall/CRC e série de descoberta de conhecimento), CRC Press, 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>Técnicas de modelagem para manutenção preditiva

Esta seção discute as técnicas de modelagem principal para problemas de PdM, junto com seus métodos de construção de rótulo específico. Observe que uma técnica de modelagem única pode ser usada em diferentes setores. A técnica de modelagem é emparelhada ao problema de ciência de dados, em vez do contexto dos dados em questão.

> [!IMPORTANT]
> Escolha de rótulos para os casos de falha e a estratégia de rótulo  
> deve ser determinada em conjunto com o especialista de domínio.

### <a name="binary-classification"></a>Classificação binária
A classificação binária é usada para _prever a probabilidade de uma parte do equipamento falhar dentro de um período de tempo futuro_ , chamado de _período de horizonte futuro X_. X é determinado pelo problema de negócios e pelos dados em mãos, em consultoria com o especialista em domínio. São exemplos:
- _prazo de entrega mínimo_ necessário para substituir componentes, implantar recursos de manutenção, realizar a manutenção para evitar um problema que pode ocorrer nesse período.
- _a contagem mínima de eventos_ que pode acontecer antes que ocorra um problema.

Nessa técnica, os dois tipos de exemplos de treinamento são identificados. Um exemplo de positivo, _que indica uma falha_, com rótulo = 1. Um exemplo de positivo, que indica uma falha, com o rótulo = 1. A variável de destino, e, portanto, os valores de rótulo, são _categóricos_. O modelo deve identificar cada novo exemplo de como a probabilidade de falhar ou funcionar normalmente nas próximas X unidades de tempo.

#### <a name="label-construction-for-binary-classification"></a>Construção de rótulo para classificação binária
Aqui, a pergunta é: "qual é a probabilidade de o ativo falhar nas próximas X unidades de tempo?" Para responder essa pergunta, registros de rótulo X antes da falha de um ativo como "prestes a falhar" (rótulo = 1) e rótulo de todos os outros registros sendo “normais” (rótulo = 0). (veja a Figura 3).

![Figura 3. Rotulação para classificação binária](./media/predictive-maintenance-playbook/labelling-for-binary-classification.png)

Figura 3. Rotulação para classificação binária

Exemplos de estratégia de rotulagem para alguns dos casos de uso estão listados abaixo.
- _Atrasos de voo_: X podem ser escolhidos como um dia, para prever atrasos nas próximas 24 horas. Então todos os voos que estão dentro do prazo de 24 horas antes das falhas foram rotulados como 1s.
- _Falhas de caixa eletrônico_: pode ser uma meta para determinar a probabilidade de falha de uma transação na próxima hora. Nesse caso, todas as transações que aconteceram na última depois da falha são rotuladas como 1. Para prever a probabilidade sobre as próximas notas de moeda N liberada, todas as notas liberadas dentro das últimas notas N de uma falha são rotuladas como 1.
- _Falhas de disjuntor_:  o objetivo pode ser prever a próxima falha do comando disjuntor. Nesse caso, X é escolhido para ser um comando futuro.
- _Falhas da porta do trem_: X pode ser escolhido como dois dias.
- _Falhas de turbina eólica_: X pode ser escolhido como dois meses.

### <a name="regression-for-predictive-maintenance"></a>Regressão para a manutenção preditiva
Os modelos de regressão são usados para _computar a vida útil restante (RUL) de um ativo_. RUL é definida como a quantidade de tempo que um ativo está funcionando antes que ocorra a próxima falha. Cada exemplo de treinamento é um registro que pertence a uma unidade de tempo _nY_ para um ativo, onde _n_ é o múltiplo. O modelo deve calcular a regra de cada novo exemplo como um _número contínuo_. Esse número indica o período de tempo restante antes da falha.

#### <a name="label-construction-for-regression"></a>Construção de rótulo de regressão
A pergunta aqui é: "Qual é o tempo de vida útil (RUL) restante do equipamento? Para cada registro antes da falha, calcule o rótulo para o número de unidades de tempo restante antes da próxima falha. Nesse método, os rótulos são variáveis contínua. (veja a Figura 4)

![Figura 4. Rotulação para a regressão](./media/predictive-maintenance-playbook/labelling-for-regression.png)

Figura 4. Rotulação para a regressão

Para regressão, a rotulação é feita com referência a um ponto de falha. O cálculo não é possível sem saber quanto tempo o ativo sobreviveu antes de uma falha. Portanto, em contraste com a classificação binária, ativos sem falhas nos dados não podem ser usados para modelagem. Esse problema é mais bem resolvido por outra técnica estatística chamada [Análise de Sobrevivência](https://en.wikipedia.org/wiki/Survival_analysis). Mas a possíveis complicações que podem surgir ao aplicar essa técnica para casos de uso PdM que envolvem dados variáveis de tempo com intervalos de frequência. Para obter mais informações sobre Análise de sobrevivência, consulte [de uma página](https://www.cscu.cornell.edu/news/news.php/stnews78.pdf).

### <a name="multi-class-classification-for-predictive-maintenance"></a>Classificação multiclasse para a manutenção preditiva
Técnicas de classificação de várias classes podem ser usadas em soluções PdM para dois cenários:
- Prever _dois resultados futuros_: O primeiro resultado é _um intervalo de tempo para falha_ de um ativo. O ativo é atribuído a um dos várias possíveis príodos de tempo. A segunda é identificar a probabilidade de falha em um período futuro devido a _uma das várias causas raízes_. Essa previsão permite que a equipe de manutenção observar os sintomas e agendas de plano de manutenção.
- Prever _a causa mais provável_ de uma determinada falha. Esse resultado recomenda o conjunto correto de ações de manutenção para corrigir uma falha. Uma lista classificada de causas raiz e reparos recomendados pode ajudar os técnicos a priorizar suas ações de reparo após uma falha.

#### <a name="label-construction-for-multi-class-classification"></a>Construção de rótulo para classificação binária
Aqui, a pergunta é: "Qual é a probabilidade de o ativo falhar nas próximas X unidades de tempo _nZ_ onde _n_ é o número de períodos?” Para responder essa pergunta, os registros nZ do rótulo antes da falha de um ativo usando buckets de tempo (3Z, 2Z, Z). Rotule todos os outros registros como "normal" (rótulo = 0). Nesse método, a variável de destino contém _valores_ categóricos. (veja a Figura 5).

![Figura 5. Rótulos de previsão de tempo de falha para classificação multiclasse](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-failure-time-prediction.png)

Figura 5. Rotulação da classificação multiclasse para a previsão da hora da falha

Aqui, a pergunta é: "qual é a probabilidade de o ativo falhar nas próximas X unidades de tempo? devido à causa raiz/problema _P <sub>i</sub>_?" onde _i_ é o número de causas raízes possíveis. Para responder essa pergunta, rótulo X registros antes da falha de um ativo como “prestes a falhar devido à causa raiz _P <sub>i</sub>_" (rótulo = _P <sub>i</sub>_). Rotule todos os outros registros como "normal" (rótulo = 0). Nesse método, os rótulos são categóricos (veja a Figura 6).

![Figura 6. Rótulos de previsão de causa raiz para classificação multiclasse](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-root-cause-prediction.png)

Figura 6. Rotulação da classificação multiclasse para a previsão da causa-raiz

O modelo atribui uma probabilidade de falha devido a cada _P <sub>i</sub>_ e também uma probabilidade de não falha. Essas probabilidades podem ser ordenadas por magnitude para permitir a previsão dos problemas que têm maior probabilidade de ocorrer no futuro.

Aqui, a pergunta é: "quais ações de manutenção você recomenda após um falha?” Para responder essa pergunta, a rotulação _não requer que um horizonte futuro seja selecionado_, porque o modelo não está prevendo falha no futuro. Ele é apenas previsão mais provável de causa raiz _depois que a falha já aconteceu_.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Métodos de treinamento, validação e teste para manutenção preditiva
O [Processo de Ciência de Dados de Equipe](./overview.md) fornece uma cobertura completa do ciclo de validação de teste de treinamento de modelo. Esta seção discute aspectos exclusivos de PdM.

### <a name="cross-validation"></a>Validação cruzada
O objetivo da [validação cruzada](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) é definir um conjunto de dados para "testar" o modelo na fase de treinamento. Esse conjunto de dados é chamado de _validação de conjunto_. Essa técnica ajuda a limitar problemas como _sobreajuste_ e fornece um insight sobre como o modelo irá generalizar a um conjunto de ddos independente. Ou seja, um conjunto de dados descibgecudi que pode ser um problema real. A rotina de treinamento e teste para PdM precisa levar em conta os aspectos de tempo diferentes para generalizar melhor sobre dados futuros despercebidos.

Muitos algoritmos de aprendizado de máquina dependem de um número de hiperparâmetros que pode alterar significativamente o desempenho do modelo. Os valores ideais desses hiperparâmetros não são calculados automaticamente durante o treinamento do modelo.  Eles devem ser especificados pelo cientista de dados. Há várias maneiras de localizar bons valores de hiperparâmetros.

O mais comum é a _validação cruzada k vezes_, que divide os exemplos aleatoriamente em _k_ dobras. Para cada conjunto de valores de hiperparâmetros, o algoritmo de aprendizado é executado _k_ vezes. Em cada iteração, os exemplos na dobra atual são usados como um conjunto de validação; o restante dos exemplos é usado como um conjunto de treinamento. Treine o algoritmo com exemplos de treinamento e as métricas de desempenho são calculadas sobre exemplos de validação. No final desse loop, calcule a média de métricas de desempenho _k_. Para cada conjunto de valores de hiperparâmetro, escolha aqueles que têm o melhor desempenho médio. A tarefa de escolher hiperparâmetros geralmente é experimental por natureza.

Nos problemas PdM, os dados s/ao gravados como série de tempo de eventos que vêm de várias fontes de dados. Esses registros podem ser classificados de acordo com a hora de rotulação. Portanto, se o banco de dados é dividido _aleatoriamente_ em conjunto de treino e validação, _alguns dos exemplos de treno podem estar posteriores no tempo do que alguns exemplos de validação_. Futuro desempenho de valores de hiperparâmetro baseados nos dados que chegaram _antes_ de o modelo ser treinado. Essas estimativas podem ser muito otimistas, especialmente se a série temporal não for estacionária e evolui ao longo do tempo. Como resultado, os valores de hiperparâmetro escolhidos podem estar abaixo do ideal.

A maneira recomendada é dividir os exemplos em treinamento e conjunto de validação definida de maneira _dependentes de tempo_, onde todos os exemplos de validação são posteriores no tempo de todos os exemplos de treinamento. Para cada conjunto de valores de hiperparâmetro, treine o algoritmo sobre o conjunto de dados de treinamento. Meça o desempenho do modelo no mesmo conjunto de validação. Escolha valores de hiperparâmetro que mostram o melhor desempenho. Valores de hiperparâmetro escolhidos por divisão de treinamento/validação resultam em desempenho de modelo do futuro do que com os valores escolhidos aleatoriamente por validação cruzada.

O modelo final pode ser gerado treinando um algoritmo de aprendizado sobre dados de treinamento inteiros usando os melhores valores de hiperparâmetros.

### <a name="testing-for-model-performance"></a>Testes para o desempenho do modelo
Depois que um modelo é criado, uma estimativa de seu desempenho de futuro em novos dados é necessária. Uma boa estimativa poderia ser a métrica de desempenho de valores de hiperparâmetro calculados sobre o conjunto de validação ou média da métrica de desempenho calculada com validação cruzada. Essas estimativas geralmente são extremamente otimistas. A empresa geralmente pode ter algumas diretrizes adicionais sobre como eles gostariam de testar o modelo.

A maneira recomendada para PdM é dividir os exemplos de treinamento, validação, e conjuntos de dados de teste de maneira _dependentes de tempo_. Todos os exemplos de teste devem ser posteriores no tempo de todos os exemplos de treinamento e validação. Depois da divisão, gerar o modelo e medir o desempenho conforme descrito anteriormente.

Quando a série temporal é estática e fácil de prever, ambas abordagens aleatórias e dependentes de tempo semelhante a estimativas de desempenho futuro. Mas quando a série temporal é dinâmica e/ou difícil de prever, a abordagem dependente de tempo irá gerar mais estimativas realísticas de desempenho futuro.

### <a name="time-dependent-split"></a>Divisão dependente do tempo
Esta seção descreve as práticas recomendadas para implementar a divisão dependente de tempo. Uma divisão bidirecional dependente de tempo entre o treinamento e conjuntos de teste está descrita abaixo.

Suponha que tenhamos um fluxo de eventos com carimbo de data/hora, como medidas de vários sensores. Define os recursos e rótulos de treinamento e exemplos de teste sobre os períodos que contenham vários eventos. Por exemplo, para classificação binária, crie recursos com base em eventos passados e crie rótulos com base em eventos futuros dentro das unidades de tempo no futuro (consulte as seções sobre Engenharia de [recursos](#feature-engineering) e técnicas de modelagem). Assim, o período de tempo de rotulamento de um exemplo ocorre depois do período de tempo de seus recursos.

Para divisão dependente de tempo, escolhemos um _tempo de corte de treinamento T <sub>c</sub>_  em que treinar um modelo, com hiperparâmetros ajustados usando dados históricos T <sub>c</sub>. Para impedir o vazamento de rótulos futuros que são além de T<sub>c</sub> nos dados de treinamento, escolha a hora mais recente para rotular exemplos de treinamento como unidades X antes de T<sub>c</sub>. No exemplo mostrado na Figura 7, cada quadrado representa um registro no conjunto de dados onde os recursos e os rótulos são computados conforme descrito acima. A figura mostra os registros que deveriam ir para conjuntos de treinamento e testes para  X=2 e W=3:

![ Figura 7. Divisão dependente do tempo para classificação binária](./media/predictive-maintenance-playbook/time-dependent-split-for-binary-classification.png)

 Figura 7. Divisão dependente do tempo para classificação binária

Os quadrados verdes representam os registros que pertencem às unidades de tempo que podem ser usadas para treinamento. Cada exemplo de treinamento é gerado, levando em consideração os últimos três pontos para a geração de recurso e dois períodos futuros para rotular antes de T<sub>c</sub>. Quando qualquer parte dos dois períodos futuros está além de T<sub>c</sub>, excluir esse exemplo do conjunto de dados de treinamento porque nenhuma visibilidade é assumida além de T<sub>c</sub>.

Os quadrados pretos representam os registros do conjunto de dados rotulados que não devem ser usados no conjunto de dados de treinamento, dada a restrição acima. Esses registros também não serão usados no teste de dados, desde que eles sejam antes de T<sub>c</sub>. Além disso, seus intervalos de rótulos parcialmente dependem do período de tempo de treinamento, que não é ideal. Treinamento e dados de teste devem ter separado período de rotulação para evitar vazamento de informação de rótulo.

A técnica discutida até aqui permite sobreposição entre treinamento e exemplos de teste que tem carimbos de data/hora próximo a T<sub>c</sub>. Uma solução para alcançar maior separação é excluir exemplos que estão em unidades de tempo W de T<sub>c</sub> do conjunto de teste. Mas essa divisão agressiva depende da disponibilidade dados ampla.

Modelos de regressão usados para prever a RUL mais graves são afetados pelo problema de vazamento. Usar o método de divisão aleatória leva o sobreajuste extremo. Para problemas de regressão, a divisão deve ser, de modo que os registros pertençam a ativos com falhas antes de T<sub>c</sub> ir no conjunto de treinamento. Registros de ativos que têm falhas após o corte para ir para o conjunto de teste.

Outra prática recomendada para dividir os dados para treinamento e teste é usar uma divisão por ID de ativo. A divisão deve ser, de modo que nenhum dos ativos usados no conjunto de treinamento sejam usados em testes de desempenho do modelo. Usando essa abordagem, um modelo tem mais chances de resultados mais realistas, fornecendo novos ativos.

### <a name="handling-imbalanced-data"></a>Tratamento dos dados em desequilíbrio
Em problemas de classificação, se houver mais exemplos de uma classe que de outras, o conjunto de dados será considerado em _desequilíbrio_. Normalmente, representantes suficientes de cada classe nos dados de treinamento para podermos diferenciar entre classes diferentes. Se uma classe for menor que 10% dos dados, os dados são considerados para não estar balanceado. A classe sub-representada é chamada de _classe minoritária_.

Muitos problemas PdM enfrentam tais conjuntos de dados desequilibrados, em que uma classe severa esteja sub-representada em comparação  a outra classe, ou classes. Em algumas situações, a classe minoritária pode constituir somente 0,001% do total de pontos de dados. Desequilíbrio da classe não é exclusivo para PdM. Outros domínios em que falhas e as anomalias são raras ocorrências que enfrentam um problema semelhante, para obter exemplos, detecção de fraudes e invasão da rede. Essas falhas compõem os exemplos de classe minoritária.

No caso de desequilíbrio de classe, o desempenho da maioria dos algoritmos de aprendizado padrão fica comprometido, já que eles tentam minimizar a taxa de erro geral. Para um conjunto de dados com exemplos positivo de % 1 e 99% negativo, um modelo pode ser mostrado com precisão de 99% rotulando todas as instâncias como negativo. Mas o modelo será classificado incorretamente todos os exemplos positivos; portanto, mesmo se a precisão for alta, o algoritmo não é útil. Consequentemente, as métricas de avaliação convencional, como _a precisão geral na taxa de erro_, não são suficientes em caso de aprendizado em desequilíbrio. Ao se deparar com conjuntos de dados desequilibrados, outras métricas são usadas para avaliação do modelo:
- Precisão
- Recall
- Pontuações F1
- Custo ajustado ROC (características operacionais do destinatário)

Para obter mais informações sobre essas métricas, consulte [avaliação de modelo](#model-evaluation).

No entanto, existem alguns métodos que ajudam a solucionar o problema de desequilíbrio de classe. As duas principais são _técnicas de amostragem_ e _aprendizado sensível ao custo_.

#### <a name="sampling-methods"></a>Métodos de amostragem
O aprendizado desequilibrado envolve o uso de métodos para modificar o conjunto de dados de treinamento para um conjunto de dados equilibrado de amostragem. Os métodos de amostragem não devem ser aplicadas ao conjunto de teste. Embora existam muitas técnicas diferentes de amostragem, as mais simples são _sobreamostragem_ e _subamostragem aleatórias_.

_Resumindo, a sobreamostragem_ envolve selecionar ma amostra aleatória da classe de minoria, replicando esses exemplos e adicionando-as ao conjunto de dados de treinamento. Consequemente, isso aumenta o número total de exemplos na classe minoritária e eventualmente equilibra o número de exemplos de classes diferentes. Um risco de sobreamostragem é que várias instâncias de determinados exemplos podem levar o classificador a se tornar muito específico, causando um sobreajuste. O modelo pode mostrar a precisão de treinamento alta, mas seu desempenho em dados de teste nunca vistos pode estar abaixo do ideal.

Por outro lado, a _subamostragem aleatória_ é a seleção de amostra aleatória da classe majoritária e a remoção dos exemplos do conjunto de dados de treinamento. No entanto, a remoção de exemplos de classe majoritária pode levar o classificador a perder conceitos importantes referentes à classe majoritária. _A amostragem híbrida_, em que a classe minoritária tem mais amostras e a classe majoritária tem menos, é outra abordagem viável.

Há várias técnicas de amostragem sofisticadas. A técnica escolhida depende das propriedades de dados e os resultados de experimentos iterativos pelo cientista de dados.

#### <a name="cost-sensitive-learning"></a>Aprendizagem sensível ao custo
Na PdM, falhas que constituem a classe minoritária são mais relevantes que exemplos normais. Portanto, o foco está principalmente no desempenho do algoritmo em caso de falha. Prever incorretamente uma classe positiva como uma classe negativa pode ter custo maior do que o contrário. Essa situação é conhecida como perda diferente ou custo assimétrico de elementos de classificação incorreta para classes diferentes. O classificador ideal deve ser capaz de fornecer alta precisão de previsão sobre a classe minoritária, sem comprometer a precisão para a classe majoritária.

Há várias maneiras de alcançar esse equilíbrio. Para mitigar o problema de perda desigual, atribua um custo de classificação incorreta de classe minoritária e tente minimizar o custo geral. Algoritmos como _SVMs (suporte a máquinas de vetor)_ adotam esse método inerentemente, permitindo que o custo dos exemplos positivos e negativos para ser especificado durante o treinamento. Da mesma forma, métodos de estímulo como _árvores de decisão impulsionada_ geralmente mostram um bom desempenho com dados desequilibrados.

## <a name="model-evaluation"></a>Avaliação de modelos
A classificação incorreta é um problema significativo para cenários PdM em que o custo de alarmes falsos para os negócios é alto. Por exemplo, a decisão de um avião com base em uma previsão incorreta de falha do mecanismo de plano de fundo pode interromper agendamentos e planos de viagem. Colocar uma máquina offline em uma linha de montagem pode levar à perda de receita. Portanto a avaliação do modelo com as métricas de desempenho à direita em relação aos novos dados de teste é fundamental.

Métricas de desempenho comuns usadas para avaliar modelos PdM são discutidas abaixo:

- A [precisão](https://en.wikipedia.org/wiki/Accuracy_and_precision) é a métrica mais popular usada para descrever o desempenho de um classificador. Mas a precisão é sensível a distribuições de dados e é uma medida ineficaz para cenários com conjuntos de dados desequilibrados. Outras métricas são usadas em vez disso. Ferramentas como o [matriz de confusão](https://en.wikipedia.org/wiki/Confusion_matrix) são usadas para calcular e argumentar sobre a precisão do modelo.
- [Precisão](https://en.wikipedia.org/wiki/Precision_and_recall) de modelos PdM se relacionam com a taxa de alarmes falsos. Menor precisão do modelo geralmente corresponde a uma taxa mais alta de alarmes falsos.
- [Taxa de recall](https://en.wikipedia.org/wiki/Precision_and_recall) denotam quantas falhas no conjunto de teste foram identificadas corretamente pelo modelo. Taxas mais altas de recuperação significam que o modelo foi bem-sucedido em capturar as falhas reais.
- [Pontuação de F1](https://en.wikipedia.org/wiki/F1_score) é a média harmônica de precisão e a recuperação, com seu valor entre 0 (pior) como 1 (melhor).

Para classificação binária,
- [Curvas operacionais do receptor (ROC)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) também é uma métrica popular. Nas curvas ROC, o desempenho do modelo é interpretado com base em um ponto fixo operacional no ROC.
- Mas para problemas de PdM _tabelas de rota_ e _gráficos de comparação de precisão_ são mais informativos. Eles se concentram apenas em classe positiva (falhas) e fornecem uma imagem mais complexa do desempenho do algoritmo de curvas ROC.
  - _Tabelas de rota_ são criadas usando os exemplos de teste em ordem decrescente de probabilidades de falha. Os exemplos ordenados são agrupados em decis (ou seja, os exemplos de 10% com maior probabilidade e, em seguida, 20%, 30% e assim por diante). A taxa (taxa positiva verdadeira) /(linha de base aleatório) para cada decile ajuda a estimar o desempenho do algoritmo em cada decile. A linha de base aleatória assume valores 0.1, 0.2 e assim por diante.
  - [Gráficos de comparação de precisão](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) são a taxa positiva verdadeira decile versus a taxa positive verdadeira aleatória pra todos os deciles. Os primeiros decis são o foco dos resultados, já que é onde vemos os maiores ganhos. Os primeiros decis também podem ser vistos como representativos para "em risco" quando usados para PdM.

## <a name="model-operationalization-for-predictive-maintenance"></a>Operacionalização do modelo para manutenção preventiva

O benefício é o exercício de ciência de dados realizado somente quando o modelo treinado for operacional. Ou seja, o modelo deve ser implantado nos sistemas dos negócios para fazer previsões com base em dados não vistos anteriormente e novos.  Os novos dados devem seguir exatamente a _assinatura modelo_ do modelo treinado de duas maneiras:
- todos os recursos devem estar presentes em cada instância lógica (digamos que uma linha em uma tabela) dos novos dados.
- os novos dados devem ser processados previamente, e cada um dos recursos de engenharia, exatamente da mesma maneira como os dados de treinamento.

O processo acima é declarado de muitas maneiras na literatura acadêmica e do setor. Mas todas as instruções a seguir têm o mesmo significado:
- _Pontuar novos dados_ usando o modelo
- _Aplicar o modelo_ aos novos dados
- Colocar o modelo em _operação_
- _Implantar_ o modelo
- _Executar o modelo_ em dados novos

Conforme mencionado anteriormente, a operacionalização de modelo para PdM é diferente de seus colegas. Cenários envolvendo detecção de anomalias e detecção de falha normalmente implementa _pontuação online_ (também chamada de _em pontuação em tempo real_). Aqui, o modelo _pontua_ cada registro de entrada e retorna uma previsão. Para detecção de anomalias, a previsão é uma indicação de que ocorreu uma anomalia (exemplo: uma classe SVM). Para detecção de falha, seria o tipo ou classe de falha.

Em contraparte, a PdM envolve _a pontuação em lote_. Para estar de acordo com a assinatura do modelo, os recursos dos novos dados devem ser feitos da mesma maneira como os dados de treinamento. Para grandes conjuntos de dados que são típicos para novos dados, os recursos são agregados em janelas de tempo e pontuação em lote. A pontuação em lote normalmente é feita em sistemas distribuídos como [Spark](https://spark.apache.org/) ou [lote do Microsoft Azure](../../batch/batch-service-workflow-features.md). Há duas alternativas - ambos de qualidade inferior:
- Mecanismos de fluxo de dados oferecem suporte a agregação em janelas na memória. Portanto, poderia argumentar que oferecem suporte a pontuação online. Mas esses sistemas são adequados para dados densos nas janelas estreitas de tempo ou  elementos esparsos em janelas mais ampla. Podem não ser dimensionadas bem para os dados densos sobre janelas de tempo maiores, como visto nos cenários de PdM.
- Se a pontuação do lote não estiver disponível, a solução é adaptar a pontuação online para lidar com novos dados em lotes pequenos por vez.

## <a name="solution-templates-for-predictive-maintenance"></a>Modelos de solução para manutenção preventiva

A seção final deste guia fornece uma lista de modelos de solução PdM, tutoriais e experiências implementadas no Azure. Esses aplicativos PdM podem ser implantados em uma assinatura do Azure em minutos em alguns casos. Eles podem ser usados como prova de conceito demonstrações, as áreas restritas para fazer experiências com alternativas ou aceleradores para implementações de produção real. Esses modelos estão localizados na [Galeria dde IA do Azure](https://gallery.azure.ai) ou [Azure GitHub](https://github.com/Azure). Esses exemplos diferentes serão transferidos para esse modelo de solução ao longo do tempo.

| # | Título | Descrição |
|--:|:------|-------------|
| 2 | [Modelo da Solução de Manutenção Preditiva do Azure](https://github.com/Azure/AI-PredictiveMaintenance) | Um modelo de solução de software livre que demonstra a modelagem do Azure ML e uma infraestrutura do Azure completa capaz de dar suporte a cenários de manutenção preditiva no contexto do monitoramento remoto de IoT. |
| 3 | [Aprendizado Profundo da Manutenção Preditiva](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Azure Notebook com solução de demonstração do uso de redes LSTM (memória de prazo curto-longo) (uma classe de redes neurais recorrente) para manutenção preditiva, com um [postagem de blog sobre esse exemplo](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [Manutenção preditiva do Azure para o setor aeroespacial](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | Um dos modelos de solução de PdM primeiro com base na versão 1.0 do ML do Azure para manutenção de aeronave. Este guia que se originou desse projeto. |
| 5 | [Kit de Ferramentas de IA para o Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge) | IA no IoT Edge usando TensorFlow; o kit de ferramentas empacota modelos de aprendizado profundo em contêineres do Docker compatíveis com Azure IoT Edge e expõe esses modelos como APIs REST.
| 6 | [Azure IoT – Manutenção preditiva](https://github.com/Azure/azure-iot-predictive-maintenance) | Azure IoT Suite PCS - Solução pré-configurada Modelo de PdM de manutenção de aeronave com IoT Suite. [Outro documento](/previous-versions/azure/iot-accelerators/about-iot-accelerators) e [passo a passo](/previous-versions/azure/iot-accelerators/iot-accelerators-predictive-walkthrough) relacionados ao mesmo projeto. |
| 7 | [Modelo de manutenção preditiva usando os Serviços R do SQL](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Demonstração do cenário de vida útil restantes com base nos serviços R. |
| 8 | [Guia de modelagem de manutenção preditiva](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | Recurso de conjunto de dados de manutenção de aeronave projetada  usando o R com [experiências](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) e [conjuntos de dados](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) e Azure Notebook e [experiências](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2)no AzureML v1.0.|

## <a name="training-resources-for-predictive-maintenance"></a>Recursos de treinamento para manutenção preditiva

O Microsoft Azure oferece roteiros de aprendizagem para os conceitos fundamentais por trás das técnicas de PdM, além de conteúdo e dá treinamento sobre conceitos e práticas gerais de inteligência artificial.

| Recursos de treinamento  | Disponibilidade |
|:-------------------|--------------|
| [Roteiro de aprendizagem do PdM usando árvores e florestas aleatórias](https://aischool.microsoft.com/learning-paths/1H5vH5wAYcAy88CoQWQcA8) | Público | 
| [Roteiro de aprendizagem do PdM usando Deep Learning](https://aischool.microsoft.com/learning-paths/FSIXxYkOGcauo0eUO8qAS) | Público |
| [Desenvolvedor de AI no Azure](https://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Público |
| [Microsoft AI School](https://aischool.microsoft.com/learning-paths) | Público |
| [Aprendizado do Azure AI do GitHub](https://github.com/Azure/connectthedots/blob/master/readme.md) | Público |
| [LinkedIn Learning](https://www.linkedin.com/learning) | Público |
| [Microsoft AI YouTube webinars](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Público |
| [Microsoft AI Show](https://channel9.msdn.com/Shows/AI-Show) | Público |
| [LearnAI@MS](https://learnanalytics.microsoft.com) | Parceiros |
| [Microsoft Partner Network](https://partner.microsoft.com/training/training-center) | Parceiros |

Além disso, MOOCS livres (cursos online abertos massivos) em AI estão online oferecidos por instituições acadêmicas como Stanford e MIT e outras empresas educacionais.