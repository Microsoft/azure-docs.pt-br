---
title: Criar um plano Azure Functions Premium no portal
description: Saiba como usar o portal do Azure para criar um aplicativo de funções que é executado no plano Premium.
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 9cab67f096665c9333fa40bcb790896fcbebd8d5
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676578"
---
# <a name="create-a-premium-plan-function-app-in-the-azure-portal"></a>Criar um aplicativo de função de plano Premium no portal do Azure

O Azure Functions oferece um plano Premium escalonável que fornece conectividade de rede virtual, sem início frio e hardware Premium. Para saber mais, confira [Azure Functions plano Premium](functions-premium-plan.md). 

Neste artigo, você aprenderá a usar o portal do Azure para criar um aplicativo de funções em um plano Premium. 

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com) com sua conta do Azure.

## <a name="create-a-function-app"></a>Criar um aplicativo de funções

Você deve ter um aplicativo de funções para hospedar a execução de suas funções. Um aplicativo de funções lhe permite agrupar funções como uma unidade lógica para facilitar o gerenciamento, a implantação, o dimensionamento e o compartilhamento de recursos.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Neste ponto, você pode criar funções no novo aplicativo de funções. Essas funções podem aproveitar os benefícios do [plano Premium](functions-premium-plan.md).

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar uma função disparada por HTTP] (./functions-get-started.md
