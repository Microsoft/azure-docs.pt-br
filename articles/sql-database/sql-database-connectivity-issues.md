---
title: "Corrigir um erro de conexão do SQL, erro transitório | Microsoft Docs"
description: "Saiba como solucionar problemas, diagnosticar e evitar um erro de conexão do SQL ou um erro transitório no Banco de Dados SQL. "
keywords: "conexão do sql, cadeia de conexão, problemas de conectividade, erro transitório, erro de conexão"
services: sql-database
documentationcenter: 
author: dalechen
manager: cshepard
editor: 
ms.assetid: efb35451-3fed-4264-bf86-72b350f67d50
ms.service: sql-database
ms.custom: develop apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/13/2017
ms.author: daleche
ms.openlocfilehash: 5260d6afd24ae0a9c60ee609b54f04bf901f219d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-diagnose-and-prevent-sql-connection-errors-and-transient-errors-for-sql-database"></a>Solucionar problemas, diagnosticar e evitar erros de conexão SQL e erros transitórios para o Banco de Dados SQL
Este artigo descreve como impedir, solucionar, diagnosticar e reduzir erros de conexão e erros transitórios que seu aplicativo cliente encontra quando interage com o Banco de Dados SQL do Azure. Saiba como configurar a lógica de repetição, construir a cadeia de conexão e ajustar outras configurações de conexão.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Erros transitórios (falhas transitórias)
Um erro transitório (também chamado de falha transitória) tem uma causa que, em breve, será resolvida. Uma causa ocasional de erros transitórios é quando o sistema do Azure rapidamente alterna os recursos de hardware para melhor balanceamento de diversas cargas de trabalho. A maioria desses eventos de reconfiguração normalmente é concluída em menos de 60 segundos. Durante esse período de tempo de reconfiguração, você pode ter problemas de conectividade com o Banco de Dados SQL do Azure. Os aplicativos que se conectam ao Banco de Dados SQL do Azure devem ser criados para esperar esses erros transitórios, manipulá-los com a implementação de lógica de repetição em seu código, em vez de mostrá-los para os usuários como erros de aplicativo.

Se seu programa cliente estiver usando o ADO.NET, o programa será informado sobre o erro transitório pelo lançamento de uma **SqlException**. A propriedade **Número** pode ser comparada à lista de erros transitórios na parte superior do tópico: [Códigos de erro SQL para aplicativos cliente do Banco de Dados SQL](sql-database-develop-error-messages.md).

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-versus-command"></a>Conexão versus comando
Você tentará fazer a conexão SQL outra vez ou estabelecê-la novamente, dependendo do seguinte:

* **Um erro transitório ocorre durante uma tentativa de conexão**: a conexão deve ser repetida após um atraso de alguns segundos.
* **Um erro transitório ocorre durante um comando de consulta SQL**: o comando não deve ser repetido imediatamente. Em vez disso, após um atraso, uma nova conexão deverá ser estabelecida. Em seguida, o comando poderá ser repetido.

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

### <a name="retry-logic-for-transient-errors"></a>Lógica de repetição para erros transitórios
Os programas cliente que ocasionalmente encontram um erro transitório são mais eficientes quando contêm lógica de repetição.

Quando seu programa se comunicar com o Banco de Dados SQL do Azure por meio de um middleware de terceiros, consulte o fornecedor para saber se o middleware contém lógica de repetição para erros transitórios.

<a id="principles-for-retry" name="principles-for-retry"></a>

#### <a name="principles-for-retry"></a>Princípios de repetição
* Uma tentativa de abrir uma conexão deverá ser repetida caso o erro seja transitório.
* Uma instrução SQL SELECT que falhe com um erro transitório não deverá ser repetida diretamente.
  
  * Em vez disso, estabeleça uma nova conexão e repita a SELECT.
* Quando uma instrução SQL UPDATE falhar com um erro transitório, uma nova conexão deverá ser estabelecida antes da nova tentativa de UPDATE.
  
  * A lógica de repetição deve garantir que a transação de banco de dados foi concluída ou que a transição inteira foi revertida.

