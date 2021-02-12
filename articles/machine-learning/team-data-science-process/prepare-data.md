---
title: Preparação de dados para ML Studio (clássico)-processo de ciência de dados de equipe
description: Pré-processe e limpe os dados para prepará-los para serem usados efetivamente para o aprendizado de máquina.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: caedcf313ab809e9607907545f26ca1b62bbeca7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012400"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Tarefas para preparar dados para o aprendizado de máquina avançado
Pré-processando e limpando dados são tarefas importantes que devem ser realizadas antes que um DataSet possa ser usado para treinamento de modelo. Dados brutos costumam conter ruídos e não são confiáveis, e pode haver valores ausentes. Usar esses dados para a modelagem pode produzir resultados incorretos. Essas tarefas fazem parte do TDSP (Processo de Ciência de Dados de Equipe) e, geralmente, seguem uma exploração inicial de um conjunto de dados usado para descobrir e planejar o pré-processamento exigido. Para obter instruções mais detalhadas sobre o processo TDSP, confira as etapas descritas em [Processo de Ciência de Dados de Equipe](overview.md).

As tarefas de pré-processamento e limpeza, como a tarefa de exploração de dados, podem ser executadas em uma ampla variedade de ambientes, como SQL ou Hive ou Azure Machine Learning Studio (clássico) e com várias ferramentas e linguagens, como R ou Python, dependendo de onde os dados são armazenados e de como eles são formatados. Como o TDSP é iterativo por natureza, essas tarefas podem ocorrer em várias etapas do fluxo de trabalho do processo.

Este artigo apresenta vários conceitos de processamento de dados e tarefas que podem ser realizadas antes ou depois da ingestão de dados em Azure Machine Learning Studio (clássico).

Para obter um exemplo de exploração de dados e pré-processamento feito dentro de Azure Machine Learning Studio (clássico), consulte o vídeo [pré-processando dados](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) .

## <a name="why-pre-process-and-clean-data"></a>Por que pré-processar e limpar os dados?
Dados do mundo real são coletados de várias fontes e processos e podem conter irregularidades ou dados corrompidos, comprometendo a qualidade do conjunto de dados. Os problemas de qualidade de dados típicos que podem surgir são:

* **Incompleto**: os dados não têm atributos ou contém valores ausentes.
* **Com ruído**: Os dados contêm registros incorretos ou exceções.
* **Inconsistente**: os dados contêm registros conflitantes ou discrepâncias.

Dados de qualidade são um pré-requisito para modelos de previsão de qualidade. Para evitar a “entrada e saída de lixo" e melhorar a qualidade de dados e, portanto, o desempenho do modelo, é fundamental conduzir uma filtragem de integridade de dados para identificar problemas de dados no início e decidir sobre os as etapas correspondentes de processamento e limpeza de dados.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Poderia dar exemplo de filtragens de integridade de dados típicas empregadas?
Podemos verificar a qualidade geral dos dados confirmando:

* O número de **registros**.
* O número de **atributos** (ou **recursos**).
* Os **tipos de dados** de atributo (nominal, ordinal ou contínuo).
* O número de **valores ausentes**.
* Dados **bem formados** .
  * Se os dados estiverem em TSV ou CSV, verifique se os separadores de colunas e os separadores de linha sempre separam colunas e linhas corretamente.
  * Se os dados estiverem em formato HTML ou XML, verifique se os dados são bem formados com base nos seus respectivos padrões.
  * A análise também pode ser necessária para extrair informações estruturadas de dados não estruturados ou semiestruturados.
* **Registros de dados inconsistentes**. Verifique se o intervalo de valores é permitido. Por exemplo, se os dados contiverem GPA do aluno (média de ponto de série), verifique se a GPA está no intervalo designado, digamos 0 ~ 4.

Quando você encontra problemas com os dados, **as etapas de processamento** são necessárias, o que geralmente envolve a limpeza de valores ausentes, normalização de dados, discretização, processamento de texto para remover e/ou substituir caracteres inseridos que podem afetar o alinhamento de dados, tipos de dados mistos em campos comuns e outros.

**Azure Machine Learning consome dados tabulares bem formados**.  Se os dados já estiverem em formato de tabela, o pré-processamento de dados poderá ser executado diretamente com Azure Machine Learning Studio (clássico) no Machine Learning.  Se os dados não estiverem em formato tabular, como XML, por exemplo XML, a análise pode ser necessária para converter os dados em formato tabular.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Quais são as principais tarefas de pré-processamento de dados?
* **Limpeza de dados**: Preencha valores ausentes, detecte e remova dados e exceções ruidosas.
* **Transformação de dados**: normalizar dados para reduzir dimensões e ruído.
* **Redução de dados**: registros de dados de exemplo ou atributos para fácil manipulação de dados.
* **Diferenciação de dados**: converter atributos contínuos para atributos categóricos para maior facilidade de uso com determinados métodos de aprendizado de máquina.
* **Limpeza de texto**: Remova os caracteres inseridos que podem causar inalinhamento de dados, por exemplo, guias inseridas em um arquivo de dados separado por tabulação, novas linhas inseridas que podem quebrar registros, por exemplo.

