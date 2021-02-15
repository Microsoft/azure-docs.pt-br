---
title: Solucionando problemas de implantações do Azure SQL Edge
description: Saiba mais sobre possíveis erros ao implantar o Azure SQL Edge
keywords: SQL Edge, solução de problemas, erros de implantação
services: sql-edge
ms.service: sql-edge
ms.topic: troubleshooting
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 517fed0dd9eb1736344546bde9f79e52ee17182f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333096"
---
# <a name="troubleshooting-azure-sql-edge-deployments"></a>Solucionando problemas de implantações do Azure SQL Edge 

Este artigo fornece informações sobre possíveis erros vistos ao implantar e usar contêineres do Azure SQL Edge e fornece técnicas de solução de problemas para ajudar a resolver esses problemas. 

O Azure SQL Edge dá suporte a dois modelos de implantação: 
- Implantação conectada por meio do Azure IoT Edge: o Azure SQL Edge está disponível no Azure Marketplace e pode ser implantado como um módulo para [Azure IOT Edge](../iot-edge/about-iot-edge.md). Para obter mais informações, consulte o artigo [Implantar o SQL do Azure no Edge](deploy-portal.md).<br>

- Implantação desconectada: as imagens de contêiner do Azure SQL Edge podem ser extraídas do Hub do Docker e implantadas como um contêiner autônomo do Docker ou em um cluster kubernetes. Para obter mais informações, consulte [implantar o Azure SQL Edge com o Docker](disconnected-deployment.md) e [implantar um contêiner do Azure SQL Edge no kubernetes](deploy-kubernetes.md).

## <a name="troubleshooting-iot-edge-device-and-deployments"></a>Solução de problemas IoT Edge dispositivos e implantações

Se você receber um erro ao implantar o SQL Edge por meio do Azure IoT Edge, verifique se o `iotedge` serviço está configurado e em execução corretamente. Os documentos a seguir podem ser úteis ao solucionar problemas relacionados a Azure IoT Edge:
- [Problemas comuns e resoluções para Azure IOT Edge](../iot-edge/troubleshoot-common-errors.md).
- [Solucionar problemas de seu dispositivo IoT Edge](../iot-edge/troubleshoot.md)

## <a name="docker-command-errors"></a>Erros de comando do Docker

Se você receber erros para qualquer comando `docker`, verifique se o serviço do Docker está em execução e tente executar com permissões elevadas.

Por exemplo, no Linux, você pode receber o seguinte erro ao executar comandos `docker`:

```output
Cannot connect to the Docker daemon. Is the docker daemon running on this host?
```

Se você receber esse erro no Linux, tente executar os mesmos comandos precedidos com `sudo`. Se isso falhar, verifique se o serviço do Docker está em execução e inicie-o, se necessário.

```bash
sudo systemctl status docker
sudo systemctl start docker
```

No Windows, verifique se você está iniciando o PowerShell ou o prompt de comando como Administrador.

## <a name="azure-sql-edge-container-startup-errors"></a>Erros de inicialização do contêiner do Azure SQL Edge

Se o contêiner do SQL Edge não for executado, tente os seguintes testes:

- Se você estiver usando Azure IoT Edge, verifique se as imagens do módulo foram baixadas com êxito e se as opções variáveis de ambiente e criação de contêiner foram especificadas corretamente no manifesto do módulo.

- Se você estiver usando a implantação baseada em Docker ou kubernetes, verifique se o `docker run` comando está formado corretamente. Para obter mais informações, consulte [implantar o Azure SQL Edge com o Docker](disconnected-deployment.md) e [implantar um contêiner do Azure SQL Edge no kubernetes](deploy-kubernetes.md).

- Se você receber um erro como `failed to create endpoint CONTAINER_NAME on network bridge. Error starting proxy: listen tcp 0.0.0.0:1433 bind: address already in use.`, isso indicará que você está tentando mapear a porta de contêiner 1433 para uma porta que já está em uso. Isso pode acontecer se você estiver executando o SQL Edge localmente no computador host. Isso também pode acontecer se você iniciar dois contêineres do SQL Edge e tentar mapeá-los para a mesma porta do host. Se isso acontecer, use o parâmetro `-p` para mapear a porta de contêiner 1433 para uma porta de host diferente. Por exemplo: 

    ```bash
    sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge-developer.
    ```

- Se você receber um erro como `Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.30tdout=1&tail=all: dial unix /var/run/docker.sock: connect: permission denied` ao tentar iniciar um contêiner, adicione o usuário ao grupo do Docker no Ubuntu. Em seguida, faça logoff e logon novamente, pois essa alteração afetará novas sessões. 

   ```bash
    usermod -aG docker $USER
   ```

- Verifique se há alguma mensagem de erro do contêiner.

   ```bash
   docker logs e69e056c702d
   ```

- Se você estiver usando qualquer software de gerenciamento de contêiner, verifique se ele dá suporte a processos de contêiner em execução como raiz. O processo sqlservr no contêiner é executado como raiz.

