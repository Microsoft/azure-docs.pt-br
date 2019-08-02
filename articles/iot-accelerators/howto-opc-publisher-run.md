---
title: Executar o Publicador OPC – Azure | Microsoft Docs
description: Executar o OPC Publisher
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 3b386171afc7916e5e803c39a9c7b3520752e6fd
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603743"
---
# <a name="run-opc-publisher"></a>Executar o OPC Publisher

Este artigo descreve como executar e depurar o Publicador OPC. Ele também aborda considerações de desempenho e memória.

## <a name="command-line-options"></a>Opções de linha de comando

Uso do aplicativo é mostrado por meio da opção de linha de comando `--help` como segue:

```sh/cmd
Current directory is: /appdata
Log file is: <hostname>-publisher.log
Log level is: info

OPC Publisher V2.3.0
Informational version: V2.3.0+Branch.develop_hans_methodlog.Sha.0985e54f01a0b0d7f143b1248936022ea5d749f9

Usage: opcpublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]

OPC Edge Publisher to subscribe to configured OPC UA servers and send telemetry to Azure IoT Hub.
To exit the application, just press CTRL-C while it is running.

applicationname: the OPC UA application name to use, required
                  The application name is also used to register the publisher under this name in the
                  IoT Hub device registry.

IoT Hubconnectionstring: the IoT Hub owner connectionstring, optional

There are a couple of environment variables which can be used to control the application:
_HUB_CS: sets the IoT Hub owner connectionstring
_GW_LOGP: sets the filename of the log file to use
_TPC_SP: sets the path to store certificates of trusted stations
_GW_PNFP: sets the filename of the publishing configuration file

Command line arguments overrule environment variable settings.

Options:
      --pf, --publishfile=VALUE
                              the filename to configure the nodes to publish.
                                Default: '/appdata/publishednodes.json'
      --tc, --telemetryconfigfile=VALUE
                              the filename to configure the ingested telemetry
                                Default: ''
  -s, --site=VALUE           the site OPC Publisher is working in. if specified
                                this domain is appended (delimited by a ':' to
                                the 'ApplicationURI' property when telemetry is
                                sent to IoT Hub.
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --ic, --iotcentral     publisher will send OPC UA data in IoTCentral
                                compatible format (DisplayName of a node is used
                                as key, this key is the Field name in IoTCentral)
                                . you need to ensure that all DisplayName's are
                                unique. (Auto enables fetch display name)
                                Default: False
      --sw, --sessionconnectwait=VALUE
                              specify the wait time in seconds publisher is
                                trying to connect to disconnected endpoints and
                                starts monitoring unmonitored items
                                Min: 10
                                Default: 10
      --mq, --monitoreditemqueuecapacity=VALUE
                              specify how many notifications of monitored items
                                can be stored in the internal queue, if the data
                                can not be sent quick enough to IoT Hub
                                Min: 1024
                                Default: 8192
      --di, --diagnosticsinterval=VALUE
                              shows publisher diagnostic info at the specified
                                interval in seconds (need log level info).
                                -1 disables remote diagnostic log and diagnostic
                                output
                                0 disables diagnostic output
                                Default: 0
      --ns, --noshutdown=VALUE
                              same as runforever.
                                Default: False
      --rf, --runforever     publisher can not be stopped by pressing a key on
                                the console, but will run forever.
                                Default: False
      --lf, --logfile=VALUE  the filename of the logfile to use.
                                Default: './<hostname>-publisher.log'
      --lt, --logflushtimespan=VALUE
                              the timespan in seconds when the logfile should be
                                flushed.
                                Default: 00:00:30 sec
      --ll, --loglevel=VALUE the loglevel to use (allowed: fatal, error, warn,
                                info, debug, verbose).
                                Default: info
        --ih, --IoT Hubprotocol=VALUE
                              the protocol to use for communication with IoT Hub (
                                allowed values: Amqp, Http1, Amqp_WebSocket_Only,
                                  Amqp_Tcp_Only, Mqtt, Mqtt_WebSocket_Only, Mqtt_
                                Tcp_Only) or IoT EdgeHub (allowed values: Mqtt_
                                Tcp_Only, Amqp_Tcp_Only).
                                Default for IoT Hub: Mqtt_WebSocket_Only
                                Default for IoT EdgeHub: Amqp_Tcp_Only
      --ms, --IoT Hubmessagesize=VALUE
                              the max size of a message which can be send to
                                IoT Hub. when telemetry of this size is available
                                it will be sent.
                                0 will enforce immediate send when telemetry is
                                available
                                Min: 0
                                Max: 262144
                                Default: 262144
      --si, --IoT Hubsendinterval=VALUE
                              the interval in seconds when telemetry should be
                                send to IoT Hub. If 0, then only the
                                IoT Hubmessagesize parameter controls when
                                telemetry is sent.
                                Default: '10'
      --dc, --deviceconnectionstring=VALUE
                              if publisher is not able to register itself with
                                IoT Hub, you can create a device with name <
                                applicationname> manually and pass in the
                                connectionstring of this device.
                                Default: none
  -c, --connectionstring=VALUE
                              the IoT Hub owner connectionstring.
                                Default: none
      --hb, --heartbeatinterval=VALUE
                              the publisher is using this as default value in
                                seconds for the heartbeat interval setting of
                                nodes without
                                a heartbeat interval setting.
                                Default: 0
      --sf, --skipfirstevent=VALUE
                              the publisher is using this as default value for
                                the skip first event setting of nodes without
                                a skip first event setting.
                                Default: False
      --pn, --portnum=VALUE  the server port of the publisher OPC server
                                endpoint.
                                Default: 62222
      --pa, --path=VALUE     the enpoint URL path part of the publisher OPC
                                server endpoint.
                                Default: '/UA/Publisher'
      --lr, --ldsreginterval=VALUE
                              the LDS(-ME) registration interval in ms. If 0,
                                then the registration is disabled.
                                Default: 0
      --ol, --opcmaxstringlen=VALUE
                              the max length of a string opc can transmit/
                                receive.
                                Default: 131072
      --ot, --operationtimeout=VALUE
                              the operation timeout of the publisher OPC UA
                                client in ms.
                                Default: 120000
      --oi, --opcsamplinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds to request the servers to sample
                                the nodes with this interval
                                this value might be revised by the OPC UA
                                servers to a supported sampling interval.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a negative value will set the sampling interval
                                to the publishing interval of the subscription
                                this node is on.
                                0 will configure the OPC UA server to sample in
                                the highest possible resolution and should be
                                taken with care.
                                Default: 1000
      --op, --opcpublishinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds for the publishing interval setting
                                of the subscriptions established to the OPC UA
                                servers.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a value less than or equal zero will let the
                                server revise the publishing interval.
                                Default: 0
      --ct, --createsessiontimeout=VALUE
                              specify the timeout in seconds used when creating
                                a session to an endpoint. On unsuccessful
                                connection attemps a backoff up to 5 times the
                                specified timeout value is used.
                                Min: 1
                                Default: 10
      --ki, --keepaliveinterval=VALUE
                              specify the interval in seconds the publisher is
                                sending keep alive messages to the OPC servers
                                on the endpoints it is connected to.
                                Min: 2
                                Default: 2
      --kt, --keepalivethreshold=VALUE
                              specify the number of keep alive packets a server
                                can miss, before the session is disconneced
                                Min: 1
                                Default: 5
      --aa, --autoaccept     the publisher trusts all servers it is
                                establishing a connection to.
                                Default: False
      --tm, --trustmyself=VALUE
                              same as trustowncert.
                                Default: False
      --to, --trustowncert   the publisher certificate is put into the trusted
                                certificate store automatically.
                                Default: False
      --fd, --fetchdisplayname=VALUE
                              same as fetchname.
                                Default: False
      --fn, --fetchname      enable to read the display name of a published
                                node from the server. this will increase the
                                runtime.
                                Default: False
      --ss, --suppressedopcstatuscodes=VALUE
                              specifies the OPC UA status codes for which no
                                events should be generated.
                                Default: BadNoCommunication,
                                BadWaitingForInitialData
      --at, --appcertstoretype=VALUE
                              the own application cert store type.
                                (allowed values: Directory, X509Store)
                                Default: 'Directory'
      --ap, --appcertstorepath=VALUE
                              the path where the own application cert should be
                                stored
                                Default (depends on store type):
                                X509Store: 'CurrentUser\UA_MachineDefault'
                                Directory: 'pki/own'
      --tp, --trustedcertstorepath=VALUE
                              the path of the trusted cert store
                                Default: 'pki/trusted'
      --rp, --rejectedcertstorepath=VALUE
                              the path of the rejected cert store
                                Default 'pki/rejected'
      --ip, --issuercertstorepath=VALUE
                              the path of the trusted issuer cert store
                                Default 'pki/issuer'
      --csr                  show data to create a certificate signing request
                                Default 'False'
      --ab, --applicationcertbase64=VALUE
                              update/set this applications certificate with the
                                certificate passed in as bas64 string
      --af, --applicationcertfile=VALUE
                              update/set this applications certificate with the
                                certificate file specified
      --pb, --privatekeybase64=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as base64 string
      --pk, --privatekeyfile=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as file
      --cp, --certpassword=VALUE
                              the optional password for the PEM or PFX or the
                                installed application certificate
      --tb, --addtrustedcertbase64=VALUE
                              adds the certificate to the applications trusted
                                cert store passed in as base64 string (multiple
                                strings supported)
      --tf, --addtrustedcertfile=VALUE
                              adds the certificate file(s) to the applications
                                trusted cert store passed in as base64 string (
                                multiple filenames supported)
      --ib, --addissuercertbase64=VALUE
                              adds the specified issuer certificate to the
                                applications trusted issuer cert store passed in
                                as base64 string (multiple strings supported)
      --if, --addissuercertfile=VALUE
                              adds the specified issuer certificate file(s) to
                                the applications trusted issuer cert store (
                                multiple filenames supported)
      --rb, --updatecrlbase64=VALUE
                              update the CRL passed in as base64 string to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --uc, --updatecrlfile=VALUE
                              update the CRL passed in as file to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --rc, --removecert=VALUE
                              remove cert(s) with the given thumbprint(s) (
                                multiple thumbprints supported)
      --dt, --devicecertstoretype=VALUE
                              the IoT Hub device cert store type.
                                (allowed values: Directory, X509Store)
                                Default: X509Store
      --dp, --devicecertstorepath=VALUE
                              the path of the iot device cert store
                                Default Default (depends on store type):
                                X509Store: 'My'
                                Directory: 'CertificateStores/IoT Hub'
  -i, --install              register OPC Publisher with IoT Hub and then exits.
                                Default:  False
  -h, --help                 show this message and exit
      --st, --opcstacktracemask=VALUE
                              ignored, only supported for backward comaptibility.
      --sd, --shopfloordomain=VALUE
                              same as site option, only there for backward
                                compatibility
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --vc, --verboseconsole=VALUE
                              ignored, only supported for backward comaptibility.
      --as, --autotrustservercerts=VALUE
                              same as autoaccept, only supported for backward
                                cmpatibility.
                                Default: False
      --tt, --trustedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted cert store will always reside in a
                                directory.
      --rt, --rejectedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the rejected cert store will always reside in a
                                directory.
      --it, --issuercertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted issuer cert store will always
                                reside in a directory.
```

