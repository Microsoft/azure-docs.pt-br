---
title: Perguntas frequentes sobre o Azure Files| Microsoft Docs
description: Obtenha respostas a perguntas frequentes sobre os Arquivos do Azure. Você pode montar compartilhamentos de arquivos do Azure simultaneamente em implantações locais ou na nuvem do Windows, do Linux ou do macOS.
author: roygara
ms.service: storage
ms.date: 02/23/2020
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: 4d7123aa22d95e3e4c3850be775ddad96f28d280
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785299"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Perguntas frequentes sobre o Azure Files
Os [Arquivos do Azure](storage-files-introduction.md) oferecem compartilhamentos de arquivos totalmente gerenciados na nuvem que são acessíveis por meio do [Protocolo SMB](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) ou do [protocolo NFS (Network File System)](https://en.wikipedia.org/wiki/Network_File_System) (versão prévia) padrão do setor. Você pode montar compartilhamentos de arquivos do Azure simultaneamente em implantações locais ou na nuvem do Windows, do Linux e do macOS. Também é possível armazenar em cache os compartilhamentos de arquivos do Azure em computadores Windows Server usando a Sincronização de Arquivos do Azure para acesso rápido próximo ao local em que os dados são usados.

Este artigo responde perguntas frequentes sobre funcionalidades e recursos do serviço Arquivos do Azure, inclusive sobre o uso dele em combinação com a Sincronização de Arquivos do Azure. Se você não vir a resposta para sua pergunta aqui, poderá entrar em contato conosco pelos seguintes canais (em ordem progressiva):

1. A seção de comentários deste artigo.
2. [Página de P e R da Microsoft para Armazenamento do Microsoft Azure](/answers/topics/azure-file-storage.html).
3. O [UserVoice do Arquivos do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. O Suporte da Microsoft. Para criar uma nova solicitação de suporte, no Portal do Azure, na guia **Ajuda**, selecione o botão **Ajuda + suporte** e, em seguida, selecione **Nova solicitação de suporte**.

## <a name="general"></a>Geral
* <a id="why-files-useful"></a>
  **Por que o serviço Arquivos do Azure é útil?**  
   Você pode usar o serviço Arquivos do Azure para criar compartilhamentos de arquivos na nuvem sem ser responsável por gerenciar a sobrecarga de um servidor físico ou dispositivo. Nós fazemos o trabalho monótono para você, incluindo a aplicação de atualizações do sistema operacional e a substituição de discos defeituosos. Para saber mais sobre os cenários em que os Arquivos do Azure podem ajudar, confira [Por que o serviço Arquivos do Azure é útil](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
  **Quais são as diferentes maneiras de acessar arquivos nos Arquivos do Azure?**  
    Os compartilhamentos de arquivos SMB podem ser montados no computador local usando o protocolo SMB 3.0 ou você pode usar ferramentas como o [Gerenciador de Armazenamento](https://storageexplorer.com/) para acessar os arquivos no compartilhamento de arquivo. Os compartilhamentos de arquivos NFS podem ser montados no computador local copiando/colando o script fornecido pelo portal do Azure. No aplicativo, você pode usar bibliotecas de cliente de armazenamento, APIs REST, o PowerShell ou a CLI do Azure para acessar os arquivos no compartilhamento de arquivos do Azure.

* <a id="what-is-afs"></a>
  **O que é a Sincronização de arquivos do Azure?**  
    Você pode usar a Sincronização de Arquivos do Azure para centralizar os compartilhamentos de arquivos da sua organização no serviço Arquivos do Azure sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. A Sincronização de Arquivos do Azure transforma computadores Windows Server em um cache rápido do seu compartilhamento de arquivos do Azure. Você pode usar qualquer protocolo disponível no Windows Server para acessar seus dados localmente, incluindo o SMB, o NFS (sistema de arquivos de rede) e o FTPS (serviço de protocolo de transferência de arquivos). Você pode ter tantos caches quantos precisar em todo o mundo.

* <a id="files-versus-blobs"></a>
  **Qual é a comparação entre usar um compartilhamento de arquivos do Azure e o Armazenamento de Blobs do Azure para meus dados?**  
    Tanto o serviço Arquivos do Azure quanto o Armazenamento de Blobs do Azure fornecem uma maneira de armazenar grandes quantidades de dados na nuvem, mas são úteis para fins ligeiramente diferentes. 
    
    O Armazenamento de Blobs do Azure é útil para aplicativos de grande escala e nativos de nuvem que precisam armazenar dados não estruturados. Para maximizar o desempenho e o dimensionamento, o Armazenamento de Blobs do Azure é uma abstração de armazenamento mais simples do que um sistema de arquivos real. O Armazenamento de Blobs do Azure só pode ser acessado por bibliotecas de cliente baseadas em REST (ou diretamente por meio do protocolo baseado em REST).

    O serviço Arquivos do Azure é especificamente um sistema de arquivos. O serviço Arquivos do Azure tem todos os resumos de arquivo que você conhece e adora após anos de trabalho com sistemas operacionais locais. Assim como o Armazenamento de Blobs do Azure, o serviço Arquivos do Azure oferece uma interface REST e bibliotecas de cliente baseadas em REST. Ao contrário do Armazenamento de Blobs do Azure, os Arquivos do Azure oferecem acesso via SMB ou NFS aos compartilhamentos de arquivos do Azure. Os compartilhamentos de arquivos podem ser montados diretamente no Windows, no Linux ou no macOS, no local ou em VMs na nuvem, sem a necessidade de escrever nenhum código nem anexar drivers especiais ao sistema de arquivos. Você também pode armazenar em cache os compartilhamentos de arquivos SMB do Azure em servidores de arquivos locais usando a Sincronização de Arquivos do Azure para acesso rápido, perto do local em que os dados são usados. 
   
    Para uma descrição mais detalhada sobre as diferenças entre os Arquivos do Azure e o Armazenamento de Blobs do Azure, confira [Introdução aos principais serviços do Armazenamento do Microsoft Azure](../common/storage-introduction.md). Para saber mais sobre o Armazenamento de Blobs do Azure, confira [Introdução ao armazenamento de Blobs](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Por que usar um compartilhamento de arquivos do Azure em vez dos Discos do Azure?**  
    Um disco nos Discos do Azure é simplesmente um disco. Para obter valor do serviço Discos do Azure, você deve anexar um disco a uma máquina virtual que está em execução no Azure. O serviço Discos do Azure pode ser usado para todos os fins para os quais você usaria um disco em um servidor local. Você pode usá-lo como um disco de sistema do SO, como espaço de permuta para um SO ou como armazenamento dedicado para um aplicativo. Um uso interessante do serviço Discos do Azure é a criação de um servidor de arquivos na nuvem para uso nos mesmos locais em que você usaria um compartilhamento de arquivos do Azure. Implantar um servidor de arquivos em Máquinas Virtuais do Azure é uma forma de alto desempenho de obter o armazenamento de arquivos no Azure quando você precisa de opções de implantação atualmente sem suporte nos Arquivos do Azure. 

    No entanto, executar um servidor de arquivos com os Discos do Azure como armazenamento de back-end geralmente é muito mais caro do que usar um compartilhamento de arquivos do Azure, por vários motivos. Primeiro, além de pagar pelo armazenamento em disco, você também precisa pagar a despesa de execução de uma ou mais VMs do Azure. Em segundo lugar, você também deve gerenciar as VMs que são usadas para executar o servidor de arquivos. Por exemplo, você é responsável por atualizações do SO. Por fim, se precisar que dados sejam armazenados em cache localmente, caberá a você configurar e gerenciar as tecnologias de replicação, por exemplo, a DFSR (Replicação do Sistema de Arquivos Distribuído) para fazer com que isso aconteça.

    Uma abordagem para obter o melhor tanto do serviço Arquivos do Azure quando de um servidor de arquivos hospedado em Máquinas Virtuais do Azure (além de usar o serviço Discos do Azure como armazenamento de back-end) é instalar a Sincronização de Arquivos do Azure no servidor de arquivos hospedado em uma VM na nuvem. Se o compartilhamento dos Arquivos do Azure está na mesma região que o servidor de arquivos, você pode habilitar a disposição em camadas na nuvem e definir o percentual do volume de espaço livre para o máximo (99%). Isso garante o mínimo de duplicação de dados. Você também pode usar quaisquer aplicativos que desejar com seus servidores de arquivo, assim como aplicativos que exigem suporte a protocolo NFS.

    Para obter informações sobre uma opção para definição de um servidor de arquivos de alto desempenho e alta disponibilidade no Azure, confira [Implantando clusters convidados em VMs IaaS no Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). Para uma descrição mais detalhada das diferenças entre os Arquivos do Azure e os Discos do Azure, confira [Introdução aos principais serviços do Armazenamento do Microsoft Azure](../common/storage-introduction.md). Para saber mais sobre os Discos do Azure, confira [Visão geral do Azure Managed Disks](../../virtual-machines/managed-disks-overview.md).

* <a id="get-started"></a>
  **Como posso começar a usar os Arquivos do Azure?**  
   É fácil começar a usar o serviço Arquivos do Azure. Primeiro, [crie um compartilhamento de arquivo SMB](storage-how-to-create-file-share.md) ou um [Como criar um compartilhamento NFS](storage-files-how-to-create-nfs-shares.md) e monte-o no sistema operacional de sua preferência: 

  * [Montar um compartilhamento SMB no Windows](storage-how-to-use-files-windows.md)
  * [Montar um compartilhamento SMB no Linux](storage-how-to-use-files-linux.md)
  * [Montar um compartilhamento SMB no macOS](storage-how-to-use-files-mac.md)
  * [Montar um compartilhamento de arquivo NFS](storage-files-how-to-mount-nfs-shares.md)

    Para obter um guia mais detalhado sobre como implantar um compartilhamento dos Arquivos do Azure para substituir os compartilhamentos de arquivos de produção em sua organização, confira [Planejando a implantação dos Arquivos do Azure](storage-files-planning.md).

* <a id="redundancy-options"></a>
  **Quais opções de redundância de armazenamento têm suporte pelos Arquivos do Azure?**  
    Atualmente, os Arquivos do Azure são compatíveis com LRS (armazenamento com redundância local), ZRS (armazenamento com redundância de zona), GRS (armazenamento com redundância geográfica) e GZRS (armazenamento com redundância geográfica e de zona). Atualmente, a camada Premium dos Arquivos do Azure só dá suporte ao LRS e ao ZRS.

* <a id="tier-options"></a>
  **Atualmente, quais camadas de armazenamento têm suporte no serviço Arquivos do Azure?**  
    Os Arquivos do Azure dão suporte a duas camadas de armazenamento: Premium e Standard. Os compartilhamentos de arquivos Standard são criados em contas de armazenamento de uso geral (GPv1 ou GPv2) e os compartilhamentos de arquivos Premium são criados em contas de armazenamento FileStorage. Saiba mais sobre como criar [compartilhamentos de arquivos Standard](storage-how-to-create-file-share.md) e [compartilhamentos de arquivos Standard Premium](./storage-how-to-create-file-share.md). 
    
    > [!NOTE]
    > Você não pode criar compartilhamentos de arquivos do Azure de contas de armazenamento para blob ou contas de armazenamento de uso geral (GPv1 ou GPv2) *Premium*. Os compartilhamentos de arquivos Standard do Azure precisam ser criados apenas em contas de uso geral *Standard* e os compartilhamentos de arquivo Premium do Azure precisam ser criados apenas em contas FileStorage. Contas de armazenamento de uso geral *Premium* (GPv1 e GPv2) são apenas para blobs de páginas premium. 

* <a id="file-locking"></a>
  **Os Arquivos do Azure dão suporte ao bloqueio de arquivos?**  
    Sim, os Arquivos do Azure têm suporte com bloqueio de arquivo no estilo do Windows, [veja mais informações](/rest/api/storageservices/managing-file-locks).

* <a id="give-us-feedback"></a>
  **Quero muito ver o recurso específico adicionado ao serviço Arquivos do Azure. Você pode adicioná-lo?**  
    A equipe do serviço Arquivos do Azure quer ouvir todos os comentários que você tem a fazer sobre nosso serviço. Vote nas solicitações de recurso no [UserVoice do Arquivos do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Estamos ansiosos para surpreendê-lo com muitos recursos novos.

## <a name="azure-file-sync"></a>Sincronização de Arquivos do Azure

* <a id="afs-region-availability"></a>
  **Quais regiões têm suporte para Sincronização de Arquivos do Azure?**  
    A lista de regiões disponíveis pode ser encontrada na seção [Disponibilidade de região](../file-sync/file-sync-planning.md#azure-file-sync-region-availability) da guia de planejamento da Sincronização de Arquivos do Azure. Adicionaremos suporte continuamente para outras regiões, incluindo regiões não públicas.

* <a id="cross-domain-sync"></a>
  **É possível ter servidores ingressados e não ingressados no domínio no mesmo grupo de sincronização?**  
    Sim. Sim, um grupo de sincronização poderá conter pontos de extremidade do servidor com diferentes associações do Active Directory, mesmo se eles não forem ingressados no domínio. Embora essa configuração funcione tecnicamente, não recomendamos isso como uma configuração típica, já que as ACLs (listas de controle de acesso) que são definidas para arquivos e pastas em um servidor podem não ser impostas por outros servidores no grupo de sincronização. Para obter melhores resultados, recomendamos a sincronização entre servidores na mesma floresta do Active Directory entre servidores que estão em diferentes florestas do Active Directory mas têm relações de confiança estabelecidas ou então entre servidores que não estão em um domínio. Recomendamos que você evite usar uma mistura dessas configurações.

* <a id="afs-change-detection"></a>
  **Criei um arquivo diretamente no meu compartilhamento de arquivos do Azure no SMB ou por meio do portal. Quanto tempo leva para que o arquivo seja sincronizado aos servidores no grupo de sincronização?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]


* <a id="afs-sync-time"></a>
  **Quanto tempo leva para a Sincronização de Arquivos do Azure carregar 1 TiB de dados?**
  
    O desempenho poderá variar conforme as configurações ambientais, a configuração e se esta é uma sincronização inicial ou contínua. Para obter mais informações, confira [Métricas de desempenho da Sincronização de Arquivos do Azure](storage-files-scale-targets.md#azure-file-sync-performance-metrics)

* <a id="afs-conflict-resolution"></a>**Se o mesmo arquivo é alterado em dois servidores quase ao mesmo tempo, o que acontece?**  
    A Sincronização de Arquivos do Azure usa uma estratégia simples de resolução de conflitos: mantemos ambas as alterações nos arquivos que são alterados em dois pontos de extremidade ao mesmo tempo. A alteração gravada mais recentemente mantém o nome do arquivo original. O arquivo mais antigo (determinado por LastWriteTime) tem o nome do ponto de extremidade e o número de conflito acrescentado ao nome do arquivo. Para os pontos de extremidade de servidor, o nome do ponto de extremidade é o nome do servidor. Para os pontos de extremidade na nuvem, o nome do ponto de extremidade é **Nuvem**. O nome segue esta taxonomia: 
   
    \<FileNameWithoutExtension\>-\<endpointName\>\[-#\].\<ext\>  

    Por exemplo, o primeiro conflito de CompanyReport.docx se tornaria CompanyReport-CentralServer.docx se CentralServer fosse o local em que a gravação mais antiga ocorreu. O segundo conflito seria nomeado CompanyReport-CentralServer-1.docx. A Sincronização de Arquivos do Azure suporta 100 arquivos de conflito por arquivo. Depois que o número máximo de arquivos de conflito for atingido, o arquivo não será sincronizado até que o número de arquivos de conflito seja menor que 100.

* <a id="afs-storage-redundancy"></a>
  **Há suporte para armazenamento com redundância geográfica na Sincronização de arquivos do Azure?**  
    Sim, o serviço Arquivos do Azure dá suporte tanto ao LRS (armazenamento com redundância local) quanto ao GRS (armazenamento com redundância geográfica). Se você iniciar um failover de conta de armazenamento entre regiões emparelhadas de uma conta configurada para GRS, a Microsoft recomenda que você trate a nova região como um backup de dados somente. A Sincronização de Arquivos do Azure não inicia automaticamente a sincronização com a nova região primária. 

* <a id="sizeondisk-versus-size"></a>
  **Por que a propriedade *Tamanho em disco* de um arquivo não corresponde à propriedade *Tamanho* depois do uso da Sincronização de arquivos do Azure?**  
  Confira [Noções básicas sobre a camada de nuvem da Sincronização de Arquivos do Azure](../file-sync/file-sync-cloud-tiering-overview.md#tiered-vs-locally-cached-file-behavior).

* <a id="is-my-file-tiered"></a>
  **Como saber se um arquivo está em camadas?**  
  Confira [Understanding Cloud Tiering](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-check-if-your-files-are-being-tiered) (Noções básicas sobre a Camada de Nuvem).

* <a id="afs-recall-file"></a>**Um arquivo que eu desejo usar foi dividido em camadas. Como é possível fazer o recall do arquivo no disco para usá-lo localmente?**  
  Confira [Understanding Cloud Tiering](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-recall-a-tiered-file-to-disk) (Noções básicas sobre a Camada de Nuvem).

* <a id="afs-force-tiering"></a>
  **Como posso forçar um arquivo ou diretório a ficar em camadas?**  
  Confira [Understanding Cloud Tiering](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-force-a-file-or-directory-to-be-tiered) (Noções básicas sobre a Camada de Nuvem).

* <a id="afs-effective-vfs"></a>
  **Como o *espaço livre no volume* é interpretado quando há vários pontos de extremidade do servidor em um volume?**  
  Confira [Understanding Cloud Tiering](../file-sync/file-sync-cloud-tiering-policy.md#multiple-server-endpoints-on-a-local-volume) (Noções básicas sobre a Camada de Nuvem).
  
* <a id="afs-tiered-files-tiering-disabled"></a>
  **Minha camada de nuvem está desabilitada. Por que existem arquivos em camadas na localização do ponto de extremidade de servidor?**  
    Há dois motivos pelos quais os arquivos em camadas podem existir na localização do ponto de extremidade de servidor:

    - Ao adicionar um novo ponto de extremidade de servidor a um grupo de sincronização existente, se você escolher a opção Recuperar namespace primeiro ou Recuperar somente namespace para o modo de download inicial, os arquivos serão exibidos como se estivessem em camadas até serem baixados localmente. Para evitar isso, selecione a opção Evitar arquivos em camadas para o modo de download inicial. Para recuperar arquivos manualmente, use o cmdlet [Invoke-StorageSyncFileRecall](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-recall-a-tiered-file-to-disk).

    - Se a camada de nuvem foi habilitada no ponto de extremidade de servidor e, depois, desabilitada, os arquivos permanecem em camadas até que sejam acessados.

* <a id="afs-tiered-files-not-showing-thumbnails"></a>
  **Por que meus arquivos em camadas não estão mostrando miniaturas nem visualizações no Windows Explorer?**  
    Para os arquivos em camadas, as miniaturas e as visualizações não estarão visíveis no ponto de extremidade de servidor. Esse comportamento é esperado, pois o recurso de cache de miniatura do Windows ignora intencionalmente a leitura de arquivos com o atributo offline. Com a camada de nuvem habilitada, a leitura de arquivos em camadas fará com que eles sejam baixados (uma nova recuperação).

    Esse comportamento não é específico da Sincronização de Arquivos do Azure. O Windows Explorer exibe um "X cinza" em todos os arquivos que têm o atributo offline definido. Você verá o ícone X ao acessar arquivos via SMB. Para obter uma explicação detalhada desse comportamento, veja [https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)

    Caso tenha dúvidas sobre como gerenciar arquivos em camadas, confira [Como gerenciar arquivos em camadas](../file-sync/file-sync-how-to-manage-tiered-files.md).

* <a id="afs-files-excluded"></a>
  **Quais arquivos ou pastas são excluídas automaticamente pela Sincronização de arquivos do Azure?**  
  Veja [Arquivos ignorados](../file-sync/file-sync-planning.md#files-skipped).

* <a id="afs-os-support"></a>
  **Posso usar a Sincronização de arquivos do Azure com o Windows Server 2008 R2, Linux ou o dispositivo NAS (armazenamento conectado à rede)?**  
    Hoje, a Sincronização de Arquivos do Azure é compatível apenas com Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2. Neste momento, não há outros planos que possamos divulgar, mas gostaríamos de dar suporte a outras plataformas com base na demanda dos clientes. Informe-nos quais plataformas você deseja que tenham suporte no [UserVoice do Arquivos do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **Por que os arquivos em camadas existem fora o namespace de ponto de extremidade do servidor?**  
    Antes do agente do Azure File Sync - Sincronização de Arquivos do Azure versão 3, o Azure File Sync bloqueava a movimentação de arquivos em camadas fora do ponto de extremidade do servidor, mas no mesmo volume que o ponto de extremidade do servidor. Operações de cópia, move arquivos não hierárquico e de em camadas para outros volumes foram afetados. O motivo para esse comportamento foi a suposição implícita de que o Explorador de Arquivos e outras APIs do Windows que têm essas operações de movimentação no mesmo volume são operações de renomeação (quase) instantâneas. Isso significa que move fará o Explorador de arquivos ou outros métodos de movimentação (como a linha de comando ou o PowerShell) pode parecer não estar respondendo enquanto a sincronização de arquivos do Azure recupera os dados da nuvem. A partir do [ agente do Azure File Sync versão 3.0.12.0 ](../file-sync/file-sync-release-notes.md#supported-versions), o Azure File Sync permitirá que você mova um arquivo em camadas fora do ponto de extremidade do servidor. Evitamos os efeitos negativos mencionados anteriormente, permitindo que o arquivo em camadas exista como um arquivo em camadas fora do terminal do servidor e, em seguida, recuperando o arquivo em segundo plano. Isso significa que movimentações no mesmo volume são instantâneas e fazemos todo o trabalho para recuperar o arquivo no disco após a movimentação ser concluída. 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **Estou tendo um problema com a Sincronização de Arquivos do Azure no meu servidor (sincronização, nível de nuvem, etc.). Deve remover e recriar o ponto de extremidade do meu servidor?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **Posso mover o serviço de sincronização de armazenamento e/ou a conta de armazenamento para um grupo de recursos, uma assinatura ou um locatário do Azure AD diferente?**  
   Sim, o serviço de sincronização de armazenamento e/ou a conta de armazenamento podem ser movidos para um grupo de recursos, uma assinatura ou um locatário do Azure AD diferente. Depois que a conta de armazenamento ou o serviço de sincronização de armazenamento for movido, você precisará permitir o acesso do aplicativo Microsoft.StorageSync na conta de armazenamento (confira [Verificar se a Sincronização de Arquivos do Azure tem acesso à conta de armazenamento](../file-sync/file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Ao criar o ponto de extremidade na nuvem, o serviço de sincronização de armazenamento e a conta de armazenamento devem estar no mesmo locatário do Azure AD. Depois que o ponto de extremidade na nuvem é criado, o serviço de sincronização de armazenamento e a conta de armazenamento podem ser movidos para diferentes locatários do Azure AD.
    
* <a id="afs-ntfs-acls"></a>
  **Sincronização de arquivos do Azure preserva o nível de diretório/arquivo ACLs do NTFS, juntamente com os dados armazenados em arquivos do Azure?**

    A partir de 24 de fevereiro de 2020, as ACLs novas e existentes colocadas em camadas pela Sincronização de Arquivos do Azure serão persistidas no formato NTFS e as modificações de ACL feitas diretamente no Compartilhamento de Arquivos do Azure serão sincronizadas em todos os servidores no grupo de sincronização. As alterações nas ACLs feitas nos Arquivos do Azure serão sincronizadas por meio da Sincronização de Arquivos do Azure. Ao copiar dados para os Arquivos do Azure, use uma ferramenta de cópia que dê suporte à "fidelidade" necessária para copiar atributos, carimbos de data/hora e ACLs em um compartilhamento de arquivo do Azure, via SMB ou REST. Ao usar as ferramentas de cópia do Azure, como o AzCopy, é importante usar a última versão. Verifique a [tabela de ferramentas de cópia de arquivo](storage-files-migration-overview.md#file-copy-tools) para obter uma visão geral das ferramentas de cópia do Azure e garantir a cópia de todos os metadados importantes de um arquivo.

    Se você tiver habilitado o Backup do Azure em seus compartilhamentos de arquivos gerenciados de sincronização de arquivos, as ACLs de arquivo poderão continuar a ser restauradas como parte do fluxo de trabalho de restauração de backup. Isso funciona para todo o compartilhamento ou arquivos/diretórios individuais.

    Se estiver usando instantâneos como parte da solução de backup autogerenciado para compartilhamentos de arquivos gerenciados pela sincronização de arquivos, suas ACLs poderão ser restauradas incorretamente para ACLs NTFS se os instantâneos forem anteriores a 24 de fevereiro de 2020. Se isso ocorrer, você pode entrar em contato com o Suporte do Azure.

* <a id="afs-lastwritetime"></a>
  **A Sincronização de Arquivos do Azure sincroniza o LastWriteTime para os diretórios?**  
    Não, a Sincronização de Arquivos do Azure não sincroniza o LastWriteTime para os diretórios. Isso ocorre por design.
    
## <a name="security-authentication-and-access-control"></a>Segurança, autenticação e controle de acesso
* <a id="ad-support"></a>
**Há suporte nos Arquivos do Azure para controle de acesso e autenticação baseado em identidade?**  
    
    Sim, os Arquivos do Azure dão suporte a controle de acesso e autenticação baseada em identidade. Você pode escolher uma destas duas maneiras de usar o controle de acesso baseado em identidade: Active Directory Domain Services local ou Azure AD DS (Azure Active Directory Domain Services). O Active Directory Domain Services (AD DS) local tem suporte a autenticação usando computadores ingressados no domínio do AD DS, seja local ou no Azure, para acessar compartilhamentos de arquivos do Azure pelo SMB. A autenticação do Azure AD DS por meio de SMB para os Arquivos do Azure permite que VMs Windows ingressadas no domínio do Azure AD DS acessem compartilhamentos, diretórios e arquivos usando as credenciais do Azure Active Directory. Para mais informações, consulte [Visão geral do suporte à autenticação baseada em identidade dos Arquivos do Azure para acesso do SMB](storage-files-active-directory-overview.md). 

    O Azure Files oferece duas maneiras adicionais de gerenciar o controle de acesso:

    - Você pode usar SAS (assinaturas de acesso compartilhado) para gerar tokens que têm permissões específicas e que são válidos para um intervalo de tempo especificado. Por exemplo, você pode gerar um token com acesso somente leitura a um arquivo específico que tem validade de 10 minutos. Qualquer pessoa que tem esse token enquanto ele é válido tem acesso somente leitura ao arquivo por 10 minutos. As chaves de assinatura de acesso compartilhado têm suporte apenas via API REST ou em bibliotecas de cliente. Você deve montar o compartilhamento de arquivos do Azure no SMB usando as chaves de conta de armazenamento.

    - A Sincronização de Arquivos do Azure preserva e replica todas as ACLs discricionárias ou DACLs (sejam elas baseadas no Active Directory ou locais) para todos os pontos de extremidade do servidor aos quais ela é sincronizada. 
    
    Você pode consultar [Autorizar acesso ao Armazenamento do Microsoft Azure](../common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para ver uma representação abrangente de todos os protocolos compatíveis nos serviços do Armazenamento do Microsoft Azure. 
    
* <a id="encryption-at-rest"></a>
**Como garantir que o compartilhamento de arquivos do Azure está criptografado em repouso?**  

    Sim. Para saber mais, consulte [Criptografia do Serviço de Armazenamento do Microsoft Azure](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Como posso fornecer acesso a um arquivo específico usando um navegador da Web?**  

    Você pode usar assinaturas de acesso compartilhado para gerar tokens que têm permissões específicas e que são válidos para um intervalo de tempo especificado. Por exemplo, você pode gerar um token que dá acesso somente leitura a um arquivo específico para um período de tempo definido. Qualquer pessoa que tem a URL pode acessar o arquivo diretamente em qualquer navegador da Web enquanto o token é válido. Você pode gerar uma chave de assinatura de acesso compartilhado facilmente por meio de uma interface do usuário como o Gerenciador de Armazenamento.

* <a id="file-level-permissions"></a>
**É possível especificar permissões somente leitura ou somente gravação em pastas no compartilhamento?**  

    Se você montar o compartilhamento de arquivos via SMB, você não terá controle em nível de pasta sobre as permissões. No entanto, se você criar uma assinatura de acesso compartilhado usando a API REST ou as bibliotecas de cliente, você poderá especificar permissões somente leitura ou somente gravação em pastas contidas no compartilhamento.

* <a id="ip-restrictions"></a>
**Posso implementar restrições de IP para um compartilhamento de Arquivos do Azure?**  

    Sim. O acesso ao compartilhamento de arquivos do Azure pode ser restringido ao nível de conta de armazenamento. Para saber mais, consulte [Configurar Redes Virtuais e Firewalls de Armazenamento do Azure](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**A quais políticas de conformidade de dados o serviço Arquivos do Azure dá suporte?**  

   O Arquivos do Azure é executado com base na mesma arquitetura de armazenamento usada em outros serviços de armazenamento no Armazenamento do Azure. O Arquivos do Azure aplica as mesmas políticas de conformidade de dados que são usadas em outros serviços de armazenamento do Azure. Para obter mais informações sobre a conformidade de dados do Armazenamento do Azure, você pode consultar as [ofertas de conformidade do Armazenamento do Microsoft Azure ](../common/storage-compliance-offerings.md) e ir à [Central de Confiabilidade da Microsoft](https://microsoft.com/trustcenter/default.aspx).

* <a id="file-auditing"></a>
**Como posso auditar o acesso aos arquivos e às alterações nos Arquivos do Azure?**

  Há duas opções que fornecem funcionalidade de auditoria para os Arquivos do Azure:
  - Se os usuários estiverem acessando o compartilhamento de arquivo do Azure diretamente, [os logs do Armazenamento do Azure (versão prévia)](../blobs/monitor-blob-storage.md?tabs=azure-powershell#analyzing-logs) poderão ser usados para controlar as alterações de arquivo e o acesso do usuário. Esses logs podem ser usados para fins de solução de problemas e as solicitações são registradas com os melhores esforços.
  - Se os usuários estiverem acessando o compartilhamento de arquivo do Azure por meio de um Windows Server com o agente de Sincronização de Arquivos do Azure instalado, use uma [política de auditoria](/windows/security/threat-protection/auditing/apply-a-basic-audit-policy-on-a-file-or-folder) ou um produto de terceiros para controlar as alterações de arquivos e o acesso do usuário no Windows Server. 
   
### <a name="ad-ds--azure-ad-ds-authentication"></a>AD DS e Autenticação do Azure AD DS
* <a id="ad-support-devices"></a>
**A autenticação do Azure Active Directory Domain Services (Azure AD DS) dos Arquivos do Azure é compatível com acesso do SMB usando credenciais do Azure Active Directory de dispositivos associados ou registrados com o Azure Active Directory?**

    Não, este cenário não é suportado.

* <a id="ad-vm-subscription"></a>
**Posso acessar compartilhamentos de arquivo do Azure com credenciais do Azure Active Directory de uma VM em uma assinatura diferente?**

    Se a assinatura sob a qual o compartilhamento de arquivos foi implantado estiver associada ao mesmo locatário do Azure AD que a implantação do Azure AD DS para a qual a VM ingressou no domínio, você poderá acessar os compartilhamentos de arquivo do Azure usando as mesmas credenciais do Azure AD. A limitação é imposta não na assinatura, mas no locatário associado do Azure AD.
    
* <a id="ad-support-subscription"></a>
**Posso habilitar o Azure AD DS ou a autenticação de AD DS local para compartilhamentos de arquivos do Azure usando um locatário do Azure AD que seja diferente do locatário principal do compartilhamento de arquivos do Azure?**

    Não, os Arquivos do Azure só oferecem suporte à integração do Azure AD ou do AD DS local com um locatário do Azure AD que reside na mesma assinatura do compartilhamento de arquivos. Somente uma assinatura pode ser associada a um locatário do Azure AD. Essa limitação se aplica tanto ao Azure AD DS quanto aos métodos de autenticação do AD DS local. Ao usar AD DS locais para autenticação, [a credencial do AD DS deve ser sincronizada com o do Azure Active Directory](../../active-directory/hybrid/how-to-connect-install-roadmap.md) ao qual a conta de armazenamento está associada.

* <a id="ad-linux-vms"></a>
**A autenticação do Azure AD DS ou do AD DS local para compartilhamentos de arquivos do Azure tem suporte a VMs do Linux?**

    Não, a autenticação das VMs do Linux não é suportada.

* <a id="ad-aad-smb-afs"></a>
**Os compartilhamentos de arquivos gerenciados pela Sincronização de Arquivos do Azure dão suporte à autenticação do AD DS local ou do Azure AD DS?**

    Sim, você pode habilitar o Azure AD DS ou a autenticação do AD DS local em um compartilhamento de arquivos gerenciado pela Sincronização de Arquivos do Azure. As alterações nas ACLs de NTFS de diretório/arquivo em servidores de arquivos locais serão dispostas em camadas para Arquivos do Azure e vice-versa.

* <a id="ad-aad-smb-files"></a>
**Como posso verificar se eu habilitei a autenticação AD DS na minha conta de armazenamento e recuperar as informações de domínio?**

    Para obter instruções, consulte [aqui](./storage-files-identity-ad-ds-enable.md#confirm-the-feature-is-enabled).

* <a id=""></a>
**A autenticação do Azure Active Directory dos Arquivos do Azure tem suporte para VMs do Linux?**

    Não, a autenticação das VMs do Linux não é suportada.

* <a id="ad-multiple-forest"></a>
**A autenticação do AD DS local para compartilhamentos de arquivos do Azure tem suporte à integração com um ambiente de AD DS usando várias florestas?**    

    A autenticação do AD DS local dos Arquivos do Azure se integra apenas com a floresta do serviço de domínio em que a conta de armazenamento está registrada. Para dar suporte à autenticação de outra floresta, seu ambiente precisar ter uma relação de confiança de floresta configurada corretamente. O modo que os Arquivos do Azure se registra no AD DS é quase o mesmo de um servidor de arquivos comum, onde ele cria uma identidade (conta de computador ou de logon de serviço) no AD DS para autenticação. A única diferença é que o SPN registrado da conta de armazenamento termina com "file.core.windows.net", que não corresponde ao sufixo do domínio. Consulte o administrador de domínio para ver se você precisa atualizar a política de roteamento de sufixo para habilitar a autenticação de várias florestas devido ao sufixo de domínio diferente. Fornecemos um exemplo abaixo para configurar a política de roteamento de sufixo.
    
    Exemplo: quando os usuários do domínio da floresta A desejam acessar um compartilhamento de arquivo com a conta de armazenamento registrada em um domínio da floresta B, isso não funcionará automaticamente porque a entidade de serviço da conta de armazenamento não tem um sufixo correspondente ao sufixo de qualquer domínio da floresta A. Podemos resolver esse problema configurando manualmente uma regra de roteamento de sufixo da floresta A para a floresta B para o sufixo personalizado "file.core.windows.net".
    Primeiro, é necessário adicionar um novo sufixo personalizado à floresta B. Verifique se você tem as permissões administrativas apropriadas para alterar a configuração e siga estas etapas:   
    1. Faça logon em um domínio de computador conectado à floresta B
    2.  Abra o console de "Domínios e Relações de Confiança do Active Directory"
    3.  Clique com o botão direito do mouse em "Domínios e Relações de Confiança do Active Directory"
    4.  Clique em "Propriedades"
    5.  Clique em "Adicionar"
    6.  Adicione "file.core.windows.net" como os sufixos UPN
    7.  Clique em "Aplicar" e em "OK" para fechar o assistente
    
    Em seguida, adicione a regra de roteamento de sufixo à floresta A, de modo que ela seja redirecionada para a floresta B.
    1.  Faça logon em um domínio de computador conectado à floresta A
    2.  Abra o console de "Domínios e Relações de Confiança do Active Directory"
    3.  Clique com o botão direito do mouse no domínio em que deseja acessar o compartilhamento de arquivo, clique na guia "Relações de Confiança" e selecione domínio da floresta B nas relações de confiança de saída. Se você não configurou a relação de confiança entre as duas florestas, configure-a primeiro
    4.  Clique em "Propriedades…" e em "Roteamento de Sufixo de Nome"
    5.  Verifique se o sufixo "*.file.core.windows.net" é exibido. Caso contrário, clique em 'Atualizar'
    6.  Selecione "*.file.core.windows.net" e clique em "Habilitar" e "Aplicar"

* <a id=""></a>
**Quais regiões estão disponíveis para a autenticação do AD DS nos Arquivos do Azure?**

    Consulte a [disponibilidade regional do AD DS](storage-files-identity-auth-active-directory-enable.md#regional-availability) para obter detalhes.
    
* <a id="ad-aad-smb-afs"></a>
**Posso aproveitar a autenticação do AD (Active Directory) nos Arquivos do Azure em compartilhamentos de arquivos gerenciados pela Sincronização de Arquivos do Azure?**

    Sim, você pode habilitar a autenticação do AD em um compartilhamento de arquivos gerenciado pela Sincronização de Arquivos do Azure. As alterações nas ACLs de NTFS de diretório/arquivo em servidores de arquivos locais serão dispostas em camadas para Arquivos do Azure e vice-versa.

* <a id="ad-aad-smb-files"></a>
**Há alguma diferença na criação de uma conta de computador ou de conta de logon de serviço para representar minha conta de armazenamento no AD?**

    Não há diferença entre criar uma [conta de computador](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (padrão) ou uma [conta de logon de serviço](/windows/win32/ad/about-service-logon-accounts) quanto ao funcionamento da autenticação com os Arquivos do Azure. Você pode escolher como representar uma conta de armazenamento como uma identidade em seu ambiente do AD. A configuração DomainAccountType padrão no cmdlet Join-AzStorageAccountForAuth é conta de computador. No entanto, o tempo de expiração de senha configurado em seu ambiente AD para conta de computador ou de logon de serviço pode ser diferente e você precisa levar isso em consideração para [Atualizar a senha da sua identidade de conta de armazenamento no AD](./storage-files-identity-ad-ds-update-password.md).
 
* <a id="ad-support-rest-apis"></a>
**Existem APIs REST que dão suporte a ACLs do Windows para obter/definir/copiar diretório/arquivo?**

    Sim, oferecemos suporte a APIs REST que obtêm, configuram ou copiam ACLs NTFS para diretórios ou arquivos ao usar a API REST de [07-07-2019](/rest/api/storageservices/versioning-for-the-azure-storage-services#version-2019-07-07) (ou posterior). Também damos suporte à persistência de ACLs do Windows em ferramentas baseadas em REST: [AzCopy v10.4 e posterior](https://github.com/Azure/azure-storage-azcopy/releases).

* <a id="ad-support-rest-apis"></a>
**Como remover as credenciais armazenadas em cache com a chave da conta de armazenamento e excluir as conexões SMB existentes antes de inicializar uma nova conexão com as credenciais do Azure AD ou do AD?**

    Siga o processo de duas etapas abaixo para remover a credencial salva associada à chave da conta de armazenamento e remover a conexão SMB: 
    1. Execute o cmdlet abaixo no Windows Cmd.exe para remover a credencial. Se você não encontrar nenhuma, isso indicará que você não persistiu a credencial e poderá ignorar esta etapa.
    
       cmdkey /delete:Domain:target=storage-account-name.file.core.windows.net
    
    2. Exclua a conexão existente com o compartilhamento de arquivo. Você pode especificar o caminho de montagem como a letra da unidade montada ou o caminho storage-account-name.file.core.windows.net.
    
       net use <letra-da-unidade/caminho-do-compartilhamento> /delete

## <a name="network-file-system"></a>NFS

* <a id="when-to-use-nfs"></a>
**Quando devo usar o NFS dos Arquivos do Azure?**

    Confira [Compartilhamentos NFS (versão prévia)](storage-files-compare-protocols.md#nfs-shares-preview).

* <a id="backup-nfs-data"></a>
**Como fazer backup dos dados armazenados em compartilhamentos NFS?**

    O backup dos seus dados em compartilhamentos NFS pode ser orquestrado com ferramentas conhecidas, como o rsync, ou com produtos de um dos nossos parceiros de backup de terceiros. Vários parceiros de backup, incluindo o [Commvault](https://documentation.commvault.com/commvault/v11/article?p=92634.htm), o [Veeam](https://www.veeam.com/blog/?p=123438) e o [Veritas](https://players.brightcove.net/4396107486001/default_default/index.html?videoId=6189967101001), faziam parte da nossa versão prévia inicial e estenderam essas soluções para trabalhar com o SMB 3.0 e o NFS 4.1 nos Arquivos do Azure.

* <a id="migrate-nfs-data"></a>
**Posso migrar os dados existentes para um compartilhamento NFS?**

    Em uma região, você pode usar ferramentas padrão como o scp, o rsync ou o SSHFS para mover os dados. Como o NFS dos Arquivos do Azure pode ser acessado em várias instâncias de computação simultaneamente, você pode aprimorar a cópia de velocidades com uploads paralelos. Caso deseje trazer dados de fora de uma região, use uma VPN ou um ExpressRoute para montagem no sistema de arquivos usando o data center local.

## <a name="on-premises-access"></a>Acesso local

* <a id="port-445-blocked"></a>
**Meu provedor de internet ou TI bloqueia a porta 445 causando erro na montagem dos Arquivos do Azure. O que devo fazer?**

    Você pode conhecer [várias maneiras soluções alternativas para a porta 445 bloqueada aqui](./storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked). Os Arquivos do Azure só permitem conexões usando SMB 3.0 (com suporte de criptografia) de fora da região ou do datacenter. O protocolo SMB 3.0 introduziu muitos recursos de segurança, inclusive criptografia de canal que é muito segura para uso na internet. No entanto, é possível que a porta 445 tenha sido bloqueada devido a motivos históricos de vulnerabilidades encontradas em versões SMB inferiores. O ideal é bloquear a porta para tráfego de SMB 1.0 e o SMB 1.0 deve estar desativado em todos os clientes.

* <a id="expressroute-not-required"></a>
**Preciso usar o Azure ExpressRoute para me conectar aos Arquivos do Azure ou para usar a Sincronização de arquivos do Azure localmente?**  

    Não. O ExpressRoute não é necessário para acessar um compartilhamento de arquivos do Azure. Se você está montando um compartilhamento de arquivos do Azure diretamente localmente, basta ter a porta 445 (TCP de saída) aberta para acesso à Internet (essa é a porta pela qual o SMB se comunica). Se você está usando a Sincronização de Arquivos do Azure, basta ter a porta 443 (TCP de saída) para acesso HTTPS (não é necessário usar SMB). No entanto, você *pode* usar ExpressRoute com qualquer uma dessas opções de acesso.

* <a id="mount-locally"></a>
**Como posso montar o compartilhamento de arquivos do Azure no meu computador local?**  

    Você poderá montar o compartilhamento de arquivos por meio do protocolo SMB, desde que a porta 445 (TCP de Saída) esteja aberta e o cliente dê suporte ao protocolo SMB 3.0 (por exemplo, se você estiver usando o Windows 10 ou o Windows Server 2016). Se a porta 445 está bloqueada pela política da sua organização ou por pelo ISP, você pode usar a Sincronização de Arquivos do Azure para acessar o compartilhamento de arquivos do Azure.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Como posso fazer backup do meu compartilhamento de arquivos do Azure?**  
    Você pode usar [instantâneos de compartilhamento](storage-snapshots-files.md) periódicos para proteção contra exclusões acidentais. Use o AzCopy, o Robocopy ou uma ferramenta de backup de terceiros que possa fazer backup de um compartilhamento de arquivos montado. O Backup do Azure oferece backup do Azure Files. Saiba mais sobre [fazer dos compartilhamos de arquivo do Azure pelo Backup do Microsoft Azure](../../backup/backup-afs.md).

## <a name="share-snapshots"></a>Instantâneos de compartilhamento

### <a name="share-snapshots-general"></a>Instantâneos de compartilhamento: Geral
* <a id="what-are-snaphots"></a>
**O que são os instantâneos de compartilhamento de arquivo?**  
    Você pode usar os instantâneos de compartilhamento de arquivos do Azure para criar versões somente leitura de seus compartilhamentos de arquivos. Você também pode usar o serviço Arquivos do Azure para copiar uma versão anterior do conteúdo para o mesmo compartilhamento ou para um local alternativo no Azure ou local para modificações adicionais. Para saber mais sobre instantâneos de compartilhamento, consulte a [Visão geral de instantâneos de compartilhamento](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>
**Onde os instantâneos de compartilhamento são armazenados?**  
    Os instantâneos de compartilhamento são armazenados na mesma conta de armazenamento do compartilhamento de arquivos.

* <a id="snapshot-consistency"></a>
**Os instantâneos de compartilhamento são consistentes com o aplicativo?**  
    Não, os instantâneos de compartilhamento não são consistentes com o aplicativo. O usuário deve liberar as gravações do aplicativo para o compartilhamento antes de tirar um instantâneo de compartilhamento.

* <a id="snapshot-limits"></a>
**Há limites para o número de instantâneos de compartilhamento que posso usar?**  
    Sim. O serviço Arquivos do Azure pode armazenar um máximo de 200 instantâneos de compartilhamento. Os instantâneos de compartilhamento não são considerados na cota de compartilhamento e, portanto, não há nenhum limite por compartilhamento no espaço total utilizado por todos os instantâneos de compartilhamento. Os limites de conta de armazenamento ainda se aplicam. Depois de 200 instantâneos de compartilhamento, os instantâneos mais antigos precisarão ser excluídos para que seja possível criar novos instantâneos de compartilhamento.

* <a id="snapshot-cost"></a>
**Quanto custam os instantâneos de compartilhamento?**  
    Transação padrão e custo de armazenamento padrão serão aplicados ao instantâneo. Os instantâneos de compartilhamento são incrementais por natureza. O instantâneo base é o próprio compartilhamento. Todos os instantâneos subsequentes são incrementais e armazenam somente a diferença do instantâneo de anterior. Isso significa que as alterações delta que serão vistas na lista serão mínimas se a variação de carga de trabalho for mínima. Consulte a [Página de preços](https://azure.microsoft.com/pricing/details/storage/files/) para obter informações sobre preços os arquivos do Standard Azure Files. Hoje a forma de olhar o tamanho consumido por compartilhamento de instantâneo é comparando a capacidade cobrada com capacidade utilizada. Estamos trabalhando em ferramentas para melhorar a emissão de relatórios.

* <a id="ntfs-acls-snaphsots"></a>
**São as ACLs do NTFS em diretórios e arquivos mantidos em instantâneos de compartilhamento?**  
    ACLs do NTFS em diretórios e arquivos são mantidas em instantâneos de compartilhamento.

### <a name="create-share-snapshots"></a>Criar instantâneos de compartilhamento
* <a id="file-snaphsots"></a>
**Posso criar instantâneo de compartilhamento de arquivos individuais?**  
    Os instantâneos de compartilhamento são criados no nível do compartilhamento de arquivos. Você pode restaurar arquivos individuais do instantâneo de compartilhamento de arquivos, mas não é possível criar instantâneos de compartilhamento no nível do arquivo. No entanto, se você tirou um instantâneo no nível do compartilhamento e desejar listar instantâneos de compartilhamento em que determinado arquivo foi alterado, pode fazer isso em **Versões Anteriores** em um compartilhamento montado no Windows. 
    
    Se você precisar de um recurso de instantâneo de arquivo, informe-nos em [UserVoice do Arquivos do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encrypted-snapshots"></a>
**Posso criar instantâneos de um compartilhamento de arquivos criptografado?**  
    Você pode tirar um instantâneo de compartilhamento de compartilhamentos de arquivos do Azure que têm a opção de criptografia em repouso habilitada. Você pode restaurar arquivos de um instantâneo de compartilhamento em um compartilhamento de arquivos criptografado. Se o compartilhamento for criptografado, o instantâneo de compartilhamento também será criptografado.

* <a id="geo-redundant-snaphsots"></a>
**Meus instantâneos de compartilhamento têm redundância geográfica?**  
    O instantâneo de compartilhamento tem a mesma redundância que o compartilhamento de arquivos do Azure para o qual ele foi criado. Se você tiver selecionado o armazenamento com redundância geográfica para sua conta, o instantâneo de compartilhamento também será armazenado de forma redundante na região emparelhada.

### <a name="manage-share-snapshots"></a>Gerenciar instantâneos de compartilhamento
* <a id="browse-snapshots-linux"></a>
**Posso navegar pelos meus instantâneos de compartilhamento no Linux?**  
    Você pode usar a CLI do Azure para criar, listar, procurar e restaurar instantâneos de compartilhamento no Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Posso copiar os instantâneos de compartilhamento em uma conta de armazenamento diferente?**  
    Você pode copiar arquivos dos instantâneos de compartilhamento para outro local, mas não pode copiar os instantâneos compartilhamento propriamente ditos.

### <a name="restore-data-from-share-snapshots"></a>Restaurar dados de instantâneos de compartilhamento
* <a id="promote-share-snapshot"></a>
**Posso promover um instantâneo de compartilhamento a compartilhamento base?**  
    Você pode copiar dados de um instantâneo de compartilhamento para qualquer outro destino. Você não pode promover um instantâneo de compartilhamento para o compartilhamento base.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Posso restaurar dados do meu instantâneo de compartilhamento em uma conta de armazenamento diferente?**  
    Sim. Arquivos de um compartilhamento de instantâneo podem ser copiados para o local original ou para um local alternativo que inclua a mesma conta de armazenamento ou uma conta de armazenamento diferente, na mesma região ou em regiões diferentes. Você também pode copiar arquivos para uma localização local ou para qualquer outra nuvem.    
  
### <a name="clean-up-share-snapshots"></a>Limpar instantâneos de compartilhamento
* <a id="delete-share-keep-snapshots"></a>
**Posso excluir meu compartilhamento sem excluir os instantâneos de compartilhamento?**  
    Se você tiver instantâneos de compartilhamento ativos no compartilhamento, não será possível excluí-lo. Você pode usar uma API para excluir compartilhamentos de instantâneos, juntamente com o compartilhamento. Você também pode excluir os instantâneos de compartilhamento e o compartilhamento no Portal do Azure.

* <a id="delete-share-with-snapshots"></a>
**O que acontece com meus instantâneos de compartilhamento se eu excluo minha conta de armazenamento?**  
    Se você excluir sua conta de armazenamento, os instantâneos de compartilhamento também serão excluídos.

## <a name="billing-and-pricing"></a>Cobrança e preços
* <a id="vm-file-share-network-traffic"></a>
**O tráfego de rede entre uma VM do Azure e um compartilhamento de arquivos do Azure conta como largura de banda externa cobrada na assinatura?**  
    Se o compartilhamento de arquivos e a VM estiverem na mesma região do Azure, não há encargos adicionais para o tráfego entre o compartilhamento de arquivos e a VM. Se o compartilhamento de arquivos e a VM estiverem em regiões diferentes, o tráfego entre eles será cobrado como largura de banda externa.

* <a id="share-snapshot-price"></a>
**Quanto custam os instantâneos de compartilhamento?**  
     Durante a versão prévia, não há nenhum encargo para a capacidade do compartilhamento de instantâneo. Os custos de saída e de transação do armazenamento Standard se aplicam. Após a disponibilidade geral, as assinaturas serão cobradas por capacidade e por transações de instantâneos de compartilhamento.
     
     Os instantâneos de compartilhamento são incrementais por natureza. O instantâneo de compartilhamento base é o próprio compartilhamento. Todos os instantâneos de compartilhamento subsequentes são incrementais e armazenam somente a diferença do instantâneo de compartilhamento anterior. Você será cobrado somente pelo conteúdo alterado. Se você tiver um compartilhamento com 100 GiB de dados, mas apenas 5 GiB foram alterados após o último instantâneo de compartilhamento, este consumirá apenas 5 GiB adicionais e você será cobrado por 105 GiB. Para obter mais informações sobre encargos de transações e de saída Standard, consulte a [Página de preços](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Escala e desempenho
* <a id="files-scale-limits"></a>
**Quais são os limites de escala dos Arquivos do Azure?**  
    Para obter informações sobre as metas de escalabilidade e desempenho do Arquivos do Azure, consulte [Metas de escalabilidade e desempenho do Arquivos do Azure](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**Quais tamanhos estão disponíveis para compartilhamentos de arquivos do Azure?**  
    Os tamanhos de compartilhamento de arquivos do Azure (Premium e Standard) podem ser escalados verticalmente para 100 TiB. Consulte a seção [integração para compartilhamentos de arquivos maiores (camada Standard)](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) do guia de planejamento para obter instruções de integração para compartilhamentos de arquivos maiores para a camada Standard.

* <a id="lfs-performance-impact"></a>
**Expandir minha cota de compartilhamento de arquivo afeta minhas cargas de trabalho ou a Sincronização de Arquivos do Azure?**
    
    Não. Expandir a cota não afetará suas cargas de trabalho ou a Sincronização de Arquivos do Azure.

* <a id="open-handles-quota"></a>
**Quantos clientes podem acessar simultaneamente o mesmo arquivo?**    
    Há uma cota de 2.000 identificadores abertos em um único arquivo. Quando você tem 2.000 identificadores abertos, uma mensagem de erro é exibida informando que a cota foi atingida.

* <a id="zip-slow-performance"></a>
**O desempenho é lento quando eu descompacto arquivos no Arquivos do Azure. O que devo fazer?**  
    Para transferir grandes quantidades de arquivos para o Arquivos do Azure, recomendamos o uso do AzCopy (para Windows, em versão prévia para Linux e UNIX) ou do Azure PowerShell. Essas ferramentas foram otimizadas para transferência de rede.

* <a id="slow-perf-windows-81-2012r2"></a>
**Por que o desempenho está lento após eu montar meu compartilhamento de arquivos do Azure no Windows Server 2012 R2 ou no Windows 8.1?**  
    Há um problema conhecido ao montar um compartilhamento de arquivos do Azure no Windows Server 2012 R2 e no Windows 8.1. O problema foi corrigido na atualização cumulativa de abril de 2014 para o Windows 8.1 e o Windows Server 2012 R2. Para um melhor desempenho, verifique se todas as instâncias do Windows Server 2012 R2 e do Windows 8.1 têm esse patch aplicado. (Você sempre deve receber patches do Windows por meio do Windows Update.) Para obter mais informações, consulte o artigo da Base de Dados de Conhecimento Microsoft associado, [Desempenho lento ao acessar o Arquivos do Azure no Windows 8.1 ou Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Recursos e interoperabilidade com outros serviços
* <a id="cluster-witness"></a>
**É possível usar o compartilhamento de arquivos do Azure como uma *Testemunha de Compartilhamento de Arquivos* para o Cluster de Failover do Windows Server?**  
    Atualmente, em um compartilhamento de arquivos do Azure, não há suporte para essa configuração. Para saber mais sobre como configurar isso para o Armazenamento de Blobs do Azure, confira [Implantar uma testemunha de nuvem em um cluster de failover](/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**Posso montar um compartilhamento de arquivos do Azure em uma instância de Contêiner do Azure?**  
    Sim, os compartilhamentos de arquivos do Azure são uma opção fantástica para manter informações além do tempo de vida de uma instância de contêiner. Para obter mais informações, consulte [Montar um compartilhamento de arquivos do Azure com Instâncias de Contêiner do Azure](../../container-instances/container-instances-volume-azure-files.md).

* <a id="rest-rename"></a>
**Há uma operação de renomeação na API REST?**  
    Não no momento.

* <a id="nested-shares"></a>
**Eu posso configurar compartilhamentos aninhados? Em outras palavras, um compartilhamento em um compartilhamento?**  
    Não. O compartilhamento de arquivos *é* o driver virtual que você pode montar; portanto, não há suporte para compartilhamentos aninhados.

* <a id="ibm-mq"></a>
**Como posso usar os Arquivos do Azure com o IBM MQ?**  
    A IBM liberou um documento que ajuda clientes do IBM MQ a configurar o Arquivos do Azure juntamente com o serviço IBM. Para obter mais informações, consulte [Como configurar o gerenciador de filas com várias instâncias do IBM MQ com o serviço Arquivos do Microsoft Azure](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Confira também
* [Solucionar problemas do Arquivos do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Solucionar problemas do Arquivos do Azure no Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Solucionar problemas da Sincronização de Arquivos do Azure](../file-sync/file-sync-troubleshoot.md)
