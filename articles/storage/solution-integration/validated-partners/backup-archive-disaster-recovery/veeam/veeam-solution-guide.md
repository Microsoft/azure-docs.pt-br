---
title: Fazer backup dos dados no Azure com o Veeam
titleSuffix: Azure Storage
description: Fornece uma visão geral dos fatores a serem considerados e etapas a serem seguidas para usar o Azure como um destino de armazenamento e um local de recuperação para backup e recuperação do Veeam
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 0b8bc0defd3314fcff691a049323201732644ff3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589898"
---
# <a name="backup-to-azure-with-veeam"></a>Backup no Azure com Veeam

Este artigo ajuda você a integrar uma infraestrutura do Veeam com o armazenamento de BLOBs do Azure. Ele inclui pré-requisitos, considerações, implementação e orientação operacional. Este artigo aborda como usar o Azure como um destino de backup externo e um site de recuperação, caso ocorra um desastre, o que impede a operação normal no site primário.

> [!NOTE]
> O Veeam também oferece uma solução de RTO (objetivo de tempo de recuperação) menor, replicação do Veeam. Essa solução permite que você tenha uma VM em espera que possa ajudá-lo a se recuperar mais rapidamente no caso de um desastre em um ambiente de produção do Azure. O Veeam também tem ferramentas dedicadas para fazer backup de recursos do Azure e do Office 365. Esses recursos estão fora do escopo deste documento.

## <a name="reference-architecture"></a>Arquitetura de referência

O diagrama a seguir fornece uma arquitetura de referência para o local para o Azure e para implantações no Azure.

![Diagrama da arquitetura de referência do Veeam para o Azure.](../media/veeam-architecture.png)

Sua implantação do Veeam existente pode integrar-se facilmente ao Azure adicionando uma conta de armazenamento do Azure ou várias contas, como um repositório de backup na nuvem. O Veeam também permite que você recupere backups do local no Azure, fornecendo um site de recuperação sob demanda no Azure.

## <a name="veeam-interoperability-matrix"></a>Matriz de interoperabilidade Veeam

| Carga de trabalho | GPv2 e armazenamento de BLOBs | Suporte de camada fria | Suporte à camada de arquivo | Suporte da família Data Box |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| VMs/dados locais | v10a | v10a | N/D | 10A<sup>*</sup> |
| VMs do Azure | v10a | v10a | N/D | 10A<sup>*</sup> |
| Blob do Azure | v10a | v10a | N/D | 10A<sup>*</sup> |
| Arquivos do Azure | v10a | v10a | N/D | 10A<sup>*</sup> |

<sup>*</sup>O backup do Veeam e a replicação oferecem suporte à API REST somente para Azure Data Box. Portanto, não há suporte para Disco do Azure Data Box.

## <a name="before-you-begin"></a>Antes de começar

Um pequeno planejamento antecipado ajudará você a usar o Azure como um destino de backup externo e um site de recuperação.

### <a name="get-started-with-azure"></a>Introdução ao Azure

A Microsoft oferece uma estrutura para ajudá-lo a começar a usar o Azure. A CAF ( [estrutura de adoção de nuvem](/azure/architecture/cloud-adoption/) ) é uma abordagem detalhada da transformação digital corporativa e do guia abrangente para o planejamento de uma adoção de nuvem de nível de produção. O CAF inclui um guia passo a passo de [instalação do Azure](/azure/cloud-adoption-framework/ready/azure-setup-guide/) para ajudá-lo a entrar em funcionamento com rapidez e segurança. Você pode encontrar uma versão interativa no [portal do Azure](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade). Você encontrará arquiteturas de exemplo, práticas recomendadas específicas para implantar aplicativos e recursos de treinamento gratuitos para colocá-lo no caminho para a experiência do Azure.

### <a name="consider-the-network-between-your-location-and-azure"></a>Considere a rede entre seu local e o Azure

Seja usando recursos de nuvem para executar produção, teste e desenvolvimento, ou como um destino de backup e um site de recuperação, é importante entender suas necessidades de largura de banda para a propagação de backup inicial e para transferências diárias contínuas.

Azure Data Box fornece uma maneira de transferir sua linha de base de backup inicial para o Azure sem exigir mais largura de banda. Isso será útil se a transferência de linha de base for estimada para levar mais tempo do que você pode tolerar. Você pode usar o Transferência de Dados estimador ao criar uma conta de armazenamento para estimar o tempo necessário para transferir o backup inicial.

