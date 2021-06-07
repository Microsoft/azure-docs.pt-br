---
title: Configurar parâmetros do servidor-portal do Azure-banco de dados do Azure para PostgreSQL-servidor flexível
description: Este artigo descreve como configurar os parâmetros postgres no banco de dados do Azure para o servidor flexível do PostgreSQL por meio do portal do Azure.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 29d85cc33dc55f3819bba6898a265c46b7e7ef85
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607432"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---flexible-server-via-the-azure-portal"></a>Configurar parâmetros de servidor no banco de dados do Azure para PostgreSQL – servidor flexível por meio do portal do Azure 

Você pode listar, mostrar e atualizar parâmetros de configuração para um servidor flexível do banco de dados do Azure para PostgreSQL por meio do portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia de instruções, você precisa:
- [Banco de dados do Azure para PostgreSQL servidor flexível](quickstart-create-server-portal.md)

## <a name="viewing-and-editing-parameters"></a>Exibir e editar parâmetros

1. Abra o [portal do Azure](https://portal.azure.com).

2. Selecione seu servidor flexível.

3. Sob a seção **configurações**, selecione **parâmetros de servidor**. A página mostra uma lista de parâmetros, valores e descrições.
![Página de visão geral de parâmetros](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Selecione o botão **suspenso** para ver os possíveis valores para parâmetros de tipo enumerado como client_min_messages.
![Enumerar a lista suspensa](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Selecione ou passe o mouse sobre o botão **i** (informações) para ver o intervalo de valores possíveis para parâmetros numéricos como cpu_index_tuple_cost.
![Botão informação](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. Se necessário, use a **caixa de pesquisa** para restringir rapidamente a um parâmetro específico. A busca está no nome e na descrição dos parâmetros.
![Pesquisar resultados](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Altere os valores de parâmetro que você deseja ajustar. Todas as alterações feitas nessa sessão são realçadas em roxo. Depois de alterar os valores, você pode selecionar **Salvar**. Ou então, você pode **Descartar** suas alterações.
![Clique em salvar ou descartar mudanças](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Se você tiver salvo os novos valores para os parâmetros, você sempre pode reverter tudo o que fazer com os valores padrão selecionando **Redefinir tudo para o padrão**.
![Redefinir tudo para padrão](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>Próximas etapas

Saiba mais:

- [Visão geral dos parâmetros do servidor no Banco de Dados do Azure para PostgreSQL](concepts-servers.md)
- [Configurando parâmetros usando a CLI do Azure](howto-configure-server-parameters-using-cli.md)
