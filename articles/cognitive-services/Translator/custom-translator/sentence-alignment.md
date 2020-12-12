---
title: Pareamento e alinhamento de frases - Custom Translator
titleSuffix: Azure Cognitive Services
description: Durante a execução do treinamento, as sentenças presentes em documentos paralelos são emparelhadas ou alinhadas. Tradutor personalizado aprende traduções uma frase de cada vez, lendo uma frase, a tradução desta frase. Em seguida, alinha palavras e frases nessas duas frases entre si.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: e5d360933cbeb611046aede6164ec2e2fa497664
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347788"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Emparelhamento de frases e alinhamento em documentos paralelos

Depois que os documentos são carregados, as frases presentes em documentos paralelos são emparelhadas ou alinhadas. O Custom Translator relata o número de sentenças que conseguiu emparelhar como as Sentenças Alinhadas em cada um dos conjuntos de dados.

## <a name="pairing-and-alignment-process"></a>Processo de emparelhamento e alinhamento

O Custom Translator aprende traduções de sentenças uma sentença de cada vez. Ele lê uma sentença da fonte e, em seguida, a tradução desta frase do alvo. Em seguida, alinha palavras e frases nessas duas frases entre si. Este processo permite criar um mapa das palavras e frases em uma frase para as palavras e frases equivalentes na tradução desta frase. O alinhamento tenta garantir que o sistema treine em sentenças que são traduções uma da outra.

## <a name="pre-aligned-documents"></a>Documentos previamente alinhados

Se você souber que possui documentos paralelos, poderá substituir o alinhamento de sentenças fornecendo arquivos de texto pré-alinhados. Você pode extrair todas as sentenças de ambos os documentos em um arquivo de texto, organizar uma sentença por linha e fazer o upload com uma extensão `.align`. A extensão `.align` sinaliza ao Custom Translator que deve pular o alinhamento das frases.

Para obter melhores resultados, verifique se você tem uma frase por linha em seus arquivos. Não tem caracteres de nova linha dentro de uma frase, pois isso causará alinhamentos ruins.

## <a name="suggested-minimum-number-of-sentences"></a>Número mínimo sugerido de frases

Para que um treinamento tenha sucesso, a tabela a seguir mostra o número mínimo de frases necessárias em cada tipo de documento.Essa limitação é uma rede de segurança para garantir que suas frases paralelas contenham um vocabulário exclusivo suficiente para treinar com êxito um modelo de tradução. A diretriz geral é ter mais frases paralelas no domínio de qualidade de tradução humana deve produzir modelos de alta qualidade.

| Tipo de documento   | Contagem de sentenças mínimas sugeridas | Contagem máxima de sentenças |
|------------|--------------------------------------------|--------------------------------|
| Treinamento   | 10.000                                     | Nenhum limite superior                 |
| Ajuste     | 500                                      | 2\.500       |
| Testes    | 500                                      | 2\.500  |
| Dicionário | 0                                          | Nenhum limite superior                 |

> [!NOTE]
> - O treinamento não será iniciado e falhará se a contagem mínima de sentenças 10.000 para treinamento não for atendida. 
> - O ajuste e o teste são opcionais. Se você não os fornecer, o sistema removerá uma porcentagem apropriada do treinamento a ser usado para validação e teste. 
> - Você pode treinar um modelo usando apenas dados do dicionário. Veja [o que é o dicionário](./what-is-dictionary.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba como usar um [dicionário](what-is-dictionary.md) no tradutor personalizado.
