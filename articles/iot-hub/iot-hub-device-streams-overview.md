---
title: Fluxos de dispositivos do Hub IoT do Azure (versão prévia) | Microsoft Docs
description: Visão geral dos fluxos de dispositivo do IoT Hub
author: robinsh
services: iot-hub
ms.service: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: robinsh
ms.openlocfilehash: 86f8c6e527f58a7c4cfca6233165f0ab05426409
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672566"
---
# <a name="iot-hub-device-streams-preview"></a>Fluxos de dispositivos do Hub IoT (versão prévia)

Os *fluxos de dispositivos* do Hub IoT do Azure facilitam a criação de túneis seguros de TCP bidirecionais para uma vários cenários de comunicação da nuvem para dispositivo. Um fluxo de dispositivo é intermediado por um *ponto de extremidade de streaming* do Hub IoT que atua como um proxy entre os pontos de extremidade de serviço e o dispositivo. Essa configuração, ilustrada no diagrama a seguir, é especialmente útil quando os dispositivos estão por trás de um firewall de rede ou residem no interior de uma rede privada. Como tal, os fluxos de dispositivo do Hub IoT ajudam atender à necessidade dos clientes de acessar dispositivos de IoT de forma amigável para o firewall e sem a necessidade de abrir completamente as portas de firewall de rede de entrada ou saída.

!["O IoT Hub fluxos visão geral do dispositivo"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-overview.png )

Ao usar os fluxos de dispositivos do Hub IoT, os dispositivos permanecem seguros e só precisarão abrir as conexões de TCP de saída para o ponto de extremidade de streaming do Hub IoT pela porta 443. Quando um fluxo é estabelecido, os aplicativos do lado do serviço e do lado do dispositivo têm acesso via programação a um objeto de cliente do WebSocket para enviar e receber os bytes brutos entre si. As garantias de confiabilidade e ordenação fornecidas por esse túnel são similares àquelas oferecidas por TCP.

## <a name="benefits"></a>Benefícios

Os fluxos de dispositivos do Hub IoT oferecem os seguintes benefícios:

* **Conectividade segura amigável para o firewall:** Os dispositivos de IoT podem ser acessados a partir dos pontos de extremidade de serviço sem a abertura da porta de firewall de entrada no perímetro de rede ou dispositivo (somente a conectividade de saída para o Hub IoT é necessária na porta 443).

* **Autenticação:** Os lados do dispositivo e do serviço do túnel precisam se autenticar com o Hub IoT usando suas credenciais correspondentes.

* **Criptografia:** Por padrão, os fluxos de dispositivos do Hub IoT usam conexões de TLS. Isso garante que o tráfego seja sempre criptografado, independentemente se o aplicativo usa a criptografia ou não.

* **Simplicidade de conectividade:** Em muitos casos, o uso de fluxos de dispositivos elimina a necessidade de uma configuração complexa de redes virtuais privadas para habilitar a conectividade a dispositivos de IoT.

* **Compatibilidade com a pilha TCP/IP:** Os fluxos de dispositivos do Hub IoT podem acomodar o tráfego de aplicativo de TCP/IP. Isso significa que uma ampla gama de protocolos proprietários, assim como protocolos baseados em padrões, podem aproveitar esse recurso.

* **Facilidade de uso em configurações de rede privada:** O serviço pode se comunicar com um dispositivo, fazendo referência à identificação do dispositivo, em vez do endereço IP do dispositivo. Isso é útil em situações em que um dispositivo está localizado dentro de uma rede privada e tem um endereço IP privado, ou seu endereço IP é atribuído dinamicamente e é desconhecido para o lado do serviço.

## <a name="device-stream-workflows"></a>Fluxos de trabalho de fluxo de dispositivo

Um fluxo de dispositivo é iniciado quando o serviço solicita uma conexão a um dispositivo, fornecendo sua identificação do dispositivo. Esse fluxo de trabalho se encaixa especialmente no modelo de comunicação cliente/servidor, incluindo SSH e RDP, em que um usuário pretende se conectar remotamente ao servidor SSH ou RDP em execução no dispositivo usando um programa de cliente SSH ou RDP.

O processo de criação de fluxo de dispositivo envolve uma negociação entre o dispositivo, o serviço e os pontos de extremidade de streaming e principais do hub IoT. Enquanto os pontos de extremidade principais do Hub IoT orquestram a criação de um fluxo de dispositivo, o ponto de extremidade de streaming manipula o tráfego que flui entre o serviço e o dispositivo.

