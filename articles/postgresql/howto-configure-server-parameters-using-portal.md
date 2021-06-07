---
title: Configurar parâmetros do servidor-portal do Azure-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve como configurar os parâmetros postgres no banco de dados do Azure para PostgreSQL por meio do portal do Azure.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 02/28/2018
ms.openlocfilehash: 2e9cecc03c1240d625fa8f6bbdcd633ada978082
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604202"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---single-server-via-the-azure-portal"></a>Configurar parâmetros de servidor no banco de dados do Azure para PostgreSQL-servidor único por meio do portal do Azure 
Você pode listar, exibir e atualizar os parâmetros de configuração de um Banco de Dados do Azure para servidor PostgreSQL via portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de instruções, você precisa:
- [Servidor do Banco de Dados do Azure para PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>Exibir e editar parâmetros
1. Abra o [portal do Azure](https://portal.azure.com).

2. Selecione seu servidor de Banco de Dados do Azure para PostgreSQL.

3. Sob a seção **configurações**, selecione **parâmetros de servidor**. A página mostra uma lista de parâmetros, valores e descrições.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png" alt-text="Página de visão geral de parâmetros":::

4. Selecione o botão **suspenso** para ver os possíveis valores para parâmetros de tipo enumerado como client_min_messages.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png" alt-text="Enumerar a lista suspensa":::

5. Selecione ou passe o mouse sobre o botão **i** (informações) para ver o intervalo de valores possíveis para parâmetros numéricos como cpu_index_tuple_cost.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-information-button.png" alt-text="Botão informação":::

6. Se necessário, use a **caixa de pesquisa** para restringir rapidamente a um parâmetro específico. A busca está no nome e na descrição dos parâmetros.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/5-search.png" alt-text="Resultados da pesquisa":::

7. Altere os valores de parâmetro que você deseja ajustar. Todas as alterações feitas nessa sessão são realçadas em roxo. Depois de alterar os valores, você pode selecionar **Salvar**. Ou então, você pode **Descartar** suas alterações.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png" alt-text="Clique em salvar ou descartar mudanças":::

8. Se você tiver salvo os novos valores para os parâmetros, você sempre pode reverter tudo o que fazer com os valores padrão selecionando **Redefinir tudo para o padrão**.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png" alt-text="Redefinir tudo para padrão":::

## <a name="next-steps"></a>Próximas etapas
Saiba mais:
- [Visão geral dos parâmetros do servidor no Banco de Dados do Azure para PostgreSQL](concepts-servers.md)
- [Configurando parâmetros usando a CLI do Azure](howto-configure-server-parameters-using-cli.md)
