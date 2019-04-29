---
title: Perguntas frequentes sobre o Azure Files| Microsoft Docs
description: Encontre respostas para perguntas frequentes sobre o Arquivos do Azure.
services: storage
author: roygara
ms.service: storage
ms.date: 01/02/2019
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: 77f07394eb895c08ef3e366bd486b4270714c2fc
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766270"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Perguntas frequentes sobre o Azure Files
[ Os arquivos do Azure](storage-files-introduction.md) oferecem compartilhamentos de arquivos totalmente gerenciados na nuvem que são acessíveis por meio do {SM} protocolo [de padrão do setor](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx). Você pode montar compartilhamentos de arquivos do Azure simultaneamente em implantações locais ou na nuvem do Windows, do Linux e do macOS. Também é possível armazenar em cache os compartilhamentos de arquivos do Azure em computadores Windows Server usando a Sincronização de Arquivos do Azure para acesso rápido próximo ao local em que os dados são usados.

Este artigo responde perguntas frequentes sobre funcionalidades e recursos do serviço Arquivos do Azure, inclusive sobre o uso dele em combinação com a Sincronização de Arquivos do Azure. Se você não vir a resposta para sua pergunta aqui, poderá entrar em contato conosco pelos seguintes canais (em ordem progressiva):

1. A seção de comentários deste artigo.
2. O [Fórum do Armazenamento do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. O [UserVoice do Arquivos do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. O Suporte da Microsoft. Para criar uma nova solicitação de suporte, no Portal do Azure, na guia **Ajuda**, selecione o botão **Ajuda + suporte** e, em seguida, selecione **Nova solicitação de suporte**.

## <a name="general"></a>Geral
* <a id="why-files-useful"></a>
  **Por que o serviço Arquivos do Azure é útil?**  
   Você pode usar o serviço Arquivos do Azure para criar compartilhamentos de arquivos na nuvem sem ser responsável por gerenciar a sobrecarga de um servidor físico ou dispositivo. Nós fazemos o trabalho monótono para você, incluindo a aplicação de atualizações do sistema operacional e a substituição de discos defeituosos. Para saber mais sobre os cenários em que os Arquivos do Azure podem ajudar, confira [Por que o serviço Arquivos do Azure é útil](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
  **Quais são as diferentes maneiras de acessar arquivos nos Arquivos do Azure?**  
    Você pode montar o compartilhamento de arquivos no computador local usando o protocolo SMB 3.0 ou pode usar ferramentas como o [Gerenciador de Armazenamento](https://storageexplorer.com/) para acessar os arquivos no compartilhamento de arquivos. No aplicativo, você pode usar bibliotecas de cliente de armazenamento, APIs REST, o PowerShell ou a CLI do Azure para acessar os arquivos no compartilhamento de arquivos do Azure.

* <a id="what-is-afs"></a>
  **O que é a Sincronização de arquivos do Azure?**  
    Você pode usar a Sincronização de Arquivos do Azure para centralizar os compartilhamentos de arquivos da sua organização no serviço Arquivos do Azure sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. A Sincronização de Arquivos do Azure transforma computadores Windows Server em um cache rápido do seu compartilhamento de arquivos do Azure. Você pode usar qualquer protocolo disponível no Windows Server para acessar seus dados localmente, incluindo o SMB, o NFS (sistema de arquivos de rede) e o FTPS (serviço de protocolo de transferência de arquivos). Você pode ter tantos caches quantos precisar em todo o mundo.

* <a id="files-versus-blobs"></a>
  **Qual é a comparação entre usar um compartilhamento de arquivos do Azure e o Armazenamento de Blobs do Azure para meus dados?**  
    Tanto o serviço Arquivos do Azure quanto o Armazenamento de Blobs do Azure fornecem uma maneira de armazenar grandes quantidades de dados na nuvem, mas são úteis para fins ligeiramente diferentes. 
    
    O Armazenamento de Blobs do Azure é útil para aplicativos de grande escala e nativos de nuvem que precisam armazenar dados não estruturados. Para maximizar o desempenho e o dimensionamento, o Armazenamento de Blobs do Azure é uma abstração de armazenamento mais simples do que um sistema de arquivos real. O Armazenamento de Blobs do Azure só pode ser acessado por bibliotecas de cliente baseadas em REST (ou diretamente por meio do protocolo baseado em REST).

    O serviço Arquivos do Azure é especificamente um sistema de arquivos. O serviço Arquivos do Azure tem todos os resumos de arquivo que você conhece e adora após anos de trabalho com sistemas operacionais locais. Assim como o Armazenamento de Blobs do Azure, o serviço Arquivos do Azure oferece uma interface REST e bibliotecas de cliente baseadas em REST. Ao contrário do Armazenamento de Blobs do Azure, o serviço Arquivos do Azure oferece acesso via SMB a compartilhamentos de arquivos do Azure. Usando SMB, você pode montar um compartilhamento dos Arquivos do Azure diretamente no Windows, no Linux ou no macOS, localmente ou em VMs na nuvem, sem escrever nenhum código nem anexar drivers especiais ao sistema de arquivos. Você também pode armazenar em cache os compartilhamentos de arquivos do Azure em servidores de arquivos locais usando a Sincronização de Arquivos do Azure para acesso rápido, perto de onde os dados são usados. 
   
    Para uma descrição mais detalhada sobre as diferenças entre os Arquivos do Azure e o Armazenamento de Blobs do Azure, confira [Decidindo quando usar o Armazenamento de Blobs do Azure, os Arquivos do Azure ou Discos do Azure](../common/storage-decide-blobs-files-disks.md). Para saber mais sobre o Armazenamento de Blobs do Azure, confira [Introdução ao armazenamento de Blobs](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Por que usar um compartilhamento de arquivos do Azure em vez dos Discos do Azure?**  
    Um disco nos Discos do Azure é simplesmente um disco. Para obter valor do serviço Discos do Azure, você deve anexar um disco a uma máquina virtual que está em execução no Azure. O serviço Discos do Azure pode ser usado para todos os fins para os quais você usaria um disco em um servidor local. Você pode usá-lo como um disco de sistema do SO, como espaço de permuta para um SO ou como armazenamento dedicado para um aplicativo. Um uso interessante do serviço Discos do Azure é a criação de um servidor de arquivos na nuvem para uso nos mesmos locais em que você usaria um compartilhamento de arquivos do Azure. Implantar um servidor de arquivos em Máquinas Virtuais do Azure é uma maneira fantástica e de alto desempenho de obter o armazenamento de arquivos no Azure quando você precisa de opções de implantação que atualmente não têm suporte pelo serviço Arquivos do Azure (como suporte a protocolo NFS ou armazenamento Premium). 

    No entanto, executar um servidor de arquivos com os Discos do Azure como armazenamento de back-end geralmente é muito mais caro do que usar um compartilhamento de arquivos do Azure, por vários motivos. Primeiro, além de pagar pelo armazenamento em disco, você também precisa pagar a despesa de execução de uma ou mais VMs do Azure. Em segundo lugar, você também deve gerenciar as VMs que são usadas para executar o servidor de arquivos. Por exemplo, você é responsável por atualizações do SO. Por fim, se precisar que dados sejam armazenados em cache localmente, caberá a você configurar e gerenciar as tecnologias de replicação, por exemplo, a DFSR (Replicação do Sistema de Arquivos Distribuído) para fazer com que isso aconteça.

    Uma abordagem para obter o melhor tanto do serviço Arquivos do Azure quando de um servidor de arquivos hospedado em Máquinas Virtuais do Azure (além de usar o serviço Discos do Azure como armazenamento de back-end) é instalar a Sincronização de Arquivos do Azure no servidor de arquivos hospedado em uma VM na nuvem. Se o compartilhamento dos Arquivos do Azure está na mesma região que o servidor de arquivos, você pode habilitar a disposição em camadas na nuvem e definir o percentual do volume de espaço livre para o máximo (99%). Isso garante o mínimo de duplicação de dados. Você também pode usar quaisquer aplicativos que desejar com seus servidores de arquivo, assim como aplicativos que exigem suporte a protocolo NFS.

    Para obter informações sobre uma opção para definição de um servidor de arquivos de alto desempenho e alta disponibilidade no Azure, confira [Implantando clusters convidados em VMs IaaS no Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). Para uma descrição mais detalhada das diferenças entre os Arquivos do Azure e os Discos do Azure, confira [Decidindo quando usar o Armazenamento de Blobs do Azure, os Arquivos do Azure ou Discos do Azure](../common/storage-decide-blobs-files-disks.md). Para saber mais sobre os Discos do Azure, confira [Visão geral do Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>
  **Como posso começar a usar os Arquivos do Azure?**  
   É fácil começar a usar o serviço Arquivos do Azure. Primeiro, [crie um compartilhamento de arquivos](storage-how-to-create-file-share.md) e, em seguida, monte-o no seu sistema operacional preferido: 

  * [Montar no Windows](storage-how-to-use-files-windows.md)
  * [Montar no Linux](storage-how-to-use-files-linux.md)
  * [Montar no macOS](storage-how-to-use-files-mac.md)

    Para obter um guia mais detalhado sobre como implantar um compartilhamento dos Arquivos do Azure para substituir os compartilhamentos de arquivos de produção em sua organização, confira [Planejando a implantação dos Arquivos do Azure](storage-files-planning.md).

* <a id="redundancy-options"></a>
  **Quais opções de redundância de armazenamento têm suporte pelos Arquivos do Azure?**  
    Atualmente, os Arquivos do Azure fornece para LRS (armazenamento com redundância local), ZRS (armazenamento com redundância de zona) e GRS (armazenamento com redundância geográfica). Planejamos dar suporte para RA-GRS (redundância geográfica com acesso de leitura) no futuro, mas não temos linhas do tempo para compartilhar nesse momento.

* <a id="tier-options"></a>
  **Atualmente, quais camadas de armazenamento têm suporte no serviço Arquivos do Azure?**  
    Atualmente, o serviço Arquivos do Azure dá suporte apenas à camada de armazenamento padrão. Não temos linhas do tempo a compartilhar para suporte ao armazenamento premium e ao armazenamento esporádico no momento. 
    
    > [!NOTE]
    > Você não pode criar compartilhamentos de Arquivos do Azure de contas de armazenamento somente para Blob ou de contas de armazenamento Premium.

* <a id="give-us-feedback"></a>
  **Quero muito ver o recurso específico adicionado ao serviço Arquivos do Azure. Você pode adicioná-lo?**  
    A equipe do serviço Arquivos do Azure quer ouvir todos os comentários que você tem a fazer sobre nosso serviço. Vote nas solicitações de recurso no [UserVoice do Arquivos do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Estamos ansiosos para surpreendê-lo com muitos recursos novos.

## <a name="azure-file-sync"></a>Sincronização de Arquivos do Azure

* <a id="afs-region-availability"></a>
  **Quais regiões têm suporte para Sincronização de Arquivos do Azure?**  
    A lista de regiões disponíveis pode ser encontrada na seção [Disponibilidade de região](storage-sync-files-planning.md#region-availability) da guia de planejamento da Sincronização de Arquivos do Azure. Adicionaremos suporte continuamente para outras regiões, incluindo regiões não públicas.

* <a id="cross-domain-sync"></a>
  **É possível ter servidores ingressados e não ingressados no domínio no mesmo grupo de sincronização?**  
    Sim. Sim, um grupo de sincronização poderá conter pontos de extremidade do servidor com diferentes associações do Active Directory, mesmo se eles não forem ingressados no domínio. Embora essa configuração funcione tecnicamente, não recomendamos isso como uma configuração típica, já que as ACLs (listas de controle de acesso) que são definidas para arquivos e pastas em um servidor podem não ser impostas por outros servidores no grupo de sincronização. Para obter melhores resultados, recomendamos a sincronização entre servidores na mesma floresta do Active Directory entre servidores que estão em diferentes florestas do Active Directory mas têm relações de confiança estabelecidas ou então entre servidores que não estão em um domínio. Recomendamos que você evite usar uma mistura dessas configurações.

* <a id="afs-change-detection"></a>
  **Criei um arquivo diretamente no meu compartilhamento de arquivos do Azure no SMB ou por meio do portal. Quanto tempo leva para que o arquivo seja sincronizado aos servidores no grupo de sincronização?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Se o mesmo arquivo é alterado em dois servidores quase ao mesmo tempo, o que acontece?**  
    A Sincronização de Arquivos do Azure usa uma estratégia simples de resolução de conflitos: mantemos ambas as alterações aos arquivos que são alterados em dois servidores simultaneamente. A alteração gravada mais recentemente mantém o nome do arquivo original. O arquivo mais antigo tem o computador de "origem" e o número do conflito anexado ao nome. Ele segue esta taxonomia: 
   
    \<FileNameWithoutExtension\>-\<MachineName\>\[-#\].\<ext\>  

    Por exemplo, o primeiro conflito de CompanyReport.docx se tornaria CompanyReport-CentralServer.docx se CentralServer fosse o local em que a gravação mais antiga ocorreu. O segundo conflito seria nomeado CompanyReport-CentralServer-1.docx.

* <a id="afs-storage-redundancy"></a>
  **Há suporte para armazenamento com redundância geográfica na Sincronização de arquivos do Azure?**  
    Sim, o serviço Arquivos do Azure dá suporte tanto ao LRS (armazenamento com redundância local) quanto ao GRS (armazenamento com redundância geográfica). Se você iniciar um failover de conta de armazenamento entre regiões emparelhadas de uma conta configurada para GRS, a Microsoft recomenda que você trate a nova região como um backup de dados somente. A Sincronização de Arquivos do Azure não inicia automaticamente a sincronização com a nova região primária. 

* <a id="sizeondisk-versus-size"></a>
  **Por que a propriedade *Tamanho em disco* de um arquivo não corresponde à propriedade *Tamanho* depois do uso da Sincronização de arquivos do Azure?**  
  Confira [Understanding Cloud Tiering](storage-sync-cloud-tiering.md#sizeondisk-versus-size) (Noções básicas sobre a Camada de Nuvem).

* <a id="is-my-file-tiered"></a>
  **Como saber se um arquivo está em camadas?**  
  Confira [Understanding Cloud Tiering](storage-sync-cloud-tiering.md#is-my-file-tiered) (Noções básicas sobre a Camada de Nuvem).

* <a id="afs-recall-file"></a>**Um arquivo que eu desejo usar foi dividido em camadas. Como é possível fazer o recall do arquivo no disco para usá-lo localmente?**  
  Confira [Understanding Cloud Tiering](storage-sync-cloud-tiering.md#afs-recall-file) (Noções básicas sobre a Camada de Nuvem).

* <a id="afs-force-tiering"></a>
  **Como posso forçar um arquivo ou diretório a ficar em camadas?**  
  Confira [Understanding Cloud Tiering](storage-sync-cloud-tiering.md#afs-force-tiering) (Noções básicas sobre a Camada de Nuvem).

* <a id="afs-effective-vfs"></a>
  **Como o *espaço livre no volume* é interpretado quando há vários pontos de extremidade do servidor em um volume?**  
  Confira [Understanding Cloud Tiering](storage-sync-cloud-tiering.md#afs-effective-vfs) (Noções básicas sobre a Camada de Nuvem).

* <a id="afs-files-excluded"></a>
  **Quais arquivos ou pastas são excluídas automaticamente pela Sincronização de arquivos do Azure?**  
    Por padrão, a Sincronização de Arquivos do Azure exclui os arquivos abaixo:
  * desktop.ini
  * thumbs.db
  * ehthumbs.db
  * ~$\*.\*
  * \*.laccdb
  * \*.tmp
  * 635D02A9D91C401B97884B82B3BCDAEA.\*

    As pastas abaixo também são excluídas por padrão:

  * \SystemVolumeInformation
  * \$RECYCLE. BIN
  * \SyncShareState

* <a id="afs-os-support"></a>
  **Posso usar a Sincronização de arquivos do Azure com o Windows Server 2008 R2, Linux ou o dispositivo NAS (armazenamento conectado à rede)?**  
    Hoje, a Sincronização de Arquivos do Azure dá suporte apenas a Windows Server 2016 e a Windows Server 2012 R2. Neste momento, não há outros planos que possamos divulgar, mas gostaríamos de dar suporte a outras plataformas com base na demanda dos clientes. Informe-nos quais plataformas você deseja que tenham suporte no [UserVoice do Arquivos do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **Por que os arquivos em camadas existem fora o namespace de ponto de extremidade do servidor?**  
    Antes do agente do Azure File Sync - Sincronização de Arquivos do Azure versão 3, o Azure File Sync bloqueava a movimentação de arquivos em camadas fora do ponto de extremidade do servidor, mas no mesmo volume que o ponto de extremidade do servidor. Operações de cópia, move arquivos não hierárquico e de em camadas para outros volumes foram afetados. O motivo para esse comportamento foi a suposição implícita de que o Explorador de Arquivos e outras APIs do Windows que têm essas operações de movimentação no mesmo volume são operações de renomeação (quase) instantâneas. Isso significa que move fará o Explorador de arquivos ou outros métodos de movimentação (como a linha de comando ou o PowerShell) pode parecer não estar respondendo enquanto a sincronização de arquivos do Azure recupera os dados da nuvem. A partir do [ agente do Azure File Sync versão 3.0.12.0 ](storage-files-release-notes.md#supported-versions), o Azure File Sync permitirá que você mova um arquivo em camadas fora do ponto de extremidade do servidor. Evitamos os efeitos negativos mencionados anteriormente, permitindo que o arquivo em camadas exista como um arquivo em camadas fora do terminal do servidor e, em seguida, recuperando o arquivo em segundo plano. Isso significa que movimentações no mesmo volume são instantâneas e fazemos todo o trabalho para recuperar o arquivo no disco após a movimentação ser concluída. 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **Estou tendo um problema com a sincronização de arquivos do Azure no servidor (sincronização, nuvem em camadas, etc). Deve remover e recriar o ponto de extremidade do meu servidor?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **Posso mover o serviço de sincronização de armazenamento e/ou a conta de armazenamento para um grupo de recursos ou assinatura diferente?**  
   Sim, o serviço de sincronização de armazenamento e/ou a conta de armazenamento podem ser movidos para um grupo de recursos ou assinatura diferente dentro do locatário do Azure AD existente. Se a conta de armazenamento for movida, você precisará conceder o acesso do Serviço de Sincronização de Arquivos Híbrido para a conta de armazenamento (consulte [Certifique-se de que a Sincronização de Arquivos do Azure tenha acesso à conta de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Sincronização de Arquivos do Azure não oferece suporte para mover a assinatura para um locatário do Azure AD diferente.
    
* <a id="afs-ntfs-acls"></a>
  **Sincronização de arquivos do Azure preserva o nível de diretório/arquivo ACLs do NTFS, juntamente com os dados armazenados em arquivos do Azure?**

    ACLs do NTFS executadas do arquivo local servidores são mantidas pela sincronização de arquivos do Azure como metadados. Os arquivos do Azure não dá suporte à autenticação com credenciais do Azure AD para acesso a compartilhamentos de arquivos gerenciado pelo serviço de sincronização de arquivos do Azure.
    
## <a name="security-authentication-and-access-control"></a>Segurança, autenticação e controle de acesso
* <a id="ad-support"></a>
**Há suporte para a autenticação baseada no Active Directory e para o controle de acesso nos Arquivos do Azure?**  
    
    Sim, os arquivos do Azure dá suporte a controle de acesso e autenticação baseada em identidade com o Azure Active Directory (Azure AD) (visualização). Autenticação do AD do Azure no SMB para arquivos do Azure aproveita o Azure Active Directory Domain Services para habilitar VMs ingressado no domínio acessar compartilhamentos, diretórios e arquivos usando as credenciais do Azure AD. Para obter mais detalhes, consulte [Visão geral da autenticação do Active Directory do Azure em SMB para arquivos do Azure (visualização)](storage-files-active-directory-overview.md). 

    O Azure Files oferece duas maneiras adicionais de gerenciar o controle de acesso:

    - Você pode usar SAS (assinaturas de acesso compartilhado) para gerar tokens que têm permissões específicas e que são válidos para um intervalo de tempo especificado. Por exemplo, você pode gerar um token com acesso somente leitura a um arquivo específico que tem validade de 10 minutos. Qualquer pessoa que tem esse token enquanto ele é válido tem acesso somente leitura ao arquivo por 10 minutos. Atualmente, chaves de assinatura de acesso compartilhado têm suporte apenas via API REST ou em bibliotecas de cliente. Você deve montar o compartilhamento de arquivos do Azure no SMB usando as chaves de conta de armazenamento.

    - A Sincronização de Arquivos do Azure preserva e replica todas as ACLs discricionárias ou DACLs (sejam elas baseadas no Active Directory ou locais) para todos os pontos de extremidade do servidor aos quais ela é sincronizada. Já que o Windows Server já pode se autenticar com o Active Directory, a Sincronização de Arquivos do Azure pode ser uma medida preventiva eficaz até que o suporte completo para autenticação baseada em Active Directory e o suporte a ACL fiquem disponíveis.

* <a id="ad-support-regions"></a>
**É a visualização do AD do Azure no SMB para arquivos do Azure disponível em todas as regiões do Azure?**

    A versão prévia está disponível em todas as regiões públicas.

* <a id="ad-support-on-premises"></a>
**Autenticação do AD do Azure no SMB para arquivos do Azure (versão prévia) dá suporte a autenticação usando o AD do Azure de máquinas locais?**

    Não, arquivos do Azure não oferece suporte a autenticação com o AD do Azure de máquinas locais na versão de visualização.

* <a id="ad-support-devices"></a>
**Autenticação de faz do Azure AD via SMB arquivos do Azure (visualização) SMB para acesso de suporte usando as credenciais do Azure AD de dispositivos ingressado no ou registrados no Azure AD?**

    Não, este cenário não é suportado.

* <a id="ad-support-rest-apis"></a>
**Existem APIs REST para dar suporte a Get/Set/copiar diretório/arquivo ACLs do NTFS?**

    A versão de visualização não oferece suporte a APIs REST para obter, definir, ou copiar ACLs do NTFS para diretórios ou arquivos.

* <a id="ad-vm-subscription"></a>
**Pode acessar arquivos do Azure com credenciais do Azure AD de uma VM em uma assinatura diferente?**

    Se a assinatura sob a qual o compartilhamento de arquivos foi implantado estiver associada ao mesmo locatário do Azure AD como a implantação do Azure AD Domain Services para o qual a VM ingressou no domínio, você poderá acessar os Arquivos do Azure usando as mesmas credenciais do Azure AD. A limitação é imposta não na assinatura, mas no locatário associado do Azure AD.    
    
* <a id="ad-support-subscription"></a>
**Posso habilitar a autenticação do Azure Active Directory no SMB para arquivos do Azure com um locatário do Azure Active Directory que é diferente do locatário primário ao qual o compartilhamento de arquivos está associado?**

    Não, o Azure Files só oferece suporte à integração do Azure AD com um locatário do Azure AD que reside na mesma assinatura do compartilhamento de arquivos. Somente uma assinatura pode ser associada a um locatário do Azure AD.

* <a id="ad-linux-vms"></a>
**A autenticação do AD do Azure em SMB para arquivos do Azure (visualização) oferece suporte a VMs do Linux?**

    Não, a autenticação das VMs do Linux não é suportada na versão de visualização.

* <a id="ad-aad-smb-afs"></a>
**Posso usar a autenticação do AD do Azure em recursos de SMB em compartilhamentos de arquivos gerenciados pelo Azure File Sync?**

    Não, o Azure Files não oferece suporte à preservação de ACLs do NTFS em compartilhamentos de arquivos gerenciados pelo Azure File Sync. As ACLs de arquivo transportadas de servidores de arquivos locais são persistidas pelo Azure File Sync. Quaisquer ACLs NTFS configuradas nativamente em Arquivos do Azure serão sobrescritas pelo serviço de Sincronização de Arquivos do Azure. Além disso, o Azure Files não oferece suporte à autenticação com credenciais do Azure AD para acesso a compartilhamentos de arquivos gerenciados pelo serviço de Sincronização de Arquivos do Azure.

* <a id="encryption-at-rest"></a>
**Como garantir que o compartilhamento de arquivos do Azure está criptografado em repouso?**  

    A criptografia do Serviço de Armazenamento do Microsoft Azure está em processo de ser habilitada por padrão em todas as regiões. Para essas regiões, você não precisa realizar nenhuma ação para habilitar a criptografia. Para outras regiões, consulte [Criptografia no servidor](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

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

   O Arquivos do Azure é executado com base na mesma arquitetura de armazenamento usada em outros serviços de armazenamento no Armazenamento do Azure. O Arquivos do Azure aplica as mesmas políticas de conformidade de dados que são usadas em outros serviços de armazenamento do Azure. Para obter mais informações sobre a conformidade de dados do Armazenamento do Azure, você pode consultar as [ofertas de conformidade do Armazenamento do Microsoft Azure ](https://docs.microsoft.com/azure/storage/common/storage-compliance-offerings) e ir à [Central de Confiabilidade da Microsoft](https://microsoft.com/trustcenter/default.aspx).

## <a name="on-premises-access"></a>Acesso local

* <a id="port-445-blocked"></a>
**Monte a meu ISP ou IT bloqueia a porta 445 que está falhando arquivos do Azure. O que devo fazer?**

    Você pode aprender sobre [várias maneiras para solucionar esse problema bloqueado a porta 445 aqui](https://docs.microsoft.com/en-us/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#cause-1-port-445-is-blocked). Os arquivos do Azure permite apenas conexões usando o SMB 3.0 (com suporte de criptografia) de fora da região ou datacenter. Protocolo SMB 3.0 introduziu muitos recursos de segurança, incluindo a criptografia de canal que é muito segura usar pela internet. No entanto, é possível que a porta 445 foi bloqueado devido a razões históricas de vulnerabilidades encontradas em versões anteriores do SMB. No caso ideal, a porta deve ser bloqueada para somente para tráfego SMB 1.0 e o SMB 1.0 deve ser desligado em todos os clientes.

* <a id="expressroute-not-required"></a>
**Preciso usar o Azure ExpressRoute para me conectar aos Arquivos do Azure ou para usar a Sincronização de arquivos do Azure localmente?**  

    Não. O ExpressRoute não é necessário para acessar um compartilhamento de arquivos do Azure. Se você está montando um compartilhamento de arquivos do Azure diretamente localmente, basta ter a porta 445 (TCP de saída) aberta para acesso à Internet (essa é a porta pela qual o SMB se comunica). Se você está usando a Sincronização de Arquivos do Azure, basta ter a porta 443 (TCP de saída) para acesso HTTPS (não é necessário usar SMB). No entanto, você *pode* usar ExpressRoute com qualquer uma dessas opções de acesso.

* <a id="mount-locally"></a>
**Como posso montar o compartilhamento de arquivos do Azure no meu computador local?**  

    Você poderá montar o compartilhamento de arquivos por meio do protocolo SMB, desde que a porta 445 (TCP de Saída) esteja aberta e o cliente dê suporte ao protocolo SMB 3.0 (por exemplo, se você estiver usando o Windows 10 ou o Windows Server 2016). Se a porta 445 está bloqueada pela política da sua organização ou por pelo ISP, você pode usar a Sincronização de Arquivos do Azure para acessar o compartilhamento de arquivos do Azure.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Como posso fazer backup do meu compartilhamento de arquivos do Azure?**  
    Você pode usar [instantâneos de compartilhamento](storage-snapshots-files.md) periódicos para proteção contra exclusões acidentais. Use o AzCopy, o Robocopy ou uma ferramenta de backup de terceiros que possa fazer backup de um compartilhamento de arquivos montado. O Backup do Azure oferece backup do Azure Files. Saiba mais sobre [fazer dos compartilhamos de arquivo do Azure pelo Backup do Microsoft Azure](https://docs.microsoft.com/azure/backup/backup-azure-files).

## <a name="share-snapshots"></a>Instantâneos de compartilhamento

### <a name="share-snapshots-general"></a>Instantâneos de compartilhamento: Geral
* <a id="what-are-snaphots"></a>
**O que são os instantâneos de compartilhamento de arquivo?**  
    Você pode usar os instantâneos de compartilhamento de arquivos do Azure para criar versões somente leitura de seus compartilhamentos de arquivos. Você também pode usar o serviço Arquivos do Azure para copiar uma versão anterior do conteúdo para o mesmo compartilhamento ou para um local alternativo no Azure ou local para modificações adicionais. Para saber mais sobre instantâneos de compartilhamento, consulte a [Visão geral de instantâneos de compartilhamento](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>
**Onde os instantâneos de compartilhamento são armazenados?**  
    Os instantâneos de compartilhamento são armazenados na mesma conta de armazenamento do compartilhamento de arquivos.

* <a id="snapshot-perf-impact"></a>
**Há qualquer implicações de desempenho para o uso de instantâneos de compartilhamento?**  
    Os instantâneos de compartilhamento não geram sobrecarga de desempenho.

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
**Preciso de um compartilhamento de arquivos maior que o oferecido atualmente pelo serviço Arquivos do Azure. Posso aumentar o tamanho do meu compartilhamento de arquivos do Azure?**  
    Não. O tamanho máximo de um compartilhamento de arquivos do Azure é 5 TiB. Atualmente, esse é um limite fixo que não pode ser ajustado. Estamos trabalhando em uma solução para aumentar o tamanho do compartilhamento para 100 TiB, mas não temos uma previsão para compartilhar neste momento.

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
    Atualmente, em um compartilhamento de arquivos do Azure, não há suporte para essa configuração. Para saber mais sobre como configurar isso para o Armazenamento de Blobs do Azure, confira [Implantar uma testemunha de nuvem em um cluster de failover](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**Posso montar um compartilhamento de arquivos do Azure em uma instância de Contêiner do Azure?**  
    Sim, os compartilhamentos de arquivos do Azure são uma opção fantástica para manter informações além do tempo de vida de uma instância de contêiner. Para obter mais informações, consulte [Montar um compartilhamento de arquivos do Azure com Instâncias de Contêiner do Azure](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>
**Há uma operação de renomeação na API REST?**  
    Não no momento.

* <a id="nested-shares"></a>
**Eu posso configurar compartilhamentos aninhados? Em outras palavras, um compartilhamento em um compartilhamento?**  
    Não. O compartilhamento de arquivos *é* o driver virtual que você pode montar; portanto, não há suporte para compartilhamentos aninhados.

* <a id="ibm-mq"></a>
**Como posso usar os Arquivos do Azure com o IBM MQ?**  
    A IBM liberou um documento que ajuda clientes do IBM MQ a configurar o Arquivos do Azure juntamente com o serviço IBM. Para obter mais informações, consulte [Como configurar o gerenciador de filas com várias instâncias do IBM MQ com o serviço Arquivos do Microsoft Azure](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Consulte também
* [Solucionar problemas do Arquivos do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Solucionar problemas do Arquivos do Azure no Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Solucionar problemas da Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md)
