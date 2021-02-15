---
title: Visão geral da segurança
titleSuffix: Azure Cognitive Search
description: Saiba mais sobre os recursos de segurança do Azure Pesquisa Cognitiva para proteger pontos de extremidade, conteúdo e operações.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/04/2021
ms.custom: references_regions
ms.openlocfilehash: 46f2035e5f8409cd38faeb9c327b88b06fc7d7a0
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100097629"
---
# <a name="security-overview-for-azure-cognitive-search"></a>Visão geral de segurança para o Azure Pesquisa Cognitiva

Este artigo descreve os recursos de segurança do Azure Pesquisa Cognitiva que protegem o conteúdo e as operações.

Para solicitações de entrada feitas a um serviço de pesquisa, há uma progressão de medidas de segurança protegendo o ponto de extremidade do serviço de pesquisa: de chaves de API na solicitação, para regras de entrada no firewall, para pontos de extremidade privados que protegem totalmente seu serviço da Internet pública.

Para solicitações de saída feitas a outros serviços, a solicitação predominante é feita por indexadores que lêem conteúdo de fontes externas. Você pode fornecer credenciais na cadeia de conexão. Ou você pode configurar uma identidade gerenciada para tornar a pesquisa um serviço confiável ao acessar dados do armazenamento do Azure, SQL do Azure, Cosmos DB ou outras fontes de dados do Azure. Uma identidade gerenciada é um substituto de credenciais ou chaves de acesso na conexão. Para obter mais informações sobre esse recurso, consulte [conectar-se a uma fonte de dados usando uma identidade gerenciada](search-howto-managed-identities-data-sources.md).

As operações de gravação para serviços externos são poucas: um serviço de pesquisa grava em arquivos de log e grava no armazenamento do Azure ao criar armazenamentos de conhecimento, persistir aprimoramentos em cache e persistir sessões de depuração. Outras chamadas de serviço a serviço, como serviços cognitivas, são feitas na rede interna.

Assista a este vídeo rápido do ritmo para obter uma visão geral da arquitetura de segurança e de cada categoria de recurso.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

## <a name="network-security"></a>Segurança de rede

<a name="service-access-and-authentication"></a>

Os recursos de segurança de entrada protegem o ponto de extremidade do serviço de pesquisa por meio de níveis crescentes de segurança e complexidade. Primeiro, todas as solicitações exigem uma chave de API para acesso autenticado. Em segundo lugar, opcionalmente, você pode definir regras de firewall que limitam o acesso a endereços IP específicos. Para proteção avançada, uma terceira opção é habilitar o link privado do Azure para proteger seu ponto de extremidade de serviço de todo o tráfego da Internet.

### <a name="public-access-using-api-keys"></a>Acesso público usando chaves de API

Por padrão, um serviço de pesquisa é acessado por meio da nuvem pública, usando a autenticação baseada em chave para acesso de administrador ou de consulta ao ponto de extremidade do serviço de pesquisa. O envio de uma chave válida é considerado uma prova de que a solicitação se origina de uma entidade confiável. A autenticação baseada em chave é abordada na próxima seção.

### <a name="configure-ip-firewalls"></a>Configurar firewalls de IP

Para controlar ainda mais o acesso ao serviço de pesquisa, você pode criar regras de firewall de entrada que permitem o acesso a um endereço IP específico ou a um intervalo de endereços IP. Todas as conexões de cliente devem ser feitas por meio de um endereço IP permitido ou a conexão é negada.

:::image type="content" source="media/search-security-overview/inbound-firewall-ip-restrictions.png" alt-text="diagrama de arquitetura de exemplo para acesso restrito por IP":::

Você pode usar o portal para [Configurar o acesso de entrada](service-configure-firewall.md).