Normalmente, você especifica a cadeia de conexão do proprietário do Hub IoT apenas na primeira execução do aplicativo. A cadeia de conexão é criptografada e armazenada no repositório de certificados da plataforma. Em execuções posteriores, o aplicativo lê a cadeia de conexão do repositório de certificados. Se você especificar a cadeia de conexão em cada execução, o dispositivo criado para o aplicativo no Registro de dispositivo do Hub IoT será removido e recriado.

## <a name="run-natively-on-windows"></a>Executar nativamente no Windows

Abra o projeto **opcpublisher.sln** com o Visual Studio, compile a solução e publique-a. Inicie o aplicativo no **Diretório de destino** no qual ele foi publicado da seguinte maneira:

```cmd
dotnet opcpublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-self-built-container"></a>Usar um contêiner criado automaticamente

Compile seu próprio contêiner e inicie-o da seguinte maneira:

```sh/cmd
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-container-from-microsoft-container-registry"></a>Usar um contêiner do Registro de Contêiner da Microsoft

Há um contêiner pré-compilado disponível no Registro de Contêiner da Microsoft. Inicie-o da seguinte maneira:

```sh/cmd
docker run mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Confira o [Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) para ver os sistemas operacionais e as arquiteturas de processador compatíveis. No caso de o SO e a arquitetura de CPU serem compatíveis, o Docker seleciona automaticamente o contêiner correto.

## <a name="run-as-an-azure-iot-edge-module"></a>Executar como um módulo do Azure IoT Edge

O Publicador OPC está pronto para ser usado como um módulo do [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge). Quando você usa o Publicador OPC como o módulo do IoT Edge, os únicos protocolos de transporte compatíveis são **Amqp_Tcp_Only** e **Mqtt_Tcp_Only**.

Para adicionar o Publicador OPC à sua implantação do módulo do IoT Edge, vá para as configurações do Hub IoT no portal do Azure e conclua as seguintes etapas:

1. Vá para o **IoT Edge** e crie ou selecione o dispositivo IoT Edge.
1. Selecione **Definir Módulos**.
1. Selecione **Adicionar** em **Módulos de Implantação** e, em seguida, **Módulo do IoT Edge**.
1. No campo **Nome**, insira **publicador**.
1. No campo **URI da Imagem**, insira `mcr.microsoft.com/iotedge/opc-publisher:<tag>`
1. Você pode encontrar as marcas disponíveis no [Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher)
1. Cole o JSON a seguir no campo **Opções de Criação de Contêiner**:

    ```json
    {
        "Hostname": "publisher",
        "Cmd": [
            "--aa"
        ]
    }
    ```

    Essa configuração define o IoT Edge para iniciar um contêiner chamado **publicador** usando a imagem do Publicador OPC. O nome do host do sistema do contêiner é definido como **publicador**. O Publicador OPC é chamado com o seguinte argumento de linha de comando: `--aa`. Com essa opção, o Publicador OPC confia em certificados do servidor OPC UA ao qual ele se conecta. Você pode usar qualquer opção de linha de comando do Publicador OPC. A única limitação é o tamanho das **Opções de Criação de Contêiner** compatíveis com o IoT Edge.

1. Deixe as outras configurações inalteradas e selecione **Salvar**.
1. Se desejar processar a saída do Publicador OPC localmente com o outro módulo do IoT Edge, volte para a página **Definir Módulos**. Em seguida, vá para a guia **Especificar Rotas** e, em seguida, adicione uma nova rota que se parece com o JSON a seguir:

    ```json
    {
      "routes": {
        "processingModuleToIoT Hub": "FROM /messages/modules/processingModule/outputs/* INTO $upstream",
        "opcPublisherToProcessingModule": "FROM /messages/modules/publisher INTO BrokeredEndpoint(\"/modules/processingModule/inputs/input1\")"
      }
    }
    ```

1. Volte na página **Definir Módulos** e selecione **Avançar** até atingir a última página da configuração.
1. Selecione **Enviar** para enviar sua configuração ao IoT Edge.
1. Depois de iniciar o IoT Edge em seu dispositivo de borda e ter o **publicador** de contêiner do Docker em execução, você pode conferir a saída de log do Publicador OPC usando `docker logs -f publisher` ou verificando o arquivo de log. No exemplo anterior, o arquivo de log está acima de `d:\iiotegde\publisher-publisher.log`. Você também pode usar a [iot-edge-opc-publisher-diagnostics tool](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics).

### <a name="make-the-configuration-files-accessible-on-the-host"></a>Disponibilizar os arquivos de configuração no host

Para tornar os arquivos de configuração do módulo do IoT Edge acessíveis no sistema de arquivos de host, use as seguintes **Opções de Criação de Contêiner**. O exemplo a seguir é de uma implantação usando Contêineres do Linux para Windows:

```json
{
    "Hostname": "publisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "d:/iiotedge:/appdata"
        ]
    }
}
```

Com essas opções, o Publicador OPC lê os nós que ele deve publicar do arquivo `./pn.json` e o diretório de trabalho do contêiner é definido como `/appdata` na inicialização. Com essas configurações, o Publicador OPC lê o arquivo `/appdata/pn.json` do contêiner para obter sua configuração. Sem a opção `--pf`, o publicador OPC tenta ler o arquivo de configuração padrão `./publishednodes.json`.

O arquivo de log, usando o nome padrão `publisher-publisher.log`, é gravado em `/appdata` e o diretório `CertificateStores` também é criado nesse diretório.

Para disponibilizar todos esses arquivos no sistema de arquivos de host, a configuração do contêiner requer um volume de montagem de associação. A associação `d://iiotedge:/appdata` mapeia o diretório `/appdata`, que é o diretório de trabalho atual na inicialização do contêiner para o diretório do host `d://iiotedge`. Sem essa opção, nenhum dado do arquivo é mantido na próxima inicialização do contêiner.

