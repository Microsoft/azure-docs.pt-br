---
title: Proteger um cluster em execução no Windows usando a Segurança do Windows | Microsoft Docs
description: Saiba como configurar a segurança de nó para nó e de cliente para nó em um cluster autônomo em execução no Windows usando a Segurança do Windows.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ce3bf686-ffc4-452f-b15a-3c812aa9e672
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/24/2017
ms.author: dekapur
ms.openlocfilehash: 394ba3b3b8189bbe96137e920745f7b8cdd1cd95
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60863956"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Proteger um cluster autônomo no Windows usando a Segurança do Windows
Para evitar acesso não autorizado a um cluster do Service Fabric, você deve proteger o cluster. A segurança é especialmente importante quando o cluster executa cargas de trabalho de produção. Este artigo descreve como configurar a segurança de nó para nó e de cliente para nó usando a Segurança do Windows no arquivo *ClusterConfig.JSON*.  O processo corresponde à etapa configurar segurança de [Criar um cluster autônomo em execução no Windows](service-fabric-cluster-creation-for-windows-server.md). Para saber mais sobre como o Service Fabric usa a Segurança do Windows, veja [Cenários de segurança de cluster](service-fabric-cluster-security.md).

> [!NOTE]
> Você deve considerar a seleção de segurança de nó para nó com cuidado, já que não há nenhuma atualização de cluster de uma opção de segurança para outra. Para alterar a seleção de segurança, você precisa recompilar o cluster completo.
>
>

