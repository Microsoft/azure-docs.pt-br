---
title: Executar um serviço de Service Fabric do Azure em uma conta do gMSA
description: Saiba como executar um serviço como uma conta de serviço gerenciado por grupo (gMSA) em um Cluster Service Fabric Windows autônomo.
ms.topic: how-to
ms.date: 03/29/2018
ms.openlocfilehash: 9750042764306c5df7a391429cc6926704db05ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91838901"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Executar um serviço como uma Conta de Serviço Gerenciado de grupo

Em um cluster autônomo do Windows Server, você pode executar um serviço como uma *conta de serviço gerenciado de grupo* (gMSA) usando uma política *runas* .  Por padrão, Service Fabric aplicativos são executados sob a conta sob a qual o `Fabric.exe` processo é executado. Executar os aplicativos em contas diferentes, mesmo em um ambiente hospedado compartilhado, torna-os mais protegidos uns dos outros. Ao usar uma gMSA, a senha ou senha criptografada não será armazenada no manifesto do aplicativo.  Você também pode executar um serviço como [Grupo ou usuário do Active Directory](service-fabric-run-service-as-ad-user-or-group.md).

O exemplo a seguir mostra como criar uma conta do gMSA chamada *svc-Test $*, como implantar essa conta de serviço gerenciado nos nós do cluster e como configurar a entidade de usuário.

> [!NOTE]
> Usar um gMSA com um Cluster Service Fabric autônomo requer Active Directory local em seu domínio (em vez de Azure Active Directory (AD do Azure)).

Pré-requisitos:

- O domínio precisa de uma chave raiz KDS.
- Deve haver pelo menos um DC do Windows Server 2012 (ou R2) no domínio.

1. Faça com que um administrador de domínio Active Directory crie uma conta de serviço gerenciado por grupo usando o `New-ADServiceAccount` cmdlet e verifique se o `PrincipalsAllowedToRetrieveManagedPassword` inclui todos os nós de Cluster Service Fabric. `AccountName`, `DnsHostName`, e `ServicePrincipalName` deve ser exclusivo.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Em cada um dos nós de cluster do Service Fabric (por exemplo, `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`), instale e teste a gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Configure a entidade de usuário e configure o `RunAsPolicy` para fazer referência ao [usuário](./service-fabric-cluster-fabric-settings.md#runas).
    
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

Os artigos a seguir irão orientá-lo nas próximas etapas:

- [Entenda o modelo de aplicativo](service-fabric-application-model.md)
- [Especificar recursos em um manifesto do serviço](service-fabric-service-manifest-resources.md)
- [Implantar um aplicativo](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
