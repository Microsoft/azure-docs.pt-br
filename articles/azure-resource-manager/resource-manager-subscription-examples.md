---
title: "Cenários e exemplos de governança de assinatura | Microsoft Docs"
description: "Fornece exemplos de como implementar a governança de assinatura do Azure para cenários comuns."
services: azure-resource-manager
documentationcenter: na
author: rdendtler
manager: timlt
editor: tysonn
ms.assetid: e8fbeeb8-d7a1-48af-804f-6fe1a6024bcb
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2017
ms.author: rodend;karlku;tomfitz
ms.openlocfilehash: 6e8335b9c2f3609bf0c48c563205ffaee8575b20
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="examples-of-implementing-azure-enterprise-scaffold"></a>Exemplos de implementação de scaffold do Azure Enterprise
Este tópico fornece exemplos de como uma empresa pode implementar as recomendações para um [scaffold do Azure Enterprise](resource-manager-subscription-governance.md). Ele usa uma empresa fictícia chamada Contoso para ilustrar as melhores práticas para cenários comuns.

## <a name="background"></a>Segundo plano
A Contoso é uma empresa mundial que fornece soluções de cadeia de fornecedores para clientes em tudo desde um modelo de "Software como Serviço" para um modelo de pacote implantado no local.  Eles desenvolvem software em todo o mundo, com centros de desenvolvimento significativos na Índia, Estados Unidos e Canadá.

A parte de ISV da empresa é dividida em várias unidades comerciais independentes que gerenciam produtos em um negócio significativo. Cada unidade de negócios tem seus próprios desenvolvedores, gerentes de produto e arquitetos.

A unidade de negócios de ETS (serviços de tecnologia empresarial) fornece funcionalidade de TI centralizada e gerencia vários data centers em que as unidades de negócios hospedam seus aplicativos. Juntamente com o gerenciamento de data centers, a organização ETS fornece e gerencia colaboração centralizada (como email e sites) e serviços de rede/telefonia. Eles também gerenciam cargas de trabalho voltadas para o cliente para unidades de negócios menores que não contam com equipe operacional.

As pessoas seguir são usados neste tópico:

* Dave é o administrador de ETS do Azure.
* Alice é diretora de desenvolvimento da Contoso na unidade de negócios de cadeia de fornecedores.

A Contoso precisa compilar um aplicativo de linha de negócios e um aplicativo voltado ao cliente. Ele decidiu executar os aplicativos no Azure. Dave lê o tópico [governança de assinatura prescritiva](resource-manager-subscription-governance.md) e agora está pronto para implementar as recomendações.

## <a name="scenario-1-line-of-business-application"></a>Cenário 1: aplicativo de linha de negócios
A Contoso está criando um sistema de gerenciamento de código-fonte (BitBucket) para ser usado pelos desenvolvedores em todo o mundo.  O aplicativo usa a IaaS (infraestrutura como serviço) para hospedagem e consiste em servidores Web e um servidor de banco de dados. Os desenvolvedores acessam servidores em seus ambientes de desenvolvimento, mas eles não precisam acessar os servidores no Azure. O ETS da Contoso deseja permitir que o proprietário do aplicativo e a equipe gerenciem o aplicativo. O aplicativo só está disponível enquanto está na rede corporativa da Contoso. Dave precisa configurar a assinatura para este aplicativo. A assinatura também hospeda outros softwares relacionados ao desenvolvedor no futuro.  

### <a name="naming-standards--resource-groups"></a>Padrões de nomenclatura e grupos de recursos
Dave cria uma assinatura para dar suporte a ferramentas de desenvolvedor que são comuns a todas as unidades de negócios. Ele precisa criar nomes significativos para os grupos de recursos e a assinatura (para o aplicativo e as redes). Ele cria a seguinte assinatura e grupos de recursos:

| Item | Nome | Descrição |
| --- | --- | --- |
| Assinatura |Produção de Ferramentas para Desenvolvedor de ETS da Contoso |Dá suporte a ferramentas de desenvolvedor comuns |
| Grupo de recursos |bitbucket-prod-rg |Contém o servidor Web e servidor de banco de dados do aplicativo |
| Grupo de recursos |corenetworks-prod-rg |Contém a conexão de gateway site a site e redes virtuais |

