---
title: Guia de solução de problemas do barramento de serviço do Azure | Microsoft Docs
description: Saiba mais sobre dicas de solução de problemas e recomendações para alguns problemas que você pode ver ao usar o barramento de serviço do Azure.
ms.topic: article
ms.date: 03/03/2021
ms.openlocfilehash: 7de39e5a3a7b6cbb8e5fa504f073023853e18366
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179690"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Guia de solução de problemas do barramento de serviço do Azure
Este artigo fornece dicas de solução de problemas e recomendações para alguns problemas que você pode ver ao usar o barramento de serviço do Azure. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemas de conectividade, certificado ou tempo limite
As etapas a seguir podem ajudá-lo a solucionar problemas de conectividade/certificado/tempo limite para todos os serviços em *. servicebus.windows.net. 

- Navegue até ou [wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/` . Ele ajuda a verificar se você tem problemas de filtragem de IP ou de rede virtual ou de cadeia de certificados, que são comuns ao usar o SDK do Java.

    Um exemplo de mensagem bem-sucedida:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Um exemplo de mensagem de erro de falha:

    ```xml
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Execute o comando a seguir para verificar se alguma porta está bloqueada no firewall. As portas usadas são 443 (HTTPS), 5671 (AMQP) e 9354 (net Messaging/SBMP). Dependendo da biblioteca que você usa, outras portas também são usadas. Aqui está o comando de exemplo que verifica se a porta 5671 está bloqueada. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    No Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Quando houver problemas intermitentes de conectividade, execute o comando a seguir para verificar se há algum pacote Descartado. Esse comando tentará estabelecer 25 conexões TCP diferentes a cada 1 segundo com o serviço. Em seguida, você pode verificar quantos deles foram bem-sucedidos/com falha e também ver a latência de conexão TCP. Você pode baixar a `psping` ferramenta [aqui](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    Você pode usar comandos equivalentes se estiver usando outras ferramentas, como `tnc` , `ping` e assim por diante. 
- Obtenha um rastreamento de rede se as etapas anteriores não ajudarem e a analisarem usando ferramentas como o [Wireshark](https://www.wireshark.org/). Entre em contato com [suporte da Microsoft](https://support.microsoft.com/) se necessário. 
- Para localizar os endereços IP corretos a serem adicionados à lista de permissões para suas conexões, consulte [quais endereços IP preciso adicionar à lista de permissões](service-bus-faq.md#what-ip-addresses-do-i-need-to-add-to-allow-list). 


## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Problemas que podem ocorrer com atualizações/reinicializações de serviço

### <a name="symptoms"></a>Sintomas
- As solicitações podem estar momentaneamente limitadas.
- Pode haver uma queda nas mensagens/solicitações de entrada.
- O arquivo de log pode conter mensagens de erro.
- Os aplicativos podem ser desconectados do serviço por alguns segundos.

### <a name="cause"></a>Causa
As atualizações e reinicializações do serviço de back-end podem causar esses problemas em seus aplicativos.

### <a name="resolution"></a>Resolução
Se o código do aplicativo usar o SDK, a política de repetição já estará interna e ativa. O aplicativo será reconectado sem um impacto significativo no aplicativo/fluxo de trabalho.

## <a name="unauthorized-access-send-claims-are-required"></a>Acesso não autorizado: as declarações de envio são necessárias

### <a name="symptoms"></a>Sintomas 
Você pode ver esse erro ao tentar acessar um tópico do barramento de serviço do Visual Studio em um computador local usando uma identidade gerenciada atribuída pelo usuário com permissões de envio.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

### <a name="cause"></a>Causa
A identidade não tem permissões para acessar o tópico do barramento de serviço. 

### <a name="resolution"></a>Resolução
Para resolver esse erro, instale a biblioteca [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) .  Para obter mais informações, consulte [autenticação de desenvolvimento local](/dotnet/api/overview/azure/service-to-service-authentication#local-development-authentication). 

Para saber como atribuir permissões a funções, consulte [autenticar uma identidade gerenciada com Azure Active Directory para acessar os recursos do barramento de serviço do Azure](service-bus-managed-service-identity.md).

## <a name="service-bus-exception-put-token-failed"></a>Exceção do barramento de serviço: falha no token put

### <a name="symptoms"></a>Sintomas
Ao tentar enviar mais de 1000 mensagens usando a mesma conexão do barramento de serviço, você receberá a seguinte mensagem de erro: 

`Microsoft.Azure.ServiceBus.ServiceBusException: Put token failed. status-code: 403, status-description: The maximum number of '1000' tokens per connection has been reached.` 

### <a name="cause"></a>Causa
Há um limite para o número de tokens que são usados para enviar e receber mensagens usando uma única conexão a um namespace do barramento de serviço. É 1000. 

### <a name="resolution"></a>Resolução
Abra uma nova conexão com o namespace do barramento de serviço para enviar mais mensagens.

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir: 

- [Azure Resource Manager exceções](service-bus-resource-manager-exceptions.md). Exceções de lista de ti geradas ao interagir com o barramento de serviço do Azure usando Azure Resource Manager (por meio de modelos ou chamadas diretas).
- [Exceções de mensagens](service-bus-messaging-exceptions.md). Ele fornece uma lista de exceções geradas pelo .NET Framework para o barramento de serviço do Azure.