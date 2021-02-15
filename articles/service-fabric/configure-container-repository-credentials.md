---
title: Service Fabric do Azure – configurar credenciais do repositório de contêiner
description: Configurar credenciais de repositório para baixar imagens do registro de contêiner
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 0c6421fed88a3909db717c13a6b3faf51c4491cd
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574812"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Configurar credenciais de repositório para seu aplicativo para baixar imagens de contêiner

Configure a autenticação do registro de contêiner adicionando `RepositoryCredentials` à `ContainerHostPolicies` seção do manifesto do aplicativo. Adicione a conta e a senha para o registro de contêiner (*myregistry.azurecr.Io* no exemplo abaixo), que permite que o serviço Baixe a imagem de contêiner do repositório.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

É recomendável criptografar a senha do repositório usando um certificado de codificação que é implantado em todos os nós do cluster. Quando o Service Fabric implanta o pacote de serviço para o cluster, o certificado de criptografia é usado para descriptografar o texto cifrado. O cmdlet Invoke-ServiceFabricEncryptText é usado para criar o texto cifrado para a senha, que é adicionado ao arquivo ApplicationManifest.xml.
Consulte [Gerenciamento de segredos](service-fabric-application-secret-management.md) para obter mais informações sobre certificados e semântica de criptografia.

## <a name="configure-cluster-wide-credentials"></a>Configurar credenciais de todo o cluster

Service Fabric permite que você configure credenciais em todo o cluster que podem ser usadas como credenciais de repositório padrão por aplicativos.

Esse recurso pode ser habilitado ou desabilitado adicionando o `UseDefaultRepositoryCredentials` atributo a `ContainerHostPolicies` em ApplicationManifest.xml com um `true` `false` valor ou.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code" UseDefaultRepositoryCredentials="true">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

Em seguida, Service Fabric usa as credenciais de repositório padrão que podem ser especificadas no ClusterManifest na `Hosting` seção.  Se `UseDefaultRepositoryCredentials` for `true`, o Service Fabric lerá os seguintes valores de ClusterManifest:

* DefaultContainerRepositoryAccountName (cadeia de caracteres)
* DefaultContainerRepositoryPassword (cadeia de caracteres)
* IsDefaultContainerRepositoryPasswordEncrypted (booliano)
* DefaultContainerRepositoryPasswordType (cadeia de caracteres)

Aqui está um exemplo do que pode ser adicionado dentro da `Hosting` seção na ClusterManifestTemplate.jsno arquivo. A `Hosting` seção pode ser adicionada na criação do cluster ou posteriormente em uma atualização de configuração. Para obter mais informações, consulte [Alterar configurações de cluster do Azure Service Fabric](service-fabric-cluster-fabric-settings.md) e [Gerenciar segredos de aplicativo do Azure Service Fabric](service-fabric-application-secret-management.md)

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
            "name": "EndpointProviderEnabled",
            "value": "true"
          },
          {
            "name": "DefaultContainerRepositoryAccountName",
            "value": "someusername"
          },
          {
            "name": "DefaultContainerRepositoryPassword",
            "value": "somepassword"
          },
          {
            "name": "IsDefaultContainerRepositoryPasswordEncrypted",
            "value": "false"
          },
          {
            "name": "DefaultContainerRepositoryPasswordType",
            "value": "PlainText"
          }
        ]
      },
]
```

## <a name="use-tokens-as-registry-credentials"></a>Usar tokens como credenciais do registro

O Service Fabric dá suporte ao uso de tokens como credenciais para baixar imagens para seus contêineres.  Esse recurso aproveita a *identidade gerenciada* do conjunto de dimensionamento de máquinas virtuais subjacente para autenticar no registro, eliminando a necessidade de gerenciar as credenciais do usuário.  Consulte [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) para obter mais informações.  O uso desse recurso requer as seguintes etapas:

1. Verifique se a *identidade gerenciada atribuída pelo sistema* está habilitada para a VM.

    ![Portal do Azure: criar opção de identidade do conjunto de dimensionamento de máquinas virtuais](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

> [!NOTE]
> Para identidade gerenciada atribuída pelo usuário, ignore esta etapa. As etapas restantes a seguir funcionarão da mesma forma, desde que o conjunto de dimensionamento só esteja associado a uma única identidade gerenciada atribuída pelo usuário.

2. Conceda permissões ao conjunto de dimensionamento de máquinas virtuais para extrair/ler imagens do registro. Na folha de controle de acesso (IAM) do seu registro de contêiner do Azure no portal do Azure, adicione uma *atribuição de função* para sua máquina virtual:

    ![Adicionar entidade de segurança de VM ao ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. Em seguida, modifique o manifesto do aplicativo. Na `ContainerHostPolicies` seção, adicione o atributo `‘UseTokenAuthenticationCredentials=”true”` .

    ```xml
      <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
      <Policies>
        <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" UseTokenAuthenticationCredentials="true">
          <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        </ContainerHostPolicies>
        <ResourceGovernancePolicy CodePackageRef="NodeService.Code" MemoryInMB="256"/>
      </Policies>
      </ServiceManifestImport>
    ```

    > [!NOTE]
    > O sinalizador `UseDefaultRepositoryCredentials` definido como true enquanto `UseTokenAuthenticationCredentials` for true causará um erro durante a implantação.

## <a name="next-steps"></a>Próximas etapas

* Veja mais sobre a [autenticação do registro de contêiner](../container-registry/container-registry-authentication.md).
