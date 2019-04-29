---
title: Proteger um cluster do Azure Service Fabric | Microsoft Docs
description: Saiba mais sobre cenários de segurança para um cluster do Azure Service Fabric e as diversas tecnologias que você pode usar para implementá-los.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 26b58724-6a43-4f20-b965-2da3f086cf8a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/14/2018
ms.author: aljo
ms.openlocfilehash: 6d67fa4af031480fda4a91f7356bff69830a654c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711470"
---
# <a name="service-fabric-cluster-security-scenarios"></a>Cenários de segurança do cluster do Service Fabric
Um cluster do Azure Service Fabric é um recurso que pertence a você. É sua responsabilidade proteger os clusters para ajudar a impedir que usuários não autorizados se conectem a eles. Um cluster seguro é especialmente importante quando você está executando cargas de trabalho de produção no cluster. Embora seja possível criar um cluster não seguro, se ele expuser pontos de extremidade de gerenciamento na Internet pública, usuários anônimos poderão se conectar a ele. Clusters desprotegidos não são compatíveis com cargas de trabalho de produção. 

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

Configure a segurança de certificado durante a criação do cluster por meio do Portal do Azure, do uso de um modelo do Azure Resource Manager ou do uso de um modelo JSON autônomo. O comportamento padrão do SDK do Service Fabric é implantar e instalar o certificado com o mais distante no futuro certificado de expiração; o comportamento clássico permitia a definição de certificados primários e secundários, permitir rollovers iniciados manualmente e não é recomendado para uso na nova funcionalidade. Os certificados primários que serão utilizados possuem a data de expiração mais longa no futuro, devem ser diferentes dos certificados de cliente admin e de leitura somente que você definiu para [segurança de cliente para nó](#client-to-node-security).

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
O Azure AD permite que as organizações (conhecidas como locatários) gerenciem o acesso dos usuários aos aplicativos. Os aplicativos são divididos entre os que têm IU de entrada na Web e os que têm experiência de cliente nativa. Se você ainda não criou um locatário, comece lendo [Como obter um locatário do Active Directory do Azure][active-directory-howto-tenant].

Os clusters do Service Fabric oferecem vários pontos de entrada para a funcionalidade de gerenciamento, incluindo o [Service Fabric Explorer][service-fabric-visualizing-your-cluster] online o [Visual Studio][service-fabric-manage-application-in-visual-studio]. Como resultado, você criará dois aplicativos do Azure AD para controlar o acesso ao cluster, um aplicativo Web e um aplicativo nativo.

Para clusters em execução no Azure, você também pode proteger o acesso aos pontos de extremidade de gerenciamento usando o Azure AD (Azure Active Directory). Para aprender como criar os artefatos necessários do Azure AD e como preenchê-los ao criar o cluster, consulte [Configurar o Azure AD para autenticar clientes](service-fabric-cluster-creation-setup-aad.md).

## <a name="security-recommendations"></a>Recomendações de segurança
Para clusters do Service Fabric implantados em uma rede pública hospedada no Azure, a recomendação para a autenticação mútua do cliente para nó é:
*   Usar o Azure Active Directory para identidade de cliente
*   Um certificado para identidade do servidor e criptografia SSL da comunicação HTTP

Para clusters do Service Fabric implantados em uma rede pública hospedada no Azure, a recomendação para a segurança de nó para nó é usar um certificado de Cluster para autenticar nós. 


Para clusters do Windows Server autônomos, se você tem o Windows Server 2012 R2 e o Active Directory, é recomendável usar a segurança do Windows com contas de serviço gerenciado de grupo. Caso contrário, use a segurança do Windows com contas do Windows.

## <a name="role-based-access-control-rbac"></a>RBAC (Controle de Acesso Baseado em Função)
Você pode usar o controle de acesso para limitar o acesso a determinadas operações de cluster para diferentes grupos de usuários. Isso ajuda a tornar o cluster mais seguro. Dois tipos de controle de acesso têm suporte para clientes que se conectam a um cluster: Função de administrador e função de usuário.

Os usuários aos quais é atribuída a função de Administrador têm acesso completo às funcionalidades de gerenciamento (incluindo funcionalidades de leitura/gravação). Os usuários aos quais é atribuída a função de Usuário, por padrão, têm apenas acesso de leitura às funcionalidades de gerenciamento (por exemplo, funcionalidades de consulta). Eles também podem resolver aplicativos e serviços.

Defina as funções de cliente de Administrador e de Usuário quando criar o cluster. Atribua as funções fornecendo identidades separadas (por exemplo, usando certificados ou o Azure AD) para cada tipo de função. Para saber mais sobre as configurações de controle de acesso padrão e como alterar as configurações padrão, consulte [Controle de Acesso Baseado em Função para clientes do Service Fabric](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>Certificados X.509 e Service Fabric
Os certificados digitais X.509 são usados normalmente para autenticar clientes e servidores. Eles também são usados para criptografar e assinar digitalmente as mensagens. O Service Fabric usa certificados x.509 para proteger um cluster e fornecer recursos de segurança do aplicativo. Para obter mais informações sobre certificados digitais X.509, consulte [Trabalhar com certificados](https://msdn.microsoft.com/library/ms731899.aspx). Você usa [Key Vault](../key-vault/key-vault-overview.md) para gerenciar certificados para clusters do Service Fabric no Azure.

Algumas coisas importantes a considerar:

* Para criar certificados para clusters que executam cargas de trabalho de produção, use um serviço de certificado do Windows Server configurado corretamente ou um obtido por meio de uma [AC (Autoridade de Certificação)](https://en.wikipedia.org/wiki/Certificate_authority) aprovada.
* Certificado de teste ou temporário criados usando ferramentas como MakeCert.exe nunca devem ser usados em um ambiente de produção.
* Você pode usar um certificado autoassinado, mas somente em um cluster de teste. Não use um certificado autoassinado em produção.
* Ao gerar a impressão digital do certificado, certifique-se de gerar uma impressão digital SHA1. SHA1 é o que é usado ao configurar as impressões digitais do certificado Client e Cluster.

### <a name="cluster-and-server-certificate-required"></a>Certificado de cluster e de servidor (necessário)
Esses certificados (um primário e, opcionalmente, um secundário) são necessárias para proteger um cluster e impedir o acesso não autorizado a ele. Esses certificados fornecem autenticação de cluster e servidor.

A autenticação de cluster autentica a comunicação de nó a nó para federação de cluster. Somente os nós que podem provar sua identidade com esse certificado podem ingressar no cluster. A autenticação do servidor autentica os pontos de extremidade de gerenciamento do cluster para um cliente de gerenciamento, para que o cliente de gerenciamento saiba que está falando com o cluster real e não com um 'man in the middle'. Esse certificado também fornece um SSL para a API de gerenciamento de HTTPS e para o Service Fabric Explorer sobre HTTPS. Quando um cliente ou um nó autentica um nó, uma das verificações iniciais é a do valor do nome comum no campo **Entidade**. Esse nome comum ou um dos SANs (nomes alternativos da entidade) dos certificados deve estar presente na lista de nomes comuns permitidos.

O certificado deve atender aos seguintes requisitos:

* O certificado deve conter uma chave privada. Esses certificados geralmente têm extensões .pfx ou .pem  
* O certificado deve ser criado para troca de chaves, que deve ser exportável para um arquivo Troca de Informações Pessoais (.pfx).
* O **nome da entidade do certificado deve corresponder ao domínio usado para acessar o cluster do Service Fabric**. Essa correspondência é necessária para fornecer um SSL para os pontos de extremidade de gerenciamento de HTTPS e o Service Fabric Explorer do cluster. Você não pode obter um certificado SSL de uma AC (autoridade de certificação) para o domínio *.cloudapp.azure.com. Você deve obter um nome de domínio personalizado para seu cluster. Quando você solicitar um certificado de uma autoridade de certificação, o nome de assunto do certificado deve corresponder ao nome de domínio personalizado usado para seu cluster.

Algumas outras coisas a considerar:

* O campo **Entidade** pode ter vários valores. Cada valor é prefixado com uma inicialização para indicar o tipo de valor. Geralmente, é a inicialização **CN** (para *nome comum*); por exemplo, **CN = www\.contoso.com**. 
* O campo **Entidade** pode ficar em branco. 
* Se o campo opcional **Nome Alternativo da Entidade** estiver populado, ele deverá conter o nome comum do certificado e também uma entrada por SAN. Eles são inseridos como valores de **Nome DNS**. Para saber como gerar certificados que têm SANs, consulte [Como adicionar um nome alternativo da entidade a um certificado LDAP seguro](https://support.microsoft.com/kb/931351).
* O valor do campo **Finalidades Pretendidas** do certificado deve incluir um valor apropriado, como **Autenticação do Servidor** ou **Autenticação do Cliente**.

### <a name="application-certificates-optional"></a>Certificados de aplicativo (opcionais)
Qualquer número de certificados adicionais pode ser instalado em um cluster para fins de segurança do aplicativo. Antes de criar o cluster, considere os cenários de segurança de aplicativos que exigem um certificado a ser instalado em nós, como:

* Criptografia e descriptografia de valores de configuração de aplicativo.
* Criptografia de dados entre nós durante a replicação.

O conceito para criar clusters seguros é o mesmo, sejam clusters do Linux ou do Windows.

### <a name="client-authentication-certificates-optional"></a>Certificados de autenticação de cliente (opcional)
Qualquer número de certificados adicionais pode ser especificado para operações do administrador ou do usuário. O cliente pode usar esse certificado quando a autenticação mútua é necessária. Normalmente, os certificados do cliente não são emitidos por uma AC de terceiros. Em vez disso, o armazenamento Pessoal do local atual do usuário geralmente contém certificados de cliente colocados lá por uma autoridade raiz. O certificado deve ter um **valor de Propósito Final** de **Autenticação do Cliente**.  

Por padrão, o certificado de cluster tem privilégios de cliente do administrador. Esses certificados de cliente adicionais não devem ser instalados no cluster, mas são especificados como permitidos na configuração do cluster.  No entanto, os certificados do cliente precisam ser instalados nas máquinas clientes para se conectar ao cluster e executar quaisquer operações.

> [!NOTE]
> Todas as operações de gerenciamento no cluster do Service Fabric exigem certificados de servidor. Certificados de cliente não podem ser usados para gerenciamento.

## <a name="next-steps"></a>Próximas etapas
* [Criar um cluster no Azure usando um modelo do Resource Manager](service-fabric-cluster-creation-via-arm.md) 
* [Criar um cluster usando o Portal do Azure](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
