---
title: incluir arquivo
description: incluir arquivo
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.openlocfilehash: 1509d175979bb65c467424db5de967f56825a3f9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91545541"
---
O aplicativo cliente precisa saber se um enunciado não é significativo ou apropriado para o aplicativo. A intenção **None** é adicionada a cada aplicativo como parte do processo de criação para determinar se um enunciado não deve ser atendido pelo aplicativo cliente.

Se o LUIS retornar a intenção **None** para um enunciado, o aplicativo cliente poderá perguntar se o usuário deseja terminar a conversa ou fornecer mais orientações para continuar a conversa.

Se você deixar a intenção **Nenhuma** vazia, um enunciado que deve ser previsto fora do domínio do assunto será previsto em uma das intenções existentes do domínio do assunto. O resultado é que o aplicativo cliente, assim como um chatbot, executará operações incorretas com base em uma previsão incorreta.

1. Selecione **Intenções** no painel esquerdo.

1. Selecione a intenção **None**. Adicione três enunciados que o usuário pode inserir, mas que não são relevantes para o seu aplicativo para pedidos de pizza:

    |Enunciados de exemplo de `None`|
    |--|
    |`Barking dogs are annoying`|
    |`Penguins in the ocean`|

    Esses exemplos não devem usar palavras que você espera no domínio do seu assunto, como `pizza`, `cheese`, `crust`, `pickup` `deliver`.