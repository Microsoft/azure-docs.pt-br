---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/30/2020
ms.author: alkohli
ms.openlocfilehash: 92ccb6127e624ace9e719ffd23324b3a1b971f72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89272183"
---
Em um dispositivo Azure Stack Edge que tem a função de computação configurada, você pode solucionar problemas ou monitorar o dispositivo usando dois conjuntos diferentes de comandos.

- Usando `iotedge` comandos. Esses comandos estão disponíveis para operações básicas para seu dispositivo.
- Usando `dkrdbe` comandos. Esses comandos estão disponíveis para um amplo conjunto de operações para seu dispositivo.

Para executar qualquer um dos conjuntos de comandos acima, você precisa [se conectar à interface do PowerShell](#connect-to-the-powershell-interface).

### <a name="use-iotedge-commands"></a>Usar `iotedge` comandos

Para ver uma lista de comandos disponíveis, [Conecte-se à interface do PowerShell](#connect-to-the-powershell-interface) e use a `iotedge` função.

```powershell
[10.100.10.10]: PS>iotedge -?                                                                                                                                                                                                 Usage: iotedge COMMAND

Commands:
   check
   list
   logs
   restart

[10.100.10.10]: PS>
```

A tabela a seguir tem uma breve descrição dos comandos disponíveis para o `iotedge` :

|.  |Descrição |
|---------|---------|
|`check`     | Executar verificações automatizadas para problemas comuns de configuração e conectividade       |
|`list`     | Listar módulos         |
|`logs`     | Buscar os logs de um módulo        |
|`restart`     | Parar e reiniciar um módulo         |


### <a name="use-dkrdbe-commands"></a>Usar `dkrdbe` comandos

Para ver uma lista de comandos disponíveis, [Conecte-se à interface do PowerShell](#connect-to-the-powershell-interface) e use a `dkrdbe` função.

```powershell
[10.100.10.10]: PS>dkrdbe -?
Usage: dkrdbe COMMAND

Commands:
   image [prune]
   images
   inspect
   login
   logout
   logs
   port
   ps
   pull
   start
   stats
   stop
   system [df]
   top

[10.100.10.10]: PS>
```
A tabela a seguir tem uma breve descrição dos comandos disponíveis para o `dkrdbe` :

|.  |Descrição |
|---------|---------|
|`image`     | Gerenciar imagens. Para remover imagens não usadas, use: `dkrdbe image prune -a -f`       |
|`images`     | Listar imagens         |
|`inspect`     | Retornar informações de baixo nível sobre objetos do Docker         |
|`login`     | Entrar em um registro do Docker         |
|`logout`     | Sair de um registro do Docker         |
|`logs`     | Buscar os logs de um contêiner        |
|`port`     | Listar mapeamentos de porta ou um mapeamento específico para o contêiner        |
|`ps`     | Listar contêineres        |
|`pull`     | Efetuar pull de uma imagem ou de um repositório de um registro         |
|`start`     | Iniciar um ou mais contêineres interrompidos         |
|`stats`     | Exibir uma transmissão ao vivo de estatísticas de uso de recursos de contêineres         |
|`stop`     | Parar um ou mais contêineres em execução        |
|`system`     | Gerenciar o Docker         |
|`top`     | Exibir os processos em execução de um contêiner         |

Para obter ajuda para qualquer comando disponível, use `dkrdbe <command-name> --help` .

Por exemplo, para entender o uso do `port` comando, digite:

```powershell
[10.100.10.10]: P> dkrdbe port --help

Usage:  dkr port CONTAINER [PRIVATE_PORT[/PROTO]]

List port mappings or a specific mapping for the container
[10.100.10.10]: P> dkrdbe login --help

Usage:  docker login [OPTIONS] [SERVER]

Log in to a Docker registry.
If no server is specified, the default is defined by the daemon.

Options:
  -p, --password string   Password
      --password-stdin    Take the password from stdin
  -u, --username string   Username
[10.100.10.10]: PS>
```

Os comandos disponíveis para a `dkrdbe` função usam os mesmos parâmetros que aqueles usados para os comandos normais do Docker. Para as opções e parâmetros usados com o comando Docker, acesse [usar a linha de comando do Docker](https://docs.docker.com/engine/reference/commandline/docker/).

### <a name="to-check-if-the-module-deployed-successfully"></a>Para verificar se o módulo foi implantado com êxito

Os módulos de computação são contêineres que têm uma lógica de negócios implementada. Para verificar se um módulo de computação foi implantado com êxito, execute o `ps` comando e verifique se o contêiner (correspondente ao módulo de computação) está em execução.

Para obter a lista de todos os contêineres (incluindo aqueles que estão em pausa), execute o `ps -a` comando.

```powershell
[10.100.10.10]: P> dkrdbe ps -a
CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
[10.100.10.10]: PS>
```

Se houvesse um erro na criação da imagem de contêiner ou durante a extração da imagem, execute `logs edgeAgent` .  `EdgeAgent` é o contêiner IoT Edge tempo de execução que é responsável por provisionar outros contêineres.

Como `logs edgeAgent` o despeja todos os logs, uma boa maneira de ver os erros recentes é usar a opção `--tail 20` .


```powershell
[10.100.10.10]: PS>dkrdbe logs edgeAgent --tail 20
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Connected socket /var/run/iotedge/mgmt.sock
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Sending request http://mgmt.sock/modules?api-version=2018-06-28
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Getting edge agent config...
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Obtained edge agent config
2019-02-28 23:38:23.469 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Edgelet.ModuleManagementHttpClient] - Received a valid Http response from unix:///var/run/iotedge/mgmt.soc
k for List modules
--------------------CUT---------------------
--------------------CUT---------------------
08:28.1007774+00:00","restartCount":0,"lastRestartTimeUtc":"2019-02-26T20:08:28.1007774+00:00","runtimeStatus":"running","version":"1.0","status":"running","restartPolicy":"always
","type":"docker","settings":{"image":"edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64","imageHash":"sha256:47778be0602fb077d7bc2aaae9b0760fbfc7c058bf4df192f207ad6cbb96f7cc","c
reateOptions":"{\"HostConfig\":{\"Binds\":[\"/home/hcsshares/share4-dl460:/home/input\",\"/home/hcsshares/share4-iot:/home/output\"]}}"},"env":{}}
2019-02-28 23:38:28.480 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Planners.HealthRestartPlanner] - HealthRestartPlanner created Plan, with 0 command(s).
```

### <a name="to-get-container-logs"></a>Para obter logs de contêiner

Para obter logs de um contêiner específico, primeiro liste o contêiner e, em seguida, obtenha os logs para o contêiner no qual você está interessado.

1. [Conecte-se à interface do PowerShell](#connect-to-the-powershell-interface).
2. Para obter a lista de contêineres em execução, execute o `ps` comando.

    ```powershell
    [10.100.10.10]: P> dkrdbe ps
    CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
    d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
    0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
    2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
    acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
    ```

3. Anote a ID do contêiner para o contêiner para o qual você precisa dos logs.

4. Para obter os logs de um contêiner específico, execute o `logs` comando fornecendo a ID do contêiner.

    ```powershell
    [10.100.10.10]: PS>dkrdbe logs d99e2f91d9a8
    02/26/2019 18:21:45: Info: Opening module client connection.
    02/26/2019 18:21:46: Info: Initializing with input: /home/input, output: /home/output.
    02/26/2019 18:21:46: Info: IoT Hub module client initialized.
    02/26/2019 18:22:24: Info: Received message: 1, SequenceNumber: 0 CorrelationId: , MessageId: 081886a07e694c4c8f245a80b96a252a Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\__Microsoft Data Box Edge__\\Upload\\Errors.xml","ShareName":"share4-dl460"}]
    02/26/2019 18:22:24: Info: Moving input file: /home/input/__Microsoft Data Box Edge__/Upload/Errors.xml to /home/output/__Microsoft Data Box Edge__/Upload/Errors.xml
    02/26/2019 18:22:24: Info: Processed event.
    02/26/2019 18:23:38: Info: Received message: 2, SequenceNumber: 0 CorrelationId: , MessageId: 30714d005eb048e7a4e7e3c22048cf20 Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\f [10]","ShareName":"share4-dl460"}]
    02/26/2019 18:23:38: Info: Moving input file: /home/input/f [10] to /home/output/f [10]
    02/26/2019 18:23:38: Info: Processed event.
    ```

### <a name="to-monitor-the-usage-statistics-of-the-device"></a>Para monitorar as estatísticas de uso do dispositivo

Para monitorar a memória, o uso da CPU e a e/s no dispositivo, use o `stats` comando.

1. [Conecte-se à interface do PowerShell](#connect-to-the-powershell-interface).
2. Execute o `stats` comando para desabilitar a transmissão ao vivo e efetuar pull somente do primeiro resultado.

   ```powershell
   dkrdbe stats --no-stream
   ```

   O exemplo a seguir mostra o uso desse cmdlet:

    ```
    [10.100.10.10]: P> dkrdbe stats --no-stream
    CONTAINER ID        NAME          CPU %         MEM USAGE / LIMIT     MEM %         NET I/O             BLOCK I/O           PIDS
    d99e2f91d9a8        movefile      0.0           24.4MiB / 62.89GiB    0.04%         751kB / 497kB       299kB / 0B          14
    0a06f6d605e9        filemove      0.00%         24.11MiB / 62.89GiB   0.04%         679kB / 481kB       49.5MB / 0B         14
    2f8a36e629db        edgeHub       0.18%         173.8MiB / 62.89GiB   0.27%         4.58MB / 5.49MB     25.7MB / 2.19MB     241
    acce59f70d60        edgeAgent     0.00%         35.55MiB / 62.89GiB   0.06%         2.23MB / 2.31MB     55.7MB / 332kB      14
    [10.100.10.10]: PS>
    ```

