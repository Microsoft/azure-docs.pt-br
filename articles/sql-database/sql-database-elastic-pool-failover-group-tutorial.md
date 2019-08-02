---
title: 'Tutorial: Adicionar um pool elástico do banco de dados SQL do Azure a um grupo de failover | Microsoft Docs'
description: Adicione um pool elástico do banco de dados SQL do Azure a um grupo de failover usando o portal do Azure, o PowerShell ou o CLI do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
manager: jroth
ms.date: 06/19/2019
ms.openlocfilehash: a9c4fb43717788703184522109301e88d4c94f0e
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444806"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Tutorial: Adicionar um pool elástico do banco de dados SQL do Azure a um grupo de failover

Configure um grupo de failover para um pool elástico do banco de dados SQL do Azure e failover de teste usando o portal do Azure.  Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> - Crie um banco de dados individual do banco de dados SQL do Azure.
> - Adicione o banco de dados individual a um pool elástico. 
> - Crie um [grupo de failover](sql-database-auto-failover-group.md) para um pool elástico entre dois SQL Servers lógicos.
> - Failover de teste.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se você tem: 

- Uma assinatura do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/) se você ainda não tiver uma.


## <a name="1---create-a-single-database"></a>1-criar um banco de dados individual 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2-adicionar banco de dados individual ao pool elástico

