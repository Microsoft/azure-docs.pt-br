---
title: Notas de versão da visualização do Azure Stack Edge pro | Microsoft Docs
description: Descreve problemas e resoluções críticos em aberto para a versão de disponibilidade de visualização do Azure Stack Edge pro em execução.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: dd72865e35318c7ff43dc17b7c92b9cc2f3e9790
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102436848"
---
# <a name="azure-stack-edge-pro-with-gpu-preview-release-notes"></a>Notas de versão do Azure Stack Edge pro com a visualização de GPU

[!INCLUDE [applies-to-Pro-GPU-sku](../../includes/azure-stack-edge-applies-to-gpu-sku.md)]

As notas de versão a seguir identificam os problemas críticos abertos e os problemas resolvidos para a versão de visualização de 2008 para seus dispositivos Azure Stack Edge pro com GPU.

As notas de versão são continuamente atualizadas e, à medida que são descobertas questões críticas que exijam uma solução alternativa, elas são adicionadas. Antes de implantar seu dispositivo Azure Stack Edge pro, Examine cuidadosamente as informações contidas nas notas de versão.

Este artigo se aplica à versão de software a seguir – **Azure Stack Edge Pro 2008**.

<!--- **2.1.1328.1904**-->

## <a name="whats-new"></a>Novidades

Os novos recursos a seguir foram adicionados na versão Azure Stack Edge 2008. Dependendo da versão de software de visualização específica que você está executando, você poderá ver um subconjunto desses recursos. 

