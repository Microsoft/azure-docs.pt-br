---
title: Gerenciar servidor-portal do Azure-banco de dados do Azure para MariaDB
description: Saiba como gerenciar um banco de dados do Azure para MariaDB Server do portal do Azure.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: ade70c884a3ef01db45273e4789f34b629473661
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664394"
---
# <a name="manage-an-azure-database-for-mariadb-server-using-the-azure-portal"></a>Gerenciar um banco de dados do Azure para o MariaDB Server usando o portal do Azure
Este artigo mostra como gerenciar o banco de dados do Azure para servidores MariaDB. As tarefas de gerenciamento incluem dimensionamento de computação e armazenamento, redefinição de senha de administrador e detalhes do servidor de exibição.

## <a name="sign-in"></a>Entrar
Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-server"></a>Criar um servidor
Visite o guia de [início rápido](quickstart-create-mariadb-server-database-using-azure-portal.md) para saber como criar e começar a usar um banco de dados do Azure para o MariaDB Server.

## <a name="scale-compute-and-storage"></a>Dimensionar a computação e o armazenamento

Após a criação do servidor, você pode dimensionar entre as camadas de Uso Geral e com otimização de memória à medida que suas necessidades mudam. Você também pode dimensionar a computação e a memória aumentando ou diminuindo o vCores. O armazenamento pode ser dimensionado verticalmente (no entanto, você não pode dimensionar o armazenamento verticalmente).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Dimensionar entre as camadas de Uso Geral e com otimização de memória

Você pode dimensionar de Uso Geral para a memória otimizada e vice-versa. Não há suporte para a alteração de e para a camada básica após a criação do servidor. 

1. Selecione o servidor na portal do Azure. Selecione **tipo de preço**, localizado na seção **configurações** .

2. Selecione **uso geral** ou **memória otimizada**, dependendo do que você está dimensionando. 

    ![Captura de tela mostra a portal do Azure com o tipo de preço selecionado e um valor de memória otimizada selecionada.](./media/howto-create-manage-server-portal/change-pricing-tier.png)

    > [!NOTE]
    > A alteração de camadas causa uma reinicialização do servidor.

4. Selecione **OK** para salvar as alterações.


### <a name="scale-vcores-up-or-down"></a>Dimensionar vCores para cima ou para baixo

1. Selecione o servidor na portal do Azure. Selecione **tipo de preço**, localizado na seção **configurações** .

2. Altere a configuração **vCore**, movendo o controle deslizante para o valor desejado.

    ![scale-compute](./media/howto-create-manage-server-portal/scaling-compute.png)

    > [!NOTE]
    > O dimensionamento de vCores causa uma reinicialização do servidor.

3. Selecione **OK** para salvar as alterações.


### <a name="scale-storage-up"></a>Escalar o armazenamento verticalmente

1. Selecione o servidor na portal do Azure. Selecione **tipo de preço**, localizado na seção **configurações** .

2. Altere a configuração de **armazenamento** movendo o controle deslizante para cima até o valor desejado.

    ![scale-storage](./media/howto-create-manage-server-portal/scaling-storage.png)

    > [!NOTE]
    > O armazenamento não pode ser reduzido verticalmente.

3. Selecione **OK** para salvar as alterações.


## <a name="update-admin-password"></a>Atualizar senha do administrador
Você pode alterar a senha da função de administrador usando o portal do Azure.

1. Selecione o servidor na portal do Azure. Na janela **visão geral** , selecione **Redefinir senha**.

   ![visão geral](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. Insira uma nova senha e confirme-a. A caixa de texto solicitará os requisitos de complexidade de senha.

   ![Captura de tela mostra a caixa de diálogo redefinir a senha com senha e confirmar senha.](./media/howto-create-manage-server-portal/reset-password.png)

3. Selecione **OK** para salvar a nova senha.


## <a name="delete-a-server"></a>Excluir um servidor

Você pode excluir o servidor se não precisar mais dele. 

1. Selecione o servidor na portal do Azure. Na janela **visão geral** , selecione **excluir**.

    ![excluir](./media/howto-create-manage-server-portal/overview-delete.png)

2. Digite o nome do servidor na caixa de entrada para confirmar que este é o servidor que você deseja excluir.

    ![Captura de tela mostra uma caixa de diálogo que verifica se você deseja excluir um banco de dados, o que é irreversível.](./media/howto-create-manage-server-portal/confirm-delete.png)

    > [!NOTE]
    > A exclusão de um servidor é irreversível.

3. Selecione **Excluir**.


## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [backups e restauração do servidor](howto-restore-server-portal.md)
- Saiba mais sobre [as opções de ajuste e monitoramento no banco de dados do Azure para MariaDB](concepts-monitoring.md)
