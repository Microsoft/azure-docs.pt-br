---
title: Conectar-se ao redirecionamento-banco de dados do Azure para MariaDB
description: Este artigo descreve como você pode configurar seu aplicativo para se conectar ao banco de dados do Azure para MariaDB com o redirecionamento.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/8/2020
ms.openlocfilehash: 3f26de72839fcaa39bff4d827aba757721736934
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664895"
---
# <a name="connect-to-azure-database-for-mariadb-with-redirection"></a>Conectar-se ao banco de dados do Azure para MariaDB com redirecionamento

Este tópico explica como conectar um aplicativo ao seu banco de dados do Azure para MariaDB Server com o modo de redirecionamento. O redirecionamento visa reduzir a latência de rede entre aplicativos cliente e servidores MariaDB, permitindo que os aplicativos se conectem diretamente aos nós do servidor back-end.

## <a name="before-you-begin"></a>Antes de começar
Entre no [portal do Azure](https://portal.azure.com). Crie um banco de dados do Azure para o servidor MariaDB com a versão 10,2 ou 10,3 do mecanismo. 

Para obter detalhes, consulte como criar um banco de dados do Azure para o servidor MariaDB usando o [portal do Azure](quickstart-create-mariadb-server-database-using-azure-portal.md) ou [CLI do Azure](quickstart-create-mariadb-server-database-using-azure-cli.md).

## <a name="enable-redirection"></a>Habilitar redirecionamento

No banco de dados do Azure para o servidor MariaDB, configure o `redirect_enabled` parâmetro para `ON` para permitir conexões com o modo de redirecionamento. Para atualizar esse parâmetro de servidor, use o [portal do Azure](howto-server-parameters.md) ou [CLI do Azure](howto-configure-server-parameters-cli.md).

## <a name="php"></a>PHP

O suporte para redirecionamento nos aplicativos PHP está disponível por meio da extensão [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure), desenvolvida pela Microsoft. 

A extensão mysqlnd_azure está disponível para ser adicionada aos aplicativos PHP por meio do PECL e é altamente recomendável instalar e configurar a extensão por meio do [pacote de PECL](https://pecl.php.net/package/mysqlnd_azure) publicado oficialmente.

> [!IMPORTANT]
> No momento, o suporte para redirecionamento na extensão [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) do PHP está na versão prévia.

### <a name="redirection-logic"></a>Lógica de redirecionamento

>[!IMPORTANT]
> A lógica/comportamento de redirecionamento a partir da versão 1.1.0 foi atualizada e **é recomendável usar a versão 1.1.0 +** .

O comportamento de redirecionamento é determinado pelo valor de `mysqlnd_azure.enableRedirect`. A tabela a seguir descreve o comportamento de redirecionamento com base no valor desse parâmetro a partir da **versão 1.1.0+** .

Se você estiver usando uma versão mais antiga da extensão mysqlnd_azure (versão 1.0.0-1.0.3), o comportamento de redirecionamento será determinado pelo valor de `mysqlnd_azure.enabled`. Os valores válidos são `off` (atua da mesma forma que o comportamento descrito na tabela abaixo) e `on` (atua como `preferred` na tabela abaixo).  

|**mysqlnd_azure.enableRedirect value**| **Comportamento**|
|----------------------------------------|-------------|
|`off` ou `0`|O redirecionamento não será usado. |
|`on` ou `1`|- Se a conexão não usar SSL no lado do driver, nenhuma conexão será feita. O erro a seguir será retornado: *"mysqlnd_azure. enableRedirect está ativado, mas a opção SSL não está definida na cadeia de conexão. O redirecionamento só é possível com SSL."*<br>-Se o SSL for usado no lado do driver, mas não houver suporte para o redirecionamento no servidor, a primeira conexão será anulada e o seguinte erro será retornado: *"a conexão foi anulada porque o redirecionamento não está habilitado no servidor MariaDB ou o pacote de rede não atende ao protocolo de redirecionamento".*<br>-Se o servidor MariaDB oferecer suporte ao redirecionamento, mas a conexão redirecionada falhar por algum motivo, também anulará a primeira conexão de proxy. Retorne o erro da conexão redirecionada.|
|`preferred` ou `2`<br> (valor padrão)|- mysqlnd_azure usará o redirecionamento, se possível.<br>- Se a conexão não usar SSL no lado do driver, o servidor não oferecer suporte ao redirecionamento ou a conexão redirecionada falhar ao se conectar por qualquer motivo não fatal, enquanto a conexão proxy ainda for válida, ela retornará à primeira conexão de proxy.|

As seções subsequentes do documento descrevem como instalar a extensão `mysqlnd_azure` usando PECL e definir o valor desse parâmetro.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Pré-requisitos 
- PHP versões 7.2.15+ e 7.3.2+
- PHP PEAR 
- php-mysql
- Banco de dados do Azure para servidor MariaDB

1. Instale [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) com [PECL](https://pecl.php.net/package/mysqlnd_azure). É recomendável usar a versão 1.1.0+.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Localize o diretório da extensão (`extension_dir`), fazendo o seguinte:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Altere os diretórios para a pasta retornada e verifique se `mysqlnd_azure.so` está localizado nessa pasta. 

4. Localize a pasta de arquivos .ini, fazendo o seguinte: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Altere os diretórios para essa pasta retornada. 

6. Crie um novo arquivo .ini para `mysqlnd_azure`. Verifique se a ordem alfabética do nome é posterior à do mysqnld, pois os módulos são carregados de acordo com a ordem dos nomes dos arquivos ini. Por exemplo, se o .ini `mysqlnd` for nomeado como `10-mysqlnd.ini`, nomeie o mysqlnd ini como `20-mysqlnd-azure.ini`.

7. No novo arquivo .ini, adicione as linhas a seguir para habilitar o redirecionamento.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Pré-requisitos 
- PHP versões 7.2.15+ e 7.3.2+
- php-mysql
- Banco de dados do Azure para servidor MariaDB

1. Determine se você está executando uma versão x64 ou x86 do PHP, executando o comando a seguir:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Baixe a versão x64 ou x86 correspondente do DLL [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) no [PECL](https://pecl.php.net/package/mysqlnd_azure) que corresponde à sua versão do PHP. É recomendável usar a versão 1.1.0+.

3. Extraia o arquivo zip e encontre o DLL nomeado como `php_mysqlnd_azure.dll`.

4. Localize o diretório da extensão (`extension_dir`), executando o comando a seguir:

    ```cmd
    php -i | find "extension_dir"
    ```

5. Copie o arquivo `php_mysqlnd_azure.dll` no diretório retornado na etapa 4. 

6. Localize a pasta PHP que contém o arquivo de `php.ini`, usando o comando a seguir:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Modifique o arquivo de `php.ini` e adicione as linhas extras a seguir para habilitar o redirecionamento. 

    Na seção Extensões dinâmicas: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    Na seção Configurações do módulo:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>Confirme o redirecionamento

Você também pode confirmar se o redirecionamento está configurado com o exemplo de código PHP abaixo. Crie um arquivo PHP chamado `mysqlConnect.php` e cole o código abaixo. Atualize o nome do servidor, o nome de usuário e a senha com seus próprios dados. 
 
 ```php
<?php
$host = '<yourservername>.mariadb.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enableRedirect: ", ini_get("mysqlnd_azure.enableRedirect"), "\n";
  $db = mysqli_init();
  //The connection must be configured with SSL for redirection test
  $link = mysqli_real_connect ($db, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL);
  if (!$link) {
     die ('Connect error (' . mysqli_connect_errno() . '): ' . mysqli_connect_error() . "\n");
  }
  else {
    echo $db->host_info, "\n"; //if redirection succeeds, the host_info will differ from the hostname you used used to connect
    $res = $db->query('SHOW TABLES;'); //test query with the connection
    print_r ($res);
    $db->close();
  }
?>
 ```

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre cadeias de conexão, confira [Cadeias de Conexão](howto-connection-string.md).
