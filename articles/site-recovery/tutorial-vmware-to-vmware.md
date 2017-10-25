---
title: "Configurar a recuperação de desastre de VMs VMware ou de servidores físicos para um site secundário com o Azure Site Recovery | Microsoft Docs"
description: "Saiba como configurar a recuperação de desastre de VMs VMware ou de servidores físicos Windows e Linux para um site secundário com o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: gauarvd
editor: 
ms.assetid: 68616d15-398c-4f40-8323-17b6ae1e65c0
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: raynew
ms.openlocfilehash: b182c00ac9a6956d07dece621d03c84788442085
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2017
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Configurar a recuperação de desastre de máquinas virtuais VMware locais ou de servidores físicos para um site secundário

O InMage Scout no [Azure Site Recovery](site-recovery-overview.md) fornece replicação em tempo real entre os sites locais do VMware. O InMage Scout está incluído nas assinaturas para o serviço Azure Site Recovery. 


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- [Examine](site-recovery-support-matrix-to-sec-site.md) os requisitos de suporte de todos os componentes.
- Verifique se as máquinas que você deseja replicar estão em conformidade com o [suporte ao computador replicado](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions).


## <a name="create-a-vault"></a>Criar um cofre

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="choose-a-protection-goal"></a>Escolher um objetivo de proteção

Selecione o que será replicado e o local em que será replicado.

1. Clique em **Site Recovery** > **Preparar a infraestrutura** > **Meta de proteção**.
2. Selecione **Para site de recuperação** > **Sim, com o VMware vSphere Hypervisor**. Em seguida, clique em **OK**.
3. Em **Instalação do Scout**, baixe o software do InMage Scout 8.0.1 GA e a chave de registro. Os arquivos de instalação de todos os componentes estão incluídos no arquivo .zip baixado.

