---
title: Proteger um cluster em execução no Windows usando a segurança do Windows
description: Saiba como configurar a segurança de nó para nó e de cliente para nó em um cluster autônomo em execução no Windows usando a Segurança do Windows.
ms.topic: conceptual
ms.date: 08/24/2017
ms.openlocfilehash: a34c7084a9faaf0d676d4f6c68da53b2bc84f01b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103574604"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Proteger um cluster autônomo no Windows usando a Segurança do Windows
Para evitar acesso não autorizado a um cluster do Service Fabric, você deve proteger o cluster. A segurança é especialmente importante quando o cluster executa cargas de trabalho de produção. Este artigo descreve como configurar a segurança de nó para nó e de cliente para nó usando a Segurança do Windows no arquivo *ClusterConfig.JSON*.  O processo corresponde à etapa configurar segurança de [Criar um cluster autônomo em execução no Windows](service-fabric-cluster-creation-for-windows-server.md). Para saber mais sobre como o Service Fabric usa a Segurança do Windows, veja [Cenários de segurança de cluster](service-fabric-cluster-security.md).

> [!NOTE]
> Você deve considerar a seleção de segurança de nó para nó com cuidado, já que não há nenhuma atualização de cluster de uma opção de segurança para outra. Para alterar a seleção de segurança, você precisa recompilar o cluster completo.
>
>

## <a name="configure-windows-security-using-gmsa"></a>Configurar a segurança do Windows usando gMSA  
gMSA é o modelo de segurança preferencial. O arquivo de configuração *ClusterConfig.gMSA.Windows.MultiMachine.JSON* de exemplo baixado com o pacote de clusters independentes [Microsoft.Azure.ServiceFabric.WindowsServer\<version>.zip](https://go.microsoft.com/fwlink/?LinkId=730690) contém um modelo para a configuração da segurança do Windows usando [Conta de Serviço Gerenciado por Grupo (gMSA)](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)):  

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {  
        "ClustergMSAIdentity": "[gMSA Identity]",
        "ClusterSPN": "[Registered SPN for the gMSA account]",
        "ClientIdentities": [
            {
                "Identity": "domain\\username",
                "IsAdmin": true
            }
        ]
    }
}
```

| **Definição de configuração** | **Descrição** |
| --- | --- |
| ClusterCredentialType |Definido como *Windows* para habilitar a Segurança do Windows para comunicação de nó a nó.  | 
| ServerCredentialType |Definido como *Windows* para habilitar a Segurança do Windows para comunicação de cliente a nó. |
| WindowsIdentities |Contém as identidades do cluster e do cliente. |
| ClustergMSAIdentity |Configura a segurança de nó para nó. Uma conta de serviço gerenciado de grupo. |
| ClusterSPN |SPN registrado para a conta gMSA|
| ClientIdentities |Configura a segurança de cliente para nó. Uma matriz de contas de usuário do cliente. |
| Identidade |Adicione o usuário de domínio, domínio\nomedeusuário, à identidade do cliente. |
| IsAdmin |Defina como true para especificar que o usuário de domínio tem acesso de cliente de administrador ou false para acesso de cliente de usuário. |

> [!NOTE]
> O valor de ClustergMSAIdentity deve estar no formato " mysfgmsa@mydomain ".

[A segurança de nó para nó](service-fabric-cluster-security.md#node-to-node-security) é configurada definindo **ClustergMSAIdentity** quando o Service Fabric precisa ser executado em gMSA. Para criar as relações de confiança entre os nós, eles deverão estar cientes uns dos outros. Isso pode ser feito de duas maneiras diferentes: especifique a conta de serviço gerenciada por grupo que inclui todos os nós no cluster, ou especifique o grupo de máquina de domínio que inclui todos os nós no cluster. É altamente recomendável usar a abordagem de [gMSA (Conta de Serviço Gerenciado de Grupo)](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)) , especialmente para clusters maiores (com mais de 10 nós) ou para clusters com probabilidade de aumentar ou reduzir.  
Essa abordagem não exige a criação de um grupo de domínios para o qual os administradores de cluster receberam direitos de acesso para adicionar e remover membros. Essas contas também são úteis para gerenciamento automático de senha. Para obter mais informações, confira [Introdução a contas de serviços gerenciados de grupo](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11)).  
 
[A segurança de cliente para nó](service-fabric-cluster-security.md#client-to-node-security) é configurada usando **ClientIdentities**. Para estabelecer a confiança entre um cliente e o cluster, você deverá configurar o cluster para saber em quais identidades de cliente ele poderá confiar. Isso pode ser feito de duas maneiras diferentes: especifique os usuários do grupo de domínio que podem se conectar ou especifique os usuários de nó do domínio que podem se conectar. O Service Fabric oferece suporte a dois tipos de controle de acesso diferentes para clientes conectados a um cluster do Service Fabric: administrador e usuário. O controle de acesso oferece a capacidade para que o administrador de cluster limite o acesso a determinados tipos de operação de cluster para diferentes grupos de usuários, tornando o cluster mais seguro.  Os administradores têm acesso completo aos recursos de gerenciamento (incluindo recursos de leitura/gravação). Os usuários, por padrão, têm apenas acesso de leitura aos recursos de gerenciamento (por exemplo, recursos de consulta) e a capacidade de resolver serviços e aplicativos. Para saber mais sobre controles de acesso, veja [Controle de acesso baseado em função para clientes do Service Fabric](service-fabric-cluster-security-roles.md).  
 
A seção de **segurança** do exemplo a seguir configura a segurança do Windows usando gMSA e especifica que os computadores no gMSA *ServiceFabric/clusterA.contoso.com* fazem parte do cluster e que *CONTOSO\usera* tem acesso de cliente do administrador:  
  
```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClustergMSAIdentity" : "ServiceFabric.clusterA.contoso.com",
        "ClusterSPN" : "http/servicefabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
}
```
  
## <a name="configure-windows-security-using-a-machine-group"></a>Configurar a segurança do Windows usando um grupo de máquinas  
Conforme detalhado acima, gMSA é preferencial, mas também tem suporte para usar esse modelo de segurança. O arquivo de configuração *ClusterConfig.Windows.MultiMachine.JSON* de exemplo baixado com o pacote de clusters independentes [Microsoft.Azure.ServiceFabric.WindowsServer\<version>.zip](https://go.microsoft.com/fwlink/?LinkId=730690) contém um modelo para a configuração da segurança do Windows.  A segurança do Windows é configurada na seção **Propriedades** : 

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "[domain\machinegroup]",
        "ClientIdentities": [{
            "Identity": "[domain\username]",
            "IsAdmin": true
        }]
    }
}
```

