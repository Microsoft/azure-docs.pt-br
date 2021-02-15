---
title: Personalização com o Machine Learning automatizado
titleSuffix: Azure Machine Learning
description: Aprenda as configurações de personalização de dados no Azure Machine Learning e como personalizar esses recursos para seus experimentos de ML automatizados.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to,automl,contperf-fy21q2
ms.date: 12/18/2020
ms.openlocfilehash: c90ef9fe49a87c18c7f4f55175bafaebfd31d722
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98610294"
---
# <a name="data-featurization-in-automated-machine-learning"></a>Personalização de dados no Machine Learning automatizado

Saiba mais sobre as configurações de personalização de dados no Azure Machine Learning e como personalizar esses recursos para [experiências automatizadas de aprendizado de máquina](concept-automated-ml.md).

## <a name="feature-engineering-and-featurization"></a>Engenharia de recursos e personalização

Os dados de treinamento consistem em linhas e colunas. Cada linha é uma observação ou um registro e as colunas de cada linha são os recursos que descrevem cada registro. Normalmente, os recursos que melhor caracterizam os padrões nos dados são selecionados para criar modelos de previsão.

Embora muitos dos campos de dados brutos possam ser usados diretamente para treinar um modelo, muitas vezes é necessário criar recursos adicionais (desenvolvidos) que fornecem informações que diferenciam melhor os padrões dos dados. Esse processo é chamado de **engenharia de recursos**, em que o uso do conhecimento de domínio dos dados é utilizado para criar recursos que, por sua vez, ajudam os algoritmos de aprendizado de máquina a aprender melhor. 

Em Azure Machine Learning, as técnicas de escalação de dados e normalização são aplicadas para facilitar a engenharia de recursos. Coletivamente, essas técnicas e essa engenharia de recursos são chamadas de **personalização** em experimentos de ml automatizados.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já sabe como configurar um experimento de ML automatizado. Para obter informações sobre a configuração, consulte os seguintes artigos:

