---
title: Proteger um cluster do Azure Service Fabric no Windows usando certificados | Microsoft Docs
description: Proteja a comunicação em um Azure Service Fabric autônomo ou em um cluster local, bem como entre clientes e o cluster.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: fe0ed74c-9af5-44e9-8d62-faf1849af68c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: ee2ce03fccc3e6556f9d261687edb050c8cfa1cc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60628142"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>Proteger um cluster autônomo no Windows usando os certificados X.509
Esse artigo descreve como proteger a comunicação entre vários nós do cluster autônomo do Windows. Ele também descreve como autenticar os clientes que estão se conectando a este cluster usando certificados X.509. Essa autenticação garante que somente usuários autorizados possam acessar o cluster e os aplicativos implantados e executar tarefas de gerenciamento. A segurança do certificado deve ser habilitada no cluster quando o cluster é criado.  

Para obter mais informações sobre segurança de cluster, como segurança de nó a nó, segurança de cliente a nó e controle de acesso baseado em função, confira [Cenários de segurança de cluster](service-fabric-cluster-security.md).

## <a name="which-certificates-do-you-need"></a>De quais certificados você precisa?
Para começar, [faça download do pacote do Service Fabric para Windows Server](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package) para um dos nós no cluster. No pacote baixado, você encontra um arquivo ClusterConfig.X509.MultiMachine.json. Abra o arquivo e examine a seção para segurança na seção de Propriedades:

```JSON
"security": {
    "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    "CertificateInformation": {
        "ClusterCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },        
        "ClusterCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ClusterCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ServerCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ServerCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ServerCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ClientCertificateThumbprints": [
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            },
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateCommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames": "Root"
            }
        ]
        "ReverseProxyCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ReverseProxyCertificateCommonNames": {
            "CommonNames": [
                {
                "CertificateCommonName": "[CertificateCommonName]"
                }
            ],
            "X509StoreName": "My"
        }
    }
},
```

Esta seção descreve os certificados necessários para proteger o cluster do Windows autônomo. Se você especificar um certificado de cluster, defina o valor de ClusterCredentialType como _X509_. Se você especificar um certificado do servidor para conexões externas, defina ServerCredentialType como _X509_. Embora não seja obrigatório, recomendamos ter esses dois certificados para um cluster protegido adequadamente. Se você definir esses valores como *X509*, também deverá especificar os certificados correspondentes; caso contrário, o Service Fabric gerará uma exceção. Em alguns cenários, talvez você apenas deseje especificar _ClientCertificateThumbprints_ ou _ReverseProxyCertificate_. Nesses cenários, não é necessário definir _ClusterCredentialType_ nem _ServerCredentialType_ como _X509_.