| **Definição de configuração** | **Descrição** |
| --- | --- |
| ClusterCredentialType |Definido como *Windows* para habilitar a Segurança do Windows para comunicação de nó a nó.  |
| ServerCredentialType |Definido como *Windows* para habilitar a Segurança do Windows para comunicação de cliente a nó. |
| WindowsIdentities |Contém as identidades do cluster e do cliente. |
| ClusterIdentity |Use um nome de grupo de computadores, domínio\grupodecomputadores, para configurar a segurança de nó para nó. |
| ClientIdentities |Configura a segurança de cliente para nó. Uma matriz de contas de usuário do cliente. |  
| Identidade |Adicione o usuário de domínio, domínio\nomedeusuário, à identidade do cliente. |  
| IsAdmin |Defina como true para especificar que o usuário de domínio tem acesso de cliente de administrador ou false para acesso de cliente de usuário. |  

[A segurança de nó para nó](service-fabric-cluster-security.md#node-to-node-security) é definida por meio da configuração usando **ClusterIdentity** se você quiser usar um grupo de computadores dentro de um domínio do Active Directory. Para saber mais, confira [Criar um grupo de máquinas no Active Directory](/previous-versions/commerce-server/aa545347(v=cs.70)).

[A segurança de cliente para nó](service-fabric-cluster-security.md#client-to-node-security) é configurada usando **ClientIdentities**. Para estabelecer a confiança entre um cliente e o cluster, você deverá configurar o cluster para saber em quais identidades de cliente ele poderá confiar. Você pode estabelecer confiança de duas maneiras diferentes:

- Especificar os usuários do grupo de domínio que podem se conectar.
- Especificar os usuários do nó de domínio que podem se conectar.

O Service Fabric oferece suporte a dois tipos de controle de acesso diferentes para clientes conectados a um cluster do Service Fabric: administrador e usuário. O controle de acesso permite que o administrador de cluster limite o acesso a determinados tipos de operação de cluster para diferentes grupos de usuários, tornando o cluster mais seguro.  Os administradores têm acesso completo aos recursos de gerenciamento (incluindo recursos de leitura/gravação). Os usuários, por padrão, têm apenas acesso de leitura aos recursos de gerenciamento (por exemplo, recursos de consulta) e a capacidade de resolver serviços e aplicativos.  

A seção de **segurança** do exemplo a seguir configura a segurança do Windows e especifica que os computadores em *ServiceFabric/clusterA.contoso.com* fazem parte do cluster e especifica que *CONTOSO\usera* tem acesso de cliente do administrador:

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
},
```

> [!NOTE]
> O Service Fabric não devem ser implantado em um controlador de domínio. Certifique-se de que ClusterConfig.json não inclui o endereço IP do controlador de domínio ao usar um grupo de computadores ou gMSA (grupo da conta de serviço gerenciado).
>
>

## <a name="next-steps"></a>Próximas etapas
Depois de configurar a segurança do Windows no arquivo *ClusterConfig.JSON* , retome o processo de criação de cluster em [Criar um cluster autônomo em execução no Windows](service-fabric-cluster-creation-for-windows-server.md).

Para saber mais sobre a segurança de nó para nó, a segurança de cliente para nó e o controle de acesso baseado em função, consulte [Cenários de segurança de cluster](service-fabric-cluster-security.md).

Consulte [Conectar a um cluster seguro](service-fabric-connect-to-secure-cluster.md) para obter exemplos de conexão usando o PowerShell ou o FabricClient.