- Por padrão, os contêineres de borda do SQL do Azure são executados como um usuário não raiz chamado `mssql` . Se você estiver usando pontos de montagem ou volumes de dados para manter os dados, verifique se o `mssql` usuário tem as permissões apropriadas no volume. Para obter mais informações, consulte [Executar como usuário não raiz](configure.md#run-azure-sql-edge-as-non-root-user) e [manter dados](configure.md#persist-your-data).

- Se o contêiner do Docker do SQL Edge for encerrado imediatamente após a inicialização, verifique os logs do Docker. Se você estiver usando o PowerShell no Windows com o comando `docker run`, use aspas duplas em vez de aspas simples. Com o PowerShell Core, use aspas simples.

- Examine os [logs de erros do SQL Edge](#errorlogs).

## <a name="sql-edge-connection-failures"></a>Falhas de conexão do SQL Edge

Se você não conseguir se conectar à instância do SQL Edge em execução no seu contêiner, tente os seguintes testes:

- Verifique se o contêiner do SQL Edge está em execução examinando a coluna **status** da `docker ps -a` saída. Caso contrário, use `docker start <Container ID>` para iniciá-lo.

- Se você mapeou para uma porta de host não padrão (não 1433), verifique se está especificando a porta na cadeia de conexão. Você pode ver o mapeamento de porta na coluna **PORTAS** da saída `docker ps -a`. Para obter mais informações sobre como se conectar ao Azure SQL Edge, consulte [conectar e consultar o Azure SQL Edge](connect.md)

- Se você já tiver implantado o SQL Edge com o volume de dados mapeado ou o contêiner de volume de dados e agora estiver usando o volume de dados mapeado existente ou o contêiner de volume de dados, o SQL Edge ignorará o valor da `MSSQL_SA_PASSWORD` variável de ambiente. Em vez disso, a senha de usuário SA configurada anteriormente é usada. Isso acontece porque o SQL Edge reutiliza os arquivos de bancos de dados mestres existentes no volume mapeado ou no contêiner de volume de dados. Se você encontrar esse problema, poderá usar as seguintes opções:

    - Conecte-se usando a senha usada anteriormente, se ela ainda estiver disponível.
    - Configure o SQL Edge para usar um volume mapeado ou contêiner de volume de dados diferente.
    - Remova os arquivos de banco de dados mestre existentes (Master. MDF e mastlog. MDF) do volume mapeado ou do contêiner de volume de data.

- Examine os [logs de erros do SQL Edge](#errorlogs).

## <a name="sql-edge-setup-and-error-logs"></a><a id="errorlogs"></a> Configuração do SQL Edge e logs de erros

Por padrão, os logs de erros do SQL Edge estão presentes no diretório **/var/opt/MSSQL/log** dentro do contêiner e podem ser acessados usando qualquer uma das seguintes maneiras:

- Se você montou um diretório de host para **/var/opt/mssql** quando criou o contêiner, poderá procurar no subdiretório de **log** no caminho mapeado no host.
- Usando um prompt de comando interativo para se conectar ao contêiner. Se o contêiner não estiver em execução, primeiro inicie-o. Em seguida, use um prompt de comando interativo para inspecionar os logs. Obtenha a ID do contêiner executando o comando `docker ps`.

    ```bash
    docker start <ContainerID>
    docker exec -it <ContainerID> "/bin/bash"
    ```

    Na sessão de Bash dentro de seu contêiner, execute os seguintes comandos:

    ```bash
    cd /var/opt/mssql/log
    cat errorlog
    ```
- Se o contêiner do SQL Edge estiver em execução e você for capaz de se conectar à instância usando as ferramentas de cliente, você poderá usar o procedimento armazenado `sp_readerrorlog` para ler o conteúdo do log de erros do SQL Edge.

## <a name="execute-commands-in-a-container"></a>Executar comandos em um contêiner

Se você tiver um contêiner em execução, poderá executar comandos dentro do contêiner de um terminal de host.

Para obter a ID do contêiner, execute:

```bash
docker ps -a
```

Para iniciar um terminal de Bash na execução do contêiner:

```bash
docker exec -it <Container ID> /bin/bash
```

Agora, você pode executar comandos como se estivesse executando-os no terminal dentro do contêiner. Quando terminar, digite `exit`. Isso é encerrado na sessão de comando interativo, mas o contêiner continua a ser executado.

### <a name="enabling-verbose-logging"></a>Habilitando o log detalhado

Se o nível de log padrão do mecanismo de streaming não fornecer informações suficientes, o log de depuração do mecanismo de streaming poderá ser habilitado no SQL Edge. Para habilitar o log de depuração, adicione a `RuntimeLogLevel=debug` variável de ambiente à implantação do SQL Edge. Depois de habilitar o log de depuração, tente reproduzir o problema e verifique os logs em busca de mensagens ou exceções relevantes. 

> [!NOTE]
> A opção de log detalhado só deve ser usada para solução de problemas e não para carga de trabalho de produção regular. 


## <a name="next-steps"></a>Próximas etapas

- [Machine Learning e inteligência artificial com ONNX no SQL Edge](onnx-overview.md)
- [Streaming de dados no SQL do Azure no Edge](stream-data.md)
- [Retenção e limpeza de dados](data-retention-overview.md)
- [Preenchendo intervalos de tempo e entrada valores ausentes](imputing-missing-values.md)







