---
title: Práticas recomendadas
description: Conheça as práticas recomendadas e dicas úteis para desenvolver suas soluções de lote do Azure.
ms.date: 03/11/2020
ms.topic: conceptual
ms.openlocfilehash: 7ef94b07a5131726c42a94088fd3ee1f413dbec7
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802345"
---
# <a name="azure-batch-best-practices"></a>Melhores práticas do Lote do Azure

Este artigo discute uma coleção de práticas recomendadas e dicas úteis para usar o serviço de lote do Azure com eficiência, com base em experiências reais com o lote. Essas dicas podem ajudá-lo a melhorar o desempenho e evitar armadilhas de design em suas soluções de lote do Azure.

> [!TIP]
> Para obter diretrizes sobre segurança no lote do Azure, consulte [práticas recomendadas de segurança e conformidade do lote](security-best-practices.md).

## <a name="pools"></a>Pools

Os [pools](nodes-and-pools.md#pools) são os recursos de computação para executar trabalhos no serviço de Lote. As seções a seguir fornecem recomendações para trabalhar com pools do Lote.

### <a name="pool-configuration-and-naming"></a>Configuração e nomenclatura de pool

- **Modo de alocação do pool:** Ao criar uma conta do lote, você pode escolher entre dois modos de alocação de pool: **serviço de lote** ou **assinatura de usuário**. Na maioria dos casos, você deve usar o modo de serviço de Lote padrão, no qual os pools são alocados em segundo plano em assinaturas gerenciadas no Lote. No modo de assinatura alternativo do usuário, as VMs do Lote e outros recursos são criados diretamente em sua assinatura, quando um pool é criado. As contas de assinatura de usuário são usadas principalmente para habilitar um subconjunto de cenários importante, mas pequeno. Você pode ler mais sobre o modo de assinatura do usuário em [Configuração adicional para o modo de assinatura do usuário](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode).

- **' virtualMachineConfiguration ' ou ' cloudServiceConfiguration ':** Embora você possa criar pools no momento usando qualquer configuração, novos pools devem ser configurados usando ' virtualMachineConfiguration ' e não ' cloudServiceConfiguration '. Todos os recursos atuais e novos do lote serão suportados pelos pools de configuração da máquina virtual. Os pools de configuração de serviços de nuvem não oferecem suporte a todos os recursos e nenhuma nova funcionalidade é planejada. Você não poderá criar novos pools ' cloudServiceConfiguration ' ou adicionar novos nós a pools existentes [após 29 de fevereiro de 2024](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/). Para obter mais informações, consulte [migrar a configuração do pool do lote dos serviços de nuvem para a máquina virtual](batch-pool-cloud-service-to-virtual-machine-configuration.md).

- **Considere o tempo de execução do trabalho e da tarefa ao determinar o mapeamento do trabalho para o pool:** Se você tiver trabalhos compostos principalmente de tarefas de execução curta, e as contagens de tarefas totais esperadas forem pequenas, para que o tempo de execução esperado geral do trabalho não seja longo, não aloque um novo pool para cada trabalho. O tempo de alocação dos nós reduzirá o tempo de execução do trabalho.

- Os **pools devem ter mais de um nó de computação:** Não há garantia de que os nós individuais estejam sempre disponíveis. Embora não sejam comuns, falhas de hardware, atualizações de sistema operacional e outros problemas podem fazer com que nós individuais fiquem offline. Se a carga de trabalho do Lote exigir um progresso determinístico e garantido, você deverá alocar pools com vários nós.

- **Não use imagens com datas de fim da vida útil (EOL) iminentes.**
    É altamente recomendável evitar imagens com datas de fim da vida útil (EOL) de suporte do lote iminente. Essas datas podem ser descobertas por meio da [ `ListSupportedImages` API](https://docs.microsoft.com/rest/api/batchservice/account/listsupportedimages), do [PowerShell](https://docs.microsoft.com/powershell/module/az.batch/get-azbatchsupportedimage)ou do [CLI do Azure](https://docs.microsoft.com/cli/azure/batch/pool/supported-images). É sua responsabilidade atualizar periodicamente sua exibição das datas de EOL pertinentes aos seus pools e migrar suas cargas de trabalho antes que a data de EOL ocorra. Se você estiver usando uma imagem personalizada com um agente de nó especificado, será necessário garantir que siga as datas de término do suporte ao lote para a imagem para a qual sua imagem personalizada é derivada ou alinhada.

- **Não reutilize nomes de recursos.**
    Os recursos do Lote (trabalhos, pools etc.) geralmente entram e saem ao longo do tempo. Por exemplo, você pode criar um pool na segunda-feira, excluí-lo na terça-feira e, em seguida, criar outro pool na quinta-feira. Os novos recursos criados devem receber um nome exclusivo que você não usou antes. Isso pode ser feito usando um GUID (como o nome do recurso inteiro ou como parte dele) ou inserindo a hora em que o recurso foi criado no nome do recurso. O Lote permite [DisplayName](/dotnet/api/microsoft.azure.batch.jobspecification.displayname), que pode ser usado para atribuir a um recurso um nome fácil, mesmo que a ID de recurso real seja algo amigável para as pessoas. Se você usar nomes exclusivos, isso facilita diferenciar um recurso específico que fez algo em logs e métricas. Ele também removerá a ambiguidade se você precisar arquivar um caso de suporte para um recurso.


- **Continuidade durante a manutenção e a falha do pool:** É melhor fazer com que seus trabalhos usem pools dinamicamente. Se seus trabalhos usarem o mesmo pool para tudo, haverá a chance de que seus trabalhos não sejam executados se algo der errado com o pool. Isso é especialmente importante para cargas de trabalho com detecção de hora. Para corrigir isso, selecione ou crie um pool dinamicamente ao agendar cada trabalho ou tenha uma maneira de substituir o nome do pool para que você possa ignorar um pool não íntegro.

- **Continuidade dos negócios durante a manutenção e a falha do pool:** Há muitas razões pelas quais um pool pode não aumentar para o tamanho desejado, como erros internos, restrições de capacidade, etc. Por esse motivo, você deve estar pronto para redirecionar trabalhos em um pool diferente (possivelmente com um tamanho de VM diferente-o lote dá suporte a isso via [UpdateJob](/dotnet/api/microsoft.azure.batch.protocol.joboperationsextensions.update)), se necessário. Evite usar uma ID de pool estático com a expectativa de que ela nunca seja excluída e nunca seja alterada.

### <a name="pool-lifetime-and-billing"></a>Tempo de vida e cobrança do pool

O tempo de vida do pool pode variar de acordo com o método de alocação e as opções aplicadas à configuração do pool. Os pools podem ter um tempo de vida arbitrário e um número variável de nós de computação no pool a qualquer momento. É sua responsabilidade gerenciar os nós de computação no pool explicitamente ou por meio de recursos fornecidos pelo serviço ([autoescala](nodes-and-pools.md#automatic-scaling-policy) ou [autopool](nodes-and-pools.md#autopools)).

- **Manter os pools atualizados:** Redimensione seus pools para zero a cada poucos meses para garantir que você obtenha as [atualizações e correções de bug mais recentes do agente de nó](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md). Seu pool não receberá atualizações do agente de nó a menos que seja recriado ou redimensionado para 0 nós de computação. Antes de recriar ou redimensionar o pool, é recomendável baixar todos os logs do agente de nó para fins de depuração, conforme discutido na seção [nós](#nodes).

- **Recriação de pool:** Em uma observação semelhante, não é recomendável excluir e recriar seus pools diariamente. Em vez disso, crie um novo pool, atualize seus trabalhos existentes para apontar para o novo pool. Depois que todas as tarefas forem movidas para o novo pool, exclua o pool antigo.

- A **eficiência e a cobrança do pool:** O próprio lote incorre em encargos extras, mas você não tem cobranças para os recursos de computação usados. Você será cobrado por cada nó de computação no pool, independentemente do estado em que ele está. Isso inclui quaisquer encargos necessários para que o nó seja executado, como os custos de armazenamento e de rede. Para saber mais melhores práticas, consulte [Análise de custo e orçamentos para Lote do Azure](budget.md).

### <a name="pool-allocation-failures"></a>Falhas de alocação de pool

As falhas de alocação de pool podem ocorrer a qualquer momento durante a primeira alocação ou redimensionamentos subsequentes. Isso pode ocorrer devido ao esgotamento de capacidade temporária em uma região ou às falhas em outros serviços do Azure dos quais o Lote depende. Sua cota principal não é uma garantia, mas sim um limite.

### <a name="unplanned-downtime"></a>Tempo de inatividade não planejado

É possível que os pools do Lote tenham eventos de tempo de inatividade no Azure. Tenha isso em mente ao planejar e desenvolver seu cenário ou fluxo de trabalho para o Lote.

No caso de um nó falhar, o Lote tentará recuperar automaticamente esses nós de computação em seu nome. Isso pode disparar o reagendamento de qualquer tarefa em execução no nó recuperado. Consulte [Criação de repetições](#design-for-retries-and-re-execution) para saber mais sobre as tarefas interrompidas.

### <a name="custom-image-pools"></a>Pools de imagens personalizadas

Ao criar um pool no Lote do Azure usando a Configuração de Máquina Virtual, você especifica uma imagem de VM que fornece o sistema operacional para cada nó de computação no pool. Você pode criar o pool com uma imagem do Azure Marketplace com suporte ou pode [criar uma imagem personalizada com uma imagem da Galeria de imagens compartilhada](batch-sig-images.md). Embora você também possa usar uma [imagem gerenciada](batch-custom-images.md) para criar um pool de imagens personalizado, é recomendável criar imagens personalizadas usando a Galeria de imagens compartilhadas sempre que possível. O uso da Galeria de imagens compartilhadas ajuda você a provisionar pools mais rapidamente, dimensionar quantidades maiores de VMs e melhorar a confiabilidade ao provisionar VMs.

### <a name="third-party-images"></a>Imagens de terceiros

Os pools podem ser criados usando imagens de terceiros publicadas no Azure Marketplace. Com as contas do lote do modo de assinatura do usuário, você pode ver o erro "falha na alocação devido à verificação da qualificação de compra do Marketplace" ao criar um pool com determinadas imagens de terceiros. Para resolver esse erro, aceite os termos definidos pelo editor da imagem. Você pode fazer isso usando [Azure PowerShell](/powershell/module/azurerm.marketplaceordering/set-azurermmarketplaceterms) ou [CLI do Azure](/cli/azure/vm/image/terms).

### <a name="azure-region-dependency"></a>Dependência da região do Azure

Você não deve contar com uma única região do Azure se tiver uma carga de trabalho de produção ou de tempo. Embora seja raro, há problemas que podem afetar toda a região. Por exemplo, se o processamento precisar ser iniciado em um momento específico, considere escalar verticalmente o pool em sua região primária *bem antes da hora de início*. Se a escala do pool falhar, você poderá fazer fallback para escalar verticalmente um pool em uma região (ou regiões) de backup. Os pools em várias contas em regiões diferentes fornecem um backup pronto e facilmente acessível se algo der errado com outro pool. Para obter mais informações, consulte [Projetar seu aplicativo para alta disponibilidade](high-availability-disaster-recovery.md).

## <a name="jobs"></a>Trabalhos

Um [trabalho](jobs-and-tasks.md#jobs) é um contêiner projetado para conter centenas, milhares ou até mesmo milhões de tarefas. Siga estas diretrizes ao criar trabalhos.

### <a name="fewer-jobs-more-tasks"></a>Menos trabalhos, mais tarefas

O uso de um trabalho para executar uma única tarefa é ineficiente. Por exemplo, é mais eficiente usar um único trabalho com 1.000 tarefas em vez de criar 100 trabalhos com 10 tarefas cada. A execução de 1.000 trabalhos, cada um com uma única tarefa, seria a abordagem menos eficiente, mais lenta e mais cara a ser tomada.

Por isso, certifique-se de não criar uma solução de Lote que exija milhares de trabalhos ativos simultaneamente. Não há nenhuma cota para tarefas; então, executar muitas tarefas com o mínimo de trabalhos possível usa com eficiência suas [cotas de trabalho e de agendamento de trabalho](batch-quota-limit.md#resource-quotas).

### <a name="job-lifetime"></a>Tempo de vida do trabalho

Um trabalho em Lotes tem um tempo de vida indefinido até que seja excluído do sistema. Seu estado designa se ele pode aceitar mais tarefas para agendamento ou não.

Um trabalho não é movido automaticamente para o estado concluído, a menos que seja explicitamente encerrado. Isso pode ser disparado automaticamente por meio da propriedade [onAllTasksComplete](/dotnet/api/microsoft.azure.batch.common.onalltaskscomplete) ou [maxWallClockTime](/rest/api/batchservice/job/add#jobconstraints).

Há uma [cota de trabalho ativo padrão e agendamento de trabalho](batch-quota-limit.md#resource-quotas). Trabalhos e agendamentos de trabalho no estado concluído não contam nesta cota.

## <a name="tasks"></a>Tarefas

As [tarefas](jobs-and-tasks.md#tasks) são unidades individuais de trabalho que compõem um trabalho. As tarefas são enviadas pelo usuário e agendadas pelo Lote para nós de computação. Há várias considerações de design a serem feitas ao criar e executar tarefas. As seções a seguir explicam cenários comuns e como projetar suas tarefas para lidar com problemas e executar com eficiência.

### <a name="save-task-data"></a>Salvar dados da tarefa

Os nós de computação são, por sua natureza, efêmeros. Há muitos recursos em lote, como o [autopool](nodes-and-pools.md#autopools) e o [dimensionamento automático](nodes-and-pools.md#automatic-scaling-policy) , que podem facilitar para os nós desaparecerem. Quando os nós deixam um pool (devido a um redimensionamento ou a uma exclusão de pool), todos os arquivos nesses nós também são excluídos. Por isso, uma tarefa deve mover sua saída do nó em execução e para um repositório durável antes de ser concluída. Da mesma forma, se uma tarefa falhar, ela deverá mover os logs necessários para diagnosticar a falha em um repositório durável.

O Lote tem suporte integrado ao Armazenamento do Azure para carregar dados por meio de [OutputFiles](batch-task-output-files.md), bem como uma variedade de sistemas de arquivos compartilhados, ou você pode executar o upload por conta própria em suas tarefas.

### <a name="manage-task-lifetime"></a>Gerenciar tempo de vida da tarefa

Exclua tarefas quando elas não forem mais necessárias ou defina uma restrição de tarefa [retentionTime](/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime). Se um `retentionTime` estiver definido, o Lote limpará automaticamente o espaço em disco usado pela tarefa quando o `retentionTime` expirar.

A exclusão de tarefas realiza duas coisas. Ela garante que você não tenha uma compilação de tarefas no trabalho, o que pode dificultar a consulta/localização da tarefa em que você está interessado (porque será necessário filtrar as tarefas concluídas). Ela também limpa os dados de tarefa correspondentes no nó (desde que o `retentionTime` ainda não tenha sido atingido). Isso ajuda a garantir que os nós não sejam preenchidos com os dados da tarefa e fique sem espaço em disco.

### <a name="submit-large-numbers-of-tasks-in-collection"></a>Enviar um grande número de tarefas na coleção

As tarefas podem ser enviadas em uma base individual ou em coleções. Envie tarefas em [coleções](/rest/api/batchservice/task/addcollection) de até 100 por vez ao realizar o envio em massa de tarefas para reduzir a sobrecarga e o tempo de envio.

### <a name="set-max-tasks-per-node-appropriately"></a>Definir o máximo de tarefas por nó adequadamente

O Lote permite tarefas de substituição em nós (a execução de mais tarefas em vez de um nó tem núcleos). Cabe a você garantir que suas tarefas se ajustem aos nós no pool. Por exemplo, você pode ter uma experiência degradada se tentar agendar oito tarefas, cada uma consumindo 25% de uso da CPU em um nó (em um pool com `taskSlotsPerNode = 8`).

### <a name="design-for-retries-and-re-execution"></a>Design para novas tentativas e nova execução

As tarefas podem ser repetidas automaticamente pelo Lote. Há dois tipos de tentativas: controlado pelo usuário e interno. As novas tentativas controladas pelo usuário são especificadas pelo [maxTaskRetryCount](/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount) da tarefa. Quando um programa especificado na tarefa é encerrado com um código de saída diferente de zero, a tarefa é repetida até o valor da `maxTaskRetryCount`.

Embora seja raro, uma tarefa pode ser repetida internamente devido a falhas no nó de computação, como não ser capaz de atualizar o estado interno ou uma falha no nó enquanto a tarefa está em execução. A tarefa será repetida no mesmo nó de computação, se possível, até um limite interno antes de desistir da tarefa e adiá-la para ser reagendada pelo Lote, potencialmente em um nó de computação diferente.

Não há diferenças de design ao executar suas tarefas em nós dedicados ou de baixa prioridade. Se uma tarefa é antecipada durante a execução em um nó de baixa prioridade ou interrompida devido a uma falha em um nó dedicado, ambas as situações são atenuadas com a criação da tarefa para resistir à falha.

### <a name="build-durable-tasks"></a>Criar tarefas duráveis

As tarefas devem ser projetadas para resistir à falha e acomodar novas tentativas. Isso é importante principalmente para tarefas de execução prolongada. Para fazer isso, verifique se as tarefas geram o mesmo resultado, mesmo que elas sejam executadas mais de uma vez. Uma maneira de conseguir isso é fazer suas tarefas "atingir metas". Outra maneira é garantir que suas tarefas sejam idempotentes (as tarefas terão o mesmo resultado, independentemente de quantas vezes elas forem executadas).

Um exemplo comum é uma tarefa que copia arquivos para um nó de computação. Uma abordagem simples é uma tarefa que copia todos os arquivos especificados toda vez que ela é executada, o que é ineficiente e não é criado para resistir a falhas. Em vez disso, crie uma tarefa para garantir que os arquivos estejam no nó de computação; uma tarefa que não copia arquivos que já estão presentes. Dessa forma, a tarefa é escolhida de onde parou se foi interrompida.

### <a name="avoid-short-execution-time"></a>Evitar tempo de execução curto

As tarefas que só são executadas por um ou dois segundos não são ideais. Tente fazer uma quantidade significativa de trabalho em uma tarefa individual (no mínimo 10 segundos, indo até horas ou dias). Se cada tarefa estiver em execução por um minuto (ou mais), a sobrecarga de agendamento como uma fração do tempo de computação geral será pequena.

### <a name="use-pool-scope-for-short-tasks-on-windows-nodes"></a>Usar escopo de pool para tarefas curtas em nós do Windows

Ao agendar uma tarefa em nós do lote, você pode escolher se deseja executá-la com escopo da tarefa ou escopo do pool. Se a tarefa for executada apenas por um curto período, o escopo da tarefa poderá ser ineficiente devido aos recursos necessários para criar a conta de usuário automático para essa tarefa. Para obter maior eficiência, considere definir essas tarefas para o escopo do pool. Para obter mais informações, consulte [executar uma tarefa como um usuário automático com escopo de pool](batch-user-accounts.md#run-a-task-as-an-auto-user-with-pool-scope).

## <a name="nodes"></a>Nós

Um [nó de computação](nodes-and-pools.md#nodes) é uma máquina virtual (VM) do Azure ou VM do serviço de nuvem que é dedicada ao processamento de uma parte da carga de trabalho do aplicativo. Siga estas diretrizes ao trabalhar com nós.

### <a name="idempotent-start-tasks"></a>Tarefas de início idempotentes

Assim como ocorre com outras tarefas, a [tarefa de início](jobs-and-tasks.md#start-task) do nó deve ser idempotente, pois ela será executada novamente sempre que o nó for inicializado. Uma tarefa idempotente produz um resultado consistente quando executada várias vezes.

### <a name="isolated-nodes"></a>Nós isolados

Considere o uso de tamanhos de VM isoladas para cargas de trabalho com requisitos normativos ou de conformidade. Os tamanhos isolados com suporte no modo de configuração de máquina virtual incluem `Standard_E80ids_v4` ,,,, `Standard_M128ms` `Standard_F72s_v2` `Standard_G5` `Standard_GS5` e `Standard_E64i_v3` . Para obter mais informações sobre tamanhos de VM isoladas, consulte [isolamento de máquina virtual no Azure](../virtual-machines/isolation.md).

### <a name="manage-long-running-services-via-the-operating-system-services-interface"></a>Gerenciar serviços de execução longa por meio da interface de serviços do sistema operacional

Às vezes, há a necessidade de executar outro agente junto com o agente do Lote no nó. Por exemplo, talvez você queira coletar dados do nó e relatá-los. Recomendamos que esses agentes sejam implantados como serviços do sistema operacional, como um serviço do Windows ou um serviço de `systemd` do Linux.

Ao executar esses serviços, eles não devem fazer bloqueios de arquivo em nenhum arquivo nos diretórios gerenciados pelo Lote no nó; caso contrário, o Lote não poderá excluir esses diretórios devido aos bloqueios de arquivo. Por exemplo, se estiver instalando um serviço do Windows em uma tarefa inicial, em vez de iniciar o serviço diretamente do diretório de trabalho da tarefa de início, copie os arquivos em outro lugar (ou se os arquivos existirem, apenas ignore a cópia). Em seguida, instale o serviço a partir desse local. Quando o Lote executar novamente a tarefa de início, ele vai excluir o diretório de trabalho da tarefa de início e criá-lo novamente. Isso funciona porque o serviço tem bloqueios de arquivo no outro diretório, não no diretório de trabalho da tarefa de início.

### <a name="avoid-creating-directory-junctions-in-windows"></a>Evitar criar junções de diretório no Windows

As junções de diretório, às vezes chamadas de links físicos de diretório, são difíceis de lidar durante a limpeza da tarefa e do trabalho. Use symlinks (links soft) em vez de links físicos.

### <a name="collect-the-batch-agent-logs"></a>Coletar logs de agente do Lote

Se você notar um problema envolvendo o comportamento de um nó ou de tarefas em execução em um nó, colete os logs do agente do Lote antes de desalocar os nós em questão. Os logs de agente do Lote podem ser coletados usando a API de logs de serviço Carregar Lote. Esses logs podem ser fornecidos como parte de um tíquete de suporte à Microsoft e ajudarão com a solução de problemas.

### <a name="manage-os-upgrades"></a>Gerenciar atualizações do sistema operacional

Para contas do lote no modo de assinatura do usuário, as atualizações automatizadas do sistema operacional podem interromper o andamento da tarefa, especialmente se as tarefas forem de execução longa. A [criação de tarefas idempotentes](#build-durable-tasks) pode ajudar a reduzir erros causados por essas interrupções. Também recomendamos o [agendamento de atualizações de imagem do sistema operacional para horários em que não se espera que as tarefas sejam executadas](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md#manually-trigger-os-image-upgrades).

Para pools do Windows, `enableAutomaticUpdates` é definido como `true` por padrão. É recomendável permitir atualizações automáticas, mas você pode definir esse valor como `false` se precisar garantir que uma atualização do sistema operacional não aconteça inesperadamente.

## <a name="isolation-security"></a>Segurança de isolamento

Para fins de isolamento, se seu cenário exigir o isolamento de trabalhos uns dos outros, faça isso colocando-os em pools separados. Um pool é o limite de isolamento de segurança no Lote e, por padrão, dois pools não são visíveis ou capazes de se comunicar entre si. Evite usar contas do Lote separadas como meio de isolamento.

## <a name="moving-batch-accounts-across-regions"></a>Como mover contas do Lote entre regiões

Há cenários em que pode ser útil mover uma conta do Lote existente de uma região para outra. Por exemplo, talvez você queira mover para outra região como parte do planejamento de recuperação de desastre.

As contas do Lote do Azure não podem ser diretamente movidas de uma região para outra. No entanto, você pode usar um modelo do Azure Resource Manager para exportar a configuração existente da sua conta do Lote. Em seguida, você pode preparar o recurso em outra região, exportando a conta do Lote para um modelo, modificando os parâmetros para corresponder à região de destino e, em seguida, implantando o modelo na nova região.

Depois de carregar o modelo para a nova região, você precisará recriar certificados, agendas de trabalho e pacotes de aplicativos. Para confirmar as alterações e concluir a movimentação da conta do Lote, lembre-se de excluir a conta do Lote original ou o grupo de recursos.

Para obter mais informações sobre o Resource Manager e modelos, consulte [Início Rápido: Crie e implante modelos do Azure Resource Manager usando o portal do Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

## <a name="connectivity"></a>Conectividade

Examine as diretrizes a seguir relacionadas à conectividade em suas soluções do lote.

### <a name="network-security-groups-nsgs-and-user-defined-routes-udrs"></a>NSGs (grupos de segurança de rede) e UDRs (rotas definidas pelo usuário)

Ao provisionar [pools do Lote em uma rede virtual](batch-virtual-network.md), verifique se você está seguindo as diretrizes em relação ao uso da marca de serviço `BatchNodeManagement`, das portas, do protocolos e da direção da regra. O uso da marca de serviço é altamente recomendado, em vez de usar os endereços IP subjacentes do serviço do Lote. Isso ocorre porque os endereços IP podem mudar ao longo do tempo. Usar endereços IP do serviço do Lote diretamente pode causar instabilidade ou interrupções nos pools do Lote.

Para UDRs (rotas definidas pelo usuário), verifique se você tem um processo em vigor para atualizar os endereços IP do serviço do Lote periodicamente em sua tabela de rotas, pois esses endereços mudam com o passar do tempo. Para saber como obter a lista de endereços IP do serviço do Lote, consulte [Marcas de serviços locais](../virtual-network/service-tags-overview.md). Os endereços IP do serviço do Lote serão associados à marca de serviço `BatchNodeManagement` (ou à variante regional que corresponde à região da sua conta do Lote).

### <a name="honoring-dns"></a>Como honrar o DNS

Verifique se seus sistemas estão respeitando a TTL (vida útil) do DNS para a URL do serviço da conta do Lote. Além disso, verifique se os clientes de serviço do lote e outros mecanismos de conectividade para o serviço de lote não dependem de endereços IP (ou [crie um pool com endereços IP públicos estáticos](create-pool-public-ip.md) , conforme descrito abaixo).

Se suas solicitações receberem respostas HTTP de nível 5xx e houver um cabeçalho "Conexão: fechamento" na resposta, o cliente do serviço do Lote deverá observar a recomendação, fechando a conexão existente e resolvendo novamente o DNS para a URL de serviço da conta do Lote, e tentar seguir estas solicitações em uma nova conexão.

### <a name="retry-requests-automatically"></a>Solicitações de repetição automaticamente

Verifique se os clientes do serviço do Lote têm políticas de repetição apropriadas em vigor para repetir automaticamente suas solicitações, mesmo durante a operação normal e não exclusivamente durante os períodos de tempo de manutenção do serviço. Essas políticas de repetição devem abranger um intervalo de pelo menos 5 minutos. Os recursos de repetição automática são fornecidos com vários SDKs do Lote, como a [classe .NET RetryPolicyProvider](/dotnet/api/microsoft.azure.batch.retrypolicyprovider).

### <a name="static-public-ip-addresses"></a>Endereços IP públicos estáticos

Normalmente, as máquinas virtuais em um pool do lote são acessadas por meio de endereços IP públicos que podem ser alterados durante o tempo de vida do pool. Isso pode dificultar a interação com um banco de dados ou outro serviço externo que limita o acesso a determinados endereços IP. Para garantir que os endereços IP públicos em seu pool não sejam alterados inesperadamente, você pode criar um pool usando um conjunto de endereços IP públicos estáticos que você controla. Para obter mais informações, consulte [criar um pool do lote do Azure com endereços IP públicos especificados](create-pool-public-ip.md).

### <a name="testing-connectivity-with-cloud-services-configuration"></a>Testando a conectividade com a configuração de serviços de nuvem

Você não pode usar o protocolo/ICMP "ping" normal com os serviços de nuvem, pois o protocolo ICMP não é permitido por meio do Azure Load Balancer. Para obter mais informações, consulte [conectividade e rede para serviços de nuvem do Azure](../cloud-services/cloud-services-connectivity-and-networking-faq.md#can-i-ping-a-cloud-service).

## <a name="batch-node-underlying-dependencies"></a>Dependências subjacentes do nó do Lote

Considere as seguintes dependências e restrições ao projetar suas soluções do Lote.

### <a name="system-created-resources"></a>Recursos criados pelo sistema

O Lote do Azure cria e gerencia um conjunto de usuários e grupos na VM, que não devem ser alterados. Elas são as seguintes:

Windows:

- Um usuário chamado **PoolNonAdmin**
- Um grupo de usuários chamado **WATaskCommon**

Linux:

- Um usuário chamado **_azbatch**

### <a name="file-cleanup"></a>Limpeza de arquivo

O Lote tenta ativamente limpar o diretório de trabalho no qual as tarefas são executadas, quando o tempo de retenção expira. [Você é responsável por limpar](#manage-task-lifetime) todos os arquivos gravados fora desse diretório para evitar o preenchimento do espaço em disco.

A limpeza automatizada para o diretório de trabalho será bloqueada se você executar um serviço no Windows a partir do diretório de trabalho startTask, porque a pasta ainda está em uso. Isso resultará em degradação do desempenho. Para corrigir isso, altere o diretório desse serviço para um diretório separado que não seja gerenciado pelo Lote.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Fluxo de trabalho e recursos primários do serviço de lote](batch-service-workflow-features.md) como pools, nós, trabalhos e tarefas.
- Saiba mais sobre as [cotas, os limites e as restrições do lote do Azure padrão e como solicitar aumentos de cota](batch-quota-limit.md).
- Saiba como [detectar e evitar falhas em operações de segundo plano do pool e do nó ](batch-pool-node-error-checking.md).
