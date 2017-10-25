---
title: "Guia de introdução para operadores de TI do Azure | Microsoft Docs"
description: "Guia de introdução para operadores de TI do Azure"
services: 
documentationcenter: 
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.devlang: 
ms.topic: 
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 06/12/2017
ms.author: nepeters
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 1180001c9fe74aab6b51c5b5969b80a8c7e1302f
ms.contentlocale: pt-br
ms.lasthandoff: 09/13/2017

---

# <a name="introduction-to-cloud-computing-and-microsoft-azure"></a>Introdução à computação em nuvem e ao Microsoft Azure

Este guia apresenta conceitos fundamentais relacionados à implantação e ao gerenciamento de uma infraestrutura do Microsoft Azure. Se você não for familiarizado com a computação em nuvem ou com o Azure, este guia serve como introdução rápida a conceitos, implantação e detalhes de gerenciamento. Muitas seções deste guia discutem uma operação como a implantação de uma máquina virtual e, em seguida, fornecem um link para detalhes técnicos aprofundados.


## <a name="cloud-computing-overview"></a>Visão geral da computação em nuvem

A computação em nuvem fornece uma alternativa moderna para o datacenter local tradicional de computação em nuvem. Fornecedores de nuvem pública fornecem e gerenciam toda a infraestrutura de computação e o software de gerenciamento subjacente. Esses fornecedores oferecem uma grande variedade de serviços de nuvem. Nesse caso, um serviço de nuvem pode ser uma máquina virtual, um servidor Web ou um mecanismo de banco de dados hospedado na nuvem. Como um cliente do provedor de nuvem, você concede esses serviços de nuvem conforme a necessidade. Dessa forma, você pode converter as despesas de capital de manutenção de hardware em uma despesa operacional. Um serviço de nuvem também oferece estes benefícios:

-   Implantação rápida de ambientes de computação grandes

-   Desalocação rápida de sistemas que não são mais necessários

-   Implantação fácil de sistemas tradicionalmente complexos, tais como balanceadores de carga

-   Habilidade de fornecer capacidade de computação flexível ou dimensionar quando necessário

-   Ambientes de computação mais econômicos

-   Acessar de qualquer lugar com um portal baseado na Web ou através de automação programática

-   Serviços baseados em nuvem para atender a maioria das necessidades de computação e de aplicativo

Com infraestrutura local, você tem controle total sobre o hardware e o software que é implantado. Historicamente, isso levou para decisões de aquisição de hardware que se concentram em expansão. Um exemplo é a compra de um servidor com mais núcleos para atender às necessidades de desempenho de pico. Infelizmente, essa infraestrutura pode ser subutilizada fora de uma janela de demanda. Com o Azure, você pode implantar apenas a infraestrutura que você precisa e ajustá-la para cima ou para baixo a qualquer momento. Isso leva a um foco em escala horizontal por meio da implantação de nós de computação adicionais para atender a uma necessidade de desempenho. Escalar horizontalmente serviços de nuvem é mais econômico que escalar verticalmente por meio de hardware caro.

A Microsoft implantou muitos datacenters do Azure em todo o mundo e planeja implantar mais. Além disso, a Microsoft está aumentando as nuvens soberanas em regiões como China e na Alemanha. Somente as maiores empresas globais podem implantar datacenters dessa maneira, então usar o Azure facilita para empresas de qualquer tamanho implantarem seus serviços perto de seus clientes.

Para pequenas empresas, o Azure permite um ponto de entrada de baixo custo, com a capacidade de dimensionar rapidamente conforme a demanda de computação aumenta. Isso evita um grande investimento de capital inicial na infraestrutura e fornece a flexibilidade para projetar e refazer a arquitetura de sistemas conforme necessário. O uso de computação em nuvem se ajusta bem com o modelo scale-fast e fail-fast de crescimento de startup.

