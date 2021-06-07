---
title: Solução de erros comuns – Banco de Dados do Azure para MySQL
description: Saiba como solucionar problemas de erros comuns de migração encontrados pelos novos usuários do serviço Banco de Dados do Azure para MySQL
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/20/2020
ms.openlocfilehash: 3bfcfee0f5dab2d978eb1856bdc915c270d43ed6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109787"
---
# <a name="commonly-encountered-errors-during-or-post-migration-to-azure-database-for-mysql-service"></a>Erros frequentemente encontrados durante ou após a migração para o serviço do Banco de Dados do Azure para MySQL

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

O Banco de Dados do Azure para MySQL é um serviço totalmente gerenciado ativado pela versão da comunidade do MySQL. A experiência do MySQL em um ambiente de serviço gerenciado pode ser diferente da execução do MySQL no próprio ambiente. Neste artigo, você verá alguns dos erros comuns que os usuários podem receber ao fazer a migração para o serviço Banco de Dados do Azure para MySQL ou fazer o desenvolvimento nele pela primeira vez.

## <a name="common-connection-errors"></a>Erros de conexão comuns

#### <a name="error-1184-08s01-aborted-connection-22-to-db-db-name-user-user-host-hostip-init_connect-command-failed"></a>ERRO 1184 (08S01): conexão 22 com db: 'db-name' usuário: 'user' host: 'hostIP' anulada (falha no comando init_connect)
O erro acima ocorre após o logon bem-sucedido, mas antes da execução de qualquer comando quando a sessão é estabelecida. A mensagem acima indica que você definiu um valor incorreto do parâmetro de servidor init_connect, que está causando a falha na inicialização da sessão.

Há alguns parâmetros do servidor como require_secure_transport que não são compatíveis com o nível de sessão. Portanto, a tentativa de alterar os valores desses parâmetros usando init_connect poderá resultar no erro 1184 durante a conexão com o servidor MySQL, conforme mostrado abaixo

mysql> show databases; ERROR 2006 (HY000): O servidor MySQL desapareceu. Sem conexão. Tentando reconectar... ID da conexão:    64897 Banco de dados atual: *** NENHUM *** ERRO 1184 (08S01): a conexão 22 com o BD foi anulada: usuário 'db-name': host 'user': 'hostIP' (falha no comando init_connect)

**Resolução**: será necessário redefinir o valor de init_connect na guia Parâmetros do servidor do portal do Azure e definir somente os parâmetros do servidor compatíveis usando o parâmetro init_connect. 


## <a name="errors-due-to-lack-of-super-privilege-and-dba-role"></a>Erros devido à ausência do privilégio SUPER e da função DBA

Não há suporte para o privilégio SUPER nem para a função DBA no serviço. Como resultado, você poderá receber alguns erros comuns listados abaixo:

#### <a name="error-1419-you-do-not-have-the-super-privilege-and-binary-logging-is-enabled-you-might-want-to-use-the-less-safe-log_bin_trust_function_creators-variable"></a>ERRO 1419: você não tem o privilégio SUPER e o log binário está habilitado (o *ideal* é usar a variável log_bin_trust_function_creators menos segura)

O erro acima pode ocorrer durante a criação de uma função ou um gatilho, conforme mostrado abaixo, ou durante a importação de um esquema. As instruções DDL como CREATE FUNCTION ou CREATE TRIGGER são gravadas no log binário, de modo que a réplica secundária possa executá-las. O thread SQL de réplica tem privilégios completos, que pode ser explorado para elevar os privilégios. Para se proteger contra esse perigo nos servidores que têm o log binário habilitado, o mecanismo MySQL exige que os criadores de funções armazenadas tenham o privilégio SUPER, além do privilégio habitual CREATE ROUTINE que é necessário. 

```sql
CREATE FUNCTION f1(i INT)
RETURNS INT
DETERMINISTIC
READS SQL DATA
BEGIN
  RETURN i;
END;
```

**Resolução**: para resolver o erro, defina log_bin_trust_function_creators como 1 na folha [Parâmetros do servidor](howto-server-parameters.md) no portal, execute as instruções DDL ou importe o esquema para criar os objetos desejados. Você pode continuar mantendo o log_bin_trust_function_creators como 1 para o seu servidor para evitar o erro no futuro. Nossa recomendação é definir log_bin_trust_function_creators, pois o risco de segurança realçado na [documentação da comunidade do MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) é mínimo no serviço do BD do Azure para MySQL, já que o log do compartimento não é exposto a nenhuma ameaça.

#### <a name="error-1227-42000-at-line-101-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation-operation-failed-with-exitcode-1"></a>ERRO 1227 (42000) na linha 101: acesso negado; você precisará ter (pelo menos um dos) privilégios SUPER para executar essa operação. Falha na operação com o código de saída 1

