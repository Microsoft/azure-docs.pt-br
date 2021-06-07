---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: lajanuar
ms.openlocfilehash: dfd2b32094aae06675ea8ee9157370f9d2833e91
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518211"
---
O Reconhecimento de Formulários funciona em documentos de entrada que atendem aos seguintes requisitos:

* O formato deve ser JPG, PNG, PDF (texto ou digitalizado) ou TIFF. PDFs com texto inserido são o melhor porque não há nenhuma possibilidade de erro na extração e na localização de caracteres.
* O tamanho do arquivo precisa ser inferior a 50 MB.
* As imagens de dimensões devem estar entre 50 x 50 pixels e 10.000 x 10.000 pixels.
* As dimensões de PDF devem ter no máximo 17 x 17 polegadas, correspondentes aos tamanhos de papel ofício ou A3 e menores.
* Para PDF e TIFF, somente as primeiras 200 páginas são processadas (com uma assinatura de camada gratuita, somente as duas primeiras páginas são processadas).
* O tamanho total do conjunto de dados de treinamento deve ser de até 500 páginas.
* Se os PDFs estiverem com bloqueio de senha, você deverá remover o bloqueio antes de enviá-los.
* Se documentos em papel forem digitalizados, os formulários deverão ser digitalizações de alta qualidade.
* Para aprendizado não supervisionado (sem dados rotulados), os dados devem conter chaves e valores.
* Para aprendizado não supervisionado (sem dados rotulados), as chaves devem aparecer acima ou à esquerda dos valores. Não podem aparecer abaixo nem à direita.