### <a name="device-stream-creation-flow"></a>Fluxo de criação do fluxo de dispositivo

A criação programática de um fluxo de dispositivo usando o SDK envolve as etapas a seguir, que também são descritas na figura abaixo:

!["Processo de handshake de fluxo de dispositivo"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-handshake.png)

1. O aplicativo do dispositivo registra um retorno de chamada com antecedência para ser notificado sobre quando um novo fluxo de dispositivo é iniciado no dispositivo. Esta etapa geralmente ocorre quando o dispositivo é inicializado e se conecta ao Hub IoT.

2. O programa do lado do serviço inicia um fluxo de dispositivo quando necessário, fornecendo a identificação do dispositivo (_não_ o endereço IP).

3. O Hub IoT notifica o programa do lado do dispositivo, invocando o retorno de chamada registrado na etapa 1. O dispositivo pode aceitar ou rejeitar a solicitação de iniciação do fluxo. Essa lógica pode ser específica para o cenário do seu aplicativo. Se a solicitação de fluxo for rejeitada pelo dispositivo, o Hub IoT informa ao serviço; caso contrário, as etapas abaixo deverão ser seguidas.

4. O dispositivo cria uma conexão TCP segura de saída para o ponto de extremidade de streaming pela porta 443 e atualiza a conexão para um WebSocket. A URL do ponto de extremidade, bem como as credenciais a serem usadas para autenticar são ambas fornecidas para o dispositivo pelo Hub IoT como parte da solicitação enviada na etapa 3.

5. O serviço é notificado do resultado do dispositivo que aceita o fluxo e cria seu próprio cliente WebSocket para o ponto de extremidade de streaming. Da mesma forma, ele recebe as informações de autenticação e a URL do ponto de extremidade de streaming do Hub IoT.

No processo de handshake acima:

* O processo de handshake deve ser concluído em até 60 segundos (etapas 2 a 5), caso contrário, o handshake falhará por ter ultrapassado o tempo limite e o serviço será notificado.

* Após a conclusão do fluxo de criação de fluxo acima, o ponto de extremidade de streaming atuará como um proxy e transferirá o tráfego entre o serviço e o dispositivo por meio dos respectivos WebSockets.

* O dispositivo e o serviço precisam de conectividade de saída para os pontos de extremidade principais do Hub IoT, assim como do ponto de extremidade de streaming pela porta 443. A URL desses pontos de extremidade está disponível na guia *Visão geral* no portal do Hub IoT.

* As garantias de confiabilidade e ordenação de um fluxo estabelecido são similares àquelas oferecidas por TCP.

* Todas as conexões ao Hub IoT e ao ponto de extremidade de streaming usam TLS e são criptografadas.

### <a name="termination-flow"></a>Fluxo de término

Um fluxo estabelecido termina quando qualquer uma das conexões TCP ao gateway é desconectada (pelo serviço ou pelo dispositivo). Isso pode ocorrer voluntariamente fechando o WebSocket em programas do dispositivo ou do serviço ou, involuntariamente, em caso de uma falha de tempo de conexão ultrapassado ou do processo. Após o término da conexão do dispositivo ou do serviço ao ponto de extremidade de streaming, a outra conexão TCP também será encerrada (de forma forçada) e o serviço e o dispositivo serão responsáveis por recriar o fluxo, se necessário.

## <a name="connectivity-requirements"></a>Requisitos de conectividade

Os lados do serviço e do dispositivo de um fluxo de dispositivo devem ser capazes de estabelecer conexões de TLS habilitado ao Hub IoT e ao seu ponto de extremidade de streaming. Isso requer conectividade de saída pela porta 443 para esses pontos de extremidade. O nome do host associado a esses pontos de extremidade pode ser encontrado na guia *Visão geral* do Hub IoT, conforme mostrado na figura abaixo:

