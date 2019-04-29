---
title: Melhores práticas para estimar os custos e dimensionar as cargas de trabalho migradas para o Azure | Microsoft Docs
description: Obtenha as melhores práticas para estimativa de custos e dimensionamento das cargas de trabalho migradas para o Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: raynew
ms.openlocfilehash: 6f6440e12840538614b4092b173ab25ae37a68a6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60790570"
---
# <a name="best-practices-for-costing-and-sizing-workloads-migrated-to-azure"></a>Melhores práticas para estimar os custos e dimensionar as cargas de trabalho migradas para o Azure

Conforme você planeja e projeta a migração, a concentração nos custos garante o sucesso a longo prazo de sua migração para o Azure. Durante um projeto de migração, é essencial que todas as equipes (finanças, gerenciamento, equipes de aplicativo, etc.) entendam os custos associados.
- Antes da migração, a estimativa dos gastos com a migração, com uma linha de base para metas de orçamento mensais, trimestrais e anuais, é essencial para o sucesso.
- Após a migração, você deverá otimizar os custos, monitorar continuamente as cargas de trabalho e planejar os padrões de uso futuros. Os recursos migrados podem começar como um tipo de carga de trabalho, mas evoluem para outro tipo, de acordo com os requisitos em constante mudança de uso, de custos e dos negócios.

Este artigo descreve as melhores práticas de estimativa de custo e dimensionamento antes e após a migração.  

> [!IMPORTANT]
> As melhores práticas e as opiniões descritas neste artigo baseiam-se na plataforma Azure e nos recursos do serviço disponíveis no momento em que o artigo foi escrito. Os recursos e as funcionalidades mudam ao longo do tempo. Nem todas as recomendações poderão ser aplicáveis à sua implantação. Portanto, selecione o que funciona para você.


## <a name="before-migration"></a>Antes da migração 

Antes de mover suas cargas de trabalho para a nuvem, estime o custo mensal de executá-las no Azure. O gerenciamento proativo dos custos com a nuvem ajuda você a respeitar o orçamento do OpEx (despesas operacionais). Se o orçamento é limitado, leve isso em conta antes da migração.  Considere a conversão de cargas de trabalho para tecnologias sem servidor do Azure, quando apropriado, para reduzir os custos.

As melhores práticas descritas nesta seção ajudam você a estimar os custos, fazer o redimensionamento ideal das VMs e do armazenamento, aproveitar o Benefício Híbrido do Azure, usar VMs reservadas e estimar os gastos com a nuvem nas assinaturas.



## <a name="best-practice-estimate-monthly-workload-costs"></a>Melhor prática: Estimar os custos da carga de trabalho mensalmente
 
Para fazer uma previsão de sua fatura mensal para as cargas de trabalho migradas, há várias ferramentas que você pode usar.

- **Calculadora de Preços do Azure**: Selecione os produtos que deseja estimar, por exemplo, VMs e armazenamento. Insira os custos na calculadora de preços para gerar uma estimativa.

  ![Calculadora de Preços do Azure](./media/migrate-best-practices-costs/pricing.png) *Calculadora de Preços do Azure*

- **Migrações para Azure**: Para estimar os custos, você precisa examinar e levar em conta todos os recursos necessários para executar suas cargas de trabalho no Azure. Para obter esses dados, crie um inventário de seus ativos, incluindo servidores, VMs, bancos de dados e armazenamento. Use as Migrações para Azure para coletar essas informações.

  - As Migrações para Azure descobrem e avaliam seu ambiente local para fornecer um inventário.
  - As Migrações para Azure podem mapear e mostrar as dependências entre as VMs para que você tenha um panorama completo.
  - Uma avaliação das Migrações para Azure contém o custo estimado.
    - Custos de computação: Usando o tamanho recomendado de VM do Azure quando você cria uma avaliação, as Migrações para Azure usam a API de Cobrança para calcular os custos mensais estimados de VM. A estimativa considera o sistema operacional, o Software Assurance, as instâncias reservadas, o tempo de atividade da VM, a localização e as configurações de moeda. Ela agrega o custo em todas as VMs na avaliação e calcula um custo mensal total de computação.
    - Custo de armazenamento: As Migrações para Azure calculam os custos totais de armazenamento mensal agregando os custos de armazenamento de todas as VMs em uma avaliação. Você pode calcular o custo mensal de armazenamento para um computador específico agregando o custo mensal de todos os discos anexados a ele. 

    ![As migrações para Azure](./media/migrate-best-practices-costs/assess.png)
    *avaliação de migrações para Azure*

