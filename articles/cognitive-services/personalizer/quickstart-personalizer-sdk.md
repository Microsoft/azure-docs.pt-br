---
title: 'Início Rápido: Criar e usar o loop de aprendizado com o SDK – Personalizador'
description: Este guia de início rápido mostra como criar e gerenciar a sua base de dados de conhecimento usando a biblioteca de clientes do Personalizador.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 08/27/2020
ms.custom: cog-serv-seo-aug-2020
keywords: personalizer, Azure personalizer, machine learning
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: 3acfd8134a2a65e6ea9f019baff24b8b8149f7f3
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777333"
---
# <a name="quickstart-personalizer-client-library"></a>Início Rápido: Biblioteca de clientes do Personalizador

Exiba o conteúdo personalizado neste início rápido com o serviço Personalizador.

Introdução à biblioteca de clientes do Personalizador. Siga essas etapas para instalar o pacote e testar o código de exemplo para tarefas básicas.

 * API de Classificação: seleciona o melhor item dentre as ações, com base em informações em tempo real fornecidas sobre o conteúdo e o contexto.
 * API de Recompensa: você determina a pontuação de recompensa de acordo com as suas necessidades empresariais e, em seguida, envia-a para o Personalizador com essa API. Essa pontuação pode ser um só valor, como 1 para bom e 0 para ruim, ou um algoritmo criado de acordo com as suas necessidades empresariais.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# SDK](./includes/quickstart-sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js SDK](./includes/quickstart-sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python SDK](./includes/quickstart-sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como o Personalizador funciona](how-personalizer-works.md)
> [Onde usar o Personalizador?](where-can-you-use-personalizer.md)
