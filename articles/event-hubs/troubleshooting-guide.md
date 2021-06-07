---
title: Solucionar problemas de conectividade-hubs de eventos do Azure | Microsoft Docs
description: Este artigo fornece informações sobre como solucionar problemas de conectividade com os hubs de eventos do Azure.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 8eddc0e8c598e4553b30759d179fecb6ae880829
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96012673"
---
# <a name="troubleshoot-connectivity-issues---azure-event-hubs"></a>Solucionar problemas de conectividade-hubs de eventos do Azure
Há várias razões para os aplicativos cliente não poderem se conectar a um hub de eventos. Os problemas de conectividade que você enfrenta podem ser permanentes ou transitórios. Se o problema ocorrer o tempo todo (permanente), talvez você queira verificar a cadeia de conexão, as configurações de firewall da sua organização, as configurações de firewall de IP, as configurações de segurança de rede (pontos de extremidade de serviço, pontos de extremidade privados, etc.) e muito mais. Para problemas transitórios, atualizar para a versão mais recente do SDK, executar comandos para verificar pacotes ignorados e obter rastreamentos de rede pode ajudar a solucionar os problemas. 

Este artigo fornece dicas para solucionar problemas de conectividade com os hubs de eventos do Azure. 

## <a name="troubleshoot-permanent-connectivity-issues"></a>Solucionar problemas de conectividade permanente
Se o aplicativo não for capaz de se conectar ao Hub de eventos, siga as etapas desta seção para solucionar o problema. 