![Mostra o estimador de transferência de dados do armazenamento do Azure no Portal.](../media/az-storage-transfer.png)

Lembre-se de que você precisará de capacidade de rede suficiente para dar suporte a transferências de dados diárias na janela de transferência necessária (janela de backup) sem afetar os aplicativos de produção. Esta seção descreve as ferramentas e técnicas disponíveis para avaliar suas necessidades de rede.

#### <a name="determine-how-much-bandwidth-youll-need"></a>Determinar a quantidade de largura de banda necessária

Várias opções de avaliação estão disponíveis para determinar a taxa de alteração e o tamanho total do conjunto de backup para a transferência de linha de base inicial para o Azure. Aqui estão alguns exemplos de ferramentas de avaliação e relatório:

- [MiTrend](https://mitrend.com/)
- [Apt são](https://www.veritas.com/insights/aptare-it-analytics)
- [Datavoss](https://www.datavoss.com/)

#### <a name="determine-unutilized-internet-bandwidth"></a>Determinar a largura de banda da Internet não utilizada

É importante saber a quantidade de largura de banda inutilizada (ou *espaço* disponível) que você disponibilizou diariamente. Isso ajuda a avaliar se você pode atender às suas metas para:

- hora inicial para carregar quando você não estiver usando Azure Data Box para propagação offline
- Concluindo backups diários com base na taxa de alteração identificada anteriormente e sua janela de backup

Use os métodos a seguir para identificar a reserva de largura de banda que os backups do Azure estão livres para consumir.

- Se você for um cliente do Azure ExpressRoute existente, exiba o [uso do circuito](../../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics) no portal do Azure.
- Entre em contato com seu ISP. Eles devem ser capazes de compartilhar relatórios que mostram sua utilização diária e mensal existente.
- Há várias ferramentas que podem medir a utilização monitorando o tráfego de rede no nível do roteador/comutador. Elas incluem:

  - [Pacote do analisador de largura de banda SolarWinds](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Assistente de rede Cisco](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choose-the-right-storage-options"></a>Escolha as opções de armazenamento corretas

Ao usar o Azure como um destino de backup, você fará uso do [armazenamento de BLOBs do Azure](../../../../blobs/storage-blobs-introduction.md). O armazenamento de BLOBs é a solução de armazenamento de objetos da Microsoft. O armazenamento de BLOBs é otimizado para armazenar grandes quantidades de dados não estruturados, que são dados que não aderem a nenhum modelo de dados ou definição. Além disso, o armazenamento do Azure é durável, altamente disponível, seguro e escalonável. Você pode selecionar o armazenamento certo para sua carga de trabalho para fornecer o [nível de resiliência](../../../../common/storage-redundancy.md) para atender aos seus SLAs internos. O armazenamento de BLOBs é um serviço de pagamento por uso. Você será [cobrado mensalmente](../../../../blobs/storage-blob-storage-tiers.md#pricing-and-billing) pela quantidade de dados armazenados, acessando esses dados e, no caso de camadas frias e de arquivo, um período de retenção mínimo necessário. As opções de resiliência e de camadas aplicáveis aos dados de backup são resumidas nas tabelas a seguir.

**Opções de resiliência do armazenamento de BLOBs:**

|  |Com redundância local  |Com redundância de zona  |Redundância geográfica  |Com redundância de zona geográfica  |
|---------|---------|---------|---------|---------|
|**N º efetivo de cópias**     | 3         | 3         | 6         | 6 |
|**n º de zonas de disponibilidade**     | 1         | 3         | 2         | 4 |
|**n º de regiões** s     | 1         | 1         | 2         | 2 |
|**Failover manual para a região secundária**     | N/D         | N/D         | Sim         | Sim |

**Camadas de armazenamento de BLOBs:**

|  | Camada quente   |Camada fria   | Camada de arquivo |
| ----------- | ----------- | -----------  | -----------  |
| **Disponibilidade** | 99,9%         | 99%         | Offline      |
| **Encargos de uso** | Custos de armazenamento mais altos, acesso menor e custos de transações | Custos de armazenamento menores, custos de acesso e de transações maiores | Custos de armazenamento mais baixos, custos de acesso e de transações mais altos |
| **Retenção mínima de dados necessária** | NA | 30 dias | 180 dias |
| **Latência (tempo até o primeiro byte)** | Milissegundos | Milissegundos | Horas |

#### <a name="sample-backup-to-azure-cost-model"></a>Exemplo de backup para o modelo de custo do Azure

O pagamento por uso pode ser assustador para clientes que são novos na nuvem. Enquanto você paga apenas a capacidade usada, você também paga por transações (leitura e gravação) e [saída de dados de](https://azure.microsoft.com/pricing/details/bandwidth/) leitura para o seu ambiente local quando o plano de dados de rota expressa [ou local direto do Azure Route](https://azure.microsoft.com/pricing/details/expressroute/) está em uso onde a saída de dados do Azure está incluída. Você pode usar a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) para executar a análise "e se". Você pode basear a análise em preços de lista ou no [preço de capacidade reservada de armazenamento do Azure](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md), que pode fornecer até 38% de economia. Aqui está um exercício de preços de exemplo para modelar o custo mensal de backup no Azure. Este é apenas um exemplo. *Seu preço pode variar devido a atividades não capturadas aqui.*

|Fator de custo  |Custo mensal  |
|---------|---------|
|100 TB de dados de backup no armazenamento frio     |$1556.48         |
|2 TB de novos dados gravados por dia x 30 dias     |$72 em transações          |
|Total estimado mensal     |$1628.48         |
|---------|---------|
|Restauração uma vez de 5 TB para local pela Internet pública   | $527.26         |

> [!Note]
> Essa estimativa foi gerada na calculadora de preços do Azure usando o preço pago conforme o uso do leste dos EUA e é baseada no padrão Veeam do tamanho da parte de 256 KB para transferências de WAN. Este exemplo pode não ser aplicável em relação às suas necessidades.

## <a name="implementation-guidance"></a>Diretrizes de implementação

Esta seção fornece um breve guia sobre como adicionar o armazenamento do Azure a uma implantação de Veeam local. Para obter orientações detalhadas e considerações de planejamento, consulte o [Guia de backup do Veeam Cloud Connect](https://helpcenter.veeam.com/docs/backup/cloud/cloud_backup.html?ver=100).

1. Abra o portal do Azure e procure contas de **armazenamento**. Você também pode clicar no ícone de serviço padrão.

      ![Mostra a adição de contas de armazenamento no portal do Azure.](../media/azure-portal.png)

      ![Mostra onde você digitou o armazenamento na caixa de pesquisa do portal do Azure.](../media/locate-storage-account.png)

2. Selecione **criar** para adicionar uma conta. Selecione ou crie um grupo de recursos, forneça um nome exclusivo, escolha a região, selecione desempenho **padrão** , sempre deixe tipo de conta como **armazenamento v2**, escolha o nível de replicação que atende aos seus SLAs e a camada padrão que seu software de backup irá aplicar. Uma conta de armazenamento do Azure disponibiliza camadas quentes, frias e de arquivo em uma única conta e as políticas de Veeam permitem que você use várias camadas para gerenciar com eficiência o ciclo de vida dos seus dados.

    ![Mostra as configurações da conta de armazenamento no portal](../media/account-create-1.png)

3. Mantenha as opções de rede padrão por enquanto e passe para a **proteção de dados**. Aqui, você pode optar por habilitar a exclusão reversível, que permite recuperar um arquivo de backup excluído acidentalmente dentro do período de retenção definido e oferece proteção contra exclusão acidental ou mal-intencionada.

    ![Mostra as configurações de proteção de dados no Portal.](../media/account-create-2.png)

4. Em seguida, recomendamos as configurações padrão da tela **avançado** para fazer backup em casos de uso do Azure.

    ![Mostra a guia Configurações avançadas no Portal.](../media/account-create-3.png)

5. Adicione marcas para organização se você usar a marcação e criar sua conta.

6. Duas etapas rápidas são todas agora necessárias para que você possa adicionar a conta ao seu ambiente Veeam. Navegue até a conta que você criou no portal do Azure e selecione **contêineres** no menu **serviço blob** . Adicione um contêiner e escolha um nome significativo. Em seguida, navegue até o item **chaves de acesso** em **configurações** e copie o nome da **conta de armazenamento** e uma das duas chaves de acesso. Será necessário o nome do contêiner, o nome da conta e a chave de acesso nas próximas etapas.

    ![Mostra a criação de contêiner no Portal.](../media/container-b.png)

    ![Mostra as configurações de chave de acesso no Portal.](../media/access-key.png)

    > [!Note]
    > O backup e a replicação do Veeam oferecem opções adicionais para se conectar ao Azure. Para o caso de uso deste artigo, usando Microsoft Azure armazenamento de BLOBs como um destino de backup, usar o método acima é a prática recomendada.

7. *(Opcional)* Você pode adicionar mais camadas de segurança à sua implantação.

     1. Configure o acesso baseado em função para limitar quem pode fazer alterações em sua conta de armazenamento. Para obter mais informações, consulte [funções internas para operações de gerenciamento](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations).

     1. Restrinja o acesso à conta para segmentos de rede específicos com [configurações de firewall de armazenamento](../../../../common/storage-network-security.md) para evitar tentativas de acesso de fora de sua rede corporativa.

        ![Mostra as configurações de firewall de armazenamento no Portal.](../media/storage-firewall.png)

     1. Defina um [bloqueio de exclusão](../../../../../azure-resource-manager/management/lock-resources.md) na conta para impedir a exclusão acidental da conta de armazenamento.

        ![Bloqueio de recurso](../media/resource-lock.png)

     1. Configure [as práticas recomendadas de segurança](../../../../../storage/blobs/security-recommendations.md)adicionais.

8. No console de gerenciamento de backup e replicação do Veaam, navegue até **infraestrutura de backup** -> clique com o botão direito do mouse no painel Visão geral e selecione **Adicionar repositório de backup** para abrir o assistente de configuração. Na caixa de diálogo, selecione **armazenamento de objetos** Microsoft Azure armazenamento de BLOBs armazenamento de  ->    ->  **BLOBs do Azure**.

    ![Mostra a seleção do armazenamento de objetos no assistente de repositório do Veeam.](../media/veeam-repo-a.png)

    ![Mostra a seleção de Microsoft Azure armazenamento de BLOBs no assistente de repositório de Veeam.](../media/veeam-repo-b.png)

    ![Mostra a seleção do armazenamento de BLOBs do Azure no assistente de repositório do Veeam.](../media/veeam-repo-c.png)

9. Em seguida, especifique um nome e uma descrição do seu novo repositório de armazenamento de BLOBs.

    ![Mostra a digitação de um nome para o repositório no assistente de repositório do Veeam.](../media/veeam-repo-d.png)

10. Na próxima etapa, adicione as credenciais para acessar sua conta de armazenamento do Azure. Selecione **armazenamento do Microsoft Azure conta** no Gerenciador de credenciais de nuvem e insira o nome da conta de armazenamento e a chave de acesso. Selecione **global do Azure** no seletor de região e qualquer servidor gateway, se aplicável.

    ![Mostra a especificação de uma conta no assistente de repositório do Veeam.](../media/veeam-repo-e.png)

    > [!Note]
    > Se você optar por não usar um servidor de gateway Veeam, certifique-se de que todas as extensões de repositório de expansão tenham acesso direto à Internet.

11. No registro de contêiner, selecione o contêiner de armazenamento do Azure e selecione ou crie uma pasta na qual armazenar os backups. Você também pode definir um limite flexível na capacidade de armazenamento geral a ser usada pelo Veeam, o que é recomendado. Examine as informações exibidas na seção Resumo e conclua a ferramenta de configuração. Agora você pode selecionar o novo repositório em sua configuração de trabalho de backup.

    ![Mostra a especificação de um contêiner no assistente de repositório Veeam.](../media/veeam-repo-f.png)

    ![Mostra a criação de uma pasta no assistente de repositório do Veeam.](../media/veeam-repo-g.png)

## <a name="operational-guidance"></a>Diretrizes operacionais

### <a name="azure-alerts-and-performance-monitoring"></a>Alertas do Azure e monitoramento de desempenho

É aconselhável monitorar os recursos do Azure e a capacidade do Veeam de aproveitá-los como você faria com qualquer destino de armazenamento em que você depende para armazenar seus backups. Uma combinação dos recursos de monitoramento do Azure Monitor e do Veeam (a guia **estatísticas** no nó **trabalhos** do console de gerenciamento do Veeam ou opções mais avançadas, como Veeam um reporter), ajudará você a manter seu ambiente íntegro.

#### <a name="azure-portal"></a>Portal do Azure

O Azure fornece uma solução de monitoramento robusta na forma de [Azure monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md). Você pode [configurar Azure monitor](../../../../blobs/monitor-blob-storage.md) para controlar a capacidade, as transações, a disponibilidade, a autenticação e muito mais do armazenamento do Azure. A referência completa das métricas acompanhadas pode ser encontrada [aqui](../../../../blobs/monitor-blob-storage-reference.md). Algumas métricas úteis a serem controladas são capacidade-para garantir que você permaneça abaixo do limite máximo de [capacidade da conta de armazenamento](../../../../common/scalability-targets-standard-account.md), entrada e saída – para acompanhar a quantidade de dados que estão sendo gravados e lidos de sua conta de armazenamento do Azure e SuccessE2ELatency-para acompanhar o tempo de ida e volta das solicitações de e para o armazenamento do Azure e o MediaAgent.

Você também pode [criar alertas de log](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) para acompanhar a integridade do serviço de armazenamento do Azure e exibir o [painel de status do Azure](https://status.azure.com/status) a qualquer momento.

#### <a name="veeam-reporting"></a>Relatórios do Veeam

- [Configurar um relatório do Veeam One](https://helpcenter.veeam.com/docs/one/reporter/configure_reporter.html?ver=100)
- [Veeam Backup e replicação de alarmes](https://helpcenter.veeam.com/docs/one/monitor/backup_alarms.html?ver=100)

### <a name="how-to-open-support-cases"></a>Como abrir casos de suporte

Quando precisar de ajuda com o backup para a solução do Azure, você deverá abrir um caso com o Veeam e o Azure. Isso ajuda nossas organizações de suporte a colaborarem, se necessário.

#### <a name="to-open-a-case-with-veeam"></a>Para abrir um caso com Veeam

No [site de suporte ao cliente do Veeam](https://www.veeam.com/support.html), entre e abra um caso.

Para entender as opções de suporte disponíveis por Veeam, consulte a [política de suporte ao cliente do Veeam](https://www.veeam.com/veeam_software_support_policy_ds.pdf).

Você também pode chamar para abrir um caso: [números de suporte no mundo inteiro](https://www.veeam.com/contacts.html?ad=in-text-link#support-numbers)

#### <a name="to-open-a-case-with-azure"></a>Para abrir um caso com o Azure

Na [portal do Azure](https://portal.azure.com) procure **suporte** na barra de pesquisa na parte superior. Selecione **ajuda + suporte**  ->  **nova solicitação de suporte**.

> [!NOTE]
> Ao abrir um caso, seja específico que você precise de assistência com o armazenamento do Azure ou a rede do Azure. Não especifique o backup do Azure. O backup do Azure é o nome de um serviço do Azure e seu caso será roteado incorretamente.

### <a name="links-to-relevant-veeam-documentation"></a>Links para a documentação relevante do Veeam

Consulte a seguinte documentação do Veeam para obter mais detalhes:

- [Guia do usuário do Veeam](https://helpcenter.veeam.com/docs/backup/hyperv/overview.html?ver=100)
- [Guia de arquitetura do Veeam](https://helpcenter.veeam.com/docs/backup/vsphere/backup_architecture.html?ver=100)

### <a name="marketplace-offerings"></a>Ofertas do Marketplace

Você pode continuar a usar a solução Veeam que você conhece e confia para proteger suas cargas de trabalho em execução no Azure. O Veeam tornou mais fácil implantar sua solução no Azure e proteger máquinas virtuais do Azure e muitos outros serviços do Azure.

- [Implantar Veeam Backup & replicação por meio do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/veeam.veeam-backup-replication?tab=overview)
- [Site de recuperação e backup do Azure do Veeam](https://www.veeam.com/backup-azure.html?ad=menu-products)

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes recursos no site do Veeam para obter informações sobre cenários de uso especializado:

- [Vídeos de Veeam](https://www.veeam.com/how-to-videos.html?ad=menu-resources)
- [Documentações técnicas do Veeam](https://www.veeam.com/documentation-guides-datasheets.html?ad=menu-resources)
- [Veeam base de dados de conhecimento e perguntas frequentes](https://www.veeam.com/knowledge-base.html?ad=menu-resources)