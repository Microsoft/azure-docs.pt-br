---
title: Conectar-se ao banco de dados do Azure para MySQL usando o dbForge Studio para MySQL
description: O artigo demonstra como se conectar ao banco de dados do Azure para servidor MySQL por meio do dbForge Studio para MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 5138e542d1cc744a23fa1017dfec1f24b2ec1a5b
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107287"
---
# <a name="connect-to-azure-database-for-mysql-using-dbforge-studio-for-mysql"></a>Conectar-se ao banco de dados do Azure para MySQL usando o dbForge Studio para MySQL

Para se conectar ao banco de dados do Azure para MySQL usando o dbForge Studio para MySQL:

1. No menu banco de dados, selecione Nova conexão.

2. Forneça um nome de host e credenciais de logon.

3. Selecione o botão testar conexão para verificar a configuração.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png" alt-text="Conexão do Azure":::

## <a name="migrate-a-database-using-the-backup-and-restore-functionality"></a>Migrar um banco de dados usando a funcionalidade de backup e restauração

O estúdio permite migrar bancos de dados para o Azure de várias maneiras, a escolha dos quais depende exclusivamente de suas necessidades. Se você precisar mover todo o banco de dados, é melhor usar a funcionalidade de backup e restauração. Neste exemplo, migramos o banco de dados *sakila* que reside no servidor MySQL para o banco de dados do Azure para MySQL. A lógica por trás do processo de migração usando a funcionalidade de backup e restauração do dbForge Studio para MySQL é criar um backup do banco de dados MySQL e, em seguida, restaurá-lo no banco de dados do Azure para MySQL.

### <a name="back-up-the-database"></a>Fazer backup do banco de dados

1. No menu banco de dados, aponte para backup e restauração e, em seguida, selecione banco de dados de backup. O assistente de backup do banco de dados é exibido.

2. Na guia conteúdo de backup do assistente de backup de banco de dados, selecione os objetos de banco de dados que você deseja fazer backup.

3. Na guia Opções, configure o processo de backup para atender às suas necessidades.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png" alt-text="Opções do assistente de backup":::

4. Em seguida, especifique os erros de comportamento de processamento e opções de log.

5. Selecione backup.

### <a name="restore-the-database"></a>Restaurar o banco de dados

1. Conecte-se ao Azure para o banco de dados para MySQL, conforme descrito acima.

2. Clique com o botão direito do mouse no corpo da Gerenciador de Banco de Dados, aponte para backup e restauração e, em seguida, selecione restaurar banco de dados.

3. No assistente de restauração de banco de dados que é aberto, selecione um arquivo com um backup de banco de dados.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png" alt-text="Etapa de restauração":::

4. Selecione Restaurar.

5. Verifique o resultado.

## <a name="migrate-a-database-using-the-copy-databases-functionality"></a>Migrar um banco de dados usando a funcionalidade de cópia de dados

A funcionalidade copiar bancos de dados é semelhante ao backup e à restauração, exceto que, com ele, você não precisa de duas etapas para migrar um banco de dados. E o que é mais, o recurso permite transferir dois ou mais bancos de dados em um só lugar. A funcionalidade copiar bancos de dados só está disponível na edição Enterprise do dbForge Studio para MySQL.
Neste exemplo, migramos o banco de dados *world_x* do MySQL Server para o banco de dados do Azure para MySQL.
Para migrar um banco de dados usando a funcionalidade de cópia de dados:

1. No menu banco de dados, selecione Copiar bancos. 

2. Na guia copiar bancos de dados que é exibida, especifique a conexão de origem e de destino e selecione o (s) banco (es) a serem migrados. Inserimos a conexão do Azure MySQL e selecionamos o banco de dados *world_x* . Selecione a seta verde para iniciar o processo.

3. Verifique o resultado.

Como resultado de nossos esforços de migração de banco de dados, o banco de dados *world_x* foi exibido com êxito no Azure MySQL.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png" alt-text="Resultado de copiar bancos de dados":::

## <a name="migrate-a-database-using-schema-and-data-compare-tools"></a>Migrar um banco de dados usando esquema e ferramentas de comparação de data

o dbForge Studio para MySQL incorpora algumas ferramentas que permitem a migração de bancos de dados MySQL, os esquemas do MySQL cone/Oum o Azure. A escolha da funcionalidade depende de suas necessidades e dos requisitos do seu projeto. Se você precisar mover seletivamente um banco de dados, ou seja, migrar determinadas tabelas do MySQL para o Azure, é melhor usar a funcionalidade de comparação de esquema e dados.
Neste exemplo, migramos o banco de dados *mundial* que reside no servidor MySQL para o banco de dados do Azure para MySQL. A lógica por trás do processo de migração usando o esquema e a funcionalidade de comparação de dados do dbForge Studio para MySQL é criar um banco de dados vazio no Azure Database para MySQL, sincronizá-lo com o banco de dados MySQL necessário primeiro usando a ferramenta de comparação de esquema e, em seguida, usando a ferramenta Data Compare. Dessa forma, os esquemas e os dados do MySQL são movidos com precisão para o Azure.

### <a name="connect-to-azure-database-for-mysql-and-create-an-empty-database"></a>Conectar-se ao banco de dados do Azure para MySQL e criar um banco de dados vazio

Conecte-se a um banco de dados do Azure para MySQL e crie um banco de dados vazio.

### <a name="step-2-schema-synchronization"></a>Etapa 2. Sincronização de esquema

1. No menu comparação, selecione Nova comparação de esquema.
O assistente de Nova comparação de esquemas é exibido.

2. Selecione a origem e o destino e, em seguida, especifique as opções de comparação de esquema. Selecione comparar.

3. Na grade resultados da comparação exibida, selecione objetos para sincronização. Selecione o botão de seta verde para abrir o assistente de sincronização de esquema.

4. Percorra as etapas do assistente para configurar a sincronização. Selecione sincronizar para implantar as alterações.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png" alt-text="Assistente de sincronização de esquema":::

### <a name="data-comparison"></a>Comparação de dados

1. No menu comparação, selecione Nova comparação de dados. O assistente de Nova comparação de dados é exibido.

2. Selecione a origem e o destino e, em seguida, especifique as opções de comparação de dados e altere os mapeamentos, se necessário. Selecione comparar.

3. Na grade resultados da comparação exibida, selecione objetos para sincronização. Selecione o botão de seta verde para abrir o assistente de sincronização de dados.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png" alt-text="Resultado da comp de dados":::

4. Percorra as etapas do assistente para configurar a sincronização. Selecione sincronizar para implantar as alterações.

5. Verifique o resultado.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png" alt-text="Resultado da sincronização de dados":::

## <a name="summary"></a>Resumo

Hoje em dia, mais empresas movem seus bancos de dados para o Azure para MySQL, pois esse serviço de banco de dados é fácil de configurar, gerenciar e dimensionar. Essa migração não precisa ser difícil. o dbForge Studio para MySQL apresenta ferramentas de migração Immaculate que podem facilitar significativamente o processo. O estúdio permite que a transferência de banco de dados seja facilmente configurada, salva, editada, automatizada e agendada.

## <a name="next-steps"></a>Próximas etapas
- [Visão geral do MySQL](overview.md)