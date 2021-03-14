---
title: Notas de versão do Azure Stack Edge pro GA | Microsoft Docs
description: Descreve problemas e resoluções críticos em aberto para o Azure Stack Edge pro executando a versão de disponibilidade geral.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: 20340a07160a0b4501069e0f4d45599c5bff33f2
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444030"
---
# <a name="azure-stack-edge-pro-with-gpu-general-availability-ga-release-notes"></a>Notas de versão do Azure Stack Edge pro com a disponibilidade geral da GPU (GA)

[!INCLUDE [applies-to-Pro-GPU-sku](../../includes/azure-stack-edge-applies-to-gpu-sku.md)]

As notas de versão a seguir identificam os problemas críticos abertos e os problemas resolvidos para a versão de GA (disponibilidade geral) para seus dispositivos Azure Stack Edge pro com GPU.

As notas de versão são continuamente atualizadas e, à medida que são descobertas questões críticas que exijam uma solução alternativa, elas são adicionadas. Antes de implantar seu dispositivo Azure Stack Edge pro, Examine cuidadosamente as informações contidas nas notas de versão.

Este artigo se aplica à versão **Pro 2010 do Azure Stack Edge** , que é mapeada para o número de versão do software **2.1.1377.2170**.

## <a name="whats-new"></a>Novidades

Os novos recursos a seguir estão disponíveis na versão do Azure Stack Edge 2010. 

