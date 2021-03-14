---
title: Planejando uma implantação de Sincronização de Arquivos do Azure | Microsoft Docs
description: Planejar uma implantação com o Sincronização de Arquivos do Azure, um serviço que permite armazenar em cache vários compartilhamentos de arquivos do Azure em um Windows Server ou VM de nuvem local.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 51814ba36eec7b1f7d8b95ce80210d93b4cbec3f
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564213"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planejando uma implantação da Sincronização de Arquivos do Azure

:::row:::
    :::column:::
        [![Entrevista e demonstração apresentando a Sincronização de Arquivos do Azure – clique para reproduzir!](./media/storage-sync-files-planning/azure-file-sync-interview-video-snapshot.png)](https://www.youtube.com/watch?v=nfWLO7F52-s)
    :::column-end:::
    :::column:::
        Sincronização de Arquivos do Azure é um serviço que permite armazenar em cache vários compartilhamentos de arquivos do Azure em um Windows Server ou VM de nuvem local. 
        
        Este artigo apresenta conceitos e recursos da Sincronização de Arquivos do Azure. Quando estiver familiarizado com a Sincronização de Arquivos do Azure, siga o [guia de implantação da Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md) para experimentar esse serviço.        
    :::column-end:::
:::row-end:::

Os arquivos serão armazenados na nuvem em [compartilhamentos de arquivos do Azure](storage-files-introduction.md). Os compartilhamentos de arquivos do Azure podem ser usados de duas maneiras: ao montar diretamente esses compartilhamentos de arquivos do Azure sem servidor (SMB) ou armazenar em cache os compartilhamentos de arquivos do Azure localmente usando a Sincronização de Arquivos do Azure. A opção de implantação escolhida altera os aspectos que você precisa considerar ao planejar sua implantação. 

- **Montagem direta de um compartilhamento de arquivo do Azure**: Como os Arquivos do Azure fornecem acesso SMB, você pode montar compartilhamentos de arquivo do Azure localmente ou na nuvem usando o cliente SMB padrão disponível no Windows, no macOS e no Linux. Como os compartilhamentos de arquivos do Azure são sem servidor, a implantação para cenários de produção não requer o gerenciamento de um servidor de arquivos nem de um dispositivo NAS. Isso significa que você não precisa aplicar patches de software nem trocar discos físicos. 

- **Armazenar em cache o compartilhamento de arquivo do Azure local com a Sincronização de Arquivos do Azure**: A Sincronização de Arquivos do Azure permite centralizar os compartilhamentos de arquivos da sua organização no serviço Arquivos do Azure sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. A Sincronização de Arquivos do Azure transforma um Windows Server local (ou em nuvem) em um cache rápido do compartilhamento de arquivo do Azure. 

## <a name="management-concepts"></a>Conceitos de gerenciamento
Uma implantação da Sincronização de Arquivos do Azure tem três objetos de gerenciamento fundamentais:

- **Compartilhamento de arquivos do Azure**: Um compartilhamento de arquivo do Azure é um compartilhamento de arquivo em nuvem sem servidor, que fornece o *ponto de extremidade na nuvem* de uma relação de sincronização da Sincronização de Arquivos do Azure. Os arquivos em um compartilhamento de arquivo do Azure podem ser acessados diretamente com o protocolo SMB ou FileREST, embora recomendemos que você acesse os arquivos principalmente por meio do cache do Windows Server quando o compartilhamento de arquivo do Azure está sendo usado com a Sincronização de Arquivos do Azure. Isso ocorre porque o serviço Arquivos do Azure atualmente não tem um mecanismo de detecção de alteração eficiente, como o Windows Server tem, portanto, as alterações feitas diretamente no compartilhamento de arquivo do Azure levarão tempo para serem propagadas de volta para os pontos de extremidade do servidor.
- **Ponto de extremidade do servidor**: O caminho no Windows Server que está sendo sincronizado com um compartilhamento de arquivo do Azure. Isso pode ser uma pasta específica em um volume ou a raiz do volume. Vários pontos de extremidade do servidor poderão existir no mesmo volume se seus namespaces não forem sobrepostos.
- **Grupo de sincronização**: O objeto que define a relação de sincronização entre um **ponto de extremidade na nuvem** ou compartilhamento de arquivo do Azure e um ponto de extremidade do servidor. Os pontos de extremidade em um grupo de sincronização são mantidos em sincronização entre si. Se, por exemplo, você tiver dois conjuntos distintos de arquivos que deseja gerenciar com a Sincronização de Arquivos do Azure, crie dois grupos de sincronização e adicione pontos de extremidade diferentes a cada grupo de sincronização.

### <a name="azure-file-share-management-concepts"></a>Conceitos de gerenciamento de compartilhamento de arquivo do Azure
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Conceitos de gerenciamento de Sincronização de Arquivos do Azure
Os grupos de sincronização são implantados em **Serviços de Sincronização de Armazenamento**, que são objetos de nível superior que registram servidores para uso com a Sincronização de Arquivos do Azure e contêm as relações do grupo de sincronização. O recurso Serviço de Sincronização de Armazenamento é um par do recurso de conta de armazenamento e pode, de maneira semelhante, ser implantado em grupos de recursos do Azure. Um Serviço de Sincronização de Armazenamento pode criar grupos de sincronização que contêm compartilhamentos de arquivo do Azure entre várias contas de armazenamento e vários Windows Servers registrados.

Para criar um grupo de sincronização em um Serviço de Sincronização de Armazenamento, primeiro você deve registrar um Windows Server no Serviço de Sincronização de Armazenamento. Isso cria o objeto **servidor registrado** que representa uma relação de confiança entre seu servidor ou cluster e o Serviço de Sincronização de Armazenamento. Para registrar um Serviço de Sincronização de Armazenamento, você deve primeiro instalar o agente da Sincronização de Arquivos do Azure no servidor. Um servidor individual ou um cluster pode ser registrado apenas em um Serviço de Sincronização de Armazenamento por vez.

Um grupo de sincronização contém um ponto de extremidade na nuvem ou um compartilhamento de arquivo do Azure e pelo menos um ponto de extremidade do servidor. O objeto de ponto de extremidade do servidor contém as configurações que definem a funcionalidade de **camada de nuvem**, que fornece a funcionalidade de cache da Sincronização de Arquivos do Azure. Para sincronizar com um compartilhamento de arquivo do Azure, a conta de armazenamento que contém o compartilhamento de arquivo do Azure deve estar na mesma região do Azure que o Serviço de Sincronização de Armazenamento.

> [!Important]  
> Você pode fazer alterações no namespace de qualquer ponto de extremidade de nuvem ou de servidor no grupo de sincronização e fazer com que os arquivos sejam sincronizados com os outros pontos de extremidade no grupo de sincronização. Se você fizer uma alteração diretamente no Ponto de extremidade de nuvem (compartilhamento de Arquivos do Azure), observe que as alterações devem primeiro ser descobertas por um trabalho de detecção de alteração de sincronização de arquivos do Azure. Um trabalho de detecção de alteração é iniciado para um ponto de extremidade da nuvem apenas uma vez a cada 24 horas. Para obter mais informações, consulte [Perguntas frequentes sobre os Arquivos do Azure](storage-files-faq.md#afs-change-detection).

### <a name="consider-the-count-of-storage-sync-services-needed"></a>Considere a contagem de serviços de sincronização de armazenamento necessários
Uma seção anterior aborda o recurso principal para configurar o para Sincronização de Arquivos do Azure: um *serviço de sincronização de armazenamento*. Um Windows Server só pode ser registrado em um serviço de sincronização de armazenamento. Em geral, é melhor implantar apenas um único serviço de sincronização de armazenamento e registrar todos os servidores que ele. 

Crie vários serviços de sincronização de armazenamento somente se você tiver:
* conjuntos distintos de servidores que nunca devem trocar dados entre si. Nesse caso, você deseja projetar o sistema para excluir determinados conjuntos de servidores para sincronização com um compartilhamento de arquivos do Azure que já está em uso como um ponto de extremidade de nuvem em um grupo de sincronização em um serviço de sincronização de armazenamento diferente. Outra maneira de examinar isso é que os servidores do Windows registrados em um serviço de sincronização de armazenamento diferente não podem ser sincronizados com o mesmo compartilhamento de arquivos do Azure.
* a necessidade de ter mais servidores registrados ou grupos de sincronização do que um único serviço de sincronização de armazenamento pode dar suporte. Examine os [destinos de escala de sincronização de arquivos do Azure](storage-files-scale-targets.md#azure-file-sync-scale-targets) para obter mais detalhes.

## <a name="plan-for-balanced-sync-topologies"></a>Planejar topologias de sincronização balanceada
Antes de implantar qualquer recurso, é importante planejar o que será sincronizado em um servidor local, com o compartilhamento de arquivos do Azure. Fazer um plano ajudará você a determinar quantas contas de armazenamento, compartilhamentos de arquivos do Azure e recursos de sincronização serão necessários. Essas considerações ainda são relevantes, mesmo se os dados não residirem atualmente em um servidor Windows ou no servidor que você deseja usar em longo prazo. A [seção migração](#migration) pode ajudar a determinar os caminhos de migração apropriados para sua situação.

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="windows-file-server-considerations"></a>Considerações sobre servidor de arquivos do Windows
Para habilitar a funcionalidade de sincronização no Windows Server, você deve instalar o agente Sincronização de Arquivos do Azure para baixar. O agente de Sincronização de Arquivos do Azure fornece dois componentes principais: `FileSyncSvc.exe`, o serviço Windows em segundo plano que é responsável por monitorar alterações nos pontos de extremidade do servidor e iniciar sessões de sincronização e `StorageSync.sys`, um filtro do sistema de arquivos que habilita a camada de nuvem e a rápida recuperação de desastre.  

### <a name="operating-system-requirements"></a>Requisitos do sistema operacional
A Sincronização de Arquivos do Azure é compatível com as seguintes versões do Windows Server:

| Versão | SKUs com suporte | Opções de implantação com suporte |
|---------|----------------|------------------------------|
| Windows Server 2019 | Datacenter, Standard e IoT | Completo e Core |
| Windows Server 2016 | Datacenter, Standard e Storage Server | Completo e Core |
| Windows Server 2012 R2 | Datacenter, Standard e Storage Server | Completo e Core |

Versões futuras do Windows Server serão adicionadas à medida que forem liberadas.

> [!Important]  
> Recomendamos manter todos os servidores usados com a Sincronização de Arquivos do Azure atualizados com as últimas atualizações do Windows Update. 

### <a name="minimum-system-resources"></a>Recursos mínimos do sistema
O serviço Sincronização de Arquivos do Azure requer um servidor, físico ou virtual, com pelo menos uma CPU e um mínimo de 2 GiB de memória.

> [!Important]  
> Se o servidor estiver sendo executado em uma máquina virtual com memória dinâmica habilitada, a VM deverá ser configurada com um mínimo de 2048 MiB de memória.

Para a maioria das cargas de trabalho de produção, não recomendamos a configuração de um servidor de sincronização da Sincronização de Arquivos do Azure somente com os requisitos mínimos. Confira [Recursos do sistema recomendados](#recommended-system-resources) para obter mais informações.

### <a name="recommended-system-resources"></a>Recursos de sistema recomendados
Assim como qualquer recurso de servidor ou aplicativo, os requisitos de recursos de sistema para a Sincronização de Arquivos do Azure são determinados pela escala da implantação; implantações maiores em um servidor exigem mais recursos do sistema. Na Sincronização de Arquivos do Azure, a escala é determinada pelo número de objetos entre os pontos de extremidade do servidor e a variação no conjunto de dados. Um servidor pode ter pontos de extremidade de servidor em vários grupos de sincronização e o número de objetos listados nas contas de tabela a seguir para o namespace completo ao qual um servidor está anexado. 

Por exemplo: ponto de extremidade do servidor A com 10 milhões de objetos + ponto de extremidade do servidor B com 10 milhões de objetos = 20 milhões de objetos. Para essa implantação de exemplo, recomendamos 8 CPUs, 16 GiB de memória para o estado estável e (se possível) 48 GiB de memória para a migração inicial.
 
Os dados de namespace são armazenados na memória por questões de desempenho. Por causa disso, namespaces maiores exigem mais memória para manter o bom desempenho e mais variação requer mais CPU para processar. 
 
Na tabela a seguir, fornecemos o tamanho do namespace, bem como uma conversão para capacidade para compartilhamentos de arquivos de uso geral típicos, em que o tamanho médio do arquivo é 512 KiB. Se os tamanhos do arquivo forem menores, considere adicionar mais memória para a mesma quantidade de capacidade. Baseie sua configuração de memória no tamanho do namespace.

| Tamanho do namespace – arquivos e diretórios (milhões)  | Capacidade típica (TiB)  | Núcleos de CPU  | Memória recomendada (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (sincronização inicial)/ 2 (variação típica)      |
| 5        | 2.3     | 2        | 16 (sincronização inicial)/ 4 (variação típica)    |
| 10       | 4.7     | 4        | 32 (sincronização inicial)/ 8 (variação típica)   |
| 30       | 14.0    | 8        | 48 (sincronização inicial)/ 16 (variação típica)   |
| 50       | 23,3    | 16       | 64 (sincronização inicial)/ 32 (variação típica)  |
| 100*     | 46,6    | 32       | 128 (sincronização inicial)/ 32 (variação típica)  |

\*A sincronização de mais de 100 milhões arquivos e diretórios não é recomendada neste momento. Esse é um limite flexível com base em limites testados. Para obter mais informações, confira [Metas de desempenho e escalabilidade de Arquivos do Azure](storage-files-scale-targets.md#azure-file-sync-scale-targets).

> [!TIP]
> A sincronização inicial de um namespace é uma operação intensiva e é recomendável alocar mais memória até que a sincronização inicial seja concluída. Isso não é necessário, mas pode acelerar a sincronização inicial. 
> 
> A variação típica é de 0,5% da alteração de namespace por dia. Para níveis mais altos de variação, considere adicionar mais CPU. 

- Um volume anexado localmente formatado com o sistema de arquivos NTFS.

### <a name="evaluation-cmdlet"></a>Cmdlet de avaliação
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando o cmdlet de avaliação da Sincronização de Arquivos do Azure. Esse cmdlet verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão do sistema operacional não compatível. As verificações abrangem a maioria dos recursos mencionados abaixo, mas não todos eles. É recomendável que você leia o restante desta seção com cuidado para garantir que sua implantação seja perfeita. 

O cmdlet de avaliação pode ser instalado por meio do módulo Az PowerShell, que pode ser instalado seguindo as instruções aqui: [Instale e configure o Azure PowerShell](/powershell/azure/install-Az-ps).

#### <a name="usage"></a>Uso  
Você pode invocar a ferramenta de avaliação de algumas maneiras diferentes: você pode executar verificações de sistema, verificações de conjunto de dados ou ambas. Para executar verificações de sistema e de conjunto de dados: 

```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

Para testar apenas o conjunto de dados:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Para testar somente os requisitos do sistema:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name> -SkipNamespaceChecks
```
 
Para exibir os resultados em CSV:
```powershell
$validation = Invoke-AzStorageSyncCompatibilityCheck C:\DATA
$validation.Results | Select-Object -Property Type, Path, Level, Description, Result | Export-Csv -Path C:\results.csv -Encoding utf8
```

### <a name="file-system-compatibility"></a>Compatibilidade do sistema de arquivos
A Sincronização de Arquivos do Azure só é compatível com volumes NTFS anexados diretamente. O armazenamento com conexão direta (ou DAS) no Windows Server significa que o sistema operacional do Windows Server é proprietário do sistema de arquivos. O DAS pode ser fornecido anexando discos físicos ao servidor de arquivos, anexando discos virtuais a uma VM do servidor de arquivos (como uma VM hospedada pelo Hyper-V) ou até mesmo por meio de ISCSI.

Somente volumes NTFS são compatíveis; ReFS, FAT, FAT32 e outros sistemas de arquivos não são compatíveis.

A seguinte tabela mostra o estado de interoperabilidade dos recursos do sistema de arquivos NTFS: 

| Recurso | Status de suporte | Observações |
|---------|----------------|-------|
| ACLs (listas de controle de acesso) | suporte completo | As listas de controle de acesso condicional de estilo do Windows são preservadas pela Sincronização de Arquivos do Azure e são impostas pelo Windows Server em pontos de extremidade de servidor. As ACLs também podem ser impostas ao montar diretamente o compartilhamento de arquivo do Azure, no entanto, isso requer configuração adicional. Confira a [Seção identidade](#identity) para obter mais informações. |
| Links físicos | Ignorado | |
| Links simbólicos | Ignorado | |
| Pontos de montagem | Suporte parcial | Pontos de montagem podem ser a raiz de um Ponto de Extremidade de Servidor, mas serão ignorados se estiverem contidos no namespace de um ponto de extremidade de servidor. |
| Junções | Ignorado | Por exemplo, as pastas DFSRoots e DfrsrPrivate do Sistema de Arquivos Distribuído. |
| Pontos de nova análise | Ignorado | |
| Compactação NTFS | suporte completo | |
| Arquivos esparsos | suporte completo | Os arquivos esparsos são sincronizados (não são bloqueados), mas são sincronizados com a nuvem como um arquivo completo. Se o conteúdo do arquivo for alterado na nuvem (ou em outro servidor), o arquivo não será mais esparso quando a alteração for baixada. |
| ADS (Fluxos de Dados Alternativos) | Preservados, mas não sincronizados | Por exemplo, as marcas de classificação criadas pela Infraestrutura de classificação de arquivos não são sincronizadas. As marcas de classificação em arquivos existentes em cada um dos pontos de extremidade do servidor são mantidas. |

<a id="files-skipped"></a>A Sincronização de Arquivos do Azure também ignorará determinados arquivos temporários e pastas do sistema:

| Arquivo/pasta | Observação |
|-|-|
| pagefile.sys | Arquivo específico do sistema |
| Desktop.ini | Arquivo específico do sistema |
| thumbs.db | Arquivo temporário para miniaturas |
| ehthumbs.db | Arquivo temporário para miniaturas de mídia |
| ~$\*.\* | Arquivo temporário do Office |
| \*.tmp | Arquivo temporário |
| \*.laccdb | Arquivo de bloqueio do banco de dados do Access|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Arquivo de sincronização interno|
| \\Informações de Volume do Sistema | Pasta específica do volume |
| $RECYCLE.BIN| Pasta |
| \\SyncShareState | Pasta para sincronização |

### <a name="failover-clustering"></a>Clustering de failover
O clustering de failover do Windows Server tem suporte pela Sincronização de Arquivo do Azure para a opção de implantação “Servidor de Arquivos de uso geral”. Não há suporte para o Clustering de Failover em “SOFS (Servidor de Arquivos de Escalabilidade Horizontal) para dados de aplicativos” ou CSVs (Volumes Compartilhados Clusterizados).

> [!Note]  
> O agente de Sincronização de Arquivos do Azure deve ser instalado em cada nó em um Cluster de Failover para a sincronização funcionar corretamente.

### <a name="data-deduplication"></a>Eliminação de duplicação de dados
**Windows Server 2016 e Windows Server 2019**   
A eliminação de duplicação de dados tem suporte, independentemente de a disposição da nuvem estar habilitada ou desabilitada em um ou mais pontos de extremidade do servidor no volume para o Windows Server 2016 e o Windows Server 2019. Habilitar a Eliminação de Duplicação de Dados em um volume com camada de nuvem habilitada permite armazenar em cache mais arquivos localmente sem ter que provisionar mais armazenamento. 

Quando a Eliminação de Duplicação de Dados é habilitada em um volume com camada de nuvem habilitada, os arquivos otimizados para eliminação de duplicação na localização do ponto de extremidade do servidor serão colocados em camadas semelhantes a um arquivo normal com base nas configurações de política de camada de nuvem. Depois que os arquivos otimizados para eliminação de duplicação estiverem em camadas, o trabalho de coleta de lixo da Eliminação de Duplicação de Dados será executado automaticamente para recuperar espaço em disco removendo partes desnecessárias que não são mais referenciadas por outros arquivos no volume.

Observe que a economia em volume se aplica somente ao servidor; seus dados no compartilhamento de arquivo do Azure não passarão pela eliminação de duplicação.

> [!Note]  
> Para dar suporte à Eliminação de Duplicação de Dados em volumes com a camada de nuvem habilitada no Windows Server 2019, o Windows Update [KB4520062](https://support.microsoft.com/help/4520062) deve ser instalado e o agente da Sincronização de Arquivos do Azure versão 9.0.0.0 ou mais recente é necessário.

**Windows Server 2012 R2**  
A Sincronização de Arquivos do Azure não é compatível com a Eliminação de Duplicação de Dados e a camada de nuvem no mesmo volume no Windows Server 2012 R2. Se a Eliminação de Duplicação de Dados estiver habilitada em um volume, a camada de nuvem deverá ser desabilitada. 

**Observações**
- Se a Eliminação de Duplicação de Dados for instalada antes da instalação do agente da Sincronização de Arquivos do Azure, uma reinicialização será necessária para dar suporte à Eliminação de Duplicação de Dados e à camada de nuvem no mesmo volume.
- Se a Eliminação de Duplicação de Dados estiver habilitada em um volume depois que a camada de nuvem estiver habilitada, o trabalho de otimização de eliminação de duplicação inicial otimizará os arquivos do volume que ainda não estiverem em camadas e terá o seguinte impacto na camada de nuvem:
    - A política de espaço livre continuará a colocar arquivos em camada de acordo com o espaço livre no volume usando o mapa de calor.
    - A política de data ignorará a camada de arquivos que, de outra forma, poderia ter sido qualificada para camada devido ao trabalho de otimização da eliminação de duplicação que acessa os arquivos.
- Para trabalhos de otimização de eliminação de duplicação em andamento, a camada de nuvem com a política de data será atrasada pela configuração [MinimumFileAgeDays](/powershell/module/deduplication/set-dedupvolume) da Eliminação de Duplicação de Dados, se o arquivo ainda não estiver em camada. 
    - Exemplo: Se a configuração MinimumFileAgeDays for de sete dias e a política de data da camada de nuvem for de 30 dias, a política de data colocará os arquivos em camada após 37 dias.
    - Observação: Quando um arquivo é colocado em camadas pela Sincronização de Arquivos do Azure, o trabalho de otimização da eliminação de duplicação ignorará o arquivo.
- Se um servidor que executa o Windows Server 2012 R2 com o agente da Sincronização de Arquivos do Azure instalado for atualizado para o Windows Server 2016 ou o Windows Server 2019, as seguintes etapas deverão ser executadas para dar suporte à Eliminação de Duplicação de Dados e à camada de nuvem no mesmo volume:  
    - Desinstale o agente da Sincronização de Arquivos do Azure para Windows Server 2012 R2 e reinicie o servidor.
    - Baixe o agente da Sincronização de Arquivos do Azure para a nova versão do sistema operacional do servidor (Windows Server 2016 ou Windows Server 2019).
    - Instale o agente de Sincronização de Arquivos do Azure e reinicie o servidor.  
    
    Observação: As definições de configuração de Sincronização de Arquivos do Azure no servidor são mantidas quando o agente é desinstalado e reinstalado.

### <a name="distributed-file-system-dfs"></a>DFS (Sistema de Arquivos Distribuído)
A Sincronização de Arquivos do Azure fornece suporte para interoperabilidade com Namespaces de DFS (DFS-N) e Replicação do DFS (DFS-R).

**DFS-N (Namespaces de DFS)** : A Sincronização de Arquivos do Azure é totalmente suportada em servidores DFS-N. É possível instalar o agente Sincronização de arquivos do Azure em um ou mais membros DFS-N para sincronizar dados entre os pontos de extremidade de servidor e o ponto de extremidade da nuvem. Para obter mais informações, consulte [visão geral de Namespaces de DFS](/windows-server/storage/dfs-namespaces/dfs-overview).
 
**DFS-R (Replicação do DFS)** : Quando tanto DFS-R como a Sincronização de arquivos do Azure forem soluções de replicação, na maioria dos casos é recomendável substituir DFS-R pela Sincronização de Arquivos do Azure. No entanto, existem vários cenários em que você deseja usar DFS-R e Sincronização de arquivos do Azure em conjunto:

- Você está migrando de uma implantação de DFS-R para uma implantação de Sincronização de arquivos do Azure. Para obter mais informações, consulte [Migrar uma implantação de DFS-R (Replicação do DFS) para Sincronização de arquivos do Azure](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Nem todo servidor local que precisa de uma cópia dos dados do seu arquivo pode ser conectado diretamente à Internet.
- Os servidor de ramificação consolidam dados em um servidor de hub único, para o qual você gostaria de usar a Sincronização de arquivos do Azure.

Para que a Sincronização de Arquivos do Azure e o DFS-R trabalharem lado a lado:

1. A camada de nuvem da Sincronização de arquivos do Azure deve ser desabilitada em volumes com pastas replicadas DFS-R.
2. Os pontos de extremidade de servidor não devem ser configurados em pastas de replicação somente leitura do DFS-R.

Para obter mais informações, consulte [Visão geral da Replicação do DFS](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj127250(v=ws.11)).

### <a name="sysprep"></a>Sysprep
Não há compatibilidade no uso do sysprep em um servidor que tenha o agente de Sincronização de Arquivos do Azure instalado e isso pode levar a resultados inesperados. A instalação do agente e o registro do servidor devem ocorrer depois da implantação da imagem do servidor e da conclusão da mini-instalação do sysprep.

### <a name="windows-search"></a>Windows Search
Se a opção de camadas em nuvem estiver habilitada em um ponto de extremidade do servidor, os arquivos que estão em camadas serão ignorados e não serão indexados pelo Windows Search. Arquivos sem camadas são indexados corretamente.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Outras soluções de HSM (Gerenciamento de Armazenamento Hierárquico)
Nenhuma outra solução de HSM deve ser usada com a Sincronização de Arquivos do Azure.

## <a name="performance-and-scalability"></a>Desempenho e escalabilidade

Como o agente do Azure File Sync é executado em um computador Windows Server que se conecta aos compartilhamentos de arquivos do Azure, o desempenho de sincronização efetivo depende de vários fatores em sua infraestrutura: Windows Server e a configuração de disco subjacente, largura de banda de rede entre o servidor e o Azure armazenamento, tamanho do arquivo, tamanho total do conjunto de dados e atividade no conjunto de dados. Desde que a sincronização de arquivos do Azure funciona no nível de arquivo, as características de desempenho de uma solução de sincronização de arquivos do Azure melhor é medido em número de objetos (arquivos e diretórios) processado por segundo.

As alterações feitas ao compartilhamento de Arquivos do Azure usando o Portal do Azure ou SMB não são detectadas e replicadas imediatamente como as alterações no Ponto de extremidade do servidor são. Os Arquivos do Azure ainda não têm notificações/diário de alteração, portanto, não há nenhuma maneira de iniciar automaticamente uma sessão de sincronização quando os arquivos são alterados. No Windows Server, Sincronização de Arquivos do Azure usa o [diário de USN do Windows](https://docs.microsoft.com/windows/win32/fileio/change-journals) para iniciar automaticamente uma sessão de sincronização quando os arquivos são alterados

Para detectar alterações para o compartilhamento de arquivos do Azure, a sincronização de arquivos do Azure tem um trabalho agendado chamado um alterar o trabalho de detecção. Um trabalho de detecção de alteração enumera todos os arquivos no compartilhamento de arquivos e, em seguida, compara a versão de sincronização para esse arquivo. Quando o trabalho de detecção de alteração determina que os arquivos foram alterados, a sincronização de Arquivos do Azure inicia uma sessão de sincronização. O trabalho de detecção de alteração é iniciado a cada 24 horas. Como o trabalho de detecção de alteração funciona enumerando todos os arquivos no compartilhamento de Arquivos do Azure, a detecção de troca demora mais nos namespaces grandes do que namespaces menores. Para esses namespaces, pode levar mais de uma vez a cada 24 horas para determinar quais arquivos foram alterados.

Para obter mais informações, consulte [métricas de desempenho sincronização de arquivos do Azure](storage-files-scale-targets.md#azure-file-sync-performance-metrics) e [destinos de escala de sincronização de arquivos do Azure](storage-files-scale-targets.md#azure-file-sync-scale-targets)

## <a name="identity"></a>Identidade
A Sincronização de Arquivos do Azure trabalha com sua identidade padrão baseada em AD sem qualquer configuração especial além da configuração da sincronização. Quando você está usando a Sincronização de Arquivos do Azure, a expectativa geral é que a maioria dos acessos percorra os servidores de cache de Sincronização de Arquivos do Azure, em vez de usar o compartilhamento de arquivo do Azure. Como os pontos de extremidade do servidor estão localizados no Windows Server e o Windows Server é compatível com ACLs de estilo do AD e do Windows há muito tempo, não é necessário nada além de garantir que os servidores de arquivo do Windows registrados com o Serviço de Sincronização de Armazenamento sejam ingressados no domínio. A Sincronização de Arquivos do Azure armazenará ACLs nos arquivos do compartilhamento de arquivo do Azure e os replicará para todos os pontos de extremidade do servidor.

Embora as alterações feitas diretamente no compartilhamento de arquivo do Azure demorem mais para serem sincronizadas com os pontos de extremidade do servidor no grupo de sincronização, também é interessante garantir a imposição das suas permissões do AD no compartilhamento de arquivo diretamente na nuvem. Para fazer isso, você precisa ingressar no domínio em sua conta de armazenamento no AD local, assim como os servidores de arquivos do Windows são ingressados no domínio. Para saber mais sobre o ingresso no domínio com sua conta de armazenamento em um Active Directory de propriedade do cliente, confira [Visão geral sobre Active Directory no serviço Arquivos do Azure](storage-files-active-directory-overview.md).

> [!Important]  
> Não é necessário ingressar sua conta de armazenamento no domínio do Active Directory para implantar a Sincronização de Arquivos do Azure com êxito. Essa é uma etapa estritamente opcional que permite que o compartilhamento de arquivo do Azure imponha ACLs locais quando os usuários montam o compartilhamento de arquivo do Azure diretamente.

## <a name="networking"></a>Rede
O agente de Sincronização de Arquivos do Azure se comunica com o Serviço de Sincronização de Armazenamento e o compartilhamento de arquivo do Azure usando o protocolo REST e o protocolo FileREST da Sincronização de Arquivos do Azure e ambos sempre usam HTTPS pela porta 443. O SMB nunca é usado para carregar nem baixar dados entre o Windows Server e o compartilhamento de arquivo do Azure. Como a maioria das organizações permite o tráfego HTTPS pela porta 443, como um requisito para visitar a maioria dos sites, normalmente a configuração de rede especial não é necessária para implantar a Sincronização de Arquivos do Azure.

Com base na política da sua organização ou em requisitos regulatórios específicos, você pode exigir comunicação mais restritiva com o Azure e, portanto, a Sincronização de Arquivos do Azure oferece vários mecanismos para você configurar a rede. Com base em seus requisitos, você pode:

- Passar tráfego de sincronização e upload/download de arquivos por túnel em seu ExpressRoute ou na VPN do Azure. 
- Fazer uso de Arquivos do Azure e recursos de Rede do Azure, como pontos de extremidade de serviço e pontos de extremidade privados.
- Configurar a Sincronização de Arquivos do Azure para compatibilidade com o proxy em seu ambiente.
- Restringir a atividade de rede da Sincronização de Arquivos do Azure.

Para saber mais sobre Sincronização de Arquivos do Azure e rede, confira [sincronização de arquivos do Azure considerações de rede](storage-sync-files-networking-overview.md).

## <a name="encryption"></a>Criptografia
Ao usar a Sincronização de Arquivos do Azure, há três camadas diferentes de criptografia a serem consideradas: criptografia no armazenamento em repouso do Windows Server, criptografia em trânsito entre o agente de Sincronização de Arquivos do Azure e o Azure e a criptografia em repouso dos dados no compartilhamento de arquivo do Azure. 

### <a name="windows-server-encryption-at-rest"></a>Criptografia em repouso do Windows Server 
Há duas estratégias para criptografar dados no Windows Server que funcionam geralmente com a Sincronização de Arquivos do Azure: criptografia subjacente do sistema de arquivos de modo que o sistema de arquivos e todos os dados gravados nele sejam criptografados e criptografia no próprio formato de arquivo. Esses métodos não são mutuamente exclusivos; eles podem ser usados juntos, se desejado, uma vez que a finalidade da criptografia é diferente.

Para fornecer criptografia subjacente ao sistema de arquivos, o Windows Server oferece a caixa de entrada do BitLocker. O BitLocker é totalmente transparente para a Sincronização de Arquivos do Azure. O principal motivo para usar um mecanismo de criptografia como o BitLocker é impedir a exfiltração física de dados do seu datacenter local por alguém que queira roubar os discos e para impedir o sideload de um sistema operacional não autorizado para executar leituras/gravações não autorizadas em seus dados. Para saber mais sobre o BitLocker, confira [Visão geral do BitLocker](/windows/security/information-protection/bitlocker/bitlocker-overview).

Os produtos de terceiros que funcionam de maneira semelhante ao BitLocker, no que ficam subjacente ao volume NTFS, devem funcionar de maneira totalmente transparente com a Sincronização de Arquivos do Azure. 

O outro método principal para criptografar dados é criptografar o fluxo de dados do arquivo quando o aplicativo salva o arquivo. Alguns aplicativos podem fazer isso nativamente, no entanto, geralmente esse não é o caso. Um exemplo de um método para criptografar o fluxo de dados do arquivo é a AIP (Proteção de Informações do Azure)/o Azure RMS (Azure Rights Management Services)/o RMS do Active Directory. O principal motivo para usar um mecanismo de criptografia como o AIP/RMS é impedir a exfiltração dos dados do seu compartilhamento de arquivo por pessoas que os copiam para locais alternativos, como em uma unidade flash ou os enviam por email para uma pessoa não autorizada. Quando o fluxo de dados de um arquivo é criptografado como parte do formato de arquivo, esse arquivo continuará a ser criptografado no compartilhamento de arquivo do Azure. 

A Sincronização de Arquivos do Azure não interopera com o EFS (sistema de arquivos criptografados) de NTFS nem com soluções de criptografia de terceiros que ficam acima do sistema de arquivos, mas abaixo do fluxo de dados do arquivo. 

### <a name="encryption-in-transit"></a>Criptografia em trânsito

> [!NOTE]
> O serviço Sincronização de Arquivos do Azure removerá o suporte para TLS 1.0 e 1.1 em 1º de agosto de 2020. Todas as versões de agente da Sincronização de Arquivos do Azure compatíveis já usam o TLS 1.2 por padrão. O uso de uma versão anterior do TLS poderia ocorrer se o TLS 1.2 fosse desabilitado no servidor ou um proxy fosse usado. Se você estiver usando um proxy, recomendamos que verifique a configuração do proxy. As regiões do serviço Sincronização de Arquivos do Azure adicionadas após 01/05/2020 serão compatíveis apenas com o TLS 1.2 e a compatibilidade com TLS 1.0 e 1.1 será removida das regiões existentes em 1º de agosto de 2020.  Para obter mais informações, confira o [guia de solução de problemas](storage-sync-files-troubleshoot.md#tls-12-required-for-azure-file-sync).

O agente de Sincronização de Arquivos do Azure se comunica com o Serviço de Sincronização de Armazenamento e o compartilhamento de arquivo do Azure usando o protocolo REST e o protocolo FileREST da Sincronização de Arquivos do Azure e ambos sempre usam HTTPS pela porta 443. A Sincronização de Arquivos do Azure não envia solicitações não criptografadas por HTTP. 

As contas de armazenamento do Azure contêm uma opção para exigir criptografia em trânsito, que é habilitada por padrão. Mesmo que a opção no nível da conta de armazenamento esteja desabilitada, o que significa que as conexões não criptografadas com os compartilhamentos de arquivo do Azure são possíveis, a Sincronização de Arquivos do Azure ainda usará canais criptografados para acessar seu compartilhamento de arquivo.

O principal motivo para desabilitar a criptografia em trânsito para a conta de armazenamento é a compatibilidade com um aplicativo herdado que deva ser executado em um sistema operacional mais antigo, como o Windows Server 2008 R2 ou a distribuição mais antiga do Linux, que converse com um compartilhamento de arquivo do Azure diretamente. Se o aplicativo herdado se comunicar com o cache do Windows Server do compartilhamento de arquivo, mudar essa configuração não terá nenhum efeito. 

É altamente recomendável garantir que a criptografia de dados em trânsito esteja habilitada.

Para obter mais informações sobre criptografia em trânsito, consulte [Exigir transferência segura no armazenamento do Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="azure-file-share-encryption-at-rest"></a>Criptografia de compartilhamento de arquivo do Azure em repouso
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Camadas de armazenamento
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Habilitar compartilhamentos de arquivo padrão para abranger até 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Disponibilidade regional
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Disponibilidade de região da sincronização de arquivos do Azure

Para disponibilidade regional, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

As seguintes regiões exigem que você solicite acesso ao armazenamento do Azure antes de poder usar Sincronização de Arquivos do Azure com eles:

- Sul da França
- Oeste da África do Sul
- EAU Central

Para solicitar acesso a essas regiões, siga o processo neste [documento](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="redundancy"></a>Redundância
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> O armazenamento com redundância geográfica e redundância de zona geográfica tem a capacidade de fazer failover manual do armazenamento na região secundária. É recomendável que você não faça isso fora de um desastre quando estiver usando a Sincronização de Arquivos do Azure devido à maior probabilidade de perda de dados. No caso de um desastre em que você gostaria de iniciar um failover manual do armazenamento, será necessário abrir um caso de suporte com a Microsoft para fazer com que a Sincronização de Arquivos do Azure retome a sincronização com o ponto de extremidade secundário.

## <a name="migration"></a>Migração
Se você tiver um servidor de arquivos do Windows 2012R2 ou mais recente, Sincronização de Arquivos do Azure pode ser instalado diretamente no local, sem a necessidade de mover dados para um novo servidor. Se você estiver planejando migrar para um novo servidor de arquivos do Windows como parte da adoção de Sincronização de Arquivos do Azure, ou se os dados estiverem atualmente localizados no NAS (armazenamento conectado à rede), há várias abordagens de migração possíveis para usar Sincronização de Arquivos do Azure com esses dados. Qual abordagem de migração você deve escolher, depende de onde os dados residem atualmente. 

Confira a [sincronização de arquivos do Azure e o artigo Visão geral da migração de compartilhamento de arquivos do Azure](storage-files-migration-overview.md) , onde você pode encontrar diretrizes detalhadas para seu cenário.

## <a name="antivirus"></a>Antivírus
Como o antivírus funciona examinando arquivos em busca de código mal-intencionado conhecido, um produto antivírus pode causar a recall de arquivos em camadas, resultando em encargos de egresso altos. Nas versões 4.0 e acima do agente de Sincronização de Arquivo do Azure, arquivos em camadas têm o conjunto FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS de atributo seguro do Windows. Recomendamos consultar o fornecedor do software para saber como configurar a solução para ignorar a leitura de arquivos com esse conjunto de atributos (muitos fazem isso automaticamente). 

As soluções antivírus internas da Microsoft, o Windows Defender e o System Center Endpoint Protection (SCEP), ignoram automaticamente a leitura de arquivos que possuem esse atributo definido. Nós os testamos e identificamos um problema menor: quando você adiciona um servidor a um grupo de sincronização existente, os arquivos com menos de 800 bytes são recuperados (feitos o download) no novo servidor. Esses arquivos permanecerão no novo servidor e não serão colocados em camadas, pois não atendem ao requisito de tamanho em camadas (> 64kb).

> [!Note]  
> Os fornecedores de antivírus podem verificar a compatibilidade entre seus produtos e a Sincronização de Arquivos do Azure usando o [Conjunto de testes de compatibilidade de antivírus da Sincronização de Arquivos do Azure](https://www.microsoft.com/download/details.aspx?id=58322), que está disponível para download no Centro de Download da Microsoft.

## <a name="backup"></a>Backup 
Se a disposição em camadas da nuvem estiver habilitada, as soluções que fazem backup diretamente do ponto de extremidade do servidor ou de uma VM na qual o ponto de extremidade do servidor está localizado não devem ser usadas. A camada de nuvem faz com que apenas um subconjunto de seus dados seja armazenado no ponto de extremidade do servidor, com o DataSet completo que reside em seu compartilhamento de arquivos do Azure. Dependendo da solução de backup usada, os arquivos em camadas serão ignorados e não será feito backup (porque têm o conjunto de atributos FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS) ou serão rechamados para o disco, resultando em encargos de egresso altos. É recomendável usar uma solução de backup de nuvem para fazer backup do compartilhamento de arquivos do Azure diretamente. Para obter mais informações, consulte [sobre o backup do compartilhamento de arquivos do Azure](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) ou contate seu provedor de backup para ver se eles dão suporte ao backup de compartilhamentos de arquivos do Azure.

Se você preferir usar uma solução de backup local, os backups devem ser executados em um servidor no grupo de sincronização que tenha a camada de nuvem desabilitada. Ao executar uma restauração, use as opções de restauração no nível do volume ou no nível do arquivo. Os arquivos restaurados usando a opção de restauração no nível do arquivo serão sincronizados com todos os pontos de extremidade no grupo de sincronização e os arquivos existentes serão substituídos pela versão restaurada do backup.  As restaurações no nível de volume não substituirão as versões de arquivo mais recentes no compartilhamento de arquivos do Azure ou em outros pontos de extremidade do servidor.

> [!WARNING]
> Não há suporte para a opção Robocopy/B com Sincronização de Arquivos do Azure. Usando a opção Robocopy/B com um ponto de extremidade do Sincronização de Arquivos do Azure Server como a origem pode levar ao arquivo corrompido.

> [!Note]  
> A restauração bare-metal (BMR) pode causar resultados inesperados e não é atualmente suportada.

> [!Note]  
> Com a versão 9 do agente de Sincronização de Arquivos do Azure, os instantâneos do VSS (incluindo a guia Versões Anteriores) não são compatíveis com volumes com camada de nuvem habilitada. No entanto, você deve habilitar a compatibilidade de versão anterior por meio do PowerShell. [Saiba como](storage-sync-files-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service).

## <a name="azure-file-sync-agent-update-policy"></a>Política de atualização do agente de Sincronização de Arquivo do Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Próximas etapas
* [Considere as configurações de firewall e proxy](storage-sync-files-firewall-and-proxy.md)
* [Planejando uma implantação de Arquivos do Azure](storage-files-planning.md)
* [Implantar os Arquivos do Azure](./storage-how-to-create-file-share.md)
* [Implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)
* [Monitorar a Sincronização de Arquivos do Azure](storage-sync-files-monitoring.md)
