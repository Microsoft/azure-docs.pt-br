---
title: "Saiba mais sobre as políticas de segurança dos microsserviços do Azure | Microsoft Docs"
description: "Uma visão geral de como executar um aplicativo Service Fabric em contas de segurança do sistema e locais, incluindo o SetupEntryPoint no qual o aplicativo precisa executar alguma ação privilegiada antes de iniciar"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: mfussell
ms.openlocfilehash: aae828489b708a5b538df1d63c12be23d0423da7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-security-policies-for-your-application"></a>Configurar políticas de segurança para seu aplicativo
Usando o Azure Service Fabric, é possível proteger aplicativos em execução no cluster em contas de usuário diferentes. O Service Fabric também protege os recursos usados pelos aplicativos no momento da implantação nas contas de usuário, por exemplo, arquivos, diretórios e certificados. Isso torna os aplicativos em execução, mesmo em um ambiente hospedado compartilhado, mais protegidos uns dos outros.

Por padrão, os aplicativos de Service Fabric são executados na conta sob a qual o processo Fabric.exe está sendo executado. O Service Fabric também fornece a capacidade de executar aplicativos em uma conta de usuário local ou em uma conta de sistema local, especificada no manifesto do aplicativo. Os tipos de conta do sistema local com suporte são **LocalUser**, **NetworkService**, **LocalService** e **LocalSystem**.

 Durante a execução do Service Fabric no Windows Server em seu datacenter usando o instalador autônomo, é possível usar contas de domínio do Active Directory, incluindo contas de serviço gerenciado de grupo.

É possível definir e criar grupos de usuários para que um ou mais usuários possam ser adicionados a cada grupo para serem gerenciados em conjunto. Isso será útil quando houver vários usuários para pontos de entrada de serviço diferentes e eles precisarem de privilégios comuns disponíveis no nível do grupo.

## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Configurar a política para um ponto de entrada de instalação do serviço
Conforme descrito no [modelo de aplicativo](service-fabric-application-model.md) , o ponto de entrada de instalação, **SetupEntryPoint**, é um ponto de entrada privilegiado executado com as mesmas credenciais que o Service Fabric (normalmente, a conta *NetworkService*) antes de qualquer outro ponto de entrada. O executável especificado pelo **EntryPoint** normalmente é o host de serviço de execução longa. Portanto, ter um ponto de entrada de instalação separado evita a necessidade de executar o executável do host de serviço com altos privilégios por longos períodos de tempo. O executável especificado pelo **EntryPoint** é executado depois que o **SetupEntryPoint** é encerrado com êxito. O processo resultante é monitorado e reiniciado, começando novamente com **SetupEntryPoint**, caso ele termine ou falhe.

O item a seguir é um exemplo simples de manifesto do serviço mostrando o SetupEntryPoint e o EntryPoint principal para o serviço.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
```

### <a name="configure-the-policy-by-using-a-local-account"></a>Configurar a política usando uma conta local
Após ter configurado do serviço para ter um SetupEntryPoint, é possível alterar as permissões de segurança sob as quais ele é executado no manifesto do aplicativo. O item a seguir mostra como configurar o serviço para ser executado com privilégios de conta de administrador de usuário.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupAdminUser">
            <MemberOf>
               <SystemGroup Name="Administrators" />
            </MemberOf>
         </User>
      </Users>
   </Principals>
</ApplicationManifest>
```

Primeiro, crie uma seção **Principals** com um nome de usuário SetupAdminUser. Isso indica que o usuário é membro do grupo do sistema Administradores.

Em seguida, na seção **ServiceManifestImport**, configure uma política para aplicar essa entidade de segurança ao **SetupEntryPoint**. Isso informa ao Service Fabric que quando o arquivo **MySetup.bat** for executado ele deve ser `RunAs` com privilégios de Administrador. Considerando que você *não* aplicou uma política ao ponto de entrada principal, o código em **MyServiceHost.exe** será executado na conta do sistema **NetworkService**. Essa é a conta padrão que todos os pontos de entrada de serviço são executados como.

Agora, vamos adicionar o arquivo MySetup.bat ao projeto do Visual Studio para testar os privilégios de Administrador. No Visual Studio, clique com o botão direito do mouse no projeto de serviço e adicione um novo arquivo chamado MySetup.bat.

Em seguida, certifique-se de que o arquivo MySetup.bat é incluído no service pack. Por padrão, não é. Selecione o arquivo, clique com o botão direito do mouse para exibir o menu de contexto e escolha **Propriedades**. Na caixa de diálogo Propriedades, verifique se **Copiar para Diretório de Saída** está definido como **Copiar se mais recente**. Consulte a seguinte captura de tela.

