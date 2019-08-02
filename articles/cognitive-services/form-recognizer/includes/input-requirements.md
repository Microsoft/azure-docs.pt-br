---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 17dc32f8948387b90229d3c4c07102cff98e3018
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562697"
---
O Reconhecimento de Formulários funciona em documentos de entrada que atendem aos seguintes requisitos:

* O formato deve ser JPG, PNG ou PDF (texto ou digitalizado). PDFs com texto inserido são o melhor porque não há nenhuma possibilidade de erro na extração e na localização de caracteres.
* Se os PDFs estiverem com bloqueio de senha, você deverá remover o bloqueio antes de enviá-los.
* O tamanho do arquivo deve ser inferior a 4 MB.
* Para imagens, as dimensões devem estar entre 50 x 50 pixels e 4.200 x 4.200 pixels.
* Se documentos em papel forem digitalizados, os formulários deverão ser digitalizações de alta qualidade.
* O texto precisa usar o alfabeto latino (caracteres em português).
* Os dados precisam conter chaves e valores.
* As chaves podem aparecer acima ou à esquerda dos valores, mas não abaixo nem à direita.

Atualmente o Reconhecimento de Formulários não dá suporte aos seguintes tipos de dados de entrada:

* Tabelas complexas (tabelas aninhadas, células ou cabeçalhos mesclados e assim por diante).
* Caixas de seleção ou botões de opção.
* Documentos PDF com mais de 50 páginas.