---
title: Mover recursos do SQL do Azure entre regiões com o Azure Resource Mover
description: Saiba como mover os recursos do SQL do Azure para outra região com o Azure Resource Mover
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 4678a6128be13ac61dc4ac67bbd1a17e99c6d24d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99820222"
---
# <a name="tutorial-move-azure-sql-database-resources-to-another-region"></a>Tutorial: Mover os recursos do Banco de Dados SQL do Azure para outra região

Neste tutorial, aprenda a mover os bancos de dados SQL do Azure e os pools elásticos para outra região do Azure usando o [Azure Resource Mover](overview.md).

> [!NOTE]
> O Azure Resource Mover está em versão prévia no momento.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Verificar os pré-requisitos e os requisitos.
> * Selecionar os recursos que deseja mover.
> * Resolver as dependências do recurso.
> * Preparar e mover o SQL Server para a região de destino.
> * Preparar e mover os bancos de dados e os pool elásticos.
> * Decidir se deseja descartar ou confirmar a movimentação. 
> * Opcionalmente, remover os recursos da região de origem após a movimentação. 

> [!NOTE]
> Os tutoriais mostram o caminho mais rápido para experimentar um cenário e usar as opções padrão. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar. Em seguida, entre no [portal do Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Pré-requisitos

-  Verifique se você tem acesso de *Proprietário* na assinatura que contém os recursos que deseja mover.
    - Na primeira vez que você adicionar um recurso para um par de origem e destino específico em uma assinatura do Azure, o Resource Mover criará uma [identidade gerenciada atribuída pelo sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (anteriormente conhecida como MSI [Identidade Gerenciada de Serviço]) que é confiável para a assinatura.
    - Para criar a identidade e atribuir a ela a função necessária (colaborador ou administrador de acesso do usuário na assinatura de origem), a conta usada para adicionar recursos precisa de permissões de *Proprietário* na assinatura. [Saiba mais](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) sobre as funções do Azure.
- A assinatura precisa de cota suficiente para criar os recursos que você está movendo na região de destino. Se não houver cota, [solicite limites adicionais](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Verifique os preços e os encargos associados à região de destino para a qual você está movendo os recursos. Use a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para obter ajuda.
    

## <a name="check-sql-requirements"></a>Verificar os requisitos do SQL

1. [Verifique](support-matrix-move-region-sql.md) quais recursos de banco de dados/pool elástico têm suporte para movimentação para outra região.
2. Na região de destino, crie um servidor de destino para cada servidor de origem. [Saiba mais](../azure-sql/database/active-geo-replication-security-configure.md#how-to-configure-logins-and-users).
4. Se os bancos de dados forem criptografados com TDE (Transparent Data Encryption) e você usar sua própria chave de criptografia no Azure Key Vault, [saiba como](../key-vault/general/move-region.md) mover os cofres de chaves para outra região.
5. Se a sincronização de dados SQL estiver habilitada, haverá suporte para a movimentação de bancos de dado membros. Após a movimentação, é preciso configurar a sincronização de dados SQL com o novo banco de dados de destino.
6. Remova as configurações avançadas de segurança de dados antes da movimentação. Após a movimentação, [defina as configurações](../azure-sql/database/azure-defender-for-sql.md) no nível do SQL Server na região de destino.
7. Se a auditoria estiver habilitada, as políticas serão redefinidas para o padrão após a movimentação. [Configure a auditoria](../azure-sql/database/auditing-overview.md) novamente, após a movimentação.
7. As políticas de retenção de backup do banco de dados de origem são transportadas para o banco de dados de destino. [Saiba mais](../azure-sql/database/long-term-backup-retention-configure.md) sobre como modificar as configurações após a movimentação.
8. Remova as regras de firewall no nível do servidor antes da movimentação. As regras de firewall no nível do banco de dados são copiadas do servidor de origem para o servidor de destino durante a movimentação. Após a movimentação, [configure regras de firewall](../azure-sql/database/firewall-create-server-level-portal-quickstart.md) para o SQL Server na região de destino.
9. Remova as configurações de ajuste automático antes da movimentação. [Configure o ajuste automático ](../azure-sql/database/automatic-tuning-enable.md) novamente após a movimentação.
10. Remova as configurações de alerta do banco de dados antes de movimentação. [Redefina](../azure-sql/database/alerts-insights-configure-portal.md) após mover.
    
## <a name="select-resources"></a>Selecionar recursos

Selecione os recursos que deseja mover.

- Você pode selecionar qualquer tipo de recurso compatível em qualquer grupo de recursos da região de origem selecionada.
- Você moverá os recursos para uma região de destino na mesma assinatura que a região de origem. Se você quiser alterar a assinatura, poderá fazer isso depois que os recursos forem movidos.

1. No portal do Azure, pesquise por *Resource Mover*. Em seguida, em **Serviços**, selecione o **Azure Resource Mover**.

     ![Resultados da pesquisa por Resource Mover no portal do Azure](./media/tutorial-move-region-sql/search.png)

2. Em **Visão Geral**, clique em **Introdução**.

    ![Botão para adicionar recursos a serem movidos para outra região](./media/tutorial-move-region-sql/get-started.png)

3. Em **Mover recursos** > **Origem + Destino**, selecione a assinatura e a região de origem.
4. Em **Destino**, selecione a região para a qual deseja mover os recursos. Em seguida, clique em **Próximo**.

    ![Página para selecionar a região de origem e de destino](./media/tutorial-move-region-sql/source-target.png)

6. Em **Recursos a serem movidos**, clique em **Selecionar recursos**.
7. Em **Selecionar recursos**, selecione os recursos desejados. Você só pode adicionar recursos compatíveis para movimentação. Em seguida, clique em **Concluído**.

    ![Página para selecionar os recursos do SQL a serem movidos](./media/tutorial-move-region-sql/select-resources.png)

8. Em **Recursos a serem movidos**, clique **Próximo**.

9. Em **Examinar + Adicionar**, verifique as configurações de origem e destino. Verifique se você entendeu que os metadados sobre a movimentação serão armazenados em um grupo de recursos criado para essa finalidade na região de metadados.


    ![Página para examinar as configurações e prosseguir com a movimentação](./media/tutorial-move-region-sql/review.png)

10. Clique em **Prosseguir**, para começar a adicionar os recursos.
11. Depois que o processo de adição for concluído com sucesso, clique em **Adicionando recursos a serem movidos** no ícone de notificação.
12. Depois de clicar na notificação, examine os recursos da página **Entre regiões**.


> [!NOTE]
> 
> - Neste momento, o SQL Server estará no estado de *Atribuição manual pendente*.
> - Outros recursos adicionados estarão no estado de *Preparação pendente*.
> - Se você quiser remover um recurso de uma coleção de movimentação, o método para fazer isso dependerá do ponto do processo de movimentação em que você se encontra. [Saiba mais](remove-move-resources.md).

## <a name="resolve-dependencies"></a>Resolver dependências


1. Em **Entre regiões**, se os recursos mostrarem uma mensagem *Validar dependências* na coluna **Problemas**, clique no botão **Validar dependências**. O processo de validação começa.
2. Se forem encontradas dependências, clique em **Adicionar dependências**.

    ![Botão para adicionar dependências](./media/tutorial-move-region-sql/add-dependencies.png)
   
3. Em **Adicionar dependências**, selecione os recursos dependentes > **Adicionar dependências**. Monitore o progresso nas notificações.

4. Adicione outras dependências, se necessário, e valide as dependências novamente. 

5. Na página **Entre regiões**, verifique se os recursos estão agora no estado de *Preparação pendente*, sem problemas.

    ![Página mostrando recursos no estado de preparação pendente](./media/tutorial-move-region-sql/prepare-pending.png)



## <a name="move-the-sql-server"></a>Mover o SQL Server

Atribua um SQL Server de destino na região de destino e confirme a operação.

### <a name="assign-a-target-sql-server"></a>Atribuir um SQL Server de destino

1. Em **Entre regiões**, para o recurso do SQL Server, na coluna de **Configuração de destino**, clique em **Recurso não atribuído**.
2. Selecione um recurso do SQL Server existente na região de destino. 
    
    ![Entrada mostrando o estado do SQL Server definido como Confirmar movimentação pendente](./media/tutorial-move-region-sql/sql-server-commit-move-pending.png) 

    
> [!NOTE]
> O estado do SQL Server de origem é alterado para *Confirmar movimentação pendente*. 

### <a name="commit-the-sql-server-move"></a>Confirmar a movimentação do SQL Server

1. Em **Entre regiões**, selecione o SQL Server e clique em **Confirmar movimentação**.
2. Em **Confirmar recursos**, clique em **Confirmar**.

    ![Página para confirmar a movimentação do SQL Server](./media/tutorial-move-region-sql/commit-sql-server.png)

3. Monitore o progresso da movimentação na barra de notificações.

> [!NOTE]
> Após a confirmação, o SQL Server entra no estado de *Excluir origem pendente*.


## <a name="prepare-resources-to-move"></a>Preparar os recursos a serem movidos

Após mover o SQL Server de origem, você pode se preparar para mover os outros recursos.

## <a name="prepare-an-elastic-pool"></a>Preparar um pool elástico

1. Em **Entre regiões**, selecione o pool elástico de origem (demo-test1-elasticpool no nosso passo a passo) e clique em **Preparar**.

    ![Botão para preparar recursos](./media/tutorial-move-region-sql/prepare-elastic.png)

2. Em **Preparar recursos**, clique em **Preparar**.
3. Quando as notificações mostrarem que o processo de preparação foi bem-sucedido, clique em **Atualizar**.

> [!NOTE]
> Então, o pool elástico estará no estado de *Iniciar movimentação pendente*.

## <a name="prepare-a-single-database"></a>Preparar um banco de dados individual

1. Em **Entre regiões**, selecione o banco de dados individual (não em um pool elástico) e clique em **Preparar**.

    ![Botão para preparar recursos selecionados](./media/tutorial-move-region-sql/prepare-db.png)

2. Em **Preparar recursos**, clique em **Preparar**.
3. Quando as notificações mostrarem que o processo de preparação foi bem-sucedido, clique em **Atualizar**.

> [!NOTE]
> Então, o banco de dados estará no estado de *Iniciar movimentação pendente* e terá sido criado na região de destino.


## <a name="move-the-pool-and-prepare-pool-databases"></a>Mover o pool e preparar bancos de dados de pool

Para preparar bancos de dados em um pool elástico, o pool elástico deve estar no estado de *Confirmar movimentação pendente*. Para passar para esse estado, inicie a movimentação para o pool.

#### <a name="initiate-move---elastic-pool"></a>Iniciar movimentação – pool elástico

1. Em **Entre regiões**, selecione o pool elástico de origem (demo-test1-elasticpool no nosso passo a passo) e clique em **Iniciar movimentação**.
2. Em **Mover recursos**, clique em **Iniciar movimentação**.

    
    ![Botão para iniciar a movimentação do pool elástico](./media/tutorial-move-region-sql/initiate-elastic.png)

1. Monitore o progresso da movimentação na barra de notificações.
1. Quando as notificações mostrarem que a movimentação foi bem-sucedida, clique em **Atualizar**.

> [!NOTE]
> Então, o pool elástico estará no estado de *Confirmar movimentação pendente*.

#### <a name="prepare-database"></a>Preparar banco de dados

1. Em **Entre regiões**, selecione o banco de dados (demo-test2-sqldb no nosso passo a passo) e clique em **Preparar**.
2. Em **Preparar recursos**, clique em **Preparar**.

    ![Botão para preparar o banco de dados no pool elástico](./media/tutorial-move-region-sql/prepare-database-elastic.png) 

Durante a preparação, o banco de dados de destino é criado na região de destino e a replicação de dados é iniciada. Após a preparação, o banco de dados estará no estado de *Iniciar movimentação pendente*. 

![Botão para preparar o banco de dados selecionado no pool elástico](./media/tutorial-move-region-sql/initiate-move-pending.png) 

## <a name="move-databases"></a>Mover bancos de dados

Comece a mover os bancos de dados.
1. Em **Entre regiões**, selecione recursos com o estado de **Iniciar movimentação pendente**. Então, clique em **Iniciar movimentação**.
2. Em **Mover recursos**, clique em **Iniciar movimentação**.

    ![Página para iniciar a movimentação](./media/tutorial-move-region-sql/initiate-move.png)

3. Monitore o progresso da movimentação na barra de notificações.

> [!NOTE]
> Então, o banco de dados estará no estado de *Confirmar movimentação pendente*.


## <a name="discard-or-commit"></a>Descartar ou confirmar?

Após a movimentação inicial, você pode decidir se deseja confirmar a movimentação ou descartá-la. 

- **Descartar**: talvez você queira descartar uma movimentação se estiver apenas testando e não quiser realmente mover o recurso de origem. Descartar a movimentação retorna o recurso para o estado de **Iniciar movimentação pendente**.
- **Confirmar**: a confirmação conclui a movimentação para a região de destino. Após a confirmação, o recurso de origem estará no estado de **Excluir origem pendente** e você poderá decidir se deseja excluí-lo.


## <a name="discard-the-move"></a>Descartar a movimentação 

Você pode descartar a movimentação da seguinte maneira:

1. Em **Entre regiões**, selecione recursos com o estado de **Confirmar movimentação pendente** e clique em **Descartar movimentação**.
2. Em **Descartar movimentação**, clique em **Descartar**.
3. Monitore o progresso da movimentação na barra de notificações.


> [!NOTE]
> - Após descartar os recursos, eles estarão no estado de *Iniciar movimentação pendente*.
> - Se houver apenas um pool elástico, o descarte continuará e o pool elástico criado na região de destino será excluído.
> - Se houver um pool elástico com bancos de dados associados no estado de *Confirmar movimentação pendente*, você não poderá descartar o pool elástico.
> - Se você descartar um banco de dados SQL, os recursos de região de destino não serão excluídos. 

Se você quiser iniciar a movimentação novamente após o descarte, selecione o banco de dados SQL ou o pool elástico e inicie a movimentação outra vez.


## <a name="commit-the-move"></a>Confirmar a movimentação

Concluindo a movimentação de bancos de dados e de pools elásticos da seguinte maneira:


1. Verifique se o SQL Server está no estado de *Excluir origem pendente*.
2. Atualize as cadeias de conexão de banco de dados para a região de destino antes de confirmar.
3. Em **Entre regiões**, selecione os recursos do SQL e clique em **Confirmar movimentação**.
4. Em **Confirmar recursos**, clique em **Confirmar**.

    ![Confirmar movimentação](./media/tutorial-move-region-sql/commit-sql-resources.png)

5. Monitore o progresso da confirmação na barra de notificações.


> [!NOTE]
> Há algum tempo de inatividade por parte dos bancos de dados SQL durante o processo de confirmação.
> Então, os bancos de dados e os pools elásticos confirmados estarão no estado de *Excluir origem pendente*.
> Após a confirmação, atualize as configurações relacionadas ao banco de dados – inclusive as políticas, os alertas e as regras de firewall – do banco de dados de destino.


## <a name="delete-source-resources-after-commit"></a>Excluir recursos de origem após a confirmação

Após a movimentação, você terá a opção de excluir os recursos da região de origem. 

> [!NOTE]
> Servidores do SQL Server não podem ser excluídos do portal e precisam ser excluídos da página de propriedades do recurso.

1. Em **Entre regiões**, clique no nome de cada recurso de origem que deseja excluir.
2. Selecione **Excluir origem**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Moveu bancos de dados SQL do Azure para outra região do Azure.
> * Moveu pools elásticos do SQL do Azure para outra região.

Agora, vamos tentar mover VMs do Azure para outra região.

> [!div class="nextstepaction"]
> [Mover VMs do Azure](./tutorial-move-region-virtual-machines.md)