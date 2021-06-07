---
title: Implantar o SQL do Azure no Edge com o Docker – SQL do Azure no Edge
description: Saiba mais sobre como implantar o SQL do Azure no Edge com o Docker
keywords: SQL no Edge, contêiner, docker
services: sql-edge
ms.service: sql-edge
ms.topic: quickstart
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: fce098767fffd36376399bbd9396699e3d9fbfd3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392071"
---
# <a name="deploy-azure-sql-edge-with-docker"></a>Implantar o SQL do Azure no Edge usando o Docker

Neste guia de início rápido, você usará o Docker para efetuar pull e executar a imagem de contêiner do SQL do Azure no Edge. Em seguida, você se conectará à ferramenta **sqlcmd** para criar seu primeiro banco de dados e executar consultas.

Essa imagem consiste no SQL do Azure no Edge com base no Ubuntu 18.04. Ela pode ser usada com o Docker Engine 1.8 ou superior no Linux ou no Docker para Mac/Windows.

## <a name="prerequisites"></a>Prerequisites

- O Docker Engine 1.8 ou superior em qualquer distribuição do Linux ou do Docker para Mac/Windows com suporte. Para obter mais informações, veja [Install Docker](https://docs.docker.com/engine/installation/) (Instalar o Docker). Como as imagens do SQL do Azure no Edge se baseiam no Ubuntu 18.04, é recomendável que você use um host do docker do Ubuntu 18.04.
- Driver de armazenamento **overlay2** do Docker. Esse é o padrão para a maioria dos usuários. Se você perceber que não está usando esse provedor de armazenamento e precisar alterar, confira as instruções e avisos na [documentação do Docker para configurar o overlay2](https://docs.docker.com/storage/storagedriver/overlayfs-driver/#configure-docker-with-the-overlay-or-overlay2-storage-driver).
- Mínimo de 10 GB de espaço em disco.
- Mínimo de 1 GB de RAM.
- [Requisitos de hardware para o SQL do Azure no Edge](./features.md#hardware-support).


## <a name="pull-and-run-the-container-image"></a>Efetuar pull e executar a imagem de contêiner

Antes de iniciar as etapas a seguir, verifique se você selecionou o shell de sua preferência (Bash, PowerShell ou cmd) na parte superior deste artigo.

1. Efetue pull da imagem de contêiner do SQL do Azure no Edge no Registro de Contêiner da Microsoft.

    - Efetuar pull da imagem de contêiner do SQL do Azure no Edge
        ```bash
        sudo docker pull mcr.microsoft.com/azure-sql-edge:latest 
        ```

> [!NOTE]
> Para os comandos de bash neste artigo, o `sudo` é usado. No macOS e no Windows, o sudo pode não ser necessário. No Linux, se você não quiser usar o sudo para executar o Docker, poderá configurar um grupo do docker e adicionar usuários a esse grupo. Para obter mais informações, veja [Etapas de pós-instalação para o Linux](https://docs.docker.com/engine/install/linux-postinstall/).

O comando anterior efetua pull das imagens de contêiner mais recentes do SQL do Azure no Edge. Para ver todas as imagens disponíveis, confira [a página do Docker hub azure-sql-egde](https://hub.docker.com/_/microsoft-azure-sql-edge).

2. Para executar a imagem de contêiner com o Docker, você pode usar o comando a seguir de um shell bash (Linux/macOS) ou do prompt de comando do PowerShell elevado.
    
    - Iniciar uma instância do SQL do Azure no Edge em execução como a camada de serviço Desenvolvedor
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```

    - Iniciar uma instância do SQL do Azure no Edge em execução como a camada de serviço Premium
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -e 'MSSQL_PID=Premium' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```
    > [!NOTE]
    > Se você estiver usando o PowerShell no Windows para executar esses comandos, use aspas duplas em vez de aspas simples.


    > [!NOTE]
    > A senha deverá seguir a política de senha padrão do Mecanismo de Banco de Dados do Microsoft SQL, caso contrário, o contêiner não poderá configurar o mecanismo SQL e deixará de funcionar. Por padrão, a senha deve ter pelo menos oito caracteres e conter caracteres de três dos quatro conjuntos a seguir: Letras maiúsculas, letras minúsculas, dígitos de base 10 e símbolos. É possível examinar o log de erros executando o comando [docker logs](https://docs.docker.com/engine/reference/commandline/logs/).
    
    A tabela a seguir fornece uma descrição dos parâmetros no exemplo de `docker run` anterior:

    | Parâmetro | Descrição |
    |-----|-----|
    | **-e "ACCEPT_EULA=Y"** |  Defina a variável **ACCEPT_EULA** com qualquer valor para confirmar sua aceitação dos [Termos de Licença](https://go.microsoft.com/fwlink/?linkid=2139274). Configuração necessária para a imagem do SQL do Azure no Edge. |
    | **-e "MSSQL_SA_PASSWORD=yourStrong(!)Password"** | Especifique a sua senha forte que tenha pelo menos oito caracteres e atenda aos [Requisitos de senha do SQL do Azure no Edge](/sql/relational-databases/security/password-policy). Configuração necessária para a imagem do SQL do Azure no Edge. |
    | **-p 1433:1433** | Mapeie uma porta TCP no ambiente do host (primeiro valor) para uma porta TCP no contêiner (segundo valor). Neste exemplo, o SQL do Azure no Edge está escutando na TCP 1433 no contêiner e isso é exposto para a porta 1433 no host. |
    | **--name azuresqledge** | Especifique um nome personalizado para o contêiner em vez de um nome gerado aleatoriamente. Se você executar mais de um contêiner, não será possível reutilizar esse mesmo nome. |
    | **-d** | Executar o contêiner em segundo plano (daemon) |

    Para obter uma lista completa de todas as variáveis de ambiente do SQL do Azure no Edge, confira [Configurar o SQL do Azure no Edge com variáveis de ambiente](configure.md#configure-by-using-environment-variables). Você também pode usar um [arquivo mssql.conf](configure.md#configure-by-using-an-mssqlconf-file) para configurar os Contêineres do SQL do Azure no Edge.

3. Para exibir seus contêineres do Docker, use o comando `docker ps`.
   
   ```bash
    sudo docker ps -a
   ```

4. Se a coluna **STATUS** mostrar o status **Up**, o SQL do Azure no Edge estará em execução no contêiner e estará ouvindo na porta especificada na coluna **PORTS**. Se a coluna **STATUS** do contêiner do SQL do Azure no Edge mostrar **Exited**, confira a seção Solução de problemas da Documentação do SQL do Azure no Edge.

    O parâmetro `-h` (nome do host) também é útil, mas para simplificar, ele não é usado neste tutorial. Ele altera o nome interno do contêiner para um valor personalizado. Este é o nome que será retornado na consulta Transact-SQL a seguir:

    ```sql
    SELECT @@SERVERNAME,
        SERVERPROPERTY('ComputerNamePhysicalNetBIOS'),
        SERVERPROPERTY('MachineName'),
        SERVERPROPERTY('ServerName')
    ```

    Configurar `-h` e `--name` com o mesmo valor é uma boa maneira de identificar facilmente o contêiner de destino.

5. Como etapa final, altere sua senha SA porque `SA_PASSWORD` está visível na saída `ps -eax` e armazenada na variável de ambiente de mesmo nome. Confira as etapas abaixo.

## <a name="change-the-sa-password"></a>Alterar a senha SA

A conta **SA** é um administrador do sistema na instância do SQL do Azure no Edge que é criada durante a configuração. Depois de criar o contêiner do SQL do Azure no Edge, a variável de ambiente `MSSQL_SA_PASSWORD` especificada é detectável executando `echo $SA_PASSWORD` no contêiner. Para fins de segurança, altere sua senha SA.

1. Escolha uma senha forte para usar no usuário de SA.

2. Use `docker exec` para executar **sqlcmd** para alterar a senha usando o Transact-SQL. No exemplo a seguir, substitua a senha antiga, `<YourStrong!Passw0rd>`e a nova senha, `<YourNewStrong!Passw0rd>`, por suas próprias senhas.

   ```bash
   sudo docker exec -it azuresqledge /opt/mssql-tools/bin/sqlcmd \
      -S localhost -U SA -P "<YourStrong@Passw0rd>" \
      -Q 'ALTER LOGIN SA WITH PASSWORD="<YourNewStrong@Passw0rd>"'
   ```

## <a name="connect-to-azure-sql-edge"></a>Conectar-se ao SQL do Azure no Edge

As etapas a seguir usam a ferramenta de linha de comando do SQL do Azure no Edge, **sqlcmd**, dentro do contêiner para se conectar ao SQL do Azure no Edge.

> [!NOTE]
> A ferramenta sqlcmd não está disponível na versão ARM64 dos contêineres do SQL no Edge.

1. Use o comando `docker exec -it` para iniciar um shell bash interativo dentro do contêiner em execução. No exemplo a seguir, `azuresqledge` é o nome especificado pelo parâmetro `--name` na criação do contêiner.

   ```bash
   sudo docker exec -it azuresqledge "bash"
   ```

2. Quando estiver dentro do contêiner, conecte-se localmente com a sqlcmd. A sqlcmd não está no caminho por padrão, portanto, você precisará especificar o caminho completo.

   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourNewStrong@Passw0rd>"
   ```

   > [!TIP]
   > É possível omitir a senha na linha de comando para receber uma solicitação para inseri-la.

3. Se isso funcionar, você será levado a um prompt de comando **sqlcmd**: `1>`.

## <a name="create-and-query-data"></a>Criar e consultar dados

As seções a seguir descrevem como usar o **sqlcmd** e o Transact-SQL para criar um novo banco de dados, adicionar dados e executar uma consulta simples.

### <a name="create-a-new-database"></a>Criar um novo banco de dados

As etapas a seguir criam um novo banco de dados denominado `TestDB`.

1. No prompt de comando **sqlcmd**, cole o seguinte comando Transact-SQL para criar um banco de dados de teste:

   ```sql
   CREATE DATABASE TestDB
   Go
   ```

2. Na próxima linha, grave uma consulta para retornar o nome de todos os bancos de dados do servidor:

   ```sql
   SELECT Name from sys.Databases
   Go
   ```

### <a name="insert-data"></a>Inserir dados

Em seguida, crie uma nova tabela, `Inventory`, e insira duas novas linhas.

1. No prompt de comando **sqlcmd**, altere o contexto para o novo banco de dados `TestDB`:

   ```sql
   USE TestDB
   ```

2. Criar nova tabela denominada `Inventory`:

   ```sql
   CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)
   ```

3. Inserir dados na nova tabela:

   ```sql
   INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);
   ```

4. Digite `GO` para executar os comandos anteriores:

   ```sql
   GO
   ```

### <a name="select-data"></a>Selecionar dados

Agora, execute uma consulta para retornar da tabela `Inventory`.

1. No prompt de comando **sqlcmd**, digite uma consulta que retorna linhas de tabela `Inventory` em que a quantidade é maior que 152:

   ```sql
   SELECT * FROM Inventory WHERE quantity > 152;
   ```

2. Execute o comando:

   ```sql
   GO
   ```

### <a name="exit-the-sqlcmd-command-prompt"></a>Saia do prompt de comando sqlcmd

1. Para encerrar a sessão **sqlcmd**, digite `QUIT`:

   ```sql
   QUIT
   ```

2. Para sair do prompt de comando interativo no contêiner, digite `exit`. O contêiner continuará a ser executado depois que você sair do shell bash interativo.

## <a name="connect-from-outside-the-container"></a> Conectar-se de fora do contêiner

Você também pode se conectar à instância do SQL do Azure no Edge no seu computador do Docker usando qualquer ferramenta externa do macOS, do Windows ou do Linux que seja compatível com conexões SQL. Para obter mais informações sobre como se conectar a um contêiner do SQL no Edge externamente, confira [Conectar e consultar o SQL do Azure no Edge](connect.md).

## <a name="remove-your-container"></a>Remover o contêiner

Se você quiser remover o contêiner do SQL do Azure no Edge usado neste tutorial, execute os seguintes comandos:

```bash
sudo docker stop azuresqledge
sudo docker rm azuresqledge
```

> [!WARNING]
> Parar e remover um contêiner permanentemente exclui todos os dados do SQL do Azure no Edge no contêiner. Se você precisar preservar os dados, [crie e copie um arquivo de backup fora do contêiner](backup-restore.md) ou use uma [técnica de persistência de dados do contêiner](configure.md#persist-your-data).

## <a name="next-steps"></a>Próximas etapas

- [Machine learning e inteligência artificial com o ONNX no SQL no Edge](onnx-overview.md).
- [Como criar uma solução de IoT de ponta a ponta com o SQL no Edge usando o IoT Edge](tutorial-deploy-azure-resources.md).
- [Streaming de dados no SQL do Azure no Edge](stream-data.md)