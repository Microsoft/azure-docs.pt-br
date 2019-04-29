---
title: Executar um serviço do Azure Service Fabric em uma conta gMSA | Microsoft Docs
description: Saiba como executar um serviço como gMSA em um cluster autônomo do Service Fabric Windows.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: dekapur
ms.openlocfilehash: 5c3781c2111fff7483a7fb65bd7b2e69c2011d18
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837735"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Executar um serviço como uma Conta de Serviço Gerenciado de grupo
Em um cluster do Windows Server autônomo, você pode executar um serviço como um grupo de Conta de Serviço gerenciado (gMSA) usando uma política de RunAs.  Por padrão, os aplicativos de Service Fabric são executados na conta sob a qual o processo Fabric.exe está sendo executado. Executar os aplicativos em contas diferentes, mesmo em um ambiente hospedado compartilhado, torna-os mais protegidos uns dos outros. Observe que esse é o Active Directory local em seu domínio e não é com o Azure Active Directory (Azure AD). Ao usar uma gMSA, a senha ou senha criptografada não será armazenada no manifesto do aplicativo.  Você também pode executar um serviço como [Grupo ou usuário do Active Directory](service-fabric-run-service-as-ad-user-or-group.md).

O exemplo a seguir mostra como criar uma conta gMSA chamada *svc-Test$*; como implantar essa conta de serviço gerenciado nos nós de cluster; e como configurar a entidade de segurança do usuário.

Pré-requisitos:
- O domínio precisa de uma chave raiz KDS.
- O domínio deve ser um Windows Server 2012 ou nível funcional posterior.

1. Peça para um administrador de domínio do Active Directory criar uma conta de serviço gerenciado de grupo usando o cmdlet `New-ADServiceAccount` e verifique se o `PrincipalsAllowedToRetrieveManagedPassword` inclui todos os nós de cluster de malha do serviço. `AccountName`, `DnsHostName`, e `ServicePrincipalName` deve ser exclusivo.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Em cada um dos nós de cluster do Service Fabric (por exemplo, `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`), instale e teste a gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Configure a entidade de usuário e a RunAsPolicy para fazer referência ao usuário.
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
          <ConfigOverrides />
          <Policies>
              <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
          </Policies>
        </ServiceManifestImport>
      <Principals>
        <Users>
          <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
        </Users>
      </Principals>
    </ApplicationManifest>
    ```

> [!NOTE] 
> Se você aplicar uma política RunAs a um serviço e o manifesto do serviço declarar recursos de ponto de extremidade com o protocolo HTTP, você deverá especificar um **SecurityAccessPolicy**.  Para obter mais informações, consulte [Atribuir uma política de acesso de segurança a pontos de extremidade HTTP e HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Como uma próxima etapa, leia os seguintes artigos:
* [Entenda o modelo de aplicativo](service-fabric-application-model.md)
* [Especificar recursos em um manifesto do serviço](service-fabric-service-manifest-resources.md)
* [Implantar um aplicativo](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