Se você estiver executando contêineres do Windows, a sintaxe do parâmetro `Binds` será diferente. Na inicialização do contêiner, o diretório de trabalho é `c:\appdata`. Para colocar o arquivo de configuração no diretório `d:\iiotedge` no host, especifique o seguinte mapeamento na seção `HostConfig`:

```json
"HostConfig": {
    "Binds": [
        "d:/iiotedge:c:/appdata"
    ]
}
```

Se você estiver executando contêineres do Linux no Linux, a sintaxe do parâmetro `Binds` novamente será diferente. Na inicialização do contêiner, o diretório de trabalho é `/appdata`. Para colocar o arquivo de configuração no diretório `/iiotedge` no host, especifique o seguinte mapeamento na seção `HostConfig`:

```json
"HostConfig": {
    "Binds": [
        "/iiotedge:/appdata"
    ]
}
```

## <a name="considerations-when-using-a-container"></a>Considerações ao usar um contêiner

As seções a seguir listam alguns pontos a lembrar quando você usa um contêiner:

### <a name="access-to-the-opc-publisher-opc-ua-server"></a>Acesso ao servidor OPC UA do Publicador OPC

Por padrão, o servidor OPC UA do Publicador OPC escuta na porta 62222. Para expor essa porta de entrada em um contêiner, use o seguinte comando:

