---
title: Fazer backup dos dados no Azure com o Veeam
titleSuffix: Azure Blob Storage Docs
description: A página da Web fornece uma visão geral dos fatores a serem considerados e das etapas a serem seguidas para aproveitar o Azure como um destino de armazenamento e um local de recuperação para backup e recuperação do Veeam
keywords: Veeam,, backup em nuvem, backup, backup no Azure, recuperação de desastres, continuidade dos negócios
author: karauten
ms.author: karauten
ms.date: 11/11/2020
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.openlocfilehash: 6d4b005a3f9c79ff14f5ba4053dc651c1ede56e0
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124424"
---
# <a name="backup-to-azure-with-veeam"></a>Backup no Azure com Veeam

Este artigo fornece um guia para a integração de uma infraestrutura do Veeam com o armazenamento de BLOBs do Azure. Ele inclui pré-requisitos, princípios de armazenamento do Azure, implementação e orientação operacional. Este artigo aborda apenas o uso do Azure como um destino de backup externo e um site de recuperação em caso de desastre, o que impede a operação normal no site primário. O Veeam também oferece uma solução de RTO inferior, replicação de Veeam, como meio de ter uma VM em espera pronta para inicializar e recuperar mais rapidamente em caso de desastre e proteção de recursos em um ambiente de produção do Azure. O Veeam também dedica ferramentas para fazer backup de recursos do Azure e do Office 365. Esses recursos estão fora do escopo deste documento. 

## <a name="reference-architecture-for-on-premises-to-azure-and-in-azure-deployments"></a>Arquitetura de referência para implantações locais para o Azure e In-Azure

![Veeam para arquitetura de referência do Azure](../media/veeam-architecture.png)

Sua implantação do Veeam existente pode integrar-se facilmente ao Azure adicionando uma conta de armazenamento do Azure ou várias contas, como um repositório de backup na nuvem. O Veeam também permite que você recupere backups do local no Azure, fornecendo um site de recuperação sob demanda no Azure.

## <a name="veeam-interoperability-matrix"></a>Matriz de interoperabilidade Veeam
| Carga de trabalho | GPv2 e armazenamento de BLOBs | Suporte de camada fria | Suporte à camada de arquivo | Suporte da família Data Box |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| VMs/dados locais | v10a | v10a | NA | 10A |
| VMs do Azure | v10a | v10a | NA | 10A |
| Blob do Azure | v10a | v10a | NA | 10A |
| Arquivos do Azure | v10a | v10a | NA | 10A | 

> [!Note]
O backup e a replicação do Veeam só dão suporte à API REST para Azure Data Box, portanto, não há suporte para Disco do Azure Data Box. O suporte para a camada de arquivamento do armazenamento de BLOBs do Azure é esperado em Veeam v11.

## <a name="before-you-begin"></a>Antes de começar

R um pequeno planejamento antecipado garantirá que você ingresse nas classificações dos muitos, muitos clientes felizes usando o Azure como um destino de backup externo e um site de recuperação.

### <a name="are-you-new-to-azure"></a>Você é novo no Azure?