Para obter mais informações sobre as regiões do Azure disponíveis, consulte [Regiões do Azure](https://azure.microsoft.com/regions/).

### <a name="cloud-computing-is-classified-into-three-categories-saas-paas-and-iaas"></a>A computação em nuvem é classificada em três categorias: SaaS, PaaS e IaaS.

#### <a name="saas-software-as-a-service"></a>SaaS: software como serviço

SaaS é um software que é hospedado e gerenciado centralmente. É normalmente baseado em uma arquitetura multilocatário — uma única versão do aplicativo é usada para todos os clientes. Ele pode ser escalado horizontalmente para várias instâncias para garantir o melhor desempenho em todas as localizações. Software SaaS normalmente é licenciado por meio de uma assinatura mensal ou anual.

O Microsoft Office 365 é um bom exemplo de uma oferta de SaaS. Os assinantes pagam uma taxa de assinatura mensal ou anual e obtêm Microsoft Exchange, Microsoft OneDrive e o restante do pacote do Microsoft Office como um serviço. Os assinantes sempre obtêm a versão mais recente e o servidor Exchange é gerenciado para você. Em comparação instalar e atualizar o Office todo ano, isso é menos dispendioso e requer menos esforço.

#### <a name="paas-platform-as-a-service"></a>PaaS: plataforma como serviço 

Com PaaS, você implanta seu aplicativo em um ambiente oferecido pelo fornecedor do serviço de nuvem. O fornecedor faz todo o gerenciamento de infraestrutura, de modo que pode se concentrar no desenvolvimento de aplicativos.

O Azure fornece diversas ofertas de computação de PaaS, incluindo o recurso de Aplicativos Web do Serviço de Aplicativo do Azure e os Serviços de Nuvem do Azure (funções Web e de trabalho). Em ambos os casos, os desenvolvedores têm várias maneiras de implantar seu aplicativo sem saber nada sobre os elementos que dão suporte a ele. Os desenvolvedores não precisam criar VMs (máquinas virtuais), usar o protocolo RDP para entrar em cada uma delas ou instalar o aplicativo. Eles apenas acionam um botão (ou próximo a ele) e as ferramentas fornecidas pelo Microsoft provisionam VMs e, em seguida, implantarem e instalar o aplicativo neles.

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: infraestrutura como serviço

Um fornecedor de nuvem de IaaS executa e gerencia todos os recursos de computação física e o software necessário para habilitar a virtualização de computador. Um cliente desse serviço implanta máquinas virtuais nesses datacenters hospedados. Embora as máquinas virtuais estejam localizadas em um datacenter externo, o consumidor IaaS tem controle sobre a configuração e o gerenciamento delas.

O Azure inclui várias soluções de IaaS, incluindo máquinas virtuais, conjuntos de dimensionamento de máquinas virtuais e a infraestrutura de rede relacionada. Máquinas virtuais são uma escolha popular para migrar inicialmente serviços para o Azure, porque ela permite um modelo de migração "lift-and-shift". Você pode configurar uma VM como a infraestrutura executando seus serviços atualmente em seu datacenter e, em seguida, migrar seu software para a nova VM. Talvez você precise fazer atualizações de configuração, tais como URLs para outros serviços ou armazenamento, mas você pode migrar vários aplicativos dessa maneira.

Conjuntos de dimensionamento de máquinas virtuais são criados sobre Máquinas Virtuais do Azure e fornecem uma maneira fácil de implantar clusters de VMs idênticas. Conjuntos de dimensionamento de máquinas virtuais também dão suporte a dimensionamento automático para que novas VMs possam ser implantadas automaticamente quando necessário. Isso torna os conjuntos de dimensionamento de máquinas virtuais uma plataforma ideal para hospedar os clusters computação de microsserviços de nível mais alto, tais como o Azure Service Fabric e o Serviço de Contêiner do Azure.

## <a name="azure-services"></a>Serviços do Azure

O Azure oferece muitos serviços em sua plataforma de computação em nuvem. Esses serviços incluem o descrito a seguir.

### <a name="compute-services"></a>Serviços de computação

Serviços para hospedar e executar a carga de trabalho do aplicativo:

-   Máquinas Virtuais do Azure – Linux e Windows

-   Serviços de Aplicativos (Aplicativos Web, Aplicativos Móveis, Aplicativos Lógicos, Aplicativos de API e Aplicativos de Função)

-   Lote do Azure (para trabalhos de computação em lote e paralelos em grande escala)

-   Azure Service Fabric

-   Serviço de Contêiner do Azure

### <a name="data-services"></a>Serviços de dados

Serviços para armazenamento e gerenciamento de dados:

-   Armazenamento do Azure (inclui os serviços de Blob do Azure, de Fila, de Tabelas e de Arquivo)

-   Banco de Dados SQL do Azure

-   Azure DocumentDB

-   Microsoft Azure StorSimple

-   Cache Redis do Azure

### <a name="application-services"></a>Serviços de aplicativo

Serviços para compilar e operar aplicativos:

-   Active Directory do Azure (Azure AD)

-   Barramento de Serviço do Azure para conectar sistemas distribuídos

-   Azure HDInsight para processamento de Big Data

-   Agendador do Azure

-   Serviços de Mídia do Azure

### <a name="network-services"></a>Serviços de rede

Serviços de rede tanto dentro do Azure quanto entre datacenters do Azure e local:

-   Rede Virtual do Azure

-   Azure ExpressRoute

-   DNS fornecido pelo Azure

-   Gerenciador de Tráfego do Azure

-   Rede de Distribuição de Conteúdo do Azure

Para obter documentação detalhada sobre os serviços do Azure, consulte [Documentação do serviço do Azure](https://docs.microsoft.com/azure).

## <a name="azure-key-concepts"></a>Conceitos principais do Azure

### <a name="datacenters-and-regions"></a>Datacenters e regiões


O Azure é uma plataforma de nuvem global que geralmente está disponível em muitas regiões em todo o mundo. Quando você provisiona um serviço, aplicativo ou VM no Azure, é solicitado que você selecione uma região. A região selecionada representa um datacenter específico em que seu aplicativo é executado. Para obter mais informações, consulte [Regiões do Azure](https://azure.microsoft.com/regions/).

Um dos benefícios de utilizar o Azure é que você pode implantar seus aplicativos em vários datacenters em todo o mundo. A região que você escolher poderá afetar o desempenho do aplicativo. É melhor escolher uma região que esteja mais próxima da maioria dos seus clientes para reduzir a latência nas solicitações de rede. Você também pode selecionar sua região visando cumprir os requisitos legais para distribuir seu aplicativo em determinados países.

### <a name="azure-portal"></a>Portal do Azure


O portal do Azure é um aplicativo baseado na Web que você pode utilizar para criar, gerenciar e remover recursos e serviços do Azure. O Portal do Azure está localizado em https://portal.azure.com. Ele inclui um painel personalizável e ferramentas para gerenciar recursos do Azure. Ele também fornece informações de cobrança e de assinatura. Para obter mais informações, consulte [Visão geral do portal do Microsoft Azure](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) e [Gerenciar Recursos do Azure por meio do Portal](https://docs.microsoft.com/azure/azure-portal/resource-group-portal).

### <a name="resources"></a>Recursos

Recursos do Azure são serviços individuais de computação, de rede, de dados ou de hospedagem de aplicativos que foram implantados em uma assinatura do Azure. Alguns recursos comuns são máquinas virtuais, contas de armazenamento ou bancos de dados SQL. Os serviços do Azure geralmente consistem em vários recursos do Azure relacionados. Por exemplo, uma máquina virtual do Azure pode incluir uma VM, uma conta de armazenamento, um adaptador de rede e um endereço IP público. Essas recursos podem ser criados, gerenciados e excluídos individualmente ou como um grupo. Recursos do Azure são abordados em mais detalhes posteriormente neste guia.

### <a name="resource-groups"></a>Grupos de recursos

Um grupo de recursos do Azure é um contêiner que mantém os recursos relacionados a uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que você deseja gerenciar como um grupo. Grupos de recursos do Azure são abordados em mais detalhes posteriormente neste guia.

### <a name="resource-manager-templates"></a>Modelos do Gerenciador de Recursos

Um modelo do Azure Resource Manager é um arquivo JSON (JavaScript Object Notation) que define um ou mais recursos para implantação em um grupo de recursos. Ele também define as dependências entre os recursos implantados. Modelos do Resource Manager são abordados em mais detalhes posteriormente neste guia.

### <a name="automation"></a>Automação


Além de criar, gerenciar e excluir recursos usando o portal do Azure, você pode automatizar essas atividades usando PowerShell ou a CLI (interface de linha de comando) do Azure.

**PowerShell do Azure**

O Azure PowerShell é um conjunto de módulo que fornece cmdlets para gerenciar o Azure. Você pode usar os cmdlets para criar, gerenciar e remover serviços do Azure. Os cmdlets podem ajudá-lo a conseguir implantações consistentes, reproduzíveis e sem intervenção. Para saber mais, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps):

**interface de linha de comando do Azure**

A interface de linha de comando do Azure é uma ferramenta que você pode usar para criar, gerenciar e remover recursos do Azure por meio da linha de comando. A CLI do Azure está disponível para Mac OS X, Linux e Windows. Para obter mais informações e detalhes técnicos, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli.md).

**APIs REST** O Azure é desenvolvido em um conjunto de APIs REST que dão suporte à interface do usuário do Portal do Azure. A maioria dessas APIs REST também tem suporte para permitir que você gerencie e provisione programaticamente seus recursos e aplicativos do Azure por meio de qualquer dispositivo habilitado para Internet. Para obter mais informações, consulte a [Referência de SDK REST do Azure](https://docs.microsoft.com/rest/api/index).


## <a name="azure-subscriptions"></a>Assinaturas do Azure


Uma assinatura é um agrupamento de serviços do Azure que está vinculada a uma conta do Azure. Uma única conta do Azure pode conter várias assinaturas. A cobrança dos serviços do Azure é feita por assinatura. As assinaturas do Azure têm um administrador da conta (que tem controle total sobre a assinatura) e um administrador de serviços (que tem controle sobre todos os serviços na assinatura). Além dos administradores, é possível conceder controle detalhado de recursos do Azure por meio de RBAC a contas individuais.

### <a name="select-and-enable-an-azure-subscription"></a>Selecionar e habilitar uma assinatura do Azure

Antes de você poder trabalhar com os serviços do Azure, você precisará de uma assinatura. Vários tipos de assinatura estão disponíveis.

**Contas gratuitas**: o link para inscrever-se para uma conta gratuita está no [site do Azure](https://azure.microsoft.com/). Isso lhe dá crédito ao longo de 30 dias para experimentar qualquer combinação de recursos no Azure. Se você exceder a quantidade de crédito, sua conta será suspensa. No final da avaliação, os serviços são encerrados e deixarão de funcionar. Você poderá atualizar para uma assinatura pré-paga a qualquer momento.

**Assinaturas do MSDN**: se você tiver uma assinatura do MSDN, você obterá um valor específico em crédito Azure por mês. Por exemplo, se você tiver uma assinatura do Microsoft Visual Studio Enterprise com MSDN, você obtém \$150 por mês em crédito Azure.

Se você exceder a quantidade de crédito, seu serviço ficará desabilitado até o início do próximo mês. Você pode desligar o limite de gastos e adicionar um cartão de crédito a ser usado para os custos adicionais. Alguns desses custos têm desconto para contas do MSDN. Por exemplo, você paga o preço do Linux para VMs executando o Windows Server e não há nenhum encargo adicional para servidores da Microsoft como o Microsoft SQL Server. Isso torna a contas do MSDN ideal para cenários de desenvolvimento e teste.

**Contas do BizSpark**: o programa Microsoft BizSpark fornece muitos benefícios para startups. Um desses benefícios é acesso a todos os softwares da Microsoft para ambientes de desenvolvimento e teste para até cinco contas do MSDN. Você obtém US$150 em crédito Azure para cada uma dessas cinco contas MSDN e paga taxas reduzidas para vários serviços do Azure, tais como máquinas virtuais.

**Pré-pago**: com essa assinatura, você paga pelo que usa anexando um cartão de crédito ou de débito à conta. Se você for uma organização, você também poderá receber aprovação para faturamento.

**Contratos Enterprise**: com um Contrato Enterprise, você confirma usando um determinado número de serviços no Azure no próximo ano e você pagará essa quantidade antecipadamente. O compromisso que você assume é consumido ao longo do ano. Se você exceder o valor do compromisso, você poderá pagar o excedente em atraso. Dependendo do valor do compromisso, você obtém um desconto nos serviços do Azure.



### <a name="grant-administrative-access-to-an-azure-subscription"></a>Conceder acesso administrativo a uma assinatura do Azure


Várias funções de administrador da conta estão disponíveis e podem ser alteradas a qualquer momento. Duas funções principais são:

-   **Administrador de serviços**: essa função está autorizada a gerenciar os serviços do Azure. Por padrão, ele recebe acesso à mesma conta que o administrador da conta.

-   **Coadministrador**: essa função tem o mesmo acesso que o administrador de serviços. No entanto, essa função não pode alterar a associação de uma assinatura para diretórios do Azure.

Para saber mais, confira [Como adicionar ou alterar as funções de administrador do Azure](../../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="view-billing-information-in-the-azure-portal"></a>Exibir informações de cobrança no portal do Azure


Um componente importante de usar o Azure é a capacidade de exibir informações de cobrança. O portal do Azure fornece insights detalhados sobre as informações de cobrança do Azure.

Para ver mais informações, confira [Como baixar sua fatura de cobrança e dados de uso diário do Azure](../../billing/billing-download-azure-invoice-daily-usage-date.md).

### <a name="get-billing-information-from-billing-apis"></a>Obter informações de cobrança de APIs de cobrança


Além de exibir a cobrança no portal, você pode acessar as informações de cobrança usando um script ou programa por meio de APIs REST de Cobrança do Azure:

-   Você pode usar a API de Uso do Azure para recuperar os dados de uso. Você pode ajustar as informações de uso de cobrança marcando recursos do Azure relacionados. Por exemplo, você pode marcar cada um dos recursos em um grupo de recursos com um nome de departamento ou nome de projeto e, em seguida, controlar os custos especificamente para essa marcação.

-   Você pode usar a API de Cartão de Taxa do Azure para listar todos os recursos disponíveis, junto com os metadados e informações de preços sobre cada um desses recursos.

Para saber mais, confira [Obtenha informações sobre o consumo de recursos do Microsoft Azure](../../billing/billing-usage-rate-card-overview.md).

### <a name="forecast-cost-with-the-pricing-calculator"></a>Custo previsto com a calculadora de preços

O preço para cada serviço no Azure é diferente. Muitos serviços do Azure fornecem camadas Básico, Standard e Premium. Normalmente, cada camada tem vários níveis de preço e de desempenho. Usando a [calculadora de preços online](http://azure.microsoft.com/pricing/calculator), você pode criar estimativas de preço. A calculadora inclui flexibilidade para estimar o custo em um único recurso ou um grupo de recursos.

### <a name="set-up-billing-alerts"></a>Configurar alertas de cobrança

Após implantar seu aplicativo ou solução no Azure será possível criar alertas, os quais enviarão emails para você ao se aproximar dos limites de gastos definidos no alerta. Para obter mais informações, veja [Configurar alertas de cobrança para suas assinaturas do Microsoft Azure](../../billing/billing-set-up-alerts.md).

## <a name="azure-resource-manager"></a>Gerenciador de Recursos do Azure

O Azure Resource Manager é um mecanismo de implantação, gerenciamento e organização de recursos do Azure. Usando o Resource Manager, você pode colocar muitos recursos individuais juntos em um grupo de recursos.

O Resource Manager também inclui recursos de implantação que permitem a implantação personalizada e a configuração de recursos relacionados. Por exemplo, utilizando o Resource Manager é possível implantar um aplicativo que consiste em máquinas virtuais múltiplas, um balanceador de carga e um Banco de Dados SQL como uma unidade única. Você pode desenvolver essas implantações usando um modelo do Resource Manager.

O Gerenciador de Recursos fornece vários benefícios:

-   Você pode implantar, gerenciar e monitorar todos os recursos da sua solução como um grupo em vez de tratá-los individualmente.

-   Você pode implantar a solução repetidamente em todo seu ciclo de vida de desenvolvimento e com a confiança de que seus recursos serão implantados em um estado consistente.

-   Você pode gerenciar sua infraestrutura por meio de modelos declarativos em vez de scripts.

-   Você pode definir as dependências entre os recursos para que eles sejam implantados na ordem correta.

-   Você pode aplicar o controle de acesso a todos os serviços no grupo de recursos, pois o RBAC é integrado nativamente à plataforma de gerenciamento.

-   Você pode aplicar marcas aos recursos para organizar de modo lógico todos os recursos em sua assinatura.

-   Você pode esclarecer a cobrança da sua organização exibindo os custos para um grupo de recursos que compartilha a mesma marcação.

### <a name="tips-for-creating-resource-groups"></a>Dicas para a criação de grupos de recursos


Quando você estiver fazendo decisões sobre os grupos de recursos, considere estas dicas:

-   Todos os serviços em um grupo de recursos devem ter o mesmo ciclo de vida.

-   Você pode atribuir um recurso a apenas um grupo por vez.

-   Você pode adicionar ou remover um recurso de um grupo de recursos a qualquer momento. Cada recurso deve pertencer a um grupo de recursos. Se você remover um recurso de um grupo, você deverá adicioná-lo a outro.

-   Você pode mover a maioria dos tipos de recursos para um grupo de recursos diferente a qualquer momento.

-   Os recursos em um grupo de recursos podem estar em regiões diferentes.

-   Você pode usar um grupo de recursos para controlar o acesso aos recursos contidos nele.

### <a name="building-resource-manager-templates"></a>Criando modelos do Resource Manager

Modelos do Resource Manager definem declarativamente os recursos e as configurações de recursos que serão implantadas em um único grupo de recursos. Você pode usar modelos do Resource Manager para orquestrar implantações complexas sem a necessidade de configuração manual ou script em excesso. Depois de desenvolver um modelo, você pode implantá-lo várias vezes — sempre com resultados idênticos.

Um modelo do Resource Manager consiste em quatro seções:

-   **Parâmetros**: esses são entradas para a implantação. Valores de parâmetro podem ser fornecidos por um ser humano ou por um processo automatizado. Um parâmetro de exemplo pode ser um nome de usuário administrador e a senha para uma VM do Windows. Os valores de parâmetro são usados em toda a implantação quando ele são especificados.

-   **Variáveis**: são usadas para manter os valores que são usados por toda a implantação. Ao contrário do que ocorre com parâmetros, um valor de variável não é fornecido no momento da implantação. Em vez disso, ele é embutido no código ou gerado dinamicamente.

-   **Recursos**: esta seção do modelo define os recursos a serem implantados, tais como máquinas virtuais, contas de armazenamento e redes virtuais.

-   **Saída**: depois de uma implantação ser concluída, o Resource Manager pode retornar dados como cadeias de conexão geradas dinamicamente.

Os mecanismos a seguir estão disponíveis para a automação da implantação:

-   **Funções**: você pode usar várias funções em modelos do Resource Manager. Isso inclui operações como converter uma cadeia de caracteres em minúsculas, implantar várias instâncias de um recurso definido e retornar dinamicamente o grupo de recursos de destino. As funções do Resource Manager ajudam a criar implantações dinâmicas.

-   **Dependências de recurso**: quando você estiver implantando vários recursos, alguns recursos têm uma dependência em outros. Para facilitar a implantação, você pode usar uma declaração de dependência para que os recursos dependentes sejam implantados antes dos outros.

-   **Vinculação de modelo**: de dentro de um modelo do Resource Manager, você pode vincular a outro modelo. Isso permite a decomposição da implantação em um conjunto de modelos direcionados, para finalidade específica.

Você pode criar modelos do Resource Manager em qualquer editor de texto. No entanto, o SDK do Azure para Visual Studio inclui ferramentas para lhe ajudar. Usando o Visual Studio, você pode adicionar recursos para o modelo por meio de um assistente e, em seguida, implantar e depurar o modelo diretamente de dentro do Visual Studio. Para obter mais informações, confira [Criação de modelos do Azure Resource Manager](../../resource-group-authoring-templates.md).

Por fim, você pode converter grupos de recursos existentes em um modelo reutilizável no portal do Azure. Isso pode ser útil se você deseja criar um modelo de implantação de um grupo de recursos existente ou apenas deseja examinar o JSON subjacente. Para exportar um grupo de recursos, selecione o botão **Script de Automação** das configurações do grupo de recursos.

## <a name="security-of-azure-resources-rbac"></a>Segurança dos recursos do Azure (RBAC)

Você pode conceder acesso operacional a contas de usuário em um escopo especificado: assinatura, grupo de recursos ou recurso individual. Isso significa que você pode implantar um conjunto de recursos em um grupo de recursos, como uma máquina virtual e todos os recursos relacionados e conceder permissões a um usuário ou grupo específico. Essa abordagem limita o acesso a apenas os recursos que pertencem ao grupo de recursos de destino. Além disso, é possível conceder acesso a um recurso único, tal como uma máquina virtual ou uma rede virtual.

Para conceder acesso, você atribui uma função ao usuário ou ao grupo de usuários. Há muitas funções predefinidas. Você também pode definir suas próprias funções personalizadas.

Aqui estão algumas funções de exemplo criadas no Azure:

-   **Proprietário**: um usuário com essa função pode gerenciar tudo, incluindo o acesso.

-   **Leitor**: um usuário com essa função pode ler recursos de todos os tipos (exceto segredos), mas não pode fazer alterações.

-   **Colaborador da máquina virtual**: um usuário com essa função pode gerenciar máquinas virtuais, mas não pode gerenciar a rede virtual à qual elas estão conectadas ou a conta de armazenamento em que o arquivo VHD reside.

-   **Colaborador do BD SQL**: um usuário com essa função pode gerenciar bancos de dados SQL, mas não as respectivas políticas relacionadas à segurança.

-   **Gerente de segurança do SQL**: um usuário com essa função pode gerenciar políticas relacionadas à segurança de servidores e bancos de dados SQL.

-   **Colaborador da conta de armazenamento**: um usuário com essa função pode gerenciar contas de armazenamento, mas não pode gerenciar o acesso às contas de armazenamento.

Para obter mais informações, consulte [Usar atribuições de função para gerenciar o acesso aos recursos de assinatura do Azure](../../active-directory/role-based-access-control-configure.md).

## <a name="azure-virtual-machines"></a>Máquinas Virtuais do Azure

O Máquinas Virtuais do Azure é um dos serviços de IaaS centrais no Azure. As Máquinas Virtuais do Azure dão suporte para a implantação de máquinas virtuais Linux ou Windows em um datacenter do Microsoft Azure. Com as Máquinas Virtuais do Azure, você tem controle total sobre a configuração da VM e é responsável por toda a instalação, a configuração e a manutenção de software.

Quando você estiver implantando uma VM do Azure, você poderá selecionar uma imagem do Azure Marketplace ou fornecer sua própria imagem generalizada. Essa imagem é usada para aplicar o sistema operacional e a configuração inicial. Durante a implantação, o Resource Manager manipulará algumas configurações, como atribuir o nome do computador, as credenciais administrativas e configuração de rede. Você pode usar extensões de máquina virtual do Azure para automatizar mais configurações como a instalação de software, configuração de antivírus e soluções de monitoramento.

Você pode criar máquinas virtuais de vários tamanhos diferentes. O tamanho da máquina virtual determina a alocação de recurso como a capacidade de processamento, a memória e o armazenamento. Em alguns casos, recursos específicos como adaptadores de rede habilitados para RDMA e discos SSD estão disponíveis apenas com determinados tamanhos de VM. Para obter uma lista completa de recursos e tamanhos de VM, consulte "Tamanhos das máquinas virtuais no Azure" para [Windows](../../virtual-machines/windows/sizes.md) e [Linux](../../virtual-machines/linux/sizes.md).

### <a name="use-cases"></a>Casos de uso

Já que as máquinas virtuais do Azure oferecem controle total sobre a configuração, elas são ideais para uma ampla variedade de cargas de trabalho de servidor que não cabem em um modelo de PaaS. Cargas de trabalho de servidor, como servidores de banco de dados (SQL Server, Oracle ou MongoDB), Active Directory do Windows Server, Microsoft SharePoint e muitas mais tornam-se possíveis de ser executadas na plataforma Microsoft Azure. Se desejar, você pode mover essas cargas de trabalho de um datacenter local para uma ou mais regiões do Azure, sem uma grande quantidade de reconfiguração.

### <a name="deployment-of-virtual-machines"></a>Implantação de máquinas virtuais

Você pode implantar máquinas virtuais do Azure usando o portal do Azure, usando a automação com o módulo do Azure PowerShell ou usando a automação com a CLI de plataforma cruzada.

**Portal**

Implantar uma máquina virtual usando o portal do Azure requer apenas uma assinatura ativa do Azure e o acesso a um navegador da Web. Você pode selecionar muitas imagens diferentes do sistema operacional com configurações diferentes. Todos os requisitos de armazenamento e de rede são configurados durante a implantação. Para obter mais informações, consulte "Criar uma máquina virtual no portal do Azure" para [Windows](../../virtual-machines/windows/quick-create-portal.md) e [Linux](../../virtual-machines/linux/quick-create-portal.md).

Além de implantar uma máquina virtual do portal do Azure, você pode implantar um modelo do Azure Resource Manager por meio do portal. Isso implantará e configurará todos os recursos, conforme definido no modelo. Para obter mais informações, veja [Implantar recursos com modelos do Resource Manager e o portal do Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).


**PowerShell**

Implantar uma máquina virtual do Azure usando o PowerShell permite a automação completa da implantação de todos os recursos de máquina virtual relacionados, incluindo armazenamento e rede. Para obter mais informações, consulte [Criar uma VM do Windows usando o Resource Manager e o PowerShell](../../virtual-machines/windows/quick-create-powershell.md).

Além de implantar recursos de computação do Azure individualmente, você pode usar o módulo do Azure PowerShell para implantar um modelo do Azure Resource Manager. Para obter mais informações, veja [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**CLI (Interface da linha de comando)**

Assim como acontece com o módulo do PowerShell, a interface de linha de comando do Azure fornece automação de implantação e pode ser usada em sistemas Windows, OS X ou Linux. Quando você estiver usando o comando **vm quick-create** da CLI do Azure, todos os recursos de máquina virtual relacionados (incluindo armazenamento e rede) e a máquina virtual em si serão implantados. Para obter mais informações, consulte [Criar uma VM Linux no Azure usando a CLI](../../virtual-machines/linux/quick-create-cli.md).

Do mesmo modo, você pode usar uma CLI do Azure para implantar um modelo do Azure Resource Manager. Para obter mais informações, veja [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-virtual-machines"></a>Acesso e segurança para máquinas virtuais

Acessar uma máquina virtual da Internet requer que o adaptador de rede associado (ou balanceador de carga, se aplicável) seja configurado com um endereço IP público. O endereço IP público inclui um nome DNS que será resolvido para a máquina virtual ou o balanceador de carga. Para obter mais informações, consulte [Endereços IP no Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).

Você gerencia o acesso à máquina virtual pelo endereço IP público usando um recurso NSG (grupo de segurança de rede). Um NSG age como um firewall e permite ou nega o tráfego pelo adaptador de rede ou sub-rede em um conjunto de portas definidas. Por exemplo, para criar uma sessão de área de trabalho remota com uma VM do Azure, você precisa configurar o NSG para permitir o tráfego de entrada na porta 3389. Para obter mais informações, consulte [Abrir portas para uma VM no Azure usando o Portal do Azure](../../virtual-machines/windows/nsg-quickstart-portal.md).

Por fim, assim como acontece com o gerenciamento de qualquer sistema de computador, você deve fornecer segurança para uma máquina virtual do Azure no sistema operacional usando as credenciais de segurança e firewalls de software.

## <a name="azure-storage"></a>Armazenamento do Azure

O Armazenamento do Azure é um serviço gerenciado pela Microsoft que fornece armazenamento redundante, escalonável e durável. Usando qualquer método de implantação de recursos, você pode adicionar uma conta de Armazenamento do Azure como um recurso para qualquer grupo de recursos. O Azure inclui quatro tipos de armazenamento: Armazenamento de Blobs, Armazenamento de Arquivos, Armazenamento de Tabelas e Armazenamento de Filas. Ao implantar uma conta de armazenamento, dois tipos de conta estão disponíveis: de uso geral e de armazenamento de blobs. Uma conta de armazenamento de uso geral fornece acesso a todos os quatro tipos de armazenamento. Contas de armazenamento de blobs são semelhantes às contas para uso geral, mas contêm blobs especializados que incluem as camadas de acesso quentes e frias. Para obter mais informações, consulte [Armazenamento de Blobs do Azure](../../storage/blobs/storage-blob-storage-tiers.md).

Contas de armazenamento do Azure podem ser configuradas com diferentes níveis de redundância:

-   **O armazenamento com redundância local** fornece alta disponibilidade, assegurando que três cópias de todos os dados sejam feitas de forma síncrona antes de uma gravação ser considerada bem-sucedida. Essas cópias são armazenadas em um único recurso em uma única região. As réplicas residem em domínios de falha e domínios de atualização separados. Isso significa que os dados ficam disponíveis mesmo se um nó de armazenamento que está contendo seus dados falha ou é deixado offline para ser atualizado.

-   O **armazenamento com redundância geográfica** faz três cópias síncronas dos dados na região primária para alta disponibilidade e faz três réplicas de forma assíncrona em uma região emparelhada para recuperação de desastre.

-   O **armazenamento com redundância geográfica com acesso de leitura** é o armazenamento com redundância geográfica e a capacidade de ler os dados na região secundária. Essa capacidade o torna adequado para a recuperação de desastre parcial. Se há um problema com a região primária, você pode alterar seu aplicativo para ter acesso somente leitura à região emparelhada.

### <a name="use-cases"></a>Casos de uso 

Cada tipo de armazenamento tem um caso de uso diferente.

**Armazenamento de Blobs** 

A palavra *blob* é um acrônimo para *objeto binário grande*. Blobs são arquivos não estruturados como aqueles que você armazena em seu computador. O Armazenamento de Blobs pode conter qualquer tipo de texto ou de dados binários, como um documento, um arquivo de mídia ou um instalador de aplicativo. O Armazenamento de Blobs também é chamado de armazenamento de objeto. O Armazenamento de Blobs do Azure também contém discos de dados de Máquinas Virtuais do Azure.

O Armazenamento do Azure dá suporte a três tipos de blobs:

-   **Blobs de blocos** são usados para manter arquivos comuns de até 195 GB (4 MB × 50.000 blocos). O caso de uso primário para blobs de bloco é o armazenamento de arquivos que são lidos a do início ao fim, como arquivos de mídia ou arquivos de imagem para sites. Eles são nomeados blobs de blocos porque arquivos maiores que 64 MB devem ser carregados como pequenos blocos. Esses blocos são, em seguida, consolidados ou confirmados no blob final.

-   **Blobs de páginas** são usados para manter arquivos de acesso aleatório até 1 TB de tamanho. Os blobs de páginas são usados principalmente como o armazenamento de backup para os VHDs que fornecem discos duráveis para Máquinas Virtuais do Azure, o serviço de computação de IaaS no Azure. Elas são nomeadas blobs de páginas porque eles fornecem acesso de leitura/gravação aleatória a páginas de 512 bytes.

-   **Blobs de acréscimo** consistem de blocos semelhantes aos blobs de blocos, mas são otimizados para operações de acréscimo. Eles são usados com frequência para registro de informações de uma ou mais fontes no mesmo Blob. Por exemplo, você pode escrever todo o log de rastreamento no mesmo blob de acréscimo para um aplicativo que está sendo executado em várias VMs. Um único blob de acréscimo pode ter até 195 GB.

Para obter mais informações, consulte [Introdução ao Armazenamento de Blobs do Azure usando o .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md).

**Armazenamento de arquivos**

O armazenamento de Arquivos do Azure é um serviço que oferece compartilhamentos de arquivos na nuvem usando o Protocolo SMB padrão. O serviço dá suporte a SMB 2.1 e a SMB 3.0. Com o armazenamento de Arquivos do Azure, você pode migrar aplicativos que dependem de compartilhamentos de arquivos para o Azure rapidamente e sem regravações caras. Os aplicativos executados em máquinas virtuais do Azure, em serviços de nuvem ou em clientes locais podem montar um compartilhamento de arquivos na nuvem. Isso é semelhante a como um aplicativo da área de trabalho monta um compartilhamento SMB típico. Qualquer quantidade de componentes de aplicativos pode montar e acessar o compartilhamento de armazenamento de arquivos simultaneamente.

Já que um compartilhamento do Armazenamento de arquivos é um compartilhamento de arquivos SMB padrão, os aplicativos executados no Azure podem acessar dados no compartilhamento por meio de APIs de E/S do sistema de arquivos. Os desenvolvedores podem, portanto, utilizar os códigos e habilidades existentes deles para migrar aplicativos existentes. Os profissionais de TI podem usar cmdlets do PowerShell para criar, montar e gerenciar compartilhamentos de armazenamento de arquivos como parte da administração de aplicativos do Azure.

Para obter mais informações, consulte [Introdução ao Armazenamento de Arquivos do Azure no Windows](../../storage/files/storage-how-to-use-files-windows.md) ou [Como usar o Armazenamento de Arquivos do Azure com o Linux](../../storage/files/storage-how-to-use-files-linux.md).

**Armazenamento de tabelas**

O Armazenamento de Tabelas do Azure é um serviço que armazena dados NoSQL estruturados na nuvem. O Armazenamento de Tabelas é um repositório de chaves/atributos com um design sem esquema. Como o armazenamento de Tabelas não tem um esquema, é fácil adaptar seus dados à medida que as necessidades de seu aplicativo evoluem. O acesso aos dados é rápido e econômico para todos os tipos de aplicativos. O armazenamento de tabela normalmente tem um custo significativamente mais baixo do que o SQL tradicional para volumes de dados semelhantes.

Você pode usar o armazenamento de tabela para armazenar conjuntos de dados flexíveis, como dados de usuário para aplicativos web, catálogos de endereços, informações sobre dispositivos e qualquer outro tipo de metadados que o serviço requeira. Você pode armazenar qualquer número de entidades em uma tabela. Uma conta de armazenamento pode conter um número ilimitado de tabelas, até o limite de capacidade da conta de armazenamento.

Para obter mais informações, consulte [Introdução ao Armazenamento de Tabelas do Azure](../../cosmos-db/table-storage-how-to-use-dotnet.md).

**Armazenamento de filas**

O armazenamento de filas do Azure fornece mensagens na nuvem entre os componentes do aplicativo. Na criação de aplicativos para escala, os componentes do aplicativo geralmente são desassociados para que possam ser dimensionados independentemente. O armazenamento de filas fornece mensagens assíncronas para a comunicação entre os componentes do aplicativo, estando eles em execução na nuvem, na área de trabalho, em um servidor local ou em um dispositivo móvel. O Armazenamento de fila também oferece suporte ao gerenciamento de tarefas assíncronas e à criação de fluxos de trabalho do processo.

Para obter mais informações, consulte [Introdução ao Armazenamento de Filas do Azure](../../storage/queues/storage-dotnet-how-to-use-queues.md).

### <a name="deploying-a-storage-account"></a>Implantar uma conta de armazenamento

Há várias opções para a implantação de uma conta de armazenamento.

**Portal**

Implantar uma conta de armazenamento usando o portal do Azure requer apenas uma assinatura ativa do Azure e o acesso a um navegador da Web. Você pode implantar uma nova conta de armazenamento em um grupo de recursos novo ou existente. Depois de criar a conta de armazenamento, você pode criar um compartilhamento de arquivo ou contêiner de blob usando o portal. Você pode criar as entidades de armazenamento de Tabela e de Filas programaticamente. Para obter mais informações, consulte [Criar uma conta de armazenamento](../../storage/common/storage-create-storage-account.md#create-a-storage-account).

Além de implantar uma conta de armazenamento do portal do Azure, você pode implantar um modelo do Azure Resource Manager por meio do portal. Isso implantará e configurará todos os recursos, conforme definido no modelo, incluindo quaisquer contas de armazenamento. Para obter mais informações, veja [Implantar recursos com modelos do Resource Manager e o portal do Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).

**PowerShell**

Implantar uma conta de armazenamento do Azure usando o PowerShell permite a automação da implantação completa da conta de armazenamento. Para obter mais informações, consulte [Usar o Azure PowerShell com o Armazenamento do Azure](../../storage/common/storage-powershell-guide-full.md).

Além de implantar recursos do Azure individualmente, você pode usar o módulo do Azure PowerShell para implantar um modelo do Azure Resource Manager. Para obter mais informações, veja [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**CLI (Interface da linha de comando)**

Assim como acontece com o módulo do PowerShell, a interface de linha de comando do Azure fornece automação de implantação e pode ser usada em sistemas Windows, OS X ou Linux. Você pode usar o comando **storage account create** da CLI do Azure para criar uma conta de armazenamento. Para obter mais informações, consulte [Usando a CLI do Azure com o Armazenamento do Azure](../../storage/common/storage-azure-cli.md).

Do mesmo modo, você pode usar uma CLI do Azure para implantar um modelo do Azure Resource Manager. Para obter mais informações, veja [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../../resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-azure-storage"></a>Acesso e segurança para o Armazenamento do Azure

O Armazenamento do Azure é acessado de várias maneiras, incluindo o Portal do Azure, durante a operação e a criação da VM e de bibliotecas de cliente de armazenamento. 

**Discos de máquina virtual**

Quando você estiver implantando uma máquina virtual, você também precisará criar uma conta de armazenamento para manter o disco do sistema operacional da máquina virtual e quaisquer discos de dados adicionais. Você pode selecionar uma conta de armazenamento existente ou criar uma nova. Como o tamanho máximo de um blob é 1.024 GB, um único disco de VM tem um tamanho máximo de 1.023 GB. Para configurar um disco de dados maior, você pode apresentar vários discos de dados para a máquina virtual e agrupá-los em pool como um único disco lógico. Para saber mais, veja "Gerenciar discos do Azure" para [Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) e [Linux](../../virtual-machines/linux/tutorial-manage-disks.md).

**Ferramentas de armazenamento**

Contas de armazenamento do Azure podem ser acessadas por meio de vários gerenciadores de armazenamento diferentes, assim como o Visual Studio Cloud Explorer. Essas ferramentas permitem navegar pelas contas de armazenamento e os dados. Para obter mais informações e uma lista de gerenciadores de armazenamento disponíveis, consulte [Ferramentas de cliente do Armazenamento do Azure](../../storage/common/storage-explorers.md).

**API de Armazenamento**

Os recursos de armazenamento podem ser acessados por qualquer linguagem que possa fazer solicitações HTTP/HTTPS. Além disso, o Armazenamento do Azure oferece bibliotecas de programação para várias linguagens populares. Essas bibliotecas simplificam o trabalho com o Armazenamento do Azure manipulando detalhes, como invocação síncrona e assíncrona, envio em lote de operações, gerenciamento de exceções e novas tentativas automáticas. Para saber mais, consulte [Referência da API REST do serviço de Armazenamento do Azure](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

**Chaves de acesso de armazenamento**

Cada conta de armazenamento tem duas chaves de autenticação, uma principal e uma secundária. Qualquer uma delas pode ser usada para operações de acesso de armazenamento. Essas chaves de armazenamento são usadas para ajudar a proteger uma conta de armazenamento e são necessárias para acessar os dados programaticamente. Há duas chaves para permitir a sobreposição ocasional das chaves para aprimorar a segurança. É fundamental para proteger as chaves porque sua posse, juntamente com o nome da conta, permite acesso ilimitado a quaisquer dados na conta de armazenamento.

**Assinaturas de acesso compartilhado**

Se você precisa permitir que os usuários tenham acesso controlado a seus recursos de armazenamento, você pode criar uma assinatura de acesso compartilhado. Uma assinatura de acesso compartilhado é um token que pode ser acrescentado a uma URL que permite acesso delegado a um recurso de armazenamento. Qualquer pessoa que tenha o token pode acessar o recurso para o qual ele aponta com as permissões que ele especifica, pelo período de tempo da sua validade. Para obter mais informações, confira [Como usar assinaturas de acesso compartilhado](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="azure-virtual-network"></a>Rede Virtual do Azure


Redes virtuais são necessárias para oferecer suporte a comunicações entre máquinas virtuais. Você pode definir sub-redes, endereços IP personalizados, configurações de DNS, filtragem de segurança e balanceamento de carga. Ao usar um gateway de VPN ou um circuito do ExpressRoute, você pode conectar redes virtuais do Azure a suas redes locais.

### <a name="use-cases"></a>Casos de uso

Há diferentes casos de uso para a rede do Azure.

**Redes virtuais somente na nuvem**

Uma rede virtual do Azure, por padrão, é acessível somente para recursos armazenados no Azure. Recursos conectado à mesma rede virtual podem se comunicar entre si. Você pode associar as interfaces de rede de máquina virtual e balanceadores de carga com um endereço IP público para tornar a máquina virtual acessível pela Internet. Você pode ajudar a proteger o acesso aos recursos expostos publicamente por meio de um grupo de segurança de rede.

**Redes virtuais entre instalações**

Você pode conectar uma rede local a uma rede virtual do Azure pelo uso de uma conexão do ExpressRoute ou uma conexão VPN site a site. Nessa configuração, a rede virtual do Azure é essencialmente uma extensão baseada em nuvem da sua rede local.

Já que a rede virtual do Azure é conectada à sua rede local, as redes virtuais entre instalações devem usar uma parte exclusiva do espaço de endereço usado por sua organização. Da mesma forma que diferentes localizações da empresa serão atribuídas a uma sub-rede IP específica, o Azure torna-se outra localização à medida que você estende sua rede.

###<a name="deploying-a-virtual-network"></a>Implantar uma rede virtual

Há várias opções para a implantação de uma rede virtual.

**Portal**

Implantar uma rede virtual do Azure usando o portal do Azure requer apenas uma assinatura ativa do Azure e o acesso a um navegador da Web. Você pode implantar uma nova rede virtual em um grupo de recursos novo ou existente. Quando você estiver criando uma nova máquina virtual por meio do portal, você pode selecionar uma rede virtual existente ou crie uma nova. Para obter mais informações, consulte [Criar uma rede virtual usando o Portal do Azure](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

Além de implantar uma rede virtual do Azure do portal do Azure, você pode implantar um modelo do Azure Resource Manager por meio do portal. Isso implantará e configurará todos os recursos, conforme definido no modelo, incluindo quaisquer recursos de rede virtual. Para obter mais informações, veja [Implantar recursos com modelos do Resource Manager e o portal do Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).

**PowerShell**

Implantar uma rede virtual do Azure usando o PowerShell permite a automação da implantação completa da conta de armazenamento. Para obter mais informações, consulte [Criar uma rede virtual usando o PowerShell](../../virtual-network/virtual-networks-create-vnet-arm-ps.md).

Além de implantar recursos do Azure individualmente, você pode usar o módulo do Azure PowerShell para implantar um modelo do Azure Resource Manager. Para obter mais informações, veja [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**CLI (Interface da linha de comando)**

Assim como acontece com o módulo do PowerShell, a interface de linha de comando do Azure fornece automação de implantação e pode ser usada em sistemas Windows, OS X ou Linux. Você pode usar o comando **network vnet create** da CLI do Azure para criar uma rede virtual. Para obter mais informações, consulte [Criar uma rede virtual usando a CLI do Azure](../../virtual-network/virtual-networks-create-vnet-arm-cli.md).

Do mesmo modo, você pode usar uma CLI do Azure para implantar um modelo do Azure Resource Manager. Para obter mais informações, veja [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-virtual-networks"></a>Acesso e segurança para redes virtuais

Você pode ajudar a proteger redes virtuais do Azure usando um grupo de segurança de rede. Os NSGs contêm uma lista de regras de ACL (lista de controle de acesso) que permitem ou negam o tráfego de rede para suas instâncias de VM em uma Rede Virtual. Você pode associar NSGs a sub-redes ou a instâncias de VM individuais dentro dessa sub-rede. Quando você associar um NSG a uma sub-rede, as regras de ACL se aplicam a todas as instâncias de VM nessa sub-rede. Além disso, você pode restringir ainda mais o tráfego para uma VM individual por meio da associação de um NSG diretamente a essa VM. Para obter mais informações, consulte [Filtrar o tráfego de rede com grupos de segurança de rede](../../virtual-network/virtual-networks-nsg.md).

## <a name="next-steps"></a>Próximas etapas

- [Criar uma VM do Windows](/virtual-machines/windows/quick-create-portal.md)
- [Criar uma VM Linux](../../virtual-machines/linux/quick-create-portal.md)

