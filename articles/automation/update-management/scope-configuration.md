---
title: Limitar a automação do Azure Gerenciamento de Atualizações escopo de implantação
description: Este artigo informa como usar as configurações de escopo para limitar o escopo de uma implantação de Gerenciamento de Atualizações.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 76063c479950d12985d5f3f52393f9bb0d5ecd8d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221919"
---
# <a name="limit-update-management-deployment-scope"></a>Limitar o escopo de implantação Gerenciamento de Atualizações

Este artigo descreve como trabalhar com configurações de escopo ao usar o recurso de [Gerenciamento de atualizações](overview.md) para implantar atualizações e patches em suas VMs. Para obter mais informações, consulte [direcionando soluções de monitoramento em Azure monitor (versão prévia)](../../azure-monitor/insights/solution-targeting.md).

## <a name="about-scope-configurations"></a>Sobre configurações de escopo

Uma configuração de escopo é um grupo de uma ou mais pesquisas salvas (consultas) usadas para limitar o escopo de Gerenciamento de Atualizações a computadores específicos. A configuração de escopo é usada no espaço de trabalho Log Analytics para direcionar os computadores a serem habilitados. Quando você adiciona um computador para receber atualizações de Gerenciamento de Atualizações, o computador também é adicionado a uma pesquisa salva no espaço de trabalho.

## <a name="set-the-scope-limit"></a>Definir o limite de escopo

Para limitar o escopo para sua implantação de Gerenciamento de Atualizações:

1. Em sua conta de automação, selecione **espaço de trabalho vinculado** em **recursos relacionados**.

2. Selecione **Acessar o workspace**.

3. Selecione **configurações de escopo (versão prévia)** em **fontes de dados do espaço de trabalho**.

4. Selecione as reticências à direita da  `MicrosoftDefaultScopeConfig-Updates` configuração de escopo e selecione **Editar**.

5. No painel de edição, expanda **Selecionar grupos de computadores**. O painel Grupos de Computadores mostra as pesquisas salvas que são usadas para criar a configuração de escopo. A pesquisa salva usada pelo Gerenciamento de Atualizações é:

    |Nome     |Categoria  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | Atualizações        | Updates__MicrosoftDefaultComputerGroup         |

6. Selecione a pesquisa salva para exibir e editar a consulta usada para popular o grupo. A imagem a seguir mostra a consulta e seus resultados:

    [![Pesquisas salvas](./media/scope-configuration/logsearch.png)](./media/scope-configuration/logsearch-expanded.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

Você pode [consultar logs de Azure monitor](query-logs.md) para analisar avaliações de atualização, implantações e outras tarefas de gerenciamento relacionadas. Ele inclui consultas predefinidas para ajudá-lo a começar.
