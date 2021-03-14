---
title: Log de auditoria-banco de dados do Azure para PostgreSQL-servidor único
description: Conceitos para o log de auditoria do pgAudit no banco de dados do Azure para PostgreSQL-servidor único.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 33fa6420f52cae9c869cc75a04ea82de0ec48262
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596292"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Log de auditoria no banco de dados do Azure para PostgreSQL-servidor único

Log de auditoria de atividades de banco de dados no banco de dados do Azure para PostgreSQL-um servidor único está disponível por meio da extensão de auditoria PostgreSQL: [pgAudit](https://www.pgaudit.org/). o pgAudit fornece o log de auditoria detalhado de sessão e/ou objeto.

> [!NOTE]
> pgAudit está em visualização no banco de dados do Azure para PostgreSQL.
> A extensão pode ser habilitada somente em servidores Uso Geral e com otimização de memória.

Se você quiser logs no nível de recursos do Azure para operações como o dimensionamento de computação e armazenamento, consulte o [log de atividades do Azure](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="usage-considerations"></a>Considerações sobre o uso
Por padrão, as instruções de log pgAudit são emitidas junto com suas instruções de log regulares usando o recurso de log padrão do Postgres. No banco de dados do Azure para PostgreSQL, esses arquivos. log podem ser baixados por meio do portal do Azure ou da CLI. O armazenamento máximo para a coleção de arquivos é de 1 GB e cada arquivo está disponível por um máximo de sete dias (o padrão é três dias). Esse serviço é uma opção de armazenamento de curto prazo.

Como alternativa, você pode configurar todos os logs a serem enviados para Azure Monitor repositório de log para análise posterior no Log Analytics. Se você habilitar Azure Monitor log de recursos, seus logs serão enviados automaticamente (no formato JSON) para o armazenamento do Azure, hubs de eventos e/ou logs de Azure Monitor, dependendo de sua escolha.

A habilitação de pgAudit gera um grande volume de log em um servidor, que tem um impacto no desempenho e no armazenamento de log. Recomendamos que você use logs de Azure Monitor, que oferece opções de armazenamento de longo prazo, bem como recursos de análise e alerta. Recomendamos que você desative o registro em log padrão para reduzir o impacto no desempenho de log adicional:

   1. Defina o parâmetro `logging_collector` como off. 
   2. Reinicie o servidor para aplicar essa alteração.

Para saber como configurar o log no armazenamento do Azure, nos hubs de eventos ou nos logs de Azure Monitor, visite a seção logs de recursos do [artigo logs do servidor](concepts-server-logs.md).

## <a name="installing-pgaudit"></a>Instalando o pgAudit

Para instalar o pgAudit, você precisa incluí-lo nas bibliotecas de pré-carregamento compartilhadas do servidor. Uma alteração no parâmetro de postgres `shared_preload_libraries` requer a reinicialização do servidor para entrar em vigor. Você pode alterar os parâmetros usando o [portal do Azure](howto-configure-server-parameters-using-portal.md), [CLI do Azure](howto-configure-server-parameters-using-cli.md)ou a [API REST](/rest/api/postgresql/configurations/createorupdate).

Usando o [portal do Azure](https://portal.azure.com):

   1. Selecione seu servidor de Banco de Dados do Azure para PostgreSQL.
   2. Na barra lateral, selecione **parâmetros do servidor**.
   3. Pesquise o parâmetro `shared_preload_libraries`.
   4. Selecione **pgaudit**.
   5. Reinicie o servidor para aplicar a alteração.

   6. Conectar-se ao servidor usando um cliente (como psql) e habilitar a extensão pgAudit
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Se você vir um erro, confirme que você reiniciou o servidor após salvar `shared_preload_libraries` .

## <a name="pgaudit-settings"></a>configurações de pgAudit

o pgAudit permite que você configure o log de auditoria de sessão ou objeto. O [log de auditoria de sessão](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) emite logs detalhados de instruções executadas. O [log de auditoria de objeto](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) tem o escopo de auditoria para relações específicas. Você pode optar por configurar um ou ambos os tipos de registro em log. 

> [!NOTE]
> as configurações de pgAudit são especificadas identificador global e não podem ser especificadas em um nível de banco de dados ou função.

Depois de [instalar o pgAudit](#installing-pgaudit), você pode configurar seus parâmetros para iniciar o registro em log. A [documentação do pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) fornece a definição de cada parâmetro. Teste os parâmetros primeiro e confirme que você está obtendo o comportamento esperado.

> [!NOTE]
> Definir `pgaudit.log_client` como on redirecionará os logs para um processo de cliente (como psql) em vez de ser gravado no arquivo. Essa configuração deve ser deixada desabilitada. <br> <br>
> `pgaudit.log_level` é habilitado somente quando o `pgaudit.log_client` está ativado.

> [!NOTE]
> No banco de dados do Azure para PostgreSQL, `pgaudit.log` não pode ser definido usando um `-` atalho de sinal (menos), conforme descrito na documentação do pgAudit. Todas as classes de instrução necessárias (LEITURA, GRAVAÇÃO etc.) devem ser especificadas individualmente.

### <a name="audit-log-format"></a>Formato do log de auditoria
Cada entrada de auditoria é indicada `AUDIT:` próximo ao início da linha de log. O formato do restante da entrada é detalhado na [documentação do pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

Se você precisar de outros campos para atender aos seus requisitos de auditoria, use o parâmetro postgres `log_line_prefix` . `log_line_prefix` é uma cadeia de caracteres que é saída no início de cada linha de log do Postgres. Por exemplo, a configuração a seguir `log_line_prefix` fornece timestamp, username, Database Name e Process ID:

```
t=%m u=%u db=%d pid=[%p]:
```

Para saber mais sobre `log_line_prefix` o, visite a [documentação do PostgreSQL](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX).

### <a name="getting-started"></a>Introdução
Para começar rapidamente, defina `pgaudit.log` como `WRITE` e abra seus logs para examinar a saída. 

## <a name="viewing-audit-logs"></a>Exibindo logs de auditoria
Se você estiver usando arquivos. log, os logs de auditoria serão incluídos no mesmo arquivo que os logs de erros do PostgreSQL. Você pode baixar arquivos de log do [portal](howto-configure-server-logs-in-portal.md) do Azure ou da [CLI](howto-configure-server-logs-using-cli.md). 

Se você estiver usando o log de recursos do Azure, a maneira como você acessa os logs dependerá do ponto de extremidade escolhido. Para o armazenamento do Azure, consulte o artigo [conta de armazenamento de logs](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) . Para os hubs de eventos, consulte o artigo [fluxos de logs do Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) .

Para logs de Azure Monitor, os logs são enviados para o espaço de trabalho selecionado. Os logs do postgres usam o modo de coleta **AzureDiagnostics** , para que possam ser consultados a partir da tabela AzureDiagnostics. Os campos na tabela são descritos abaixo. Saiba mais sobre como consultar e alertar na visão geral de [consulta de logs de Azure monitor](../azure-monitor/logs/log-query-overview.md) .

Você pode usar essa consulta para começar. Você pode configurar alertas com base em consultas.

Pesquisar todos os logs do postgres para um servidor específico no último dia
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Próximas etapas
- [Saiba mais sobre o log no banco de dados do Azure para PostgreSQL](concepts-server-logs.md)
- Saiba como definir parâmetros usando o [portal do Azure](howto-configure-server-parameters-using-portal.md), [CLI do Azure](howto-configure-server-parameters-using-cli.md)ou a [API REST](/rest/api/postgresql/configurations/createorupdate).