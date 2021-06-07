---
title: Tutorial – Configurar funções do Azure Analysis Services | Microsoft Docs
description: Neste tutorial, aprenda a configurar as funções de administrador e usuário do Azure Analysis Services usuário usando o portal do Azure ou o SQL Server Management Studio.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: aea2196671a136145671b977a6d925849b635b73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92018688"
---
# <a name="tutorial-configure-server-administrator-and-user-roles"></a>Tutorial: Configurar funções de administrador do servidor e usuário

 Neste tutorial, você usa o SSMS (SQL Server Management Studio) para conectar o servidor no Azure para configurar as funções de modelo de banco de dados e administrador do servidor. Você também é apresentado à [TMSL (Linguagem de Scripts de Modelo de Tabela)](/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200). A TMSL é uma linguagem de scripts baseada em JSON para modelos tabulares nos níveis de compatibilidade 1200 e superior. Ela pode ser utilizada para automatizar muitas tarefas de modelagem de tabela. A TMSL é frequentemente utilizada com o PowerShell mas, neste tutorial, você usará o editor de consulta XMLA no SSMS. Com este tutorial, você concluirá estas tarefas: 
  
> [!div class="checklist"]
> * Obter o nome do servidor no portal
> * Conectar o servidor usando SSMS
> * Adicionar um usuário ou grupo à função de administrador do servidor 
> * Adicionar um usuário ou grupo à função de administrador do modelo de banco de dados
> * Adicionar uma nova função de modelo de banco de dados e adicionar um usuário ou grupo

Para saber mais sobre segurança do usuário no Azure Analysis Services, consulte [Autenticação e permissões de usuário](../analysis-services-manage-users.md). 

## <a name="prerequisites"></a>Pré-requisitos

