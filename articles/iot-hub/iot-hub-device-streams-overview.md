---
title: Fluxos de dispositivo do Hub IoT do Azure | Microsoft Docs
description: Visão geral dos fluxos de dispositivo do Hub IoT do Azure, que facilitam os túneis TCP bidirecionais seguros para uma variedade de cenários de comunicação da nuvem para o dispositivo.
author: robinsh
services: iot-hub
ms.service: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
ms.openlocfilehash: 9487fc562fa099d2650aabc8d15fc1449c7fcb5c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97825179"
---
# <a name="iot-hub-device-streams-preview"></a>Fluxos de dispositivos do Hub IoT (versão prévia)

Os *fluxos de dispositivos* do Hub IoT do Azure facilitam a criação de túneis seguros de TCP bidirecionais para uma vários cenários de comunicação da nuvem para dispositivo. Um fluxo de dispositivo é intermediado por um *ponto de extremidade de streaming* do Hub IoT que atua como um proxy entre os pontos de extremidade de serviço e o dispositivo. Essa configuração, ilustrada no diagrama a seguir, é especialmente útil quando os dispositivos estão por trás de um firewall de rede ou residem no interior de uma rede privada. Como tal, os fluxos de dispositivo do Hub IoT ajudam atender à necessidade dos clientes de acessar dispositivos de IoT de forma amigável para o firewall e sem a necessidade de abrir completamente as portas de firewall de rede de entrada ou saída.

!["Visão geral dos fluxos de dispositivo do Hub IoT"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-overview.png )

Ao usar os fluxos de dispositivos do Hub IoT, os dispositivos permanecem seguros e só precisarão abrir as conexões de TCP de saída para o ponto de extremidade de streaming do Hub IoT pela porta 443. Quando um fluxo é estabelecido, os aplicativos do lado do serviço e do lado do dispositivo têm acesso via programação a um objeto de cliente do WebSocket para enviar e receber os bytes brutos entre si. As garantias de confiabilidade e ordenação fornecidas por esse túnel são similares àquelas oferecidas por TCP.

## <a name="benefits"></a>Benefícios

Os fluxos de dispositivos do Hub IoT oferecem os seguintes benefícios:

* **Conectividade segura compatível com firewall:** Os dispositivos IoT podem ser acessados de pontos de extremidade de serviço sem abrir a porta de firewall de entrada no dispositivo ou perímetros de rede (somente a conectividade de saída para o Hub IoT é necessária na porta 443).

* **Autenticação:** Os lados de dispositivo e serviço do túnel precisam se autenticar no Hub IoT usando suas credenciais correspondentes.

* **Criptografia:** Por padrão, os fluxos de dispositivo do Hub IoT usam conexões habilitadas para TLS. Isso garante que o tráfego seja sempre criptografado, independentemente se o aplicativo usa a criptografia ou não.

* **Simplicidade de conectividade:** Em muitos casos, o uso de fluxos de dispositivo elimina a necessidade de configuração complexa de redes virtuais privadas para habilitar a conectividade com dispositivos IoT.

* **Compatibilidade com a pilha TCP/IP:** Os fluxos de dispositivo do Hub IoT podem acomodar o tráfego do aplicativo TCP/IP. Isso significa que uma ampla gama de protocolos proprietários, assim como protocolos baseados em padrões, podem aproveitar esse recurso.

* **Facilidade de uso em configurações de rede privada:** O serviço pode se comunicar com um dispositivo referenciando sua ID do dispositivo, em vez do endereço IP do dispositivo. Isso é útil em situações em que um dispositivo está localizado dentro de uma rede privada e tem um endereço IP privado, ou seu endereço IP é atribuído dinamicamente e é desconhecido para o lado do serviço.

## <a name="device-stream-workflows"></a>Fluxos de trabalho de fluxo de dispositivo

Um fluxo de dispositivo é iniciado quando o serviço solicita uma conexão a um dispositivo, fornecendo sua identificação do dispositivo. Esse fluxo de trabalho se encaixa especialmente no modelo de comunicação cliente/servidor, incluindo SSH e RDP, em que um usuário pretende se conectar remotamente ao servidor SSH ou RDP em execução no dispositivo usando um programa de cliente SSH ou RDP.

O processo de criação de fluxo de dispositivo envolve uma negociação entre o dispositivo, o serviço e os pontos de extremidade de streaming e principais do hub IoT. Enquanto os pontos de extremidade principais do Hub IoT orquestram a criação de um fluxo de dispositivo, o ponto de extremidade de streaming manipula o tráfego que flui entre o serviço e o dispositivo.