O erro acima pode ocorrer durante a importação de um arquivo de despejo ou a criação de um procedimento que contenha [definidores](https://dev.mysql.com/doc/refman/5.7/en/create-procedure.html). 

**Resolução:**  para resolver esse erro, o usuário administrador pode conceder privilégios para criar ou executar procedimentos executando o comando GRANT, como nos seguintes exemplos:

```sql
GRANT CREATE ROUTINE ON mydb.* TO 'someuser'@'somehost';
GRANT EXECUTE ON PROCEDURE mydb.myproc TO 'someuser'@'somehost';
```
Como alternativa, você pode substituir os definidores pelo nome do usuário administrador que está executando o processo de importação, conforme mostrado abaixo.

```sql
DELIMITER;;
/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
DELIMITER;;

/* Modified to */

DELIMITER ;;
/*!50003 CREATE*/ /*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
DELIMITER ;
```
#### <a name="error-1227-42000-at-line-295-access-denied-you-need-at-least-one-of-the-super-or-set_user_id-privileges-for-this-operation"></a>ERRO 1227 (42000) na linha 295: acesso negado; você precisará ter (pelo menos um dos) privilégios SUPER ou SET_USER_ID para executar essa operação

O erro acima poderá ocorrer durante a execução de CREATE VIEW com instruções DEFINER como parte da importação de um arquivo de despejo ou da execução de um script. O Banco de Dados do Azure para MySQL não permite privilégios SUPER ou SET_USER_ID para nenhum usuário. 

**Resolução**: 
* use o usuário do definidor para executar CREATE VIEW, se possível. É provável que haja muitas exibições com diferentes definidores tendo permissões diferentes. Portanto, talvez isso não seja viável.  OU
* Edite o arquivo de despejo ou o script CREATE VIEW e remova a instrução DEFINER= do arquivo de despejo OU 
* Edite o arquivo de despejo ou o script CREATE VIEW e substitua os valores do definidor pelo usuário com as permissões de administrador que está executando a importação ou execute o arquivo de script.

> [!Tip] 
> Use sed ou o PERL para modificar um arquivo de despejo ou um script SQL para substituir a instrução DEFINER=

#### <a name="error-1227-42000-at-line-18-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation"></a>ERRO 1227 (42000) na linha 18: acesso negado; você precisará ter (pelo menos um dos) privilégios SUPER para executar essa operação

O erro acima poderá ocorrer se você estiver tentando importar o arquivo de despejo do servidor MySQL com o GTID habilitado para o servidor de destino do Banco de Dados do Azure para MySQL. Mysqldump adiciona a instrução SET @@SESSION.sql_log_bin=0 a um arquivo de despejo de um servidor em que os GTIDs estão em uso, o que desabilita o log binário enquanto o arquivo de despejo está sendo recarregado.

**Resolução**: para resolver esse erro durante a importação, remova ou comente as linhas abaixo no arquivo mysqldump e execute a importação novamente para garantir que ela seja bem-sucedida. 

SET @MYSQLDUMP_TEMP_LOG_BIN = @@SESSION.SQL_LOG_BIN; SET @@SESSION.SQL_LOG_BIN= 0; SET @@GLOBAL.GTID_PURGED=''; SET @@SESSION.SQL_LOG_BIN = @MYSQLDUMP_TEMP_LOG_BIN;

## <a name="common-connection-errors-for-server-admin-login"></a>Erros comuns de conexão do logon de administrador do servidor

Quando um servidor do Banco de Dados do Azure para MySQL é criado, um logon de administrador do servidor é fornecido pelo usuário final durante a criação do servidor. O logon de administrador do servidor permite que você crie bancos de dados, adicione novos usuários e conceda permissões. Se o logon de administrador do servidor for excluído, suas permissões forem revogadas ou sua senha for alterada, você poderá começar a ver erros de conexões no aplicativo durante as conexões. Estes são alguns dos erros comuns

#### <a name="error-1045-28000-access-denied-for-user-usernameip-address-using-password-yes"></a>ERRO 1045 (28000): Acesso negado para o usuário 'nome de usuário'@'endereço IP' (usando a senha: YES)

O erro acima ocorre se:

* O nome de usuário não existe
* O nome de usuário foi excluído
* a senha foi alterada ou redefinida.

**Resolução**: 
* confirme se o "nome de usuário" existe como um usuário válido no servidor ou se ele foi excluído acidentalmente. Execute a seguinte consulta fazendo logon no usuário do Banco de Dados do Azure para MySQL:
  ```sql
  select user from mysql.user;
  ```
* Se você não pode fazer logon no MySQL para executar a própria consulta, recomendamos [redefinir a senha de administrador usando o portal do Azure](howto-create-manage-server-portal.md). A opção de redefinição de senha do portal do Azure ajudará a recriar o usuário, redefinir a senha e restaurar as permissões de administrador, o que permitirá que você faça logon usando o administrador do servidor e execute outras operações.

## <a name="next-steps"></a>Próximas etapas
Caso não encontre a resposta que estava procurando, considere o seguinte:

- Poste sua pergunta na [página de perguntas do Microsoft Q&A](/answers/topics/azure-database-mysql.html) ou no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
- Envie um email para a Equipe do Banco de Dados do Azure para MySQL [@Ask Azure DB para MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com). Esse endereço de email não é um alias de suporte técnico.
- Para entrar em contato com o Suporte do Azure, [crie um tíquete no portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Para corrigir um problema com sua conta, apresente uma [solicitação de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.
- Para fornecer comentários ou solicitar novos recursos, crie uma entrada por meio do [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).