- **Classes de armazenamento** -na versão anterior, você poderia provisionar estaticamente o armazenamento por meio de compartilhamentos SMB ou NFS para aplicativos com estado implantados no cluster kubernetes em execução no dispositivo Pro Edge Azure Stack. Nesta versão, foram adicionadas classes de armazenamento que permitem provisionar dinamicamente o armazenamento. Para obter mais informações, consulte [Gerenciamento de armazenamento kubernetes em seu dispositivo Azure Stack Edge pro GPU](azure-stack-edge-gpu-kubernetes-storage.md#dynamicprovisioning). 
- **Painel do kubernetes com o servidor de métricas** – nesta versão, um painel do kubernetes é adicionado com um complemento do servidor de métricas. Você pode usar o painel para obter uma visão geral dos aplicativos em execução no dispositivo Azure Stack Edge pro, exibir o status dos recursos de cluster do kubernetes e ver os erros que ocorreram no dispositivo. O servidor de métricas agrega o uso de CPU e de memória entre os recursos do kubernetes no dispositivo. Para obter mais informações, consulte [usar o painel do kubernetes para monitorar seu dispositivo de GPU pro do Azure Stack Edge](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).
- **Azure ARC para Azure Stack Edge pro** -iniciando esta versão, você pode implantar cargas de trabalho de aplicativo em seu dispositivo Azure Stack Edge pro por meio do Azure Arc. O Arc do Azure é uma ferramenta de gerenciamento híbrido que permite implantar aplicativos em seus clusters kubernetes. Para obter mais informações, consulte [implantar cargas de trabalho por meio do Azure ARC em seu dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).  

## <a name="known-issues"></a>Problemas conhecidos 

A tabela a seguir fornece um resumo dos problemas conhecidos para o dispositivo Azure Stack Edge pro.

| Não. | Recurso | Problema | Solução alternativa/comentários |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge pro + SQL do Azure | A criação do banco de dados SQL requer acesso de administrador.   |Execute as etapas a seguir em vez das etapas de 1-2 no [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database) . <ul><li>Na interface do usuário local do seu dispositivo, habilite a interface de computação. Selecione **computação > porta n º > habilitar para computação > aplicar.**</li><li>Baixar `sqlcmd` no computador cliente de https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Conecte-se ao seu endereço IP da interface de computação (a porta que estava habilitada), adicionando um ", 1401" ao final do endereço.</li><li>O comando final terá a seguinte aparência: sqlcmd-S {interface IP}, 1401-U SA-P "Strong! Passw0rd".</li>Depois disso, as etapas 3-4 da documentação atual devem ser idênticas. </li></ul> |
| **2.** |Atualizar| Não há suporte para alterações incrementais em BLOBs restaurados via **atualização** |Para pontos de extremidade de BLOB, atualizações parciais de BLOBs após uma atualização, podem resultar em atualizações não sendo carregadas para a nuvem. Por exemplo, sequência de ações como:<ul><li>Criar blob na nuvem. Ou exclua um blob carregado anteriormente do dispositivo.</li><li>Atualize o blob da nuvem para o dispositivo usando a funcionalidade de atualização.</li><li>Atualize apenas uma parte do blob usando as APIs REST do SDK do Azure.</li></ul>Essas ações podem fazer com que as seções atualizadas do BLOB não sejam atualizadas na nuvem. <br>**Solução alternativa**: Use ferramentas como Robocopy ou cópia de arquivo regular por meio do Explorer ou da linha de comando para substituir BLOBs inteiros.|
|**3.**|Limitação|Durante a limitação, se novas gravações não forem permitidas no dispositivo, as gravações feitas pelo cliente NFS falharão com o erro "permissão negada".| O erro será mostrado como abaixo:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: não é possível criar o diretório ' test ': permissão negada|
|**4.**|Ingestão de armazenamento de BLOBs|Ao usar o AzCopy versão 10 para ingestão de armazenamento de BLOBs, execute AzCopy com o seguinte argumento: `Azcopy <other arguments> --cap-mbps 2000`| Se esses limites não forem fornecidos para AzCopy, ele poderá enviar um grande número de solicitações para o dispositivo e resultar em problemas com o serviço.|
|**5.**|Contas de armazenamento em camadas|O seguinte se aplica ao usar contas de armazenamento em camadas:<ul><li> Somente há suporte para BLOBs de blocos. Blobs de página não têm suporte.</li><li>Não há suporte para instantâneo ou API de cópia.</li><li> Não há suporte para a ingestão de carga de trabalho do Hadoop `distcp` , pois ela usa a operação de cópia de forma intensiva.</li></ul>||
|**6.**|Conexão de compartilhamento NFS|Se vários processos estiverem sendo copiados para o mesmo compartilhamento e o `nolock` atributo não for usado, você poderá ver erros durante a cópia.|O `nolock` atributo deve ser passado para o comando mount para copiar arquivos para o compartilhamento NFS. Por exemplo: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**7.**|Cluster do Kubernetes|Ao aplicar uma atualização em seu dispositivo que está executando um cluster kubernetes, as máquinas virtuais kubernetes serão reiniciadas e reiniciadas. Nessa instância, somente os pods implantados com réplicas especificadas são automaticamente restaurados após uma atualização.  |Se você tiver criado pods individuais fora de um controlador de replicação sem especificar um conjunto de réplicas, esses pods não serão restaurados automaticamente após a atualização do dispositivo. Você precisará restaurar esses pods.<br>Um conjunto de réplicas substitui os pods que são excluídos ou encerrados por qualquer motivo, como falha de nó ou atualização de nó de interrupção. Por esse motivo, recomendamos que você use um conjunto de réplicas mesmo que seu aplicativo exija apenas um único Pod.|
|**8.**|Cluster do Kubernetes|Só há suporte para kubernetes no Azure Stack Edge pro com o Helm V3 ou posterior. Para obter mais informações, acesse as perguntas frequentes [: remoção do gaveta](https://v3.helm.sh/docs/faq/).|
|**9.**|Azure Arc + Azure Stack Edge pro|Não há suporte para implantações de arco do Azure se o proxy Web estiver configurado em seu dispositivo Azure Stack Edge pro.||
|**254.**|Kubernetes |A porta 31000 é reservada para o painel do kubernetes. Da mesma forma, na configuração padrão, os endereços IP 172.28.0.1 e 172.28.0.10, são reservados para o serviço kubernetes e o serviço DNS principal, respectivamente.|Não use IPs reservados.|
|**11.**|Kubernetes |O kubernetes atualmente não permite serviços de balanceador de vários protocolos. Por exemplo, um serviço DNS que teria que escutar em TCP e UDP. |Para solucionar essa limitação de kubernetes com MetalLB, dois serviços (um para TCP, um para UDP) podem ser criados no mesmo seletor de Pod. Esses serviços usam a mesma chave de compartilhamento e spec. loadBalancerIP para compartilhar o mesmo endereço IP. Os IPs também podem ser compartilhados se você tiver mais serviços do que os endereços IP disponíveis. <br> Para obter mais informações, consulte [compartilhamento de endereço IP](https://metallb.universe.tf/usage/#ip-address-sharing).|
|**12.**|Cluster do Kubernetes|Os módulos existentes do Marketplace Azure IoT Edge não serão executados no cluster kubernetes como plataforma de hospedagem para IoT Edge no dispositivo Azure Stack Edge.|Os módulos precisarão ser modificados antes de serem implantados no dispositivo Azure Stack Edge. Para obter mais informações, consulte modificar módulos de Azure IoT Edge do Marketplace para executar em Azure Stack dispositivo de borda.<!-- insert link-->|
|**13.**|Kubernetes |Não há suporte para montagens de associação baseadas em arquivo com Azure IoT Edge em kubernetes no dispositivo Azure Stack Edge.|IoT Edge usa uma camada de conversão para converter `ContainerCreate` opções em construções kubernetes. A criação de `Binds` mapas para `hostpath` diretório ou criação e, portanto, montagens de associação baseadas em arquivo não podem ser associadas a caminhos em contêineres de IOT Edge.|
|**140.**|Kubernetes |Se você colocar seus próprios certificados para IoT Edge e adicioná-los em seu dispositivo Azure Stack Edge, os novos certificados não serão selecionados como parte da atualização de gráficos do Helm.|Para solucionar esse problema, [Conecte-se à interface do PowerShell do dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md). Reiniciar `iotedged` e `edgehub` pods.|
|**15.**|Certificados |Em determinadas instâncias, o estado do certificado na interface do usuário local pode levar vários segundos para ser atualizado. |Os cenários a seguir na interface do usuário local podem ser afetados.<ul><li>Coluna **status** na página **certificados** .</li><li>Bloco **segurança** na **página Introdução.**</li><li>Bloco **configuração** na página **visão geral** .</li></ul>  |

## <a name="next-steps"></a>Próximas etapas

- [Preparar para implantar o dispositivo pro Edge Azure Stack com GPU](azure-stack-edge-gpu-deploy-prep.md)