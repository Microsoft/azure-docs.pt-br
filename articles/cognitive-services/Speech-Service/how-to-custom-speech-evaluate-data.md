---
title: Avaliar a precisão do serviço de Fala Personalizada-fala
titleSuffix: Azure Cognitive Services
description: Neste documento, você aprenderá a medir de forma quantitativa a qualidade do nosso modelo de fala-para-texto ou do modelo personalizado. Áudio e dados de transcrição com rótulo humano são necessários para testar a precisão e 30 minutos a 5 horas de áudio representativo devem ser fornecidos.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: bd8bbc28247ecd924db25cb4b916d1d466065606
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562862"
---
# <a name="evaluate-custom-speech-accuracy"></a>Avaliar a precisão de Fala Personalizada

Neste documento, você aprenderá a medir de forma quantitativa a qualidade do modelo de fala-para-texto da Microsoft ou de seu modelo personalizado. Áudio e dados de transcrição com rótulo humano são necessários para testar a precisão e 30 minutos a 5 horas de áudio representativo devem ser fornecidos.

## <a name="what-is-word-error-rate-wer"></a>O que é a taxa de erros do Word (WER)?

O padrão do setor para medir a precisão do modelo é o WER ( *taxa de erros do Word* ). O WER conta o número de palavras incorretas identificadas durante o reconhecimento e divide pelo número total de palavras fornecidas na transcrição de rótulo humano. Por fim, esse número é multiplicado por 100% para calcular o WER.

![Fórmula do WER](./media/custom-speech/custom-speech-wer-formula.png)

Palavras incorretamente identificadas se enquadram em três categorias:

* Inserção (I): Palavras que são adicionadas incorretamente à transcrição de hipótese
* Exclusão (D): Palavras que não são detectadas na transcrição da hipótese
* Substituição (ões): Palavras que foram substituídas entre referência e hipótese

Veja um exemplo:

![Exemplo de palavras identificadas incorretamente](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>Resolver erros e aprimorar o WER

Você pode usar o WER nos resultados de reconhecimento do computador para avaliar a qualidade do modelo que você está usando com seu aplicativo, ferramenta ou produto. Um WER de 5% a 10% é considerado uma boa qualidade e está pronto para uso. Um WER de 20% é aceitável, mas talvez você queira considerar treinamento adicional. Um WER de 30% ou mais sinais de baixa qualidade e requer personalização e treinamento.

A forma como os erros são distribuídos é importante. Quando muitos erros de exclusão são encontrados, isso geralmente ocorre devido à intensidade do sinal de áudio fraco. Para resolver esse problema, você precisará coletar dados de áudio mais perto da origem. Erros de inserção significam que o áudio foi registrado em um ambiente barulhento e crosstalk pode estar presente, causando problemas de reconhecimento. Erros de substituição geralmente são encontrados quando uma amostra insuficiente de termos específicos do domínio foi fornecida como transcrições com rótulo humano ou texto relacionado.

Ao analisar arquivos individuais, você pode determinar quais tipos de erros existem e quais erros são exclusivos para um arquivo específico. Entender os problemas no nível de arquivo ajudará você a aprimorar as melhorias.

## <a name="create-a-test"></a>Criar um teste

Se você quiser testar a qualidade do modelo de linha de base de fala para texto da Microsoft ou de um modelo personalizado que você tenha treinado, você pode comparar dois modelos lado a lado para avaliar a precisão. A comparação inclui resultados de WER e de reconhecimento. Normalmente, um modelo personalizado é comparado com o modelo de linha de base da Microsoft.

Para avaliar modelos lado a lado:

1. Navegue até a **> de fala para texto fala personalizada teste de >** .
2. Clique em **Adicionar teste**.
3. Selecione **avaliar exatidão**. Dê ao teste um nome, uma descrição e selecione seu conjunto de testes de áudio e de transcrição com rótulo humano.
4. Selecione até dois modelos que você gostaria de testar.
5. Clique em **Criar**.

Depois que o teste tiver sido criado com êxito, você poderá comparar os resultados lado a lado.

## <a name="side-by-side-comparison"></a>Comparação lado a lado

Depois que o teste for concluído, indicado pela alteração do status para *êxito*, você encontrará um número do WER para ambos os modelos incluídos no teste. Clique no nome do teste para exibir a página de detalhes de teste. Essa página de detalhes lista todos os declarações no conjunto de seus conjuntos de anotações, indicando os resultados de reconhecimento dos dois modelos junto com a transcrição do conjunto de resultados enviado. Para ajudar a inspecionar a comparação lado a lado, você pode alternar vários tipos de erro, incluindo inserção, exclusão e substituição. Ao ouvir o áudio e comparar os resultados de reconhecimento em cada coluna, que mostra a transcrição com rótulo humano e os resultados de dois modelos de fala em texto, você pode decidir qual modelo atende às suas necessidades e em que outros treinamentos e aprimoramentos são Necessário.

## <a name="next-steps"></a>Próximas etapas

* [Treinar seu modelo](how-to-custom-speech-train-model.md)
* [Implantar seu modelo](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Preparar e testar seus dados](how-to-custom-speech-test-data.md)
* [Inspecione seus dados](how-to-custom-speech-inspect-data.md)
