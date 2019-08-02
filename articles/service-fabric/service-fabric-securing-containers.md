---
title: Importar certificados em um contêiner em execução no Azure Service Fabric | Microsoft Docs
description: Saiba como importar arquivos de certificado para um serviço de contêiner do Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 3a6ea5e2776ae5e016426ba0ddaf288f1476e932
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612778"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Importe um arquivo de certificado para um contêiner em execução no Service Fabric

Você pode proteger seus serviços de contêiner especificando um certificado. O Service Fabric fornece um mecanismo para serviços dentro de um contêiner para acessar um certificado que está instalado em nós em um cluster do Windows ou Linux (versão 5.7 ou superior). O certificado deve ser instalado em um repositório de certificados na LocalMachine em todos os nós do cluster. A chave privada correspondente ao certificado deve estar disponível, acessível e, no Windows, exportável. As informações do certificado são fornecidas no manifesto do aplicativo sob a marca `ContainerHostPolicies`, como mostra o snippet de código a seguir:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

Para clusters do Windows, ao iniciar o aplicativo, o tempo de execução exporta cada certificado referenciado e sua chave privada correspondente em um arquivo PFX, protegido por uma senha gerada aleatoriamente. Os arquivos PFX e de senha, respectivamente, podem ser acessados dentro do contêiner usando as seguintes variáveis de ambiente: 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

Para clusters do Linux, os certificados (PEM) são copiados do repositório especificado pelo X509StoreName para o contêiner. As variáveis de ambiente correspondentes no Linux são:

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

Como alternativa, se você já tiver os certificados no formato necessário e desejar acessá-los dentro do contêiner, será possível criar um pacote de dados dentro do seu pacote do aplicativo e especificar o seguinte dentro do seu manifesto do aplicativo:

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

O serviço ou o processo do contêiner é responsável por importar os arquivos de certificado para o contêiner. Para importar o certificado, é possível usar scripts `setupentrypoint.sh` ou executar o código dentro do processo do contêiner. Segue o código de exemplo em C# para importar o arquivo PFX:

```csharp
string certificateFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_PFX");
string passwordFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_Password");
X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
password = password.Replace("\0", string.Empty);
X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
store.Open(OpenFlags.ReadWrite);
store.Add(cert);
store.Close();
```
Esse certificado PFX pode ser usado para autenticar o aplicativo ou o serviço ou proteger a comunicação com outros serviços. Por padrão, os arquivos são colocados na lista de controle de acesso apenas para SYSTEM. É possível colocá-lo na ACL para outras contas, conforme exigido pelo serviço.

Como uma próxima etapa, leia os seguintes artigos:

* [Implantar um contêiner do Windows no Service Fabric no Windows Server 2016](service-fabric-get-started-containers.md)
* [Implantar um contêiner do Docker no Service Fabric no Linux](service-fabric-get-started-containers-linux.md)
