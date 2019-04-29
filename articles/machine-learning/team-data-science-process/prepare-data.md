---
title: Limpar e preparar dados para o Azure Machine Learning - Processo de Ciência de Dados da Equipe
description: Pré-processe e limpe os dados para prepará-los para serem usados efetivamente para o aprendizado de máquina.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 2b3ec3352d6e1939b195bbba87b8a824404346ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61044584"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Tarefas para preparar dados para o aprendizado de máquina avançado
O pré-processamento e a limpeza de dados são tarefas importantes e geralmente devem ser realizadas antes que o conjunto de dados possa ser usado com eficiência para o aprendizado de máquina. Dados brutos costumam conter ruídos e não são confiáveis, e pode haver valores ausentes. Usar esses dados para a modelagem pode produzir resultados incorretos. Essas tarefas fazem parte do TDSP (Processo de Ciência de Dados de Equipe) e, geralmente, seguem uma exploração inicial de um conjunto de dados usado para descobrir e planejar o pré-processamento exigido. Para obter instruções mais detalhadas sobre o processo TDSP, confira as etapas descritas em [Processo de Ciência de Dados de Equipe](overview.md).

As tarefas de pré-processamento e de limpeza, assim como a tarefa de exploração de tarefas, podem ser executadas em uma ampla variedade de ambientes, como o SQL ou o Hive ou o Azure Machine Learning Studio e com diversas ferramentas e linguagens, como R ou Python, dependendo de onde seus dados estejam armazenados e de como estejam formatados. Como o TDSP é iterativo por natureza, essas tarefas podem ocorrer em várias etapas do fluxo de trabalho do processo.

Este artigo apresenta vários conceitos e tarefas de processamento de dados que podem ser executados antes ou depois da ingestão de dados no Azure Machine Learning.

Para obter um exemplo de exploração de dados e pré-processamento feitos no Azure Machine Learning Studio, consulte o vídeo [Pré-processando dados no Azure Machine Learning Studio](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) .

## <a name="why-pre-process-and-clean-data"></a>Por que pré-processar e limpar os dados?
Dados do mundo real são coletados de várias fontes e processos e podem conter irregularidades ou dados corrompidos, comprometendo a qualidade do conjunto de dados. Os problemas de qualidade de dados típicos que podem surgir são:

* **Não concluído**: Os dados não têm atributos ou contém valores ausentes.
* **Com ruídos**: Os dados contêm registros incorretos ou exceções.
* **Inconsistente**: Os dados contêm registros conflitantes ou discrepâncias.

Dados de qualidade são um pré-requisito para modelos de previsão de qualidade. Para evitar a “entrada e saída de lixo" e melhorar a qualidade de dados e, portanto, o desempenho do modelo, é fundamental conduzir uma filtragem de integridade de dados para identificar problemas de dados no início e decidir sobre os as etapas correspondentes de processamento e limpeza de dados.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Poderia dar exemplo de filtragens de integridade de dados típicas empregadas?
Podemos verificar a qualidade geral dos dados confirmando:

* O número de **registros**.
* O número de **atributos** (ou **recursos**).
* O atributo **tipos de dados** (nominais, ordinais ou contínuos).
* O número de **valores ausentes**.
* **Boa formação** dos dados.
  * Se os dados estiverem em TSV ou CSV, verifique se os separadores de colunas e os separadores de linha sempre separam colunas e linhas corretamente.
  * Se os dados estiverem em formato HTML ou XML, verifique se os dados são bem formados com base nos seus respectivos padrões.
  * A análise também pode ser necessária para extrair informações estruturadas de dados não estruturados ou semiestruturados.
* **Registros de dados inconsistentes**. Verifique se o intervalo de valores é permitido. Por exemplo, se os dados contiverem a média de notas de um aluno, verifique se a média de notas está no intervalo designado, digamos, 0 a 4.

Quando você encontrar problemas com os dados, **etapas de processamento** serão necessárias, geralmente envolvendo a limpeza de valores ausentes, normalização de dados, diferenciação, processamento de texto para remover e/ou substituir caracteres incorporados que podem afetar o alinhamento dos dados e tipos de dados mistos em campos comuns, entre outros.

**O Azure Machine Learning consome dados tabulares bem formados**.  Se os dados já estiverem em formato tabular, o pré-processamento deles poderá ser realizado diretamente com o Azure Machine Learning no Machine Learning Studio.  Se os dados não estiverem em formato tabular, como XML, por exemplo XML, a análise pode ser necessária para converter os dados em formato tabular.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Quais são as principais tarefas de pré-processamento de dados?
* **Limpeza de dados**:  Preencher ou valores ausentes, detectar e remover exceções e dados com ruídos.
* **Transformação de dados**:  Normalizar dados para reduzir o ruído e dimensões.
* **Redução de Dados**:  Registros de dados de exemplo ou atributos para fácil manipulação de dados.
* **Diferenciação de dados**:  Converter atributos contínuos para atributos categóricos para maior facilidade de uso com determinados métodos de aprendizado de máquina.
* **Limpeza de texto**: remover caracteres inseridos que podem causar desalinhamento de dados, como por exemplo guias incorporadas em um arquivo de dados separado por tabulações, novas linhas incorporadas que podem quebrar registros, etc.

