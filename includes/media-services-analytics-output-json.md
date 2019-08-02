---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 065cb4daa9501ee658d364dad43b9e03798e4083
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172336"
---
O trabalho produz um arquivo de saída JSON que contém metadados sobre as faces detectadas e controladas. Os metadados incluem coordenadas que indicam o local das faces, bem como um número de identificação da face indicando o acompanhamento dessa pessoa. Os números de identificação de face estão sujeitos à redefinição em circunstâncias nas quais a face frontal é perdida ou sobreposta no quadro, resultando na atribuição de várias IDs às mesmas pessoas.

O JSON de saída contém os seguintes elementos:

### <a name="root-json-elements"></a>Elementos raiz JSON

| Elemento | DESCRIÇÃO |
| --- | --- |
| version |Refere-se à versão da API de Vídeo. |
| escala de tempo |"Tiques" por segundo do vídeo. |
| deslocamento |Essa é a diferença de tempo para carimbos de hora. Na versão 1.0 das APIs de Vídeo, sempre será 0. Em cenários futuro para os quais oferecemos suporte, esse valor poderá ser alterado. |
| largura, altura |A largura e a altura do quadro de vídeo de saída, em pixels.|
| taxa de quadros |Quadros por segundo do vídeo. |
| [fragmentos](#fragments-json-elements) |Os metadados são agrupados em segmentos diferentes, chamados fragmentos. Cada fragmento contém um início, uma duração, um número de intervalo e evento(s). |

### <a name="fragments-json-elements"></a>Elementos JSON de fragmentos

|Elemento|DESCRIÇÃO|
|---|---|
| start |A hora de início do primeiro evento em "tiques". |
| duration |A duração do fragmento, em “tiques”. |
| index | (Aplicável somente ao Azure Media Redactor) define o índice do quadro do evento atual. |
| intervalo |O intervalo de cada entrada de evento dentro do fragmento, em “tiques”. |
| events |Cada evento contém as faces detectadas e acompanhadas dentro desse período. É uma matriz de eventos. A matriz externa representa um intervalo de tempo. A matriz interna é composta por 0 ou mais eventos que ocorreram nesse ponto no tempo. Um colchete vazio [] significa que nenhuma face foi detectada. |
| id |A ID da face que está sendo acompanhada. Esse número pode mudar inadvertidamente se uma face se tornar indetectável. Uma certa pessoa deve ter a mesma ID durante todo o vídeo geral, mas isso não pode ser garantido devido a limitações no algoritmo de detecção (oclusão etc.). |
| x, y |As coordenadas X e Y no lado superior esquerdo da caixa delimitadora da face em uma escala normalizada de 0,0 a 1,0. <br/>As coordenadas -X e Y sempre têm relação à orientação paisagem, portanto, se você tiver um vídeo em orientação retrato (ou de cabeça para baixo, no caso do iOS), será necessário transpor as coordenadas adequadamente. |
| largura, altura |A largura e a altura da caixa delimitadora de face em uma escala normalizada do 0,0 a 1,0. |
| facesDetected |Isso é encontrado ao final dos resultados JSON e resume o número de faces que o algoritmo detectou durante o vídeo. Como as IDs podem ser redefinidas inadvertidamente caso uma face fique indetectável (por exemplo, se a face sair da tela, olhar para outro lado), esse número nem sempre será igual ao número verdadeiro de faces no vídeo. |
