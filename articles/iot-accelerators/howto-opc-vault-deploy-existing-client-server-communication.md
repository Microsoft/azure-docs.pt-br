---
title: Proteger o OPC UA cliente e o aplicativo de servidor UA OPC usando o Cofre de OPC - Azure | Microsoft Docs
description: Cliente OPC UA e o OPC UA servidor aplicativo seguro com um novo par de chaves e o certificado usando o Cofre de OPC.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f170d05c513e7f0cc638c5a35e2264f9e043cbcd
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603682"
---
# <a name="secure-opc-ua-client-and-opc-ua-server-application"></a>Proteger o OPC UA cliente e o aplicativo de servidor OPC UA 
O OPC Vault é um microsserviço que pode configurar, registrar e gerenciar o ciclo de vida do certificado para aplicativos servidor e cliente da UA de OPC na nuvem. Este artigo mostra como proteger um cliente OPC UA e um OPC UA aplicativo de servidor com um novo par de chaves e o certificado usando o Cofre de OPC.

Na configuração a seguir, o cliente OPC está testando a conectividade com o PLC OPC. Por padrão, a conectividade não é possível porque ambos os componentes ainda não foram provisionados com os certificados à direita. Neste fluxo de trabalho, não use os certificados autoassinados de componentes do OPC UA e assiná-las por meio do Cofre de OPC. Consulte o [plataforma de teste](howto-opc-vault-deploy-existing-client-plc-communication.md). Em vez disso, essa plataforma de teste provisiona os componentes com um novo certificado, bem como com uma nova chave privada que são gerados pelo Cofre de OPC. Algumas informações básicas sobre a segurança do OPC UA podem ser encontradas neste [white paper](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf). As informações completas podem ser encontradas na especificação do OPC UA.

Plataforma de teste: O seguinte ambiente está configurado para teste.

Scripts de OPC cofre:
- Cliente OPC UA e o OPC UA proteger aplicativos de servidor com um novo par de chaves e o certificado usando o Cofre de OPC.

> [!NOTE]
> Para obter mais informações, consulte o GitHub [repositório](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds).

## <a name="generate-a-new-certificate-and-private-key"></a>Gerar um novo certificado e a chave privada 
**Preparação**
- Certifique-se de que as variáveis de ambiente `$env:_PLC_OPT` e `$env:_CLIENT_OPT` são indefinidos. Por exemplo, `$env:_PLC_OPT=""` no seu PowerShell
- Defina a variável de ambiente `$env:_OPCVAULTID` para uma cadeia de caracteres que permite que você encontre os dados novamente no cofre de OPC. É recomendável defini-lo como um número de 6 dígitos. Para nosso exemplo, "123456" foi usada como valor da variável.
- Verifique se há um volume do docker `opcclient` ou `opcplc`. Entre em contato com `docker volume ls` e removê-los com `docker volume rm <volumename>`. Convém também remover contêineres com `docker rm <containerid>` se os volumes ainda são utilizados por um contêiner.