As seções a seguir detalham algumas dessas etapas de processamento de dados.

## <a name="how-to-deal-with-missing-values"></a>Como lidar com valores ausentes?
Para lidar com valores ausentes, é melhor primeiro identificar o motivo dos valores ausentes para melhor tratar o problema. Métodos de manipulação de valores ausentes típicos são:

* **Exclusão**: Remover os registros com valores ausentes
* **Substituição fictícia**: Substituir os valores ausentes por um valor fictício: por exemplo, *desconhecido* para valores categóricos ou 0 para valores numéricos.
* **Substituição média**: Se os dados ausentes forem numéricos, substitua os valores ausentes pela média.
* **Substituição média**: Se os dados ausentes forem categóricos, substitua os valores ausentes pelo item mais frequente
* **Substituição de regressão**: Usar um método de regressão para substituir valores ausentes por valores de regressão.  

## <a name="how-to-normalize-data"></a>Como normalizar dados?
A normalização de dados escala novamente valores numéricos para um intervalo especificado. Métodos de normalização de dados populares incluem:

* **Normalização de mín.– máx**: Transformar os dados de forma linear em um intervalo, digamos, entre 0 e 1, em que o valor mínimo é dimensionado para 0 e o valor máximo para 1.
* **Normalização de pontuação Z**: Escalar dados com base na média e desvio padrão: dividir a diferença entre os dados e a média pelo desvio padrão.
* **Dimensionamento decimal**: Dimensionar os dados movendo o ponto decimal do valor do atributo.  

## <a name="how-to-discretize-data"></a>Como diferenciar os dados?
Dados podem ser diferenciados ao converter valores contínuos em atributos nominais ou intervalos. Algumas formas de fazer isso são:

* **Compartimentalização de largura igual**: Dividir o intervalo de todos os possíveis valores de um atributo entre N grupos do mesmo tamanho e atribuir os valores correspondentes a um compartimento a um número do compartimento.
* **Compartimentalização de altura igual**: Dividir o intervalo de todos os possíveis valores de um atributo em N grupos que contém o mesmo número de instâncias e atribuir os valores correspondentes a um compartimento ao número do compartimento.  

## <a name="how-to-reduce-data"></a>Como reduzir os dados?
Há vários métodos para reduzir o tamanho dos dados para facilitar a manipulação de dados. Dependendo do tamanho dos dados e do domínio, os métodos a seguir podem ser aplicados:

* **Amostragem de registros**: Realizar a amostragem dos registros de dados de exemplo e escolha somente o subconjunto representativo dos dados.
* **Amostragem de atributo**: Selecionar apenas um subconjunto dos atributos mais importantes dos dados.  
* **Agregação**: Dividir os dados em grupos e armazenar os números em cada grupo. Por exemplo, os números de receita diária de uma cadeia de restaurante nos últimos 20 anos podem ser agregados para receita mensal para reduzir o tamanho dos dados.  

## <a name="how-to-clean-text-data"></a>Como limpar dados de texto?
**Campos de texto em dados tabulares** podem incluir caracteres que afetam os alinhamentos das colunas e/ou limites de registros. Por exemplo, guias incorporadas em um arquivo separado por tabulações causa desalinhamento de coluna e caracteres de nova linha incorporados quebram linhas de registros. A manipulação incorreta de codificação de texto durante a gravação/leitura do texto resulta em perda de informações e introdução indesejada de caracteres ilegíveis, como por exemplo valores nulos, podendo também afetar a análise de texto. Uma análise e edição detalhada podem ser necessárias para limpar os campos de texto para alinhamento adequado e/ou extrair dados estruturados de dados de texto não estruturados ou semiestruturados.

**exploração de dados** oferece uma exibição antecipada dos dados. Alguns problemas de dados podem ser descobertos durante essa etapa e métodos correspondentes podem ser aplicados para resolver esses problemas.  É importante fazer perguntas, tal como qual é a origem do problema e como o problema pode ter sido introduzido. Isso também ajuda você a decidir sobre as etapas de processamento de dados que precisam ser seguidas para resolvê-los. O tipo de informações que devem ser derivadas dos dados também pode ser usado para priorizar os esforços de processamento de dados.

## <a name="references"></a>Referências
> *Data Mining: Concepts and Techniques*, Third Edition, Morgan Kaufmann, 2011, Jiawei Han, Micheline Kamber e Jian Pei
> 
> 

