---
title: Referência de domínio predefinido-LUIS
titleSuffix: Azure Cognitive Services
description: Referência para os domínios predefinidos, que são conjuntos predefinidos de tentativas e entidades de idioma Noções básicas sobre inteligente serviços (LUÍS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: ce043e23a0384a74fd5d2c9dd514045578ef836d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563462"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Referência de domínio predefinida para seu aplicativo LUIS
Esta referência contém informações sobre o [domínios pré-compilada](luis-how-to-use-prebuilt-domains.md), que são conjuntos predefinidos de entidades que oferece LUÍS e tentativas.

[Domínios personalizados](luis-how-to-start-new-app.md), por outro lado, iniciar sem tentativas e modelos. Você pode adicionar quaisquer tentativas de domínio pré-compilada e entidades a um modelo personalizado.

# <a name="supported-domains-across-cultures"></a>Domínios com suporte entre culturas

A única cultura com suporte é o inglês. 

<!--

The table below summarizes the currently supported domains. Support for English is usually more complete than others.


| Entity Type       | EN-US      | ZH-CN   | DE    | FR     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| Calendar    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -      | -    | -    | -     | -  |
| Communication   | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Email           | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| HomeAutomation           | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Notes      | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Places    | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| RestaurantReservation   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo_IPA        | ✓    | ✓       | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Utilities          | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Weather        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Web    | ✓    | -        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
||||||||||||| 

-->

<br><br>

|Tipo de entidade|description|
|--|--|
|Calendário|O calendário é algo sobre reuniões pessoais e compromissos, _não_ eventos públicos (como agendas do World Cup, calendários de eventos de Seattle) ou calendários genéricos (como, por exemplo, o dia atual, o que o Outono começa, quando é dia do trabalho).|
|Comunicação|Solicitações para fazer chamadas, enviar textos ou mensagens instantâneas, localizar e adicionar contatos e várias outras solicitações relacionadas à comunicação (geralmente de saída). As consultas somente do nome de contato não pertencem ao domínio de comunicação.|
|Email|O email é um subdomínio do domínio de comunicação. Ele contém principalmente solicitações para enviar e receber mensagens por emails.|
|HomeAutomation|O domínio HomeAutomation fornece intenções e entidades relacionadas ao controle de dispositivos do Smart Home. Ele dá suporte principalmente ao comando de controle relacionado a luzes e ar-condicionado, mas tem algumas capacidades gerais de generalização para outros dispositivos elétricos.|
|Observações|Observação o domínio fornece intenções e entidades para a criação de anotações e a gravação de itens para os usuários.|
|Locais|Os locais incluem empresas, instituições, restaurantes, espaços públicos e endereços. O domínio dá suporte ao local localizando e perguntando as informações de um local público, como local, horas operacionais e distância.|
|RestaurantReservation|O domínio de reserva de restaurantes dá suporte a tentativas de tratamento de reservas para restaurantes.|
|ToDo|ToDo o domínio fornece tipos de listas de tarefas para que os usuários adicionem, marquem e excluam seus itens de tarefa.|
|Utilidades|O domínio de utilitários é um domínio geral entre todos os modelos predefinidos do LUIS que contém tentativas comuns e declarações em cenários de diferença.|
|Clima|O clima de domínio concentra-se em verificar a condição e os avisos meteorológicos com localização e hora ou tempo de verificação por condições de clima.|
|Web|O domínio da Web fornece a intenção e as entidades para pesquisar um site.|
