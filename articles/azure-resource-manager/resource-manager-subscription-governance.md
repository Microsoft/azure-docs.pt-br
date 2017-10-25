---
title: "Práticas recomendadas para empresas que estão migrando para o Azure | Microsoft Docs"
description: "Descreve um andaime que as empresas podem usar para garantir um ambiente seguro e gerenciável."
services: azure-resource-manager
documentationcenter: na
author: rdendtler
manager: timlt
editor: tysonn
ms.assetid: 8692f37e-4d33-4100-b472-a8da37ce628f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2017
ms.author: rodend;karlku;tomfitz
ms.openlocfilehash: 3a19f2cf7566f38f80639d7c966638a3ec900cf4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-enterprise-scaffold---prescriptive-subscription-governance"></a>Andaime empresarial do Azure — governança de assinatura prescritiva
As empresas estão adotando cada vez mais a nuvem pública em busca de agilidade e flexibilidade. Elas estão usando os pontos fortes da nuvem para gerar receita ou otimizar recursos para os negócios. O Microsoft Azure fornece uma variedade de serviços que as empresas podem montar como blocos de construção para atender a uma ampla gama de aplicativos e cargas de trabalho. 

Mas, muitas vezes, é difícil saber por onde começar. Depois de decidir usar o Azure, normalmente surgem algumas perguntas:

* "Como atender aos nossos requisitos legais para domínio dos dados em determinados países?"
* "Como garantir que alguém não altere inadvertidamente um sistema crítico?"
* "Como saber o que cada recurso aceita para que eu possa responsabilizá-lo e cobrá-lo de volta precisamente?"

A possibilidade de uma assinatura vazia sem recursos de segurança é assustadora. Esse espaço em branco pode atrasar sua migração para o Azure.

Este artigo fornece um ponto de partida para que profissionais técnicos atendam à necessidade de governança e a equilibrem com a necessidade de agilidade. Ele apresenta o conceito de um andaime empresarial que orienta as organizações na implementação e no gerenciamento de suas assinaturas do Azure. 

## <a name="need-for-governance"></a>Necessidade de governança
Ao migrar para o Azure, você deve abordar o tópico de governança antecipadamente para garantir o uso bem-sucedido da nuvem dentro da empresa. Infelizmente, o tempo e a burocracia de criar um sistema abrangente de governança significam que alguns grupos de negócios vão diretamente aos fornecedores sem se envolverem com a TI empresarial. Essa abordagem pode deixar a empresa aberta a vulnerabilidades se os recursos não forem gerenciados corretamente. As características da nuvem pública — agilidade, flexibilidade e preço com base no consumo — são importantes para os grupos de negócios que precisam atender rapidamente às demandas dos clientes (internos e externos). Porém, a TI empresarial precisa garantir que sistemas e dados sejam protegidos eficazmente.

Na vida real, o andaime é usado para criar a base da estrutura. O andaime guia o plano geral, além de fornecer pontos de ancoragem para que mais sistemas permanentes sejam montados. Um andaime empresarial é igual: um conjunto de controles flexíveis e recursos do Azure que fornecem estrutura ao ambiente, bem como âncoras para serviços criados com base na nuvem pública. Ele fornece aos criadores (TI e grupos de negócios) uma base para criar e agregar novos serviços.

Um andaime se baseia em práticas que coletamos dos muitos compromissos com clientes de vários tamanhos. Esses clientes variam de pequenas organizações que desenvolvem soluções na nuvem a grandes empresas da Fortune 500, bem como fornecedores de software independentes que estão migrando e desenvolvendo soluções na nuvem. O andaime empresarial foi "criado especificamente" para ser flexível e aceitar cargas de TI tradicionais e cargas de trabalho ágeis; por exemplo, desenvolvedores que criam aplicativos SaaS (software como serviço) baseados em recursos do Azure.

O andaime empresarial destina-se a ser a base de cada nova assinatura no Azure. Ele permite aos administradores garantir que as cargas de trabalho atendam aos requisitos mínimos de governança de uma organização sem impedir que grupos de negócios e desenvolvedores cumpram rapidamente suas próprias metas.

