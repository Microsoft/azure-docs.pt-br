---
title: Azure Lab Services - guia do administrador | Microsoft Docs
description: Este guia ajuda os administradores que criam e gerenciam contas de laboratório usando Azure Lab Services.
ms.topic: article
ms.date: 10/20/2020
ms.openlocfilehash: 3ad3ee38a6c08a6af85822d76012cc6dfc34ff4e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96462480"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services - guia do administrador
Os administradores de ti (tecnologia da informação) que gerenciam os recursos de nuvem de uma universidade são normalmente responsáveis por configurar a conta de laboratório para sua escola. Depois de configurar uma conta de laboratório, os administradores ou educadores criam os laboratórios que estão contidos na conta. Este artigo fornece uma visão geral de alto nível dos recursos do Azure que estão envolvidos e as diretrizes para criá-los.

![Diagrama de uma exibição de alto nível dos recursos do Azure em uma conta de laboratório.](./media/administrator-guide/high-level-view.png)

- Os laboratórios são hospedados em uma assinatura do Azure que pertence à Azure Lab Services.
- As contas de laboratório, uma galeria de imagens compartilhada e versões de imagem são hospedadas em sua assinatura.
- Você pode ter sua conta de laboratório e a galeria de imagens compartilhadas no mesmo grupo de recursos. Neste diagrama, elas estão em grupos de recursos diferentes.

Para obter mais informações sobre a arquitetura, consulte [conceitos básicos da arquitetura de laboratórios](./classroom-labs-fundamentals.md).

## <a name="subscription"></a>Subscription
Sua Universidade pode ter uma ou mais assinaturas do Azure. Você usa assinaturas para gerenciar a cobrança e a segurança de todos os recursos e serviços do Azure que são usados dentro dele, incluindo contas de laboratório.

A relação entre uma conta de laboratório e sua assinatura é importante porque:

- A cobrança é relatada por meio da assinatura que contém a conta de laboratório.
- Você pode conceder aos usuários o acesso de locatário do Azure Active Directory (Azure AD) da assinatura a Azure Lab Services. Você pode adicionar um usuário como um proprietário ou colaborador da conta de laboratório, ou como um criador de laboratório ou proprietário de laboratório.

Os laboratórios e suas VMs (máquinas virtuais) são gerenciados e hospedados para você em uma assinatura de propriedade de Azure Lab Services.

## <a name="resource-group"></a>Resource group
Uma assinatura contém um ou mais grupos de recursos. Os grupos de recursos são usados para criar agrupamentos lógicos de recursos do Azure que são usados em conjunto na mesma solução.  

Ao criar uma conta de laboratório, você deve configurar o grupo de recursos que contém a conta de laboratório. 

