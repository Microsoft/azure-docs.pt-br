---
title: O que posso fazer com o Machine Learning Studio (clássico)? – Azure
description: O Machine Learning Studio (clássico) é uma ferramenta do tipo "arrastar e soltar" para criação rápida de modelos com base em uma biblioteca de algoritmos e módulos prontos para uso.
services: machine-learning
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: overview
ms.date: 08/19/2020
ms.openlocfilehash: ad0550dc20edfbc10fb8877ad049c27fc2edf086
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517528"
---
# <a name="what-can-i-do-with-machine-learning-studio-classic"></a>O que posso fazer com o Machine Learning Studio (clássico)?

**APLICA-SE A:**  ![Essa é uma marca de seleção, o que significa que este artigo se aplica ao Machine Learning Studio (clássico).](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (clássico) ![Este é um X, o que significa que este artigo se aplica ao Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

O Machine Learning Studio (clássico) é uma ferramenta do tipo "arrastar e soltar", que pode ser usada para criar, testar e implantar modelos de machine learning. O Studio (clássico) publica modelos como serviços Web que podem ser facilmente consumidos por aplicativos personalizados ou ferramentas de BI como o Excel.


## <a name="studio-classic--interactive-workspace"></a>Workspace interativo do Studio (clássico)

Para desenvolver um modelo de análise preditiva, normalmente você usa dados de uma ou mais fontes, transforma e os analisa por meio de várias funções estatísticas e de manipulação de dados, além de gerar um conjunto de resultados. Desenvolver um modelo como este é um processo iterativo. À medida que você modifica as diversas funções e seus parâmetros, seus resultados convergem até que você esteja satisfeito, com um modelo treinado e eficiente.

O Machine Learning Studio (clássico) oferece um workspace visual e interativo para criar, testar e iterar em um modelo de análise preditivo. Arraste e solte ***conjuntos de dados** _ e _*_módulos_*_ de análise em uma tela interativa, conectando-os para formar um _*_teste_*_, que você executa no Machine Learning Studio (clássico). Para iterar em seu design de modelo, você edita o teste, salva uma cópia, se desejado, e executa-o novamente. Quando estiver pronto, você poderá converter o _*_teste de treinamento_*_ em um _*_experimento preditivo_*_ e publicá-lo como um _ *_serviço Web_** para que o modelo possa ser acessado por outras pessoas.

Não há necessidade de programação, conecte visualmente os conjuntos de dados e módulos para construir seu modelo de análise preditivo.

![Diagrama do Machine Learning Studio (clássico): crie experimentos, leia dados de várias fontes, grave dados de pontuação, grave modelos.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-ml-studio-classic-overview-diagram"></a>Baixe o diagrama de visão geral do ML Studio (clássico)
Baixe o diagrama de **Visão geral de recursos do Microsoft ML Studio (clássico)** e obtenha uma exibição de alto nível das funcionalidades do Machine Learning Studio (clássico). Para mantê-lo próximo, imprima o diagrama em tamanho tabloide (11 x 17 polegadas).

**Baixe aqui o diagrama: [Visão geral das funcionalidades do Microsoft Machine Learning Studio (clássico)](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)** 
![Visão geral das funcionalidades do Microsoft Machine Learning Studio (clássico)](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)


## <a name="components-of-a-studio-classic--experiment"></a>Componentes de um teste do Studio (clássico)
Um teste consiste em conjuntos de dados que fornecem dados para módulos analíticos, os quais você conecta para construir um modelo de análise preditiva. Especificamente, um teste válido possui três características:

* O teste tem no mínimo um conjunto de dados e um módulo.
* Os conjuntos de dados podem ser conectados somente aos módulos.
* Os módulos podem ser conectados a conjuntos de dados ou a outros módulos.
* Todas as portas de entrada dos módulos devem ter uma conexão com o fluxo de dados.
* Todos os parâmetros necessários para cada módulo devem estar configurados.

Você pode criar uma experiência do zero, ou você pode usar uma experiência de exemplo existente como um modelo. Para saber mais, confira [Copiar os testes de amostra para criar novos experimentos do aprendizado de máquina](sample-experiments.md).

Para obter um exemplo de criação de um experimento, confira [Criar um experimento no Machine Learning Studio (clássico)](create-experiment.md).

Para obter um passo a passo mais completo da criação de uma solução de análise preditiva, confira [Desenvolver uma solução preditiva com o Machine Learning Studio (clássico)](tutorial-part1-credit-risk.md).

### <a name="datasets"></a>Conjunto de dados
Um conjunto de dados inclui dados que foram atualizados no Machine Learning Studio (clássico), de forma que possam ser usados no processo de modelagem. Alguns conjuntos de dados de amostra estão incluídos no Machine Learning Studio (clássico), com os quais você pode testar, além de ser possível também carregar mais conjuntos de dados, caso necessário. Aqui estão alguns exemplos dos conjuntos de dados incluídos:

* **Dados MPG para vários automóveis** – valores MPG (milhas por galão) para automóveis identificados por número de cilindros, cavalo-vapor, etc.
* **Dados de câncer de mama** – dados de diagnósticos de câncer de mama
* **Dados de incêndios em florestas** – tamanhos dos incêndios em floresta na região nordeste de Portugal

Conforme você cria um teste, pode escolher na lista de conjunto de dados disponíveis à esquerda da tela.

Para obter uma lista de conjuntos de dados de exemplo incluídos no Machine Learning Studio (clássico), confira [Usar os conjuntos de dados de exemplo no Machine Learning Studio (clássico)](use-sample-datasets.md).

### <a name="modules"></a>Módulos
Um módulo é um algoritmo que você pode executar em seus dados. O Machine Learning Studio (clássico) tem uma série de módulos, desde funções ingress até treinamento, pontuação e processos de validação. Aqui estão alguns exemplos dos módulos incluídos:

* [Converter em ARFF][convert-to-arff] – converte um conjunto de dados serializados do .NET no ARFF (Formato de Arquivo de Atributo-Relação).
* [Calcular Estatísticas Elementares][elementary-statistics] – calcula as estatísticas elementares como média, desvio padrão etc.
* [Regressão Linear][linear-regression] – cria um modelo online de regressão linear com base em gradiente descendente.
* [Modelo de Pontuação][score-model] – pontua uma classificação treinada ou modelo de regressão.

À medida que você compila um teste, você pode escolher a partir da lista de módulos à esquerda das telas.

Um módulo pode ter um conjunto de parâmetros que você pode usar para configurar os algoritmos internos do módulo. Ao selecionar um módulo nas telas, os parâmetros do módulo são exibidos no painel **Propriedades** à direita das telas. Você pode modificar os parâmetros nesse painel para ajustar seu modelo.

Para obter ajuda sobre navegação por meio da grande biblioteca de algoritmos de machine learning disponíveis, confira [Como escolher algoritmos para o Microsoft Machine Learning Studio (clássico)](../how-to-select-algorithms.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Implantando um serviço Web de análise preditiva
Quando seu modelo de análise preditiva estiver pronto, você pode implantá-lo como um serviço Web diretamente no Machine Learning Studio (clássico). Para obter mais informações sobre esse processo, consulte [Implantar um serviço Web do Azure Machine Learning](deploy-a-machine-learning-web-service.md).

## <a name="next-steps"></a>Próximas etapas
Você pode aprender os fundamentos da análise preditiva e aprendizado de máquina usando um [guia de início rápido passo a passo](create-experiment.md) e [aproveitando os exemplos](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: /azure/machine-learning/studio-module-reference/convert-to-arff
[elementary-statistics]: /azure/machine-learning/studio-module-reference/compute-elementary-statistics
[linear-regression]: /azure/machine-learning/studio-module-reference/linear-regression
[score-model]: /azure/machine-learning/studio-module-reference/score-model