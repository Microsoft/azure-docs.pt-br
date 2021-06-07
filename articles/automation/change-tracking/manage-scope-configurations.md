---
title: Limitar o escopo de implantação do Controle de Alterações e Estoque de Automação do Azure
description: Este artigo informa como trabalhar com configurações de escopo para limitar o escopo de uma implantação de Controle de Alterações e Estoque.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 6eb9a20920f0a340491459f0875fc85b90dfa193
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92209388"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>Limitar o escopo de implantação do Controle de Alterações e Estoque

Este artigo descreve como trabalhar com configurações de escopo ao usar o [Controle de Alterações e Estoque](overview.md), recurso para implantar alterações em suas VMs. Para saber mais, consulte [Direcionando soluções de monitoramento no Azure Monitor (versão prévia)](../../azure-monitor/insights/solution-targeting.md).

## <a name="about-scope-configurations"></a>Sobre configurações de escopo

A configuração de escopo é um grupo de uma ou mais pesquisas (consultas) salvas utilizadas para limitar o escopo do Controle de Alterações e Estoque em computadores específicos. A configuração de escopo é usada dentro do workspace do Log Analytics para definir quais computadores habilitar. Quando você adiciona um computador para ser alterado no recurso, o computador também é adicionado em uma pesquisa salva no espaço de trabalho.

## <a name="set-the-scope-limit"></a>Definir o limite do escopo

Para limitar o escopo para sua implantação de Controle de Alterações e Estoque:

1. Entre no [portal do Azure](https://portal.azure.com).

2. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Automação**. Quando você começa a digitar, a lista filtra sugestões baseadas na sua entrada. Selecione **Contas de Automação**.

3. Na lista de contas de Automação do Azure, selecione a conta que você escolheu quando habilitou Controle de Alterações e Estoque.

4. Na sua conta de Automação, selecione **Workspace vinculado** em **Recursos relacionados**.

5. Clique em **Acessar o workspace**.

6. Selecione **Configurações de Escopo (versão prévia)** em **Fontes de Dados do Workspace**.

7. Selecione as reticências à direita da  `MicrosoftDefaultScopeConfig-ChangeTracking` configuração de escopo e clique em **Editar**.

8. No painel Edição, selecione **Selecionar Grupos de Computadores**. O painel Grupos de Computadores mostra as pesquisas salvas que são usadas para criar a configuração de escopo. A pesquisa salva usada pelo Controle de Alterações e Estoque é:

    |Nome     |Categoria  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

9. Selecione a pesquisa salva para visualizar e editar a consulta usada para preencher o grupo. A imagem a seguir mostra a consulta e seus resultados:

    ![Pesquisas salvas](media/manage-scope-configurations/logsearch.png)

## <a name="next-steps"></a>Próximas etapas

* Para trabalhar com o Controle de Alterações e o Inventário, consulte [Gerenciar o Controle de Alterações e o Inventário](manage-change-tracking.md).
* Para solucionar problemas gerais de recurso, consulte [Solucionar problemas do Controle de Alterações e do Inventário](../troubleshoot/change-tracking.md).
