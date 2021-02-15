---
title: Gerenciar o banco de dados do Azure para PostgreSQL-portal do Azure
description: Saiba como gerenciar um banco de dados do Azure para o servidor PostgreSQL do portal do Azure.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 11/20/2019
ms.openlocfilehash: 393e67d1b690f7231b5bf298b44f4db7c489d184
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91704507"
---
# <a name="manage-an-azure-database-for-postgresql-server-using-the-azure-portal"></a>Gerenciar um servidor de banco de dados do Azure para PostgreSQL usando o portal do Azure

Este artigo mostra como gerenciar seu banco de dados do Azure para servidores PostgreSQL. As tarefas de gerenciamento incluem dimensionamento de computação e armazenamento, redefinição de senha de administrador e detalhes do servidor de exibição.

## <a name="sign-in"></a>Entrar

Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-server"></a>Criar um servidor

Visite o guia de [início rápido](quickstart-create-server-database-portal.md) para saber como criar e começar a usar um banco de dados do Azure para o servidor PostgreSQL.

## <a name="scale-compute-and-storage"></a>Dimensionar a computação e o armazenamento

Após a criação do servidor, você pode dimensionar entre as camadas de Uso Geral e com otimização de memória à medida que suas necessidades mudam. Você também pode dimensionar a computação e a memória aumentando ou diminuindo o vCores. O armazenamento pode ser dimensionado verticalmente (no entanto, você não pode dimensionar o armazenamento verticalmente).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Dimensionar entre as camadas de Uso Geral e com otimização de memória

Você pode dimensionar de Uso Geral para a memória otimizada e vice-versa. Não há suporte para a alteração de e para a camada básica após a criação do servidor.

1. Selecione o servidor na portal do Azure. Selecione **tipo de preço**, localizado na seção **configurações** .

2. Selecione **uso geral** ou **memória otimizada**, dependendo do que você está dimensionando.

   :::image type="content" source="./media/howto-create-manage-server-portal/change-pricing-tier.png" alt-text="Captura de tela de portal do Azure para escolher camada básica, Uso Geral ou com otimização de memória no banco de dados do Azure para PostgreSQL":::

   > [!NOTE]
   > A alteração de camadas causa uma reinicialização do servidor.

3. Selecione **OK** para salvar as alterações.

### <a name="scale-vcores-up-or-down"></a>Dimensionar vCores para cima ou para baixo

1. Selecione o servidor na portal do Azure. Selecione **tipo de preço**, localizado na seção **configurações** .

2. Altere a configuração **vCore**, movendo o controle deslizante para o valor desejado.

   :::image type="content" source="./media/howto-create-manage-server-portal/scaling-compute.png" alt-text="Captura de tela de portal do Azure para escolher camada básica, Uso Geral ou com otimização de memória no banco de dados do Azure para PostgreSQL":::

   > [!NOTE]
   > O dimensionamento de vCores causa uma reinicialização do servidor.

3. Selecione **OK** para salvar as alterações.

### <a name="scale-storage-up"></a>Escalar o armazenamento verticalmente

1. Selecione o servidor na portal do Azure. Selecione **tipo de preço**, localizado na seção **configurações** .

2. Altere a configuração de **armazenamento** movendo o controle deslizante para cima até o valor desejado.

   :::image type="content" source="./media/howto-create-manage-server-portal/scaling-storage.png" alt-text="Captura de tela de portal do Azure para escolher camada básica, Uso Geral ou com otimização de memória no banco de dados do Azure para PostgreSQL":::

   > [!NOTE]
   > O armazenamento não pode ser reduzido verticalmente.

3. Selecione **OK** para salvar as alterações.

## <a name="update-admin-password"></a>Atualizar senha do administrador

Você pode alterar a senha da função de administrador usando o portal do Azure.

1. Selecione o servidor na portal do Azure. Na janela **visão geral** , selecione **Redefinir senha**.

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-reset-password.png" alt-text="Captura de tela de portal do Azure para escolher camada básica, Uso Geral ou com otimização de memória no banco de dados do Azure para PostgreSQL":::

2. Insira uma nova senha e confirme-a. A caixa de texto solicitará os requisitos de complexidade de senha.

   :::image type="content" source="./media/howto-create-manage-server-portal/reset-password.png" alt-text="Captura de tela de portal do Azure para escolher camada básica, Uso Geral ou com otimização de memória no banco de dados do Azure para PostgreSQL":::

3. Selecione **OK** para salvar a nova senha.

## <a name="delete-a-server"></a>Excluir um servidor

Você pode excluir o servidor se não precisar mais dele. 

1. Selecione o servidor na portal do Azure. Na janela **visão geral** , selecione **excluir**.

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-delete.png" alt-text="Captura de tela de portal do Azure para escolher camada básica, Uso Geral ou com otimização de memória no banco de dados do Azure para PostgreSQL":::

2. Digite o nome do servidor na caixa de entrada para confirmar que este é o servidor que você deseja excluir.

   :::image type="content" source="./media/howto-create-manage-server-portal/confirm-delete.png" alt-text="Captura de tela de portal do Azure para escolher camada básica, Uso Geral ou com otimização de memória no banco de dados do Azure para PostgreSQL":::

   > [!NOTE]
   > A exclusão de um servidor é irreversível.

3. Selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [backups e restauração do servidor](howto-restore-server-portal.md)
- Saiba mais sobre [as opções de ajuste e monitoramento no banco de dados do Azure para PostgreSQL](concepts-monitoring.md)
