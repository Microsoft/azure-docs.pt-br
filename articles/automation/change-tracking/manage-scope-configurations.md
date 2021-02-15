---
title: Limitar o Controle de Alterações de automação do Azure e o escopo de implantação de estoque
description: Este artigo informa como trabalhar com configurações de escopo para limitar o escopo de uma implantação de Controle de Alterações e de inventário.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 6eb9a20920f0a340491459f0875fc85b90dfa193
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209388"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>Limitar Controle de Alterações e escopo de implantação de estoque

Este artigo descreve como trabalhar com configurações de escopo ao usar o [controle de alterações e](overview.md) o recurso de inventário para implantar alterações em suas VMs. Para obter mais informações, consulte [direcionando soluções de monitoramento em Azure monitor (versão prévia)](../../azure-monitor/insights/solution-targeting.md).

## <a name="about-scope-configurations"></a>Sobre configurações de escopo

Uma configuração de escopo é um grupo de uma ou mais pesquisas salvas (consultas) usadas para limitar o escopo de Controle de Alterações e inventário a computadores específicos. A configuração de escopo é usada no espaço de trabalho Log Analytics para direcionar os computadores a serem habilitados. Quando você adiciona um computador a alterações do recurso, o computador também é adicionado a uma pesquisa salva no espaço de trabalho.

## <a name="set-the-scope-limit"></a>Definir o limite de escopo

Para limitar o escopo para sua implantação de Controle de Alterações e inventário:

1. Entre no [portal do Azure](https://portal.azure.com).

2. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Automação**. Conforme você começa a digitar, a lista filtra as sugestões com base em sua entrada. Selecione **Contas de Automação**.

3. Na lista de contas de automação, selecione a conta que você escolheu quando habilitou Controle de Alterações e inventário.

4. Em sua conta de automação, selecione **espaço de trabalho vinculado** em **recursos relacionados**.

5. Clique em **ir para espaço de trabalho**.

6. Selecione **configurações de escopo (versão prévia)** em **fontes de dados do espaço de trabalho**.

7. Selecione as reticências à direita da  `MicrosoftDefaultScopeConfig-ChangeTracking` configuração de escopo e clique em **Editar**.

8. No painel Edição, selecione **Selecionar Grupos de Computadores**. O painel Grupos de Computadores mostra as pesquisas salvas que são usadas para criar a configuração de escopo. A pesquisa salva usada pelo Controle de Alterações e pelo inventário é:

    |Nome     |Categoria  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

9. Selecione a pesquisa salva para exibir e editar a consulta usada para popular o grupo. A imagem a seguir mostra a consulta e seus resultados:

    ![Pesquisas salvas](media/manage-scope-configurations/logsearch.png)

## <a name="next-steps"></a>Próximas etapas

* Para trabalhar com o Controle de Alterações e o Inventário, consulte [Gerenciar o Controle de Alterações e o Inventário](manage-change-tracking.md).
* Para solucionar problemas gerais de recurso, consulte [Solucionar problemas do Controle de Alterações e do Inventário](../troubleshoot/change-tracking.md).