## <a name="install-component-updates"></a>Instalar atualizações de componentes

 Examine e instale as [atualizações](#updates) mais recentes. As atualizações devem ser instaladas nos servidores na seguinte ordem:

1. Servidor RX (caso se aplique)
2. Servidores de configuração
3. Servidores de processo
4. Servidores de destino mestre
5. Servidores vContinuum
6. Servidor de origem (servidores Linux e Windows)

Instale as atualizações da seguinte maneira:

> [!NOTE]
>A versão de atualização do arquivo de todos os componentes Scout pode não ser a mesma no arquivo .zip da atualização. A versão mais antiga indica que não há nenhuma alteração no componente desde a atualização anterior até essa atualização.

Baixe o arquivo .zip de [atualização](https://aka.ms/asr-scout-update6). O arquivo contém os seguintes componentes: 
  - RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
  - CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
  - UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
  - UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
  - vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
  - UA update4 bits para RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
1. Extraia os arquivos .zip.
2. **Servidor RX**: copie **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** para o servidor RX e extraia-o. Na pasta extraída, execute **/Install**.
3. **Servidor de configuração e servidor de processo**: copie **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** para o servidor de configuração e para o servidor de processo. Clique duas vezes para executá-lo.<br>
4. **Servidor de destino mestre do Windows**: para atualizar o agente unificado, copie **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** para o servidor. Clique duas vezes nele para executá-lo. A mesma atualização de agente unificado também se aplica ao servidor de origem. Se o de origem ainda não foi atualizado para a Atualização 4, você deve atualizar o agente unificado.
  A atualização não precisa ser aplicada ao destino mestre preparado com **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe**, pois esse é o novo instalador GA com todas as alterações mais recentes.
5. **Servidor vContinuum**: copie **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe** para o servidor.  Verifique se que você fechou o assistente vContinuum. Clique duas vezes no arquivo para executá-lo.
    A atualização não precisa ser aplicada ao destino mestre preparado com **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe**, pois esse é o novo instalador GA com todas as alterações mais recentes.
6. **Servidor Linux de destino mestre**: para atualizar o agente unificado, copie **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** para o servidor de destino mestre e extraia-o. Na pasta extraída, execute **/Install**.
7. **Servidor de origem do Windows**: para atualizar o agente unificado, copie **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** para o servidor de origem. Clique duas vezes no arquivo para executá-lo. 
    Você não precisará instalar o agente de Atualização 5 no servidor de origem, se ele já tiver sido atualizado para a Atualização 4, ou se o agente de origem tiver sido instalado com o instalador base mais recente **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**.
8. **Servidor Linux de origem**: para atualizar o agente unificado, copie a versão correspondente do arquivo de agente unificado para o servidor Linux e extraia-o. Na pasta extraída, execute **/Install**.  Exemplo: para o servidor RHEL 6.7 de 64 bits, copie **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** para o servidor e extraia-o. Na pasta extraída, execute **/Install**.

## <a name="enable-replication"></a>Habilitar a replicação

1. Configure a replicação entre os sites do VMware de origem e de destino.
2. Para obter orientação, use a documentação do InMage Scout baixada com o produto. Como alternativa, você pode acessar a documentação da seguinte maneira:

   * [Notas de versão](https://aka.ms/asr-scout-release-notes)
   * [Matriz de compatibilidade](https://aka.ms/asr-scout-cm)
   * [Guia do usuário](https://aka.ms/asr-scout-user-guide)
   * [Guia do usuário do RX](https://aka.ms/asr-scout-rx-user-guide)
   * [Guia de instalação rápida](https://aka.ms/asr-scout-quick-install-guide)

## <a name="updates"></a>Atualizações

### <a name="site-recovery-scout-801-update-6"></a>Site Recovery Scout 8.0.1 Atualização 6 
Atualização: 6 de outubro de 2017

A Atualização 6 do Scout é uma atualização cumulativa. Ela contém todas as correções desde a Atualização 1 até a Atualização 5 e as novas correções e aprimoramentos descritos abaixo. 

#### <a name="new-platform-support"></a>Novo suporte à plataforma
* Foi adicionado suporte para o Windows Server 2016 de origem
* Foi adicionado suporte para os seguintes sistemas operacionais Linux:
    - Red Hat Enterprise Linux (RHEL) 6.9
    - CentOS 6.9
    - Oracle Linux 5.11
    - Oracle Linux 6.8
* Foi adicionado suporte para o VMware Center 6.5

> [!NOTE]
> * O instalador de UA (Agente Unificado) Base para Windows foi atualizado para dar suporte ao Windows Server 2016. O novo instalador **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** vem com o pacote base da GA Scout (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). O mesmo instalador será usado para todas as versões com suporte do Windows. 
> * O instalador base Windows vContinuum & Destino Mestre foi atualizado para dar suporte ao Windows Server 2016. O novo instalador **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** vem com o pacote base da GA Scout (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). O mesmo instalador será usado para implantar o Destino Mestre do Windows 2016 e o Destino Mestre do Windows 2012 R2.
> * Baixe o pacote GA do portal, conforme é descrito em [criar um cofre](#create-a-vault).
>

#### <a name="bug-fixes-and-enhancements"></a>Correções de bug e melhorias
- Falha de proteção de failback para a VM do Linux com lista de discos a serem replicados vazia no final da configuração.


### <a name="site-recovery-scout-801-update-5"></a>Site Recovery Scout 8.0.1 Atualização 5
A Atualização 5 do Scout é uma atualização cumulativa. Ela contém todas as correções desde a Atualização 1 até a Atualização 4 e as novas correções descritas abaixo.
- As correções da Atualização 4 até a Atualização 5 do Site Recovery Scout são específicas para os componentes de destino mestre e do vContinuum.
- Se os servidores de origem, os servidores de destino mestre, de configuração, de processo e RX já estiverem executando a Atualização 4, aplique-a somente ao servidor de destino mestre. 

#### <a name="new-platform-support"></a>Novo suporte à plataforma
* SUSE Linux Enterprise Server 11 Service Pack 4 (SP4)
* O SLES 11 SP4 de 64 bits  **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** é empacotado com o pacote base do Scout GA (**InMage_Scout_Standard_8.0.1 GA.zip**). Baixe o pacote GA do portal, conforme é descrito em [criar um cofre](#create-a-vault).


#### <a name="bug-fixes-and-enhancements"></a>Correções de bug e melhorias

* Correções para aumentar a confiabilidade do suporte de cluster do Windows:
    * Corrigido – Alguns dos discos de cluster do MSCS P2V tornam-se brutos após a recuperação.
    * Corrigido – A recuperação de cluster do MSCS P2V falha devido à incompatibilidade de ordem do disco.
    * Corrigido – A operação de adicionar discos do cluster do MSCS falha com um erro de incompatibilidade de tamanho de disco.
    * Corrigido – A verificação de preparação do cluster do MSCS de origem com mapeamento de LUNs de RDM falha na verificação de tamanho.
    * Corrigido – A proteção de cluster de nó único falha devido a um problema de incompatibilidade de SCSI. 
    * Corrigido – A nova proteção do servidor de cluster do Windows P2V falha quando há discos de cluster de destino presentes. 
    
* Corrigido: durante a proteção de failback, se o servidor de destino mestre selecionado não está no mesmo servidor ESXi que o computador de origem protegido (durante a proteção antecipada), o vContinuum recebe o servidor de destino mestre errado durante a recuperação de failback e a operação de recuperação falha.

#### <a name="issues"></a>Problemas
* As correções do cluster P2V são aplicáveis somente a clusters físicos do MSCS recém-protegidos com o Site Recovery Scout Atualização 5. Para instalar as correções de cluster nos clusters do MSCS P2V protegidos com as atualizações mais antigas, siga as etapas de atualização mencionadas na seção 12 das [Site Recovery Scout Release Notes](https://aka.ms/asr-scout-release-notes) (Notas de versão do Site Recovery Scout).
* Se no momento da nova proteção, o mesmo conjunto de discos estiver ativo em cada um dos nós do cluster como estavam quando foram protegidos inicialmente, a nova proteção de um cluster do MSCS físico só poderá reutilizar os discos de destino existentes. Se não, use as etapas manuais na seção 12 das [Site Recovery Scout Release Notes](https://aka.ms/asr-scout-release-notes) (Notas de versão do Site Recovery Scout), para mover os discos do lado do destino para o caminho do armazenamento de dados correto, para reutilização durante a nova proteção. Se você proteger novamente o cluster do MSCS no modo P2V sem seguir as etapas de atualização, será criado um novo disco no servidor ESXi de destino. Você precisará excluir manualmente os discos antigos do armazenamento de dados.
* Quando um servidor SLES11 de origem ou SLES11 (com qualquer service pack) for reiniciado normalmente, marque manualmente os pares de replicação de disco **raiz** para ressincronização. Não há nenhuma notificação na interface do CX. Se você não marcar o disco raiz para ressincronização, poderão ocorrer problemas de integridade de dados.


### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 Atualização 4
Scout Atualização 4 é uma atualização cumulativa. Ela inclui todas as correções desde a Atualização 1 até a Atualização 3 e as novas correções descritas abaixo.

#### <a name="new-platform-support"></a>Novo suporte à plataforma

* Foi adicionado suporte para o vCenter/vSphere 6.0, 6.1 e 6.2
* Foi adicionado suporte para estes sistemas operacionais de Linux:
  * Red Hat Enterprise Linux (RHEL) 7.0, 7.1 e 7.2
  * CentOS 7.0, 7.1 e 7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> O RHEL/CentOS 7 de 64 bits **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** é empacotado com o pacote **InMage_Scout_Standard_8.0.1 GA.zip** base do Scout GA. Baixe o pacote do Scout GA do portal, conforme é descrito em [criar um cofre](#create-a-vault).

#### <a name="bug-fixes-and-enhancements"></a>Correções de bug e melhorias

* Melhoria da manipulação de desligamento para os seguintes sistemas operacionais Linux e clones, para evitar problemas de ressincronização indesejados:
    * Red Hat Enterprise Linux (RHEL) 6.x
    * Oracle Linux (OL) 6.x
* Para o Linux, todas as permissões de acesso de pasta no diretório de instalação do agente unificado agora são restritas somente ao usuário local.
* No Windows, uma correção para um problema de tempo limite que ocorria ao emitir indicadores de consistência distribuídos comuns, em aplicativos distribuídos muito carregados, como clusters do SQL Server e do Share Point.
* Uma correção relacionada a log no instalador base do servidor de configuração.
* Um link de download para o VMware vCLI 6.0 foi adicionado ao instalador base de destino mestre do Windows.
* Logs e verificações adicionais foram adicionados, para alterações de configuração de rede durante as simulações de recuperação de desastre e failover.
* Uma correção para um problema que fazia com que as informações de retenção não fossem relatadas ao servidor de configuração.  
* Para clusters físicos, uma correção para um problema que causava falha do redimensionamento de volume no assistente do vContinuum, ao reduzir o volume de origem.
* Uma correção para um problema de proteção do cluster que falhava com o erro: "Falha ao localizar a assinatura do disco", quando o disco de cluster era um disco PRDM.
* Uma correção para uma falha de servidor de transporte cxps causado por uma exceção fora do intervalo.
* Os nome do servidor e as colunas de endereço IP agora são ajustáveis na página **Instalação por Push** do assistente do vContinuum.
* Melhorias da API RX:
  * Os cinco pontos de consistência comuns mais recentes disponíveis agora estão disponíveis (apenas marcas garantidas).
  * Os detalhes de espaço livre e de capacidade são exibidos para todos os dispositivos protegidos.
  * O estado do driver do Scout no servidor de origem está disponível.

#### <a name="issues"></a>Problemas

* O pacote base **InMage_Scout_Standard_8.0.1_GA.zip** tem:
    * Um instalador base do servidor de configuração atualizado (**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
    * Um instalador de base de destino mestre do Windows (**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**).
    * Para todas as novas instalações, use o novo servidor de configuração e os bits de GA do destino mestre do Windows.
* A Atualização 4 pode ser aplicada diretamente no GA 8.0.1.
* As atualizações do servidor de configuração e do RX não poderão ser revertidas depois que forem aplicadas.


### <a name="azure-site-recovery-scout-801-update-3"></a>Atualização 3 do Azure Site Recovery Scout 8.0.1

Todas as atualizações de Recuperação de Site são cumulativas. A Atualização 3 contém todas as correções da Atualização 1 e da Atualização 2. A Atualização 3 pode ser aplicada diretamente a 8.0.1 GA. As atualizações do servidor de configuração e do RX não poderão ser revertidas depois que forem aplicadas.

#### <a name="bug-fixes-and-enhancements"></a>Correções de bug e melhorias
A Atualização 3 corrige os problemas a seguir:

* O servidor de configuração e o RX não são registrados no cofre quando estão atrás do proxy.
* O número de horas em que o RPO (objetivo de ponto de recuperação) não foi atingido não é atualizado no relatório de integridade.
* O servidor de configuração não sincroniza com RX quando os detalhes de hardware do ESX ou os detalhes da rede contêm caracteres UTF-8.
* Os controladores de domínio do Windows Server 2008 R2 não iniciam após a recuperação.
* A sincronização offline não está funcionando conforme o esperado.
* Após o failover da VM, a exclusão do par de replicação não progride no console do servidor de configuração por um longo tempo. Os usuários não conseguem concluir o failback ou retomar as operações.
* As operações de instantâneo gerais que são feitas pelo trabalho de consistência foram otimizadas para ajudar a reduzir desconexões de aplicativo, como clientes do SQL Server.
* O desempenho da ferramenta de consistência (VACP.exe) melhorou. O uso de memória necessário para a criação de instantâneos no Windows foi reduzido.
* O serviço de instalação por push falha quando a senha tem mais de 16 caracteres.
* O vContinuum não verifica e solicita novas credenciais do vCenter quando as credenciais são modificadas.
* No Linux, o gerenciador de cache de destino mestre (cachemgr) não baixa arquivos do servidor de processo. Isso resulta na limitação do par de replicação.
* Quando a ordem de disco de MSCS (cluster de failover físico) não é a mesma em todos os nós, a replicação não é definida para alguns dos volumes de cluster. O cluster deve ser protegido novamente para usufruir dessa correção.  
* A funcionalidade de SMTP não funciona como esperado depois que o RX é atualizado do Scout 7.1 para o Scout 8.0.1.
* Foram adicionadas mais estatísticas no log para que a operação de reversão acompanhe o tempo gasto para concluí-la.
* Foi adicionado suporte para sistemas operacionais Linux no servidor de origem:
  * Red Hat Enterprise Linux (RHEL) 6 atualização 7
  * CentOS 6 Atualização 7
* Os consoles do servidor de configuração e do RX agora mostram notificações para o par que entra no modo de bitmap.
* As seguintes correções de segurança foram adicionadas no RX:
    * Autorização ignorada por meio de violação de parâmetros: acesso restrito a usuários não aplicável.
    * Falsificação de solicitação entre sites: o conceito de token de página foi implementado e é gerado aleatoriamente para todas as páginas. Isso significa que há apenas uma única instância de entrada única para o mesmo usuário e a atualização de página não funciona. Nessa caso, ela redireciona para o painel.
    * Upload de arquivo mal-intencionado: os arquivos são restritos a extensões específicas: z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, mid, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf, sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml e zip.
    * Script entre sites persistente: validações de entrada foram adicionadas.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 Atualização 2 (atualização de 3 de dezembro de 2015)

As correções na Atualização 2 incluem:

* **Servidor de configuração**: problemas que impediam que o recurso de medição gratuito por 31 dias funcionasse conforme o esperado quando o servidor de configuração era registrado no Site Recovery.
* **Agente unificado**: correção para um problema na Atualização 1 que fazia com que a atualização não fosse instalada no servidor de destino mestre durante a atualização da versão 8.0 para a 8.0.1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 Atualização 1
A atualização 1 inclui as seguintes correções de bugs e novos recursos:

* 31 dias de proteção gratuita por instância de servidor. Isso permite testar a funcionalidade ou definir uma prova de conceito.
* Todas as operações no servidor, inclusive failover e failback, são gratuitas nos primeiros 31 dias. O tempo começa quando um servidor é protegido pela primeira vez com o Site Recovery Scout. A partir do 32º dia, todos os servidores protegidos são cobrados na taxa padrão de instância pela proteção do Azure Site Recovery para um site de propriedade do cliente.
* O número de servidores protegidos que estão sendo cobrados no momento está sempre disponível no **Painel** no cofre.
* Foi adicionado suporte para vCLI (Interface de Linha de Comando do vSphere) na 5.5 Atualização 2.
* Foi adicionado suporte para estes sistemas operacionais Linux no servidor de origem:
    * RHEL 6 Update 6
    * RHEL 5 Update 11
    * CentOS 6 Update 6
    * CentOS 5 Update 11
* Correções de bugs para resolver estes problemas:
  * Falha no registro do cofre para o servidor de configuração ou o servidor RX.
  * Os volumes de cluster não aparecem conforme o esperado quando as VMs clusterizadas são protegidas novamente para que possam retomar.
  * O failback falha quando o servidor de destino mestre é hospedado em um outro servidor ESXi das VMs locais de produção.
  * As permissões de arquivo de configuração são alteradas quando você atualiza para 8.0.1. Esta alteração afeta a proteção e as operações.
  * O limite de ressincronização não é imposto conforme o esperado, causando um comportamento de replicação inconsistente.
  * As configurações de RPO não são exibidas corretamente no console do servidor de configuração. O valor de dados não compactados mostra incorretamente o valor compactado.
  * A operação de remoção não exclui conforme esperado no assistente do vContinuum e a replicação não é excluída no console do servidor de configuração.
  * No assistente do vContinuum, o disco é desmarcado automaticamente quando você clica em **Detalhes** na exibição do disco durante a proteção de VMs MSCS.
  * No cenário de P2V (físico para virtual), os serviços HP necessários (como CIMnotify e CqMgHost) não são movidos para manual na recuperação de VM. Esse problema resulta em mais tempo de inicialização.
  * Falha na proteção de VM do Linux quando há mais de 26 discos no servidor de destino mestre.

