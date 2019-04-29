---
title: Atribuir políticas de acesso aos pontos de extremidade de serviço do Azure Service Fabric | Microsoft Docs
description: Saiba como atribuir políticas de segurança acesso aos pontos de extremidade HTTP ou HTTPS em seu serviço Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: atsenthi
ms.openlocfilehash: 3e892e443f5e3309add48f939f26ba14eaf5a51b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614197"
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Atribuir uma política de acesso de segurança a pontos de extremidade HTTP e HTTPS
Se você aplicar uma política run-as a um serviço e o manifesto do serviço declarar recursos de ponto de extremidade, você deverá especificar um **SecurityAccessPolicy**.  O **SecurityAccessPolicy** garante que portas alocadas para esses pontos de extremidade sejam restritos corretamente para a conta de usuário que o serviço é executado. Caso contrário, o **http.sys** não terá acesso ao serviço e você receberá uma falha com chamadas do cliente. O exemplo a seguir aplica a conta Customer1 a um ponto de extremidade chamado **EndpointName**, concedendo a ele direitos de acesso completo.

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