**Saiba mais:**
- [Use](https://azure.microsoft.com/pricing/calculator/) a Calculadora de Preços do Azure.
- [Obtenha uma visão geral](https://docs.microsoft.com/azure/migrate/migrate-overview) das Migrações para Azure.
- [Leia mais sobre](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation) as avaliações das Migrações para Azure.
- [Saiba mais](https://docs.microsoft.com/azure/dms/dms-overview) sobre o DMS (Serviço de Migração de Banco de Dados).

## <a name="best-practice-right-size-vms"></a>Melhor prática: Dimensionar as VMs

Escolha uma entre uma variedade de opções ao implantar VMs do Azure para dar suporte às cargas de trabalho. Cada tipo de VM tem recursos específicos e diferentes combinações de CPU, memória e discos. As VMs são agrupadas conforme indicado a seguir.

**Tipo** | **Detalhes** | **Uso**
--- | --- | ---
**Propósito geral** | CPU/memória equilibrados. | Boa para teste e desenvolvimento, bancos de dados pequenos a médios e servidores Web de tráfego baixo a médio.
**Com otimização de computação** | Relação de CPU/memória alta. | Boa para servidores Web de tráfego médio, dispositivos de rede, processos de lote e servidores de aplicativos.
**Com otimização de memória** | Alta relação de memória/CPU. | Boa para banco de dados relacionais, caches médio a grande e análise in-memory.
**Armazenamento otimizado** | Alta taxa de transferência de disco e de E/S. | Boa para Big Data, bancos de dados SQL e NoSQL.
**GPU otimizada** | VMs especializadas. GPUs únicas ou múltiplas. | Elementos gráficos pesados e edição de vídeo.
**Alto desempenho** | CPU mais rápida e eficiente. VMs com adaptadores de rede de alta produtividade (RDMA) opcionais | Aplicativos de alto desempenho críticos.

- É importante entender as diferenças de preços entre essas VMs e os efeitos de orçamento de longo prazo.
- Cada tipo tem um número de série de VM.
- Além disso, quando você seleciona uma VM em uma série, pode apenas escalar ou reduzir verticalmente a VM dentro dessa série. Por exemplo, uma DSv2\_2 pode ser escalada verticalmente para DSv2\_4, mas não pode ser alterada para uma série diferente como Fsv2\_2.

**Saiba mais:**
- [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) sobre os tipos e o dimensionamento de VM, além do mapeamento de tamanhos para tipos.
- [Planeje](https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs) o dimensionamento da VM.
- [Examine](https://docs.microsoft.com/azure/migrate/contoso-migration-assessment) uma avaliação de exemplo para a empresa fictícia Contoso.

## <a name="best-practice-select-the-right-storage"></a>Melhor prática: Selecionar o armazenamento ideal

O ajuste e a manutenção do armazenamento local (SAN ou NAS), e as redes usadas para dar suporte a ele, podem ser caros e demorados. Os dados de arquivo (armazenamento) normalmente são migrados para a nuvem para ajudar a aliviar as preocupações operacionais e de gerenciamento. A Microsoft fornece várias opções para movimentação de dados para o Azure e você precisa tomar decisões sobre essas opções. A escolha do tipo de armazenamento ideal para os dados pode economizar vários milhares de dólares todo mês para sua organização. Algumas considerações:

- Os dados que não são muito acessados e não são comercialmente críticos não precisam ser colocados no armazenamento mais caro.
- Por outro lado, os dados importantes e comercialmente críticos devem estar localizados nas opções de armazenamento de camada superior.
- Durante o planejamento da migração, faça um inventário dos dados e classifique-os por importância, a fim de mapeá-los para o armazenamento mais adequado. Considere o orçamento e os custos, bem como o desempenho. O custo não necessariamente deve ser o fator principal da tomada de decisão. A escolha da opção menos cara pode expor a carga de trabalho a riscos de desempenho e disponibilidade. 

### <a name="storage-data-types"></a>Tipos de dados de armazenamento

O Azure fornece diferentes tipos de dados de armazenamento.

| **Tipo de dados** | **Detalhes** | **Uso** |
|--- | --- |  --- |
|**Blobs** | Otimizado para armazenar grandes quantidades de objetos não estruturados, como dados de texto ou binários<br/>Acesse os dados em qualquer lugar via HTTP/HTTPS. | Use para cenários de streaming e acesso aleatório. Por exemplo, para fornecer imagens e documentos diretamente para um navegador, transmitir áudio e vídeo e armazenar dados de backup e recuperação de desastre.|
|**Arquivos** | Compartilhamentos de arquivos gerenciados acessados via SMB 3.0 | Use ao migrar compartilhamentos de arquivos locais e para fornecer várias conexões/acesso aos dados de arquivo.|
|**Discos** | Com base nos blobs de páginas.<br/><br/> Tipo de disco (velocidade): Standard (HD ou SSD) ou Premium (SSD).<br/><br/>Gerenciamento de disco: Não gerenciado (você gerencia as configurações de disco e armazenamento) ou Gerenciado (você seleciona o tipo de disco e o Azure gerencia o disco para você). | Use discos Premium para VMs. Use discos gerenciados para gerenciamento e dimensionamento simples.|
|**Filas** | Armazene e recupere grandes quantidades de mensagens acessadas por meio de chamadas autenticadas (HTTP ou HTTPS) | Conecte componentes de aplicativo com o enfileiramento de mensagens assíncronas.|
|**Tabelas** | Armazene tabelas. | Agora parte da API de Tabela do Azure Cosmos DB.|



### <a name="access-tiers"></a>Níveis de acesso
O Armazenamento do Azure oferece diferentes opções para acessar dados de blob de blocos. A seleção da camada de acesso certa ajuda a garantir que você armazene dados de blob de blocos da maneira mais econômica.

**Tipo** | **Detalhes** | **Uso**
--- | --- | ---
**Frequente** | Custo de armazenamento superior ao Esporádico. Encargos de acesso inferiores ao Esporádico.<br/><br/>Essa é a camada padrão. | Use-a para dados em uso ativo que são acessados com frequência.
**Esporádico** | Custo de armazenamento inferior ao Frequente. Encargos de acesso superiores ao Frequente.<br/><br/> Armazenamento por, no mínimo, 30 dias. | Armazene dados de curto prazo que ficam disponíveis, mas acessados com pouca frequência.
**Arquivar** | Usado para blobs de blocos individuais.<br/><br/> Opção mais econômica de armazenamento. O acesso a dados é mais caro do que o frequente e esporádico. | Use-a para dados que podem tolerar horas de latência de recuperação do servidor e permanecerão na camada por, pelo menos, 180 dias. 

### <a name="storage-account-types"></a>Tipos de conta de armazenamento

O Azure fornece diferentes tipos de contas de armazenamento e níveis de desempenho.

**Tipo de conta** | **Detalhes** | **Uso**
--- | --- | ---
**Uso Geral v2 Standard** | Dá suporte a blobs (blocos, páginas, acréscimo), arquivos, discos, filas e tabelas.<br/><br/> Dá suporte às camadas de acesso Frequente, Esporádico e de Arquivos. Há suporte para o ZRS. | Use-a para a maioria dos cenários e tipos de dados. As contas de armazenamento padrão podem ser baseadas em HDD ou SSD.
**Uso Geral v2 Premium** | Dá suporte a dados do Armazenamento de Blobs (blobs de páginas). Dá suporte às camadas de acesso Frequente, Esporádico e de Arquivos. Há suporte para o ZRS.<br/><br/> Armazenado em SSD. | A Microsoft recomenda seu uso para todas as VMs.
**Uso Geral v1** | Não há suporte a camadas de acesso. Não dá suporte ao ZRS | Use-a se os aplicativos precisarem do modelo de implantação clássico do Azure.
**Blob** | Conta de armazenamento especializada para armazenamento de objetos não estruturados. Fornece somente blobs de blocos e blobs de acréscimo (nenhum serviço de Armazenamento de Arquivos, de Filas, de Tabelas ou em Disco). Fornece a mesma durabilidade, disponibilidade, escalabilidade e desempenho do Uso Geral v2. | Não é possível armazenar blobs de páginas nessas contas e, portanto, você não pode armazenar arquivos VHD. Você pode definir uma camada de acesso Frequente ou Esporádico.

### <a name="storage-redundancy-options"></a>Opções de redundância do armazenamento

As contas de armazenamento podem usar diferentes tipos de redundância para resiliência e alta disponibilidade.

**Tipo** | **Detalhes** | **Uso**
--- | --- | ---
**LRS (Armazenamento com Redundância Local)** | Protege contra uma interrupção local por meio da replicação em uma única unidade de armazenamento para um domínio de falha e um domínio de atualização separados. Mantém várias cópias de seus dados em um datacenter. Fornece durabilidade de, pelo menos, 99,999999999% (11 9\'s) dos objetos em determinado ano. | Considere se o aplicativo armazena dados que possam ser reconstruídos com facilidade.
**ZRS (Armazenamento com Redundância de Zona)** | Protege novamente uma interrupção do datacenter por meio da replicação entre os três clusters de armazenamento em uma única região. Cada cluster de armazenamento é fisicamente separado e localizado em sua própria zona de disponibilidade. Projetado para fornecer durabilidade de, pelo menos, 99,9999999999 % (12 9\'s) de objetos em determinado ano, mantendo várias cópias de seus dados em diversos datacenters ou regiões. | Considere se você precisa de consistência, durabilidade e alta disponibilidade. Pode não proteger contra um desastre regional em que várias zonas são permanentemente afetadas.
**GRS (Armazenamento com Redundância Geográfica)** | Protege contra uma interrupção de toda a região por meio da replicação dos dados para uma região secundária a centenas de quilômetros de distância da primária. Fornece durabilidade de, pelo menos, 99,99999999999999% (16 9\'s) dos objetos em determinado ano. | Os dados de réplica não ficam disponíveis, a menos que a Microsoft inicie um failover para a região secundária. Se ocorrer um failover, o acesso de leitura e gravação ficará disponível.
**RA-GRS (Armazenamento com Redundância Geográfica com Acesso de Leitura)** | Similar ao GRS. Fornece durabilidade de, pelo menos, 99,99999999999999 % (16 9\'s) dos objetos em determinado ano | Fornece uma disponibilidade de leitura de 99,99%, permitindo o acesso de leitura da segunda região usada para GRS.

**Saiba mais:**
- [Examine](https://azure.microsoft.com/pricing/details/storage/) os preços do Armazenamento do Azure.
- [Saiba mais sobre](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) a Importação/Exportação do Azure para migração de grandes quantidades de dados para blobs e arquivos do Azure. 
- [Compare](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) os tipos de dados do armazenamento de blobs, de arquivos e em disco.
- [Saiba mais](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) sobre as camadas de acesso.
- [Examine](https://docs.microsoft.com/azure/storage/common/storage-account-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) diferentes tipos de contas de armazenamento.
- Saiba mais sobre [redundância de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-redundancy), [LRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json), [ZRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json), [GRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) e [GRS com acesso de leitura](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#read-access-geo-redundant-storage).
- [Saiba mais](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) sobre os Arquivos do Azure.



## <a name="best-practice-leverage-azure-hybrid-benefits"></a>Melhor prática: Aproveitar o Benefício Híbrido do Azure

Devido a muitos anos de investimentos de software em sistemas como Windows Server e SQL Server, a Microsoft está em uma posição única para oferecer aos clientes o valor na nuvem, com descontos substanciais que nenhum outro provedor de nuvem necessariamente poderá fornecer. 

Um portfólio integrado de produtos da Microsoft locais/do Azure gera vantagens competitivas e de custo. Caso você tenha um sistema operacional ou outro licenciamento de software por meio do SA (Software Assurance), leve essas licenças com você para a nuvem com o Benefício Híbrido do Azure.

**Saiba mais:**

- [Dê uma olhada](https://azure.microsoft.com/pricing/hybrid-benefit/) na Calculadora de economias do Benefício Híbrido.
- [Saiba mais](https://azure.microsoft.com/pricing/hybrid-benefit/) sobre o Benefício Híbrido para Windows Server.
- [Examine](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) as diretrizes de preços para VMs do Azure do SQL Server.


## <a name="best-practice-use-reserved-vm-instances"></a>Melhor prática: Usar instâncias de VM reservada

A maioria das plataformas de nuvem é configurada como pago conforme o uso. Esse modelo apresenta desvantagens, uma vez que você não sabe necessariamente quão dinâmicas serão as cargas de trabalho. Ao especificar intenções claras para uma carga de trabalho, você contribui com o planejamento da infraestrutura.

Usando Instâncias de VM Reservadas do Azure, você paga antecipadamente por uma instância de VM com prazo de um a três anos. 

- O pagamento antecipado fornece um desconto sobre os recursos usados.
- Você pode reduzir consideravelmente os custos com VM, computação do Banco de Dados SQL, Azure Cosmos DB ou outros recursos em até 72% nos preços pagos conforme o uso. 
- As reservas fornecem um desconto de cobrança e não afetam o estado de tempo de execução dos recursos.
- Você pode cancelar as instâncias reservadas.

![Instâncias reservadas](./media/migrate-best-practices-costs/reserve.png)
*VMs reservadas do Azure*

**Saiba mais:**
- [Saiba mais sobre](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) as Reservas do Azure.
- [Leia](https://azure.microsoft.com/pricing/reserved-vm-instances/#faq) as perguntas frequentes sobre as instâncias reservadas.
- [Obtenha diretrizes de preços](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) para VMs do Azure do SQL Server.


## <a name="best-practice-aggregate-cloud-spend-across-subscriptions"></a>Melhor prática: Agregar os gastos com a nuvem nas assinaturas

É inevitável que você acabe tendo mais de uma assinatura do Azure. Por exemplo, talvez você precise ter uma assinatura adicional para separar os limites de desenvolvimento e produção ou pode ter uma plataforma que exija uma assinatura separada para cada cliente. Um recurso valioso é ter a capacidade de agregar relatórios de dados de todas as assinaturas em uma única plataforma.

Para fazer isso, você pode usar as APIs de Gerenciamento de Custos do Azure. Em seguida, após a agregação de dados em uma única fonte como o SQL do Azure, você poderá usar ferramentas como o Power BI para exibir os dados agregados. Você poderá criar relatórios agregados de assinatura e relatórios granulares. Por exemplo, para usuários que precisam de insights proativos sobre o gerenciamento de custos, você pode criar exibições específicas de custos, com base no departamento, grupo de recursos, etc. Você não precisa fornecer a eles o acesso completo aos dados de cobrança do Azure.

**Saiba mais:**

- [Obtenha uma visão geral](https://docs.microsoft.com/azure/billing/billing-consumption-api-overview) da API de Consumo do Azure.
- [Saiba mais sobre](https://docs.microsoft.com/power-bi/desktop-connect-azure-consumption-insights) como se conectar ao Azure Consumption Insights no Power BI Desktop.
- [Saiba como](https://docs.microsoft.com/azure/billing/billing-manage-access) gerenciar o acesso a informações de cobrança do Azure usando o RBAC (controle de acesso baseado em função).
 
## <a name="after-migration"></a>Após a migração

Depois de uma migração bem-sucedida das cargas de trabalho e algumas semanas de coleta de dados de consumo, você terá uma ideia clara dos custos de recursos.
- Conforme você analisa os dados, pode começar a gerar uma linha de base de orçamento para recursos e grupos de recursos do Azure.
- Em seguida, à medida que você entender em quais itens seu orçamento da nuvem está sendo gasto, poderá analisar como reduzir ainda mais os custos.

As melhores práticas desta seção incluem o uso do Gerenciamento de Custos do Azure para orçamento de custo e análise, monitoramento de recursos implementação de orçamentos de grupo de recursos e otimização de monitoramento, armazenamento e VMs.

## <a name="best-practice-use-azure-cost-management"></a>Melhor prática: Usar o Gerenciamento de Custos do Azure

A Microsoft fornece o Gerenciamento de Custos do Azure para ajudá-lo a acompanhar os gastos, da seguinte maneira:

- Ajuda você a monitorar e controlar os gastos do Azure e otimizar o uso de recursos.
- Examina a assinatura inteira e todos os seus recursos e faz recomendações.
- Fornece uma API completa para integrar ferramentas externas e sistemas financeiros para relatórios.
- Acompanha o uso de recursos e gerencia os custos da nuvem com uma única exibição unificada.
- Fornece insights financeiros e operacionais avançados para ajudá-lo a tomar decisões informadas.

No Gerenciamento de Custos, você pode:


- **Criar um orçamento**: Crie um orçamento para responsabilidade financeira.
  - Você pode considerar os serviços consumidos ou assinar por um período específico (mensal, trimestral, anualmente) e um escopo (assinaturas/grupos de recursos). Por exemplo, você pode criar um orçamento de assinatura do Azure para um período mensal, trimestral ou anual.
  - Depois que você criar um orçamento, ele será mostrado na análise de custo. A exibição de seu orçamento em relação ao gasto atual é uma das primeiras etapas necessárias ao analisar os custos e os gastos.
  - Notificações por email podem ser enviadas quando os limites de orçamento são atingidos.
  - Você pode exportar os dados de gerenciamento de custos para o Armazenamento do Azure para análise.

    ![Orçamento de gerenciamento de custo](./media/migrate-best-practices-costs/budget.png)
    *orçamento de gerenciamento de custos do Azure*

- **Fazer uma análise de custo**: Obtenha uma análise de custo para explorar e analisar os custos organizacionais, a fim de ajudá-lo a entender como os custos são acumulados e identificar as tendências de gastos.
  - A análise de custo está disponível para usuários do EA.
  - Exiba dados de análise de custo para vários escopos, incluindo por departamento, conta, assinatura ou grupo de recursos.
  - Obtenha uma análise de custo que mostra os custos totais para o mês atual e os custos diários acumulados. 

    ![Gerenciamento de análise de custo](./media/migrate-best-practices-costs/analysis.png)
    *análise de gerenciamento de custos do Azure*
- **Obter recomendações**: Obtenha recomendações do Assistente que mostram como você pode otimizar e melhorar a eficiência.


**Saiba mais:**

- [Obtenha uma visão geral](https://docs.microsoft.com/azure/cost-management/overview) do Gerenciamento de Custos do Azure.
- [Saiba como](https://docs.microsoft.com/azure/cost-management/cost-mgt-best-practices) otimizar seu investimento na nuvem com o Gerenciamento de Custos do Azure.
- [Saiba como](https://docs.microsoft.com/azure/cost-management/use-reports) usar relatórios de Gerenciamento de Custos do Azure.
- [Obtenha um tutorial](https://docs.microsoft.com/azure/cost-management/tutorial-acm-opt-recommendations?toc=/azure/billing/TOC.json) sobre como otimizar os custos com base nas recomendações.
- [Examine](https://docs.microsoft.com/rest/api/consumption/budgets) a API de Consumo do Azure.

## <a name="best-practice-monitor-resource-utilization"></a>Melhor prática: Monitorar a utilização de recursos

No Azure, você paga pelo que usar, quando os recursos são consumidos e não paga quando eles não são. Para VMs, a cobrança ocorre quando uma VM é alocada e você não é cobrado depois de uma VM ser desalocada. Com isso em mente, você deve monitorar as VMs em uso e verificar o dimensionamento da VM.

- Avalie continuamente suas cargas de trabalho de VM para determinar as linhas de base.
- Por exemplo, se a carga de trabalho é usada intensamente de segunda a sexta-feira, das 8h às 18h, mas dificilmente usada fora desse horário, você pode fazer downgrade das VMs fora do horário de pico. Isso pode significar a alteração dos tamanhos de VM ou o uso de conjuntos de dimensionamento de máquinas virtuais para escalar ou reduzir verticalmente as VMs.
- Algumas empresas "adiam" as VMs colocando-as em um calendário que especifica quando devem estar disponíveis e quando não são necessárias. 
- Além do monitoramento da VM, você deve monitorar outros recursos de rede, como o ExpressRoute e gateways de rede virtual para subutilização e uso excessivo.
- Monitore o uso da VM usando uma série de ferramentas da Microsoft, incluindo Gerenciamento de Custos do Azure, Azure Monitor e o Assistente do Azure. Ferramentas de terceiros também estão disponíveis.  

**Saiba mais:**
- Obtenha uma visão geral do [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) e do [Assistente do Azure](https://docs.microsoft.com/azure/advisor/advisor-overview).
- [Obtenha](https://docs.microsoft.com/azure/advisor/advisor-cost-recommendations) recomendações de custo do Assistente.
- [Saiba como [otimizar os custos com base nas recomendações](https://docs.microsoft.com/azure/cost-management/tutorial-acm-opt-recommendations?toc=/azure/billing/TOC.json) e [evitar encargos inesperados](https://docs.microsoft.com/azure/billing/billing-getting-started)].
- [Saiba mais sobre](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit/) o ARO (Azure Resource Optimization) Toolkit

## <a name="best-practice-implement-resource-group-budgets"></a>Melhor prática: Implementar orçamentos de grupo de recursos

Geralmente, os grupos de recursos são usados para representar limites de custo. Junto com esse padrão de uso, a equipe do Azure continua desenvolvendo maneiras novas e aprimoradas para acompanhar e analisar os gastos com recursos em diferentes níveis, incluindo a capacidade de criar orçamentos no grupo de recursos e nos recursos.  

- Um orçamento de grupo de recursos ajuda você a controlar os custos associados a um grupo de recursos.
- Dispare alertas e execute uma ampla variedade de guias estratégicos quando o orçamento for atingido ou excedido. 

**Saiba mais:**

- [Saiba como](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario) gerenciar os custos com os Orçamentos do Azure.
- [Siga um tutorial](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?toc=/azure/billing/TOC.json) para criar e gerenciar um orçamento do Azure.


## <a name="best-practice-optimize-azure-monitor-retention"></a>Melhor prática: Otimizar a retenção do Azure Monitor

Conforme você move recursos para o Azure e habilita o log de diagnósticos para eles, você gera muitos dados de log. Normalmente, esses dados de log são enviados para uma conta de armazenamento que é mapeada para um espaço de trabalho do Log Analytics.

- Quanto maior for o período de retenção de dados de log, mais dados você terá.
- Nem todos os dados de log são iguais e alguns recursos gerarão mais dados de log do que outros.
- Devido à regulamentação e à conformidade, é provável que você precise reter os dados de log de alguns recursos por um período mais longo do que outros.
- Você deve manter um equilíbrio cuidadoso entre a otimização dos custos de armazenamento de log e a retenção dos dados de log necessários.
- Recomendamos avaliar e configurar o log imediatamente após a conclusão de uma migração, de modo que você não gaste dinheiro retendo logs sem importância.

**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs) como monitorar o uso e os custos estimados.
 
## <a name="best-practice-optimize-storage"></a>Melhor prática: Otimizar o armazenamento

Se você seguiu as melhores práticas para a seleção do armazenamento antes da migração, provavelmente, está colhendo alguns benefícios. No entanto, talvez haja custos de armazenamento adicionais que você ainda possa otimizar. Ao longo do tempo, blobs e arquivos se tornam obsoletos. Os dados podem não ser mais utilizados, mas os requisitos regulamentares podem indicar que você precise mantê-los por determinado período. Dessa forma, talvez você não precise armazená-los no armazenamento de alto desempenho que usou para a migração original.

A identificação e a movimentação de dados obsoletos para áreas de armazenamento mais baratas podem ter um grande impacto em seu orçamento de armazenamento mensal e na economia de custos. O Azure fornece várias maneiras para ajudá-lo a identificar e, em seguida, armazenar esses dados obsoletos.

- Aproveite as camadas de acesso para o armazenamento de uso geral v2, movendo dados menos importantes da camada Frequente para as camadas Esporádica e de Arquivos.
- Use o StorSimple para ajudar a mover dados obsoletos, de acordo com políticas personalizadas. 

**Saiba mais:**
- [Saiba mais](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) sobre as camadas de acesso.
- [Obtenha uma visão geral](https://docs.microsoft.com/azure/azure-monitor/overview) do StorSimple e dos [preços do StorSimple](https://azure.microsoft.com/pricing/details/storsimple/).

## <a name="best-practice-automate-vm-optimization"></a>Melhor prática: Automatizar a otimização de VM

O objetivo final de executar uma VM na nuvem é maximizar a CPU, a memória e o disco que ela utiliza. Se você descobrir VMs que não são otimizadas ou se tiver períodos de frequência em que as VMs não são utilizadas, fará sentido desligá-las ou diminuí-las usando conjuntos de dimensionamento de VMs.

Você pode otimizar uma VM com a Automação do Azure, conjuntos de dimensionamento de VMs, desligamento automático e soluções de script ou de terceiros. 

**Saiba mais:**

- [Saiba como](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision) usar o dimensionamento automático vertical.
- [Agende](https://azure.microsoft.com/updates/azure-devtest-labs-schedule-vm-auto-start/) a inicialização automática de uma VM.
- [Saiba como](https://docs.microsoft.com/azure/automation/automation-solution-vm-management) iniciar ou parar VMs fora do horário comercial na Automação do Azure.
- [Obtenha mais informações] sobre o [Assistente do Azure](https://docs.microsoft.com/azure/advisor/advisor-overview) e o [ARO (Azure Resource Optimization) Toolkit](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit/).

## <a name="best-practices-use-logic-apps-and-runbooks-with-budgets-api"></a>Melhores práticas: Usar Aplicativos Lógicos e runbooks com a API de Orçamentos

O Azure fornece uma API REST que tem acesso às suas informações de cobrança de locatário.

- Você pode usar a API de Orçamentos para integrar sistemas externos e fluxos de trabalho que são disparados por métricas criadas por você com base nos dados da API.
- Efetue pull dos dados de uso e do recurso para suas ferramentas de análise de dados preferidas.
- As APIs RateCard e de Uso de Recursos do Azure e podem ajudá-lo a prever e gerenciar seus custos com precisão.
- As APIs são implementadas como um Provedor de Recursos e são incluídas nas APIs expostas pelo Azure Resource Manager.
- A API de Orçamentos pode ser integrada aos Aplicativos Lógicos do Azure e aos Runbooks.

**Saiba mais:**

- [Saiba mais](https://docs.microsoft.com/rest/api/consumption/budgets) sobre a API de Orçamentos.
- [Obtenha insights](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) sobre o uso do Azure com a API de Cobrança.


## <a name="best-practice-implement-serverless-technologies"></a>Melhor prática: Implementar tecnologias sem servidor

As cargas de trabalho de VM são migradas com frequência "no estado em que se encontram" para evitar tempo de inatividade. Geralmente, as VMs podem hospedar tarefas intermitentes, levando um curto período para serem executadas ou, como alternativa, muitas horas. Por exemplo, VMs que executam tarefas agendadas, como o Agendador de Tarefas do Windows ou scripts do PowerShell. Quando essas tarefas não estão em execução, mesmo assim, você está absorvendo os custos da VM e do armazenamento em disco.

Após a migração, depois de uma análise completa desses tipos de tarefas, você poderá considerar a possibilidade de migrá-las para tecnologias sem servidor, como Azure Functions ou trabalhos do Lote do Azure. Com essa solução, você não precisa mais gerenciar e manter as VMs, gerando uma economia de custo adicional. 

**Saiba mais:**
- [Saiba mais sobre](https://azure.microsoft.com/services/functions/) o Azure Functions
- [Saiba mais sobre](https://azure.microsoft.com/en-us/services/batch/) o Lote do Azure
  
## <a name="next-steps"></a>Próximas etapas 

Examine outras melhores práticas:

- [Melhores práticas](migrate-best-practices-security-management.md) de segurança e gerenciamento após a migração.
- [Melhores práticas](migrate-best-practices-networking.md) de rede após a migração.

