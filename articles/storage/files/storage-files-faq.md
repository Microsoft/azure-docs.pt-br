---
title: Perguntas frequentes sobre o Arquivos do Azure | Microsoft Docs
description: Encontre respostas para perguntas frequentes sobre o Arquivos do Azure.
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/08/2017
ms.author: renash
ms.openlocfilehash: cbe3c3a8f223a4bcc05e19b7a723cf5aff413527
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="frequently-asked-questions-about-azure-files"></a>Perguntas frequentes sobre o Arquivos do Azure

## <a name="general"></a>Geral
* **O que Arquivos do Azure?**  
    Os Arquivos do Azure oferecem compartilhamentos de arquivos totalmente gerenciados na nuvem, acessíveis por meio do protocolo [SMB (Service Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) padrão do setor (também conhecido como CIFS ou Common Internet File System). Os compartilhamentos de Arquivos do Azure podem ser montados de maneira simultânea por implantações locais ou na nuvem do Windows, do Linux e do MacOS.

* **Por que os Arquivos do Azure são úteis?**  
   Os Arquivos do Azure permitem que você crie compartilhamentos de arquivos na nuvem sem a necessidade de gerenciar a sobrecarga de um servidor físico ou dispositivo/aparelho. Isso significa que é possível gastar menos tempo aplicando atualizações do sistema operacional e substituindo discos defeituosos; fazemos todo esse trabalho monótono para você. Para saber mais sobre os cenários em que os Arquivos do Azure podem ajudar, confira [Por que o serviço Arquivos do Azure é útil](storage-files-introduction.md#why-azure-files-is-useful).

* **Quais são as diferentes maneiras de acessar arquivos nos Arquivos do Azure?**  
    Você pode montar o compartilhamento de arquivos no computador local usando o protocolo SMB 3.0 ou usar ferramentas como o [Gerenciador de Armazenamento](http://storageexplorer.com/) para acessar os arquivos no compartilhamento de arquivos. No aplicativo, você pode usar bibliotecas de cliente de armazenamento, APIs REST ou o PowerShell para acessar os arquivos no compartilhamento de arquivos do Azure.

* **O que é a Sincronização de Arquivos do Azure?**  
    A Sincronização de Arquivos do Azure (versão prévia) permite que você centralize os compartilhamentos de arquivos da sua organização em Arquivos do Azure sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. Ele faz isso transformando Windows Servers em um cache rápido do seu compartilhamento de Arquivos do Azure. Você pode usar qualquer protocolo disponível no Windows Server para acessar seus dados localmente (incluindo SMB, NFS e FTPS) e pode ter todos os caches de que precisar ao redor do mundo.

* **Qual é a comparação entre usar um compartilhamento dos Arquivos do Azure e o Armazenamento de Blobs do Azure para meus dados?**  
    Tanto os Arquivos do Azure quanto o Armazenamento de Blobs do Azure fornecem uma maneira de armazenar grandes quantidades de dados na nuvem, mas são úteis para fins ligeiramente diferentes. O Armazenamento de Blobs do Azure é útil para aplicativos de grande escala e nativos de nuvem que precisam armazenar dados não estruturados. Para maximizar o desempenho e o dimensionamento, o Armazenamento de Blobs do Azure é uma abstração de armazenamento mais simples do que um sistema de arquivos real. Além disso, o Armazenamento de Blobs do Azure só pode ser acessado por bibliotecas de cliente baseadas em REST (ou diretamente por meio do protocolo baseado em REST).

    Os Arquivos do Azure, por outro lado, buscam ser efetivamente um sistema de arquivos, com todos os resumos de arquivo que você conhece e adora há anos usando sistemas operacionais locais. Assim como o Armazenamento de Blobs do Azure, os Arquivos do Azure oferecem uma interface REST e bibliotecas de cliente baseadas em REST, mas ao contrário do Armazenamento de Blobs do Azure, os Arquivos do Azure também oferecem acesso SMB para os compartilhamentos de Arquivos do Azure. Isso significa que você pode montar um compartilhamento dos Arquivos do Azure diretamente no Windows, no Linux ou no macOS, localmente ou em VMs na nuvem, sem precisar codificar ou anexar drivers especiais ao sistema de arquivos. Além disso, os compartilhamentos dos Arquivos do Azure podem ser armazenados em cache nos servidores de arquivos no local usando a Sincronização de Arquivos do Azure para acesso rápido próximo a onde os dados estão sendo usados. 
   
    Para uma discussão mais detalhada sobre as diferenças entre os Arquivos do Azure e o Armazenamento de Blobs do Azure, confira [Decidindo quando usar o Armazenamento de Blobs do Azure, os Arquivos do Azure ou Discos do Azure](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Para saber mais sobre o Armazenamento de Blobs do Azure, confira [Introdução ao armazenamento de Blobs](../blobs/storage-blobs-introduction.md).

* **Qual é a comparação entre usar um compartilhamento dos Arquivos do Azure e os Discos do Azure?**  
    Um Disco do Azure é apenas isso, um disco. Um disco autônomo, por si só, não é muito útil; para fazer valer um Disco do Azure, você precisa anexa-lo à máquina virtual em execução no Azure. Os Discos do Azure podem ser usados para todas as necessidades que exigem um disco em um servidor local: como um disco de sistema do sistema operacional, como espaço de permuta para um sistema operacional ou como armazenamento dedicado para um aplicativo. Um uso interessante dos Discos do Azure é a criação de um servidor de arquivos na nuvem para uso exatamente nos mesmos locais em que você usaria um compartilhamento dos Arquivos do Azure. Implantar um servidor de arquivos em VMs do Azure é uma maneira fantástica e de alto desempenho de obter o armazenamento de arquivos no Azure quando você precisa de opções de implantação sem suporte pelos Arquivos do Azure atualmente (como suporte a protocolo NFS ou armazenamento Premium). 

    Por outro lado, executar um servidor de arquivos com os Discos do Azure como armazenamento de back-end geralmente será muito mais caro do que usar um compartilhamento dos Arquivos do Azure por vários motivos. Primeiro, além de pagar pelo armazenamento em disco, você também precisa pagar a despesa de execução de uma ou mais VMs do Azure. Em segundo lugar, você também deve gerenciar as VMs usadas para executar o servidor de arquivos, como as que são responsáveis por atualizações do sistema operacional, etc. Por fim, se precisar de dados armazenados em cache localmente, caberá a você configurar e gerenciar as tecnologias de replicação (por exemplo, a Replicação do Sistema de Arquivos Distribuído) para fazer com que isso aconteça.

    Uma abordagem interessante para obter o melhor tanto dos Arquivos do Azure quando de um servidor de arquivos hospedado em VMs do Azure com Discos do Azure como armazenamento de back-end é instalar a Sincronização de Arquivos do Azure no servidor de arquivos hospedado em uma VM na nuvem. Se o compartilhamento dos Arquivos do Azure está na mesma região que o servidor de arquivos, você pode habilitar camadas na nuvem e definir o percentual de espaço livre no volume máximo (99%). Isso garante a duplicação mínima de dados ao mesmo tempo que permite que você use os aplicativos que quiser em seus servidores de arquivos, por exemplo, qualquer coisa que requeira o protocolo NFS.

    Para obter informações sobre uma forma de definir alto desempenho e alta disponibilidade no Azure, confira [Implantando clusters convidados em VMs IaaS no Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). Para uma discussão mais detalhada sobre as diferenças entre os Arquivos do Azure e os Discos do Azure, confira [Decidindo quando usar o Armazenamento de Blobs do Azure, os Arquivos do Azure ou Discos do Azure](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Para saber mais sobre os Discos do Azure, confira [Visão geral do Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

* **Como posso começar a usar os Arquivos do Azure?**  
    É fácil começar a usar os Arquivos do Azure: basta [criar um compartilhamento de arquivos](storage-how-to-create-file-share.md) e montá-lo no seu sistema operacional preferido: 

    - [Montar no Windows](storage-how-to-use-files-windows.md)
    - [Montar no Linux](storage-how-to-use-files-linux.md)
    - [Montar no macOS](storage-how-to-use-files-mac.md)

    Para obter um guia mais detalhado sobre como implantar um compartilhamento dos Arquivos do Azure para substituir os compartilhamentos de arquivos de produção em sua organização, confira [Planejando a implantação dos Arquivos do Azure](storage-files-planning.md).

* **Quais opções de redundância de armazenamento têm suporte pelos Arquivos do Azure?**  
    Atualmente, os Arquivos do Azure atualmente dão suporte apenas a LRS (armazenamento com redundância local) e a GRS (armazenamento com redundância geográfica). Planejamos dar suporte a ZRS (armazenamento com redundância de zona) e a RA-GRS (armazenamento com redundância geográfica com acesso de leitura) no futuro, mas não temos uma linha do tempo para divulgar neste momento.

* **Quero muito ver o recurso *x* adicionado aos Arquivos do Azure; vocês podem adicioná-lo?**  
    Com certeza! A equipe dos Arquivos do Azure quer ouvir todos os comentários que você tem a fazer sobre nosso serviço. Vote nas solicitações de recurso no [UserVoice dos Arquivos do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Estamos ansiosos para surpreendê-lo com muitos recursos novos.

## <a name="azure-file-sync"></a>Sincronização de Arquivos do Azure
* **Quais regiões têm suporte atualmente para Sincronização de Arquivos do Azure (versão prévia)?**  
    A Sincronização de Arquivos do Azure está disponível atualmente no Oeste dos EUA, na Europa Ocidental, no Leste da Austrália e no Sudeste da Ásia. Adicionaremos suporte para outras regiões conforme vamos trabalhando para obter disponibilidade geral. Para saber mais, confira [Disponibilidade de regiões](storage-sync-files-planning.md#region-availability).

* **É possível ter servidores ingressados e não ingressados no domínio em um mesmo grupo de sincronização?**  
    Sim, um grupo de sincronização pode conter pontos de extremidade do servidor com diferentes associações do Active Directory, incluindo sem ingresso no domínio. Embora a configuração funcione tecnicamente, não recomendamos isso como uma configuração normal, já que as ACLs que são definidas para arquivos/pastas em um servidor podem não ser impostas por outros servidores no grupo de sincronização. Para obter melhores resultados, recomendamos a sincronização entre servidores na mesma floresta do Active Directory, servidores em diferentes florestas do Active Directory com relações de confiança estabelecida ou servidores que não estejam em um domínio, mas nunca uma combinação de todas as opções acima.

* **Criei um arquivo diretamente no meu compartilhamento dos Arquivos do Azure no SMB ou por meio do portal. Quanto tempo até que o arquivo seja sincronizado com os servidores no grupo de sincronização?** 
    Quando você cria ou modifica arquivos em um compartilhamento dos Arquivos do Azure usando o portal do Azure, o SMB ou o REST, ele pode levar até 24 horas para sincronizar as alterações com os servidores no grupo de sincronização.

* **Quando o mesmo arquivo é alterado em dois servidores quase ao mesmo tempo, o que acontece?**  
    A Sincronização de Arquivos do Azure usa uma estratégia de resolução de conflito simples: mantemos as duas alterações. O gravado mais recentemente mantém o nome do arquivo original. O arquivo mais antigo tem o computador de 'fonte' e o número de conflitos anexado ao nome com esta taxonomia: 
   
    `<FileNameWithoutExtension>-<MachineName>[-#].<ext>`  

    Por exemplo, o primeiro conflito de `CompanyReport.docx` se torna `CompanyReport-CentralServer.docx` se `CentralServer` é onde ocorreu a gravação mais antiga. O segundo conflito deve ser identificado como `CompanyReport-CentralServer-1.docx`.

* **Ao registrar um servidor, obtenho várias respostas de "sites não confiáveis". Por quê?**  
    Esse erro ocorre porque a política **Segurança reforçada do Internet Explorer** está habilitada durante o registro do servidor. Para saber mais sobre como desabilitar corretamente a política **Segurança reforçada do Internet Explorer**, confira [Preparar servidores do Windows para uso com a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md#prepare-windows-servers-for-use-with-azure-file-sync) e [Como implantar a Sincronização de Arquivos do Azure (versão prévia)](storage-sync-files-deployment-guide.md).

* **Há suporte para GRS (armazenamento com redundância geográfica) na Sincronização de Arquivos do Azure?**  
    Sim, tanto o LRS (armazenamento com redundância local) quanto o GRS (armazenamento com redundância geográfica) têm suporte pela Sincronização de Arquivos do Azure. No caso de um failover de GRS entre regiões emparelhadas, recomendamos tratar a nova região somente como um backup de dados. A Sincronização de Arquivos do Azure não iniciará a sincronização com a nova região primária automaticamente. 

* **Por a propriedade *Tamanho em disco* de um arquivo corresponde à propriedade *Tamanho* depois do uso da Sincronização de Arquivos do Azure?**  
    O Explorador de Arquivos do Windows expõe duas propriedades, **Tamanho** e **Tamanho em disco** para representar o tamanho de um arquivo. Essas propriedades diferem sutilmente em significado: **Tamanho** representa o tamanho completo do arquivo e **Tamanho em disco** representa o tamanho do fluxo de arquivo efetivamente armazenado em disco. Eles podem ser diferentes por vários motivos, como compactação, uso da eliminação de duplicação de dados ou, em nosso caso, camadas na nuvem com a Sincronização de Arquivos do Azure. Se um arquivo está em camadas em um Compartilhamento dos Aquivos do Azure, o tamanho em disco será zero porque o fluxo de arquivos está armazenado em seu compartilhamento dos Arquivos do Azure, não no disco. Também é possível que um arquivo esteja parcialmente em camadas (ou seja parcialmente em recall), o que significa que essa parte do arquivo está no disco. Isso pode acontecer quando os arquivos são lidos parcialmente por aplicativos como players de multimídia ou utilitários de compactação. 

* **Como saber se um arquivo está em camadas?**  
    Há várias maneiras de verificar se um arquivo foi colocado em camadas no compartilhamento dos Arquivos do Azure:
    
    1. **Verifique os atributos de arquivo no arquivo.** Para fazer isso, clique com botão direito do mouse em um arquivo, navegue até **Details** (Detalhes) e role para baixo até a propriedade **Attributes**. Um arquivo em camadas terá os seguintes atributos definidos:     
        
        | Carta de atributo | Atributo | Definição |
        |:----------------:|-----------|------------|
        | Uma | Arquivo | Indica que o arquivo deve ter o backup feito pelo software de backup. Esse atributo é sempre definido independentemente de o arquivo estar em camadas ou totalmente armazenado no disco. |
        | P | Arquivos esparsos | Indica que o arquivo é um arquivo esparso, que é um tipo especializado de arquivo que o NTFS oferece para uso eficiente quando o fluxo no disco do arquivo está basicamente vazio. A Sincronização de Arquivos do Azure usa arquivos esparsos porque um arquivo está totalmente em camadas, o que significa que seu fluxo de arquivos é armazenado somente na nuvem, ou parcialmente em recall, o que significa que parte do arquivo já está no disco. Se o recall de um arquivo é feito totalmente em disco, a Sincronização de Arquivos do Azure o converterá de um arquivo esparso em um arquivo regular. |
        | L | Ponto de nova análise | Indica que o arquivo tem um ponto de nova análise, que é um ponteiro especial para ser usado por um filtro do sistema de arquivos. A Sincronização de Arquivos do Azure usa pontos de nova análise a fim de definir, para o filtro do sistema de arquivos da Sincronização de Arquivos do Azure (StorageSync.sys), onde o arquivo está armazenado na nuvem. Isso permite que o acesso contínuo sem que seu usuário final precise saber que a Sincronização de Arquivos do Azure está sendo usada ou como obter acesso ao arquivo em seu compartilhamento dos Arquivos do Azure. Quando o recall de um arquivo é totalmente feito, a Sincronização de Arquivos do Azure remove o ponto de nova análise do arquivo. |
        | O | Off-line | Indica que parte ou nenhum conteúdo do arquivo está armazenado em disco. Quando o recall de um arquivo é totalmente feito, a Sincronização de Arquivos do Azure remove esse atributo. |

        ![O diálogo Propriedades de um arquivo com a guia Detalhes selecionada](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Também é possível ver os atributos de todos os arquivos em uma pasta adicionando o campo **Atributos** à exibição de tabela do Explorador de Arquivos. Para fazer isso, clique em uma coluna existente (por exemplo, Tamanho), selecione **More** e selecione **Attributes** na lista suspensa.
        
    2. **Use `fsutil` para verificar se há Pontos de Nova Análise em um arquivo.** Conforme mencionado na opção anterior, um arquivo em camadas sempre terá um ponto de nova análise, ou um ponteiro especial para o filtro do sistema de arquivos da Sincronização de Arquivos do Azure (StorageSync.sys), definido. Você pode verificar se um arquivo tem um ponto de nova análise com o utilitário `fsutil` em um prompt de comando elevado ou em uma sessão do PowerShell:
    
        ```PowerShell
        fsutil reparsepoint query <your-file-name>
        ```

        Se o arquivo tiver um ponto de nova análise, você deve ver **Valor de Marca de Nova Análise: 0x8000001e**. Esse valor hexadecimal é o valor de ponto de nova análise pertencente à Sincronização de Arquivos do Azure. A saída também conterá os dados da nova análise que representam o caminho para o arquivo em seu compartilhamento dos Arquivos do Azure.

        > [!Warning]  
        > O comando de utilitário `fsutil reparsepoint` também contém a capacidade de excluir um ponto de nova análise. Não execute esse comando se não receber instruções para isso pela equipe de engenharia da Sincronização de Arquivos do Azure; isso pode resultar na perda de dados. 

* **Um arquivo que desejo usar está em camadas. Como posso cancelar o envio para disco a fim de usá-lo localmente?**  
    A maneira mais fácil de fazer o recall de um arquivo em disco é abri-lo. O filtro de sistema de arquivos da Sincronização de Arquivos do Azure (StorageSync.sys) baixará o arquivo do seu compartilhamento dos Arquivos do Azure perfeitamente, sem que você precise fazer qualquer trabalho. Para tipos de arquivo que podem ser parcialmente de leitura, como multimídia ou arquivos zip, abrir um arquivo não resultará no download do arquivo inteiro.

    Também é possível forçar o recall de um arquivo usando o PowerShell. Por exemplo, isso pode ser útil se você quiser fazer o recall de vários arquivos ao mesmo tempo (por exemplo, todos os arquivos dentro de uma pasta). Abra uma sessão do PowerShell para o nó do servidor em que a Sincronização de Arquivos do Azure está instalada e execute os seguintes comandos do PowerShell:
    
    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

* **Como posso forçar um arquivo ou diretório a ficar em camadas?**  
    Quando habilitado, o recurso camadas em nuvem coloca os arquivos em camada automaticamente com base no último acesso e na última modificação para alcançar a porcentagem de espaço livre no volume especificada no ponto de extremidade de nuvem; no entanto, às vezes convém forçar um arquivo a ficar em camadas manualmente. Por exemplo, isso pode ser útil se você salva um arquivo grande que não pretende usar novamente por um longo período e agora deseja que o espaço livre no volume para outros arquivos ou pastas. Você pode forçar as camadas com os seguintes comandos do PowerShell:

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

* **Um arquivo ou pasta está sendo excluído pela Sincronização de Arquivos do Azure. Por quê?**
    Por padrão, a Sincronização de Arquivos do Azure exclui os arquivos abaixo:
    
    - desktop.ini
    - thumbs.db
    - ehthumbs.db
    - ~$\*.\*
    - \*.laccdb
    - \*.tmp
    - 635D02A9D91C401B97884B82B3BCDAEA.\*

    As pastas abaixo também são excluídas por padrão:

    - \SystemVolumeInformation
    - \$RECYCLE. BIN
    - \SyncShareState

* **Gostaria de usar a Sincronização de Arquivos do Azure com o Windows Server 2008 R2, com Linux ou com o dispositivo NAS. A Sincronização de Arquivos do Azure dá suporte a eles?**
    Hoje, Sincronização de Arquivos do Azure dá suporte apenas a Windows Server 2016 e a Windows Server 2012 R2. Neste momento, não há outros planos que possamos divulgar, mas gostaríamos de dar suporte a outras plataformas com base na demanda dos clientes. Informe-nos quais plataformas você deseja que tenham suporte no [UserVoice dos Arquivos do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).

## <a name="security-authentication-and-access-control"></a>Segurança, autenticação e controle de acesso
* **Há suporte para a autenticação baseada no Active Directory e para o controle de acesso nos Arquivos do Azure?**  
    Os Arquivos do Azure não dão suporte a autenticação baseada em AD ou ACLs, mas isso é algo em que estamos trabalhando ativamente. Agora, há duas maneiras de contornar a falta de controle de acesso e autenticação baseada em AD:

    - Com a SAS, você pode gerar tokens com permissões específicas que são válidas por um intervalo de tempo especificado. Por exemplo, você pode gerar um token com acesso somente leitura a determinado arquivo com validade de 10 minutos. Qualquer pessoa que possui esse token enquanto ele é válido tem acesso somente leitura ao arquivo por 10 minutos. As chaves SAS só têm suporte pelas bibliotecas de cliente ou pela API REST atualmente. Você deve montar o compartilhamento dos Arquivos do Azure no SMB com as chaves da conta de armazenamento.

    - A Sincronização de Arquivos do Azure preservará e replicará todas as ACLs (baseadas em AD ou locais) para todos os pontos de extremidade do servidor com os quais ela sincroniza. Como o Windows Server já pode autenticar com o Active Directory, a Sincronização de Arquivos do Azure pode ser uma boa medida preventiva até que o suporte completo para autenticação baseada em AD e ACL fiquem disponíveis.

* **Como posso garantir que meu compartilhamento dos Arquivos do Azure está criptografado em repouso?**  
    Você não precisa fazer nada para habilitar a criptografia; a [Criptografia no Lado do Servidor](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) agora está habilitada por padrão para os Arquivos do Azure em todas as regiões públicas e nuvens nacionais. 

* **Como posso fornecer acesso a um arquivo específico usando um navegador da Web?**  
    Com a SAS, você pode gerar tokens com permissões específicas que são válidas por um intervalo de tempo especificado. Por exemplo, você pode gerar um token de acesso somente leitura a um arquivo específico para um período de tempo específico. Qualquer pessoa que possui essa URL pode acessar o arquivo diretamente em qualquer navegador da Web enquanto ele é válido. Chaves SAS podem ser facilmente geradas na interface do usuário como o Gerenciador de armazenamento.

* **É possível especificar permissões somente leitura ou somente gravação em pastas no compartilhamento?**  
    Você não terá esse nível de controle sobre as permissões se montar o compartilhamento de arquivos via SMB. No entanto, você pode conseguir isso criando uma SAS (assinatura de acesso compartilhado) por meio de bibliotecas de API REST ou de cliente.
    
* **Quais são as políticas de conformidade de dados com suporte nos Arquivos do Azure?**  
   O Arquivos do Azure é executado com base na mesma arquitetura de armazenamento de outros serviços de armazenamento no Armazenamento do Azure e aplica as mesmas políticas de conformidade de dados. Para obter mais informações sobre a conformidade de dados do Armazenamento do Azure, baixe e consulte o [documento Proteção de dados do Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409).

## <a name="on-premises-access"></a>Acesso local
* **Preciso usar o Azure ExpressRoute para me conectar aos Arquivos do Azure ou para usar a Sincronização de Arquivos do Azure localmente?**  
    Não, o ExpressRoute não é necessário para acessar um compartilhamento dos Arquivos do Azure. Se você está montando um compartilhamento dos Arquivos do Azure diretamente no local, basta ter a porta 445 (TCP de saída) aberta para acesso à Internet (essa é a porta pela qual o SMB se comunica). Se você está usando a Sincronização de Arquivos do Azure, basta ter a porta 443 (TCP de saída) para acesso HTTPS (não é necessário usar SMB). No entanto, o ExpressRoute pode ser usado com qualquer opção de acesso, se desejado.

* **Como posso montar o compartilhamento de Arquivos do Azure no computador local?**  
    Você pode montar o compartilhamento de arquivos por meio do protocolo SMB, desde que a porta 445 (TCP de Saída) esteja aberta e o cliente dê suporte ao protocolo SMB 3.0 (por exemplo, você está usando o Windows 10 ou o Windows Server 2016). Se a porta 445 está bloqueada pela política da sua organização ou por pelo ISP, você pode usar a Sincronização de Arquivos do Azure para acessar o compartilhamento dos Arquivos do Azure.

## <a name="backup"></a>Backup
* **Como posso fazer backup do meu Compartilhamento de Arquivos do Azure?**  
    Você pode usar instantâneos periódicos do compartilhamento (versão prévia) para proteger contra exclusões acidentais. Use o AzCopy, o RoboCopy ou uma ferramenta de backup de terceiros que pode fazer backup de um compartilhamento de arquivos montado.

## <a name="share-snapshots"></a>Instantâneos de compartilhamento
### <a name="share-snapshots---general"></a>Instantâneos de compartilhamento - geral
* **O que é um instantâneo de compartilhamento de arquivos?**
    Os instantâneos de compartilhamento dos Arquivos do Azure permitem que você crie versões somente leitura de seus compartilhamentos de arquivos. Eles também permitem que você copie uma versão anterior do conteúdo para o mesmo compartilhamento ou para um local alternativo no Azure ou no local para modificações adicionais. Para saber mais sobre o instantâneo de compartilhamento, consulte a [Visão geral de instantâneo de compartilhamento](storage-snapshots-files.md).

* **Onde os instantâneos de compartilhamento são armazenados?**
    Os instantâneos de compartilhamento são armazenados na mesma conta de armazenamento do compartilhamento de arquivos.

* **Há implicações no desempenho?**
    Os instantâneos de compartilhamento não geram sobrecarga de desempenho.

* **Os instantâneos de compartilhamento são consistentes com o aplicativo?**
    Não. Os instantâneos de compartilhamento não são consistentes com o aplicativo. O usuário terá que enviar as gravações do aplicativo para o compartilhamento antes de tirar um instantâneo de compartilhamento.

* **Há limites no número de instantâneos de compartilhamento?**
    Há um limite de 200 instantâneos de compartilhamento que os Arquivos do Azure podem manter. Os instantâneos de compartilhamento não são considerados na cota de compartilhamento e, portanto, não há nenhum limite por compartilhamento no espaço total utilizado por todos os instantâneos de compartilhamento. Os limites de conta de armazenamento ainda se aplicam. Depois de 200 instantâneos de compartilhamento, os instantâneos mais antigos precisarão ser excluídos para criar novos instantâneos de compartilhamento.

### <a name="create-share-snapshots"></a>Criar instantâneos de compartilhamento
* **Posso criar instantâneo de compartilhamento de arquivos individuais?**
    Os instantâneos de compartilhamento são criados no nível do compartilhamento de arquivos. Você pode restaurar arquivos individuais do instantâneo do compartilhamento de arquivos, mas não é possível criar instantâneos de compartilhamento no nível do arquivo. No entanto, se você tirou um instantâneo no nível do compartilhamento e desejar listar instantâneos de compartilhamento em que determinado arquivo foi alterado, pode fazer isso na experiência "Versões anteriores" em um compartilhamento montado no Windows.

* **Posso criar instantâneos de compartilhamento de compartilhamento de arquivos criptografado?**
    Você pode tirar um instantâneo de compartilhamento de compartilhamentos dos Arquivos do Azure que tenham a opção de criptografia em repouso habilitada. Você pode restaurar arquivos de um instantâneo de compartilhamento em um compartilhamento de arquivos criptografado. Se o compartilhamento for criptografado, o instantâneo de compartilhamento também será criptografado.

* **Meus instantâneos de compartilhamento são com redundância geográfica?**
    O instantâneo de compartilhamento terá a mesma redundância que o compartilhamento dos Arquivos do Azure. Se você tiver selecionado o GRS (armazenamento com redundância geográfica) para sua conta, o instantâneo de compartilhamento também será armazenado de forma redundante na região emparelhada.

### <a name="manage-share-snapshots"></a>Gerenciar instantâneos de compartilhamento
* **Posso navegar pelos meus instantâneos de compartilhamento no Linux?**
    Você pode usar a CLI do Azure 2.0 para criar, listar, procurar e restaurar no Linux.

* **Posso copiar os instantâneos de compartilhamento em uma conta de armazenamento diferente?**
    Você pode copiar arquivos dos instantâneos de compartilhamento para outro local, mas não os próprios instantâneos compartilhamento.

### <a name="restore-data-from-share-snapshots"></a>Restaurar dados de instantâneos de compartilhamento
* **Posso promover um instantâneo de compartilhamento a compartilhamento base?**
    Você pode copiar dados de um instantâneo de compartilhamento para qualquer destino desejado. No entanto, não é possível promover um instantâneo de compartilhamento a compartilhamento base.

* **Posso restaurar dados do meu instantâneo de compartilhamento em uma conta de armazenamento diferente?**
    Sim. Os arquivos de um instantâneo de compartilhamento podem ser copiados para o local original ou alternativo, que inclui a mesma conta de armazenamento ou uma diferente nas mesmas regiões ou em regiões diferentes. Você também pode copiar arquivos localmente ou para outra nuvem.    
  
### <a name="cleanup-share-snapshot"></a>Limpar instantâneo de compartilhamento
* **Posso excluir meu compartilhamento, mas não os instantâneos de compartilhamento?**
    Você não pode excluir o compartilhamento se você tem instantâneos de compartilhamento ativos no compartilhamento. Há uma API disponível para excluir instantâneos de compartilhamento com o compartilhamento, e você pode fazer o mesmo no portal do Azure.

* **O que acontece com meus instantâneos de compartilhamento se eu excluo minha conta de armazenamento?**
    Se você excluir sua conta de armazenamento, os instantâneos de compartilhamento também serão excluídos.

## <a name="billing-and-pricing"></a>Cobrança e preços
* **O tráfego de rede entre uma VM do Azure e uma contagem de compartilhamento de arquivos conta como largura de banda externa cobrada na assinatura?**  
    Se o compartilhamento de arquivos e a VM estiverem na mesma região do Azure, o tráfego entre eles será gratuito. Se eles estiverem em regiões diferentes, o tráfego entre eles será cobrado como largura de banda externa.

* **Quanto custam os instantâneos de compartilhamento?**
     Na versão prévia, a funcionalidade de instantâneo de compartilhamento não será cobrada. Os custos de saída e de transação do Armazenamento Standard ainda se aplicam. Após a disponibilidade geral, a capacidade e as transações de instantâneos de compartilhamento serão cobradas.
     
     Os instantâneos de compartilhamento são incrementais por natureza. O instantâneo de compartilhamento base é o próprio compartilhamento. Todos os instantâneos de compartilhamento subsequentes são incrementais e armazenam somente a diferença do instantâneo de compartilhamento anterior. Você será cobrado somente pelo conteúdo alterado. Se você tiver um compartilhamento com 100 GB de dados, mas apenas 5 GB foram alterados após o último instantâneo de compartilhamento, este consumirá apenas 5 GB adicionais e você será cobrado somente por 105 GB. Confira a [Página de preços](https://azure.microsoft.com/pricing/details/storage/files/) para saber mais sobre encargos de transações e de saída Standard.

## <a name="scale-and-performance"></a>Escala e desempenho
* **Quais são os limites de escala do Arquivos do Azure?**  
    Para obter informações sobre as metas de escalabilidade e desempenho do Arquivos do Azure, consulte [Metas de escalabilidade e desempenho do Armazenamento do Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#scalability-targets-for-blobs-queues-tables-and-files).

* **Quantos clientes podem acessar simultaneamente o mesmo arquivo?**  
    Há uma cota de 2.000 identificadores abertos em um único arquivo. Depois que você tiver 2.000 identificadores abertos, você obterá um erro dizendo que a cota foi atingida.

* **O desempenho estava lento durante a tentativa de descompactar arquivos nos Arquivos do Azure. O que devo fazer?**  
    Para transferir grandes quantidades de arquivos para o Arquivos do Azure, recomendamos o uso do AzCopy (Windows, Versão Prévia para Linux/Unix) ou do Azure PowerShell, já que essas ferramentas foram otimizadas para transferência de rede.

* **Montei meu compartilhamento dos Arquivos do Azure no Windows Server 2012 R2 ou no Windows 8.1 e o desempenho está lento. Por quê?**  
    Há um problema conhecido ao montar um compartilhamento dos Arquivos do Azure no Windows Server 2012 R2 e no Windows 8.1 que foi corrigido na atualização cumulativa de abril de 2014 para Windows 8.1 e Windows Server 2012 R2. Verifique se todas as instâncias do Windows Server 2012 R2 e do Windows 8.1 têm esse patch aplicado para um desempenho ideal (é claro que sempre recomendamos usar todos os patches do Windows por meio do Windows Update). Para saber mais, confira o artigo da base de dados associado, [Desempenho lento ao acessar o Arquivos do Azure no Windows 8.1 ou Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Recursos e interoperabilidade com outros serviços
* **Uma “Testemunha de Compartilhamento de Arquivos” de um cluster de failover é um dos casos de uso dos Arquivos do Azure?**  
    Isso não tem suporte atualmente para um compartilhamento dos Arquivos do Azure. Para saber mais sobre como configurar isso para o Armazenamento de Blobs do Azure, confira [Implantar uma testemunha de nuvem em um cluster de failover](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* **Há uma operação de renomeação na API REST?**  
    Não no momento.

* **Pode-se ter compartilhamentos aninhados, ou seja, um compartilhamento em outro compartilhamento?**  
    Não. O compartilhamento de arquivos é o driver virtual que você pode montar; portanto, não há suporte para compartilhamentos aninhados.

* **Como usar o Arquivos do Azure com o IBM MQ**  
    A IBM liberou um documento para orientar os clientes do IBM MQ durante a configuração do Arquivos do Azure com esse serviço. Para saber mais, confira [Como configurar o gerenciador de filas com várias instâncias do IBM MQ com o Serviço de Arquivos do Microsoft Azure](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Consulte também
* [Solucionar problemas de Arquivos do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Solucionar problemas de Arquivos do Azure no Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Solucionar problemas da Sincronização de Arquivos do Azure (versão prévia)](storage-sync-files-troubleshoot.md)
