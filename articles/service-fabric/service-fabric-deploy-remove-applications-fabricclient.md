---
title: Implantação de Service Fabric do Azure com FabricClient
description: Use as APIs de FabricClient para implantar e remover aplicativos no Service Fabric.
ms.topic: conceptual
ms.date: 01/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 343a37c983b1d64a4b1986913d9d6fd648a113fe
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785542"
---
# <a name="deploy-and-remove-applications-using-fabricclient"></a>Implantar e remover aplicativos usando FabricClient
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [CLI do Service Fabric](service-fabric-application-lifecycle-sfctl.md)
> * [APIs do FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

Assim que um [tipo de aplicativo for empacotado][10], ele está pronto para implantação em um cluster do Azure Service Fabric. A implantação envolve as três etapas a seguir:

1. Carregar o pacote de aplicativos no repositório de imagens
2. Registrar o tipo de aplicativo
3. Remover o pacote de aplicativos do repositório de imagens
4. Criar a instância do aplicativo

Depois de implantar um aplicativo e executar uma instância no cluster, você pode excluir a instância do aplicativo e seu tipo de aplicativo. Remova completamente um aplicativo do cluster seguindo estas etapas:

1. Remover (ou excluir) a execução da instância do aplicativo
2. Cancelar o registro do tipo de aplicativo se você não precisar mais dele

Se você usar o Visual Studio para implantar e depurar aplicativos no cluster de desenvolvimento local, todas as etapas anteriores serão tratadas automaticamente por meio de um script do PowerShell.  Esse script é encontrado na pasta *scripts* do projeto de aplicativo. Este artigo fornece informações sobre o que o script está fazendo para que você possa fazer as mesmas operações fora do Visual Studio. 
 
## <a name="connect-to-the-cluster"></a>Conectar-se ao cluster
Conecte-se ao cluster criando uma instância [FabricClient](/dotnet/api/system.fabric.fabricclient) antes de executar qualquer um dos exemplos de código neste artigo. Para obter exemplos de como se conectar a um cluster de desenvolvimento local ou a um cluster remoto ou protegido usando o Azure Active Directory, certificados X509 ou o Microsoft Active Directory, consulte [Conectar a um cluster seguro](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis). Para se conectar ao cluster de desenvolvimento local, execute o seguinte exemplo:

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## <a name="upload-the-application-package"></a>Carregar o pacote de aplicativos
Suponha que você crie e empacote um aplicativo chamado *MyApplication* no Visual Studio. Por padrão, o nome do tipo de aplicativo listado no ApplicationManifest.xml é "MyApplicationType".  O pacote de aplicativos, que contém o manifesto do aplicativo, os manifestos do serviço e os pacotes de código/configuração/dados necessários, está localizado em *C:\Users \& lt; username &gt; \Documents\Visual Studio 2019 \ Projects\MyApplication\MyApplication\pkg\Debug*.

O carregamento do pacote de aplicativos coloca-o em um local acessível pelos componentes internos do Service Fabric. O Service Fabric verifica o pacote de aplicativos durante o registro do pacote de aplicativos. No entanto, se você quiser verificar o pacote de aplicativos localmente (ou seja, antes de carregar), use o cmdlet [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) .

A API [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) carrega o pacote de aplicativos para o repositório de imagens do cluster. 

Se o pacote de aplicativos for grande e/ou tiver vários arquivos, será possível [compactá-lo](service-fabric-package-apps.md#compress-a-package) e copiá-lo para o repositório de imagens usando o PowerShell. A compactação reduz o tamanho e o número de arquivos.

Confira [Noções básicas sobre a configuração ImageStoreConnectionString](service-fabric-image-store-connection-string.md) para obter informações suplementares sobre o repositório de imagens e a cadeia de conexão de armazenamento de imagens.

## <a name="register-the-application-package"></a>Registrar o pacote de aplicativos
O tipo e a versão do aplicativo declarados no manifesto do aplicativo tornam-se disponíveis para uso quando o pacote de aplicativos é registrado. O sistema lê o pacote carregado na etapa anterior, verifica o pacote, processa o conteúdo do pacote e copia o pacote processado em um local interno do sistema.  

A API [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) registra o tipo de aplicativo no cluster e o disponibiliza para implantação.

A API [GetApplicationTypeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationtypelistasync) fornece informações sobre todos os tipos de aplicativo registrados com êxito. Você pode usar essa API para determinar quando o registro é feito.

## <a name="remove-an-application-package-from-the-image-store"></a>Remover um pacote de aplicativos do repositório de imagens
É recomendável que você remova o pacote de aplicativos depois que o aplicativo for registrado com êxito.  Excluir pacotes de aplicativos do repositório de imagens libera recursos do sistema.  Manter pacotes de aplicativos não utilizados consome o armazenamento em disco e leva a problemas de desempenho do aplicativo. Exclua o pacote de aplicativos do repositório de imagens usando a API [RemoveApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage).

## <a name="create-an-application-instance"></a>Criar uma instância do aplicativo
É possível criar uma instância de um aplicativo de qualquer tipo de aplicativo registrada com êxito usando a API [CreateApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync). O nome de cada aplicativo deve começar com o esquema *"fabric:"* e ser exclusivo para cada instância do aplicativo (dentro de um cluster). Quaisquer serviços padrão definidos no manifesto do aplicativo do tipo de aplicativo de destino também são criados.

Várias instâncias do aplicativo podem ser criadas para qualquer determinada versão de um tipo de aplicativo registrado. Cada instância do aplicativo é executada isoladamente, com seu próprio diretório de trabalho e conjunto de processos.

Para ver quais aplicativos e serviços nomeados estão em execução no cluster, execute as APIs [GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync) e [GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync).

## <a name="create-a-service-instance"></a>Crie uma instância de serviço
É possível criar uma instância de um serviço com base em um tipo de serviço de que usa a API [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync).  Se o serviço for declarado como um serviço padrão no manifesto do aplicativo, a instância desse serviço será criada com a criação da instância do aplicativo.  Chamar a API [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) para um serviço que já está instanciado retornará uma exceção do tipo fabricexception. A exceção conterá um código de erro com um valor de FabricErrorCode. ServiceAlreadyExists.

## <a name="remove-a-service-instance"></a>Remover uma instância de serviço
Quando uma instância de serviço não for mais necessária, será possível removê-la da instância do aplicativo em execução chamando a API [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync).  

> [!WARNING]
> Essa operação não pode ser revertida e o estado do serviço não pode ser recuperado.

## <a name="remove-an-application-instance"></a>Remover uma instância do aplicativo
Quando a instância de um aplicativo não for mais necessária, será possível removê-la permanentemente pelo nome que usa a API [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync). [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) também remove automaticamente todos os serviços que pertencem ao aplicativo, removendo permanentemente todos os estados de serviço.

> [!WARNING]
> Essa operação não pode ser revertida e o estado do aplicativo não pode ser recuperado.

## <a name="unregister-an-application-type"></a>Cancelar o registro de um tipo de aplicativo
Quando uma versão específica de um tipo de aplicativo não for mais necessária, você deve cancelar o registro dessa versão específica do tipo de aplicativo usando a API [Unregister-ServiceFabricApplicationType](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync). O cancelamento de registro de versões não utilizadas de tipos de aplicativo libera espaço de armazenamento usado pelo repositório de imagens. Uma versão de um tipo de aplicativo pode ter seu registro cancelado, desde que nenhum aplicativo seja instanciado em relação a essa versão do tipo de aplicativo. Além disso, o tipo de aplicativo pode não ter atualizações de aplicativo pendentes que fazem referência a essa versão do tipo de aplicativo.

## <a name="troubleshooting"></a>Solução de problemas
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage solicita um ImageStoreConnectionString
O ambiente do SDK da Malha do Serviço já deve ter os padrões corretos configurados. Mas, se necessário, o ImageStoreConnectionString para todos os comandos deve corresponder ao valor que o cluster de Malha do Serviço está usando. Você pode encontrar ImageStoreConnectionString no manifesto do cluster, recuperado usando os comandos [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest) e Get-ImageStoreConnectionStringFromClusterManifest:

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

O cmdlet **Get-ImageStoreConnectionStringFromClusterManifest** , que faz parte do módulo do PowerShell do SDK do Service Fabric, é usado para obter a cadeia de conexão do repositório de imagens.  Para importar o módulo do SDK, execute:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```


ImageStoreConnectionString é encontrado no manifesto do cluster:

```xml
<ClusterManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Confira [Noções básicas sobre a configuração ImageStoreConnectionString](service-fabric-image-store-connection-string.md) para obter informações suplementares sobre o repositório de imagens e a cadeia de conexão de armazenamento de imagens.

### <a name="deploy-large-application-package"></a>Implantar um pacote de aplicativos grande
Problema: a API [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) atinge o tempo limite para um pacote de aplicativos grande (na ordem de GB).
Experimente:
- Especificar um tempo limite maior para o método [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) com o parâmetro `timeout`. Por padrão, o tempo limite é de 30 minutos.
- Verifique a conexão de rede entre o computador de origem e o cluster. Se a conexão estiver lenta, considere usar um computador com uma conexão de rede melhor.
Se o computador cliente estiver em uma região diferente do cluster, considere usar um computador cliente em uma região mais próxima ou na mesma que o cluster.
- Verifique se você está sofrendo limitação externa. Por exemplo, quando o repositório de imagens é configurado para usar o Armazenamento do Azure, o upload pode ser limitado.

Problema: o pacote de carregamento foi concluído com êxito, mas a API [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) expira. Tente
- [Compactar o pacote](service-fabric-package-apps.md#compress-a-package) antes de copiar para o repositório de imagens.
A compactação reduz o tamanho e o número de arquivos, o que por sua vez reduz a quantidade de tráfego e trabalho que o Service Fabric deve executar. A operação de upload pode ser mais lenta (especialmente se você incluir o tempo de compactação), mas registrar e cancelar o registro do tipo de aplicativo é mais rápido.
- Especifique um tempo limite maior para a API [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) com o parâmetro `timeout`.

### <a name="deploy-application-package-with-many-files"></a>Implantar pacote de aplicativos com muitos arquivos
Problema: [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) atinge o tempo limite para um pacote de aplicativos com muitos arquivos (na ordem de milhares).
Experimente:
- [Compactar o pacote](service-fabric-package-apps.md#compress-a-package) antes de copiar para o repositório de imagens. A compactação reduz o número de arquivos.
- Especifique um tempo limite maior para [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) com o parâmetro `timeout`.

## <a name="code-example"></a>Exemplo de código
O exemplo a seguir copia um pacote de aplicativos para o repositório de imagens e provisiona o tipo de aplicativo. Em seguida, o exemplo cria uma instância do aplicativo e cria uma instância de serviço. Por fim, o exemplo remove a instância do aplicativo, cancela o provisionamento do tipo de aplicativo e exclui o pacote de aplicativos do repositório de imagens.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Reflection;
using System.Threading.Tasks;

using System.Fabric;
using System.Fabric.Description;
using System.Threading;

namespace ServiceFabricAppLifecycle
{
class Program
{
static void Main(string[] args)
{

    string clusterConnection = "localhost:19000";
    string appName = "fabric:/MyApplication";
    string appType = "MyApplicationType";
    string appVersion = "1.0.0";
    string serviceName = "fabric:/MyApplication/Stateless1";
    string imageStoreConnectionString = "file:C:\\SfDevCluster\\Data\\ImageStoreShare";
    string packagePathInImageStore = "MyApplication";
    string packagePath = "C:\\Users\\username\\Documents\\Visual Studio 2019\\Projects\\MyApplication\\MyApplication\\pkg\\Debug";
    string serviceType = "Stateless1Type";

    // Connect to the cluster.
    FabricClient fabricClient = new FabricClient(clusterConnection);

    // Copy the application package to a location in the image store
    try
    {
        fabricClient.ApplicationManager.CopyApplicationPackage(imageStoreConnectionString, packagePath, packagePathInImageStore);
        Console.WriteLine("Application package copied to {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package copy to Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Provision the application.  "MyApplicationV1" is the folder in the image store where the application package is located. 
    // The application type with name "MyApplicationType" and version "1.0.0" (both are found in the application manifest) 
    // is now registered in the cluster.            
    try
    {
        fabricClient.ApplicationManager.ProvisionApplicationAsync(packagePathInImageStore).Wait();

        Console.WriteLine("Provisioned application type {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Provision Application Type failed:");

        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete the application package from a location in the image store.
    try
    {
        fabricClient.ApplicationManager.RemoveApplicationPackage(imageStoreConnectionString, packagePathInImageStore);
        Console.WriteLine("Application package removed from {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package removal from Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    //  Create the application instance.
    try
    {
        ApplicationDescription appDesc = new ApplicationDescription(new Uri(appName), appType, appVersion);
        fabricClient.ApplicationManager.CreateApplicationAsync(appDesc).Wait();
        Console.WriteLine("Created application instance of type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Create the stateless service description.  For stateful services, use a StatefulServiceDescription object.
    StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
    serviceDescription.ApplicationName = new Uri(appName);
    serviceDescription.InstanceCount = 1;
    serviceDescription.PartitionSchemeDescription = new SingletonPartitionSchemeDescription();
    serviceDescription.ServiceName = new Uri(serviceName);
    serviceDescription.ServiceTypeName = serviceType;

    // Create the service instance.  If the service is declared as a default service in the ApplicationManifest.xml,
    // the service instance is already running and this call will fail.
    try
    {
        fabricClient.ServiceManager.CreateServiceAsync(serviceDescription).Wait();
        Console.WriteLine("Created service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete a service instance.
    try
    {
        DeleteServiceDescription deleteServiceDescription = new DeleteServiceDescription(new Uri(serviceName));

        fabricClient.ServiceManager.DeleteServiceAsync(deleteServiceDescription);
        Console.WriteLine("Deleted service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete an application instance from the application type.
    try
    {
        DeleteApplicationDescription deleteApplicationDescription = new DeleteApplicationDescription(new Uri(appName));

        fabricClient.ApplicationManager.DeleteApplicationAsync(deleteApplicationDescription).Wait();
        Console.WriteLine("Deleted application instance {0}", appName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Un-provision the application type.
    try
    {
        fabricClient.ApplicationManager.UnprovisionApplicationAsync(appType, appVersion).Wait();
        Console.WriteLine("Un-provisioned application type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Un-provision application type failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    Console.WriteLine("Hit enter...");
    Console.Read();
}        
}
}

```

## <a name="next-steps"></a>Próximas etapas
[Atualização de aplicativos do Service Fabric](service-fabric-application-upgrade.md)

[Introdução à integridade da Malha do Serviço](service-fabric-health-introduction.md)

[Diagnosticar e solucionar problemas de um serviço da Malha do Serviço](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelar um aplicativo na Malha do Serviço](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