1. Escolha **criar um recurso** no canto superior esquerdo do [portal do Azure](https://portal.azure.com).
1. Digite `elastic pool` na caixa de pesquisa, pressione Enter, selecione o ícone **pool de banco de dados elástico do SQL** e, em seguida, selecione **criar**. 

    ![Escolha o pool elástico do Marketplace](media/sql-database-elastic-pool-create-failover-group-tutorial/elastic-pool-market-place.png)

1. Configure seu pool elástico com os seguintes valores:
   - **Nome**: Forneça um nome exclusivo para o pool elástico, `myElasticPool`como. 
   - **Assinatura**: Selecione sua assinatura na lista suspensa.
   - **ResourceGroup**: Selecione `myResourceGroup` na lista suspensa o grupo de recursos que você criou na seção 1. 
   - **Servidor**: Selecione o servidor que você criou na seção 1 na lista suspensa.  

       ![Criar novo servidor para pool elástico](media/sql-database-elastic-pool-create-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **Computação + armazenamento**: Selecione **Configurar pool elástico** para configurar sua computação, armazenamento e adicionar seu banco de dados individual ao pool elástico. Na guia **configurações de pool** , deixe o padrão de Gen5, com 2 vCores e 32 GB. 

1. Na página **Configurar** , selecione a guia **bancos** de dados e, em seguida, escolha **Adicionar Banco de dados**. Escolha o banco de dados criado na seção 1 e, em seguida, selecione **aplicar** para adicioná-lo ao pool elástico. Selecione **aplicar** novamente para aplicar as configurações de pool elástico e fechar a página **Configurar** . 

    ![Adicionar Banco de BD SQL ao pool elástico](media/sql-database-elastic-pool-create-failover-group-tutorial/add-database-to-elastic-pool.png)

1. Selecione **examinar + criar** para examinar as configurações do pool elástico e, em seguida, selecione **criar** para criar seu pool elástico. 


## <a name="3---create-the-failover-group"></a>3-criar o grupo de failover 
Nesta etapa, você criará um [grupo de failover](sql-database-auto-failover-group.md) entre um SQL Server do Azure existente e um novo SQL Server do Azure em outra região. Em seguida, adicione o pool elástico ao grupo de failover. 


1. Selecione **todos os serviços** no canto superior esquerdo do [portal do Azure](https://portal.azure.com). 
1. Digite `sql servers` na caixa de pesquisa. 
1. Adicional Selecione o ícone de estrela ao lado de servidores SQL para os **SQL Servers** favoritos e adicione-o ao painel de navegação à esquerda. 
    
    ![Localizar servidores SQL](media/sql-database-single-database-create-failover-group-tutorial/all-services-sql-servers.png)

1. Selecione **SQL** Servers e escolha o servidor que você criou na seção 1.
1. Selecione **grupos de failover** no painel **configurações** e, em seguida, selecione **Adicionar grupo** para criar um novo grupo de failover. 

    ![Adicionar novo grupo de failover](media/sql-database-elastic-pool-create-failover-group-tutorial/add-elastic-pool-to-failover-group.png)

1. Na página **grupo de failover** , insira ou selecione os seguintes valores e, em seguida, selecione **criar**:
    - **Nome do grupo de failover**: Digite um nome de grupo de failover exclusivo, `failovergrouptutorial`como. 
    - **Servidor secundário**: Selecione a opção para *definir as configurações necessárias* e, em seguida, escolha **criar um novo servidor**. Como alternativa, você pode escolher um servidor já existente como o servidor secundário. Depois de inserir os valores a seguir, selecione **selecionar**. 
        - **Nome do servidor**: Digite um nome exclusivo para o servidor secundário, `mysqlsecondary`como. 
        - **Logon de administrador do servidor**: Escreva`azureuser`
        - **Senha**: Digite uma senha complexa que atenda aos requisitos de senha.
        - **Localização**: Escolha um local na lista suspensa, como leste dos EUA 2. Esse local não pode ser o mesmo local que o servidor primário.

       > [!NOTE]
       > As configurações de logon e firewall do servidor devem corresponder à do seu servidor primário. 
    
       ![Criar um servidor secundário para o grupo de failover](media/sql-database-single-database-create-failover-group-tutorial/create-secondary-failover-server.png)

1. Depois que um servidor secundário é selecionado, os **bancos de dados dentro da opção de grupo** se tornam desbloqueados. Selecione-o para **selecionar os bancos de dados a serem adicionados** e, em seguida, selecione o pool elástico criado na seção 2. Um aviso deve ser exibido, solicitando que você crie um pool elástico no servidor secundário. Selecione o aviso e, em seguida, selecione **OK** para criar o pool elástico no servidor secundário. 
        
    ![Adicionar Banco de BD SQL ao grupo de failover](media/sql-database-single-database-create-failover-group-tutorial/add-sqldb-to-failover-group.png)
        
1. Selecione **selecionar** para aplicar as configurações de pool elástico ao grupo de failover e, em seguida, selecione **criar** para criar seu grupo de failover. Adicionar o pool elástico ao grupo de failover iniciará automaticamente o processo de replicação geográfica. 


## <a name="4---test-failover"></a>4-failover de teste 
Nesta etapa, você falhará no grupo de failover para o servidor secundário e, em seguida, fará o failback usando o portal do Azure. 

1. Navegue até o servidor de **servidores SQL** dentro do [portal do Azure](https://portal.azure.com). 
1. Selecione **grupos de failover** no painel **configurações** e escolha o grupo de failover que você criou na seção 2. 
  
   ![Selecione o grupo de failover no portal](media/sql-database-single-database-create-failover-group-tutorial/select-failover-group.png)

1. Examine qual servidor é primário e qual servidor é secundário. 
1. Selecione **failover** no painel de tarefas para fazer failover do grupo de failover que contém o pool elástico. 
1. Selecione **Sim** no aviso que notifica que as sessões de TDS serão desconectadas. 

   ![Fazer failover do grupo de failover que contém o banco de dados SQL](media/sql-database-single-database-create-failover-group-tutorial/failover-sql-db.png)

1. Examine qual servidor é primário, qual servidor é secundário. Se o failover tiver sido bem-sucedido, os dois servidores deverão ter funções trocadas. 
1. Selecione **failover** novamente para falhar o grupo de failover de volta para as configurações originais. 

## <a name="clean-up-resources"></a>Limpar recursos 
Limpe os recursos excluindo o grupo de recursos. 

1. Navegue até o grupo de recursos na [portal do Azure](https://portal.azure.com).
1. Selecione **excluir grupo de recursos** para excluir todos os recursos no grupo, bem como o próprio grupo de recursos. 
1. Digite o nome do grupo de recursos, `myResourceGroup`, na caixa de texto e, em seguida, selecione **excluir** para excluir o grupo de recursos.  


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você adicionou um banco de dados do banco de dados SQL do Azure a um grupo de failover e testou o failover. Você aprendeu como:

> [!div class="checklist"]
> - Crie um banco de dados individual do banco de dados SQL do Azure.
> - Adicione o banco de dados individual a um pool elástico. 
> - Crie um [grupo de failover](sql-database-auto-failover-group.md) para um pool elástico entre dois SQL Servers lógicos.
> - Failover de teste.

Avance para o próximo tutorial sobre como migrar usando DMS.

> [!div class="nextstepaction"]
> [Tutorial: Migrar SQL Server para um banco de dados em pool usando DMS](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