!["Pontos de extremidade de fluxo de dispositivo"](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Como alternativa, as informações de pontos de extremidade podem ser recuperadas usando a CLI do Azure na seção de propriedades do hub, especificamente, `property.hostname` e `property.deviceStreams` chaves.

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

A saída é um objeto JSON com todos os pontos de extremidade que seu dispositivo e serviço do hub possam precisar para se conectarem a fim de estabelecer um fluxo de dispositivo.

```json
{
  "streamingEndpoints": [
    "https://<YourIoTHubName>.<region-stamp>.streams.azure-devices.net"
  ]
}
```

> [!NOTE]
> Verifique se a CLI do Azure versão 2.0.57 ou posterior está instalada. Você pode baixar a versão mais recente do [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) página.
>

## <a name="allow-outbound-connectivity-to-the-device-streaming-endpoints"></a>Permitir a conectividade de saída para o dispositivo a pontos de extremidade de streaming

Conforme mencionado no início deste artigo, o dispositivo cria uma conexão de saída para o ponto de extremidade de streaming do IoT Hub durante o processo de iniciação de fluxos de dispositivo. Os firewalls no dispositivo ou em sua rede devem permitir a conectividade de saída para o gateway de streaming pela porta 443 (observe que a comunicação ocorre em uma conexão WebSocket que é criptografada usando TLS).

O nome do host do ponto de extremidade de streaming do dispositivo pode ser encontrado no portal do Hub IoT do Azure, na guia Visão geral. !["Pontos de extremidade de fluxo de dispositivo"](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Como alternativa, você pode encontrar essas informações usando a CLI do Azure:

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

> [!NOTE]
> Verifique se a CLI do Azure versão 2.0.57 ou posterior está instalada. Você pode baixar a versão mais recente do [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) página.
>

## <a name="troubleshoot-via-device-streams-activity-logs"></a>Solucionar problemas por meio de logs de atividades de fluxos de dispositivos

Você pode configurar logs do Azure Monitor para coletar o log de atividades de fluxos de dispositivo no IoT Hub. Isso pode ser muito útil em cenários de solução de problemas.

Siga as etapas abaixo para configurar logs do Azure Monitor para atividades de fluxo de dispositivo do IoT Hub:

1. Navegue até a guia *Configurações de diagnóstico* em seu Hub IoT e, em seguida, clique no link *Ativar diagnóstico*.

   !["Habilitando os logs de diagnóstico"](./media/iot-hub-device-streams-overview/device-streams-diagnostics-settings-pane.png)

2. Forneça um nome para as configurações de diagnóstico e, em seguida, escolha a opção *Enviar para o Log Analytics*. Você será guiado para escolher um recurso do espaço de trabalho do Log Analytics existente ou crie um novo. Além disso, verifique os *DeviceStreams* na lista.

    !["Habilitar logs de fluxos de dispositivo"](./media/iot-hub-device-streams-overview/device-streams-configure-diagnostics.png)

3. Agora você pode acessar seus logs de fluxos de dispositivo na guia *Logs* no portal do seu Hub IoT. Os logs de atividades de fluxo de dispositivo aparecerão na tabela `AzureDiagnostics` e possuem `Category=DeviceStreams`.

   Conforme mostrado abaixo, a identidade do dispositivo de destino e o resultado da operação também está disponível nos logs.

   !["Acesso a logs de fluxo de dispositivo"](./media/iot-hub-device-streams-overview/device-streams-view-logs.png)

## <a name="regional-availability"></a>Disponibilidade regional

Durante a visualização pública, os fluxos de dispositivo do Hub IoT estão disponíveis nas regiões: EUA Central e EUA Central EUAP. Certifique-se de criar seu hub em uma dessas regiões.

## <a name="sdk-availability"></a>Disponibilidade do SDK

Dois lados de cada fluxo (no lado do dispositivo e do serviço) usam o SDK do Hub IoT para estabelecer o túnel. Durante a versão prévia pública, os clientes podem escolher entre as seguintes linguagens do SDK:

* Os SDKs de C e C# dão suporte a fluxos de dispositivos no lado do dispositivo.

* Os SDKs de Node.js e C# dão suporte a fluxos de dispositivos no lado do serviço.

## <a name="iot-hub-device-stream-samples"></a>Exemplos de fluxo de dispositivo do IoT Hub

Há dois [exemplos de início rápido](/azure/iot-hub) disponível na página de IoT Hub. Eles demonstram o uso de fluxos de dispositivo por aplicativos.

* O *echo* exemplo demonstra o uso programático dos fluxos de dispositivo (chamando a API de SDK diretamente).

* O exemplo de *proxy local* demonstra o túnel de tráfego de aplicativo do servidor/cliente pronto para uso (como Web, RDP ou SSH) por meio de fluxos de dispositivo.

Esses exemplos são abordados em mais detalhes abaixo.

### <a name="echo-sample"></a>Exemplo de echo

O exemplo de eco demonstra o uso via programação dos fluxos de dispositivos para enviar e receber bytes entre os aplicativos de serviço e de dispositivo. Observe que você pode usar programas de serviço e dispositivo em diferentes idiomas. Por exemplo, você pode usar o programa do dispositivo C com o C# programa de serviço.

Aqui estão os exemplos de eco:

* [C#serviço e o programa de serviço](quickstart-device-streams-echo-csharp.md)

* [Programa de serviço do Node. js](quickstart-device-streams-echo-nodejs.md)

* [Programa de dispositivo de C](quickstart-device-streams-echo-c.md)

### <a name="local-proxy-sample-for-ssh-or-rdp"></a>Exemplo de proxy local (para o SSH ou RDP)

O exemplo de proxy local demonstra uma maneira de habilitar o túnel de tráfego de um aplicativo existente que envolva a comunicação entre um programa de cliente e de servidor. Essa configuração funciona para protocolos de cliente/servidor, como SSH e RDP, em que o lado do serviço atua como um cliente (executando programas de cliente SSH ou RDP) e o lado do dispositivo atua como o servidor (executando programas de daemon SSH ou de servidor RDP).

Esta seção descreve o uso de fluxos de dispositivos para habilitar que os usuários usem SSH em um dispositivo em relação a fluxos de dispositivos (o caso para RDP ou outro aplicativo de cliente/servidor é semelhante e utiliza a porta correspondente do protocolo).

A configuração utiliza dois programas de *proxy local* mostrados na figura abaixo, ou seja, *proxy local do dispositivo* e *proxy local do serviço*. Os programas de proxy locais são responsáveis por executar o [handshake de início do fluxo de dispositivo](#device-stream-creation-flow) com o Hub IoT e interagir com o cliente SSH e o daemon SSH usando os soquetes de cliente/servidor comuns.

!["Dispositivo fluxo configuração do proxy para RDP/SSH"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-ssh.png)

1. O usuário executa o proxy local de serviço para iniciar um fluxo de dispositivo para o dispositivo.

2. O proxy local de dispositivo aceita a solicitação de início do fluxo e o túnel é estabelecido com o ponto de extremidade de streaming do Hub IoT (como discutido acima).

3. O proxy local de dispositivo conecta-se ao ponto de extremidade do daemon SSH escutando na porta 22 do dispositivo.

4. O proxy local de serviço escuta em uma porta designada aguardando novas conexões SSH do usuário (a porta 2222 é usada no exemplo, mas isso pode ser configurado para qualquer outra porta disponível). O usuário encaminha o cliente SSH para a porta do proxy local de serviço local no localhost.

### <a name="notes"></a>Observações

* As etapas acima concluem um túnel de ponta a ponta entre o cliente SSH (à direita) e o daemon SSH (à esquerda). Parte dessa conectividade de ponta a ponta envolve o envio de tráfego em um fluxo de dispositivo ao Hub IoT.

* As setas na figura indicam a direção na qual as conexões são estabelecidas entre os pontos de extremidade. Especificamente, observe que não há nenhuma conexão de entrada até o dispositivo (ela é frequentemente bloqueada por um firewall).

* A opção de usar a porta 2222 no proxy local de serviço é arbitrária. O proxy pode ser configurado para usar qualquer outra porta disponível.

* A escolha da porta 22 é dependente de protocolo e específico para o SSH, nesse caso. No caso do RDP, a porta 3389 deve ser usada. Isso pode ser configurado nos programas de exemplo fornecidos.

Use os links abaixo para obter instruções sobre como executar os programas de proxy local na linguagem de sua escolha. Semelhante ao [exemplo de eco](#echo-sample), é possível executar os programas de proxy locais de dispositivo e serviço em linguagens diferentes, já que eles são totalmente interoperáveis.

* [C#serviço e o programa de serviço](quickstart-device-streams-proxy-csharp.md)

* [Programa de serviço do Node. js](quickstart-device-streams-proxy-nodejs.md)

* [Programa de dispositivo de C](quickstart-device-streams-proxy-c.md)

## <a name="next-steps"></a>Próximas etapas

Use os links abaixo para saber mais sobre fluxos de dispositivo.

> [!div class="nextstepaction"]
> [Fluxos de dispositivo no IoT mostram (Channel 9)](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fchannel9.msdn.com%2FShows%2FInternet-of-Things-Show%2FAzure-IoT-Hub-Device-Streams&data=02%7C01%7Crezas%40microsoft.com%7Cc3486254a89a43edea7c08d67a88bcea%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636831125031268909&sdata=S6u9qiehBN4tmgII637uJeVubUll0IZ4p2ddtG5pDBc%3D&reserved=0)
