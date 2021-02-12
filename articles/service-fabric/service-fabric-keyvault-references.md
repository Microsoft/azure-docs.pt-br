---
title: Service Fabric do Azure – usando referências de keyvault de aplicativo Service Fabric
description: Este artigo explica como usar o suporte do Service-Fabric KeyVaultReference para segredos do aplicativo.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: a0e4ef0decae8cc9ab4dc5f8c69dfef854af81f3
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898589"
---
# <a name="keyvaultreference-support-for-azure-deployed-service-fabric-applications"></a>Suporte KeyVaultReference para aplicativos Service Fabric implantados pelo Azure

Um desafio comum ao criar aplicativos em nuvem é como distribuir com segurança segredos para seus aplicativos. Por exemplo, você pode desejar implantar uma chave de banco de dados em seu aplicativo sem expor a chave durante o pipeline ou para o operador. Service Fabric suporte a KeyVaultReference facilita a implantação de segredos em seus aplicativos simplesmente referenciando a URL do segredo armazenado no Key Vault. Service Fabric irá manipular a busca desse segredo em nome da identidade gerenciada do seu aplicativo e ativar o aplicativo com o segredo.

> [!NOTE]
> O suporte do KeyVaultReference para aplicativos Service Fabric é GA (fora de visualização), começando com Service Fabric versão 7,2 CU5. É recomendável que você atualize para esta versão antes de usar esse recurso.

> [!NOTE]
> O suporte do KeyVaultReference para aplicativos Service Fabric dá suporte apenas a segredos com [controle de versão](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) . Não há suporte para segredos sem versão. O Key Vault precisa estar na mesma assinatura que o cluster do Service Fabric. 

## <a name="prerequisites"></a>Pré-requisitos

- Identidade gerenciada para aplicativos Service Fabric

    Service Fabric suporte a KeyVaultReference usa a identidade gerenciada de um aplicativo para buscar segredos em nome do aplicativo, de modo que seu aplicativo deve ser implantado por meio de uma identidade gerenciada e atribuída a ela. Siga este [documento](concepts-managed-identity.md) para habilitar a identidade gerenciada para seu aplicativo.

- CSS (repositório de segredos central).

    O repositório de segredos central (CSS) é o cache de segredos locais criptografados do Service Fabric. Esse recurso usa CSS para proteger e persistir os segredos depois que eles são buscados de Key Vault. A habilitação desse serviço de sistema opcional também é necessária para consumir esse recurso. Siga este [documento](service-fabric-application-secret-store.md) para habilitar e configurar o CSS.

- Conceder permissão de acesso de identidade gerenciada do aplicativo para o keyvault

    Referencie este [documento](how-to-grant-access-other-resources.md) para ver como conceder acesso de identidade gerenciada ao keyvault. Observe também que se você estiver usando a identidade gerenciada atribuída pelo sistema, a identidade gerenciada será criada somente após a implantação do aplicativo. Isso pode criar condições de corrida em que o aplicativo tenta acessar o segredo antes que a identidade possa receber acesso ao cofre. O nome da identidade atribuída ao sistema será `{cluster name}/{application name}/{service name}` .
    
## <a name="use-keyvaultreferences-in-your-application"></a>Usar o KeyVaultReferences em seu aplicativo
KeyVaultReferences pode ser consumido de várias maneiras
- [Como uma variável de ambiente](#as-an-environment-variable)
- [Montado como um arquivo em seu contêiner](#mounted-as-a-file-into-your-container)
- [Como uma referência a uma senha de repositório de contêiner](#as-a-reference-to-a-container-repository-password)

### <a name="as-an-environment-variable"></a>Como uma variável de ambiente

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
</EnvironmentVariables>
```

```C#
string secret =  Environment.GetEnvironmentVariable("MySecret");
```

### <a name="mounted-as-a-file-into-your-container"></a>Montado como um arquivo em seu contêiner

- Adicionar uma seção a settings.xml

    Definir `MySecret` parâmetro com tipo `KeyVaultReference` e valor `<KeyVaultURL>`

    ```xml
    <Section Name="MySecrets">
        <Parameter Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
    </Section>
    ```

- Faça referência à nova seção no ApplicationManifest.xml `<ConfigPackagePolicies>`

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="MyServiceMI" ApplicationIdentityRef="MyApplicationMI" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="MySecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- Consumir os segredos do código de serviço

    Cada parâmetro listado em `<Section  Name=MySecrets>` será um arquivo sob a pasta apontada por EnvironmentVariable SecretPath. O trecho de código C# abaixo mostra como ler MySecret de seu aplicativo.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "MySecret"))) 
    {
        string secret =  sr.ReadToEnd();
    }
    ```
    > [!NOTE] 
    > MountPoint controla a pasta onde os arquivos que contêm valores secretos serão montados.

### <a name="as-a-reference-to-a-container-repository-password"></a>Como uma referência a uma senha de repositório de contêiner

```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="MyACRUser" Type="KeyVaultReference" Password="<KeyVaultURL>"/>
      </ContainerHostPolicies>
```

## <a name="next-steps"></a>Próximas etapas

* [Documentação do Azure keyvault](../key-vault/index.yml)
* [Saiba mais sobre o repositório de segredos central](service-fabric-application-secret-store.md)
* [Saiba mais sobre identidade gerenciada para aplicativos Service Fabric](concepts-managed-identity.md)