```sh/cmd
docker run -p 62222:62222 mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="enable-intercontainer-name-resolution"></a>Habilitar a resolução de nome entre contêineres

Para habilitar a resolução de nome de dentro do contêiner para outros contêineres, crie uma rede de ponte do Docker definida pelo usuário e conecte o contêiner a essa rede usando a opção `--network`. Também atribua um nome ao contêiner usando a opção `--name` da seguinte maneira:

```sh/cmd
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Usando o nome `publisher`, o contêiner agora está acessível a outros contêineres na mesma rede.

### <a name="access-other-systems-from-within-the-container"></a>Acesso a outros sistemas de dentro do contêiner

Outros contêineres podem ser acessados usando os parâmetros descritos na seção anterior. Se o sistema operacional em que o Docker está hospedado estiver habilitado para DNS, acessar todos os sistemas conhecidos por DNS funcionará.

Em redes que usam a resolução de nomes NetBIOS, habilite o acesso a outros sistemas iniciando seu contêiner com a opção `--add-host`. Essa opção efetivamente adiciona uma entrada ao arquivo de host do contêiner:

```cmd/sh
docker run --add-host mydevbox:192.168.178.23  mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="assign-a-hostname"></a>Atribuir um nome de host

O Publicador OPC usa o nome do host do computador em que ele está sendo executado para geração de ponto de extremidade e certificado. O Docker escolherá um nome de host aleatório caso um não tenha sido definido pela opção `-h`. O exemplo a seguir mostra como definir o nome do host interno do contêiner como `publisher`:

```sh/cmd
docker run -h publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-bind-mounts-shared-filesystem"></a>Usar montagens associadas (sistema de arquivos compartilhado)

