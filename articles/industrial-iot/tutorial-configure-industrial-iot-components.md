---
title: Configurar os componentes da IoT Industrial do Azure
description: Neste tutorial, você aprenderá a alterar os valores padrão da configuração.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 348276a71b2227063374da6455445118fcb00fcf
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787166"
---
# <a name="tutorial-configure-the-industrial-iot-components"></a>Tutorial: Configurar os componentes da IoT Industrial

O script de implantação configura automaticamente todos os componentes para trabalharem entre si usando valores padrão. No entanto, as configurações dos valores padrão podem ser alteradas de acordo com suas necessidades.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Personalizar a configuração dos componentes


Estas são algumas das configurações de personalização mais relevantes para os componentes:
* Hub IoT
    * Rede → Acesso público: configura o acesso à Internet; por exemplo, filtros de IP
    * Rede → Conexões de ponto de extremidade privado: cria um ponto de extremidade que não esteja acessível pela Internet e possa ser consumido internamente por outros serviços do Azure ou dispositivos locais (por exemplo, por uma conexão VPN)
    * IoT Edge: gerencia a configuração dos dispositivos de borda conectados aos servidores OPC UA 
* Cosmos DB
    * Replicar dados globalmente: configura a redundância de dados
    * Firewall e redes virtuais: configura o acesso à Internet e à VNET e os filtros de IP
    * Conexões de ponto de extremidade privado: cria um ponto de extremidade que não esteja acessível pela Internet 
* Key Vault
    * Segredos: gerencia as configurações da plataforma
    * Políticas de acesso: gerencia quais aplicativos e usuários podem acessar os dados do Key Vault e quais operações (por exemplo, leitura, gravação, lista, exclusão) eles podem executar na rede, no firewall, na VNET e nos pontos de extremidade privados
* AAD (Azure Active Directory) → Registros de aplicativo
    * <NOME_DO_APLICATIVO>-Web 	 Autenticação: gerencia os URIs de resposta, que é a lista de URIs que podem ser usados como páginas de aterrissagem após a autenticação ser realizada com sucesso. O script de implantação pode não conseguir configurar isso automaticamente em determinados cenários, como a falta de direitos de administrador do AAD. O ideal é adicionar ou modificar os URIs ao alterar o nome do host do aplicativo Web, por exemplo, o número da porta usado pelo localhost para depuração
* Serviço de Aplicativo
    * Configuração: gerencia as variáveis de ambiente que controlam os serviços ou a interface do usuário
* Máquina virtual
    * Rede: configura as regras de firewall e as redes compatíveis
    * Console serial: acessa o SSH para obter insights ou para depuração, obtém as credenciais da saída do script de implantação ou redefine a senha
* Hub IoT → IoT Edge
    * Gerencia as identidades dos dispositivos IoT Edge que podem acessar o hub, configura os módulos instalados e a configuração usada por eles, por exemplo, parâmetros de codificação para o OPC Publisher
* Hub IoT → IoT Edge → \<DEVICE> → Definir Módulos → OpcPublisher (somente para operação autônoma do OPC Publisher)


## <a name="configuration-options"></a>Opções de configuração

|Opção de configuração (nome abreviado/completo)    |    Descrição   |
|----------------------------------------------|------------------|
pf/publishfile |O nome do arquivo usado para configurar os nós a serem publicados. Se essa opção for especificada, ela colocará o OPC Publisher no modo autônomo.
lf/logfile |O nome do arquivo de log a ser usado.
ll/loglevel |O nível de log a ser usado (permitidos: fatal, erro, aviso, informações, depuração e detalhado).
me/messageencoding |A codificação para mensagens de saída; valores permitidos: JSON, UADP
mm/messagingmode |O modo para mensagens de saída; valores permitidos: PubSub, exemplos
fm/fullfeaturedmessage |O modo completo para mensagens (todos os campos preenchidos). O padrão é 'true'; para compatibilidade herdada, use 'false'
aa/autoaccept |O editor confia em todos os servidores com os quais tem uma conexão
bs/batchsize |O número de mensagens de alteração de dados do OPC UA a serem armazenadas em cache para envio em lote.
si/iothubsendinterval |O intervalo de disparo do envio em lote em segundos.
ms/iothubmessagesize |O tamanho máximo da mensagem (IoT D2C).
om/maxoutgressmessages |O tamanho máximo do buffer de saída de mensagem (IoT D2C).
di/diagnosticsinterval |Mostra as informações de diagnóstico do editor no intervalo especificado em segundos (informações de nível de log necessárias). -1 desabilita o log de diagnóstico remoto e a saída de diagnóstico
lt/logflugtimespan |O intervalo de tempo em segundos em que o arquivo de log deve ser liberado.
ih/iothubprotocol |Protocolo a ser usado para comunicação com o hub. Valores permitidos: AmqpOverTcp, AmqpOverWebsocket, MqttOverTcp, MqttOverWebsocket, Amqp, Mqtt, Tcp, Websocket e Any
hb/heartbeatinterval |O editor usa isso como o valor padrão em segundos para a configuração do intervalo de pulsação de nós sem uma configuração do intervalo de pulsação.
ot/operationtimeout |O tempo limite da operação do cliente OPC UA do editor em ms.
ol/opcmaxstringlen |O tamanho máximo de cadeia de caracteres que pode ser transmitido/recebido pelo OPC.
oi/opcsamplinginterval |Valor padrão em milissegundos para solicitar aos servidores a amostragem dos valores
op/opcpublishinginterval |Valor padrão em milissegundos para a configuração do intervalo de publicação das assinaturas no servidor OPC UA.
ct/createsessiontimeout |O intervalo no qual o editor envia mensagens keep alive em segundos para os servidores OPC nos pontos de extremidade aos quais está conectado.
kt/keepalivethresholt |Especifique o número de pacotes keep alive que um servidor pode ignorar, antes que a sessão seja desconectada.
tm/trustmyself |O certificado do editor é colocado no repositório confiável automaticamente.
at/appcertstoretype |O próprio tipo de repositório de certificados do aplicativo (permitidos: Directory, X509Store).


## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu a alterar os valores padrão da configuração, poderá 

> [!div class="nextstepaction"]
> [Efetuar pull de dados de IIoT no ADX](tutorial-industrial-iot-azure-data-explorer.md)

> [!div class="nextstepaction"]
> [Visualizar e analisar os dados usando o Time Series Insights](tutorial-visualize-data-time-series-insights.md)
