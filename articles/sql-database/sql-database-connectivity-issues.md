---
title: Trabalhando com erros transitórios - Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba como solucionar problemas, diagnosticar e evitar um erro de conexão do SQL ou um erro transitório no Banco de Dados SQL do Azure.
keywords: conexão do sql, cadeia de conexão, problemas de conectividade, erro transitório, erro de conexão
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
ms.author: ninarn
ms.reviewer: carlrab
manager: craigg
ms.date: 06/14/2019
ms.openlocfilehash: adbe8dfd41725c11516f820656b0476ed1aa8881
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144042"
---
# <a name="working-with-sql-database-connection-issues-and-transient-errors"></a>Trabalhando com problemas de conexão do Banco de Dados SQL do Azure e erros transitórios

Este artigo descreve como impedir, solucionar, diagnosticar e reduzir erros de conexão e erros transitórios que seu aplicativo cliente encontra quando interage com o Banco de Dados SQL do Azure. Saiba como configurar a lógica de repetição, construir a cadeia de conexão e ajustar outras configurações de conexão.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Erros transitórios (falhas transitórias)

Um erro transitório, também chamado de falha transitória, tem uma causa subjacente que será resolvida em breve. Uma causa ocasional de erros transitórios é quando o sistema do Azure rapidamente alterna os recursos de hardware para melhor balanceamento de diversas cargas de trabalho. A maioria desses eventos de reconfiguração termina em menos de 60 segundos. Durante esse período de reconfiguração, você pode enfrentar problemas de conectividade com o Banco de Dados SQL. Aplicativos que se conectam ao Banco de Dados SQL devem ser criados para esperar esses erros transitórios. Para lidar com eles, implemente a lógica de repetição no código, em vez de mostrá-los aos usuários como erros de aplicativo.

Se o programa cliente estiver usando ADO.NET, o programa será informado do erro transitório pelo lançamento de **SqlException**. Compare a propriedade **Number** com a lista de erros transitórios encontrada na parte superior do artigo: [Códigos de erro SQL para aplicativos cliente do Banco de Dados SQL](sql-database-develop-error-messages.md).

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Conexão X comando

Tente novamente estabelecer a conexão SQL repeti-la, dependendo do seguinte:

- **Um erro transitório ocorre durante uma tentativa de conexão**

Após um atraso de vários segundos, tente novamente a conexão.

- **Um erro transitório ocorre durante um comando de consulta SQL**

Não repita imediatamente o comando. Em vez disso, depois de um atraso, estabeleça a conexão novamente. Em seguida, tente novamente o comando.

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Lógica de repetição para erros transitórios

Os programas cliente que ocasionalmente encontram um erro transitório são mais eficientes quando contêm lógica de repetição. Quando o programa se comunica com o Banco de Dados SQL por meio de um middleware de terceiros, pergunte ao fornecedor se o middleware contém lógica de repetição para erros transitórios.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Princípios de repetição

- Se o erro for transitório, tente novamente abrir uma conexão.
- Não tente novamente uma instrução SQL `SELECT` diretamente que falhou com um erro transitório. Em vez disso, estabeleça uma conexão nova e tente novamente o comando `SELECT`.
- Quando uma instrução SQL `UPDATE` falhar com um erro transitório, estabeleça uma conexão nova antes de tentar novamente o comando UPDATE. A lógica de repetição deve garantir que toda a transação de banco de dados seja concluída ou que a transição inteira seja revertida.

### <a name="other-considerations-for-retry"></a>Outras considerações sobre tentativas de repetição

- Um programa em lote iniciado automaticamente depois do horário comercial e que termina antes da manhã pode se dar ao luxo de ser muito paciente com longos intervalos entre as tentativas de repetição.
- Um programa de interface do usuário deve levar em consideração a tendência humana de desistir após uma longa espera. No entanto, a solução não deve ser tentada novamente em alguns segundos, porque essa política pode inundar o sistema com solicitações.

### <a name="interval-increase-between-retries"></a>Aumento do intervalo entre tentativas de repetição

