---
title: Exibir detalhes do modelo - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: A guia Modelos, em qualquer projeto, mostra detalhes de cada modelo como nome do modelo, status do modelo, pontuação BLEU, treinamento, ajuste, contagem de frase de teste.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 13f3d88ad69d2acc64b9a6469415eceaf22fa491
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61456388"
---
# <a name="view-model-details"></a>Exibir detalhes do modelo

A guia Modelos no projeto mostra todos os modelos nesse projeto. Todos os modelos treinados para esse projeto estão listados nesta guia.

Para cada modelo do projeto, esses detalhes são exibidos.

1.  Nome do modelo: Mostra o nome do modelo de um modelo especificado.

2.  Status: Mostra o status de um modelo especificado. O novo treinamento terá um status de Enviado até que seja aceito. O status será alterado para processamento de dados, enquanto o serviço avalia o conteúdo dos documentos. Quando a avaliação dos documentos estiver concluída, o status será alterado para Em Execução e você poderá ver o número de frases que fazem parte do treinamento, incluindo os conjuntos de ajustes e testes criados automaticamente para você. Abaixo está uma lista de status do modelo que descreve o estado dos modelos.

    -  Enviado: Especifica que o back-end está processando os documentos para esse modelo.

    -  TrainingQueued: Especifica que o treinamento está sendo colocado na fila do sistema MT para esse modelo.

    -  Em execução: Especifica que o treinamento está sendo executado no sistema MT para esse modelo.

    -  Êxito: Especifica que o treinamento teve êxito no sistema MT e um modelo está disponível. Nesse status, uma pontuação BLEU é exibida para esse modelo.

    -  Implantado: Especifica que o modelo treinado com êxito é enviado ao sistema MT para implantação.

    -  Cancelando Implantação: Especifica que a implantação do modelo implantado está sendo cancelada.

    -  Implantação Cancelada: Especifica que o processo de cancelamento da implantação de um modelo foi concluído com êxito.

    -  Falha de Treinamento: Especifica que o treinamento falhou. Se ocorrer uma falha de treinamento, tente novamente o trabalho de treinamento. Se o erro persistir, contate-nos. Não exclua o modelo com falha.

    - DataProcessingFailed: Especifica que o processamento de dados falhou para um ou mais documentos pertencentes ao modelo.

    - DeploymentFailed: Especifica que a implantação de modelo falhou.

    - MigratedDraft: Especifica que o modelo está no estado de rascunho após a migração do Hub para o Tradutor Personalizado.

4.  Pontuação BLEU: mostra a pontuação BLEU (Bilingual Evaluation Understudy) do modelo, indicando a qualidade do sistema de tradução. Essa pontuação informa o quanto as traduções feitas pelo sistema de tradução resultantes desse treinamento correspondem às frases de referência no conjunto de dados de teste. A pontuação BLEU será exibida se o treinamento for concluído com êxito. Se o treinamento não estiver concluído/com falha, nenhuma pontuação BLEU será exibida.

5.  Contagem de Sentenças de Treinamento: Mostra o número total de sentenças usadas como o conjunto de treinamento.

6.  Contagem de Sentenças de Ajuste: Mostra o número total de sentenças usadas como o conjunto de ajuste.

7.  Contagem de Sentenças de Treinamento: Mostra o número total de sentenças usadas como o conjunto de teste.

8.  Contagem de Sentenças Mono: Mostra o número total de sentenças usadas como o conjunto Mono.

9.  Botão de ação Implantar: Para um modelo treinado com êxito, ele mostra o botão “Implantar” se ele não foi implantado. Se houver um modelo implantado, um botão "Desimplantar" será exibido.

10. Excluir: Use esse botão se desejar excluir o modelo. A exclusão de um modelo não excluirá nenhum documento usado para criar esse modelo.

    ![Exibir detalhes do modelo](media/how-to/how-to-view-model-details.png)

>[!Note]
>Para comparar treinamentos consecutivos para os mesmos sistemas, é importante manter o conjunto de ajustes e o conjunto de testes constantes.

## <a name="view-model-training-details"></a>Exibir detalhes do treinamento do modelo

Quando o treinamento estiver concluído, você poderá revisar os detalhes do treinamento na página de detalhes. Selecione um projeto, localize e selecione a guia de modelos e escolha um modelo.

A página de modelo tem duas guias: Detalhes do treinamento e Teste.

1.  **Detalhes do Treinamento:** Essa guia mostra a lista de documentos usados no treinamento:

    -  Nome dos Documentos: Esse campo mostra o nome do documento

    -  Tipo de Documento: Esse campo mostra se o documento é paralelo/Mono.

    -  Contagem de sentenças no idioma de origem: Esse campo mostra o número de sentenças que existem como parte do idioma de origem.

    -  Contagem de sentenças no idioma de destino: Esse campo mostra o número de sentenças que existem como parte do idioma de destino.

    -  Sentenças Alinhadas: Esse campo mostra que o número de sentenças que foram alinhadas pelo Tradutor Personalizado durante o processo de alinhamento.

    -  Sentenças Usadas: Esse campo mostra o número de sentenças que foram usadas pelo Tradutor Personalizado durante esse treinamento.

    ![Detalhes do treinamento de modelo](media/how-to/how-to-model-training-details.png)

2.  **Teste:** Essa guia mostra os detalhes do teste de um treinamento com êxito.

## <a name="next-steps"></a>Próximas etapas

- Revise os [resultados do teste](how-to-view-system-test-results.md) e analise os resultados do treinamento.
