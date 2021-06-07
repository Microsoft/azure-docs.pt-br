---
title: Modelos personalizados – reconhecedor de formulário
titleSuffix: Azure Cognitive Services
description: Aprenda os conceitos relacionados aos modelos personalizados de API do reconhecedor de formulário – uso e limites.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: lajanuar
ms.openlocfilehash: 74ced2ecadb5ccfe5cdb7966550e469ae4f8ab31
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612255"
---
# <a name="form-recognizer-custom-models"></a>Modelos personalizados do reconhecedor de formulário

O reconhecedor de formulário usa tecnologia avançada de aprendizado de máquina para analisar e extrair dados de seus formulários e documentos. Um modelo de reconhecimento de formulário é uma representação de dados extraídos que são usados como uma referência para analisar seu conteúdo específico. Há dois tipos de modelos do reconhecedor de formulário:

* **Modelos personalizados**. Modelos personalizados do reconhecedor de formulário representam dados extraídos de _formulários_ específicos para seu negócio. Modelos personalizados devem ser treinados para analisar seus dados de formulário distintos.

* **Modelos predefinidos**. O reconhecedor de formulário atualmente dá suporte a modelos predefinidos para _recibos, cartões de visita, cartões de identificação_ e _faturas_. Modelos predefinidos detectam e extraem informações de imagens de documentos e retornam os dados extraídos em uma saída JSON estruturada.

## <a name="what-does-a-custom-model-do"></a>O que um modelo personalizado faz?

Com o reconhecedor de formulário, você pode treinar um modelo que irá extrair informações de formulários relevantes para seu caso de uso. Você só precisa de cinco exemplos do mesmo tipo de formulário para começar. Seu modelo personalizado pode ser treinado com ou sem rótulos de conjuntos de valores.

## <a name="create-use-and-manage-your-custom-model"></a>Criar, usar e gerenciar seu modelo personalizado

Em um alto nível, as etapas para criar, treinar e usar seu modelo personalizado são as seguintes:

> [!div class="nextstepaction"]
> [1. montar seu conjunto de seus conjuntos de seu treinamento](build-training-data-set.md#custom-model-input-requirements)

A criação de um modelo personalizado começa com o estabelecimento do seu conjunto de seus de treinamento. Você precisará de um mínimo de cinco formulários concluídos do mesmo tipo para seu conjunto de exemplo. Eles podem ser de tipos de arquivo diferentes e conter texto e manuscrito. Seus formulários devem ser do mesmo tipo de documento e seguir os [requisitos de entrada](build-training-data-set.md#custom-model-input-requirements) para o reconhecedor de formulário.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;

> [!div class="nextstepaction"]
> [2. Carregue seu conjunto de seus de treinamento](build-training-data-set.md#upload-your-training-data)

Você precisará carregar seus dados de treinamento em um contêiner de armazenamento de BLOBs do Azure. Se você não souber como criar uma conta de armazenamento do Azure com um contêiner, *consulte* [início rápido do armazenamento do Azure para Portal do Azure](../../storage/blobs/storage-quickstart-blobs-portal.md). Use o tipo de preço gratuito (F0) para experimentar o serviço e atualizar mais tarde para uma camada paga para produção.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;
> [!div class="nextstepaction"]
> [3. treinar seu modelo personalizado](quickstarts/client-library.md#train-a-custom-model)

Você pode treinar seu modelo [sem](quickstarts/client-library.md#train-a-model-without-labels) ou [com conjuntos de](quickstarts/client-library.md#train-a-model-with-labels) dados rotulados. Conjuntos de dados sem rótulo dependem exclusivamente da API de layout para detectar e identificar informações de chave sem a inclusão de entrada humana. Os conjuntos de dados rotulados também dependem da API de layout, mas a entrada humana suplementar é incluída, como seus rótulos específicos e locais de campo. Para usar dados rotulados e sem rótulo, comece com pelo menos cinco formulários concluídos do mesmo tipo para os dados de treinamento rotulados e, em seguida, adicione dados sem rótulo ao conjunto de dados necessário.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;  

>[!div class="nextstepaction"]
> [4. analisar documentos com seu modelo personalizado](quickstarts/client-library.md#analyze-forms-with-a-custom-model)

Teste seu modelo treinado recentemente usando um formulário que não era parte do conjunto de testes de treinamento. Você pode continuar a fazer um treinamento adicional para melhorar o desempenho do seu modelo personalizado.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;

> [!div class="nextstepaction"]
> [5. gerenciar seus modelos personalizados](quickstarts/client-library.md#manage-custom-models)

A qualquer momento, você pode exibir uma lista de todos os modelos personalizados em sua assinatura, recuperar informações sobre um modelo personalizado específico ou excluir um modelo personalizado de sua conta.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a biblioteca de cliente do reconhecedor de formulário explorando nossa documentação de referência de API.
> [!div class="nextstepaction"]
> [Referência da API do reconhecedor de formulário](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291)
>