> [!IMPORTANT]
> A governança é essencial para o sucesso do Azure. Este artigo foca na implementação técnica de um andaime empresarial, mas toca apenas no processo mais amplo e nas relações entre os componentes. A governança da política flui de cima para baixo e é determinada por aquilo que a empresa quer alcançar. Naturalmente, a criação de um modelo de governança para o Azure inclui representantes da TI, mas o mais importante é ter uma forte representação dos líderes do grupo de negócios, além de gerenciamento de segurança e risco. No fim, um andaime empresarial é sobre reduzir o risco aos negócios para facilitar a missão e os objetivos de uma organização.
> 
> 

A imagem a seguir descreve os componentes do andaime. A base depende de um plano sólido para departamentos, contas e assinaturas. Os pilares consistem em políticas do Resource Manager e sólidos padrões de nomenclatura. O restante do andaime vem dos principais recursos e capacidades do Azure que possibilitam um ambiente seguro e gerenciável.

![componentes do andaime](./media/resource-manager-subscription-governance/components.png)

> [!NOTE]
> O Azure cresceu rapidamente desde sua apresentação em 2008. Esse crescimento exigiu que as equipes de engenharia da Microsoft repensassem a abordagem de gerenciamento e implantação de serviços. O modelo do Azure Resource Manager foi introduzido em 2014 e substitui o modelo de implantação clássico. O Resource Manager permite que as organizações implantem, organizem e controlem mais facilmente os recursos do Azure. O Resource Manager inclui a paralelização ao criar recursos para implantação mais rápida de soluções complexas e interdependentes. Ele também inclui controle de acesso granular e a capacidade de marcar recursos com metadados. A Microsoft recomenda criar todos os recursos por meio do modelo do Resource Manager. O andaime empresarial foi explicitamente desenvolvido para o modelo do Resource Manager.
> 
> 

## <a name="define-your-hierarchy"></a>Definir sua hierarquia
A base do andaime é a inscrição no Azure Enterprise (e no Enterprise Portal). A inscrição empresarial define a forma e o uso dos serviços do Azure em uma empresa e é a principal estrutura de governança. No contrato empresarial, os clientes podem subdividir o ambiente em departamentos, contas e, por fim, em assinaturas. Uma assinatura do Azure é a unidade básica onde todos os recursos estão contidos. Ela também define vários limites dentro do Azure, como o número de núcleos, recursos, etc.

![hierarquia](./media/resource-manager-subscription-governance/agreement.png)

Cada empresa é diferente e a hierarquia na imagem anterior permite flexibilidade significativa em como o Azure é organizado dentro da empresa. Antes de implementar as orientações contidas neste documento, você deve modelar a hierarquia e entender o impacto na cobrança, no acesso aos recursos e na complexidade.

Os três padrões comuns para inscrições do Azure são:

* O padrão **funcional**
  
    ![funcional](./media/resource-manager-subscription-governance/functional.png)
* O padrão da **unidade de negócios** 
  
    ![negócios](./media/resource-manager-subscription-governance/business.png)
* O padrão **geográfico**
  
    ![geográfico](./media/resource-manager-subscription-governance/geographic.png)

Aplique o andaime no nível de assinatura para estender os requisitos de governança da empresa na assinatura.

## <a name="naming-standards"></a>Padrões de nomenclatura
O primeiro pilar do andaime são os padrões de nomenclatura. Os padrões de nomenclatura bem definidos permitem identificar recursos no portal, em uma cobrança e dentro de scripts. Provavelmente, você já tem padrões de nomenclatura para a infraestrutura local. Ao adicionar o Azure ao seu ambiente, você deve estender esses padrões de nomenclatura para os recursos do Azure. O padrão de nomenclatura facilita gerenciamento eficiente do ambiente em todos os níveis.

> [!TIP]
> Para convenções de nomenclatura:
> * Analise e adote onde possível as [Orientações de padrões e práticas](../guidance/guidance-naming-conventions.md). Essas diretrizes ajudam a decidir sobre um padrão de nomenclatura significativo.
> * Use camelCasing para nomes de recursos (como myResourceGroup e vnetNetworkName). Observação: há determinados recursos, como contas de armazenamento, em que a única opção é usar letras minúsculas (e não outros caracteres especiais).
> * Considere o uso de políticas do Azure Resource Manager (descritas na próxima seção) para impor padrões de nomenclatura.
> 
> As dicas anteriores lhe ajudarão-lo a implementar uma convenção de nomenclatura consistente.

