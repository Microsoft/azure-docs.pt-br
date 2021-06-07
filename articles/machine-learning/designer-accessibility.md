---
title: Usar recursos de acessibilidade no designer
titleSuffix: Azure Machine Learning
description: Saiba mais sobre atalhos de teclado e recursos de acessibilidade do leitor de tela disponíveis no designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.custom: designer
ms.openlocfilehash: 86cb5260a59f864658fbb7ac1c1da2d943c6253e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90893418"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer"></a>Usar um teclado para usar o designer de Azure Machine Learning

Saiba como usar um teclado e um leitor de tela para usar o designer do Azure Machine Learning. Para obter uma lista de atalhos de teclado que funcionam em qualquer lugar no portal do Azure, consulte [Atalhos de teclado no portal do Azure](../azure-portal/azure-portal-keyboard-shortcuts.md)

Este fluxo de trabalho foi testado com [Narrator](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) e [JAWS](https://www.freedomscientific.com/products/software/jaws/), mas ele deve funcionar com outros leitores de tela padrão.

## <a name="navigate-the-pipeline-graph"></a>Navegue pelo grafo de pipeline

O grafo de pipeline é organizado como uma lista aninhada. A lista externa é uma lista de módulos que descreve todos os módulos no grafo de pipeline. A lista interna é uma lista de conexões, que descreve todas as conexões de um módulo específico.  

1. Na lista do módulo, use a tecla de direção para alternar os módulos.
1. Use a tecla Tab para abrir a lista de conexões do módulo de destino.
1. Use a tecla de direção para alternar entre as portas de conexão do módulo.
1. Use “G” para ir ao módulo de destino.

## <a name="edit-the-pipeline-graph"></a>Edite o grafo de pipeline

### <a name="add-a-module-to-the-graph"></a>Adicione um módulo ao grafo

1. Use Ctrl + F6 para alternar o foco da tela da árvore de módulos.
1. Localize o módulo desejado na árvore de módulos usando o controle TreeView padrão.

### <a name="edit-a-module"></a>Edite um módulo

Para conectar um módulo a outro módulo:

1. Use Ctrl + Shift + H ao direcionar um módulo na lista de módulos para abrir o auxiliar de conexão.
1. Edite as portas de conexão para o módulo.

Para ajustar as propriedades do módulo:

1. Use Ctrl + Shift + H ao direcionar um módulo para abrir as propriedades do módulo.
1. Edite as propriedades do módulo.

## <a name="navigation-shortcuts"></a>Atalhos de navegação

| Teclas | Descrição |
|-|-|
| Ctrl + F6 | Alterna o foco entre a tela e a árvore de módulos |
| Ctrl + F1   | Abre o cartão de informações ao se concentrar em um nó na árvore de módulos |
| Ctrl + Shift + H | Abre o auxiliar de conexão quando o foco estiver em um nó |
| Ctrl + Shift + E | Abre as propriedades do módulo quando o foco estiver em um nó |
| Ctrl + G | Move o foco para o primeiro nó com falha se a execução do pipeline falhar |

## <a name="action-shortcuts"></a>Atalhos de ação

Use os seguintes atalhos com a tecla de acesso. Para obter mais informações sobre teclas de acesso, consulte https://en.wikipedia.org/wiki/Access_key.

| Teclas | Ação |
|-|-|
| Chave de acesso + R | Executar |
| Chave de acesso + P | Publicar |
| Chave de acesso + C | Clone |
| Chave de acesso + D | Implantar |
| Chave de acesso + I | Criar/atualizar pipeline de inferência |
| Chave de acesso + B | Criar/atualizar pipeline de inferência do lote |
| Chave de acesso + K | Abrir o menu suspenso “Criar pipeline de inferência” |
| Chave de acesso + U | Abrir o menu suspenso “Atualizar pipeline de inferência” |
| Chave de acesso + M | Abrir o menu suspenso Mais (...) |

## <a name="next-steps"></a>Próximas etapas

- [Ativar alto contraste ou alterar tema](../azure-portal/set-preferences.md#choose-a-theme-or-enable-high-contrast)
- [Ferramentas relativas à acessibilidade na Microsoft](https://www.microsoft.com/accessibility)
