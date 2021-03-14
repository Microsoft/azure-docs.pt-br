---
title: Cluster de failover do Windows Server na solução do Azure VMware vSAN com discos compartilhados nativos
description: Configure o WSFC (cluster de failover do Windows Server) na solução VMware do Azure e aproveite as soluções que exigem o recurso do WSFC.
ms.topic: how-to
ms.date: 03/09/2021
ms.openlocfilehash: d667eef00fcad0e3f5243c6ab580e2e8371c6793
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518986"
---
# <a name="windows-server-failover-cluster-on-azure-vmware-solution-vsan-with-native-shared-disks"></a>Cluster de failover do Windows Server na solução do Azure VMware vSAN com discos compartilhados nativos

Neste artigo, vamos examinar a configuração do cluster de failover do Windows Server na solução VMware do Azure. A implementação neste artigo é para fins de prova de conceito e piloto. É recomendável usar uma configuração de CIB (cluster-in-the-box) até que as políticas de posicionamento estejam disponíveis.

O WSFC (cluster de failover do Windows Server), anteriormente conhecido como Microsoft Service Cluster Service (MSCS), é um recurso do sistema operacional Windows Server (SO). O WSFC é um recurso crítico para os negócios, e para muitos aplicativos é necessário. Por exemplo, o WSFC é necessário para as seguintes configurações:

- SQL Server configurado como:
  - Always On FCI (instância de cluster de failover), para alta disponibilidade em nível de instância.
  - AG (Always On grupo de disponibilidade), para alta disponibilidade no nível do banco de dados.
- Serviços de arquivos do Windows:
  - Compartilhamento de arquivos genérico em execução no nó de cluster ativo.
  - Scale-Out servidor de arquivos (SOFS), que armazena arquivos em CSV (volumes compartilhados do cluster).
  - Espaços de Armazenamento Diretos (S2D); discos locais usados para criar pools de armazenamento em diferentes nós de cluster.

Você pode hospedar o Cluster WSFC em diferentes instâncias de solução do Azure VMware, conhecidas como CAB (cluster-entre caixas). Você também pode posicionar o Cluster WSFC em um único nó de solução do Azure VMware. Essa configuração é conhecida como cluster-in-box (CIB). Não recomendamos o uso de uma solução CIB para uma implementação de produção. Um único nó de solução do Azure VMware falhava, todos os nós de Cluster WSFC seriam desligados e o aplicativo experimentaria tempo de inatividade. A solução Azure VMware requer um mínimo de três nós em um cluster de nuvem privada.