## <a name="configure-windows-security-using-gmsa"></a>Configurar a segurança do Windows usando gMSA  
O exemplo *Clusterconfig* arquivo de configuração baixado com o [WindowsServer.\< versão >. zip](https://go.microsoft.com/fwlink/?LinkId=730690) pacote autônomo do cluster contém um modelo para a configuração de segurança do Windows usando [conta de serviço gerenciado de grupo (gMSA)](https://technet.microsoft.com/library/hh831782.aspx):  

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

| **Parâmetro de configuração** | **Descrição** |
| --- | --- |
| ClusterCredentialType |Defina como *Windows* para ativar a segurança do Windows para a comunicação do nó de nó.  | 
| ServerCredentialType |Definido como *Windows* para habilitar a Segurança do Windows para comunicação de cliente a nó. |
| WindowsIdentities |Contém as identidades do cluster e do cliente. |
| ClustergMSAIdentity |Configura a segurança de nó para nó. Uma conta de serviço gerenciado de grupo. |
| ClusterSPN |SPN registrado para a conta gMSA|
| ClientIdentities |Configura a segurança de cliente para nó. Uma matriz de contas de usuário do cliente. |
| Identidade |Adicione o usuário de domínio, domínio\nomedeusuário, à identidade do cliente. |
| IsAdmin |Defina como true para especificar que o usuário de domínio tem acesso de cliente de administrador ou false para acesso de cliente de usuário. |

> [!NOTE]
> O valor de ClustergMSAIdentity não pode incluir o nome de domínio e só pode ser o nome de conta de serviço gerenciado de grupo. I.E. "mysfgmsa" está correto e ambos "mydomain / / mysfgmsa" ou "mysfgmsa@mydomain" são inválidos; porque o domínio é insinuado pela máquina host.

[A segurança entre nós](service-fabric-cluster-security.md#node-to-node-security) é configurada definindo **ClustergMSAIdentity** quando a malha do serviço precisar ser executada em gMSA. Para criar as relações de confiança entre os nós, eles deverão estar cientes uns dos outros. Isso pode ser feito de duas maneiras diferentes: Especifique o grupo de conta de serviço gerenciado que inclui todos os nós no cluster ou o grupo de computador do domínio que inclui todos os nós no cluster. É altamente recomendável usar a abordagem de [gMSA (Conta de Serviço Gerenciado de Grupo)](https://technet.microsoft.com/library/hh831782.aspx) , especialmente para clusters maiores (com mais de 10 nós) ou para clusters com probabilidade de aumentar ou reduzir.  
Essa abordagem não exige a criação de um grupo de domínios para o qual os administradores de cluster receberam direitos de acesso para adicionar e remover membros. Essas contas também são úteis para gerenciamento automático de senha. Para obter mais informações, confira [Introdução a contas de serviços gerenciados de grupo](https://technet.microsoft.com/library/jj128431.aspx).  
 
[Segurança de cliente para nó](service-fabric-cluster-security.md#client-to-node-security) é configurada usando **ClientIdentities**. Para estabelecer a confiança entre um cliente e o cluster, você deverá configurar o cluster para saber em quais identidades de cliente ele poderá confiar. Isso pode ser feito de duas maneiras diferentes: Especifique os usuários do grupo de domínio que podem se conectar ou especifique os usuários de nó de domínio que podem se conectar. O Service Fabric oferece suporte a dois tipos de controle de acesso diferentes para clientes conectados a um cluster do Service Fabric: administrador e usuário. O controle de acesso oferece a capacidade para que o administrador de cluster limite o acesso a determinados tipos de operação de cluster para diferentes grupos de usuários, tornando o cluster mais seguro.  Os administradores têm acesso completo aos recursos de gerenciamento (incluindo recursos de leitura/gravação). Os usuários, por padrão, têm apenas acesso de leitura aos recursos de gerenciamento (por exemplo, recursos de consulta) e a capacidade de resolver serviços e aplicativos. Para saber mais sobre controles de acesso, veja [Controle de acesso baseado em função para clientes do Service Fabric](service-fabric-cluster-security-roles.md).  
 
A seção **security** do exemplo a seguir configura a segurança do Windows usando o gMSA e especifica que as máquinas no *ServiceFabric.clusterA.contoso.com* gMSA fazem parte do cluster e que *CONTOSO \ usera* tem acesso do cliente admin:  
  
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
Esse modelo está sendo preterido. Recomenda-se usar o gMSA conforme descrito acima. O exemplo *Multimachine* arquivo de configuração baixado com o [WindowsServer.\< versão >. zip](https://go.microsoft.com/fwlink/?LinkId=730690) pacote autônomo do cluster contém um modelo para configurar a segurança do Windows.  A segurança do Windows é configurada na seção **Propriedades** : 

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

| **Parâmetro de configuração** | **Descrição** |
| --- | --- |
| ClusterCredentialType |Defina como *Windows* para ativar a segurança do Windows para a comunicação do nó de nó.  |
| ServerCredentialType |Definido como *Windows* para habilitar a Segurança do Windows para comunicação de cliente a nó. |
| WindowsIdentities |Contém as identidades do cluster e do cliente. |
| ClusterIdentity |Use um nome de grupo de computadores, domínio\grupodecomputadores, para configurar a segurança de nó para nó. |
| ClientIdentities |Configura a segurança de cliente para nó. Uma matriz de contas de usuário do cliente. |  
| Identidade |Adicione o usuário de domínio, domínio\nomedeusuário, à identidade do cliente. |  
| IsAdmin |Defina como true para especificar que o usuário de domínio tem acesso de cliente de administrador ou false para acesso de cliente de usuário. |  

[A segurança entre nós](service-fabric-cluster-security.md#node-to-node-security) é definida usando a configuração **ClusterIdentity**, se você quiser usar um grupo de máquinas em um Domínio do Active Directory. Para saber mais, confira [Criar um grupo de máquinas no Active Directory](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx).

A [segurança de cliente para nó](service-fabric-cluster-security.md#client-to-node-security) é configurada usando **ClientIdentities**. Para estabelecer a confiança entre um cliente e o cluster, você deverá configurar o cluster para saber em quais identidades de cliente ele poderá confiar. Você pode estabelecer confiança de duas maneiras diferentes:

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
