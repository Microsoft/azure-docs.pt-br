---
title: Auditoria da Instância Gerenciada de SQL
description: Saiba como começar a usar a auditoria do Azure SQL Instância Gerenciada usando o T-SQL
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 05/26/2020
ms.openlocfilehash: 31a1169ca6c2194b8d5564e5d0df50116dd25084
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94505658"
---
# <a name="get-started-with-azure-sql-managed-instance-auditing"></a>Introdução à auditoria do Azure SQL Instância Gerenciada
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A auditoria [do Azure SQL instância gerenciada](sql-managed-instance-paas-overview.md) rastreia eventos de banco de dados e os grava em um log de auditoria em sua conta de armazenamento do Azure. A auditoria também:

- Ajuda a manter conformidade com as normas, entender a atividade do banco de dados e aprofundar-se sobre discrepâncias e anomalias que podem indicar preocupações comerciais ou violações suspeitas de segurança.
- Permite e facilita a adesão aos padrões de conformidade, embora não garanta a conformidade. Para obter mais informações sobre os programas do Azure que dão suporte à conformidade com padrões, consulte a [central de confiabilidade do Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), em que você pode encontrar a lista mais atual de certificações de conformidade.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Configurar a auditoria para o servidor no Armazenamento do Microsoft Azure

A seção a seguir descreve a configuração da auditoria na instância gerenciada.

