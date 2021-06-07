---
title: Gerenciar servidor-portal do Azure-banco de dados do Azure para PostgreSQL-servidor flexível
description: Saiba como gerenciar um servidor de banco de dados do Azure para PostgreSQL-flexível do portal do Azure.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: mvc
ms.openlocfilehash: 1ac418d855696138341115412dc7e2601d4cf3a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91961401"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Gerenciar um banco de dados do Azure para PostgreSQL – servidor flexível usando o portal do Azure

> [!IMPORTANT]
> O Servidor Flexível do Banco de Dados do Azure para PostgreSQL está em versão prévia

Este artigo mostra como gerenciar seu banco de dados do Azure para PostgreSQL – servidor flexível. As tarefas de gerenciamento incluem dimensionamento de computação e armazenamento, redefinição de senha de administrador e detalhes do servidor de exibição.

## <a name="sign-in"></a>Entrar

Entre no [portal do Azure](https://portal.azure.com). Vá para o recurso de servidor flexível na portal do Azure.

## <a name="scale-compute-and-storage"></a>Dimensionar a computação e o armazenamento

Após a criação do servidor, você pode dimensionar entre os vários [tipos de preço](https://azure.microsoft.com/pricing/details/postgresql/) conforme suas necessidades mudam. Você também pode escalar ou reduzir verticalmente sua computação e sua memória aumentando ou diminuindo vCores.

> [!NOTE]
> O armazenamento não pode ser reduzido para o valor mais baixo.

1. Selecione o servidor na portal do Azure. Selecione **computação + armazenamento**, localizado na seção **configurações** .
2. Você pode alterar a **camada de computação** , **vCore**, **armazenamento** para escalar verticalmente o servidor usando a camada de computação mais alta ou escalar verticalmente na mesma camada aumentando o armazenamento ou vCores para o valor desejado.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/scale-server.png" alt-text="dimensionamento do servidor flexível de armazenamento":::

> [!Important]
> - O armazenamento não pode ser reduzido verticalmente.
> - O dimensionamento de vCores causa uma reinicialização do servidor.

3. Selecione **OK** para salvar as alterações.

## <a name="reset-admin-password"></a>Redefinir senha de administrador

Você pode alterar a senha da função de administrador usando o portal do Azure.

1. Selecione o servidor na portal do Azure. Na janela **visão geral** , selecione **Redefinir senha**.
2. Insira uma nova senha e confirme-a. A caixa de texto solicitará os requisitos de complexidade de senha.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/reset-password.png" alt-text="redefinir sua senha para o servidor flexível":::

3. Selecione **salvar** para salvar a nova senha.

## <a name="delete-a-server"></a>Excluir um servidor

Você pode excluir o servidor se não precisar mais dele.

1. Selecione o servidor na portal do Azure. Na janela **visão geral** , selecione **excluir**.
2. Digite o nome do servidor na caixa de entrada para confirmar que você deseja excluir o servidor.

   :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="excluir o servidor flexível":::

   > [!IMPORTANT]
   > A exclusão de um servidor é irreversível.

  > [!div class="mx-imgBorder"]
  > ![excluir o servidor flexível](./media/howto-manage-server-portal/delete-server.png)  

3. Selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

- [Entender os conceitos de backup e restauração](concepts-backup-restore.md)
- [Ajustar e monitorar o servidor](concepts-monitoring.md)
