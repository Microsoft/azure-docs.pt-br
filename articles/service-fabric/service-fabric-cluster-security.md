---
title: Proteger um cluster do Azure Service Fabric | Microsoft Docs
description: "Saiba mais sobre cenários de segurança para um cluster do Azure Service Fabric e as diversas tecnologias que você pode usar para implementá-los."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 26b58724-6a43-4f20-b965-2da3f086cf8a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: chackdan
ms.openlocfilehash: e75929ee5d3f57af77c66910cc294a7c0fb6629a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-cluster-security-scenarios"></a>Cenários de segurança do cluster do Service Fabric
Um cluster do Azure Service Fabric é um recurso que pertence a você. Você deve proteger os clusters para ajudar a impedir que usuários não autorizados se conectem a eles. Um cluster seguro é especialmente importante quando você está executando cargas de trabalho de produção no cluster. Embora seja possível criar um cluster não seguro, se ele expuser pontos de extremidade de gerenciamento na Internet pública, usuários anônimos poderão se conectar a ele. 

Este artigo é uma visão geral dos cenários de segurança para clusters do Azure e clusters autônomos, bem como das diversas tecnologias que você pode usar para implementá-los:

* Segurança de nó para nó
* Segurança de cliente para nó
* RBAC (Controle de Acesso Baseado em Função)

## <a name="node-to-node-security"></a>Segurança de nó para nó
A segurança de nó para nó ajuda a proteger a comunicação entre as VMs ou os computadores em um cluster. Esse cenário de segurança faz com que somente os computadores autorizados a ingressar no cluster possam hospedar aplicativos e serviços no cluster.

![Diagrama de comunicação de nó para nó][Node-to-Node]

