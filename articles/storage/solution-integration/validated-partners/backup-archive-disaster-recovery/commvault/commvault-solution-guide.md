---
title: Fazer backup dos dados no Azure com o CommVault
titleSuffix: Azure Blob Storage Docs
description: A página da Web fornece uma visão geral dos fatores a serem considerados e das etapas a serem seguidas para aproveitar o Azure como um destino de armazenamento e um local de recuperação para backup e recuperação completos do CommVault
keywords: CommVault, backup em nuvem, backup, backup no Azure, recuperação de desastres, continuidade dos negócios
author: karauten
ms.author: karauten
ms.date: 11/11/2020
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.openlocfilehash: bc5bcca394fa66cea9cbf6bc20ac7d164c671cf7
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632944"
---
# <a name="back-up-to-azure-with-commvault"></a>Fazer backup para o Azure com CommVault

Este artigo fornece um guia para a integração de uma infraestrutura do CommVault ao armazenamento de BLOBs do Azure. Ele inclui pré-requisitos, princípios de armazenamento do Azure, implementação e orientação operacional. Este artigo aborda apenas o uso do Azure como um destino de backup externo e um site de recuperação em caso de desastre, o que impede a operação normal no site primário. O CommVault também oferece uma solução de RTO mais baixa, o CommVault Live Sync, como um meio de ter uma VM em espera pronta para inicializar e recuperar mais rapidamente em caso de desastre e proteção de recursos em um ambiente de produção do Azure. Esses recursos estão fora do escopo deste documento. 

## <a name="reference-architecture-for-on-premises-to-azure-and-in-azure-deployments"></a>Arquitetura de referência para implantações locais para o Azure e In-Azure

![Arquitetura de referência do CommVault para o Azure](../media/commvault-diagram.png)

Sua implantação existente do CommVault pode integrar-se facilmente ao Azure adicionando uma conta de armazenamento do Azure ou várias contas, como um destino de armazenamento em nuvem. O CommVault também permite que você recupere backups locais no Azure, fornecendo um site de recuperação sob demanda no Azure.   

## <a name="commvault-interoperability-matrix"></a>Matriz de interoperabilidade do CommVault
| Carga de trabalho | GPv2 e armazenamento de BLOBs | Suporte de camada fria | Suporte à camada de arquivo | Suporte da família Data Box |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| VMs/dados locais | v 11,5 | v 11,5 | v 11.10 | v 11.10 |
| VMs do Azure | v 11,5 | v 11,5 | v 11,5 | NA |
| Blob do Azure | v 11.6 | v 11.6 | v 11.6 | NA |
| Arquivos do Azure | v 11.6 | v 11.6 | v 11.6 | NA | 

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

