---
title: Gerenciar segredos do aplicativo do Microsoft Azure Service Fabric | Microsoft Docs
description: Saiba como proteger os valores do segredo em um aplicativo do Service Fabric (independente de plataforma).
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: d151dbf20e68a2152e9d886a74e51786bb8fbfa6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614476"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Gerenciar segredos criptografados nos aplicativos do Service Fabric
Este guia explica as etapas do gerenciamento de segredos em um aplicativo do Service Fabric. Os segredos podem ser informações confidenciais, como cadeias de conexão de armazenamento, senhas ou outros valores que não devem ser tratados como texto sem formatação.

O uso de segredos criptografados em um aplicativo do Service Fabric envolve três etapas:
* Configurar um certificado de criptografia e criptografar segredos.
* Especifique segredos criptografados em um aplicativo.
* Descriptografar segredos criptografados do código de serviço.

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>Configurar um certificado de criptografia e criptografar segredos
A configuração de um certificado de criptografia e seu uso para criptografar segredos secretas varia entre o Windows e o Linux.
* [Configurar um certificado de criptografia e criptografar segredos em clusters do Windows.][secret-management-windows-specific-link]
* [Configurar um certificado de criptografia e criptografar segredos em clusters do Linux.][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>Especificar segredos criptografados em um aplicativo
A etapa anterior descreve como criptografar um segredo com um certificado e produzir uma cadeia de caracteres codificada em base 64 para uso em um aplicativo. Essa cadeia de caracteres codificada de base 64 pode ser especificada como um [parâmetro][parameters-link] criptografado no Settings.xml de um serviço ou como uma [variável de ambiente][environment-variables-link] criptografada no ServiceManifest.xml de um serviço.

Especifique um [parâmetro][parameters-link] no arquivo de configuração Settings.xml do seu serviço com o atributo `IsEncrypted` definido como `true`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
Especifique uma [variável de ambiente][environment-variables-link] criptografada no arquivo ServiceManifest.xml do seu serviço com o atributo `Type` definido como `Encrypted`:
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

### <a name="inject-application-secrets-into-application-instances"></a>Inserir segredos do aplicativo em instâncias do aplicativo
Idealmente, a implantação em ambientes diferentes deve ser mais automatizada possível. Isso pode ser feito executando a criptografia secreta em um ambiente de compilação e fornecendo os segredos criptografados como parâmetros durante a criação de instâncias do aplicativo.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Usar parâmetros substituíveis em Settings.xml
O arquivo de configuração de Settings.xml permite a existência de parâmetros substituíveis que podem ser fornecidos no momento da criação de aplicativos. Use o atributo `MustOverride` em vez de fornecer um valor para um parâmetro:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Para substituir valores em Settings.xml, declare um parâmetro de substituição para o serviço em ApplicationManifest.xml:

```xml
<ApplicationManifest ... >
  <Parameters>
    <Parameter Name="MySecret" DefaultValue="" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides>
      <ConfigOverride Name="Config">
        <Settings>
          <Section Name="MySettings">
            <Parameter Name="MySecret" Value="[MySecret]" IsEncrypted="true" />
          </Section>
        </Settings>
      </ConfigOverride>
    </ConfigOverrides>
  </ServiceManifestImport>
 ```

Agora, o valor pode ser especificado como um *parâmetro de aplicativo* ao criar uma instância do aplicativo. A criação de uma instância do aplicativo pode ser controlada por script usando o PowerShell, ou escrita em C#, para fácil integração em um processo de compilação.

Usando o PowerShell, o parâmetro é fornecido para o comando `New-ServiceFabricApplication` como uma [tabela de hash](https://technet.microsoft.com/library/ee692803.aspx):

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Usando C#, os parâmetros do aplicativo são especificados em um `ApplicationDescription` como um `NameValueCollection`:

```csharp
FabricClient fabricClient = new FabricClient();

NameValueCollection applicationParameters = new NameValueCollection();
applicationParameters["MySecret"] = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=";

ApplicationDescription applicationDescription = new ApplicationDescription(
    applicationName: new Uri("fabric:/MyApp"),
    applicationTypeName: "MyAppType",
    applicationTypeVersion: "1.0.0",
    applicationParameters: applicationParameters)
);

await fabricClient.ApplicationManager.CreateApplicationAsync(applicationDescription);
```

## <a name="decrypt-encrypted-secrets-from-service-code"></a>Descriptografar segredos criptografados do código de serviço
As APIs para acessar [parâmetros][parameters-link] e [variáveis de ambiente][environment-variables-link] permitem descriptografar com facilidade valores criptografados. Já que a cadeia de caracteres criptografada contém informações sobre o certificado usado para criptografia, você não precisa especificar o certificado manualmente. Apenas o certificado deve ser instalado no nó que o serviço está em execução.

```csharp
// Access decrypted parameters from Settings.xml
ConfigurationPackage configPackage = FabricRuntime.GetActivationContext().GetConfigurationPackageObject("Config");
bool MySecretIsEncrypted = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].IsEncrypted;
if (MySecretIsEncrypted)
{
    SecureString MySecretDecryptedValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue();
}

// Access decrypted environment variables from ServiceManifest.xml
// Note: you do not have to call any explicit API to decrypt the environment variable.
string MyEnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [segurança de aplicativo e serviço](service-fabric-application-and-service-security.md)

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