Em vez de usar o sistema de arquivos do contêiner, você pode escolher o sistema de arquivos de host para armazenar informações de configuração e arquivos de log. Para configurar essa opção, use a opção `-v` da `docker run` no modo de montagem associada.

## <a name="opc-ua-x509-certificates"></a>Certificados X.509 do protocolo OPC UA

O protocolo OPC UA usa certificados X.509 para autenticar o cliente e o servidor OPC UA quando ele estabelecerem uma conexão e para criptografar a comunicação entre eles. O Publicador OPC usa repositórios de certificados mantidos pela pilha OPC UA para gerenciar todos os certificados. Na inicialização, o Publicador OPC verifica se há um certificado para si mesmo. Se não houver certificado no repositório de certificados e não for passado um certificado na linha de comando, o Publicador OPC criará um certificado autoassinado. Para obter mais informações, confira o método **InitApplicationSecurityAsync** em `OpcApplicationConfigurationSecurity.cs`.

Os certificados autoassinados não fornecem nenhuma segurança, pois não são assinados por uma AC confiável.

O Publicador OPC fornece opções de linha de comando para:

- Recuperar informações de CSR do certificado de aplicativo atual usado pelo Publicador OPC.
- Provisionar ao Publicador OPC um certificado assinado por AC.
- Provisionar ao Publicador OPC um novo par de chaves e um certificado assinado por AC correspondente.
- Adicionar certificados a um par confiável ou a um repositório de certificados de emissor confiável.
- Adicionar uma CRL.
- Remover um certificado do par confiável ou de um repositório de certificados de emissor confiável.