As seções a seguir detalham algumas dessas etapas de processamento de dados.

## <a name="how-to-deal-with-missing-values"></a>Como lidar com valores ausentes?
Para lidar com valores ausentes, é melhor primeiro identificar o motivo dos valores ausentes para melhor tratar o problema. Métodos de manipulação de valores ausentes típicos são:

* **Exclusão**: remover os registros com valores ausentes
* **Substituição fictícia**: substituir os valores ausentes por um valor fictício: por exemplo, *desconhecido* para valores categóricos ou 0 para valores numéricos.
* **Substituição da média**: se os dados ausentes forem numéricos, substitua os valores ausentes pela média.
* **Substituição frequente**: se os dados ausentes forem categóricos, substitua os valores ausentes pelo item mais frequente
* **Substituição de regressão**: usar um método de regressão para substituir valores ausentes por valores de regressão.  

## <a name="how-to-normalize-data"></a>Como normalizar dados?
A normalização de dados redimensiona valores numéricos para um intervalo especificado. Métodos de normalização de dados populares incluem:

* **Normalização Mín-Máx**: transformar os dados de forma linear em um intervalo, digamos, entre 0 e 1, em que o valor mínimo é dimensionado para 0 e o valor máximo para 1.
* **Normalização de pontuação Z**: escalar dados com base na média e desvio padrão: dividir a diferença entre os dados e a média pelo desvio padrão.
* **Dimensionamento decimal**: dimensionar os dados movendo o ponto decimal do valor do atributo.  

## <a name="how-to-discretize-data"></a>Como diferenciar os dados?
Dados podem ser diferenciados ao converter valores contínuos em atributos nominais ou intervalos. Algumas formas de fazer isso são:

* **Compartimentalização de largura igual**: dividir o intervalo de todos os possíveis valores de um atributo entre N grupos do mesmo tamanho e atribuir os valores correspondentes a um compartimento a um número do compartimento.
* **Compartimentação de altura igual**: dividir o intervalo de todos os possíveis valores de um atributo em N grupos que contém o mesmo número de instâncias e atribuir os valores correspondentes a um compartimento ao número do compartimento.  

## <a name="how-to-reduce-data"></a>Como reduzir os dados?
Há vários métodos para reduzir o tamanho dos dados para facilitar a manipulação de dados. Dependendo do tamanho dos dados e do domínio, os métodos a seguir podem ser aplicados:

* **Amostragem de registros**: realizar a amostragem dos registros de dados de exemplo e escolha somente o subconjunto representativo dos dados.
* **Amostragem de atributo**: selecionar apenas um subconjunto dos atributos mais importantes dos dados.  
* **Agregação**: dividir os dados em grupos e armazenar os números em cada grupo. Por exemplo, os números de receita diária de uma cadeia de restaurante nos últimos 20 anos podem ser agregados para receita mensal para reduzir o tamanho dos dados.  

## <a name="how-to-clean-text-data"></a>Como limpar dados de texto?
**Campos de texto em dados tabulares** podem incluir caracteres que afetam o alinhamento de colunas e/ou limites de registro. Por exemplo, as guias inseridas em um arquivo separado por tabulação causam desalinhamento de coluna e os caracteres de nova linha inseridos quebram linhas de registro. A manipulação imprópria de codificação de texto durante a gravação ou leitura de texto leva à perda de informações, introdução inadvertida de caracteres ilegíveis (como nulos) e também pode afetar a análise de texto. Uma análise e edição detalhada podem ser necessárias para limpar os campos de texto para alinhamento adequado e/ou extrair dados estruturados de dados de texto não estruturados ou semiestruturados.

**exploração de dados** oferece uma exibição antecipada dos dados. Alguns problemas de dados podem ser descobertos durante essa etapa e métodos correspondentes podem ser aplicados para resolver esses problemas.  É importante fazer perguntas, tal como qual é a origem do problema e como o problema pode ter sido introduzido. Esse processo também ajuda a decidir as etapas de processamento de dados que precisam ser executadas para resolvê-las. Identificar os casos de uso finais e personas também pode ser usado para priorizar o esforço de processamento de dados.

## <a name="references"></a>Referências
> *Data Mining: Concepts and Techniques*, Third Edition, Morgan Kaufmann, 2011, Jiawei Han, Micheline Kamber e Jian Pei
> 
> 