- Um Azure Active Directory em sua assinatura.
- Um [servidor do Azure Analysis Services](../analysis-services-create-server.md) criado em sua assinatura.
- Ter permissões de [administrador do servidor](../analysis-services-server-admins.md).
- [Adicione o modelo de exemplo adventureworks](../analysis-services-create-sample-model.md) ao servidor.
- [Instale a última versão do SSMS](/sql/ssms/download-sql-server-management-studio-ssms) (SQL Server Management Studio).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal](https://portal.azure.com/).

## <a name="get-server-name"></a>Obter nome do servidor
Para conectar o servidor a partir do SSMS, primeiro é necessário o nome do servidor. É possível obter o nome do servidor no portal.

No **Portal do Azure** > servidor > **Visão geral** > **Nome do servidor**, copie o nome do servidor.
   
   ![Obter o nome do servidor no Azure](./media/analysis-services-tutorial-roles/aas-copy-server-name.png)

## <a name="connect-in-ssms"></a>Conectar-se no SSMS

Para as tarefas restantes, será utilizado o SSMS para conectar e gerenciar o servidor.

1. No SSMS > **Pesquisador de Objetos**, clique em **Conectar** > **Analysis Services**.

    ![Conectar](./media/analysis-services-tutorial-roles/aas-ssms-connect.png)

2. Na caixa de diálogo **Conectar o Servidor** em **Nome do servidor**, cole o nome do servidor que você copiou do portal. Em **Autenticação**, escolha **Universal do Active Directory com suporte MFA** e, em seguida, insira a conta de usuário e pressione **Conectar**.
   
    ![Conectar-se no SSMS](./media/analysis-services-tutorial-roles/aas-connect-ssms-auth.png)

    > [!TIP]
    > É recomendável escolher Universal do Active Directory com suporte MFA. Este tipo de autenticação dá suporte para [autenticação multifator e não interativa ](../../azure-sql/database/authentication-mfa-ssms-overview.md). 

3. Em **Pesquisador de Objetos**, expanda para ver os objetos do servidor. Clique com o botão direito do mouse para ver as propriedades do servidor.
   
    ![Pesquisador de Objetos no SSMS](./media/analysis-services-tutorial-roles/aas-connect-ssms-objexp.png)

## <a name="add-a-user-account-to-the-server-administrator-role"></a>Adicionar uma conta de usuário à função de administrador do servidor

Nesta tarefa, você adiciona uma conta de usuário ou grupo do Azure AD à função de administrador do servidor. Ao especificar um grupo de segurança, use `obj:groupid@tenantid`.

1. Em **Pesquisador de Objetos**, clique com o botão direito do mouse no nome do servidor e clique em **Propriedades**. 
2. Na janela **Propriedades do Analysis Server**, clique em **Segurança** > **Adicionar**.
3. Na janela **Selecionar um Usuário ou Grupo**, insira uma conta de usuário ou grupo no Azure AD e, em seguida, clique em **Adicionar**. 
   
     ![Adicionar administrador do servidor](./media/analysis-services-tutorial-roles/aas-add-server-admin.png)

4. Cliquem em **OK**, para fecha as **Propriedades do Analysis Server**.

    > [!TIP]
    > Também é possível adicionar administradores do servidor, utilizando **Administradores do Analysis Services** no portal. 

## <a name="add-a-user-to-the-model-database-administrator-role"></a>Adicionar um usuário à função de administrador do modelo de banco de dados

Nesta tarefa, você adicionará uma conta de usuário ou grupo à função Administrador de Vendas pela Internet que já existe no modelo. Essa função tem permissões de Controle total (Administrador) para o modelo de banco de dados de exemplo adventureworks. Essa tarefa usa o comando de TMSL [CreateOrReplace](/analysis-services/tmsl/createorreplace-command-tmsl) em um script criado para você.

1. No **Pesquisador de Objetos**, expanda **Bancos de Dados** > **adventureworks** > **Funções**. 
2. Clique com o botão direito do mouse em **Administrador de Vendas pela Internet** e, em seguida, clique em **Função de Script como** > **CREATE OR REPLACE To** > **Janela do Editor de Nova Consulta**.

    ![Janela do Editor de Nova Consulta](./media/analysis-services-tutorial-roles/aas-add-db-admin.png)

3. Em **XMLAQuery**, altere o valor para **"memberName":** para uma conta de usuário ou grupo no Azure AD. Por padrão, a conta na qual está conectado está incluída, no entanto, não é necessário adicionar sua própria conta porque já é um administrador do servidor.

    ![Script TMSL na consulta XMLA](./media/analysis-services-tutorial-roles/aas-add-db-admin-script.png)

4. Pressione **F5**, para executar o script.


## <a name="add-a-new-model-database-role-and-add-a-user-or-group"></a>Adicionar uma nova função de modelo de banco de dados e adicionar um usuário ou grupo

Nesta tarefa, você usa o comando [Criar](/analysis-services/tmsl/create-command-tmsl) em um script TMSL para criar uma nova função Global de Vendas pela Internet, especifica permissões de *leitura* para a função e adiciona uma conta de grupo ou usuário do Azure AD.

1. Em **Pesquisador de Objetos**, clique com o botão direito do mouse em **adventureworks**, e, em seguida, clique em **Nova Consulta** > **XMLA**. 
2. Copie e cole o seguinte script TMSL no editor de consultas:

    ```JSON
    {
    "create": {
      "parentObject": {
        "database": "adventureworks",
       },
       "role": {
         "name": "Internet Sales Global",
         "description": "All users can query model data",
         "modelPermission": "read",
         "members": [
           {
             "memberName": "globalsales@adventureworks.com",
             "identityProvider": "AzureAD"
           }
         ]
       }
      }
    }
    ```

3. Altere o valor do objeto `"memberName": "globalsales@adventureworks.com"` para uma conta de grupo ou usuário no Azure AD.
4. Pressione **F5**, para executar o script.

## <a name="verify-your-changes"></a>Verifique as alterações

1. Em **Pesquisador de Objetos**, clique no nome do servidor e clique em **Atualizar** ou pressione **F5**.
2. Expanda **Bancos de Dados** > **adventureworks** > **Funções**. Verifique a conta de usuário e as novas alterações de função adicionadas nas tarefas anteriores serão exibidas.   

    ![Verificar no Pesquisador de Objetos](./media/analysis-services-tutorial-roles/aas-connect-ssms-verify.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessárias, exclua as funções e as contas de grupo e usuário. Para fazer isso, use **Propriedades de Função** > **Associação** para remover as contas de usuário, ou clique com o botão direito do mouse em uma função e, em seguida, clique em **Excluir**.


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como conectar o servidor de sistema autônomo do Azure e a explorar os modelos de banco de dados de exemplo adventureworks no SSMS. Você também aprendeu a usar scripts SSMS e TMSL para adicionar usuários ou grupos a funções existentes e novas. Agora que você tem permissões de usuário configuradas para o servidor e modelo de banco de dados de exemplo, você e outros usuários poderão conectar-se a ele usando aplicativos clientes, como o Power BI. Para saber mais, continue no próximo tutorial. 

> [!div class="nextstepaction"]
> [Tutorial: Conectar-se com o Power BI Desktop](analysis-services-tutorial-pbid.md)