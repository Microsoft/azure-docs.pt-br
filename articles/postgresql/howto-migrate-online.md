---
title: Migração de tempo de inatividade mínimo para o banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve como executar uma migração de tempo de inatividade mínimo de um banco de dados PostgreSQL para o banco de dados do Azure para PostgreSQL-um único servidor usando o serviço de migração de banco de dados do Azure.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 27da5f1b731b2cdb0604f91f7f9e78b19ee2908b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489788"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>Migração de tempo de inatividade mínimo para o banco de dados do Azure para PostgreSQL-servidor único
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server-hyperscale.md)]

É possível realizar migrações do PostgreSQL para o Banco de Dados do Azure para PostgreSQL com tempo de inatividade mínimo usando o **recurso de sincronização contínua** introduzido recentemente para [DMS](https://aka.ms/get-dms) (Serviço de Migração de Banco de Dados do Azure). Essa funcionalidade limita o tempo de inatividade incorrido pelo aplicativo.

## <a name="overview"></a>Visão geral
O DMS do Azure executa uma carga inicial do local no Banco de Dados do Azure para PostgreSQL e, em seguida, sincroniza continuamente quaisquer novas transações no Azure enquanto o aplicativo permanece em execução. Depois que os dados são atualizados no lado do Azure de destino, você interrompe o aplicativo por um breve momento (tempo de inatividade mínimo), aguarde o último lote de dados (desde o momento em que você para o aplicativo até que o aplicativo esteja efetivamente indisponível para receber qualquer novo tráfego) para atualizar no destino e, em seguida, atualize a cadeia de conexão para apontar para o Azure. Quando você terminar, o aplicativo estará ativo no Azure!

:::image type="content" source="./media/howto-migrate-online/ContinuousSync.png" alt-text="Sincronização contínua com o Serviço de Migração de Banco de Dados do Azure":::

## <a name="next-steps"></a>Próximas etapas
- Exibir o vídeo [Modernização de aplicativos com o Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), que contém uma demonstração mostrando como migrar aplicativos do PostgreSQL para o Banco de Dados do Azure para PostgreSQL.
- Consulte o tutorial [Migrar o PostgreSQL para o Banco de Dados do Azure para PostgreSQL on-line usando o DMS](../dms/tutorial-postgresql-azure-postgresql-online.md).