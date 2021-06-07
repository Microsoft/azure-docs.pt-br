---
title: Adicionar um ponto de extremidade HTTPS usando o Kestrel
description: Neste tutorial, você aprenderá a adicionar um ponto de extremidade HTTPS a um serviço Web de front-end do ASP.NET Core usando Kestrel e a implantar o aplicativo em um cluster.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: c675f8ece8369bcfc0055343221ac82aea59dec1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91326228"
---
# <a name="tutorial-add-an-https-endpoint-to-an-aspnet-core-web-api-front-end-service-using-kestrel"></a>Tutorial: adicionar um ponto de extremidade HTTPS a um serviço de front-end de API Web do ASP.NET Core usando o Kestrel

Este tutorial é a parte três de uma série.  Você aprenderá a habilitar HTTPS em um serviço ASP.NET Core em execução no Service Fabric. Quando você terminar, terá um aplicativo de votação com um front-end Web do ASP.NET Core habilitado para HTTPS escutando na porta 443. Se você não quiser criar manualmente o aplicativo de votação em [Criar um aplicativo do Service Fabric no .NET](service-fabric-tutorial-deploy-app-to-party-cluster.md), [baixe o código-fonte](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) do aplicativo concluído.

Na terceira parte da série, você aprenderá a:

> [!div class="checklist"]
> * Definir um ponto de extremidade HTTPS no serviço
> * Configurar o Kestrel para usar HTTPS
> * Instalar o certificado TLS/SSL nos nós de cluster remoto
> * Conceder ao SERVIÇO DE REDE acesso à chave privada do certificado
> * Abrir a porta 443 no balanceador de carga do Azure
> * Implantar o aplicativo em um cluster remoto

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * [Criar um aplicativo .NET do Service Fabric](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Implantar o aplicativo em um cluster remoto](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * Adicionar um ponto de extremidade HTTPS a um serviço de front-end do ASP.NET Core
> * [Configurar CI/CD usando o Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Configurar monitoramento e diagnóstico para o aplicativo](service-fabric-tutorial-monitoring-aspnet.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se você não tem uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Instale o Visual Studio 2019](https://www.visualstudio.com/) versão 16.5 ou posterior com as cargas de trabalho de **Desenvolvimento do Azure** e **Desenvolvimento para a Web e ASP.NET**.
* [Instalar o SDK do Service Fabric](service-fabric-get-started.md)

## <a name="obtain-a-certificate-or-create-a-self-signed-development-certificate"></a>Obter um certificado ou criar um certificado de desenvolvimento autoassinado

Para aplicativos de produção, use um certificado de uma [CA (autoridade de certificação)](https://wikipedia.org/wiki/Certificate_authority). Para fins de desenvolvimento e teste, você pode criar e usar um certificado autoassinado. O SDK do Service Fabric fornece o script *CertSetup.ps1*, que cria um certificado autoassinado e o importa para o repositório de certificados `Cert:\LocalMachine\My`. Abra um prompt de comando como administrador e execute o seguinte comando para criar um certificado com o a entidade"CN=mytestcert":

```powershell
PS C:\program files\microsoft sdks\service fabric\clustersetup\secure> .\CertSetup.ps1 -Install -CertSubjectName CN=mytestcert
```

Se você já tiver um arquivo PFX de certificado, execute o seguinte para importar o certificado para o repositório de certificados `Cert:\LocalMachine\My`:

```powershell

PS C:\mycertificates> Import-PfxCertificate -FilePath .\mysslcertificate.pfx -CertStoreLocation Cert:\LocalMachine\My -Password (ConvertTo-SecureString "!Passw0rd321" -AsPlainText -Force)


   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

## <a name="define-an-https-endpoint-in-the-service-manifest"></a>Definir um ponto de extremidade HTTPS no manifesto do serviço

Inicie o Visual Studio como um **administrador** e abra a solução de votação. No Gerenciador de Soluções, abra *VotingWeb/PackageRoot/ServiceManifest.xml*. O manifesto do serviço define os pontos de extremidade de serviço.  Localize a seção **Pontos de extremidade** e edite o ponto de extremidade "ServiceEndpoint" existente.  Altere o nome para "EndpointHttps", defina o protocolo como *https*, o tipo como *Entrada* e a porta como *443*.  Salve suas alterações.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="configure-kestrel-to-use-https"></a>Configurar o Kestrel para usar HTTPS

No Gerenciador de Soluções, abra o arquivo *VotingWeb/VotingWeb.cs*.  Configure o Kestrel para usar HTTPS e pesquisar o certificado no repositório `Cert:\LocalMachine\My`. Adicione as seguintes instruções using:

```csharp
using System.Net;
using Microsoft.Extensions.Configuration;
using System.Security.Cryptography.X509Certificates;
```

Atualize o `ServiceInstanceListener` para usar o novo ponto de extremidade *EndpointHttps* e escutar na porta 443. Ao configurar o host da Web para usar o servidor Kestrel, você deve configurar o Kestrel para escutar endereços IPv6 em todos os adaptadores de rede: `opt.Listen(IPAddress.IPv6Any, port, listenOptions => {...}`.

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(FindMatchingCertificateBySubject());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

Adicione também o método a seguir para que o Kestrel possa encontrar o certificado no repositório `Cert:\LocalMachine\My` usando o assunto.  

Substitua "&lt;your_CN_value&gt;" por "mytestcert" se você tiver criado um certificado autoassinado com o comando anterior do PowerShell ou use o valor de CN do seu certificado.
Lembre-se de que, no caso da implantação local em `localhost`, é preferível usar "CN=localhost" para evitar exceções de autenticação.

```csharp
private X509Certificate2 FindMatchingCertificateBySubject(string subjectCommonName)
{
    using (var store = new X509Store(StoreName.My, StoreLocation.LocalMachine))
    {
        store.Open(OpenFlags.OpenExistingOnly | OpenFlags.ReadOnly);
        var certCollection = store.Certificates;
        var matchingCerts = new X509Certificate2Collection();
    
    foreach (var enumeratedCert in certCollection)
    {
      if (StringComparer.OrdinalIgnoreCase.Equals(subjectCommonName, enumeratedCert.GetNameInfo(X509NameType.SimpleName, forIssuer: false))
        && DateTime.Now < enumeratedCert.NotAfter
        && DateTime.Now >= enumeratedCert.NotBefore)
        {
          matchingCerts.Add(enumeratedCert);
        }
    }

        if (matchingCerts.Count == 0)
    {
        throw new Exception($"Could not find a match for a certificate with subject 'CN={subjectCommonName}'.");
    }
        
        return matchingCerts[0];
    }
}


```

## <a name="grant-network-service-access-to-the-certificates-private-key"></a>Conceder ao SERVIÇO DE REDE acesso à chave privada do certificado

Em uma etapa anterior, você importou o certificado para o repositório `Cert:\LocalMachine\My` no computador de desenvolvimento.  Agora, permita acesso explicitamente à conta que executa o serviço (SERVIÇO DE REDE, por padrão) à chave privada do certificado. Você pode executar essa etapa manualmente (usando a ferramenta certlm.msc), mas é melhor executar automaticamente um script do PowerShell [configurando um script de inicialização](service-fabric-run-script-at-service-startup.md) no **SetupEntryPoint** do manifesto do serviço.

>[!NOTE]
> O Service Fabric é compatível com a declaração de certificados de ponto de extremidade por impressão digital ou por nome comum da entidade. Nesse caso, o runtime configurará a associação e a ACL da chave privada do certificado para a identidade em que o serviço está sendo executado. O runtime também monitorará o certificado em busca de alterações/renovações e solicitará a nova ACL da chave privada correspondente.

### <a name="configure-the-service-setup-entry-point"></a>Configurar um ponto de entrada de instalação do serviço

No Gerenciador de Soluções, abra *VotingWeb/PackageRoot/ServiceManifest.xml*.  Na seção **CodePackage**, adicione o nó **SetupEntryPoint** e um nó **ExeHost**.  Em **ExeHost**, defina **Program** como "Setup.bat" e **WorkingFolder** como "CodePackage".  Quando o serviço VotingWeb é iniciado, o script Setup.bat é executado na pasta CodePackage antes do início de VotingWeb.exe.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Setup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>

    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

### <a name="add-the-batch-and-powershell-setup-scripts"></a>Adicionar os scripts de instalação do PowerShell e o batch

Para executar o PowerShell do ponto **SetupEntryPoint**, execute PowerShell.exe em um arquivo em lotes que aponte para um arquivo do PowerShell. Primeiro, adicione o arquivo em lotes ao projeto de serviço.  No Gerenciador de Soluções, clique com o botão direito do mouse em **VotingWeb**, selecione **Adicionar**->**Novo Item** e adicione um novo arquivo denominado "Setup.bat".  Edite o arquivo *Setup.bat* e adicione o comando a seguir:

```cmd
powershell.exe -ExecutionPolicy Bypass -Command ".\SetCertAccess.ps1"
```

Modifique as propriedades do arquivo *Setup.bat* para definir **Copiar para Diretório de Saída** como "Copiar se mais recente".

![Definir propriedades do arquivo][image1]

No Gerenciador de Soluções, clique com o botão direito do mouse em **VotingWeb**, selecione **Adicionar**->**Novo Item** e adicione um novo arquivo denominado "SetCertAccess.ps1”.  Edite o arquivo *SetCertAccess.ps1* e adicione o script a seguir:

```powershell
$subject="mytestcert"
$userGroup="NETWORK SERVICE"

Write-Host "Checking permissions to certificate $subject.." -ForegroundColor DarkCyan

$cert = (gci Cert:\LocalMachine\My\ | where { $_.Subject.Contains($subject) })[-1]

if ($cert -eq $null)
{
    $message="Certificate with subject:"+$subject+" does not exist at Cert:\LocalMachine\My\"
    Write-Host $message -ForegroundColor Red
    exit 1;
}elseif($cert.HasPrivateKey -eq $false){
    $message="Certificate with subject:"+$subject+" does not have a private key"
    Write-Host $message -ForegroundColor Red
    exit 1;
}else
{
    $keyName=$cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName

    $keyPath = "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys\"
    $fullPath=$keyPath+$keyName
    $acl=(Get-Item $fullPath).GetAccessControl('Access')


    $hasPermissionsAlready = ($acl.Access | where {$_.IdentityReference.Value.Contains($userGroup.ToUpperInvariant()) -and $_.FileSystemRights -eq [System.Security.AccessControl.FileSystemRights]::FullControl}).Count -eq 1

    if ($hasPermissionsAlready){
        Write-Host "Account $userGroup already has permissions to certificate '$subject'." -ForegroundColor Green
        return $false;
    } else {
        Write-Host "Need add permissions to '$subject' certificate..." -ForegroundColor DarkYellow

        $permission=$userGroup,"Full","Allow"
        $accessRule=new-object System.Security.AccessControl.FileSystemAccessRule $permission
        $acl.AddAccessRule($accessRule)
        Set-Acl $fullPath $acl

        Write-Output "Permissions were added"

        return $true;
    }
}

```

Modifique as propriedades do arquivo *SetCertAccess.ps1* para definir **Copiar para Diretório de Saída** como "Copiar se mais recente".

### <a name="run-the-setup-script-as-a-local-administrator"></a>Executar o script de instalação como um administrador local

Por padrão, o executável de ponto de entrada de instalação do serviço é executado com as mesmas credenciais do Service Fabric (normalmente a conta NetworkService). O *SetCertAccess.ps1* requer privilégios de administrador. No manifesto do aplicativo, você pode alterar as permissões de segurança para executar o script de inicialização em uma conta de administrador local.

No Gerenciador de Soluções, abra *Voting/ApplicationPackageRoot/ApplicationManifest.xml*. Primeiro, crie uma seção **Entidades** e adicione um novo usuário (por exemplo, "SetupAdminUser". Adicione a conta de suário SetupAdminUser ao grupo do sistema Administradores.
Em seguida, na seção **ServiceManifestImport** do VotingWebPkg, configure uma **RunAsPolicy** para aplicar a entidade SetupAdminUser ao ponto de entrada da instalação. Essa política informa ao Service Fabric que o arquivo Setup.bat é executado como SetupAdminUser (com privilégios de administrador).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
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

## <a name="run-the-application-locally"></a>Executar o aplicativo localmente

No Gerenciador de Soluções, selecione o aplicativo de **Votação** e defina a propriedade **URL do aplicativo** como "https:\//localhost:443".

Salve todos os arquivos e pressione F5 para executar o aplicativo localmente.  Depois que o aplicativo é implantado, um navegador da Web abre como https:\//localhost:443. Se você estiver usando um certificado autoassinado, verá um aviso informando que seu computador não confia na segurança desse site.  Siga até a página da Web.

![Captura de tela do aplicativo Exemplo de Votação do Service Fabric em execução em uma janela do navegador com a URL https://localhost/.][image2]

## <a name="install-certificate-on-cluster-nodes"></a>Instalar certificado em nós de cluster

Antes de implantar o aplicativo no Azure, instale o certificado no repositório `Cert:\LocalMachine\My` de todos os nós de cluster remoto.  Os serviços podem migrar para diferentes nós do cluster.  Quando o serviço Web de front-end é iniciado em um nó de cluster, o script de inicialização pesquisará o certificado e configurará permissões de acesso.

Primeiro, exporte o certificado para um arquivo PFX. Abra o aplicativo certlm.msc e navegue até **Pessoal**>**Certificados**.  Clique com o botão direito do mouse no certificado *mytestcert* e selecione **Todas as Tarefas**>**Exportar**.

![Exportar o certificado][image4]

No assistente de exportação, escolha **Sim, exportar a chave privada** e escolha o formato PFX (Troca de Informações Pessoais).  Exporte o arquivo *C:\Users\sfuser\votingappcert.pfx*.

Em seguida, instale o certificado no cluster remoto usando [estes scripts do PowerShell fornecidos](./scripts/service-fabric-powershell-add-application-certificate.md).

> [!Warning]
> Um certificado autoassinado é suficiente para aplicativos de desenvolvimento e teste. Para aplicativos de produção, use um certificado de uma [CA (autoridade de certificação)](https://wikipedia.org/wiki/Certificate_authority) em vez de um certificado autoassinado.

## <a name="open-port-443-in-the-azure-load-balancer-and-virtual-network"></a>Abrir a porta 443 no Azure Load Balancer e na rede virtual

Abra a porta 443 no balanceador de carga, se já não estiver aberta.

```powershell
$probename = "AppPortProbe6"
$rulename="AppPortLBRule6"
$RGname="voting_RG"
$port=443

# Get the load balancer resource
$resource = Get-AzResource | Where {$_.ResourceGroupName –eq $RGname -and $_.ResourceType -eq "Microsoft.Network/loadBalancers"}
$slb = Get-AzLoadBalancer -Name $resource.Name -ResourceGroupName $RGname

# Add a new probe configuration to the load balancer
$slb | Add-AzLoadBalancerProbeConfig -Name $probename -Protocol Tcp -Port $port -IntervalInSeconds 15 -ProbeCount 2

# Add rule configuration to the load balancer
$probe = Get-AzLoadBalancerProbeConfig -Name $probename -LoadBalancer $slb
$slb | Add-AzLoadBalancerRuleConfig -Name $rulename -BackendAddressPool $slb.BackendAddressPools[0] -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -Probe $probe -Protocol Tcp -FrontendPort $port -BackendPort $port

# Set the goal state for the load balancer
$slb | Set-AzLoadBalancer
```

Faça o mesmo para a rede virtual associada.

```powershell
$rulename="allowAppPort$port"
$nsgname="voting-vnet-security"
$RGname="voting_RG"
$port=443

# Get the NSG resource
$nsg = Get-AzNetworkSecurityGroup -Name $nsgname -ResourceGroupName $RGname

# Add the inbound security rule.
$nsg | Add-AzNetworkSecurityRuleConfig -Name $rulename -Description "Allow app port" -Access Allow `
    -Protocol * -Direction Inbound -Priority 3891 -SourceAddressPrefix "*" -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange $port

# Update the NSG.
$nsg | Set-AzNetworkSecurityGroup
```

## <a name="deploy-the-application-to-azure"></a>Implantar o aplicativo no Azure

Salve todos os arquivos, alterne de Depurar para Lançar e pressione F6 para recompilar.  No Gerenciador de Soluções, clique com o botão direito do mouse em **Votação** e selecione **Publicar**. Selecione o ponto de extremidade de conexão do cluster criado em [Implantar um aplicativo em um cluster](service-fabric-tutorial-deploy-app-to-party-cluster.md) ou selecione outro cluster.  Clique em **Publicar** para publicar o aplicativo no cluster remoto.

Quando o aplicativo é implantado, abra um navegador da Web e navegue até `https://mycluster.region.cloudapp.azure.com:443` (atualize a URL com o ponto de extremidade de conexão para o cluster). Se você estiver usando um certificado autoassinado, verá um aviso informando que seu computador não confia na segurança desse site.  Siga até a página da Web.

![Captura de tela do aplicativo Exemplo de Votação do Service Fabric em execução em uma janela do navegador com a URL https://mycluster.region.cloudapp.azure.com:443.][image3]

## <a name="next-steps"></a>Próximas etapas

Nesta parte do tutorial, você aprendeu a:

> [!div class="checklist"]
> * Definir um ponto de extremidade HTTPS no serviço
> * Configurar o Kestrel para usar HTTPS
> * Instalar o certificado TLS/SSL nos nós de cluster remoto
> * Conceder ao SERVIÇO DE REDE acesso à chave privada do certificado
> * Abrir a porta 443 no balanceador de carga do Azure
> * Implantar o aplicativo em um cluster remoto

Prosseguir para o próximo tutorial:
> [!div class="nextstepaction"]
> [Configurar CI/CD usando o Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)

[image1]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/SetupBatProperties.png
[image2]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/VotingAppLocal.png
[image3]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/VotingAppAzure.png
[image4]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/ExportCert.png