![CopyToOutput do Visual Studio para o arquivo em lotes SetupEntryPoint][image1]

Agora, abra o arquivo MySetup.bat e adicione os comandos a seguir.

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Em seguida, compile e implante a solução em um cluster de desenvolvimento local. Após a inicialização do serviço, conforme mostrado no Gerenciador do Service Fabric, é possível ver que o arquivo MySetup.bat foi bem-sucedido de duas maneiras. Abra um prompt de comando do PowerShell e digite:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Em seguida, anote o nome do nó no qual o serviço foi implantado e iniciado no Service Fabric Explorer, por exemplo, Nó 2. Em seguida, navegue até a pasta de trabalho de instância do aplicativo para localizar o arquivo out.txt que mostra o valor de **TestVariable**. Por exemplo, se o serviço foi implantado no Nó 2, você pode acessar este caminho até o **MyApplicationType**:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Configurar a política usando contas do sistema local
Em geral, é preferível executar o script de inicialização usando uma conta do sistema local em vez de uma conta de administrador. A execução da política RunAs como membro do grupo Administradores geralmente não funciona bem, porque os computadores têm o UAC (Controle de Acesso de Usuário) habilitado por padrão. Em tais casos, **a recomendação é executar o SetupEntryPoint como LocalSystem em vez de um usuário local adicionado ao grupo Administradores**. O exemplo a seguir mostra a definição de SetupEntryPoint para ser executado como LocalSystem:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
```

Para clusters do Linux, para executar um serviço ou o ponto de entrada de instalação como **raiz**, é possível especificar o **AccountType** como **LocalSystem**.

## <a name="start-powershell-commands-from-a-setup-entry-point"></a>Iniciar comandos do PowerShell em um ponto de entrada de instalação
Para executar o PowerShell do ponto **SetupEntryPoint**, execute **PowerShell.exe** em um arquivo em lotes que aponte para um arquivo do PowerShell. Primeiro, adicione um arquivo do PowerShell ao projeto de serviço, por exemplo, **MySetup.ps1**. Não deixe de definir a propriedade *Copiar se for mais recente* para que esse arquivo também seja incluído no pacote de serviço. O exemplo a seguir mostra um exemplo de arquivo em lotes que inicia um arquivo do PowerShell chamado MySetup.ps1, que define uma variável de ambiente do sistema chamada **TestVariable**.

MySetup.bat para iniciar o arquivo do PowerShell:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

No arquivo do PowerShell, adicione o seguinte para definir uma variável de ambiente do sistema:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Por padrão, quando o arquivo em lotes é executado, ele examina a pasta de aplicativo chamada **trabalho** em busca de arquivos. Nesse caso, quando MySetup.bat é executado, queremos que o arquivo MySetup.ps1 seja encontrado na mesma pasta, que é a pasta do **pacote de códigos** do aplicativo. Para alterar essa pasta, defina a pasta de trabalho:
> 
> 

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
```

## <a name="use-console-redirection-for-local-debugging"></a>Use o redirecionamento de console para depuração local
De vez em quando, é útil ver a saída do console da execução de um script para fins de depuração. Para fazer isso, você pode definir uma política de redirecionamento de console que grava a saída em um arquivo. A saída do arquivo é gravada na pasta de aplicativos chamada **log** no nó no qual o aplicativo é implantado e executado. (Consulte onde encontrá-lo no exemplo anterior.)

> [!WARNING]
> Nunca use a política de redirecionamento de console em um aplicativo implantado na produção, pois isso pode afetar o failover do aplicativo. *Só* use isso para fins de depuração e de desenvolvimento locais.  
> 
> 

O exemplo a seguir mostra a configuração de redirecionamento do console com um valor FileRetentionCount:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Se você alterar o arquivo MySetup.ps1 agora para gravar um comando **Echo** , ele gravará o arquivo de saída para fins de depuração:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

**Depois de depurar seu script, remova imediatamente a política de redirecionamento de console**.

## <a name="configure-a-policy-for-service-code-packages"></a>Configurar uma política para pacotes de código de serviço
Nas etapas anteriores, você viu como aplicar uma política RunAs a um SetupEntryPoint. Agora, vamos analisar com mais detalhes como criar entidades diferentes que possam ser aplicadas como políticas de serviço.

