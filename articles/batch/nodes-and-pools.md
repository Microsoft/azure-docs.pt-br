---
title: Nós e pools no Lote do Azure
description: Saiba mais sobre nós e pools de computação e como eles são usados em um fluxo de trabalho do Lote do Azure do ponto de vista de desenvolvimento.
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 7d4c2d45849deb011498efe4c8a1ae91724b9acd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563888"
---
# <a name="nodes-and-pools-in-azure-batch"></a>Nós e pools no Lote do Azure

Em um fluxo de trabalho do Lote do Azure, um *nó de computação* (ou *nó*) é uma máquina virtual que processa uma parte da carga de trabalho do aplicativo. Um *pool* é uma coleção desses nós para execução do aplicativo. Este artigo explica melhor nós e pools, juntamente com considerações ao criá-los e usá-los em um fluxo de trabalho do Lote do Azure.

## <a name="nodes"></a>Nós

Um nó é uma máquina virtual (VM) do Azure ou VM do serviço de nuvem que é dedicada ao processamento de uma parte da carga de trabalho do aplicativo. O tamanho de um nó determina o número de núcleos de CPU, a capacidade da memória e o tamanho do sistema de arquivos local alocado para o nó.

Você pode criar pools de nós de Windows ou Linux usando os Serviços de Nuvem do Azure, imagens do [Marketplace de Máquinas Virtuais do Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?filters=virtual-machine-images&page=1) ou imagens personalizadas preparadas por você.

Os nós podem executar qualquer executável ou script que tenha suporte no ambiente do sistema operacional do nó. Os executáveis ou scripts incluem \*.exe, \*.cmd, \*.bat e os scripts do PowerShell (para Windows) e binários, shell e scripts Python (para Linux).

Todos os nós de computação no Lote também incluem:

