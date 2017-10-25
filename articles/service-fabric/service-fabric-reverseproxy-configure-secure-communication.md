---
title: "Comunicação segura por proxy reverso do Azure Service Fabric | Microsoft Docs"
description: "Configure o proxy reverso para permitir a comunicação segura de ponta a ponta."
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/10/2017
ms.author: kavyako
ms.openlocfilehash: 568f9638c59282bcd7d3fae058a1588a889c22dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Conectar-se a um serviço seguro com o proxy inverso

Este artigo explica como estabelecer uma conexão segura entre o proxy reverso e serviços, permitindo um canal seguro de ponta a ponta.

A conexão aos serviços seguros tem suporte apenas quando o proxy reverso é configurado para escutar em HTTPS. O restante do documento supõe que esse é o caso.
Consulte [Proxy reverso no Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) para configurar o proxy reverso no Service Fabric.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Estabelecimento de conexão segura entre o proxy reverso e os serviços 

### <a name="reverse-proxy-authenticating-to-services"></a>Autenticação do proxy reverso nos serviços:
O proxy reverso identifica-se para os serviços usando seu certificado, que é especificado com a propriedade ***reverseProxyCertificate*** na seção **Cluster** [Tipo de recurso](../azure-resource-manager/resource-group-authoring-templates.md). Os serviços podem implementar a lógica para verificar o certificado apresentado pelo proxy reverso. Os serviços podem especificar os detalhes do certificado de cliente aceito como definições de configuração no pacote de configuração. Isso pode ser lido em tempo de execução e usado para validar o certificado apresentado pelo proxy reverso. Consulte [Gerenciar parâmetros do aplicativo](service-fabric-manage-multiple-environment-app-configuration.md) para adicionar as definições de configuração. 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Proxy reverso verificando a identidade do serviço por meio do certificado apresentado pelo serviço:
Para executar a validação de certificado do servidor dos certificados apresentados pelos serviços, o proxy reverso dá suporte a uma das seguintes opções: Nenhum, ServiceCommonNameAndIssuer e ServiceCertificateThumbprints.
Para selecionar uma dessas três opções, especifique o **ApplicationCertificateValidationPolicy** na seção de parâmetros do elemento ApplicationGateway/Http em [fabricSettings](service-fabric-cluster-fabric-settings.md).

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
              {
                "name": "ApplicationCertificateValidationPolicy",
                "value": "None"
              }
            ]
          }
        ],
        ...
}
```

Consulte a próxima seção para obter detalhes sobre a configuração adicional para cada uma dessas opções.

### <a name="service-certificate-validation-options"></a>Opções de validação do certificado de serviço 

- **Nenhum**: o proxy reverso ignora a verificação do certificado de serviço com proxy e estabelece a conexão segura. Esse é o comportamento padrão.
Especifique o **ApplicationCertificateValidationPolicy** com o valor **Nenhum** na seção de parâmetros do elemento ApplicationGateway/Http.

- **ServiceCommonNameAndIssuer**: o proxy reverso verifica o certificado apresentado pelo serviço com base no nome comum do certificado e a impressão digital do emissor imediato: especifique **ApplicationCertificateValidationPolicy** com o valor **ServiceCommonNameAndIssuer** na seção de parâmetros do elemento ApplicationGateway/Http.

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
              {
                "name": "ApplicationCertificateValidationPolicy",
                "value": "ServiceCommonNameAndIssuer"
              }
            ]
          }
        ],
        ...
}
```

Para especificar a lista de nomes de serviço comuns e as impressões digitais do emissor, adicione um elemento **Http/ApplicationGateway/ServiceCommonNameAndIssuer** em fabricSettings, conforme mostrado abaixo. É possível adicionar vários pares de nome de certificado comum e impressão digital do emissor no elemento da matriz dos parâmetros. 

Se o proxy reverso do ponto de extremidade estiver se conectando para apresentar um certificado cujo nome comum e impressão digital do emissor correspondam a qualquer um dos valores especificados aqui, o canal SSL será estabelecido. Em caso de falha de correspondência dos detalhes do certificado, a solicitação do cliente não será realizada pelo proxy reverso com um código de status 502 (Gateway incorreto). A linha de status HTTP também conterá a frase "Certificado SSL Inválido." 

```json
{
"fabricSettings": [
          ...
          {
             "name": "ApplicationGateway/Http/ServiceCommonNameAndIssuer",
            "parameters": [
              {
                "name": "WinFabric-Test-Certificate-CN1",
                "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 b4 22 11"
              },
              {
                "name": "WinFabric-Test-Certificate-CN2",
                "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 11 33 44"
              }
            ]
          }
        ],
        ...
}
```


