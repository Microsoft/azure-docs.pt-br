---
title: Como criar um conjunto de dados de treinamento para um modelo personalizado - reconhecedor de formulário
titleSuffix: Azure Cognitive Services
description: Saiba como garantir que seu conjunto de dados de treinamento é otimizado para treinar um modelo de formulário reconhecedor.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 643f0d6dd3ee073bd19f8697346689523032ad9f
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592636"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Criar um conjunto de dados de treinamento para um modelo personalizado

Quando você usa o modelo de formulário reconhecedor personalizado, você fornecer seus próprios dados de treinamento para que o modelo poderá fazê-lo aos seus formulários específicos do setor. Você pode treinar um modelo com cinco formulários preenchidos ou um formulário vazio (você deve incluir o palavra "vazio" no nome do arquivo) além de dois formulários preenchidos. Mesmo se você tiver suficiente formulários preenchidos para treinar com, a adição de um formulário vazio ao seu conjunto de dados de treinamento pode melhorar a precisão do modelo.

## <a name="training-data-tips"></a>Dicas de dados de treinamento

É importante usar um conjunto de dados que é otimizado para treinamento. Use as dicas a seguir para garantir que você obtém os melhores resultados do [modelo de treinamento](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) operação:

* Se possível, use os documentos em PDF baseado em texto, em vez de documentos com base em imagem. PDFs digitalizados são tratadas como imagens.
* Use um formulário vazio e dois formulários preenchidos, se você os tiver disponíveis.
* Para formulários preenchidos, use os exemplos que têm todos os seus campos preenchidos.
* Use formulários com valores diferentes em cada campo.
* Se suas imagens de formulário de qualidade inferior, use um conjunto de dados maior (10 a 15 imagens, por exemplo).

## <a name="general-input-requirements"></a>Requisitos gerais de entrada

Verifique se que seu conjunto de dados de treinamento também segue os requisitos de entrada para todo o conteúdo do reconhecedor de formulário. 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>Carregar seus dados de treinamento

Quando você reunimos o conjunto de documentos de forma que você usará para treinamento, você precisa carregá-lo em um contêiner de armazenamento de BLOBs do Azure. Se você não souber como criar uma conta de armazenamento do Azure com um contêiner, seguindo a [início rápido do armazenamento do Azure para o portal do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

### <a name="organize-your-data-in-subfolders-optional"></a>Organizar seus dados em subpastas (opcionais)

Por padrão, o [modelo de treinamento](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) API usará apenas os documentos de forma que estão localizados na raiz do seu contêiner de armazenamento. No entanto, você pode treinar com os dados em subpastas, se você especificá-lo na chamada à API. Normalmente, o corpo dos [modelo de treinamento](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) chamada tem o seguinte formato, onde `<SAS URL>` é a URL de assinatura de acesso compartilhado do seu contêiner:

```json
{
  "source":"<SAS URL>"
}
```

Se você adicionar o conteúdo a seguir ao corpo da solicitação, a API treinada com documentos localizados em subpastas. O `"prefix"` campo é opcional e limitará o conjunto de dados de treinamento para arquivos cujos caminhos começam com a cadeia de caracteres fornecida. Portanto, um valor de `"Test"`, por exemplo, fará com que a API para examinar apenas os arquivos ou pastas que começam com a palavra "Test".

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  }
}
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como criar um conjunto de dados de treinamento, siga um início rápido para treinar um modelo de formulário reconhecedor personalizado e começar a usá-lo em seus formulários.

* [Início Rápido: Treinar um modelo e extrair dados de formulário usando o cURL](./quickstarts/curl-train-extract.md)
* [Início Rápido: Treinar um modelo e extrair dados de formulário usando a API REST com o Python](./quickstarts/python-train-extract.md)