- **Classes de armazenamento** – nesta versão, as classes de armazenamento estão disponíveis para que você provisione dinamicamente o armazenamento. Para obter mais informações, consulte [Gerenciamento de armazenamento kubernetes em seu dispositivo Azure Stack Edge pro GPU](azure-stack-edge-gpu-kubernetes-storage.md#dynamicprovisioning). 
- **Painel do kubernetes com o servidor de métricas** – nesta versão, um painel do kubernetes é adicionado com um complemento do servidor de métricas. Você pode usar o painel para obter uma visão geral dos aplicativos em execução no dispositivo Azure Stack Edge pro, exibir o status dos recursos de cluster do kubernetes e ver os erros que ocorreram no dispositivo. O servidor de métricas agrega o uso de CPU e de memória entre os recursos do kubernetes no dispositivo. Para obter mais informações, consulte [usar o painel do kubernetes para monitorar seu dispositivo de GPU pro do Azure Stack Edge](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).
- **Azure Arc habilitado kubernetes no Azure Stack Edge pro** -iniciando esta versão, você pode implantar cargas de trabalho de aplicativo em seu dispositivo do Azure Stack Edge pro por meio do kubernetes habilitado para Arc do Azure. O Arc do Azure é uma ferramenta de gerenciamento híbrido que permite implantar aplicativos em seus clusters kubernetes. Para obter mais informações, consulte [implantar cargas de trabalho por meio do Azure ARC em seu dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).  

## <a name="known-issues"></a>Problemas conhecidos 

A tabela a seguir fornece um resumo dos problemas conhecidos para o dispositivo Azure Stack Edge pro.

| Não. | Recurso | Problema | Solução alternativa/comentários |
| --- | --- | --- | --- |
|**1.**|Versão prévia dos recursos |Para esta versão de GA, os seguintes recursos: local Azure Resource Manager, VMs, kubernetes, kubernetes do Azure habilitados para o setor, MPS (serviço de vários processos) para GPU, estão disponíveis em versão prévia para seu dispositivo Azure Stack Edge pro.  |Esses recursos estarão disponíveis para o público em geral em uma versão posterior. |
| **2.** |Azure Stack Edge pro + SQL do Azure | A criação do banco de dados SQL requer acesso de administrador.   |Execute as etapas a seguir em vez das etapas de 1-2 no [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database) . <ul><li>Na interface do usuário local do seu dispositivo, habilite a interface de computação. Selecione **computação > porta n º > habilitar para computação > aplicar.**</li><li>Baixar `sqlcmd` no computador cliente de https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Conecte-se ao seu endereço IP da interface de computação (a porta que estava habilitada), adicionando um ", 1401" ao final do endereço.</li><li>O comando final terá a seguinte aparência: sqlcmd-S {interface IP}, 1401-U SA-P "Strong! Passw0rd".</li>Depois disso, as etapas 3-4 da documentação atual devem ser idênticas. </li></ul> |
| **3.** |Atualizar| Não há suporte para alterações incrementais em BLOBs restaurados via **atualização** |Para pontos de extremidade de BLOB, atualizações parciais de BLOBs após uma atualização, podem resultar em atualizações não sendo carregadas para a nuvem. Por exemplo, sequência de ações como:<ul><li>Criar blob na nuvem. Ou exclua um blob carregado anteriormente do dispositivo.</li><li>Atualize o blob da nuvem para o dispositivo usando a funcionalidade de atualização.</li><li>Atualize apenas uma parte do blob usando as APIs REST do SDK do Azure.</li></ul>Essas ações podem fazer com que as seções atualizadas do BLOB não sejam atualizadas na nuvem. <br>**Solução alternativa**: Use ferramentas como Robocopy ou cópia de arquivo regular por meio do Explorer ou da linha de comando para substituir BLOBs inteiros.|
|**4.**|Limitação|Durante a limitação, se novas gravações não forem permitidas no dispositivo, as gravações feitas pelo cliente NFS falharão com o erro "permissão negada".| O erro será mostrado como abaixo:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: não é possível criar o diretório ' test ': permissão negada|
|**5.**|Ingestão de armazenamento de BLOBs|Ao usar o AzCopy versão 10 para ingestão de armazenamento de BLOBs, execute AzCopy com o seguinte argumento: `Azcopy <other arguments> --cap-mbps 2000`| Se esses limites não forem fornecidos para AzCopy, ele poderá enviar um grande número de solicitações para o dispositivo e resultar em problemas com o serviço.|
|**6.**|Contas de armazenamento em camadas|O seguinte se aplica ao usar contas de armazenamento em camadas:<ul><li> Somente há suporte para BLOBs de blocos. Blobs de página não têm suporte.</li><li>Não há suporte para instantâneo ou API de cópia.</li><li> Não há suporte para a ingestão de carga de trabalho do Hadoop `distcp` , pois ela usa a operação de cópia de forma intensiva.</li></ul>||
|**7.**|Conexão de compartilhamento NFS|Se vários processos estiverem sendo copiados para o mesmo compartilhamento e o `nolock` atributo não for usado, você poderá ver erros durante a cópia.|O `nolock` atributo deve ser passado para o comando mount para copiar arquivos para o compartilhamento NFS. Por exemplo: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**8.**|Cluster do Kubernetes|Ao aplicar uma atualização em seu dispositivo que está executando um cluster kubernetes, as máquinas virtuais kubernetes serão reiniciadas e reiniciadas. Nessa instância, somente os pods implantados com réplicas especificadas são automaticamente restaurados após uma atualização.  |Se você tiver criado pods individuais fora de um controlador de replicação sem especificar um conjunto de réplicas, esses pods não serão restaurados automaticamente após a atualização do dispositivo. Você precisará restaurar esses pods.<br>Um conjunto de réplicas substitui os pods que são excluídos ou encerrados por qualquer motivo, como falha de nó ou atualização de nó de interrupção. Por esse motivo, recomendamos que você use um conjunto de réplicas mesmo que seu aplicativo exija apenas um único Pod.|
|**9.**|Cluster do Kubernetes|Só há suporte para kubernetes no Azure Stack Edge pro com o Helm V3 ou posterior. Para obter mais informações, acesse as perguntas frequentes [: remoção do gaveta](https://v3.helm.sh/docs/faq/).|
|**254.**|Kubernetes habilitado para Azure Arc |Para a versão GA, o kubernetes habilitado para Arc do Azure é atualizado da versão 0.1.18 para 0.2.9. Como a atualização kubernetes habilitada para o Arc do Azure não tem suporte no dispositivo Azure Stack Edge, você precisará reimplantar o kubernetes habilitado para Arc do Azure.|Siga estas etapas:<ol><li>[Aplique o software do dispositivo e as atualizações do kubernetes](azure-stack-edge-gpu-install-update.md).</li><li>Conecte-se à [interface do PowerShell do dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md).</li><li>Remova o agente de arco do Azure existente. Digite: `Remove-HcsKubernetesAzureArcAgent`.</li><li>Implante [o arco do Azure em um novo recurso](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). Não use um recurso de arco do Azure existente.</li></ol>|
|**11.**|Kubernetes habilitado para Azure Arc|Não há suporte para implantações de arco do Azure se o proxy Web estiver configurado em seu dispositivo Azure Stack Edge pro.||
|**12.**|Kubernetes |A porta 31000 é reservada para o painel do kubernetes. Da mesma forma, na configuração padrão, os endereços IP 172.28.0.1 e 172.28.0.10, são reservados para o serviço kubernetes e o serviço DNS principal, respectivamente.|Não use IPs reservados.|
|**13.**|Kubernetes |O kubernetes atualmente não permite serviços de balanceador de vários protocolos. Por exemplo, um serviço DNS que teria que escutar em TCP e UDP. |Para solucionar essa limitação de kubernetes com MetalLB, dois serviços (um para TCP, um para UDP) podem ser criados no mesmo seletor de Pod. Esses serviços usam a mesma chave de compartilhamento e spec. loadBalancerIP para compartilhar o mesmo endereço IP. Os IPs também podem ser compartilhados se você tiver mais serviços do que os endereços IP disponíveis. <br> Para obter mais informações, consulte [compartilhamento de endereço IP](https://metallb.universe.tf/usage/#ip-address-sharing).|
|**140.**|Cluster do Kubernetes|Os módulos existentes do Marketplace Azure IoT Edge podem exigir modificações para serem executados em IoT Edge no dispositivo Azure Stack Edge.|Para obter mais informações, consulte modificar módulos de Azure IoT Edge do Marketplace para executar em Azure Stack dispositivo de borda.<!-- insert link-->|
|**15.**|Kubernetes |Não há suporte para montagens de associação baseadas em arquivo com Azure IoT Edge em kubernetes no dispositivo Azure Stack Edge.|IoT Edge usa uma camada de conversão para converter `ContainerCreate` opções em construções kubernetes. A criação de `Binds` mapas para o `hostpath` diretório e, portanto, montagens de associação baseadas em arquivo não podem ser associadas a caminhos em contêineres de IOT Edge. Se possível, mapeie o diretório pai.|
|**16.**|Kubernetes |Se você colocar seus próprios certificados para IoT Edge e adicioná-los em seu dispositivo Azure Stack Edge depois que a computação estiver configurada no dispositivo, os novos certificados não serão selecionados.|Para contornar esse problema, você deve carregar os certificados antes de configurar a computação no dispositivo. Se a computação já estiver configurada, [Conecte-se à interface do PowerShell do dispositivo e execute IOT Edge comandos](azure-stack-edge-gpu-connect-powershell-interface.md#use-iotedge-commands). Reiniciar `iotedged` e `edgehub` pods.|
|**16.**|Certificados |Em determinadas instâncias, o estado do certificado na interface do usuário local pode levar vários segundos para ser atualizado. |Os cenários a seguir na interface do usuário local podem ser afetados.<ul><li>Coluna **status** na página **certificados** .</li><li>Bloco **segurança** na **página Introdução.**</li><li>Bloco **configuração** na página **visão geral** .</li></ul>  |
|**16.**|IoT Edge |Módulos implantados por meio de IoT Edge não podem usar a rede de host. | |
|**anos.**|Computação + kubernetes |Compute/kubernetes não oferece suporte a proxy da Web NTLM. ||
|**aprimora.**|Computação + proxy da Web + atualização |Se você tiver a computação configurada com o proxy Web, a atualização de computação poderá falhar. |Recomendamos que você desabilite a computação antes da atualização. |

<!--|**18.**|Azure Private Edge Zone (Preview) |There is a known issue with Virtual Network Function VM if the VM was created on Azure Stack Edge device running earlier preview builds such as 2006/2007b and then the device was updated to 2009 GA release. The issue is that the VNF information can't be retrieved or any new VNFs can't be created unless the VNF VMs are deleted before the device is updated.  |Before you update Azure Stack Edge device to 2009 release, use the PowerShell command `get-mecvnf` followed by `remove-mecvnf <VNF guid>` to remove all Virtual Network Function VMs one at a time. After the upgrade, you will need to redeploy the same VNFs.|-->




## <a name="next-steps"></a>Próximas etapas

- [Preparar para implantar o dispositivo pro Edge Azure Stack com GPU](azure-stack-edge-gpu-deploy-prep.md)