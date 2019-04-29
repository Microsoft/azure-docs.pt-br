---
title: Melhores práticas de segurança do Azure Service Fabric | Microsoft Docs
description: Este artigo fornece um conjunto de melhores práticas de segurança do Azure Service Fabric.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2019
ms.author: tomsh
ms.openlocfilehash: 8bafc4a95ca9af4567ed70c190a72f3b351da47c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611515"
---
# <a name="azure-service-fabric-security-best-practices"></a>Melhores práticas de segurança do Azure Service Fabric
Implantar um aplicativo no Azure é rápido, fácil e econômico. Antes de implantar seu aplicativo na nuvem em produção, examine a nossa lista de melhores práticas recomendadas e essenciais para a implementação de clusters seguros no seu aplicativo.

O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, implantação e gerenciamento de microsserviços escalonáveis e confiáveis. O Service Fabric resolve os desafios significativos de desenvolvimento e gerenciamento de aplicativos em nuvem. Desenvolvedores e administradores podem evitar problemas complexos de infraestrutura e se concentrarem na implementação de cargas de trabalho essenciais e exigentes que são escalonáveis, confiáveis e gerenciáveis.

Para cada prática recomendada, vamos explicar:

-   O que é a prática recomendada.
-   Por que você deve implementar a prática recomendada.
-   O que poderia acontecer se você não implementa a prática recomendada.
-   Como você deve implementar a prática recomendada.

Recomendamos as seguintes melhores práticas de segurança do Azure Service Fabric:

-   Use os modelo dos Azure Resource Manager e o módulo PowerShell do Service Fabric para criar clusters seguros.
-   Usar certificados X.509.
-   Configurar políticas de segurança.
-   Implementar a configuração de segurança de Reliable Actors.
-   Configurar o SSL para o Azure Service Fabric.
-   Usar segurança e isolamento de rede com o Azure Service Fabric.
-   Configurar o Azure Key Vault para segurança.
-   Atribuir usuários a funções.


## <a name="best-practices-for-securing-your-clusters"></a>Melhores práticas para proteger os clusters

Usar sempre um cluster seguro:
-   Implementar a segurança de cluster usando certificados.
-   Fornecer acesso para cliente (admin e somente leitura) usando o Azure Active Directory (Azure AD).

Usar implantações automatizadas:
-   Usar scripts para gerar, implantar e sobrepor segredos.
-   Armazenar os segredos no Azure Key Vault e usar o Azure AD para todos os outros acessos de cliente.
-   Exigir autenticação para acesso humano aos segredos.

Além disso, considere as opções de configuração a seguir:
-   Criar redes de perímetro (também conhecidas como zonas desmilitarizadas, DMZs e sub-redes filtradas) usando Grupos de Segurança de Rede (NSGs) do Azure.
-   Acessar máquinas virtuais (VMs) de cluster ou gerenciar o cluster usando servidores de salto com Conexão de Área de Trabalho Remota.

Os clusters devem ser protegidos para evitar que usuários não autorizados se conectem a eles, especialmente quando eles tiverem cargas de trabalho de produção em execução. Embora seja possível criar um cluster não seguro, usuários anônimos poderão se conectar ao seu cluster se ele expuser pontos de extremidade de gerenciamento na Internet pública.

Há três [cenários](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) para implementar a segurança de cluster usando diversas tecnologias:

-   Segurança nó a nó: Esse cenário protege a comunicação entre as VMs e os computadores do cluster. Esse tipo de segurança faz com que somente os computadores autorizados a ingressar no cluster possam hospedar aplicativos e serviços no cluster.
Nesse cenário, os clusters em execução no Azure ou clusters autônomos que sáo executados no Windows podem usar a [segurança de certificado](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) ou então a [segurança do Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security) para computadores Windows Server.
-   Segurança cliente a nó: Esse cenário protege a comunicação entre um cliente do Service Fabric e os nós individuais do cluster.
-   RBAC (Controle de Acesso Baseado em Função): Esse cenário usa identidades separadas (certificados, o Azure AD e assim por diante) para cada função de cliente de usuário e administrador que acessa o cluster. Especifique as identidades de função ao criar o cluster.

