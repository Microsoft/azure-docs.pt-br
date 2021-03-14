---
title: Matriz de proteção UR1 MABS (Servidor de Backup do Azure) v3
description: Este artigo fornece uma matriz de suporte listando todas as cargas de trabalho, tipos de dados e instalações que o Servidor de Backup do Azure protege.
ms.date: 03/19/2020
ms.topic: conceptual
ms.openlocfilehash: d8f2a38db468ec57cdc0a4f0813fe7ae8e341d52
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102609763"
---
# <a name="mabs-azure-backup-server-v3-ur1-protection-matrix"></a>Matriz de proteção UR1 MABS (Servidor de Backup do Azure) v3

Este artigo lista os vários servidores e as cargas de trabalho que você pode proteger com o Servidor de Backup do Azure. A matriz a seguir lista o que pode ser protegido com o Servidor de Backup do Azure.

Use a matriz a seguir para MABS v3 UR1:

* Cargas de trabalho – o tipo de carga de trabalho da tecnologia.

* Versão – versão MABS com suporte para as cargas de trabalho.

* Instalação do MABS – o computador/local onde você deseja instalar o MABS.

* Proteção e recuperação – lista as informações detalhadas sobre a carga de trabalho, como o contêiner de armazenamento com suporte ou a implantação com suporte.

