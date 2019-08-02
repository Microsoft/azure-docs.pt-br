---
title: Pareamento e alinhamento de frases - Custom Translator
titleSuffix: Azure Cognitive Services
description: Durante a execução do treinamento, as sentenças presentes em documentos paralelos são emparelhadas ou alinhadas. Tradutor personalizado aprende traduções uma frase de cada vez, lendo uma frase, a tradução desta frase. Em seguida, alinha palavras e frases nessas duas frases entre si.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: e9bc5c876da6bd2be1b22b389b819e51330b2e50
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595465"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Emparelhamento de frases e alinhamento em documentos paralelos

Durante o treinamento, as frases presentes em documentos paralelos são emparelhadas ou alinhadas. O Custom Translator relata o número de sentenças que conseguiu emparelhar como as Sentenças Alinhadas em cada um dos conjuntos de dados.

## <a name="pairing-and-alignment-process"></a>Processo de emparelhamento e alinhamento

O Custom Translator aprende traduções de sentenças uma sentença de cada vez. Ele lê uma sentença da fonte e, em seguida, a tradução desta frase do alvo. Em seguida, alinha palavras e frases nessas duas frases entre si. Este processo permite criar um mapa das palavras e frases em uma frase para as palavras e frases equivalentes na tradução desta frase. O alinhamento tenta garantir que o sistema treine em sentenças que são traduções uma da outra.

## <a name="pre-aligned-documents"></a>Documentos previamente alinhados

Se você souber que possui documentos paralelos, poderá substituir o alinhamento de sentenças fornecendo arquivos de texto pré-alinhados. Você pode extrair todas as sentenças de ambos os documentos em um arquivo de texto, organizar uma sentença por linha e fazer o upload com uma extensão `.align`. A extensão `.align` sinaliza ao Custom Translator que deve pular o alinhamento das frases.

Para obter melhores resultados, verifique se você tem uma frase por linha em seus arquivos. Não tem caracteres de nova linha dentro de uma frase, pois isso causará alinhamentos ruins.

## <a name="suggested-minimum-number-of-extracted-and-aligned-sentences"></a>Número mínimo sugerido de sentenças extraídas e alinhadas

Para que um treinamento seja bem-sucedido, a tabela abaixo mostra o número mínimo de sentenças extraídas e frases alinhadas necessárias em cada conjunto de dados. O número mínimo sugerido de sentenças extraídas é muito maior do que o número mínimo sugerido de sentenças alinhadas para levar em conta o fato de que o alinhamento da frase pode não conseguir alinhar todas as sentenças extraídas com êxito.

| Conjunto de dados   | Sugerido contagem mínima de sentença extraídos | Sugerido contagem mínima de sentença alinhado | Contagem máxima de sentença alinhado |
|------------|--------------------------------------------|------------------------------------------|--------------------------------|
| Treinamento   | 10.000                                     | 2\.000                                    | Nenhum limite superior                 |
| Ajuste     | 2\.000                                      | 500                                      | 2\.500                          |
| Testes    | 2\.000                                      | 500                                      | 2\.500                          |
| Dicionário | 0                                          | 0                                        | Nenhum limite superior                 |

## <a name="next-steps"></a>Próximas etapas

- Saiba como usar um [dicionário](what-is-dictionary.md) no tradutor personalizado.