### <a name="device-stream-creation-flow"></a>Fluxo de criação do fluxo de dispositivo

A criação programática de um fluxo de dispositivo usando o SDK envolve as etapas a seguir, que também são descritas na figura abaixo:

!["Processo de handshake de fluxo do dispositivo"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-handshake.png)

1. O aplicativo do dispositivo registra um retorno de chamada com antecedência para ser notificado sobre quando um novo fluxo de dispositivo é iniciado no dispositivo. Esta etapa geralmente ocorre quando o dispositivo é inicializado e se conecta ao Hub IoT.

2. O programa do lado do serviço inicia um fluxo de dispositivo quando necessário, fornecendo a identificação do dispositivo (_não_ o endereço IP).

3. O Hub IoT notifica o programa do lado do dispositivo, invocando o retorno de chamada registrado na etapa 1. O dispositivo pode aceitar ou rejeitar a solicitação de iniciação do fluxo. Essa lógica pode ser específica para o cenário do seu aplicativo. Se a solicitação de fluxo for rejeitada pelo dispositivo, o Hub IoT informa ao serviço; caso contrário, as etapas abaixo deverão ser seguidas.

4. O dispositivo cria uma conexão TCP segura de saída para o ponto de extremidade de streaming pela porta 443 e atualiza a conexão para um WebSocket. A URL do ponto de extremidade, bem como as credenciais a serem usadas para autenticar são ambas fornecidas para o dispositivo pelo Hub IoT como parte da solicitação enviada na etapa 3.

5. O serviço é notificado do resultado do dispositivo que aceita o fluxo e cria seu próprio cliente WebSocket para o ponto de extremidade de streaming. Da mesma forma, ele recebe as informações de autenticação e a URL do ponto de extremidade de streaming do Hub IoT.

No processo de handshake acima:

* O processo de handshake deve ser concluído em até 60 segundos (etapas 2 a 5), caso contrário, o handshake falhará por ter ultrapassado o tempo limite e o serviço será notificado.

* Após a conclusão do fluxo de criação de fluxo acima, o ponto de extremidade de streaming atuará como um proxy e transferirá o tráfego entre o serviço e o dispositivo por meio dos respectivos WebSockets.

* O dispositivo e o serviço precisam de conectividade de saída para os pontos de extremidade principais do Hub IoT, assim como do ponto de extremidade de streaming pela porta 443. A URL desses pontos de extremidade está disponível na guia *visão geral* no portal do Hub IOT.

* As garantias de confiabilidade e ordenação de um fluxo estabelecido são similares àquelas oferecidas por TCP.

* Todas as conexões ao Hub IoT e ao ponto de extremidade de streaming usam TLS e são criptografadas.

### <a name="termination-flow"></a>Fluxo de término

Um fluxo estabelecido termina quando qualquer uma das conexões TCP ao gateway é desconectada (pelo serviço ou pelo dispositivo). Isso pode ocorrer voluntariamente fechando o WebSocket em programas do dispositivo ou do serviço ou, involuntariamente, em caso de uma falha de tempo de conexão ultrapassado ou do processo. Após o término da conexão do dispositivo ou do serviço ao ponto de extremidade de streaming, a outra conexão TCP também será encerrada (de forma forçada) e o serviço e o dispositivo serão responsáveis por recriar o fluxo, se necessário.

## <a name="connectivity-requirements"></a>Requisitos de conectividade

Os lados do serviço e do dispositivo de um fluxo de dispositivo devem ser capazes de estabelecer conexões de TLS habilitado ao Hub IoT e ao seu ponto de extremidade de streaming. Isso requer conectividade de saída pela porta 443 para esses pontos de extremidade. O nome do host associado a esses pontos de extremidade pode ser encontrado na guia *Visão geral* do Hub IoT, conforme mostrado na figura abaixo:

