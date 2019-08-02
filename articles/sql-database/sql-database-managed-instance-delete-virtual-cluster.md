---
title: Excluir uma sub-rede depois de excluir uma instância gerenciada do banco de dados SQL do Azure | Microsoft Docs
description: Saiba como excluir uma rede virtual do Azure depois de excluir uma instância gerenciada do banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
manager: craigg
ms.date: 06/26/2019
ms.openlocfilehash: ead7ea91e172f608c5364e4d5164d2a71dbf2f5f
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297633"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Excluir uma sub-rede depois de excluir uma instância gerenciada do banco de dados SQL do Azure

Este artigo fornece diretrizes sobre como excluir uma sub-rede manualmente depois de excluir a última instância gerenciada do banco de dados SQL do Azure que a reside.

O banco de dados SQL usa um [cluster virtual](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) para conter a instância gerenciada excluída. O cluster virtual persiste por 12 horas após a exclusão da instância, para permitir que você crie rapidamente instâncias gerenciadas na mesma sub-rede. Não há nenhum encargo para manter um cluster virtual vazio. Durante esse período, a sub-rede associada ao cluster virtual não pode ser excluída.

Se você não quiser aguardar 12 horas e preferir excluir o cluster virtual e sua sub-rede imediatamente, poderá fazer isso manualmente. Exclua o cluster virtual manualmente usando a portal do Azure ou a API de clusters virtuais.

> [!NOTE]
> O cluster virtual não deve conter nenhuma instância gerenciada para que a exclusão seja bem-sucedida.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Excluir o cluster virtual do portal do Azure

Para excluir um cluster virtual usando o portal do Azure, procure os recursos do cluster virtual.

![Captura de tela da portal do Azure, com a caixa de pesquisa realçada](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Depois de localizar o cluster virtual que você deseja excluir, selecione esse recurso e selecione **excluir**. Você será solicitado a confirmar a exclusão do cluster virtual.

![Captura de tela do painel portal do Azure clusters virtuais, com a opção Excluir realçada](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

A área notificações de portal do Azure mostra a confirmação de que o cluster virtual foi excluído. A exclusão bem-sucedida do cluster virtual libera a sub-rede imediatamente para reutilização.

> [!TIP]
> Se não houver nenhuma instância gerenciada mostrada no cluster virtual e você não puder excluir o cluster virtual, certifique-se de que você não tenha uma implantação de instância em andamento. Isso inclui implantações iniciadas e canceladas que ainda estão em andamento. Revisando a guia implantações do grupo de recursos em que a instância foi implantada indicará todas as implantações em andamento. Nesse caso, aguardar a conclusão da implantação, excluir instância gerenciada e, em seguida, o cluster virtual.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Excluir o cluster virtual usando a API

Para excluir um cluster virtual por meio da API, use os parâmetros de URI especificados no [método de exclusão de clusters virtuais](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, confira [O que é uma Instância Gerenciada?](sql-database-managed-instance.md).
- Saiba mais sobre [arquitetura de conectividade na Instância Gerenciada](sql-database-managed-instance-connectivity-architecture.md).
- Saiba como [modificar uma rede virtual existente para a Instância Gerenciada](sql-database-managed-instance-configure-vnet-subnet.md).
- Para um tutorial que mostra como criar uma rede virtual, criar uma Instância Gerenciada e restaurar um banco de dados com base em um backup de banco de dados, confira [Criar uma Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-get-started.md).
- Para problemas de DNS, confira [Configurar um DNS personalizado](sql-database-managed-instance-custom-dns.md).