Recomendamos que você aguarde 5 segundos até a primeira tentativa. Tentar novamente após um atraso inferior a 5 segundos poderá sobrecarregar o serviço de nuvem. Para cada tentativa subsequente, o atraso deve aumentar exponencialmente, até um máximo de 60 segundos.

Para ver uma discussão sobre o período de bloqueio para clientes que usam o ADO.NET, consulte [Pool de conexão do SQL Server (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx).

Talvez você também queira definir um número máximo de novas tentativas antes que o programa seja encerrado automaticamente.

### <a name="code-samples-with-retry-logic"></a>Exemplos de código com lógica de repetição

Há exemplos de código com lógica de repetição disponíveis em:

- [Conectar-se de forma resiliente ao SQL com ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [Conectar-se de forma resiliente ao SQL com PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>Testar sua lógica de repetição

Para testar a lógica de repetição, você deve simular ou causar um erro que possa ser corrigido enquanto o programa ainda estiver em execução.

#### <a name="test-by-disconnecting-from-the-network"></a>Testar por meio da desconexão da rede

Uma forma de testar sua lógica de repetição é desconectar seu computador cliente da rede enquanto o programa está em execução. O erro é:

- **SqlException.Number** = 11001
- Mensagem: "Nenhum host desse tipo é conhecido"

Como parte da primeira tentativa de repetição, você pode reconectar o computador cliente à rede e, em seguida, tentar se conectar.

Para tornar esse teste prático, desconecte o computador da rede antes de iniciar o programa. Em seguida, o programa reconhece um parâmetro de tempo de execução que faz com que o programa:

- Adicione temporariamente 11001 à sua lista de erros a serem considerados como transitórios.
- Tente fazer sua primeira conexão como de costume.
- Depois que o erro for detectado, remova 11001 da lista.
- Exiba uma mensagem instruindo o usuário a conectar o computador à rede.
- Pause a execução usando o método **Console.ReadLine** ou uma caixa de diálogo com um botão OK. O usuário pressiona a tecla Enter depois que o computador é conectado à rede.
- Tente se conectar novamente, esperando êxito.

#### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Testar errando o nome do banco de dados ao se conectar

Seu programa pode errar intencionalmente o nome de usuário antes da primeira tentativa de conexão. O erro é:

- **SqlException.Number** = 18456
- Mensagem: "Falha de logon do usuário 'WRONG_MyUserName'."

Como parte da primeira tentativa de repetição, o programa pode corrigir a ortografia e tentar se conectar.

Para tornar esse teste prático, o programa reconhece um parâmetro de tempo de execução que faz com que o programa:

- Adicione temporariamente 18456 à sua lista de erros a serem considerados como transitórios.
- Adicione propositadamente 'WRONG_' ao nome de usuário.
- Depois que o erro for detectado, remova 18456 da lista.
- Remova 'WRONG_' do nome de usuário.
- Tente se conectar novamente, esperando êxito.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>Parâmetros SqlConnection .NET para repetição de conexão

Se o programa cliente se conectar ao Banco de Dados SQL usando a classe **System.Data.SqlClient.SqlConnection** do .NET Framework, use o .NET 4.6.1 ou posterior (ou o .NET Core), de maneira que você possa usar o recurso de nova tentativa de conexão. Para obter mais informações sobre o recurso, consulte [esta página da Web](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

Ao criar a [cadeia de conexão](https://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) para o objeto **SqlConnection**, coordene os valores dentre os seguintes parâmetros:

- **ConnectRetryCount**:&nbsp;&nbsp;o padrão é 1. O intervalo vai de 0 a 255.
- **ConnectRetryInterval**:&nbsp;&nbsp;padrão é 10 segundos. O intervalo vai de 1 a 60.
- **Connection Timeout**:&nbsp;&nbsp;o padrão é 15 segundos. O intervalo vai de 0 a 2147483647.

Especificamente, os valores escolhidos devem tornar verdadeira esta igualdade: Connection Timeout = ConnectRetryCount * ConnectionRetryInterval

Por exemplo, se a contagem for igual a 3 e o intervalo é igual a 10 segundos, um tempo limite de apenas 29 segundos não dará tempo suficiente para sua terceira e última tentativa para se conectar: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Conexão X comando

Os parâmetros **ConnectRetryCount** e **ConnectRetryInterval** permitem que seu objeto **SqlConnection** repita a operação de conexão sem informar ou incomodar o programa como, por exemplo, ao devolver o controle ao programa. As tentativas podem ocorrer nas seguintes situações:

- Chamada ao método mySqlConnection.Open
- Chamada ao método mySqlConnection.Execute

Mas há uma sutileza aqui. Se um erro transitório ocorrer durante a execução da *consulta*, o objeto **SqlConnection** não tentará repetir a operação de conexão. Isso certamente não repete a consulta. No entanto, **SqlConnection** verifica rapidamente a conexão antes de enviar a consulta para execução. Se a verificação rápida detectar um problema de conexão, **SqlConnection** repetirá a operação de conexão. Se a nova tentativa for bem-sucedida, a consulta será enviada para execução.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>O ConnectRetryCount deve ser combinado com a lógica de repetição de aplicativo

Suponha que seu aplicativo tenha lógica de repetição personalizada robusta. Ele pode repetir a operação de conexão quatro vezes. Se você adicionar **ConnectRetryInterval** e **ConnectRetryCount** = 3 à cadeia de conexão, aumentará a contagem de repetição para 4 * 3 = 12 repetições. Talvez você não queira um número tão alto de repetições.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-sql-database"></a>Conexões com o Banco de Dados SQL

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Conexão: Cadeia de conexão

A cadeia de conexão necessária para se conectar ao Banco de Dados SQL é um pouco diferente da cadeia de caracteres usada para se conectar ao SQL Server. Você pode copiar a cadeia de conexão para o seu banco de dados no [Portal do Azure](https://portal.azure.com/).

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Conexão: Endereço IP

Você deve configurar o servidor do Banco de Dados SQL para aceitar a comunicação do endereço IP do computador que hospeda o programa cliente. Para definir essa configuração, edite as configurações do firewall por meio do [Portal do Azure](https://portal.azure.com/).

Se você se esquecer de configurar o endereço IP, o programa falhará com uma mensagem de erro prática que indica o endereço IP necessário.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

Para obter mais informações, consulte [Definir configurações de firewall no Banco de Dados SQL](sql-database-configure-firewall-settings.md).
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Conexão: Portas

Geralmente, você só precisa garantir que a porta 1433 esteja aberta para comunicação de saída no computador que hospeda o programa cliente.

Por exemplo, quando o programa cliente está hospedado em um computador com o Windows, você pode usar o Firewall do Windows no host para abrir a porta 1433.

1. Abra o Painel de Controle.
2. Selecione **Todos os Itens do Painel de Controle** > **Firewall do Windows** > **Configurações Avançadas** > **Regras de Saída** > **Ações** > **Nova Regra**.

Se o programa cliente estiver hospedado em uma máquina virtual (VM) do Azure, leia [Portas além de 1433 para ADO.NET 4.5 e Banco de Dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md).

Para obter mais informações sobre a configuração de portas e endereços IP, consulte: [Firewall do Banco de Dados SQL do Azure](sql-database-firewall-configure.md).

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-462-or-later"></a>Conexão: ADO.NET 4.6.2 ou posterior

Se seu programa usa classes do ADO.NET como **System.Data.SqlClient.SqlConnection** para se conectar ao Banco de Dados SQL, recomendamos que você use o .NET Framework versão 4.6.2 ou posterior.

#### <a name="starting-with-adonet-462"></a>Iniciando com ADO.NET 4.6.2

- A tentativa de abrir a conexão para ser repetida imediatamente para bancos de dados SQL do Azure, melhorando assim o desempenho de aplicativos habilitados para nuvem.

#### <a name="starting-with-adonet-461"></a>Iniciando com ADO.NET 4.6.1

- Para o Banco de Dados SQL, há mais confiabilidade quando você abre uma conexão usando o método **SqlConnection.Open**. O método **Open** agora incorpora mecanismos de repetição de melhor esforço em resposta a falhas transitórias para determinados erros dentro do período de tempo limite da conexão.
- O pool de conexão é compatível, o que inclui uma verificação eficiente se o objeto de conexão oferecido ao programa esteja funcionando.

Quando você usa um objeto de conexão de um pool de conexão, é recomendável que seu programa Feche temporariamente a conexão quando não for imediatamente em uso. Não é caro reabrir uma conexão, mas criar uma nova conexão é.

Se você estiver usando o ADO.NET 4.0 ou anterior, recomendaremos fazer upgrade para o ADO.NET mais recente. A partir de agosto de 2018, você pode [baixar o ADO.NET 4.6.2](https://blogs.msdn.microsoft.com/dotnet/20../../announcing-the-net-framework-4-7-2/).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnósticos

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnóstico: Testar se os utilitários podem se conectar

Se o programa não se conectar ao Banco de Dados SQL, uma opção de diagnóstico será tentar se conectar usando um programa utilitário. O ideal é que o utilitário se conecte usando a mesma biblioteca que o programa.

Em qualquer computador Windows, você pode experimentar estes utilitários:

- SQL Server Management Studio (ssms.exe), que se conecta usando ADO.NET
- `sqlcmd.exe`, que conecta-se usando [ODBC](https://msdn.microsoft.com/library/jj730308.aspx)

Depois que o programa se conectar, teste se uma consulta SQL SELECT curta funciona.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnóstico: Verificar as portas abertas

Se suspeitar de uma falha na conexão por causa de problemas na porta, você poderá executar um utilitário no computador relatando as configurações de porta.

No Linux, os seguintes utilitários podem ser úteis:

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`: Altere o valor de exemplo para ser o endereço IP.

No Windows, o utilitário [PortQry.exe](https://www.microsoft.com/download/details.aspx?id=17148) pode ser útil. Aqui está uma execução de exemplo que consultou a situação da porta em um servidor do Banco de Dados SQL e que foi executada em um computador laptop:

```cmd
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called: johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```

<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnóstico: Os erros de log

Às vezes, um problema intermitente é mais bem diagnosticado pela detecção de um padrão geral ao longo de dias ou de semanas.

O cliente pode auxiliar em um diagnóstico ao registrar em log todos os erros encontrados. Convém correlacionar as entradas de log com os dados do erro que o próprio Banco de Dados SQL registra em log internamente.

O Enterprise Library 6 (EntLib60) oferece classes gerenciadas .NET para auxiliar no registro em log. Para obter mais informações, consulte [5 - tão fácil quanto evitar um log: Use o Logging Application Block](https://msdn.microsoft.com/library/dn440731.aspx).

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnóstico: Examine os logs de erros do sistema

Aqui estão algumas instruções SQL SELECT que consultam logs de erros e outras informações.

| Consulta de log | DESCRIÇÃO |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |A exibição [sys.event_log](https://msdn.microsoft.com/library/dn270018.aspx) oferece informações sobre eventos individuais, o que inclui alguns que podem causar erros transitórios ou falhas de conectividade.<br/><br/>O ideal é que você possa correlacionar os valores **start_time** ou **end_time** com as informações sobre quando o programa cliente enfrentou problemas.<br/><br/>Você deve se conectar ao banco de dados  *mestre* para executar essa consulta. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |A exibição [sys.database_connection_stats](https://msdn.microsoft.com/library/dn269986.aspx) oferece contagens agregadas dos tipos de eventos para diagnóstico adicional.<br/><br/>Você deve se conectar ao banco de dados  *mestre* para executar essa consulta. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnóstico: Pesquisar eventos de problema no log do banco de dados SQL

Você pode procurar entradas sobre eventos de problemas no log do Banco de Dados SQL. Experimente a instrução SELECT Transact-SQL a seguir no banco de dados *mestre* :

```sql
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```

#### <a name="a-few-returned-rows-from-sysfnxetelemetryblobtargetreadfile"></a>Algumas linhas retornadas de sys.fn_xe_telemetry_blob_target_read_file

O exemplo a seguir mostra como pode ser a aparência de uma linha retornada. Os valores nulos mostrados normalmente não são nulos em outras linhas.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```

<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Enterprise Library 6

O Enterprise Library 6 (EntLib60) é uma estrutura de classes .NET que ajuda a implementar clientes robustos de serviços de nuvem, e um deles é o serviço Banco de Dados SQL. Para localizar tópicos dedicados a cada área nas quais o EntLib60 pode ajudar, consulte [Enterprise Library 6 - abril de 2013](https://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx).

A lógica de repetição para tratar erros transitórios é uma área na qual EntLib60 pode auxiliar. Para obter mais informações, consulte [4 - Perseverança, segredo de todos os triunfos: Usar o Transient Fault Handling Application Block](https://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx).

> [!NOTE]
> O código-fonte de EntLib60 está disponível para fazer o download público no [Centro de Download](https://go.microsoft.com/fwlink/p/?LinkID=290898). A Microsoft não tem planos de fazer mais atualizações de manutenção ou de recursos no EntLib.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>Classes do EntLib60 para erros transitórios e tentativas de repetição

As classes do EntLib60 a seguir são particularmente úteis para lógica de repetição. Todas essas classes são encontradas em ou no namespace **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**.

No namespace **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

- **RetryPolicy**
  - **ExecuteAction**
- **ExponentialBackoff**
- **SqlDatabaseTransientErrorDetectionStrategy**
- **ReliableSqlConnection**
  - **ExecuteCommand**

No namespace **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

- **AlwaysTransientErrorDetectionStrategy**
- **NeverTransientErrorDetectionStrategy**

Estes são alguns links para informações sobre o EntLib60:

- Download gratuito do livro: [Guia do desenvolvedor para o Microsoft Enterprise Library, 2ª edição](https://www.microsoft.com/download/details.aspx?id=41145).
- Melhores práticas: [Diretrizes gerais de repetição](../best-practices-retry-general.md) tem uma excelente discussão detalhada sobre lógica de repetição.
- Download do NuGet: [Enterprise Library – Transient Fault Handling Application Block 6.0](https://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: O bloqueio de log

- O bloqueio de registro em log é uma solução altamente flexível e configurável que permite que você:
  - Crie e armazene mensagens de log em uma grande variedade de locais.
  - Categorize e filtre as mensagens.
  - Colete informações contextuais que sejam úteis para depuração e rastreamento, bem como para requisitos de auditoria e de log geral.
- O bloqueio de registro em log abstrai a funcionalidade de registro em log do destino de log, de maneira que o código do aplicativo seja consistente, independentemente do local e do tipo de armazenamento de log de destino.

Para obter mais informações, consulte [5 - tão fácil quanto evitar um log: Use o Logging Application Block](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>O EntLib60 é o código-fonte do método IsTransient

Em seguida, na classe **SqlDatabaseTransientErrorDetectionStrategy**, está o código-fonte C# do método **IsTransient**. O código-fonte esclarece quais erros são considerados temporários e dignos de repetição até abril de 2013.

```csharp
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre como resolver outros problemas de conexão do Banco de Dados SQL, visite [Solucionar problemas de conexão com o Banco de Dados SQL do Azure](sql-database-troubleshoot-common-connection-issues.md).
- [Bibliotecas de conexão para Banco de Dados SQL e SQL Server](sql-database-libraries.md)
- [Pool de conexões do SQL Server (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
- [*Retrying* é uma biblioteca de novas tentativas para fins gerais licenciada do Apache 2.0, escrita em Python,](https://pypi.python.org/pypi/retrying) para simplificar a tarefa de adicionar comportamento de nova tentativa a quase tudo.

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
