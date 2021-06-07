---
title: Criar ações e recursos personalizados
description: Este tutorial descreve como criar ações e recursos personalizados no Azure Resource Manager. Também mostra como os fluxos de trabalho personalizados interoperam com modelos do Azure Resource Manager, a CLI do Azure, o Azure Policy e o Log de Atividades do Azure.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 45f18727b53b802ba746da41b47fe955543ed9d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "75648543"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Criar ações e recursos personalizados no Azure

Um provedor personalizado é um contrato entre o Azure e um ponto de extremidade. Com provedores personalizados, você pode alterar os fluxos de trabalho no Azure adicionando novas APIs ao Azure Resource Manager. Com essas APIs personalizadas, o Resource Manager pode usar novas funcionalidades de implantação e gerenciamento.

Este tutorial descreve um exemplo simples de como adicionar novas ações e novos recursos ao Azure e como integrá-los.

## <a name="set-up-azure-functions-for-azure-custom-providers"></a>Configurar o Azure Functions para os Provedores Personalizados do Azure

A primeira parte deste tutorial descreve como configurar um aplicativo de funções do Azure para funcionar com provedores personalizados:

- [Configurar o Azure Functions para os Provedores Personalizados do Azure](./tutorial-custom-providers-function-setup.md)

Provedores personalizados podem funcionar com qualquer URL pública.

## <a name="author-a-restful-endpoint-for-custom-providers"></a>Criar um ponto de extremidade RESTful para provedores personalizados

A segunda parte deste tutorial descreve como criar um ponto de extremidade RESTful para provedores personalizados:

- [Como criar um ponto de extremidade RESTful para provedores personalizados](./tutorial-custom-providers-function-authoring.md)

## <a name="create-and-use-a-custom-provider"></a>Criar e usar um provedor personalizado

A terceira parte deste tutorial descreve como criar um provedor personalizado e usar suas ações e seus recursos personalizados:

- [Criar e usar um provedor personalizado](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu mais sobre provedores personalizados e como criar um. Para prosseguir para o próximo tutorial, confira [Tutorial: Configurar o Azure Functions para os Provedores Personalizados do Azure](./tutorial-custom-providers-function-setup.md).

Se você está procurando referências ou um início rápido, aqui estão alguns links úteis:

- [Início Rápido: Criar um provedor de recursos personalizado do Azure e implantar recursos personalizados](./create-custom-provider.md)
- [Como adicionar ações personalizadas à API REST do Azure](./custom-providers-action-endpoint-how-to.md)
- [Como adicionar recursos personalizados à API REST do Azure](./custom-providers-resources-endpoint-how-to.md)