- Uma [estrutura de pastas](files-and-directories.md) padrão e as [variáveis de ambiente](jobs-and-tasks.md) associadas disponíveis para a referência por tarefas.
- **firewall** que são definidas para controlar o acesso.
- [Acesso remoto](error-handling.md#connect-to-compute-nodes) para os nós do Windows (protocolo RDP (RDP)) e do Linux (Secure Shell (SSH)) (a menos que você [crie seu pool com acesso remoto desabilitado](pool-endpoint-configuration.md)).

Por padrão, os nós podem se comunicar entre si, mas não podem se comunicar com máquinas virtuais que não fazem parte do mesmo pool. Para permitir que os nós se comuniquem com segurança com outras máquinas virtuais, ou com uma rede local, você pode provisionar o pool [em uma sub-rede de uma VNet (rede virtual) do Azure](batch-virtual-network.md). Quando você fizer isso, os nós poderão ser acessados por meio de endereços IP públicos. Esses endereços IP públicos são criados pelo lote e podem ser alterados durante o tempo de vida do pool. Você também pode [criar um pool com endereços IP públicos estáticos](create-pool-public-ip.md) que você controla, o que garante que eles não serão alterados inesperadamente.

## <a name="pools"></a>Pools

Um pool é uma coleção de nós onde o aplicativo é executado.

Os pools do Lote do Azure se baseiam na plataforma de computação principal do Azure. Eles fornecem alocação em grande escala, instalação de aplicativos, distribuição de dados, monitoramento de integridade e ajuste flexível ([dimensionamento](#automatic-scaling-policy)) do número de nós de computação em um pool.

Todos os nós adicionados a um pool recebem um nome e um endereço IP exclusivos. Quando um nó é removido de um pool, todas as alterações feitas no sistema operacional ou arquivos são perdidas e seu nome e endereço IP são liberados para o uso futuro. Quando um nó deixa um pool, seu tempo de vida termina.

Um pool pode ser usado somente pela conta do Lote na qual foi criado. Uma conta do Lote pode criar vários pools para atender aos requisitos de recursos dos aplicativos que serão executados.

O pool pode ser criado manualmente ou [automaticamente pelo serviço de lote](#autopools) quando você especifica o trabalho a ser feito. Ao criar um pool, você pode especificar os seguintes atributos:

- [Sistema operacional e versão do nó](#operating-system-and-version)
- [Tipo de nó e número de nós de destino](#node-type-and-target)
- [Tamanho do nó](#node-size)
- [Política de dimensionamento automático](#automatic-scaling-policy)
- [Política de agendamento de tarefas](#task-scheduling-policy)
- [Estado da comunicação](#communication-status)
- [Tarefas iniciais](#start-tasks)
- [Pacotes de aplicativos](#application-packages)
- [Configuração de firewall e rede virtual (Vnet)](#virtual-network-vnet-and-firewall-configuration)
- [Tempo de vida](#pool-and-compute-node-lifetime)

> [!IMPORTANT]
> As contas do Lote têm uma cota padrão que limita o número de núcleos em uma conta do Lote. O número de núcleos corresponde ao número de nós de computação. Você pode encontrar as cotas padrão e instruções sobre como [aumentar uma cota](batch-quota-limit.md#increase-a-quota) em [Cotas e limites para o serviço Lote do Azure](batch-quota-limit.md). Se o pool não está alcançando seu número desejado de nós, a cota de núcleo pode ser o motivo.

## <a name="operating-system-and-version"></a>Sistema operacional e versão

Quando você cria um pool do Lote, especifique a configuração de máquina virtual do Azure e o tipo de sistema operacional que deseja executar em cada nó de computação no pool.

## <a name="configurations"></a>Configurações

Existem dois tipos de configuração de pool disponíveis no Lote.

> [!IMPORTANT]
> Embora você possa criar pools no momento usando qualquer configuração, novos pools devem ser configurados usando a configuração de máquina virtual e não a configuração de serviços de nuvem. Todos os recursos atuais e novos do lote serão suportados pelos pools de configuração da máquina virtual. Os pools de configuração de serviços de nuvem não oferecem suporte a todos os recursos e nenhuma nova funcionalidade é planejada. Você não poderá criar novos pools ' CloudServiceConfiguration ' ou adicionar novos nós a pools existentes [após 29 de fevereiro de 2024](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/).

### <a name="virtual-machine-configuration"></a>Configuração de Máquina Virtual

A **Configuração de Máquina Virtual** especifica que o pool é composto de máquinas virtuais do Azure. Essas máquinas virtuais podem ser criadas de imagens Linux ou Windows.

O [agente de nó de lote](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md) é um programa executado em cada nó no pool e fornece a interface de comando e controle entre o nó e o serviço de lote. Há diferentes implementações do agente do nó, conhecido como SKUs, para diferentes sistemas operacionais. Ao criar um pool baseado na Configuração da Máquina Virtual, você deverá especificar não apenas o tamanho dos nós e a origem das imagens usadas para criá-los, mas também a **referência da imagem da máquina virtual** e a **SKU do agente de nó** do Lote a ser instalada nos nós. Para saber mais sobre como especificar essas propriedades de pool, confira [Provisionar nós de computação do Linux em pools do Lote do Azure](batch-linux-nodes.md). Opcionalmente, você pode anexar um ou mais discos de dados vazios ao pool de máquinas virtuais criadas desde imagens do Marketplace ou incluir discos de dados em imagens personalizadas usadas para criar as máquinas virtuais. Quando incluir discos de dados, você precisará montar e formatar os discos de dentro de uma VM para usá-los.

### <a name="cloud-services-configuration"></a>Configuração de Serviços de Nuvem

> [!WARNING]
> Os pools de configuração de serviços de nuvem foram [preteridos](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/). Em vez disso, use pools de configuração de máquina virtual. Para obter mais informações, consulte [migrar a configuração do pool do lote dos serviços de nuvem para a máquina virtual](batch-pool-cloud-service-to-virtual-machine-configuration.md).

A **Configuração dos Serviços de Nuvem** especifica que o pool é composto de nós dos Serviços de Nuvem do Azure. Os serviços de nuvem fornecem apenas nós de computação do Windows.

Os sistemas operacionais disponíveis para os pools de configuração dos serviços de nuvem são listados na [matriz de compatibilidade do SDK e versões do SO convidado do Azure](../cloud-services/cloud-services-guestos-update-matrix.md), e os tamanhos de nó de computação disponíveis são listados em [tamanhos para serviços de nuvem](../cloud-services/cloud-services-sizes-specs.md). Ao criar um pool que contém nós de serviços de nuvem, você especifica o tamanho do nó e sua *família de sistemas operacionais* (que determina quais versões do .NET são instaladas com o sistema operacional). Os Serviços de Nuvem são implantados no Azure mais rapidamente do que as máquinas virtuais que executam o Windows. Se você deseja ter pools de nós de computação do Windows, pode achar os Serviços de Nuvem mais vantajosos no desempenho em relação a tempo de implantação.

Assim como acontece com as funções de trabalho nos serviços de nuvem, você pode especificar uma *versão do sistema operacional*. É recomendável especificar `Latest (*)` para a *Versão do SO* de forma que os nós sejam automaticamente atualizados e não haja nenhum trabalho necessário para atender as versões recém-lançadas. O caso de uso principal para selecionar uma versão específica do SO é garantir a compatibilidade dos aplicativos, permitindo que os testes de compatibilidade retroativa sejam executados antes de permitir que a versão seja atualizada. Após a validação, a *Versão do SO* para o pool pode ser atualizada e a nova imagem do SO pode ser instalada. As tarefas em execução serão interrompidas e colocadas novamente na fila.

### <a name="node-agent-skus"></a>SKUs do Agente de Nó

Quando você cria um pool, precisa selecionar o **nodeAgentSkuId** apropriado, dependendo do sistema operacional da imagem base do seu VHD. Obtenha um mapeamento das IDs de SKU do agente de nó disponível para suas referências de imagem do SO chamando a operação [Listar SKUs Suportadas do Agente de Nó](/rest/api/batchservice/list-supported-node-agent-skus).

### <a name="custom-images-for-virtual-machine-pools"></a>Imagens personalizadas para pools de máquina virtual

Para saber como criar um pool com imagens personalizadas, veja [Usar a Galeria de Imagens Compartilhadas para criar um pool personalizado](batch-sig-images.md).

Como alternativa, crie um pool personalizado de máquinas virtuais usando um recurso de [imagem gerenciada](batch-custom-images.md). Para obter informações sobre como preparar imagens personalizadas do Linux de VMs do Azure, consulte [Como criar uma imagem de uma máquina virtual ou de um VHD](../virtual-machines/linux/capture-image.md). Para obter informações sobre como preparar imagens personalizadas do Windows de VMs do Azure, confira [Criar uma imagens gerenciada de uma VM generalizada no Azure](../virtual-machines/windows/capture-image-resource.md).

### <a name="container-support-in-virtual-machine-pools"></a>Suporte de contêiner em pools de Máquina Virtual

Ao criar um pool de Configuração de Máquina Virtual usando as APIs do Lote, você pode configurar o pool para executar tarefas em contêineres do Docker. Atualmente, você deve criar o pool usando uma imagem que dê suporte a contêineres do Docker. Use o Windows Server 2016 Datacenter com a imagem de Contêineres do Azure Marketplace, ou fornecer uma imagem de VM personalizada que inclua o Docker Community Edition ou Enterprise Edition e todos os drivers necessários. As configurações de pool devem incluir uma [configuração de contêiner](/rest/api/batchservice/pool/add) que copie as imagens de contêiner para as máquinas virtuais quando o pool é criado. As tarefas executadas no pool podem fazer referência a imagens de contêiner e a opções de execução do contêiner.

Para obter mais informações, consulte [Executar aplicativos de contêiner do Docker no Lote do Azure](batch-docker-container-workloads.md).

## <a name="node-type-and-target"></a>Tipo e destino do nó

Quando cria um pool, especifique os tipos de nós de que você deseja e o número de destino para cada um. Os dois tipos de nós são:

- **Nós dedicados.** Nós de computação dedicados são reservados para suas cargas de trabalho. Eles são mais caros que os nós de baixa prioridade, mas é garantido que nunca sofrerão preempção.
- **Nós de baixa prioridade.** Nós de baixa prioridade aproveitam a capacidade excedente no Azure para executar cargas de trabalho de Lote. Nós de baixa prioridade são mais baratos por hora do que nós dedicados e permitem cargas de trabalho que exigem uma capacidade de computação significativa. Para obter mais informações, consulte [Usar VMs de baixa prioridade com o Lote](batch-low-pri-vms.md).

Pode ocorrer preempção de nós de baixa prioridade quando o Azure tem capacidade excedente insuficiente. Se ocorrer preempção de um nó durante a execução de tarefas, as tarefas serão retiradas da fila e executadas novamente depois que um nó de computação ficar disponível novamente. Nós de baixa prioridade são uma boa opção para cargas de trabalho nas quais o tempo para conclusão do trabalho é flexível e o trabalho é distribuído entre muitos nós. Antes de decidir usar nós de baixa prioridade para seu cenário, verifique se os trabalhos perdidos devido a preempção serão poucos e fáceis de se recriar.

Você pode ter nós de computação de baixa prioridade e dedicados no mesmo pool. Cada tipo de nó tem sua própria configuração de destino, para a qual você especifica o número desejado de nós.

O número de nós de computação é conhecido como um *destino* porque, em algumas situações, o pool pode não alcançar o número desejado de nós. Por exemplo, um pool talvez não alcance o destino se ele atingir a [cota de núcleo](batch-quota-limit.md) da conta do Lote primeiro. Ou então, o pool poderá não alcançar o destino se você tiver aplicado uma fórmula de dimensionamento automático que limita o número máximo de nós ao pool.

Para informações sobre preços de ambos os nós de baixa prioridade e dedicado, consulte [Preços de Lote](https://azure.microsoft.com/pricing/details/batch/).

## <a name="node-size"></a>Tamanho do nó

Ao criar um pool de Lote do Azure, você pode escolher dentre quase todas as famílias de VMs e os tamanhos disponíveis no Azure. O Azure oferece vários tamanhos de VM para diferentes cargas de trabalho, incluindo tamanhos de VM especializados para [HPC](../virtual-machines/sizes-hpc.md) ou [habilitados para GPU](../virtual-machines/sizes-gpu.md). Observe que os tamanhos de nó só podem ser escolhidos no momento em que um pool é criado. Em outras palavras, depois que um pool é criado, seu tamanho de nó não pode ser alterado.

Para obter mais informações, confira [Escolha um tamanho de VM para nós de computação em um pool do Lote do Azure](batch-pool-vm-sizes.md).

## <a name="automatic-scaling-policy"></a>Política de dimensionamento automático

Para as cargas de trabalho dinâmicas, aplique uma fórmula de dimensionamento automático em um pool. O serviço de Lote avaliará periodicamente a fórmula e ajustará dinamicamente o número de nós dentro do pool de acordo com a carga de trabalho e o uso de recursos atuais do cenário de computação. Isso permite reduzir o custo geral de execução do aplicativo usando apenas os recursos necessários e liberando os que você não precisa.

Você habilita o dimensionamento automático escrevendo uma [fórmula de dimensionamento automático](batch-automatic-scaling.md#autoscale-formulas) e associando-a a um pool. O serviço de Lote usa a fórmula para determinar o número de nós no pool de destino para o próximo intervalo de dimensionamento (um intervalo que você pode configurar). Você pode especificar as configurações de dimensionamento automático para um pool ao criá-lo ou habilitar o dimensionamento mais tarde em um pool. Você também pode atualizar as configurações de dimensionamento em um pool com dimensionamento habilitado.

Por exemplo, talvez um trabalho exija que você envie um grande número de tarefas a serem executadas. Você pode atribuir uma fórmula de dimensionamento ao pool que ajusta o número de nós nele com base no número atual de tarefas enfileiradas e a taxa de conclusão das tarefas no trabalho. Periodicamente, o serviço de Lote avalia a fórmula e redimensiona o pool com base na carga de trabalho e em outras configurações da fórmula. O serviço adiciona nós conforme necessário quando há um grande número de tarefas em fila e remove os nós quando não existem tarefas em execução ou fila.

Uma fórmula de dimensionamento pode basear-se nas seguintes métricas:

- **métricas do tempo** são baseadas nas estatísticas coletadas a cada cinco minutos no número de horas especificado.
- **métricas do recurso** são baseadas nos usos da CPU, da largura de banda e da memória, e no número de nós.
- As **métricas de tarefa** são baseadas no estado da tarefa, como *Ativa* (na fila), *Em execução* ou *Concluída*.

Quando o dimensionamento automático diminui o número de nós de computação em um pool, você deve considerar como lidar com as tarefas em execução no momento da operação de redução. Para aceitar isso, o Lote fornece uma [*opção de desalocação do nó*](/rest/api/batchservice/pool/removenodes#computenodedeallocationoption) que você pode incluir em suas fórmulas. Por exemplo, você pode especificar que as tarefas em execução sejam interrompidas imediatamente e colocadas novamente na fila para execução em outro nó ou concluídas antes que o nó seja removido do pool. Observe que definir a opção de desalocação de nó como `taskcompletion` ou `retaineddata` impedirá operações de redimensionamento de pool até que todas as tarefas sejam concluídas ou que todos os períodos de retenção da tarefa tenham expirado, respectivamente.

Para saber mais sobre o dimensionamento automático de um aplicativo, consulte [Dimensionar automaticamente nós de computação em um pool do Lote do Azure](batch-automatic-scaling.md).

> [!TIP]
> Para maximizar a utilização de recursos de computação, defina o número de destino de nós como zero ao fim de um trabalho, mas permita a conclusão das tarefas em execução.

## <a name="task-scheduling-policy"></a>Política de agendamento de tarefas

A opção de configuração [máx. de tarefas por nó](batch-parallel-node-tasks.md) determina o número máximo de tarefas que podem ser executadas em paralelo em cada nó de computação no pool.

A configuração padrão especifica que uma tarefa de cada vez seja executada em um nó, mas há situações em que é útil ter duas ou mais tarefas executadas simultaneamente em um nó. Veja o [cenário de exemplo](batch-parallel-node-tasks.md#example-scenario) no artigo sobre as [tarefas de nó simultâneas](batch-parallel-node-tasks.md) para ver como você pode aproveitar as várias tarefas por nó.

Você também pode especificar um *tipo de preenchimento* que determina se o Lote distribui as tarefas igualmente entre todos os nós de um pool ou empacota cada nó com o número máximo de tarefas antes de atribuir tarefas a outro nó.

## <a name="communication-status"></a>Estado da comunicação

Na maioria dos cenários, as tarefas operam de forma independente e não precisam comunicar-se umas com as outras. No entanto, há alguns aplicativos em que as tarefas precisam se comunicar, como os [cenários MPI](batch-mpi.md).

Você pode configurar um pool para permitir a  **comunicação entre os nós**, de modo que os nós em um pool possam comunicar-se durante a execução. Quando a comunicação entre nós é habilitada, os nós nos pools de Configuração dos Serviços de Nuvem podem comunicar-se uns com os outros nas portas acima de 1100 e os pools de Configuração da Máquina Virtual não restringem o tráfego em nenhuma porta.

Habilitar a comunicação entre nós também afeta a colocação dos nós nos clusters e pode limitar o número máximo de nós em um pool devido às restrições da implantação. Se seu aplicativo não precisar da comunicação entre os nós, o serviço de Lote poderá alocar um número potencialmente grande de nós para o pool a partir de vários clusters e data centers diferentes para permitir uma capacidade maior do processamento paralelo.

## <a name="start-tasks"></a>Tarefas iniciais

Se quiser, adicione uma [tarefa inicial](jobs-and-tasks.md#start-task), que executará em cada nó quando ele ingressa no pool e sempre que um nó é reiniciado ou sua imagem é refeita. A tarefa inicial é particularmente útil para preparar nós de computação para a execução de tarefas, como instalar aplicativos que as tarefas executarão nos nós de computação.

## <a name="application-packages"></a>pacotes de aplicativos

Especifique pacotes de aplicativos para implantar os nós de computação no pool. Os pacotes de aplicativos fornecem uma implantação simplificada e controle de versão dos aplicativos que suas tarefas executam. Os pacotes de aplicativos que você especifica para um pool são instalados em cada nó que ingressa no pool e sempre que um nó é reinicializado ou sua imagem é recriada.

Para saber mais sobre como usar pacotes de aplicativos para implantar os aplicativos nos nós do Lote, veja [Implantar aplicativos em nós de computação com pacotes de aplicativos do Lote](batch-application-packages.md).

## <a name="virtual-network-vnet-and-firewall-configuration"></a>Configuração de firewall e VNet (rede virtual)

Quando você provisiona um pool de nós de computação no Lote, pode associar o pool de uma sub-rede de uma [VNet (rede virtual)](../virtual-network/virtual-networks-overview.md) do Azure. Para usar uma rede virtual do Azure, a API do cliente do Lote deverá usar a autenticação do Azure Active Directory (AD). O suporte ao Lote do Azure para o Azure AD está documentado em [Autenticar soluções do serviço Lote com o Active Directory](batch-aad-auth.md).

### <a name="vnet-requirements"></a>Requisitos de rede virtual

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

Para saber mais sobre como configurar um pool do Lote em uma rede virtual, veja [Criar um pool de máquinas virtuais com sua rede virtual](batch-virtual-network.md).

> [!TIP]
> Para garantir que os endereços IP públicos usados para acessar nós não sejam alterados, você pode [criar um pool com os endereços IP públicos especificados que você controla](create-pool-public-ip.md).

## <a name="pool-and-compute-node-lifetime"></a>Tempo de vida de nó de computação e de pool

Quando projetar sua solução do Lote do Azure, você deverá especificar como e quando os pools são criados, e por quanto tempo os nós de computação nesses pools ficarão disponíveis.

Em uma extremidade do espectro, você pode criar um pool para cada trabalho enviado e excluir o pool logo após o término da execução de suas tarefas. Isso maximiza a utilização porque os nós só serão alocados quando necessário, e serão desligados uma vez que ficarem ociosos. Embora isso signifique que o trabalho deva aguardar até que os nós sejam alocados, é importante observar que as tarefas serão agendadas para execução assim que os nós estiverem alocados individualmente e a tarefa inicial tiver sido concluída. O Lote *não* aguarda até que todos os nós em um pool estejam disponíveis antes de atribuir as tarefas aos nós. Isso garante a máxima utilização de todos os nós disponíveis.

Por outro lado, se ter os trabalhos iniciados imediatamente for a prioridade mais alta, você poderá criar um pool antecipadamente e tornar seus nós disponíveis antes dos trabalhos serem enviados. Nesse cenário, as tarefas podem começar imediatamente, mas os nós poderão ficar ociosos enquanto aguardam a atribuição delas.

Uma abordagem combinada normalmente é usada para lidar com uma carga variável, mas em andamento. Você pode ter um pool no qual vários trabalhos são enviados e pode aumentar ou diminuir o número de nós de acordo com a carga de trabalho. Isso pode ser feito de maneira reativa, com base na carga atual, ou proativamente, se a carga puder ser prevista. Para obter mais informações, veja [Política de dimensionamento automático](#automatic-scaling-policy).

## <a name="autopools"></a>Pools

Um [autopool](/rest/api/batchservice/job/add#autopoolspecification) é um pool criado pelo serviço de lote quando um trabalho é enviado, em vez de ser criado antes dos trabalhos que serão executados no pool. O serviço de lote gerenciará o tempo de vida de um pool autoagrupado de acordo com as características que você especificar. Geralmente, esses pools também são definidos para serem excluídos automaticamente depois que seus trabalhos são concluídos.

## <a name="security-with-certificates"></a>Segurança com certificados

Normalmente, você precisa usar certificados ao criptografar ou descriptografar informações confidenciais para as tarefas, como a chave para uma [conta de Armazenamento do Azure](accounts.md#azure-storage-accounts). Para dar suporte a isso, você pode instalar certificados nos nós. Os segredos criptografados são passados para tarefas por meio dos parâmetros de linha de comando ou incorporados em um dos recursos de tarefa, e os certificados instalados podem ser usados para descriptografá-los.

Você usa a operação [Adicionar certificado](/rest/api/batchservice/certificate/add) (REST do Lote) ou o método [CertificateOperations.CreateCertificate](/dotnet/api/microsoft.azure.batch.certificateoperations) (.NET do Lote) para adicionar um certificado a uma conta do Lote. Então, pode associar o certificado a um pool novo ou existente.

Quando um certificado está associado a um pool, o serviço em lote instala o certificado em cada nó presente no pool. O serviço Lote instala os certificados apropriados quando o nó é inicializado, antes que ele execute qualquer tarefa (incluindo a [tarefa inicial](jobs-and-tasks.md#start-task) e a [tarefa do gerenciador de trabalhos](jobs-and-tasks.md#job-manager-task)).

Se adicionar um certificado a um pool existente, você deve reinicializar seus nós de computação para que os certificados sejam aplicados aos nós.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [trabalhos e tarefas](jobs-and-tasks.md).
- Saiba como [detectar e evitar falhas em operações de segundo plano do pool e do nó ](batch-pool-node-error-checking.md).
