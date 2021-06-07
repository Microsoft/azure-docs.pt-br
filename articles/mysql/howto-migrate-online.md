---
title: Migração de tempo de inatividade mínimo-banco de dados do Azure para MySQL
description: Este artigo descreve como realizar uma migração de tempo de inatividade mínimo de um banco de dados MySQL para o Banco de Dados do Azure para MySQL usando o Serviço de Migração de Banco de Dados do Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 75b548b53de4c0f4ea39959c3d70e9ddb361d64c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94540324"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migração com tempo de inatividade mínimo para o Banco de Dados do Azure para MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

É possível realizar migrações do MySQL para o Banco de Dados do Azure para MySQL com tempo de inatividade mínimo usando o **recurso de sincronização contínua** introduzido recentemente para [DMS](https://aka.ms/get-dms) (Serviço de Migração de Banco de Dados do Azure). Essa funcionalidade limita o tempo de inatividade incorrido pelo aplicativo.

Você também pode consultar o [Guia de migração de banco de dados](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide) para obter informações detalhadas e casos de uso sobre como migrar bancos de dados para o banco de dados do Azure para MySQL. Este guia fornece diretrizes que levarão o planejamento e a execução bem-sucedidos de uma migração do MySQL para o Azure.

## <a name="overview"></a>Visão geral
O DMS do Azure executa uma carga inicial do local para o Banco de Dados do Azure para MySQL e, em seguida, sincroniza continuamente novas transações no Azure enquanto o aplicativo permanece em execução. Depois que os dados são atualizados no lado do Azure de destino, você interrompe o aplicativo por um breve momento (tempo de inatividade mínimo), aguarde o último lote de dados (desde o momento em que você para o aplicativo até que o aplicativo esteja efetivamente indisponível para receber qualquer novo tráfego) para atualizar no destino e, em seguida, atualize a cadeia de conexão para apontar para o Azure. Quando você terminar, o aplicativo estará ativo no Azure!

:::image type="content" source="./media/howto-migrate-online/ContinuousSync.png" alt-text="Sincronização contínua com o Serviço de Migração de Banco de Dados do Azure":::

## <a name="next-steps"></a>Próximas etapas
- Para obter mais informações de como migrar bancos de dados para o Banco de Dados do Azure para MySQL, confira o [Guia de Migração de Banco de Dados](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
- Exibir o vídeo [Migrar facilmente aplicativos MySQL/PostgreSQL para o serviço gerenciado do Azure](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), que contém uma demonstração mostrando como migrar aplicativos MySQL para o Banco de Dados do Azure para MySQL.
- Confira o tutorial [Migrar o MySQL para o Banco de Dados do Azure para MySQL online usando o DMS](../dms/tutorial-mysql-azure-mysql-online.md).