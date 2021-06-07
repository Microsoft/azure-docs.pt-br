---
title: Comunicação segura de proxy reverso do Azure Service Fabric
description: Configure o proxy reverso para habilitar a comunicação de ponta a ponta segura em um aplicativo Service Fabric do Azure.
ms.topic: conceptual
ms.date: 08/10/2017
ms.openlocfilehash: b01ce559b3c790164992d6618149afa9df069466
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86256128"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Conectar-se a um serviço seguro com o proxy inverso

Este artigo explica como estabelecer uma conexão segura entre o proxy reverso e serviços, permitindo um canal seguro de ponta a ponta. Para saber mais sobre o proxy reverso, consulte o [proxy reverso no Microsoft Azure Service Fabric](service-fabric-reverseproxy.md)

> [!IMPORTANT]
> A conexão aos serviços seguros tem suporte apenas quando o proxy reverso é configurado para escutar em HTTPS. Este artigo pressupõe que este é o caso. Consulte [Configuração reversa no Microsoft Azure Service Fabric](service-fabric-reverseproxy-setup.md) para configurar o proxy reverso no Service Fabric.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Estabelecimento de conexão segura entre o proxy reverso e os serviços 

### <a name="reverse-proxy-authenticating-to-services"></a>Autenticação do proxy reverso nos serviços:
O proxy reverso identifica-se aos serviços usando seu certificado. Para clusters do Azure, o certificado é especificado com a propriedade ***reverseProxyCertificate** _ na [seção do tipo de recurso](../azure-resource-manager/templates/template-syntax.md) [_ *Microsoft.* * /clusters](/azure/templates/microsoft.servicefabric/clusters) do modelo do Resource Manager. Para clusters autônomos, o certificado é especificado com a **propriedade _ReverseProxyCertificate_*_ ou _*_ReverseProxyCertificateCommonNames_*_ na seção _* Security** de ClusterConfig.jsem. Para obter mais informações, consulte [Habilitar o proxy reverso nos clusters autônomos](service-fabric-reverseproxy-setup.md#enable-reverse-proxy-on-standalone-clusters). 

Os serviços podem implementar a lógica para verificar o certificado apresentado pelo proxy reverso. Os serviços podem especificar os detalhes do certificado de cliente aceito como definições de configuração no pacote de configuração. Isso pode ser lido em runtime e usado para validar o certificado apresentado pelo proxy reverso. Consulte [Gerenciar parâmetros do aplicativo](service-fabric-manage-multiple-environment-app-configuration.md) para adicionar as definições de configuração. 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Proxy reverso verificando a identidade do serviço por meio do certificado apresentado pelo serviço:
O proxy reverso suporta as seguintes políticas para executar a validação do certificado de servidor dos certificados apresentados pelos serviços: Nenhum, ServiceCommonNameAndIssuer e ServiceCertificateThumbprints.
Para selecionar a política para o proxy reverso usar, especifique o **ApplicationCertificateValidationPolicy** na **ApplicationGateway/Http** seção sob [fabricSettings](service-fabric-cluster-fabric-settings.md).

A próxima seção mostra detalhes de configuração para cada uma dessas opções.

### <a name="service-certificate-validation-options"></a>Opções de validação do certificado de serviço 

- **Nenhum**: o proxy reverso ignora a verificação do certificado de serviço com proxy e estabelece a conexão segura. Esse é o comportamento padrão.
Especifique o **ApplicationCertificateValidationPolicy** com o valor **None** no [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) seção.

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

- **ServiceCommonNameAndIssuer**: proxy reverso verifica o certificado apresentado pelo serviço com base no nome comum do certificado e a impressão digital do emissor imediato: especifique o **ApplicationCertificateValidationPolicy** com o valor **ServiceCommonNameAndIssuer** no [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) seção.

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

   Para especificar a lista de impressões digitais do emissor e de nome comum do serviço, adicione uma [**ApplicationGateway/Http/ServiceCommonNameAndIssuer**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttpservicecommonnameandissuer) seção no **fabricSettings**, conforme mostrado abaixo. Vários certificado comum e nome de impressão digital pares podem ser adicionados a **parâmetros** matriz. 

   Se o proxy reverso do ponto de extremidade estiver se conectando para apresentar um certificado que é o nome comum e a impressão digital do emissor corresponde a qualquer um dos valores especificados aqui, um canal TLS é estabelecido.
   Em caso de falha de correspondência dos detalhes do certificado, a solicitação do cliente não será realizada pelo proxy reverso com um código de status 502 (Gateway incorreto). A linha de status HTTP também conterá a frase "Certificado SSL Inválido." 

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

- **ServiceCertificateThumbprints**: o proxy reverso verificará se o certificado de serviço com proxy tem base em sua impressão digital. Você pode optar por acessar certificados autoassinados dessa rota quando os serviços estiverem configurados com: especifique o **ApplicationCertificateValidationPolicy** com o valor **ServiceCertificateThumbprints** na seção [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp).

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

   Especifique também as impressões digitais com um **ServiceCertificateThumbprints** entrada na seção **ApplicationGateway/Http**. É possível especificar várias impressões digitais como uma lista separada por vírgulas no campo de valor, conforme mostrado abaixo:

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

   Se a impressão digital do certificado do servidor estiver listada nessa entrada de configuração, o proxy reverso terá sucesso na conexão TLS. Caso contrário, ele encerrará a conexão e a solicitação do cliente falhará com um erro 502 (Gateway incorreto). A linha de status HTTP também conterá a frase "Certificado SSL Inválido."

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Lógica de seleção do ponto de extremidade quando os serviços expõem pontos de extremidade seguros e inseguros
O Service Fabric oferece suporte à configuração de vários pontos de extremidade para um serviço. Para obter mais informações, consulte [Especificar recursos em um manifesto de serviço](service-fabric-service-manifest-resources.md).

Proxy reverso seleciona um dos pontos de extremidade para encaminhar a solicitação de acordo com o **ListenerName** parâmetro na consulta a [URI de serviço](./service-fabric-reverseproxy.md#uri-format-for-addressing-services-by-using-the-reverse-proxy). Se o **ListenerName** parâmetro não for especificado, o proxy reverso pode escolher qualquer ponto de extremidade da lista de pontos de extremidade. Dependendo os pontos de extremidade configurados para o serviço, o ponto de extremidade selecionado pode ser um ponto de extremidade HTTP ou HTTPS. Pode haver requisitos ou cenários onde você deseja que o proxy reverso opere em um "somente modo seguro”; ou seja, você não deseja que o proxy reverso seguro encaminhe solicitações para pontos de extremidade não seguros. Para configurar o proxy reverso para o modo somente seguro, especifique a entrada de configuração **SecureOnlyMode** com o valor **verdadeiro** na seção [**ApplicationGateway / Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp).   

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

> [!NOTE]
> Ao operar em **SecureOnlyMode**, se um cliente tiver especificado um **ListenerName** correspondente a um ponto de extremidade HTTP (não seguro), o proxy reverso falhará na solicitação com um código de status HTTP 404 (Não encontrado).

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Configurar a autenticação de certificado do cliente através do proxy reverso
A terminação de TLS ocorre no proxy reverso e todos os dados de certificado do cliente são perdidos. Para os serviços realizar a autenticação de certificado do cliente, especifique o **ForwardClientCertificate** definindo na seção [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp).

1. Quando **ForwardClientCertificate** for definido como **false**, o proxy reverso não solicitará o certificado do cliente durante seu handshake TLS com o cliente.
Esse é o comportamento padrão.

2. Quando **ForwardClientCertificate** é definido como **true**, o proxy reverso solicita o certificado do cliente durante seu handshake TLS com o cliente.
Em seguida, ele encaminhará os dados do certificado do cliente em um cabeçalho HTTP personalizado chamado **X-Client-Certificate**. O valor do cabeçalho é a cadeia de formato PEM codificado em base64 do certificado do cliente. O serviço pode conseguir ou não atender à solicitação com o código de status apropriado depois de inspecionar os dados do certificado.
Se o cliente não apresentar um certificado, o proxy reverso encaminha um cabeçalho vazio e permite que o serviço manipule o caso.

> [!NOTE]
> O proxy reverso atua apenas como um serviço de encaminhamento. Ele não executará nenhuma validação de certificado do cliente.


## <a name="next-steps"></a>Próximas etapas
* [Definir e configurar proxy reverso em um cluster](service-fabric-reverseproxy-setup.md).
* Consulte [Configurar o proxy reverso para se conectar a serviços seguros](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services)
* Confira um exemplo de comunicação HTTP entre serviços em um [projeto de exemplo no GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Comunicação remota de serviço com os Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [API Web que usa o OWIN nos Reliable Services](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Gerenciar certificados do cluster](service-fabric-cluster-security-update-certs-azure.md)
