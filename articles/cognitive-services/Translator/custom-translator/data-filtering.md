---
title: Filtragem de dados - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Quando você envia documentos a serem usados para treinar um sistema personalizado, os documentos passam por uma série de processamento e etapas de filtragem para se prepararem para treinamento.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 1028443eaaf6c483cd7cd57289b0dcf2a9f11902
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595910"
---
# <a name="data-filtering"></a>Filtragem de dados

Quando você envia documentos a serem usados para treinar um sistema personalizado, os documentos passam por uma série de processamento e etapas de filtragem para se prepararem para treinamento. Essas etapas são explicadas aqui. O conhecimento da filtragem pode ajudar a entender a contagem de sentença exibida no tradutor personalizado, bem como as etapas que você pode tomar para preparar documentos para treinamento com um tradutor personalizado.

## <a name="sentence-alignment"></a>Alinhamento de frases
Se seu documento não estiver no formato XLIFF, TMX ou ALING, o tradutor personalizado alinha as frases de seus documentos de origem e destino um ao outro, frase por frase. O tradutor não executa o alinhamento de documento – ele segue sua nomenclatura dos documentos para localizar o documento correspondente de outro idioma. Dentro do documento, o tradutor personalizado tenta encontrar a frase correspondente no outro idioma. Ele usa as marcas do documento como as marcas HTML incorporadas para ajudar com o alinhamento.  

Se você vir uma grande discrepância entre o número de sentenças nos documentos do lado de origem e de destino, o documento poderá não ter sido paralelo em primeiro lugar ou, por outros motivos, não poderá ser alinhado. O pares de documento com uma grande diferença (> 10%) de frases em cada lado garante uma segunda olhada para garantir que eles são, realmente, paralelos. O tradutor personalizado mostra um aviso ao lado do documento se a contagem de frases é duvidosamente diferente.  


## <a name="deduplication"></a>Eliminação de duplicação
O tradutor personalizado remove as frases que estão presentes no teste e ajusta os documentos dos dados de treinamento. A remoção ocorre dinamicamente dentro da execução de treinamento, não na etapa de processamento de dados. O tradutor personalizado reporta a contagem de frases para você na visão geral do projeto antes dessa remoção.  

## <a name="length-filter"></a>Filtro de comprimento
* Remova as frases com apenas uma palavra em ambos os lados.
* Remova as frases com mais de 100 palavras em ambos os lados.  Chinês, japonês, coreano são isentos.
* Remova as frases com menos de 3 caracteres. Chinês, japonês, coreano são isentos.
* Remova frases com mais de 2.000 caracteres do chinês, japonês, coreano.
* Remova as frases com menos de 1% de caracteres alfa.
* Remova as entradas de dicionário que contém mais de 50 palavras.

## <a name="white-space"></a>Espaço em branco
* Substitua qualquer sequência de caracteres de espaço em branco, incluindo guias e sequências de CR/LF com um único caractere de espaço.
* Remover espaços à esquerda ou à direita na frase

## <a name="sentence-end-punctuation"></a>Pontuação de final de frase
Substitua vários caracteres de pontuação final de frase com uma única instância.  

## <a name="japanese-character-normalization"></a>Normalização de caracteres japoneses
Converta letras de largura inteira e dígitos em caracteres de meia largura.

## <a name="unescaped-xml-tags"></a>Marcas XML sem escape
A filtragem transformar as marcas sem escape em marcas com escape:
* `&lt;` torna-se `&amp;lt;`
* `&gt;` torna-se `&amp;gt;`
* `&amp;` torna-se `&amp;amp;`

## <a name="invalid-characters"></a>Caracteres inválidos
O tradutor personalizado remove frases que contenham o caractere Unicode U+FFFD. O caractere U+FFFD indica uma falha na conversão de codificação.

## <a name="next-steps"></a>Próximas etapas

- [Treinar um modelo](how-to-train-model.md) no Tradutor Personalizado.
