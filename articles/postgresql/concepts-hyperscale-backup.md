---
title: Backup e restauração – Citus (hiperescala)-banco de dados do Azure para PostgreSQL
description: Protegendo dados contra a corrupção ou exclusão acidental
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 9656e6ab608665e325ac5f772e06df9ac296c521
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92485368"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Backup e restauração no banco de dados do Azure para PostgreSQL-Citus (hiperescala)

Banco de dados do Azure para PostgreSQL – o Citus (hiperescala) cria automaticamente backups de cada nó e os armazena em um armazenamento com redundância local. Os backups podem ser usados para restaurar seu cluster de hiperescala (Citus) para um horário especificado. Os recursos de backup e restauração são uma parte essencial de qualquer estratégia de continuidade dos negócios, pois eles protegem seus dados contra exclusão ou corrupção acidentais.

## <a name="backups"></a>Backups

Pelo menos uma vez por dia, o banco de dados do Azure para PostgreSQL usa backups de instantâneo de arquivos de dados e o log de transações do banco de dado. Os backups permitem que você restaure um servidor para qualquer ponto no tempo dentro do período de retenção. (No momento, o período de retenção é de 35 dias para todos os clusters.) Todos os backups são criptografados usando a criptografia AES de 256 bits.

Em regiões do Azure que dão suporte a zonas de disponibilidade, os instantâneos de backup são armazenados em três zonas de disponibilidade. Desde que pelo menos uma zona de disponibilidade esteja online, o cluster de hiperescala (Citus) é restaurável.

Os arquivos de backup não podem ser exportados. Eles só podem ser usados para operações de restauração no banco de dados do Azure para PostgreSQL.

### <a name="backup-storage-cost"></a>Custo do armazenamento de backup

Para obter o preço atual do armazenamento de backup, consulte a [página de preços](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)do banco de dados do Azure para PostgreSQL (Citus).

## <a name="restore"></a>Restaurar

No banco de dados do Azure para PostgreSQL, a restauração de um cluster de hiperescala (Citus) cria um novo cluster a partir dos backups dos nós originais. 

> [!IMPORTANT]
>Você só pode restaurar o cluster de hiperescala (Citus) dentro da mesma assinatura e grupo de recursos e com um nome de cluster diferente.


> [!IMPORTANT]
> Os clusters de hiperescala (Citus) excluídos não podem ser restaurados. Se você excluir o cluster, todos os nós que pertencem ao cluster serão excluídos e não poderão ser recuperados. Para proteger os recursos de cluster, pós-implantação, de exclusão acidental ou alterações inesperadas, os administradores podem aproveitar os [bloqueios de gerenciamento](../azure-resource-manager/management/lock-resources.md).

### <a name="point-in-time-restore-pitr"></a>Restauração pontual (PITR)

Você pode restaurar um cluster para qualquer ponto no tempo nos últimos 35 dias.
A Restauração pontual é útil em vários cenários. Por exemplo, quando um usuário exclui dados, remove uma tabela ou um banco de dados importante por engano ou se um aplicativo substitui, acidentalmente, dados válidos por dados inválidos.

O processo de restauração cria um novo cluster na mesma região, assinatura e grupo de recursos do Azure que o original. O cluster tem a configuração original: o mesmo número de nós, o número de vCores, o tamanho do armazenamento, as funções de usuário, a versão PostgreSQL e a versão da extensão Citus.

As configurações de firewall e os parâmetros do servidor PostgreSQL não são preservados do grupo de servidores original, eles são redefinidos para valores padrão. O firewall impedirá todas as conexões. Você precisará ajustar manualmente essas configurações após a restauração.

> [!IMPORTANT]
> Você precisará abrir uma solicitação de suporte para executar a restauração pontual do seu cluster de hiperescala (Citus).

### <a name="post-restore-tasks"></a>Tarefas de pós-restauração

Após uma restauração de qualquer mecanismo de recuperação, você deve fazer o seguinte para colocar seus usuários e aplicativos em funcionamento:

* Se o novo servidor é usado para substituir o servidor original, redirecione clientes e aplicativos de cliente para o novo servidor
* Garanta que as regras de rede virtual e de firewall no nível de servidor apropriadas estejam em vigor para que os usuários se conectem. Essas regras não são copiadas do grupo de servidores original.
* Ajuste os parâmetros do servidor PostgreSQL conforme necessário. Os parâmetros não são copiados do grupo de servidores original.
* Verifique se as permissões e os logons adequados no nível do banco de dados estão em vigor
* Configurar os alertas, conforme apropriado

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre as [zonas de disponibilidade do Azure](../availability-zones/az-overview.md).
* Defina [alertas sugeridos](./howto-hyperscale-alert-on-metric.md#suggested-alerts) em grupos de servidores de hiperescala (Citus).