### <a name="check-if-there-is-a-service-outage"></a>Verificar se há uma interrupção de serviço
Verifique a interrupção do serviço dos hubs de eventos do Azure no [site de status do serviço do Azure](https://azure.microsoft.com/status/).

### <a name="verify-the-connection-string"></a>Verificar a cadeia de conexão 
Verifique se a cadeia de conexão que você está usando está correta. Consulte [obter cadeia de conexão](event-hubs-get-connection-string.md) para obter a cadeia de conexão usando o portal do Azure, a CLI ou o PowerShell. 

Para clientes Kafka, verifique se os arquivos producer.config ou consumer.config estão configurados corretamente. Para obter mais informações, consulte [Enviar e receber mensagens com Kafka em hubs de eventos](event-hubs-quickstart-kafka-enabled-event-hubs.md#send-and-receive-messages-with-kafka-in-event-hubs).

[!INCLUDE [event-hubs-connectivity](../../includes/event-hubs-connectivity.md)]

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>Verifique se a marca de serviço AzureEventGrid é permitida em seus grupos de segurança de rede
Se seu aplicativo estiver em execução dentro de uma sub-rede e houver um grupo de segurança de rede associado, confirme se a saída da Internet é permitida ou se a marca de serviço AzureEventGrid é permitida. Consulte [marcas de serviço de rede virtual](../virtual-network/service-tags-overview.md) e pesquise `EventHub` .

### <a name="check-if-the-application-needs-to-be-running-in-a-specific-subnet-of-a-vnet"></a>Verificar se o aplicativo precisa estar em execução em uma sub-rede específica de uma vnet
Confirme se seu aplicativo está sendo executado em uma sub-rede de rede virtual que tem acesso ao namespace. Se não for, execute o aplicativo na sub-rede que tem acesso ao namespace ou adicione o endereço IP do computador no qual o aplicativo está sendo executado no [Firewall de IP](event-hubs-ip-filtering.md). 

Quando você cria um ponto de extremidade de serviço de rede virtual para um namespace de Hub de eventos, o namespace aceita o tráfego somente da sub-rede associada ao ponto de extremidade de serviço. Há uma exceção a esse comportamento. Você pode adicionar endereços IP específicos no firewall de IP para habilitar o acesso ao ponto de extremidade público do hub de eventos. Para obter mais informações, consulte [pontos de extremidade de serviço de rede](event-hubs-service-endpoints.md).

### <a name="check-the-ip-firewall-settings-for-your-namespace"></a>Verifique as configurações de firewall de IP para seu namespace
Verifique se o endereço IP público do computador no qual o aplicativo está sendo executado não está bloqueado pelo firewall de IP.  

Por padrão, os namespaces dos Hubs de Eventos são acessíveis da Internet, desde que a solicitação acompanhe autenticação e autorização válidas. Com o firewall de IP, você pode restringir ainda mais a um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (roteamento entre domínios sem classificação)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

As regras de firewall de IP são aplicadas no nível do namespace dos Hubs de Eventos. Portanto, as regras se aplicam a todas as conexões de clientes que usam qualquer protocolo com suporte. Qualquer tentativa de conexão de um endereço IP que não corresponda a uma regra IP permitida no namespace dos Hubs de Eventos será rejeitada como não autorizada. A resposta não menciona a regra IP. As regras de filtro IP são aplicadas na ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

Para obter mais informações, consulte [configurar regras de firewall de IP para um namespace de hubs de eventos do Azure](event-hubs-ip-filtering.md). Para verificar se você tem problemas de filtragem de IP, rede virtual ou cadeia de certificados, consulte [solucionar problemas relacionados à rede](#troubleshoot-network-related-issues).

### <a name="check-if-the-namespace-can-be-accessed-using-only-a-private-endpoint"></a>Verifique se o namespace pode ser acessado usando apenas um ponto de extremidade privado
Se o namespace de hubs de eventos estiver configurado para ser acessível somente por meio do ponto de extremidade privado, confirme se o aplicativo cliente está acessando o namespace sobre o ponto de extremidade privado. 

O [serviço de vínculo privado do Azure](../private-link/private-link-overview.md) permite que você acesse os hubs de eventos do Azure por meio de um **ponto de extremidade privado** em sua rede virtual. O ponto de extremidade privado é uma interface de rede que conecta você de forma privada e segura a um serviço com tecnologia do Link Privado do Azure. O ponto de extremidade privado usa um endereço IP privado de sua rede virtual, efetivamente colocando o serviço em sua rede virtual. Todo o tráfego para o serviço pode ser roteado por meio do ponto de extremidade privado; assim, nenhum gateway, nenhum dispositivo NAT, nenhuma conexão ExpressRoute ou VPN e nenhum endereço IP público é necessário. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Você pode se conectar a uma instância de um recurso do Azure, fornecendo o nível mais alto de granularidade no controle de acesso.

Para obter mais informações, consulte [Configurar pontos de extremidade privados](private-link-service.md). Consulte a seção **validar se a conexão de ponto de extremidade privado funciona** para confirmar que um ponto de extremidade privado é usado. 

### <a name="troubleshoot-network-related-issues"></a>Solucionar problemas relacionados à rede
Para solucionar problemas relacionados à rede com os hubs de eventos, siga estas etapas: 

Navegue até ou [wget](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/` . Ele ajuda a verificar se você tem problemas de filtragem de IP ou de rede virtual ou de cadeia de certificados (mais comuns ao usar o SDK do Java).

Um exemplo de **mensagem bem-sucedida**:

```xml
<feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
```

Um exemplo de **mensagem de erro de falha**:

```json
<Error>
    <Code>400</Code>
    <Detail>
        Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
    </Detail>
</Error>
```

## <a name="troubleshoot-transient-connectivity-issues"></a>Solucionar problemas de conectividade transitório
Se você estiver enfrentando problemas de conectividade intermitentes, siga as seções a seguir para obter dicas de solução de problemas. 

### <a name="use-the-latest-version-of-the-client-sdk"></a>Usar a versão mais recente do SDK do cliente
Alguns dos problemas de conectividade transitórios podem ter sido corrigidos nas versões posteriores do SDK do que o que você está usando. Verifique se você está usando a versão mais recente dos SDKs do cliente em seus aplicativos. Os SDKs são aprimorados continuamente com recursos novos/atualizados e correções de bugs, portanto, sempre teste com o pacote mais recente. Verifique as notas de versão para problemas corrigidos e os recursos adicionados/atualizados. 

Para obter informações sobre SDKs do cliente, consulte o artigo [hubs de eventos do Azure-SDKs do cliente](sdks.md) . 

### <a name="run-the-command-to-check-dropped-packets"></a>Execute o comando para verificar os pacotes ignorados
Quando houver problemas intermitentes de conectividade, execute o comando a seguir para verificar se há algum pacote Descartado. Esse comando tentará estabelecer 25 conexões TCP diferentes a cada 1 segundo com o serviço. Em seguida, você pode verificar quantos deles foram bem-sucedidos/com falha e também ver a latência de conexão TCP. Você pode baixar a `psping` ferramenta [aqui](/sysinternals/downloads/psping).

```shell
.\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
```
Você pode usar comandos equivalentes se estiver usando outras ferramentas, como `tnc` , `ping` e assim por diante. 

Obtenha um rastreamento de rede se as etapas anteriores não ajudarem e a analisarem usando ferramentas como o [Wireshark](https://www.wireshark.org/). Entre em contato com [suporte da Microsoft](https://support.microsoft.com/) se necessário. 

### <a name="service-upgradesrestarts"></a>Atualizações/reinicializações de serviço
Problemas de conectividade transitórios podem ocorrer devido a atualizações e reinicializações do serviço de back-end. Quando ocorrerem, você poderá ver os seguintes sintomas: 

- Pode haver uma queda nas mensagens/solicitações de entrada.
- O arquivo de log pode conter mensagens de erro.
- Os aplicativos podem ser desconectados do serviço por alguns segundos.
- As solicitações podem estar momentaneamente limitadas.

Se o código do aplicativo utilizar o SDK, a política de repetição já estará interna e ativa. O aplicativo será reconectado sem um impacto significativo no aplicativo/fluxo de trabalho. A captura desses erros transitórios, o backup e a repetição da chamada garantirá que seu código seja resiliente a esses problemas transitórios.

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

* [Solucionar problemas de autenticação e autorização](troubleshoot-authentication-authorization.md)
