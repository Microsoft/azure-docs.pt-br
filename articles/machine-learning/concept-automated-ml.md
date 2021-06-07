---
title: O que é o ML automatizado? AutoML
titleSuffix: Azure Machine Learning
description: Saiba como Azure Machine Learning pode gerar automaticamente um modelo usando os parâmetros e os critérios fornecidos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: cartacioS
ms.author: sacartac
ms.date: 10/27/2020
ms.custom: automl
ms.openlocfilehash: 44bcc27f877f6b2a547c51ecd3848214719cb085
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643652"
---
# <a name="what-is-automated-machine-learning-automl"></a>O que é AutoML (machine learning automatizado)?

O machine learning automatizado, também conhecido como ML automatizado ou AutoML, é o processo de automatizar as tarefas demoradas e iterativas do desenvolvimento de modelo de machine learning. Com ele, cientistas de dados, analistas e desenvolvedores podem criar modelos de ML com alta escala, eficiência e produtividade, ao mesmo tempo em que dão suporte à qualidade do modelo. O ML automatizado no Azure Machine Learning é baseado em uma inovação da [divisão do Microsoft Research](https://www.microsoft.com/research/project/automl/).

O desenvolvimento do modelo de machine learning tradicional tem uso intensivo de recursos, exigindo conhecimento de domínio significativo e tempo para produzir e comparar dezenas de modelos. Com o machine learning automatizado, você vai acelerar o tempo necessário para obter modelos de ML prontos para produção com grande facilidade e eficiência.

## <a name="when-to-use-automl-classify-regression--forecast"></a>Quando usar o AutoML: classificação, regressão e previsão

Aplique o ML automatizado quando quiser que o Azure Machine Learning treine e ajuste um modelo para você usando a métrica de destino especificada. O ML automatizado democratiza o processo de desenvolvimento do modelo de machine learning e capacita os usuário, independentemente do conhecimento em ciência de dados deles, a identificar um pipeline de machine learning de ponta a ponta para qualquer problema.

Cientistas de dados, analistas e desenvolvedores em setores podem usar o ML automatizado para:
+ Implementar soluções de ML sem conhecimento abrangente de programação
+ Economize tempo e recursos
+ Aproveite as melhores práticas de ciência de dados
+ Fornecer solução de problemas ágil

### <a name="classification"></a>classificação

A classificação é uma tarefa comum de machine learning. A classificação é um tipo de aprendizado supervisionado no qual os modelos aprendem a usar dados de treinamento e aplicam esses aprendizados a novos dados. O Azure Machine Learning oferece personalizações especificamente para essas tarefas, como personalizadores de texto de rede neural profunda para classificação. Saiba mais sobre as [opções de definição de recursos](how-to-configure-auto-features.md#featurization). 

A principal meta dos modelos de classificação é prever em quais categorias novos dados se encaixarão com base em aprendizados de dados de treinamento. Exemplos de classificação comuns incluem detecção de fraudes, reconhecimento de manuscrito e detecção de objetos. Saiba mais e veja um exemplo em [criar um modelo de classificação com ml automatizado](tutorial-first-experiment-automated-ml.md).

Confira exemplos de classificação e machine learning automatizado nestes notebooks Python: [Detecção de Fraudes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb), [Previsão de Marketing](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) e [Classificação de Dados do Grupo de Notícias](https://towardsdatascience.com/automated-text-classification-using-machine-learning-3df4f4f9570b)

### <a name="regression"></a>Regressão

De maneira semelhante à classificação, as tarefas de regressão também são uma tarefa de aprendizado supervisionado comum. O Azure Machine Learning oferece [personalizações especificamente para essas tarefas](how-to-configure-auto-features.md#featurization).

Diferentemente da classificação em que os valores de saída previstos são categóricos, os modelos de regressão preveem valores de saída numéricos com base em preditores independentes. Na regressão, o objetivo é ajudar a estabelecer a relação entre essas variáveis de preditores independentes estimando como uma variável afeta as outras. Por exemplo, o preço de automóveis baseado em características como gasto de combustível por quilometragem, classificação de segurança etc. Saiba mais e veja um exemplo de [regressão com machine learning automatizado](tutorial-auto-train-models.md).

Veja exemplos de regressão e machine learning automatizado para previsões nestes notebooks do Python: [Previsão de Desempenho da CPU](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-explanation-featurization/auto-ml-regression-explanation-featurization.ipynb), 

### <a name="time-series-forecasting"></a>Previsão de série temporal

Criar previsões é uma parte integrante de qualquer negócio, independentemente de ser para receita, inventário, vendas ou demanda do cliente. Você pode usar o ML automatizado para combinar técnicas e abordagens e obter uma previsão de série temporal de alta qualidade recomendada. Saiba mais com este guia de instruções: [machine learning automatizado para previsão de série temporal](how-to-auto-train-forecast.md). 

Um experimento de série temporal automatizado é tratado como um problema de regressão multivariada. Os valores de série temporal passados são “dinamizados” para se tornarem dimensões adicionais para o regressor junto com outros preditores. Essa abordagem, diferentemente de métodos de série temporal clássicos, tem uma vantagem de incorporar naturalmente muitas variáveis contextuais e a relação delas entre si durante o treinamento. O ML automatizado aprende um modelo único, mas geralmente ramificado internamente, para todos os itens no conjunto de dados e horizontes de previsão. Assim, mais dados estão disponíveis para estimar os parâmetros de modelo e se torna possível fazer generalizações para séries não vistas.

A configuração de previsão avançada inclui:
* detecção de feriados e definição de recursos
* série temporal e aprendizes DNN (Auto-ARIMA, Prophet, ForecastTCN)
* suporte a muitos modelos por meio do agrupamento
* validação cruzada de origem sem interrupção
* retardos configuráveis
* recursos de agregação de janela sem interrupção


Veja exemplos de regressão e machine learning automatizado para previsões nestes notebooks do Python: [Previsão de Vendas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb), [Previsão de Demanda](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) e [Previsão da Produção de Bebidas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb).

## <a name="how-automated-ml-works"></a>Como o ML automatizado funciona

Durante o treinamento, o Azure Machine Learning cria um número de pipelines em paralelo que experimentam diferentes parâmetros e algoritmos para você. O serviço é iterado por meio de algoritmos de ML emparelhados com seleções de recursos, em que cada iteração produz um modelo com uma pontuação de treinamento. Quanto maior a pontuação, melhor o modelo é considerado para "ajustar" seus dados.  Ela vai parar depois que atingir os critérios de saída definidos no experimento. 

Usando o **Azure Machine Learning**, você pode criar e executar seus experimentos de treinamento de ML automatizado com estas etapas:

1. **Identificar o problema de ML** a ser resolvido: classificação, previsão ou regressão

1. **Escolha se deseja usar o SDK do Python ou a experiência na Web do Studio**: Saiba mais sobre a paridade entre o [SDK do Python e a experiência na Web do Studio](#parity).

   * Para ter uma experiência limitada ou sem código, experimente a experiência na Web do Azure Machine Learning Studio em [https://ml.azure.com](https://ml.azure.com/)  
   * Para desenvolvedores Python, confira o [SDK do Python do Azure Machine Learning](how-to-configure-auto-train.md) 
    
1. **Especifique a origem e o formato dos dados de treinamento rotulados**: Matrizes Numpy ou dataframe do Pandas

1. **Configure o destino de computação para o treinamento de modelo**, como o [computador local, as Computações do Azure Machine Learning, as VMs remotas ou o Azure Databricks](how-to-set-up-training-targets.md).  Saiba ais sobre o treinamento automatizado [em um recuso remoto](how-to-auto-train-remote.md).

1. **Configure os parâmetros de machine learning automatizado** que determinam quantas iterações em diferentes modelos, configurações de hiperparâmetro, pré-processamento/definição de recursos avançados e quais métricas examinar ao determinar o melhor modelo.  
1. **Enviar a execução de treinamento.**

1. **Examine os resultados** 

O diagrama a seguir ilustra esse processo. 
![Machine learning automatizado](./media/concept-automated-ml/automl-concept-diagram2.png)


Você também pode inspecionar as informações de execução registradas em log, que [contêm métricas](how-to-understand-automated-ml.md) coletadas durante a execução. A execução de treinamento produz um objeto serializado para Python (arquivo `.pkl`) que contém o pré-processamento de modelo e dados.

Embora a criação de modelos seja automatizada, você também pode [saber como são os recursos importantes ou relevantes](how-to-configure-auto-train.md#explain) para os modelos gerados.

Saiba como usar um [destino de computação remota](how-to-auto-train-remote.md).



> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]


## <a name="feature-engineering"></a>Engenharia de recursos

A engenharia de recursos é o processo de usar o conhecimento de domínio dos dados para criar recursos que ajudem os algoritmos ML a obter melhor. Em Azure Machine Learning, as técnicas de dimensionamento e normalização são aplicadas para facilitar a engenharia de recursos. Coletivamente, essas técnicas e a engenharia de recursos são chamadas de personalização.

Para experiências automatizadas de aprendizado de máquina, o personalização é aplicado automaticamente, mas também pode ser personalizado com base em seus dados. [Saiba mais sobre qual definição de recursos está incluída](how-to-configure-auto-features.md#featurization).  

> [!NOTE]
> As etapas de definição de recursos de machine learning automatizado (normalização de recursos, manipulação de dados ausentes, conversão de texto em números, etc.) tornam-se parte do modelo subjacente. Ao usar o modelo para previsões, as mesmas etapas de definição de recursos aplicadas durante o treinamento são aplicadas aos dados de entrada automaticamente.

### <a name="automatic-featurization-standard"></a>Personalização automática (padrão)

Em todo experimento de machine learning automatizado, seus dados são dimensionados ou normalizados automaticamente para ajudar os algoritmos a ter um bom desempenho. Durante o treinamento de modelos, uma das técnicas de dimensionamento ou normalização a seguir serão aplicadas a cada modelo. Saiba como o AutoML ajuda a [impedir o ajuste excessivo e os dados desequilibrados](concept-manage-ml-pitfalls.md) em seus modelos.

|Processamento de dimensionamento &nbsp; & &nbsp;| Descrição |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Padronizar recursos removendo a mediana e dimensionando para a variância da unidade  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transformar os recursos dimensionando cada recurso pelo mínimo e máximo dessa coluna  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Dimensionar cada recurso pelo valor absoluto máximo dele |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Esses recursos do Scaler pelo intervalo de quantil |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Redução de dimensionalidade linear usando a Decomposição de Valor Singular dos dados para projetá-los em um espaço dimensional inferior |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Esse transformador executa a redução de dimensionalidade linear por meio da SVD (decomposição de valor singular) truncada. Ao contrário do PCA, esse avaliador não centraliza os dados antes de calcular a decomposição de valor singular, o que significa que ele pode trabalhar com matrizes scipy.sparse com eficiência |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Cada exemplo (ou seja, cada linha da matriz de dados) com pelo menos um componente diferente de zero é redimensionado independentemente de outros exemplos para que a norma (l1 ou l2) seja igual a um |

### <a name="customize-featurization"></a>Personalizar o personalização

Técnicas de engenharia de recursos adicionais, como codificação e transformações, também estão disponíveis. 

Habilite essa configuração com:

+ Azure Machine Learning Studio: Habilite a **Definição de recursos automática** na seção **Exibir configuração adicional** [com essas etapas](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

+ SDK do Python: especifique `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` no seu objeto [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) . Saiba mais sobre como [habilitar o personalização](how-to-configure-auto-features.md). 

## <a name="ensemble-models"></a><a name="ensemble"></a> Modelos Ensemble

O machine learning automatizado dá suporte a modelos ensemble, que são habilitados por padrão. O aprendizado Ensemble aprimora os resultados do machine learning e o desempenho preditivo combinando vários modelos em contraste com o uso de modelos únicos. As iterações Ensemble são exibidas como as iterações finais de sua execução. O machine learning automatizado usa métodos Ensemble de votação e empilhamento para combinar modelos:

* **Votação**: prevê com base na média ponderada das probabilidades de classe previstas (para tarefas de classificação) ou destinos de regressão previstos (para tarefas de regressão).
* **Empilhamento**: o empilhamento combina modelos heterogêneos e treina um metamodelo com base na saída dos modelos individuais. Os metamodelos padrão atuais são LogisticRegression para tarefas de classificação e ElasticNet para tarefas de regressão/previsão.

O [algoritmo de seleção Caruana Ensemble](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) com a inicialização Ensemble classificada é usado para decidir quais modelos usar dentro do Ensemble. Em um alto nível, esse algoritmo Inicializa o Ensemble com até cinco modelos com as melhores pontuações individuais e verifica se esses modelos estão dentro do limite de 5% da melhor pontuação para evitar um Ensemble inicial inadequado. Em seguida, para cada iteração Ensemble, um novo modelo é adicionado ao Ensemble existente e a pontuação resultante é calculada. Se um novo modelo tiver aprimorado a pontuação Ensemble existente, o Ensemble será atualizado para incluir o novo modelo.

Confira o [guia de instruções](how-to-configure-auto-train.md#ensemble) para alterar as configurações do Ensemble padrão no machine learning automatizado.

## <a name="guidance-on-local-vs-remote-managed-ml-compute-targets"></a><a name="local-remote"></a>Diretrizes sobre destinos de computação do ML gerenciada local versus remota

A interface da Web para o ML automatizado sempre usa um [destino de computação](concept-compute-target.md) remota.  Contudo, ao usar o SDK do Python, você escolherá um destino de computação local ou remota para treinamento de ML automatizado.

* **Computação local**: o treinamento ocorre em seu laptop local ou na computação da VM. 
* **Computação remota**: o treinamento ocorre em clusters de computação do Machine Learning.  

### <a name="choose-compute-target"></a>Escolher o destino de computação
Considere estes fatores ao escolher seu destino de computação:

 * **Escolher uma computação local**: se seu cenário for sobre explorações ou demonstrações iniciais que usam dados pequenos e treinamentos breves (por exemplo, segundos ou alguns minutos por execução filho), o treinamento em seu computador local poderá ser uma opção melhor.  Não há tempo de configuração e os recursos de infraestrutura (seu PC ou sua VM) estão diretamente disponíveis.
 * **Escolha um cluster de computação do ml remoto**: se você estiver treinando com grandes conjuntos de data, como em treinamento em produção criando modelos que precisam de treinamentos mais longos, a computação remota fornecerá um desempenho de ponta a ponta muito melhor, pois `AutoML` paralelizará as trens nos nós do cluster. Em uma computação remota, o tempo de inicialização para a infraestrutura interna será adicionado cerca de 1,5 minutos por execução de filho, além de minutos adicionais para a infraestrutura de cluster se as VMs ainda não estiverem em funcionamento.

### <a name="pros-and-cons"></a>Prós e contras
Considere esses prós e contras ao optar por usar a computação local versus remota.

|  | Prós (vantagens)  |Contras (desvantagens)  |
|---------|---------|---------|---------|
|**Destino de computação local** |  <li> Sem tempo de inicialização do ambiente   | <li>  Subconjunto de recursos<li>  Não é possível paralelizar execuções <li> Pior para dados grandes. <li>Não há streaming de dados durante o treinamento <li>  Nenhuma definição de recursos baseada em DNN <li> Somente SDK do Python |
|**Clusters de computação do ML remota**|  <li> Conjunto completo de recursos <li> Paralelizar execuções filhas <li>   Suporte a dados grandes<li>  Definição de recursos baseada em DNN <li>  Escalabilidade dinâmica de cluster de computação sob demanda <li> Experiência sem código (interface do usuário da Web) também disponível  |  <li> Tempo de inicialização para nós de cluster <li> Hora de início para cada execução de filho    |

### <a name="feature-availability"></a>Disponibilidade de recursos 

 Mais recursos estão disponíveis quando você usa a computação remota, conforme mostrado na tabela abaixo. 

| Recurso                                                    | Remote | Local | 
|------------------------------------------------------------|--------|-------|
| Streaming de dados (suporte a dados grandes, de até 100 GB)          | ✓      |       | 
| Definição de recursos de texto baseada em DNN-BERT e treinamento             | ✓      |       |
| Suporte de GPU pronto para uso (treinamento e inferência)        | ✓      |       |
| Suporte à classificação e rotulagem de imagens                  | ✓      |       |
| Modelos Auto-ARIMA, Prophet e ForecastTCN para previsão | ✓      |       | 
| Várias execuções/iterações em paralelo                       | ✓      |       |
| Criar modelos com interpretabilidade na interface do usuário da experiência na Web do AutoML Studio      | ✓      |       |
| Personalização da engenharia de recursos em uma interface do usuário da experiência na Web do Studio| ✓      |       |
| Ajuste de hiperparâmetros do Azure ML                             | ✓      |       |
| Suporte ao fluxo de trabalho do Pipeline do Azure ML                         | ✓      |       |
| Continuar uma execução                                             | ✓      |       |
| Previsão                                                | ✓      | ✓     |
| Criar e executar experimentos em notebooks                    | ✓      | ✓     |
| Registrar e visualizar as informações e métricas do experimento na interface do usuário | ✓      | ✓     |
| Verificadores de integridade dos dados                                            | ✓      | ✓     |

## <a name="many-models"></a>Muitos modelos 

O [Acelerador de Solução de Muitos Modelos](https://aka.ms/many-models) (versão prévia) é baseado no Azure Machine Learning e permite que você use um ML automatizado para treinar, operar e gerenciar centenas ou até milhares de modelos de machine learning.

Por exemplo, criar um modelo __para cada instância ou indivíduo__ nos cenários a seguir pode levar a resultados aprimorados:

* Como prever as vendas para cada repositório individual
* Manutenção preditiva para centenas de poços de petróleo
* Como personalizando uma experiência para usuários individuais.

## <a name="automl-in-azure-machine-learning"></a>AutoML no Azure Machine Learning

O Azure Machine Learning oferece duas experiências para trabalhar com ML automatizado:

* Para clientes experientes em código, [SDK do Python do Azure Machine Learning](/python/api/overview/azure/ml/intro) 

* Para clientes com experiência limitada/sem código, confira o Azure Machine Learning Studio em [https://ml.azure.com](https://ml.azure.com/)  

<a name="parity"></a>

### <a name="experiment-settings"></a>Configurações do experimento 

As configurações a seguir permitem que você configure seu experimento de ML automatizado. 

| |O SDK do Python|A experiência Web do estúdio|
----|:----:|:----:
|**Dividir dados em conjuntos de treinamento/validação**| ✓|✓
|**Dá suporte a tarefas de ML: classificação, regressão e previsão**| ✓| ✓
|**Otimiza com base na métrica primária**| ✓| ✓
|**Dá suporte à computação do Azure ML como destino de computação** | ✓|✓
|**Configurar horizonte de previsão, atraso de destino & janela sem interrupção**|✓|✓
|**Definir critérios de saída** |✓|✓ 
|**Definir iterações simultâneas**| ✓|✓
|**Remover colunas**| ✓|✓
|**Bloquear algoritmos**|✓|✓
|**Validação cruzada** |✓|✓
|**Dá suporte ao treinamento em clusters do Azure Databricks**| ✓|
|**Exibir nomes de recursos com engenharia**|✓|
|**Resumo da definição de recursos**| ✓|
|**Definição de recursos para feriados**|✓|
|**Níveis de detalhes do arquivo de log**| ✓|

### <a name="model-settings"></a>Configurações do modelo

Essas configurações podem ser aplicadas ao melhor modelo como resultado de seu experimento de ML automatizado.

| |O SDK do Python|A experiência Web do estúdio|
|----|:----:|:----:|
|**Melhor registro, implantação, capacidade de explicação do modelo**| ✓|✓|
|**Habilitar Ensemble de votação & os modelos de Ensemble de pilha**| ✓|✓|
|**Mostrar o melhor modelo com base na métrica não primária**|✓||
|**Habilitar/desabilitar a compatibilidade do modelo de ONNX**|✓||
|**Testar o modelo** | ✓| |

### <a name="run-control-settings"></a>Configurações do controle de execução

Essas configurações permitem que você examine e controle suas execuções de experimento de as execuções filho. 

| |O SDK do Python|A experiência Web do estúdio|
|----|:----:|:----:|
|**Executar tabela de resumo**| ✓|✓|
|**Cancelar execuções & as execuções filhas**| ✓|✓|
|**Obter proteções**| ✓|✓|
|**Pausar & retomar execuções**| ✓| |

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>AutoML e ONNX

Com o Azure Machine Learning, você pode usar o ML automatizado para criar um modelo do Python e fazê-lo ser convertido no formato ONNX. Depois que os modelos estiverem no formato ONNX, eles poderão ser executados em vários serviços e plataformas. Saiba mais sobre como [acelerar modelos de ML com ONNX](concept-onnx.md).

Veja como converter em formato ONNX [neste exemplo de notebook Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb). Saiba quais [algoritmos têm suporte no ONNX](how-to-configure-auto-train.md#select-your-experiment-type).

O runtime do ONNX também dá suporte ao C#, assim você pode usar o modelo criado automaticamente em seus aplicativos C# sem a necessidade de registrar ou sem nenhuma latência de rede que os pontos de extremidade REST introduzem. Saiba mais sobre como [usar um modelo AUTOML ONNX em um aplicativo .NET com](./how-to-use-automl-onnx-model-dotnet.md) [os modelos ml.net e inferência ONNX com a API C# do tempo de execução do ONNX](https://github.com/plaidml/onnxruntime/blob/plaidml/docs/CSharp_API.md). 

## <a name="next-steps"></a>Próximas etapas

Há vários recursos para colocá-lo em funcionamento com o AutoML. 

### <a name="tutorials-how-tos"></a>TUTORIAIS/instruções
Os tutoriais são exemplos introdutórios de ponta a ponta de cenários AutoML.
+ **Para uma experiência de código inicial**, siga o [tutorial: treinar automaticamente um modelo de regressão com Azure Machine Learning SDK do Python](tutorial-auto-train-models.md).

 + **Para uma experiência baixa ou sem código**, consulte o [tutorial: criar modelos de classificação de ml automatizados com o Azure Machine Learning Studio](tutorial-first-experiment-automated-ml.md).

Os artigos de instruções fornecem detalhes adicionais sobre a funcionalidade que o AutoML oferece. Por exemplo, 

+ Definir as configurações para experimentos de treinamento automático
    + No Azure Machine Learning Studio, [use estas etapas](how-to-use-automated-ml-for-ml-models.md). 
    + Com o SDK do Python, [use estas etapas](how-to-configure-auto-train.md).

+  Saiba como treinar automaticamente usando dados de série temporal, [com estas etapas](how-to-auto-train-forecast.md).

### <a name="jupyter-notebook-samples"></a>Exemplos de notebook Jupyter 

Examine exemplos de código detalhados e casos de uso no [repositório do bloco de anotações do GitHub para obter amostras automatizadas de aprendizado de máquina](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/).

### <a name="python-sdk-reference"></a>Referência do SDK do Python

Aprofunde sua experiência em padrões de design do SDK e especificações de classe com a [documentação de referência da classe AutoML](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

> [!Note]
> Os recursos automatizados de aprendizado de máquina também estão disponíveis em outras soluções da Microsoft, como [ml.net](/dotnet/machine-learning/automl-overview), [HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md), [Power bi](/power-bi/service-machine-learning-automated) e [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)