## <a name="policies-and-auditing"></a>Políticas e auditoria
O segundo pilar do andaime envolve a criação de [políticas do Azure Resource Manager](resource-manager-policy.md) e a [auditoria do log de atividades](resource-group-audit.md). As políticas do Resource Manager fornecem a capacidade de gerenciar riscos no Azure. Você pode definir políticas que garantem o domínio dos dados, restringindo, impondo ou auditando determinadas ações. 

* A política é um sistema de **permissão** padrão. Controle ações definindo e atribuindo políticas a recursos que negam ou auditam ações nos recursos.
* As políticas são descritas pelas definições de política em uma linguagem de definição de política (condições "se-então").
* Crie políticas com arquivos formatados para JSON (Javascript Object Notation). Após definir uma política, atribua-a a um escopo específico: assinatura, grupo de recursos ou recurso.

As políticas têm várias ações que permitem uma abordagem refinada para seus cenários. As ações são:

* **Negar**: bloqueia a solicitação do recurso
* **Auditar**: permite a solicitação, mas adiciona uma linha ao log de atividades (que pode ser usado para fornecer alertas ou disparar runbooks)
* **Acrescentar**: adiciona informações especificadas ao recurso. Por exemplo, se não houver uma marcação "CostCenter" em um recurso, adicione essa marcação com um valor padrão.

### <a name="common-uses-of-resource-manager-policies"></a>Usos comuns das políticas do Resource Manager
As políticas do Azure Resource Manager são uma poderosa ferramenta do kit de ferramentas do Azure. Elas permitem evitar custos inesperados, de modo a identificar um centro de custos para recursos por meio de marcação, e garantir que os requisitos de conformidade sejam atendidos. Quando as políticas são combinadas com os recursos internos de auditoria, você pode adaptar soluções complexas e flexíveis. As políticas permitem às empresas fornecer controles para cargas de trabalho "TI Tradicional" e cargas de trabalho "Ágeis"; por exemplo, para desenvolver aplicativos para cliente. Os padrões mais comuns que vemos para políticas são:

* **Domínio geográfico de conformidade/dados**: o Azure fornece regiões pelo mundo. Muitas vezes, as empresas desejam controlar onde os recursos são criados (seja para garantir o domínio dos dados, seja apenas para garantir que os recursos sejam criados para os consumidores finais dos recursos).
* **Gerenciamento de custo**: uma assinatura do Azure pode conter recursos de muitos tipos e escala. As empresas muitas vezes querem garantir que as assinaturas padrão evitem usar muitos recursos desnecessariamente, o que pode custar muito dinheiro no mês ou no ano.
* **Governança padrão por meio de marcações obrigatórias**: exigir marcações é um dos recursos mais comuns e altamente desejados. Usando políticas do Azure Resource Manager, as empresas são capazes de garantir que um recurso seja apropriadamente marcado. As marcações mais comuns são: Departamento, Proprietário do Recurso e tipo de Ambiente (por exemplo: produção, teste, desenvolvimento)

**Exemplos**

Assinatura de "TI Tradicional" para aplicativos de linha de negócios

* Impor as marcações Departamento e Proprietário a todos os recursos
* Restringir a criação de recursos à região América do Norte
* Restringir a capacidade de criar VMs da série G e Clusters HDInsight

Ambiente "Ágil" para uma unidade de negócios que cria aplicativos de nuvem

* Para atender aos requisitos de domínio de dados, permita a criação de recursos APENAS em uma região específica.
* Impor a marcação Ambiente a todos os recursos. Se um recurso for criado sem uma marcação, acrescente a marcação **Ambiente: desconhecido** ao recurso.
* Fazer auditoria quando os recursos forem criados fora da América do Norte, mas não impedir.
* Fazer auditoria quando recursos de alto custo forem criados.

> [!TIP]
> O uso mais comum das políticas do Resource Manager entre organizações é controlar *onde* os recursos podem ser criados e *quais* tipos de recurso podem ser criados. Além de fornecer controles sobre *onde* e *quais*, muitas empresas usam políticas para garantir que os recursos tenham os metadados apropriados para cobrar pelo consumo. É recomendável aplicar políticas no nível de assinatura para:
> 
> * Domínio geográfico de conformidade/dados
> * Gerenciamento de custo
> * Marcações obrigatórias (determinadas pelas necessidades de negócios, como BillTo, Proprietário do Aplicativo)
> 
> Você pode aplicar políticas adicionais em níveis inferiores do escopo.
> 
> 

