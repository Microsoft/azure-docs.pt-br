---
title: "Introdução à Preparação de Dados para Azure Machine Learning | Microsoft Docs"
description: "Este é o guia de introdução para a seção de preparação de dados do AML Workbench"
author: cforbe
ms.author: cforbe
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: faf6e557914a33454229b5830419b9ef6f200a8c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-data-preparation"></a>Introdução à preparação de dados

Bem-vindo ao guia de introdução à Preparação de Dados. 

A Preparação de Dados fornece um conjunto de ferramentas para explorar com eficiência, entender e corrigir problemas nos dados. Ela permite que você consuma dados de várias formas e transforme os dados em dados limpos que são mais adequados para o uso de downstream.

A Preparação de Dados é instalada como parte da experiência do Azure Machine Learning Workbench.  Use a Preparação de Dados localmente ou implante em uma nuvem e um cluster de destino como um tempo de execução ou ambiente de execução.

O tempo de execução de tempo de design usa Python para extensibilidade e depende de várias bibliotecas do Python como Pandas. Como com outros componentes do Azure ML Workbench, não é necessário instalar o Python, ele é instalado para você. No entanto se você precisar instalar bibliotecas adicionais, elas precisam ser instaladas no diretório do Python do Azure ML Workbench, não em seu diretório do Python normal. Mais detalhes sobre como instalar os pacotes podem ser encontrados [aqui](data-prep-python-extensibility-overview.md).

Um projeto é necessário antes de você poder usar a Preparação de Dados. Após a criação do projeto, você pode preparar os dados. 

Navegue até a seção Dados do projeto selecionando o ícone de Dados ![ícone de fonte de dados](media/data-prep-getting-started/data-source-icon.png) à esquerda da tela.  Clique em "+" novamente para **Adicionar uma Fonte de Dados**. O Assistente de Fonte de Dados deve iniciar e adiciona um arquivo de **Fonte de Dados** (.dsource) ao projeto depois de concluir o assistente. Por padrão, a exibição dos dados é a grade. Acima da grade, também é possível selecionar a exibição Métricas. Na exibição Métricas, são mostradas estatísticas de resumo.  Depois de examinar as estatísticas de resumo, clique em **Preparar** na parte superior da tela. [Mais informações sobre o Assistente de Fonte de Dados](data-source-wizard.md) 

## <a name="building-blocks-of-data-preparation"></a>Blocos de construção da preparação de dados ##
### <a name="the-package"></a>O pacote ###

Um pacote é o contêiner primário para seu trabalho. Um pacote é o artefato que é salvo e carregado do disco. Enquanto estiver trabalhando dentro do cliente, o pacote é constantemente salvo automaticamente em segundo plano. 

Os resultados/saída de um pacote podem ser explorados em Python ou por meio de um Notebook Jupyter.

Um pacote pode ser executado em vários tempos de execução, incluindo o Python, o Spark (inclusive no Docker) e HDInsight locais.

Um pacote contém um ou mais fluxos de dados que são as etapas e transformações aplicadas aos dados.

Um pacote pode usar outro pacote como uma fonte de dados (conhecido como um fluxo de dados de referência).

### <a name="the-dataflow"></a>O fluxo de dados ###
Um fluxo de dados tem uma fonte e transformações opcionais que são organizadas por meio de uma série de etapas e destinos opcionais. Clicar em uma etapa executa novamente todas as fontes e transformações antes e incluindo a etapa selecionada.  Os dados transformados por essa etapa são mostrados dentro da grade. As etapas podem ser adicionadas, movidas e excluídas dentro de um fluxo de dados através da lista de etapas.

A lista de etapas no lado direito do cliente pode ser aberta e fechada para fornecer mais espaço na tela.

Podem existir vários fluxos de dados na interface do usuário de cada vez, cada fluxo de dados é representado como uma guia na interface do usuário.

### <a name="the-source"></a>A fonte
Uma fonte é de onde os dados vêm e o formato em que estão. Um pacote de preparação de dados origina seus dados de outro fluxo de dados (fonte de dados). É esse Fluxo de Dados de Referência que contém as informações. Cada fonte tem uma experiência de usuário diferente para permitir que ela seja configurada. A origem produz uma exibição tabular/"retangular" dos dados. Se os dados de origem eram originalmente “irregulares à direita”, a estrutura é normalizada para ser “retangular”. [O Apêndice 2 fornece a lista atual de fontes com suporte](data-prep-appendix2-supported-data-sources.md).

