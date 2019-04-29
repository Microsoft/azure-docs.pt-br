---
title: Passo a passo da solução de Fábrica Conectada - Azure | Microsoft Docs
description: Uma descrição da Fábrica Conectada do acelerador de solução do Azure IoT e sua arquitetura.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 950d248d2525f053981c8642ee2d39021b9a0494
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61449866"
---
# <a name="connected-factory-solution-accelerator-walkthrough"></a>Passo a passo do acelerador de solução da Fábrica Conectada

O [acelerador de solução][lnk-preconfigured-solutions] da Fábrica Conectada é uma implementação de uma solução industrial de ponta a ponta que:

* Conecta-se a dispositivos industriais simulados que executam servidores OPC UA em linhas de produção de fábrica simuladas e a dispositivos de servidor OPC UA reais. Para obter mais informações sobre o OPC UA, consulte as [Perguntas frequentes sobre Connected Factory](iot-accelerators-faq-cf.md).
* Mostra KPIs operacionais e OEE desses dispositivos e de linhas de produção.
* Demonstra como um aplicativo baseado em nuvem pode ser usado para interagir com sistemas de servidor UA OPC.
* Permite que você conecte seus próprios dispositivos de servidor OPC UA.
* Permite procurar e modificar os dados de servidor UA OPC.
* Integra-se ao serviço TSI (Azure Time Series Insights) para fornecer exibições personalizadas de dados dos servidores UA OPC.

Você pode usar a solução como um ponto de partida para sua própria implementação e [personalizar][lnk-customize] para atender às suas próprias necessidades de negócios específicas.

Este artigo explica alguns dos principais elementos da solução da Fábrica Conectada para que você possa entender como ela funciona. O artigo também descreve como os dados fluem através da solução. Esse conhecimento ajuda a:

* Solucionar problemas na solução.
* Planejar como personalizar a solução para atender a seus próprios requisitos específicos.
* Criar sua própria solução IoT que usa os serviços do Azure.

Para saber mais, confira as [Perguntas frequentes sobre a Fábrica Conectada](iot-accelerators-faq-cf.md).

## <a name="logical-architecture"></a>Arquitetura lógica

O seguinte diagrama descreve os componentes lógicos do acelerador de solução:

![Arquitetura lógica da Fábrica Conectada][connected-factory-logical]

## <a name="communication-patterns"></a>Padrões de comunicação