- **ServiceCertificateThumbprints**: o proxy reverso verificará se o certificado de serviço com proxy tem base em sua impressão digital. Você pode optar por acessar essa rota quando os serviços estiverem configurados com certificados autoassinados: especifique o **ApplicationCertificateValidationPolicy** com o valor **ServiceCertificateThumbprints** na seção de parâmetros do elemento ApplicationGateway/Http.

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
              {
                "name": "ApplicationCertificateValidationPolicy",
                "value": "ServiceCertificateThumbprints"
              }
            ]
          }
        ],
        ...
}
```

Especifique também as impressões digitais com uma entrada **ServiceCertificateThumbprints** na seção de parâmetros do elemento ApplicationGateway/Http. É possível especificar várias impressões digitais como uma lista separada por vírgulas no campo de valor, conforme mostrado abaixo:

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "ServiceCertificateThumbprints",
                "value": "78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf,78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 b9"
              }
            ]
          }
        ],
        ...
}
```

Se a impressão digital do certificado do servidor estiver listada nesta entrada de configuração, o proxy reverso terá êxito na conexão SSL. Caso contrário, ele encerrará a conexão e a solicitação do cliente falhará com um erro 502 (Gateway incorreto). A linha de status HTTP também conterá a frase "Certificado SSL Inválido."

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Lógica de seleção do ponto de extremidade quando os serviços expõem pontos de extremidade seguros e inseguros
O Service Fabric oferece suporte à configuração de vários pontos de extremidade para um serviço. Confira [Especificar recursos em um manifesto do serviço](service-fabric-service-manifest-resources.md).

O proxy reverso seleciona um dos pontos de extremidade para encaminhar a solicitação com base no parâmetro de consulta **ListenerName**. Se isso não for especificado, ele poderá escolher qualquer ponto de extremidade da lista de pontos de extremidade. Pode ser um ponto de extremidade HTTP ou HTTPS. Pode haver requisitos ou situações nas quais você deseja que o proxy reverso opere em um "modo somente de segurança", ou seja você não quer que o proxy reverso seguro encaminhe solicitações para pontos de extremidade não seguros. Isso pode ser feito especificando a entrada de configuração **SecureOnlyMode** com o valor **true** na seção de parâmetros do elemento ApplicationGateway/Http.   

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "SecureOnlyMode",
                "value": true
              }
            ]
          }
        ],
        ...
}
```

> 
> Ao operar em **SecureOnlyMode**, se o cliente tiver especificado um **ListenerName** correspondente a um ponto de extremidade HTTP(não seguro), a solicitação não será realizada pelo proxy reverso com um código de status HTTP 404 (Não Encontrado).

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Configurar a autenticação de certificado do cliente através do proxy reverso
A terminação SSL ocorre no proxy reverso e todos os dados de certificado do cliente são perdidos. Para que os serviços realizem a autenticação de certificado do cliente, defina a configuração **ForwardClientCertificate** na seção de parâmetros do elemento ApplicationGateway/Http.

1. Quando **ForwardClientCertificate** for definido como **false**, o proxy reverso não solicitará o certificado de cliente durante o handshake de SSL com o cliente.
Esse é o comportamento padrão.

2. Quando **ForwardClientCertificate** for definido como **true**, o proxy reverso solicitará o certificado de cliente durante o handshake de SSL com o cliente.
Em seguida, ele encaminhará os dados do certificado do cliente em um cabeçalho HTTP personalizado chamado **X-Client-Certificate**. O valor do cabeçalho é a cadeia de formato PEM codificado em base64 do certificado do cliente. O serviço pode conseguir ou não atender à solicitação com o código de status apropriado depois de inspecionar os dados do certificado.
Se o cliente não apresentar um certificado, o proxy reverso encaminhará um cabeçalho vazio e permitirá ao serviço lidar com o caso.

> Proxy reverso é um mero encaminhador. Ele não executará nenhuma validação de certificado do cliente.


## <a name="next-steps"></a>Próximas etapas
* Consulte [Configurar o proxy reverso para se conectar aos serviços seguros](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services) para obter exemplos de modelo do Azure Resource Manager a fim de configurar o proxy reverso seguro com as diferentes opções de validação de certificado do serviço.
* Confira um exemplo de comunicação HTTP entre serviços em um [projeto de exemplo no GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Comunicação remota de serviço com os Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [API Web que usa o OWIN nos Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Gerenciar certificados de cluster](service-fabric-cluster-security-update-certs-azure.md)
