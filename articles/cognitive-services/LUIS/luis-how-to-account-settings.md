---
title: Gerenciar conta e chaves-LUIS
titleSuffix: Azure Cognitive Services
description: As duas partes principais de informação para uma conta LUIS são a conta de usuário e a chave de criador. Suas informações de logon são gerenciadas em account.microsoft.com. Sua chave de criação é gerenciada na página de configurações do portal do LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 28f8a15922a3a3e4e3b464b86bdfea07d329a848
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638277"
---
# <a name="manage-account-and-authoring-key"></a>Gerenciar conta e chave de criação

As duas partes principais de informação para uma conta LUIS são a conta de usuário e a chave de criador. Suas informações de logon são gerenciadas em [account.microsoft.com](https://account.microsoft.com). Sua chave de criação é gerenciada na página de **configurações** do portal do [Luis](luis-reference-regions.md) .

## <a name="authoring-key"></a>Chave de criação

Essa única chave de criação específica da região, na página **configurações** , permite que você crie todos os seus aplicativos do portal do [Luis](luis-reference-regions.md) , bem como as [APIs de criação](https://go.microsoft.com/fwlink/?linkid=2092087). Para sua comodidade, a chave de criador é permitida para fazer um número [limitado](luis-boundaries.md) de consultas de ponto de extremidade a cada mês.

[![Página Configurações de LUIS](./media/luis-how-to-account-settings/account-settings.png)](./media/luis-how-to-account-settings/account-settings.png#lightbox)

A chave de criador é usada para todos os seus aplicativos, bem como quaisquer aplicativos que você listou como colaborador.

## <a name="authoring-key-regions"></a>Regiões da chave de criador

A chave de criador é específica para a [região de autoria](luis-reference-regions.md#publishing-regions). A chave não funciona em uma região diferente.

## <a name="reset-authoring-key"></a>Redefinir a chave de criador

Se a chave de criador estiver comprometida, redefina a chave. A chave é redefinida em todos os seus aplicativos no portal do [Luis](luis-reference-regions.md) . Se você criar seus aplicativos por meio de APIs de criador, você precisa alterar o valor de `Ocp-Apim-Subscription-Key` para a nova chave.

## <a name="delete-account"></a>Exclui a conta

Consulte [Armazenamento e remoção de dados](luis-concept-data-storage.md#accounts) para obter informações sobre quais dados são excluídos quando você exclui sua conta.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre sua [chave de criador](luis-concept-keys.md#authoring-key).

