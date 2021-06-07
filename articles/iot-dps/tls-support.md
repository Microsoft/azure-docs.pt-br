---
title: Suporte a TLS do DPS (Serviço de Provisionamento de Dispositivos) de IoT do Azure
description: Melhores práticas no uso de conexões TLS seguras para dispositivos e serviços se comunicando com o DPS (Serviço de Provisionamento de Dispositivos) de IoT
services: iot-dps
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: wesmc
ms.openlocfilehash: 3a8910cf0e81bd041d74ef95f45220f1c1e0b34c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91761287"
---
# <a name="tls-support-in-azure-iot-hub-device-provisioning-service-dps"></a>Suporte a TLS no DPS (Serviço de Provisionamento de Dispositivos) no Hub IoT do Azure

O DPS usa o [Protocolo TLS](http://wikipedia.org/wiki/Transport_Layer_Security) para proteger conexões de dispositivos IoT. 

As versões atuais de protocolo TLS com suporte do DPS são: 
* TLS 1.2

O TLS 1.0 e o 1.1 são considerados herdados e são planejados para substituição. Para obter mais informações, confira [Substituir TLS 1.0 e 1.1 para o Hub IoT](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md). 

## <a name="restrict-connections-to-tls-12"></a>Restringir conexões ao TLS 1.2

Para maior segurança, é aconselhável configurar suas instâncias de DPS para permitir *somente* conexões de cliente de dispositivo que usam a versão 1.2 do TLS e para impor o uso das [codificações recomendadas](#recommended-ciphers).

Para fazer isso, provisione um novo recurso DPS definindo a `minTlsVersion` propriedade na `1.2` especificação de recurso DPS de modelos do Azure Resource Manager. O JSON de modelo de exemplo a seguir especifica a propriedade `minTlsVersion` para uma nova instância de DPS.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/ProvisioningServices",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-DPS-resource-name>",
            "location": "<any-region>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "S1",
                "capacity": 1
            },
        }     
    ]
}
```

Para implantar o modelo, execute o comando da CLI do Azure a seguir. 

```azurecli
az deployment group create -g <your resource group name> --template-file template.json
```

Para obter mais informações sobre a criação de recursos de DPS com modelos do Resource Manager, confira [Configurar o DPS com um modelo do Azure Resource Manager](quick-setup-auto-provision-rm.md).

O recurso de DPS criado usando essa configuração recusará dispositivos que tentam se conectar usando as versões 1.0 e 1.1 do TLS. Da mesma maneira, o handshake do TLS será recusado se a mensagem "OLÁ" do cliente do dispositivo não listar nenhuma das [codificações recomendadas](#recommended-ciphers).

> [!NOTE]
> A propriedade `minTlsVersion` é somente leitura e não pode ser alterada depois que o recurso de DPS é criado. Portanto, é essencial que você teste e valide corretamente e com antecedência que *todos* os dispositivos IoT são compatíveis com o TLS 1.2 e com as [codificações recomendadas](#recommended-ciphers).


> [!NOTE]
> Após os failovers, a propriedade `minTlsVersion` de seu DPS permanecerá efetiva na região emparelhada geograficamente após o failover.

## <a name="recommended-ciphers"></a>Codificações recomendadas

As instâncias de DPS configuradas para aceitar somente o TLS 1.2 também vão impor o uso dos pacotes de codificação a seguir:


| Pacotes de codificação TLS 1.2 recomendados |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`<br>`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256` |


### <a name="legacy-cipher-suites"></a>Pacotes de codificação herdados 

Esses pacotes de criptografia ainda são compatíveis com o DPS, mas logo serão depreciados. Se possível, use os pacotes de criptografia recomendados acima.

| Opção nº 1 (melhor segurança) |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P384   (uses SHA-1)`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256   (uses SHA-1)`<br>`TLS_RSA_WITH_AES_256_GCM_SHA384           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_GCM_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)` |

| Opção nº 2 (melhor desempenho) |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256   (uses SHA-1)`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P384   (uses SHA-1)`<br>`TLS_RSA_WITH_AES_128_GCM_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_GCM_SHA384           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)` |


## <a name="use-tls-12-in-the-iot-sdks"></a>Usar o TLS 1.2 nos SDKs de IoT

Use os links abaixo para configurar o TLS 1.2 e as codificações permitidas nos SDKs do cliente IoT do Azure.

| Linguagem | Versões compatíveis com o TLS 1.2 | Documentação |
|----------|------------------------------------|---------------|
| C        | Marca 2019-12-11 ou mais recente            | [Link](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Versão 2.0.0 ou mais recente             | [Link](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Versão 1.21.4 ou mais recente            | [Link](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Versão 1.19.0 ou mais recente            | [Link](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Versão 1.12.2 ou mais recente            | [Link](https://aka.ms/Tls_Node_SDK_IoT) |

## <a name="use-tls-12-with-iot-hub"></a>Usar o TLS 1.2 com o Hub IoT

O Hub IoT pode ser configurado para usar o TLS 1.2 ao se comunicar com dispositivos. Para obter mais informações, confira [Substituir TLS 1.0 e 1.1 para o Hub IoT](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md).

## <a name="use-tls-12-with-iot-edge"></a>Usar o TLS 1.2 com IoT Edge

Dispositivos do IoT Edge podem ser configurados para usar o TLS 1.2 ao se comunicarem com o Hub IoT e o DPS. Para saber mais, confira a [página de documentação IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).