!["Pontos de extremidade de fluxo de dispositivo"](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Como alternativa, as informações dos pontos de extremidade podem ser recuperadas usando CLI do Azure na seção Propriedades do Hub, especificamente `property.hostname` e `property.deviceStreams` chaves.

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
> Verifique se a CLI do Azure versão 2.0.57 ou posterior está instalada. Você pode baixar a versão mais recente na página [instalar CLI do Azure](/cli/azure/install-azure-cli) .
>

## <a name="allow-outbound-connectivity-to-the-device-streaming-endpoints"></a>Permitir conectividade de saída para os pontos de extremidade de streaming do dispositivo

Conforme mencionado no início deste artigo, seu dispositivo cria uma conexão de saída para o ponto de extremidade de streaming do Hub IoT durante o processo de inicialização de fluxos de dispositivo. Os firewalls no dispositivo ou em sua rede devem permitir a conectividade de saída para o gateway de streaming pela porta 443 (observe que a comunicação ocorre em uma conexão WebSocket que é criptografada usando TLS).

O nome do host do ponto de extremidade de streaming do dispositivo pode ser encontrado no portal do Hub IoT do Azure, na guia Visão geral. ![ " Pontos de extremidade de fluxo do dispositivo "](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Como alternativa, você pode encontrar essas informações usando a CLI do Azure:

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

> [!NOTE]
> Verifique se a CLI do Azure versão 2.0.57 ou posterior está instalada. Você pode baixar a versão mais recente na página [instalar CLI do Azure](/cli/azure/install-azure-cli) .
>

## <a name="troubleshoot-via-device-streams-resource-logs"></a>Solucionar problemas via logs de recursos de fluxos de dispositivo

Você pode configurar Azure Monitor para coletar os [logs de recursos para fluxos de dispositivo](monitor-iot-hub-reference.md#device-streams-preview) emitidos pelo Hub IOT. Isso pode ser muito útil em cenários de solução de problemas.

Siga as etapas abaixo para criar uma configuração de diagnóstico para enviar logs de fluxos de dispositivo para o Hub IoT para Azure Monitor logs:

1. Em portal do Azure, navegue até o Hub IoT. No painel esquerdo, em **monitoramento**, selecione **configurações de diagnóstico**. Em seguida, selecione **Adicionar configuração de diagnóstico**.

2. Forneça um nome para a configuração de diagnóstico e selecione **DeviceStreams** na lista de logs. Em seguida, selecione **Enviar para log Analytics**. Você será guiado para escolher um espaço de trabalho Log Analytics existente ou criar um novo.

    :::image type="content" source="media/iot-hub-device-streams-overview/device-streams-configure-diagnostics.png" alt-text="Habilitar logs de fluxos de dispositivo":::

3. Depois de criar uma configuração de diagnóstico para enviar os logs de fluxos de dispositivo para um espaço de trabalho Log Analytics, você pode acessar os logs selecionando **logs** em **monitoramento** no painel esquerdo do Hub IOT no portal do Azure. Os logs de fluxos de dispositivo serão exibidos na `AzureDiagnostics` tabela e terão `Category=DeviceStreams` . Lembre-se de que pode levar vários minutos após uma operação para que os logs apareçam na tabela.

   Como mostrado abaixo, a identidade do dispositivo de destino e o resultado da operação também estão disponíveis nos logs.

   !["Acessar logs de fluxo do dispositivo"](./media/iot-hub-device-streams-overview/device-streams-view-logs.png)

Para saber mais sobre como usar Azure Monitor com o Hub IoT, confira [monitorar o Hub IOT](monitor-iot-hub.md). Para obter informações sobre todos os logs de recursos, as métricas e as tabelas disponíveis para o Hub IoT, consulte [monitorando a referência de dados do Hub IOT do Azure](monitor-iot-hub-reference.md).

## <a name="regional-availability"></a>Disponibilidade regional

Durante a visualização pública, os fluxos de dispositivo do Hub IoT estão disponíveis nas regiões EUA Central, EUA Central EUAP, Europa Setentrional e sudeste asiático. Certifique-se de criar seu hub em uma dessas regiões.

## <a name="sdk-availability"></a>Disponibilidade do SDK

Dois lados de cada fluxo (no lado do dispositivo e do serviço) usam o SDK do Hub IoT para estabelecer o túnel. Durante a versão prévia pública, os clientes podem escolher entre as seguintes linguagens do SDK:

* Os SDKs de C e C# dão suporte a fluxos de dispositivos no lado do dispositivo.

* Os SDKs de Node.js e C# dão suporte a fluxos de dispositivos no lado do serviço.

## <a name="iot-hub-device-stream-samples"></a>Amostras de fluxo de dispositivo do Hub IoT

Há duas [amostras de início rápido](./index.yml) disponíveis na página do Hub IOT. Eles demonstram o uso de fluxos de dispositivo por aplicativos.

* O exemplo *Echo* demonstra o uso programático de fluxos de dispositivo (chamando diretamente a API do SDK).

* O exemplo de *proxy local* demonstra o túnel de tráfego de aplicativo do servidor/cliente pronto para uso (como Web, RDP ou SSH) por meio de fluxos de dispositivo.

Esses exemplos são abordados em mais detalhes abaixo.

### <a name="echo-sample"></a>Exemplo de eco

O exemplo de eco demonstra o uso via programação dos fluxos de dispositivos para enviar e receber bytes entre os aplicativos de serviço e de dispositivo. Observe que você pode usar programas de serviço e dispositivo em diferentes idiomas. Por exemplo, você pode usar o programa de dispositivo C com o programa de serviço C#.

Aqui estão os exemplos de eco:

* [Programa de serviço e serviço C#](quickstart-device-streams-echo-csharp.md)

* [ Programa de serviço deNode.js](quickstart-device-streams-echo-nodejs.md)

* [Programa de dispositivo C](quickstart-device-streams-echo-c.md)

### <a name="local-proxy-sample-for-ssh-or-rdp"></a>Exemplo de proxy local (para SSH ou RDP)

O exemplo de proxy local demonstra uma maneira de habilitar o túnel de tráfego de um aplicativo existente que envolva a comunicação entre um programa de cliente e de servidor. Essa configuração funciona para protocolos de cliente/servidor, como SSH e RDP, em que o lado do serviço atua como um cliente (executando programas de cliente SSH ou RDP) e o lado do dispositivo atua como o servidor (executando programas de daemon SSH ou de servidor RDP).

Esta seção descreve o uso de fluxos de dispositivos para habilitar que os usuários usem SSH em um dispositivo em relação a fluxos de dispositivos (o caso para RDP ou outro aplicativo de cliente/servidor é semelhante e utiliza a porta correspondente do protocolo).

A configuração utiliza dois programas de *proxy local* mostrados na figura abaixo, ou seja, *proxy local do dispositivo* e *proxy local do serviço*. Os programas de proxy locais são responsáveis por executar o [handshake de início do fluxo de dispositivo](#device-stream-creation-flow) com o Hub IoT e interagir com o cliente SSH e o daemon SSH usando os soquetes de cliente/servidor comuns.

!["Configuração de proxy do fluxo do dispositivo para SSH/RDP"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-ssh.png)

1. O usuário executa o proxy local de serviço para iniciar um fluxo de dispositivo para o dispositivo.

2. O proxy local de dispositivo aceita a solicitação de início do fluxo e o túnel é estabelecido com o ponto de extremidade de streaming do Hub IoT (como discutido acima).

3. O proxy local de dispositivo conecta-se ao ponto de extremidade do daemon SSH escutando na porta 22 do dispositivo.

4. O proxy local de serviço escuta em uma porta designada aguardando novas conexões SSH do usuário (a porta 2222 é usada no exemplo, mas isso pode ser configurado para qualquer outra porta disponível). O usuário encaminha o cliente SSH para a porta do proxy local de serviço local no localhost.

### <a name="notes"></a>Observações

* As etapas acima concluem um túnel de ponta a ponta entre o cliente SSH (à direita) e o daemon SSH (à esquerda). Parte dessa conectividade de ponta a ponta envolve o envio de tráfego em um fluxo de dispositivo ao Hub IoT.

* As setas na figura indicam a direção na qual as conexões são estabelecidas entre os pontos de extremidade. Especificamente, observe que não há nenhuma conexão de entrada até o dispositivo (ela é frequentemente bloqueada por um firewall).

* A opção de usar a porta 2222 no proxy local de serviço é arbitrária. O proxy pode ser configurado para usar qualquer outra porta disponível.

* A escolha da porta 22 é dependente de protocolo e específica ao SSH, nesse caso. No caso do RDP, a porta 3389 deve ser usada. Isso pode ser configurado nos programas de exemplo fornecidos.

Use os links abaixo para obter instruções sobre como executar os programas de proxy local na linguagem de sua escolha. Semelhante ao [exemplo de eco](#echo-sample), é possível executar os programas de proxy locais de dispositivo e serviço em linguagens diferentes, já que eles são totalmente interoperáveis.

* [Programa de serviço e serviço C#](quickstart-device-streams-proxy-csharp.md)

* [ Programa de serviço deNode.js](quickstart-device-streams-proxy-nodejs.md)

* [Programa de dispositivo C](quickstart-device-streams-proxy-c.md)

## <a name="next-steps"></a>Próximas etapas

Use os links abaixo para saber mais sobre os fluxos de dispositivo.

> [!div class="nextstepaction"]
> [Fluxos de dispositivo no IoT show (canal 9)](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fchannel9.msdn.com%2FShows%2FInternet-of-Things-Show%2FAzure-IoT-Hub-Device-Streams&data=02%7C01%7Crezas%40microsoft.com%7Cc3486254a89a43edea7c08d67a88bcea%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636831125031268909&sdata=S6u9qiehBN4tmgII637uJeVubUll0IZ4p2ddtG5pDBc%3D&reserved=0)
