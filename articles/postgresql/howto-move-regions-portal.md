---
title: Mover regiões do Azure-portal do Azure-banco de dados do Azure para PostgreSQL-servidor único
description: Mova um banco de dados do Azure para o servidor PostgreSQL de uma região do Azure para outra usando uma réplica de leitura e a portal do Azure.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/29/2020
ms.openlocfilehash: 204815d36baf9fe6e7fe8c0e194ac7acc14f72e4
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606463"
---
# <a name="move-an-azure-database-for-azure-database-for-postgresql---single-server-to-another-region-by-using-the-azure-portal"></a>Mover um banco de dados do Azure para o banco de dados do Azure para PostgreSQL-servidor único para outra região usando o portal do Azure

Há vários cenários para mover um servidor existente do banco de dados do Azure para PostgreSQL de uma região para outra. Por exemplo, talvez você queira mover um servidor de produção para outra região como parte de seu planejamento de recuperação de desastre.

Você pode usar uma [réplica de leitura entre regiões](concepts-read-replicas.md#cross-region-replication) do banco de dados do Azure para PostgreSQL para concluir a mudança para outra região. Para fazer isso, primeiro crie uma réplica de leitura na região de destino. Em seguida, interrompa a replicação para o servidor de réplica de leitura para torná-lo um servidor autônomo que aceita tráfego de leitura e gravação. 

> [!NOTE]
> Este artigo se concentra em mover o servidor para uma região diferente. Se você quiser mover o servidor para um grupo de recursos ou assinatura diferente, consulte o artigo [mover](../azure-resource-manager/management/move-resource-group-and-subscription.md) . 

## <a name="prerequisites"></a>Pré-requisitos

- O recurso de réplica de leitura entre regiões está disponível somente para o banco de dados do Azure para PostgreSQL-servidor único nos tipos de preço Uso Geral ou com otimização de memória. Verifique se o servidor de origem está em um desses tipos de preço.

- Verifique se o servidor de origem do banco de dados do Azure para PostgreSQL está na região do Azure da qual você deseja mover.

## <a name="prepare-to-move"></a>Preparar para mover

Para preparar o servidor de origem para replicação usando o portal do Azure, use as seguintes etapas: 

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
1. Selecione o servidor do banco de dados do Azure para PostgreSQL existente que você deseja usar como o servidor de origem. Essa ação abre a página **Visão geral** do runbook.
1. No menu do servidor, selecione **replicação**. Se o suporte à replicação do Azure for definido como pelo menos **réplica**, você poderá criar réplicas de leitura. 
1. Se o suporte à replicação do Azure não estiver definido como pelo menos **réplica**, defina-o. Selecione **Salvar**.
1. Reinicie o servidor para aplicar a alteração selecionando **Sim**.
1. Você receberá duas notificações de portal do Azure quando a operação for concluída. Há uma notificação para atualizar o parâmetro do servidor. Há outra notificação para a reinicialização do servidor que segue imediatamente.
1. Atualize a página de portal do Azure para atualizar a barra de ferramentas de replicação. Agora você pode criar réplicas de leitura para este servidor.

Para criar um servidor de réplica de leitura entre regiões na região de destino usando o portal do Azure, use as seguintes etapas:

1. Selecione o servidor do banco de dados do Azure para PostgreSQL existente que você deseja usar como o servidor de origem.
1. Selecione **Replicação** no menu, em **CONFIGURAÇÕES**.
1. Selecione **para adicionar réplica**.
1. Insira um nome para o servidor de réplica.
1. Selecione o local para o servidor de réplica. O local padrão é o mesmo que o do servidor primário. Verifique se você selecionou o local de destino onde deseja que a réplica seja implantada.
1. Selecione **OK** para confirmar a criação da réplica. Durante a criação da réplica, os dados são copiados do servidor de origem para a réplica. O tempo de criação pode durar vários minutos ou mais, em proporção ao tamanho do servidor de origem.

>[!NOTE]
> Quando você cria uma réplica, ela não herda as regras de firewall e os pontos de extremidade de serviço de VNet do servidor primário. Essas regras precisam ser configuradas independentemente da réplica.

## <a name="move"></a>Mover

> [!IMPORTANT]
> O servidor autônomo não pode se tornar uma réplica novamente.
> Antes de interromper a replicação em uma réplica de leitura, verifique se a réplica tem todos os dados de que você precisa.

Para interromper a replicação na réplica do portal do Azure, use as seguintes etapas:

1. Depois que a réplica tiver sido criada, localize e selecione o servidor de origem do banco de dados do Azure para PostgreSQL. 
1. Selecione **Replicação** no menu, em **CONFIGURAÇÕES**.
1. Selecione o servidor de réplica.
1. Selecione **Parar replicação**.
1. Confirme que você deseja interromper a replicação clicando em **OK**.

## <a name="clean-up-source-server"></a>Limpar servidor de origem

Talvez você queira excluir o servidor do banco de dados do Azure de origem para PostgreSQL. Para fazer isso, execute as seguintes etapas:

1. Depois que a réplica tiver sido criada, localize e selecione o servidor de origem do banco de dados do Azure para PostgreSQL.
1. Na janela **visão geral** , selecione **excluir**.
1. Digite o nome do servidor de origem para confirmar que você deseja excluir.
1. Selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você moveu um servidor de banco de dados do Azure para PostgreSQL de uma região para outra usando o portal do Azure e, em seguida, limpou os recursos de origem desnecessários. 

- Saiba mais sobre [ler réplicas](concepts-read-replicas.md)
- Saiba mais sobre como [gerenciar réplicas de leitura no portal do Azure](howto-read-replicas-portal.md)
- Saiba mais sobre as opções de [continuidade dos negócios](concepts-business-continuity.md)