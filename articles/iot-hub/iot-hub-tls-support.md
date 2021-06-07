---
title: Suporte a TLS no Hub IoT do Azure
description: Saiba mais sobre como usar conexões TLS seguras para dispositivos e serviços se comunicando com o Hub IoT
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: jlian
ms.openlocfilehash: d36a7917693aef9063ade473759f2f451d3a677f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98234011"
---
# <a name="transport-layer-security-tls-support-in-iot-hub"></a>Suporte ao protocolo TLS no Hub IoT

O Hub IoT usa o protocolo TLS para proteger as conexões contra dispositivos e serviços de IoT. Atualmente, três versões do protocolo TLS são compatíveis, principalmente as versões 1.0, 1.1 e 1.2.

O TLS 1.0 e o 1.1 são considerados herdados e são planejados para substituição. Para obter mais informações, confira [Substituir TLS 1.0 e 1.1 para o Hub IoT](iot-hub-tls-deprecating-1-0-and-1-1.md). Para evitar problemas futuros, use o TLS 1,2 como a única versão de TLS ao se conectar ao Hub IoT.

## <a name="iot-hubs-server-tls-certificate"></a>Certificado TLS do servidor do Hub IoT

Durante um handshake de TLS, o Hub IoT apresenta certificados de servidor com chave RSA para conectar clientes. Sua raiz é a CA raiz Baltimore Cybertrust. Recentemente, distribuímos uma alteração em nosso certificado do servidor TLS para que ele seja emitido agora por novas ICA (autoridades de certificação) intermediárias. Para obter mais informações, consulte [atualização de certificado TLS do Hub IOT](https://azure.microsoft.com/updates/iot-hub-tls-certificate-update/).

### <a name="elliptic-curve-cryptography-ecc-server-tls-certificate-preview"></a>Certificado TLS do servidor de criptografia de curva elíptica (ECC) (visualização)

O certificado TLS do servidor ECC do Hub IoT está disponível para visualização pública. Ao mesmo tempo em que oferece segurança semelhante aos certificados RSA, a validação de certificado ECC (com conjuntos de codificação somente ECC) usa até 40% menos computação, memória e largura de banda. Essas economias são importantes para dispositivos IoT devido a seus perfis menores e memória e para dar suporte a casos de uso em ambientes limitados de largura de banda de rede. 

Para visualizar o certificado do servidor ECC do Hub IoT:

1. [Crie um novo hub IOT com o modo de visualização ativado](iot-hub-preview-mode.md).
1. [Configure seu cliente](#tls-configuration-for-sdk-and-iot-edge) para incluir *apenas* conjuntos de codificação de ECDSA e *excluir* qualquer RSA. Estes são os conjuntos de codificação com suporte para a visualização pública do certificado ECC:
    - `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`
    - `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384`
1. Conecte seu cliente ao Hub IoT de visualização.

## <a name="tls-12-enforcement-available-in-select-regions"></a>Imposição de TLS 1,2 disponível em regiões selecionadas

Para maior segurança, configure seus hubs IoT para permitir *somente* conexões de cliente que usam TLS versão 1,2 e para impor o uso de [conjuntos de codificação](#cipher-suites). Este recurso só tem suporte nestas regiões:

* Leste dos EUA
* Centro-Sul dos Estados Unidos
* Oeste dos EUA 2
* Governo dos EUA do Arizona
* US Gov-Virgínia (o suporte a TLS 1.0/1.1 não está disponível nesta região-TLS 1,2 a imposição deve ser habilitada ou a criação do Hub IoT falhará)

Para habilitar a imposição de TLS 1,2, siga as etapas em [criar Hub IOT no portal do Azure](iot-hub-create-through-portal.md), exceto

- Escolha uma **região** de uma na lista acima.
- Em **Gerenciamento-> avançado-> segurança de camada de transporte (TLS)-> versão mínima do TLS**, selecione **1,2**. Essa configuração só aparece para o Hub IoT criado na região com suporte.

    :::image type="content" source="media/iot-hub-tls-12-enforcement.png" alt-text="Captura de tela mostrando como ativar a imposição de TLS 1,2 durante a criação do Hub IoT":::

Para usar o modelo ARM para criação, provisione um novo hub IoT em qualquer uma das regiões com suporte e defina a `minTlsVersion` propriedade como `1.2` na especificação de recurso:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

O recurso de Hub IoT criado usando essa configuração recusará clientes de dispositivos e serviços que tentam se conectar usando as versões 1.0 e 1.1 do TLS. Da mesma forma, o handshake TLS será recusado se a `ClientHello` mensagem não listar nenhuma das [codificações recomendadas](#cipher-suites).

> [!NOTE]
> A propriedade `minTlsVersion` é somente leitura e não pode ser alterada depois que o recurso de Hub IoT é criado. Portanto, é essencial que você teste e valide corretamente e com antecedência que *todos* os dispositivos e serviços de IoT são compatíveis com o TLS 1.2 e com as [codificações recomendadas](#cipher-suites).
> 
> Após os failovers, a propriedade `minTlsVersion` de seu Hub IoT permanecerá efetiva na região emparelhada geograficamente após o failover.

## <a name="cipher-suites"></a>Conjuntos de criptografia

Os hubs IoT configurados para aceitar somente o TLS 1,2 também impedirão o uso dos seguintes conjuntos de codificação recomendados:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

Para os hubs IoT não configurados para imposição de TLS 1,2, o TLS 1,2 ainda funciona com os seguintes conjuntos de codificação:

* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_DHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_DHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_CBC_SHA256`
* `TLS_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

Um cliente pode sugerir uma lista de pacotes de codificação mais altos a serem usados durante `ClientHello` . No entanto, alguns deles podem não ter suporte do Hub IoT (por exemplo, `ECDHE-ECDSA-AES256-GCM-SHA384` ). Nesse caso, o Hub IoT tentará seguir a preferência do cliente, mas, eventualmente, negociará o pacote de codificação com `ServerHello` .

## <a name="tls-configuration-for-sdk-and-iot-edge"></a>Configuração de TLS para SDK e IoT Edge

Use os links abaixo para configurar o TLS 1.2 e as codificações permitidas nos SDKs do cliente do Hub IoT.

| Linguagem | Versões compatíveis com o TLS 1.2 | Documentação |
|----------|------------------------------------|---------------|
| C        | Marca 2019-12-11 ou mais recente            | [Link](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Versão 2.0.0 ou mais recente             | [Link](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Versão 1.21.4 ou mais recente            | [Link](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Versão 1.19.0 ou mais recente            | [Link](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Versão 1.12.2 ou mais recente            | [Link](https://aka.ms/Tls_Node_SDK_IoT) |

Dispositivos do IoT Edge podem ser configurados para usar o TLS 1.2 ao se comunicarem com o Hub IoT. Para essa finalidade, use a página de documentação do [IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).

## <a name="device-authentication"></a>Autenticação de dispositivo

Após um handshake de TLS bem-sucedido, o Hub IoT pode autenticar um dispositivo usando uma chave simétrica ou um certificado X. 509. Para a autenticação baseada em certificado, isso pode ser qualquer certificado X. 509, incluindo ECC. O Hub IoT valida o certificado em relação à impressão digital ou à AC (autoridade de certificação) fornecida por você. Para saber mais, consulte [certificados X. 509 com suporte](iot-hub-devguide-security.md#supported-x509-certificates).

## <a name="tls-maximum-fragment-length-negotiation-preview"></a>Negociação de comprimento máximo de fragmento de TLS (visualização)

O Hub IoT também dá suporte à negociação de comprimento máximo de fragmento TLS, que às vezes é conhecido como negociação de tamanho de quadro TLS. Esse recurso está em uma versão prévia. 

Use esse recurso para especificar o comprimento máximo do fragmento de texto não criptografado com um valor menor que o padrão de 2 ^ 14 bytes. Depois de negociado, o Hub IoT e o cliente começam a fragmentar mensagens para garantir que todos os fragmentos sejam menores do que o comprimento negociado. Esse comportamento é útil para computação ou dispositivos com restrição de memória. Para saber mais, confira a [especificação de extensão TLS oficial](https://tools.ietf.org/html/rfc6066#section-4).

O suporte oficial do SDK para esse recurso de visualização pública ainda não está disponível. Para começar

1. [Crie um novo hub IOT com o modo de visualização ativado](iot-hub-preview-mode.md).
1. Ao usar o OpenSSL, chame [SSL_CTX_set_tlsext_max_fragment_length](https://manpages.debian.org/testing/libssl-doc/SSL_CTX_set_max_send_fragment.3ssl.en.html) para especificar o tamanho do fragmento.
1. Conecte seu cliente ao Hub IoT de visualização.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre segurança e controle de acesso do Hub IoT, confira [controlar o acesso ao Hub IOT](iot-hub-devguide-security.md).
- Para saber mais sobre como usar o certificado X509 para autenticação de dispositivo, confira [autenticação de dispositivo usando certificados de AC X. 509](iot-hub-x509ca-overview.md)