>[!NOTE]
>**Recomendação de segurança para clusters do Azure:** Use a segurança do Azure AD para autenticar clientes e certificados para a segurança nó a nó.

Para configurar o cluster do Windows autônomo, confira [Definir as configurações de cluster do Windows autônomo](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest).

Use os modelos do Azure Resource Manager e o módulo PowerShell do Service Fabric para criar um cluster seguro.
Para obter instruções passo a passo para criar um cluster do Service Fabric seguro usando modelos do Azure Resource Manager, consulte [Criar um cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Usar o modelo do Azure Resource Manager:
-   Personalize o seu cluster usando o modelo para configurar o armazenamento gerenciado para discos rígidos virtuais (VHDs) de VM.
-   Faça alterações no seu grupo de recursos usando o modelo para facilitar o gerenciamento de configuração e a auditoria.

Tratar a configuração do cluster como código:
-   Seja meticuloso(a) ao verificar as configurações de implantação.
-   Evite usar comandos implícitos para modificar diretamente seus recursos.

Muitos aspectos do [ciclo de vida de um aplicativo do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle) podem ser automatizados. O [módulo do PowerShell do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications#upload-the-application-package) automatiza tarefas comuns de implantação, atualização, remoção e teste de aplicativos do Service Fabric. APIs gerenciadas e APIs de HTTP para gerenciamento de aplicativos também estão disponíveis.

## <a name="use-x509-certificates"></a>Usar certificados X.509
Sempre proteja os seus clusters usando certificados X.509 ou a segurança do Windows. A segurança só é configurada no momento da criação do cluster. Não é possível ativar a segurança após a criação do cluster.

Para especificar um [certificado de cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security), defina o valor da propriedade **ClusterCredentialType** como X509. Se estiver especificando um certificado do servidor para conexões externas, defina a propriedade **ServerCredentialType** como X509.

Além disso, siga estas práticas:
-   Crie os certificados para clusters de produção usando um serviço de certificado do Windows Server configurado corretamente. Você também pode obter os certificados de uma autoridade de certificação aprovada (CA).
-   Nunca use um certificado de teste ou temporário para clusters de produção se o certificado tiver sido criado usando MakeCert.exe ou uma ferramenta semelhante.
-   Use um certificado autoassinado para clusters de teste, mas não para clusters de produção.

Se um cluster não for seguro, qualquer pessoa pode se conectar ao cluster de forma anônima e realizar operações de gerenciamento. Por isso, sempre proteja os clusters de produção usando certificados X.509 ou a segurança do Windows.

Para saber mais sobre como usar os certificados X.509, confira [Adicionar ou remover certificados para um cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure).

## <a name="configure-security-policies"></a>Configurar políticas de segurança
O Service Fabric também protege os recursos que são usados por aplicativos. Recursos, como arquivos, diretórios e certificados são armazenados em contas de usuário quando o aplicativo é implantado. Esse recurso torna os aplicativos em execução mais protegidos uns dos outros, mesmo em um ambiente hospedado compartilhado.

-   Usar um usuário ou um grupo de domínio do Active Directory: Execute o serviço com as credenciais de uma conta de usuário ou grupo do Active Directory. Certifique-se de usar o Active Directory local em seu domínio e não o Azure Active Directory. Acesse outros recursos do domínio que receberam permissões usando um usuário ou grupo de domínio. Por exemplo, recursos, como compartilhamentos de arquivos.

-   Atribua uma política de acesso de segurança a pontos de extremidade HTTP e HTTPS: Especifique a propriedade **SecurityAccessPolicy** para aplicar uma política **RunAs** a um serviço quando o manifesto do serviço declarar recursos de ponto de extremidade com HTTP. Portas alocadas para os pontos de extremidade HTTP são listas de acesso controlado corretamente para a conta de usuário RunAs onde o serviço é executado. Quando a política não é definida, o http.sys não possui acesso ao serviço e você receberá falhas em chamadas do cliente.

Para saber como usar políticas de segurança em um cluster do Service Fabric, confira [Configurar políticas de segurança para o seu aplicativo](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-runas-security).

## <a name="implement-the-reliable-actors-security-configuration"></a>Implementar a configuração de segurança de Reliable Actors
Os Reliable Actors do Service Fabric são uma implementação do padrão de design de ator. Assim como ocorre com qualquer padrão de design de software, a escolha de determinado padrão leva em conta se um problema relacionado ao software é adequado ao padrão ou não.

Em geral, use o padrão de design de ator para ajudar a modelar soluções para os seguintes problemas de software ou cenários de segurança:
-   Seu problema de espaço envolve um grande número (milhares ou milhões) de pequenas unidades de estado e lógica que, além de serem independentes, são isoladas.
-   Você está trabalhando com objetos single-threaded que não exigem interação significativa de componentes externos, incluindo a consulta de estado em um conjunto de atores.
-   Suas instâncias de ator não bloqueiam chamadores com atrasos imprevisíveis emitindo operações de E/S.

No Service Fabric atores são implementados na estrutura do aplicativo Reliable Actors. Essa estrutura é baseada no padrão de ator e criada sobre os [Reliable Services do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction). Cada serviço escrito do Reliable Actor é, de fato, um serviço confiável com estado particionado.

Cada ator é definido como uma instância de um tipo de ator, da mesma forma que um objeto do .NET é uma instância de um tipo do .NET. Por exemplo, um **tipo de ator** que implementa a funcionalidade de uma calculadora pode ter muitos atores desse tipo que são distribuídos em vários nós em um cluster. Cada um dos atores distribuídos é caracterizado exclusivamente por um identificador de ator.

As [configurações de segurança do replicador](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration) servem para proteger o canal de comunicação que é usado durante a replicação. Essa configuração impede os serviços de enxergar o tráfego de replicação uns dos outros e garante que os dados de alta disponibilidade fiquem seguros. Por padrão, uma seção de configuração de segurança vazia evita a segurança de replicação.
Configurações do replicador configuram o replicador que será responsável por tornar o Provedor de Estado do Ator altamente confiável.

## <a name="configure-ssl-for-azure-service-fabric"></a>Configurar o SSL para o Azure Service Fabric
O processo de autenticação do servidor [autentica](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) os pontos de extremidade de gerenciamento de cluster para um cliente de gerenciamento. O cliente de gerenciamento, em seguida, reconhece que ele está se comunicando com o cluster real. Esse certificado também fornece um [SSL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) para a API de gerenciamento de HTTPS e para o Service Fabric Explorer sobre HTTPS.
Você deve obter um nome de domínio personalizado para seu cluster. Quando você solicitar um certificado de uma autoridade de certificação, o nome de assunto do certificado deve corresponder ao nome de domínio personalizado usado para o seu cluster.

Para configurar SSL para um aplicativo, você precisa primeiro obter um certificado SSL que tenha sido assinado por uma autoridade de certificação. A autoridade de certificação é um terceiro confiável que emite certificados para fins de segurança SSL. Se você ainda não tiver um certificado SSL, você precisará obtê-lo junto a uma empresa que venda certificados SSL.

O certificado deve atender aos seguintes requisitos para certificados SSL no Azure:
-   O certificado deve conter uma chave privada.

-   O certificado deve ser criado para troca de chaves e ser exportável para um arquivo troca de informações pessoais (.pfx).

-   O nome de assunto do certificado deve corresponder ao nome de domínio usado para acessar o serviço de nuvem.

    - Você deve adquirir um nome de domínio personalizado para usar quando acessar o serviço de nuvem.
    - Solicite um certificado de uma autoridade de certificação com um nome de assunto que coincida com o nome de domínio personalizado do seu serviço. Por exemplo, se o nome de domínio personalizado for __contoso__**.com**, você pode solicitar um certificado da autoridade de certificação para **.contoso.com** ou __www__**.contoso.com**.

    >[!NOTE]
    >Você não pode obter um certificado SSL de uma autoridade de certificação para o domínio __cloudapp__**.net**.

-   O certificado deve usar, no mínimo, uma criptografia de 2.048 bits.

O protocolo HTTP não é seguro e está sujeito a ataques de interceptação. Dados que são transmitidos por HTTP são enviados como texto sem formatação no navegador da Web para o servidor Web ou entre outros pontos de extremidade. Os invasores podem interceptar e exibir dados confidenciais que são enviados via HTTP, como detalhes de cartão de crédito e logons de conta. Quando os dados são enviados ou postados em um navegador usando HTTPS, o protocolo SSL faz com que essas informações sejam criptografadas e fiquem protegidas contra interceptação.

Para saber mais sobre como usar certificados SSL, consulte [Configurar SSL para aplicativos do Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-configure-ssl-certificate).

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Usar segurança e isolamento de rede com o Azure Service Fabric
Configurar um cluster seguro 3 nodetype usando o [modelo do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) como exemplo. Controle o tráfego de rede de entrada e saída usando o modelo e os Grupos de Segurança de Rede.

O modelo tem um Grupo de Segurança de Rede para cada conjunto de dimensionamento de máquinas virtuais a fim de controlar o tráfego dentro e fora do conjunto. Por padrão, as regras são configuradas para permitir todo o tráfego necessário aos serviços do sistema e às portas de aplicativo especificadas no modelo. Examine essas regras e faça alterações de acordo com suas necessidades, incluindo adicionar novas regras para seus aplicativos.

Para saber mais, confira [Cenários comuns de rede para Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

## <a name="set-up-azure-key-vault-for-security"></a>Configurar o Azure Key Vault para segurança
O Service Fabric usa os certificados para fornecer autenticação e criptografia para proteger um cluster e seus aplicativos.

O Service Fabric usa certificados x.509 para proteger um cluster e fornecer recursos de segurança do aplicativo. O Azure Key Vault é usado para [gerenciar certificados](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure) de clusters do Service Fabric no Azure. O provedor de recursos do Azure que cria os clusters extrai os certificados de um cofre de chaves. O provedor, em seguida, instala os certificados nas máquinas virtuais quando o cluster for implantado no Azure.

Existe uma relação de certificado entre o [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), o cluster do Service Fabric e o provedor de recursos que usa os certificados. Quando o cluster for criado, as informações sobre a relação de certificado são armazenadas em um cofre de chaves.

Há duas etapas básicas para configurar um cofre de chaves:
1. Criar um grupo de recursos especificamente para o cofre de chaves.

    Recomendamos que você coloque o cofre de chaves em seu próprio grupo de recursos. Essa ação ajuda a evitar a perda de suas chaves e segredos se outros grupos de recursos forem removidos, como armazenamento, computação ou o grupo que contém o cluster. O grupo de recursos que contém o cofre de chaves deve estar na mesma região que o cluster que está sendo usado.

2. Crie um cofre de chaves no novo grupo de recursos.

    O cofre de chaves deve ser habilitado para a implantação. O provedor de recursos de computação, em seguida, pode obter os certificados do cofre e instalá-los em instâncias de VM.

Para saber mais sobre como configurar um cofre de chaves, confira [O que é o Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).

## <a name="assign-users-to-roles"></a>Atribuir usuários a funções
Depois de criar os aplicativos para representar seu cluster, atribua os usuários às funções com suporte no Service Fabric para leitura e administrador. É possível atribuir essas funções usando o Portal do Azure.

>[!NOTE]
> Para obter mais informações sobre como usar funções no Service Fabric, consulte [Controle de acesso baseado em função para clientes do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

O Azure Service Fabric dá suporte a dois tipos de controle de acesso diferentes para clientes conectados a um [cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm): administrador e usuário. O administrador do cluster pode usar o controle de acesso para limitar o acesso a determinadas operações de cluster para diferentes grupos de usuários. O controle de acesso torna o cluster mais seguro.

## <a name="next-steps"></a>Próximas etapas

- [Lista de verificação de segurança do Service Fabric](azure-service-fabric-security-checklist.md)
- Configurar o [ambiente de desenvolvimento](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started) do Service Fabric.
- Saiba mais sobre as [opções de suporte do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
