---
title: Gerenciar servidor-portal do Azure-banco de dados do Azure para MySQL
description: Saiba como gerenciar um servidor de banco de dados do Azure para MySQL do portal do Azure.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 1/26/2021
ms.openlocfilehash: 83876f77e0d7ffc0ae20bc5a545c1f18f53f4a8f
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897978"
---
# <a name="manage-an-azure-database-for-mysql-server-using-the-azure-portal"></a>Gerenciar um servidor de banco de dados do Azure para MySQL usando o portal do Azure

Este artigo mostra como gerenciar seu banco de dados do Azure para servidores MySQL. As tarefas de gerenciamento incluem dimensionamento de computação e armazenamento, redefinição de senha de administrador e detalhes do servidor de exibição.

> [!NOTE]
> Este artigo contém referências ao termo _subordinado_, um termo que a Microsoft não usa mais. Quando o termo for removido do software, também o removeremos deste artigo.
>

## <a name="sign-in"></a>Entrar

Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-server"></a>Criar um servidor

Visite o guia de [início rápido](quickstart-create-mysql-server-database-using-azure-portal.md) para saber como criar e começar a usar um banco de dados do Azure para servidor MySQL.

## <a name="scale-compute-and-storage"></a>Dimensionar a computação e o armazenamento

Após a criação do servidor, você pode dimensionar entre as camadas de Uso Geral e com otimização de memória à medida que suas necessidades mudam. Você também pode dimensionar a computação e a memória aumentando ou diminuindo o vCores. O armazenamento pode ser dimensionado verticalmente (no entanto, você não pode dimensionar o armazenamento verticalmente).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Dimensionar entre as camadas de Uso Geral e com otimização de memória

Você pode dimensionar de Uso Geral para a memória otimizada e vice-versa. Não há suporte para a alteração de e para a camada básica após a criação do servidor.

1. Selecione o servidor na portal do Azure. Selecione **tipo de preço**, localizado na seção **configurações** .

2. Selecione **uso geral** ou **memória otimizada**, dependendo do que você está dimensionando.

   :::image type="content" source="./media/howto-create-manage-server-portal/change-pricing-tier.png" alt-text="Captura de tela de portal do Azure para escolher camada básica, Uso Geral ou com otimização de memória no banco de dados do Azure para MySQL":::

   > [!NOTE]
   > A alteração de camadas causa uma reinicialização do servidor.

3. Selecione **OK** para salvar as alterações.

### <a name="scale-vcores-up-or-down"></a>Dimensionar vCores para cima ou para baixo

1. Selecione o servidor na portal do Azure. Selecione **tipo de preço**, localizado na seção **configurações** .

2. Altere a configuração **vCore**, movendo o controle deslizante para o valor desejado.

    :::image type="content" source="./media/howto-create-manage-server-portal/scaling-compute.png" alt-text="Captura de tela de portal do Azure para escolher a opção vCore no banco de dados do Azure para MySQL":::

    > [!NOTE]
    > O dimensionamento de vCores causa uma reinicialização do servidor.

3. Selecione **OK** para salvar as alterações.

### <a name="scale-storage-up"></a>Escalar o armazenamento verticalmente

1. Selecione o servidor na portal do Azure. Selecione **tipo de preço**, localizado na seção **configurações** .

2. Altere a configuração de **armazenamento** movendo o controle deslizante para cima até o valor desejado.

   :::image type="content" source="./media/howto-create-manage-server-portal/scaling-storage.png" alt-text="Captura de tela de portal do Azure para escolher a escala de armazenamento no banco de dados do Azure para MySQL":::

   > [!NOTE]
   > O armazenamento não pode ser reduzido verticalmente.

3. Selecione **OK** para salvar as alterações.

## <a name="update-admin-password"></a>Atualizar senha do administrador

Você pode alterar a senha da função de administrador usando o portal do Azure.

1. Selecione o servidor na portal do Azure. Na janela **visão geral** , selecione **Redefinir senha**.

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-reset-password.png" alt-text="Captura de tela de portal do Azure para redefinir a senha no banco de dados do Azure para MySQL":::

2. Insira uma nova senha e confirme-a. A caixa de texto solicitará os requisitos de complexidade de senha.

   :::image type="content" source="./media/howto-create-manage-server-portal/reset-password.png" alt-text="Captura de tela de portal do Azure para redefinir sua senha e salvar no banco de dados do Azure para MySQL":::

3. Selecione **OK** para salvar a nova senha.
 

> [!IMPORTANT]
> A redefinição da senha de administrador do servidor redefinirá automaticamente os privilégios de administrador do servidor como padrão. Considere redefinir a senha de administrador do servidor se você acidentalmente revogou um ou mais privilégios de administrador do servidor.
   
> [!NOTE]
> O usuário administrador do servidor tem os seguintes privilégios por padrão: selecionar, inserir, atualizar, excluir, criar, descartar, recarregar, processar, fazer referência, INDEXar, alterar, mostrar bancos de dados, criar tabelas TEMPORÁRIAs, bloquear tabelas, executar, replicação escravo, cliente de replicação, criar exibição, mostrar exibição, criar rotina, alterar rotina, criar usuário, evento, gatilho

## <a name="delete-a-server"></a>Excluir um servidor

Você pode excluir o servidor se não precisar mais dele.

1. Selecione o servidor na portal do Azure. Na janela **visão geral** , selecione **excluir**.

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-delete.png" alt-text="Captura de tela de portal do Azure para excluir o servidor no banco de dados do Azure para MySQL":::

2. Digite o nome do servidor na caixa de entrada para confirmar que este é o servidor que você deseja excluir.

   :::image type="content" source="./media/howto-create-manage-server-portal/confirm-delete.png" alt-text="Captura de tela de portal do Azure para confirmar a exclusão do servidor no banco de dados do Azure para MySQL":::

   > [!NOTE]
   > A exclusão de um servidor é irreversível.

3. Selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [backups e restauração do servidor](howto-restore-server-portal.md)
- Saiba mais sobre [as opções de ajuste e monitoramento no banco de dados do Azure para MySQL](concepts-monitoring.md)