### <a name="audit---what-happened"></a>Auditoria — o que aconteceu?
Para exibir o funcionamento do seu ambiente, você precisa auditar a atividade do usuário. A maioria dos tipos de recurso no Azure cria logs de diagnóstico que você pode analisar por meio de uma ferramenta de log ou no Azure Operations Management Suite. É possível coletar logs de atividades em várias assinaturas para fornecer uma visão departamental ou empresarial. Os registros de auditoria são uma ferramenta importante de diagnóstico e um mecanismo crucial para disparar eventos no ambiente do Azure.

Os logs de atividades das implantações do Resource Manager permitem determinar as **operações** que ocorreram e quem as executou. Os logs de atividades podem ser coletados e agregados usando ferramentas como o Log Analytics.

## <a name="resource-tags"></a>Marcações de recursos
Conforme os usuários em sua organização adicionam recursos à assinatura, torna-se cada vez mais importante associar recursos ao departamento, cliente e ambiente apropriados. Você pode anexar metadados a recursos por meio de [marcações](resource-group-using-tags.md). Você pode usar marcações para fornecer informações sobre o recurso ou o proprietário. Além de permitir agregar e agrupar recursos de várias maneiras, as marcações usam esses dados para a finalidade de estorno. Você pode marcar recursos com até 15 pares de chave/valor. 

As marcações de recurso são flexíveis e devem ser anexadas à maioria dos recursos. Os exemplos de marcações de recurso comuns são:

* BillTo
* Departamento (ou Unidade de Negócios)
* Ambiente (Produção, Preparação, Desenvolvimento)
* Camada (Camada da Web, Camada de Aplicativo)
* Proprietário do Aplicativo
* ProjectName

![marcas](./media/resource-manager-subscription-governance/resource-group-tagging.png)

Para obter mais exemplos de marcações, confira [Convenções de nomenclatura recomendadas para recursos do Azure](../guidance/guidance-naming-conventions.md).

> [!TIP]
> Considere criar uma política que exija marcação para:
> 
> * Grupos de recursos
> * Armazenamento
> * Máquinas Virtuais
> * Servidores Web/Ambientes do Serviço de Aplicativo
> 
> Essa estratégia de marcação identifica nas assinaturas quais metadados são necessários para os negócios, as finanças, a segurança, o gerenciamento de riscos e o gerenciamento geral do ambiente. 

## <a name="resource-group"></a>Grupo de recursos
O Resource Manager permite colocar recursos em grupos significativos para gerenciamento, cobrança ou afinidade natural. Conforme mencionado anteriormente, o Azure tem dois modelos de implantação. No modelo Clássico mais antigo, a unidade básica de gerenciamento era a assinatura. Era difícil dividir recursos em uma assinatura, o que levou à criação de um grande número de assinaturas. Com o modelo do Resource Manager, vimos a introdução dos grupos de recursos. Os grupos de recursos são contêineres de recursos que têm um ciclo de vida comum ou compartilham um atributo, como "todos os servidores SQL" ou "Aplicativo A".

Os grupos de recursos não podem ser contidos um dentro do outro e os recursos podem pertencer apenas a um grupo de recursos. É possível aplicar determinadas ações em todos os recursos em um grupo de recursos. Por exemplo, a exclusão de um grupo de recursos remove todos os recursos do grupo de recursos. Normalmente, você coloca um aplicativo inteiro ou sistema relacionado no mesmo grupo de recursos. Por exemplo, um aplicativo de três camadas chamado Aplicativo Web da Contoso conteria o servidor Web, o servidor de aplicativos e o servidor SQL no mesmo grupo de recursos.

> [!TIP]
> O modo como organizar os grupos de recursos pode variar de cargas de trabalho de "TI Tradicional" a cargas de trabalho de "TI Ágil":
> 
> * As cargas de trabalho "TI Tradicional" são mais frequentemente agrupadas por itens no mesmo ciclo de vida, como um aplicativo. O agrupamento por aplicativo permite o gerenciamento individual de aplicativo.
> * As cargas de trabalho "TI da Agile" tendem a se concentrar nos aplicativos de nuvem voltados para o cliente. Os grupos de recursos devem refletir as camadas da implantação (como Camada da Web, Camada de Aplicativo) e o gerenciamento.
> 
> Noções básicas sobre sua carga de trabalho ajudam a desenvolver uma estratégia de grupo de recursos.

