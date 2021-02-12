---
title: Solucionar problemas de conectividade de rede – grade de eventos do Azure | Microsoft Docs
description: Este artigo fornece informações sobre como solucionar problemas de conectividade de rede com a grade de eventos do Azure.
author: batrived
ms.topic: article
ms.date: 06/21/2020
ms.author: batrived
ms.openlocfilehash: fa119784715b8c88ef3c9f2700b2cac1cc467234
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96339738"
---
# <a name="troubleshoot-connectivity-issues---azure-event-grid"></a>Solucionar problemas de conectividade-grade de eventos do Azure

Há várias razões para os aplicativos cliente não poderem se conectar a um tópico/domínio da grade de eventos. Os problemas de conectividade que você enfrenta podem ser permanentes ou transitórios. Se o problema ocorrer o tempo todo (permanente), talvez você queira verificar as configurações de firewall da sua organização, as configurações de firewall de IP, as marcas de serviço, os pontos de extremidade privados e muito mais. Para problemas transitórios, executar comandos para verificar pacotes ignorados e obter rastreamentos de rede pode ajudar a solucionar os problemas.

Este artigo fornece dicas para solucionar problemas de conectividade com a grade de eventos do Azure.

## <a name="troubleshoot-permanent-connectivity-issues"></a>Solucionar problemas de conectividade permanente

Se o aplicativo não for capaz de se conectar à grade de eventos, siga as etapas desta seção para solucionar o problema.

### <a name="check-if-theres-a-service-outage"></a>Verificar se há uma interrupção de serviço

