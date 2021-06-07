---
title: Executar um serviço de Service Fabric do Azure como um usuário ou grupo do AD
description: Saiba como executar um serviço como um usuário do Active Directory em um cluster autônomo do Service Fabric Windows.
ms.topic: conceptual
ms.date: 03/29/2018
ms.openlocfilehash: d4a7afc2ddb0f39014a7cf0fd006d7fe23673a95
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91840720"
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Executar um serviço como um grupo ou usuário do Active Directory
Em um cluster autônomo do Windows Server, é possível executar um serviço como um grupo ou usuário do Active Directory usando uma política RunAs.  Por padrão, os aplicativos de Service Fabric são executados na conta sob a qual o processo Fabric.exe está sendo executado. Executar os aplicativos em contas diferentes, mesmo em um ambiente hospedado compartilhado, torna-os mais protegidos uns dos outros. Observe que esse é o Active Directory local em seu domínio e não é com o Azure Active Directory (Azure AD).  É possível executar um serviço como uma [gMSA (Conta de Serviço Gerenciado) de grupo](service-fabric-run-service-as-gmsa.md).

Ao usar um usuário de domínio ou grupo, você pode acessar outros recursos do domínio (por exemplo, compartilhamentos de arquivos) que receberam permissões.

O exemplo a seguir mostra um usuário do Active Directory denominado *TestUser* com a senha de domínio criptografada usando um certificado chamado *MyCert*. É possível usar o comando `Invoke-ServiceFabricEncryptText` do Powershell para criar o texto cifrado secreto. Consulte [Gerenciamento de segredos em aplicativos do Service Fabric](service-fabric-application-secret-management.md) para obter detalhes.

É necessário implantar a chave privada do certificado para descriptografar a senha no computador local usando um método fora de banda (no Azure, isso ocorre por meio do Azure Resource Manager). Em seguida, quando o Service Fabric implanta o pacote de serviço no computador, ele é capaz de descriptografar o segredo e (juntamente com o nome de usuário) autenticar com o Active Directory para execução com essas credenciais.

```xml
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
```

> [!NOTE] 
> Se você aplicar uma política RunAs a um serviço e o manifesto do serviço declarar recursos de ponto de extremidade com o protocolo HTTP, você também deverá especificar um **SecurityAccessPolicy**.  Para obter mais informações, consulte [Atribuir uma política de acesso de segurança a pontos de extremidade HTTP e HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Como uma próxima etapa, leia os seguintes artigos:
* [Entenda o modelo de aplicativo](service-fabric-application-model.md)
* [Especificar recursos em um manifesto do serviço](service-fabric-service-manifest-resources.md)
* [Implantar um aplicativo](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
