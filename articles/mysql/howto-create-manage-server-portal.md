---
title: Criar e gerenciar um servidor de Banco de Dados do Azure para MySQL usando o Portal do Azure | Microsoft Docs
description: "Este artigo descreve como você pode criar rapidamente um novo servidor de Banco de Dados do Azure para MySQL e gerenciar o servidor usando o Portal do Azure."
services: mysql
author: v-chenyh
ms.author: nolanwu
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 6e9c541aac1241b6af0e4a58f5591d46f9a98c40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Criar e gerenciar um servidor de Banco de Dados do Azure para MySQL usando o Portal do Azure
Este tópico descreve como criar rapidamente um novo servidor de Banco de Dados do Azure para MySQL. Também inclui informações sobre como gerenciar o servidor usando o Portal do Azure. O gerenciamento de servidor inclui a exibição de detalhes do servidor e bancos de dados, redefinição de senha e exclusão do servidor.

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure
Faça logon no [Portal do Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-mysql-server"></a>Criar um servidor de Banco de Dados do Azure para MySQL
Execute estas etapas para criar um servidor de Banco de Dados do Azure para MySQL chamado "mysqlserver4demo".

1. Clique no botão **Novo** localizado na parte superior esquerda do Portal do Azure.

2. Na página Novo, selecione **Banco de Dados** e, em seguida, na página Banco de Dados, selecione **Banco de Dados do Azure para MySQL**.

    > Um Banco de Dados do Azure para MySQL é criado com um conjunto definido de recursos de [computação e armazenamento](./concepts-compute-unit-and-storage.md). O banco de dados é criado dentro de um grupo de recursos do Azure e em um servidor de Banco de Dados do Azure para MySQL.

   ![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3. Preencha o formulário do Banco de Dados do Azure para MySQL usando as seguintes informações:

    | **Campo de Formulário** | **Descrição do Campo** |
    |----------------|-----------------------|
    | *Nome do servidor* | azure-mysql (o nome do servidor é globalmente exclusivo) |
    | *Assinatura* | MySQLaaS (selecione no menu suspenso) |
    | *Grupo de recursos* | myresource (crie um novo grupo de recursos ou use um existente) |
    | *Logon de administrador do servidor* | myadmin (nome de conta do administrador de configuração) |
    | *Senha* | configurar senha da conta do administrador |
    | *Confirmar senha* | confirmar senha da conta do administrador |
    | *Localidade* | Europa Setentrional (escolha entre o Europa Setentrional e Oeste dos EUA) |
    | *Versão* | 5.6 (escolha uma versão do servidor de Banco de Dados do Azure para MySQL) |

4. Clique em **Tipo de preço** para especificar o nível de desempenho e o tipo de serviço para o novo servidor. A Unidade de Computação pode ser configurada entre 50 e 100 na Camada básica, entre 100 e 200 na Camada padrão, e o armazenamento pode ser adicionado com base na quantidade incluída. Neste guia de instruções, vamos escolher 50 Unidades de Computação e 50 GB. Clique em **OK** para salvar sua seleção.

   ![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. Clique em **Criar** para provisionar o servidor. O provisionamento demora alguns minutos.

    > Selecione a opção **Fixar no painel** para permitir o controle fácil das suas implantações.
    > [!NOTE]
    > Embora exista suporte para até 1.000 GB na Camada básica e 10.000 GB na Camada padrão para armazenamento, para Visualização Pública, o armazenamento máximo está limitado temporariamente a 1.000 GB.</Include>

## <a name="update-an-azure-database-for-mysql-server"></a>Atualizar um servidor de Banco de Dados do Azure para MySQL
Após o provisionamento do novo servidor, o usuário terá duas opções para editar um servidor existente: redefinir a senha de administrador ou escalar vertical ou horizontalmente o servidor, alterando as unidades de computação.

### <a name="change-the-administrator-user-password"></a>Alterar a senha de usuário do administrador
1. Na folha **Visão geral** do servidor, clique em **Redefinir senha** para preencher uma janela de entrada e confirmação de senha.

2. Insira a nova senha e confirme-a na janela, conforme mostrado:

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. Clique em **OK** para salvar a nova senha.

### <a name="scale-updown-by-changing-compute-units"></a>Escalar verticalmente alterando as Unidades de Computação

1. Na folha do servidor, em **Configurações**, clique em **Tipo de preço** para abrir a folha Tipo de preço para o servidor de Banco de Dados do Azure para MySQL.

2. Execute a Etapa 4 de **Criar um servidor de Banco de Dados do Azure para MySQL** para alterar as Unidades de Computação no mesmo Tipo de preço.

## <a name="delete-an-azure-database-for-mysql-server"></a>Excluir um servidor de Banco de Dados do Azure para MySQL

1. Na folha **Visão geral** do servidor, clique no botão de comando **Excluir** para abrir a folha de Confirmação de exclusão.

2. Digite o nome correto do servidor na caixa de entrada da folha para confirmar duas vezes.

3. Clique no botão **Excluir** novamente para confirmar a ação de exclusão e aguarde a exibição do pop-up "Exclusão bem-sucedida" na barra de notificação.

## <a name="list-the-azure-database-for-mysql-databases"></a>Listar os bancos de dados do Banco de Dados do Azure para MySQL
Na folha **Visão geral** do servidor, role para baixo até ver o bloco do banco de dados na parte inferior. Todos os bancos de dados estão listados na tabela. Clique no botão de comando **Excluir** para abrir a folha de Confirmação de exclusão.

   ![show-databases](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>Mostrar detalhes de um servidor de Banco de Dados do Azure para MySQL
Na folha do servidor, em **Configurações**, clique em **Propriedades** para abrir a folha **Propriedades** e, em seguida, exibir todas as informações detalhadas sobre o servidor.

## <a name="next-steps"></a>Próximas etapas

[Início rápido: criar e gerenciar um servidor de Banco de Dados do Azure para MySQL usando o Portal do Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)