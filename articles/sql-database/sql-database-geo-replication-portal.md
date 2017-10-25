---
title: "Portal do Azure: replicação geográfica do Banco de Dados SQL | Microsoft Docs"
description: "Configurar a replicação geográfica para o Banco de Dados SQL do Azure usando o Portal do Azure e inicializar o failover"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d0b29822-714f-4633-a5ab-fb1a09d43ced
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/06/2016
ms.author: carlrab
ms.openlocfilehash: db90fad2fe397f0c8466db6bdc1bd8c8d1cf8f15
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Configurar a replicação geográfica ativa para o Banco de Dados SQL do Azure usando o Portal do Azure e inicializar o failover

Este artigo mostra como configurar a replicação geográfica ativa para o Banco de Dados SQL no [portal do Azure](http://portal.azure.com) e para inicializar o failover.

Para iniciar um failover com o portal do Azure, veja [Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure com o portal do Azure](sql-database-geo-replication-portal.md).

Para configurar a replicação geográfica ativa usando o Portal do Azure, você precisa do seguinte recurso:

* Um Banco de Dados SQL do Azure: o banco de dados primário que você deseja replicar para uma região geográfica diferente.

> [!Note]
A replicação geográfica ativa deve estar entre bancos de dados da mesma assinatura.

## <a name="add-a-secondary-database"></a>Adicionar um banco de dados secundário
As etapas a seguir criam um novo banco de dados secundário em uma parceria de replicação geográfica.  

Para adicionar um banco de dados secundário, você deve ser o proprietário ou coproprietário da assinatura.

O banco de dados secundário tem o mesmo nome do banco de dados primário e, por padrão, tem o mesmo nível de serviço. O banco de dados secundário pode ser um banco de dados individual ou um banco de dados em um pool elástico. Para obter mais informações, consulte [Camadas de serviço](sql-database-service-tiers.md).
Depois que o banco de dados secundário for criado e propagado, os dados começarão a serem replicados desde o banco de dados primário até o novo banco de dados secundário.

> [!NOTE]
> O comando falhará se o banco de dados do parceiro já existir (por exemplo, como resultado do término de uma relação de replicação geográfica anterior).
> 

1. No [portal do Azure](http://portal.azure.com), navegue até o banco de dados que você deseja configurar para a replicação geográfica.
2. Na página de banco de dados SQL, selecione **replicação geográfica** e, em seguida, selecione a região para criar o banco de dados secundário. É possível selecionar qualquer região além da região que hospeda o banco de dados primário, mas recomendamos a [região emparelhada](../best-practices-availability-paired-regions.md).
   
    ![Configurar a replicação geográfica](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Selecione ou configure o servidor e o tipo de preço do banco de dados secundário.
   
    ![Configurar secundário](./media/sql-database-geo-replication-portal/create-secondary.png)
4. Opcionalmente, você pode adicionar um banco de dados secundário a um pool elástico. Para criar o banco de dados secundário em um pool, clique em **pool elástico** e selecione um pool no servidor de destino. Um pool já deve existir no servidor de destino. Esse fluxo de trabalho não cria um pool.
5. Clique em **Criar** para adicionar o secundário.
6. O banco de dados secundário é criado e começa o processo de propagação.
   
    ![Configurar secundário](./media/sql-database-geo-replication-portal/seeding0.png)
7. Quando o processo de propagação for concluído, o banco de dados secundário exibirá seu status.
   
    ![Propagação concluída](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Iniciar um failover

O banco de dados secundário pode ser alternado para se tornar primário.  

1. No [portal do Azure](http://portal.azure.com), navegue até o banco de dados primário na parceria de replicação geográfica.
2. Na folha Banco de dados SQL, selecione **Todas as configurações** > **replicação geográfica**.
3. Na lista **SECUNDÁRIOS**, selecione o banco de dados que deverá se tornar o novo primário e clique em **Failover**.
   
    ![Failover](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. Clique em **Sim** para iniciar o failover.

O comando mudará imediatamente o banco de dados secundário para a função primária. 

Há um breve período durante o qual os bancos de dados não estão disponíveis (na ordem de 0 a 25 segundos) enquanto as funções são alternadas. Se o banco de dados primário tiver vários bancos de dados secundários, o comando reconfigurará automaticamente os outros secundários para se conectarem ao novo primário. A operação inteira deve levar menos de um minuto para ser concluída em circunstâncias normais. 

> [!NOTE]
> Este comando destina-se à recuperação rápida do banco de dados em caso de interrupção. Ele dispara o failover sem sincronização de dados (failover forçado).  Se o primário estiver online e realizando transações quando o comando for emitido, alguma perda de dados poderá ocorrer. 
> 
> 

## <a name="remove-secondary-database"></a>Remover banco de dados secundário
Essa operação termina permanentemente a replicação para o banco de dados secundário e altera a função do secundário para um banco de dados de leitura/gravação normal. Se a conectividade com o banco de dados secundário for desfeita, o comando terá êxito, mas o secundário só se tornará de leitura/gravação quando a conectividade for restaurada.  

1. No [portal do Azure](http://portal.azure.com), navegue até o banco de dados primário na parceria de replicação geográfica.
2. Na página do Banco de dados SQL, selecione **Replicação geográfica**.
3. Na lista **SECUNDÁRIOS**, selecione o banco de dados que você deseja remover da parceria de replicação geográfica.
4. Clique em **Parar Replicação**.
   
    ![Remover secundário](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Uma janela de confirmação é aberta. Clique em **Sim** para remover o banco de dados da parceria de replicação geográfica. (Defina-o como um banco de dados de leitura/gravação que não faz parte de nenhuma replicação.)

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre a replicação geográfica ativa, confira [Replicação geográfica ativa](sql-database-geo-replication-overview.md).
* Para obter uma visão geral e os cenários de continuidade dos negócios, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).