## <a name="role-based-access-control"></a>Controle de acesso baseado em função
Você provavelmente está se perguntando: "quem deve ter acesso aos recursos?" e "como controlar esse acesso?" Permitir ou negar acesso ao portal do Azure, bem como controlar o acesso aos recursos no portal é essencial. 

Quando o Azure foi inicialmente lançado, os controles de acesso para uma assinatura eram básicos: Administrador ou Coadministrador. O acesso a uma assinatura no modelo Clássico implicava acesso a todos os recursos no portal. Essa falta de um controle refinado levou à proliferação de assinaturas para fornecer um nível de controle de acesso razoável para uma Inscrição no Azure.

Essa proliferação de assinaturas não é mais necessária. Com o controle de acesso baseado em função, você pode atribuir usuários às funções padrão (como tipos de função comuns "leitor" e "gravador"). Você também pode definir funções personalizadas.

> [!TIP]
> Para implementar um controle de acesso baseado em função:
> * Conecte seu repositório de identidades corporativo (mais frequentemente, o Active Directory) ao Azure Active Directory usando a ferramenta AD Connect.
> * Controle o Administrador/Coadministrador de uma assinatura usando uma identidade gerenciada. **Não** atribua Administrador/Coadministrador a um novo proprietário da assinatura. Em vez disso, use funções RBAC para fornecer direitos de **Proprietário** a um grupo ou indivíduo.
> * Adicione usuários do Azure a um grupo (por exemplo, Proprietários do Aplicativo X) no Active Directory. Use o grupo sincronizado para fornecer aos membros do grupo os direitos apropriados para gerenciar o grupo de recursos que contém o aplicativo.
> * Siga o princípio de conceder o **privilégio mínimo** exigido para realizar o trabalho esperado. Por exemplo:
>   * Grupo de Implantação: um grupo que só é capaz de implantar recursos.
>   * Gerenciamento de Máquinas Virtuais: um grupo que é capaz de reiniciar VMs (para operações)
> 
> Essas dicas ajudam a gerenciar o acesso do usuário em sua assinatura.

## <a name="azure-resource-locks"></a>Bloqueios de recursos do Azure
À medida que sua organização adiciona serviços principais à assinatura, torna-se cada vez mais importante garantir que esses serviços estejam disponíveis para evitar interrupção nos negócios. Os [Bloqueios de recursos](resource-group-lock-resources.md) permitem restringir operações nos recursos de alto valor, onde sua modificação ou exclusão teria um impacto significativo nos aplicativos ou na infraestrutura da nuvem. Você pode aplicar bloqueios a uma assinatura, um grupo de recursos ou um recurso. Normalmente, você aplica bloqueios a recursos fundamentais, como redes virtuais, gateways e contas de armazenamento. 

Atualmente, os Bloqueios de recursos aceitam dois valores: CanNotDelete e ReadOnly. CanNotDelete significa que os usuários (com os direitos apropriados) ainda podem ler ou modificar um recurso, mas não podem excluí-lo. ReadOnly significa que usuários autorizados não podem excluir nem modificar um recurso.

Para criar ou excluir bloqueios de gerenciamento, você deve ter acesso às ações `Microsoft.Authorization/*` ou `Microsoft.Authorization/locks/*`.
Das funções internas, somente Proprietário e Administrador do Acesso de Usuário recebem essas ações.

> [!TIP]
> As opções de rede principais devem ser protegidas com bloqueios. A exclusão acidental de uma VPN site a site, de gateway seria desastroso para uma assinatura do Azure. O Azure não permite a exclusão de uma rede virtual que esteja em uso, mas aplicar mais restrições é uma precaução útil. 
> 
> * Rede virtual: CanNotDelete
> * Grupo de Segurança de Rede: CanNotDelete
> * Políticas: CanNotDelete
> 
> As políticas também são essenciais para a manutenção dos controles adequados. É recomendável aplicar um bloqueio **CanNotDelete** para políticas que estão em uso.