Um grupo de recursos também é necessário quando você cria uma [Galeria de imagens compartilhada](#shared-image-gallery). Você pode posicionar sua conta de laboratório e a Galeria de imagens compartilhadas no mesmo grupo de recursos ou em dois grupos de recursos separados. Talvez você queira adotar essa segunda abordagem se planeja compartilhar a Galeria de imagens em várias soluções.

Ao criar uma conta de laboratório, você pode criar e anexar automaticamente uma galeria de imagens compartilhadas ao mesmo tempo.  Essa opção resulta na conta de laboratório e na galeria de imagens compartilhadas sendo criadas em grupos de recursos diferentes. Você verá esse comportamento ao seguir as etapas descritas na [Galeria configurar imagem compartilhada no momento do tutorial de criação de conta do laboratório](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation) . A imagem no início deste artigo usa essa configuração. 

Recomendamos que você Invista o tempo de vida para planejar a estrutura de seus grupos de recursos, pois *não* é possível alterar uma conta de laboratório ou um grupo de recursos da Galeria de imagens compartilhada depois que ele é criado. Se precisar alterar o grupo de recursos para esses recursos, você precisará excluir e recriar sua conta de laboratório ou galeria de imagens compartilhadas.

## <a name="lab-account"></a>Conta de laboratório

Uma conta de laboratório serve como um contêiner para um ou mais laboratórios. Quando você estiver começando a usar o Azure Lab Services, é mais comum ter uma única conta de laboratório. À medida que o uso do seu laboratório for escalado verticalmente, você poderá optar por criar mais contas de laboratório mais tarde.

A lista a seguir realça os cenários em que mais de uma conta de laboratório pode ser benéfica:

- **Gerenciar requisitos de política diferentes nos laboratórios**

    Ao configurar uma conta de laboratório, você define as políticas que se aplicam a *todos os* laboratórios na conta do laboratório, como:
    - A rede virtual do Azure com recursos compartilhados que o laboratório pode acessar. Por exemplo, você pode ter um conjunto de laboratórios que precisam de acesso a um conjunto de dados compartilhado em uma rede virtual.
    - As imagens de máquina virtual que os laboratórios podem usar para criar VMs. Por exemplo, você pode ter um conjunto de laboratórios que precisam de acesso ao [VM de ciência de dados para](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) a imagem do Azure Marketplace do Linux.

    Se cada um dos seus laboratórios tiver requisitos de política exclusivos, talvez seja benéfico criar contas de laboratório separadas para gerenciar cada laboratório separadamente.

- **Atribuir um orçamento separado a cada conta de laboratório**
  
    Em vez de reportar todos os custos de laboratório por meio de uma única conta de laboratório, talvez seja necessário um orçamento mais bem divisões. Por exemplo, você pode criar contas de laboratório separadas para o departamento de matemática da sua universidade, o departamento de ciência da computação e assim por diante, para distribuir o orçamento entre os departamentos.  Em seguida, você pode exibir o custo de cada conta de laboratório individual usando o [Gerenciamento de custos do Azure](../cost-management-billing/cost-management-billing-overview.md).

- **Isole os laboratórios piloto dos laboratórios de produção ou ativos**
  
    Você pode ter casos em que deseja que as alterações de política piloto para uma conta de laboratório sem afetar os laboratórios de produção ou ativos. Nesse tipo de cenário, a criação de uma conta de laboratório separada para fins de piloto permite isolar as alterações. 

## <a name="lab"></a>Laboratório

Um laboratório contém VMs que são atribuídas a um único aluno.  Em geral, você pode esperar o seguinte:

- Ter um laboratório para cada classe.
- Crie um novo conjunto de laboratórios para cada semestre, trimestre ou outro sistema acadêmico que você esteja usando. Para classes que precisam usar a mesma imagem, você deve usar uma [Galeria de imagens compartilhadas](#shared-image-gallery). Dessa forma, você pode reutilizar imagens entre laboratórios e períodos acadêmicos.

Quando estiver determinando como estruturar seus laboratórios, considere os seguintes pontos:

- **Todas as VMs em um laboratório são implantadas com a mesma imagem que é publicada**

    Como resultado, se você tiver uma classe que exija que imagens de laboratório diferentes sejam publicadas ao mesmo tempo, um laboratório separado deverá ser criado para cada imagem.
  
- **A cota de uso é definida no nível de laboratório e se aplica a todos os usuários no laboratório**

    Para definir cotas diferentes para usuários, você deve criar laboratórios separados. No entanto, é possível adicionar mais horas a usuários específicos depois de definir a cota.
  
- **A agenda de inicialização ou desligamento é definida no nível do laboratório e se aplica a todas as VMs no laboratório**

    Semelhante à configuração de cota, se você precisar definir agendamentos diferentes para usuários, será necessário criar um laboratório separado para cada agenda.

Por padrão, cada laboratório tem sua própria rede virtual.  Se você tiver o emparelhamento de rede virtual habilitado, cada laboratório terá sua própria sub-rede emparelhada com a rede virtual especificada.

## <a name="shared-image-gallery"></a>Galeria de imagens compartilhadas

Uma galeria de imagens compartilhadas é anexada a uma conta de laboratório e funciona como um repositório central para armazenar imagens. Uma imagem é salva na Galeria quando um professor opta por exportá-la da VM de modelo de um laboratório. Cada vez que um professor faz alterações na VM de modelo e exporta-a, novas versões da imagem são salvas e as versões anteriores são mantidas.

Os instrutores podem publicar uma versão de imagem da Galeria de imagens compartilhadas ao criar um novo laboratório. Embora a Galeria armazene várias versões de uma imagem, os educadores podem selecionar apenas a versão mais recente durante a criação do laboratório.

O serviço da Galeria de imagens compartilhadas é um recurso opcional que talvez não seja necessário imediatamente se você estiver começando com apenas alguns laboratórios. No entanto, a Galeria de imagens compartilhadas oferece muitos benefícios que são úteis à medida que você escala verticalmente para laboratórios adicionais:

- **Você pode salvar e gerenciar versões de uma imagem de VM de modelo**

    É útil criar uma imagem personalizada ou fazer alterações (software, configuração e assim por diante) em uma imagem da Galeria pública do Azure Marketplace.  Por exemplo, é comum que educadores exijam software ou ferramentas diferentes instalados. Em vez de exigir que os alunos instalem manualmente esses pré-requisitos por conta própria, diferentes versões da imagem de VM de modelo podem ser exportadas para uma galeria de imagens compartilhadas. Você pode usar essas versões de imagem ao criar novos laboratórios.

- **Você pode compartilhar e reutilizar imagens de VM de modelo nos laboratórios**

    Você pode salvar e reutilizar uma imagem para que não precise configurá-la do zero toda vez que criar um novo laboratório. Por exemplo, se várias classes precisarem usar a mesma imagem, você poderá criá-la uma vez e exportá-la para a Galeria de imagens compartilhadas para que ela possa ser compartilhada entre os laboratórios.

- **A disponibilidade da imagem é garantida por meio da replicação automática**

    Quando você salva uma imagem de um laboratório na Galeria de imagens compartilhadas, ela é replicada automaticamente para outras [regiões na mesma geografia](https://azure.microsoft.com/global-infrastructure/regions/). Se houver uma interrupção para uma região, a publicação da imagem em seu laboratório não será afetada, pois uma réplica de imagem de outra região pode ser usada.  A publicação de VMs de várias réplicas também pode ajudar no desempenho.

Para agrupar logicamente as imagens compartilhadas, você pode fazer o seguinte:

- Criar várias galerias de imagens compartilhadas. Cada conta de laboratório pode se conectar a apenas uma galeria de imagens compartilhadas, portanto, essa opção também exige que você crie várias contas de laboratório.
- Use uma única Galeria de imagens compartilhada compartilhada por várias contas de laboratório. Nesse caso, cada conta de laboratório pode habilitar apenas as imagens aplicáveis aos laboratórios dessa conta.

## <a name="naming"></a>Nomenclatura

Ao começar a usar o Azure Lab Services, recomendamos que você estabeleça convenções de nomenclatura para grupos de recursos, contas de laboratório, laboratórios e a Galeria de imagens compartilhadas. Embora as convenções de nomenclatura estabelecidas sejam exclusivas às necessidades da sua organização, a tabela a seguir fornece diretrizes gerais:

| Tipo de recurso | Função | Padrão sugerido | Exemplos |
| ------------- | ---- | ----------------- | -------- | 
| Resource group | Contém uma ou mais contas de laboratório e uma ou mais galerias de imagens compartilhadas | \<organization short name\>-\<environment\>-RG<ul><li>**Nome curto da organização** identifica o nome da organização à qual o grupo de recursos dá suporte.</li><li>**Ambiente** identifica o ambiente para o recurso, como *piloto* ou *produção*.</li><li>**RG** significa o *grupo de recursos* do tipo de recurso.</li></ul> | contosouniversitylabs-rg<br/>contosouniversitylabs-pilot-rg<br/>contosouniversitylabs-prod-rg |
| Conta de laboratório | Contém um ou mais laboratórios | \<organization short name\>-\<environment\>-la<ul><li>**Nome curto da organização** identifica o nome da organização à qual o grupo de recursos dá suporte.</li><li>**Ambiente** identifica o ambiente para o recurso, como *piloto* ou *produção*.</li><li>**La** significa a *conta de laboratório* do tipo de recurso.</li></ul> | contosouniversitylabs-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-pilot-la<br/>sciencedeptlabs-prod-la |
| Laboratório | Contém uma ou mais VMs |\<class name\>-\<timeframe\>-\<educator identifier\><ul><li>**Nome da classe** identifica o nome da classe à qual o laboratório dá suporte.</li><li>**Período de tempo** identifica o período no qual a classe é oferecida.</li>O **identificador do professor** identifica o professor que possui o laboratório.</li></ul> | CS1234-fall2019-diogomartins<br/>CS1234-spring2019-diogomartins |
| Galeria de imagens compartilhadas | Contém uma ou mais versões da imagem da VM | \<organization short name\>clip | contosouniversitylabsgallery |

Para obter mais informações sobre como nomear outros recursos do Azure, consulte [convenções de nomenclatura para recursos do Azure](/azure/architecture/best-practices/naming-conventions).

## <a name="regionslocations"></a>Regiões\locais

Ao configurar seus recursos de Azure Lab Services, será necessário fornecer uma região ou local do datacenter que hospedará os recursos. As seções a seguir descrevem como uma região ou local podem afetar cada recurso envolvido na configuração de um laboratório.

### <a name="resource-group"></a>Resource group

A região especifica o datacenter onde as informações sobre um grupo de recursos são armazenadas. Os recursos do Azure contidos no grupo de recursos podem estar em uma região diferente da do seu pai.

### <a name="lab-account"></a>Conta de laboratório

O local de uma conta de laboratório indica a região em que um recurso existe.  

### <a name="lab"></a>Laboratório

O local em que um laboratório existe varia, dependendo dos seguintes fatores:

  - **A conta do laboratório é emparelhada com uma rede virtual**
  
    Você pode [emparelhar uma conta de laboratório com uma rede virtual](./how-to-connect-peer-virtual-network.md) quando ela estiver na mesma região.  Quando uma conta de laboratório é emparelhada com uma rede virtual, os laboratórios são criados automaticamente na mesma região que a conta de laboratório e a rede virtual.

    > [!NOTE]
    > Quando uma conta de laboratório é emparelhada com uma rede virtual, a configuração **permitir que o criador do laboratório selecione o local do laboratório** está desabilitada. Para obter mais informações, consulte [Permitir que o criador do laboratório escolha o local do laboratório](./allow-lab-creator-pick-lab-location.md).
    
  - **Nenhuma rede virtual está emparelhada *e* os criadores de laboratório não têm permissão para escolher o local do laboratório**
  
    Quando *nenhuma* rede virtual está emparelhada com a conta de laboratório e os [criadores de laboratório *não têm permissão* para escolher o local do laboratório, os](./allow-lab-creator-pick-lab-location.md)laboratórios são criados automaticamente em uma região com capacidade de VM disponível.  Especificamente, o Azure Lab Services buscará disponibilidade em regiões [que estejam na mesma área geográfica que a conta de laboratório](https://azure.microsoft.com/global-infrastructure/regions).

  - **Nenhuma rede virtual está emparelhada *e* os criadores de laboratório têm permissão para escolher o local do laboratório**
       
    Quando *nenhuma* rede virtual é emparelhada e os [criadores de laboratório *têm permissão* para escolher o local do laboratório](./allow-lab-creator-pick-lab-location.md), os locais que podem ser selecionados pelo criador do laboratório dependem da capacidade disponível.

> [!NOTE]
> Para ajudar a garantir que uma região tenha capacidade de VM suficiente, é importante solicitar primeiro a capacidade por meio da conta de laboratório quando você estiver criando o laboratório.

Uma regra geral é definir a região de um recurso para uma que seja mais próxima de seus usuários. Para os laboratórios, isso significa criar o laboratório mais próximo de seus alunos. Para cursos online cujos alunos estejam localizados em todo o mundo, use o melhor julgamento para criar um laboratório localizado centralmente. Ou você pode dividir uma classe em vários laboratórios de acordo com as regiões dos seus alunos.

## <a name="vm-sizing"></a>Dimensionamento da VM

Quando os administradores ou os criadores de laboratório criam um laboratório, eles podem escolher entre uma variedade de tamanhos de VM, dependendo das necessidades de sua sala de aula. Lembre-se de que a disponibilidade de tamanho de computação depende da região em que sua conta de laboratório está localizada.

| Tamanho | Especificações | Série | Uso sugerido |
| ---- | ----- | ------ | ------------- |
| Pequena| <ul><li>2 &nbsp; núcleos</li><li>3,5 gigabytes (GB) de RAM</li> | [Standard_A2_v2](../virtual-machines/av2-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Mais adequado para linha de comando, abertura de navegador da Web, servidores Web de tráfego baixo, bancos de dados pequenos a médios. |
| Médio | <ul><li>4 &nbsp; núcleos</li><li>7 &nbsp; GB de &nbsp; RAM</li> | [Standard_A4_v2](../virtual-machines/av2-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Mais adequado para bancos de dados relacionais, cache na memória e análise. |
| Médio (virtualização aninhada) | <ul><li>4 &nbsp; núcleos</li><li>16 &nbsp; GB de &nbsp; RAM</li></ul> | [Standard_D4s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#dsv3-series) | Mais adequado para bancos de dados relacionais, cache na memória e análise.
| Grande | <ul><li>8 &nbsp; núcleos</li><li>16 &nbsp; GB de &nbsp; RAM</li></ul>  | [Standard_A8_v2](../virtual-machines/av2-series.md) | Mais adequado para aplicativos que precisam de CPUs mais rápidas, melhor desempenho de disco local, bancos de dados grandes, caches de memória grandes.  Este tamanho também dá suporte à virtualização aninhada. |
| Grande (virtualização aninhada) | <ul><li>8 &nbsp; núcleos</li><li>32 &nbsp; GB de &nbsp; RAM</li></ul>  | [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#dsv3-series) | Mais adequado para aplicativos que precisam de CPUs mais rápidas, melhor desempenho de disco local, bancos de dados grandes, caches de memória grandes. |
| GPU pequena (visualização) | <ul><li>6 &nbsp; núcleos</li><li>56 &nbsp; GB de &nbsp; RAM</li>  | [Standard_NV6](../virtual-machines/nv-series.md) | Mais adequado para visualização remota, streaming, jogos e codificação usando estruturas como OpenGL e DirectX. |
| GPU Pequena (Computação) | <ul><li>6 &nbsp; núcleos</li><li>56 &nbsp; GB de &nbsp; RAM</li></ul>  | [Standard_NC6](../virtual-machines/nc-series.md) |Mais adequado para aplicativos com uso intensivo de computador, como ia e aprendizado profundo. |
| GPU média (visualização) | <ul><li>12 &nbsp; núcleos</li><li>112 &nbsp; GB de &nbsp; RAM</li></ul>  | [Standard_NV12](../virtual-machines/nv-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Mais adequado para visualização remota, streaming, jogos e codificação usando estruturas como OpenGL e DirectX. |

## <a name="manage-identity"></a>Gerenciar identidade

Usando o [RBAC (controle de acesso baseado em função) do Azure](../role-based-access-control/overview.md) para acesso a contas de laboratório e laboratórios, você pode atribuir as seguintes funções:

- **Proprietário** da conta do laboratório

    Um administrador que cria uma conta de laboratório recebe automaticamente a função de proprietário de conta de laboratório. A função de proprietário pode:
     - Altere as configurações de conta do laboratório.
     - Conceder a outros administradores acesso à conta de laboratório como um proprietário ou colaborador.
     - Conceder aos educadores acesso a laboratórios como criador, proprietário ou colaborador.
     - Crie e gerencie todos os laboratórios na conta do laboratório.

- **Colaborador** de conta de laboratório

    Um administrador ao qual foi atribuída a função Colaborador pode:
    - Altere as configurações de conta do laboratório.
    - Crie e gerencie todos os laboratórios na conta do laboratório.

    No entanto, o colaborador *não pode* conceder a outros usuários acesso a contas de laboratório ou laboratórios.

- **Criador de laboratório**

    Para criar laboratórios em uma conta de laboratório, um professor deve ser membro da função de criador de laboratório.  Um professor que cria um laboratório é adicionado automaticamente como um proprietário de laboratório. Para obter mais informações, consulte [Adicionar um usuário à função criador de laboratório](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role). 

- **Proprietário** ou **colaborador** do laboratório
  
    Um professor em uma função de proprietário ou colaborador do laboratório pode exibir e alterar as configurações de um laboratório. A pessoa também deve ser membro da função de leitor de conta de laboratório.

    Uma diferença importante entre o proprietário do laboratório e as funções de colaborador é que apenas um proprietário pode conceder a outros usuários acesso para gerenciar um laboratório. Um colaborador *não pode* conceder a outros usuários acesso para gerenciar um laboratório.

- **Galeria de imagens compartilhadas**

    Quando você anexa uma galeria de imagens compartilhada a uma conta de laboratório, os proprietários e colaboradores de conta de laboratório e os criadores de laboratório, os proprietários de laboratório e os colaboradores de laboratório recebem automaticamente acesso para exibir e salvar imagens na galeria.

Quando você está atribuindo funções, ele ajuda a seguir estas dicas:

   - Normalmente, somente os administradores devem ser membros de uma função de colaborador ou proprietário de conta de laboratório. A conta de laboratório pode ter mais de um proprietário ou colaborador.
   - Para dar aos educadores a capacidade de criar novos laboratórios e gerenciar os laboratórios que eles criam, você só precisa atribuir a função de criador de laboratório.
   - Para dar aos educadores a capacidade de gerenciar laboratórios específicos, mas *não* a capacidade de criar novos laboratórios, atribua-os à função de proprietário ou colaborador para cada laboratório que eles gerenciarão. Por exemplo, talvez você queira permitir que um professor e um assistente de ensino coexistam um laboratório. Para obter mais informações, consulte [Adicionar proprietários a um laboratório](./how-to-add-user-lab-owner.md).

## <a name="pricing"></a>Preços

### <a name="azure-lab-services"></a>Azure Lab Services

Para saber mais sobre preços, consulte [preços de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).


### <a name="shared-image-gallery"></a>Galeria de imagens compartilhadas

Você também precisa considerar o preço do serviço da Galeria de imagens compartilhadas se planeja usar galerias de imagens compartilhadas para armazenar e gerenciar versões de imagem. 

Criar uma galeria de imagens compartilhadas e anexá-la à sua conta de laboratório é gratuito. Nenhum custo é incorrido até que você salve uma versão de imagem na galeria. O preço para usar uma galeria de imagens compartilhada normalmente é bastante insignificante, mas é importante entender como ele é calculado, pois não está incluído no preço de Azure Lab Services.  

#### <a name="storage-charges"></a>Encargos de armazenamento

Para armazenar versões de imagem, uma galeria de imagens compartilhadas usa discos gerenciados por HDD (unidade de disco rígido) padrão. O tamanho do disco gerenciado por HDD que é usado depende do tamanho da versão da imagem que está sendo armazenada. Para saber mais sobre preços, consulte [preços de Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).

#### <a name="replication-and-network-egress-charges"></a>Encargos de saída de rede e replicação

Quando você salva uma versão de imagem usando uma VM de modelo de laboratório, Azure Lab Services primeiro as armazena em uma região de origem e, em seguida, replica automaticamente a versão da imagem de origem para uma ou mais regiões de destino. 

É importante observar que Azure Lab Services replica automaticamente a versão da imagem de origem para todas as [regiões de destino na geografia](https://azure.microsoft.com/global-infrastructure/regions/) onde o laboratório está localizado. Por exemplo, se o laboratório estiver na geografia dos EUA, uma versão de imagem será replicada para cada uma das oito regiões existentes nos EUA.

Um encargo de saída de rede ocorre quando uma versão de imagem é replicada da região de origem para regiões de destino adicionais. O valor cobrado é baseado no tamanho da versão da imagem quando os dados são transferidos inicialmente da região de origem.  Para obter detalhes de preços, consulte [detalhes de preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

Os encargos de egresso podem ser cancelados para clientes de [soluções educacionais](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) . Para saber mais, entre em contato com seu gerente de conta. 

Para obter mais informações, consulte "quais programas de transferência de dados existem para clientes acadêmicos e como posso me qualificar?" na seção de perguntas frequentes da página [programas para instituições educacionais](https://azure.microsoft.com/pricing/details/bandwidth/) .

#### <a name="pricing-example"></a>Exemplo de preço

Vejamos um exemplo do custo de salvar uma imagem de VM de modelo em uma galeria de imagens compartilhada. Considere os seguintes cenários:

- Você tem uma imagem de VM personalizada.
- Você está salvando duas versões da imagem.
- Seu laboratório está nos EUA, que tem um total de oito regiões.
- Cada versão de imagem tem 32 GB de tamanho. Consequentemente, o preço do disco gerenciado por HDD é de US$ 1,54 por mês.

O custo total por mês é estimado como:

* *Número de imagens número de &times; versões &times; número de réplicas &times; preço de disco gerenciado = custo total por mês*

Neste exemplo, o custo é:

* 1 imagem personalizada (32 GB) &times; 2 versões &times; 8 regiões dos EUA &times; $1.54 = $24.64 por mês

> [!NOTE]
> O cálculo anterior é apenas para fins de exemplo. Ele aborda os custos de armazenamento associados ao uso da Galeria de imagens compartilhadas e *não* inclui custos de egresso. Para obter os preços reais do armazenamento, consulte [Managed disks preços](https://azure.microsoft.com/en-us/pricing/details/managed-disks/).

#### <a name="cost-management"></a>Gerenciamento de custo

É importante que os administradores de contas de laboratório gerenciem os custos, excluindo rotineiramente as versões de imagem desnecessárias da galeria. 

Não exclua a replicação para regiões específicas como uma maneira de reduzir os custos, embora essa opção exista na Galeria de imagens compartilhadas. As alterações de replicação podem ter efeitos adversos sobre a capacidade de Azure Lab Services publicar VMs de imagens salvas em uma galeria de imagens compartilhada.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como configurar e gerenciar laboratórios, consulte:

- [Guia de configuração da conta do laboratório](account-setup-guide.md)  
- [Guia de configuração do laboratório](setup-guide.md)  
- [Gerenciamento de custos para laboratórios](cost-management-guide.md)  
- [Usar Azure Lab Services em equipes](lab-services-within-teams-overview.md)
