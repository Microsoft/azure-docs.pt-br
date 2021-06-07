---
title: Aprendizado de máquina e ia com ONNX no Azure SQL Edge
description: O Machine Learning no Azure SQL Edge dá suporte a modelos no formato de troca de rede neural aberto (ONNX). O ONNX é um formato aberto que você pode usar para trocar modelos entre várias estruturas e ferramentas de aprendizado de máquina.
keywords: Implantar o SQL no Edge
services: sql-edge
ms.service: sql-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 05/19/2020
ms.openlocfilehash: 5dc3d44ac4396897fd43831d51ee628bb06048cb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93392054"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-edge"></a>Aprendizado de máquina e ia com ONNX no SQL Edge

O Machine Learning no Azure SQL Edge dá suporte a modelos no formato de [troca de rede neural aberto (ONNX)](https://onnx.ai/) . O ONNX é um formato aberto que você pode usar para trocar modelos entre várias [estruturas e ferramentas de aprendizado de máquina](https://onnx.ai/supported-tools).

## <a name="overview"></a>Visão geral

Para inferir modelos de aprendizado de máquina no SQL do Azure no Edge, primeiro você precisará obter um modelo. Pode ser um modelo pré-treinado ou um modelo personalizado treinado com a estrutura de sua escolha. O SQL do Azure no Edge é compatível com o formato ONNX e você precisará converter o modelo nesse formato. A precisão do modelo não deve ser afetada e, assim que você tiver o modelo ONNX, poderá implantá-lo no SQL do Azure no Edge e usar a [pontuação nativa com a função T-SQL PREDICT](/sql/advanced-analytics/sql-native-scoring/).

## <a name="get-onnx-models"></a>Obter modelos ONNX

Para obter um modelo no formato ONNX:

- **Serviços de compilação de modelo**: Serviços como o [recurso Machine Learning automatizado no Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) e o [serviço Visão Personalizada do Azure](../cognitive-services/custom-vision-service/getting-started-build-a-classifier.md) são compatíveis com a exportação direta do modelo treinado no formato ONNX.

- [**Converter e/ou exportar modelos existentes**](https://github.com/onnx/tutorials#converting-to-onnx-format): Várias estruturas de treinamento (por exemplo, [PyTorch](https://pytorch.org/docs/stable/onnx.html), Chainer e Caffe2) são compatíveis com a funcionalidade de exportação nativa para o ONNX, o que permite salvar o modelo treinado em uma versão específica do formato ONNX. Para estruturas incompatíveis com a exportação nativa, há pacotes instaláveis autônomos do conversor de ONNX que permitem converter modelos treinados de diversas estruturas de aprendizado de máquina no formato ONNX.

     **Estruturas com suporte**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [Tensorflow](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [Scikit-learn](https://github.com/onnx/sklearn-onnx)
   * [CoreML](https://github.com/onnx/onnxmltools)
    
    Para obter a lista completa de estruturas e exemplos com suporte, consulte [Conversão no formato ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format).

## <a name="limitations"></a>Limitações

Atualmente, nem todos os modelos ONNX são compatíveis com o SQL do Azure no Edge. A compatibilidade se limita a modelos com **tipos de dados numéricos**:

- [int e bigint](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [real e float](/sql/t-sql/data-types/float-and-real-transact-sql).
  
Outros tipos numéricos podem ser convertidos em tipos compatíveis usando [CAST e CONVERT](/sql/t-sql/functions/cast-and-convert-transact-sql).

As entradas do modelo devem ser estruturadas de forma que cada entrada para o modelo corresponda a uma única coluna de uma tabela. Por exemplo, se você estiver usando uma estrutura de dados pandas para treinar um modelo, cada entrada deverá ser uma coluna separada para o modelo.

## <a name="next-steps"></a>Próximas etapas

- [Implantar o SQL do Azure no Edge por meio do portal do Azure](deploy-portal.md)
- [Implantar um modelo ONNX no Azure SQL Edge ](deploy-onnx.md)