---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: d242b2815d59676432beb878bbc955a9f39de0f1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171919"
---
# <a name="backup-and-disaster-recovery-for-azure-iaas-disks"></a>Backup e recuperação de desastre de discos de IaaS do Azure

Este artigo explica como planejar o backup e a DR (recuperação de desastre) de VMs (máquinas virtuais) e discos de IaaS no Azure. Este documento aborda discos gerenciados e discos não gerenciados.

Primeiro, abordamos as funcionalidades internas de tolerância a falhas da plataforma Azure que ajudam a proteger contra falhas locais. Em seguida, abordamos os cenários de desastre que não são totalmente cobertos pelas funcionalidades internas. Também mostramos vários exemplos de cenários de carga de trabalho nos quais diferentes considerações sobre backup e DR se aplicam. Depois, examinamos possíveis soluções para DR de discos de IaaS.

## <a name="introduction"></a>Introdução

A plataforma Azure usa vários métodos de redundância e tolerância a falhas para ajudar a proteger os clientes contra falhas de hardware localizadas. Falhas locais podem incluir problemas com um computador de servidor do Armazenamento do Azure que armazena parte dos dados de um disco virtual ou falhas de um SSD ou HD nesse servidor. Falhas de componente de hardware isoladas como essas podem ocorrer durante operações normais.

A plataforma Azure foi projetada para ser resiliente a essas falhas. Desastres graves podem resultar em falhas ou na incapacidade de acesso de muitos servidores de armazenamento ou até mesmo de um datacenter inteiro. Embora as VMs e os discos normalmente sejam protegidos contra falhas localizadas, etapas adicionais são necessárias para proteger a carga de trabalho contra falhas catastróficas de toda a região, como um desastre grave, que podem afetar a VM e os discos.

Além da possibilidade de falhas na plataforma, podem ocorrer problemas com os dados ou o aplicativo de um cliente. Por exemplo, uma nova versão do aplicativo pode inadvertidamente fazer uma alteração nos dados que provoca sua interrupção. Nesse caso, talvez você deseje reverter o aplicativo e os dados para uma versão anterior que contém o último estado válido conhecido. Isso exige a manutenção de backups regulares.

Para a recuperação de desastre regional, você deve fazer backup dos discos da VM de IaaS em outra região.

Antes de examinarmos as opções de backup e DR, vamos recapitular alguns métodos disponíveis para o tratamento de falhas localizadas.

### <a name="azure-iaas-resiliency"></a>Resiliência de IaaS do Azure

*Resiliência* refere-se à tolerância a falhas normais que ocorrem em componentes de hardware. A resiliência é a capacidade de se recuperar de falhas e continuar funcionando. Não se trata de evitar falhas, mas responder a elas de uma maneira que evite o tempo de inatividade ou a perda de dados. A meta de resiliência é retornar o aplicativo a um estado totalmente funcional após uma falha. As máquinas virtuais e os discos do Azure foram projetados para serem resilientes a falhas comuns de hardware. Vamos dar uma olhada em como a plataforma de IaaS do Azure fornece essa resiliência.

Uma máquina virtual consiste principalmente em duas partes: um servidor de computação e os discos persistentes. Ambos afetam a tolerância a falhas de uma máquina virtual.

Se o servidor host de computação do Azure que hospeda a VM tiver uma falha de hardware, o que é raro, o Azure restaurará automaticamente a VM em outro servidor, pois foi projetado para isso. Se esse cenário ocorrer, o computador será reinicializado e a VM volta a funcionar após algum tempo. O Azure detecta essas falhas de hardware automaticamente e executa recuperações para ajudar a garantir que a VM do cliente estará disponível assim que possível.

Em relação aos discos de IaaS, a durabilidade dos dados é crítica para uma plataforma de armazenamento persistente. Os clientes do Azure têm aplicativos de negócios importantes em execução na IaaS e dependem da persistência dos dados. O Azure projeta a proteção para esses discos de IaaS, com três cópias redundantes dos dados armazenados localmente. Essas cópias fornecem alta durabilidade contra falhas locais. Se um dos componentes de hardware que contém o disco falhar, a VM não será afetada porque há duas cópias adicionais para dar suporte às solicitações do disco. Isso funcionará bem, mesmo se dois componentes de hardware diferentes que dão suporte a um disco falharem ao mesmo tempo (o que é raro). 

