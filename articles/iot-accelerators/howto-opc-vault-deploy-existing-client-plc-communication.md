---
title: Proteger a comunicação do cliente OPC e PLC OPC OPC Vault - Azure | Microsoft Docs
description: Proteger a comunicação do cliente OPC e OPC PLC ao assinar os certificados usando a autoridade de certificação do Cofre de OPC.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 30eedd982fa0536ce45506c159de6d04132e9a14
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61451082"
---
# <a name="secure-the-communication-of-opc-client-and-opc-plc"></a>Proteger a comunicação do cliente OPC e OPC PLC

Cofre de OPC é um microsserviço que pode configurar, registrar e gerenciar o ciclo de vida de certificado para o servidor OPC UA e aplicativos de cliente na nuvem. Este artigo mostra como proteger a comunicação do cliente OPC e OPC PLC ao assinar os certificados usando a autoridade de certificação do Cofre de OPC.

A configuração a seguir, o cliente OPC testa a conectividade com o PLC OPC. Por padrão, a conectividade não é possível porque os dois componentes não são provisionados com os certificados à direita. Se um componente de OPC UA ainda não foi provisionado com um certificado, ele seria gerar um certificado autoassinado na inicialização. No entanto, o certificado pode ser assinado por uma autoridade de certificação (CA) e instalado no componente de OPC UA. Depois que isso é feito para o cliente OPC e PLC OPC, a conectividade está habilitada. O fluxo de trabalho a seguir descreve o processo. Algumas informações básicas sobre a segurança do OPC UA podem ser encontradas no [este documento](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf) white paper. As informações completas podem ser encontradas na especificação do OPC UA.

Plataforma de teste: O seguinte ambiente está configurado para teste.

Scripts de OPC cofre:
- Proteger a comunicação do cliente OPC e OPC PLC ao assinar os certificados usando a autoridade de certificação do Cofre de OPC.

> [!NOTE]
> Para obter mais informações, consulte o GitHub [repositório](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds).

## <a name="generate-a-self-signed-certificate-on-startup"></a>Gerar um certificado autoassinado na inicialização

**Preparação**

- Certifique-se de que as variáveis de ambiente `$env:_PLC_OPT` e `$env:_CLIENT_OPT` são indefinidos, por exemplo, `$env:_PLC_OPT=""` no seu PowerShell.

- Defina a variável de ambiente `$env:_OPCVAULTID` para uma cadeia de caracteres que permite que você encontre os dados novamente no cofre de OPC. Somente caracteres alfanuméricos são permitidos. Para nosso exemplo, "123456" foi usado como o valor para essa variável.

- Verifique se há não volumes docker `opcclient` ou `opcplc`. Entre em contato com `docker volume ls` e removê-los com `docker volume rm <volumename>`. Convém também remover contêineres com `docker rm <containerid>` se os volumes ainda são utilizados por um contêiner.

**Início rápido**

Execute o seguinte comando do PowerShell na raiz do repositório:

```
docker-compose -f connecttest.yml up
```

**Verificação**

Verifique no log que não há nenhum certificado instalado na primeira inicialização. Aqui, a saída do log de OPC PLC (aparece semelhante para o cliente OPC):...
```
opcplc-123456 | [20:51:32 INF] Trusted issuer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted issuer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Trusted peer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted peer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Rejected certificate store contains 0 certs
```
Se você vir certificados relatados, siga as etapas de preparação acima e exclua os volumes do docker.

Verifique se que a conexão para o OPC PLC falhou. Você verá a seguinte saída na saída de log do cliente OPC:

```
opcclient-123456 | [20:51:35 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:51:36 ERR] Session creation to endpoint 'opc.tcp://opcplc-123456:50000/' failed 1 time(s). Please verify if server is up and OpcClient configuration is correct.
opcclient-123456 | Opc.Ua.ServiceResultException: Certificate is not trusted.
```
O motivo da falha é que o certificado não é confiável. Isso significa que `opc-client` tentou se conectar ao `opc-plc` e recebeu uma resposta de volta, indicando que `opc-plc` não confia `opc-client`, um resultado `opc-plc` não é possível validar o certificado que `opc-client` forneceu. Este é um certificado autoassinado com nenhuma outra configuração de certificado no `opc-plc`, e, portanto, a conexão falhou.