A Microsoft oferece uma estrutura para ajudá-lo a começar a usar o Azure. A [estrutura de adoção de nuvem](https://docs.microsoft.com/azure/architecture/cloud-adoption/) \( CAF \) é uma abordagem detalhada para a transformação digital corporativa e o guia abrangente para planejar uma adoção de nuvem de nível de produção. O CAF inclui um guia passo a passo de [instalação do Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/azure-setup-guide/) para aqueles novos no Azure para ajudá-lo a entrar em funcionamento com rapidez e segurança e pode encontrar uma versão interativa no [portal do Azure](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade). Você encontrará arquiteturas de exemplo e práticas recomendadas específicas para implantar aplicativos e liberar recursos de treinamento para colocá-lo no caminho para a experiência do Azure.

### <a name="consider-the-network-between-your-location-and-azure"></a>Considere a rede entre seu local e o Azure

Independentemente de aproveitar os recursos de nuvem para executar produção, teste e desenvolvimento, ou como destino de backup e site de recuperação, é importante entender suas necessidades de largura de banda para a propagação de backup inicial e para transferências diárias em andamento.

Azure Data Box fornece um meio de transferir sua linha de base de backup inicial para o Azure sem exigir largura de banda adicional se a transferência de linha de base for estimada para levar mais tempo do que você pode tolerar. Você pode aproveitar o Transferência de Dados estimador ao criar uma conta de armazenamento para estimar o tempo necessário para transferir o backup inicial.

![Estimador de Transferência de Dados de armazenamento do Azure](../media/az-storage-transfer.png)

Lembre-se de que você precisará de capacidade de rede suficiente para dar suporte a transferências de dados diárias na janela de transferência necessária (janela de backup) sem afetar os aplicativos de produção. Esta seção descreverá as ferramentas e as técnicas disponíveis para avaliar suas necessidades de rede.

#### <a name="how-can-you-determine-how-much-bandwidth-you-will-need"></a>Como você pode determinar Quanta largura de banda será necessária?

Várias opções de avaliação estão disponíveis para determinar a taxa de alteração e o tamanho total do conjunto de backup para a transferência de linha de base inicial para o Azure. Aqui estão alguns exemplos de ferramentas de avaliação e relatório como:
  - [MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
  - [Datavoss](https://www.datavoss.com/)

#### <a name="how-will-i-know-how-much-headroom-i-have-with-my-current-internet-connection"></a>Como posso saber quanto tempo de sobra tenho com minha conexão com a Internet atual?

É importante saber quanto tempo de indisponibilidade ou normalmente não utilizado, largura de banda que você disponibilizou diariamente. Isso permitirá que você avalie corretamente se você puder atender às suas metas de tempo inicial para carregar, quando não estiver usando Azure Data Box para propagação offline e para concluir backups diários com base na taxa de alteração identificada acima e na janela de backup. Abaixo estão os métodos que você pode usar para identificar a reserva de largura de banda que os backups para o Azure estão livres para consumir.

- Você é um cliente existente do Azure ExpressRoute? Exiba o [uso do circuito](https://docs.microsoft.com/azure/expressroute/expressroute-monitoring-metrics-alerts#circuits-metrics) no portal do Azure.
- Você pode entrar em contato com seu ISP. Eles devem ter relatórios para compartilhar com você ilustrando sua utilização diária e mensal existente.
- Há várias ferramentas que podem medir a utilização monitorando o tráfego de rede em seu nível de roteador/comutador, incluindo:
  - [Pacote do analisador de largura de banda SolarWinds](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Assistente de rede Cisco](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choosing-the-right-storage-options"></a>Escolhendo as opções de armazenamento corretas

Ao usar o Azure como um destino de backup, os clientes fazem uso do [armazenamento de BLOBs do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)\. O armazenamento de BLOBs do Azure é a solução de armazenamento de objetos da Microsoft. O armazenamento de BLOBs é otimizado para armazenar grandes quantidades de dados não estruturados, que são dados que não aderem a nenhum modelo de dados ou definição. Além disso, o armazenamento do Azure é durável, altamente disponível, seguro e escalonável. A plataforma da Microsoft oferece flexibilidade para selecionar o armazenamento certo para a carga de trabalho correta a fim de fornecer o [nível de resiliência](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=/azure/storage/blobs/toc.json) para atender aos seus SLAs internos. O armazenamento de BLOBs é um serviço de pagamento por uso. Você é [cobrado mensalmente](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal#pricing-and-billing) pela quantidade de dados armazenados, acessando esses dados e, no caso de camadas frias e de arquivo morto, um período de retenção mínimo necessário. As opções de resiliência e de camadas aplicáveis aos dados de backup são resumidas nas tabelas a seguir.

**Opções de resiliência do armazenamento de BLOBs do Azure:**

|  |Redundância Local  |Com redundância de zona  |Geograficamente redundante  |Com redundância de zona geográfica  |
|---------|---------|---------|---------|---------|
|N º efetivo de cópias     | 3         | 3         | 6         | 6 |
|n º de Zonas de Disponibilidade     | 1         | 3         | 2         | 4 |
|n º de regiões     | 1         | 1         | 2         | 2 |
|Failover manual para a região secundária     | NA         | NA         | Sim         | Sim |

**Camadas de armazenamento de BLOBs do Azure:**

|  | Camada quente   |Camada fria   | Camada de arquivo |
| ----------- | ----------- | -----------  | -----------  |
| Disponibilidade | 99,9%         | 99%         | Offline      |
| Encargos de Uso | Custos de armazenamento mais altos, acesso menor e custos de transações | Custos de armazenamento menores, custos de acesso e de transações maiores | Custos de armazenamento mais baixos, custos de acesso e de transações mais altos |
| Retenção mínima de dados necessária | NA | 30 dias | 180 dias |
| Latência (tempo até o primeiro byte) | Milissegundos | Milissegundos | Horas |

#### <a name="sample-backup-to-azure-cost-model"></a>Exemplo de backup para o modelo de custo do Azure

O conceito de pagamento por uso pode ser assustador para clientes que são novos na nuvem pública. Enquanto você paga apenas a capacidade usada, você também paga por transações (leitura e gravação) e [saída de dados de](https://azure.microsoft.com/pricing/details/bandwidth/) leitura para o seu ambiente local quando o plano de dados de rota expressa [ou local direto do Azure Route](https://azure.microsoft.com/pricing/details/expressroute/) está em uso onde a saída de dados do Azure está incluída. Você pode executar a análise e se com base no preço da lista ou com o [preço de capacidade reservada do armazenamento do Azure](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations), que pode fornecer até 38% de economia, na [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/). Aqui está um exemplo de exercício de preços para modelar o custo mensal de backup no Azure, este é apenas um exemplo e ***seus preços podem variar devido a atividades não capturadas aqui:***


|Fator de custo  |Custo mensal  |
|---------|---------|
|100 TB de dados de backup no armazenamento frio     |$1556.48         |
|2 TB de novos dados gravados por dia x 30 dias     |$72 em transações          |
|Total estimado mensal     |$1628.48         |
|---------|---------|
|Restauração uma vez de 5 TB para local pela Internet pública   | $527.26         |

> [!Note]
Essa estimativa foi gerada na calculadora de preços do Azure usando o preço pago conforme o uso do leste dos EUA e é baseada no padrão Veeam de tamanho da parte de 256KB para transferências de WAN. Este exemplo pode não ser aplicável em relação às suas necessidades.

## <a name="implementation-and-operational-guidance"></a>Diretrizes operacionais e de implementação

Esta seção fornece um breve guia para adicionar o armazenamento do Azure a uma implantação de Veeam local. Se você estiver interessado em considerações detalhadas de orientação e planejamento, recomendamos revisar o [Guia de backup do Veeam Cloud Connect](https://helpcenter.veeam.com/docs/backup/cloud/cloud_backup.html?ver=100).

1. Abra o portal do Azure e procure "contas de armazenamento" ou clique no ícone serviços padrão.

      ![Portal do Azure](../media/azure-portal.png)

      ![Contas de armazenamento no portal do Azure](../media/locate-storage-account.png)

2. Escolha Adicionar uma conta e selecione ou crie um grupo de recursos, forneça um nome exclusivo, escolha a região, selecione desempenho "padrão", sempre deixe o tipo de conta como "armazenamento v2", escolha o nível de replicação, que atende aos seus SLAs e a camada padrão que o software de backup utilizará. Uma conta de armazenamento do Azure disponibiliza camadas quentes, frias e de arquivo em uma única conta e as políticas de Veeam permitem que você utilize várias camadas para gerenciar com eficiência o ciclo de vida dos seus dados. Vá para a próxima etapa. 
    
      ![Criar uma conta de armazenamento](../media/account-create-1.png)

3. Fique com as opções de rede padrão por enquanto e passe para "proteção de dados". Aqui, você pode optar por habilitar a "exclusão reversível", que permite recuperar um arquivo de backup excluído acidentalmente dentro do período de retenção definido e oferece proteção contra exclusão acidental ou mal-intencionada. 
    ![Criando uma conta de armazenamento parte 2](../media/account-create-2.png)

4. Em seguida, recomendamos as configurações padrão da tela "avançado" para backup em casos de uso do Azure.

    ![Criando uma conta de armazenamento parte 3](../media/account-create-3.png) 

5. Adicione marcas para organização se você aproveitar a marcação e criar sua conta. Agora você tem petabytes de armazenamento sob demanda à sua disposição!

6. Duas etapas rápidas são todas agora necessárias para que você possa adicionar a conta ao seu ambiente Veeam. Navegue até a conta que você criou no portal do Azure e selecione "contêineres" no menu "serviço blob" na folha do Portal. Adicione um novo contêiner e escolha um nome significativo. Em seguida, navegue até o item "chaves de acesso" em "configurações" e copie o "nome da conta de armazenamento" e uma das duas chaves de acesso. Será necessário o nome do contêiner, o nome da conta e a chave de acesso em nossas próximas etapas.

    ![Criando um contêiner](../media/container-b.png)
    
    ![Pegue as informações da conta](../media/access-key.png)
    
    > [!Note]
O backup e a replicação do Veeam oferecem opções adicionais para se conectar ao Azure. Para o caso de uso deste artigo, aproveitando Microsoft Azure armazenamento de BLOBs como um destino de backup, usar o método acima é a prática recomendada.

7. ***(Opcional)*** Você pode adicionar mais camadas de segurança à sua implantação.

     1. Configure o acesso baseado em função para limitar quem pode fazer alterações em sua conta de armazenamento. [Saiba mais aqui](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider?toc=/azure/storage/blobs/toc.json)

    1. Restrinja o acesso à conta para segmentos de rede específicos com o [Firewall de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal) para evitar tentativas de acesso de fora da rede corporativa.

    ![Firewall de armazenamento](../media/storage-firewall.png) 

    1. Defina um [bloqueio de exclusão](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) na conta para impedir a exclusão acidental da conta de armazenamento.

    ![Bloqueio de recurso ](../media/resource-lock.png) 1.) Configure [as práticas recomendadas de segurança](https://docs.microsoft.com/azure/storage/blobs/security-recommendations)adicionais. 
8. No console de gerenciamento de backup e replicação do Veaam, navegue até "infraestrutura de backup"--> clique com o botão direito do mouse no painel Visão geral e selecione "Adicionar repositório de backup" para abrir o assistente de configuração. Na caixa de diálogo, selecione armazenamento de objeto--> Microsoft Azure armazenamento de BLOBs--> armazenamento de BLOBs do Azure.
    
    ![Tela do assistente do repositório Veeam](../media/veeam-repo-a.png)

    ![Tela do assistente do repositório Veeam b](../media/veeam-repo-b.png)

    ![Tela do assistente do repositório Veeam c](../media/veeam-repo-c.png)

9. Em seguida, especifique um nome e uma descrição do seu novo repositório de blob Microsoft Azure.
    
    ![Tela do assistente do repositório Veeam d](../media/veeam-repo-d.png)

10. Na próxima etapa, adicione as credenciais para acessar sua conta de armazenamento do Azure. Selecione "conta de Armazenamento do Microsoft Azure" no Gerenciador de credenciais de nuvem, insira o nome da conta de armazenamento e a chave de acesso. Selecione global do Azure no seletor de região e qualquer servidor gateway, se aplicável.
    
    ![Tela do assistente do repositório Veeam e](../media/veeam-repo-e.png)

> [!Note]
Se você optar por não usar um servidor de gateway Veeam, certifique-se de que todas as extensões de repositório de expansão tenham acesso direto à Internet.

11. No registro de contêiner, selecione o contêiner de armazenamento do Azure e selecione ou crie uma pasta na qual armazenar os backups. Você também pode definir um limite flexível na capacidade de armazenamento geral a ser usada pelo Veeam (recomendado). Examine as informações exibidas na seção Resumo e conclua a ferramenta de configuração. O novo repositório pode ser selecionado em sua configuração de trabalho de backup.

    ![Tela do assistente do repositório Veeam](../media/veeam-repo-f.png)
    
    ![Tela do assistente do repositório Veeam g](../media/veeam-repo-g.png)

### <a name="azure-alerting-and-performance-monitoring"></a>Alertas do Azure e monitoramento de desempenho

É aconselhável monitorar os recursos do Azure e a capacidade do Veeam de aproveitá-los como você faria com qualquer destino de armazenamento em que você depende para armazenar seus backups. Uma combinação dos recursos de monitoramento do Azure Monitor e do Veeam (ou seja, a guia Estatísticas no nó trabalhos do console de gerenciamento do Veeam ou opções mais avançadas, como Veeam um reporter), ajudará você a manter seu ambiente íntegro.

#### <a name="microsoft-azure-portal"></a>Portal do Microsoft Azure

Microsoft Azure fornece uma solução de monitoramento robusta na forma de [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource). Você pode [configurar Azure monitor](https://docs.microsoft.com/azure/storage/common/monitor-storage?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-powershell#configuration) para controlar a capacidade, as transações, a disponibilidade, a autenticação e muito mais do armazenamento do Azure. A referência completa das métricas acompanhadas pode ser encontrada [aqui](https://docs.microsoft.com/azure/storage/common/monitor-storage-reference). Algumas métricas úteis a serem controladas são capacidade-para garantir que você permaneça abaixo do limite máximo de [capacidade da conta de armazenamento](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account), entrada e saída – para acompanhar a quantidade de dados que estão sendo gravados e lidos de sua conta de armazenamento do Azure e SuccessE2ELatency-para controlar o tempo de ida e volta das solicitações de e para o armazenamento do Azure e o MediaAgent. 

Você também pode [criar alertas de log](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) para acompanhar a integridade do serviço de armazenamento do Azure e exibir o [painel de status do Azure](https://status.azure.com/status) a qualquer momento.

#### <a name="veeam-reporting"></a>Relatórios do Veeam

[Configurando o Veeam um relatório](https://helpcenter.veeam.com/docs/one/reporter/configure_reporter.html?ver=100)

[Veeam Backup e replicação de alarmes](https://helpcenter.veeam.com/docs/one/monitor/backup_alarms.html?ver=100) 

### <a name="how-to-open-support-cases"></a>Como abrir casos de suporte

Quando você precisar de assistência com o backup para a solução do Azure, é recomendável abrir um caso com o Veeam e o Azure para que nossas organizações de suporte possam se envolver de forma colaborativa, se necessário.

#### <a name="how-to-open-a-case-with-veeam"></a>Como abrir um caso com o Veeam

Navegue até o [portal de suporte ao cliente do Veeam](https://www.veeam.com/support.html), entre e abra um caso.

Se você precisar entender as opções de suporte disponíveis por Veeam, consulte política de [suporte ao cliente do Veeam](https://www.veeam.com/veeam_software_support_policy_ds.pdf)

Você também pode chamar em para abrir um caso:

[Números de suporte em todo o mundo](https://www.veeam.com/contacts.html?ad=in-text-link#support-numbers)

#### <a name="how-to-open-a-case-with-the-azure-support-team"></a>Como abrir um caso com a equipe de suporte do Azure

No [portal do Azure](https://portal.azure.com) procure "suporte" na barra de pesquisa na parte superior do portal e escolha "+ nova solicitação de suporte" 
> [!Note]
Ao abrir um caso, seja específico que você precise de assistência com "armazenamento do Azure" ou "rede do Azure" e **não** "backup do Azure". O backup do Azure é um serviço nativo Microsoft Azure e seu caso será roteado incorretamente.

### <a name="links-to-relevant-veeam-documentation"></a>Links para a documentação relevante do Veeam

Documentação do Veeam fornecendo mais detalhes:

[Guia do usuário do Veeam](https://helpcenter.veeam.com/docs/backup/hyperv/overview.html?ver=100)

[Guia de arquitetura do Veeam](https://helpcenter.veeam.com/docs/backup/vsphere/backup_architecture.html?ver=100)

### <a name="link-to-marketplace-offering"></a>Vincular à oferta do Marketplace

Você também pode continuar a usar a solução Veeam que você conhece e confia para proteger suas cargas de trabalho em execução no Azure. O Veeam tornou mais fácil implantar sua solução no Azure e proteger máquinas virtuais do Azure e muitos outros serviços do Azure.

[Implantar Veeam B&R por meio do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/veeam.veeam-backup-replication?tab=overview)

[Folha de tabela do Azure](https://www.veeam.com/backup-azure.html?ad=menu-products)


## <a name="next-steps"></a>Próximas etapas

Explore recursos adicionais nesses sites externos para obter informações sobre cenários de uso especializado:

[Vídeos de Veeam](https://www.veeam.com/how-to-videos.html?ad=menu-resources)

[Documentações técnicas do Veeam](https://www.veeam.com/documentation-guides-datasheets.html?ad=menu-resources)

[Veeam base de dados de conhecimento e perguntas frequentes](https://www.veeam.com/knowledge-base.html?ad=menu-resources)
