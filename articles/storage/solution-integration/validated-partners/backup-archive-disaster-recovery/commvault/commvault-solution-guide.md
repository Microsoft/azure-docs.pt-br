---
title: Fazer backup dos dados no Azure com o CommVault
titleSuffix: Azure Storage
description: Fornece uma visão geral dos fatores a serem considerados e etapas a serem seguidas para usar o Azure como um destino de armazenamento e um local de recuperação para backup e recuperação completos do CommVault
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: ce321574ce2878f51864f55bf5618df2c96d1068
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589881"
---
# <a name="backup-to-azure-with-commvault"></a>Backup no Azure com CommVault

Este artigo ajuda você a integrar uma infraestrutura do CommVault ao armazenamento de BLOBs do Azure. Ele inclui pré-requisitos, considerações, implementação e orientação operacional. Este artigo aborda como usar o Azure como um destino de backup externo e um site de recuperação, caso ocorra um desastre, o que impede a operação normal no site primário.

> [!NOTE]
> A CommVault oferece uma solução de RTO (objetivo de tempo de recuperação) menor, o CommVault Live Sync. Essa solução permite que você tenha uma VM em espera que possa ajudá-lo a se recuperar mais rapidamente no caso de um desastre em um ambiente de produção do Azure. Esses recursos estão fora do escopo deste documento.

## <a name="reference-architecture"></a>Arquitetura de referência

O diagrama a seguir fornece uma arquitetura de referência para o local para o Azure e para implantações no Azure.

![Arquitetura de referência do CommVault para o Azure](../media/commvault-diagram.png)

Sua implantação existente do CommVault pode integrar-se facilmente ao Azure adicionando uma conta de armazenamento do Azure ou várias contas, como um destino de armazenamento em nuvem. O CommVault também permite que você recupere backups locais no Azure, fornecendo um site de recuperação sob demanda no Azure.

## <a name="commvault-interoperability-matrix"></a>Matriz de interoperabilidade do CommVault

| Carga de trabalho | GPv2 e armazenamento de BLOBs | Suporte de camada fria | Suporte à camada de arquivo | Suporte da família Data Box |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| VMs/dados locais | v 11,5 | v 11,5 | v 11.10 | v 11.10 |
| VMs do Azure | v 11,5 | v 11,5 | v 11,5 | N/D |
| Blob do Azure | v 11.6 | v 11.6 | v 11.6 | N/D |
| Arquivos do Azure | v 11.6 | v 11.6 | v 11.6 | N/D |

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

Para determinar a quantidade de largura de banda necessária, use os seguintes recursos:

