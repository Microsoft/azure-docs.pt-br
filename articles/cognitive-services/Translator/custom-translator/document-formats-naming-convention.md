---
title: Formatos de documentos e convenções de nomenclatura - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Este é um guia sobre formatos de documentos e convenção de nomenclatura no Tradutor Personalizado. Esse conceito ajuda a gerenciar nomes de documentos melhor e evitar conflitos de nomenclatura.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 9598d3c1d339d1c37b02892cc83164acae447434
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895842"
---
# <a name="document-formats-and-naming-convention-guidance"></a>Formatos de documentos e diretrizes de convenção de nomenclatura

Qualquer arquivo usado para tradução personalizada deve ter pelo menos **quatro** caracteres de comprimento.

Essa tabela inclui todos os formatos de arquivos com suporte que podem ser utilizados para construir o sistema de tradução:

| Formatar            | Extensões   | Descrição                                                                                                                                                                                                                                                                    |
|-------------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| XLIFF             | .XLF, .XLIFF | Um formato de documento paralelo, exportação de sistemas de Memória de Tradução. Os idiomas utilizados são definidos dentro do arquivo.                                                                                                                                                              |
| TMX               | .TMX         | Um formato de documento paralelo, exportação de sistemas de Memória de Tradução. Os idiomas utilizados são definidos dentro do arquivo.                                                                                                                                                              |
| ZIP               | .ZIP         | ZIP é um formato de arquivo.                                                                                                                                                                                                        |
| Locstudio         | .LCL         | Um formato da Microsoft para documentos paralelos                                                                                                                                                                                                                                      |
| Microsoft Word    | .DOCX        | Documento do Microsoft Word                                                                                                                                                                                                                                                        |
| Adobe Acrobat     | .PDF         | Documento portátil do Adobe Acrobat                                                                                                                                                                                                                                                |
| HTML              | .HTML, .HTM  | Documento HTML                                                                                                                                                                                                                                                                  |
| Arquivo de texto         | .TXT         | Arquivos de texto codificados em UTF-16 ou UTF-8. O nome do arquivo não deve conter caracteres japoneses.                                                                                                                                                                                        |
| Arquivo de texto alinhado | .ALIGN       | A extensão `.ALIGN` é uma extensão especial que você poderá usar se souber que as sentenças no par de documentos estão perfeitamente alinhadas. Se você fornecer um arquivo `.ALIGN`, o Tradutor Personalizado não alinhará as sentenças para você. |
| Arquivo do Excel        | .XLSX        | Arquivo Excel (2013 ou posterior). A primeira linha/linha da planilha deverá ser o código do idioma.                                                                                                                                                                                                                                                      |

## <a name="dictionary-formats"></a>Formatos de dicionário

Para dicionários, o tradutor personalizado dá suporte a todos os formatos de arquivo que têm suporte para conjuntos de treinamento. Se você estiver usando um dicionário do Excel, a primeira linha/linha da planilha deverá ser de códigos de idioma.

## <a name="zip-file-formats"></a>Formatos de arquivo zip

Os documentos podem ser agrupados em um único arquivo zip e enviados. O Tradutor Personalizado dá suporte a formatos de arquivos zip (ZIP, GZ e TGZ).

Cada documento no arquivo zip com extensão TXT, HTML, HTM, PDF, DOCX, ALIGN deve seguir essa convenção de nomenclatura:

{nome do documento}\_{código do idioma} onde {nome do documente} é o nome do documento, {código do idioma} é a ID de idioma ISO (dois caracteres), indicando que o documento contém sentenças nesse idioma. É necessário ter um sublinhado (_) antes do código do idioma.

Por exemplo, para enviar dois documentos paralelos dentro de um zip para um sistema de inglês para espanhol, os arquivos deverão ser nomeados "data_en" e "data_es".

Arquivos de memória de tradução (TMX, XLF, XLIFF, LCL, XLSX) não precisam seguir a convenção de nomenclatura de idioma específico.  

## <a name="next-steps"></a>Próximas etapas

- Leia sobre o [projeto](workspace-and-project.md#what-is-a-custom-translator-project) para criá-los e gerenciá-los.
