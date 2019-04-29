---
title: Solucionar problemas – Azure IoT Edge | Microsoft Docs
description: Use este artigo para aprender habilidades de diagnóstico padrão para o Azure IoT Edge, como recuperar status e logs do componente e resolver problemas comuns
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 83595bf045de412954c176028babc4f94fcb21e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60612256"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Problemas comuns e resoluções para o Azure IoT Edge

Se você tiver problemas com o Azure IoT Edge no seu ambiente, use este artigo como um guia para a solução desses problemas. 

## <a name="standard-diagnostic-steps"></a>Etapas de diagnóstico padrão 

Quando você encontrar um problema, saiba mais sobre o estado do dispositivo do IoT Edge examinando os logs de contêiner e as mensagens que entram e saem do dispositivo. Use as ferramentas e os comandos desta seção para coletar informações. 

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Verifique o status do Gerenciador de Segurança do IoT Edge e seus logs:

No Linux:
- Para ver o status do Gerenciador de Segurança do IoT Edge:

   ```bash
   sudo systemctl status iotedge
   ```

- Para ver os logs do Gerenciador de Segurança do IoT Edge:

    ```bash
    sudo journalctl -u iotedge -f
    ```

- Para ver mais detalhadamente os logs do Gerenciador de Segurança do IoT Edge:

   - Edite as configurações de daemon iotedge:

      ```bash
      sudo systemctl edit iotedge.service
      ```
   
   - Atualize as seguintes linhas:
    
      ```
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```
    
   - Reinicie o daemon de segurança do IoT Edge:
    
      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

No Windows:
- Para ver o status do Gerenciador de Segurança do IoT Edge:

   ```powershell
   Get-Service iotedge
   ```

- Para ver os logs do Gerenciador de Segurança do IoT Edge:

   ```powershell
   # Displays logs from today, newest at the bottom.
 
   Get-WinEvent -ea SilentlyContinue `
   -FilterHashtable @{ProviderName= "iotedged";
     LogName = "application"; StartTime = [datetime]::Today} |
   select TimeCreated, Message |
   sort-object @{Expression="TimeCreated";Descending=$false} |
   format-table -autosize -wrap
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Se o Gerenciador de segurança de IoT Edge não está em execução, verifique se o arquivo de configuração yaml

> [!WARNING]
> Arquivos YAML não podem conter guias como recuo. Use 2 espaços no lugar.

No Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

No Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Verifique os logs de contêiner de problemas

Depois que o Daemon de segurança de IoT Edge está em execução, examine os logs de contêineres para detectar problemas. Comece com os contêineres implantados e, em seguida, examine os contêineres que compõem o tempo de execução do IoT Edge: edgeAgent e edgeHub. Os logs do agente do IoT Edge normalmente fornecem informações sobre o ciclo de vida de cada contêiner. Os logs do hub do IoT Edge fornecem informações sobre mensagens e roteamento. 

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Exibir as mensagens que passam por meio do hub do IoT Edge

Você pode exibir as mensagens que passam por meio do hub do IoT Edge e coletar informações dos logs detalhados dos contêineres de tempo de execução. Para ativar os logs detalhados nesses contêineres, defina `RuntimeLogLevel` em seu arquivo de configuração yaml. Para abrir o arquivo:

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
   > Arquivos YAML não podem conter guias como recuo. Use 2 espaços no lugar.

Salve o arquivo e reinicie o gerenciador de segurança do IoT Edge.

