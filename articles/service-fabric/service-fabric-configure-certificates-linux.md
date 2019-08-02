---
title: Configurar certificados para aplicativos do Azure Service Fabric no Linux | Microsoft Docs
description: Configurar certificados para seu aplicativo com o tempo de execução do Service Fabric em um cluster do Linux
services: service-fabric
documentationcenter: NA
author: JimacoMS2
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2018
ms.author: chackdan
ms.openlocfilehash: 4a5a67133d52a0cdc0cc082ab85c1cc791c13ad5
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876570"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Certificados e segurança em clusters do Linux

Este artigo fornece informações sobre como configurar certificados x. 509 em clusters do Linux.

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>Local e formato de certificados X.509 em nós do Linux

Malha do serviço espera geralmente certificados x. 509 no */var/lib/sfcerts* diretório em nós de cluster do Linux. Isso é verdadeiro para certificados de cluster, os certificados de cliente, etc. Em alguns casos, você pode especificar um local diferente do *var/lib/sfcerts* pasta certificados. Por exemplo, com serviços confiáveis criados usando o SDK de Java de malha do serviço, você pode especificar um local diferente de pacote de configuração (Settings.xml) para alguns certificados específicos do aplicativo. Para obter mais informações, consulte [certificados referenciados no pacote de configuração (Settings.xml)](#certificates-referenced-in-the-configuration-package-settingsxml).

Para clusters do Linux, o Service Fabric espera certificados deve existir como um arquivo. PEM que contém o certificado e a chave privada ou como um arquivo. crt que contém o certificado e um arquivo de chave que contém a chave privada. Todos os arquivos devem estar no formato PEM. 

Se você instalar o certificado do Cofre de chaves do Azure usando um [modelo do Gerenciador de recursos](./service-fabric-cluster-creation-create-template.md) ou [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/?view=latest#service_fabric) comandos, o certificado é instalado no formato correto no */var/ lib/sfcerts* diretório em cada nó. Se você instalar um certificado por outro método, você deve garantir que o certificado está instalado corretamente em nós de cluster.

## <a name="certificates-referenced-in-the-application-manifest"></a>Certificados referenciados no manifesto de aplicativo

Os certificados especificados no manifesto do aplicativo, por exemplo, por meio dos elementos [**SecretsCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) ou [**EndpointCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#endpointcertificate-element), devem estar presentes no diretório */var/lib/sfcerts*. Os elementos usados para especificar certificados no manifesto do aplicativo não usam um atributo de caminho, portanto, os certificados devem estar presentes no diretório padrão. Estes elementos tomam um atributo **X509StoreName** opcional. O padrão é "My", que aponta para o diretório */var/lib/sfcerts* nos nós do Linux. Qualquer outro valor é indefinido em um clister Linux. Recomendamos que você omita o atributo **X509StoreName** para aplicativos executados em clusters do Linux. 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>Certificados referenciados no pacote de configuração (Settings.xml)

Para alguns serviços, você pode configurar certificados X.509 no [ConfigPackage](./service-fabric-application-and-service-manifests.md) (por padrão, Settings.xml). Por exemplo, esse é o caso quando você declara certificados usados para proteger canais RPC para serviços do Reliable Services criados com o Service Fabric .NET Core ou Java SDKs. Há duas maneiras de referenciar certificados no pacote de configuração. Suporte varia entre o .NET Core e Java SDKs.

### <a name="using-x509-securitycredentialstype"></a>Usando X509 SecurityCredentialsType

Com o .NET ou Java SDks, você consegue especificar **X509** para o **SecurityCredentialsType**. Isto corresponde ao `X509Credentials` ([.NET](https://msdn.microsoft.com/library/system.fabric.x509credentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric.x509credentials)) tipo de `SecurityCredentials` ([.NET](https://msdn.microsoft.com/library/system.fabric.securitycredentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric.securitycredentials)).

A referência **X509** localiza o certificado em um armazenamento de certificados. O XML a seguir mostra os parâmetros usados para especificar o local do certificado:

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

Para um serviço em execução no Linux, **LocalMachine**/**My** aponta para o local padrão para certificados, o diretório */var/lib/sfcerts* . Para Linux, quaisquer outras combinações de **CertificateStoreLocation** e **CertificateStoreName** são indefinidas. 

Sempre especificar **LocalMachine** para o parâmetro **CertificateStoreLocation**. Não há necessidade de especificar o parâmetro **CertificateStoreName** porque ele tem como padrão “My”. Com uma referência **X509**, os arquivos de certificado devem estar localizados no diretório */var/lib/sfcerts* no nó do cluster.  

O seguinte XML mostra uma seção **TransportSettings** baseada neste estilo:

```xml
<Section Name="HelloWorldStatefulTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
    <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
    <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
    <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
</Section>
```

### <a name="using-x5092-securitycredentialstype"></a>Usando X509_2 SecurityCredentialsType

Com Java SDks, você consegue especificar **X509_2** para o **SecurityCredentialsType**. Isto corresponde ao `X509Credentials2` ([Java](https://docs.microsoft.com/java/api/system.fabric.x509credentials2)) tipo de `SecurityCredentials` ([Java](https://docs.microsoft.com/java/api/system.fabric.securitycredentials)). 

Com uma referência **X509_2**, você especifica um parâmetro path, para poder localizar o certificado em um diretório diferente de */var/lib/sfcerts*.  O XML a seguir mostra os parâmetros usados para especificar o local do certificado: 

```xml
     <Parameter Name="SecurityCredentialsType" Value="X509_2" />
     <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
```

O seguinte XML mostra uma seção **TransportSettings** baseada neste estilo.

```xml
<!--Section name should always end with "TransportSettings".-->
<!--Here we are using a prefix "HelloWorldStateless".-->
<Section Name="HelloWorldStatelessTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509_2" />
    <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
    <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
</Section>
```

> [!NOTE]
> O certificado é especificado como um arquivo .crt no XML precedente. Isto implica que há também um arquivo .key contendo uma chave privada no mesmo local.

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>Configurar um aplicativo de Reliable Services para executar em clusters do Linux

Os SDKs do Service Fabric permitem que você se comunique com as APIs de tempo de execução do Service Fabric para aproveitar a plataforma. Quando você executa qualquer aplicativo que usa essa funcionalidade em clusters Linux seguros, é necessário configurar seu aplicativo com um certificado que possa ser usado para validar com o tempo de execução do Service Fabric. Aplicativos que contêm serviços Service Fabric Reliable Service escritos usando o .NET Core ou Java SDKs exigem essa configuração. 

Para configurar um aplicativo, adicione um elemento [**SecretsCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) sob a marca **Certificates**, localizada sob a marca **ApplicationManifest** no arquivo *ApplicationManifest.xml*. O XML seguinte mostra um certificado referenciado pela sua impressão digital: 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

Você consegue referenciar tanto o certificado de cluster quanto um certificado que você instalou em cada nó de cluster. No Linux, os arquivos de certificado devem estar presentes no diretório */var/lib/sfcerts*. Para saber mais, consulte [Local e formato de certificados X.509 em nós do Linux](#location-and-format-of-x509-certificates-on-linux-nodes).



