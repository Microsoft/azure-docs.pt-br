---
title: Solucionar problemas – Azure IoT Edge | Microsoft Docs
description: Use este artigo para aprender as habilidades de diagnóstico padrão para Azure IoT Edge, como recuperar o status e os logs do componente
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 035cf5be4471cad7ac11eb8ce9a8a0ecb13a68da
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462367"
---
# <a name="troubleshoot-your-iot-edge-device"></a>Solucionar problemas de seu dispositivo IoT Edge

Se você tiver problemas em execução Azure IoT Edge em seu ambiente, use este artigo como um guia para solução de problemas e diagnósticos.

## <a name="run-the-check-command"></a>Executar o comando ' check '

Sua primeira etapa ao solucionar problemas IoT Edge deve ser usar o `check` comando, que executa uma coleção de testes de configuração e conectividade para problemas comuns. O `check` comando está disponível na [versão 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) e posterior.

>[!NOTE]
>A ferramenta de solução de problemas não pode executar verificações de conectividade se o dispositivo de IoT Edge estiver protegido por um servidor proxy.

Você pode executar o `check` comando da seguinte maneira ou incluir o `--help` sinalizador para ver uma lista completa de opções:

No Linux:

```bash
sudo iotedge check
```

No Windows:

```powershell
iotedge check
```

A ferramenta de solução de problemas executa muitas verificações que são classificadas nestas três categorias:

* As *verificações de configuração* examinam os detalhes que podem impedir que IOT Edge dispositivos se conectem à nuvem, incluindo problemas com *config. YAML* e o mecanismo de contêiner.
* *Verificações de conexão* Verifique se o tempo de execução de IOT Edge pode acessar portas no dispositivo de host e se todos os componentes de IOT Edge podem se conectar ao Hub IOT. Esse conjunto de verificações retornará erros se o dispositivo IoT Edge estiver protegido por um proxy.
* As *verificações de preparação de produção* procuram práticas recomendadas de produção recomendadas, como o estado de certificados de autoridade de certificação de dispositivo (CA) e configuração de arquivo de log de módulo.