Você também pode verificar as mensagens que estão sendo enviadas entre os dispositivos do Hub IoT e do IoT Edge. Veja essas mensagens usando a extensão do [Kit de Ferramentas do Hub IoT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (anteriormente conhecida como extensão do Kit de Ferramentas do Azure IoT) para Visual Studio Code. Para obter mais informações, confira [Handy tool when you develop with Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/) (Ferramenta útil ao desenvolver com o Azure IoT).

### <a name="restart-containers"></a>Reinicie os contêineres
Após investigar os logs e as mensagens para obter informações, você poderá tentar reiniciar contêineres:

```
iotedge restart <container name>
```

Reiniciar contêineres de tempo de execução do IoT Edge:

```
iotedge restart edgeAgent && iotedge restart edgeHub
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

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>Agente do IoT Edge é interrompido após cerca de um minuto

O módulo do edgeAgent é iniciado e executado com êxito por cerca de um minuto e para. Os registros indicam que o agente do IoT Edge tenta se conectar ao IoT Hub sobre AMQP e, em seguida, tenta se conectar usando o AMQP com WebSocket. Quando ele falhar, o agente do IoT Edge é encerrado. 

Logs do edgeAgent de exemplo:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>Causa raiz
Uma configuração de rede na rede do host está impedindo que o agente do IoT Edge alcance a rede. O agente tenta se conectar com AMQP (porta 5671) primeiro. Se a conexão falhar, ele tentará usar WebSockets (porta 443).

O tempo de execução do IoT Edge configura uma rede para que cada um dos módulos se comunique. No Linux, esta rede é uma rede de ponte. No Windows, ele usa o NAT. Esse problema é mais comum em dispositivos Windows com contêineres do Windows que usam a rede NAT. 

### <a name="resolution"></a>Resolução
Verifique se existe uma rota para a Internet para os endereços IP atribuídos a esta rede de ponte/NAT. Às vezes, uma configuração de VPN no host substitui a rede do IoT Edge. 

## <a name="iot-edge-hub-fails-to-start"></a>Hub do IoT Edge não pode ser iniciado

O módulo do edgeHub falhar para iniciar e imprime a seguinte mensagem nos logs: 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>Causa raiz
Algum outro processo no computador host tem a porta 443 associada. O hub do IoT Edge mapeia as portas 5671 e 443 para usar em cenários de gateway. Esse mapeamento de porta falhará se outro processo já estiver associado a essa porta. 

### <a name="resolution"></a>Resolução
Localize e interrompa o processo que está usando a porta 443. Esse processo geralmente é um servidor Web.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>Agente do IoT Edge não é possível acessar a imagem de um módulo (403)
Falha de um contêiner na execução e os logs do edgeAgent mostram um erro 403. 

### <a name="root-cause"></a>Causa raiz
O agente do Iot Edge não tem permissões para acessar a imagem de um módulo. 

### <a name="resolution"></a>Resolução
Certifique-se de que suas credenciais de registro estão especificadas corretamente em seu manifesto de implantação

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>O daemon de segurança de IoT Edge falhará com um nome de host inválido

O comando `sudo journalctl -u iotedge` falha e imprime a mensagem a seguir: 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

### <a name="root-cause"></a>Causa raiz
O tempo de execução de IoT Edge só pode oferecer suporte a nomes de host com menos de 64 caracteres. Normalmente, máquinas físicas não têm nomes de host longos, mas o problema é mais comum em uma máquina virtual. Os nomes de host gerados automaticamente para máquinas virtuais do Windows hospedadas no Azure, em particular, tendem a ser longos. 

### <a name="resolution"></a>Resolução
Quando você vir esse erro, você pode resolvê-lo a configurar o nome DNS de sua máquina virtual e, em seguida, definindo o nome DNS de nome de host no comando de instalação.

1. No portal do Azure, navegue até a página de visão geral de sua máquina virtual. 
2. Selecione **configurar** em nome DNS. Caso sua máquina virtual já tenha um nome DNS configurado, você não precisará configurar um novo. 

   ![Configurar o nome DNS da máquina virtual](./media/troubleshoot/configure-dns.png)

3. Forneça um valor para **rótulo do nome DNS** e selecione **Salvar**.
4. Copie o novo nome DNS, que deve estar no formato **\<DNSnamelabel\>.\<vmlocation\>.cloudapp.azure.com**.
5. Dentro da máquina virtual, use o comando a seguir para configurar o tempo de execução do IoT Edge com seu nome DNS:

   - No Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   - No Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Problemas de estabilidade em dispositivos com restrição de recursos 
Talvez você encontre problemas de estabilidade em dispositivos com restrição como o Raspberry Pi, principalmente quando usados como um gateway. Os sintomas incluem exceções fora da memória no módulo de hub do edge, os dispositivos downstream não podem se conectar ou o dispositivo padra de enviar mensagens de telemetria após algumas horas.

### <a name="root-cause"></a>Causa raiz
O hub do IoT Edge, que faz parte do tempo de execução do IoT Edge, é otimizado para desempenho por padrão e tenta alocar blocos grandes de memória. Essa otimização não é ideal para dispositivos de borda com restrição e pode causar problemas de estabilidade.

### <a name="resolution"></a>Resolução
Para o hub do IoT Edge, definir uma variável de ambiente **OptimizeForPerformance** à **falso**. Há duas maneiras de fazer isso:

Na interface do usuário: 

No portal, navegue até **detalhes do dispositivo** > **definir módulos** > **definir configurações de tempo de execução do Edge avançadas**. Criar uma variável de ambiente para o módulo de Hub do Edge chamado *OptimizeForPerformance* que é definido como *falso*.

![OptimizeForPerformance definido como false](./media/troubleshoot/optimizeforperformance-false.png)

**OR**

No manifesto de implantação:

```json
  "edgeHub": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
      "createOptions": <snipped>
    },
    "env": {
      "OptimizeForPerformance": {
          "value": "false"
      }
    },
