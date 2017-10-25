---
title: "Segurança de contêiner do Azure Service Fabric | Microsoft Docs"
description: "Saiba como proteger serviços de contêiner."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 3e41e293cc5340c0e32cf2cc6ef7ab7534330884
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="container-security"></a>Segurança do contêiner

O Service Fabric fornece um mecanismo para serviços dentro de um contêiner para acessar um certificado que está instalado em nós em um cluster do Windows ou Linux (versão 5.7 ou superior). Além disso, o Service Fabric também dá suporte a gMSA (Contas de Serviço Gerenciado do grupo) para contêineres do Windows. 

## <a name="certificate-management-for-containers"></a>Gerenciamento de certificados para contêineres

Você pode proteger seus serviços de contêiner especificando um certificado. O certificado deve ser instalado na LocalMachine em todos os nós do cluster. As informações do certificado são fornecidas no manifesto do aplicativo sob a marca `ContainerHostPolicies`, como mostra o trecho de código a seguir:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

Para clusters do Windows, ao iniciar o aplicativo, o tempo de execução lê os certificados e gera um arquivo PFX e uma senha para cada certificado. Este arquivo PFX e a senha podem ser acessados dentro do contêiner usando as seguintes variáveis de ambiente: 

* **Certificate_ServicePackageName_CodePackageName_CertName_PFX**
* **Certificate_ServicePackageName_CodePackageName_CertName_Password**

Para clusters do Linux, os certificados (PEM) simplesmente são copiados do repositório especificado pelo X509StoreName para o contêiner. As variáveis de ambiente correspondentes no Linux são:

* **Certificate_ServicePackageName_CodePackageName_CertName_PEM**
* **Certificate_ServicePackageName_CodePackageName_CertName_PrivateKey**

Como alternativa, se você já tiver os certificados no formato necessário e desejar simplesmente acessá-lo dentro do contêiner, será possível criar um pacote de dados dentro do seu pacote do aplicativo e especificar o seguinte dentro do seu manifesto do aplicativo:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
   <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

O serviço ou o processo do contêiner é responsável por importar os arquivos de certificado para o contêiner. Para importar o certificado, é possível usar scripts `setupentrypoint.sh` ou executar o código dentro do processo do contêiner. Segue o código de exemplo em C# para importar o arquivo PFX:

```c#
    string certificateFilePath = Environment.GetEnvironmentVariable("Certificate_MyServicePackage_NodeContainerService.Code_MyCert1_PFX");
    string passwordFilePath = Environment.GetEnvironmentVariable("Certificate_MyServicePackage_NodeContainerService.Code_MyCert1_Password");
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
    string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
    password = password.Replace("\0", string.Empty);
    X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
    store.Open(OpenFlags.ReadWrite);
    store.Add(cert);
    store.Close();
```
Esse certificado PFX pode ser usado para autenticar o aplicativo ou o serviço ou proteger a comunicação com outros serviços. Por padrão, os arquivos são colocados na lista de controle de acesso apenas para SYSTEM. É possível colocá-lo na ACL para outras contas, conforme exigido pelo serviço.


## <a name="set-up-gmsa-for-windows-containers"></a>Configurar o gMSA para contêineres do Windows

Para configurar o gMSA (Contas de Serviço Gerenciado de grupo), um arquivo de especificação de credencial (`credspec`) é colocado em todos os nós no cluster. O arquivo pode ser copiado em todos os nós usando uma extensão de VM.  O arquivo `credspec` deve conter as informações da conta gMSA. Para saber mais sobre o arquivo `credspec`, veja [Contas de serviço](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/live/windows-server-container-tools/ServiceAccounts). A especificação de credenciais e a marca `Hostname` são especificadas no manifesto do aplicativo. A marca `Hostname` deve corresponder ao nome de conta de gMSA em que o contêiner é executado.  A marca `Hostname` permite que o contêiner para se autenticar em outros serviços no domínio usando a autenticação Kerberos.  Um exemplo para especificar o `Hostname` e o `credspec` no manifesto do aplicativo é mostrado no trecho a seguir:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
## <a name="next-steps"></a>Próximas etapas

* [Implantar um contêiner do Windows no Service Fabric no Windows Server 2016](service-fabric-get-started-containers.md)
* [Implantar um contêiner do Docker no Service Fabric no Linux](service-fabric-get-started-containers-linux.md)