1. Vá para o [Portal do Azure](https://portal.azure.com).
2. Crie um **contêiner** do Armazenamento do Azure no qual os logs de auditoria são armazenados.

   1. Navegue até a conta de armazenamento do Azure em que você deseja armazenar seus logs de auditoria.

      > [!IMPORTANT]
      > - Use uma conta de armazenamento na mesma região da instância gerenciada para evitar leituras/gravações entre regiões. 
      > - Se sua conta de armazenamento estiver atrás de uma Rede Virtual ou um firewall, consulte [Conceder acesso de uma rede virtual](../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network).
      > - Se você alterar o período de retenção de 0 (retenção ilimitada) para qualquer outro valor, observe que a retenção será aplicada somente aos logs gravados após a alteração do valor de retenção (os logs gravados durante o período em que a retenção era definida como ilimitada serão preservados, mesmo após a retenção ser habilitada).

   1. Na página da conta de armazenamento, vá até **Visão geral** e clique em **Blobs**.

      ![Widget BLOBs do Azure](./media/auditing-configure/1_blobs_widget.png)

   1. No menu superior, clique em **+ Contêiner** para criar um novo contêiner.

      ![Ícone de Criar contêiner de blobs](./media/auditing-configure/2_create_container_button.png)

   1. Forneça um **nome** de contêiner, defina **nível de acesso público** como **particular** e clique em **OK**.

      ![Criar a configuração do contêiner de blobs](./media/auditing-configure/3_create_container_config.png)

    > [!IMPORTANT]
    > Os clientes que desejam configurar um repositório de logs imutável para seus eventos de auditoria no nível de servidor ou banco de dados devem seguir as [instruções fornecidas pelo armazenamento do Azure](../../storage/blobs/storage-blob-immutability-policies-manage.md#enabling-allow-protected-append-blobs-writes). (Verifique se você selecionou **permitir acréscimos adicionais** ao configurar o armazenamento de blob imutável.)
  
3. Depois de criar o contêiner para os logs de auditoria, há duas maneiras de configurá-lo como o destino dos logs de auditoria: [usando T-SQL](#blobtsql) ou [usando a interface do usuário do SQL Server Management Studio (SSMS)](#blobssms):

   - <a id="blobtsql"></a>**Configurar o armazenamento de BLOB para logs de auditoria usando o T-SQL:**

     1. Na lista de contêineres, clique no contêiner recém-criado e, em seguida, clique em **Propriedades do contêiner**.

        ![Botão Propriedades do contêiner de blobs](./media/auditing-configure/4_container_properties_button.png)

     1. Copie a URL do contêiner, clicando no ícone de cópia e salve-a (por exemplo, no bloco de notas) para uso futuro. O formato da URL do contêiner deve ser `https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![URL de cópia do contêiner de blobs](./media/auditing-configure/5_container_copy_name.png)

     1. Gere um **token SAS** do armazenamento do Azure para conceder direitos de acesso de auditoria de instância gerenciada à conta de armazenamento:

        - Navegue até a conta de armazenamento do Azure em que você criou o contêiner na etapa anterior.

        - Clique em **assinatura de acesso compartilhado** no menu **configurações de armazenamento** .

          ![Ícone de assinatura de acesso compartilhado no menu de configurações de armazenamento](./media/auditing-configure/6_storage_settings_menu.png)

        - Configure a SAS da seguinte forma:

          - **Serviços permitidos** : Blob

          - **Data de início** : para evitar problemas relacionados a fuso horário, use a data de ontem

          - **Data de término** : escolha a data em que esse token SAS expira

            > [!NOTE]
            > Renove o token após o vencimento para evitar falhas de auditoria.

          - Clique em **Gerar SAS**.

            ![Configuração do SAS](./media/auditing-configure/7_sas_configure.png)

        - O token SAS é exibido na parte inferior. Copie o token clicando no ícone de cópia e salve-o (por exemplo, no bloco de notas) para uso futuro.

          ![Copiar o token SAS](./media/auditing-configure/8_sas_copy.png)

          > [!IMPORTANT]
          > Remova o caractere de ponto de interrogação ("?") do início do token.

     1. Conecte-se à sua instância gerenciada por meio de SQL Server Management Studio ou qualquer outra ferramenta com suporte.

     1. Execute a seguinte instrução T-SQL para **criar uma nova credencial** usando a URL do contêiner e o token SAS que você criou nas etapas anteriores:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Execute a seguinte instrução T-SQL para criar uma nova auditoria de servidor (escolha seu próprio nome de auditoria e use a URL do contêiner que você criou nas etapas anteriores). Se não for especificado, o `RETENTION_DAYS` padrão será 0 (retenção ilimitada):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' , RETENTION_DAYS =  integer )
        GO
        ```

     1. Continue [criando uma especificação de auditoria de servidor ou especificação de auditoria de banco de dados](#createspec).

   - <a id="blobssms"></a>**Configure o armazenamento de BLOBs para logs de auditoria usando o SQL Server Management Studio 18:**

     1. Conecte-se à instância gerenciada usando a interface do usuário do SQL Server Management Studio.

     1. Expanda a observação raiz do pesquisador de objetos.

     1. Expanda o nó **segurança** , clique com o botão direito do mouse no nó **auditorias** e clique em **nova auditoria** :

        ![Expandir os nós de segurança e auditoria](./media/auditing-configure/10_mi_SSMS_new_audit.png)

     1. Verifique se a **URL** está selecionada em **destino de auditoria** e clique em **procurar** :

        ![Procurar no Armazenamento do Azure](./media/auditing-configure/11_mi_SSMS_audit_browse.png)

     1. (Opcional) Entre em sua conta do Azure:

        ![Entrar no Azure](./media/auditing-configure/12_mi_SSMS_sign_in_to_azure.png)

     1. Selecione uma assinatura, uma conta de armazenamento e um contêiner de BLOBs nos menus suspensos ou crie seu próprio contêiner clicando em **criar**. Depois de concluir, clique em **OK** :

        ![Selecionar assinatura, conta de armazenamento e contêiner de blobs do Azure](./media/auditing-configure/13_mi_SSMS_select_subscription_account_container.png)

     1. Clique em **OK** na caixa de diálogo **criar auditoria** .
     
     1. <a id="createspec"></a>Depois de configurar o contêiner de blob como destino para os logs de auditoria, crie e habilite uma especificação de auditoria de servidor ou especificação de auditoria de banco de dados como você faria para SQL Server:

   - [Criar guia T-SQL de especificação de auditoria de servidor](/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Criar guia T-SQL de especificação de auditoria de banco de dados](/sql/t-sql/statements/create-database-audit-specification-transact-sql)

5. Habilite a auditoria de servidor criada na etapa 3:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Para informações adicionais:

- [Diferenças de auditoria entre o Azure SQL Instância Gerenciada e um banco de dados no SQL Server](#auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server)
- [CRIAR AUDITORIA DE SERVIDOR](/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTERAR AUDITORIA DE SERVIDOR](/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hubs-or-azure-monitor-logs"></a>Configurar a auditoria do servidor para os hubs de eventos ou logs de Azure Monitor

Os logs de auditoria de uma instância gerenciada podem ser enviados para os hubs de eventos do Azure ou logs de Azure Monitor. Esta seção descreve como fazer essa configuração:

1. Navegue no [portal do Azure](https://portal.azure.com/) para a instância gerenciada.

2. Clique em **Configurações de diagnóstico**.

3. Clique em **Ativar diagnóstico**. Se o diagnóstico já estiver habilitado, **+ Adicionar configuração de diagnóstico** será mostrada em vez disso.

4. Selecione **SQLSecurityAuditEvents** na lista de logs.

5. Selecione um destino para os eventos de auditoria: hubs de eventos, logs de Azure Monitor ou ambos. Configure os parâmetros necessários (por exemplo, espaço de trabalho do Log Analytics) para cada destino.

6. Clique em **Save** (Salvar).

    ![Definir as configurações de diagnóstico](./media/auditing-configure/9_mi_configure_diagnostics.png)

7. Conecte-se à instância gerenciada usando o **SSMS (SQL Server Management Studio)** ou qualquer outro cliente com suporte.

8. Execute a seguinte instrução T-SQL para criar uma auditoria de servidor:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Crie e habilite uma especificação de auditoria de servidor ou especificação de auditoria de banco de dados como faria para o SQL Server:

   - [Guia Criar T-SQL de especificação de auditoria de servidor](/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Guia Criar T-SQL de especificação de auditoria de banco de dados](/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Habilite a auditoria de servidor criada na etapa 8:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Consumir logs de auditoria

### <a name="consume-logs-stored-in-azure-storage"></a>Consumir logs armazenados no Armazenamento do Azure

Há vários métodos que podem ser usados para exibir os logs de auditoria de blob.

- Use a função do sistema caractere `sys.fn_get_audit_file` (T-SQL) para retornar os dados do log de auditoria em um formato tabular. Para obter mais informações sobre como usar essa função, consulte a [documentação de sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Explore os logs de auditoria usando uma ferramenta como o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/). No armazenamento do Azure, os logs de auditoria são salvos como uma coleção de arquivos de blob dentro de um contêiner que foi definido para armazenar os logs de auditoria. Para obter mais detalhes sobre a hierarquia da pasta de armazenamento, as convenções de nomenclatura e o formato do log, consulte a [Referência de formato do log de auditoria de blob](../database/audit-log-format.md).

- Para obter uma lista completa dos métodos de consumo de log de auditoria, consulte Introdução à [auditoria do banco de dados SQL do Azure](../../azure-sql/database/auditing-overview.md).

### <a name="consume-logs-stored-in-event-hubs"></a>Consumir logs armazenados em hubs de eventos

Para consumir dados de logs de auditoria dos hubs de eventos, você precisará configurar um fluxo para consumir eventos e gravá-los em um destino. Para obter mais informações, consulte a documentação dos Hubs de Evento do Azure.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Consumir e analisar logs armazenados em logs de Azure Monitor

Se os logs de auditoria forem gravados nos logs do Azure Monitor, eles estarão disponíveis no workspace do Log Analytics, onde você poderá executar pesquisas avançadas nos dados de auditoria. Como ponto de partida, navegue até o espaço de trabalho Log Analytics. Na seção **geral** , clique em **logs** e insira uma consulta simples, como: `search "SQLSecurityAuditEvents"` para exibir os logs de auditoria.  

Os logs do Azure Monitor fornecem insights operacionais em tempo real usando pesquisa integrada e painéis personalizados para analisar prontamente milhões de registros em todas as suas cargas de trabalho e servidores. Para ver informações úteis adicionais sobre o idioma e os comandos da pesquisa de logs do Azure Monitor, consulte [Referência de pesquisa dos logs do Azure Monitor](../../azure-monitor/log-query/log-query-overview.md).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server"></a>Diferenças de auditoria entre bancos de dados no Azure SQL Instância Gerenciada e bancos de dados no SQL Server

As principais diferenças entre auditoria em bancos de dados no Azure SQL Instância Gerenciada e bancos de dados no SQL Server são:

- Com o Azure SQL Instância Gerenciada, a auditoria funciona no nível do servidor e armazena `.xel` arquivos de log no armazenamento de BLOBs do Azure.
- No SQL Server, a auditoria funciona no nível do servidor, mas armazena eventos em logs de eventos do sistema de arquivos/Windows.

A auditoria de XEvent em instâncias gerenciadas dá suporte a destinos do armazenamento de BLOBs do Azure. **Não há suporte** para logs de arquivo e do Windows.

As principais diferenças na sintaxe `CREATE AUDIT` para a auditoria do armazenamento de Blobs do Azure são:

- Uma nova sintaxe `TO URL` é fornecida e permite que você especifique a URL do contêiner de armazenamento de BLOBs do Azure onde os `.xel` arquivos são colocados.
- Uma nova sintaxe `TO EXTERNAL MONITOR` é fornecida para habilitar os hubs de eventos e Azure monitor destinos de log.
- `TO FILE` **Não há suporte para** a sintaxe porque o Azure SQL instância gerenciada não pode acessar compartilhamentos de arquivos do Windows.
- A opção de desligamento **não é compatível**.
- **Não há suporte** para `queue_delay` de 0.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma lista completa dos métodos de consumo de log de auditoria, consulte Introdução à [auditoria do banco de dados SQL do Azure](../../azure-sql/database/auditing-overview.md).
- Para obter mais informações sobre os programas do Azure que dão suporte à conformidade com padrões, consulte a [central de confiabilidade do Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), em que você pode encontrar a lista mais atual de certificações de conformidade.

<!--Image references-->