-  Relatórios de seu software de backup. 
  O CommVault fornece relatórios padrão para determinar a [taxa de alteração](https://documentation.commvault.com/commvault/v11_sp19/article?p=39699.htm) e o [Tamanho total do conjunto de backup](https://documentation.commvault.com/commvault/v11_sp19/article?p=39621.htm) para a transferência de linha de base inicial para o Azure.
- Ferramentas de relatório e avaliação independentes de software de backup como:
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
|2 TB de novos dados gravados por dia x 30 dias     |$39 em transações          |
|Total estimado mensal     |$1595.48         |
|---------|---------|
|Restauração uma vez de 5 TB para local pela Internet pública   | $491.26         |


> [!Note] 
Essa estimativa foi gerada na calculadora de preços do Azure usando o preço pago conforme o uso do leste dos EUA e baseia-se no padrão CommVault do tamanho da subparte de 32MB, que gera 65.536 solicitações PUT, também conhecidas como transações de gravação, por dia. Este exemplo pode não ser aplicável em relação às suas necessidades.

## <a name="implementation-and-operational-guidance"></a>Diretrizes operacionais e de implementação

Esta seção fornece um breve guia para adicionar o armazenamento do Azure a uma implantação local do CommVault. Se você estiver interessado em considerações detalhadas de orientação e planejamento, recomendamos revisar o [Guia de arquitetura do CommVault Azure](https://www.commvault.com/resources/public-cloud-architecture-guide-for-microsoft-azure-v11-sp16).

1. Abra o portal do Azure e procure "contas de armazenamento" ou clique no ícone serviços padrão.
    
    1. ![Portal do Azure](../media/azure-portal.png)
  
    1. ![Contas de armazenamento no portal do Azure](../media/locate-storage-account.png)

2. Escolha Adicionar uma conta e selecione ou crie um grupo de recursos, forneça um nome exclusivo, escolha a região, selecione desempenho "padrão", sempre deixe o tipo de conta como "armazenamento v2", escolha o nível de replicação, que atende aos seus SLAs e a camada padrão que o software de backup utilizará. Uma conta de armazenamento do Azure disponibiliza camadas quentes, frias e de arquivo em uma única conta e as políticas do CommVault permitem aproveitar várias camadas para gerenciar com eficiência o ciclo de vida de seus dados. Vá para a próxima etapa. 

    ![Criar uma conta de armazenamento](../media/account-create-1.png)

3. Fique com as opções de rede padrão por enquanto e passe para "proteção de dados". Aqui, você pode optar por habilitar a "exclusão reversível", que permite recuperar um arquivo de backup excluído acidentalmente dentro do período de retenção definido e oferece proteção contra exclusão acidental ou mal-intencionada. 
    
    ![Criando uma conta de armazenamento parte 2](../media/account-create-2.png)

4. Em seguida, recomendamos as configurações padrão da tela "avançado" para backup em casos de uso do Azure.

    ![Criando uma conta de armazenamento parte 3](../media/account-create-3.png) 

5. Adicione marcas para organização se você aproveitar a marcação e criar sua conta. Agora você tem petabytes de armazenamento sob demanda à sua disposição!

6. Duas etapas rápidas são todas agora necessárias para que você possa adicionar a conta ao seu ambiente CommVault. Navegue até a conta que você criou na portal do Azure e selecione "contêineres" no menu "serviço blob" na folha do Portal. Adicione um novo contêiner e escolha um nome significativo. Em seguida, navegue até o item "chaves de acesso" em "configurações" e copie o "nome da conta de armazenamento" e uma das duas chaves de acesso. Será necessário o nome do contêiner, o nome da conta e a chave de acesso em nossas próximas etapas.
    
    ![Criando um contêiner](../media/container.png)
    
    ![Pegue as informações da conta](../media/access-key.png)

7. ***(Opcional)*** Você pode adicionar mais camadas de segurança à sua implantação.
    
    1. Configure o acesso baseado em função para limitar quem pode fazer alterações em sua conta de armazenamento. [Saiba mais aqui](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider?toc=/azure/storage/blobs/toc.json)
    1. Restrinja o acesso à conta para segmentos de rede específicos com o [Firewall de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal) para evitar tentativas de acesso de fora da rede corporativa.

    ![Firewall de armazenamento](../media/storage-firewall.png) 

    1. Defina um [bloqueio de exclusão](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) na conta para impedir a exclusão acidental da conta de armazenamento.

    ![Bloqueio de recurso](../media/resource-lock.png)
    
    1. Configure [as práticas recomendadas de segurança](https://docs.microsoft.com/azure/storage/blobs/security-recommendations)adicionais.
    
1. No centro de comando do CommVault, navegue até "gerenciar"--> "segurança"--> "Gerenciador de credenciais". Escolha uma "conta de nuvem", "tipo de fornecedor" de Armazenamento do Microsoft Azure, selecione o "MediaAgent", que transferirá dados de e para o Azure, adicione o nome da conta de armazenamento e a chave de acesso.
    
    ![Credencial do CommVault](../media/commvault-credential.png)

9. Em seguida, navegue para "Storage"--> "Cloud" no centro de comando do CommVault. Escolha "Adicionar". Insira um nome amigável para a conta de armazenamento e, em seguida, selecione "Armazenamento do Microsoft Azure" na lista "tipo". Selecione um servidor de agente de mídia a ser usado para transferir backups para o armazenamento do Azure. Adicione o contêiner que você criou, escolha a camada de armazenamento para aproveitar na conta de armazenamento do Azure e selecione as credenciais criadas na etapa #8. Por fim, escolha se deseja ou não transferir backups com eliminação de duplicação ou não e um local para o banco de dados de eliminação de duplicação.
    
     ![Captura de tela da interface Adicionar usuário de nuvem. No menu suspenso arquivo morto, * * arquivo * * está selecionado.](../media/commvault-add-storage.png)

10. Por fim, adicione seu novo recurso de armazenamento do Azure a um plano novo ou existente no centro de comando do CommVault por meio de "gerenciar"--> "planos" como um "destino de backup".

    ![Captura de tela da interface do usuário do COMMVAULT Command Center. No painel de navegação esquerdo, em * * gerenciar * *, * * planos * * está selecionado.](../media/commvault-plan.png)

11. ***(Opcional)*** Se você planeja aproveitar o Azure como um site de recuperação ou o CommVault para migrar servidores e aplicativos para o Azure, é uma prática recomendada implantar um proxy de VSA no Azure. Você pode encontrar instruções detalhadas [aqui](https://documentation.commvault.com/commvault/v11/article?p=106208.htm).  

### <a name="azure-alerting-and-performance-monitoring"></a>Alertas do Azure e monitoramento de desempenho

É aconselhável monitorar os recursos do Azure e a capacidade do CommVault de aproveitá-los como você faria com qualquer destino de armazenamento em que você dependa para armazenar seus backups. Uma combinação de Azure Monitor e monitoramento do centro de comando CommVault ajudará você a manter seu ambiente íntegro.

#### <a name="microsoft-azure-portal"></a>Portal do Microsoft Azure

Microsoft Azure fornece uma solução de monitoramento robusta na forma de [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource). Você pode [configurar Azure monitor](https://docs.microsoft.com/azure/storage/common/monitor-storage?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-powershell#configuration) para controlar a capacidade, as transações, a disponibilidade, a autenticação e muito mais do armazenamento do Azure. A referência completa das métricas acompanhadas pode ser encontrada [aqui](https://docs.microsoft.com/azure/storage/common/monitor-storage-reference). Algumas métricas úteis a serem controladas são capacidade-para garantir que você permaneça abaixo do limite máximo de [capacidade da conta de armazenamento](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account), entrada e saída – para acompanhar a quantidade de dados que estão sendo gravados e lidos de sua conta de armazenamento do Azure e SuccessE2ELatency-para acompanhar o tempo de ida e volta das solicitações de e para o armazenamento do Azure e o MediaAgent. 

Você também pode [criar alertas de log](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) para acompanhar a integridade do serviço de armazenamento do Azure e exibir o [painel de status do Azure](https://status.azure.com/status) a qualquer momento.

#### <a name="commvault-command-center"></a>Centro de comando do CommVault

[Criando alertas para pools de armazenamento em nuvem](https://documentation.commvault.com/commvault/v11/article?p=100514_3.htm)

[Onde os clientes podem ir para exibir relatórios de desempenho, conclusão do trabalho e iniciar a solução básica de problemas](https://documentation.commvault.com/commvault/v11/article?p=95306_1.htm) 

### <a name="how-to-open-support-cases"></a>Como abrir casos de suporte

Quando você precisar de assistência com o backup para a solução do Azure, é recomendável abrir um caso com o CommVault e o Azure para que nossas organizações de suporte possam se envolver de forma colaborativa, se necessário.

#### <a name="how-to-open-a-case-with-commvault"></a>Como abrir um caso com o CommVault

Navegue até o [site de suporte do CommVault](https://www.commvault.com/support), entre e abra um caso.

Se você precisar entender as opções de contrato de suporte disponíveis para você, consulte [Opções de suporte do CommVault](https://ma.commvault.com/support)

Você também pode chamar em para abrir um caso ou acessar o suporte do CommVault por email:

Ligação gratuita: + 1 877-780-3077

[Números de suporte em todo o mundo](https://ma.commvault.com/Support/TelephoneSupport)

[Email de suporte do CommVault](mailto:support@commvault.com)

#### <a name="how-to-open-a-case-with-the-azure-support-team"></a>Como abrir um caso com a equipe de suporte do Azure

No [portal do Azure](https://portal.azure.com) procure "suporte" na barra de pesquisa na parte superior do portal e escolha "+ nova solicitação de suporte" 
> [!Note]
Ao abrir um caso, seja específico que você precise de assistência com "armazenamento do Azure" ou "rede do Azure" e **não** "backup do Azure". O backup do Azure é um serviço nativo Microsoft Azure e seu caso será roteado incorretamente.

### <a name="links-to-relevant-commvault-documentation"></a>Links para a documentação relevante do CommVault

Documentação do CommVault fornecendo mais detalhes:

[Guia do usuário do CommVault](https://documentation.commvault.com/commvault/v11/article?p=37684_1.htm)

[Guia de arquitetura do CommVault Azure](https://www.commvault.com/resources/public-cloud-architecture-guide-for-microsoft-azure-v11-sp16) 

### <a name="link-to-marketplace-offering"></a>Vincular à oferta do Marketplace

Você também pode continuar a usar a solução CommVault que você conhece e confia para proteger suas cargas de trabalho em execução no Azure. A CommVault facilitou a implantação de sua solução no Azure e a proteção de máquinas virtuais do Azure e de muitos outros serviços do Azure.

[Implantar o CommVault por meio do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault?tab=Overview)

[Folha de tabela do Azure](https://www.commvault.com/resources/microsoft-azure-cloud-platform-datasheet)

[Lista abrangente de recursos e serviços do Azure com suporte](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)

[Como usar o CommVault para proteger SAP HANA no Azure](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)

## <a name="next-steps"></a>Próximas etapas

Explore recursos adicionais nesses sites externos para obter informações sobre cenários de uso especializado:

[Use o CommVault para migrar seus servidores e aplicativos para o Azure](https://www.commvault.com/resources/demonstration-vmware-to-azure-migrations-with-commvault)

[Proteger o SAP no Azure com o CommVault](https://www.youtube.com/watch?v=4ZGGE53mGVI)

[Proteger o Office365 com o CommVault](https://www.youtube.com/watch?v=dl3nvAacxZU)
