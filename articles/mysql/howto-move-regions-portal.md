---
title: Mover regiões do Azure-portal do Azure-banco de dados do Azure para MySQL
description: Mova um banco de dados do Azure para o servidor MySQL de uma região do Azure para outra usando uma réplica de leitura e a portal do Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/26/2020
ms.openlocfilehash: 279526d31adf25d1f4cd5a6d9b15519679bd88b9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94540222"
---
# <a name="move-an-azure-database-for-mysql-server-to-another-region-by-using-the-azure-portal"></a>Mover um banco de dados do Azure para o servidor MySQL para outra região usando o portal do Azure

Há vários cenários para mover um servidor existente do banco de dados do Azure para MySQL de uma região para outra. Por exemplo, talvez você queira mover um servidor de produção para outra região como parte de seu planejamento de recuperação de desastre.

Você pode usar uma [réplica de leitura entre regiões](concepts-read-replicas.md#cross-region-replication) do banco de dados do Azure para MySQL para concluir a mudança para outra região. Para fazer isso, primeiro crie uma réplica de leitura na região de destino. Em seguida, interrompa a replicação para o servidor de réplica de leitura para torná-lo um servidor autônomo que aceita tráfego de leitura e gravação. 

> [!NOTE]
> Este artigo se concentra em mover o servidor para uma região diferente. Se você quiser mover o servidor para um grupo de recursos ou assinatura diferente, consulte o artigo [mover](../azure-resource-manager/management/move-resource-group-and-subscription.md) . 

## <a name="prerequisites"></a>Pré-requisitos

- O recurso de réplica de leitura está disponível apenas para bancos de dados do Azure para servidores MySQL nas camadas de preços de uso geral ou de otimização de memória. Verifique se o servidor de origem está em um desses tipos de preço.

- Verifique se o servidor de origem do banco de dados do Azure para MySQL está na região do Azure da qual você deseja mover.

## <a name="prepare-to-move"></a>Preparar para mover

Para criar um servidor de réplica de leitura entre regiões na região de destino usando o portal do Azure, use as seguintes etapas:

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
1. Selecione o servidor de banco de dados do Azure para MySQL existente que você deseja usar como o servidor de origem. Essa ação abre a página **Visão geral** do runbook.
1. Selecione **Replicação** no menu, em **CONFIGURAÇÕES**.
1. Selecione **para adicionar réplica**.
1. Insira um nome para o servidor de réplica.
1. Selecione o local para o servidor de réplica. O local padrão é o mesmo que o do servidor de origem. Verifique se você selecionou o local de destino onde deseja que a réplica seja implantada.
1. Selecione **OK** para confirmar a criação da réplica. Durante a criação da réplica, os dados são copiados do servidor de origem para a réplica. O tempo de criação pode durar vários minutos ou mais, em proporção ao tamanho do servidor de origem.

>[!NOTE]
> Quando você cria uma réplica, ela não herda os pontos de extremidade do serviço de VNet do servidor de origem. Essas regras precisam ser configuradas independentemente da réplica.

## <a name="move"></a>Mover

> [!IMPORTANT]
> O servidor autônomo não pode se tornar uma réplica novamente.
> Antes de interromper a replicação em uma réplica de leitura, verifique se a réplica tem todos os dados de que você precisa.

Parar a replicação no servidor de réplica faz com que ele se torne um servidor autônomo. Para interromper a replicação na réplica do portal do Azure, use as seguintes etapas:

1. Depois que a réplica tiver sido criada, localize e selecione o servidor de origem do banco de dados do Azure para MySQL. 
1. Selecione **Replicação** no menu, em **CONFIGURAÇÕES**.
1. Selecione o servidor de réplica.
1. Selecione **Parar replicação**.
1. Confirme que você deseja interromper a replicação clicando em **OK**.

## <a name="clean-up-source-server"></a>Limpar servidor de origem

Talvez você queira excluir o banco de dados do Azure de origem para o servidor MySQL. Para fazer isso, execute as seguintes etapas:

1. Depois que a réplica tiver sido criada, localize e selecione o servidor de origem do banco de dados do Azure para MySQL.
1. Na janela **visão geral** , selecione **excluir**.
1. Digite o nome do servidor de origem para confirmar que você deseja excluir.
1. Selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você moveu um servidor de banco de dados do Azure para MySQL de uma região para outra usando o portal do Azure e, em seguida, limpou os recursos de origem desnecessários. 

- Saiba mais sobre [ler réplicas](concepts-read-replicas.md)
- Saiba mais sobre como [gerenciar réplicas de leitura no portal do Azure](howto-read-replicas-portal.md)
- Saiba mais sobre as opções de [continuidade dos negócios](concepts-business-continuity.md)