- Relatórios de seu software de backup.
- O CommVault fornece relatórios padrão para determinar a [taxa de alteração](https://documentation.commvault.com/commvault/v11_sp19/article?p=39699.htm) e o [Tamanho total do conjunto de backup](https://documentation.commvault.com/commvault/v11_sp19/article?p=39621.htm) para a transferência de linha de base inicial para o Azure.
- Ferramentas de relatório e avaliação independentes de software de backup como:
  - [MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
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
|**n º de regiões**     | 1         | 1         | 2         | 2 |
|**Failover manual para a região secundária**     | N/D         | N/D         | Sim         | Sim |

**Camadas de armazenamento de BLOBs:**

|  | Camada quente   |Camada fria   | Camada de arquivo |
| ----------- | ----------- | -----------  | -----------  |
| **Disponibilidade** | 99,9%         | 99%         | Offline      |
| **Encargos de uso** | Custos de armazenamento mais altos, acesso menor e custos de transações | Custos de armazenamento menores, custos de acesso e de transações maiores | Custos de armazenamento mais baixos, custos de acesso e de transações mais altos |
| **Retenção mínima de dados necessária**| N/D | 30 dias | 180 dias |
| **Latência (tempo até o primeiro byte)** | Milissegundos | Milissegundos | Horas |

#### <a name="sample-backup-to-azure-cost-model"></a>Exemplo de backup para o modelo de custo do Azure

O pagamento por uso pode ser assustador para clientes que são novos na nuvem. Enquanto você paga apenas a capacidade usada, você também paga por transações (leitura e gravação) e [saída de dados de](https://azure.microsoft.com/pricing/details/bandwidth/) leitura para o seu ambiente local quando o plano de dados de rota expressa [ou local direto do Azure Route](https://azure.microsoft.com/pricing/details/expressroute/) está em uso onde a saída de dados do Azure está incluída. Você pode usar a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) para executar a análise "e se". Você pode basear a análise em preços de lista ou no [preço de capacidade reservada de armazenamento do Azure](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md), que pode fornecer até 38% de economia. Aqui está um exercício de preços de exemplo para modelar o custo mensal de backup no Azure. Este é apenas um exemplo. *Seu preço pode variar devido a atividades não capturadas aqui.*

|Fator de custo  |Custo mensal  |
|---------|---------|
|100 TB de dados de backup no armazenamento frio     |$1556.48         |
|2 TB de novos dados gravados por dia x 30 dias     |$39 em transações          |
|Total estimado mensal     |$1595.48         |
|---------|---------|
|Restauração uma vez de 5 TB para local pela Internet pública   | $491.26         |

> [!NOTE]
> Essa estimativa foi gerada na calculadora de preços do Azure usando o preço pago conforme o uso do leste dos EUA e baseia-se no padrão CommVault de tamanho de subparte de 32 MB que gera 65.536 solicitações PUT (transações de gravação), por dia. Este exemplo pode não ser aplicável em relação às suas necessidades.

## <a name="implementation-guidance"></a>Diretrizes de implementação

Esta seção fornece um breve guia sobre como adicionar o armazenamento do Azure a uma implantação local do CommVault. Para obter orientações detalhadas e considerações de planejamento, consulte o [Guia de arquitetura de nuvem pública do CommVault para Microsoft Azure](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf).

1. Abra o portal do Azure e procure contas de **armazenamento**. Você também pode clicar no ícone **contas de armazenamento** padrão.

    ![Mostra a adição de contas de armazenamento no portal do Azure.](../media/azure-portal.png)
  
    ![Mostra onde você digitou o armazenamento na caixa de pesquisa do portal do Azure.](../media/locate-storage-account.png)

2. Selecione **criar** para adicionar uma conta. Selecione ou crie um grupo de recursos, forneça um nome exclusivo, escolha a região, selecione desempenho **padrão** , sempre deixe tipo de conta como **armazenamento v2**, escolha o nível de replicação que atende aos seus SLAs e a camada padrão que seu software de backup irá aplicar. Uma conta de armazenamento do Azure disponibiliza camadas quentes, frias e de arquivo em uma única conta e as políticas do CommVault permitem que você use várias camadas para gerenciar com eficiência o ciclo de vida dos seus dados.

    ![Mostra as configurações da conta de armazenamento no portal](../media/account-create-1.png)

3. Mantenha as opções de rede padrão por enquanto e passe para a **proteção de dados**. Aqui, você pode optar por habilitar a exclusão reversível, que permite recuperar um arquivo de backup excluído acidentalmente dentro do período de retenção definido e oferece proteção contra exclusão acidental ou mal-intencionada.

    ![Mostra as configurações de proteção de dados no Portal.](../media/account-create-2.png)

4. Em seguida, recomendamos as configurações padrão da tela **avançado** para fazer backup em casos de uso do Azure.

    ![Mostra a guia Configurações avançadas no Portal.](../media/account-create-3.png)

5. Adicione marcas para organização se você usar a marcação e criar sua conta.

6. Duas etapas rápidas são todas agora necessárias para que você possa adicionar a conta ao seu ambiente CommVault. Navegue até a conta que você criou no portal do Azure e selecione **contêineres** no menu **serviço blob** . Adicione um contêiner e escolha um nome significativo. Em seguida, navegue até o item **chaves de acesso** em **configurações** e copie o nome da **conta de armazenamento** e uma das duas chaves de acesso. Você precisará do nome do contêiner, do nome da conta e da chave de acesso nas próximas etapas.

    ![Mostra a criação de contêiner no Portal.](../media/container.png)

    ![Mostra as configurações de chave de acesso no Portal.](../media/access-key.png)

7. (*Opcional*) Você pode adicionar mais camadas de segurança à sua implantação.

    1. Configure o acesso baseado em função para limitar quem pode fazer alterações em sua conta de armazenamento. Para obter mais informações, consulte [funções internas para operações de gerenciamento](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations).
    1. Restrinja o acesso à conta para segmentos de rede específicos com [configurações de firewall de armazenamento](../../../../common/storage-network-security.md) para evitar tentativas de acesso de fora de sua rede corporativa.

        ![Mostra as configurações de firewall de armazenamento no Portal.](../media/storage-firewall.png)

    1. Defina um [bloqueio de exclusão](../../../../../azure-resource-manager/management/lock-resources.md) na conta para impedir a exclusão acidental da conta de armazenamento.

        ![Mostra a configuração de um bloqueio de exclusão no Portal.](../media/resource-lock.png)

    1. Configure [as práticas recomendadas de segurança](../../../../../storage/blobs/security-recommendations.md)adicionais.

1. No centro de comando do CommVault, navegue até **gerenciar**  ->  Gerenciador de credenciais de **segurança**  ->  . Escolha uma **conta de nuvem**, **tipo de fornecedor** de **armazenamento do Microsoft Azure**, selecione o **MediaAgent**, que transferirá dados de e para o Azure, adicione o nome da conta de armazenamento e a chave de acesso.

    ![Mostra a adição de credenciais no centro de comando do CommVault.](../media/commvault-credential.png)

9. Em seguida, navegue até **Storage**  ->  **Cloud** no CommVault Command Center. Escolha **Adicionar**. Insira um nome amigável para a conta de armazenamento e, em seguida, selecione **armazenamento do Microsoft Azure** na lista **tipo** . Selecione um servidor de agente de mídia a ser usado para transferir backups para o armazenamento do Azure. Adicione o contêiner que você criou, escolha a camada de armazenamento a ser usada na conta de armazenamento do Azure e selecione as credenciais criadas na etapa #8. Por fim, escolha se deseja ou não transferir backups com eliminação de duplicação ou não e um local para o banco de dados de eliminação de duplicação.

     ![Captura de tela da interface do usuário adicionar nuvem do CommVault. No menu suspenso arquivo morto, * * arquivo * * está selecionado.](../media/commvault-add-storage.png)

10. Por fim, adicione seu novo recurso de armazenamento do Azure a um plano novo ou existente no centro de comando do CommVault por meio de **gerenciar**  ->  **planos** como um destino de backup.

    ![Captura de tela da interface do usuário do CommVault Command Center. No painel de navegação esquerdo, em * * gerenciar * *, * * planos * * está selecionado.](../media/commvault-plan.png)

11. *(Opcional)* Se você planeja usar o Azure como um site de recuperação ou o CommVault para migrar servidores e aplicativos para o Azure, é uma prática recomendada implantar um proxy de VSA no Azure. Você pode encontrar instruções detalhadas [aqui](https://documentation.commvault.com/commvault/v11/article?p=106208.htm).

## <a name="operational-guidance"></a>Diretrizes operacionais

### <a name="azure-alerts-and-performance-monitoring"></a>Alertas do Azure e monitoramento de desempenho

É aconselhável monitorar os recursos do Azure e a capacidade do CommVault de aproveitá-los como você faria com qualquer destino de armazenamento em que você dependa para armazenar seus backups. Uma combinação de Azure Monitor e monitoramento do centro de comando CommVault ajudará você a manter seu ambiente íntegro.

#### <a name="azure-portal"></a>Portal do Azure

O Azure fornece uma solução de monitoramento robusta na forma de [Azure monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md). Você pode [configurar Azure monitor](../../../../blobs/monitor-blob-storage.md) para controlar a capacidade, as transações, a disponibilidade, a autenticação e muito mais do armazenamento do Azure. Você pode encontrar a referência completa de métricas que são coletadas [aqui](../../../../blobs/monitor-blob-storage-reference.md). Algumas métricas úteis a serem controladas são capacidade-para garantir que você permaneça abaixo do limite máximo de [capacidade da conta de armazenamento](../../../../common/scalability-targets-standard-account.md), entrada e saída – para acompanhar a quantidade de dados que estão sendo gravados e lidos de sua conta de armazenamento do Azure e SuccessE2ELatency-para acompanhar o tempo de ida e volta das solicitações de e para o armazenamento do Azure e o MediaAgent.

Você também pode [criar alertas de log](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) para acompanhar a integridade do serviço de armazenamento do Azure e exibir o [painel de status do Azure](https://status.azure.com/status) a qualquer momento.

#### <a name="commvault-command-center"></a>Centro de comando do CommVault

- [Criar um alerta para pools de armazenamento em nuvem](https://documentation.commvault.com/commvault/v11/article?p=100514_3.htm)
- Para obter informações sobre onde você pode exibir relatórios de desempenho, conclusão de trabalho e iniciar a solução básica de problemas, consulte [painéis](https://documentation.commvault.com/commvault/v11/article?p=95306_1.htm).

### <a name="how-to-open-support-cases"></a>Como abrir casos de suporte

Quando precisar de ajuda com a solução de backup para o Azure, você deverá abrir um caso com o CommVault e o Azure. Isso ajuda nossas organizações de suporte a colaborarem, se necessário.

#### <a name="to-open-a-case-with-commvault"></a>Para abrir um caso com o CommVault

No [site de suporte do CommVault](https://www.commvault.com/support), entre e abra um caso.

Para entender as opções de contrato de suporte disponíveis para você, consulte [Opções de suporte do CommVault](https://ma.commvault.com/support)

Você também pode chamar em para abrir um caso ou acessar o suporte do CommVault por email:

- Ligação gratuita: + 1 877-780-3077
- [Números de suporte em todo o mundo](https://ma.commvault.com/Support/TelephoneSupport)
- [Email de suporte do CommVault](mailto:support@commvault.com)

#### <a name="to-open-a-case-with-azure"></a>Para abrir um caso com o Azure

Na [portal do Azure](https://portal.azure.com) procure **suporte** na barra de pesquisa na parte superior. Selecione **ajuda + suporte**  ->  **nova solicitação de suporte**.

> [!NOTE]
> Ao abrir um caso, seja específico que você precise de assistência com o armazenamento do Azure ou a rede do Azure. Não especifique o backup do Azure. O backup do Azure é o nome de um serviço do Azure e seu caso será roteado incorretamente.

### <a name="links-to-relevant-commvault-documentation"></a>Links para a documentação relevante do CommVault

Consulte a documentação do CommVault a seguir para obter mais detalhes:

- [Guia do usuário do CommVault](https://documentation.commvault.com/commvault/v11/article?p=37684_1.htm)
- [Guia de arquitetura do CommVault Azure](https://www.commvault.com/resources/public-cloud-architecture-guide-for-microsoft-azure-v11-sp16)

### <a name="marketplace-offerings"></a>Ofertas do Marketplace

A CommVault facilitou a implantação de sua solução no Azure para proteger as máquinas virtuais do Azure e muitos outros serviços do Azure. Para saber mais, consulte as referências a seguir:

- [Implantar o CommVault por meio do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault?tab=Overview)
- [CommVault para folha de tabela do Azure](https://www.commvault.com/resources/microsoft-azure-cloud-platform-datasheet)
- [Lista de recursos e serviços do Azure com suporte da CommVault](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)
- [Como usar o CommVault para proteger SAP HANA no Azure](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)

## <a name="next-steps"></a>Próximas etapas

Consulte estes recursos adicionais do CommVault para obter informações sobre cenários de uso especializado.

- [Use o CommVault para migrar seus servidores e aplicativos para o Azure](https://www.commvault.com/resources/demonstration-vmware-to-azure-migrations-with-commvault)
- [Proteger o SAP no Azure com o CommVault](https://www.youtube.com/watch?v=4ZGGE53mGVI)
- [Proteger o Office365 com o CommVault](https://www.youtube.com/watch?v=dl3nvAacxZU)