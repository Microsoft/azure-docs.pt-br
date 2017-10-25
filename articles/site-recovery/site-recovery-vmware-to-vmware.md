---
title: "Replicar VMs ou servidores físicos do VMware para outro site (Portal Clássico do Azure) | Microsoft Docs"
description: "Use este artigo para replicar máquinas virtuais VMware ou servidores físicos Windows/Linux para um site secundário com o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: jwhit
editor: 
ms.assetid: b2cba944-d3b4-473c-8d97-9945c7eabf63
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: nisoneji
ms.openlocfilehash: 01a6f35fe61290f8c7275c34273d66956a53d3f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site-in-the-classic-azure-portal"></a>Replicar máquinas virtuais locais ou servidores físicos do VMware em um site secundário no Portal Clássico do Azure

## <a name="overview"></a>Visão geral
O InMage Scout no Azure Site Recovery fornece replicação em tempo real entre os sites do VMWare no local. O InMage Scout está incluído nas assinaturas para o serviço Azure Site Recovery. 

## <a name="prerequisites"></a>Pré-requisitos
**Conta do Azure**: você precisará de uma conta do [Microsoft Azure](https://azure.microsoft.com/) . Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre os preços da Recuperação de Site.

## <a name="step-1-create-a-vault"></a>Etapa 1: criar um cofre
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique em Novo > Gerenciamento > Backup e Site Recovery (OMS). Como alternativa, é possível clicar em Procurar > Cofre de Serviços de Recuperação > Adicionar.
3. Em **Nome** , especifique um nome amigável para identificar o cofre. Se você tiver mais de uma assinatura, selecione uma delas.
4. No **Grupo de recursos**, crie um novo grupo de recursos ou selecione um existente. Especifique uma região do Azure para preencher os campos obrigatórios.
5. Em **Local**, selecione a região geográfica para o cofre. Para verificar as regiões com suporte, confira [Preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Se você deseja acessar rapidamente o cofre pelo Painel, clique em Fixar no painel e, em seguida, em Criar.
7. O novo cofre será exibido no Painel > Todos os recursos, e na folha de cofres Serviços de Recuperação principal.

## <a name="step-2-configure-the-vault-and-download-inmage-scout-components"></a>Etapa 2: Configurar o cofre e baixar componentes InMage Scout
1. Na folha Cofres dos Serviços de Recuperação, selecione seu cofre e clique em Configurações.
2. Em **Configurações** > **Introdução**, clique em **Site Recovery** > Etapa 1: **Preparar a Infraestrutura** > **Meta de proteção**.
3. Em **Meta de proteção**, selecione Para o site de recuperação e selecione Sim, com o Hipervisor VMware vSphere. Em seguida, clique em OK.
4. Em **Configuração do Scout**, clique em download para baixar o software InMage Scout 8.0.1 GA e a chave de registro. Os arquivos de instalação para todos os componentes necessários estão no arquivo zip baixado.

## <a name="step-3-install-component-updates"></a>Etapa 3: Instalar atualizações de componentes
Leia sobre as últimas [atualizações](#updates). Você instalará os arquivos de atualização nos servidores na seguinte ordem:

1. Servidor RX se houver um
2. Servidores de configuração
3. Servidores de processo
4. Servidores de destino mestre
5. Servidores vContinuum
6. Servidor de origem (Windows e Linux Server)

Instale as atualizações da seguinte maneira:

1. Baixe o arquivo .zip de [atualização](https://aka.ms/asr-scout-update5). Este arquivo zip contém os seguintes arquivos:

   * RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
   * CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe
   * UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
   * UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
   * vCon_Windows_8.0.5.0_GA_Update_5_11525767_20Apr17.exe
   * UA update4 bits para RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
2. Extraia os arquivos .zip.<br>
3. **Para o servidor RX**: copie **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** para o servidor RX e o extraia. Na pasta extraída, execute **/Install**.<br>
4. **Para o servidor de configuração/servidor de processo**: copie **CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe** para o servidor de configuração e para o servidor de processo. Clique duas vezes para executá-lo.<br>
5. **Para o servidor de destino mestre do Windows**: para atualizar o agente unificado, copie **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** para o servidor de destino mestre. Clique duas vezes nele para executá-lo. Observe que o agente unificado também se aplica ao servidor de origem se a origem não for atualizada até a Atualização4. Você deve instalá-lo no servidor de origem também, conforme mencionado nesta lista.<br>
6. **Para o servidor vContinuum**: copie **vCon_Windows_8.0.5.0_GA_Update_5_11525767_20Apr17.exe** para o servidor vContinuum.  Verifique se que você fechou o assistente vContinuum. Clique duas vezes no arquivo para executá-lo.<br>
7. **Para o servidor de destino mestre do Linux**: para atualizar o agente unificado, copie **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** para o servidor de destino mestre e o extraia. Na pasta extraída, execute **/Install**.<br>
8. **Para o servidor de origem do Windows**: você não precisa instalar o agente de Atualização 5 na origem se a origem já está na atualização 4. Se for inferior à atualização 4, aplique o agente de atualização 5.
Para atualizar o agente unificado, copie **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** para o servidor de origem. Clique duas vezes nele para executá-lo. <br>
9. **Para o servidor de origem do Linux**: para atualizar o agente unificado, copie a versão correspondente do arquivo UA para o servidor do Linux e o extraia. Na pasta extraída, execute **/Install**.  Exemplo: para o servidor RHEL 6.7 de 64 bits, copie **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** para o servidor e o extraia. Na pasta extraída, execute **/Install**.

## <a name="step-4-set-up-replication"></a>Etapa 4: Configurar a replicação
1. Configure a replicação entre os sites do VMware de origem e de destino.
2. Para obter orientação, use a documentação do InMage Scout baixada com o produto. Como alternativa, você pode acessar a documentação da seguinte maneira:

   * [Notas de versão](https://aka.ms/asr-scout-release-notes)
   * [Matriz de compatibilidade](https://aka.ms/asr-scout-cm)
   * [Guia do usuário](https://aka.ms/asr-scout-user-guide)
   * [Guia do usuário do RX](https://aka.ms/asr-scout-rx-user-guide)
   * [Guia de instalação rápida](https://aka.ms/asr-scout-quick-install-guide)

## <a name="updates"></a>Atualizações
### <a name="azure-site-recovery-scout-801-update-5"></a>Azure Site Recovery Scout 8.0.1 Atualização 5
A Atualização 5 do Scout é uma atualização cumulativa. Ela tem todas as correções da atualização 1 até a atualização 4, além das novas correções de bug e melhorias apresentadas a seguir.
Correções que são adicionadas do ASR Scout atualização 4 para atualização 5 são específicas para componentes de Destino Mestre e vContinuum. Se todos os seus servidores de origem, de destino mestre, de configuração, de processo e RX já estão no ASR Scout atualização 4, você precisa aplicar a atualização 5 apenas no servidor de destino mestre. 

**Novo suporte de plataforma**
* SUSE Linux Enterprise Server 11 Service Pack 4 (SP4)

> [!NOTE]
> O SLES 11 SP4 64 bits **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** é empacotado com o pacote base **InMage_Scout_Standard_8.0.1 GA.zip** do Scout GA. Baixe o pacote Scout GA do portal conforme mencionado na [etapa 1](#step-1-create-a-vault).
>

**Correção de bugs e melhorias**

* Aumentar a confiabilidade de suporte de Cluster do Windows
    * Corrigido – Algumas vezes, alguns dos discos de cluster do MSCS P2V tornam-se brutos após a recuperação
    * Corrigido – A recuperação de cluster do MSCS P2V falha devido à incompatibilidade de ordem do disco
    * Corrigido – A operação de adicionar discos do cluster do MSCS falha com incompatibilidade de tamanho de disco
    * Corrigido – A verificação de preparação de mapeamento de cluster do MSCS de origem com LUNs de RDM falha na verificação de tamanho
    * Corrigido – A proteção de cluster de nó único falha devido a um problema de incompatibilidade de SCSI 
    * Corrigido – A nova proteção do servidor de cluster do Windows P2V falha se há discos de cluster de destino presentes. 
    
* Durante a proteção de failback, se o MT selecionado não está no mesmo servidor ESXi que o computador de origem protegido (durante a proteção antecipada), o vContinuum recebe o MT errado durante a recuperação de failback e, subsequentemente, a operação de recuperação falha.

> [!NOTE]
> 
> * As correções de cluster P2V acima são aplicáveis somente a esses clusters do MSCS físicos protegidos recentemente com ASR Scout update5. Para se beneficiar das correções de cluster no cluster do MSCS P2V já protegido com as atualizações mais antigas, você precisa seguir as etapas de atualização que são mencionadas na seção 12 das [Notas de Versão do ASR Scout](https://aka.ms/asr-scout-release-notes), Atualizar cluster do MSCS P2V protegido para Scout Update5.
> 
> * Proteger novamente do cluster do MSCS físico pode reutilizar discos de destino existentes somente se, no momento em que se vai proteger novamente, o mesmo conjunto de discos estão ativos em cada um dos nós do cluster em que esses discos estavam quando protegidos inicialmente. Se não, em seguida, há etapas manuais conforme mencionado na seção 12 das [Notas de Versão do ASR Scout](https://aka.ms/asr-scout-release-notes) para mover os discos no lado de destino para o caminho de armazenamento de dados correto para reutilizá-los durante a proteção novamente. Se você proteger novamente o cluster do MSCS no modo P2V sem seguir as etapas de atualização, ele criará um novo disco no servidor ESXi de destino. Você precisa excluir manualmente os discos antigos do armazenamento de dados.
> 
> * Sempre que o SLES11 ou SLES11 de origem com qualquer servidor de service pack é reinicializado normalmente, deve-se marcar manualmente os pares de replicação de disco **raiz** para sincronizar novamente, pois ele não será notificado na interface do usuário do CX. Se você não marcar o disco raiz para ressincronização, talvez você veja problemas de DI (integridade de dados).
> 

### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 Atualização 4
Scout Atualização 4 é uma atualização cumulativa. Ele tem todas as correções de update1 até update3 e as seguintes novas correções de bug e melhorias.

**Novo suporte de plataforma**

* Foi adicionado suporte para o vCenter/vSphere 6.0, 6.1 e 6.2
* Foi adicionado suporte para os seguintes sistemas operacionais Linux
  * Red Hat Enterprise Linux (RHEL)7.0, 7.1 e 7.2
  * CentOS 7.0, 7.1 e 7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> O RHEL/CentOS 7 64 bits **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** é empacotado com o pacote **InMage_Scout_Standard_8.0.1 GA.zip** do Scout GA. Baixe o pacote Scout GA do portal conforme mencionado na [etapa 1](#step-1-create-a-vault).
>
>

**Correção de bugs e melhorias**

* Melhor tratamento de desligamento para os seguintes SOs Linux e clones para evitar problemas de ressincronização indesejados.
  * Red Hat Enterprise Linux (RHEL) 6.x
  * Oracle Linux (OL) 6.x
* No Linux, as permissões completas de acesso da pasta no diretório de instalação do agente unificado agora estão restritas apenas ao usuário local.
* No Windows, o problema de tempo limite ao emitir o indicador de consistência comum distribuído em aplicativos distribuídos muito carregados como clusters SQL e SharePoint.
* Adicionado log relacionado à correção no instalador base do CX.
* O link de download do VMware vCLI 6.0 foi adicionado ao instalador base de destino mestre do Windows.
* Adicionadas mais verificações e logs para alterações de configurações de rede durante o failover e análise de DR.
* Em algum momento, as informações de retenção não são relatadas ao CX.  
* Para o cluster físico, a operação de redimensionamento do volume por meio do assistente vContinuum falhará quando tiver ocorrido redução de volume.
* Falha na proteção do cluster com o erro "Falha ao localizar a assinatura de disco" quando o disco de cluster é o disco PRDM.
* falha do servidor de transporte de cxps devido à exceção fora do intervalo.
* O nome do servidor e as colunas IP agora são redimensionáveis na página de instalação por push do assistente vContinuum.
* Melhorias de API do RX
  * Fornece os cinco pontos de consistência comuns disponíveis mais recentes (apenas marcas garantidas).
  * Fornece detalhes de espaço livre e capacidade para todos os dispositivos protegidos.
  * Fornece o estado do driver Scout no servidor de origem.

> [!NOTE]
> * O pacote base **InMage_Scout_Standard_8.0.1_GA.zip** agora atualizou o instalador base do CX **InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe** e o instalador base de Destino Mestre do Windows **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**. Para toda nova instalação, use os novos bits de GA de Destino Mestre do Windows e do CX.
> * A Atualização 4 pode ser aplicada diretamente no GA 8.0.1.
> * As atualizações de servidor de configuração e RX não podem ser revertidas depois de serem aplicadas no sistema.
>
>

### <a name="azure-site-recovery-scout-801-update-3"></a>Atualização 3 do Azure Site Recovery Scout 8.0.1
A Atualização 3 inclui as seguintes correções de bugs e aprimoramentos:

* O servidor de configuração e o RX não serão registrados no cofre de Recuperação de Site quando estiverem por trás do proxy.
* O número de horas que o RPO (objetivo de ponto de recuperação) não é atendido não é atualizado no relatório de integridade.
* O servidor de configuração não é sincronizado com RX quando os detalhes da rede ou detalhes de hardware ESX contêm caracteres UTF-8.
* Controladores de domínio do Windows Server 2008 R2 não são inicializados após a recuperação.
* A sincronização offline não está funcionando conforme o esperado.
* Após o failover da VM (máquina virtual), a exclusão de par de replicação fica bloqueada na IU de CX por um longo tempo, e os usuários podem concluir o failback nem retomar a operação.
* Operações de instantâneo gerais que são feitas pelo trabalho de consistência foram otimizadas para ajudar a reduzir desconexões de aplicativo, como clientes SQL.
* O desempenho da ferramenta de consistência (VACP.exe) foi aprimorado com a redução do uso de memória necessário para a criação de instantâneos no Windows.
* O serviço de instalação por push falha quando a senha tem mais de 16 caracteres.
* vContinuum não verifica e solicita novas credenciais vCenter quando as credenciais são alteradas.
* No Linux, o gerenciador de cache de destino mestre (cachemgr) não está baixando arquivos do servidor de processo, o que resulta na limitação do par de replicação.
* Quando a ordem de disco de MSCS (cluster de failover físico) não é a mesma em todos os nós, a replicação não é definida para alguns dos volumes de cluster.
  <br/>Observe que o cluster deve ser protegido para tirar proveito dessa correção.  
* A Funcionalidade SMTP não está funcionando conforme o esperado depois que o RX é atualizado de Scout 7.1 para Scout 8.0.1.
* Foram adicionadas mais estatísticas no log para que a operação de reversão acompanhe o tempo gasto para concluí-la.
* Foi adicionado suporte para sistemas operacionais Linux no servidor de origem:
  * Red Hat Enterprise Linux (RHEL) 6 atualização 7
  * CentOS 6 Atualização 7
* As interfaces do usuário de RX e CX agora podem mostrar a notificação para o par que entra no modo bitmap.
* As seguintes correções de segurança foram adicionadas no RX:

| **Descrição do problema** | **Procedimentos de implementação** |
| --- | --- |
| Autorização ignorada por meio de violação de parâmetros |Acesso restringido a usuários não aplicáveis. |
| Solicitação entre sites forjada |Conceito token-página implementado e sendo gerado aleatoriamente para cada página. <br/>Com isso, você verá: <li> Há apenas uma instância de entrada única para o mesmo usuário.</li><li>A atualização da página não funciona – ela será redirecionada para o painel.</li> |
| Carregamento de arquivo mal-intencionado |Arquivos restritos a certas extensões. Extensões permitidas: 7z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, mid, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf, sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml e zip. |
| Script persistente entre sites |Validações de entrada adicionadas. |

> [!NOTE]
> * Todas as atualizações de Recuperação de Site são cumulativas. A atualização 3 tem todas as correções da Atualização 1 e da Atualização 2. A Atualização 3 pode ser aplicada diretamente a 8.0.1 GA.
> * As atualizações de servidor de configuração e RX não podem ser revertidas depois de serem aplicadas no sistema.
>
>

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 Atualização 2 (atualização de 3 de dezembro de 2015)
As correções na Atualização 2 incluem:

* **Servidor de configuração**: corrige um problema que impediu que o recurso de medição gratuito por 31 dias funcionasse conforme o esperado quando o servidor de configuração foi registrado na Recuperação de Site.
* **Agente unificado**: corrige um problema na atualização 1 que fazia com que a atualização que fosse instalada no servidor de destino mestre quando ele era atualizado da versão 8.0 para 8.0.1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 Atualização 1
A atualização 1 inclui as seguintes correções de bugs e novos recursos:

* 31 dias de proteção gratuita por instância de servidor. Isso lhe permite testar a funcionalidade ou configurar uma verificação de conceito.
  * Todas as operações no servidor, incluindo failover e failback, são gratuitas pelos primeiros 31 dias a partir da hora em que um servidor é protegido pela primeira vez com o Scout de Recuperação de Site.
  * A partir do dia 32, todos os servidores protegidos pagarão a taxa padrão de instância pela proteção do Azure Site Recovery de um site de propriedade do cliente.
  * A qualquer momento, todos os servidores protegidos que atualmente estão sendo cobrados estão disponíveis na página Painel do cofre do Azure Site Recovery.
* Foi adicionado suporte para vCLI (Interface de Linha de Comando de vSphere) em 5.5 Atualização 2.
* Suporte adicionado para sistemas operacionais Linux no servidor de origem:
  * RHEL 6 Update 6
  * RHEL 5 Update 11
  * CentOS 6 Update 6
  * CentOS 5 Update 11
* Correções de bugs para resolver estes problemas:
  * Falha de registro do cofre para o servidor de configuração ou RX.
  * Os volumes de cluster não aparecem conforme o esperado quando máquinas virtuais clusterizadas são protegidas ao serem retomadas.
  * O failback falha quando o servidor de destino mestre é hospedado em outro servidor ESXi das máquinas virtuais locais de produção.
  * Permissões de arquivo de configuração são alteradas quando você atualiza para 8.0.1, o que afeta a proteção e as operações.
  * O limite de ressincronização não é imposto conforme o esperado, o que resulta em um comportamento de replicação inconsistente.
  * As configurações de RPO não aparecem corretamente na interface de configuração do servidor. O valor de dados não compactados mostra incorretamente o valor compactado.
  * A operação Remover não exclui conforme o esperado no assistente vContinuum e replicação não é excluída da interface do servidor de configuração.
  * No assistente vContinuum, o disco é desmarcado automaticamente quando você clica em **Detalhes** na exibição do disco durante a proteção de máquinas virtuais MSCS.
  * Durante o cenário de P2V (físico para virtual), serviços HP necessários, como CIMnotify e CqMgHost, não são movidos para manual na recuperação de máquina virtual. Isso resulta em tempo de inicialização adicional.
  * A máquina virtual Linux protegida falha quando há mais de 26 discos no servidor de destino mestre.

## <a name="next-steps"></a>Próximas etapas
Publique qualquer pergunta que você desejar no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).