A solução usa a [especificação OPC UA Pub/Sub](https://opcfoundation.org/news/opc-foundation-news/opc-foundation-announces-support-of-publish-subscribe-for-opc-ua/) para enviar dados de telemetria do OPC UA ao Hub IoT no formato JSON. A solução usa o módulo [Publicador OPC](https://github.com/Azure/iot-edge-opc-publisher) do IoT Edge para essa finalidade.

A solução também tem um cliente de OPC UA integrado a um aplicativo Web que pode estabelecer conexões com servidores OPC UA locais. O cliente usa um [proxy reverso](https://wikipedia.org/wiki/Reverse_proxy) e recebe ajuda do Hub IoT para fazer a conexão sem a necessidade de abrir portas no firewall local. Esse padrão de comunicação é chamado de comunicação auxiliada por serviço. A solução usa o módulo [Proxy OPC](https://github.com/Azure/iot-edge-opc-proxy/) do IoT Edge para essa finalidade.


## <a name="simulation"></a>Simulação

As estações simuladas e os sistemas MES (simulação de execução de fabricação) simulados formam uma linha de produção de fábrica. Os dispositivos simulados e o Módulo OPC Publisher se baseiam no [Padrão OPC UA .NET][lnk-OPC-UA-NET-Standard] publicado pela OPC Foundation.

O proxy OPC e o fornecedor OPC são implementados como módulos com base no [Azure IoT Edge][lnk-Azure-IoT-Gateway]. Cada linha de produção simulada tem um gateway conectado.

Todos os componentes de simulação executados em contêineres Docker hospedados em uma VM do Linux do Azure. A simulação é configurada para executar oito linhas de produção simuladas por padrão.

## <a name="simulated-production-line"></a>Linha de produção simulada

Uma linha de produção fabrica peças. É composto de diferentes estações: uma estação de montagem, uma estação de teste e uma estação de embalagem.

A simulação é executada e atualiza os dados disponibilizados pelos nós do OPC UA. Todas as estações de linha de produção simulada são organizadas por MES por meio de UA OPC.

## <a name="simulated-manufacturing-execution-system"></a>Sistema de execução de fabricação simulado

O MES monitora cada estação na linha de produção por meio de UA OPC para detectar alterações de status da estação. Ele chama métodos OPC UA para controlar as estações e passa um produto de uma estação para a próxima até que esteja completo.

## <a name="gateway-opc-publisher-module"></a>Módulo de editor de gateway OPC

O Módulo OPC Publisher conecta-se aos servidores OPC UA da estação e assina os nós OPC para publicação. O módulo:

1. Converte os dados do nó em formato JSON.
1. Criptografa o JSON.
1. Envia o JSON ao IoT Hub como OPC UA pub/sub mensagens.

O módulo OPC Publisher exige apenas uma porta de saída https (443) e pode trabalhar com a infraestrutura corporativa existente.

## <a name="gateway-opc-proxy-module"></a>Módulo de proxy de gateway OPC

O módulo de Proxy OPC UA de Gateway encapsula mensagens de controle e comando OPC UA binário e requer apenas uma porta de saída https (443). Pode funcionar com a infraestrutura existente da empresa, inclusive Proxies da Web.

It uses IoT Hub Device methods to transfer packetized TCP/IP data at the application layer to ensure endpoint trust, data encryption, and integrity using SSL/TLS.

O protocolo binário OPC UA retransmitido pelo próprio proxy usa criptografia e autenticação UA.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

O Módulo Gateway OPC Publisher assina nós de servidor OPC UA para detectar alterações nos valores de dados. Se for detectada uma alteração de dados em um de nós, esse módulo enviará mensagens ao Hub IoT do Azure.

O Hub IoT fornece uma fonte de eventos para o Azure TSI. O TSI armazena dados por 30 dias com base em carimbos de data/hora anexados às mensagens. Os dados incluem:

* ApplicationUri UA OPC
* NodeId UA OPC
* Valor do nó
* Carimbo de data/hora de origem
* DisplayName UA OPC

Atualmente, o TSI não permite que os clientes personalizem por quanto tempo desejam manter os dados.

Consultas TSI contra dados de nó usando um **SearchSpan** baseado em tempo e agregados por **OPC UA ApplicationUri** ou **OPC UA NodeId** ou **OPC UA DisplayName**.

Para recuperar os dados para os indicadores OEE e KPI e os gráficos de séries temporais, a solução agrega dados pela contagem de eventos, **Soma**, **Média**, **Mín.**, e **Máx**.

A série temporal é criada usando um processo diferente. A solução calcula os valores OEE e KPI dos dados de base da estação e faz com que os valores subam para as linhas de produção, fábricas e empresas.

Além disso, a série temporal para a topologia OEE e KPI é calculada no aplicativo, sempre que um período de tempo exibido está pronto. Por exemplo, o modo de exibição de dia é atualizado a cada hora cheia.

A exibição da série temporal dos dados de nó vem diretamente de TSI usando uma agregação para o período de tempo.

## <a name="iot-hub"></a>Hub IoT
O [hub IoT][lnk-IoT Hub] recebe os dados enviados do Módulo OPC Publisher para a nuvem e os disponibiliza para o serviço Azure TSI. 

O Hub IoT na solução também:
- Mantém um registro de identidade que armazena as IDs de todos os Módulos de Editor OPC e todos os Módulos de Proxy OPC.
- É usado como o canal de transporte para a comunicação bidirecional do Módulo de Proxy OPC.

## <a name="azure-storage"></a>Armazenamento do Azure
A solução usa o armazenamento de blobs do Azure como armazenamento em disco para a VM e para armazenar dados de implantação.

## <a name="web-app"></a>Aplicativo Web
O aplicativo da Web implementado como parte do acelerador de solução inclui um cliente OPC UA integrado, processamento de alertas e visualização de telemetria.

## <a name="telemetry-data-flow"></a>Fluxo de dados de telemetria

![Fluxo de dados de telemetria](./media/iot-accelerators-connected-factory-sample-walkthrough/telemetry_dataflow.png)

### <a name="flow-steps"></a>Etapas do fluxo

1. O Publicador de OPC lê os certificados X509 de agente do usuário de OPC e as credenciais de segurança de Hub IoT necessários do repositório de certificados local.
    - Se necessário, o Publicador de OPC cria e armazena os certificados ou credenciais ausentes no repositório de certificados.

2. O Publicador de OPC se registra com o Hub IoT.
    - Usa o protocolo configurado. Pode usar qualquer protocolo com suporte do SDK do cliente do Hub IoT. O padrão é MQTT.
    - A comunicação do protocolo é protegida por TLS.

3. O Publicador de OPC lê o arquivo de configuração.

4. O Publicador de OPC cria uma sessão de OPC com cada servidor de agente do usuário de OPC configurado.
    - Usa a conexão TCP.
    - O Publicador de OPC e o servidor de agente do usuário de OPC autenticam-se um ao outro usando certificados X509.
    - Todo o tráfego de agente do usuário de OPC adicional é criptografado com o mecanismo de criptografia de agente do usuário de OPC configurado.
    - O Publicador de OPC cria, na sessão OPC para cada intervalo de publicação configurado, uma assinatura de OPC.
    - Cria itens monitorados de OPC para os nós de OPC publicarem na assinatura de OPC.

5. Se um valor de nó de OPC monitorado é alterado, o servidor de agente do usuário de OPC envia atualizações para o Publicador de OPC.

6. O Publicador de OPC transcodifica o novo valor.
    - Processa em lotes várias alterações se o envio em lote estiver habilitado.
    - Cria uma mensagem de Hub IoT.

7. O Publicador de OPC envia uma mensagem para o Hub IoT.
    - Usa o protocolo configurado.
    - A comunicação é protegida por TLS.

8. O Time Series Insights (TSI) lê as mensagens do Hub IoT.
    - Usa o AMQP sobre TCP/TLS.
    - Esta etapa é interna para o datacenter.

9. Dados em repouso em TSI.

10. O WebApp da Fábrica Conectada ao Serviço de Aplicativo Azure consulta os dados necessários a partir do TSI.
    - Usa comunicação protegida por TCP/TLS.
    - Esta etapa é interna para o datacenter.

11. O navegador da Web se conecta ao WebApp da Fábrica Conectada.
    - Exibe o painel do Connected Factory.
    - Conecta-se via HTTPS.
    - O acesso ao Aplicativo da Fábrica Conectada requer a autenticação do usuário por meio do Azure Active Directory.
    - Qualquer chamada do WebApi para o aplicativo da Fábrica Conectada é protegida por Anti-Forgery-Tokens.

12. Nas atualizações de dados, o WebApp da Fábrica Conectada envia dados atualizados para o navegador da Web.
    - Usa o protocolo SignalR.
    - Protegido por TCP/TLS.

## <a name="browsing-data-flow"></a>Navegação de fluxo de dados

![Navegação de fluxo de dados](./media/iot-accelerators-connected-factory-sample-walkthrough/browsing_dataflow.png)

### <a name="flow-steps"></a>Etapas do fluxo

1. O proxy de OPC (componente do servidor) é iniciado.
    - Lê as chaves de acesso compartilhado de um repositório local.
    - Se necessário, armazena as chaves de acesso ausentes no repositório.

2. O proxy de OPC (componente do servidor) se registra com o Hub IoT.
    - Lê todos os dispositivos conhecidos do Hub IoT.
    - Usa MQTT sobre TLS através de Socket ou Secure WebSocket.

3. O navegador da Web se conecta ao Connected Factory WebApp e exibe o painel do Connected Factory.
    - Usa HTTPS.
    - Um usuário seleciona um servidor de agente do usuário de OPC para se conectar-se a ele.

4. O WebApp da Fábrica Conectada estabelece uma sessão de agente do usuário de OPC para o servidor de agente do usuário de OPC selecionado.
    - Usa a pilha de agente do usuário de OPC.

5. O transporte de proxy de OPC recebe uma solicitação da pilha de agente do usuário de OPC para estabelecer uma conexão de soquete TCP ao servidor de agente do usuário de OPC.
    - Ele apenas recupera o conteúdo de TCP e usa-o inalterado.
    - Essa etapa é interna no WebApp da Fábrica Conectada.

6. O proxy de OPC (componente do cliente) procura o dispositivo de Proxy de OPC (componente do servidor) no registro de dispositivo do Hub IoT. Em seguida, chama um método de dispositivo do dispositivo do proxy de OPC (componente do servidor) no Hub IoT.
    - Usa HTTPS sobre TCP/TLS para procurar o proxy de OPC.
    - Usa HTTPS sobre TCP/TLS para estabelecer uma conexão de soquete TCP para o servidor de agente do usuário de OPC.
    - Esta etapa é interna para o datacenter.

7. O Hub IoT chama um método de dispositivo do dispositivo do proxy de OPC (componente do servidor).
    - Usa um MQTT estabelecido sobre TLS pela conexão Socket ou Secure Websocket para estabelecer uma conexão de soquete TCP para o servidor de agente do usuário de OPC.

8. O proxy de OPC (componente de servidor) envia o conteúdo TCP para a rede de chão de fábrica.

9. O servidor de agente do usuário de OPC processa o conteúdo e envia a resposta.

10. A resposta é recebida pelo soquete do proxy de OPC (componente de servidor).
    - O proxy de OPC envia os dados como o valor retornado do método de dispositivo para o Hub IoT e para o proxy de OPC (componente do cliente).
    - Esses dados são entregues para a pilha de agente do servidor de OPC no aplicativo da Fábrica Conectada.

11. O WebApp da Fábrica Conectada WebApp retorna a experiência do usuário do navegador de OPC enriquecida com as informações específicas de agente do usuário de OPC recebidas do servidor de agente do usuário de OPC para o navegador da Web para fazer a renderização.
    - Enquanto um usuário navega pelo espaço de endereço do OPC e aplica funções a nós no espaço de endereço do OPC, o cliente do OPC Browser UX usa chamadas AJAX por HTTPS protegidas com tokens do Anti-Forgery para obter dados do Connected Factory WebApp.
    - Se necessário, o cliente usa a comunicação explicada nas etapas 4 a 10 para trocar informações com o servidor de agente do usuário de OPC.

> [!NOTE]
> O proxy de OPC (componente do servidor) e o proxy de OPC Proxy (componente do cliente) concluem as etapas de 4 a 10 para todo o tráfego TCP relacionado à comunicação de agente do servidor de OPC.

> [!NOTE]
> Para o servidor de agente do usuário de OPC e a pilha de agente do usuário de OPC no WebApp da Fábrica Conectada, a comunicação de Proxy de OPC é transparente e todos os recursos de segurança de agente do usuário de OPC para autenticação e criptografia são aplicáveis.

## <a name="next-steps"></a>Próximas etapas

Você pode continuar a introdução aos aceleradores de solução de IoT lendo os seguintes artigos:

* [Permissões no site azureiotsolutions.com][lnk-permissions]
* [Implantar um gateway no Windows ou Linux para o acelerador de solução da Fábrica Conectada](iot-accelerators-connected-factory-gateway-deployment.md)
* [Implementação de referência do Publicador de OPC](https://github.com/Azure/iot-edge-opc-publisher/blob/master/README.md).

[connected-factory-logical]:media/iot-accelerators-connected-factory-sample-walkthrough/cf-logical-architecture.png

[lnk-preconfigured-solutions]:about-iot-accelerators.md
[lnk-customize]: iot-accelerators-connected-factory-customize.md
[lnk-IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-OPC-UA-NET-Standard]:https://github.com/OPCFoundation/UA-.NETStandardLibrary
[lnk-Azure-IoT-Gateway]: https://github.com/azure/iot-edge
[lnk-permissions]: iot-accelerators-faq.md
