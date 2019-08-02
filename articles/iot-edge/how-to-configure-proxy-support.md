---
title: Configurar dispositivos para proxies de rede - Azure IoT Edge | Microsoft Docs
description: Como configurar o tempo de execução do Azure IoT Edge e quaisquer módulos de IoT Edge voltados para a Internet para comunicação por meio de um servidor proxy.
author: kgremban
manager: ''
ms.author: kgremban
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 47d3018015d05d0587e841c216a5eb89f2a0ae20
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414557"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy

Os dispositivos IoT Edge enviam solicitações HTTPS para se comunicarem com o Hub IoT. Se o seu dispositivo estiver conectado a uma rede que usa um servidor proxy, você precisará configurar o tempo de execução do IoT Edge para se comunicar através do servidor. Os servidores proxy também podem afetar os módulos individuais do IoT Edge se eles fizerem solicitações HTTP ou HTTPS que não são roteadas pelo hub do IoT Edge. 

Este artigo percorre as quatro etapas a seguir para configurar e gerenciar um dispositivo IoT Edge atrás de um servidor proxy: 

1. **Instale o IoT Edge tempo de execução em seu dispositivo.**

   Os scripts de instalação do IoT Edge efetuam pull de pacotes e arquivos da Internet, para que seu dispositivo precise se comunicar por meio do servidor proxy para fazer essas solicitações. Para obter etapas detalhadas, consulte a seção [instalar o tempo de execução por meio de um proxy](#install-the-runtime-through-a-proxy) deste artigo. Para dispositivos Windows, o script de instalação também fornece uma opção de [instalação offline](how-to-install-iot-edge-windows.md#offline-installation) . 

   Essa etapa é um processo único executado no dispositivo IoT Edge quando você o configura pela primeira vez. As mesmas conexões também são necessárias quando você atualiza o tempo de execução do IoT Edge. 

2. **Configure o daemon do Docker e o daemon do IoT Edge em seu dispositivo.**

   IoT Edge usa dois daemons no dispositivo, ambos precisam fazer solicitações da Web por meio do servidor proxy. O daemon de IoT Edge é responsável pelas comunicações com o Hub IoT. O daemon Moby é responsável pelo gerenciamento de contêineres, portanto, comunica-se com registros de contêiner. Para obter etapas detalhadas, consulte a seção [configurar os daemons](#configure-the-daemons) deste artigo. 

   Essa etapa é um processo único executado no dispositivo IoT Edge quando você o configura pela primeira vez.

3. **Configure as propriedades do agente de IoT Edge no arquivo config. YAML em seu dispositivo.**

   O daemon de IoT Edge inicia inicialmente o módulo edgeAgent, mas, em seguida, o módulo edgeAgent é responsável por recuperar o manifesto de implantação do Hub IoT e iniciar todos os outros módulos. Para que o agente de IoT Edge faça a conexão inicial com o Hub IoT, configure manualmente as variáveis de ambiente do módulo edgeAgent no próprio dispositivo. Após a conexão inicial, você pode configurar o módulo edgeAgent remotamente. Para obter etapas detalhadas, consulte a seção [Configurar o agente de IOT Edge](#configure-the-iot-edge-agent) deste artigo.

   Essa etapa é um processo único executado no dispositivo IoT Edge quando você o configura pela primeira vez.

4. **Para todas as implantações de módulo futuras, defina variáveis de ambiente para qualquer módulo que se comunique por meio do proxy.**

   Depois que o dispositivo de IoT Edge estiver configurado e conectado ao Hub IoT por meio do servidor proxy, você precisará manter a conexão em todas as implantações de módulo futuras. Para obter etapas detalhadas, consulte a seção [Configurar manifestos de implantação](#configure-deployment-manifests) deste artigo. 

   Esta etapa é um processo contínuo executado remotamente para que cada novo módulo ou atualização de implantação mantenha a capacidade do dispositivo de se comunicar por meio do servidor proxy. 

## <a name="know-your-proxy-url"></a>Saber a sua URL do proxy

Antes de começar qualquer uma das etapas deste artigo, você precisa saber a URL do proxy.

Os URLs proxy usam o seguinte formato: **protocolo**: // **proxy_host**: **proxy_port**.

* O **protocolo** é HTTP ou HTTPS. O daemon do Docker pode usar qualquer um dos protocolos, dependendo das configurações do registro de contêiner, mas o daemon de IoT Edge e os contêineres de tempo de execução sempre devem usar HTTP para se conectar ao proxy.

* O **proxy_host** é um endereço para o servidor proxy. Se o servidor proxy exigir autenticação, você poderá fornecer suas credenciais como parte do host de proxy com o seguinte formato: **User**:**password**\@**proxy_host**.

* O **proxy_port** é a porta de rede em que o proxy responde ao tráfego de rede.

## <a name="install-the-runtime-through-a-proxy"></a>Instalar o tempo de execução por meio de um proxy

Se o dispositivo IoT Edge for executado no Windows ou no Linux, você precisará acessar os pacotes de instalação por meio do servidor proxy. Dependendo do seu sistema operacional, siga as etapas para instalar o IoT Edge tempo de execução por meio de um servidor proxy. 

### <a name="linux"></a>Linux

Se você estiver instalando o tempo de execução do IoT Edge em um dispositivo Linux, configure o gerenciador de pacotes para percorrer seu servidor proxy para acessar o pacote de instalação. Por exemplo, [Configurar o apt-get para usar um proxy http](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Quando o Gerenciador de pacotes estiver configurado, siga as instruções em [instalar Azure IOT Edge tempo de execução no Linux](how-to-install-iot-edge-linux.md) como de costume.

### <a name="windows"></a>Windows

Se estiver instalando o tempo de execução do IoT Edge em um dispositivo Windows, você precisará passar pelo servidor proxy duas vezes. A primeira conexão baixa o arquivo de script do instalador e a segunda conexão é durante a instalação para baixar os componentes necessários. Você pode configurar as informações de proxy nas configurações do Windows ou incluir suas informações de proxy diretamente nos comandos do PowerShell. 

As etapas a seguir demonstram um exemplo de instalação do Windows `-proxy` usando o argumento:

1. O comando Invoke-WebRequest precisa de informações de proxy para acessar o script do instalador. Em seguida, o comando Deploy-IoTEdge precisa das informações de proxy para baixar os arquivos de instalação. 

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. O comando Initialize-IoTEdge não precisa passar pelo servidor proxy, portanto, a segunda etapa requer apenas informações de proxy para Invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

Se tiver credenciais complicadas para o servidor proxy que não podem ser incluídas na URL, use o `-ProxyCredential` parâmetro em `-InvokeWebRequestParameters`. Por exemplo,

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Para obter mais informações sobre parâmetros de proxy, consulte [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). Para obter mais informações sobre as opções de instalação do Windows, incluindo a instalação offline, consulte [instalar Azure IOT Edge tempo de execução no Windows](how-to-install-iot-edge-windows.md).

## <a name="configure-the-daemons"></a>Configurar os daemons

IoT Edge se baseia em dois daemons em execução no dispositivo IoT Edge. O daemon Moby faz solicitações da Web para efetuar pull de imagens de contêiner de registros de contêiner. O daemon do IoT Edge faz solicitações da Web para se comunicar com o Hub IoT.

Os daemons Moby e IoT Edge precisam ser configurados para usar o servidor proxy para a funcionalidade de dispositivo contínua. Esta etapa ocorre no dispositivo de IoT Edge durante a configuração inicial do dispositivo. 

### <a name="moby-daemon"></a>Daemon do Moby

Como o Moby é criado no Docker, consulte a documentação do Docker para configurar o daemon do Moby com variáveis de ambiente. A maioria dos registros de contêiner (incluindo DockerHub e Azure Container Registries) oferece suporte a solicitações HTTPS, portanto, o parâmetro que você deve definir é **HTTPS_PROXY**. Se você está obtendo imagens de um registro que não dão suporte ao protocolo TLS (TLS), você deve definir o parâmetro **HTTP_PROXY**. 

Escolha o artigo que se aplica ao sistema operacional do dispositivo de IoT Edge: 

* [Configurar o daemon do Docker no Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
    * O daemon do Moby em dispositivos Linux mantém o Docker de nome.
* [Configurar o daemon do Docker no Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)
    * O daemon do Moby em dispositivos Windows é chamado de iotedge-Moby. Os nomes são diferentes porque é possível executar o Docker desktop e o Moby em paralelo em um dispositivo Windows. 

### <a name="iot-edge-daemon"></a>Daemon do IoT Edge

O daemon de IoT Edge é configurado de maneira semelhante ao daemon do Moby. Use as etapas a seguir para definir uma variável de ambiente para o serviço, com base em seu sistema operacional. 

O daemon de IoT Edge sempre usa HTTPS para enviar solicitações ao Hub IoT.

#### <a name="linux"></a>Linux

Abra um editor no terminal para configurar o daemon do IoT Edge. 

```bash
sudo systemctl edit iotedge
```

Insira o texto a seguir, substituindo  **\<URL do proxy >** com seu endereço do servidor proxy e a porta. Em seguida, salve e saia. 

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Atualize o gerenciador de serviços para pegar a nova configuração do IoT Edge.

```bash
sudo systemctl daemon-reload
```

Reinicie o IoT Edge para que as alterações entrem em vigor.

```bash
sudo systemctl restart iotedge
```

Verifique se sua variável de ambiente foi criada e se a nova configuração foi carregada. 

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

Abra uma janela do PowerShell como administrador e execute o seguinte comando para editar o registro com a nova variável de ambiente. Substitua **\<URL do proxy>** pelo endereço e porta do servidor proxy. 

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Reinicie o IoT Edge para que as alterações entrem em vigor.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>Configurar o dispositivo IoT Edge

O agente IoT Edge é o primeiro módulo a ser iniciado em qualquer dispositivo IoT Edge. É iniciado pela primeira vez com base nas informações do arquivo config.yaml do IoT Edge. Em seguida, o agente do IoT Edge se conecta ao Hub IoT para recuperar os manifestos de implantação, que declaram quais outros módulos devem ser implantados no dispositivo.

Esta etapa ocorre uma vez no dispositivo IoT Edge durante a configuração inicial do dispositivo. 

1. Abra o arquivo config.yaml no seu dispositivo IoT Edge. Nos sistemas Linux, esse arquivo está localizado em **/etc/iotedge/config.yaml**. Nos sistemas Windows, esse arquivo está localizado em **C: \ ProgramData \ iotedge \ config.yaml**. O arquivo de configuração é protegido, você precisa de privilégios administrativos para acessá-lo. Em sistemas Linux, use o `sudo` comando antes de abrir o arquivo em seu editor de texto preferido. No Windows, abra um editor de texto como o bloco de notas como administrador e, em seguida, abra o arquivo. 

2. No arquivo config.yaml, localize a seção **Especificação do módulo Edge Agent**. A definição do agente IoT Edge inclui um parâmetro **env**, no qual é possível incluir variáveis de ambiente. 

3. Remova as chaves que são espaços reservados para o parâmetro env e inclua a nova variável em uma nova linha. Lembre-se de que os recuos em YAML são dois espaços. 

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. O tempo de execução do IoT Edge usa o AMQP por padrão para falar com o IoT Hub. Alguns servidores proxy bloqueiam as portas AMQP. Se for esse o caso, você também precisará configurar o edgeAgent para usar o AMQP em WebSocket. Adicione uma segunda variável de ambiente.

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![definição de edgeAgent com variáveis de ambiente](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. Salve as alterações no config. YAML e feche o editor. Reinicie o IoT Edge para que as alterações entrem em vigor. 

   * Linux: 

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows:

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>Configurar manifestos de implantação  

Depois que o dispositivo IoT Edge estiver configurado para funcionar com o servidor proxy, continue a declarar as variáveis de ambiente em todos os manifestos de implantação futuros. Você pode editar os manifestos de implantação usando o assistente de portal do Azure ou editando um arquivo JSON de manifesto de implantação. 

Sempre configure os dois módulos de tempo de execução: edgeAgent e edgeHub para se comunicar por meio do servidor proxy para que eles possam manter uma conexão com o Hub IoT. Se você remover as informações de proxy do módulo edgeAgent, a única maneira de restabelecer a conexão é editando o arquivo config. YAML no dispositivo, conforme descrito na seção anterior. 

Outros módulos IoT Edge que se conectam à Internet também devem ser configurados para se comunicar por meio do servidor proxy. No entanto, os módulos que roteiam as suas mensagens por meio do edgeHub ou que se comunicam somente com outros módulos no dispositivo não precisam dos detalhes do servidor proxy. 

Esta etapa está em andamento durante a vida útil do dispositivo IoT Edge. 

### <a name="azure-portal"></a>Portal do Azure

Quando você usa o assistente **Configurar módulos** para criar implantações para dispositivos IoT Edge, cada módulo tem uma seção **Variáveis de Ambiente** que você pode usar para configurar conexões do servidor proxy. 

Para configurar o agente do IoT Edge e os módulos do hub do IoT Edge, selecione **Definir configurações avançadas do Edge Runtime** na primeira etapa do assistente. 

![Definir configurações avançadas do tempo de execução do Edge](./media/how-to-configure-proxy-support/configure-runtime.png)

Adicione a variável de ambiente **https_proxy** às definições do módulo de agente IoT Edge e do hub do IoT Edge. Se você incluiu a variável de ambiente **UpstreamProtocol** no arquivo config.yaml em seu dispositivo IoT Edge, inclua isso na definição do módulo do agente do IoT Edge também. 

![Defina a variável de ambiente https_proxy](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Todos os outros módulos adicionados a um manifesto de implantação seguem o mesmo padrão. Na página em que você define o nome e a imagem do módulo, há uma seção de variáveis de ambiente.

### <a name="json-deployment-manifest-files"></a>Arquivos de manifesto de implantação de JSON

Se você criar implantações para dispositivos IoT Edge usando os modelos no Visual Studio Code ou criando manualmente arquivos JSON, poderá adicionar as variáveis de ambiente diretamente a cada definição de módulo. 

Use o seguinte formato JSON: 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

Com as variáveis de ambiente incluídas, sua definição de módulo deve ser semelhante ao seguinte exemplo edgeHub:

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": ""
    },
    "env": {
        "https_proxy": {
            "value": "http://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Se você incluiu a variável de ambiente **UpstreamProtocol** no arquivo confige.yaml em seu dispositivo IoT Edge, inclua isso na definição do módulo do agente do IoT Edge também. 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as funções do tempo de execução do [IoT Edge](iot-edge-runtime.md).

Solucione problemas de erros de instalação e configuração com [problemas comuns e resoluções para o Azure IoT Edge](troubleshoot.md)

