---
title: Perguntas frequentes – Avere vFXT para Azure
description: Use essas perguntas frequentes para decidir se avere vFXT para o Azure atende às suas necessidades. Saiba como o avere vFXT for Azure funciona com outros componentes do Azure.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.custom: references_regions
ms.openlocfilehash: 647a9792384a21dfc191371ef444cbdc098bc819
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92342358"
---
# <a name="avere-vfxt-for-azure-faq"></a>Perguntas frequentes do Avere vFXT para Azure

Este artigo responde às perguntas que podem ajudar você a decidir se o Avere vFXT para Azure é adequado para suas necessidades. Ele fornece informações básicas sobre as funcionalidades do Avere vFXT e explica como ele funciona com outros componentes do Azure e com produtos de fornecedores externos.

## <a name="general"></a>Geral

### <a name="what-is-avere-vfxt-for-azure"></a>O que é o Avere vFXT para Azure?

Avere vFXT para Azure é um sistema de arquivos de alto desempenho que armazena dados ativos em cache na computação do Azure para processamento eficiente de cargas de trabalho críticas.

### <a name="is-avere-vfxt-a-storage-solution"></a>Avere vFXT é uma solução de armazenamento?

Não. O avere vFXT for Azure é um *cache* de sistema de arquivos que é anexado a ambientes de armazenamento, como o nas da EMC ou do NetApp, ou um contêiner de BLOBs do Azure. O Avere vFXT simplifica as solicitações de dados de clientes e armazena em cache os dados que ele serve para melhorar o desempenho em escala e ao longo do tempo. O Avere vFXT em si não armazena dados. Ele não tem informações sobre a quantidade de dados armazenados por trás dele.

### <a name="is-avere-vfxt-a-tiering-solution"></a>Avere vFXT é uma solução de camada?

O avere vFXT para Azure não hierarquiza automaticamente os dados entre as camadas quentes e frias.  

### <a name="how-do-i-know-if-an-environment-is-right-for-avere-vfxt"></a>Como saber se um ambiente é certo para o Avere vFXT?

A melhor maneira de pensar nessa pergunta é se perguntar "A carga de trabalho pode ser armazenada em cache?" Ou seja, a carga de trabalho tem uma alta taxa de leitura para gravação? Um exemplo é 80/20 ou 70/30 leituras/gravações.

Considere o Avere vFXT para Azure se tiver um pipeline de análise baseado em arquivo que é executado em um grande número de máquinas virtuais do Azure e ele atende a uma ou mais das seguintes condições:

* O desempenho geral é lento ou incoerente devido a longos tempos de acesso do arquivo (dezenas de milissegundos ou segundos, dependendo dos requisitos). Essa latência é inaceitável para o cliente final.

* Os dados necessários para processamento estão localizados na extremidade de um ambiente WAN e movê-los permanentemente é impraticável. Os dados podem estar em uma região diferente do Azure ou no datacenter de um cliente.

* Um número significativo de clientes estão solicitando os dados – por exemplo, em um cluster HPC (computação de alto desempenho). O grande número de solicitações simultâneas pode aumentar a latência.

* O cliente quer executar o pipeline atual "no estado atual" nas máquinas virtuais do Azure e precisa de uma solução de armazenamento compartilhado baseada em POSIX (ou cache) para escalabilidade. Usando o Avere vFXT para Azure, não é necessário refazer a arquitetura do pipeline de trabalho para fazer chamadas nativas para o Armazenamento de Blob do Azure.

* Seu aplicativo HPC baseia-se em clientes NFSv3. (Em algumas circunstâncias, ele pode usar clientes do SMB 2.1, mas o desempenho é limitado.)

O diagrama a seguir pode ajudá-lo a responder a essa pergunta. Quanto mais próximo o fluxo de trabalho estiver no canto superior direito, mais provável será que a solução de avere vFXT para o cache do Azure seja adequada para seu ambiente.

