---
title: Gerenciamento de segredos em aplicativos do Service Fabric | Microsoft Docs
description: Este artigo descreve como proteger os valores do segredo em um aplicativo do Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: d71924cda8bb3bffbe221946d80dba150359e38e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="managing-secrets-in-service-fabric-applications"></a>Gerenciamento de segredos em aplicativos do Service Fabric
Este guia explica as etapas do gerenciamento de segredos em um aplicativo do Service Fabric. Os segredos podem ser informações confidenciais, como cadeias de conexão de armazenamento, senhas ou outros valores que não devem ser tratados como texto sem formatação.

Este guia usa o Cofre de Chaves do Azure para gerenciar chaves e segredos. No entanto, o *uso* de segredos em um aplicativo é independente de plataforma de nuvem para permitir que os aplicativos sejam implantados em um cluster hospedado em qualquer lugar. 

## <a name="overview"></a>Visão geral
A maneira recomendada de gerenciar as definições de configuração de serviço é por meio de [pacotes de configuração de serviço][config-package]. Os pacotes de configuração são atualizáveis e têm controle de versão por meio de atualizações sem interrupção gerenciadas com reversão automática e validação de integridade. Isso é preferível à configuração global, pois reduz as chances de uma interrupção de serviços globais. Segredos criptografados não são exceção. O Service Fabric tem recursos internos para criptografar e descriptografar valores em um arquivo Settings.XML do pacote de configuração usando a criptografia de certificado.

O diagrama a seguir ilustra o fluxo básico para gerenciamento de segredos em um aplicativo do Service Fabric:

![visão geral do gerenciamento de segredos][overview]

Há quatro etapas principais nesse fluxo:

1. Obtenha um certificado de codificação de dados.
2. Instale o certificado em seu cluster.
3. Criptografe valores do segredo ao implantar um aplicativo com o certificado e coloque-os no arquivo de configuração Settings.xml de um serviço.
4. Leia os valores criptografados de Settings.xml ao descriptografar com o mesmo certificado de codificação. 

O [Azure Key Vault][key-vault-get-started] é usado aqui como um local de armazenamento seguro para certificados e como uma maneira de obter certificados instalados em clusters do Service Fabric no Azure. Se não estiver implantando no Azure, você não precisará usar o cofre de chaves para gerenciar segredos em aplicativos do Service Fabric.

## <a name="data-encipherment-certificate"></a>Certificado de codificação de dados
Um certificado de codificação de dados é usado estritamente para criptografia e descriptografia de valores de configuração no arquivo Settings.xml de um serviço e não é usado para autenticação nem assinatura do texto da criptografia. O certificado deve atender aos seguintes requisitos:

* O certificado deve conter uma chave privada.
* O certificado deve ser criado para troca de chaves, exportável para um arquivo Troca de Informações Pessoais (.pfx).
* O uso da chave de certificado deve incluir a Codificação de Dados (10) e não deve incluir a Autenticação de Servidor ou de Cliente. 
  
  Por exemplo, ao criar um certificado autoassinado usando o PowerShell, o sinalizador `KeyUsage` deverá ser definido como `DataEncipherment`:
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Instalar o certificado em seu cluster
Esse certificado deve ser instalado em cada nó no cluster. Ele será usado em tempo de execução para descriptografar valores armazenados no arquivo Settings.xml de um serviço. Veja [como criar um cluster usando o Azure Resource Manager][service-fabric-cluster-creation-via-arm] para obter instruções de instalação. 

## <a name="encrypt-application-secrets"></a>Criptografar segredos do aplicativo
O SDK do Service Fabric tem funções internas de criptografia e descriptografia de segredos. Os valores secretos podem ser criptografados em tempo de compilação e então descriptografados e lidos programaticamente no código de serviço. 

O comando do PowerShell a seguir é usado para criptografar um segredo. Esse comando só criptografa o valor; ele **não** assina o texto da criptografia. Você deve usar o mesmo certificado de codificação instalado no seu cluster para produzir texto cifrado para valores do segredo:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

A cadeia de caracteres de base 64 resultante contém tanto o texto cifrado secreto como informações sobre o certificado usado para criptografá-lo.  A cadeia de caracteres codificada em base 64 pode ser inserida em um parâmetro no arquivo de configuração Settings.xml do seu serviço com o atributo `IsEncrypted` definido como `true`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```

### <a name="inject-application-secrets-into-application-instances"></a>Inserir segredos do aplicativo em instâncias do aplicativo
Idealmente, a implantação em ambientes diferentes deve ser mais automatizada possível. Isso pode ser feito executando a criptografia secreta em um ambiente de compilação e fornecendo os segredos criptografados como parâmetros durante a criação de instâncias do aplicativo.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Usar parâmetros substituíveis em Settings.xml
O arquivo de configuração de Settings.xml permite a existência de parâmetros substituíveis que podem ser fornecidos no momento da criação de aplicativos. Use o atributo `MustOverride` em vez de fornecer um valor para um parâmetro:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
PS C:\Users\vturecek> New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
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

## <a name="decrypt-secrets-from-service-code"></a>Descriptografar segredos de código de serviço
Os serviços do Service Fabric são executados em NETWORK SERVICE por padrão no Windows e não têm acesso a certificados instalados no nó sem alguma configuração adicional.

Ao usar um certificado de codificação de dados, você precisa certificar-se de que NETWORK SERVICE ou qualquer conta sob a qual o serviço esteja sendo executado terá acesso à chave privada do certificado. O Service Fabric tratará da concessão de acesso para seu serviço de forma automática caso você o configure para isso. Essa configuração pode ser feita em ApplicationManifest.xml por meio da definição de políticas de usuários e de segurança para certificados. No exemplo a seguir, a conta NETWORK SERVICE obtém acesso de leitura para um certificado definido por sua impressão digital:

```xml
<ApplicationManifest … >
    <Principals>
        <Users>
            <User Name="Service1" AccountType="NetworkService" />
        </Users>
    </Principals>
  <Policies>
    <SecurityAccessPolicies>
      <SecurityAccessPolicy GrantRights=”Read” PrincipalRef="Service1" ResourceRef="MyCert" ResourceType="Certificate"/>
    </SecurityAccessPolicies>
  </Policies>
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```

> [!NOTE]
> Ao copiar uma impressão digital do certificado do snap-in de armazenamento de certificado no Windows, um caractere invisível é colocado no início da cadeia de caracteres de impressão digital. Esse caractere invisível pode causar um erro ao tentar localizar um certificado por impressão digital, portanto exclua esse caractere extra.
> 
> 

### <a name="use-application-secrets-in-service-code"></a>Use os segredos do aplicativo no código de serviço
A API para acessar valores de configuração de Settings.xml em um pacote de configuração permite fácil descriptografia de valores que têm o `IsEncrypted` atributo definido como `true`. Como o texto criptografado contém informações sobre o certificado usado para criptografia, você não precisa encontrar o certificado manualmente. Apenas o certificado deve ser instalado no nó que o serviço está em execução. Basta chamar o `DecryptValue()` método para recuperar o valor do segredo original:

```csharp
ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");
SecureString mySecretValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue()
```

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [executar aplicativos com permissões de segurança diferentes](service-fabric-application-runas-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[config-package]: service-fabric-application-model.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-secret-management/overview.png