Como alternativa, você pode usar as APIs REST de gerenciamento. A partir da versão de API 2020-03-13, com o parâmetro [IpRule](/rest/api/searchmanagement/services/createorupdate#iprule) , você pode restringir o acesso ao seu serviço identificando endereços IP, individualmente ou em um intervalo, que você deseja conceder acesso ao serviço de pesquisa.

### <a name="network-isolation-through-a-private-endpoint-no-internet-traffic"></a>Isolamento de rede por meio de um ponto de extremidade privado (sem tráfego de Internet)

Você pode estabelecer um [ponto de extremidade privado](../private-link/private-endpoint-overview.md) para o Azure pesquisa cognitiva permite que um cliente em uma [rede virtual](../virtual-network/virtual-networks-overview.md) acesse dados com segurança em um índice de pesquisa por um [link privado](../private-link/private-link-overview.md).

O ponto de extremidade privado usa um endereço IP do espaço de endereço de rede virtual para conexões com o serviço de pesquisa. O tráfego de rede entre o cliente e o serviço de pesquisa atravessa a rede virtual e um link privado na rede de backbone da Microsoft, eliminando a exposição da Internet pública. Uma VNET permite a comunicação segura entre os recursos, com sua rede local, bem como com a Internet.

:::image type="content" source="media/search-security-overview/inbound-private-link-azure-cog-search.png" alt-text="diagrama de arquitetura de exemplo para acesso de ponto de extremidade privado":::

Embora essa solução seja a mais segura, usar serviços adicionais é um custo adicional, portanto, certifique-se de ter uma compreensão clara dos benefícios antes de mergulhar no. ou mais informações sobre os custos, consulte a [página de preços](https://azure.microsoft.com/pricing/details/private-link/). Para obter mais informações sobre como esses componentes funcionam em conjunto, Assista ao vídeo na parte superior deste artigo. A cobertura da opção de ponto de extremidade particular começa em 5:48 no vídeo. Para obter instruções sobre como configurar o ponto de extremidade, consulte [criar um ponto de extremidade privado para o Azure pesquisa cognitiva](service-create-private-endpoint.md).

## <a name="authentication"></a>Autenticação

Para solicitações de entrada para o serviço de pesquisa, a autenticação é por meio de uma [chave de API obrigatória](search-security-api-keys.md) (uma cadeia composta de números e letras gerados aleatoriamente) que prova que a solicitação é de uma fonte confiável. No momento, o Pesquisa Cognitiva não oferece suporte à autenticação Azure Active Directory para solicitações de entrada.

As solicitações de saída feitas por um indexador estão sujeitas à autenticação pelo serviço externo. O subserviço do indexador no Pesquisa Cognitiva pode se tornar um serviço confiável no Azure, conectando-se a outros serviços usando uma identidade gerenciada. Para obter mais informações, consulte [Configurar uma conexão de indexador com uma fonte de dados usando uma identidade gerenciada](search-howto-managed-identities-data-sources.md).

## <a name="authorization"></a>Autorização

O Pesquisa Cognitiva fornece modelos de autorização diferentes para gerenciamento de conteúdo e gerenciamento de serviços. 

### <a name="authorization-for-content-management"></a>Autorização para gerenciamento de conteúdo

A autorização de conteúdo e as operações relacionadas ao conteúdo são o acesso de gravação, como conferido por meio da [chave de API](search-security-api-keys.md) fornecida na solicitação. A chave de API é um mecanismo de autenticação, mas também autoriza o acesso dependendo do tipo de chave de API.

+ Chave de administração (permite acesso de leitura/gravação para operações de criação-leitura-atualização-exclusão no serviço de pesquisa), criada quando o serviço é provisionado

+ Chave de consulta (permite o acesso somente leitura à coleção de documentos de um índice), criada conforme necessário e projetada para aplicativos cliente que emitem consultas

No código do aplicativo, você especifica o ponto de extremidade e uma chave de API para permitir o acesso ao conteúdo e às opções. Um ponto de extremidade pode ser o próprio serviço, a coleção de índices, um índice específico, uma coleção de documentos ou um documento específico. Quando encadeadas, o ponto de extremidade, a operação (por exemplo, uma solicitação de criação ou atualização) e o nível de permissão (direitos totais ou somente leitura com base na chave) constituem a fórmula de segurança que protege o conteúdo e as operações.

### <a name="controlling-access-to-indexes"></a>Controlando o acesso a índices

No Azure Pesquisa Cognitiva, um índice individual não é um objeto protegível. Em vez disso, o acesso a um índice é determinado na camada de serviço (acesso de leitura ou gravação com base na chave de API que você fornece), juntamente com o contexto de uma operação.

Para acesso somente leitura, você pode estruturar solicitações de consulta para se conectar usando uma [chave de consulta](search-security-rbac.md)e incluir o índice específico usado pelo seu aplicativo. Em uma solicitação de consulta, não há o conceito de adicionar índices ou acessar vários índices simultaneamente. Portanto, todas as solicitações têm um único índice de destino por definição. Como tal, a construção da solicitação de consulta em si (uma chave mais um único índice de destino) define o limite de segurança.

O acesso de administrador e de desenvolvedor aos índices não é diferenciado: ambos precisam de acesso de gravação para criar, excluir e atualizar os objetos gerenciados pelo serviço. Qualquer pessoa com uma [chave de administração](search-security-rbac.md) para seu serviço pode ler, modificar ou excluir qualquer índice no mesmo serviço. Para proteção contra exclusão de índices acidental ou mal-intencionada, o controle do código-fonte interno para ativos de código é a solução para reverter uma modificação ou exclusão de índice indesejada. O Azure Pesquisa Cognitiva tem um failover no cluster para garantir a disponibilidade, mas não armazena ou executa o código proprietário usado para criar ou carregar índices.

Para soluções de multilocação que exigem limites de segurança no nível do índice, essas soluções normalmente incluem uma camada intermediária que os clientes usam para lidar com isolamento de índice. Para obter mais informações sobre o caso de uso multilocatário, consulte [padrões de design para aplicativos SaaS multilocatários e pesquisa cognitiva do Azure](search-modeling-multitenant-saas-applications.md).

### <a name="controlling-access-to-documents"></a>Controlando o acesso a documentos

Se você precisar de controle granular por usuário sobre os resultados da pesquisa, poderá criar filtros de segurança em suas consultas, retornando documentos associados a uma determinada identidade de segurança. 

Conceitualmente equivalente a "segurança em nível de linha", a autorização para o conteúdo dentro do índice não tem suporte nativo usando funções predefinidas ou atribuições de função que são mapeadas para entidades em Azure Active Directory. Quaisquer permissões de usuário em dados em sistemas externos, como Cosmos DB, não são transferidas com esses dados como sendo indexadas por Pesquisa Cognitiva.

Soluções alternativas para soluções que exigem "segurança em nível de linha" incluem a criação de um campo na fonte de dados que representa uma identidade de usuário ou grupo de segurança e, em seguida, o uso de filtros no Pesquisa Cognitiva para aparar seletivamente os resultados da pesquisa de documentos e conteúdo com base em identidades. A tabela a seguir descreve duas abordagens para cortar resultados da pesquisa com conteúdo não autorizado.

| Abordagem | Descrição |
|----------|-------------|
|[Filtragem de segurança com base nos filtros de identidade](search-security-trimming-for-azure-search.md)  | Documenta o fluxo de trabalho básico para implementar o controle de acesso de identidade do usuário. Ele aborda a adição de identificadores de segurança a um índice e explica a filtragem em relação a esse campo para cortar resultados de conteúdo proibido. |
|[Filtragem de segurança com base em Identidades do Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | Este artigo se expande no artigo anterior, fornecendo etapas para recuperar identidades do Azure Active Directory (AD do Azure), um dos [serviços gratuitos](https://azure.microsoft.com/free/) na plataforma de nuvem do Azure. |

### <a name="authorization-for-service-management"></a>Autorização para gerenciamento de serviços

As operações de gerenciamento de serviço são autorizadas por meio do [controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/overview.md). O RBAC do Azure é um sistema de autorização criado em [Azure Resource Manager](../azure-resource-manager/management/overview.md) para provisionamento de recursos do Azure. 

No Azure Pesquisa Cognitiva, o Resource Manager é usado para criar ou excluir o serviço, gerenciar chaves de API e dimensionar o serviço. Dessa forma, as atribuições de função do Azure determinarão quem pode executar essas tarefas, independentemente de estarem usando o [portal](search-manage.md), o [PowerShell](search-manage-powershell.md)ou as [APIs REST de gerenciamento](/rest/api/searchmanagement/search-howto-management-rest-api).

[Três funções básicas](search-security-rbac.md#management-tasks-by-role) são definidas para a administração do serviço de pesquisa. As atribuições de função podem ser feitas usando qualquer metodologia com suporte (portal, PowerShell e assim por diante) e são respeitadas em todo o serviço. As funções de proprietário e colaborador podem executar uma variedade de funções de administração. Você pode atribuir a função leitor a usuários que só exibem informações essenciais.

> [!Note]
> Usando mecanismos do Azure, você pode bloquear uma assinatura ou um recurso para impedir a exclusão acidental ou não autorizada de seu serviço de pesquisa por usuários com direitos de administrador. Para obter mais informações, consulte [Bloquear recursos para evitar a exclusão inesperada](../azure-resource-manager/management/lock-resources.md).

<a name="encryption"></a>

## <a name="data-protection"></a>Proteção de dados

Na camada de armazenamento, a criptografia de dados é interna para todo o conteúdo gerenciado pelo serviço salvo em disco, incluindo índices, mapas de sinônimos e as definições de indexadores, fontes de dados e habilidades. Opcionalmente, você pode adicionar chaves gerenciadas pelo cliente (CMK) para criptografia complementar de conteúdo indexado. Para serviços criados após 1 2020 de agosto, a criptografia CMK se estende aos dados em discos temporários, para "criptografia dupla" completa de conteúdo indexado.

### <a name="data-in-transit"></a>Dados em trânsito

No Azure Pesquisa Cognitiva, a criptografia começa com conexões e transmissões e se estende ao conteúdo armazenado em disco. Para serviços de pesquisa na Internet pública, o Azure Pesquisa Cognitiva escuta na porta HTTPS 443. Todas as conexões de cliente para serviço usam a criptografia TLS 1,2. Não há suporte para versões anteriores (1,0 ou 1,1).

### <a name="encrypted-data-at-rest"></a>Dados criptografados em repouso

Para dados manipulados internamente pelo serviço de pesquisa, a tabela a seguir descreve os [modelos de criptografia de dados](../security/fundamentals/encryption-models.md). Alguns recursos, como armazenamento de conhecimento, enriquecimento incremental e indexação baseada em indexador, leitura ou gravação em estruturas de dados em outros serviços do Azure. Esses serviços têm seus próprios níveis de suporte de criptografia separados do Azure Pesquisa Cognitiva.

| Modelar | Novas&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Requirement&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Restrições | Aplica-se a |
|------------------|-------|-------------|--------------|------------|
| criptografia do lado do servidor | Chaves gerenciadas pela Microsoft | Nenhum (interno) | Nenhum, disponível em todas as camadas, em todas as regiões, para o conteúdo criado após janeiro de 24 2018. | Conteúdo (índices e mapas de sinônimos) e definições (indexadores, fontes de dados, habilidades) |
| criptografia do lado do servidor | chaves gerenciadas pelo cliente | Cofre de Chave do Azure | Disponível em camadas faturáveis, em todas as regiões, para conteúdo criado depois de janeiro de 2019. | Conteúdo (mapas e índices de sinônimos) em discos de dados |
| criptografia dupla do lado do servidor | chaves gerenciadas pelo cliente | Cofre de Chave do Azure | Disponível em camadas faturáveis, em regiões selecionadas, em serviços de pesquisa depois de agosto de 1 2020. | Conteúdo (mapas e índices de sinônimos) em discos de dados e discos temporários |

### <a name="service-managed-keys"></a>Chaves gerenciadas pelo serviço

A criptografia gerenciada por serviço é uma operação interna da Microsoft, com base no [Azure criptografia do serviço de armazenamento](../storage/common/storage-service-encryption.md), usando a [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits. Ele ocorre automaticamente em toda a indexação, incluindo em atualizações incrementais para índices que não são totalmente criptografados (criados antes de janeiro de 2018).

### <a name="customer-managed-keys-cmk"></a>Chaves gerenciadas pelo cliente (CMK)

Chaves gerenciadas pelo cliente exigem um serviço Faturável adicional, Azure Key Vault, que pode estar em uma região diferente, mas na mesma assinatura, como o Azure Pesquisa Cognitiva. Habilitar a criptografia CMK aumentará o tamanho do índice e reduzirá o desempenho da consulta. Com base nas observações até a data, você pode esperar um aumento de 30%-60% nos tempos de consulta, embora o desempenho real varie dependendo da definição de índice e dos tipos de consultas. Devido a esse impacto no desempenho, recomendamos que você habilite apenas esse recurso em índices que realmente o exigem. Para obter mais informações, consulte [Configurar chaves de criptografia gerenciadas pelo cliente no Azure pesquisa cognitiva](search-security-manage-encryption-keys.md).

<a name="double-encryption"></a>

### <a name="double-encryption"></a>Criptografia dupla

No Azure Pesquisa Cognitiva, a criptografia dupla é uma extensão de CMK. Ele é entendido como criptografia de duas dobras (uma vez por CMK e novamente pelas chaves gerenciadas pelo serviço) e abrangente no escopo, abrangendo o armazenamento de longo prazo que é gravado em um disco de dados e armazenamento de curto prazo gravado em discos temporários. A diferença entre CMK antes de agosto de 1 2020 e posteriores e o que torna o CMK um recurso de criptografia dupla no Azure Pesquisa Cognitiva é a criptografia adicional de dados em repouso em discos temporários.

A criptografia dupla está disponível atualmente em novos serviços que são criados nessas regiões após 1º de agosto:

+ Oeste dos EUA 2
+ Leste dos EUA
+ Centro-Sul dos Estados Unidos
+ Gov. dos EUA – Virgínia
+ Governo dos EUA do Arizona

## <a name="security-management"></a>Gerenciamento de segurança

### <a name="api-keys"></a>Chaves de API

A dependência da autenticação baseada em chave de API significa que você deve ter um plano para regenerar a chave de administração em intervalos regulares, por práticas recomendadas de segurança do Azure. Há um máximo de duas chaves de administração por serviço de pesquisa. Para obter mais informações sobre como proteger e gerenciar chaves de API, consulte [criar e gerenciar chaves de API](search-security-api-keys.md).

#### <a name="activity-and-diagnostic-logs"></a>Logs de atividade e diagnóstico

Pesquisa Cognitiva não registra as identidades de usuário para que você não possa fazer referência a logs para obter informações sobre um usuário específico. No entanto, o serviço faz log de operações de criação-leitura-atualização-exclusão, que você poderá correlacionar com outros logs para entender a agência de ações específicas.

Usando alertas e a infraestrutura de log no Azure, você pode escolher os picos de volume de consulta ou outras ações que se desviam das cargas de trabalho esperadas. Para obter mais informações sobre como configurar logs, consulte [coletar e analisar dados de log](search-monitor-logs.md) e [monitorar solicitações de consulta](search-monitor-queries.md).

### <a name="certifications-and-compliance"></a>Certificações e conformidade

O Azure Pesquisa Cognitiva participa de auditorias regulares e foi certificado em relação a vários padrões globais, regionais e específicos do setor para a nuvem pública e o Azure governamental. Para obter a lista completa, baixe o [White Paper de **ofertas de conformidade Microsoft Azure**](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) na página de relatórios oficiais de auditoria.

Para fins de conformidade, você pode usar [Azure Policy](../governance/policy/overview.md) para implementar as práticas recomendadas de alta segurança do [benchmark de segurança do Azure](../security/benchmarks/introduction.md). O benchmark de segurança do Azure é uma coleção de recomendações de segurança, codificados em controles de segurança que são mapeados para as principais ações que você deve executar para reduzir as ameaças a serviços e dados. Atualmente, há 11 controles de segurança, incluindo [segurança de rede](../security/benchmarks/security-control-network-security.md), [registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md)e [proteção de dados](../security/benchmarks/security-control-data-protection.md) para citar alguns.

Azure Policy é um recurso interno do Azure que ajuda a gerenciar a conformidade de vários padrões, incluindo os do benchmark de segurança do Azure. Para benchmarks conhecidos, o Azure Policy fornece definições internas que fornecem critérios, bem como uma resposta acionável que resolve a não conformidade.

Para o Azure Pesquisa Cognitiva, há atualmente uma definição interna. É para o log de diagnóstico. Com esse interno, você pode atribuir uma política que identifica qualquer serviço de pesquisa que não tem o log de diagnóstico e, em seguida, ativa-o. Para obter mais informações, consulte [Azure Policy controles de conformidade regulatória para o pesquisa cognitiva do Azure](security-controls-policy.md).

## <a name="see-also"></a>Consulte também

+ [Conceitos básicos de segurança do Azure](../security/fundamentals/index.yml)
+ [Segurança do Azure](https://azure.microsoft.com/overview/security)
+ [Central de Segurança do Azure](../security-center/index.yml)