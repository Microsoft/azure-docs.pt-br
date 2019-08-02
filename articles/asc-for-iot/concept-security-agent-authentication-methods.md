---
title: Métodos de autenticação para a Central de segurança do Azure para a versão prévia do IoT | Microsoft Docs
description: Saiba mais sobre os diferentes métodos de autenticação disponíveis ao usar a Central de segurança do Azure para o serviço de IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: d5701ae37d64e25fba981cd85deed2c4e4d87a15
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618348"
---
# <a name="security-agent-authentication-methods"></a>Métodos de autenticação do agente de segurança 

> [!IMPORTANT]
> A Central de Segurança do Azure para IoT está em versão prévia pública no momento.
> Esta versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica os diferentes métodos de autenticação que você pode usar com o agente AzureIoTSecurity para autenticar com o IoT Hub.

Para cada dispositivo integrada ao Azure Security ASC (Central) para IoT no IoT Hub, um módulo de segurança é necessário. Para autenticar o dispositivo, ASC para IoT pode usar um dos dois métodos. Escolha o método que funciona melhor para sua solução de IoT existente. 

> [!div class="checklist"]
> * Opção de módulo de segurança
> * Opção de dispositivo

## <a name="authentication-methods"></a>Métodos de autenticação

Os dois métodos para o agente AzureIoTSecurity realizar a autenticação:

 - **Módulo** modo de autenticação<br>
   O módulo é autenticado, independentemente do dispositivo gêmeo.
   Use esse tipo de autenticação se desejar que o agente de segurança para usar um método de autenticação dedicado por meio do módulo de segurança (somente a chave simétrica).
        
 - **Dispositivo** modo de autenticação<br>
    Nesse método, o agente de segurança pela primeira vez é autenticado com a identidade do dispositivo. Após a autenticação inicial, o ASC para agente do IoT executa um **REST** chamada para o IoT Hub usando a API REST com os dados de autenticação do dispositivo. O ASC para agente do IoT, em seguida, solicita o método de autenticação do módulo de segurança e os dados do IoT Hub. Na etapa final, o ASC para agente do IoT executa uma autenticação em relação a ASC para o módulo do IoT.
    
    Use esse tipo de autenticação se desejar que o agente de segurança reutilizar um método de autenticação de dispositivo existente (autoassinado certificado ou chave simétrica). 

Ver [parâmetros de instalação do agente de segurança](#security-agent-installation-parameters) para aprender a configurar.
                                
## <a name="authentication-methods-known-limitations"></a>Métodos de autenticação limitações conhecidas

- **Módulo** apenas o modo de autenticação dá suporte à autenticação de chave simétrica.
- Certificado assinado por autoridade de certificação não é compatível com **dispositivo** modo de autenticação.  

## <a name="security-agent-installation-parameters"></a>Parâmetros de instalação do agente de segurança

Quando [implantar um agente de segurança](how-to-deploy-agent.md), detalhes de autenticação devem ser fornecidos como argumentos.
Esses argumentos são documentados na tabela a seguir.


|Parâmetro|DESCRIÇÃO|Opções|
|---------|---------------|---------------|
|**identidade**|Modo de autenticação| **Módulo** ou **dispositivo**|
|**type**|Tipo de autenticação|**SymmetricKey** ou **SelfSignedCertificate**|
|**filePath**|Absoluto caminho completo do arquivo que contém o certificado ou a chave simétrica| |
|**gatewayHostname**|FQDN do Hub IoT|Exemplo: ContosoIotHub.azure-devices.net|
|**deviceId**|Id do Dispositivo|Exemplo: MyDevice1|
|**certificateLocationKind**|Local de armazenamento de certificado|**LocalFile** ou **Store**|


Ao usar o script de agente de segurança de instalação, a configuração a seguir é executada automaticamente.

Para editar manualmente a autenticação do agente de segurança, edite o arquivo config. 

## <a name="change-authentication-method-after-deployment"></a>Alterar o método de autenticação após a implantação

Ao implantar um agente de segurança com um script de instalação, um arquivo de configuração é criado automaticamente.

Para alterar os métodos de autenticação após a implantação, a edição manual do arquivo de configuração é necessária.


### <a name="c-based-security-agent"></a>C#-com base em agente de segurança

Edite _Authentication_ com os seguintes parâmetros:

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

### <a name="c-based-security-agent"></a>Agente de segurança com base em C

Edite _LocalConfiguration.json_ com os seguintes parâmetros:

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>Consulte também
- [Visão geral dos agentes de segurança](security-agent-architecture.md)
- [Implantar o agente de segurança](how-to-deploy-agent.md)
- [Acessar os dados brutos de segurança](how-to-security-data-access.md)