```
## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Não é possível obter os logs do daemon do IoT Edge no Windows
Se você receber uma EventLogException ao usar `Get-WinEvent` no Windows, verifique suas entradas de registro.

### <a name="root-cause"></a>Causa raiz
O comando do PowerShell `Get-WinEvent` depende de uma entrada de Registro estar presente para encontrar logs por um `ProviderName` específico.

### <a name="resolution"></a>Resolução
Defina uma entrada de registro para o daemon do IoT Edge. Crie um arquivo **iotedge.reg** com o conteúdo a seguir e importe para o Registro do Windows, clicando duas vezes nele ou usando o comando`reg import iotedge.reg`:

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>Falha do módulo do IoT Edge ao enviar uma mensagem para o edgeHub com o erro 404

Um módulo do IoT Edge personalizado falha ao enviar uma mensagem para o edgeHub com um erro 404 `Module not found`. O daemon do IoT Edge imprime a seguinte mensagem nos logs: 

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 ) 
```

### <a name="root-cause"></a>Causa raiz
O daemon do IoT Edge impõe a identificação do processo para todos os módulos que se conectam ao edgeHub por motivos de segurança. Ele verifica se todas as mensagens enviadas por um módulo vêm da ID do processo principal do módulo. Se uma mensagem estiver sendo enviada por um módulo de uma ID de processo diferente da estabelecida inicialmente, ele rejeitará a mensagem com uma mensagem de erro 404.

### <a name="resolution"></a>Resolução
Verifique se a mesma ID de processo sempre é usada pelo módulo personalizado do IoT Edge para enviar mensagens ao edgeHub. Por exemplo, certifique-se `ENTRYPOINT` em vez de `CMD` comando em seu arquivo do Docker, desde `CMD` levará a um processo de identificação para o módulo e outra ID de processo para o comando bash executando o programa principal, enquanto `ENTRYPOINT` levará a um ID de processo único.


## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Regras de configuração de firewall e de porta para implantação do IoT Edge
O Azure IoT Edge permite a comunicação de um servidor de local para nuvem do Azure usando os protocolos com suporte do IoT Hub, consulte [escolhendo um protocolo de comunicação](../iot-hub/iot-hub-devguide-protocols.md). Para maior segurança, os canais de comunicação entre o Azure IoT Edge e o Hub IoT do Azure sempre são configurados para ser de Saída. Essa configuração se baseia no [padrão de comunicação assistida por serviços](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/), que minimiza a superfície de ataque a ser explorada por entidades mal-intencionadas. A comunicação de entrada só é necessária para cenários específicos em que o Hub IoT do Azure precisa enviar mensagens por push para o dispositivo do Azure IoT Edge. Mensagens da nuvem para dispositivo são protegidas usando canais TLS seguros e podem ser ainda mais protegidas usando certificados X.509 e módulos de dispositivo do TPM. O Gerenciador de Segurança do Azure IoT Edge rege como essa comunicação pode ser estabelecida. Confira [Gerenciador de Segurança do IoT Edge](../iot-edge/iot-edge-security-manager.md).

Embora o IoT Edge forneça configuração avançada para proteger o tempo de execução do Azure IoT Edge e os módulos implantados, ele ainda depende da configuração do computador e da rede subjacente. Portanto, é fundamental para garantir que a rede adequada e regras de firewall são configuradas para a borda segura para comunicação em nuvem. A tabela a seguir pode ser usada como uma diretriz quando regras de firewall de configuração para os servidores subjacentes em que o tempo de execução do IoT Edge do Azure está hospedado:

