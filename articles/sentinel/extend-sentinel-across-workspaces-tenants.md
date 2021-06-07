---
title: Estenda o Azure Sentinel entre espaços de trabalho e locatários | Microsoft Docs
description: Como usar o Azure Sentinel para consultar e analisar dados entre espaços de trabalho e locatários.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2020
ms.author: yelevin
ms.openlocfilehash: 49b267d36fb6c365cf2125912c0d27fe7d669474
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100585285"
---
# <a name="extend-azure-sentinel-across-workspaces-and-tenants"></a>Estender o Azure Sentinel entre workspaces e locatários

## <a name="the-need-to-use-multiple-azure-sentinel-workspaces"></a>A necessidade de usar vários espaços de trabalho do Azure Sentinel

O Azure Sentinel é criado sobre um espaço de trabalho Log Analytics. Você observará que a primeira etapa na integração do Azure Sentinel é selecionar o Log Analytics espaço de trabalho que você deseja usar para essa finalidade.

Você pode obter o benefício completo da experiência do Azure Sentinel ao usar um único workspace. Mesmo assim, há algumas circunstâncias que podem exigir que você tenha vários espaços de trabalho. A tabela a seguir lista algumas dessas situações e, quando possível, sugere como o requisito pode ser satisfeito com um único espaço de trabalho:

