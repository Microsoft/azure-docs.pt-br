---
title: SmartNoise de privacidade diferencial (versão prévia)
titleSuffix: Azure Machine Learning
description: Saiba como aplicar práticas recomendadas de privacidade diferencial para Azure Machine Learning modelos usando as bibliotecas de código-fonte aberto do SmartNoise.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, responsible-ml
ms.author: slbird
author: slbird
ms.reviewer: luquinta
ms.date: 01/21/2020
ms.openlocfilehash: 62a002569696da4ef18e7bd967f027eb8247ef65
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98681397"
---
# <a name="use-differential-privacy-in-azure-machine-learning-preview"></a>Usar a privacidade diferencial no Azure Machine Learning (versão prévia)

Saiba como aplicar práticas recomendadas de privacidade diferencial para Azure Machine Learning modelos usando as bibliotecas de código-fonte aberto do SmartNoise Python.

Privacidade diferencial é a definição padrão de privacidade. Os sistemas que aderem a essa definição de privacidade oferecem fortes garantias contra uma ampla variedade de ataques de reconstrução e reidentificação de dados, incluindo ataques de adversários que possuem informações auxiliares. Saiba mais sobre [como a privacidade diferencial funciona](./concept-differential-privacy.md).


## <a name="prerequisites"></a>Pré-requisitos

- Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).
- [Python 3](https://www.python.org/downloads/)

## <a name="install-smartnoise-python-libraries"></a>Instalar bibliotecas do SmartNoise Python

### <a name="standalone-installation"></a>Instalação autônoma

As bibliotecas são projetadas para funcionar a partir de clusters Spark distribuídos e podem ser instaladas como qualquer outro pacote.

As instruções abaixo presumem que seus comandos `python` e `pip` estejam mapeados para `python3` e `pip3`.

Use o Pip para instalar os [pacotes SmartNoise Python](https://pypi.org/project/opendp-smartnoise/).

`pip install opendp-smartnoise`

Para verificar se os pacotes estão instalados, inicie um prompt python e digite:

```python
import opendp.smartnoise.core
import opendp.smartnoise.sql
```

Se as importações forem bem-sucedidas, as bibliotecas serão instaladas e estarão prontas para uso.

### <a name="docker-image-installation"></a>Instalação da imagem do Docker

Você também pode usar pacotes SmartNoise com o Docker.

Puxe a imagem `opendp/smartnoise` para usar as bibliotecas dentro de um contêiner do Docker que inclui Spark, Jupyter e código de exemplo.

```sh
docker pull opendp/smartnoise:privacy
```

Depois de extrair a imagem, inicie o servidor Jupyter:

```sh
docker run --rm -p 8989:8989 --name smartnoise-run opendp/smartnoise:privacy
```

Isso inicia um servidor Jupyter na porta `8989` no seu `localhost`, com a senha `pass@word99`. Supondo que você tenha usado a linha de comando acima para iniciar o contêiner com o nome `smartnoise-privacy`, é possível abrir um terminal bash no servidor Jupyter executando:

```sh
docker exec -it smartnoise-run bash
```

A instância do Docker limpa todo o estado no desligamento, para que você perca os notebooks criados na instância em execução. Para remediar isso, você pode vincular a montagem de uma pasta local ao contêiner ao iniciá-lo:

```sh
docker run --rm -p 8989:8989 --name smartnoise-run --mount type=bind,source=/Users/your_name/my-notebooks,target=/home/privacy/my-notebooks opendp/smartnoise:privacy
```

Qualquer bloco de anotações que você criar na pasta *my-notebooks* será armazenado no seu sistema de arquivos local.

## <a name="perform-data-analysis"></a>Realizar análise de dados

Para preparar uma versão diferencialmente privada, você precisa escolher uma fonte de dados, uma estatística e alguns parâmetros de privacidade, indicando o nível de proteção da privacidade.  

Este exemplo faz referência aos Microdados de uso público da Califórnia (PUMS), representando registros anônimos da demografia de cidadãos:

```python
import os
import sys
import numpy as np
import opendp.smartnoise.core as sn

data_path = os.path.join('.', 'data', 'PUMS_california_demographics_1000', 'data.csv')
var_names = ["age", "sex", "educ", "race", "income", "married", "pid"]
```

Neste exemplo, calculamos a média e a variação da idade.  Usamos um total de `epsilon` de 1,0 (epsilon é o nosso parâmetro de privacidade, distribuindo nosso orçamento de privacidade pelas duas quantidades que queremos calcular. Saiba mais sobre [métricas de privacidade](concept-differential-privacy.md#differential-privacy-metrics).

```python
with sn.Analysis() as analysis:
    # load data
    data = sn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = sn.dp_mean(data = sn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
    # get variance of age
    age_var = sn.dp_variance(data = sn.cast(data['age'], type="FLOAT"),
                             privacy_usage = {'epsilon': .35},
                             data_lower = 0.,
                             data_upper = 100.,
                             data_n = 1000
                            )
analysis.release()

print("DP mean of age: {0}".format(age_mean.value))
print("DP variance of age: {0}".format(age_var.value))
print("Privacy usage: {0}".format(analysis.privacy_usage))
```

Os resultados são semelhantes aos abaixo:

```text
DP mean of age: 44.55598845931517
DP variance of age: 231.79044646429134
Privacy usage: approximate {
  epsilon: 1.0
}
```

Há algumas coisas importantes a serem observadas sobre este exemplo.  Primeiro, o objeto `Analysis` representa um gráfico de processamento de dados.  Neste exemplo, a média e a variação são calculadas a partir do mesmo nó de origem.  No entanto, você pode incluir expressões mais complexas que combinam entradas e saídas de maneiras arbitrárias.

O gráfico de análise inclui os metadados `data_upper` e `data_lower`, especificando os limites inferior e superior para as idades.  Esses valores são usados para calibrar com precisão o ruído para garantir privacidade diferencial.  Esses valores também são usados em algumas manipulações de valores discrepantes ou ausentes.

Por fim, o gráfico de análise controla o orçamento total de privacidade gasto.

Você pode usar a biblioteca para compor gráficos de análise mais complexos, com vários mecanismos, estatísticas e funções de utilitário:

| Estatísticas    | Mecanismos | Utilidades  |
| ------------- |------------|------------|
| Contagem         | Gaussiano   | Conversão       |
| Histograma     | Geométrico  | Clamping   |
| Média          | Laplace    | Digitalização   |
| Quantis     |            | Filtrar     |
| SUM           |            | Imputação |
| Variância/Covariância |      | Transformar  |

Consulte o [notebook de análise de dados](https://github.com/opendifferentialprivacy/smartnoise-samples/blob/master/analysis/basic_data_analysis.ipynb) para obter mais detalhes.

## <a name="approximate-utility-of-differentially-private-releases"></a>Utilidade aproximada de lançamentos diferencialmente privados

Como a privacidade diferencial opera calibrando o ruído, a utilidade das versões pode variar dependendo do risco de privacidade.  Geralmente, o ruído necessário para proteger cada indivíduo se torna insignificante à medida que o tamanho da amostra aumenta, mas sobrecarrega o resultado de versões direcionadas a um único indivíduo.  Os analistas podem revisar as informações de precisão de um lançamento para determinar a utilidade do lançamento:

```python
with sn.Analysis() as analysis:
    # load data
    data = sn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = sn.dp_mean(data = sn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
analysis.release()

print("Age accuracy is: {0}".format(age_mean.get_accuracy(0.05)))
```

O resultado dessa operação deve ser semelhante ao abaixo:

```text
Age accuracy is: 0.2995732273553991
```

Este exemplo calcula a média como mostrado acima e usa a função `get_accuracy` para precisão da solicitação em `alpha` de 0,05. Um `alpha` de 0,05 representa um intervalo de 95%, pois o valor liberado cairá dentro dos limites de precisão relatados cerca de 95% das vezes.  Neste exemplo, a precisão relatada é 0,3, o que significa que o valor liberado estará dentro de um intervalo de largura 0,6, cerca de 95% das vezes.  Não é correto pensar nesse valor como uma barra de erro, pois o valor liberado ficará fora do intervalo de precisão relatado na taxa especificada por `alpha` e os valores fora do intervalo poderão estar fora em qualquer direção.

Os analistas podem consultar `get_accuracy` valores diferentes de `alpha` para obter intervalos de confiança mais estreitos ou mais amplos, sem incorrer em custos adicionais de privacidade.

## <a name="generate-a-histogram"></a>Gerar um histograma

A função interna `dp_histogram` cria histogramas diferencialmente privados sobre qualquer um dos seguintes tipos de dados:

- Uma variável contínua, em que o conjunto de números deve ser dividido em posições
- Uma variável booleana ou dicotômica, que pode assumir apenas dois valores
- Uma variável categórica, na qual existem categorias distintas enumeradas como sequências

Aqui está um exemplo de `Analysis` especificando posições para um histograma de variável contínua:

```python
income_edges = list(range(0, 100000, 10000))

with sn.Analysis() as analysis:
    data = sn.Dataset(path = data_path, column_names = var_names)

    income_histogram = sn.dp_histogram(
            sn.cast(data['income'], type='int', lower=0, upper=100),
            edges = income_edges,
            upper = 1000,
            null_value = 150,
            privacy_usage = {'epsilon': 0.5}
        )
```

Como os indivíduos são divididos de maneira distinta entre os compartimentos do histograma, o custo de privacidade é incorrido apenas uma vez por histograma, mesmo que o histograma inclua muitos compartimentos.

Para mais informações sobre histogramas, consulte o [caderno de histogramas](https://github.com/opendifferentialprivacy/smartnoise-samples/blob/master/analysis/histograms.ipynb).

## <a name="generate-a-covariance-matrix"></a>Gerar uma matriz de covariância

O SmartNoise oferece três funcionalidades diferentes com sua `dp_covariance` função:

- Covariância entre dois vetores
- Matriz de covariância de uma matriz
- Matriz de covariância cruzada de um par de matrizes

Aqui está um exemplo de computação de uma covariância escalar:

```python
with sn.Analysis() as analysis:
    wn_data = sn.Dataset(path = data_path, column_names = var_names)

    age_income_cov_scalar = sn.dp_covariance(
      left = sn.cast(wn_data['age'], 
      type = "FLOAT"), 
      right = sn.cast(wn_data['income'], 
      type = "FLOAT"), 
      privacy_usage = {'epsilon': 1.0},
      left_lower = 0., 
      left_upper = 100., 
      left_n = 1000, 
      right_lower = 0., 
      right_upper = 500_000.,
      right_n = 1000)
```

Para obter mais informações, consulte o [notebook de covariância](
https://github.com/opendifferentialprivacy/smartnoise-samples/blob/master/analysis/covariance.ipynb)

## <a name="next-steps"></a>Próximas etapas

- Explore os [notebooks de exemplo SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-samples/tree/master/analysis).