>[!NOTE]
>O suporte para o agente de proteção de 32 bits foi preterido com UR1 MABS v3. Consulte [substituição do agente de proteção de 32 bits](backup-mabs-whats-new-mabs.md#32-bit-protection-agent-deprecation).

## <a name="protection-support-matrix"></a>Matriz de suporte de proteção

As seções a seguir detalham a matriz de suporte à proteção para MABS:

* [Backup de aplicativos](#applications-backup)
* [Backup de VM](#vm-backup)
* [Linux](#linux)

## <a name="applications-backup"></a>Backup de aplicativos

| **Carga de trabalho**               | **Versão**                                                  | **Instalação do Servidor de Backup do Azure**                       | **Servidor de Backup do Azure com suporte** | **Proteção e recuperação**                                  |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------------------- | ------------------------------------------------------------ |
| Computadores cliente (64 bits) | Windows 10                                                  | Servidor físico  <br><br>    Máquina virtual do Hyper-V   <br><br>   Máquina virtual do VMware | UR1 V3                            | Volume, compartilhamento, pasta, arquivos, volumes com eliminação de duplicação   <br><br>   Os volumes protegidos devem ser NTFS. Não há suporte para o FAT e o FAT32.  <br><br>    Os volumes devem ser de, pelo menos, 1 GB. Servidor de Backup do Azure usa Serviço de Cópias de Sombra de Volume (VSS) para pegar o instantâneo de dados e o instantâneo só funciona se o volume for de pelo menos 1 GB. |
| Servidores (64 bits)          | Windows Server 2019, 2016, 2012 R2, 2012                    | Máquina virtual do Azure (quando a carga de trabalho estiver em execução como máquina virtual do Azure)  <br><br>    Servidor físico  <br><br>    Máquina virtual do Hyper-V <br><br>     Máquina virtual do VMware  <br><br>    Azure Stack | UR1 V3                            | Volume, compartilhamento, pasta, arquivo <br><br>    Volumes com eliminação de duplicação (somente NTFS) <br><br>Ao proteger um volume com eliminação de duplicação NTFS do WS 2016 com o MABS v3 em execução no Windows Server 2019, as recuperações podem ser afetadas. Temos uma correção para fazer recuperações em uma forma sem eliminação de duplicação que fará parte das versões posteriores do MABS. Contate o suporte do MABS se precisar dessa correção no MABS v3 UR1.<br><br> Ao proteger um volume com eliminação de duplicação NTFS do WS 2019 com o MABS v3 no Windows Server 2016, os backups e restaurações não serão duplicados. Isso significa que os backups consumirão mais espaço no servidor MABS do que o volume de eliminação de duplicação NTFS original.   <br><br>   Estado do sistema e bare-metal (sem suporte quando a carga de trabalho está em execução como máquina virtual do Azure) |
| Servidores (64 bits)          | Windows Server 2008 R2 SP1, Windows Server 2008 SP2 (você precisa instalar o [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616)) | Servidor físico  <br><br>    Máquina virtual do Hyper-V  <br><br>      Máquina virtual VMware  <br><br>   Azure Stack | UR1 V3                            | Volume, compartilhamento, pasta, arquivo, estado do sistema/bare metal        |
| SQL Server                | SQL Server 2019, 2017, 2016 e [SPS com suporte](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202016), 2014 e [SPS](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202014) com suporte | Servidor físico  <br><br>     Máquina virtual do Hyper-V   <br><br>     Máquina virtual VMware  <br><br>   Máquina virtual do Azure (quando a carga de trabalho está sendo executada como máquina virtual do Azure)  <br><br>     Azure Stack | UR1 V3                            | Todos os cenários de implantação: banco de dados       <br><br>  O MABS v3 UR1 dá suporte ao backup de bancos de dados SQL em volumes ReFS     <br><br>     O MABS não dá suporte a bancos de dados SQL Server hospedados no Windows Server 2012 Scale-Out File Servers (SOFS). <br><br>   O MABS não pode proteger o DAG (grupo de disponibilidade distribuída) do SQL Server ou o AG (grupo de disponibilidade), em que o nome da função no cluster de failover é diferente do denominado AG no SQL.       |
| Exchange                   | Exchange 2019, 2016                                         | Servidor físico   <br><br>   Máquina virtual do Hyper-V  <br><br>      Máquina virtual VMware  <br><br>   Azure Stack  <br><br>    Máquina virtual do Azure (quando a carga de trabalho está sendo executada como máquina virtual do Azure) | UR1 V3                            | Proteger (todos os cenários de implantação): Exchange Server autônomo, banco de dados em um grupo de disponibilidade de banco de dados (DAG)  <br><br>    Recupera (todos os cenários de implantação): caixa de correio, bancos de dados de caixa de correio em um DAG    <br><br>  Há suporte para o backup do Exchange em ReFS com o MABS v3 UR1 |
| SharePoint                 | SharePoint 2019, 2016 com SPs mais recentes                       | Servidor físico  <br><br>    Máquina virtual do Hyper-V <br><br>    Máquina virtual VMware  <br><br>   Máquina virtual do Azure (quando a carga de trabalho está sendo executada como máquina virtual do Azure)   <br><br>   Azure Stack | UR1 V3                            | Proteger (todos os cenários de implantação): Farm, conteúdo do servidor Web front-end  <br><br>    Recuperar (todos os cenários de implantação): Farm, banco de dados, aplicativo Web, arquivo ou item de lista, pesquisa do SharePoint, servidor Web front-end  <br><br>    Não há suporte para a proteção de um farm do SharePoint que está usando o SQL Server recurso AlwaysOn 2012 para os bancos de dados de conteúdo. |

## <a name="vm-backup"></a>Backup de VM

| **Carga de trabalho**                                                 | **Versão**                                             | **Instalação do Servidor de Backup do Azure**                      | **Servidor de Backup do Azure com suporte** | **Proteção e recuperação**                                 |
| ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Host Hyper-V-agente de proteção MABS no servidor host do Hyper-V, cluster ou VM | Windows Server 2019, 2016, 2012 R2, 2012               | Servidor físico  <br><br>    Máquina virtual do Hyper-V <br><br>    Máquina virtual VMware | UR1 V3                             | Proteger: computadores Hyper-V, volumes compartilhados do cluster (CSVs)  <br><br>    Recuperar: máquina virtual, recuperação em nível de item de arquivos e pastas disponíveis somente para Windows, volumes, discos rígidos virtuais |
| VMs VMware                                                  | VMware Server 5,5, 6,0 ou 6,5, 6,7 (versão licenciada) | Máquina virtual do Hyper-V  <br><br>   Máquina virtual VMware         | UR1 V3                             | Proteger: VMs VMware em CSVs (volumes compartilhados de cluster), NFS e armazenamento SAN   <br><br>     Recuperar: máquina virtual, recuperação em nível de item de arquivos e pastas disponíveis somente para Windows, volumes, discos rígidos virtuais <br><br>    Não há suporte para o VMware vApps. |

>[!NOTE]
> O MABS não dá suporte a backup de máquinas virtuais com discos de passagem ou aqueles que usam um VHD remoto. Recomendamos que nesses cenários você use o backup em nível de convidado usando o MABS e instale um agente na máquina virtual para fazer backup dos dados.

## <a name="linux"></a>Linux

| **Carga de trabalho** | **Versão**                               | **Instalação do Servidor de Backup do Azure**                      | **Servidor de Backup do Azure com suporte** | **Proteção e recuperação**                                 |
| ------------ | ----------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Linux        | Linux em execução como [Hyper-V](back-up-hyper-v-virtual-machines-mabs.md) ou convidado do [VMware](backup-azure-backup-server-vmware.md) | Servidor físico, VM Hyper-V local, VM do Windows no VMware | UR1 V3                             | O Hyper-V deve estar em execução no Windows Server 2012 R2, no Windows Server 2016 ou no Windows Server 2019. Proteger: máquina virtual inteira   <br><br>   Recupera: a máquina virtual inteira   <br><br>    Somente os instantâneos consistentes de arquivos têm suporte.    <br><br>   Para obter uma lista completa de distribuições e versões do Linux com suporte, consulte o artigo [Linux em distribuições endossadas pelo Azure](../virtual-machines/linux/endorsed-distros.md). |

## <a name="azure-expressroute-support"></a>Suporte do Azure ExpressRoute

Você pode fazer backup dos dados por meio do Azure ExpressRoute com o emparelhamento público (disponível para circuitos antigos) e o emparelhamento da Microsoft. Não há suporte para backup em emparelhamento privado.

Com o emparelhamento público: Verifique o acesso aos seguintes domínios/endereços:

* URLs
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* Endereços IP
  * 20.190.128.0/18
  * 40.126.0.0/18

Com o emparelhamento da Microsoft, selecione os seguintes serviços/regiões e os valores de comunidade relevantes:

* Azure Active Directory (12076:5060)
* Região Microsoft Azure (de acordo com o local do cofre dos serviços de recuperação)
* Armazenamento do Azure (de acordo com o local do cofre dos serviços de recuperação)

Para obter mais informações, consulte os [requisitos de roteamento do ExpressRoute](../expressroute/expressroute-routing.md).

>[!NOTE]
>O emparelhamento público foi preterido para novos circuitos.

## <a name="cluster-support"></a>Suporte do cluster

O Servidor de Backup do Azure pode proteger dados nos seguintes aplicativos em cluster:

* Servidores de arquivos

* SQL Server

* Hyper-V – se você proteger um cluster Hyper-V usando o agente de proteção MABS expandido, não poderá adicionar proteção secundária para as cargas de trabalho protegidas do Hyper-V.

* Exchange Server – o Servidor de Backup do Azure pode proteger clusters de discos não compartilhados para as versões com suporte do Exchange Server (replicação contínua de cluster) e também pode proteger o Exchange Server configurado para replicação contínua local.

* SQL Server – o Servidor de Backup do Azure não oferece suporte a backup de bancos de dados do SQL Server hospedados em volumes compartilhados de cluster (CSVs).

>[!NOTE]
>O MABS só dá suporte à proteção de máquinas virtuais do Hyper-V em volumes compartilhados do cluster (CSVs). Não há suporte para a proteção de outras cargas de trabalho hospedadas em CSVs.

Servidor de Backup do Azure pode proteger as cargas de trabalho de cluster que estão localizadas no mesmo domínio que o servidor MABS e em um domínio filho ou confiável. Se você deseja proteger fontes de dados em grupos de trabalho ou domínios não confiáveis, use NTLM ou autenticação de certificados para um único servidor ou autenticação de certificados apenas para um cluster.

## <a name="data-protection-issues"></a>Problemas de proteção de dados

* O MABS não pode fazer backup de VMs usando unidades compartilhadas (que podem ser anexadas a outras VMs), já que o gravador VSS do Hyper-V não pode fazer backup de volumes que são submetidos a backup por VHDs compartilhados.

* Ao proteger uma pasta compartilhada, o caminho para ela inclui o caminho lógico no volume. Se a pasta compartilhada for movida, a proteção falhará. Caso precise mover uma pasta compartilhada protegida, remova-a do grupo de proteção e adicione-a à proteção após a movimentação.  Além disso, se você alterar o caminho de uma fonte de dados protegida em um volume que usa o sistema de arquivos com criptografia (EFS) e o novo caminho de arquivo exceder 5120 caracteres, a proteção de dados falhará.

* Não é possível alterar o domínio de um computador protegido e continuar a proteção sem interrupções. Além disso, você não pode alterar o domínio de um computador protegido e associar as réplicas e os pontos de recuperação existentes ao computador quando ele é protegido novamente. Se você precisar alterar o domínio de um computador protegido, remova primeiro as fontes de dados no computador da proteção. Em seguida, proteja a fonte de dados no computador depois que ele tiver um novo domínio.

* Não é possível alterar o nome de um computador protegido e continuar a proteção sem interrupções. Além disso, você não pode alterar o nome de um computador protegido e associar as réplicas e os pontos de recuperação existentes ao computador quando ele é protegido novamente. Se você precisar alterar o nome de um computador protegido, remova primeiro as fontes de dados no computador da proteção. Em seguida, proteja a fonte de dados no computador depois que ele tiver um novo nome.

* O MABS identifica automaticamente o fuso horário de um computador protegido durante a instalação do agente de proteção. Se um computador protegido for movido para um fuso horário diferente depois que a proteção estiver configurada, certifique-se de alterar o horário do computador no Painel de controle. Em seguida, atualize o fuso horário no banco de dados MABS.

* O MABS pode proteger cargas de trabalho no mesmo domínio que o servidor MABS ou em domínios confiáveis e filho. Você também pode proteger as seguintes cargas de trabalho em grupos de trabalho e domínios não confiáveis usando a autenticação NTLM ou de certificado:

  * SQL Server
  * Servidor de Arquivos
  * Hyper-V

  Essas cargas de trabalho podem ser executadas em um único servidor ou em uma configuração de cluster. Para proteger uma carga de trabalho que não esteja em um domínio confiável, confira [preparar computadores em grupos e domínios não confiáveis](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm) para obter detalhes exatos sobre o que tem suporte e qual autenticação é necessária.

## <a name="unsupported-data-types"></a>Tipos de dados sem suporte

O MABS não dá suporte à proteção dos seguintes tipos de dados:

* Links físicos

* Pontos de nova análise, incluindo links DFS e pontos de junção

* Metadados do ponto de montagem — Um grupo de proteção pode conter dados com pontos de montagem. Neste caso, o DPM protegerá o volume montado que é o destino do ponto de montagem, mas não protegerá os metadados do ponto de montagem. Ao recuperar os dados que contenham pontos de montagem, você precisará recriar manualmente sua hierarquia de ponto de montagem.

* Dados em volumes montados dentro de volumes montados

* Lixeira

* Arquivos de paginação

* Pasta Informações de Volume do Sistema. Para proteger as informações do sistema de um computador, você precisará selecionar o estado do sistema do computador como o membro do grupo de proteção.

* Volumes não NTFS

* Arquivos que contêm links físicos ou links simbólicos do Windows Vista.

* Dados em compartilhamentos de arquivos que hospedam UPDs (discos de perfil do usuário)

* Arquivos com qualquer uma das seguintes combinações de atributos:

  * Criptografia e nova análise

  * Criptografia e SIS (Armazenamento de Instância Única)

  * Criptografia e diferenciação de maiúsculas e minúsculas

  * Criptografia e esparso

  * Diferenciação de maiúsculas e minúsculas e SIS

  * Compactação e SIS

## <a name="next-steps"></a>Próximas etapas

* [Matriz de suporte para backup com o Backup do Microsoft Azure Server ou o System Center DPM](backup-support-matrix-mabs-dpm.md)
