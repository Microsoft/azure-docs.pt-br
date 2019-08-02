---
title: Perguntas frequentes - Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Fornece respostas a perguntas frequentes sobre o Custom Translator.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: reference
ms.openlocfilehash: 25b7e7015b32609356eb138c86fbe537a87a7a22
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595825"
---
# <a name="frequently-asked-questions"></a>Perguntas frequentes

Este artigo contém respostas para perguntas frequentes sobre o [Tradutor personalizado](https://portal.customtranslator.azure.ai).

## <a name="what-are-the-current-restrictions-in-custom-translator"></a>Quais são as restrições atuais no Custom Translator?

Existem restrições e limites em relação ao tamanho do arquivo, treinamento do modelo e implantação do modelo. Tenha estas restrições em mente ao configurar seu treinamento para criar um modelo no Tradutor Personalizado.

- Arquivos enviado devem ser menor que 100 MB de tamanho.
- Dados monolíngues não são suportados.

## <a name="when-should-i-request-deployment-for-a-translation-system-that-has-been-trained"></a>Quando devo solicitar a implantação de um sistema de tradução que foi treinado?

Pode levar vários treinamentos para criar o sistema de tradução ideal para o seu projeto. Você pode tentar usar mais dados de treinamento ou dados cuidadosamente filtrados, se a pontuação da BLEU e / ou os resultados do teste não forem satisfatórios. Você deve ser rigoroso e cuidadoso ao projetar seu conjunto de ajustes e seu conjunto de testes, para ser totalmente representativo da terminologia e do estilo do material que deseja traduzir. Você pode ser mais liberal ao compor seus dados de treinamento e experimentar diferentes opções. Solicite uma implantação do sistema quando estiver satisfeito com as traduções nos resultados do teste do sistema, não tiver mais dados para adicionar ao treinamento para melhorar seu sistema treinado e desejar acessar o modelo treinado por meio de APIs.

## <a name="how-many-trained-systems-can-be-deployed-in-a-project"></a>Quantos sistemas treinados podem ser implantados em um projeto?

Apenas um sistema treinado pode ser implantado por projeto. Pode ser necessário vários treinamentos para criar um sistema de tradução adequado para o seu projeto e incentivamos você a solicitar a implantação de um treinamento que ofereça o melhor resultado. Você pode determinar a qualidade do treinamento pela pontuação do BLEU (quanto maior melhor) e consultando os revisores antes de decidir que a qualidade das traduções é adequada para implantação.

## <a name="when-can-i-expect-my-trainings-to-be-deployed"></a>Quando esperar meu treinamentos para ser implantado?

A implantação geralmente leva menos de uma hora.

## <a name="how-do-you-access-a-deployed-system"></a>Como você acessa um sistema implantado?

Os sistemas implantados podem ser acessados através da API de tradução de Texto do Microsoft Translator V3, especificando o CategoryID. Mais informações sobre a API de tradução de Texto do tradutor podem ser encontradas na página da Web da [Referência da API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference).

## <a name="how-do-i-skip-alignment-and-sentence-breaking-if-my-data-is-already-sentence-aligned"></a>Como fazer alinhamento e a frase quebrar se meus dados já estão alinhado de frase?

O Custom Translator ignora o alinhamento de sentenças e quebra de frases para arquivos TMX e para arquivos de texto com a extensão `.align`. O arquivo `.align` dá aos usuários uma opção para ignorar o processo de alinhamento e quebra de frases do Tradutor Personalizado para os arquivos perfeitamente alinhados e que não precisam de mais processamento. Recomendamos usar a extensão `.align` apenas para arquivos perfeitamente alinhados.

Se o número de sentenças extraídas não corresponder aos dois arquivos com o mesmo nome base, o Custom Translator ainda executará o alinhador de sentenças em arquivos `.align`.

## <a name="i-tried-uploading-my-tmx-but-it-says-document-processing-failed"></a>Eu tentei carregar meu TMX, mas ele diz "processamento de documento falhou".

Assegure-se de que o TMX esteja em conformidade com a Especificação TMX 1.4b em <https://www.gala-global.org/tmx-14b>.