|Protocolo|Porta|Entrada|Saída|Diretrizes|
|--|--|--|--|--|
|MQTT|8883|BLOQUEADO (padrão)|BLOQUEADO (padrão)|<ul> <li>Configure a Saída como Aberta ao usar o MQTT como o protocolo de comunicação.<li>Não há suporte para o 1883 para MQTT no IoT Edge. <li>As conexões de Entrada devem ser bloqueadas.</ul>|
|AMQP|5671|BLOQUEADO (padrão)|ABERTO (padrão)|<ul> <li>Protocolo de comunicação padrão do IoT Edge. <li> Precisa ser configurado como Aberto, quando o Azure IoT Edge não está configurado para outros protocolos com suporte ou quando o AMQP é o protocolo de comunicação desejado.<li>Não há suporte para o 5672 para AMQP no IoT Edge.<li>Bloqueie essa porta quando o Azure IoT Edge usar outro protocolo do Hub IoT com suporte.<li>As conexões de Entrada devem ser bloqueadas.</ul></ul>|
|HTTPS|443|BLOQUEADO (padrão)|ABERTO (padrão)|<ul> <li>Configure a Saída para ficar aberta na porta 443 para provisionamento do IoT Edge. Essa configuração é necessária ao usar scripts manuais ou o DPS (serviço de provisionamento de dispositivos) do Azure IoT. <li>A conexão de Entrada deve ser Aberta somente para cenários específicos: <ul> <li>  Se você tiver um gateway transparente com dispositivos de folha que possam enviar solicitações de método. Nesse caso, a Porta 443 não precisa estar aberta para redes externas para conectar-se ao Hub IoT ou fornecer serviços do Hub IoT por meio do Azure IoT Edge. Assim, a regra de entrada pode ser restrita para abrir somente a Entrada da rede interna. <li> Para cenários de cliente para dispositivo (C2D).</ul><li>Não há suporte para a 80 para HTTP no IoT Edge.<li>Se os protocolos que não são HTTP (por exemplo, AMQP ou MQTT) não puderem ser configurados na empresa, as mensagens poderão ser enviadas por meio de WebSockets. A porta 443 será usada para a comunicação do WebSocket nesse caso.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Módulo de agente do Edge continuamente relatórios 'arquivo de configuração vazia' e nenhum módulo iniciar no dispositivo

O dispositivo tem problemas para iniciar módulos definidos na implantação. Somente o edgeAgent está em execução, mas relata continuamente '... arquivo de configuração vazio'.

### <a name="potential-root-cause"></a>Possível causa raiz
Por padrão, o IoT Edge inicia módulos em sua própria rede de contêiner isolado. O dispositivo pode estar tendo problemas com a resolução de nomes DNS dentro dessa rede privada.

### <a name="resolution"></a>Resolução

**Opção 1: Defina o servidor DNS no contêiner de configurações do mecanismo**

Especifique o servidor DNS para seu ambiente nas configurações do mecanismo de contêiner que serão aplicado a todos os módulos de contêiner iniciados pelo mecanismo. Crie um arquivo chamado `daemon.json` especificando o servidor DNS para usar. Por exemplo: 

```
{
    "dns": ["1.1.1.1"]
}
```

O exemplo acima define o servidor DNS para um serviço DNS publicamente acessível. Se o dispositivo de borda não puder acessar esse IP de seu ambiente, substitua-o pelo endereço do servidor DNS que está acessível.

Local `daemon.json` no local certo para sua plataforma: 

| Plataforma | Local padrão |
| --------- | -------- |
| Linux | `/etc/docker` |
| Host do Windows com contêineres do Windows | `C:\ProgramData\iotedge-moby-data\config` |

Se o local já contiver `daemon.json` do arquivo, adicione o **dns** a ele da chave e salve o arquivo.

*Reinicie o mecanismo de contêiner para as atualizações entrem em vigor*

| Plataforma | Comando |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (Administração do Powershell) | `Restart-Service iotedge-moby -Force` |

**Opção 2: Defina o servidor DNS na implantação do IoT Edge por módulo**

Você pode definir o servidor DNS para cada módulo *createOptions* na implantação do IoT Edge. Por exemplo: 

```
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Certifique-se de definir isso para o *edgeAgent* e *edgeHub* módulos também. 

## <a name="next-steps"></a>Próximas etapas
Você acha que encontrou um bug na plataforma IoT Edge? [Envie um problema](https://github.com/Azure/iotedge/issues) para que possamos continuar melhorando. 

Se você tiver mais dúvidas, crie uma [Solicitação de suporte](https://portal.azure.com/#create/Microsoft.Support) para obter ajuda. 

