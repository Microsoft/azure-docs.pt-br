---
title: Erros comuns-Azure IoT Edge | Microsoft Docs
description: Use este artigo para resolver problemas comuns encontrados ao implantar uma solução de IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 98ee865a3ddf6c26ffe9cb77767f3872b42018d8
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442354"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Problemas comuns e resoluções para o Azure IoT Edge

Use este artigo para encontrar etapas para resolver problemas comuns que podem ocorrer ao implantar soluções de IoT Edge. Se você precisar saber como encontrar logs e erros de seu dispositivo IoT Edge, consulte [solucionar problemas do dispositivo de IOT Edge](troubleshoot.md).

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>IoT Edge agente parar após cerca de um minuto

**Comportamento observado:**

O módulo edgeAgent é iniciado e executado com êxito por cerca de um minuto e, em seguida, para. Os logs indicam que o agente de IoT Edge tenta se conectar ao Hub IoT em AMQP e, em seguida, tenta se conectar usando o AMQP por WebSocket. Quando isso falhar, o agente de IoT Edge será encerrado.

EdgeAgent logs de exemplo:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**Causa raiz:**

Uma configuração de rede na rede do host está impedindo o agente de IoT Edge de acessar a rede. O agente tenta se conectar com AMQP (porta 5671) primeiro. Se a conexão falhar, ele tentará usar WebSockets (porta 443).

O runtime do IoT Edge configura uma rede para que cada um dos módulos se comunique. No Linux, esta rede é uma rede de ponte. No Windows, ele usa o NAT. Esse problema é mais comum em dispositivos Windows com contêineres do Windows que usam a rede NAT.

**Resolução:**

Verifique se existe uma rota para a Internet para os endereços IP atribuídos a esta rede de ponte/NAT. Às vezes, uma configuração de VPN no host substitui a rede do IoT Edge.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>O agente de IoT Edge não pode acessar a imagem de um módulo (403)

**Comportamento observado:**

Um contêiner não é executado e os logs do edgeAgent mostram um erro 403.

**Causa raiz:**

O agente de IoT Edge não tem permissões para acessar a imagem de um módulo.

**Resolução:**

Verifique se suas credenciais de registro estão especificadas corretamente em seu manifesto de implantação.

## <a name="edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device"></a>O módulo do agente do Edge relata ' arquivo de configuração vazio ' e nenhum módulo inicia no dispositivo

**Comportamento observado:**

O dispositivo tem problemas ao iniciar os módulos definidos na implantação. Somente o edgeAgent está em execução, mas está continuamente relatando ' arquivo de configuração vazio... '.

**Causa raiz:**

Por padrão, IoT Edge inicia os módulos em sua própria rede de contêiner isolado. O dispositivo pode estar tendo problemas com a resolução de nomes DNS nessa rede privada.

**Resolução:**

**Opção 1: definir o servidor DNS em configurações do mecanismo de contêiner**

Especifique o servidor DNS para seu ambiente nas configurações do mecanismo de contêiner, que será aplicado a todos os módulos de contêiner iniciados pelo mecanismo. Crie um arquivo chamado `daemon.json` especificando o servidor DNS a ser usado. Por exemplo:

```json
{
    "dns": ["1.1.1.1"]
}
```

O exemplo acima define o servidor DNS para um serviço DNS acessível publicamente. Se o dispositivo de borda não puder acessar esse IP de seu ambiente, substitua-o pelo endereço do servidor DNS que está acessível.

Coloque `daemon.json` no local certo para sua plataforma:

| Plataforma | Location |
| --------- | -------- |
| Linux | `/etc/docker` |
| Host do Windows com contêineres do Windows | `C:\ProgramData\iotedge-moby\config` |

Se o local já contiver o `daemon.json` arquivo, adicione a chave de **DNS** a ele e salve o arquivo.

Reinicie o mecanismo de contêiner para que as atualizações entrem em vigor.

| Plataforma | Comando |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (PowerShell do administrador) | `Restart-Service iotedge-moby -Force` |

**Opção 2: definir o servidor DNS na implantação IoT Edge por módulo**

Você pode definir o servidor DNS para *criaroptions* de cada módulo na implantação do IOT Edge. Por exemplo:

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Certifique-se de definir essa configuração para os módulos *edgeAgent* e *edgeHub* também.

## <a name="iot-edge-hub-fails-to-start"></a>Falha ao iniciar o Hub de IoT Edge

**Comportamento observado:**

Falha ao iniciar o módulo edgeHub. Você pode ver uma mensagem como um dos seguintes erros nos logs:

```output
One or more errors occurred.
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80):
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

Ou

```output
info: edgelet_docker::runtime -- Starting module edgeHub...
warn: edgelet_utils::logging -- Could not start module edgeHub
warn: edgelet_utils::logging --     caused by: failed to create endpoint edgeHub on network nat: hnsCall failed in Win32:  
        The process cannot access the file because it is being used by another process. (0x20)