## <a name="core-networking-resources"></a>Recursos de rede essenciais
O acesso a recursos pode ser interno (dentro da rede da empresa) ou externo (por meio da Internet). É fácil para os usuários em sua organização colocar recursos inadvertidamente no ponto errado e potencialmente abri-los para o acesso mal-intencionado. Assim como acontece com dispositivos locais, as empresas devem adicionar os controles adequados para garantir que os usuários do Azure tomam as decisões certas. Para governança da assinatura, identificamos recursos principais que fornecem controle básico de acesso. Os principais recursos consistem em:

* **Redes virtuais** são objetos de contêiner para sub-redes. Embora não sejam estritamente necessárias, geralmente elas são usadas ao conectar aplicativos aos recursos corporativos internos.
* Os **grupos de segurança de rede** são semelhantes a um firewall e fornecem regras de como um recurso pode "falar" pela rede. Eles fornecem controle granular sobre como/se uma sub-rede (ou máquina virtual) pode se conectar à Internet ou a outras sub-redes na mesma rede virtual.

![rede principal](./media/resource-manager-subscription-governance/core-network.png)

> [!TIP]
> Para rede:
> * Crie redes virtuais dedicadas a cargas de trabalho externas e internas. Essa abordagem reduz a possibilidade de colocar acidentalmente máquinas virtuais que são destinadas a cargas de trabalho internas em um espaço voltado para as cargas externas.
> * Configure grupos de segurança de rede para limitar o acesso. No mínimo, bloqueie o acesso à Internet em redes virtuais internas e bloqueie o acesso à rede corporativa em redes virtuais externas.
> 
> Essas dicas lhe ajudarão a implementar recursos de rede seguros.

### <a name="automation"></a>Automação
Gerenciar recursos individualmente é demorado e potencialmente sujeito a erros para determinadas operações. O Azure fornece vários recursos de automação, incluindo Automação do Azure, Aplicativos Lógicos e Azure Functions. A [Automação do Azure](../automation/automation-intro.md) permite aos administradores criar e definir runbooks para lidar com tarefas comuns no gerenciamento de recursos. Crie runbooks usando um editor de código do PowerShell ou um editor gráfico. Você pode produzir fluxos de trabalho complexos de vários estágios. A Automação do Azure geralmente é usada para lidar com tarefas comuns, como desligar recursos não utilizados ou criar recursos em resposta a um gatilho específico sem a necessidade de intervenção humana.

> [!TIP]
> Para automação:
> * Crie uma conta de Automação do Azure e examine os runbooks (linha de comando e gráfico) disponíveis na [Galeria de Runbooks](../automation/automation-runbook-gallery.md).
> * Importe e personalize runbooks importantes para seu próprio uso.
> 
> Um cenário comum é a capacidade de Iniciar/Desligar máquinas virtuais de modo programado. Há runbooks de exemplo que estão disponíveis na Galeria que lidam com esse cenário e ensinam como expandi-lo.
> 
> 

## <a name="azure-security-center"></a>Central de Segurança do Azure
Talvez um dos maiores impedimentos à adoção da nuvem tenham sido as preocupações com a segurança. Os gerentes de risco à TI e os departamentos de segurança precisam garantir que esses recursos no Azure sejam seguros. 

A [Central de Segurança do Azure](../security-center/security-center-intro.md) fornece uma visão central do status de segurança dos recursos nas assinaturas, além de recomendações que ajudam a evitar recursos comprometidos. Ela pode permitir políticas mais granulares (por exemplo, aplicar políticas a grupos de recursos específicos que permitem à empresa adaptar sua postura ao risco que está enfrentando). Por fim, a Central de Segurança do Azure é uma plataforma aberta que permite aos parceiros da Microsoft e fornecedores de software independentes criar software que se conecte à Central de Segurança do Azure para aprimorar seus recursos. 

> [!TIP]
> A Central de Segurança do Azure é habilitada por padrão em cada assinatura. No entanto, você deve habilitar a coleta de dados em máquinas virtuais de modo a permitir que a Central de Segurança do Azure instale seu agente e comece a coletar dados.
> 
> ![coleta de dados](./media/resource-manager-subscription-governance/data-collection.png)
> 
> 

## <a name="next-steps"></a>Próximas etapas
* Agora que você aprendeu sobre governança de assinatura, é hora de ver essas recomendações na prática. Veja [Exemplos de implementação da governança de assinatura do Azure](resource-manager-subscription-examples.md).

