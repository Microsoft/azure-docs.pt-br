---
title: 'Tutorial: Replicação geográfica e failover no portal'
description: Configure a replicação geográfica para um banco de dados usando o portal do Azure e inicialize o failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 02/13/2019
ms.openlocfilehash: 71c73fec4f559b34b097556243617636acd77480
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92673267"
---
# <a name="tutorial-configure-active-geo-replication-and-failover-in-the-azure-portal-azure-sql-database"></a>Tutorial: Configurar a replicação geográfica ativa e o failover no portal do Azure (Banco de Dados SQL do Azure)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo mostra como configurar a [replicação geográfica ativa para o Banco de Dados SQL do Azure](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities) usando o [portal do Azure](https://portal.azure.com) e como inicializar o failover.

Para obter as melhores práticas usando grupos de failover automático, confira [Melhores práticas para o Banco de Dados SQL do Azure](auto-failover-group-overview.md#best-practices-for-sql-database) e [Melhores práticas para a Instância Gerenciada de SQL do Azure](auto-failover-group-overview.md#best-practices-for-sql-managed-instance). 



## <a name="prerequisites"></a>Pré-requisitos

Para configurar a replicação geográfica ativa usando o Portal do Azure, você precisa do seguinte recurso:

* Um banco de dados no Banco de Dados SQL do Azure: O banco de dados primário que você deseja replicar para uma região geográfica diferente.

> [!Note]
> Ao usar o portal do Azure, você só pode criar um banco de dados secundário na mesma assinatura que o primário. Se o banco de dados secundário precisar estar em outra assinatura, use a [API REST de Criação de Banco de Dados](/rest/api/sql/databases/createorupdate) ou a [API do Transact-SQL ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql).

## <a name="add-a-secondary-database"></a>Adicionar um banco de dados secundário

As etapas a seguir criam um novo banco de dados secundário em uma parceria de replicação geográfica.  

Para adicionar um banco de dados secundário, você deve ser o proprietário ou coproprietário da assinatura.

O banco de dados secundário tem o mesmo nome do banco de dados primário e, por padrão, tem a mesma camada de serviço e tamanho da computação. O banco de dados secundário pode ser um banco de dados individual ou um banco de dados em pool. Para obter mais informações, consulte [Modelo de compra baseado em DTU](service-tiers-dtu.md) e [Modelo de compra baseado em vCore](service-tiers-vcore.md).
Depois que o banco de dados secundário for criado e propagado, os dados começarão a serem replicados desde o banco de dados primário até o novo banco de dados secundário.

> [!NOTE]
> O comando falhará se o banco de dados do parceiro já existir (por exemplo, como resultado do término de uma relação de replicação geográfica anterior).

1. No [portal do Azure](https://portal.azure.com), navegue até o banco de dados que você deseja configurar para a replicação geográfica.
2. Na página do Banco de Dados SQL, selecione **replicação geográfica** e escolha a região para criar o banco de dados secundário. É possível selecionar qualquer região além da região que hospeda o banco de dados primário, mas recomendamos a [região emparelhada](../../best-practices-availability-paired-regions.md).

    ![Configurar a replicação geográfica](./media/active-geo-replication-configure-portal/configure-geo-replication.png)
3. Selecione ou configure o servidor e o tipo de preço do banco de dados secundário.

    ![formulário de criação de secundário](./media/active-geo-replication-configure-portal/create-secondary.png)
4. Opcionalmente, você pode adicionar um banco de dados secundário a um pool elástico. Para criar o banco de dados secundário em um pool, clique em **pool elástico** e selecione um pool no servidor de destino. Um pool já deve existir no servidor de destino. Esse fluxo de trabalho não cria um pool.
5. Clique em **Criar** para adicionar o secundário.
6. O banco de dados secundário é criado e começa o processo de propagação.

    ![mapa de secundários](./media/active-geo-replication-configure-portal/seeding0.png)
7. Quando o processo de propagação for concluído, o banco de dados secundário exibirá seu status.

    ![Propagação concluída](./media/active-geo-replication-configure-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Iniciar um failover

O banco de dados secundário pode ser alternado para se tornar primário.  

1. No [portal do Azure](https://portal.azure.com), navegue até o banco de dados primário na parceria de replicação geográfica.
2. Na folha Banco de dados SQL, selecione **Todas as configurações** > **replicação geográfica**.
3. Na lista **SECUNDÁRIOS**, selecione o banco de dados que deverá se tornar o novo primário e clique em **Failover Forçado**.

    ![failover](./media/active-geo-replication-configure-portal/secondaries.png)
4. Clique em **Sim** para iniciar o failover.

O comando mudará imediatamente o banco de dados secundário para a função primária. Normalmente, esse processo deverá ser concluído em 30 segundos ou menos.

Há um breve período durante o qual os bancos de dados não estão disponíveis (na ordem de 0 a 25 segundos) enquanto as funções são alternadas. Se o banco de dados primário tiver vários bancos de dados secundários, o comando reconfigurará automaticamente os outros secundários para se conectarem ao novo primário. A operação inteira deve levar menos de um minuto para ser concluída em circunstâncias normais.

> [!NOTE]
> Este comando destina-se à recuperação rápida do banco de dados em caso de interrupção. Ele dispara o failover sem sincronização de dados (failover forçado).  Se o primário estiver online e realizando transações quando o comando for emitido, alguma perda de dados poderá ocorrer.

## <a name="remove-secondary-database"></a>Remover um banco de dados secundário

Essa operação termina permanentemente a replicação para o banco de dados secundário e altera a função do secundário para um banco de dados de leitura/gravação normal. Se a conectividade com o banco de dados secundário for desfeita, o comando terá êxito, mas o secundário só se tornará de leitura/gravação quando a conectividade for restaurada.  

1. No [portal do Azure](https://portal.azure.com), navegue até o banco de dados primário na parceria de replicação geográfica.
2. Na página do Banco de dados SQL, selecione **Replicação geográfica**.
3. Na lista **SECUNDÁRIOS**, selecione o banco de dados que você deseja remover da parceria de replicação geográfica.
4. Clique em **Parar Replicação**.

    ![Remover secundário](./media/active-geo-replication-configure-portal/remove-secondary.png)
5. Uma janela de confirmação é aberta. Clique em **Sim** para remover o banco de dados da parceria de replicação geográfica. (Defina-o como um banco de dados de leitura/gravação que não faz parte de nenhuma replicação.)

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a replicação geográfica ativa, confira [Replicação geográfica ativa](active-geo-replication-overview.md).
* Para saber mais sobre grupos de failover automático, confira [Grupos de failover automático](auto-failover-group-overview.md)
* Para obter uma visão geral e os cenários de continuidade dos negócios, consulte [Visão geral da continuidade dos negócios](business-continuity-high-availability-disaster-recover-hadr-overview.md).