## <a name="sign-and-install-certificates-in-opc-ua-components"></a>Entre e instalar certificados nos componentes do OPC UA

**Preparação**
1. Examine a saída de log da etapa 1 e buscar "CreateSigningRequest informações" para o PLC OPC e o cliente OPC. Veja a saída só é exibida para OPC PLC:

    ```
    opcplc-123456 | [20:51:32 INF] ----------------------- CreateSigningRequest information ------------------
    opcplc-123456 | [20:51:32 INF] ApplicationUri: urn:OpcPlc:opcplc-123456
    opcplc-123456 | [20:51:32 INF] ApplicationName: OpcPlc
    opcplc-123456 | [20:51:32 INF] ApplicationType: Server
    opcplc-123456 | [20:51:32 INF] ProductUri: https://github.com/azure-samples/iot-edge-opc-plc
    opcplc-123456 | [20:51:32 INF] DiscoveryUrl[0]: opc.tcp://opcplc-123456:50000
    opcplc-123456 | [20:51:32 INF] ServerCapabilities: DA
    opcplc-123456 | [20:51:32 INF] CSR (base64 encoded):
    opcplc-123456 | MIICmzCCAYMCAQAwETEPMA0GA1UEAwwGT3BjUGxjMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAwTvlbinAPWPxR9Lw1ndGsrLGy8GiqVOxyGaDpPUcMchX0k0/ncg28h7xrB2H1PThdiZxUJuUwsNM74HrVgt
    ofmXhA4dLM1cTxZHyJVFjl2L3vK5M58NNf6UNdKcB0x3LyuoT6mAIMXmCioqymFCk1TMzIMzbAe7JVAdUaSRBP1vuqQ1rV/cfNAe35dKQW4aPYgl7pR5f1hqprcDu/oca67X8L4kTl4oN0/bCYTk+Ibcd9cG462oAN+bSwbHn8a2jNky8rGsofA
    o9DOT+0ALPhk6CApCYIP2yxoI/kT188eqUUxzAFF9nyU79AyCkpGPuY8DGMyf56pDofgtGpfY3wQIDAQABoEUwQwYJKoZIhvcNAQkOMTYwNDAyBgNVHREEKzAphhh1cm46T3BjUGxjOm9wY3BsYy0xMjM0NTaCDW9wY3BsYy0xMjM0NTYwDQYJK
    oZIhvcNAQELBQADggEBAAsZLoOLzS2VhDcQRu0QhRbG7CGAxX19l7fDCG2WjU7lTFnCvYVTWTYyaY61ljmrWc7IbCaQdMJM8GRnAnvAzUh/PBDxkOX7NqI2+8F1yQOHgs/AfKuppOd6DIP8EzFAHnc0H85jay6zFdmIDWoWwpy0ACqOVooOTKST
    7uty0mT87bj8Cdy1yf4mvBNQx+nsuTbKgxWCBxGYAyg9dIL2uKL0aeB/ROW5Gkelz5sCEzQ1fFDokUA4oC5QiATQBN3cY7EmvRbPgdToY7CpRN3iiO7J+7bC7BP9YKfuE34E8xOFpskHPHAPf3r002/L0S67HyuVSXLUj1+Jc0LeAAF9Bw0=
    opcplc-123456 | [20:51:32 INF] ---------------------------------------------------------------------------
    ```
    