![Diagrama de gráfico mostrando que cargas pesadas de leitura com milhares de clientes são mais adequadas para avere vFXT](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>Em que escala de clientes a solução do Avere vFXT faz mais sentido?

A solução de cache do Avere vFXT é criada para lidar com centenas, milhares ou dezenas de milhares de núcleos de computação. Se você tiver poucos computadores executando trabalho leve, o Avere vFXT não é a solução certa.

Os clientes do típicos do Avere vFXT executam cargas de trabalho exigentes que começam em cerca de 1.000 núcleos de CPU. Esses ambientes podem ser tão grandes quanto 50.000 núcleos ou mais. Como o Avere vFXT é escalonável, é possível adicionar nós para dar suporte a essas cargas de trabalho à medida que elas crescem para exigir mais taxa de transferência ou mais IOPS.

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>Quantos dados um ambiente do Avere vFXT pode armazenar?

Avere vFXT for Azure é um cache. Ele não armazena dados especificamente. Ele usa uma combinação de RAM e SSDs para armazenar os dados armazenados em cache. Os dados são armazenados permanentemente em um sistema de armazenamento de back-end (por exemplo, um sistema NAS do NetApp ou um contêiner de blob). O sistema Avere vFXT não tem informações sobre a quantidade de dados armazenados por trás dele. O Avere vFXT armazena em cache somente o subconjunto de dados que os clientes solicitam.  

### <a name="what-regions-are-supported"></a>Quais regiões têm suporte?

O avere vFXT para Azure tem suporte em todas as regiões, exceto nas regiões de soberanas (China, Alemanha). Certifique-se de que a região que você deseja usar pode oferecer suporte à grande quantidade de núcleos de computação e instâncias de VM necessárias para criar o cluster do Avere vFXT.

### <a name="how-do-i-get-help-with-avere-vfxt"></a>Como fazer para obter ajuda com o Avere vFXT?

Um grupo especializado de equipe de suporte oferece ajuda com o avere vFXT para o Azure. Siga as instruções em [Obtenha ajuda com o seu sistema](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) para abrir um tíquete de suporte no portal do Azure.

### <a name="is-avere-vfxt-highly-available"></a>O Avere vFXT é altamente disponível?

Sim, o Avere vFXT é executado exclusivamente como uma solução de HA.

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>O Avere vFXT para Azure também dá suporte a outros serviços de nuvem?

Sim, os clientes podem usar mais de um provedor de nuvem com o cluster do Avere vFXT. Ele dá suporte a buckets padrão S3 do AWS e buckets padrão do Google Cloud Services, além de contêineres de blob do Azure.

> [!NOTE]
> Uma taxa de software se aplica ao uso do avere vFXT com AWS ou Google Cloud Storage. Não há nenhuma taxa de software adicional para usar o armazenamento de BLOBs do Azure.

## <a name="technical-compute"></a>Técnico: computação

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>Você pode descrever a "aparência" de um ambiente do Avere vFXT?

Avere vFXT é um dispositivo clusterizado composto por várias máquinas virtuais do Azure. Uma biblioteca Python lida com a criação, exclusão e modificação do cluster. Leia [O que é Avere vFXT para Azure?](avere-vfxt-overview.md) para saber mais.

### <a name="what-kind-of-azure-virtual-machines-does-avere-vfxt-run-on"></a>Em quais tipos de máquinas virtuais do Azure o Avere vFXT é executado?  

Um avere vFXT para o cluster do Azure usa Microsoft Azure máquinas virtuais E32s_v3.

<!-- ### Can I mix and match virtual machine types for my cluster?

No, you must choose one virtual machine type or the other.
    
### Can I move between virtual machine types?

Yes, there is a migration path to move from one VM type to the other. [Open a support ticket](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) to learn how.
-->

### <a name="does-the-avere-vfxt-environment-scale"></a>O ambiente do Avere vFXT é dimensionado?

O cluster do Avere vFXT pode ser tão pequeno quanto três nós de máquina virtual ou tão grande quanto 24 nós. Contate o suporte técnico do Azure para ajudar a planejar se você achar que precisa de um cluster de mais de nove nós. O número maior de nós requer uma arquitetura de implantação maior.

### <a name="does-the-avere-vfxt-environment-autoscale"></a>O ambiente do Avere vFXT tem "dimensionamento automático"?

Não. É possível aumentar e diminuir o tamanho do cluster, mas adicionar ou remover nós de cluster é uma etapa manual.

### <a name="can-i-run-the-avere-vfxt-cluster-as-a-virtual-machine-scale-set"></a>É possível executar o cluster do Avere vFXT como um conjunto de dimensionamento de máquinas virtuais?

O Avere vFXT não dá suporte à implantação de um conjunto de dimensionamento de máquinas virtuais. Vários mecanismos de suporte de disponibilidade interna destinam-se apenas às VMs atômicas que participam de um cluster.  

### <a name="can-i-run-the-avere-vfxt-cluster-on-low-priority-vms"></a>É possível executar o cluster do Avere vFXT em VMs de baixa prioridade?

Não, o sistema requer um conjunto estável subjacente de máquinas virtuais.

### <a name="can-i-run-the-avere-vfxt-cluster-in-containers"></a>É possível executar o cluster do Avere vFXT em contêineres?

Não, o Avere vFXT deve ser implantado como um aplicativo independente.

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>As VMs do Avere vFXT contam para minha cota de computação?

Sim. Certifique-se de ter cota suficiente na região para dar suporte ao cluster.  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>É possível executar os computadores do cluster do Avere vFXT em diferentes zonas de disponibilidade?

Não. Atualmente, o modelo de alta disponibilidade no Avere vFXT não dá suporte a membros individuais do cluster do Avere vFXT localizados em diferentes zonas de disponibilidade.

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>É possível clonar máquinas virtuais do Avere vFXT?

Não, é necessário usar o script do Python com suporte para adicionar ou remover nós no cluster do Avere vFXT. Para obter mais informações, leia [Manage the Avere vFXT cluster](avere-vfxt-manage-cluster.md) (Gerenciar o cluster do Avere vFXT).

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>Há uma versão de "VM" do software que eu posso executar em meu próprio ambiente local?

Não, o sistema é oferecido como um dispositivo clusterizado e testado em tipos específicos de máquina virtual. Essa restrição ajuda os clientes a evitar criar um sistema que não pode dar suporte aos requisitos de alto desempenho de um fluxo de trabalho típico do Avere vFXT.

## <a name="technical-disks"></a>Técnico: discos

### <a name="what-types-of-disks-are-supported-for-the-azure-vms"></a>Quais tipos de discos têm suporte para as VMs do Azure?

O Avere vFXT para Azure pode usar configurações de SSD Premium de 1 TB ou 4 TB. A configuração de SSD Premium pode ser implantada como vários discos gerenciados.

### <a name="does-the-cluster-support-unmanaged-disks"></a>O cluster dá suporte a discos não gerenciados?

Não, o cluster requer discos gerenciados.

### <a name="does-the-system-support-local-attached-ssds"></a>O sistema dá suporte a SSDs locais (anexados)?

No momento, o Avere vFXT para Azure não dá suporte a SSDs locais. Os discos usados para o Avere vFXT devem poder desligar e reiniciar, mas os SSDs anexados localmente nessa configuração só podem ser encerrados.

### <a name="does-the-system-support-ultra-ssds"></a>O sistema é compatível com ultra SSDs?

Não, o sistema é compatível apenas com configurações de SSD Premium.

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>É possível desanexar meus SSDs premium e reconectá-los mais tarde para preservar o conteúdo de cache entre o uso?

Não há suporte para desanexar e anexar novamente os SSDs. Os metadados ou o conteúdo de arquivo na origem podem ter sido alterados entre usos, o que poderia causar problemas de integridade dos dados.

### <a name="does-the-system-encrypt-the-cache"></a>O sistema criptografa o cache?

Os dados são distribuídos entre os discos, mas não são criptografados. No entanto, os discos em si podem ser criptografados. Para obter mais informações, consulte [Proteger e usar políticas em máquinas virtuais no Azure](../virtual-machines/security-policy.md#encryption).

## <a name="technical-networking"></a>Técnico: rede

### <a name="what-network-is-recommended"></a>Qual rede é recomendada?

Se você estiver usando o armazenamento local com avere vFXT, você deverá ter uma conexão de rede de 1 Gbps ou melhor entre o armazenamento e o cluster. Se você tiver uma pequena quantidade de dados e estiver disposto a copiá-los para a nuvem antes de executar trabalhos, a conectividade da VPN pode ser suficiente.

> [!TIP]
> Quanto mais lento for o link de rede, mais lentas serão as leituras "Cold" iniciais. Leituras lentas aumentam a latência do pipeline de trabalho.

### <a name="can-i-run-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>É possível executar o Avere vFXT em uma rede virtual diferente da rede do meu cluster de cálculo?

Sim, é possível criar seu sistema do Avere vFXT em uma rede virtual diferente. Leia [Planeje seu sistema do Avere vFXT](avere-vfxt-deploy-plan.md) para saber mais.

### <a name="does-avere-vfxt-require-its-own-subnet"></a>O Avere vFXT requer sua própria sub-rede?

Sim. Avere vFXT é executado estritamente como um cluster de alta disponibilidade (HA) e requer vários endereços IP para operar. Se o cluster estiver em sua própria sub-rede, evite o risco de conflitos no endereço IP, que podem causar problemas para instalação e operação normal. A sub-rede do cluster pode estar dentro de uma rede virtual usada por outros recursos, desde que nenhum endereço IP se sobreponha.

### <a name="can-i-run-avere-vfxt-on-infiniband"></a>É possível executar o Avere vFXT no Infiniband?

Não, o Avere vFXT usa somente Ethernet/IP.

### <a name="how-do-i-access-my-on-premises-nas-environment-from-avere-vfxt"></a>Como fazer para acessar meu ambiente NAS local a partir do Avere vFXT?

O ambiente do Avere vFXT é semelhante a qualquer outra VM do Azure no sentido que ele requer acesso roteado por meio de um gateway de rede ou VPN para o datacenter do cliente (e de volta). Considere usar a conectividade do Azure ExpressRoute se ela estiver disponível em seu ambiente.

### <a name="what-are-the-bandwidth-requirements-for-avere-vfxt"></a>Quais são os requisitos de largura de banda do Avere vFXT?

O requisito geral de largura de banda depende de dois fatores:

* A quantidade de dados que está sendo solicitada da origem
* A tolerância do sistema do cliente para latência durante o carregamento inicial de dados  

Para ambientes sensíveis à latência, você deve usar uma solução de fibra com uma velocidade mínima do link de 1 Gbps. Use o ExpressRoute se ele estiver disponível.  

### <a name="can-i-run-avere-vfxt-with-public-ip-addresses"></a>É possível executar o Avere vFXT com endereços IP públicos?

Não, o Avere vFXT deve ser operado dentro de um ambiente de rede protegido usando as melhores práticas.

### <a name="can-i-restrict-internet-access-from-my-clusters-virtual-network"></a>Posso restringir o acesso à Internet da minha rede virtual do cluster?

Em geral, você pode configurar a segurança adicional em sua rede virtual conforme necessário, mas algumas restrições podem interferir na operação do cluster.

Por exemplo, restringir o acesso à Internet de saída de sua rede virtual causa problemas para o cluster, a menos que você também adicione uma regra que permita explicitamente o acesso ao AzureCloud. Essa situação é descrita em [documentação suplementar no GitHub](https://github.com/Azure/Avere/tree/master/src/vfxt/internet_access.md).

Para obter ajuda com a segurança personalizada, entre em contato com o suporte conforme descrito em [obter ajuda com o sistema](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt).

## <a name="technical-back-end-storage-core-filers"></a>Técnico: armazenamento de back-end (Filers principais)

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>A quantos arquivistas principais um único ambiente do Avere vFXT dá suporte?

Um cluster do Avere vFXT dá suporte a até 20 arquivistas principais.

### <a name="how-does-the-avere-vfxt-environment-store-data"></a>Como o ambiente do Avere vFXT armazena dados?

Avere vFXT não é armazenamento. Ele é um cache que lê e grava dados de vários destinos de armazenamento chamados arquivistas principais. Os dados armazenados em discos SSD Premium do Avere vFXT são temporários e são liberados eventualmente no armazenamento do arquivista principal de back-end.

### <a name="which-core-filers-does-avere-vfxt-support"></a>A quais arquivistas principais o Avere vFXT dá suporte?

Em termos gerais, o Avere vFXT para Azure dá suporte aos seguintes sistemas como arquivistas principais:

* Dell EMC Isilon (OneFS 7.1, 7.2, 8.0 e 8.1) 
* NetApp ONTAP (Modo Clusterizado 9.4, 9.3, 9.2, 9.1P1, 8.0-8.3) e (7-Mode 7.*, 8.0-8.3)

* Contêineres de blob do Azure (somente para armazenamento com redundância local)
* Buckets do AWS S3
* Buckets do Google Cloud

### <a name="why-doesnt-avere-vfxt-support-all-nfs-filers"></a>Por que o Avere vFXT não dá suporte a todos os arquivistas do NFS?

Embora todas as plataformas do NFS atendam aos mesmos padrões de IETF, na prática, cada implementação tem características próprias. Esses detalhes afetam a maneira como o Avere vFXT interage com o sistema de armazenamento. Os sistemas com suporte são as plataformas mais amplamente usadas no marketplace.

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>O Avere vFXT é compatível com o armazenamento de objetos privados (como o Swiftstack)?

O Avere vFXT não é compatível com o armazenamento de objetos privados.

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>Como é possível obter um produto de armazenamento específico sob suporte?

O suporte é baseado na quantidade de demanda no campo. Se houver solicitações baseadas em receita suficientes para dar suporte a uma solução NAS, isso será considerado. Faça solicitações por meio do suporte do Azure.

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>É possível usar o Armazenamento de Blobs do Azure como um arquivista principal?

Sim, o Avere vFXT para Azure pode usar um contêiner de blob de blocos como um arquivista principal de nuvem.

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>Quais são os requisitos da conta de armazenamento para um arquivista principal de blob?

Sua conta de armazenamento deve ser uma conta GPv2 (uso geral v2) e configurada apenas para armazenamento com redundância local. Não há suporte para armazenamento com redundância geográfica e armazenamento com redundância de zona.

Leia [Azure Blob Storage Cloud principal Filer](avere-vfxt-add-storage.md#azure-blob-storage-cloud-core-filer) para obter mais detalhes sobre os requisitos da conta de armazenamento.

### <a name="can-i-use-archive-blob-storage"></a>É possível usar o armazenamento de blob de arquivos?

Não. O SLA (Contrato de Nível de Serviço) para o armazenamento de arquivos não é compatível com o diretório em tempo real e as necessidades de acesso ao arquivo do sistema Avere vFXT.

### <a name="can-i-use-cool-blob-storage"></a>É possível usar o armazenamento de blob esporádico?

O armazenamento de blobs de camada fria geralmente não é recomendado para um avere vFXT para o Azure Core Filer. A camada fria oferece custos de armazenamento menores, mas custos de operações mais altos. (Consulte [preço do blob de blocos](<https://azure.microsoft.com/pricing/details/storage/blobs/>) para obter mais detalhes.) Se os dados forem acessados e modificados ou excluídos com frequência, considere o uso da camada quente.

As [camadas de acesso](../storage/blobs/storage-blob-storage-tiers.md#cool-access-tier) fornecem mais informações sobre quando pode fazer sentido usar o armazenamento de camada fria como um vFXT Core Filer.

### <a name="how-do-i-encrypt-the-blob-container"></a>Como fazer para criptografar o contêiner de blob?

É possível configurar a criptografia de blob no Azure (preferencial) ou no nível do arquivista principal do Avere vFXT.  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>Posso usar minha própria chave de criptografia para um arquivista principal de blob?

Por padrão, os dados são criptografados por meio de chaves gerenciadas pela Microsoft para armazenamento de Blob do Azure, de Tabela e Fila, além de Arquivos do Azure. É possível trazer sua própria chave para criptografia do armazenamento de Blob e Arquivos do Azure. Se optar por usar a criptografia do Avere vFXT, será necessário usar a chave gerada pelo Avere e armazená-la localmente.

## <a name="purchasing"></a>Compra

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>Como fazer para obter o licenciamento do Avere vFXT para Azure?

Obter um licença do Avere vFXT para Azure é fácil por meio do Azure Marketplace. Inscreva-se em uma conta do Azure e, em seguida, siga as instruções em [Implantar o cluster do Avere vFXT](avere-vfxt-deploy.md) para criar um cluster do Avere vFXT.

### <a name="how-much-does-avere-vfxt-cost"></a>Quanto custa o Avere vFXT?

No Azure, não há taxa de licenciamento adicional para usar clusters do Avere vFXT. Os clientes são responsáveis pelo armazenamento e por outras taxas de consumo do Azure.

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>As VMs do Avere vFXT podem ser executadas como baixa prioridade?

Não, os clusters do Avere vFXT exigem serviço "Always On". Os clusters poderão ser desligados quando não forem necessários.

## <a name="next-steps"></a>Próximas etapas

Para uma introdução ao Avere vFXT para Azure, leia estes artigos para saber como planejar e implantar seu próprio sistema:

* [Planejar seu sistema Avere vFXT](avere-vfxt-deploy-plan.md)
* [Visão geral da implantação](avere-vfxt-deploy-overview.md)
* [Preparar para criar um cluster do Avere vFXT](avere-vfxt-prereqs.md)
* [Implantar cluster do Avere vFXT](avere-vfxt-deploy.md)

Para saber mais sobre as funcionalidades e casos de uso do Avere vFXT, visite [Avere vFXT para Azure](https://azure.microsoft.com/services/storage/avere-vfxt/).