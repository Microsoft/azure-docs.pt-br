---
title: Erros de conectividade transitórios-banco de dados do Azure para MySQL
description: Saiba como lidar com erros de conectividade transitórios e conecte-se com eficiência ao banco de dados do Azure para MySQL.
keywords: conexão do MySQL, Cadeia de conexão, problemas de conectividade, erro transitório, erro de conexão, conectar com eficiência
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 89673c14c38947dc5aeb91cacde1eb2755e84138
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542602"
---
# <a name="handle-transient-errors-and-connect-efficiently-to-azure-database-for-mysql"></a>Manipular erros transitórios e conectar-se com eficiência ao banco de dados do Azure para MySQL

Este artigo descreve como lidar com erros transitórios e conectar-se com eficiência ao banco de dados do Azure para MySQL.

## <a name="transient-errors"></a>Erros transitórios

Um erro transitório, também conhecido como uma falha transitória, é um erro que será resolvido por si só. Geralmente, esses erros manifestam como uma conexão para o servidor de banco de dados que está sendo descartado. Além disso, as novas conexões com um servidor não podem ser abertas. Os erros transitórios podem ocorrer, por exemplo, quando ocorre uma falha de hardware ou de rede. Outro motivo pode ser uma nova versão de um serviço PaaS que está sendo distribuído. A maioria desses eventos é automaticamente mitigada pelo sistema em menos de 60 segundos. Uma prática recomendada para projetar e desenvolver aplicativos na nuvem é esperar erros transitórios. Suponha que pode acontecer em qualquer componente a qualquer momento e ter a lógica apropriada em vigor para lidar com essas situações.

## <a name="handling-transient-errors"></a>Tratamento de erros transitórios

Os erros transitórios devem ser manipulados usando a lógica de repetição. Situações em que devem ser consideradas:

* Ocorre um erro quando você tentar abrir uma conexão
* Uma conexão ociosa é descartada no lado do servidor. Quando você tenta emitir um comando, ele não pode ser executado
* Uma conexão ativa que esteja executando um comando é descartada.

A primeira e a segunda ocorrência são razoavelmente diretas de lidar. Tente abrir a conexão novamente. Quando você tiver êxito, o erro transitório terá sido reduzido pelo sistema. Você pode usar seu Banco de Dados do Azure para MySQL novamente. Recomendamos ter esperas antes de tentar novamente a conexão. Desista se as tentativas iniciais falharem. Dessa forma, o sistema pode usar todos os recursos disponíveis para superar a situação de erro. Um bom padrão a seguir é:

* Aguarde cinco segundos até a primeira tentativa.
* Para cada próxima repetição, a espera aumenta exponencialmente, para até 60 segundos.
* Defina um número máximo de repetições no ponto em que seu aplicativo considera que a operação falhou.

Quando uma conexão com uma transação ativa falha, é mais difícil de lidar com a recuperação corretamente. Há dois casos: se a transação era somente leitura por natureza, é seguro para reabrir a conexão e tentar a transação novamente. Se, no entanto, a transação também estiver gravando no banco de dados, você deverá determinar se a transação foi revertida ou se foi bem-sucedida antes de o erro transitório acontecer. Nesse caso, você pode simplesmente não ter recebido a confirmação de confirmação do servidor de banco de dados.

Uma maneira de fazer isso, é gerar uma ID exclusiva no cliente que é usado para todas as tentativas. Você pode passar essa ID exclusiva como parte da transação para o servidor e armazená-los em uma coluna com uma restrição exclusiva. Dessa forma, com segurança, você pode repetir a transação. Ele será bem sucedido se a transação anterior tiver sido revertida e a ID exclusiva gerada pelo cliente ainda não existir no sistema. Ocorrerá uma falha indicando que uma violação de chave duplicada se a ID exclusiva foi armazenada anteriormente porque a transação anterior foi concluída com êxito.

Quando o programa se comunica com o Banco de Dados do Azure para MySQL por meio de um middleware de terceiros, pergunte ao fornecedor se o middleware contém lógica de repetição para erros transitórios.

Teste a lógica de repetição. Por exemplo, tente executar seu código ao escalar ou reduzir verticalmente os recursos de computação do banco de dados do Azure para o servidor MySQL. Seu aplicativo deve lidar com o breve tempo de inatividade encontrado durante a operação sem qualquer problema.

## <a name="connect-efficiently-to-azure-database-for-mysql"></a>Conectar-se com eficiência ao banco de dados do Azure para MySQL

As conexões de banco de dados são um recurso limitado, portanto, fazer uso efetivo do pool de conexões para acessar o banco de dados do Azure para MySQL otimiza o desempenho. A seção abaixo explica como usar o pool de conexões ou conexões persistentes para acessar com mais eficiência o banco de dados do Azure para MySQL.

## <a name="access-databases-by-using-connection-pooling-recommended"></a>Acessar bancos de dados usando o pool de conexões (recomendado)