1. Vá para o [site do Cofre de OPC](https://opcvault.azurewebsites.net/).

1. Selecione `Register New`

1. Insira as informações de OPC PLC das saídas de log `CreateSigningRequest information` área nos campos de entrada a `Register New OPC UA Application` página, selecione `Server` como ApplicationType.

1. Selecione `Register`.

1. Na próxima página, `Request New Certificate for OPC UA Application` selecionar `Request new Certificate with Signing Request`.

1. Na próxima página, `Generate a new Certificate with a Signing Request` cole o `CSR (base64 encoded)` cadeia de caracteres da saída do log no `CreateRequest` campo de entrada. Verifique se que você copiar a cadeia de caracteres completa.

1. Selecione `Generate New Certificate`.

1. Você agora está movendo para frente para `View Certificate Request Details`. Nessa página, você pode baixar todas as informações necessárias para provisionar armazenamentos de certificados de `opc-plc`.

1. Nesta página:  
    - Selecione `Certificate` no `Download as Base64` e copie a cadeia de caracteres de texto apresentada na `EncodedBase64` do campo e armazená-lo para uso posterior. Nós nos referimos a eles como `<applicationcertbase64-string>` mais tarde. Selecione `Back`.

    - Selecione `Issuer` no `Download as Base64` e copie a cadeia de caracteres de texto apresentada na `EncodedBase64` do campo e armazená-lo para uso posterior. Nós nos referimos a eles como `<addissuercertbase64-string>` mais tarde. Selecione `Back`.

    - Selecione `Crl` no `Download as Base64` e copie a cadeia de caracteres de texto apresentada na `EncodedBase64` do campo e armazená-lo para uso posterior. Nós nos referimos a eles como `<updatecrlbase64-string>` mais tarde. Selecione `Back`.

1. Agora, definido no seu PowerShell uma variável chamada `$env:_PLC_OPT`:

    ```
    `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  
    ```
    > [!NOTE] 
    > Substitua as cadeias de caracteres passadas como a opção valores cadeias de caracteres Base64 buscadas do site.

Repita o processo completo, começando com `Register New` (etapa 3 acima) para o cliente OPC. Há apenas as seguintes diferenças que você precisa estar atento:

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

**Verificação** verifica se os dois componentes agora tiveram assinado os certificados de aplicativo. Verifique a saída de log para a saída a seguir:

```
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Starting to update the current CRL.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted peer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted issuer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Start updating the current application certificate.
opcplc-123456 | [20:54:38 INF] The current application certificate has SubjectName 'CN=OpcPlc' and thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] Remove the existing application certificate with thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' was added to the application certificate store.
opcplc-123456 | [20:54:39 INF] Activating the new application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586'.
opcplc-123456 | [20:54:39 INF] Application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' found in the application certificate store.
opcplc-123456 | [20:54:39 INF] Application certificate is for ApplicationUri 'urn:OpcPlc:opcplc-123456', ApplicationName 'OpcPlc' and Subject is 'OpcPlc'
 ```
O certificado do aplicativo está lá e assinado por uma autoridade de certificação.

Verifique se que agora há certificados instalados no log. Abaixo está a saída do log de OPC PLC e cliente OPC tem uma saída semelhante.
```
opcplc-123456 | [20:54:39 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Trusted peer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Rejected certificate store contains 0 certs
```
O emissor do certificado do aplicativo é a autoridade de certificação `CN=Azure IoT OPC Vault CA, O=Microsoft Corp.` e o OPC PLC também todos os certificados assinados por esta autoridade de certificação de confiança.


Verifique se a conexão para o OPC PLC foi criado com êxito e o cliente OPC pode ler dados do OPC PLC. Você verá a seguinte saída na saída de log do cliente OPC:
```
opcclient-123456 | [20:54:42 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:54:42 INF] Session successfully created with Id ns=3;i=1085867946.
opcclient-123456 | [20:54:42 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [20:54:42 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [20:54:42 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [20:54:42 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [20:54:42 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [20:54:42 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [20:54:42 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [20:54:42 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [20:54:42 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/20/2018 20:54:42
```
Se você vir essa saída, em seguida, o OPC PLC é agora confiantes OPC cliente e vice-versa, desde que ambos têm agora certificados assinados por uma autoridade de certificação e a relação de confiança de certificados que where assinado por esta autoridade de certificação.

> [!NOTE] 
> Embora somente para PLC OPC, mostramos as primeiras etapas de verificação de duas, aqueles precisam ser verificadas também para o cliente OPC.


## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como implantar o OPC cofre a um projeto existente, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Implantar o gêmeo de OPC para um projeto existente](howto-opc-twin-deploy-existing.md)