Para ajudar a garantir que você sempre mantém três réplicas, o Armazenamento do Azure gera automaticamente uma nova cópia dos dados em segundo plano se uma das três cópias não fica disponível. Portanto, não deve ser necessário usar o RAID com discos do Azure para a tolerância a falhas. Uma simples configuração RAID 0 deve ser suficiente para a distribuição dos discos, se necessário, para criar volumes maiores.

Devido a essa arquitetura, o Azure proporcionou durabilidade de nível empresarial de modo consistente para discos de IaaS, com uma [taxa de falha anualizada](https://en.wikipedia.org/wiki/Annualized_failure_rate) líder do setor de 0%.

Falhas de hardware localizadas no host de computação ou na plataforma de Armazenamento podem, às vezes, resultar na indisponibilidade temporária para a VM que é coberta pelo [SLA do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) em relação à disponibilidade da VM. O Azure também fornece um SLA líder do setor para instâncias de VM individuais que usam SSDs premium do Azure.

Para proteger cargas de trabalho do aplicativo contra o tempo de inatividade devido à indisponibilidade temporária de um disco ou de uma VM, os clientes podem usar [conjuntos de disponibilidade](../articles/virtual-machines/windows/manage-availability.md). Duas ou mais máquinas virtuais em um conjunto de disponibilidade fornecem redundância para o aplicativo. Em seguida, o Azure cria essas VMs e esses discos em domínios de falha separados com diferentes componentes de energia, rede e servidor.

Devido a esses domínios de falha separados, as falhas de hardware localizadas geralmente não afetam várias VMs no conjunto ao mesmo tempo. Ter domínios de falha separados fornece alta disponibilidade para o aplicativo. É considerada uma melhor prática usar conjuntos de disponibilidade quando a alta disponibilidade é necessária. A próxima seção aborda o aspecto de recuperação de desastre.

### <a name="backup-and-disaster-recovery"></a>Backup e recuperação de desastres

A recuperação de desastre é a capacidade de recuperação de incidentes raros, mas importantes. Esses incidentes incluem falhas não transitórias de larga escala, como interrupções de serviço que afetam toda uma região. A recuperação de desastre inclui backup e arquivamento de dados e pode incluir a intervenção manual, como a restauração um banco de dados com base em um backup.

A proteção interna da plataforma Azure contra falhas localizadas poderá não proteger totalmente as VMs e os discos se desastres graves causarem interrupções em grande escala. Isso inclui interrupções em larga escala, como se um data center for atingido por um furacão, terremoto, incêndio ou se houver falhas de unidade de hardware em grande escala. Além disso, você pode encontrar falhas devido a problemas dos dados ou do aplicativo.

Para ajudar a proteger as cargas de trabalho de IaaS contra interrupções, você deve planejar a redundância e ter backups para permitir a recuperação. Para a recuperação de desastre, você deve fazer backup em uma localização geográfica diferente fora do site primário. Essa abordagem ajuda a garantir que o backup não é afetado pelo mesmo evento que originalmente afetou a VM ou os discos. Para obter mais informações, consulte [Recuperação de desastre para aplicativos do Azure](/azure/architecture/resiliency/disaster-recovery-azure-applications).

Suas considerações sobre DR podem incluir os seguintes aspectos:

- Alta disponibilidade: A capacidade do aplicativo de continuar em execução em um estado íntegro, sem tempo de inatividade significativo. Por *estado íntegro*, esse estado significa que o aplicativo está respondendo e que os usuários podem se conectar ao aplicativo e interagir com ele. Alguns aplicativos e bancos de dados críticos podem precisar estar sempre disponíveis, mesmo quando há falhas na plataforma. Para essas cargas de trabalho, talvez você precise planejar a redundância para o aplicativo, bem como para os dados.

- Durabilidade dos dados: Em alguns casos, a principal consideração é garantir que os dados são preservados no caso de um desastre. Portanto, talvez seja necessário fazer um backup dos dados em outro site. Para essas cargas de trabalho, talvez não seja necessário ter a redundância total para o aplicativo, mas apenas um backup regular dos discos.

## <a name="backup-and-dr-scenarios"></a>Cenários de backup e DR

Vamos examinar alguns exemplos típicos de cenários de carga de trabalho do aplicativo e as considerações sobre o planejamento da recuperação de desastre.

### <a name="scenario-1-major-database-solutions"></a>Cenário 1: Principais soluções de banco de dados

Considere um servidor de banco de dados de produção, como o SQL Server ou o Oracle, que pode dar suporte à alta disponibilidade. Usuários e aplicativos de produção críticos dependem desse banco de dados. O plano de recuperação de desastre para esse sistema pode precisar dar suporte aos seguintes requisitos:

- Os dados devem ser protegidos e recuperáveis.
- O servidor deve estar disponível para uso.

O plano de recuperação de desastre pode exigir a manutenção de uma réplica do banco de dados em outra região como um backup. Dependendo dos requisitos de disponibilidade do servidor e de recuperação de dados, a solução pode variar de um site de réplica ativo-ativo ou ativo-passivo para backups offline periódicos dos dados. Bancos de dados relacionais, como o SQL Server e o Oracle, fornecem várias opções de replicação. Para o SQL Server, use [Grupos de Disponibilidade AlwaysOn do SQL Server](https://msdn.microsoft.com/library/hh510230.aspx) para alta disponibilidade.

Bancos de dados NoSQL, como o MongoDB, também dão suporte a [réplicas](https://docs.mongodb.com/manual/replication/) para redundância. As réplicas para alta disponibilidade são usadas.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Cenário 2: Um cluster de VMs redundantes

Considere uma carga de trabalho manipulada por um cluster de VMs que fornece redundância e balanceamento de carga. Um exemplo é um cluster do Cassandra implantado em uma região. Esse tipo de arquitetura já fornece um alto nível de redundância nessa região. No entanto, para proteger a carga de trabalho contra uma falha de nível regional, você deve considerar a distribuição do cluster em duas regiões ou a realização de backups periódicos em outra região.

### <a name="scenario-3-iaas-application-workload"></a>Cenário 3: Carga de trabalho de aplicativos IaaS

Vamos examinar a carga de trabalho de aplicativos IaaS. Por exemplo, isso pode ser uma carga de trabalho de produção típica em execução em uma VM do Azure. Isso pode ser um servidor Web ou servidor de arquivos que mantém o conteúdo e outros recursos de um site. Também pode ser um aplicativo de negócios personalizado em execução em uma VM que armazenou seus dados, recursos e o estado do aplicativo nos discos da VM. Nesse caso, é importante fazer backups regularmente. A frequência de backup deve se basear na natureza da carga de trabalho da VM. Por exemplo, se o aplicativo é executado diariamente e modifica dados, o backup deve ser feito a cada hora.

Outro exemplo é um servidor de relatórios que efetua pull de dados de outras fontes e gera relatórios agregados. A perda dessa VM ou desses discos poderá levar à perda dos relatórios. No entanto, talvez seja possível executar o processo de relatórios novamente e regenerar o resultado. Nesse caso, você realmente não tem uma perda de dados, mesmo se o servidor de relatório é atingido por um desastre. Como resultado, talvez você tenha um nível mais alto de tolerância da perda de parte dos dados no servidor de relatório. Nesse caso, backups menos frequentes são uma opção para reduzir os custos.

### <a name="scenario-4-iaas-application-data-issues"></a>Cenário 4: Problemas de dados de aplicativos IaaS

Problemas de dados de aplicativos IaaS são outra possibilidade. Considere um aplicativo que calcula, mantém e fornece dados comerciais críticos, como informações sobre preços. Uma nova versão do aplicativo tinha um bug de software que calculava os preços incorretamente e corrompeu os dados comerciais existentes fornecidos pela plataforma. Aqui, a melhor decisão é reverter para a versão anterior do aplicativo e dos dados. Para possibilitar isso, faça backups periódicos do sistema.

## <a name="disaster-recovery-solution-azure-backup"></a>Solução de recuperação de desastre: Serviço de Backup do Azure 

O [Backup do Azure](https://azure.microsoft.com/services/backup/) é usado para backups e DR e funciona com [discos gerenciados](../articles/virtual-machines/windows/managed-disks-overview.md), bem como com discos não gerenciados. Crie um trabalho de backup com backups baseados em tempo, fácil restauração de VM e políticas de retenção de backup.

Se você usar [SSDs premium](../articles/virtual-machines/windows/disks-types.md), [discos gerenciados](../articles/virtual-machines/windows/managed-disks-overview.md) ou outros tipos de disco com a opção [armazenamento com redundância local](../articles/storage/common/storage-redundancy-lrs.md), é especialmente importante fazer backups periódicos de DR. O Backup do Azure armazena os dados no cofre dos serviços de recuperação para retenção de longo prazo. Escolha a opção [armazenamento com redundância geográfica](../articles/storage/common/storage-redundancy-grs.md) para o cofre dos serviços de recuperação de backup. Essa opção garante que os backups são replicados em outra região do Azure para proteger contra desastres regionais.

Para discos não gerenciados, use o tipo de armazenamento com redundância local para discos de IaaS, mas verifique se o Backup do Azure está habilitado com a opção de armazenamento com redundância geográfica no cofre dos serviços de recuperação.

> [!NOTE]
> Se você usar a opção [armazenamento com redundância geográfica](../articles/storage/common/storage-redundancy-grs.md) ou [armazenamento com redundância geográfica com acesso de leitura](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) para os discos não gerenciados, ainda precisará de instantâneos consistentes para backup e DR. Use o [Backup do Azure](https://azure.microsoft.com/services/backup/) ou [instantâneos consistentes](#alternative-solution-consistent-snapshots).

 A tabela a seguir é um resumo das soluções disponíveis para DR.

| Cenário | Replicação automática | Solução de DR |
| --- | --- | --- |
| Discos SSD Premium | Local ([armazenamento com redundância local](../articles/storage/common/storage-redundancy-lrs.md)) | [Serviço de Backup do Azure](https://azure.microsoft.com/services/backup/) |
| Discos gerenciados | Local ([armazenamento com redundância local](../articles/storage/common/storage-redundancy-lrs.md)) | [Serviço de Backup do Azure](https://azure.microsoft.com/services/backup/) |
| Discos não gerenciados com armazenamento com redundância local | Local ([armazenamento com redundância local](../articles/storage/common/storage-redundancy-lrs.md)) | [Serviço de Backup do Azure](https://azure.microsoft.com/services/backup/) |
| Discos não gerenciados com armazenamento com redundância geográfica | Várias regiões ([armazenamento com redundância geográfica](../articles/storage/common/storage-redundancy-grs.md)) | [Serviço de Backup do Azure](https://azure.microsoft.com/services/backup/)<br/>[Instantâneos consistentes](#alternative-solution-consistent-snapshots) |
| Discos não gerenciados de armazenamento com redundância geográfica com acesso de leitura | Entre regiões ([armazenamento com redundância geográfica com acesso de leitura](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)) | [Serviço de Backup do Azure](https://azure.microsoft.com/services/backup/)<br/>[Instantâneos consistentes](#alternative-solution-consistent-snapshots) |

A alta disponibilidade é melhor alcançada com discos gerenciados em um conjunto de disponibilidade junto com o Backup do Azure. Se você usar discos não gerenciados, ainda poderá usar o Backup do Azure para DR. Se não for possível usar o Backup do Azure, tirar [instantâneos consistentes](#alternative-solution-consistent-snapshots), conforme descrito em uma seção posterior, é uma solução alternativa para backup e DR.

Suas escolhas de alta disponibilidade, backup e DR nos níveis do aplicativo ou da infraestrutura podem ser representadas da seguinte maneira:

| Nível |   Alta disponibilidade   | Backup ou DR |
| --- | --- | --- |
| Aplicativo | SQL Server AlwaysOn | Serviço de Backup do Azure |
| Infraestrutura    | Conjunto de disponibilidade  | Armazenamento com redundância geográfica com instantâneos consistentes |

### <a name="using-azure-backup"></a>Usando o Backup do Azure 

O [Backup do Azure](../articles/backup/backup-azure-vms-introduction.md) pode fazer backup das VMs que executam Windows ou Linux no cofre dos serviços de recuperação do Azure. Fazer backup e restaurar dados críticos para os negócios é complicado, pois os dados críticos para os negócios precisam ser copiados em backup enquanto os aplicativos que produzem os dados estão em execução. 

Para resolver esse problema, o Backup do Azure fornece backups consistentes com o aplicativo para cargas de trabalho da Microsoft. Ele usa o serviço de sombra de volume para garantir que os dados são gravados corretamente no armazenamento. Para VMs Linux, somente backups consistentes com os arquivos são possíveis, pois o Linux não tem uma funcionalidade equivalente ao serviço de sombra de volume.

![Fluxo do Backup do Azure][1]

Quando o Backup do Azure inicia um trabalho de backup no horário agendado, ele dispara a extensão de backup instalada na VM para tirar um instantâneo pontual. Um instantâneo é tirado em conjunto com o serviço de sombra de volume para obter um instantâneo consistente dos discos na máquina virtual sem precisar desligá-la. A extensão de backup na VM libera todas as gravações antes de tirar um instantâneo consistente de todos os discos. Depois de tirar o instantâneo, os dados são transferidos pelo Backup do Azure para o cofre de backup. Para tornar o processo de backup mais eficiente, o serviço identifica e transfere apenas os blocos de dados que foram alterados após o último backup.

Para restaurar, exiba os backups disponíveis por meio do Backup do Azure e, em seguida, inicie uma restauração. Crie e restaure backups do Azure por meio do [portal do Azure](https://portal.azure.com/), [usando o PowerShell](../articles/backup/backup-azure-vms-automation.md) ou usando a [CLI do Azure](/cli/azure/).

### <a name="steps-to-enable-a-backup"></a>Etapas para habilitar um backup

Use as etapas a seguir para habilitar backups das VMs usando o [portal do Azure](https://portal.azure.com/). Há algumas variações dependendo do cenário exato. Consulte a documentação do [Backup do Azure](../articles/backup/backup-azure-vms-introduction.md) para obter detalhes completos. O Backup do Azure também [dá suporte a VMs com discos gerenciados](https://azure.microsoft.com/blog/azure-managed-disk-backup/).

1.  Crie um cofre dos serviços de recuperação para uma VM:

    a. No [portal do Azure](https://portal.azure.com/), procure **Todos os recursos** e localize **Cofres dos Serviços de Recuperação**.

    b. No menu **Cofres dos Serviços de Recuperação**, clique em **Adicionar** e siga as etapas para criar um novo cofre na mesma região da VM. Por exemplo, se a VM estiver na região Oeste dos EUA, escolha Oeste dos EUA para o cofre.

1.  Verifique a replicação de armazenamento do cofre recém-criado. Acesse o cofre em **cofres dos serviços de recuperação** e vá para **Properties** > **configuração de Backup** > **atualização** . Verifique se a opção **Armazenamento com redundância geográfica** está selecionada por padrão. Essa opção garante que o cofre é replicado automaticamente em um data center secundário. Por exemplo, o cofre do Oeste dos EUA é replicado automaticamente no Leste dos EUA.

1.  Configure a política de backup e selecione a VM na mesma interface do usuário.

1.  Verifique se o Agente de Backup está instalado na VM. Se a VM for criada usando uma imagem da galeria do Azure, o Agente de Backup já estará instalado. Caso contrário (ou seja, se você estiver usando uma imagem personalizada), use as instruções para [instalar o agente de VM em uma máquina virtual](../articles/backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent).

1.  Verifique se a VM permite a conectividade de rede para que o serviço de backup funcione. Siga as instruções para a [conectividade de rede](../articles/backup/backup-azure-arm-vms-prepare.md#establish-network-connectivity).

1.  Depois que as etapas anteriores forem concluídas, o backup será executado em intervalos regulares, conforme especificado na política de backup. Se necessário, dispare o primeiro backup manualmente no painel do cofre do portal do Azure.

Para automatizar o Backup do Azure usando scripts, consulte [Cmdlets do PowerShell para backup de VM](../articles/backup/backup-azure-vms-automation.md).

### <a name="steps-for-recovery"></a>Etapas de recuperação

Se você precisar reparar ou recriar uma VM, restaure a VM por meio de um dos pontos de recuperação de backup no cofre. Há duas opções diferentes para realizar a recuperação:

-   Você pode criar uma nova VM como uma representação pontual da VM copiada em backup.

-   Você pode restaurar os discos e, em seguida, usar o modelo da VM para personalizar e recriar a VM restaurada.

Para obter mais informações, consulte as instruções sobre como [usar o portal do Azure para restaurar máquinas virtuais](../articles/backup/backup-azure-arm-restore-vms.md). Este documento também explica as etapas específicas para a restauração de VMs copiadas em backup para um datacenter emparelhado usando o cofre de backup com redundância geográfica, se houver um desastre no datacenter primário. Nesse caso, o Backup do Azure usa o serviço de Computação da região secundária para criar a máquina virtual restaurada.

Use também o PowerShell para [criar uma nova VM com base em discos restaurados](../articles/backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="alternative-solution-consistent-snapshots"></a>Solução alternativa: Instantâneos consistentes

Se não for possível usar o Backup do Azure, implemente seu próprio mecanismo de backup usando instantâneos. É complicado criar instantâneos consistentes para todos os discos usados por uma VM e, em seguida, replicar esses instantâneos em outra região. Por esse motivo, o Azure considera o uso do serviço de Backup uma opção melhor do que a criação de uma solução personalizada.

Se você usar o armazenamento com redundância geográfica/armazenamento com redundância geográfica com acesso de leitura para discos, os instantâneos serão replicados automaticamente em um datacenter secundário. Se você usar o armazenamento com redundância local para discos, precisará replicar os dados por conta própria. Para obter mais informações, consulte [Fazer backup de discos não gerenciados de VM do Azure com instantâneos incrementais](../articles/virtual-machines/windows/incremental-snapshots.md).

Um instantâneo é uma representação de um objeto em um ponto específico no tempo. Um instantâneo incorre em cobrança pelo tamanho incremental dos dados que ele mantém. Para obter mais informações, consulte [Criar um instantâneo de blob](../articles/storage/blobs/storage-blob-snapshots.md).

### <a name="create-snapshots-while-the-vm-is-running"></a>Criar instantâneos enquanto a VM está em execução

Embora você possa tirar um instantâneo a qualquer momento, se a VM estiver em execução, ainda haverá dados sendo transmitidos para os discos. Os instantâneos podem conter operações parciais que estavam em trânsito. Além disso, se houver vários discos envolvidos, os instantâneos de discos diferentes poderão ter ocorrido em horários diferentes. Esses cenários podem fazer com que os instantâneos não sejam coordenados. Essa falta de coordenação é especialmente problemática para volumes distribuídos cujos arquivos podem ser corrompidos se alterações estavam sendo feitas durante o backup.

Para evitar essa situação, o processo de backup deve implementar as seguintes etapas:

1.  Congele todos os discos.

1.  Libere todas as gravações pendentes.

1.  [Crie um instantâneo de blob](../articles/storage/blobs/storage-blob-snapshots.md) para todos os discos.

Alguns aplicativos do Windows, como o SQL Server, fornecem um mecanismo de backup coordenado por meio do serviço de sombra de volume para criar backups consistentes com o aplicativo. No Linux, use uma ferramenta como o *fsfreeze* para coordenar os discos. Essa ferramenta fornece backups consistentes com os arquivos, mas não instantâneos consistentes com o aplicativo. Esse processo é complexo. Portanto, considere o uso de [Backup do Azure](../articles/backup/backup-azure-vms-introduction.md) ou de uma solução de backup de terceiros que já implementa esse procedimento.

O processo anterior resulta em uma coleção de instantâneos coordenados para todos os discos de VM, representando uma exibição pontual específica da VM. Esse é um ponto de restauração de backup para a VM. Repita o processo em intervalos agendados para criar backups periódicos. Consulte [Copiar os backups para outra região](#copy-the-snapshots-to-another-region) para obter as etapas para copiar os instantâneos para outra região para DR.

### <a name="create-snapshots-while-the-vm-is-offline"></a>Criar instantâneos enquanto a VM está offline

Outra opção para criar backups consistentes é desligar a VM e tirar instantâneos de blob de cada disco. Tirar instantâneos de blob é mais fácil do que coordenar instantâneos de uma VM em execução, mas exige alguns minutos de inatividade.

1. Desligue a VM.

1. Crie um instantâneo de cada blob de disco rígido virtual, o que leva apenas alguns segundos.

    Para criar um instantâneo, use o [PowerShell](../articles/storage/common/storage-powershell-guide-full.md), a [API REST do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/ee691971.aspx), a [CLI do Azure](/cli/azure/) ou uma das bibliotecas de clientes do Armazenamento do Azure, como [a biblioteca de clientes do Armazenamento para .NET](https://msdn.microsoft.com/library/azure/hh488361.aspx).

1. Inicie a VM, o que encerra o tempo de inatividade. Normalmente, todo o processo é concluído em alguns minutos.

Esse processo gera uma coleção de instantâneos consistentes para todos os discos, fornecendo um ponto de restauração de backup para a VM.

### <a name="copy-the-snapshots-to-another-region"></a>Copiar os instantâneos para outra região

A criação de instantâneos por si só pode não ser suficiente para DR. Também é necessário replicar os backups de instantâneo em outra região.

Se você usar o armazenamento com redundância geográfica ou o armazenamento com redundância geográfica com acesso de leitura para os discos, os instantâneos serão replicados automaticamente para a região secundária. Pode haver alguns minutos de retardo antes da replicação. Se o datacenter primário ficar inativo antes que os instantâneos concluam a replicação, você não poderá acessar os instantâneos no datacenter secundário. A probabilidade de que isso ocorra é pequena.

> [!NOTE]
> Somente ter os discos em uma conta de armazenamento com redundância geográfica ou de armazenamento com redundância geográfica com acesso de leitura não protege a VM contra desastres. Também é necessário criar instantâneos coordenados ou usar o Backup do Azure. Isso é necessário para recuperar uma VM para um estado consistente.

Se estiver usando o armazenamento com redundância local, copie os instantâneos para outra conta de armazenamento imediatamente após a criação do instantâneo. O destino de cópia pode ser uma conta de armazenamento com redundância local em outra região, resultando na localização da cópia em uma região remota. Também copie o instantâneo para uma conta de armazenamento com redundância geográfica com acesso de leitura na mesma região. Nesse caso, o instantâneo é replicado lentamente na região secundária remota. O backup é protegido contra desastres no site primário após a conclusão da cópia e da replicação.

Para copiar os instantâneos incrementais para DR com eficiência, examine as instruções em [Fazer backup de discos não gerenciados de VM do Azure com instantâneos incrementais](../articles/virtual-machines/windows/incremental-snapshots.md).

![Faça backup dos discos não gerenciados de VM do Azure com instantâneos incrementais][2]

### <a name="recovery-from-snapshots"></a>Recuperação com base em instantâneos

Para recuperar um instantâneo, copie-o para criar um novo blob. Se estiver copiando o instantâneo da conta primária, copie o instantâneo sobre o blob base do instantâneo. Esse processo reverte o disco para o instantâneo. Esse processo é conhecido como promoção do instantâneo. Se estiver copiando o backup de instantâneo de uma conta secundária, no caso de uma conta de armazenamento com redundância geográfica com acesso de leitura, você deverá copiá-lo para uma conta primária. Copie um instantâneo [usando o PowerShell](../articles/storage/common/storage-powershell-guide-full.md) ou o utilitário AzCopy. Para obter mais informações, consulte [Transferir dados com o utilitário de linha de comando AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy).

Para VMs com vários discos, é necessário copiar todos os instantâneos que fazem parte do mesmo ponto de restauração coordenado. Depois de copiar os instantâneos para blobs de VHD graváveis, use os blobs para recriar a VM usando o modelo da VM.

## <a name="other-options"></a>Outras opções

### <a name="sql-server"></a>SQL Server

O SQL Server em execução em uma VM tem suas próprias funcionalidades internas para fazer backup do banco de dados do SQL Server para o armazenamento de Blobs do Azure ou um compartilhamento de arquivos. Se a conta de armazenamento for de armazenamento com redundância geográfica ou de armazenamento com redundância geográfica com acesso de leitura, você poderá acessar esses backups no datacenter secundário da conta de armazenamento em caso de desastre, com as mesmas restrições, conforme abordado anteriormente. Para obter mais informações, consulte [Backup e restauração para o SQL Server em máquinas virtuais do Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md). Além de fazer backup e restaurar, os [grupos de disponibilidade AlwaysOn do SQL Server](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md) podem manter réplicas secundárias de bancos de dados. Essa capacidade reduz consideravelmente o tempo de recuperação de desastre.

## <a name="other-considerations"></a>Outras considerações

Este artigo abordou como fazer backup ou tirar instantâneos das VMs e de seus discos para dar suporte à recuperação de desastre e como realizes esses backups ou instantâneos para recuperar seus dados. Com o modelo do Azure Resource Manager, muitas pessoas usam modelos para criar suas VMs e outras infraestruturas no Azure. Use um modelo para criar uma VM que tem sempre a mesma configuração. Se você usar imagens personalizadas para criar as VMs, também deverá verificar se as imagens são protegidas usando uma conta de armazenamento com redundância geográfica com acesso de leitura para armazená-las.

Consequentemente, o processo de backup pode ser uma combinação de duas coisas:

- Fazer backup dos dados (discos).
- Fazer backup da configuração (modelos e imagens personalizadas).

Dependendo da opção de backup escolhida, talvez você precise administrar o backup dos dados e da configuração ou o serviço de backup pode administrar tudo isso para você.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>Apêndice: Noções básicas sobre o impacto da redundância de dados

Para contas de armazenamento no Azure, há três tipos de redundância de dados que devem ser considerados em relação à recuperação de desastre: redundância local, redundância geográfica ou redundância geográfica com acesso de leitura. 

O armazenamento com redundância local retém três cópias dos dados no mesmo datacenter. Quando a VM grava os dados, todas as três cópias são atualizadas antes que uma operação bem-sucedida seja retornada ao chamador, de modo que você saiba que elas são idênticas. O disco está protegido contra falhas locais, pois é improvável que todas as três cópias sejam afetadas ao mesmo tempo. No caso do armazenamento com redundância local, não há nenhuma redundância geográfica e, portanto, o disco não está protegido contra falhas catastróficas que podem afetar todo um datacenter ou uma unidade de armazenamento.

Com o armazenamento com redundância geográfica e o armazenamento com redundância geográfica com acesso de leitura, três cópias dos dados são retidas na região primária escolhida por você. Mais três cópias dos dados são retidas em uma região secundária correspondente que é definida pelo Azure. Por exemplo, se você armazenar dados no Oeste dos EUA, os dados serão replicados no Leste dos EUA. A retenção de cópia é feita de forma assíncrona e há um pequeno atraso entre as atualizações nos sites primário e secundário. As réplicas dos discos no site secundário são consistentes por disco (com o atraso), mas as réplicas de vários discos ativos podem não estar sincronizadas entre si. Para ter réplicas consistentes em vários discos, são necessários instantâneos consistentes.

A principal diferença entre o armazenamento com redundância geográfica e o armazenamento com redundância geográfica com acesso de leitura é que, com o armazenamento com redundância geográfica com acesso de leitura, você pode ler a cópia secundária a qualquer momento. Se houver um problema que torna os dados na região primária inacessíveis, a equipe do Azure envidará todos os esforços para restaurar o acesso. Embora o primário esteja inativo, se você tiver o armazenamento com redundância geográfica com acesso de leitura habilitado, poderá acessar os dados no datacenter secundário. Portanto, se você pretende ler na réplica enquanto o primário estiver inacessível, o armazenamento com redundância geográfica com acesso de leitura deve ser considerado.

Se isso acabar se tornando uma interrupção significativa, a equipe do Azure poderá disparar um failover geográfico e alterar as entradas DNS primárias para que apontem para o armazenamento secundário. Neste ponto, se você tiver o armazenamento com redundância geográfica ou o armazenamento com redundância geográfica com acesso de leitura habilitado, poderá acessar os dados na região que costumava ser a secundária. Em outras palavras, se sua conta de armazenamento for um armazenamento com redundância geográfica e houver um problema, você poderá acessar o armazenamento secundário somente se houver um failover geográfico.

Para obter mais informações, consulte [O que fazer se ocorrer uma interrupção do Armazenamento do Azure](../articles/storage/common/storage-disaster-recovery-guidance.md).

>[!NOTE] 
>A Microsoft controla a ocorrência de um failover. O failover não é controlado por conta de armazenamento e, portanto, isso não é decidido por clientes individuais. Para implementar a recuperação de desastre em contas de armazenamento ou discos de máquina virtual específicos, use as técnicas descritas anteriormente neste artigo.

[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png