Todas essas opções permitem que você passe parâmetros usando arquivos ou cadeias de caracteres codificadas em base64.

O tipo de armazenamento padrão para todos os repositórios de certificado é o sistema de arquivos, que pode ser alterado usando as opções de linha de comando. Como o contêiner não fornece armazenamento persistente em seu sistema de arquivos, você deve escolher um tipo de repositório diferente. Usar a opção `-v` do Docker para manter os repositórios de certificados no sistema de arquivos de host ou em um volume do Docker. Se você usar um volume do Docker, poderá passar certificados usando cadeias de caracteres codificadas em base64.

O ambiente de tempo de execução afeta a persistência dos certificados. Evite criar novos repositórios de certificados sempre que executar o aplicativo:

- Executando nativamente no Windows, você não pode usar um repositório de certificados de aplicativo do tipo `Directory` porque o acesso à chave privada falha. Nesse caso, use a opção `--at X509Store`.
- Em execução como um contêiner do Docker do Linux, você pode mapear os repositórios de certificados para o sistema de arquivos de host com a opção de execução do Docker `-v <hostdirectory>:/appdata`. Essa opção faz o certificado persistir entre as execuções do aplicativo.
- Em execução como um contêiner do Docker do Linux e você deseja usar um repositório X509 para o certificado de aplicativo, use a opção de execução do Docker `-v x509certstores:/root/.dotnet/corefx/cryptography/x509stores` e a opção de aplicativo `--at X509Store`

## <a name="performance-and-memory-considerations"></a>Considerações sobre desempenho e memória

Esta seção discute as opções para gerenciar memória e desempenho:

### <a name="command-line-parameters-to-control-performance-and-memory"></a>Parâmetros de linha de comando para controlar desempenho e memória

