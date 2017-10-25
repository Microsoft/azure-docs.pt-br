---
title: "Governança no Azure | Microsoft Docs"
description: "Aprenda sobre os serviços de computação baseados em nuvem que incluem uma ampla seleção de instâncias e serviços de computação, os quais podem ser escalados verticalmente de forma automática para atender às necessidades de seu aplicativo ou empresa."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/01/2017
ms.author: TomSh
ms.openlocfilehash: f3fbca281dbbfca7ff9392d003429719c589afbd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="governance-in-azure"></a>Governança no Azure

Sabemos que a segurança é o primeiro trabalho na nuvem e o quanto é importante que você encontre informações precisas e atualizadas sobre a segurança do Azure. Uma das melhores razões para usar o Azure para seus aplicativos e serviços é aproveitar sua ampla variedade de ferramentas e recursos de segurança. Essas ferramentas e recursos ajudam a tornar possível criar soluções seguras na plataforma segura do Azure.

Para ajudar você a entender melhor a matriz de controles de Governança implementada no Microsoft Azure das perspectivas do cliente e das operações da Microsoft, este artigo, “Governança no Azure”, foi elaborado para fornecer uma visão abrangente dos recursos do Governance disponíveis com o Microsoft Azure.

## <a name="azure-platform"></a>Plataforma do Azure

O Azure é uma plataforma de serviço de nuvem pública que dá suporte a uma ampla seleção de sistemas operacionais, linguagens de programação, estruturas, ferramentas, bancos de dados e dispositivos. Ele pode executar contêineres do Linux com a integração com o Docker, criar aplicativos com JavaScript, Python, .NET, PHP, Java e Node.js e criar back-ends para dispositivos iOS, Android e Windows. Os serviços de nuvem pública do Azure dão suporte às mesmas tecnologias com as quais milhões de desenvolvedores e profissionais de TI já contam e nas quais confiam.

Quando você compila ou migra ativos de TI para um provedor de serviços de nuvem pública, você está confiando na capacidade dessa organização de proteger seus aplicativos e dados com os serviços e os controles que ela oferece para gerenciar a segurança de seus ativos baseados em nuvem.

A infraestrutura do Azure foi projetada desde a instalação até os aplicativos para hospedar milhões de clientes simultaneamente e fornecer uma base confiável com a qual as empresas podem atender aos seus requisitos de segurança. Além disso, o Azure fornece muitas opções de segurança e a habilidade de controlar elas para que você possa personalizar a segurança para satisfazer os requisitos das implantações de sua organização.

Este documento ajudará você a entender como as funcionalidades do Governance do Azure podem ajudá-lo a atender a esses requisitos.

## <a name="abstract"></a>Resumo

Governança de nuvem do Microsoft Azure fornece uma auditoria integrada e abordagem de consultoria para revisar e aconselhar organizações no uso da plataforma Azure. Governança de nuvem do Microsoft Azure se refere aos processos de tomada de decisão, critérios e políticas envolvidas no planejamento, arquitetura, aquisição, implantação, operação e gerenciamento de uma nuvem de computação.

Para criar um plano para governança de nuvem do Microsoft Azure, você precisa olhar profundamente para as pessoas, processos e tecnologias atualmente em vigor, e depois criar estruturas que tornam mais fácil para o departamento de TI para consistentemente dar suporte às necessidades de negócios, proporcionando aos usuários finais a flexibilidade para usar os recursos avançados do Microsoft Azure.

Este documento descreve como você pode obter um nível elevado de governança de recursos de TI no Microsoft Azure. Este documento pode ajudá-lo a entender os recursos de segurança e governança integrados do Microsoft Azure.

Estes são os principais problemas de governança abordados neste documento:

- Implementação de políticas, processos e procedimentos de acordo com as metas da organização.

- Segurança e conformidade contínua com os padrões da organização

- Monitoramento e alertas

## <a name="implementation-of-policies-processes-and-procedures"></a>Implementação de políticas, processos e procedimentos 

Gerenciamento estabeleceu funções e responsabilidades para acompanhar a implementação de política de segurança de informações e continuidade operacional no Azure. Gerenciamento do Microsoft Azure é responsável por supervisionar a segurança e as práticas de continuidade em suas respectivas equipes (incluindo terceiros) e facilitar a conformidade com padrões, processos e políticas de segurança.

Aqui estão os fatores que evoluíram:

- Provisionamento de conta de usuário

- Controles de assinatura

- Controle de Acesso Baseado em Função

- Gerenciamento de recursos

- Acompanhamento de recursos

- Controle de recursos críticos

- API de Acesso a Informações De Cobrança

- Controles de rede

## <a name="account-provisioning"></a>Provisionamento de conta

Definição de hierarquia de contas é uma etapa importante para usar e a estruturar os serviços do Azure dentro de uma empresa e é a estrutura de governança central. Em caso de clientes com contrato empresarial, os clientes podem subdividir o ambiente em departamentos, contas e, por fim, em assinaturas.

![Provisionamento de conta de usuário](./media/governance-in-azure/security-governance-in-azure-fig1.png)

Se você não tiver um contrato empresarial, considere o uso de [marcas Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) no nível de assinatura para definir a hierarquia. Uma assinatura do Azure é a unidade básica onde todos os recursos estão contidos. Ela também define vários limites dentro do Azure, como o número de núcleos, recursos, etc. As assinaturas podem conter [Grupos de Recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), que podem conter Recursos. Princípios [RBAC](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control) se aplicam nesses três níveis.

Cada empresa é diferente e a hierarquia usando Marcas Azure em caso de clientes não empresariais permite flexibilidade significativa em como o Azure é organizado dentro da empresa. Antes de implantar os recursos do Microsoft Azure, você deve modelar a hierarquia e entender o impacto na cobrança, o acesso a recursos e a complexidade.

## <a name="subscription-controls"></a>Controles de assinatura

Assinatura controla como o uso de recursos é relatado e cobrado. As assinaturas podem ser configuradas para cobrança e pagamento separados. Como mencionado anteriormente, em uma conta do Azure pode haver várias assinaturas. As assinaturas podem ser usadas para determinar o uso de recursos do Azure de vários departamentos em uma empresa.

