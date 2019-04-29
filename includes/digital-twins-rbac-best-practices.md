---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 12/28/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: e81b8fb06240d566e46ca0b45a0910e014dee329
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60534041"
---
O controle de acesso baseado em função é uma estratégia de segurança orientada por herança para o gerenciamento de acesso, permissões e funções. Funções descendentes herdam permissões das funções pai. As permissões também podem ser atribuídas sem serem herdadas de uma função pai. Eles também podem ser atribuídos para personalizar uma função conforme necessário.

Por exemplo, um administrador de espaço pode precisar de acesso global para executar todas as operações de um espaço especificado. O acesso inclui todos os nós abaixo ou dentro do espaço. Um instalador de dispositivo pode precisar apenas de *read* e *atualização* de permissões para dispositivos e sensores.

Em todos os casos, as funções são concedidas *exatamente e não mais do que o acesso necessário* para cumprir suas tarefas de acordo com o Princípio de Menor Privilégio. De acordo com esse princípio, uma identidade é concedida *apenas*:

* A extensão de acesso necessária para concluir o trabalho.
* Uma função apropriada e limitada para a realização do trabalho.

>[!IMPORTANT]
> Sempre siga o princípio de privilégios mínimos.

Duas outras importantes práticas de controle de acesso baseadas em função devem ser seguidas:

> [!div class="checklist"]
> * Auditar periodicamente as atribuições de função para verificar se cada função tem as permissões corretas.
> * Limpe funções e atribuições quando os indivíduos mudam de função ou atribuição.