### <a name="create-local-user-groups"></a>Criar grupos de usuários locais
É possível definir e criar grupos de usuários que permitem que um ou mais usuários sejam adicionados a um grupo. Isso será útil se houverem vários usuários para pontos de entrada de serviço diferentes e eles precisarem de privilégios comuns disponíveis no nível do grupo. O exemplo a seguir mostra um grupo local chamado **LocalAdminGroup** com privilégios de administrador. Dois usuários, Customer1 e Customer2, tornam-se membros desse grupo local.

```xml
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
```

### <a name="create-local-users"></a>Criar usuários locais
É possível criar um usuário local que pode ser usado para ajudar a proteger um serviço dentro do aplicativo. Quando um tipo de conta **LocalUser** é especificado na seção Entidades do manifesto do aplicativo, o Service Fabric cria contas de usuário local em máquinas nas quais o aplicativo foi implantado. Por padrão, essas contas não têm os mesmos nomes especificados no manifesto do aplicativo (por exemplo, Customer3 no exemplo a seguir). Em vez disso, eles são gerados dinamicamente e têm senhas aleatórias.

```xml
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
```

Se um aplicativo exigir que a conta de usuário e senha sejam iguais em todos os computadores (por exemplo, para habilitar a autenticação NTLM), o manifesto do cluster deve definir NTLMAuthenticationEnabled como true. O manifesto do cluster também deve especificar um NTLMAuthenticationPasswordSecret que será usado para gerar a mesma senha em todos os computadores.

```xml
<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
```

### <a name="assign-policies-to-the-service-code-packages"></a>Atribuir políticas aos pacotes de códigos do serviço
A seção **RunAsPolicy** para uma **ServiceManifestImport** especifica a conta da seção de entidades que deve ser usada para executar um pacote de códigos. Ela também associa os pacotes de código do manifesto do serviço com contas de usuário na seção entidades. Você pode especificar isso para os pontos de entrada de configuração ou principal, ou especificar `All` para aplicar a ambos. O exemplo a seguir mostra a aplicação de políticas diferentes:

```xml
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
```

Se **EntryPointType** não for especificado, o padrão será definido como EntryPointType ="Main". A especificação de um **SetupEntryPoint** é especialmente útil quando você deseja executar determinadas operações de instalação de privilégio elevado em uma conta do sistema. O código de serviço real pode ser executado em uma conta de baixo privilégio.

### <a name="apply-a-default-policy-to-all-service-code-packages"></a>Aplicar uma política padrão a todos os pacotes de códigos de serviço
Use a seção **DefaultRunAsPolicy** para especificar uma conta de usuário padrão para todos os pacotes de códigos que não tenham uma **RunAsPolicy** específica definida. Quando a maioria dos pacotes de códigos especificados no manifesto do serviço usado por um aplicativo precisar ser executada com o mesmo usuário, o aplicativo poderá definir apenas uma política RunAs padrão nessa conta de usuário. O exemplo a seguir especifica que quando um pacote de códigos não tiver uma **RunAsPolicy** especificada, ele deverá ser executado em uma **MyDefaultAccount** especificada na seção Entidades.

```xml
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
```
### <a name="use-an-active-directory-domain-group-or-user"></a>Usar um usuário ou um grupo de domínio do Active Directory
Para uma instância do Service Fabric instalada no Windows Server usando o instalador autônomo, é possível executar o serviço com as credenciais para uma conta de grupo ou de usuário do Active Directory. Esse é o Active Directory local em seu domínio e não é com o Azure Active Directory (Azure AD). Ao usar um usuário de domínio ou grupo, você pode acessar outros recursos do domínio (por exemplo, compartilhamentos de arquivos) que receberam permissões.

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
### <a name="use-a-group-managed-service-account"></a>Use uma Conta de Serviço Gerenciado de Grupo.
Para uma instância do Service Fabric instalada no Windows Server usando o instalador autônomo, é possível executar o serviço como uma Conta de Serviço Gerenciado (gMSA). Observe que esse é o Active Directory local em seu domínio e não é com o Azure AD (Active Directory). Ao usar uma gMSA, a senha ou senha criptografada não será armazenada no `Application Manifest`.

O exemplo a seguir mostra como criar uma conta gMSA chamada *svc-Test$*; como implantar essa conta de serviço gerenciado nos nós de cluster; e como configurar a entidade de segurança do usuário.

##### <a name="prerequisites"></a>Pré-requisitos.
- O domínio precisa de uma chave raiz KDS.
- O domínio deve ser um Windows Server 2012 ou nível funcional posterior.

