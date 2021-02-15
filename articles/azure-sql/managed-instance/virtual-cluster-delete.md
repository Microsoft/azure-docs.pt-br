---
title: Excluir uma sub-rede depois de excluir um SQL Instância Gerenciada
description: Saiba como excluir uma rede virtual do Azure depois de excluir um Instância Gerenciada de SQL do Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: douglas, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 496ff6c7ec39706a99bb40447b6443ca71f19e5e
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99093665"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-managed-instance"></a>Excluir uma sub-rede depois de excluir um Azure SQL Instância Gerenciada
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo fornece diretrizes sobre como excluir uma sub-rede manualmente depois de excluir a última Instância Gerenciada do Azure SQL que reside nela.

As instâncias gerenciadas do SQL são implantadas em [clusters virtuais](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture). Cada cluster virtual é associado a uma sub-rede. O cluster virtual persiste por design por 12 horas após a última exclusão da instância para permitir que você crie com mais rapidez instâncias gerenciadas do SQL na mesma sub-rede. Não há nenhum encargo para manter um cluster virtual vazio. Durante esse período, a sub-rede associada ao cluster virtual não pode ser excluída.

Se você não quiser aguardar 12 horas e preferir excluir o cluster virtual e sua sub-rede mais cedo, poderá fazer isso manualmente. Exclua o cluster virtual manualmente usando a portal do Azure ou a API de clusters virtuais.

> [!IMPORTANT]
> - O cluster virtual não deve conter nenhuma instância gerenciada do SQL para que a exclusão seja bem-sucedida. 
> - A exclusão de um cluster virtual é uma operação de execução longa duradoura por cerca de 1,5 horas (consulte [operações de gerenciamento de instância gerenciada do SQL](./sql-managed-instance-paas-overview.md#management-operations) para tempo de exclusão de cluster virtual atualizado). O cluster virtual ainda estará visível no portal até que esse processo seja concluído.

## <a name="delete-a-virtual-cluster-from-the-azure-portal"></a>Excluir um cluster virtual do portal do Azure

Para excluir um cluster virtual usando o portal do Azure, procure os recursos do cluster virtual.

![Captura de tela da portal do Azure, com a caixa de pesquisa realçada](./media/virtual-cluster-delete/virtual-clusters-search.png)

Depois de localizar o cluster virtual que você deseja excluir, selecione esse recurso e selecione **excluir**. Você será solicitado a confirmar a exclusão do cluster virtual.

![Captura de tela do painel portal do Azure clusters virtuais, com a opção Excluir realçada](./media/virtual-cluster-delete/virtual-clusters-delete.png)

Portal do Azure notificações mostrarão uma confirmação de que a solicitação para excluir o cluster virtual foi enviada com êxito. A própria operação de exclusão durará por cerca de 1,5 horas, durante as quais o cluster virtual ainda estará visível no Portal. Quando o processo for concluído, o cluster virtual não ficará mais visível e a sub-rede associada a ele será liberada para reutilização.

> [!TIP]
> Se não houver nenhuma instância gerenciada do SQL mostrada no cluster virtual e você não puder excluir o cluster virtual, certifique-se de que você não tenha uma implantação de instância em andamento. Isso inclui implantações iniciadas e canceladas que ainda estão em andamento. Isso ocorre porque essas operações ainda usarão o cluster virtual, bloqueando-o da exclusão. Revisando a guia **implantações** do grupo de recursos em que a instância foi implantada indicará todas as implantações em andamento. Nesse caso, aguarde a conclusão da implantação, exclua o SQL Instância Gerenciada e, em seguida, exclua o cluster virtual.

## <a name="delete-a-virtual-cluster-by-using-the-api"></a>Excluir um cluster virtual usando a API

Para excluir um cluster virtual por meio da API, use os parâmetros de URI especificados no [método de exclusão de clusters virtuais](/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, consulte [o que é o Azure SQL instância gerenciada?](sql-managed-instance-paas-overview.md).
- Saiba mais sobre a [arquitetura de conectividade no SQL instância gerenciada](connectivity-architecture-overview.md).
- Saiba como [modificar uma rede virtual existente para o SQL instância gerenciada](vnet-existing-add-subnet.md).
- Para obter um tutorial que mostra como criar uma rede virtual, criar um Instância Gerenciada SQL do Azure e restaurar um banco de dados de um backup de banco de dados, consulte [criar um instância gerenciada SQL do Azure (Portal)](instance-create-quickstart.md).
- Para problemas de DNS, consulte [configurar um DNS personalizado](custom-dns-configure.md).