**Início rápido**
1. Vá para o [site OPC cofre](https://opcvault.azurewebsites.net/)

1. Selecione `Register New`

1. Insira as informações de OPC PLC conforme mostrado na saída de log da plataforma de teste anteriores `CreateSigningRequest information` área nos campos de entrada a `Register New OPC UA Application` página, selecione `Server` como ApplicationType.

1. Selecione `Register`

1. Na próxima página, `Request New Certificate for OPC UA Application` selecione `Request new KeyPair and Certificate`

1. Na próxima página, `Generate a new Certificate with a Signing Request` cole o `CSR (base64 encoded)` cadeia de caracteres da saída do log no `CreateRequest` campo de entrada. Verifique se que você copiar a cadeia de caracteres completa.

1. Na próxima página, `Request New Certificate for OPC UA Application` selecione `Request new Certificate with Signing Request`

1. Na próxima página `Generate a new KeyPair and for an OPC UA Application` inserir `CN=OpcPlc` como SubjectName, `opcplc-<_OPCVAULTID>` (substitua `<_OPCVAULTID>` com a sua) como DomainName, selecione `PEM` como PrivateKeyFormat e digite uma senha (mais tarde, fazemos referência a ele como `<certpassword-string>`)

1. Selecione `Generate New KeyPair`

1. Você agora está movendo para frente para `View Certificate Request Details`. Nessa página, você pode baixar todas as informações necessárias para provisionar armazenamentos de certificados de `opc-plc`.

1. Nesta página:  
    - Selecione `Certificate` no `Download as Base64` e copie a cadeia de caracteres de texto apresentada na `EncodedBase64` do campo e armazená-lo para uso posterior. Nós nos referimos a eles como `<applicationcertbase64-string>` mais tarde. Selecione `Back`.
    - Selecione `PrivateKey` no `Download as Base64` e copie a cadeia de caracteres de texto apresentada na `EncodedBase64` do campo e armazená-lo para uso posterior. Nós nos referimos a eles como `<privatekeybase64-string>` mais tarde. Selecione `Back`.
    - Selecione `Issuer` no `Download as Base64` e copie a cadeia de caracteres de texto apresentada na `EncodedBase64` do campo e armazená-lo para uso posterior. Nós nos referimos a eles como `<addissuercertbase64-string>` mais tarde. Selecione `Back`.
    - Selecione `Crl` no `Download as Base64` e copie a cadeia de caracteres de texto apresentada na `EncodedBase64` do campo e armazená-lo para uso posterior. Nós nos referimos a eles como `<updatecrlbase64-string>` mais tarde. Selecione `Back`.

1. Agora, definido no seu PowerShell uma variável chamada `$env:_PLC_OPT`:

   `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --privatekeybase64 <privatekeybase64-string> --certpassword <certpassword-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  

    Substitua as cadeias de caracteres passadas como a opção valores cadeias de caracteres Base64 buscadas do site.  

1. Repita o processo completo, começando com `Register New` para o cliente OPC. Há apenas as seguintes diferenças que você precisa estar atento:
    - Usar a saída de log da `opcclient`.
    - Selecione `Client` como ApplicationType durante o registro.
    - Use `$env:_CLIENT_OPT` como nome da variável do PowerShell.

    > [!NOTE] 
    > Ao trabalhar com esse cenário, talvez você tenha reconhecido que o `<addissuercertbase64-string>` e `<updatecrlbase64-string>` valores são idênticos para `opcplc` e `opcclient`. Isso é verdadeiro para nosso caso de uso e pode economizar algum tempo enquanto faz as etapas.

**Início rápido**

Execute o seguinte comando do PowerShell na raiz do repositório:

```
docker-compose -f connecttest.yml up
```

**Verificação**

Verifique se os dois componentes não tenham um certificado de aplicativo existente. Verifique a saída de log. Abaixo está a saída de OPC PLC e cliente OPC tem uma saída de log semelhantes.

```
opcplc-123456 | [13:40:08 INF] There is no existing application certificate.
```
Se houver um certificado de aplicativo, remova os volumes do docker, conforme explicado nas etapas de preparação.

Verifique se que o certificado de autoridade de certificação de OPC cofre foi instalado no repositório de certificados do emissor, bem como no repositório de certificados confiáveis do par no log. Abaixo está a saída do log de OPC PLC e cliente OPC tem uma saída de log semelhantes. 

```
opcplc-123456 | [13:40:09 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Trusted peer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Rejected certificate store contains 0 certs
```
O OPC PLC agora confiar em todos os clientes de OPC UA com certificados assinados pelo Cofre de OPC.

Verifique no log de que o formato da chave privado é reconhecido como PEM e que o novo certificado de aplicativo está instalado. Abaixo está a saída do log de OPC PLC e cliente OPC tem uma saída de log semelhantes. 

```
opcplc-123456 | [13:40:09 INF] The private key for the new certificate was passed in using PEM format.
opcplc-123456 | [13:40:09 INF] Remove the existing application certificate.
opcplc-123456 | [13:40:09 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C' was added to the application certificate store.
opcplc-123456 | [13:40:09 INF] Activating the new application certificate with thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C'.
```

O certificado do aplicativo e a chave privada agora são instalados no repositório de certificados de aplicativo e usados pelo aplicativo OPC UA.

Verifique se a conexão entre o cliente OPC e o OPC PLC pode ser estabelecida com êxito e o cliente OPC com êxito pode ler dados do OPC PLC. Você verá a seguinte saída na saída de log do cliente OPC:
```
opcclient-123456 | [13:40:12 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [13:40:12 INF] Session successfully created with Id ns=3;i=941910499.
opcclient-123456 | [13:40:12 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [13:40:12 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [13:40:12 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [13:40:12 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [13:40:12 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [13:40:12 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [13:40:12 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [13:40:12 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [13:40:12 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/21/2018 13:40:12
```
Se você vir essa saída, em seguida, o PLC OPC é agora confiar na OPC cliente e vice-versa, pois ambas têm agora certificados assinados por uma autoridade de certificação e ambos os certificados de confiança que foram assinados por essa AC.

### <a name="a-testbed-for-opc-publisher"></a>Uma plataforma de teste para o publicador de OPC ###

**Início rápido**

Execute o seguinte comando do PowerShell na raiz do repositório:
```
docker-compose -f testbed.yml up
```

**Verificação**
- Verifique se que os dados são enviados ao hub IOT é configurada definindo `_HUB_CS` usando [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) ou [iothub-explorer](https://github.com/Azure/iothub-explorer).
- Cliente de teste OPC vai usar chamadas de método direto do hub IOT e chamadas de método OPC para configurar o publicador de OPC para publicar/cancelar a publicação de nós do servidor de teste OPC.
- Assista a saída para mensagens de erro.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como implantar o OPC cofre a um projeto existente, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Implantar o gêmeo de OPC para um projeto existente](howto-opc-twin-deploy-existing.md)