Quando você executa o Publicador OPC, precisa estar ciente dos seus requisitos de desempenho e dos recursos de memória disponíveis em seu host.

Desempenho e memória são interdependentes e ambos dependem da configuração de quantos nós você define para publicar. Verifique se os seguintes parâmetros atendem às suas necessidades:

- Intervalo de envios do Hub IoT: `--si`
- Tamanho de mensagem do Hub IoT (padrão `1`): `--ms`
- Capacidade de fila de itens monitorados: `--mq`

O parâmetro `--mq` controla o limite superior da capacidade da fila interna, que armazena em buffer todas as notificações de alteração de valor do nó OPC. Se o Publicador OPC não puder enviar mensagens ao Hub IoT com a rapidez suficiente, essa fila armazenará as notificações em buffer. O parâmetro define o número de notificações que podem ser armazenadas em buffer. Se você perceber que o número de itens na fila está aumentando em suas execuções de teste, para evitar perder mensagens você deverá:

- Reduzir o intervalo de envio do Hub IoT
- Aumentar o tamanho de mensagem do Hub IoT

O parâmetro `--si` força o Publicador OPC a enviar mensagens ao Hub IoT ao intervalo especificado. O Publicador OPC envia uma mensagem assim que o tamanho da mensagem especificado pelo parâmetro `--ms` é atingido ou assim que o intervalo especificado pelo parâmetro `--si` é atingido. Para desabilitar a opção de tamanho de mensagem, use `--ms 0`. Nesse caso, o Publicador OPC usa o maior tamanho possível de mensagem do Hub IoT de 256 KB para criar lotes de dados.

O parâmetro `--ms` permite criar lotes de mensagens enviadas para o Hub IoT. O protocolo que você está usando determina se a sobrecarga de enviar uma mensagem ao Hub IoT é alta em comparação ao tempo real do envio do conteúdo. Caso seu cenário permita latência quando os dados são ingeridos pelo Hub IoT, configure o Publicador OPC para usar o maior tamanho de mensagem de 256 KB.

Antes de usar o Publicador OPC em cenários de produção, teste o desempenho e uso de memória sob condições de produção. Você pode usar o parâmetro `--di` para especificar o intervalo, em segundos, que o Publicador OPC grava informações de diagnóstico.

### <a name="test-measurements"></a>Testar medições

O exemplo de diagnóstico a seguir mostra as medidas com valores diferentes para os parâmetros `--si` e `--ms` publicando 500 nós com um intervalo de publicação de OPC de 1 segundo.  O teste usou um build de depuração do Publicador OPC no Windows 10 nativamente por 120 segundos. O protocolo do Hub IoT era o protocolo MQTT padrão.

#### <a name="default-configuration---si-10---ms-262144"></a>Configuração padrão (--si 10 --ms 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:33:05 (started @ 26.10.2017 15:31:09)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54363
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54363
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:33:04
bytes sent to IoT Hub: 12709429
avg msg size: 116600
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 10
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

A configuração padrão envia dados ao Hub IoT a cada 10 segundos ou quando 256 KB de dados estão disponíveis para o Hub IoT ingerir. Essa configuração adiciona uma latência moderada de cerca de 10 segundos, mas tem menor probabilidade de perder dados devido ao grande tamanho da mensagem. A saída de diagnóstico mostra que não há nenhuma atualização de nó OPC perdida: `monitored item notifications enqueue failure: 0`.

#### <a name="constant-send-interval---si-1---ms-0"></a>Intervalo de envio de constante (--si 1 --ms 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:35:59 (started @ 26.10.2017 15:34:03)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54243
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54243
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:35:59
bytes sent to IoT Hub: 12683836
avg msg size: 116365
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 1
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

Quando o tamanho da mensagem é definido como 0, o Publicador OPC envia internamente lotes de dados usando o maior tamanho compatível de mensagem do Hub IoT, que é de 256 KB. A saída de diagnóstico mostra que o tamanho médio da mensagem é de 115.019 bytes. Nesta configuração, o Publicador OPC não perde nenhuma atualização de valor de nó OPC e, comparado ao padrão, tem uma latência menor.