##### <a name="example"></a>Exemplo
1. Peça para um administrador de domínio do Active Directory criar uma conta de serviço gerenciado de grupo usando o cmdlet `New-ADServiceAccount` e verifique se o `PrincipalsAllowedToRetrieveManagedPassword` inclui todos os nós de cluster de malha do serviço. Observe que `AccountName`, `DnsHostName` e `ServicePrincipalName` devem ser exclusivos.
```
New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
```
2. Em cada um dos nós de cluster do Service Fabric (por exemplo, `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`), instale e teste a gMSA.
```
Add-WindowsFeature RSAT-AD-PowerShell
Install-AdServiceAccount svc-Test$
Test-AdServiceAccount svc-Test$
```
3. Configure a entidade de usuário e a RunAsPolicy para fazer referência ao usuário.
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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

## <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Atribuir uma política de acesso de segurança a pontos de extremidade HTTP e HTTPS
Se você aplicar uma política RunAs a um serviço e o manifesto do serviço declarar recursos de ponto de extremidade com o protocolo HTTP, será necessário especificar uma **SecurityAccessPolicy** para assegurar que as portas alocadas a esses pontos de extremidade sejam corretamente listadas no controle de acesso para a conta de usuário RunAs na qual o serviço é executado. Caso contrário, o **http.sys** não terá acesso ao serviço e você receberá uma falha com chamadas do cliente. O exemplo a seguir aplica a conta Customer1 a um ponto de extremidade chamado **EndpointName**, concedendo a ele direitos de acesso completo.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

Para o ponto de extremidade HTTPS, você também precisa indicar o nome do certificado para retornar para o cliente. Você pode fazer isso usando **EndpointBindingPolicy**, com o certificado definido em uma seção de certificados no manifesto do aplicativo.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
```
## <a name="upgrading-multiple-applications-with-https-endpoints"></a>Atualizando vários aplicativos com pontos de extremidade https
Você precisa ter cuidado para não usar a **mesma porta** para instâncias diferentes do mesmo aplicativo quando estiver usando http**s**. O motivo é que o Service Fabric não será capaz de fazer upgrade do certificado para uma das instâncias do aplicativo. Por exemplo, se o aplicativo 1 ou o aplicativo 2 deseja fazer upgrade do seu cert 1 cert 2. Quando o upgrade é feito, o Service Fabric pode apagar o registro cert 1 com o http.sys, embora o outro aplicativo ainda o está usando. Para evitar isso, o Service Fabric detecta se já há em outra instância do aplicativo registrada na porta com o certificado (devido ao http. sys) e faz a operação falhar.

Portanto, o Service Fabric não suporta a atualização de dois serviços diferentes usando **a mesma porta** em instâncias de aplicativo diferentes. Em outras palavras, você não pode usar o mesmo certificado em serviços diferentes na mesma porta. Se você precisa ter um certificado compartilhado na mesma porta, precisa garantir que os serviços sejam colocados em computadores diferentes com restrições de posicionamento. Ou, considere o uso de portas dinâmicas do Service Fabric, se possível, para cada serviço em cada instância do aplicativo. 

Se você vir um upgrade falhar com https, um aviso de erro dizendo "A API de servidor HTTP do Windows não suporta vários certificados para aplicativos que compartilham uma porta".

## <a name="a-complete-application-manifest-example"></a>Um exemplo completo de manifesto do aplicativo
O manifesto do aplicativo a seguir mostra várias configurações diferentes:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application3Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Stateless1_InstanceCount" DefaultValue="-1" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
         <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup" />
        <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
         <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
        <!--EndpointBindingPolicy is needed the EndpointName is secured with https -->
        <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
     </Policies>
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Stateless1">
         <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
   <Principals>
      <Groups>
         <Group Name="LocalAdminGroup">
            <Membership>
               <SystemGroup Name="Administrators" />
            </Membership>
         </Group>
      </Groups>
      <Users>
         <User Name="LocalAdmin">
            <MemberOf>
               <Group NameRef="LocalAdminGroup" />
            </MemberOf>
         </User>
         <!--Customer1 below create a local account that this service runs under -->
         <User Name="Customer1" />
         <User Name="MyDefaultAccount" AccountType="NetworkService" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="LocalAdmin" />
   </Policies>
   <Certificates>
     <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas
* [Entenda o modelo de aplicativo](service-fabric-application-model.md)
* [Especificar recursos em um manifesto do serviço](service-fabric-service-manifest-resources.md)
* [Implantar um aplicativo](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
