---
title: Atribuir políticas de acesso a pontos de extremidade de serviço
description: Saiba como atribuir políticas de segurança acesso aos pontos de extremidade HTTP ou HTTPS em seu serviço Service Fabric.
ms.topic: conceptual
ms.date: 03/21/2018
ms.openlocfilehash: c7d30e85848f045b5724bb8bdc6e5c810102c044
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "75614648"
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Atribuir uma política de acesso de segurança a pontos de extremidade HTTP e HTTPS
Se você aplicar uma política run-as a um serviço e o manifesto do serviço declarar recursos de ponto de extremidade, você deverá especificar um **SecurityAccessPolicy**.  O **SecurityAccessPolicy** garante que portas alocadas para esses pontos de extremidade sejam restritos corretamente para a conta de usuário que o serviço é executado. Caso contrário, **http.sys** não tem acesso ao serviço e você obtém falhas com chamadas do cliente. O exemplo a seguir aplica a conta Customer1 a um ponto de extremidade chamado **EndpointName**, concedendo a ele direitos de acesso completo.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

Para o ponto de extremidade HTTPS, também indique o nome do certificado para retornar para o cliente. Sua referência de certificado usando **EndpointBindingPolicy**.  O certificado é definido na seção **Certificados** do manifesto do aplicativo.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies>
```

> [!WARNING] 
> Ao usar HTTPS, não use a mesma porta e o certificado de instâncias de serviço diferente (independentemente do aplicativo) implantados no mesmo nó. O upgrade de dois serviços diferentes usando a mesma porta em instâncias de aplicativo diferentes resultará em uma falha de upgrade. Para obter mais informações, consulte [Atualizando vários aplicativos com pontos de extremidade HTTPS](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Para as próximas etapas, leia os seguintes artigos:
* [Entenda o modelo de aplicativo](service-fabric-application-model.md)
* [Especificar recursos em um manifesto do serviço](service-fabric-service-manifest-resources.md)
* [Implantar um aplicativo](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