### <a name="the-transform"></a>A transformação ###
As transformações consomem dados em um determinado formato, executam alguma operação nos dados (como alterar o tipo de dados) e, em seguida, geram dados no novo formato. Cada transformação tem sua própria interface do usuário e comportamento. Encadear várias transformações por meio das etapas no fluxo de dados é o cerne da funcionalidade de preparação de dados. [O Apêndice 3 fornece a lista atual das transformações com suporte](data-prep-appendix3-supported-transforms.md).

### <a name="the-inspector"></a>O inspetor ###

Os inspetores são visualizações dos dados e estão disponíveis para melhorar o entendimento dos dados.  Entender os dados e os problemas de qualidade de dados ajuda você a decidir quais ações (transformações) devem ser realizadas. Alguns inspetores dão suporte a ações que geram transformações. Por exemplo, o inspetor de contagem de valor permite que você selecione um valor e, em seguida, aplique um filtro para incluir ou excluir esse valor. Os inspetores também podem fornecer contexto para transformações. Por exemplo, selecionar uma ou mais colunas altera as transformações possíveis que podem ser aplicadas.

Uma coluna pode ter vários inspetores em qualquer ponto no tempo (por exemplo, as estatísticas de coluna e um histograma). Também pode haver instâncias de um inspetor em várias colunas. Por exemplo, todas as colunas numéricas podem ter histogramas ao mesmo tempo.

Os inspetores aparecem na Fonte de Criação de Perfil na parte inferior da tela.  Maximize os inspetores para vê-los maiores dentro da área de conteúdo principal. Considere a grade de dados como o inspetor de padrão. Qualquer inspetor pode ser expandido para a área de conteúdo principal. Os inspetores na área de conteúdo principal são minimizados na Fonte de Criação de Perfil. Personalize um inspetor clicando no ícone de lápis no inspetor. Reordene os inspetores dentro da Fonte usando o recurso de arrastar e soltar.

Alguns inspetores de têm suporte ao modo “Halo”. Esse modo mostra o valor ou o estado antes de a última transformação ter sido aplicada. O valor antigo é exibido em cinza com o valor atual em primeiro plano e mostra o impacto de uma transformação. [O Apêndice 4 fornece a lista atual dos inspetores com suporte](data-prep-appendix4-supported-inspectors.md).

### <a name="the-destination"></a>O destino
 Um destino é para onde você grava/exporta os dados depois de prepará-los em um fluxo de dados. Um determinado fluxo de dados pode ter vários destinos. Cada destino tem uma experiência de usuário diferente para permitir que ela seja configurada. O destino consome dados em um formato "retangular"/tabular e os grava em um local em um determinado formato. [O Apêndice 5 fornece a lista atual dos destinos com suporte](data-prep-appendix5-supported-destinations.md).

### <a name="using-data-preparation"></a>Usando a preparação de dados ###
A preparação de dados pressupõe uma abordagem/metodologia básica de cinco passos para a preparação dos dados.

#### <a name="step-1-ingestion"></a>Etapa 1: ingestão ####
Importe dados para a preparação de dados usando a opção **Adicionar Fonte de Dados** de dentro da exibição do projeto.  Todas a ingestão inicial dos dados é tratada por meio do Assistente de Fonte de Dados.

#### <a name="step-2-understandprofile-the-data"></a>Etapa 2: entender/criar perfil de dados ####

Primeiro, analise a barra de qualidade de dados na parte superior de cada coluna. O verde representa as linhas com valores. O cinza representa as linhas com um valor ausente, nulo etc. O vermelho indica valores de erro. Passe o mouse sobre a barra para obter uma dica de ferramenta com os números exatos de linhas em cada um dos três buckets. A barra de qualidade de dados usa uma escala logarítmica, portanto, sempre verifique os números reais para obter uma ideia aproximada do volume de dados ausentes.

![colunas](media/data-prep-getting-started/columns.png)

Em seguida, use uma combinação de outros inspetores mais a grade para entender melhor as características de dados.  Comece a formular hipóteses sobre a preparação de dados necessária para análise adicional. A maioria dos inspetores funciona em uma única coluna ou um pequeno número de colunas.  

É provável que vários inspetores em várias colunas sejam necessários para entender os dados. Você pode rolar pelos vários inspetores na Fonte de Criação de Perfil. Dentro da fonte, você também pode mover os inspetores para o início da lista para vê-los na área imediatamente visualizável.

