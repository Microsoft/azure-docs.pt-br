---
title: Gerenciar servidor-portal do Azure-banco de dados do Azure para servidor flexível MySQL
description: Saiba como gerenciar um servidor flexível do banco de dados do Azure para MySQL do portal do Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 7a01863b3a0c29e94550be67ca957655cff32660
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90932883"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>Gerenciar um banco de dados do Azure para MySQL-servidor flexível (versão prévia) usando o portal do Azure


> [!IMPORTANT]
> O Banco de Dados do Azure para MySQL – Servidor Flexível está atualmente na versão prévia pública.

Este artigo mostra como gerenciar seus servidores flexíveis do banco de dados do Azure para MySQL (versão prévia). As tarefas de gerenciamento incluem dimensionamento de computação e armazenamento, senha de administrador do servidor REST e excluir seu servidor.

## <a name="sign-in"></a>Entrar
Entre no [portal do Azure](https://portal.azure.com). Vá para o recurso de servidor flexível na portal do Azure.

## <a name="scale-compute-and-storage"></a>Dimensionar a computação e o armazenamento

Após a criação do servidor, você pode dimensionar entre os vários [tipos de preço](https://azure.microsoft.com/pricing/details/mysql/) conforme suas necessidades mudam. Você também pode escalar ou reduzir verticalmente sua computação e sua memória aumentando ou diminuindo vCores.

1. Selecione o servidor na portal do Azure. Selecione **computação + armazenamento**, localizado na seção **configurações** .

2. Você pode alterar a **camada de computação**, **vCore**, **armazenamento** para escalar verticalmente o servidor usando uma camada de computação mais alta ou escalar verticalmente na mesma camada, aumentando o armazenamento ou vCoresndo para um valor desejado.

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

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="excluir o servidor flexível":::

   > [!NOTE]
   > A exclusão de um servidor é irreversível.

3. Selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas
- [Saiba como iniciar ou parar um servidor](how-to-stop-start-server-portal.md)
- [Saiba como restaurar um servidor](how-to-restore-server-portal.md)
- [Solucionar problemas de conexão](how-to-troubleshoot-common-connection-issues.md)

