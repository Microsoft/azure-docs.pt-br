---
title: Acompanhar alterações de dados no Azure SQL Edge
description: Saiba mais sobre o controle de alterações e a captura de dados de alteração no Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: dddaad3e171c757b353deb81ffcb77cfbe706340
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108255"
---
# <a name="track-data-changes-in-azure-sql-edge"></a>Acompanhar alterações de dados no Azure SQL Edge

O Azure SQL Edge dá suporte aos dois recursos SQL Server que controlam alterações em dados em um banco de dados: [controle de alterações](/sql/relational-databases/track-changes/track-data-changes-sql-server#Tracking) e [Change Data Capture](/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture). Esses recursos permitem que os aplicativos determinem as alterações na linguagem de modificação de dados (operações de inserção, atualização e exclusão) que foram feitas em tabelas de usuário em um banco de dados. Você pode habilitar a captura de dados de alterações e o controle de alterações no mesmo banco de dado. Nenhuma consideração especial é necessária.

Um requisito importante para a eficiência de alguns aplicativos é a capacidade de consultar os dados que foram alterados em um banco de dados. Em geral, para determinar alterações de dados, os desenvolvedores de aplicativos devem implementar um método personalizado de controle nos aplicativos usando uma combinação de gatilhos, colunas de carimbo de data/hora e tabelas adicionais. A criação desses aplicativos costumava envolve uma implementação muito trabalhosa, leva a atualizações de esquema e frequentemente acarreta uma sobrecarga de alto desempenho.

No caso de uma solução de IoT, em que você precisa mover dados periodicamente da borda para uma nuvem ou Datacenter, o controle de alterações pode ser muito útil. Os usuários podem consultar com rapidez e eficiência apenas as alterações da última sincronização e carregar essas alterações no destino da nuvem ou do datacenter. Para obter detalhes adicionais, consulte [benefícios do uso da captura de dados de alteração ou controle de alterações](/sql/relational-databases/track-changes/track-data-changes-sql-server#benefits-of-using-change-data-capture-or-change-tracking). 

Esses dois recursos não são os mesmos. Para obter mais informações, consulte [diferenças de recursos entre a captura de dados de alterações e o controle de alterações](/sql/relational-databases/track-changes/track-data-changes-sql-server#feature-differences-between-change-data-capture-and-change-tracking)

## <a name="change-data-capture"></a>captura de dados de alterações

Para entender os detalhes de como esse recurso funciona, consulte [sobre a captura de dados de alterações](/sql/relational-databases/track-changes/about-change-data-capture-sql-server).

Para entender como habilitar ou desabilitar esse recurso, consulte [habilitar e desabilitar a captura de dados de alterações](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server).

Para administrar e monitorar esse recurso, consulte [administrar e monitorar a captura de dados de alterações](/sql/relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server).

Para entender como consultar e trabalhar com os dados alterados, confira [trabalhar com dados de alteração](/sql/relational-databases/track-changes/work-with-change-data-sql-server).

> [!NOTE]
> As funções de captura de dados de alterações que dependem de CLR não têm suporte no Azure SQL Edge.

## <a name="change-tracking"></a>Change tracking

Para entender os detalhes de como esse recurso funciona, consulte [sobre o controle de alterações](/sql/relational-databases/track-changes/about-change-tracking-sql-server).

Para entender como habilitar ou desabilitar esse recurso, consulte [habilitar e desabilitar o controle de alterações](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server).

Para administrar, monitorar e gerenciar esse recurso, consulte [administrar e monitorar o controle de alterações](/sql/relational-databases/track-changes/manage-change-tracking-sql-server).

Para entender como consultar e trabalhar com os dados alterados, confira [trabalhar com dados de alteração](/sql/relational-databases/track-changes/work-with-change-tracking-sql-server).

## <a name="temporal-tables"></a>Tabelas temporais

O Azure SQL Edge também dá suporte ao recurso de tabelas temporais do SQL Server. Esse recurso (também conhecido como *tabelas temporais com controle de versão do sistema*) traz suporte interno para fornecer informações sobre os dados armazenados na tabela a qualquer momento. O recurso não fornece apenas informações sobre os dados que estão corretos no momento atual.

Uma tabela temporal com controle da versão do sistema é um tipo de tabela de usuário criado para manter um histórico completo de alterações de dados e permitir uma análise pontual fácil. Esse tipo de tabela temporal é conhecido como uma tabela temporal com controle de versão do sistema porque o período de validade de cada linha é gerenciado pelo sistema (ou seja, o mecanismo de banco de dados).

Cada tabela temporal tem duas colunas definidas explicitamente, cada uma com um `datetime2` tipo de dados. Essas colunas são chamadas de colunas de *período* . O sistema usa essas colunas de período exclusivamente para registrar o período de validade de cada linha sempre que uma linha é modificada.

Além dessas colunas de período, uma tabela temporal também contém uma referência a outra tabela com um esquema espelhado. O sistema usa essa tabela para armazenar a versão anterior da linha automaticamente sempre que uma linha na tabela temporal é atualizada ou excluída. Essa tabela adicional é conhecida como a tabela de *histórico* , enquanto a tabela principal que armazena as versões de linha atuais (reais) é conhecida como a tabela *atual* ou simplesmente como a tabela temporal. Durante a criação da tabela temporal, os usuários podem especificar uma tabela de histórico existente (deve estar em conformidade com o esquema) ou deixar que o sistema crie a tabela de histórico padrão.

Para obter mais informações, consulte [tabelas temporais](/sql/relational-databases/tables/temporal-tables).

## <a name="next-steps"></a>Próximas etapas

- [Streaming de dados no Azure SQL Edge ](stream-data.md)
- [Aprendizado de máquina e ia com ONNX no Azure SQL Edge ](onnx-overview.md)
- [Configurar a replicação para o Azure SQL Edge](configure-replication.md)
- [Backup e restauração de bancos de dados no Azure SQL Edge](backup-restore.md)
