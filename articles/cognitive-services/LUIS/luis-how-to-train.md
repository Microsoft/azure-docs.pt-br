---
title: Treinar aplicativo-LUIS
titleSuffix: Azure Cognitive Services
description: O treinamento é o processo de ensinar sua versão do aplicativo LUIS (Reconhecimento vocal) para melhorar o reconhecimento vocal natural. Treine seu aplicativo de LUIS após atualizações ao modelo, como adicionar, editar, rotular ou excluir entidades, intenções ou enunciados.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 11/15/2019
ms.openlocfilehash: 47b006932aace3149dd94e136e334c1b6e5bfcef
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762692"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Treinar sua versão ativa do aplicativo LUIS

O treinamento é o processo de ensinar seu aplicativo de LUIS (Serviço Inteligente de Reconhecimento Vocal) para melhorar seu reconhecimento vocal de idioma natural. Treine seu aplicativo de LUIS após atualizações ao modelo, como adicionar, editar, rotular ou excluir entidades, intenções ou enunciados.

O treinamento e o [teste](luis-concept-test.md) de um aplicativo é um processo iterativo. Após treinar seu aplicativo de LUIS, teste-o com enunciados de amostra para ver se as intenções e as entidades são corretamente reconhecidas. Caso não sejam, faça atualizações ao aplicativo de LUIS, treine e teste novamente.

O treinamento é aplicado para a versão ativa no portal do LUIS.

## <a name="how-to-train-interactively"></a>Como treinar interativamente

Para iniciar o processo iterativo no [portal LUIS](https://www.luis.ai), primeiro você precisa treinar seu aplicativo LUIS pelo menos uma vez. Verifique se que cada intenção tem pelo menos um enunciado antes do treinamento.

1. Acesse seu aplicativo selecionando seu nome na página **Meus Aplicativos**.

1. Em seu aplicativo, selecione **Treinar** no painel superior.

1. Quando o treinamento for concluído, uma notificação aparecerá na parte superior do navegador.

## <a name="training-date-and-time"></a>Data e hora de treinamento

A data e a hora de treinamento são GMT + 2.

## <a name="train-with-all-data"></a>Treinar com todos os dados

O treinamento usa um pequeno percentual de amostragem negativa. Você pode usar todos os dados disponíveis em vez disso usando o portal ou a API. 

### <a name="using-the-luis-portal"></a>Usando o portal do LUIS

Faça logon no [portal do Luis](https://www.luis.ai/) e clique em seu aplicativo. Selecione **gerenciar** na parte superior da tela e, em seguida, selecione **configurações** e habilite ou desabilite a opção de **treinamento de uso determinístico** . Quando desabilitado, o treinamento usará todos os dados disponíveis.

![Um botão para habilitar ou desabilitar o treinamento não determinístico](./media/non-determinstic-training.png)

### <a name="using-the-version-settings-api"></a>Usando a API de configurações de versão

Use a [API de configurações de versão](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) com o `UseAllTrainingData` definido como true para desativar esse recurso.

## <a name="unnecessary-training"></a>Treinamento desnecessário

Não é preciso treinar após cada alteração. O treinamento deve ser feito depois que um grupo de alterações são aplicadas ao modelo e a próxima etapa em que você deseja fazer é testar ou publicar. Se você não precisar de teste ou publicar, o treinamento não é necessário.

## <a name="training-with-the-rest-apis"></a>Trenamento com as APIs REST

O treinamento no portal do LUIS é uma etapa única para pressionar o botão **Treinar**. Treinamento com as APIs REST é um processo de duas etapas. A primeira é [solicitar treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) com HTTP POST. Em seguida, solicite o [status de treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) com HTTP Get.

Para saber quando o treinamento está concluído, você precisa sondar o status até que todos os modelos sejam treinados com êxito.

## <a name="next-steps"></a>Próximas etapas

* [Teste interativo](luis-interactive-test.md)
* [Teste em lote](luis-how-to-batch-test.md)
