---
title: Solucionar problemas da Sincronização de Dados SQL
description: Saiba como identificar, solucionar problemas e resolver problemas comuns com o Sincronização de Dados SQL no Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync, sqldbrb=1
ms.devlang: ''
ms.topic: troubleshooting
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/20/2018
ms.openlocfilehash: 02eaec4c86c934e8d2638de1b60aa9267babf7a8
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790161"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>Solucionar problemas com a Sincronização de Dados SQL do Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo descreve como solucionar problemas conhecidos com o Sincronização de Dados SQL no Azure. Caso haja uma resolução para um problema, ela será fornecida aqui.

Para obter uma visão geral da Sincronização de Dados SQL, consulte [Sincronizar dados entre vários bancos de dados locais e de nuvem com a Sincronização de Dados SQL no Azure](sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> No momento, a Sincronização de Dados SQL **não** dá suporte à Instância Gerenciada de SQL do Azure.

## <a name="sync-issues"></a>Problemas de sincronização

- [A sincronização falha na interface do usuário do portal para bancos de dados locais associados ao agente cliente](#sync-fails)

- [Meu grupo de sincronização está paralisado no estado de processamento](#sync-stuck)

- [Há dados errados em minhas tabelas](#sync-baddata)

- [Há dados de chave primária inconsistentes após uma sincronização com êxito](#sync-pkdata)

- [Há uma degradação significativa no desempenho](#sync-perf)

- [Vejo esta mensagem: "não é possível inserir o valor NULL na coluna \<column> . A coluna não permite valores nulos. " O que isso significa e como posso corrigi-lo?](#sync-nulls)

- [Como a Sincronização de Dados trata referências circulares? Ou seja, quando os mesmos dados são sincronizados em vários grupos de sincronização e continuam sendo alterados como resultado?](#sync-circ)

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a><a name="sync-fails"></a> Falha de sincronização na interface do usuário do portal em bancos de dados locais associados ao agente cliente

A sincronização falha na interface do usuário do portal da Sincronização de Dados SQL para bancos de dados locais associados ao agente cliente. No computador local que executa o agente, são exibidos erros System.IO.IOException no Log de Eventos. Os erros informam que o disco não tem espaço suficiente.

- **Causa** . A unidade não tem espaço suficiente.

- **Resolução** . Crie mais espaço na unidade na qual o diretório %TEMP% está localizado.

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a><a name="sync-stuck"></a> Meu grupo de sincronização paralisou no estado de processamento

Um grupo de sincronização na Sincronização de Dados SQL esteve no estado de processamento por muito tempo. Ele não responde ao comando de **parada** e os logs não mostram novas entradas.

Qualquer uma das condições a seguir pode fazer com que um grupo de sincronização trave no estado de processamento:

- **Causa** . O agente cliente está offline

- **Resolução** . Verifique se o agente cliente está online e tente novamente.

- **Causa** . O agente cliente está desinstalado ou ausente.

- **Resolução** . Se o agente cliente estiver desinstalado ou de outra maneira ausente:

    1. Remova o arquivo XML do agente da pasta de instalação da Sincronização de Dados SQL caso o arquivo exista.
    1. Instale o agente em um computador local (pode ser o mesmo ou em outro computador). Em seguida, envie a chave do agente que é gerada no portal para o agente que é mostrado como offline.

- **Causa** . O serviço Sincronização de Dados SQL está interrompido.

- **Resolução** . Reinicie o serviço de Sincronização de Dados SQL.

    1. No menu **Iniciar** , pesquise por **Serviços** .
    1. Nos resultados da pesquisa, selecione **Serviços** .
    1. Localize o serviço de **Sincronização de Dados SQL** .
    1. Se o status do serviço for **Parado** , clique com o botão direito do mouse no nome do serviço e selecione **Iniciar** .

> [!NOTE]
> Se as informações anteriores não retirarem seu grupo de sincronização do estado de processamento, o Suporte da Microsoft poderá redefinir o status de seu grupo de sincronização. Para que o status do grupo de sincronização seja redefinido, na [Página de perguntas de P e R da Microsoft para o Banco de Dados SQL do Azure](/answers/topics/azure-sql-database.html), crie uma postagem. Na postagem, inclua sua ID de assinatura e a ID do grupo de sincronização para o grupo que precisa ser redefinido. Um engenheiro do Suporte da Microsoft responderá à sua postagem e o informará quando o status tiver sido redefinido.

### <a name="i-see-erroneous-data-in-my-tables"></a><a name="sync-baddata"></a> Há dados errados em minhas tabelas

Se as tabelas que têm o mesmo nome, mas que são de esquemas de banco de dados diferente forem incluídas em uma sincronização, você verá dados incorretos nas tabelas após a sincronização.

- **Causa** . O processo de provisionamento da Sincronização de Dados SQL usa as mesmas tabelas de acompanhamento para tabelas com o mesmo nome, mas que estão em esquemas diferentes. Por isso, as alterações de ambas as tabelas são refletidas na mesma tabela de rastreamento. Isso causa alterações de dados incorretas durante a sincronização.

- **Resolução** . Garanta que os nomes das tabelas envolvidas na sincronização sejam diferentes, mesmo que pertençam a esquemas diferentes em um banco de dados.

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a><a name="sync-pkdata"></a> Há dados de chave primária inconsistentes após uma sincronização com êxito

Uma sincronização é relatada como bem-sucedida e o log não mostra nenhuma linha com falha ou ignorada, mas você observa que os dados da chave primária são inconsistentes entre os bancos de dados no grupo de sincronização.

- **Causa** . Esse resultado ocorre por design. As alterações em qualquer coluna de chave primária resultam em dados inconsistentes nas linhas nas quais a chave primária foi alterada.

- **Resolução** . Para evitar esse problema, garanta que nenhum dado em uma coluna de chave primária é alterado. Para corrigir esse problema depois que ele tiver ocorrido, exclua a linha que contém dados inconsistentes de todos os pontos de extremidade no grupo de sincronização. Em seguida, reinsira a linha.

### <a name="i-see-a-significant-degradation-in-performance"></a><a name="sync-perf"></a> Há uma degradação significativa no desempenho

O desempenho é degradado de forma significativa, possivelmente até o ponto em que você não pode nem mesmo abrir a interface do usuário da Sincronização de Dados.

- **Causa** . A causa mais provável é um loop de sincronização. Um loop de sincronização ocorre quando uma sincronização por grupo de sincronização A aciona uma sincronização por grupo de sincronização B, que dispara uma sincronização por grupo de sincronização A. A situação real pode ser mais complexa e pode envolver mais de dois grupos de sincronização no loop. O problema é que há um acionamento circular de sincronização que é causado por grupos de sincronização sobrepostos entre si.

- **Resolução** . A melhor correção é a prevenção. Verifique se você não tem referências circulares nos grupos de sincronização. Nenhuma linha sincronizada por um grupo de sincronização pode ser sincronizada por outro grupo de sincronização.

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a><a name="sync-nulls"></a> Vejo esta mensagem: "não é possível inserir o valor NULL na coluna \<column> . A coluna não permite valores nulos”. O que isso significa e como posso corrigir o erro? 
Essa mensagem de erro indica que um dos dois problemas a seguir ocorreu:
-  Uma tabela não tem uma chave primária. Para corrigir esse problema, adicione uma chave primária a todas as tabelas que você está sincronizando.
-  Há uma cláusula WHERE na instrução CREATE INDEX. A Sincronização de Dados não lidar com essa condição. Para corrigir esse problema, remova a cláusula WHERE ou realize as alterações manualmente para todos os bancos de dados. 
 
### <a name="how-does-data-sync-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a><a name="sync-circ"></a> Como a Sincronização de Dados trata referências circulares? Ou seja, quando os mesmos dados são sincronizados em vários grupos de sincronização, fazendo com que sejam continuamente alterados?
A Sincronização de Dados não trata referências circulares. Evite usá-las. 

## <a name="client-agent-issues"></a>Problemas de agente cliente

Para solucionar problemas com o agente cliente, veja [Solucionar problemas do Agente de Sincronização de Dados](sql-data-sync-agent-overview.md#agent-tshoot).

## <a name="setup-and-maintenance-issues"></a>Problemas de instalação e manutenção

- [Recebi uma mensagem que indica "disco sem espaço"](#setup-space)

- [Não consigo excluir meu grupo de sincronização](#setup-delete)

- [Não consigo cancelar o registro de um banco de dados SQL Server](#setup-unreg)

- [Não tenho privilégios suficientes para iniciar serviços do sistema](#setup-perms)

- [Um banco de dados tem o status "Desatualizado"](#setup-date)

- [Um grupo de sincronização tem o status "Desatualizado"](#setup-date2)

- [Um grupo de sincronização não pode ser excluído em até três minutos após a desinstalação ou interrupção do agente](#setup-delete2)

- [O que acontece quando eu restaurar um banco de dados perdido ou corrompido?](#setup-restore)

### <a name="i-get-a-disk-out-of-space-message"></a><a name="setup-space"></a> Recebi uma mensagem que indica "disco sem espaço"

- **Causa** . A mensagem "disco sem espaço" poderá aparecer se arquivos restantes precisarem ser excluídos. Isso pode ser causado pelo software antivírus ou porque arquivos estavam abertos quando de operações de exclusão estavam sendo realizadas.

- **Resolução** . Exclua manualmente os arquivos de sincronização que estão na pasta %temp% (`del \*sync\* /s`). Em seguida, exclua os subdiretórios na pasta %temp%.

> [!IMPORTANT]
> Não exclua nenhum arquivo enquanto a sincronização estiver em andamento.

### <a name="i-cant-delete-my-sync-group"></a><a name="setup-delete"></a> Não consigo excluir meu grupo de sincronização

Falha ao tentar excluir um grupo de sincronização. Qualquer um dos cenários a seguir pode resultar em uma falha ao excluir um grupo de sincronização:

- **Causa** . O agente cliente está offline.

- **Resolução** . Verifique se o agente cliente está online e tente novamente.

- **Causa** . O agente cliente está desinstalado ou ausente.

- **Resolução** . Se o agente cliente estiver desinstalado ou de outra maneira ausente:  
    a. Remova o arquivo XML do agente da pasta de instalação da Sincronização de Dados SQL caso o arquivo exista.  
    b. Instale o agente em um computador local (pode ser o mesmo ou em outro computador). Em seguida, envie a chave do agente que é gerada no portal para o agente que é mostrado como offline.

- **Causa** . Um banco de dados está offline.

- **Resolução** . Verifique se os bancos de dados estão todos online.

- **Causa** . O grupo de sincronização está sendo provisionado ou sincronizado.

- **Resolução** . Aguarde até que o processo de provisionamento ou sincronização seja concluído e tente excluir o grupo de sincronização novamente.

### <a name="i-cant-unregister-a-sql-server-database"></a><a name="setup-unreg"></a> Não consigo cancelar o registro de um banco de dados SQL Server

- **Causa** . Muito provavelmente, você está tentando cancelar o registro de um banco de dados que já foi excluído.

- **Resolução** . Para cancelar o registro de um banco de dados SQL Server, selecione o banco de dados e, em seguida, selecione **forçar exclusão** .

  Se essa operação não conseguir remover o banco de dados do grupo de sincronização:

  1. Pare e, em seguida, reinicie o serviço de host do agente cliente:  
    a. Selecione o menu **Iniciar** .  
    b. Insira **services.msc** na caixa de pesquisa.  
    c. Na seção **Programas** do painel de resultados da pesquisa, clique duas vezes em **Serviços** .  
    d. Clique com o botão direito do mouse no serviço **Sincronização de Dados SQL** .  
    e. Se o serviço estiver em execução, interrompa-o.  
    f. Clique com o botão direito do mouse no serviço e, em seguida, selecione **Iniciar** .  
    g. Verifique se o banco de dados ainda está registrado. Se ele não estiver mais registrado, você terminou. Caso contrário, continue com a próxima etapa.
  1. Abra o aplicativo do agente cliente (SqlAzureDataSyncAgent).
  1. Selecione **Editar Credenciais** e, em seguida, insira as credenciais para o banco de dados.
  1. Continue com o cancelamento do registro.

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a><a name="setup-perms"></a> Não tenho privilégios suficientes para iniciar serviços do sistema

- **Causa** . Esse erro ocorre em duas situações:
  -   O nome de usuário e/ou a senha estão incorretos.
  -   A conta de usuário especificada não tem privilégios suficientes para fazer logon como serviço.

- **Resolução** . Conceda credenciais de logon como serviço à conta de usuário:

  1. Vá para **iniciar** > **Painel de Controle** > **Ferramentas Administrativas** > **Política de Segurança Local** > **Diretiva Local** > **Gerenciamento de Direitos do Usuário** .
  1. Selecione **Fazer logon como um serviço** .
  1. Na caixa de diálogo **Propriedades** , adicione a conta de usuário.
  1. Selecione **Aplicar** e, depois, **OK** .
  1. Feche todas as janelas.

### <a name="a-database-has-an-out-of-date-status"></a><a name="setup-date"></a> Um banco de dados tem o status "Desatualizado"

- **Causa** . A Sincronização de Dados SQL remove banco de dados que ficaram offline do serviço por 45 dias ou mais (contado a partir da hora em que o banco de dados ficou offline). Se um banco de dados estiver offline por 45 dias ou mais e, em seguida, voltar a ficar online, seu status será definido como **Desatualizado** .

- **Resolução** . Evite obter o status **Desatualizado** garantindo que nenhum dos bancos de dados fique offline por 45 dias ou mais.

  Se o status de um banco de dados for **Desatualizado** :

  1. Remova o banco de dados que tem um status de **Desatualizadas** do grupo de sincronização.
  1. Adicione o banco de dados de volta ao grupo de sincronização.

  > [!WARNING]
  > Você perderá todas as alterações feitas nesse banco de dados enquanto ele estava offline.

### <a name="a-sync-group-has-an-out-of-date-status"></a><a name="setup-date2"></a> Um grupo de sincronização tem o status "Desatualizado"

- **Causa** . Se uma ou mais alterações não puderem ser aplicadas ao período de retenção inteiro de 45 dias, um grupo de sincronização poderá ficar desatualizado.

- **Resolução** . Para evitar obter um status **Desatualizado** , examine os resultados dos trabalhos de sincronização no visualizador de histórico regularmente. Investigue e resolva todas as alterações que não puderem ser aplicadas.

  Se o status de um grupo de sincronização for **Desatualizado** , exclua o grupo de sincronização e recrie-o.

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a><a name="setup-delete2"></a> Um grupo de sincronização não pode ser excluído em até três minutos após a desinstalação ou interrupção do agente

Não é possível excluir um grupo de sincronização em até três minutos após a desinstalação ou interrupção do agente cliente da Sincronização de Dados SQL associado.

- **Resolução** .

  1. Remova um grupo de sincronização enquanto os agentes de sincronização associados estiverem online (recomendado).
  1. Se o agente estiver offline, mas estiver instalado, coloque-o online no computador local. Aguarde até que o status do agente seja exibido como **Online** no portal da Sincronização de Dados SQL. Em seguida, remova o grupo de sincronização.
  1. Se o agente estiver offline porque foi desinstalado:  
    a.  Remova o arquivo XML do agente da pasta de instalação da Sincronização de Dados SQL caso o arquivo exista.  
    b.  Instale o agente em um computador local (pode ser o mesmo ou em outro computador). Em seguida, envie a chave do agente que é gerada no portal para o agente que é mostrado como offline.  
    c. Tente excluir o grupo de sincronização.

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a><a name="setup-restore"></a> O que acontece quando eu restaurar um banco de dados perdido ou corrompido?

Se você restaurar um banco de dados perdido ou corrompido de um backup, poderá haver uma não convergência de dados nos grupos de sincronização aos quais o banco de dados pertence.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre a Sincronização de Dados SQL, consulte:

-   Visão geral – [Sincronize dados em vários bancos de dados locais e na nuvem com a Sincronização de Dados SQL no Azure](sql-data-sync-data-sql-server-sql-database.md)
-   Configurar sincronização de dados
    - No portal- [tutorial: configurar o sincronização de dados SQL para sincronizar dados entre o Azure SQL Database e SQL Server](sql-data-sync-sql-server-configure.md)
    - Com o PowerShell
        -  [Usar o PowerShell para sincronizar entre vários bancos de dados no banco de dados SQL do Azure](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [Usar o PowerShell para sincronizar entre um banco de dados no banco de dados SQL do Azure e um banco de dados em uma instância do SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Data Sync Agent – [Data Sync Agent para Sincronização de Dados SQL no Azure](sql-data-sync-agent-overview.md)
-   Melhores práticas – [Melhores práticas para a Sincronização de Dados SQL no Azure](sql-data-sync-best-practices.md)
-   Monitor – [monitore a Sincronização de Dados SQL com logs do Azure Monitor](./monitor-tune-overview.md)
-   Atualizar o esquema de sincronização
    -   Com o Transact-SQL – [automatizar a replicação de alterações de esquema no sincronização de dados SQL no Azure](sql-data-sync-update-sync-schema.md)
    -   Com o PowerShell - [usar o PowerShell para atualizar o esquema de sincronização em um grupo de sincronização existente](scripts/update-sync-schema-in-sync-group.md)

Para saber mais sobre Bancos de Dados SQL, confira:

-   [Visão geral do Banco de Dados SQL](sql-database-paas-overview.md)
-   [Gerenciamento de ciclo de vida do banco de dados](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))