O gerenciamento de conexões de banco de dados pode ter um impacto significativo no desempenho do aplicativo como um todo. Para otimizar o desempenho do seu aplicativo, o objetivo deve ser reduzir o número de vezes que as conexões são estabelecidas e o tempo para estabelecer conexões em caminhos de código-chave. É altamente recomendável usar o pool de conexões de banco de dados ou conexões persistentes para se conectar ao banco de dados do Azure para MySQL. O pool de conexões de banco de dados lida com a criação, o gerenciamento e a alocação de conexões de banco de dados. Quando um programa solicita uma conexão de banco de dados, ele prioriza a alocação de conexões de banco de dados ociosas existentes, em vez da criação de uma nova conexão. Depois que o programa terminar de usar a conexão de banco de dados, a conexão será recuperada na preparação para uso posterior, em vez de simplesmente ser fechada.

Para uma ilustração melhor, este artigo fornece [um trecho de código de exemplo](./sample-scripts-java-connection-pooling.md) que usa Java como exemplo. Para obter mais informações, consulte [apache common dbcp](https://commons.apache.org/proper/commons-dbcp/).

> [!NOTE]
> O servidor configura um mecanismo de tempo limite para fechar uma conexão que esteve em estado ocioso por algum tempo para liberar recursos. Certifique-se de configurar o sistema de verificação para garantir a eficácia de conexões persistentes quando você as estiver usando. Para obter mais informações, consulte [Configurar sistemas de verificação no lado do cliente para garantir a eficácia de conexões persistentes](concepts-connectivity.md#configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections).

## <a name="access-databases-by-using-persistent-connections-recommended"></a>Acessar bancos de dados usando conexões persistentes (recomendado)

O conceito de conexões persistentes é semelhante ao do pool de conexões. A substituição de conexões curtas com conexões persistentes exige apenas pequenas alterações no código, mas tem um grande efeito em termos de melhorar o desempenho em muitos cenários de aplicativos típicos.

## <a name="access-databases-by-using-wait-and-retry-mechanism-with-short-connections"></a>Acessar bancos de dados usando o mecanismo de espera e de repetição com conexões curtas

Se você tiver limitações de recursos, é altamente recomendável que você use o pool de banco de dados ou conexões persistentes para acessar bancos de dados. Se seu aplicativo usar conexões curtas e experimentar falhas de conexão ao abordar o limite superior no número de conexões simultâneas, você poderá tentar o mecanismo de espera e de repetição. Você pode definir um tempo de espera apropriado, com um tempo de espera mais curto após a primeira tentativa. Depois disso, você pode tentar aguardar eventos várias vezes.

## <a name="configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections"></a>Configurar mecanismos de verificação em clientes para confirmar a eficácia de conexões persistentes

O servidor configura um mecanismo de tempo limite para fechar uma conexão que está em um estado ocioso por algum tempo para liberar recursos. Quando o cliente acessa o banco de dados novamente, é equivalente a criar uma nova solicitação de conexão entre o cliente e o servidor. Para garantir a eficácia das conexões durante o processo de usá-las, configure um mecanismo de verificação no cliente. Conforme mostrado no exemplo a seguir, você pode usar o pool de conexões do Tomcat JDBC para configurar esse mecanismo de verificação.

Ao definir o parâmetro TestOnBorrow, quando há uma nova solicitação, o pool de conexões verifica automaticamente a eficácia de todas as conexões ociosas disponíveis. Se essa conexão for eficaz, o pool de conexão retornado de outra forma retornará a conexão. O pool de conexões cria uma nova conexão efetiva e a retorna. Esse processo garante que o banco de dados seja acessado com eficiência. 

Para obter informações sobre as configurações específicas, consulte o [documento introdução oficial do pool de conexões JDBC](https://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html#Common_Attributes). Você precisa definir principalmente os três parâmetros a seguir: TestOnBorrow (definido como true), ValidationQuery (definido como SELECT 1) e ValidationQueryTimeout (definido como 1). O código de exemplo específico é mostrado abaixo:

```java
public class SimpleTestOnBorrowExample {
      public static void main(String[] args) throws Exception {
          PoolProperties p = new PoolProperties();
          p.setUrl("jdbc:mysql://localhost:3306/mysql");
          p.setDriverClassName("com.mysql.jdbc.Driver");
          p.setUsername("root");
          p.setPassword("password");
            // The indication of whether objects will be validated by the idle object evictor (if any). 
            // If an object fails to validate, it will be dropped from the pool. 
            // NOTE - for a true value to have any effect, the validationQuery or validatorClassName parameter must be set to a non-null string. 
          p.setTestOnBorrow(true); 

            // The SQL query that will be used to validate connections from this pool before returning them to the caller.
            // If specified, this query does not have to return any data, it just can't throw a SQLException.
          p.setValidationQuery("SELECT 1");

            // The timeout in seconds before a connection validation queries fail. 
            // This works by calling java.sql.Statement.setQueryTimeout(seconds) on the statement that executes the validationQuery. 
            // The pool itself doesn't timeout the query, it is still up to the JDBC driver to enforce query timeouts. 
            // A value less than or equal to zero will disable this feature.
          p.setValidationQueryTimeout(1);
            // set other useful pool properties.
          DataSource datasource = new DataSource();
          datasource.setPoolProperties(p);

          Connection con = null;
          try {
            con = datasource.getConnection();
            // execute your query here
          } finally {
            if (con!=null) try {con.close();}catch (Exception ignore) {}
          }
      }
  }
```

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas de conexão no Banco de Dados do Azure para MySQL](howto-troubleshoot-common-connection-issues.md)
