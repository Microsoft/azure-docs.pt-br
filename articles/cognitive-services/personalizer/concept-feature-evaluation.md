---
title: Avaliação de recurso-personalizador
titleSuffix: Azure Cognitive Services
description: Quando você executa uma avaliação em seu recurso personalizado do portal do Azure, o personalizador fornece informações sobre quais recursos de contexto e ações estão influenciando o modelo.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: d03c5f66f760a2bea9f99501cec478831a347c5d
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668719"
---
# <a name="feature-evaluation"></a>Avaliação de recurso

Quando você executa uma avaliação em seu recurso personalizado do [portal do Azure](https://portal.azure.com), o personalizador fornece informações sobre quais recursos de contexto e ações estão influenciando o modelo. 

Isso é útil para:

* Imagine recursos adicionais que você pode usar, obtendo inspiração de quais recursos são mais importantes no modelo.
* Veja quais recursos não são importantes e possivelmente remova-os ou analise ainda mais o que pode estar afetando o uso.
* Forneça orientação para as equipes editoriais ou de organização sobre novos conteúdos ou produtos que valem a pena colocar no catálogo.
* Solucionar problemas comuns e erros que ocorrem ao enviar recursos para o personalizador.

Os recursos mais importantes têm pesos mais fortes no modelo. Como esses recursos têm um peso mais alto, eles tendem a estar presentes quando o personalizador obtém recompensas mais altas.

## <a name="getting-feature-importance-evaluation"></a>Obtendo a avaliação de importância do recurso

Para ver os resultados de importância do recurso, você deve executar uma avaliação. As informações resultantes sobre a importância do recurso representam o modelo atual do personalizador online. A avaliação analisa a importância do recurso do modelo salvo na data de término do período de avaliação. 

A avaliação cria rótulos de recursos legíveis com base nos nomes de recursos observados durante o período de avaliação.

Os resultados de importância do recurso não representam outras políticas e modelos testados ou criados durante a avaliação.  A avaliação não incluirá recursos enviados ao personalizado após o término do período de avaliação.

## <a name="how-to-interpret-the-feature-importance-evaluation"></a>Como interpretar a avaliação de importância do recurso

O personalizador avalia os recursos criando "grupos" de recursos que têm importância semelhante. Pode-se dizer que um grupo tem uma importância geral mais forte do que outras, mas dentro do grupo, a ordenação de recursos é em ordem alfabética.

As informações sobre cada recurso incluem:

* Se o recurso vem de contexto ou ações.
* Chave de recurso e valor.

Por exemplo, um aplicativo de ordenação de sorvetes de gelo pode ver "context. clima: quente" como um recurso muito importante.

O personalizador exibe correlações de recursos que, quando levados em conta, produzem recompensas mais altas.

Por exemplo, você pode ver "context. clima: quente *com* Action. MenuItem: sorvete", bem como "context. clima: frio *com* Action. MenuItem: WarmTea:

## <a name="actions-you-can-take-based-on-feature-evaluation"></a>Ações que você pode tomar com base na avaliação de recursos

### <a name="imagine-additional-features-you-could-use"></a>Imagine recursos adicionais que você pode usar

Obtenha inspiração dos recursos mais importantes no modelo. Por exemplo, se você vir "context. MobileBattery: Low" em um aplicativo móvel de vídeo, talvez ache que o tipo de conexão também pode fazer com que os clientes optem por ver um clipe de vídeo sobre outro e, em seguida, adicionar recursos sobre o tipo de conectividade e a largura de banda em seu aplicativo.

### <a name="see-what-features-are-not-important"></a>Veja quais recursos não são importantes

Potencialmente, remova recursos não importantes ou analise ainda mais o que pode afetar o uso. Os recursos podem ter uma classificação baixa por vários motivos. Uma delas poderia ser que o recurso original não afete o comportamento do usuário. Mas isso também pode significar que o recurso não é aparente para o usuário. 

Por exemplo, um site de vídeo poderia ver que "Action. VideoResolution = 4K" é um recurso de baixa prioridade, contradiz a pesquisa do usuário. A causa pode ser que o aplicativo não mencione nem mostrar a resolução de vídeo, de modo que os usuários não alterariam seu comportamento com base nele.

### <a name="provide-guidance-to-editorial-or-curation-teams"></a>Fornecer orientação para as equipes editoriais ou de organização

Forneça diretrizes sobre novos conteúdos ou produtos que valem a pena colocar no catálogo. O personalizador foi projetado para ser uma ferramenta que aumenta a Insight e as equipes humanas. Uma maneira de fazer isso é fornecendo informações aos grupos editorial sobre o que ele faz sobre produtos, artigos ou conteúdo que conduz o comportamento. Por exemplo, o cenário do aplicativo de vídeo pode mostrar que há um recurso importante chamado "Action. VideoEntities. Cat: true", solicitando que a equipe editorial traga mais vídeos em gato.

### <a name="troubleshoot-common-problems-and-mistakes"></a>Solucionar problemas e erros comuns

Problemas comuns e erros podem ser corrigidos alterando o código do aplicativo para que ele não envie recursos inadequados ou formatados incorretamente para o personalizador. 

Erros comuns ao enviar recursos incluem o seguinte:

* Enviando informações de identificação pessoal (PII). PII específicas para um indivíduo (como nome, número de telefone, números de cartão de crédito, endereços IP) não devem ser usadas com o personalizador. Se seu aplicativo precisar controlar os usuários, use um UUID sem identificação ou algum outro número de UserID. Na maioria dos cenários, isso também é problemático.
* Com um grande número de usuários, é improvável que a interação de cada usuário avalie mais do que toda a interação da população; portanto, o envio de IDs de usuário (mesmo se não-PII) provavelmente adicionará mais ruído do que o valor ao modelo.
* Enviando campos de data e hora como carimbos de hora precisos em vez de valores de tempo destacados. Ter recursos como Context. TimeStamp. Day = segunda-feira ou "context. TimeStamp. Hour" = "13" é mais útil. Haverá no máximo 7 ou 24 valores de recurso para cada um. Mas "context. TimeStamp": "1985-04-12T23:20:50.52 Z" é tão preciso que não haverá nenhuma maneira de aprender com ele, pois nunca acontecerá novamente.

## <a name="next-steps"></a>Próximas etapas

Entenda a [escalabilidade e o desempenho](concepts-scalability-performance.md) com o personalizador.

