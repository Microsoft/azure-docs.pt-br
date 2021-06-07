---
title: Métodos de autenticação do agente de segurança
description: Saiba mais sobre os diferentes métodos de autenticação disponíveis ao usar o serviço defender para IoT.
ms.topic: conceptual
ms.date: 01/24/2021
ms.openlocfilehash: bd4d89d8664dae2421ff7f268b3710b5a906427b
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784604"
---
# <a name="security-agent-authentication-methods"></a>Métodos de autenticação do agente de segurança

Este artigo explica os diferentes métodos de autenticação que você pode usar com o agente AzureIoTSecurity para autenticar com o Hub IoT.

Para cada dispositivo integrado ao defender para IoT no Hub IoT, é necessário um defender-IoT-micro-Agent. Para autenticar o dispositivo, o defender para IoT pode usar um dos dois métodos. Escolha o método que funciona melhor para sua solução de IoT existente.

- Opção SecurityModule
- Opção de dispositivo

## <a name="authentication-methods"></a>Métodos de autenticação

Os dois métodos para o agente do defender for IoT AzureIoTSecurity para realizar a autenticação:

- Modo de autenticação do **defender-IOT-micro-Agent**<br>
O agente é autenticado usando a identidade do defender-IoT-micro-Agent independentemente da identidade do dispositivo.
Use esse tipo de autenticação se desejar que o agente de segurança Use um método de autenticação dedicado por meio do defender-IoT-micro-Agent (somente chave simétrica).

- Modo de autenticação do **dispositivo**<br>
Nesse método, o agente de segurança primeiro é autenticado com a identidade do dispositivo. Após a autenticação inicial, o agente do defender para IoT executa uma chamada **REST** para o Hub IOT usando a API REST com os dados de autenticação do dispositivo. Em seguida, o agente do defender para IoT solicita o método de autenticação do defender-IoT-micro-Agent e os dados do Hub IoT. Na etapa final, o agente do defender para IoT executa uma autenticação no módulo defender para IoT.

Use esse tipo de autenticação se desejar que o agente de segurança reutilize um método de autenticação de dispositivo existente (certificado autoassinado ou chave simétrica).

Consulte [parâmetros de instalação do agente de segurança](#security-agent-installation-parameters) para saber como configurar o.

## <a name="authentication-methods-known-limitations"></a>Limitações conhecidas dos métodos de autenticação

- O modo de autenticação **SecurityModule** dá suporte apenas à autenticação de chave simétrica.
- O modo de autenticação do **dispositivo** não dá suporte para CA-Signed certificado.

## <a name="security-agent-installation-parameters"></a>Parâmetros de instalação do agente de segurança

Ao [implantar um agente de segurança](how-to-deploy-agent.md), os detalhes de autenticação devem ser fornecidos como argumentos.
Esses argumentos são documentados na tabela a seguir.

|Nome do parâmetro do Linux | Nome do parâmetro do Windows | Parâmetro abreviado |Descrição|Opções|
|---------------------|---------------|---------|---------------|---------------|
|autenticação-identidade|AuthenticationIdentity|aui|Identidade de autenticação| **SecurityModule** ou **dispositivo**|
|método de autenticação|AuthenticationMethod|aum|Método de autenticação|**SymmetricKey** ou **SelfSignedCertificate**|
|caminho do arquivo|FilePath|f|Caminho completo absoluto para o arquivo que contém o certificado ou a chave simétrica| |
|nome do host|HostName|HN|FQDN do Hub IoT|Exemplo: ContosoIotHub.azure-devices.net|
|ID do dispositivo|DeviceId|di|ID do Dispositivo|Exemplo: MyDevice1|
|tipo de local de certificado|CertificateLocationKind|l|Local de armazenamento do certificado|**Local** ou **repositório**|
|

Ao usar o script de instalação do agente de segurança, a configuração a seguir é executada automaticamente. Para editar a autenticação do agente de segurança manualmente, edite o arquivo de configuração.

## <a name="change-authentication-method-after-deployment"></a>Alterar o método de autenticação após a implantação

Ao implantar um agente de segurança com um script de instalação, um arquivo de configuração é criado automaticamente.

Para alterar os métodos de autenticação após a implantação, a edição manual do arquivo de configuração é necessária.

### <a name="c-based-security-agent"></a>Agente de segurança baseado em C#

Edite _Authentication.config_ com os seguintes parâmetros:

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>Agente de segurança baseado em C

Edite _LocalConfiguration.js_ com os seguintes parâmetros:

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>Veja também

- [Visão geral dos agentes de segurança](security-agent-architecture.md)
- [Implantar agente de segurança](how-to-deploy-agent.md)
- [Acessar dados de segurança brutos](how-to-security-data-access.md)
