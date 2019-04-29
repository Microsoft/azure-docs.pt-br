---
title: Visão geral para desenvolvedores – Lote do Azure | Microsoft Docs
description: Conheça os recursos do serviço de Lote e suas APIs do ponto de vista de um desenvolvedor.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 416b95f8-2d7b-4111-8012-679b0f60d204
ms.service: batch
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 12/18/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 1107842444ad0ac77ab890f07e65c8b489030461
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60721669"
---
# <a name="develop-large-scale-parallel-compute-solutions-with-batch"></a>Desenvolva soluções de computação paralela em larga escala com o Lote

Nesta visão geral dos componentes principais do serviço de Lote do Azure, vamos abordar os recursos do serviço primário que os desenvolvedores do Lote podem usar para criar soluções de computação paralela em grande escala.

Se você estiver desenvolvendo um aplicativo de computador distribuído ou serviço que faz chamadas diretas da [API REST][batch_rest_api] ou estiver usando um dos [SDKs de Lote](batch-apis-tools.md#azure-accounts-for-batch-development), usará muitos dos recursos analisados neste artigo.

> [!TIP]
> Para obter uma introdução de nível superior do serviço de Lote, consulte os [Fundamentos do Lote do Azure](batch-technical-overview.md). Confira também as [atualizações de serviço Lote](https://azure.microsoft.com/updates/?product=batch) mais recentes.
>
>

## <a name="batch-service-workflow"></a>Fluxo de trabalho de serviço do Lote
O seguinte fluxo de trabalho de alto nível é típico de quase todos os aplicativos e serviços que usam o serviço de Lote para o processamento de cargas de trabalho paralelas:

1. Carregue os **arquivos de dados** que você deseja processar em uma conta de [Armazenamento do Azure ][azure_storage]. O Lote inclui suporte interno para acessar o armazenamento de Blobs do Azure, e as tarefas podem baixar esses arquivos para [nós de computação](#compute-node) quando são executadas.
2. Carregue os **arquivos de aplicativo** que as tarefas executarão. Esses arquivos podem ser binários ou scripts e suas dependências e são executados pelas tarefas em seus trabalhos. As tarefas podem baixar esses arquivos de sua conta de armazenamento ou você pode usar o recurso dos [pacotes de aplicativos](#application-packages) de lote para a implantação e o gerenciamento dos aplicativos.
3. Crie um [pool](#pool) de nós de computação. Quando você cria um pool, pode especificar o número de nós de computação para o pool, seu tamanho e o sistema operacional. Quando cada tarefa no trabalho é executada, ela é atribuída para ser executada em um de nós no pool.
4. Crie um [trabalho](#job). Um trabalho gerencia uma coleção de tarefas. Você associa cada trabalho a um pool específico no qual as tarefas do trabalho serão executadas.
5. Adicione [tarefas](#task) ao trabalho. Cada tarefa executa o aplicativo ou script que você carregou para processar os arquivos de dados que ele baixa de sua conta do Armazenamento. À medida que cada tarefa é concluída, pode carregar sua saída para o Armazenamento do Azure.
6. Monitore o andamento dos trabalhos e recupere a saída de tarefas do Armazenamento do Azure.

As seções a seguir discutem esses e outros recursos do Lote que habilitarão que seu cenário de computação distribuída.

> [!NOTE]
> Você precisa de uma [Conta do Lote](#account) para usar o serviço do Lote. A maioria das soluções do Lote também usa uma conta de [Armazenamento do Azure][azure_storage] associada para o armazenamento de arquivos e a recuperação. 
>
>

## <a name="batch-service-resources"></a>Recursos do serviço de lote
Alguns dos recursos a seguir - contas, nó de computação, pool, trabalhos e tarefa - são necessários para todas as soluções que usam o serviço de Lote. Outros, como agendas de trabalho e pacotes de aplicativos, são recursos úteis, mas opcionais.

* [Conta](#account)
* [Nó de computação](#compute-node)
* [Pool](#pool)
* [Trabalho](#job)
  * [Agendas de trabalho](#scheduled-jobs)
* [Tarefa](#task)
  * [Iniciar tarefa](#start-task)
  * [Tarefa do Gerenciador de Trabalhos](#job-manager-task)
  * [Tarefas de preparação e liberação do trabalho](#job-preparation-and-release-tasks)
  * MPI (tarefa de várias instâncias)
  * [Dependências da tarefa](#task-dependencies)
* [Pacotes de aplicativos](#application-packages)

## <a name="account"></a>Conta
Uma conta do Batch é uma entidade identificada exclusivamente no serviço Batch. Todo o processamento é feito por meio de uma conta do Lote.

Você pode criar uma conta do Lote do Azure usando o [portal do Azure](batch-account-create-portal.md) ou por meio de programação, como com a [biblioteca .NET do Gerenciamento de Lote](batch-management-dotnet.md). Ao criar a conta, você pode associar uma conta de armazenamento do Azure para armazenar dados de entrada e saída ou aplicativos relacionados ao trabalho.

Você pode executar várias cargas de trabalho do Lote em uma única conta do Lote ou distribuir suas cargas de trabalho entre contas do Lote que estão na mesma assinatura mas em diferentes regiões do Azure.

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

## <a name="azure-storage-account"></a>Conta de Armazenamento do Azure

A maioria das soluções do Lote usa o Armazenamento do Azure para armazenar arquivos de recurso e de saída. Por exemplo, as tarefas do Lote (incluindo as tarefas padrão, tarefas iniciais, tarefas de preparação do trabalho e tarefas de liberação do trabalho) especificam tipicamente os arquivos de recurso que residem nas contas de armazenamento.

O Lote é compatível com as seguintes opções de conta de Armazenamento do Azure:

* Contas de v2 (GPv2) de uso geral 
* Contas v1 (GPv1) de uso geral
* Contas de armazenamento de Blobs (atualmente com suporte para pools de configuração de máquina virtual)

Para saber mais sobre as contas de armazenamento, confira [Visão geral da conta de armazenamento do Azure](../storage/common/storage-account-overview.md).

Você pode associar uma conta de armazenamento à sua conta do Lote durante a criação da conta do Lote, ou depois. Considere seus requisitos de desempenho e custo ao escolher uma conta de armazenamento. Por exemplo, as opções de conta de armazenamento de blob e GPv2 dão suporte a [limites maiores de capacidade e escalabilidade](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) em comparação com a GPv1. (Contate o Suporte do Azure para solicitar um aumento do limite de armazenamento.) Essas opções de conta podem melhorar o desempenho das soluções do Lote que contêm uma grande quantidade de tarefas paralelas lidas ou gravadas na conta de armazenamento.

## <a name="compute-node"></a>Nó de computação
Um nó de computação é uma máquina virtual (VM) do Azure ou VM do serviço de nuvem que é dedicada ao processamento de uma parte da carga de trabalho do aplicativo. O tamanho de um nó determina o número de núcleos de CPU, a capacidade da memória e o tamanho do sistema de arquivos local alocado para o nó. Você pode criar pools de nós de Windows ou Linux usando os Serviços de Nuvem do Azure, imagens do [Marketplace de Máquinas Virtuais do Azure][vm_marketplace] ou imagens personalizadas preparadas por você. Consulte o seguinte seção [Pool](#pool) para obter mais informações sobre essas opções.

Os nós podem executar qualquer executável ou script que tenha suporte no ambiente do sistema operacional do nó. Isso inclui \*.exe, \*.cmd, \*.bat e os scripts do PowerShell para Windows e binários, shell e scripts Python para Linux.

Todos os nós de computação no Lote também incluem:

* Uma [estrutura de pastas](#files-and-directories) padrão e as [variáveis de ambiente](#environment-settings-for-tasks) associadas disponíveis para a referência por tarefas.
* **firewall** que são definidas para controlar o acesso.
* [Acesso remoto](#connecting-to-compute-nodes) para os nós do Windows (Remote Desktop Protocol (RDP)) e do Linux (Secure Shell (SSH)).

## <a name="pool"></a>pool
Um pool é uma coleção de nós na qual seu aplicativo é executado. O pool pode ser criado manualmente por você ou automaticamente pelo serviço de Lote quando você especifica o trabalho a ser feito. Você pode criar e gerenciar um pool que atenda às exigências de recursos de seu aplicativo. Um pool pode ser usado somente pela conta do Lote na qual foi criado. Uma conta do Batch pode ter mais de um pool.

Os pools do Lote do Azure se baseiam na plataforma de computação principal do Azure. Eles fornecem alocação em larga escala, instalação de aplicativos, distribuição de dados, monitoramento de integridade e ajuste flexível do número de nós de computação em um pool ([dimensionamento](#scaling-compute-resources)).

Todos os nós adicionados a um pool recebem um nome e um endereço IP exclusivos. Quando um nó é removido de um pool, todas as alterações feitas no sistema operacional ou arquivos são perdidas e seu nome e endereço IP são liberados para o uso futuro. Quando um nó deixa um pool, seu tempo de vida termina.

Ao criar um pool, você pode especificar os seguintes atributos:

- Sistema operacional e versão do nó de computação
- Tipo de nó de computação e número de nós de destino
- Tamanho dos nós de computação
- Política de dimensionamento
- Política de agendamento de tarefas
- Status de comunicação de nós de computação
- Tarefas iniciais para nós de computação
- pacotes de aplicativos
- Configuração de rede

Cada uma dessas configurações é descrita mais detalhadamente nas seções a seguir.

> [!IMPORTANT]
> As contas do Lote têm uma cota padrão que limita o número de núcleos em uma conta do Lote. O número de núcleos corresponde ao número de nós de computação. Você pode encontrar as cotas padrão e instruções sobre como [aumentar uma cota](batch-quota-limit.md#increase-a-quota) em [Cotas e limites para o serviço Lote do Azure](batch-quota-limit.md). Se o pool não está alcançando seu número desejado de nós, a cota de núcleo pode ser o motivo.
>


### <a name="compute-node-operating-system-and-version"></a>Sistema operacional e versão do nó de computação

Quando você cria um pool do Lote, pode especificar a configuração de máquina virtual do Azure e o tipo de sistema operacional que deseja executar em cada nó de computação no pool. Os dois tipos de configuração disponíveis no Lote são:

- A **configuração de máquina virtual**, que especifica que o pool é composto de máquinas virtuais do Azure. Essas máquinas virtuais podem ser criadas de imagens Linux ou Windows. 

    Ao criar um pool baseado na Configuração da Máquina Virtual, você deverá especificar não apenas o tamanho dos nós e a origem das imagens usadas para criá-los, mas também a **referência da imagem da máquina virtual** e a **SKU do agente de nó** do Lote a ser instalada nos nós. Para saber mais sobre como especificar essas propriedades de pool, confira [Provisionar nós de computação do Linux em pools do Lote do Azure](batch-linux-nodes.md). Opcionalmente, você pode anexar um ou mais discos de dados vazios ao pool de máquinas virtuais criadas desde imagens do Marketplace ou incluir discos de dados em imagens personalizadas usadas para criar as máquinas virtuais.

- A **configuração dos Serviços de Nuvem**, que especifica que o pool é composto de nós dos Serviços de Nuvem do Azure. Os Serviços de Nuvem fornecem *somente* nós de computação do Windows.

    Os sistemas operacionais disponíveis para pools de Configuração de Serviços de Nuvem são listados na [matriz de compatibilidade de SDK e versões de SO Convidado do Azure](../cloud-services/cloud-services-guestos-update-matrix.md). Ao criar um pool que contém os nós dos Serviços de Nuvem, você precisa especificar o tamanho do nó e sua *Família de SOs*. Os Serviços de Nuvem são implantados no Azure mais rapidamente do que as máquinas virtuais que executam o Windows. Se você deseja ter pools de nós de computação do Windows, pode achar os Serviços de Nuvem mais vantajosos no desempenho em relação a tempo de implantação.

    * A *Família de SO* também determina quais versões do .NET são instaladas com o sistema operacional.
    * Assim como ocorre com as funções de trabalho nos Serviços de Nuvem, você pode especificar uma *Versão do SO* (para obter mais informações sobre as funções de trabalho, consulte [Visão geral dos Serviços de Nuvem](../cloud-services/cloud-services-choose-me.md)).
    * Assim como ocorre com as funções de trabalho, é recomendável especificar `*` para a *Versão do SO* de forma que os nós sejam automaticamente atualizados e não haja nenhum trabalho necessário para atender as versões recém-lançadas. O caso de uso principal para selecionar uma versão específica do SO é garantir a compatibilidade dos aplicativos, permitindo que os testes de compatibilidade retroativa sejam executados antes de permitir que a versão seja atualizada. Após a validação, a *Versão do SO* para o pool pode ser atualizada e a nova imagem do SO pode ser instalada – as tarefas em execução serão interrompidas e colocadas novamente na fila.

Quando você cria um pool, precisa selecionar o **nodeAgentSkuId** apropriado, dependendo do sistema operacional da imagem base do seu VHD. Você pode obter um mapeamento das IDs de SKU do agente de nó disponível para suas referências de imagem do SO chamando a operação [Listar SKUs do agente de nó](https://docs.microsoft.com/rest/api/batchservice/list-supported-node-agent-skus).


#### <a name="custom-images-for-virtual-machine-pools"></a>Imagens personalizadas para pools de máquina virtual

Para usar uma imagem personalizada, você precisará preparar a imagem generalizando-a. Para obter informações sobre como preparar imagens personalizadas do Linux de VMs do Azure, consulte [Como criar uma imagem de uma máquina virtual ou de um VHD](../virtual-machines/linux/capture-image.md). Para obter informações sobre como preparar imagens personalizadas do Windows de VMs do Azure, confira [Criar uma imagens gerenciada de uma VM generalizada no Azure](../virtual-machines/windows/capture-image-resource.md). 

Para obter requisitos e etapas detalhadas, consulte [Usar uma imagem personalizada para criar um pool de máquinas virtuais](batch-custom-images.md).

#### <a name="container-support-in-virtual-machine-pools"></a>Suporte de contêiner em pools de Máquina Virtual

Ao criar um pool de Configuração de Máquina Virtual usando as APIs do Lote, você pode configurar o pool para executar tarefas em contêineres do Docker. Atualmente, você deve criar o pool usando uma imagem que dê suporte a contêineres do Docker. Use o Windows Server 2016 Datacenter com a imagem de Contêineres do Azure Marketplace, ou fornecer uma imagem de VM personalizada que inclua o Docker Community Edition ou Enterprise Edition e todos os drivers necessários. As configurações de pool devem incluir uma [configuração de contêiner](/rest/api/batchservice/pool/add) que copie as imagens de contêiner para as máquinas virtuais quando o pool é criado. As tarefas executadas no pool podem fazer referência a imagens de contêiner e a opções de execução do contêiner.

Para obter mais informações, consulte [Executar aplicativos de contêiner do Docker no Lote do Azure](batch-docker-container-workloads.md).

## <a name="compute-node-type-and-target-number-of-nodes"></a>Tipo de nó de computação e número de nós de destino

Quando você cria um pool, você pode especificar os tipos de nós de computação que você deseja e o número de destino para cada um. Os dois tipos de nós de computação são:

- **Nós de computação dedicados.** Nós de computação dedicados são reservados para suas cargas de trabalho. Eles são mais caros que os nós de baixa prioridade, mas é garantido que nunca sofrerão preempção.

- **Nós de computação de baixa prioridade.** Nós de baixa prioridade aproveitam a capacidade excedente no Azure para executar cargas de trabalho de Lote. Nós de baixa prioridade são mais baratos por hora do que nós dedicados e permitem cargas de trabalho que exigem muita capacidade de computação. Para obter mais informações, consulte [Usar VMs de baixa prioridade com o Lote](batch-low-pri-vms.md).

    Pode ocorrer preempção de nós de computação de baixa prioridade quando o Azure tem capacidade excedente insuficiente. Se ocorrer preempção de um nó durante a execução de tarefas, as tarefas serão retiradas da fila e executadas novamente depois que um nó de computação ficar disponível novamente. Nós de baixa prioridade são uma boa opção para cargas de trabalho nas quais o tempo para conclusão do trabalho é flexível e o trabalho é distribuído entre muitos nós. Antes de decidir usar nós de baixa prioridade para seu cenário, verifique se os trabalhos perdidos devido a preempção serão poucos e fáceis de se recriar.

    
Você pode ter nós de computação de baixa prioridade e dedicados no mesmo pool. Cada tipo de nó &mdash; dedicado e de baixa prioridade &mdash; tem sua própria configuração de destino, para que você pode especificar o número desejado de nós. 
    
O número de nós de computação é conhecido como um *destino* porque, em algumas situações, o pool pode não alcançar o número desejado de nós. Por exemplo, um pool talvez não alcance o destino se ele atingir a [cota de núcleo](batch-quota-limit.md) da conta do Lote primeiro. Ou então, o pool poderá não alcançar o destino se você tiver aplicado uma fórmula de dimensionamento automático que limita o número máximo de nós ao pool.

Para informações sobre preços de ambos os nós de computação de baixa prioridade e dedicado, consulte [Preços de Lote](https://azure.microsoft.com/pricing/details/batch/).

### <a name="size-of-the-compute-nodes"></a>Tamanho dos nós de computação

Ao criar um pool de Lote do Azure, você pode escolher dentre quase todas as famílias de VMs e os tamanhos disponíveis no Azure. O Azure oferece vários tamanhos de VM para diferentes cargas de trabalho, incluindo tamanhos de VM especializados para [HPC](../virtual-machines/linux/sizes-hpc.md) ou [habilitados para GPU](../virtual-machines/linux/sizes-gpu.md). 

Para obter mais informações, confira [Escolha um tamanho de VM para nós de computação em um pool do Lote do Azure](batch-pool-vm-sizes.md).

### <a name="scaling-policy"></a>Política de dimensionamento

Para as cargas de trabalho dinâmicas, você pode gravar e aplicar uma [fórmula de dimensionamento automático](#scaling-compute-resources) em um pool. O serviço de Lote avalia periodicamente a fórmula e ajusta o número de nós no pool com base em vários parâmetros do pool, trabalho e tarefa que você pode especificar.

### <a name="task-scheduling-policy"></a>Política de agendamento de tarefas

A opção de configuração [máx. de tarefas por nó](batch-parallel-node-tasks.md) determina o número máximo de tarefas que podem ser executadas em paralelo em cada nó de computação no pool.

A configuração padrão especifica que uma tarefa de cada vez seja executada em um nó, mas há situações em que é útil ter duas ou mais tarefas executadas simultaneamente em um nó. Veja o [cenário de exemplo](batch-parallel-node-tasks.md#example-scenario) no artigo sobre as [tarefas de nó simultâneas](batch-parallel-node-tasks.md) para ver como você pode aproveitar as várias tarefas por nó.

Você também pode especificar um *tipo de preenchimento* que determina se o Lote distribui as tarefas igualmente entre todos os nós de um pool ou empacota cada nó com o número máximo de tarefas antes de atribuir tarefas a outro nó.

### <a name="communication-status-for-compute-nodes"></a>Status de comunicação de nós de computação

Na maioria dos cenários, as tarefas operam de forma independente e não precisam comunicar-se umas com as outras. No entanto, há alguns aplicativos em que as tarefas precisam se comunicar, como os [cenários MPI](batch-mpi.md).

Você pode configurar um pool para permitir a  **comunicação entre os nós**, de modo que os nós em um pool possam comunicar-se durante a execução. Quando a comunicação entre nós é habilitada, os nós nos pools de Configuração dos Serviços de Nuvem podem comunicar-se uns com os outros nas portas acima de 1100 e os pools de Configuração da Máquina Virtual não restringem o tráfego em nenhuma porta.

Observe que habilitar a comunicação entre nós também afeta a colocação dos nós nos clusters e pode limitar o número máximo de nós em um pool devido às restrições da implantação. Se seu aplicativo não precisar da comunicação entre os nós, o serviço de Lote poderá alocar um número potencialmente grande de nós para o pool a partir de vários clusters e data centers diferentes para permitir uma capacidade maior do processamento paralelo.

### <a name="start-tasks-for-compute-nodes"></a>Tarefas iniciais para nós de computação

A *tarefa inicial* opcional é executada em cada nó quando ele ingressa no pool e sempre que um nó é reiniciado ou sua imagem é refeita. A tarefa inicial é particularmente útil para preparar nós de computação para a execução de tarefas, como instalar aplicativos que as tarefas executarão nos nós de computação.

### <a name="application-packages"></a>pacotes de aplicativos

Você pode especificar [pacotes de aplicativos](#application-packages) para implantar os nós de computação no pool. Os pacotes de aplicativos fornecem uma implantação simplificada e controle de versão dos aplicativos que suas tarefas executam. Os pacotes de aplicativos que você especifica para um pool são instalados em cada nó que ingressa no pool e sempre que um nó é reinicializado ou sua imagem é recriada.

> [!NOTE]
> Os pacotes de aplicativos têm suporte em todos os pools do Lote criados após 5 de julho de 2017. Elas só terão suporte em pools do Lote criados entre 10 de março de 2016 e 5 de julho de 2017 se o pool tiver sido criado usando uma configuração de Serviço de Nuvem. Os pools do Lote criados antes de 10 de março de 2016 não dão suporte a pacotes de aplicativos. Para saber mais sobre como usar pacotes de aplicativos para implantar os aplicativos nos nós do Lote, veja [Implantar aplicativos em nós de computação com pacotes de aplicativos do Lote](batch-application-packages.md).
>
>

### <a name="network-configuration"></a>Configuração de rede

Você pode especificar a sub-rede de uma [rede virtual (VNet)](../virtual-network/virtual-networks-overview.md) do Azure em que nós de computação do pool devem ser criados. Confira a seção Configuração da rede do pool para obter mais informações.


## <a name="job"></a>Trabalho
Um trabalho é uma coleção de tarefas. Ele gerencia como a computação é realizada por suas tarefas nos nós de computação em um pool.

* O trabalho especifica o **pool** no qual o trabalho é executado. Você pode criar um novo pool para cada trabalho ou usar um pool para vários trabalhos. Você pode criar um pool para cada trabalho associado a um agendamento de trabalho ou para todos os trabalhos associados a um agendamento de trabalho.
* Você pode especificar uma **prioridade de trabalho**opcional. Quando um trabalho é enviado com uma prioridade mais alta que os trabalhos em andamento atualmente, as tarefas do trabalho com prioridade mais alta são inseridas na fila antes das tarefas dos trabalhos com prioridade mais baixa. As tarefas com prioridade mais baixa que já estejam em execução não são antecipadas.
* Você pode usar as restrições do **trabalho** para especificar certos limites para seus trabalhos:

    Você pode definir uma **hora do relógio máxima**, de modo que se um trabalho for executado por mais tempo que a hora do relógio máxima especificada, o trabalho e todas as suas tarefas serão encerrados.

    O Lote pode detectar e repetir as tarefas com falha. Você pode especificar o **número máximo de tentativas de tarefa** como uma restrição, inclusive se uma tarefa é repetida *sempre* ou *nunca* é repetida. Repetir uma tarefa significa que a tarefa é colocada na fila para ser executada novamente.
* O aplicativo do cliente pode adicionar tarefas a um trabalho ou você pode especificar uma [tarefa do gerenciador de trabalhos](#job-manager-task). Uma tarefa do gerenciador de trabalhos contém as informações necessárias para criar as tarefas necessárias para um trabalho, com a tarefa do gerenciador de trabalhos sendo executada em um de nós de computação no pool. A tarefa do gerenciador de trabalhos é tratada especificamente pelo Lote – ela é colocada na fila assim que o trabalho é criado e é reiniciada, caso falhe. Uma tarefa do gerenciador de trabalhos é *necessária* para os trabalhos criados por um [agendamento de trabalho](#scheduled-jobs) , pois é a única maneira de definir as tarefas antes do trabalho ser instanciado.
* Por padrão, os trabalhos permanecem no estado ativo quando todas as tarefas no trabalho são concluídas. Você pode alterar esse comportamento para que o trabalho seja encerrado automaticamente quando todas as tarefas no trabalho forem concluídas. Defina a propriedade **onAllTasksComplete** do trabalho ([OnAllTasksComplete][net_onalltaskscomplete] no .NET do Lote) para *terminatejob* para encerrar automaticamente o trabalho quando todas as tarefas estão no estado concluído.

    Observe que o serviço de Lote considera que um trabalho *sem* tarefas tem todas as suas tarefas concluídas. Portanto, essa opção é mais comumente usada com uma [tarefa do gerenciador de trabalhos](#job-manager-task). Se você quiser usar o encerramento automático de trabalho sem um gerenciador de trabalhos, defina inicialmente a propriedade **onAllTasksComplete** de um novo trabalho como *noaction*. Depois, defina-a como *terminatejob* somente depois que você terminar de adicionar tarefas ao trabalho.

### <a name="job-priority"></a>prioridade de trabalho
Você pode atribuir uma prioridade a trabalhos criados no Lote. O serviço Lote usa o valor da prioridade do trabalho para determinar a ordem de agendamento dos trabalhos em uma conta (isso não deve ser confundido com um [trabalho agendado](#scheduled-jobs)). Os valores de prioridade variam de -1000 a 1000, em que -1000 é a prioridade mais baixa e 1000 a mais alta. Para atualizar a prioridade de um trabalho, chame a operação [Atualizar as propriedades de um trabalho] [rest_update_job] \(REST do Lote) ou modifique a propriedade [CloudJob.Priority] [net_cloudjob_priority] \(.NET do Lote).

Em uma mesma conta, os trabalhos com prioridade mais alta têm precedência no agendamento sobre aqueles com prioridade mais baixa. Um trabalho com valor de prioridade mais alto em uma conta não tem precedência no agendamento sobre outro trabalho com valor de prioridade mais baixo em uma conta diferente.

O plano de trabalho em pools é independente. Entre pools diferentes, não é garantido que um trabalho com prioridade mais alta seja agendado primeiro, caso faltem nós ociosos em seu pool associado. No mesmo pool, trabalhos com o mesmo nível de prioridade têm a mesma chance de ser agendados.

### <a name="scheduled-jobs"></a>Trabalhos agendados
As [Agendas de trabalho][rest_job_schedules] permitem criar trabalhos recorrentes dentro do serviço de Lote. Um plano de trabalho especifica quando executar trabalhos e inclui as especificações para os trabalhos a serem executados. Você pode especificar a duração do agendamento - quanto tempo e quando o agendamento entra em vigor – e com que frequência os trabalhos são criados durante o período agendado.

## <a name="task"></a>Tarefa
Uma tarefa é uma unidade de computação que está associada a um trabalho. Ela é executada em um nó. As tarefas são atribuídas a um nó para execução ou estão na fila até que um nó fique livre. Resumindo, uma tarefa executa um ou mais programas ou scripts em um nó de computação para executar o trabalho necessário.

Ao criar uma tarefa, você pode especificar:

* A **linha de comando** da tarefa. Essa é a linha de comando que executa o aplicativo ou script nos nós de computação.

    É importante observar que a linha de comando realmente não é executada em um shell. Portanto, não é possível aproveitar nativamente os recursos do shell, como a expansão da [variável de ambiente](#environment-settings-for-tasks) (isso inclui `PATH`). Para aproveitar esses recursos, você deve chamar o shell na linha de comando – por exemplo, iniciando `cmd.exe` nos nós do Windows ou `/bin/sh` no Linux:

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Se as tarefas precisarem executar um aplicativo ou script que não esteja no `PATH` do nó ou fazer referência às variáveis de ambiente, chame o shell explicitamente na linha de comando da tarefa.
* **Arquivos de recursos** que contêm os dados a serem processados. Esses arquivos são copiados automaticamente para o nó a partir do armazenamento de Blobs em uma conta de Armazenamento do Azure, antes da linha de comando da tarefa ser executada. Para obter mais informações, consulte as seções [Tarefa inicial](#start-task) e [Arquivos e diretórios](#files-and-directories).
* As **variáveis de ambiente** que são exigidas pelo aplicativo. Para obter mais informações, consulte a seção [Configurações do ambiente para tarefas](#environment-settings-for-tasks) .
* As **restrições** de acordo com as quais a tarefa deve ser executada. Por exemplo, as restrições incluem o tempo máximo que a tarefa pode ser executada, o número máximo de vezes que uma tarefa com falha deve ser repetida e o tempo máximo que os arquivos no diretório de trabalho da tarefa são mantidos.
* **Pacotes de aplicativos** para implantar no nó de computação no qual a tarefa está agendada para ser executada. [Application packages](#application-packages) fornecem uma implantação simplificada e controle de versão dos aplicativos que suas tarefas executam. Os pacotes de aplicativos de nível de tarefa são especialmente úteis em ambientes de pool compartilhado, em que diferentes trabalhos são executados em um pool e o pool não é excluído quando um trabalho é concluído. Se o trabalho tiver menos tarefas do que os nós no pool, pacotes de aplicativos de tarefa poderão minimizar a transferência de dados, pois o aplicativo é implantado apenas para os nós que executam tarefas.
* Uma referência de **imagem de contêiner** no Hub do Docker ou um registro particular e configurações adicionais para criar um contêiner do Docker no qual a tarefa é executada no nó. Só será possível especificar essas informações se o pool for definido com uma configuração de contêiner.

> [!NOTE]
> O tempo de vida máximo de uma tarefa, desde quando ela é adicionada ao trabalho até sua conclusão, é de 180 dias. As tarefas concluídas persistem por 7 dias. Os dados das tarefas não concluídas dentro do tempo de vida máximo não podem ser acessados.

Além das tarefas que você pode definir para realizar computação em um nó, as tarefas especiais a seguir também são fornecidas pelo serviço Lote:

* [Iniciar tarefa](#start-task)
* [Tarefa do Gerenciador de Trabalhos](#job-manager-task)
* [Tarefas de preparação e liberação do trabalho](#job-preparation-and-release-tasks)
* MPI (tarefas de várias instâncias)
* [Dependências da tarefa](#task-dependencies)

### <a name="start-task"></a>Iniciar tarefa
Associando uma **tarefa inicial** a um pool, você pode preparar o ambiente operacional de seus nós. Por exemplo, você pode executar ações como instalar os aplicativos que suas tarefas executarão e iniciar os processos em segundo plano. A tarefa inicial é executada sempre que um nó inicia, contanto que ele permaneça no pool - incluindo quando o nó é adicionado pela primeira vez ao pool e quando ele é reiniciado ou sua imagem é recriada.

O principal benefício da tarefa inicial é que ela pode conter todas as informações necessárias para configurar um nó de computação e instalar os aplicativos necessários para a execução da tarefa. Portanto, aumentar o número de nós em um pool é tão simples quanto especificar a nova contagem de nós de destino. A tarefa inicial fornece ao serviço de Lote as informações necessárias para configurar os novos nós e prepará-los para a aceitação das tarefas.

Como com qualquer tarefa em Lote do Azure, você pode especificar uma lista de **arquivos de recursos** no [Armazenamento do Azure][azure_storage], além de uma **linha de comando** a ser executada. Primeiro, o serviço de Lote copia os arquivos de recursos para o nó no Armazenamento do Azure, depois, executa a linha de comando. Para uma tarefa inicial do pool, a lista de arquivos normalmente contém o aplicativo da tarefa e suas dependências.

No entanto, ela também pode incluir os dados de referência a serem usados por todas as tarefas em execução no nó de computação. Por exemplo, a linha de comando da tarefa inicial pode executar uma `robocopy` operação para copiar os arquivos do aplicativo (que foram especificados como arquivos de recurso e baixados para o nó) do [diretório de trabalho](#files-and-directories) da tarefa inicial para a [pasta compartilhada](#files-and-directories), em seguida, executar um MSI ou `setup.exe`.

Geralmente é desejável que o serviço de Lote aguarde a conclusão da tarefa inicial antes de considerar o nó pronto para ter tarefas atribuídas, mas isso pode ser configurado.

Se uma tarefa de inicialização falhar em um nó de computação, o estado do nó será atualizado para refletir a falha e o nó não tem nenhuma tarefa atribuída a ele. Uma tarefa inicial poderá falhar se houver um problema ao copiar seus arquivos de recursos do armazenamento ou se o processo executado por sua linha de comando retornar um código de saída diferente de zero.

Se você adicionar ou atualizar a tarefa inicial para um pool existente , deverá reinicializar seus nós de computação para que a tarefa inicial seja aplicada aos nós.

>[!NOTE]
> O lote limita o tamanho total de uma tarefa de inicialização, que inclui arquivos de recurso e variáveis de ambiente. Se você precisar reduzir o tamanho de uma tarefa de inicialização, poderá usar uma das duas abordagens:
>
> 1. Você pode usar pacotes de aplicativos para distribuir aplicativos ou dados em cada nó no pool do Lote. Para saber mais sobre pacotes de aplicativos, veja [Implantar aplicativos em nós de computação com pacotes de aplicativos do Lote](batch-application-packages.md).
> 2. Você pode criar um arquivo compactado que contém os arquivos de aplicativos manualmente. Carregue seu arquivo compactado no Armazenamento do Azure como um blob. Especifique o arquivo compactado como um arquivo de recurso para a tarefa de início. Antes de executar a linha de comando para a tarefa de início, descompacte o arquivo da linha de comando. 
>
>    Para descompactar o arquivo, você pode usar sua ferramenta de arquivamento preferida. Você precisará incluir a ferramenta usada para descompactar o arquivo como um arquivo de recurso para a tarefa inicial.
>
>

### <a name="job-manager-task"></a>Tarefa do Gerenciador de Trabalhos
Você geralmente usa uma **tarefa do gerenciador de trabalhos** para controlar e/ou monitorar a execução do trabalho — por exemplo, para criar e enviar tarefas para um trabalho, determinar as tarefas adicionais a executar e quando o trabalho é concluído. No entanto, uma tarefa do gerenciador de trabalhos não está limitada a essas atividades. É uma tarefa completa que pode executar as ações necessárias para o trabalho. Por exemplo, uma tarefa do gerenciador de trabalhos pode baixar um arquivo especificado como um parâmetro, analisar o conteúdo desse arquivo e enviar tarefas adicionais com base no conteúdo.

Uma tarefa do gerenciador de trabalho é iniciada antes de todas as outras tarefas. Ela fornece os seguintes recursos:

* Ela é criada automaticamente pelo serviço Lote quando o trabalho é criado.
* É agendada para execução antes das outras tarefas em um trabalho.
* O nó associado é o último a ser removido de um pool quando o pool está sendo reduzido.
* Seu término pode ser vinculado ao término de todas as tarefas existentes no trabalho.
* Uma tarefa do gerenciador de trabalhos tem a prioridade mais alta quando precisa ser reiniciada. Se não houver um nó ocioso disponível, o serviço de Lote poderá encerrar uma das outras tarefas em execução no pool para liberar espaço para a tarefa do gerenciador de trabalhos ser executada.
* Uma tarefa de gerenciador de trabalhos em um trabalho não tem prioridade sobre tarefas em outros trabalhos. Entre diferentes trabalhos, somente as prioridades de nível de trabalho são observadas.

### <a name="job-preparation-and-release-tasks"></a>Tarefas de preparação e liberação do trabalho
O Lote fornece tarefas de preparação do trabalho para a instalação de execução pré-trabalho. As tarefas de liberação do trabalho são para a manutenção ou a limpeza pós-trabalho.

* **Tarefa de preparação de trabalho**: a tarefa de preparação do trabalho é executada em todos os nós de computação agendados para executar tarefas, antes que qualquer outra tarefa do trabalho seja executada. É possível usar a tarefa de preparação do trabalho para copiar os dados compartilhados por todas as tarefas, mas é exclusiva para o trabalho, por exemplo.
* **Tarefa de liberação de trabalho**: quando um trabalho é concluído, a tarefa de liberação do trabalho é executada em cada nó no pool que executou pelo menos uma tarefa. É possível usar a tarefa de liberação do trabalho para excluir os dados copiados pela tarefa de preparação do trabalho ou compactar e carregar os dados do log de diagnóstico, por exemplo.

As tarefas de preparação e liberação do trabalho permitem especificar uma linha de comando a ser executada quando a tarefa é chamada. Elas oferecem recursos, como download de arquivo, execução elevada, variáveis de ambiente personalizadas, duração máxima da execução, contagem de repetição e tempo de retenção do arquivo.

Para saber mais sobre tarefas de preparação e de liberação de trabalho, consulte [Executar tarefas de preparação e de conclusão de trabalhos em nós de computação do Lote do Azure](batch-job-prep-release.md).

### <a name="multi-instance-task"></a>Tarefa de várias instâncias
Uma [tarefa de várias instâncias](batch-mpi.md) é a que é configurada para ser executada simultaneamente em mais de um nó de computação. Com as tarefas de várias instâncias, você pode habilitar os cenários de computação de alto desempenho, que requerem um grupo de nós de computação alocados juntos para processar uma única carga de trabalho (como a Interface de Troca de Mensagens (MPI)).

Para obter uma análise detalhada sobre como executar os trabalhos da MPI no Lote usando a biblioteca .NET do Lote, confira [Usar tarefas de várias instâncias para executar os aplicativos da MPI (Interface de Troca de Mensagens) no Lote do Azure](batch-mpi.md).

### <a name="task-dependencies"></a>Dependências da tarefa
As [dependências de tarefas](batch-task-dependencies.md), como o nome indica, permitem especificar que uma tarefa depende da conclusão de outras tarefas antes de sua execução. Este recurso fornece suporte para situações em que uma tarefa "downstream" consome a saída de uma tarefa "upstream" - ou quando uma tarefa upstream executa alguma inicialização necessária para uma tarefa downstream. Para usar esse recurso, primeiro você deve habilitar as dependências em seu trabalho do Lote. Em seguida, para cada tarefa que dependa de outra (ou de muitas outras), especifique as tarefas das quais essa tarefa depende.

Com as dependências de tarefas, você pode configurar cenários como o seguinte:

* A *tarefaB* depende da *tarefaA* (a *tarefaB* não começará sua execução até que a *tarefaA* tenha terminado).
* A *tarefaC* depende da *tarefaA* e da *tarefaB*.
* A *tarefaD* depende de várias tarefas, como as tarefas de *1* a *10*, antes de ser executada.

Confira as [Dependências da tarefa no Lote do Azure](batch-task-dependencies.md) e o exemplo de código [TaskDependencies][github_sample_taskdeps] no repositório GitHub [azure-batch-samples][github_samples] para obter mais detalhes sobre esse recurso.

## <a name="environment-settings-for-tasks"></a>Configurações do ambiente para tarefas
Cada tarefa executada pelo serviço Lote tem acesso a variáveis de ambiente definidas em nós de computação. Isso inclui as variáveis de ambiente definidas pelo serviço de Lote ([service-defined][msdn_env_vars]) e as variáveis de ambiente personalizadas que você pode definir para suas tarefas. Os aplicativos e scripts executados pelas tarefas têm acesso a essas variáveis de ambiente durante a execução.

Você pode definir variáveis de ambiente personalizadas no nível de tarefa ou de trabalho populando a propriedade *configurações de ambiente* para essas entidades. Por exemplo, consulte a operação [Adicionar uma tarefa a um trabalho][rest_add_task] (API REST do Lote) ou as propriedades [CloudTask.EnvironmentSettings][net_cloudtask_env] e [CloudJob.CommonEnvironmentSettings][net_job_env] no .NET do Lote.

Seu aplicativo cliente ou serviço pode obter as variáveis de ambiente da tarefa, definidas pelo serviço e personalizadas, usando a operação [Obter informações sobre uma tarefa][rest_get_task_info] (REST do Lote) ou acessando a propriedade [CloudTask.EnvironmentSettings][net_cloudtask_env] (.NET do Lote). Os processos em execução em um nó de computação podem acessar essas e outras variáveis de ambiente no nó, por exemplo, usando a sintaxe familiar do `%VARIABLE_NAME%` (Windows) ou `$VARIABLE_NAME` (Linux).

Você pode encontrar uma lista completa de todas as variáveis de ambiente definidas pelo serviço em [Variáveis de ambiente do nó de computação][msdn_env_vars].

## <a name="files-and-directories"></a>Arquivos e diretórios
Cada tarefa tem um *diretório de trabalho* em que ela cria zero ou mais arquivos e diretórios. Esse diretório de trabalho pode ser usado para armazenar o programa executado pela tarefa, os dados que ele processa e a saída do processamento executado. Todos os arquivos e diretórios de uma tarefa são pertencentes ao usuário de tarefa.

O serviço de Lote exibe uma parte do sistema de arquivos em um nó como o *diretório-raiz*. As tarefas podem acessar esse diretório-raiz referenciando a variável de ambiente `AZ_BATCH_NODE_ROOT_DIR` . Para saber mais sobre como usar as variáveis de ambiente, consulte [Configurações de ambiente para tarefas](#environment-settings-for-tasks).

O diretório raiz contém a seguinte estrutura de diretório:

![Estrutura de diretórios do nó de computação][1]

* **compartilhado**: esse diretório fornece acesso de leitura/gravação a *todas* as tarefas executadas em um nó. Qualquer tarefa executada no nó pode criar, ler, atualizar e excluir arquivos nesse diretório. As tarefas podem acessar esse diretório referenciando a variável de ambiente `AZ_BATCH_NODE_SHARED_DIR` .
* **inicialização**: esse diretório é usado por uma tarefa inicial como seu diretório de trabalho. Todos os arquivos que são baixados para o nó pela tarefa de inicialização são armazenados aqui. A tarefa inicial pode criar, ler, atualizar e excluir arquivos nesse diretório. As tarefas podem acessar esse diretório referenciando a variável de ambiente `AZ_BATCH_NODE_STARTUP_DIR` .
* **Tarefas**: um diretório é criado para cada tarefa executada no nó. É acessado referenciando a variável de ambiente `AZ_BATCH_TASK_DIR` .

    Em cada diretório de tarefas, o serviço Lote cria um diretório de trabalho (`wd`) cujo caminho exclusivo é especificado pela variável de ambiente `AZ_BATCH_TASK_WORKING_DIR`. Esse diretório oferece acesso de leitura/gravação à tarefa. A tarefa pode criar, ler, atualizar e excluir arquivos contidos nesse diretório. Esse diretório é mantido com base na restrição *RetentionTime* especificada para a tarefa.

    `stdout.txt` e `stderr.txt`: esses arquivos são gravados na pasta de tarefas durante a execução da tarefa.

> [!IMPORTANT]
> Quando um nó é removido do pool, *todos* os arquivos armazenados no nó são removidos.
>
>

## <a name="application-packages"></a>pacotes de aplicativos
O recurso dos [pacotes de aplicativos](batch-application-packages.md) fornece um gerenciamento e implantação fáceis dos aplicativos para os nós de computação em seus pools. Você pode carregar e gerenciar várias versões dos aplicativos executados por suas tarefas, incluindo seus binários e arquivos de suporte. Então, você pode implantar automaticamente um ou mais desses aplicativos nos nós de computação em seu pool.

Você pode especificar os pacotes de aplicativos no nível do pool e de tarefa. Quando você especifica os pacotes de aplicativos do pool, o aplicativo é implantado para todos os nós no pool. Quando você especifica os pacotes de aplicativos de tarefa, o aplicativo é implantado apenas para nós que estão agendados para execução de pelo menos uma das tarefas do trabalho, antes que a linha de comando da tarefa seja executada.

O Lote lida com os detalhes de como trabalhar com o Armazenamento do Azure para armazenar pacotes de aplicativos e implantá-los para nós de computação. Portanto, a sobrecarga de gerenciamento e código pode ser simplificada.

Para saber mais sobre o recurso do pacote de aplicativos, confira a [Implantar aplicativos em nós de computação com pacotes de aplicativos do Lote do Azure](batch-application-packages.md).

> [!NOTE]
> Se você adicionar pacotes de aplicativos do pool a um pool *existente* , deverá reinicializar seus nós de computação para que os pacotes de aplicativos sejam implantados nos nós.
>
>

## <a name="pool-and-compute-node-lifetime"></a>Tempo de vida de nó de computação e de pool
Ao projetar sua solução do Lote do Azure, você deve tomar uma decisão de design sobre como e quando os pools são criados, e por quanto tempo os nós de computação nesses pools ficarão disponíveis.

Em uma extremidade do espectro, você pode criar um pool para cada trabalho enviado e excluir o pool logo após o término da execução de suas tarefas. Isso maximiza a utilização porque os nós só serão alocados quando necessário, e desligados assim que ficarem ociosos. Embora isso signifique que o trabalho deva aguardar até que os nós sejam alocados, é importante observar que as tarefas serão agendadas para execução assim que os nós estiverem disponíveis individualmente, alocadas e a tarefa inicial tiver sido concluída. O Lote *não* aguarda até que todos os nós em um pool estejam disponíveis antes de atribuir as tarefas aos nós. Isso garante a máxima utilização de todos os nós disponíveis.

Por outro lado, se ter os trabalhos iniciados imediatamente for a prioridade mais alta, você poderá criar um pool antecipadamente e tornar seus nós disponíveis antes dos trabalhos serem enviados. Nesse cenário, as tarefas podem começar imediatamente, mas os nós poderão ficar ociosos enquanto aguardam a atribuição delas.

Uma abordagem combinada normalmente é usada para lidar com uma carga variável, mas em andamento. Você pode ter um pool para o qual vários trabalhos são enviados, mas pode aumentar ou diminuir o número de nós de acordo com a carga de trabalho (confira [Dimensionando os recursos de computação](#scaling-compute-resources) na seção a seguir). Isso pode ser feito de maneira reativa, com base na carga atual, ou proativamente, se a carga puder ser prevista.

## <a name="virtual-network-vnet-and-firewall-configuration"></a>Configuração de firewall e VNet (rede virtual) 

Quando você provisiona um pool de nós de computação no Lote, pode associar o pool de uma sub-rede de uma [VNet (rede virtual)](../virtual-network/virtual-networks-overview.md) do Azure. Para usar uma rede virtual do Azure, a API do cliente do Lote deverá usar a autenticação do Azure Active Directory (AD). O suporte ao Lote do Azure para o Azure AD está documentado em [Autenticar soluções do serviço Lote com o Active Directory](batch-aad-auth.md).  

### <a name="vnet-requirements"></a>Requisitos de rede virtual
[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

Para saber mais sobre como configurar um pool do Lote em uma rede virtual, veja [Criar um pool de máquinas virtuais com sua rede virtual](batch-virtual-network.md).

## <a name="scaling-compute-resources"></a>Dimensionando os recursos de computação
Com o [dimensionamento automático](batch-automatic-scaling.md), você pode deixar que o serviço de Lote ajuste dinamicamente o número de nós de computação em um pool de acordo com a carga de trabalho e o uso de recursos atuais do cenário de computação. Isso permite reduzir o custo geral de execução do aplicativo usando apenas os recursos necessários e liberando os que você não precisa.

Você habilita o dimensionamento automático escrevendo uma [fórmula de dimensionamento automático](batch-automatic-scaling.md#automatic-scaling-formulas) e associando-a a um pool. O serviço de Lote usa a fórmula para determinar o número de nós no pool de destino para o próximo intervalo de dimensionamento (um intervalo que você pode configurar). Você pode especificar as configurações de dimensionamento automático para um pool ao criá-lo ou habilitar o dimensionamento mais tarde em um pool. Você também pode atualizar as configurações de dimensionamento em um pool com dimensionamento habilitado.

Por exemplo, talvez um trabalho exija que você envie um grande número de tarefas a serem executadas. Você pode atribuir uma fórmula de dimensionamento ao pool que ajusta o número de nós nele com base no número atual de tarefas enfileiradas e a taxa de conclusão das tarefas no trabalho. Periodicamente, o serviço de Lote avalia a fórmula e redimensiona o pool com base na carga de trabalho e em outras configurações da fórmula. O serviço adiciona nós conforme necessário quando há um grande número de tarefas em fila e remove os nós quando não existem tarefas em execução ou fila.

Uma fórmula de dimensionamento pode basear-se nas seguintes métricas:

* **métricas do tempo** são baseadas nas estatísticas coletadas a cada cinco minutos no número de horas especificado.
* **métricas do recurso** são baseadas nos usos da CPU, da largura de banda e da memória, e no número de nós.
* As **métricas de tarefa** são baseadas no estado da tarefa, como *Ativa* (na fila), *Em execução* ou *Concluída*.

Quando o dimensionamento automático diminui o número de nós de computação em um pool, você deve considerar como lidar com as tarefas em execução no momento da operação de redução. Para aceitar isso, o Lote fornece uma *opção de desalocação do nó* que você pode incluir em suas fórmulas. Por exemplo, você pode especificar que as tarefas em execução sejam interrompidas imediatamente e colocadas novamente na fila para execução em outro nó ou concluídas antes que o nó seja removido do pool.

Para saber mais sobre o dimensionamento automático de um aplicativo, consulte [Dimensionar automaticamente nós de computação em um pool do Lote do Azure](batch-automatic-scaling.md).

> [!TIP]
> Para maximizar a utilização de recursos de computação, defina o número de destino de nós como zero ao fim de um trabalho, mas permita a conclusão das tarefas em execução.
>
>

## <a name="security-with-certificates"></a>Segurança com certificados
Normalmente, você precisa usar certificados ao criptografar ou descriptografar informações confidenciais para as tarefas, como a chave para uma [conta de Armazenamento do Azure][azure_storage]. Para dar suporte a isso, você pode instalar certificados nos nós. Os segredos criptografados são passados para tarefas por meio dos parâmetros de linha de comando ou incorporados em um dos recursos de tarefa, e os certificados instalados podem ser usados para descriptografá-los.

Você usa a operação [Adicionar certificado][rest_add_cert] (REST do Lote) ou o método [CertificateOperations.CreateCertificate][net_create_cert] (.NET do Lote) para adicionar um certificado a uma conta do Lote. Então, pode associar o certificado a um pool novo ou existente. Quando um certificado está associado a um pool, o serviço em lote instala o certificado em cada nó presente no pool. O serviço Lote instala os certificados apropriados quando o nó é inicializado, antes que ele execute qualquer tarefa (incluindo a tarefa inicial e a tarefa do gerenciador de trabalhos).

Se você adicionar certificados a um pool *existente* , deverá reinicializar seus nós de computação para que os certificados sejam aplicados aos nós.

## <a name="error-handling"></a>Tratamento de erros
Talvez seja necessário lidar com as falhas da tarefa e do aplicativo em sua solução do Lote.

### <a name="task-failure-handling"></a>Manipulação de falha de tarefa
As falhas de tarefas se enquadram nestas categorias:

* **Falhas de pré-processamento**

    Se uma tarefa não for iniciada, um erro de pré-processamento é definido para a tarefa.  

    Poderão ocorrer erros de processamento se os arquivos de recurso da tarefa forem movidos, se a conta de armazenamento não estiver mais disponível ou se outro problema que impediu a cópia bem-sucedida dos arquivos para o nó foi encontrado.

* **Falhas de carregamento de arquivo**

    Se o carregamento de arquivos especificados para uma tarefa falhar por algum motivo, um erro de carregamento de arquivo é definido para a tarefa.

    Poderá ocorrer erros de carregamento de arquivo se as SAS fornecidas para acessar o armazenamento do Azure estiverem inválidas ou não se forneceram permissões de gravação, se a conta de armazenamento não estiver mais disponível ou se foi encontrado um outro problema que impediu a cópia bem-sucedida dos arquivos a partir do nó.    

* **Falhas de aplicativo**

    O processo especificado pela linha de comando da tarefa também pode falhar. O processo é considerado com falha quando é retornado um código de saída diferente de zero pelo processo executado pela tarefa (consulte *Códigos de saída da tarefa* na tarefa a seguir).

    Para as falhas do aplicativo, você pode configurar o Lote para repetir automaticamente a tarefa até um número especificado de vezes.

* **Falhas de restrição**

    Você pode definir uma restrição que especifique a duração máxima da execução de um trabalho ou uma tarefa, *maxWallClockTime*. Isso pode ser útil para encerrar as tarefas sem progresso.

    Quando o tempo máximo tiver sido excedido, a tarefa será marcada como *concluída*, mas o código de saída será definido para `0xC000013A` e o campo *schedulingError* será marcado como `{ category:"ServerError", code="TaskEnded"}`.

### <a name="debugging-application-failures"></a>Falhas de depuração de aplicativos
* `stderr` e `stdout`

    Durante a execução, um aplicativo pode produzir uma saída de diagnóstico que pode ser usada para solucionar os problemas. Conforme mencionado na seção [Arquivos e diretórios](#files-and-directories) anterior, o serviço de Lote grava a saída padrão e os erros padrão nos arquivos `stdout.txt` e `stderr.txt` no diretório da tarefa no nó de computação. Você pode usar o portal do Azure ou um dos SDKs do Lote para baixar esses arquivos. Por exemplo, você pode recuperar esses e outros arquivos para solucionar problemas usando [ComputeNode.GetNodeFile][net_getfile_node] e [CloudTask.GetNodeFile][net_getfile_task] na biblioteca do .NET do Lote.

* **Códigos de saída de tarefas**

    Conforme mencionado anterior, uma tarefa é marcada como tendo falhas pelo serviço de Lote se o processo executado pela tarefa retorna um código de saída diferente de zero. Quando uma tarefa executa um processo, o Lote preenche a propriedade do código de saída da tarefa com o *código de retorno do processo*. É importante observar que o código de saída da tarefa não **é** determinado pelo serviço de Lote. O código de saída da tarefa é determinado pelo próprio processo ou pelo sistema operacional no qual o processo é executado.

### <a name="accounting-for-task-failures-or-interruptions"></a>Contabilidade de interrupções ou de falhas de tarefas
As tarefas podem falhar ou ser interrompidas ocasionalmente. O próprio aplicativo da tarefa pode falhar, o nó no qual a tarefa está em execução pode ser reinicializado ou o nó pode ser removido do pool durante uma operação de redimensionamento, caso a política de desalocação do pool seja definida para remover o nó imediatamente sem esperar que as tarefas sejam concluídas. Em todos os casos, a tarefa pode ser automaticamente recolocada na fila pelo Lote para a execução em outro nó.

Também é possível que um problema intermitente fazer com que uma tarefa pare de responder ou levar muito tempo para executar. Você pode definir o intervalo máximo de execução de uma tarefa. Se o intervalo máximo de execução for excedido, o serviço de Lote irá interromper o aplicativo da tarefa.

### <a name="connecting-to-compute-nodes"></a>Conectar-se a nós de computação
Você pode executar uma depuração e solução de problemas adicionais conectando um nó de computação remotamente. Você pode usar o portal do Azure para baixar um arquivo RDP (Remote Desktop Protocol) para os nós do Windows e obter informações da conexão SSH (Secure Shell) para os nós do Linux. Você também pode fazer isso usando as APIs do Lote – por exemplo, com o [.NET do Lote][net_rdpfile] ou o [Python do Lote](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh).

> [!IMPORTANT]
> Para se conectar a um nó via RDP ou SSH, primeiro você deve criar um usuário no nó. Para tanto, você pode usar o portal do Azure, [adicionar uma conta de usuário a um nó][rest_create_user] usando a API REST do Lote, chamar o método [ComputeNode.CreateComputeNodeUser][net_create_user] no .NET do Lote ou chamar o método [add_user][py_add_user] no módulo Python do Lote.
>
>

Se for necessário restringir ou desabilitar o acesso RDP ou SSH para nós de computação, consulte [Configurar ou desabilitar o acesso remoto para nós de computação em um pool do Lote do Azure](pool-endpoint-configuration.md).

### <a name="troubleshooting-problematic-compute-nodes"></a>Solucionando os nós de computação problemáticos
Em situações em que algumas das tarefas falham, o aplicativo cliente ou o serviço de Lote pode examinar os metadados das tarefas com falha para identificar um nó com comportamento inadequado. Cada nó em um pool tem uma ID exclusiva, e o nó no qual uma tarefa é executada é incluído nos metadados da tarefa. Após identificar um nó com problemas, você poderá executar várias ações nele:

* **Reiniciar o nó** ([REST][rest_reboot] | [.NET][net_reboot])

    Às vezes, reiniciar o nó pode corrigir problemas latentes, como processos bloqueados ou com falha. Observe que, se o pool usar uma tarefa de inicialização ou se o trabalho usar uma tarefa de preparação de trabalho, eles serão executados quando o nó for reiniciado.
* **Refazer a imagem do nó** ([REST][rest_reimage] | [.NET][net_reimage])

    Esse procedimento reinstala o sistema operacional no nó. Assim como ocorre com a reinicialização de um nó, as tarefas de inicialização e de preparação de trabalho são executadas novamente depois que a imagem do nó é refeita.
* **Remover o nó do pool** ([REST][rest_remove] | [.NET][net_remove])

    Às vezes, é necessário remover completamente o nó do pool.
* **Desabilitar o agendamento de tarefas no nó** ([REST][rest_offline] | [.NET][net_offline])

    Isso efetivamente coloca o nó offline para que nenhuma tarefa adicional seja atribuída a ele, mas permite que o nó permaneça em execução e no pool. Isso o habilita a continuar a investigar a causa das falhas sem perder os dados da tarefa com falha e sem que o nó cause falhas de tarefas adicionais. Por exemplo, você pode desabilitar o agendamento de tarefas no nó e [fazer logon remotamente](#connecting-to-compute-nodes) para examinar os logs de evento do nó ou solucionar outros problemas. Depois de concluir sua investigação, você poderá colocar o nó online novamente, permitindo o agendamento da tarefa ([REST][rest_online] | [.NET][net_online]) ou executar uma das ações analisadas anteriormente.

> [!IMPORTANT]
> Com cada ação descrita nesta seção – reinicializar, refazer a imagem, remover e desabilitar o agendamento de tarefas – é possível especificar como as tarefas atualmente em execução no nó são lidadas quando você executa a ação. Por exemplo, quando você desabilita o agendamento de tarefas em um nó usando a biblioteca de cliente .NET do Lote, pode especificar um valor de enumeração [DisableComputeNodeSchedulingOption][net_offline_option] para especificar se deseja **Terminar** as tarefas em execução, **Recolocar em fila** para o agendamento em outros nós ou permitir que a execução das tarefas conclua antes de executar a ação (**TaskCompletion**).
>
>

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre as [Ferramentas e APIs do Lote](batch-apis-tools.md) disponíveis para a criação de soluções do Lote.
* Obtenha as noções básicas sobre o desenvolvimento de um aplicativo habilitado para o Lote usando a [biblioteca de cliente .NET do Lote](quick-run-dotnet.md) ou do [Python](quick-run-python.md). O artigo de início rápido orienta você por meio de um aplicativo de exemplo que usa o serviço em Lotes para executar uma carga de trabalho em vários nós de computação e que inclui o uso do Armazenamento do Azure para preparação e recuperação de um arquivo de carga de trabalho.
* Baixe e instale o [Batch Explorer][batch_labs] para usá-lo durante a instalação das suas soluções de lote. Use o Batch Explorer para ajudar a criar, depurar e monitorar os aplicativos do Lote do Azure. 
* Consulte os recursos da comunidade incluindo o [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-batch), o [repositório da Comunidade de Lote](https://github.com/Azure/Batch), e o [fórum do Lote do Azure][batch_forum] no MSDN. 

[1]: ./media/batch-api-basics/node-folder-structure.png

[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]:  https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[batch_labs]: https://azure.github.io/BatchExplorer/
[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[msdn_env_vars]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_job_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.commonenvironmentsettings.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_onalltaskscomplete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.onalltaskscomplete.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx
[net_rdpfile]: https://msdn.microsoft.com/library/azure/Mt272127.aspx
[vnet]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_netconf

[py_add_user]: https://docs.microsoft.com/python/azure/?view=azure-python

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_job_schedules]: https://msdn.microsoft.com/library/azure/mt282179.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