![inspetores](media/data-prep-getting-started/inspectors.PNG)

São fornecidos inspetores diferentes para as colunas/variáveis contínuas vs categóricas. O menu Inspetor habilita e desabilita as opções dependendo do tipo de variáveis/colunas que você tem.

Ao trabalhar com conjuntos de dados grande com muitas colunas, uma abordagem de trabalho pragmática com subconjuntos é recomendável. Essa abordagem inclui se concentrar em um número pequeno de colunas (por exemplo, de 5 a 10), prepará-las e, em seguida, trabalhar no restante das colunas. O inspetor de grade dá suporte ao particionamento vertical das colunas, portanto, se você tiver mais de 300 colunas, precisará “paginar” por elas.
 

#### <a name="step-3-transform-the-data"></a>Etapa 3: transformar os dados ####
As transformações alteram os dados e permitem a execução dos dados para dar suporte à hipótese de trabalho atual. As transformações aparecem como Etapas na lista de etapas no lado direito. É possível "viajar nos tempos" por meio da lista de etapas clicando em qualquer ponto arbitrário na lista de etapas.

Um ícone verde à esquerda de uma determinada Etapa indica que ela foi executada e os dados refletem a execução da transformação. Uma barra vertical à esquerda da Etapa indica o estado atual dos dados nos inspetores.

![etapas](media/data-prep-getting-started/steps.PNG)

Tente fazer pequenas alterações frequentes nos dados e validar (Etapa 4) após cada alteração conforme a hipótese evolui.

#### <a name="step-4-verify-the-impact-of-the-transformation"></a>Etapa 4: verificar o impacto da transformação. 
Decida se a hipótese estava correta. Se estiver correta, desenvolva a próxima hipótese e repita as etapas 2 e 3 para a nova. Se estiver incorreta, desfaça a última transformação, desenvolva uma nova hipótese e repita as etapas 2 e 3.

A principal maneira de determinar se a transformação teve o impacto correto é usar os inspetores. Use os existentes. Use os inspetores com o efeito de halo habilitado ou inicie vários inspetores para exibir os dados em determinados pontos no tempo.

![inspetor de halo](media/data-prep-getting-started/halo1.PNG) ![inspetor de halo](media/data-prep-getting-started/halo2.PNG)

Para desfazer uma transformação, acesse a lista de etapas à direita da interface do usuário. (O painel de lista de etapas talvez precise ser destacado novamente. Para abri-lo, clique na divisa dupla apontando para a esquerda). No painel, selecione a transformação executada que você deseja desfazer. Selecione na lista suspensa no lado direito do bloco da interface do usuário. Selecione **Editar** para fazer alterações ou em **Excluir** para remover a transformação da lista de etapas e do fluxo de dados.

#### <a name="step-5-output"></a>Etapa 5: saída 
Quando concluir a preparação de dados, você pode gravar o fluxo de dados em uma saída. Um fluxo de dados pode ter várias saídas. No menu Transformações, você pode selecionar com qual saída você deseja que o conjunto de dados seja gravado. Você também pode selecionar o destino de saída. 

## <a name="list-of-appendices"></a>Lista de apêndices 
[Apêndice 2 – Fontes de dados com suporte](data-prep-appendix2-supported-data-sources.md)  
[Apêndice 3 – Transformações com suporte](data-prep-appendix3-supported-transforms.md)  
[Apêndice 4 – Inspetores com suporte](data-prep-appendix4-supported-inspectors.md)  
[Apêndice 5 – Destinos com suporte](data-prep-appendix5-supported-destinations.md)  
[Apêndice 6 – Exemplo de expressões de filtro em Python](data-prep-appendix6-sample-filter-expressions-python.md)  
[Apêndice 7 – Exemplo de expressões de fluxo de dados de transformação em Python](data-prep-appendix7-sample-transform-data-flow-python.md)  
[Apêndice 8 – Exemplo de fontes de dados em Python](data-prep-appendix8-sample-source-connections-python.md)  
[Apêndice 9 – Exemplo de conexões de destino em Python](data-prep-appendix9-sample-destination-connections-python.md)  
[Apêndice 10 – Exemplo de transformações de coluna em Python](data-prep-appendix10-sample-custom-column-transforms-python.md)  

## <a name="see-also"></a>Consulte também

[Tutorial de preparação de dados avançada](tutorial-bikeshare-dataprep.md)