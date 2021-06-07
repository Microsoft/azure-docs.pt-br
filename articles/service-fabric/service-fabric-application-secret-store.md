---
title: Repositório de segredos do Azure Service Fabric central
description: Este artigo descreve como usar o repositório de segredos centrais no Azure Service Fabric.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: e9fd435803ad5354b0eb2d4f5de50009a8cbbfe2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88869748"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Repositório de segredos centrais no Azure Service Fabric 
Este artigo descreve como usar o armazenamento de segredos centrais (CSS) no Azure Service Fabric para criar segredos em aplicativos Service Fabric. O CSS é um cache de repositório de segredo local que mantém dados confidenciais, como senha, tokens e chaves, criptografados na memória.

  > [!NOTE] 
  > Ao ativar o CSS pela primeira vez antes da versão 7,1. CU3, a ativação poderá falhar e deixar o CSS em um estado permanentemente não íntegro se: CSS for ativado em um cluster autenticado do Windows; O CSS é ativado em qualquer cluster, mas `EncryptionCertificateThumbprint` é declarado incorretamente ou o certificado correspondente não está instalado/ACL-Ed em nós. Para o cluster de autenticação do Windows, visite 7,1. CU3 antes de continuar. Para outros clusters, verifique novamente essas invariáveis ou chegue em 7,1. Cu3.
  
## <a name="enable-central-secrets-store"></a>Habilitar repositório de segredos centrais
Adicione o script a seguir à sua configuração de cluster em `fabricSettings` para habilitar o CSS. Recomendamos que você use um certificado diferente de um certificado de cluster para CSS. Verifique se o certificado de criptografia está instalado em todos os nós e se `NetworkService` tem permissão de leitura para a chave privada do certificado.
  ```json
    "fabricSettings": 
    [
        ...
    {
        "name":  "CentralSecretService",
        "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "1"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                },
                 {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                 }
                ,
                ],
            },
            ]
     }
        ...
     ]
```
## <a name="declare-a-secret-resource"></a>Declarar um recurso secreto
Você pode criar um recurso secreto usando a API REST.
  > [!NOTE] 
  > Se o cluster estiver usando a autenticação do Windows, a solicitação REST será enviada por um canal HTTP não seguro. A recomendação é usar um cluster baseado em X509 com pontos de extremidade seguros.

Para criar um `supersecret` recurso secreto usando a API REST, faça uma solicitação Put para `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview` . Você precisa do certificado do cluster ou do certificado do cliente administrador para criar um recurso secreto.

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>Definir o valor secreto

Use o script a seguir para usar a API REST para definir o valor secreto.
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>Examinar o valor secreto
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>Usar o segredo em seu aplicativo

Siga estas etapas para usar o segredo em seu aplicativo Service Fabric.

1. Adicione uma seção no arquivo de **settings.xml** com o trecho a seguir. Observe aqui que o valor está no formato { `secretname:version` }.

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. Importe a seção no **ApplicationManifest.xml**.
   ```xml
     <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" EnvironmentVariableName="SecretPath" />
           </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```

   A variável de ambiente `SecretPath` apontará para o diretório onde todos os segredos são armazenados. Cada parâmetro listado na `testsecrets` seção é armazenado em um arquivo separado. O aplicativo agora pode usar o segredo da seguinte maneira:
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. Monte os segredos em um contêiner. A única alteração necessária para tornar os segredos disponíveis dentro do contêiner é para `specify` um ponto de montagem no `<ConfigPackage>` .
O trecho a seguir é o **ApplicationManifest.xml** modificado.  

   ```xml
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="C:\secrets" EnvironmentVariableName="SecretPath" />
           <!-- Linux Container
            <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="/mnt/secrets" EnvironmentVariableName="SecretPath" />
           -->
         </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```
   Os segredos estão disponíveis no ponto de montagem dentro de seu contêiner.

1. Você pode associar um segredo a uma variável de ambiente de processo especificando `Type='SecretsStoreRef` . O trecho a seguir é um exemplo de como associar a `supersecret` versão `ver1` à variável de ambiente `MySuperSecret` no **ServiceManifest.xml**.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a [segurança de aplicativos e serviços](service-fabric-application-and-service-security.md).