Por exemplo, se uma empresa tiver departamentos de RH, TI e de Marketing e esses departamentos têm diferentes projetos em execução. Com base no uso de recursos do Azure como máquinas virtuais para cada departamento, eles podem ser cobrados de acordo. Por isso podemos controlar as finanças de cada departamento.

As assinaturas do Azure estabelecem três parâmetros:

- uma ID exclusiva do assinante

- um local de cobrança

- Conjunto de recursos disponíveis

De um indivíduo, que inclui uma ID de conta da Microsoft, um número de cartão de crédito e o conjunto completo de serviços do Azure – embora, Microsoft impõe limites de consumo, dependendo do tipo de assinatura.

Hierarquias de registro do Azure definem como os serviços são estruturados dentro de um Contrato Empresarial. O Portal da Empresa permite aos clientes dividir o acesso aos recursos do Azure associados com um Contrato Empresarial com base em hierarquias flexíveis personalizáveis às necessidades exclusivas de uma organização. O padrão de hierarquia deve corresponder o gerenciamento e a estrutura geográfica de uma organização para que o acesso de recursos e de cobrança associados possam ser contabilizados com precisão.

Os três padrões de alto nível são unidades funcionais e comerciais, e geográficas, usando departamentos como uma construção administrativa para agrupamentos de conta. Dentro de cada departamento, contas podem ter assinaturas atribuídas, as quais criam silos de cobrança e vários limites de chave no Azure (por exemplo, o número de VMs, contas de armazenamento, etc.).

![Controles de assinatura](./media/governance-in-azure/security-governance-in-azure-fig2.png)


Para organizações com um Acordo Empresarial, as assinaturas do Azure seguem uma hierarquia de quatro níveis:

- administrador de registro corporativo

- administrador de departamento

- proprietário da conta

- Administrador de serviço

Essa hierarquia rege o seguinte:

- Relação de cobrança

- Administração de conta

- Controle de Acesso Baseado em Função (RBAC) para artefatos

- Limites/limites

- Limites

  - Uso e cobrança (cartão de taxa baseada nos números de oferta)

  - limites

  - Rede Virtual

- Anexado a 1 AAD (1 AAD pode ser associado a várias assinaturas)

- Associado a uma conta de registro da empresa

## <a name="role-based-access-controls"></a>Controle de acesso baseado em função

Quando o Azure foi inicialmente lançado, os controles de acesso para uma assinatura eram básicos: Administrador ou Coadministrador. O acesso a uma assinatura no modelo Clássico implicava acesso a todos os recursos no portal. Essa falta de um controle refinado levou à proliferação de assinaturas para fornecer um nível de controle de acesso razoável para uma Inscrição no Azure.

![Controle de acesso baseado em função](./media/governance-in-azure/security-governance-in-azure-fig3.png)

Essa proliferação de assinaturas não é mais necessária. Com o controle de acesso baseado em função, você pode atribuir usuários às funções padrão (como tipos de função comuns "leitor" e "gravador"). Você também pode definir funções personalizadas.