### <a name="send-each-opc-node-value-update---si-0---ms-0"></a>Enviar cada atualização de valor de nó OPC (--si 0 --ms 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:39:33 (started @ 26.10.2017 15:37:37)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 8184
monitored item notifications enqueued: 54232
monitored item notifications enqueue failure: 44624
monitored item notifications dequeued: 1424
---------------------------------
messages sent to IoT Hub: 1423
last successful msg sent @: 26.10.2017 15:39:33
bytes sent to IoT Hub: 333046
avg msg size: 234
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 96
--si setting: 0
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

Essa configuração envia para cada alteração de valor de nó OPC uma mensagem ao Hub IoT. O diagnóstico mostra que o tamanho médio da mensagem é de 234 bytes, o que é pequeno. A vantagem dessa configuração é que o Publicador OPC não adiciona nenhuma latência. O número de atualizações de valor de nó OPC perdidas (`monitored item notifications enqueue failure: 44624`) é alto, o que torna essa configuração inadequada para cenários com grandes volumes de telemetria a serem publicados.

### <a name="maximum-batching---si-0---ms-262144"></a>Envio em lote máximo (--si 0 --ms 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:42:55 (started @ 26.10.2017 15:41:00)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54137
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54137
---------------------------------
messages sent to IoT Hub: 48
last successful msg sent @: 26.10.2017 15:42:55
bytes sent to IoT Hub: 12565544
avg msg size: 261782
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 0
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

Essa configuração cria lotes com o maior número possível de atualizações de valor de nó OPC. O tamanho máximo da mensagem do Hub IoT é de 256 KB, o que é configurado aqui. Não há nenhum intervalo de envio solicitado, o que significa que a quantidade de dados para o Hub IoT ingerir determina a latência. Essa configuração tem a menor probabilidade de perder quaisquer valores de nó OPC e é adequada para publicar um grande número de nós. Quando você usa essa configuração, verifique se o cenário não tem condições em que é introduzida alta latência se o tamanho da mensagem de 256 KB não é atingido.

## <a name="debug-the-application"></a>Depurar o aplicativo

Para depurar o aplicativo, abra o arquivo de solução **opcpublisher.sln** com o Visual Studio e use as ferramentas de depuração do Visual Studio.

Se precisar acessar o servidor OPC UA no Publicador OPC, verifique se o firewall permite o acesso à porta em que o servidor escuta. A porta padrão é: 62222.

## <a name="control-the-application-remotely"></a>Controlar o aplicativo remotamente

A configuração dos nós a serem publicados pode ser feita usando métodos diretos do Hub IoT.

O Publicador OPC implementa algumas chamadas de método direto do Hub IoT adicionais para ler:

- Informações gerais.
- Informações de diagnóstico em sessões, assinaturas e itens monitorados de OPC.
- Informações de diagnóstico em eventos e mensagens do Hub IoT.
- O log de inicialização.
- As últimas 100 linhas do log.
- Desligar o aplicativo.

Os seguintes repositórios do GitHub contêm ferramentas para [configurar os nós a serem publicados](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) e [ler as informações de diagnóstico](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics). Ambas as ferramentas também estão disponíveis como contêineres no Docker Hub.

## <a name="use-a-sample-opc-ua-server"></a>Usar um exemplo de servidor OPC UA

Se você não tiver um servidor OPC UA real, poderá usar o [exemplo de PLC de OPC UA](https://github.com/Azure-Samples/iot-edge-opc-plc) para começar. Este exemplo de PLC também está disponível no Docker Hub.

Ele implementa várias marcas, o que gera dados aleatórios e marcas com anomalias. Você poderá estender o exemplo se precisar simular valores de tag adicionais.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a executar o Publicador OPC, as próximas etapas recomendadas são saber mais sobre o [Gêmeo OPC](overview-opc-twin.md) e o [Cofre OPC](overview-opc-vault.md).
