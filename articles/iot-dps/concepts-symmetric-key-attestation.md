---
title: Serviço de Provisionamento de Dispositivos no Hub IoT do Azure – atestado de chave simétrica
description: Este artigo fornece uma visão geral conceitual do atestado de chave simétrica usando o serviço de provisionamento de dispositivos de IoT.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: b1a849732539dbc9e066bee7cc20141f56ffe10c
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348361"
---
# <a name="symmetric-key-attestation"></a>Atestado de chave simétrica

Este artigo descreve o processo de atestado de identidade ao usar chaves simétricas com o serviço de provisionamento de dispositivos. 

O atestado de chave simétrica é uma abordagem simples para autenticar o dispositivo com uma instância do serviço de provisionamento de dispositivos. Esse método de atestado representa uma experiência de "Olá, Mundo" para desenvolvedores que são novos no provisionamento de dispositivos ou não tem requisitos de segurança rígidos. O atestado de dispositivos usando um [TPM](concepts-tpm-attestation.md) ou um [certificado X.509](concepts-security.md#x509-certificates) é mais seguro e deve ser usado para as exigências de segurança mais rigorosas.

Os registros de chave simétrica também são uma ótima maneira para que dispositivos herdados, com a funcionalidade de segurança limitada, sejam inicializados na nuvem por meio do IoT do Azure. Para obter mais informações sobre o atestado de chave simétrica com dispositivos herdados, confira [Como usar chaves simétricas com dispositivos herdados](how-to-legacy-device-symm-key.md).


## <a name="symmetric-key-creation"></a>Criação da chave simétrica

Por padrão, o serviço de provisionamento de dispositivos cria chaves simétricas com um tamanho padrão de 32 bytes quando novos registros são salvos com a opção **Gerar chaves automaticamente** habilitada.

![Gerar chaves simétricas automaticamente](./media/concepts-symmetric-key-attestation/auto-generate-keys.png)

Você também pode fornecer suas próprias chaves simétricas para registros desabilitando essa opção. Ao especificar suas próprias chaves simétricas, saiba que elas precisam ter um tamanho entre 16 bytes e 64 bytes. Além disso, as chaves simétricas precisam ser fornecidas no formato Base64 válido.



## <a name="detailed-attestation-process"></a>Processo de atestado detalhado

O atestado de chave simétrica com o serviço de provisionamento de dispositivos é executado usando os mesmos [Tokens de segurança](../iot-hub/iot-hub-devguide-security.md#security-token-structure) com suporte dos Hubs IoT para identificar os dispositivos. Esses tokens de segurança são [tokens SAS (Assinatura de Acesso Compartilhado)](../service-bus-messaging/service-bus-sas.md). 

Os tokens SAS têm uma *assinatura* com hash que é criada usando a chave simétrica. A assinatura é recriada pelo serviço de provisionamento de dispositivos para verificar se um token de segurança apresentado durante o atestado é autêntico ou não.

Os tokens SAS têm o seguinte formato:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Estes são os componentes de cada token:

| Valor | Descrição |
| --- | --- |
| {signature} |Uma cadeia de caracteres de assinatura HMAC-SHA256. Para registros individuais, essa assinatura é produzida usando a chave simétrica (primária ou secundária) para realizar o hash. Para grupos de registro, uma chave derivada da chave do grupo de registro é usada para executar o hash. O hash é executado em uma mensagem no formato: `URL-encoded-resourceURI + "\n" + expiry`. **Importante**: A chave deve ser decodificada de Base64 antes de ser usada para executar a computação HMAC-SHA256. Além disso, o resultado da assinatura precisa ser codificada como URL. |
| {resourceURI} |O URI do ponto de extremidade de registro que pode ser acessado com esse token, começando com a ID do escopo da instância do serviço de provisionamento de dispositivos. Por exemplo, `{Scope ID}/registrations/{Registration ID}` |
| {expiry} |As cadeias de caracteres UTF8 para o número de segundos desde a época 00:00:00 UTC em 1º de janeiro de 1970. |
| {URL-encoded-resourceURI} |Codificação de URL em letras minúsculas do URI de recurso em letras minúsculas |
| {policyName} |O nome da política de acesso compartilhado a qual se refere esse token. O nome da política usada durante o provisionamento com atestado de chaves simétricas é **registro**. |

Quando um dispositivo é atestado com um registro individual, ele usa a chave simétrica definida na entrada do registro individual para criar a assinatura com hash para o token SAS.

Para obter exemplos de código que criam um token SAS, confira [Tokens de segurança](../iot-hub/iot-hub-devguide-security.md#security-token-structure).

Há suporte para a criação de tokens de segurança para atestado de chave simétrica pelo SDK C de IoT do Azure. Para obter um exemplo de uso do SDK C de IoT do Azure a ser atestado com um registro individual, confira [Provisionar um dispositivo simulado com chaves simétricas](quick-create-simulated-device-symm-key.md).


## <a name="group-enrollments"></a>Registros de grupo

As chaves simétricas para registros de grupo não são usadas diretamente pelos dispositivos durante o provisionamento. Nesse caso, os dispositivos que pertencem a um grupo de registro são provisionados usando uma chave de dispositivo derivada. 

Primeiro, uma ID de registro exclusiva é definida para cada dispositivo que está sendo atestado com um grupo de registro. Os caracteres válidos para a ID do registro são alfanuméricos de letras minúsculas e hífen ('-'). Essa ID de registro deve ser algo exclusivo que identifique o dispositivo. Por exemplo, um dispositivo herdado não pode dar suporte a vários recursos de segurança. O dispositivo herdado pode ter apenas um endereço MAC ou número de série disponível para identificar exclusivamente esse dispositivo. Nesse caso, uma ID de registro pode ser composta do endereço MAC e do número de série, semelhante à seguinte:

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Esse exemplo exato é usado no artigo [Como provisionar dispositivos herdados usando chaves simétricas](how-to-legacy-device-symm-key.md).

Depois que uma ID de registro tiver sido definida para o dispositivo, a chave simétrica do grupo de registro é usada para calcular um hash [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) da ID do registro para produzir uma chave de dispositivo derivada. O hash da ID do registro pode ser executado com o código C# a seguir:

```csharp
using System; 
using System.Security.Cryptography; 
using System.Text;  

public static class Utils 
{ 
    public static string ComputeDerivedSymmetricKey(byte[] masterKey, string registrationId) 
    { 
        using (var hmac = new HMACSHA256(masterKey)) 
        { 
            return Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(registrationId))); 
        } 
    } 
} 
```

```csharp
String deviceKey = Utils.ComputeDerivedSymmetricKey(Convert.FromBase64String(masterKey), registrationId);
```

Em seguida, a chave do dispositivo resultante é usada para gerar um token SAS a ser usado para o atestado. Cada dispositivo em um grupo de registro é necessário para atestar usando um token de segurança gerado de uma chave derivada exclusiva. A chave simétrica do grupo de registro não pode ser usada diretamente para o atestado.

#### <a name="installation-of-the-derived-device-key"></a>Instalação da chave de dispositivo derivada

O ideal é que as chaves de dispositivo sejam derivadas e instaladas na fábrica. Esse método garante que a chave de grupo nunca seja incluída em nenhum software implantado no dispositivo. Quando o dispositivo é atribuído a um endereço MAC ou número de série, a chave pode ser derivada e injetada no dispositivo independentemente da maneira que o fabricante deseja armazená-la.

Considere o seguinte diagrama que mostra uma tabela de chaves de dispositivo geradas em um alocador inserindo hash em cada ID de registro de dispositivo com chave de registro de grupo (**K**). 

![Chaves de dispositivo atribuídas de uma fábrica](./media/concepts-symmetric-key-attestation/key-diversification.png)

A identidade de cada dispositivo é representada pela ID de registro e a chave do dispositivo derivada que instaladas no alocador. A chave do dispositivo nunca é copiada para outro local e a chave de grupo nunca é armazenada em um dispositivo.

Se as chaves de dispositivo não estiverem instaladas no alocador, um [HSM de módulo de segurança de hardware](concepts-security.md#hardware-security-module) deverá ser usado para armazenar a identidade do dispositivo com segurança.

## <a name="next-steps"></a>Próximas etapas

Agora que você já entende um pouco sobre atestado de chave simétrica, confira os artigos a seguir para saber mais:

* [Início Rápido: Provisionar um dispositivo simulado com chaves simétricas](quick-create-simulated-device-symm-key.md)
* [Saiba mais sobre os conceitos de provisionamento automático](./concepts-auto-provisioning.md)
* [Introdução ao uso do provisionamento automático](./quick-setup-auto-provision.md) 
