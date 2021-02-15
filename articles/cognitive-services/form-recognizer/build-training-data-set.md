---
title: Como criar um conjunto de dados de treinamento para um reconhecedor de formulário de modelo personalizado
titleSuffix: Azure Cognitive Services
description: Saiba como garantir que o conjunto de dados de treinamento seja otimizado para treinar um modelo de reconhecimento de formulário.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: ee57ccb82e771ee8ab93b09e476a94df32278069
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585102"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Criar um conjunto de dados de treinamento para um modelo personalizado

Ao usar o modelo personalizado do reconhecedor de formulário, você fornece seus próprios dados de treinamento para a operação [treinar modelo personalizado](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) , para que o modelo possa treinar seus formulários específicos do setor. Siga este guia para aprender a coletar e preparar dados para treinar o modelo com eficiência.

Você precisa de pelo menos cinco formulários preenchidos do mesmo tipo.

Se você quiser usar dados de treinamento rotulados manualmente, deverá começar com pelo menos cinco formulários preenchidos do mesmo tipo. Você ainda pode usar formulários sem rótulo além do conjunto de dados necessário.

## <a name="custom-model-input-requirements"></a>Requisitos de entrada de modelo personalizado

Primeiro, verifique se o conjunto de dados de treinamento segue os requisitos de entrada para o reconhecedor de formulário.

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="training-data-tips"></a>Dicas de dados de treinamento

Siga estas dicas adicionais para otimizar ainda mais seu conjunto de dados para treinamento.

* Se possível, use documentos PDF baseados em texto em vez de documentos baseados em imagem. Os PDFs digitalizados são tratados como imagens.
* Para formulários preenchidos, use exemplos que tenham todos os seus campos preenchidos.
* Use os formulários com diferentes valores em cada campo.
* Se suas imagens de formulário forem de qualidade inferior, use um conjunto de dados maior (10-15 imagens, por exemplo).

## <a name="upload-your-training-data"></a>Carregue seus dados de treinamento

Depois de reunir o conjunto de documentos de formulário que você usará para treinamento, você precisa carregá-lo em um contêiner de armazenamento de BLOBs do Azure. Se você não souber como criar uma conta de armazenamento do Azure com um contêiner, seguindo o guia de [início rápido do armazenamento do Azure para Portal do Azure](../../storage/blobs/storage-quickstart-blobs-portal.md). Use o nível de desempenho padrão.

Se você quiser usar dados rotulados manualmente, também precisará carregar o *.labels.js* e *.ocr.jsem* arquivos que correspondam aos seus documentos de treinamento. Você pode usar a [ferramenta de rotulagem de exemplo](./quickstarts/label-tool.md) (ou sua própria interface do usuário) para gerar esses arquivos.

### <a name="organize-your-data-in-subfolders-optional"></a>Organizar seus dados em subpastas (opcional)

Por padrão, a API de [modelo personalizado Train](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) só usará documentos de formulário localizados na raiz do seu contêiner de armazenamento. No entanto, você pode treinar com dados em subpastas se especificá-los na chamada à API. Normalmente, o corpo da chamada de [modelo personalizado Train](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) tem o seguinte formato, em que `<SAS URL>` é a URL de assinatura de acesso compartilhado do seu contêiner:

```json
{
  "source":"<SAS URL>"
}
```

Se você adicionar o conteúdo a seguir ao corpo da solicitação, a API será treinada com documentos localizados em subpastas. O `"prefix"` campo é opcional e limitará o conjunto de dados de treinamento a arquivos cujos caminhos começam com a cadeia de caracteres especificada. Portanto, um valor de `"Test"` , por exemplo, fará com que a API examine apenas os arquivos ou pastas que começam com a palavra "Test".

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  },
  "useLabelFile": false
}
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como criar um conjunto de dados de treinamento, siga um guia de início rápido para treinar um modelo de reconhecedor de formulário personalizado e comece a usá-lo em seus formulários.

* [Treinar um modelo e extrair dados de formulário usando a biblioteca de cliente ou a API REST](./quickstarts/client-library.md)
* [Treine com rótulos usando a ferramenta de rotulagem de exemplo](./quickstarts/label-tool.md)

## <a name="see-also"></a>Confira também

* [O que é o Reconhecimento de Formulários?](./overview.md)