- Para uma experiência de primeiro código: [Configure os experimentos de ml automatizados usando o SDK do Azure Machine Learning para Python](how-to-configure-auto-train.md).
- Para uma experiência de baixo código ou sem código: [crie, revise e implante modelos de aprendizado de máquina automatizados usando o Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

## <a name="configure-featurization"></a>Configurar o personalização

Em cada experimento automatizado de aprendizado de máquina, [técnicas de dimensionamento automático e de normalização](#featurization) são aplicadas aos seus dados por padrão. Essas técnicas são tipos de personalização que ajudam *certos* algoritmos que são sensíveis a recursos em escalas diferentes. Você pode habilitar mais personalização, como *valores ausentes de imputação*, *codificação* e *transformações*.

> [!NOTE]
> As etapas para personalização de Machine Learning automatizadas (como normalização de recursos, manipulação de dados ausentes ou conversão de texto em numeric) se tornam parte do modelo subjacente. Quando você usa o modelo para previsões, as mesmas etapas personalização aplicadas durante o treinamento são aplicadas aos dados de entrada automaticamente.

Para os experimentos que você configura com o SDK do Python, você pode habilitar ou desabilitar a configuração personalização e especificar ainda as etapas de personalização a serem usadas para o experimento. Se você estiver usando o Azure Machine Learning Studio, consulte as [etapas para habilitar o personalização](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

A tabela a seguir mostra as configurações aceitas para `featurization` na [classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig):

|Configuração do personalização | Descrição|
------------- | ------------- |
|`"featurization": 'auto'`| Especifica que, como parte do pré-processamento, [as etapas](#featurization) de guardrails e personalização de [dados](#data-guardrails) são feitas automaticamente. Essa é a configuração padrão.|
|`"featurization": 'off'`| Especifica que as etapas do personalização não devem ser feitas automaticamente.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Especifica que as etapas de personalização personalizadas devem ser usadas. [Saiba como personalizar a definição de recursos](#customize-featurization).|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>Personalização automática

A tabela a seguir resume as técnicas que são aplicadas automaticamente aos seus dados. Essas técnicas são aplicadas para experimentos que são configurados usando o SDK ou o estúdio. Para desabilitar esse comportamento, defina `"featurization": 'off'` em seu `AutoMLConfig` objeto.

> [!NOTE]
> Se você planeja exportar seus modelos AutoML para um [modelo ONNX](concept-onnx.md), somente as opções de personalização indicadas com um asterisco ("*") têm suporte no formato ONNX. Saiba mais sobre [conversão de modelos para ONNX](how-to-use-automl-onnx-model-dotnet.md).

|Etapas de personalização &nbsp;| Descrição |
| ------------- | ------------- |
|**Descartar alta cardinalidade ou nenhum recurso de variação** _ |Descartar esses recursos de conjuntos de treinamento e validação. Aplica-se a recursos com todos os valores ausentes, com o mesmo valor em todas as linhas ou com alta cardinalidade (por exemplo, hashes, IDs ou GUIDs).|
|_*Imputar valores ausentes**_ |Para recursos numéricos, imputar com a média de valores na coluna.<br/><br/>Para recursos categóricos, imputar com o valor mais frequente.|
|_*Gerar mais recursos**_ |Para recursos DateTime: Ano, mês, dia, dia da semana, dia do ano, trimestre, semana do ano, hora, minuto, segundo.<br><br> _For tarefas de previsão, * esses recursos de DateTime adicionais são criados: ano ISO, semestre, mês como cadeia de caracteres, semana, dia da semana como cadeia de caracteres, dia do trimestre, dia do ano, AM/PM (0 se a hora for anterior ao meio-dia (12 PM), 1 caso contrário), AM/PM como cadeia de caracteres, hora do dia<br/><br/>Para recursos de texto: a frequência de termos com base em unigrams, bigrams e trigrams. Saiba mais sobre [como isso é feito com o Bert.](#bert-integration)|
|**Transformar e codificar** _|Transforme recursos numéricos que têm poucos valores exclusivos em recursos categóricos.<br/><br/>A codificação One-Hot é usada para recursos categóricos de baixa cardinalidade. A codificação um-Hot-hash é usada para recursos categóricos de alta cardinalidade.|
|_ *Incorporações de palavras**|Uma featurizer de texto converte vetores de tokens de texto em vetores de sentença usando um modelo pré-treinado. O vetor de incorporação de cada palavra em um documento é agregado com o restante para produzir um vetor de recursos de documento.|
|**Distância do cluster**|Treina um modelo de clustering k-means em todas as colunas numéricas. Produz novos recursos do *k* (um novo recurso numérico por cluster) que contém a distância de cada amostra para o centróide de cada cluster.|

## <a name="data-guardrails"></a>Verificadores de integridade dos dados

*Os data guardrails* ajudam a identificar possíveis problemas com seus dados (por exemplo, valores ausentes ou [desequilíbrio de classe](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)). Eles também ajudam você a tomar medidas corretivas para resultados aprimorados.

Os Guardrails de dados são aplicados:

- **Para experimentos do SDK**: quando os parâmetros `"featurization": 'auto'` ou `validation=auto` são especificados em seu `AutoMLConfig` objeto.
- **Para experimentos de estúdio**: quando a personalização automática está habilitada.

Você pode revisar os dados guardrails para seu experimento:

- Definindo `show_output=True` quando você envia um experimento usando o SDK.

- No estúdio, na guia **Data guardrails** da execução de ml automatizada.

### <a name="data-guardrail-states"></a>Estados de guardrail de dados

Os Guardrails de dados exibem um dos três Estados:

|Estado| Descrição |
|----|---- |
|**Aprovado**| Nenhum problema de dados foi detectado e nenhuma ação é exigida por você. |
|**Concluído**| As alterações foram aplicadas aos seus dados. Incentivamos você a examinar as ações corretivas que o AutoML levou, para garantir que as alterações se alinhem com os resultados esperados. |
|**Alertado**| Foi detectado um problema de dados, mas não foi possível remediar. Incentivamos você a revisar e corrigir o problema.|

### <a name="supported-data-guardrails"></a>Guardrails de dados com suporte

A tabela a seguir descreve os dados guardrails que têm suporte no momento e os status associados que você pode ver ao enviar seu experimento:

Verificador de integridade|Status|Condição&nbsp;para&nbsp;gatilho
---|---|---
**Imputação de valores de recurso ausente** |Aprovado <br><br><br> Concluído| Não foram detectados valores de recursos ausentes em seus dados de treinamento. Saiba mais sobre [imputação de valor ausente.](./how-to-use-automated-ml-for-ml-models.md#customize-featurization) <br><br> Valores de recursos ausentes foram detectados em seus dados de treinamento e foram imputadosdos.
**Tratamento de recursos de alta cardinalidade** |Aprovado <br><br><br> Concluído| Suas entradas foram analisadas e nenhum recurso de alta cardinalidade foi detectado. <br><br> Os recursos de alta cardinalidade foram detectados nas suas entradas e foram manipulados.
**Tratamento de divisão de validação** |Concluído| A configuração de validação foi definida como `'auto'` e os dados de treinamento continham *menos de 20.000 linhas*. <br> Cada iteração do modelo treinado foi validada usando a validação cruzada. Saiba mais sobre [os dados de validação](./how-to-configure-auto-train.md#training-validation-and-test-data). <br><br> A configuração de validação foi definida como `'auto'` e os dados de treinamento continham *mais de 20.000 linhas*. <br> Os dados de entrada foram divididos em um conjunto de dados de treinamento e um conjunto de dados de validação para a validação do modelo.
**Detecção de equilíbrio de classe** |Aprovado <br><br><br><br>Alertado <br><br><br>Concluído | Suas entradas foram analisadas, e todas as classes estão equilibradas nos dados de treinamento. Um conjunto de um DataSet é considerado como balanceado se cada classe tem uma boa representação no DataSet, conforme medido por número e proporção de amostras. <br><br> Foram detectadas classes desequilibradas nas suas entradas. Para corrigir a tendência do modelo, corrija o problema de balanceamento. Saiba mais sobre [dados desequilibrados](./concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data).<br><br> As classes desbalanceadas foram detectadas nas suas entradas e a lógica de varredura determinou para aplicar o balanceamento.
**Detecção de problemas de memória** |Aprovado <br><br><br><br> Concluído |<br> Os valores selecionados (Horizonte, retardo, janela sem interrupção) foram analisados e nenhum problema potencial de memória insuficiente foi detectado. Saiba mais sobre [as configurações de previsão](./how-to-auto-train-forecast.md#configuration-settings)de série temporal. <br><br><br>Os valores selecionados (Horizonte, retardo, janela sem interrupção) foram analisados e, potencialmente, farão com que o teste fique sem memória. As configurações de atraso ou janela de rolagem foram desativadas.
**Detecção de frequência** |Aprovado <br><br><br><br> Concluído |<br> A série temporal foi analisada e todos os pontos de dados estão alinhados com a frequência detectada. <br> <br> A série temporal foi analisada e os pontos de dados que não se alinham com a frequência detectada foram detectados. Esses pontos de dados foram removidos do conjunto de dados. Saiba mais sobre [a preparação de dados para a previsão de série temporal](./how-to-auto-train-forecast.md#preparing-data).

## <a name="customize-featurization"></a>Personalizar o personalização

Você pode personalizar suas configurações de personalização para garantir que os dados e recursos usados para treinar seu modelo de ML resultem em previsões relevantes.

Para personalizar o featurizations, especifique `"featurization": FeaturizationConfig` em seu `AutoMLConfig` objeto. Se você estiver usando o Azure Machine Learning Studio para seu experimento, consulte o [artigo de instruções](how-to-use-automated-ml-for-ml-models.md#customize-featurization). Para personalizar o personalização para os tipos de tarefa previsão, consulte a [previsão de instruções](how-to-auto-train-forecast.md#customize-featurization).

As personalizações com suporte incluem:

|Personalização|Definição|
|--|--|
|**Atualização de finalidade de coluna**|Substituir o tipo de recurso detectado automaticamente para a coluna especificada.|
|**Atualização de parâmetro do transformador** |Atualize os parâmetros para o transformador especificado. Atualmente dá suporte a *imputer* (média, mais frequente e mediana) e *HashOneHotEncoder*.|
|**Remover colunas** |Especifica que as colunas a serem descartadas são destacados.|
|**Bloquear transformadores**| Especifica os transformadores de bloqueio a serem usados no processo personalização.|

>[!NOTE]
> A funcionalidade **remover colunas** foi preterida a partir da versão 1,19 do SDK. Remova as colunas do conjunto de dados como parte da limpeza, antes de consumi-la em seu experimento de ML automatizado. 

Crie o `FeaturizationConfig` objeto usando chamadas de API:

```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

## <a name="featurization-transparency"></a>Transparência de personalização

Cada modelo de AutoML tem personalização aplicado automaticamente.  O personalização inclui engenharia automatizada de recursos (quando `"featurization": 'auto'` ) e dimensionamento e normalização, que impacta o algoritmo selecionado e seus valores de hiperparâmetro. O AutoML dá suporte a métodos diferentes para garantir que você tenha visibilidade do que foi aplicado ao seu modelo.

Considere este exemplo de previsão:

+ Há quatro recursos de entrada: A (numérico), B (numérico), C (numérico), D (DateTime).
+ O recurso numérico C é Descartado porque é uma coluna de ID com todos os valores exclusivos.
+ Os recursos numéricos A e B têm valores ausentes e, portanto, são imputadosdos pela média.
+ O recurso DateTime D é destacados em 11 recursos de engenharia diferentes.

Para obter essas informações, use a `fitted_model` saída da execução do experimento do ml automatizado.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```
### <a name="automated-feature-engineering"></a>Engenharia de recursos automatizada 
O `get_engineered_feature_names()` retorna uma lista de nomes de recursos com engenharia.

  >[!Note]
  >Use “timeseriestransformer” para task=”forecasting”, caso contrário, use “datatransformer” para a tarefa “regression” ou “classification”.

  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

Essa lista inclui todos os nomes de recursos de engenharia. 

  ```
  ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

O `get_featurization_summary()` Obtém um resumo personalização de todos os recursos de entrada.

  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

Saída

  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   |Saída|Definição|
   |----|--------|
   |RawFeatureName|Nome de recurso/coluna de entrada do conjunto de dados fornecido.|
   |TypeDetected|Tipo de dados detectado do recurso de entrada.|
   |Dropped|Indica se o recurso de entrada foi removido ou usado.|
   |EngineeringFeatureCount|Número de recursos gerados por meio de transformações automatizadas de engenharia de recursos.|
   |Transformações|Lista de transformações aplicadas aos recursos de entrada para gerar recursos de engenharia.|

### <a name="scaling-and-normalization"></a>Dimensionamento e normalização

Para entender o dimensionamento/normalização e o algoritmo selecionado com seus valores de hiperparâmetro, use `fitted_model.steps` . 

A seguinte saída de exemplo é de execução `fitted_model.steps` para uma execução escolhida:

```
[('RobustScaler', 
  RobustScaler(copy=True, 
  quantile_range=[10, 90], 
  with_centering=True, 
  with_scaling=True)), 

  ('LogisticRegression', 
  LogisticRegression(C=0.18420699693267145, class_weight='balanced', 
  dual=False, 
  fit_intercept=True, 
  intercept_scaling=1, 
  max_iter=100, 
  multi_class='multinomial', 
  n_jobs=1, penalty='l2', 
  random_state=None, 
  solver='newton-cg', 
  tol=0.0001, 
  verbose=0, 
  warm_start=False))
```

Para obter mais detalhes, use esta função auxiliar: 

```python
from pprint import pprint

def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()

print_model(model)
```

Essa função auxiliar retorna a saída a seguir para uma execução específica usando `LogisticRegression with RobustScalar` como o algoritmo específico.

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

### <a name="predict-class-probability"></a>Prever probabilidade de classe

Os modelos produzidos usando o ML automatizado têm objetos wrapper que espelham a funcionalidade de sua classe de origem open-source. A maioria dos objetos do wrapper do modelo de classificação retornados pelo ML automatizado implementam a função `predict_proba()`, que aceita uma amostra de dados de matriz ou de matriz esparsa dos seus recursos (valores X) e retorna uma matriz n-dimensional de cada amostra e sua respectiva probabilidade de classe.

Supondo que você tenha recuperado a melhor execução e o modelo ajustado usando as mesmas chamadas acima, você pode chamar `predict_proba()` diretamente do modelo ajustado, fornecendo uma amostra de `X_test` no formato apropriado, dependendo do tipo de modelo.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Se o modelo subjacente não oferecer suporte à função `predict_proba()` ou o formato estiver incorreto, uma exceção específica para classe de modelo será lançada. Consulte os documentos de referência de [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) e [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) para obter exemplos de como essa função é implementada para diferentes tipos de modelo.

<a name="bert-integration"></a>

## <a name="bert-integration-in-automated-ml"></a>Integração do BERT no ML automatizado

[Bert](https://techcommunity.microsoft.com/t5/azure-ai/how-bert-is-integrated-into-azure-automated-machine-learning/ba-p/1194657) é usado na camada personalização de AutoML. Nessa camada, se uma coluna contiver texto livre ou outros tipos de dados como carimbos de data/hora ou números simples, personalização será aplicado de acordo.

Para o BERT, o modelo é ajustado e treinado utilizando os rótulos fornecidos pelo usuário. A partir daqui, as incorporações de documentos são geradas como recursos ao lado de outros, como recursos baseados em carimbo de data/hora, dia da semana. 


### <a name="steps-to-invoke-bert"></a>Etapas para invocar BERT

Para invocar BERT, defina  `enable_dnn: True` em seu automl_settings e use uma computação de GPU ( `vm_size = "STANDARD_NC6"` ou uma GPU superior). Se uma computação de CPU for usada, em vez de BERT, AutoML habilitará o BiLSTM DNN featurizer.

AutoML executa as seguintes etapas para o BERT. 

1. **Pré-processamento e geração de tokens de todas as colunas de texto**. Por exemplo, o transformador "StringCast" pode ser encontrado no Resumo de personalização do modelo final. Um exemplo de como produzir o resumo de personalização do modelo pode ser encontrado neste [bloco de anotações](https://towardsdatascience.com/automated-text-classification-using-machine-learning-3df4f4f9570b).

2. **Concatenar todas as colunas de texto em uma única coluna de texto**, portanto, `StringConcatTransformer` no modelo final. 

    Nossa implementação de BERT limita o tamanho de texto total de um exemplo de treinamento a tokens de 128. Isso significa que, de forma ideal, todas as colunas de texto quando concatenadas devem ter, no máximo, 128 tokens de comprimento. Se várias colunas estiverem presentes, cada coluna deverá ser removida para que essa condição seja satisfeita. Caso contrário, para colunas concatenadas de comprimento >a camada criador de tokens de 128 BERT trunca essa entrada para 128 tokens.

3. **Como parte da limpeza de recursos, o AutoML compara BERT com a linha de base (recursos de conjunto de palavras) em uma amostra dos dados.** Essa comparação determina se o BERT forneceria melhorias de precisão. Se BERT tiver um desempenho melhor do que a linha de base, o AutoML usará BERT para o texto personalização para os dados inteiros. Nesse caso, você verá o `PretrainedTextDNNTransformer` no modelo final.

BERT geralmente é executado por mais tempo do que outros featurizers. Para obter um melhor desempenho, recomendamos o uso de "STANDARD_NC24r" ou "STANDARD_NC24rs_V3" para seus recursos RDMA. 

O AutoML distribuirá o treinamento do BERT em vários nós se eles estiverem disponíveis (até um máximo de oito nós). Isso pode ser feito em seu `AutoMLConfig` objeto definindo o `max_concurrent_iterations` parâmetro como maior que 1. 

## <a name="supported-languages-for-bert-in-automl"></a>Idiomas com suporte para BERT no autoML 

O AutoML atualmente dá suporte a idiomas 100 e, dependendo do idioma do conjunto de um, o autoML escolhe o modelo de BERT apropriado. Para dados em alemão, usamos o modelo alemão BERT. Para o inglês, usamos o modelo BERT em inglês. Para todas as outras linguagens, usamos o modelo BERT multilíngue.

No código a seguir, o modelo alemão BERT é disparado, uma vez que a linguagem do conjunto de dado é especificada como `deu` , o código de idioma de três letras para o alemão de acordo com a [classificação ISO](https://iso639-3.sil.org/code/deu):

```python
from azureml.automl.core.featurization import FeaturizationConfig

featurization_config = FeaturizationConfig(dataset_language='deu')

automl_settings = {
    "experiment_timeout_minutes": 120,
    "primary_metric": 'accuracy', 
# All other settings you want to use 
    "featurization": featurization_config,
    
  "enable_dnn": True, # This enables BERT DNN featurizer
    "enable_voting_ensemble": False,
    "enable_stack_ensemble": False
}
```

## <a name="next-steps"></a>Próximas etapas

* Saiba como configurar seus experimentos de ML automatizados:

    * Para uma experiência de primeiro código: [Configure os experimentos de ml automatizados usando o SDK do Azure Machine Learning](how-to-configure-auto-train.md).
    * Para uma experiência de baixo código ou sem código: [crie seus experimentos de ml automatizados no Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

* Saiba mais sobre [como e onde implantar um modelo](how-to-deploy-and-where.md).

* Saiba mais sobre [como treinar um modelo de regressão usando o aprendizado de máquina automatizado](tutorial-auto-train-models.md) ou [como treinar usando o aprendizado de máquina automatizado em um recurso remoto](how-to-auto-train-remote.md).