A ferramenta de verificação de IoT Edge usa um contêiner para executar seu diagnóstico. A imagem de contêiner, `mcr.microsoft.com/azureiotedge-diagnostics:latest` , está disponível por meio do [registro de contêiner da Microsoft](https://github.com/microsoft/containerregistry). Se você precisar executar uma verificação em um dispositivo sem acesso direto à Internet, seus dispositivos precisarão acessar a imagem de contêiner.

Para obter informações sobre cada uma das verificações de diagnóstico executadas por essa ferramenta, incluindo o que fazer se você receber um erro ou aviso, consulte [IOT Edge solucionar problemas de verificações](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="gather-debug-information-with-support-bundle-command"></a>Coletar informações de depuração com o comando ' support-Bundle '

Quando você precisa coletar logs de um dispositivo IoT Edge, a maneira mais conveniente é usar o `support-bundle` comando. Por padrão, esse comando coleta o módulo, IoT Edge Gerenciador de segurança e logs do mecanismo de contêiner, `iotedge check` saída JSON e outras informações de depuração úteis. Ele os compacta em um único arquivo para facilitar o compartilhamento. O `support-bundle` comando está disponível na [versão 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) e posterior.

Execute o `support-bundle` comando com o `--since` sinalizador para especificar quanto tempo do passado você deseja obter logs. Por exemplo, os `6h` logs serão obtidos desde as últimas seis horas, desde os últimos seis `6d` dias, `6m` desde os últimos seis minutos e assim por diante. Inclua o `--help` sinalizador para ver uma lista completa de opções.

No Linux:

```bash
sudo iotedge support-bundle --since 6h
```

No Windows:

```powershell
iotedge support-bundle --since 6h
```

Você também pode usar uma chamada de [método direto](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics) para seu dispositivo para carregar a saída do comando support-Bundle para o armazenamento de BLOBs do Azure.

> [!WARNING]
> A saída do `support-bundle` comando pode conter nomes de host, dispositivo e módulo, informações registradas pelos seus módulos etc. Lembre-se disso se estiver compartilhando a saída em um fórum público.

## <a name="check-your-iot-edge-version"></a>Verifique sua versão do IoT Edge

A atualização poderá resolver o problema se você estiver executando uma versão mais antiga do IoT Edge. A `iotedge check` ferramenta verifica se o daemon de segurança IOT Edge é a versão mais recente, mas não verifica as versões dos módulos IOT Edge Hub e agente. Para verificar a versão dos módulos de tempo de execução em seu dispositivo, use os comandos `iotedge logs edgeAgent` e `iotedge logs edgeHub` . O número de versão é informado nos logs na inicialização do módulo.

Para obter instruções sobre como atualizar seu dispositivo, consulte [atualizar o daemon de segurança IOT Edge e o tempo de execução](how-to-update-iot-edge.md).

## <a name="verify-the-installation-of-iot-edge-on-your-devices"></a>Verifique a instalação do IoT Edge em seus dispositivos

Você pode verificar a instalação do IoT Edge em seus dispositivos [monitorando o módulo de edgeAgent](./how-to-monitor-module-twins.md).

Para obter o módulo edgeAgent mais recente, execute o seguinte comando a partir de [Azure cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub module-twin show --device-id <edge_device_id> --module-id $edgeAgent --hub-name <iot_hub_name>
   ```

Esse comando produzirá todas as [Propriedades relatadas](./module-edgeagent-edgehub.md)do edgeAgent. Aqui estão algumas úteis que monitoram o status do dispositivo:

* status do tempo de execução
* hora de início do tempo de execução
* hora da última saída do tempo de execução
* contagem de reinício de tempo de execução

## <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Verificar o status do IoT Edge Security Manager e seus logs

O [Gerenciador de segurança IOT Edge](iot-edge-security-manager.md) é responsável por operações como inicializar o sistema de IOT Edge na inicialização e nos dispositivos de provisionamento. Se IoT Edge não estiver iniciando, os logs do Gerenciador de segurança poderão fornecer informações úteis.

No Linux:

* Exibir o status do Gerenciador de segurança do IoT Edge:

   ```bash
   sudo systemctl status iotedge
   ```

* Exiba os logs do Gerenciador de segurança do IoT Edge:

    ```bash
    sudo journalctl -u iotedge -f
    ```

* Exibir logs mais detalhados do IoT Edge Security Manager:

  * Edite as configurações do daemon de IoT Edge:

      ```bash
      sudo systemctl edit iotedge.service
      ```

  * Atualize as seguintes linhas:

      ```bash
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```

  * Reinicie o daemon de segurança do IoT Edge:

      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

No Windows:

* Exibir o status do Gerenciador de segurança do IoT Edge:

   ```powershell
   Get-Service iotedge
   ```

* Exiba os logs do Gerenciador de segurança do IoT Edge:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

* Exibir somente os últimos 5 minutos dos logs do IoT Edge Security Manager:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog -StartTime ([datetime]::Now.AddMinutes(-5))
   ```

* Exibir logs mais detalhados do IoT Edge Security Manager:

  * Adicione uma variável de ambiente no nível do sistema:

      ```powershell
      [Environment]::SetEnvironmentVariable("IOTEDGE_LOG", "debug", [EnvironmentVariableTarget]::Machine)
      ```

  * Reinicie o daemon de segurança do IoT Edge:

      ```powershell
      Restart-Service iotedge
      ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Se o Gerenciador de segurança do IoT Edge não estiver em execução, verifique o arquivo de configuração do YAML

> [!WARNING]
> Os arquivos YAML não podem conter guias como recuo. Use 2 espaços no lugar. Elementos de nível superior não devem ter espaços à esquerda.

No Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

No Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="restart-the-iot-edge-security-manager"></a>Reinicie o gerenciador de segurança do IoT Edge

Se o problema ainda é persistente, você pode tentar reiniciar o Gerenciador de segurança de IoT Edge.

No Linux:

   ```cmd
   sudo systemctl restart iotedge
   ```

No Windows:

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="check-container-logs-for-issues"></a>Verifique os logs de contêiner de problemas

Depois que o daemon de segurança do IoT Edge estiver em execução, examine os logs dos contêineres para detectar problemas. Comece com seus contêineres implantados e examine os contêineres que compõem o tempo de execução de IoT Edge: edgeAgent e edgeHub. Os logs do agente de IoT Edge normalmente fornecem informações sobre o ciclo de vida de cada contêiner. Os logs de Hub de IoT Edge fornecem informações sobre mensagens e roteamento.

```cmd
iotedge logs <container name>
```

Você também pode usar uma chamada de [método direto](how-to-retrieve-iot-edge-logs.md#upload-module-logs) para um módulo em seu dispositivo para carregar os logs desse módulo para o armazenamento de BLOBs do Azure.

## <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Exibir as mensagens que passam pelo hub de IoT Edge

Você pode exibir as mensagens que passam pelo hub de IoT Edge e coletar informações de logs detalhados dos contêineres de tempo de execução. Para ativar os logs detalhados nesses contêineres, defina `RuntimeLogLevel` em seu arquivo de configuração yaml. Para abrir o arquivo:

No Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

No Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Por padrão, o elemento `agent` será parecido com o seguinte exemplo:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

Substitua `env: {}` por:

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > Arquivos YAML não podem conter guias como recuo. Use 2 espaços no lugar. Itens de nível superior não podem ter espaço em branco à esquerda.

Salve o arquivo e reinicie o gerenciador de segurança do IoT Edge.

Você também pode verificar as mensagens que estão sendo enviadas entre os dispositivos do Hub IoT e do IoT Edge. Exiba essas mensagens usando a [extensão do Hub IOT do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). Para obter mais informações, confira [Handy tool when you develop with Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/) (Ferramenta útil ao desenvolver com o Azure IoT).

## <a name="restart-containers"></a>Reinicie os contêineres

Após investigar os logs e as mensagens para obter informações, você poderá tentar reiniciar contêineres:

```cmd
iotedge restart <container name>
```

Reiniciar contêineres de runtime do IoT Edge:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

## <a name="check-your-firewall-and-port-configuration-rules"></a>Verifique as regras de configuração do firewall e da porta

Azure IoT Edge permite a comunicação de um servidor local para a nuvem do Azure usando protocolos de Hub IoT com suporte, consulte [escolhendo um protocolo de comunicação](../iot-hub/iot-hub-devguide-protocols.md). Para maior segurança, os canais de comunicação entre o Azure IoT Edge e o Hub IoT do Azure sempre são configurados para ser de Saída. Essa configuração se baseia no [padrão de comunicação assistida por serviços](/archive/blogs/clemensv/service-assisted-communication-for-connected-devices), que minimiza a superfície de ataque a ser explorada por entidades mal-intencionadas. A comunicação de entrada só é necessária para cenários específicos em que o Hub IoT do Azure precisa enviar mensagens por push para o dispositivo do Azure IoT Edge. Mensagens da nuvem para dispositivo são protegidas usando canais TLS seguros e podem ser ainda mais protegidas usando certificados X.509 e módulos de dispositivo do TPM. O Gerenciador de Segurança do Azure IoT Edge rege como essa comunicação pode ser estabelecida. Confira [Gerenciador de Segurança do IoT Edge](../iot-edge/iot-edge-security-manager.md).

Embora o IoT Edge forneça configuração avançada para proteger o runtime do Azure IoT Edge e os módulos implantados, ele ainda depende da configuração do computador e da rede subjacente. Portanto, é imperativo garantir que as regras adequadas de rede e firewall sejam configuradas para proteger a comunicação em nuvem. A tabela a seguir pode ser usada como uma diretriz quando as regras de firewall de configuração para os servidores subjacentes em que o tempo de execução Azure IoT Edge está hospedado:

|Protocolo|Porta|Entrada|Saída|Diretrizes|
|--|--|--|--|--|
|MQTT|8883|BLOQUEADO (padrão)|BLOQUEADO (padrão)|<ul> <li>Configure a Saída como Aberta ao usar o MQTT como o protocolo de comunicação.<li>Não há suporte para o 1883 para MQTT no IoT Edge. <li>As conexões de Entrada devem ser bloqueadas.</ul>|
|AMQP|5671|BLOQUEADO (padrão)|ABERTO (padrão)|<ul> <li>Protocolo de comunicação padrão do IoT Edge. <li> Precisa ser configurado como Aberto, quando o Azure IoT Edge não está configurado para outros protocolos com suporte ou quando o AMQP é o protocolo de comunicação desejado.<li>Não há suporte para o 5672 para AMQP no IoT Edge.<li>Bloqueie essa porta quando o Azure IoT Edge usar outro protocolo do Hub IoT com suporte.<li>As conexões de Entrada devem ser bloqueadas.</ul></ul>|
|HTTPS|443|BLOQUEADO (padrão)|ABERTO (padrão)|<ul> <li>Configure a Saída para ficar aberta na porta 443 para provisionamento do IoT Edge. Essa configuração é necessária ao usar scripts manuais ou o DPS (serviço de provisionamento de dispositivos) do Azure IoT. <li>A conexão de Entrada deve ser Aberta somente para cenários específicos: <ul> <li>  Se você tiver um gateway transparente com dispositivos de folha que possam enviar solicitações de método. Nesse caso, a Porta 443 não precisa estar aberta para redes externas para conectar-se ao Hub IoT ou fornecer serviços do Hub IoT por meio do Azure IoT Edge. Assim, a regra de entrada pode ser restrita para abrir somente a Entrada da rede interna. <li> Para cenários de cliente para dispositivo (C2D).</ul><li>Não há suporte para a 80 para HTTP no IoT Edge.<li>Se os protocolos que não são HTTP (por exemplo, AMQP ou MQTT) não puderem ser configurados na empresa, as mensagens poderão ser enviadas por meio de WebSockets. A porta 443 será usada para a comunicação do WebSocket nesse caso.</ul>|

## <a name="next-steps"></a>Próximas etapas

Você acha que encontrou um bug na plataforma IoT Edge? [Envie um problema](https://github.com/Azure/iotedge/issues) para que possamos continuar melhorando.

Se você tiver mais dúvidas, crie uma [Solicitação de suporte](https://portal.azure.com/#create/Microsoft.Support) para obter ajuda.