> [!NOTE]
> Uma [impressão digital](https://en.wikipedia.org/wiki/Public_key_fingerprint) é a principal identidade de um certificado. Leia [Recuperar a impressão digital de um certificado](https://msdn.microsoft.com/library/ms734695.aspx) para descobrir a impressão digital dos certificados que você cria.
> 
> 

A tabela a seguir lista os certificados que serão necessários na configuração do seu cluster:

| **Configuração de CertificateInformation** | **Descrição** |
| --- | --- |
| ClusterCertificate |Recomendado para um ambiente de teste. Esse certificado é necessário para proteger a comunicação entre os nós em um cluster. Você pode usar dois certificados diferentes, um principal e um secundário para atualização. Defina a impressão digital do certificado principal na seção Impressão Digital e a do secundário nas variáveis ThumbprintSecondary. |
| ClusterCertificateCommonNames |Recomendado para um ambiente de produção. Esse certificado é necessário para proteger a comunicação entre os nós em um cluster. Você pode usar um ou dois nomes comuns de certificado de cluster. CertificateIssuerThumbprint corresponde à impressão digital do emissor deste certificado. Especifique várias impressões digitais de emissor se mais de um certificado com o mesmo nome comum estiver sendo usado.|
| ClusterCertificateIssuerStores |Recomendado para um ambiente de produção. Este certificado corresponde ao emissor do certificado do cluster. Você pode fornecer o nome comum do emissor e o nome do repositório correspondente nesta seção em vez de especificar a impressão digital do emissor em ClusterCertificateCommonNames.  Isso facilita a sobreposição de certificados de emissor do cluster. Vários emissores poderão ser especificados se mais de um certificado do cluster for usado. Um IssuerCommonName vazio coloca na lista de permissões todos os certificados nos repositórios correspondentes especificados em X509StoreNames.|
| ServerCertificate |Recomendado para um ambiente de teste. Esse certificado é apresentado ao cliente quando ele tenta se conectar a esse cluster. Para sua conveniência, você pode optar por usar o mesmo certificado para ClusterCertificate e ServerCertificate. Você pode usar dois certificados de servidor diferentes, um principal e um secundário, para atualização. Defina a impressão digital do certificado principal na seção Impressão Digital e a do secundário nas variáveis ThumbprintSecondary. |
| ServerCertificateCommonNames |Recomendado para um ambiente de produção. Esse certificado é apresentado ao cliente quando ele tenta se conectar a esse cluster. CertificateIssuerThumbprint corresponde à impressão digital do emissor deste certificado. Especifique várias impressões digitais de emissor se mais de um certificado com o mesmo nome comum estiver sendo usado. Para sua conveniência, você pode optar por usar o mesmo certificado para ClusterCertificateCommonNames e ServerCertificateCommonNames. Você pode usar um ou dois nomes comuns de certificado. |
| ServerCertificateIssuerStores |Recomendado para um ambiente de produção. Este certificado corresponde ao emissor do certificado do servidor. Você pode fornecer o nome comum do emissor e o nome do repositório correspondente nesta seção em vez de especificar a impressão digital do emissor em ServerCertificateCommonNames.  Isso facilita a sobreposição de certificados de emissor do servidor. Vários emissores poderão ser especificados se mais de um certificado do servidor for usado. Um IssuerCommonName vazio coloca na lista de permissões todos os certificados nos repositórios correspondentes especificados em X509StoreNames.|
| ClientCertificateThumbprints |Instale esse conjunto de certificados nos clientes autenticados. Você pode ter alguns certificados de cliente diferentes instalados nos computadores para os quais você deseja permitir o acesso ao cluster. Defina a impressão digital de cada certificado na variável CertificateThumbprint. Se você definir IsAdmin como *true*, o cliente com esse certificado instalado poderá realizar atividades de gerenciamento de administrador no cluster. Se IsAdmin for *false*, o cliente com esse certificado só poderá executar as ações permitidas somente para direitos de acesso do usuário, normalmente, somente leitura. Para obter mais informações sobre funções, consulte [Controle de acesso baseado em função (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac). |
| ClientCertificateCommonNames |Defina o nome comum do primeiro certificado do cliente para CertificateCommonName. A CertificateIssuerThumbprint é a impressão digital para o emissor deste certificado. Consulte [Trabalhar com certificados](https://msdn.microsoft.com/library/ms731899.aspx) para saber mais sobre os nomes comuns e o emissor. |
| ClientCertificateIssuerStores |Recomendado para um ambiente de produção. Este certificado corresponde ao emissor do certificado do cliente (funções de administrador e não administrador). Você pode fornecer o nome comum do emissor e o nome do repositório correspondente nesta seção em vez de especificar a impressão digital do emissor em ClientCertificateCommonNames.  Isso facilita a sobreposição de certificados de emissor do cliente. Vários emissores poderão ser especificados se mais de um certificado do cliente for usado. Um IssuerCommonName vazio coloca na lista de permissões todos os certificados nos repositórios correspondentes especificados em X509StoreNames.|
| ReverseProxyCertificate |Recomendado para um ambiente de teste. Esse certificado opcional pode ser especificado se você desejar proteger o [proxy Reverso](service-fabric-reverseproxy.md). Verifique se reverseProxyEndpointPort está definido em nodeTypes caso você use esse certificado. |
| ReverseProxyCertificateCommonNames |Recomendado para um ambiente de produção. Esse certificado opcional pode ser especificado se você desejar proteger o [proxy Reverso](service-fabric-reverseproxy.md). Verifique se reverseProxyEndpointPort está definido em nodeTypes caso você use esse certificado. |

Aqui está um exemplo de configuração de cluster em que os certificados de cluster, servidor e cliente foram fornecidos. Para certificados de cluster/servidor/reverseProxy, a impressão digital e o nome comum não podem ser configurados juntos para o mesmo tipo de certificado.

 ```JSON
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "10-2017",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "10.7.0.6",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        },
        "security": {
            "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myClusterCertCommonName"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClusterCertificateIssuerStores": [
                    {
                        "IssuerCommonName": "ClusterIssuer1",
                        "X509StoreNames" : "Root"
                    },
                    {
                        "IssuerCommonName": "ClusterIssuer2",
                        "X509StoreNames" : "Root"
                    }
                ],
                "ServerCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myServerCertCommonName",
                      "CertificateIssuerThumbprint": "7c fc 91 97 13 16 8d ff a8 ee 71 2b a2 f4 62 62 00 03 49 0d"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpointPort": "19001",
            "leaseDriverEndpointPort": "19002",
            "serviceConnectionEndpointPort": "19003",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="certificate-rollover"></a>Sobreposição de certificado
Ao usar um nome comum do certificado em vez de uma impressão digital, a sobreposição de certificado não requer a atualização de configuração do cluster. Para atualizações de impressão digital do emissor, certifique-se de que a nova lista de impressão digital faça interseções com a lista antiga. Primeiro, você deve fazer uma atualização de configuração com as novas impressões digitais do emissor e, em seguida, instalar os novos certificados (certificado do servidor/cluster e certificados do emissor) no repositório. Mantenha o certificado do emissor antigo no repositório de certificados por, pelo menos, duas horas depois de instalar o novo certificado do emissor.
Se você estiver usando repositórios de emissor, nenhuma atualização de configuração precisará ser feita para a sobreposição do certificado do emissor. Instale o novo certificado do emissor com uma data de expiração posterior no repositório de certificados correspondente e remova o antigo certificado do emissor depois de algumas horas.

## <a name="acquire-the-x509-certificates"></a>Adquirir os certificados X.509
Para proteger a comunicação no cluster, primeiro, você precisa obter certificados X.509 para os nós de cluster. Além disso, para limitar a conexão a este cluster a computadores e a usuários autorizados, você precisa obter e instalar certificados para os computadores cliente.

Para clusters que estão executando cargas de trabalho de produção, use um certificado X.509 assinado pela [AC (Autoridade de Certificação)](https://en.wikipedia.org/wiki/Certificate_authority) para proteger o cluster. Para obter mais informações sobre como obter esses certificados, consulte [Como obter um certificado](https://msdn.microsoft.com/library/aa702761.aspx).

Para clusters usados para fins de teste, você pode optar por usar um certificado assinado automaticamente.

## <a name="optional-create-a-self-signed-certificate"></a>Opcional: Crie um certificado autoassinado
Uma maneira de criar um certificado autoassinado que pode ser protegido corretamente é usar o script CertSetup.ps1 na pasta do SDK do Service Fabric no diretório C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure. Edite esse arquivo para alterar o nome padrão do certificado. (Procure o valor CN=ServiceFabricDevClusterCert.) Execute esse script como `.\CertSetup.ps1 -Install`.

Agora, exporte o certificado para um arquivo .pfx com uma senha protegida. Primeiro, obtenha a impressão digital do certificado. 
1. No menu **Iniciar**, execute **Gerenciar certificados de computador**. 

2. Navegue até a pasta **Computador Local\Pessoal** e localize o certificado que você criou. 

3. Clique duas vezes no certificado para abri-lo, selecione a guia **Detalhes** e role para baixo até o campo **Impressão digital**. 

4. Remova os espaços e copie o valor de impressão digital para o comando do PowerShell abaixo. 

5. Altere o valor `String` para uma senha segura adequada para protegê-lo e execute o seguinte no PowerShell:

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. Para ver os detalhes de um certificado instalado no computador, execute o seguinte comando do PowerShell:

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

Como alternativa, se você tiver uma assinatura do Azure, siga as etapas em [Criar um cluster do Service Fabric usando o Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="install-the-certificates"></a>Instalar os certificados
Quando tiver os certificados, você poderá instalá-los nos nós de cluster. Os nós precisam ter o mais recente Windows PowerShell 3. x instalado neles. Repita essas etapas em cada nó, para os certificados do cluster e do servidor e todos os certificados secundários.

1. Copie o(s) arquivo(s) .pfx para o nó.

2. Abra uma janela do PowerShell como administrador e insira os comandos a seguir. Substitua *$pswd* pela senha que você usou para criar esse certificado. Substitua *$PfxFilePath* pelo caminho completo do .pfx copiado nesse nó.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Agora defina o controle de acesso nesse certificado para que o processo do Service Fabric, executado na conta de Serviço de Rede, possa usá-lo executando o script a seguir. Forneça a impressão digital do certificado e o **SERVIÇO DE REDE** para a conta de serviço. Verifique se as ACLs no certificado estão corretas abrindo o certificado em **Iniciar** > **Gerenciar certificados de computador** e observando **Todas as Tarefas** > **Gerenciar Chaves Privadas**.
   
    ```powershell
    param
    (
    [Parameter(Position=1, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$pfxThumbPrint,
   
    [Parameter(Position=2, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$serviceAccount
    )
   
    $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; }
   
    # Specify the user, the permissions, and the permission type
    $permission = "$($serviceAccount)","FullControl","Allow"
    $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission
   
    # Location of the machine-related keys
    $keyPath = Join-Path -Path $env:ProgramData -ChildPath "\Microsoft\Crypto\RSA\MachineKeys"
    $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName
    $keyFullPath = Join-Path -Path $keyPath -ChildPath $keyName
   
    # Get the current ACL of the private key
    $acl = (Get-Item $keyFullPath).GetAccessControl('Access')
   
    # Add the new ACE to the ACL of the private key
    $acl.SetAccessRule($accessRule)
   
    # Write back the new ACL
    Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop
   
    # Observe the access rights currently assigned to this certificate
    get-acl $keyFullPath| fl
    ```
4. Repita as etapas acima para cada certificado do servidor. Você também pode usar essas etapas para instalar os certificados de cliente nos computadores para os quais deseja permitir acesso ao cluster.

## <a name="create-the-secure-cluster"></a>Criar o cluster seguro
Depois de configurar a seção de segurança do arquivo ClusterConfig.X509.MultiMachine.json, vá para a seção [Criar o cluster](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) para configurar os nós e criar o cluster autônomo. Lembre-se de usar o arquivo ClusterConfig.X509.MultiMachine.json ao criar o cluster. Por exemplo, o comando pode ser semelhante ao seguinte:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

Depois que o cluster do Windows autônomo seguro estiver sendo executado com sucesso e os clientes autenticados tiverem sido configurados para se conectarem a ele, siga as etapas na seção [Conectar-se a um cluster seguro usando o PowerShell](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) para se conectar a ele. Por exemplo: 

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

Em seguida, é possível executar outros comandos do PowerShell para trabalhar com esse cluster. Por exemplo, você pode executar [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) para mostrar uma lista de nós nesse cluster seguro.


Para remover o cluster, conecte-se ao nó no cluster em que você baixou o pacote do Service Fabric, abra uma linha de comando e vá até a pasta do pacote. Agora execute o seguinte comando:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> A configuração incorreta de certificado pode impedir que o cluster seja exibido durante a implantação. Para autodiagnosticar problemas de segurança, procure no grupo de Visualizador de Eventos **Logs Aplicativos e Serviços** > **Microsoft Service Fabric**.
> 
> 

