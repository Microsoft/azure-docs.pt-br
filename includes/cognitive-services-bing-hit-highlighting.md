---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: fccc036a5e0422508f7ebc3370a4b5faa5176dc2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172250"
---
O Bing suporta o realce de ocorrências que marca os termos de consulta (ou outros termos que o Bing achar relevantes) nas cadeias de exibição de algumas das respostas. Por exemplo, os campos `name`, `displayUrl` e `snippet` de uma página da Web podem marcar os termos da consulta.

Por padrão, o Bing não inclui realce de marcadores em cadeias de exibição. Para incluir os marcadores, inclua o parâmetro `textDecorations` na solicitação de consulta e defina-o como **true**. Bing marca os termos da consulta usando os caracteres Unicode de E000 e E001 para marcar o início e o final do termo. Por exemplo, se o termo da consulta for Velejando Barquinho e qualquer termo existe no campo, o termo é colocado entre caracteres de realce de ocorrências, conforme mostrado no exemplo a seguir:  
  
![Realce de ocorrência](./media/cognitive-services-bing-hit-highlighting/bing-hit-highlighting.PNG) 

Antes de exibir a cadeia de caracteres na interface do usuário, você poderia substituir os caracteres Unicode por caracteres que são apropriadas para o formato de exibição. Por exemplo, se você estiver exibindo o texto como HTML, você pode realçar o termo da consulta, substituindo E000 por <b\> e E001 por </b\>. Se você não deseja aplicar formatação, remova os marcadores da cadeia de caracteres. 

O Bing oferece a opção de usar caracteres Unicode ou marcas HTML como marcadores. Para especificar quais marcadores usar, inclua o parâmetro de consulta `textFormat`. Para marcar o conteúdo com caracteres Unicode, defina `textFormat` para Raw (o padrão) e para marcar o conteúdo com marcas HTML, defina `textFormat` para HTML. 
  
Se `textDecorations` for **true**, o Bing pode incluir os marcadores a seguir em cadeias de exibição de respostas. Se não houver nenhum equivalente HTML, a célula de tabela HTML está vazia.

|Unicode|HTML|DESCRIÇÃO
|-|-|-
|U+E000|\<b>|Marca o início do termo de consulta (realce de ocorrências)
|U+E001|\</b>|Marca o final do termo de consulta
|U+E002|\<i>|Marca o início do conteúdo em itálico 
|U+E003|\</i>|Marca o final do conteúdo em itálico
|U+E004|\<br/>|Marca uma quebra de linha
|U+E005||Marca o início de um número de telefone
|U+E006||Marca o final de um número de telefone
|U+E007||Marca o início de um endereço
|U+E008||Marca o final de um endereço
|U+E009|\&nbsp;|Marca um espaço rígido
|U+E00C|\<strong>|Marca o início do conteúdo em negrito
|U+E00D|\</strong>|Marca o fim do conteúdo em negrito
|U+E00E||Marca o início do conteúdo cujo plano de fundo deve ser mais claro do que seu plano de fundo ao redor
|U+E00F||Marca o final do conteúdo cujo plano de fundo deve ser mais claro do que seu plano de fundo ao redor
|U+E010||Marca o início do conteúdo cujo plano de fundo deve ser mais escuro do que seu plano de fundo ao redor
|U+E011||Marca o final do conteúdo cujo plano de fundo deve ser mais escuro do que seu plano de fundo ao redor
|U+E012|\<del>|Marca o início do conteúdo que deve ser ignorado
|U+E013|\</del>|Marca o final do conteúdo que deve ser ignorado
|U+E016|\<sub>|Marca o início do conteúdo subscrito
|U+E017|\</sub>|Marca o final do conteúdo subscrito
|U+E018|\<sup>|Marca o início do conteúdo sobrescrito
|U+E019|\</sup>|Marca o final do conteúdo sobrescrito

O exemplo a seguir mostra uma resposta `Computation` que contém os marcadores de subscrito para um termo de consulta log(2). O campo `expression` conterá os marcadores somente se `textDecoration` for **true**.

![marcadores de computação](./media/cognitive-services-bing-hit-highlighting/bing-markers-computation.PNG) 

Se a solicitação não solicitou decorações, a expressão seria log10(2). 
  
