---
title: incluir arquivo
description: incluir arquivo
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.openlocfilehash: 5eb2517b4b71b61dd70406f345c005d007d9e3ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91535943"
---
A entidade pattern.any permite localizar dados de formulário livre em que as palavras da entidade dificultam determinar o fim da entidade no resto do enunciado.

Um aplicativo de Recursos Humanos ajuda os funcionários a encontrar os formulários da empresa.

|Enunciado|
|--|
|Onde está **HRF-123456**?|
|Quem criou **HRF 123234**?|
|**HRF-456098** está publicado em francês?|

No entanto, cada formulário tem um nome formatado, usado na tabela anterior, bem como um nome amigável, como `Request relocation from employee new to the company 2018 version 5`.

Enunciados com o nome amigável do formulário se parecem com:

|Enunciado|
|--|
|Onde está **Solicitar a realocação do funcionário novo para a versão 5 da empresa 2018**?|
|Quem criou **“Solicitar a realocação do funcionário novo para a versão 5 da empresa 2018”** ?|
|**Solicitar a realocação do funcionário novo para a versão 5 da empresa 2018** está publicado em francês?|

O comprimento variável inclui palavras que podem confundir o LUIS sobre onde a entidade termina. Usando uma entidade Pattern.any em um padrão permite especificar o início e o fim do nome do formulário para que o LUIS extraia corretamente o nome do formulário.

|Exemplo de enunciado de modelo|
|--|
|Onde está {FormName}[?]|
|Quem criou {FormName}[?]|
|{FormName} foi publicado em francês[?]|

Padrão de revisão. Alguma informação de [referência](../reference-entity-pattern-any.md)