#### <a name="other-considerations-for-retry"></a>Outras considerações sobre tentativas de repetição
* Um arquivo em lotes iniciado automaticamente depois do horário comercial e que será concluído antes da manhã poderá se dar ao luxo de ser muito paciente com os longos intervalos entre as tentativas de repetição.
* Um programa de interface do usuário deve levar em consideração a tendência humana de desistir após uma longa espera.
  
  * No entanto, a solução não deve ser tentar novamente em alguns segundos, porque essa política pode inundar o sistema com solicitações.

#### <a name="interval-increase-between-retries"></a>Aumento do intervalo entre tentativas de repetição
É recomendável que você aguarde 5 segundos antes de sua primeira tentativa. Tentar novamente após um atraso inferior a 5 segundos poderá sobrecarregar o serviço de nuvem. Para cada tentativa subsequente, o atraso deverá aumentar exponencialmente, até um máximo de 60 segundos.

Uma discussão sobre o *período de bloqueio* para clientes que usam o ADO.NET está disponível em [Pool de conexão do SQL Server (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

Você também poderá definir um número máximo de tentativas antes que o programa seja automaticamente encerrado.

#### <a name="code-samples-with-retry-logic"></a>Exemplos de código com lógica de repetição
Os exemplos de código com lógica de repetição, em uma variedade de linguagens de programação, estão disponíveis em:

* [Bibliotecas de conexão para Banco de Dados SQL e SQL Server](sql-database-libraries.md)

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

#### <a name="test-your-retry-logic"></a>Testar sua lógica de repetição
Para testar sua lógica de repetição, você deverá simular ou causar um erro que possa ser corrigido enquanto o programa ainda estiver em execução.

##### <a name="test-by-disconnecting-from-the-network"></a>Testar por meio da desconexão da rede
Uma forma de testar sua lógica de repetição é desconectar seu computador cliente da rede enquanto o programa está em execução. O erro será:

* **SqlException.Number** = 11001
* Mensagem: "Este host não é conhecido"

Como parte da primeira tentativa de repetição, seu programa poderá corrigir o problema de ortografia e tentar se conectar.

Para que isso seja prático, desconecte seu computador da rede antes de iniciar o programa. Em seguida, seu programa reconhecerá um parâmetro de tempo de execução que faz com que o programa:

1. Adicione temporariamente 11001 à sua lista de erros a serem considerados como transitórios.
2. Tente fazer sua primeira conexão como de costume.
3. Depois que o erro for detectado, remova 11001 da lista.
4. Exiba uma mensagem instruindo o usuário a conectar o computador à rede.
   * Pause a execução usando o método **Console.ReadLine** ou uma caixa de diálogo com um botão OK. O usuário pressiona a tecla Enter depois que o computador é conectado à rede.
5. Tente se conectar novamente, esperando êxito.

##### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Testar errando o nome do banco de dados ao se conectar
Seu programa pode errar intencionalmente o nome de usuário antes da primeira tentativa de conexão. O erro será:

* **SqlException.Number** = 18456
* Mensagem: "Falha de logon para o usuário 'WRONG_MyUserName'."

Como parte da primeira tentativa de repetição, seu programa poderá corrigir o problema de ortografia e tentar se conectar.

Para que isso seja prático, seu programa poderá reconhecer um parâmetro de tempo de execução que faça com que o programa:

1. Adicione temporariamente 18456 à sua lista de erros a serem considerados como transitórios.
2. Adicione propositadamente 'WRONG_' ao nome de usuário.
3. Depois que o erro for detectado, remova 18456 da lista.
4. Remova 'WRONG_' do nome de usuário.
5. Tente se conectar novamente, esperando êxito.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

### <a name="net-sqlconnection-parameters-for-connection-retry"></a>Parâmetros SqlConnection .NET para repetição de conexão
Se o programa cliente se conectar ao Banco de Dados SQL do Azure usando a classe **System.Data.SqlClient.SqlConnection** do .NET Framework, você deverá usar o .NET 4.6.1 ou posterior (ou o .NET Core) para poder aproveitar o recurso de repetição de conexão. Os detalhes do recurso estão [aqui](http://go.microsoft.com/fwlink/?linkid=393996).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->


Quando você cria a [cadeia de conexão](http://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) para o seu objeto **SqlConnection** , deve coordenar os valores entre os seguintes parâmetros:

* ConnectRetryCount &nbsp;&nbsp;*(o Padrão é 1. O intervalo vai de 0 a 255).*
* ConnectRetryInterval &nbsp;&nbsp;*(o Padrão é 1 segundo. O intervalo vai de 1 a 60).*
* Connection Timeout &nbsp;&nbsp;*(o Padrão é 15 segundos. O intervalo vai de 0 a 2147483647)*

Especificamente, os valores escolhidos devem tornar verdadeira esta igualdade:

* Connection Timeout = ConnectRetryCount * ConnectionRetryInterval

Por exemplo, se a contagem for igual a 3 e o intervalo igual a 10 segundos, um tempo limite de apenas 29 segundos não seria suficiente para fornecer ao sistema tempo suficiente para sua terceira e última tentativa de conexão: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-versus-command"></a>Conexão versus comando
Os parâmetros **ConnectRetryCount** e **ConnectRetryInterval** permitem que seu objeto **SqlConnection** repita a operação de conexão sem informar ou incomodar o programa como, por exemplo, ao devolver o controle ao programa. As tentativas podem ocorrer nas seguintes situações:

* Chamada ao método mySqlConnection.Open
* Chamada ao método mySqlConnection.Execute

Mas há uma sutileza aqui. Se ocorrer um erro transitório durante a execução da sua *consulta* , o objeto **SqlConnection** não tentará repetir a operação de conexão e certamente não repetirá a consulta. No entanto, **SqlConnection** verifica rapidamente a conexão antes de enviar a consulta para execução. Se a verificação rápida detectar um problema de conexão, **SqlConnection** repetirá a operação de conexão. Se a tentativa for bem-sucedida, sua consulta será enviada para execução.

#### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>ConnectRetryCount deve ser combinada à lógica de repetição de aplicativo?
Suponha que seu aplicativo tenha lógica de repetição personalizada robusta. Ele pode repetir a operação de conexão quatro vezes. Se você adicionar **ConnectRetryInterval** e **ConnectRetryCount** = 3 à cadeia de conexão, aumentará a contagem de repetição para 4 * 3 = 12 repetições. Talvez você não queira um número tão alto de repetições.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-azure-sql-database"></a>Conexões ao Banco de Dados SQL do Azure
<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Conexão: cadeia de conexão
A cadeia de conexão necessária para conectar ao Banco de Dados SQL do Azure é um pouco diferente da cadeia de conexão para o Microsoft SQL Server. Você pode copiar a cadeia de conexão para o seu banco de dados no [Portal do Azure](https://portal.azure.com/).

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Conexão: endereço IP
Você deve configurar o servidor do Banco de Dados SQL para aceitar a comunicação do endereço IP do computador que hospeda o programa cliente. Você pode fazer isso editando as configurações do firewall por meio do [Portal do Azure](https://portal.azure.com/).

Se você se esquecer de configurar o endereço IP, o programa falhará com uma mensagem de erro útil que indica o endereço IP necessário.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

Para saber mais, consulte [Como definir as configurações de firewall no Banco de Dados SQL](sql-database-configure-firewall-settings.md)

<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Conexão: portas
Geralmente, você só precisa garantir que a porta 1433 esteja aberta para comunicação de saída, no computador que hospeda o programa cliente.

Por exemplo, quando o programa cliente estiver hospedado em um computador com Windows, o Firewall do Windows no host permite que você abra a porta 1433:

1. Abrir o Painel de Controle
2. &gt; Todos os Itens do Painel de Controle
3. &gt; Firewall do Windows
4. &gt; Configurações Avançadas
5. &gt; Regras de Saída
6. &gt; Ações
7. &gt; Nova Regra

Se seu programa cliente estiver hospedado em uma máquina virtual do Azure (VM), você deverá ler:<br/>[Portas acima da 1433 para o ADO.NET 4.5 e o Banco de Dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md).

Para saber mais sobre a configuração de portas e de endereços IP, consulte: [Firewall do Banco de Dados SQL do Azure](sql-database-firewall-configure.md)

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-461"></a>Conexão: ADO.NET 4.6.1
Caso seu programa use classes do ADO.NET, como **System.Data.SqlClient.SqlConnection** , para se conectar ao Banco de Dados SQL do Azure, recomendamos que você use o .NET Framework versão 4.6.1 ou posterior.

ADO.NET 4.6.1:

* Para o Banco de Dados SQL do Azure, há maior confiabilidade quando você abre uma conexão usando o método **SqlConnection.Open** . O método **Open** agora incorpora mecanismos de repetição de melhor esforço em resposta a falhas transitórias, para certos erros no período de tempo limite da conexão.
* Dá suporte ao pool de conexões. Isso inclui uma verificação eficiente de que o objeto de conexão oferecido ao seu programa esteja funcionando.

Quando você usa um objeto de conexão de um pool de conexões, é recomendável que seu programa feche temporariamente a conexão quando ela não for usada imediatamente. Reabrir uma conexão não é tão caro quanto criar uma nova conexão.

Se você estiver usando o ADO.NET 4.0 ou anterior, será recomendável atualizar para o ADO.NET mais recente.

* Desde novembro de 2015, você pode [baixar o ADO.NET 4.6.1](http://blogs.msdn.com/b/dotnet/archive/2015/11/30/net-framework-4-6-1-is-now-available.aspx).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnostics
<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnóstico: testar se os utilitários podem se conectar
Se seu programa estiver falhando ao conectar com o Banco de Dados SQL do Azure, uma opção de diagnóstico será tentar se conectar com um programa utilitário. Idealmente, o utilitário conectaria usando a mesma biblioteca que seu programa.

Em qualquer computador Windows, você pode experimentar estes utilitários:

* SQL Server Management Studio (ssms.exe), que se conecta usando ADO.NET.
* sqlcmd.exe, que se conecta usando [ODBC](http://msdn.microsoft.com/library/jj730308.aspx).

Uma vez conectado, teste se uma consulta SQL SELECT curta funciona.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnóstico: verificar as portas abertas
Suponha que você suspeite que as tentativas de conexão estão falhando devido a problemas de porta. Em seu computador, você poderá executar um utilitário que relate as configurações de porta.

No Linux, os seguintes utilitários podem ser úteis:

* `netstat -nap`
* `nmap -sS -O 127.0.0.1`
  * (Altere o valor de exemplo para seu endereço IP.)

No Windows, o utilitário [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148) pode ser útil. Veja uma execução de exemplo que consultou a situação da porta no servidor do Banco de Dados SQL do Azure e que foi executado em um computador laptop:

```
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called:
 johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```


<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnóstico: registrar seus erros em log
Às vezes, um problema intermitente é mais bem diagnosticado pela detecção de um padrão geral ao longo de dias ou de semanas.

O cliente pode auxiliar em um diagnóstico ao registrar em log todos os erros encontrados. Você poderá correlacionar as entradas de log com os dados do erro que o próprio Banco de Dados SQL do Azure registra em log internamente.

Enterprise Library 6 (EntLib60) oferece classes .NET gerenciadas para ajudá-lo com logs:

* [5 - Tão fácil como evitar um log: usando o bloqueio de aplicativos de log](http://msdn.microsoft.com/library/dn440731.aspx)

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnóstico: examinar logs de erros do sistema
Aqui estão algumas instruções Transact-SQL SELECT que a consulta registra em log os erros e outras informações.

| Consulta de log | Descrição |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |A exibição [sys.event_log](http://msdn.microsoft.com/library/dn270018.aspx) oferece informações sobre os eventos individuais, incluindo algumas que podem causar erros transitórios ou falhas de conectividade.<br/><br/>O ideal é que você possa correlacionar os valores **start_time** ou **end_time** com informações sobre quando o seu programa cliente apresentou problemas.<br/><br/>**DICA:** você deve se conectar ao banco de dados **mestre** para executá-lo. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |A exibição [sys.database_connection_stats](http://msdn.microsoft.com/library/dn269986.aspx) oferece contagens agregadas dos tipos de eventos para diagnóstico adicional.<br/><br/>**DICA:** você deve se conectar ao banco de dados **mestre** para executá-lo. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnóstico: procurar eventos de problema no log do Banco de Dados SQL
Você pode procurar entradas sobre eventos de problemas no log do Banco de Dados SQL do Azure. Experimente a instrução SELECT Transact-SQL a seguir no banco de dados **mestre** :

```
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
A seguir, como seria a aparência de uma linha retornada. Os valores nulos mostrados normalmente não são nulos em outras linhas.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Enterprise Library 6
O Enterprise Library 6 (EntLib60) é uma estrutura de classes .NET que ajuda a implementar clientes robustos dos serviços de nuvem, e um deles é o serviço Banco de Dados SQL do Azure. Você pode localizar tópicos dedicados a cada área nas quais o EntLib60 pode ajudar acessando:

* [A Enterprise Library 6 – abril de 2013](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)

A lógica de repetição para tratar erros transitórios é uma área em que EntLib60 pode auxiliar:

* [4 - Perseverança, os segredos de todos os triunfos: usando o Bloco de Aplicativos para Tratamento de Falhas Transitórias](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx)

> [!NOTE]
> O código-fonte para EntLib60 está disponível para [download](http://go.microsoft.com/fwlink/p/?LinkID=290898)público. A Microsoft não tem planos de fazer mais atualizações de manutenção ou de recursos no EntLib.
> 
> 

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>Classes do EntLib60 para erros transitórios e tentativas de repetição
As classes do EntLib60 a seguir são particularmente úteis para lógica de repetição. Todas elas estão contidas ou estão sob um nível do namespace **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

*No namespace **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:*

* **RetryPolicy** 
  
  * **ExecuteAction** 
* **ExponentialBackoff** 
* **SqlDatabaseTransientErrorDetectionStrategy** 
* **ReliableSqlConnection** 
  
  * **ExecuteCommand** 

No namespace **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

* **AlwaysTransientErrorDetectionStrategy** 
* **NeverTransientErrorDetectionStrategy** 

Estes são links para as informações sobre o EntLib60:

* [Download gratuito do livro: Guia do desenvolvedor para o Microsoft Enterprise Library, 2ª edição](http://www.microsoft.com/download/details.aspx?id=41145)
* Práticas recomendadas: [Diretrizes gerais para tentativas de repetição](../best-practices-retry-general.md) tem uma excelente discussão detalhada sobre lógica de repetição.
* Download NuGet de [Enterprise Library - bloqueio de aplicativos de manipulação de falhas transitórias 6.0](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: o bloqueio de log
* O bloqueio de log é uma solução altamente flexível e configurável que permite que você:
  
  * Crie e armazene mensagens de log em uma grande variedade de locais.
  * Categorize e filtre as mensagens.
  * Colete informações contextuais que sejam úteis para depuração e rastreamento, bem como para requisitos de auditoria e de log geral.
* O bloqueio de log abstrai a funcionalidade de log do destino de log de forma que o código do aplicativo seja consistente, independentemente do local e do tipo de armazenamento de log de destino.

Para obter detalhes, consulte: [5 - Tão fácil como evitar um log: usando o bloqueio de aplicativos de log](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx)

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>O EntLib60 é o código-fonte do método IsTransient
Em seguida, na classe **SqlDatabaseTransientErrorDetectionStrategy**, está o código-fonte C# do método **IsTransient**. O código-fonte explica quais erros são considerados transitórios e dignos de repetição, até abril de 2013.

Diversas linhas de **//comentário** foram removidas desta cópia para enfatizar a legibilidade.

```
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
* Para solucionar outros problemas comuns de conexão de Banco de Dados SQL, visite [Solucionar problemas de conexão no Banco de Dados SQL do Azure](sql-database-troubleshoot-common-connection-issues.md).
* [Pool de conexões do SQL Server (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx)
* [*Retrying* é uma biblioteca de novas tentativas para fins gerais licenciada do Apache 2.0, escrita em **Python**, para simplificar a tarefa de adicionar comportamento de nova tentativa a quase tudo.](https://pypi.python.org/pypi/retrying)