É importante implantar uma configuração de WSFC com suporte. Você desejará que sua solução tenha suporte no vSphere e com a solução VMware do Azure. O VMware fornece um documento detalhado sobre o WSFC no vSphere 6,7, [configuração para clustering de failover e serviço de cluster da Microsoft](https://docs.vmware.com/en/VMware-vSphere/6.7/vsphere-esxi-vcenter-server-67-setup-mscs.pdf).

Este artigo se concentra no WSFC no Windows Server 2016 e no Windows Server 2019. As versões mais antigas do Windows Server estão fora do [suporte base](https://support.microsoft.com/lifecycle/search?alpha=windows%20server) e, portanto, não as consideramos aqui.

Você precisará primeiro [criar um WSFC](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster). Para obter mais informações sobre o WSFC, consulte [clustering de failover no Windows Server](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview). Use as informações que fornecemos neste artigo para obter as especificações de uma implantação do WSFC na solução VMware do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Ambiente da solução VMware do Azure
- Mídia de instalação do sistema operacional Microsoft Windows Server

## <a name="reference-architecture"></a>Arquitetura de referência

A solução Azure VMware fornece suporte nativo para WSFC virtualizado. Ele dá suporte a reservas persistentes SCSI-3 (SCSI3PR) em um nível de disco virtual. Esse suporte é exigido pelo WSFC para arbitrar o acesso a um disco compartilhado entre nós. O suporte do SCSI3PRs permite a configuração do WSFC com um recurso de disco compartilhado entre VMs nativamente em repositórios de armazenamento vSAN.

O diagrama a seguir ilustra a arquitetura de nós virtuais do WSFC em uma nuvem privada da solução Azure VMware. Ele mostra onde a solução Azure VMware reside, incluindo os servidores virtuais WSFC (caixa vermelha), em relação à plataforma mais ampla do Azure. Este diagrama ilustra uma arquitetura de hub spoke típica, mas uma configuração semelhante é possível com o uso da WAN virtual do Azure. Ambas oferecem todos os valores que outros serviços do Azure podem trazer.

[![Diagrama mostrando a arquitetura de nós virtuais do WSFC em uma nuvem privada da solução Azure VMware.](media/windows-server-failover-cluster/windows-server-failover-architecture.png)](media/windows-server-failover-cluster/windows-server-failover-architecture.png#lightbox)

## <a name="supported-configurations"></a>Configurações com suporte

No momento, há suporte para as seguintes configurações:

- Microsoft Windows Server 2012 ou posterior.
- Até cinco nós de clustering de failover por cluster.
- Até quatro adaptadores PVSCSI por VM.
- Até 64 discos por adaptador PVSCSI.

## <a name="virtual-machine-configuration-requirements"></a>Requisitos de configuração de máquina virtual

### <a name="wsfc-node-configuration-parameters"></a>Parâmetros de configuração de nó do WSFC

- Instale as ferramentas do VMware mais recentes em cada nó do WSFC.
- Não há suporte para a combinação de discos compartilhados e não compartilhados em um único adaptador SCSI virtual. Por exemplo, se o disco do sistema (unidade C:) está anexado a SCSI0:0, o primeiro disco compartilhado seria anexado a SCSI1:0. Um nó de VM de um WSFC tem o mesmo máximo de controlador SCSI virtual que uma VM comum-até quatro (4) controladores SCSI virtuais.
- As IDs de SCSI de discos virtuais devem ser consistentes entre todas as VMs que hospedam nós do mesmo WSFC.

| **Componente** | **Requirements** |
| --- | --- |
| Versão de hardware de VM | 11 ou superior para dar suporte ao vMotion em tempo real. |
| NIC virtual | NIC (placa de interface de rede) paravirtualizada VMXNET3; Habilite o RSS (recebimento do Windows) no convidado na NIC virtual. |
| Memória | Use a memória completa de reserva de VM para os nós no Cluster WSFC. |
| Aumente o tempo limite de e/s de cada nó do WSFC. | Modifique HKEY \_ local \_ MACHINE\System\CurrentControlSet\Services\Disk\TimeOutValueSet para 60 segundos ou mais. (Se você recriar o cluster, esse valor poderá ser redefinido para seu padrão, portanto, você deve alterá-lo novamente.) |
| Monitoramento de integridade do cluster do Windows | O valor do parâmetro SameSubnetThreshold do monitoramento de integridade do cluster do Windows deve ser modificado para permitir 10 pulsações perdidas no mínimo. Esse é [o padrão no Windows Server 2016](https://techcommunity.microsoft.com/t5/failover-clustering/tuning-failover-cluster-network-thresholds/ba-p/371834). Essa recomendação se aplica a todos os aplicativos que usam o WSFC, incluindo discos compartilhados e não compartilhados. |

### <a name="wsfc-node---boot-disks-configuration-parameters"></a>Parâmetros de configuração dos discos de inicialização do nó do WSFC


| **Componente** | **Requirements** |
| --- | --- |
| Tipo de controlador SCSI | SAS de lógica LSI |
| Modo de disco | Máquina |
| Compartilhamento de barramento SCSI | Nenhum |
| Modifique as configurações avançadas para um controlador SCSI virtual que hospeda o dispositivo de inicialização. | Adicione as seguintes configurações avançadas a cada nó do WSFC:<br /> scsiX. returnNoConnectDuringAPD = "TRUE"<br />scsiX. returnBusyOnNoConnectStatus = "FALSE"<br />Em que X é o número de ID do controlador de barramento SCSI do dispositivo de inicialização. Por padrão, X é definido como 0. |

### <a name="wsfc-node---shared-disks-configuration-parameters"></a>Nó WSFC-parâmetros de configuração de discos compartilhados


| **Componente** | **Requirements** |
| --- | --- |
| Tipo de controlador SCSI | Paravirtualizate VMware (PVSCSI) |
| Modo de disco | Independente-persistente (etapa 2 na ilustração abaixo). Ao usar essa configuração, você garante que todos os discos sejam excluídos dos instantâneos. Não há suporte para instantâneos em VMs baseadas em WSFC. |
| Compartilhamento de barramento SCSI | Físico (etapa 1 na ilustração abaixo) |
| Sinalizador de vários gravadores | Não usado |
| Formato de disco | Espesso provisionado. (EZT (com baixo zero) não é necessário com o vSAN.) |

:::image type="content" source="media/windows-server-failover-cluster/edit-settings-virtual-hardware.png" alt-text="Uma captura de tela mostrando a página Editar configurações para hardware virtual.":::

## <a name="non-supported-scenarios"></a>Cenários sem suporte

As seguintes funcionalidades não têm suporte para o WSFC na solução VMware do Azure:

- Armazenamentos de dados NFS
- Espaços de Armazenamento
- vSAN usando o serviço iSCSI
- Cluster do vSAN ampliado
- Compatibilidade do vMotion avançada (EVC)
- vSphere tolerância a falhas (FT)
- Instantâneos
- Live (online) Storage vMotion
- NPIV (N-Port ID Virtualization)

Alterações quentes no hardware da máquina virtual podem interromper a pulsação entre os nós do WSFC.

As seguintes atividades não têm suporte e podem causar o failover do nó do WSFC:

- Adição de memória a quente
- CPU de adição quente
- Usando instantâneos
- Aumentando o tamanho de um disco compartilhado
- Pausando e retomando o estado da máquina virtual
- Comprometimento excessivo de memória que leva à troca de ESXi ou ao balão de memória VM
- Estender o arquivo VMDK local, mesmo que ele não esteja associado ao controlador de compartilhamento de barramento SCSI

## <a name="configure-wsfc-with-shared-disks-on-azure-vmware-solution-vsan"></a>Configurar o WSFC com discos compartilhados na solução vSAN do Azure VMware

1. Verifique se um ambiente de Active Directory está disponível.
2. Crie máquinas virtuais (VMs) no repositório de armazenamento vSAN.
3. Ligue todas as VMs, configure o nome do host, os endereços IP, ingresse todas as VMs em um domínio Active Directory e instale as atualizações mais recentes do so disponíveis.
4. Instale as ferramentas do VMware mais recentes.
5. Habilite e configure o recurso de cluster de failover do Windows Server em cada VM.
6. Configure uma testemunha de cluster para quorum (uma testemunha de compartilhamento de arquivos funciona bem).
7. Desligue todos os nós do Cluster WSFC.
8. Adicione um ou mais controladores SCSI paravirtuais (até quatro) a cada parte da VM do WSFC. Use as configurações de acordo com os parágrafos anteriores.
9. No primeiro nó do cluster, adicione todos os discos compartilhados necessários usando **Adicionar novo**  >  **disco rígido** do dispositivo. O compartilhamento de disco deve ser deixado como **não especificado** (padrão) e o modo de disco como **independente persistente**. Anexe-o aos controladores criados nas etapas anteriores.
10. Continue com os nós restantes do WSFC. Adicione os discos criados na etapa anterior selecionando **Adicionar novo dispositivo**  >  **disco rígido existente**. Certifique-se de manter as mesmas IDs de SCSI de disco em todos os nós do WSFC.
11. Ligar o primeiro nó WSFC; entre e abra o console de gerenciamento de disco (MMC). Verifique se os discos compartilhados adicionados podem ser gerenciados pelo sistema operacional e se foram inicializados. Formate os discos e atribua uma letra de unidade.
12. Ligue os outros nós do WSFC.
13. Adicione o disco ao Cluster WSFC usando o **Assistente para adicionar disco** e adicione-o a um volume compartilhado clusterizado.
14. Teste um failover usando o **Assistente para mover disco** e verifique se o Cluster WSFC com discos compartilhados funciona corretamente.
15. Execute o **Assistente de cluster de validação** para confirmar se o cluster e seus nós estão funcionando corretamente.

    É importante manter os seguintes itens específicos do teste de validação do cluster em mente:

       - **Validar reserva persistente de espaços de armazenamento**. Se você não estiver usando espaços de armazenamento com o cluster (por exemplo, na solução vSAN do Azure VMware), esse teste não será aplicável. Você pode ignorar todos os resultados do teste de reserva persistente para validar espaços de armazenamento, incluindo este aviso. Para evitar avisos, você pode excluir este teste.
        
      - **Validar a comunicação de rede**. O teste de validação do cluster emitirá um aviso de que apenas uma interface de rede por nó de cluster está disponível. Você pode ignorar este aviso. A solução Azure VMware fornece a disponibilidade e o desempenho necessários, já que os nós estão conectados a um dos segmentos do NSX-T. No entanto, mantenha esse item como parte do teste de validação de cluster, pois ele validará outros aspectos da comunicação de rede.

16. Crie uma regra do DRS para posicionar as VMs do WSFC nos mesmos nós de solução do Azure VMware. Para fazer isso, você precisa de uma regra de afinidade de host para VM. Dessa forma, os nós de cluster serão executados no mesmo host de solução do Azure VMware. Novamente, isso é para fins pilotos até que as políticas de posicionamento estejam disponíveis.

    >[!NOTE]
    > Para isso, você precisa criar um tíquete de solicitação de suporte. Nossa organização de suporte do Azure poderá ajudá-lo com isso.

## <a name="related-information"></a>Informações relacionadas

- [Clustering de failover no Windows Server](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [Diretrizes para o Microsoft Clustering no vSphere (1037959) (vmware.com)](https://kb.vmware.com/s/article/1037959)
- [Sobre a instalação do clustering de failover e o serviço de cluster da Microsoft (vmware.com)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.mscs.doc/GUID-1A2476C0-CA66-4B80-B6F9-8421B6983808.html)
- [vSAN 6,7 U3-WSFC com discos compartilhados &amp; – reservas persistentes SCSI-3 (VMware.com)](https://blogs.vmware.com/virtualblocks/2019/08/23/vsan67-u3-wsfc-shared-disksupport/)
- [Limites da solução VMware do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-vmware-solution-limits)

## <a name="next-steps"></a>Próximas etapas

Agora que você já abordou a configuração de um WSFC na solução do Azure VMware, talvez queira saber mais sobre:

- Configurando seu novo WSFC adicionando mais aplicativos que exigem o recurso do WSFC. Por exemplo, SQL Server e SAP ASCS.
- Configurando uma solução de backup.
  - [Configurando o Servidor de Backup do Azure para a solução VMware do Azure](https://docs.microsoft.com/azure/azure-vmware/set-up-backup-server-for-azure-vmware-solution)
  - [Soluções de backup para máquinas virtuais da solução Azure VMware](https://docs.microsoft.com/azure/azure-vmware/ecosystem-back-up-vms)