```

**Causa raiz:**

Algum outro processo no computador host associou uma porta que o módulo edgeHub está tentando associar. O Hub IoT Edge mapeia as portas 443, 5671 e 8883 para uso em cenários de gateway. O módulo não será iniciado se outro processo já tiver associado uma dessas portas.

**Resolução:**

Você pode resolver esse problema de duas maneiras:

Se o dispositivo de IoT Edge estiver funcionando como um dispositivo de gateway, você precisará localizar e parar o processo que está usando a porta 443, 5671 ou 8883. Um erro para a porta 443 geralmente significa que o outro processo é um servidor Web.

Se você não precisar usar o dispositivo IoT Edge como um gateway, poderá remover as associações de porta das opções de criação de módulo do edgeHub. Você pode alterar as opções de criação no portal do Azure ou diretamente no deployment.jsno arquivo.

No portal do Azure:

1. Navegue até o Hub IoT e selecione **IOT Edge**.

2. Selecione o dispositivo IoT Edge que você deseja atualizar.

3. Selecione **definir módulos**.

4. Selecione **configurações de tempo de execução**.

5. Nas configurações do módulo **Hub do Edge** , exclua tudo da caixa de texto **criar opções** .

6. Salve as alterações e crie a implantação.

Na deployment.jsno arquivo:

1. Abra o deployment.jsno arquivo que você aplicou ao seu dispositivo de IoT Edge.

2. Localize as `edgeHub` configurações na seção de propriedades desejadas do edgeAgent:

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
           "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

3. Remova a `createOptions` linha e a vírgula à direita no final da `image` linha antes dela:

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.0"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

4. Salve o arquivo e aplique-o ao seu dispositivo de IoT Edge novamente.

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>O daemon de segurança de IoT Edge falhará com um nome de host inválido

**Comportamento observado:**

A tentativa de [verificar os logs do IOT Edge Security Manager](troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs) falha e imprime a seguinte mensagem:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Causa raiz:**

O runtime do IoT Edge só pode oferecer suporte a nomes de host com menos de 64 caracteres. Normalmente, máquinas físicas não têm nomes de host longos, mas o problema é mais comum em uma máquina virtual. Os nomes de host gerados automaticamente para máquinas virtuais do Windows hospedadas no Azure, em particular, tendem a ser longos.

**Resolução:**

Quando você vir esse erro, você pode resolvê-lo a configurar o nome DNS de sua máquina virtual e, em seguida, definindo o nome DNS de nome de host no comando de instalação.

1. No portal do Azure, navegue até a página de visão geral de sua máquina virtual.
2. Selecione **configurar** em nome DNS. Caso sua máquina virtual já tenha um nome DNS configurado, você não precisará configurar um novo.

   ![Configurar o nome DNS da máquina virtual](./media/troubleshoot/configure-dns.png)

3. Forneça um valor para **rótulo do nome DNS** e selecione **Salvar**.
4. Copie o novo nome DNS, que deve estar no formato **\<DNSnamelabel\> . \<vmlocation\> . cloudapp.azure.com**.
5. Dentro da máquina virtual, use o comando a seguir para configurar o runtime do IoT Edge com seu nome DNS:

   * No Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * No Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Não é possível obter os logs do daemon do IoT Edge no Windows

**Comportamento observado:**

Você Obtém um EventLogexception ao usar `Get-WinEvent` no Windows.

**Causa raiz:**

O comando do PowerShell `Get-WinEvent` depende de uma entrada de Registro estar presente para encontrar logs por um `ProviderName` específico.

**Resolução:**

Defina uma entrada de registro para o daemon do IoT Edge. Crie um arquivo **iotedge.reg** com o conteúdo a seguir e importe para o Registro do Windows, clicando duas vezes nele ou usando o comando`reg import iotedge.reg`:

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="stability-issues-on-smaller-devices"></a>Problemas de estabilidade em dispositivos menores

**Comportamento observado:**

Você pode enfrentar problemas de estabilidade em dispositivos com restrição de recursos, como o Raspberry Pi, especialmente quando usado como um gateway. Os sintomas incluem exceções de memória insuficiente no módulo Hub IoT Edge, dispositivos downstream falham ao se conectar ou o dispositivo não consegue enviar mensagens de telemetria após algumas horas.

**Causa raiz:**

O Hub de IoT Edge, que faz parte do tempo de execução do IoT Edge, é otimizado para desempenho por padrão e tenta alocar grandes partes de memória. Essa otimização não é ideal para dispositivos de borda com restrição e pode causar problemas de estabilidade.

**Resolução:**

Para o Hub de IoT Edge, defina uma variável de ambiente **OptimizeForPerformance** como **false**. Há duas maneiras de definir variáveis de ambiente:

No portal do Azure:

No Hub IOT, selecione o dispositivo IOT Edge e, na página detalhes do dispositivo e selecione **definir**  >  **configurações de tempo de execução** de módulos. Crie uma variável de ambiente para o módulo Hub de IoT Edge chamado *OptimizeForPerformance* que está definido como *false*.

![OptimizeForPerformance definido como false](./media/troubleshoot/optimizeforperformance-false.png)

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

## <a name="iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error"></a>IoT Edge módulo falha ao enviar uma mensagem para edgeHub com erro 404

**Comportamento observado:**

Um módulo de IoT Edge personalizado falha ao enviar uma mensagem para o Hub de IoT Edge com um `Module not found` erro 404. O daemon do IoT Edge imprime a seguinte mensagem nos logs:

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**Causa raiz:**

O daemon do IoT Edge impõe a identificação do processo para todos os módulos que se conectam ao edgeHub por motivos de segurança. Ele verifica se todas as mensagens enviadas por um módulo vêm da ID do processo principal do módulo. Se uma mensagem estiver sendo enviada por um módulo de uma ID de processo diferente da estabelecida inicialmente, ele rejeitará a mensagem com uma mensagem de erro 404.

**Resolução:**

A partir da versão 1.0.7, todos os processos de módulo estão autorizados a se conectar. Para obter mais informações, consulte a [versão de changelog do 1.0.7](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Se a atualização para 1.0.7 não for possível, conclua as etapas a seguir. Verifique se a mesma ID de processo sempre é usada pelo módulo personalizado do IoT Edge para enviar mensagens ao edgeHub. Por exemplo, certifique-se de que, em `ENTRYPOINT` vez do `CMD` comando no arquivo do Docker. O `CMD` comando leva a uma ID de processo para o módulo e outra ID de processo para o comando bash que executa o programa principal, mas `ENTRYPOINT` leva a uma única ID de processo.

## <a name="iot-edge-module-deploys-successfully-then-disappears-from-device"></a>IoT Edge módulo é implantado com êxito e desaparece do dispositivo

**Comportamento observado:**

Depois de definir os módulos para um dispositivo IoT Edge, os módulos são implantados com êxito, mas depois de alguns minutos eles desaparecem do dispositivo e dos detalhes do dispositivo na portal do Azure. Outros módulos do que aqueles definidos também podem aparecer no dispositivo.

**Causa raiz:**

Se uma implantação automática se destinar a um dispositivo, ela terá prioridade sobre a configuração manual dos módulos para um único dispositivo. A funcionalidade **definir módulos** no portal do Azure ou **criar implantação para** a funcionalidade de dispositivo único no Visual Studio Code entrará em vigor por um momento. Você verá os módulos que definiu iniciar no dispositivo. Em seguida, a prioridade da implantação automática é acionada e substitui as propriedades desejadas do dispositivo.

**Resolução:**

Use apenas um tipo de mecanismo de implantação por dispositivo, uma implantação automática ou implantações de dispositivo individuais. Se você tiver várias implantações automáticas direcionadas a um dispositivo, poderá alterar a prioridade ou as descrições de destino para certificar-se de que a correta se aplica a um determinado dispositivo. Você também pode atualizar o dispositivo de atualização para não corresponder mais à descrição de destino da implantação automática.

Para saber mais, veja [Noções básicas sobre implantações automáticas do IoT Edge para dispositivos únicos ou em escala](module-deployment-monitoring.md).

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

## <a name="iot-edge-behind-a-gateway-cannot-perform-http-requests-and-start-edgeagent-module"></a>IoT Edge por trás de um gateway não pode executar solicitações HTTP e iniciar o módulo edgeAgent

**Comportamento observado:**

O daemon IoT Edge está ativo com um arquivo de configuração válido, mas não pode iniciar o módulo edgeAgent. O comando `iotedge list` retorna uma lista vazia. O relatório de logs do IoT Edge daemon `Could not perform HTTP request` .

**Causa raiz:**

IoT Edge dispositivos atrás de um gateway, obtenha suas imagens de módulo do dispositivo de IoT Edge pai especificado no `parent_hostname` campo do arquivo config. YAML. O `Could not perform HTTP request` erro significa que o dispositivo filho não consegue acessar seu dispositivo pai via http.

**Resolução:**

Verifique se o dispositivo de IoT Edge pai pode receber solicitações de entrada do dispositivo de IoT Edge filho. Abra o tráfego de rede nas portas 443 e 6617 para solicitações provenientes do dispositivo filho.

:::moniker-end

## <a name="next-steps"></a>Próximas etapas

Você acha que encontrou um bug na plataforma IoT Edge? [Envie um problema](https://github.com/Azure/iotedge/issues) para que possamos continuar melhorando.

Se você tiver mais dúvidas, crie uma [Solicitação de suporte](https://portal.azure.com/#create/Microsoft.Support) para obter ajuda.