O [Controle De Acesso Baseado Em Função do Azure (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) permite o gerenciamento de acesso refinado para o Azure. Usando o RBAC, você pode conceder apenas a quantidade de acesso que os usuários precisam para realizar seus trabalhos. As empresas direcionadas a segurança devem focar em fornecer aos funcionários as permissões exatas necessárias. Muitas permissões expõem uma conta a ataques. Permissões insuficientes fazem com que os funcionários não consigam trabalhar com eficiência. O RBAC (controle de acesso baseado em função) do Azure ajuda a resolver esse problema ao oferecer o gerenciamento de acesso refinado para o Azure. O RBAC te ajuda a separar as tarefas dentro de sua equipe e conceder somente a quantidade de acesso que os usuários precisam para realizar seus trabalhos. Em vez de apresentar todos irrestrito permissões em sua assinatura do Azure ou recursos, você pode permitir apenas determinadas ações.

Por exemplo, use o RBAC para permitir que um funcionário gerencie máquinas virtuais em uma assinatura, enquanto outro pode gerenciar bancos de dados SQL dentro da mesma assinatura.

O RBAC do Azure tem três funções básicas que se aplicam a todos os tipos de recurso:

- **proprietário** tem acesso total a todos os recursos, inclusive o direito de delegar acesso a outros usuários.

- **Colaborador** pode criar e gerenciar todos os tipos de recursos do Azure, mas não pode conceder acesso a outras pessoas.

- **leitor** pode exibir os recursos existentes do Azure.

As demais funções RBAC no Azure permitem o gerenciamento de recursos específicos do Azure. Por exemplo, a função Colaborador de Máquina Virtual permite que o usuário crie e gerencie máquinas virtuais. Ela não lhes concede acesso à rede virtual ou à sub-rede com qual a máquina virtual se conecta.

[Funções internas RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) lista as funções disponíveis no Azure. Ela especifica as operações e o escopo que cada função interna concede aos usuários.

Conceda acesso ao atribuir a função RBAC apropriada a usuários, grupos e aplicativos em determinado escopo. O escopo de uma atribuição de função pode ser uma assinatura, um grupo de recursos ou um único recurso. Uma função atribuída a um escopo pai também concede acesso aos filhos contidos nele.

Por exemplo, um usuário com acesso a um grupo de recursos pode gerenciar todos os recursos que ele contém, como sites, máquinas virtuais e sub-redes.

O RBAC do Azure tem suporte somente para as operações de gerenciamento dos recursos do Azure no Portal do Azure e nas APIs do Azure Resource Manager. Ele não pode autorizar todas as operações no nível de dados para os recursos do Azure. Por exemplo, você pode autorizar alguém para gerenciar Contas de Armazenamento, mas não para blobs ou tabelas em uma Conta de Armazenamento. Da mesma forma, um banco de dados SQL pode ser gerenciado, mas não as tabelas dentro dele.

Se você quiser saber mais sobre como o RBAC ajuda você a gerenciar o acesso, confira [O que é Controle de Acesso Baseado em Função](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is).

Você pode também [criar uma função personalizada](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) no Controle de Acesso Baseado em Função do Azure (RBAC) se nenhuma das funções internas atender às suas necessidades de acesso específicas. É possível criar funções personalizadas usando o [Azure PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell), a [interface de linha de comando (CLI) do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-azure-cli) e a [API REST](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-rest). Assim como as funções internas, as funções personalizadas podem ser atribuídas a usuários, grupos e aplicativos na assinatura, no grupo de recursos e nos escopos de recurso.

Dentro de cada assinatura, você pode conceder até 2000 atribuições de função.

## <a name="resource-management"></a>Gerenciamento de recursos

Originalmente, o Azure fornecia o modelo de implantação clássico. Nesse modelo, cada recurso existia independentemente; não havia uma maneira de agrupar recursos relacionados. Em vez disso, era necessário controlar manualmente quais recursos compunham sua solução ou aplicativo, e lembrar-se de gerenciá-los em uma abordagem coordenada.

Para implantar uma solução, você precisava criar cada recurso individualmente por meio do portal clássico ou criar um script que implantava todos os recursos na ordem correta. Para excluir uma solução, você precisava excluir cada recurso individualmente. Não era possível aplicar e atualizar facilmente políticas de controle de acesso para recursos relacionados. Por fim, você não podia aplicar marcas aos recursos para rotulá-los com os termos que ajudariam a monitorar seus recursos e gerenciar a cobrança.

Em 2014, o Azure introduziu o Resource Manager, que adicionou o conceito de um grupo de recursos. Um grupo de recursos é um contêiner de recursos que compartilham um ciclo de vida comum. O modelo de implantação do Gerenciador de Recursos fornece vários benefícios:

- Você pode implantar, gerenciar e monitorar todos os serviços da sua solução como um grupo, em vez de tratá-los individualmente.

- Você pode implantar a solução repetidamente em todo seu ciclo de vida e com a confiança de que seus recursos serão implantados em um estado consistente.

- Você pode aplicar o controle de acesso a todos os recursos em seu grupo de recursos, e essas políticas são aplicadas automaticamente aos novos recursos adicionados ao grupo de recursos.

- Você pode aplicar marcas aos recursos para organizar de modo lógico todos os recursos em sua assinatura.

- Você pode usar a notação JSON (JavaScript Object Notation) para definir a infraestrutura de sua solução. O arquivo JSON é conhecido como um modelo do Resource Manager.

- Você pode definir as dependências entre os recursos para que eles sejam implantados na ordem correta.

![Gerenciamento de recursos](./media/governance-in-azure/security-governance-in-azure-fig4.png)

O Resource Manager permite colocar recursos em grupos significativos para gerenciamento, cobrança ou afinidade natural. Conforme mencionado anteriormente, o Azure tem dois modelos de implantação. No [modelo Clássico](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) mais antigo, a unidade básica de gerenciamento era a assinatura. Era difícil dividir recursos em uma assinatura, o que levou à criação de um grande número de assinaturas. Com o modelo do Resource Manager, vimos a introdução dos grupos de recursos.

Um grupo de recursos é um contêiner que mantém os recursos relacionados a uma solução do Azure. [O grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) pode incluir todos os recursos para a solução ou apenas os recursos que você deseja gerenciar como um grupo. Você decide como deseja alocar recursos para grupos de recursos com base no que faz mais sentido para sua organização.

Para recomendações sobre modelos, consulte [Práticas recomendadas para criar modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices).

O Azure Resource Manager analisa as dependências para garantir que os recursos sejam criados na ordem correta. Se um recurso depende de um valor de outro recurso (como uma máquina virtual que precisa de uma conta de armazenamento para discos), você pode definir uma dependência.

>[!Note]
>Para saber mais, confira [Definindo as dependências nos modelos do Gerenciador de Recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies).

Você também pode usar o modelo para atualizações de infraestrutura. Por exemplo, você pode adicionar um recurso à sua solução e adicionar regras de configuração para os recursos que já foram implantados. Se o modelo especificar a criação de um recurso, mas esse recurso já existir, o Azure Resource Manager executará uma atualização em vez de criar um novo ativo. O Azure Resource Manager atualiza o ativo existente para o mesmo estado de um novo.

O Gerenciador de Recursos fornece extensões para cenários quando você precisar de operações adicionais, como a instalação de um software específico que não está incluído na configuração.

## <a name="resource-tracking"></a>Acompanhamento de recursos

Conforme os usuários em sua organização adicionam recursos à assinatura, torna-se cada vez mais importante associar recursos ao departamento, cliente e ambiente apropriados. Você pode anexar metadados a recursos por meio de marcações. Você pode usar [marcações](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) para fornecer informações sobre o recurso ou o proprietário. Marcações permitem que você não somente agregue e agrupe recursos de diversas maneiras, mas também usar esses dados com o propósito de estorno.

Use as marcas quando você tiver um conjunto complexo de grupos de recursos e recursos e precisar visualizar os ativos da maneira que fizer mais sentido. Por exemplo, você pode marcar os recursos que servem para uma função semelhante em sua organização ou pertencem ao mesmo departamento.

Sem marcas, os usuários de sua organização podem criar vários recursos que podem ser difíceis de identificar e gerenciar mais tarde. Por exemplo, você pode querer excluir todos os recursos de um projeto. Se esses recursos não estão marcados para o projeto, você precisa encontrá-los manualmente. A marcação pode ser uma maneira importante de reduzir custos desnecessários em sua assinatura.

Recursos não precisam residir no mesmo grupo de recursos para compartilhar uma marca. Você pode criar sua própria taxonomia de marca para garantir que todos os usuários na sua organização usem marcas comuns em vez de aplicarem marcas ligeiramente diferentes inadvertidamente (como por exemplo, "Dept" em vez de "Departamento").

Políticas de recursos permitem que você crie regras padrão para sua organização. Você pode criar políticas que garantem que os recursos sejam marcados com os valores apropriados.

> [!Note]
> Para obter mais informações, consulte [Aplicar políticas de recursos para marcas](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy-tags).

Você também pode exibir os recursos marcados por meio do portal do Azure.

O [relatório de uso](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) para sua assinatura inclui nomes de marca e valores, o que o habilita a dividir os custos por marcas.

> [!Note]
> Para obter mais informações sobre marcas, consulte [Usando marcas para organizar os recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags).

As seguintes limitações se aplicam a marcas:

- Cada recurso ou grupo de recursos pode ter um máximo de 15 pares chave/valor de marca. Essa limitação se aplica somente a marcas aplicadas diretamente ao grupo de recursos ou recurso. Um grupo de recursos pode conter muitos recursos que possuem 15 pares de chave/valor de marca.

- O nome da marca é limitado a 512 caracteres.

- O valor da marca é limitado a 256 caracteres.

- Marcas aplicadas ao grupo de recursos não são herdadas pelos recursos desse grupo de recursos.

Se você tiver mais de 15 valores que você precisa associar a um recurso, use uma cadeia de caracteres JSON para o valor da marca. A cadeia de caracteres JSON pode conter vários valores que são aplicados a uma única chave de marca.

### <a name="tags-and-billing"></a>Marcas e cobrança

Marcas permitem que você agrupe os dados de cobrança. Por exemplo, se você estiver executando várias VMs para organizações diferentes, use as marcas para a utilização do grupo por centro de custo. Você também pode usar marcas para categorizar os custos pelo ambiente de tempo de execução, como por exemplo, o uso de cobrança para VMs em execução no ambiente de produção.

Você pode recuperar as informações sobre as marcações por meio das [APIs RateCard e Uso de Recursos do Azure](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) ou do arquivo de uso CSV (com valores separados por vírgula). Baixe o arquivo de uso no [portal de contas do Azure](https://account.windowsazure.com/) ou no [portal de EA](https://ea.azure.com/).

>[!Note]
> Para saber mais sobre o acesso programático às informações de cobrança, confira [Obter informações sobre o consumo de recursos do Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview). Para operações de API REST, confira [Referência da API REST de cobrança do Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Quando você baixa o CSV de uso para serviços que dão suporte a marcações com cobrança, as marcações aparecerão na coluna Marcações.

## <a name="critical-resource-controls"></a>Controle de recursos críticos

À medida que sua organização adiciona serviços principais à assinatura, torna-se cada vez mais importante garantir que esses serviços estejam disponíveis para evitar interrupção nos negócios. Os [Bloqueios de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) permitem restringir operações nos recursos de alto valor, onde sua modificação ou exclusão teria um impacto significativo nos aplicativos ou na infraestrutura da nuvem. Você pode aplicar bloqueios a uma assinatura, um grupo de recursos ou um recurso. Normalmente, você aplica bloqueios a recursos fundamentais, como redes virtuais, gateways e contas de armazenamento.

Atualmente, os Bloqueios de recursos aceitam dois valores: CanNotDelete e ReadOnly. CanNotDelete significa que os usuários (com os direitos apropriados) ainda podem ler ou modificar um recurso, mas não podem excluí-lo. ReadOnly significa que usuários autorizados não podem excluir nem modificar um recurso.

Bloqueios do Gerenciador de Recursos se aplicam apenas às operações que ocorrem no plano de gerenciamento, que consiste em operações enviadas para <https://management.azure.com>. Os bloqueios não restringem a maneira como os recursos executam suas próprias funções. Alterações de recursos são restritas, mas as operações de recursos não são restritas. Por exemplo, um bloqueio ReadOnly em um Banco de Dados SQL impede que você exclua ou modifique o banco de dados, mas ele não impede que você crie, atualize ou exclua dados no banco de dados.

A aplicação de **ReadOnly** pode gerar resultados inesperados, pois algumas operações que parecem operações de leitura exigem ações adicionais. Por exemplo, aplicar um bloqueio **ReadOnly** em uma conta de armazenamento impedirá que todos os usuários listem as chaves. A operação de lista de chaves é tratada por meio de uma solicitação POST, pois as chaves retornadas estão disponíveis para operações de gravação.

![Controle de Recursos Críticos](./media/governance-in-azure/security-governance-in-azure-fig5.png)

Em outro exemplo, a aplicação de um bloqueio ReadOnly em um recurso do Serviço de Aplicativo impedirá o Visual Studio Server Explorer de exibir os arquivos para o recurso, pois essa interação exige acesso de gravação.

Ao contrário do controle de acesso baseado em função, é possível usar bloqueios de gerenciamento para aplicar uma restrição a todos os usuários e a todas as funções. Para saber mais sobre como configurar permissões para usuários e funções, veja [Controle de Acesso Baseado em Função do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

Quando você aplica um bloqueio a um escopo pai, todos os recursos filho herdam o mesmo bloqueio. Até mesmo os recursos que você adiciona posteriormente herdam o bloqueio do pai. O bloqueio mais restritivo na herança terá precedência.

Para criar ou excluir os bloqueios de gerenciamento, você deve ter acesso às ações Microsoft.Authorization/ _ou Microsoft.Authorization/locks/_. Das funções internas, somente **Proprietário** e **Administrador do Acesso de Usuário** recebem essas ações.

## <a name="api-access-to-billing-information"></a>API de Acesso a Informações De Cobrança

Use APIs de cobrança do Azure para efetuar pull de dados de uso e de recurso em suas ferramentas de análise de dados preferidas. As APIs RateCard e de Uso de Recursos do Azure e podem ajudá-lo a prever e gerenciar seus custos com precisão. As APIs são implementadas como um Provedor de Recursos e como parte da família de APIs expostas pelo Azure Resource Manager.

### <a name="azure-resource-usage-api-preview"></a>API de uso de recursos do Azure (visualização)

Use as [API de Uso de Recursos](https://msdn.microsoft.com/library/azure/mt219003) do Azure para obter seus dados de consumo estimado do Azure. A API inclui:

- **Controle de Acesso baseado em Funções do Azure** – configure suas políticas de acesso no [Portal do Azure](https://portal.azure.com/) ou através dos [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar quais usuários ou aplicativos podem ter acesso aos dados de uso da assinatura. Os chamadores devem usar tokens padrão do Azure Active Directory para autenticação. Adicione o chamador à função Leitor de Fatura, Leitor, Proprietário ou Colaborador para obter acesso aos dados de uso para uma assinatura específica do Azure.

- **Agregações diárias ou por hora** - os chamadores podem especificar se eles desejam seus dados de uso do Azure em buckets por hora ou buckets diários. O padrão é diário.

- **Metadados da instância (inclui as marcas do recurso)** – obtenha detalhes no nível da instância, como o URI de recurso totalmente qualificado (/subscriptions/{subscription-id}/..), as informações do grupo de recursos e marcas do recurso. Esses metadados ajudam você a alocar de forma determinista e programática o uso por marcas, para casos de uso como cobrança cruzada.

- **Metadados de recurso** – detalhes de recurso como nome do medidor, categoria do medidor, subcategoria do medidor, unidade e região proporcionam ao chamador um melhor entendimento do que foi consumido. Também estamos trabalhando para alinhar a terminologia de metadados de recursos entre o portal do Azure, o CSV de uso do Azure, o CSV de cobrança EA e outras experiências públicas, para permitir que você correlacione dados entre as experiências.

- **Uso para todos os tipos de oferta** – os dados de uso estão disponíveis para todos os tipos de oferta, assim como pré-pago, MSDN, investimento e crédito monetário e EA.

**API RateCard de Recursos do Azure (visualização)**

Use a API RateCard de Recursos do Azure para obter a lista de recursos do Azure disponíveis, juntamente com as informações estimadas de preço para cada um deles. A API inclui:

- **Controle de Acesso baseado em Funções do Azure** – configure suas políticas de acesso no Portal do Azure ou através dos cmdlets do Azure PowerShell para especificar quais usuários ou aplicativos podem ter acesso aos dados do RateCard. Os chamadores devem usar tokens padrão do Azure Active Directory para autenticação. Adicione o chamador à função Leitor, Proprietário ou Colaborador para obter acesso aos dados de uso para uma assinatura particular do Azure.

- **Suporte para pré-pago, MSDN, ofertas de investimento e crédito monetário (EA não tem suporte)** – Esta API fornece informações de taxa no nível da oferta do Azure. O chamador dessa API deve passar as informações de oferta para obter taxas e detalhes do recurso. Já que as ofertas de EA têm taxas personalizadas por registro, não seremos capazes de fornecer as taxas de EA neste momento. Aqui estão alguns dos cenários possíveis com a combinação das APIs de Uso e RateCard:

- **Gasto do Azure durante o mês** – use a combinação das APIs RateCard e de uso para obter melhores informações sobre o seu gasto com nuvem durante o mês. Você pode analisar os buckets horários e diários de uso e as estimativas de custo.

- **Configurar alertas** – use as APIs RateCard e de uso para obter o consumo de nuvem e encargos estimados e configurar alertas com base em recursos ou em valores financeiros.

- **Previsão de fatura** – obtenha seu consumo estimado e gasto de nuvem e aplicar algoritmos de aprendizado de máquina para prever qual seria sua fatura no final do ciclo de cobrança.

- **Análise de custo de pré-consumo** – usar a API RateCard para prever quanto sua fatura seria para seu uso esperado ao mover suas cargas de trabalho para o Azure. Se você tem cargas de trabalho existentes em outras nuvens ou nuvens privadas, você também pode mapear seu uso com as taxas do Azure para obter uma melhor estimativa do seu gasto com o Azure. Essa estimativa fornece a você a capacidade de dinamizar a oferta e comparar entre os tipos diferentes de oferta além do pré-pago, tais como o compromisso monetário e o crédito monetário. A API também oferece a capacidade de ver as diferenças de custo por região e permite que você faça uma análise hipotética do custo para lhe ajudar a tomar decisões de implantação.

- **Análise E-Se** - Você pode determinar se é mais econômico executar suas cargas de trabalho em outra região ou em outra configuração do recurso do Azure. Os custos de recursos do Azure podem mudar com base na região do Azure que você está usando.

- Você também pode determinar se outro tipo de oferta do Azure oferece uma melhor taxa em um recurso do Azure.

## <a name="networking-controls"></a>Controles de rede

O acesso a recursos pode ser interno (dentro da rede da empresa) ou externo (por meio da Internet). É fácil para os usuários em sua organização colocar recursos inadvertidamente no ponto errado e potencialmente abri-los para o acesso mal-intencionado. Assim como acontece com dispositivos locais, as empresas devem adicionar os controles adequados para garantir que os usuários do Azure tomam as decisões certas.

![Controles de rede](./media/governance-in-azure/security-governance-in-azure-fig6.png)

Para governança da assinatura, identificamos recursos principais que fornecem controle básico de acesso. Os principais recursos consistem em:

### <a name="network-connectivity"></a>Conectividade de rede

[Redes virtuais](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) são objetos de contêiner para sub-redes. Embora não sejam estritamente necessárias, geralmente elas são usadas ao conectar aplicativos aos recursos corporativos internos. O serviço de Rede Virtual do Azure permite que você conecte com segurança os recursos do Azure usando redes virtuais (VNets).

Uma VNet é uma representação da sua própria rede na nuvem. Uma VNet é um isolamento lógico da nuvem do Azure dedicada à sua assinatura. Você também pode conectar VNets à sua rede local.

Os seguintes são os recursos para redes virtuais do Azure:

- **Isolamento**: as VNets são isoladas umas das outras. Crie VNets separadas para desenvolvimento, teste e produção que usem os mesmos blocos de endereços CIDR. Por outro lado, você pode criar várias VNets que usam blocos de endereço CIDR diferentes e conectam as redes. Você pode segmentar uma VNet em várias sub-redes. O Azure fornece resolução de nome interna para instâncias de função de VMs e Serviços de Nuvem conectadas a uma rede virtual. Opcionalmente, configure uma VNet para usar seus próprios servidores DNS, em vez de usar a resolução de nome interna do Azure.

- **Conectividade com a Internet**: Por padrão, todas as instâncias de função de Serviços de Nuvem e Máquinas Virtuais (VM) do Azure são conectadas a uma VNet que tem acesso à Internet. Você também pode habilitar o acesso de entrada a recursos específicos, conforme o necessário.

- **Conectividade de recursos do Azure**: Recursos do Azure, como Serviços de Nuvem e VMs, podem ser conectados à mesma VNet. Os recursos podem se conectar usando endereços IP privados, mesmo se estiverem em sub-redes diferentes. O Azure fornece roteamento padrão entre sub-redes, VNets e redes locais, portanto você não precisa configurar e gerenciar rotas.

- **Conectividade de VNet**: as VNets podem ser conectadas entre si, permitindo que os recursos conectados a qualquer VNet se comuniquem com recursos de outra VNet.

- **Conectividade local**: as VNets podem ser conectadas a redes locais por meio de conexões de rede privada entre a sua rede e o Azure, ou por uma conexão de VPN site a site pela Internet.

- **Filtragem de tráfego**: o tráfego de rede de instâncias de função de VMs e do Serviços de Nuvem pode ser filtrado na entrada e na saída pelo endereço IP e porta de origem, endereço IP e porta de destino e protocolo.

- **Roteamento**: opcionalmente, você pode substituir o roteamento padrão do Azure configurando suas próprias rotas ou usando rotas BGP por meio de um gateway de rede.

## <a name="network-access-controls"></a>Controles de acesso de rede

Os [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) são semelhantes a um firewall e fornecem regras de como um recurso pode "falar" pela rede. Eles fornecem controle granular sobre como/se uma sub-rede (ou máquina virtual) pode se conectar à Internet ou a outras sub-redes na mesma rede virtual.

Um NSG (grupo de segurança de rede) contém uma lista de regras de segurança que permitem ou negam o tráfego de rede para recursos conectados a VNets (redes virtuais) do Azure. Os NSGs podem ser associados a sub-redes, VMs individuais (clássicas) ou interfaces de rede individuais (NIC) anexadas a VMs (Resource Manager).

Quando um NSG está associado a uma sub-rede, as regras se aplicam a todos os recursos conectados à sub-rede. O tráfego pode ser restritao também associando um NSG a uma VM ou NIC.

## <a name="security-and-continuous-compliance-with-organizational-standards"></a>Segurança e conformidade contínua com os padrões da organização

Cada empresa tem diferentes necessidades e todo negócio obterá distintos benefícios das soluções de nuvem. Ainda assim, clientes de todos os tipos têm as mesmas preocupações básicas sobre como mover para a nuvem. Eles desejam manter o controle de seus dados e desejam que os dados sejam mantidos seguros e privados, tudo isso mantendo transparência e conformidade.

O que os clientes desejam de provedores de nuvem é:

- **Proteger nossos dados** enquanto confirmam que a nuvem pode proporcionar maior segurança de dados e controle administrativo, líderes de TI ainda se preocupam se migrar para a nuvem irá deixá-los mais vulneráveis a hackers que suas soluções internas atuais.

- **Manter nossos dados** serviços de Nuvem privados aumentam desafios de privacidade única para empresas. Como as empresas olham para a nuvem para economizar nos custos de infraestrutura e melhorar sua flexibilidade, elas também se preocupar sobre perder o controle de onde seus dados estão armazenados, quem os está acessando e como é usado.

- **Fornecer controle** mesmo que eles tirem proveito da nuvem para implantar soluções mais inovadoras, as empresas são muito preocupadas em perder o controle de seus dados. As recentes divulgações de agências governamentais acessando os dados dos clientes, tanto por meios legais e ilegais, fez alguns CIOs ficarem atentos sobre armazenarem seus dados na nuvem.

- **Promover a transparência** Enquanto o controle, privacidade e segurança são importantes para decisões, eles também querem a capacidade de verificar de forma independente como os seus dados estão armazenados, acessados e protegidos.

- **Manter a conformidade** como as empresas ampliam o uso de tecnologias de nuvem, a complexidade e o escopo dos padrões e normas continuam a evoluir. As empresas precisam saber que seus padrões de conformidade serão atendidos e essa conformidade evoluirá conforme os regulamentos se alterem ao longo do tempo.

## <a name="security-configuration-monitoring-and-alerting"></a>Configuração de segurança, monitoramento e alertas

Os assinantes do Azure podem gerenciar ambientes de nuvem de vários dispositivos, incluindo estações de trabalho, computadores de desenvolvedores e até mesmo dispositivos de usuário final com privilégios que têm permissões de tarefas específicas. Em alguns casos, as funções administrativas são executadas por meio de consoles baseado na Web, como o Portal do Azure. Em outros casos, pode haver conexões diretas ao Azure de sistemas locais por meio de VPNs (Redes Virtuais Privadas), Serviços de Terminal, protocolos de aplicativos de cliente ou (programaticamente) a SMAPI (API de Gerenciamento de Serviços do Azure). Além disso, os pontos de extremidade do cliente podem ser unidos ao domínio ou isolados e não gerenciados, como tablets ou smartphones.

Embora vários recursos de gerenciamento e acesso forneçam um conjunto avançado de opções, essa variação pode adicionar um risco significativo para uma implantação de nuvem. Pode ser difícil gerenciar, controlar e auditar as ações administrativas. Essa variação também pode introduzir ameaças de segurança por meio do acesso não regulado a pontos de extremidade do cliente que são usados para gerenciar serviços de nuvem. O uso de estações de trabalho pessoais ou gerais para desenvolvimento e gerenciamento de infraestrutura abre vetores de ameaça imprevisíveis, como navegação na Web (por exemplo, ataques do tipo "watering hole") ou email (por exemplo, engenharia social e phishing).

O monitoramento, o registro em log e a auditoria fornecem uma base para acompanhar e entender atividades administrativas, mas nem sempre é viável auditar completamente todas as ações, devido à quantidade de dados gerados. No entanto, a auditoria da eficácia das políticas de gerenciamento é uma prática recomendada.

Governança de segurança do Azure para AD DS GPOs para controlar todas as interfaces do Windows dos administradores, tal como o compartilhamento de arquivos. Inclua as estações de trabalho de gerenciamento em processos de auditoria, monitoramento e registro em log. Acompanhe todo o acesso e uso de administradores e desenvolvedores.

### <a name="azure-security-center"></a>Central de Segurança do Azure

A [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) fornece uma visão central do status de segurança dos recursos nas assinaturas, além de recomendações que ajudam a evitar recursos comprometidos. Ela pode permitir políticas mais granulares (por exemplo, aplicar políticas a grupos de recursos específicos que permitem à empresa adaptar sua postura ao risco que está enfrentando).

![Central de Segurança do Azure](./media/governance-in-azure/security-governance-in-azure-fig7.png)

O Centro de Segurança permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança. Depois de habilitar as [políticas de segurança](https://docs.microsoft.com/azure/security-center/security-center-policies) para os recursos de uma assinatura, a Central de Segurança analisa a segurança de seus recursos para identificar as possíveis vulnerabilidades. Informações sobre a configuração de rede estão disponíveis imediatamente.

Central de Segurança do Azure representa uma combinação de análise de melhor prática e gerenciamento de política de segurança para todos os recursos dentro de uma assinatura do Azure. Esta ferramenta poderosa e fácil de usar permite equipes de segurança e funcionários de risco prevenirem, detectarem e responderem a ameaças de segurança, pois ela automaticamente analisa e colecta dados de segurança dos seus recursos do Azure, da rede, e soluções de parceiro como programas e firewalls anti-malware.

Além disso, a Central de Segurança do Azure usa análises avançadas, incluindo aprendizagem de máquina e análise de comportamento enquanto aproveita a inteligência de ameaça global dos produtos e serviços Microsoft, o Microsoft Digital Crimes Unit (DCU), o Microsoft Security Response Center (MSRC), e feeds externos. [Governança de segurança](https://www.credera.com/blog/credera-site/azure-governance-part-4-other-tools-in-the-toolbox/) pode ser usada amplamente no nível de assinatura ou restringida a requisitos granulares e específicos aplicados a recursos individuais por meio de definição de política.

Finalmente, Central de segurança do Azure analisa a integridade dos recursos de segurança com base nessas políticas e a utiliza para fornecer criteriosos painéis e alertas de eventos, como detecção de malware ou conexão de IP mal-intencionado tentativas.

>[!Note]
> Para obter mais informações sobre como aplicar recomendações, leia [Implementar as recomendações de segurança na Central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

A Central de Segurança coleta dados das máquinas virtuais para avaliar seu estado de segurança, fornecer recomendações de segurança e alertar sobre ameaças. Quando você acessa pela primeira vez a Central de Segurança, a coleta de dados é habilitada em todas as máquinas virtuais em sua assinatura. A coleta de dados é recomendada, mas você pode recusá-la [desabilitando a coleta de dados](https://docs.microsoft.com/azure/security-center/security-center-faq) na política da Central de Segurança.

Por fim, a Central de Segurança do Azure é uma plataforma aberta que permite aos parceiros da Microsoft e fornecedores de software independentes criar software que se conecte à Central de Segurança do Azure para aprimorar seus recursos.

A Central de Segurança do Azure monitora os seguintes recursos do Azure:

- Máquinas virtuais (VMs) (incluindo os Serviços de Nuvem)

- Redes Virtuais do Azure

- Serviço do SQL Azure

- Soluções de parceiros integradas com sua assinatura do Azure, como um firewall de aplicativo Web em VMs e no [Ambiente do Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/app-service-app-service-environments-readme).

### <a name="operations-management-suite"></a>Operations Management Suite

O desenvolvimento de software OMS e o serviço de segurança da informação da equipe e o [programa de governança](https://github.com/Microsoft/azure-docs/blob/master/articles/log-analytics/log-analytics-security.md) dão suporte aos requisitos do negócio e aderem a leis e regulamentos como descrito em [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) e [Microsoft Trust Center Compliance](https://www.microsoft.com/TrustCenter/Compliance/default.aspx). Como o OMS estabelece os requisitos de segurança, identifica os controles de segurança, gerencia e monitora os riscos também são descritos lá. Anualmente, revisamos as políticas, padrões, procedimentos e diretrizes.

Cada membro de equipe de desenvolvimento do OMS recebe treinamento formal sobre a segurança de aplicativo. Internamente, usamos um sistema de controle de versão para o desenvolvimento de software. Cada projeto de software é protegido pelo sistema de controle de versão.

A Microsoft tem uma equipe de segurança e conformidade que monitora e analisa todos os serviços na Microsoft. Os responsáveis pela segurança de informações compõem a equipe e eles não são associados aos departamentos de engenharia que desenvolvem o OMS. Os responsáveis pela segurança têm sua própria cadeia de gerenciamento e realizaram avaliações independentes dos produtos e serviços para garantir a segurança e a conformidade.

O OMS (Operations Management Suite) é um conjunto de serviços de gerenciamento que foram criados na nuvem desde o início. Em vez de implantar e gerenciar recursos locais, os componentes do OMS estão totalmente hospedados no Azure. A configuração é mínima e você pode ter tudo funcionando literalmente em questão de minutos.

![Pacote do Operations Manager](./media/governance-in-azure/security-governance-in-azure-fig8.png)

Só porque os serviços do OMS são executados na nuvem não significa que eles não possam gerenciar efetivamente seu ambiente local.

Coloque um agente em qualquer computador Windows ou Linux no seu data center e ele enviará dados para o Log Analytics, onde poderão ser analisados junto com todos os outros dados coletados de serviços na nuvem ou locais. Use o Backup do Azure e o Azure Site Recovery para aproveitar a nuvem como backup e alta disponibilidade de recursos locais.

Os runbooks na nuvem normalmente não podem acessar os recursos locais, mas você pode instalar um agente em um ou mais computadores para hospedar runbooks em seu data center. Quando você inicia um runbook, basta simplesmente especificar se deseja que ele seja executado na nuvem ou em um local de trabalho.

A principal funcionalidade do OMS é oferecida por um conjunto de serviços que são executados no Azure. Cada serviço fornece uma função de gerenciamento específico, e você pode combinar serviços para obter cenários de gerenciamento diferentes.

![Pacote do Operations Manager](./media/governance-in-azure/security-governance-in-azure-fig9.JPG)

Gerenciador de operação do Azure estende suas funcionalidades fornecendo soluções de gerenciamento. [Soluções de gerenciamento](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions) são conjuntos de lógica pré-empacotados que implementam um cenário de gerenciamento específico utilizando um ou mais serviços do OMS.

![Gerenciamento de operação do Azure](./media/governance-in-azure/security-governance-in-azure-fig10.png)

Existem soluções diferentes disponibilizadas pela Microsoft e por parceiros que você pode adicionar facilmente à assinatura do Azure para aumentar o valor de seu investimento no OMS.

Como parceiro, você pode criar suas próprias soluções para dar suporte a seus aplicativos e serviços e oferecê-las aos usuários por meio do Azure Marketplace ou como Modelos de Início Rápido.

## <a name="performance-alerting-and-monitoring"></a>Desempenho do monitoramento e alertas

### <a name="alerting"></a>Alertas

Os alertas são um método para monitorar as métricas do recurso do Azure, eventos ou logs, e ser notificado quando uma condição especificada é atendida.

**Alertas nos diferentes serviços do Azure**

Os alertas estão disponíveis em diferentes serviços, incluindo:

- Application Insights: Permite os alertas de teste da Web e métrica.

>[!Note]
> Consulte [Definir Alertas no Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) e [Monitorar a disponibilidade e a capacidade de resposta de qualquer site](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability).

- Log Analytics (Conjunto de Gerenciamento de Operação): Habilita o roteamento de Atividade e Logs de Diagnóstico para o Log Analytics. O Operations Management Suite permite métricas, logs e outros tipos de alerta.

>[!Note]
> Para obter mais informações, consulte Alertas no [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts).

- Azure Monitor: Permite alertas com base nos valores das métricas e nos eventos do log de atividades. Você pode usar a [API REST do Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx) para gerenciar alertas.

>[!Note]
> Para obter mais informações, consulte [Using the Azure portal, PowerShell, or the command-line interface to create alerts](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal).

### <a name="monitoring"></a>Monitoramento

Problemas de desempenho em seu aplicativo de nuvem podem afetar seus negócios. Com diversos componentes interconectados e frequentes lançamentos, degradações podem ocorrer a qualquer momento. E se você estiver desenvolvendo um aplicativo, seus usuários normalmente descobrirão problemas que você não encontrou durante os testes. Você precisa tomar conhecimento dessas questões imediatamente e precisa ter ferramentas para diagnosticar e corrigir problemas. O Microsoft Azure tem uma variedade de ferramentas para identificar esses problemas.

**Como posso monitorar meus aplicativos de nuvem do Azure?**

Há uma variedade de ferramentas para monitorar serviços e aplicativos do Azure. Alguns de seus recursos se sobrepõem. Isso se deve em parte a razões históricas e, em parte, à fusão entre o desenvolvimento e a operação de um aplicativo.

Estas são as ferramentas de entidade de serviço:

- O **Azure Monitor** é uma ferramenta básica para monitorar serviços em execução no Azure. Ele fornece dados de nível da infraestrutura sobre a taxa de transferência de um serviço e o ambiente em redor. Se você estiver gerenciando todos os seus aplicativos no Azure ou decidindo se deseja expandir ou reduzir recursos, o Azure Monitor oferece o que você usa para iniciar.

- O **Application Insights** pode ser usado para desenvolvimento e como uma solução de monitoramento de produção. Ele funciona instalando um pacote em seu aplicativo e, assim, fornecendo uma exibição mais interna do que está acontecendo. Seus dados incluem tempos de resposta de dependências, rastreamentos de exceção, instantâneos de depuração, perfis de execução. Ele fornece poderosas ferramentas inteligentes para analisar toda essa telemetria, tanto para ajudá-lo a depurar um aplicativo quanto para ajudá-lo a entender o que os usuários estão fazendo com ele. Você pode discernir se um pico no tempo de resposta se deve a algo em um aplicativo ou a algum problema de obtenção de recursos externo. Se você usar o Visual Studio e o aplicativo estiver apresentando falhas, você poderá ser levado diretamente para as linhas do código com o problema para que possa corrigi-lo.

- O **Log Analytics** é voltado para pessoas que precisam ajustar o desempenho e planejar a manutenção de aplicativos em execução na produção. Ele se baseia no Azure. Ele coleta e agrega dados de várias fontes, embora haja um atraso de 10 a 15 minutos. Ele fornece uma solução holística de gerenciamento de TI para a infraestrutura baseada em nuvem de terceiros (como o Amazon Web Services), do Azure e local. Ele fornece ferramentas mais sofisticadas para analisar dados de mais origens, permite consultas complexas em todos os logs e pode alertar proativamente sobre condições especificadas. Você pode até mesmo coletar dados personalizados em seu repositório central para que possa consultar e visualizá-los.

- O **SCOM (System Center Operations Manager)** é voltado ao gerenciamento e monitoramento de grandes instalações de nuvem. Você pode já estar familiarizado como ele como uma ferramenta de gerenciamento para nuvens baseadas em Hyper-V e Windows Server local, mas ele também pode se integrar e gerenciar aplicativos do Azure. Entre outras coisas, ele pode instalar o Application Insights em aplicativos ativos existentes. Se um aplicativo falhar, ele lhe informa em segundos.


## <a name="next-steps"></a>Próximas etapas

- [Melhores práticas para criação de modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices).

- [Exemplos de implementação da governança de assinatura do Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-subscription-examples).

- [Governo do Microsoft Azure](https://docs.microsoft.com/azure/azure-government/).
