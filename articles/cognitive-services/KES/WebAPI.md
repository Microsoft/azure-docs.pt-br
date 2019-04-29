---
title: Interface de API Web - API de Serviço de Exploração de Conhecimento
titlesuffix: Azure Cognitive Services
description: Use a interface da API Web para criar uma experiência de pesquisa semântica avançada na API de KES (Serviço de Exploração de Conhecimento).
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 970db5984eedebf98bbb087cfd0b4a35a21a0f54
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60814423"
---
# <a name="web-api-interface"></a>Interface da API Web

Os arquivos de modelo criados pelo Serviço de Exploração de Conhecimento podem ser hospedados e acessados por meio de um conjunto de APIs Web.  As APIs que possam ser hospedadas no computador local usando o comando [`host_service`](CommandLine.md#host_service-command), ou pode ser implantada em um serviço de nuvem do Azure usando o comando [`deploy_service`](CommandLine.md#deploy_service-command).  Ambas as técnicas expõem os seguintes pontos de extremidade de API:

* [*interpret*](interpretMethod.md) – Interpreta uma cadeia de consulta de linguagem natural. Retorna interpretações anotadas para habilitar experiências de preenchimento automático da caixa de pesquisa que antecipam o que o usuário está digitando.
* [*evaluate* ](evaluateMethod.md) – Avalia e retorna o resultado de uma expressão de consulta estruturada.
* [*calchistogram* ](calchistogramMethod.md) – Calcula um histograma dos valores de atributo de objetos retornados por uma expressão de consulta estruturada.

Usados juntos, esses métodos de API permitem a criação de uma experiência avançada de pesquisa semântica.  Recebe uma cadeia de caracteres de consulta de linguagem natural, o método *interpret* fornece versões com anotações de consulta de entrada com expressões de consulta estruturada, com base nos dados subjacentes de índice e gramática.  O método *evaluate* avalia a expressão de consulta estruturada e retorna os objetos de índice de correspondência para exibição.  O método *calchistogram* calcula as distribuições de valor de atributo para habilitar a filtragem e ajuste.

**Exemplo**

Em um domínio de publicações acadêmicas, se um usuário digitar a cadeia de caracteres "latente", o método *interpret* pode fornecer um conjunto de interpretações classificadas, sugerindo que o usuário pode procurar a palavra-chave "análise semântica latente", o título "análise de estrutura latente" ou outras expressões começando com "latente".  Essas informações podem ser usadas para orientar rapidamente o usuário para os resultados de pesquisa desejado.

Para este domínio, o método *evaluate* pode ser usado para recuperar um conjunto de publicações correspondentes do índice acadêmico e o método *calchistogram* pode ser usado para calcular a distribuição de valores de atributo para as publicações correspondentes, que podem ser usados para filtragem adicional e refinar os resultados da pesquisa.

Observe que, para melhorar a legibilidade dos exemplos, as chamadas da API REST contém caracteres (tais como espaços) que não foram codificados de URL. Seu código precisa aplicar as codificações de URL apropriadas.