Verifique a interrupção do serviço de grade de eventos do Azure no [site de status do serviço do Azure](https://azure.microsoft.com/status/).

### <a name="check-if-the-ports-required-to-communicate-with-event-grid-arent-blocked-by-organizations-firewall"></a>Verifique se as portas necessárias para se comunicar com a grade de eventos não estão bloqueadas pelo firewall da organização

Verifique se as portas usadas na comunicação com a grade de eventos do Azure não estão bloqueadas no firewall da sua organização. Consulte a tabela a seguir para as portas de saída que você precisa abrir para se comunicar com a grade de eventos do Azure.

| Protocolo | Portas |
| -------- | ----- |
| HTTPS    | 443   |

Aqui está um comando de exemplo que verifica se a porta 443 está bloqueada.

```powershell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

No Linux:

```shell
telnet {sampletopicname}.{region}-{suffix}.eventgrid.azure.net 443
```

### <a name="verify-that-ip-addresses-are-allowed-in-your-corporate-firewall"></a>Verificar se os endereços IP são permitidos em seu firewall corporativo

Quando você está trabalhando com o Azure, às vezes você precisa permitir intervalos de endereços IP específicos ou URLs em seu firewall corporativo ou proxy para acessar todos os serviços do Azure que você está usando ou tentando usar. Verifique se o tráfego é permitido em endereços IP usados pela grade de eventos. Para endereços IP usados pela grade de eventos do Azure: consulte [intervalos de IP e marcas de serviço do Azure – nuvem pública](https://www.microsoft.com/download/details.aspx?id=56519) e [marca de serviço-AzureEventGrid](network-security.md#service-tags).

Os [intervalos de IP do Azure e as marcas de serviço – o documento de nuvem pública](https://www.microsoft.com/download/details.aspx?id=56519) também lista os endereços IP **por região**. Você pode permitir intervalos de endereços para a **região do tópico** e a **região emparelhada** em seu firewall ou proxy corporativo. Para uma região emparelhada para uma região, consulte [continuidade de negócios e recuperação de desastres (BCDR): regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md). 

> [!NOTE]
> Novos endereços IP podem ser adicionados à marca de serviço AzureEventGrid, embora não seja comum. Portanto, é bom fazer uma verificação semanal nas marcas de serviço.

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>Verifique se a marca de serviço AzureEventGrid é permitida em seus grupos de segurança de rede

Se seu aplicativo estiver sendo executado dentro de uma sub-rede e se houver um grupo de segurança de rede associado, confirme se a saída da Internet é permitida ou se a marca de serviço AzureEventGrid é permitida. Consulte [marcas de serviço](../virtual-network/service-tags-overview.md)

### <a name="check-the-ip-firewall-settings-for-your-topicdomain"></a>Verifique as configurações de firewall de IP para seu tópico/domínio

Verifique se o endereço IP público do computador no qual o aplicativo está em execução não está bloqueado pelo tópico EventGrid/firewall de IP do domínio.

Por padrão, os tópicos/domínios da grade de eventos podem ser acessados pela Internet, desde que a solicitação venha com autenticação e autorização válidas. Com o firewall de IP, você pode restringir ainda mais a um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (roteamento entre domínios sem classificação)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

As regras de firewall IP são aplicadas no nível de domínio/tópico da grade de eventos. Portanto, as regras se aplicam a todas as conexões de clientes que usam qualquer protocolo com suporte. Qualquer tentativa de conexão de um endereço IP que não corresponda a uma regra de IP permitida no tópico/domínio da grade de eventos é rejeitada como proibido. A resposta não menciona a regra de IP.

Para obter mais informações, consulte [configurar regras de firewall de IP para um tópico/domínio da grade de eventos do Azure](configure-firewall.md).

#### <a name="find-the-ip-addresses-blocked-by-ip-firewall"></a>Localizar os endereços IP bloqueados pelo firewall de IP

Habilitar logs de diagnóstico para o tópico/domínio da grade de eventos [Habilitar logs de diagnóstico](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-custom-topic). Você verá o endereço IP para a conexão que foi negada.

```json
{
  "time": "2019-11-01T00:17:13.4389048Z",
  "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME",
  "category": "PublishFailures",
  "operationName": "Post",
  "message": "inputEventsCount=null, requestUri=https://SAMPLE-TOPIC-NAME.region-suffix.eventgrid.azure.net/api/events, publisherInfo=PublisherInfo(category=User, inputSchema=EventGridEvent, armResourceId=/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME), httpStatusCode=Forbidden, errorType=ClientIPRejected, errorMessage=Publishing to SAMPLE-TOPIC-NAME.{region}-{suffix}.EVENTGRID.AZURE.NET by client {clientIp} is rejected due to IpAddress filtering rules."
}
```

### <a name="check-if-the-eventgrid-topicdomain-can-be-accessed-using-only-a-private-endpoint"></a>Verifique se o tópico/domínio do EventGrid pode ser acessado usando apenas um ponto de extremidade privado

Se o tópico/domínio da grade de eventos estiver configurado para ser acessível somente por meio do ponto de extremidade privado, confirme se o aplicativo cliente está acessando o tópico/domínio sobre o ponto de extremidade privado. Para confirmar, verifique se o aplicativo cliente está sendo executado dentro de uma sub-rede e se há um ponto de extremidade privado para o tópico/domínio da grade de eventos nessa sub-rede.

O [serviço de vínculo privado do Azure](../private-link/private-link-overview.md) permite que você acesse a grade de eventos do Azure em um **ponto de extremidade privado** em sua rede virtual. O ponto de extremidade privado é uma interface de rede que conecta você de forma privada e segura a um serviço com tecnologia do Link Privado do Azure. O ponto de extremidade privado usa um endereço IP privado de sua VNet, colocando efetivamente em sua VNet. Todo o tráfego para o serviço pode ser roteado por meio do ponto de extremidade privado; assim, nenhum gateway, nenhum dispositivo NAT, nenhuma conexão ExpressRoute ou VPN e nenhum endereço IP público é necessário. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Você pode se conectar a uma instância de um recurso do Azure, fornecendo o nível mais alto de granularidade no controle de acesso.

Para obter mais informações, consulte [Configurar pontos de extremidade privados](configure-private-endpoints.md).

## <a name="troubleshoot-transient-connectivity-issues"></a>Solucionar problemas de conectividade transitório

Se você estiver enfrentando problemas de conectividade intermitentes, siga as seções a seguir para obter dicas de solução de problemas.

### <a name="run-the-command-to-check-dropped-packets"></a>Execute o comando para verificar os pacotes ignorados

Quando houver problemas intermitentes de conectividade, execute o comando a seguir para verificar se há algum pacote Descartado. Esse comando tentará estabelecer 25 conexões TCP diferentes a cada 1 segundo com o serviço. Em seguida, você pode verificar quantos deles foram bem-sucedidos/com falha e também ver a latência de conexão TCP. Você pode baixar a `psping` ferramenta [aqui](/sysinternals/downloads/psping).

```shell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

Você pode usar comandos equivalentes se estiver usando outras ferramentas, como `tcpping` [tcpping.exe](https://www.elifulkerson.com/projects/tcping.php).

Obtenha um rastreamento de rede se as etapas anteriores não ajudarem e a analisarem usando ferramentas como o [Wireshark](https://www.wireshark.org/). Entre em contato com [suporte da Microsoft](https://support.microsoft.com/) se necessário.

### <a name="service-upgradesrestarts"></a>Atualizações/reinicializações de serviço

Problemas de conectividade transitórios podem ocorrer devido a atualizações e reinicializações do serviço de back-end. Quando ocorrerem, você poderá ver os seguintes sintomas:

- Pode haver uma queda nas mensagens/solicitações de entrada.
- O arquivo de log pode conter mensagens de erro.
- Os aplicativos podem ser desconectados do serviço por alguns segundos.
- As solicitações podem estar momentaneamente limitadas.

A captura desses erros transitórios, o backup e a repetição da chamada garantirá que seu código seja resiliente a esses problemas transitórios.

## <a name="next-steps"></a>Próximas etapas

Se precisar de mais ajuda, poste seu problema no [fórum do Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) ou abra um [tíquete de suporte](https://azure.microsoft.com/support/options/).