Os clusters executados em clusters do Azure e clusters autônomos em execução no Windows podem usar a [Segurança de Certificado](https://msdn.microsoft.com/library/ff649801.aspx) ou então a [Segurança do Windows](https://msdn.microsoft.com/library/ff649396.aspx) para computadores Windows Server.

### <a name="node-to-node-certificate-security"></a>Segurança de certificado de nó para nó
O Service Fabric usa os certificados do servidor X.509 que você especifica como parte da configuração de tipo de nó ao criar um cluster. Confira no final deste artigo uma visão geral rápida do que são esses certificados e como você pode adquiri-los ou criá-los.

Configure a segurança de certificado durante a criação do cluster por meio do Portal do Azure, do uso de um modelo do Azure Resource Manager ou do uso de um modelo JSON autônomo. Você pode definir um certificado primário e um certificado secundário opcional, que é usado para substituições de certificado. Os certificados primários e secundários que você definir deverão ser diferentes dos certificados de cliente administrador e certificados cliente somente leitura definidos para a [segurança de cliente para nó](#client-to-node-security).

Para saber como configurar a segurança de certificado em um cluster para o Azure, consulte [Configurar um cluster usando um modelo do Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Para saber como configurar a segurança de certificado em um cluster para um cluster do Windows Server autônomo, consulte [Proteger um cluster autônomo no Windows usando certificados X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="node-to-node-windows-security"></a>Segurança do Windows de nó para nó
Para saber como configurar a segurança do Windows para um cluster do Windows Server autônomo, consulte [Proteger um cluster autônomo no Windows usando a segurança do Windows](service-fabric-windows-cluster-windows-security.md).

## <a name="client-to-node-security"></a>Segurança de cliente para nó
A segurança de cliente para nó autentica clientes e ajuda a proteger a comunicação entre um cliente e nós individuais no cluster. Esse tipo de segurança ajuda a assegurar que somente usuários autorizados possam acessar o cluster e os aplicativos implantados no cluster. Os clientes são identificados exclusivamente por meio de suas credenciais de segurança do Windows ou pelas credenciais de segurança do certificado deles.

![Diagrama de comunicação de cliente para nó][Client-to-Node]

Os clusters em execução no Azure e clusters autônomos em execução no Windows podem usar a [segurança de certificado](https://msdn.microsoft.com/library/ff649801.aspx) ou então a [segurança do Windows](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>Segurança de certificado de cliente para nó
Configure a segurança de certificado de cliente para nó durante a criação do cluster por meio do Portal do Azure, do uso de um modelo do Resource Manager ou do uso de um modelo JSON autônomo. Para criar o certificado, especifique um certificado do cliente administrador e/ou um certificado do cliente do usuário. Como melhor prática, os certificados do cliente administrador e certificados do cliente de usuário que você especificar deverão ser diferentes dos certificados primários e secundários que você especificar para a [segurança de nó para nó](#node-to-node-security). Por padrão, os certificados de cluster para segurança de nó para nó são adicionados à lista de certificados de administrador do cliente permitidos.

Clientes que se conectam ao cluster usando o certificado de administrador têm acesso completo aos recursos de gerenciamento. Clientes que se conectam ao cluster usando o certificado de cliente de usuário somente leitura têm somente acesso de leitura aos recursos de gerenciamento. Esses certificados são usados para o RBAC descrito posteriormente neste artigo.

Para saber como configurar a segurança de certificado em um cluster para o Azure, consulte [Configurar um cluster usando um modelo do Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Para saber como configurar a segurança de certificado em um cluster para um cluster do Windows Server autônomo, consulte [Proteger um cluster autônomo no Windows usando certificados X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Segurança do Azure Active Directory de cliente para nó no Azure
Para clusters em execução no Azure, você também pode proteger o acesso aos pontos de extremidade de gerenciamento usando o Azure AD (Azure Active Directory). Para aprender como criar os artefatos do Azure AD necessários, como populá-los durante a criação do cluster e como se conectar-se a esses clusters posteriormente, consulte [Configurar um cluster usando um modelo do Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="security-recommendations"></a>Recomendações de segurança
Para clusters do Azure, para segurança de nó para nó, é recomendável usar a segurança do Azure AD para autenticar clientes e certificados.

Para clusters do Windows Server autônomos, se você tem o Windows Server 2012 R2 e o Active Directory, é recomendável usar a segurança do Windows com contas de serviço gerenciado de grupo. Caso contrário, use a segurança do Windows com contas do Windows.

## <a name="role-based-access-control-rbac"></a>RBAC (Controle de Acesso Baseado em Função)
Você pode usar o controle de acesso para limitar o acesso a determinadas operações de cluster para diferentes grupos de usuários. Isso ajuda a tornar o cluster mais seguro. Dois tipos de controle de acesso têm suporte para clientes que se conectam a um cluster: função de Administrador e função de Usuário.

Os usuários aos quais é atribuída a função de Administrador têm acesso completo às funcionalidades de gerenciamento (incluindo funcionalidades de leitura/gravação). Os usuários aos quais é atribuída a função de Usuário, por padrão, têm apenas acesso de leitura às funcionalidades de gerenciamento (por exemplo, funcionalidades de consulta). Eles também podem resolver aplicativos e serviços.

Defina as funções de cliente de Administrador e de Usuário quando criar o cluster. Atribua as funções fornecendo identidades separadas (por exemplo, usando certificados ou o Azure AD) para cada tipo de função. Para saber mais sobre as configurações de controle de acesso padrão e como alterar as configurações padrão, consulte [Controle de Acesso Baseado em Função para clientes do Service Fabric](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>Certificados X.509 e Service Fabric
Os certificados digitais X.509 são usados normalmente para autenticar clientes e servidores. Eles também são usados para criptografar e assinar digitalmente as mensagens. Para obter mais informações sobre certificados digitais X.509, consulte [Trabalhar com certificados](http://msdn.microsoft.com/library/ms731899.aspx).

Algumas coisas importantes a considerar:

* Para criar certificados para clusters que executam cargas de trabalho de produção, use um serviço de certificado do Windows Server configurado corretamente ou um obtido por meio de uma [AC (Autoridade de Certificação)](https://en.wikipedia.org/wiki/Certificate_authority) aprovada.
* Certificado de teste ou temporário criados usando ferramentas como MakeCert.exe nunca devem ser usados em um ambiente de produção.
* Você pode usar um certificado autoassinado, mas somente em um cluster de teste. Não use um certificado autoassinado em produção.

### <a name="server-x509-certificates"></a>Certificados X.509 do servidor
Os certificados de servidor têm a tarefa principal de autenticar o servidor (nó) para clientes ou autenticar um servidor (nó) para um servidor (nó). Quando um cliente ou um nó autentica um nó, uma das verificações iniciais é a do valor do nome comum no campo **Entidade**. Esse nome comum ou um dos SANs (nomes alternativos da entidade) dos certificados deve estar presente na lista de nomes comuns permitidos.

Para saber como gerar certificados que têm SANs, consulte [Como adicionar um nome alternativo da entidade a um certificado LDAP seguro](http://support.microsoft.com/kb/931351).

O campo **Entidade** pode ter vários valores. Cada valor é prefixado com uma inicialização para indicar o tipo de valor. Geralmente, a inicialização é **CN** (de *nome comum*), por exemplo, **CN = www.contoso.com**. O campo **Entidade** pode ficar em branco. Se o campo opcional **Nome Alternativo da Entidade** estiver populado, ele deverá conter o nome comum do certificado e também uma entrada por SAN. Eles são inseridos como valores de **Nome DNS**.

O valor do campo **Finalidades Pretendidas** do certificado deve incluir um valor apropriado, como **Autenticação do Servidor** ou **Autenticação do Cliente**.

### <a name="client-x509-certificates"></a>Certificados X.509 do cliente
Normalmente, os certificados do cliente não são emitidos por uma AC de terceiros. Em vez disso, o repositório Pessoal do local atual do usuário geralmente contém os certificados colocados lá por uma autoridade raiz, com um valor de **Finalidades Pretendidas** de **Autenticação do Cliente**. O cliente pode usar esse certificado quando a autenticação mútua é necessária.

> [!NOTE]
> Todas as operações de gerenciamento no cluster do Service Fabric exigem certificados de servidor. Certificados de cliente não podem ser usados para gerenciamento.

## <a name="next-steps"></a>Próximas etapas
* [Criar um cluster no Azure usando um modelo do Resource Manager](service-fabric-cluster-creation-via-arm.md) 
* [Criar um cluster usando o Portal do Azure](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png