| Requisito | Descrição | Maneiras de reduzir a contagem de workspace |
|-------------|-------------|--------------------------------|
| A soberania e conformidade regulatória | Um workspace está vinculado a uma região específica. Se os dados precisarem ser mantidos em [geografias diferentes do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) para atender aos requisitos regulatórios, eles deverão ser divididos em espaços de trabalho separados. |  |
| Propriedade dos dados | Os limites de propriedade de dados, por exemplo, por subsidiárias ou empresas afiliadas, são melhores delineados usando espaços de trabalho separados. |  |
| Vários locatários do Azure | O Azure Sentinel dá suporte à coleta de dados dos recursos Microsoft e SaaS do Azure somente dentro de seu próprio limite de locatário do Azure Active Directory (AD do Azure). Portanto, cada locatário do Azure AD requer um workspace separado. |  |
| Controle de acesso a dados granular | Uma organização pode precisar permitir que diferentes grupos, dentro ou fora da organização, acessem alguns dos dados coletados pelo Azure Sentinel. Por exemplo:<br><ul><li>O acesso dos proprietários de recursos aos dados pertencentes aos seus recursos</li><li>Acesso regional ou subsidiário SOCs aos dados relevantes para suas partes da organização</li></ul> | Usar [recurso do Azure RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/controlling-access-to-azure-sentinel-data-resource-rbac/ba-p/1301463) ou do Azure [RBAC de nível de tabela](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) |
| Configurações de retenção granulares | Historicamente, vários espaços de trabalho eram a única maneira de definir diferentes períodos de retenção para diferentes tipos de dados. Isso não é mais necessário em muitos casos, graças à introdução das configurações de retenção de nível de tabela. | Usar [as configurações de retenção de nível de tabela](https://techcommunity.microsoft.com/t5/azure-sentinel/new-per-data-type-retention-is-now-available-for-azure-sentinel/ba-p/917316) ou automatizar a [exclusão de dados](../azure-monitor/logs/personal-data-mgmt.md#how-to-export-and-delete-private-data) |
| Cobrança dividida | Ao colocar espaços de trabalho em assinaturas separadas, eles podem ser cobrados por partes diferentes. | Relatório de uso e cobrança cruzada |
| Arquitetura herdada | O uso de vários espaços de trabalho pode ser proveniente de um design histórico que levava em consideração as limitações ou as práticas recomendadas que não são mais verdadeiras. Também pode ser uma opção de design arbitrária que pode ser modificada para acomodar melhor o Azure Sentinel.<br><br>Os exemplos incluem:<br><ul><li>Usando um espaço de trabalho padrão por assinatura ao implantar a central de segurança do Azure</li><li>A necessidade de controle de acesso granular ou configurações de retenção, as soluções para as quais são relativamente novas</li></ul> | Rearquitetar workspaces |

### <a name="managed-security-service-provider-mssp"></a>Provedor de serviço de segurança gerenciado (MSSP)

Um caso de uso específico que exige vários espaços de trabalho é um serviço MSSP do Azure Sentinel. Nesse caso, muitos, caso contrário, todos os requisitos acima se aplicam, fazendo vários espaços de trabalho, entre locatários, a prática recomendada. O MSSP pode usar o [Lighthouse do Azure](../lighthouse/overview.md) para estender recursos entre espaços de trabalho do Azure Sentinel entre locatários.

## <a name="azure-sentinel-multiple-workspace-architecture"></a>Arquitetura de vários workspaces do Azure Sentinel

Conforme indicado pelos requisitos acima, há casos em que vários espaços de trabalho do Azure Sentinel, potencialmente em locatários Azure Active Directory (Azure AD), precisam ser monitorados centralmente e gerenciados por um único SOC.

- Um serviço MSSP do Azure Sentinel.

- Um SOC global que atende a várias subsidiárias, cada uma com seu próprio SOC local.

- Um SOC monitorando vários locatários do Azure AD em uma organização.

Para atender a esse requisito, o Azure Sentinel oferece recursos de vários espaços de trabalho que permitem o monitoramento central, a configuração e o gerenciamento, fornecendo um único painel de vidro em todos os itens cobertos pelo SOC, conforme apresentado no diagrama a seguir.

:::image type="content" source="media/extend-sentinel-across-workspaces-tenants/cross-workspace-architecture.png" alt-text="Arquitetura entre espaços de trabalho":::

Esse modelo oferece vantagens significativas em relação a um modelo totalmente centralizado no qual todos os dados são copiados para um único espaço de trabalho:

- Atribuição de função flexível para o SOCs global e local, ou para a MSSP de seus clientes.

- Menos desafios relacionados à propriedade de dados, privacidade de dados e conformidade regulatória.

- Latência mínima de rede e preços.

- Facilidade de integração e remoção de novas subsidiárias ou clientes.

Nas seções a seguir, explicaremos como operar esse modelo e, particularmente, como:

- Monitore centralmente vários espaços de trabalho, potencialmente em locatários, fornecendo o SOC com um único painel de vidro.

- Configure e gerencie centralmente vários espaços de trabalho, potencialmente em locatários, usando a automação.

## <a name="cross-workspace-monitoring"></a>Monitoramento entre espaços de trabalho

### <a name="manage-incidents-on-multiple-workspaces"></a>Gerenciar incidentes em vários espaços de trabalho

O Azure Sentinel dá suporte a uma [exibição de incidentes de vários espaços de trabalho](./multiple-workspace-view.md) facilitando o monitoramento e o gerenciamento de incidentes central em vários espaços de trabalho. A exibição de incidentes centralizado permite que você gerencie incidentes diretamente ou faça drill down de forma transparente para os detalhes do incidente no contexto do espaço de trabalho de origem.

### <a name="cross-workspace-querying"></a>Consulta entre espaços de trabalho

O Azure Sentinel dá suporte à consulta de [vários espaços de trabalho em uma única consulta](../azure-monitor/logs/cross-workspace-query.md), permitindo que você pesquise e correlacione dados de vários espaços de trabalho em uma única consulta. 

- Use a [expressão Workspace ()](../azure-monitor/logs/workspace-expression.md) para fazer referência a uma tabela em um espaço de trabalho diferente. 
- Use o [operador Union](/azure/data-explorer/kusto/query/unionoperator?pivots=azuremonitor) junto com a expressão de espaço de trabalho () para aplicar uma consulta entre tabelas em vários espaços de trabalho.

Você pode usar [funções](../azure-monitor/logs/functions.md) salvas para simplificar as consultas entre espaços de trabalho. Por exemplo, se uma referência a um espaço de trabalho for longa, talvez você queira salvar a expressão `workspace("customer-A's-hard-to-remember-workspace-name").SecurityEvent` como uma função chamada `SecurityEventCustomerA` . Em seguida, você pode escrever consultas como `SecurityEventCustomerA | where ...` .

Uma função também pode simplificar uma União comumente usada. Por exemplo, você pode salvar a seguinte expressão como uma função chamada `unionSecurityEvent` :

`union workspace(“hard-to-remember-workspace-name-1”).SecurityEvent, workspace(“hard-to-remember-workspace-name-2”).SecurityEvent`

Em seguida, você pode escrever uma consulta em ambos os espaços de trabalho começando com `unionSecurityEvent | where ...` .

#### <a name="cross-workspace-analytics-rules"></a>Regras de análise entre espaços de trabalho<a name="scheduled-alerts"></a>
<!-- Bookmark added for backward compatibility with old heading -->
As consultas entre espaços de trabalho agora podem ser incluídas em regras de análise agendada, sujeitas às seguintes limitações:

- Até 20 espaços de trabalho podem ser incluídos em uma única consulta.
- O Azure Sentinel deve ser implantado em todos os espaços de trabalho referenciados na consulta.

> [!NOTE] 
> Consultar vários espaços de trabalho na mesma consulta pode afetar o desempenho e, portanto, é recomendado somente quando a lógica requer essa funcionalidade.

#### <a name="cross-workspace-workbooks"></a>Pastas de trabalho entre espaços de trabalho<a name="using-cross-workspace-workbooks"></a>
<!-- Bookmark added for backward compatibility with old heading -->
As [pastas de trabalho](./overview.md#workbooks) fornecem painéis e aplicativos para o Azure Sentinel. Ao trabalhar com vários espaços de trabalho, eles fornecem monitoramento e ações entre espaços de trabalho.

As pastas de trabalho podem fornecer consultas entre espaços de trabalho em um dos três métodos, cada uma das quais atendem a diferentes níveis de experiência do usuário final:

| Método  | Descrição | Quando devo usar? |
|---------|-------------|--------------------|
| Gravar consultas entre espaços de trabalho | O criador da pasta de trabalho pode gravar consultas entre espaços de trabalho (descritas acima) na pasta de trabalho. | Essa opção permite que os criadores da pasta de trabalho protejam totalmente o usuário da estrutura do espaço de trabalho. |
| Adicionar um seletor de espaço de trabalho à pasta de trabalho | O criador da pasta de trabalho pode implementar um seletor de espaço de trabalho como parte da pasta de trabalho, conforme descrito [aqui](https://techcommunity.microsoft.com/t5/azure-sentinel/making-your-azure-sentinel-workbooks-multi-tenant-or-multi/ba-p/1402357). | Essa opção fornece ao usuário controle sobre os espaços de trabalho mostrados pela pasta de trabalho, por meio de uma caixa suspensa fácil de usar. |
| Editar a pasta de trabalho interativamente | Um usuário avançado que modifica uma pasta de trabalho existente pode editar as consultas nela, selecionando os espaços de trabalho de destino usando o seletor de espaço de trabalho no editor. | Essa opção permite que um usuário avançado modifique facilmente pastas de trabalho existentes para trabalhar com vários espaços de trabalho. |
|

#### <a name="cross-workspace-hunting"></a>Caça entre espaços de trabalho

O Azure Sentinel fornece exemplos de consulta pré-carregados projetados para ajudá-lo a começar e a familiarizar-se com as tabelas e a linguagem de consulta. Essas consultas de busca interna são desenvolvidas pelos pesquisadores de segurança da Microsoft de forma contínua, adicionando novas consultas e ajustando consultas existentes, para fornecer a você um ponto de entrada para procurar novas detecções e identificar sinais de intrusão que podem ter sido despercebidos por suas ferramentas de segurança.  

Os recursos de busca entre espaços de trabalho permitem que suas caçadores de ameaça criem novas consultas de busca ou adaptem as existentes para abranger vários espaços de trabalho, usando o operador Union e a expressão Workspace (), como mostrado acima.

## <a name="cross-workspace-management-using-automation"></a>Gerenciamento entre espaços de trabalho usando automação

Para configurar e gerenciar vários espaços de trabalho do Azure Sentinel, você precisará automatizar o uso da API de gerenciamento do Sentinela do Azure. Para obter mais informações sobre como automatizar a implantação de recursos do Azure Sentinel, incluindo regras de alerta, consultas de busca, pastas de trabalho e guias estratégicos, consulte [estendendo o Azure sentinela: APIs, integração e automação de gerenciamento](https://techcommunity.microsoft.com/t5/azure-sentinel/extending-azure-sentinel-apis-integration-and-management/ba-p/1116885).

Consulte também [Implantando e gerenciando o Azure Sentinel como código](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928) e [combinando o Azure Lighthouse com os recursos de DevOps do Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/combining-azure-lighthouse-with-sentinel-s-devops-capabilities/ba-p/1210966) para uma metodologia consolidada e contribuida pela Comunidade para gerenciar o Azure Sentinel como código e para implantar e configurar recursos de um repositório GitHub privado. 

## <a name="managing-workspaces-across-tenants-using-azure-lighthouse"></a>Gerenciando espaços de trabalho entre locatários usando o Azure Lighthouse

Conforme mencionado acima, em muitos cenários, os diferentes espaços de trabalho do Azure Sentinel podem estar localizados em locatários diferentes do Azure AD. Você pode usar o [Azure Lighthouse](../lighthouse/overview.md) para estender todas as atividades entre espaços de trabalho entre limites de locatário, permitindo que os usuários em seu locatário de gerenciamento trabalhem em espaços de trabalho do Azure Sentinel em todos os locatários. Depois que o Azure Lighthouse estiver [integrado](../lighthouse/how-to/onboard-customer.md), use o [seletor de diretório + assinatura](./multiple-tenants-service-providers.md#how-to-access-azure-sentinel-in-managed-tenants) na portal do Azure para selecionar todas as assinaturas que contêm espaços de trabalho que você deseja gerenciar, para garantir que todos estarão disponíveis nos seletores de espaço de trabalho diferentes no Portal.

Ao usar o Azure Lighthouse, é recomendável criar um grupo para cada função de sentinela do Azure e delegar permissões de cada locatário para esses grupos.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como os recursos do Azure Sentinel podem ser estendidos em vários espaços de trabalho e locatários. Para obter orientações práticas sobre como implementar a arquitetura entre espaços de trabalho do Azure Sentinel, consulte os seguintes artigos:

- Saiba como [trabalhar com vários locatários](./multiple-tenants-service-providers.md) no Azure Sentinel, usando o Azure Lighthouse.
- Saiba como [Exibir e gerenciar incidentes em vários espaços de trabalho](./multiple-workspace-view.md) diretamente.