### <a name="role-based-access-control"></a>Controle de acesso baseado em função
Depois de criar sua assinatura, Dave deseja garantir que as equipes e proprietários do aplicativo apropriados possam acessar seus recursos. Dave reconhece que cada equipe tem requisitos diferentes. Ele utiliza os grupos que foram sincronizados do AD (Active Directory) local da Contoso com o Azure Active Directory e fornece o nível certo de acesso para as equipes.

Dave atribui as funções a seguir para a assinatura:

| Função | Atribuído a | Descrição |
| --- | --- | --- |
| [Proprietário](../active-directory/role-based-access-built-in-roles.md#owner) |ID Gerenciada do AD da Contoso |Essa ID é controlada com acesso JIT (Just in Time) por meio da ferramenta de Gerenciamento de Identidades da Contoso e garante que o acesso de proprietário da assinatura seja totalmente auditado |
| [Gerenciador de Segurança](../active-directory/role-based-access-built-in-roles.md#security-manager) |Departamento de gerenciamento de riscos e de segurança |Esta função permite que os usuários consultem a Central de Segurança do Azure e o status dos recursos |
| [Colaborador de rede](../active-directory/role-based-access-built-in-roles.md#network-contributor) |Equipe de rede |Esta função permite que a equipe de rede da Contoso gerencie o VPN Site a Site e as redes virtuais |
| *Função personalizada* |Proprietário do aplicativo |Dave cria uma função que concede a capacidade de modificar os recursos no grupo de recursos. Para obter mais informações, veja [Funções personalizadas no RBAC do Azure](../active-directory/role-based-access-control-custom-roles.md) |

### <a name="policies"></a>Políticas
Dave tem os seguintes requisitos para o gerenciamento de recursos na assinatura:

* Como as ferramentas de desenvolvimento dão suporte a desenvolvedores em todo o mundo, ele não quer impedir que usuários criem recursos em qualquer região. No entanto, ele precisa saber onde os recursos são criados.
* Ele está preocupado com os custos. Portanto, ele deseja impedir que os proprietários do aplicativo criem máquinas virtuais desnecessariamente caras.  
* Como esse aplicativo serve os desenvolvedores em várias unidades de negócios, ele deseja marcar cada recurso com o proprietário do aplicativo e unidade de negócios. Usando essas marcas, o ETS pode cobrar as equipes apropriadas.

Ele cria as seguintes [políticas do Resource Manager](resource-manager-policy.md):

| Campo | Efeito | Descrição |
| --- | --- | --- |
| location |auditar |Auditar a criação de recursos em qualquer região |
| type |deny |Negar a criação de máquinas virtuais da série G |
| marcas |deny |Exigir a marca do proprietário do aplicativo |
| marcas |deny |Exigir a marca do centro de custo |
| marcas |acrescentar |Acrescentar o nome de marca **BusinessUnit** e valor da marca **ETS** a todos os recursos |

### <a name="resource-tags"></a>Marcações de recursos
Dave entende que ele precisa ter informações específicas sobre a lista para identificar o centro de custo para a implementação do BitBucket. Além disso, Dave quer saber todos os recursos que o ETS tem.

Ele adiciona as seguintes [marcas](resource-group-using-tags.md) aos recursos e grupos de recursos.

| Nome da marca | Valor da marca |
| --- | --- |
| ApplicationOwner |O nome da pessoa que gerencia este aplicativo |
| CostCenter |O centro de custo do grupo que está pagando pelo consumo do Azure |
| BusinessUnit |**ETS** (a unidade de negócios associada à assinatura) |

### <a name="core-network"></a>Rede principal
A equipe de informações de segurança e gerenciamento de riscos da Contoso ETS examina o plano proposto por Dave de mover o aplicativo para o Azure. Eles querem garantir que o aplicativo não seja exposto à Internet.  Dave também tem aplicativos de desenvolvedor que, no futuro, serão movidos para o Azure. Esses aplicativos exigem interfaces públicas.  Para atender a esses requisitos, ele fornece redes virtuais internas e externas e um grupo de segurança de rede para restringir o acesso.

Ele cria os seguintes recursos:

| Tipo de recurso | Nome | Descrição |
| --- | --- | --- |
| Rede Virtual |internal-vnet |Usado com o aplicativo BitBucket e está conectado por meio do ExpressRoute à rede corporativa da Contoso.  Uma sub-rede (`bitbucket`) fornece um espaço de endereço IP específico ao aplicativo |
| Rede Virtual |external-vnet |Disponível para futuros aplicativos que necessitarem de pontos de extremidade voltados para o público |
| Grupo de Segurança de Rede |bitbucket-nsg |Garante que a superfície de ataque dessa carga de trabalho seja minimizada, permitindo conexões apenas na porta 443 para a sub-rede em que o aplicativo reside (`bitbucket`) |

### <a name="resource-locks"></a>Bloqueios de recurso
Dave reconhece que a conectividade da rede corporativa da Contoso para a rede virtual interna deve ser protegida de qualquer script errático ou exclusão acidental.

Ele cria o seguinte [bloqueio de recurso](resource-group-lock-resources.md):

| Tipo de bloqueio | Recurso | Descrição |
| --- | --- | --- |
| **CanNotDelete** |internal-vnet |Impede que os usuários excluam a rede virtual ou sub-redes, mas não impede a adição de novas sub-redes |

### <a name="azure-automation"></a>Automação do Azure
Dave não tem nada a automatizar para este aplicativo. Embora ele tenha criado uma conta de automação do Azure, inicialmente ele não a usará.

### <a name="azure-security-center"></a>Central de Segurança do Azure
Gerenciamento de serviços de TI da Contoso precisa identificar e lidar com as ameaças rapidamente. Eles também desejam compreender quais problemas podem existir.  

Para atender a esses requisitos, Dave habilita a [Central de Segurança do Azure](../security-center/security-center-intro.md) e fornece acesso à função de Gerenciador de Segurança.

## <a name="scenario-2-customer-facing-app"></a>Cenário 2: aplicativo voltado ao cliente
A liderança de negócios na unidade de negócios da cadeia de fornecedores identificou várias oportunidades para aumentar o envolvimento com clientes da Contoso usando um cartão de fidelidade. Equipe de Alice deve criar esse aplicativo e decide que o Azure aumenta sua capacidade de atender às necessidades de negócios. Alice trabalha com Dave de ETS para configurar duas assinaturas para desenvolvimento e operação desse aplicativo.

### <a name="azure-subscriptions"></a>Assinaturas do Azure
Dave faz logon no Azure Enterprise Portal e vê que o departamento de cadeia de fornecedores já existe.  No entanto, como esse projeto é o primeiro projeto de desenvolvimento da equipe de cadeia de fornecedores no Azure, Dave reconhece a necessidade de uma nova conta para a equipe de desenvolvimento de Alice.  Ele cria a conta "P&D" para sua equipe e atribui acesso para Alice. Alice faz logon por meio do Portal do Azure e cria duas assinaturas: uma para conter os servidores de desenvolvimento e outra para conter os servidores de produção.  Ela segue os padrões de nomenclatura estabelecidos anteriormente ao criar as seguintes assinaturas:

| Uso de assinaturas | Nome |
| --- | --- |
| Desenvolvimento |Contoso SupplyChain ResearchDevelopment LoyaltyCard Desenvolvimento |
| Produção |Contoso SupplyChain Operações LoyaltyCard Produção |

### <a name="policies"></a>Políticas
Dave e Alice discutem o aplicativo e identificam que esse aplicativo atende apenas aos clientes na região da América do Norte.  Alice e sua equipe planejam usar o ambiente de serviço de aplicativo do Azure e Azure SQL para criar o aplicativo. Talvez eles precisem criar máquinas virtuais durante o desenvolvimento.  Alice quer garantir que seus desenvolvedores tenham os recursos necessários para explorar e examinar os problemas sem efetuar pull no ETS.

Para a **assinatura de desenvolvimento**, eles criam a seguinte política:

| Campo | Efeito | Descrição |
| --- | --- | --- |
| location |auditar |Auditar a criação de recursos em qualquer região |

Eles não limitam o tipo de sku, que um usuário pode criar no desenvolvimento e não requerem marcas para todos os recursos ou grupos de recursos.

Para a **assinatura de produção**, eles criam as seguintes políticas:

| Campo | Efeito | Descrição |
| --- | --- | --- |
| location |deny |Negar a criação de recursos fora dos data centers dos EUA |
| marcas |deny |Exigir a marca do proprietário do aplicativo |
| marcas |deny |Exigir a marca do departamento |
| marcas |acrescentar |Acrescentar a marca a cada grupo de recursos que indicar o ambiente de produção |

Eles não limitam o tipo de sku que um usuário pode criar em produção.

### <a name="resource-tags"></a>Marcações de recursos
Dave entende que ele precisa ter informações específicas para identificar os grupos de negócios corretos para cobrança e propriedade. Ele define as marcas de recurso para recursos e grupos de recursos.

| Nome da marca | Valor da marca |
| --- | --- |
| ApplicationOwner |O nome da pessoa que gerencia este aplicativo |
| department |O centro de custo do grupo que está pagando pelo consumo do Azure |
| EnvironmentType |**Produção** (mesmo que a assinatura inclua **Produção** no nome, a inclusão dessa marca permite uma identificação fácil ao examinar os recursos no portal ou na fatura) |

### <a name="core-networks"></a>Redes principais
A equipe de informações de segurança e gerenciamento de riscos da Contoso ETS examina o plano proposto por Dave de mover o aplicativo para o Azure. Eles querem garantir que o aplicativo de cartão de fidelidade seja isolado e protegido adequadamente em uma rede DMZ.  Para atender a esse requisito, Dave e Alice criam uma rede virtual externa e um grupo de segurança de rede para isolar o aplicativo do cartão de fidelidade da rede corporativa Contoso.  

Para a **assinatura de desenvolvimento**, eles criam:

| Tipo de recurso | Nome | Descrição |
| --- | --- | --- |
| Rede Virtual |internal-vnet |Serve o ambiente de desenvolvimento do cartão de fidelidade da Contoso e é conectada via ExpressRoute à rede corporativa da Contoso |

Para a **assinatura de produção**, eles criam:

| Tipo de recurso | Nome | Descrição |
| --- | --- | --- |
| Rede Virtual |external-vnet |Hospeda o aplicativo do cartão de fidelidade e não está conectado diretamente à ExpressRoute da Contoso. O código é enviado por push por seu sistema de código-fonte diretamente para os serviços de PaaS |
| Grupo de Segurança de Rede |loyaltycard-nsg |Garante que a superfície de ataque dessa carga de trabalho seja minimizada, permitindo somente a comunicação de entrada na porta TCP 443.  A Contoso também está investigando o uso de um Firewall do aplicativo Web para obter proteção adicional |

### <a name="resource-locks"></a>Bloqueios de recurso
Dave e Alice confabulam e optam por adicionar bloqueios de recurso a alguns dos principais recursos do ambiente para evitar a exclusão acidental durante um envio de código por push errôneo.

Eles criam o bloqueio a seguir:

| Tipo de bloqueio | Recurso | Descrição |
| --- | --- | --- |
| **CanNotDelete** |external-vnet |Para evitar que pessoas excluam as sub-redes ou rede virtual. O bloqueio não impede a adição de novas sub-redes |

### <a name="azure-automation"></a>Automação do Azure
Alice e sua equipe de desenvolvimento têm runbooks abrangentes para gerenciar o ambiente para esse aplicativo. Os Runbooks permitem a adição ou exclusão de nós para o aplicativo e outras tarefas de DevOps.

Para usar esses runbooks, elas habilitam a [Automação](../automation/automation-intro.md).

### <a name="azure-security-center"></a>Central de Segurança do Azure
Gerenciamento de serviços de TI da Contoso precisa identificar e lidar com as ameaças rapidamente. Eles também desejam compreender quais problemas podem existir.  

Para atender a esses requisitos, Dave habilita a Central de Segurança do Azure. Ele garante que a Central de Segurança do Azure esteja monitorando os recursos e fornece acesso às equipes de DevOps e segurança.

## <a name="next-steps"></a>Próximas etapas
* Para aprender sobre como criar modelos do Resource Manager, veja [Melhores práticas para criar modelos do Azure Resource Manager](resource-manager-template-best-practices.md).
