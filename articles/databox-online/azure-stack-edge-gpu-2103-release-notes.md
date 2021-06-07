---
title: Notas de versão do Azure Stack Edge pro 2103
description: Descreve os problemas e as resoluções críticas em aberto para o Azure Stack Edge pro executando a versão 2103.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/23/2021
ms.author: alkohli
ms.openlocfilehash: 846d4a259f0fcd204bcad6c898efc999c3765fd3
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104962472"
---
# <a name="azure-stack-edge-2103-release-notes"></a>Notas de versão do Azure Stack Edge 2103

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

As notas de versão a seguir identificam os problemas críticos abertos e os problemas resolvidos para a versão 2103 para seus dispositivos Azure Stack Edge. Essas notas de versão são aplicáveis para o Azure Stack Edge pro GPU, o Azure Stack Edge pro R e o Azure Stack dispositivos mini R do Edge. Os recursos e problemas que correspondem a um modelo específico são chamados sempre que aplicável.

As notas de versão são continuamente atualizadas e, à medida que são descobertas questões críticas que exijam uma solução alternativa, elas são adicionadas. Antes de implantar seu dispositivo, Examine cuidadosamente as informações contidas nas notas de versão.

Este artigo se aplica à versão do **Azure Stack Edge 2103** , que é mapeada para o número de versão do software **2.2.1540.2890**. Esse software pode ser aplicado ao seu dispositivo se você estiver executando pelo menos Azure Stack o software 2010 Edge (2.1.1377.2170).

## <a name="whats-new"></a>Novidades

Os novos recursos a seguir estão disponíveis na versão do Azure Stack Edge 2103. 
 
- **Novos recursos para máquinas virtuais** -iniciando esta versão, você pode executar as seguintes operações de gerenciamento nas máquinas virtuais por meio do portal do Azure:
    - Adicione ou remova várias interfaces de rede em uma VM existente.
    - Adicionar ou remover vários discos de uma VM existente.
    - Redimensione a VM.
    - Adicione dados personalizados ao implantar uma VM do Windows ou do Linux.

  Você também pode [se conectar ao console da VM em seu dispositivo](azure-stack-edge-gpu-connect-virtual-machine-console.md) e solucionar problemas de VM.

- **Conectar à interface do PowerShell via HTTPS** – iniciando esta versão, você não poderá mais abrir uma sessão remota do PowerShell em um dispositivo por *http*. Por padrão, o *https* será usado para todas as sessões. Para obter mais informações, consulte como [se conectar à interface do PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) do seu dispositivo.

- **Melhorias para computação** – vários aprimoramentos e aprimoramentos foram feitos, incluindo aqueles para:

    - **Qualidade geral da plataforma de computação**. Esta versão tem correções de bugs para melhorar a qualidade geral da plataforma de computação. Consulte os [problemas corrigidos na versão 2103](#issues-fixed-in-2103-release). 
    - **Componentes da plataforma de computação**. Atualizações de segurança foram aplicadas para computar a imagem da VM. IoT Edge e o Arc do Azure para versões kubernetes também foram atualizados.
    - **Diagnóstico**. Uma nova API é liberada para verificar as condições de recursos e de rede. Você pode se conectar à interface do PowerShell do dispositivo e usar o `Test-HcsKubernetesStatus` comando para verificar a prontidão da rede do dispositivo.
    - **Coleção de log** que levaria a uma depuração aprimorada. 
    - A **infraestrutura de alerta** que permitirá detectar conflitos de endereço IP para endereços IP de computação. 
    - **Combinação de carga de trabalho** de kubernetes e local Azure Resource Manager. 

- **Log proativo por padrão** -iniciando esta versão, a coleta de log proativo é habilitada por padrão em seu dispositivo. Esse recurso permite que a Microsoft colete logs proativamente com base nos indicadores de integridade do sistema para ajudar a solucionar com eficiência qualquer problema de dispositivo. Para obter mais informações, consulte [coleta de log proativo em seu dispositivo](azure-stack-edge-gpu-proactive-log-collection.md).

## <a name="issues-fixed-in-2103-release"></a>Problemas corrigidos na versão 2103

A tabela a seguir lista os problemas que foram observados em versões anteriores e corrigidos na versão atual.

| Não. | Recurso | Problema | 
| --- | --- | --- |
|**1.**|Kubernetes |O registro de contêiner do Edge não funciona quando o proxy Web está habilitado.|
|**2.**|Kubernetes |O registro de contêiner do Edge não funciona com módulos IoT Edge.| 

## <a name="known-issues-in-2103-release"></a>Problemas conhecidos na versão 2103

A tabela a seguir fornece um resumo dos problemas conhecidos na versão 2103.

| Não. | Recurso | Problema | Solução alternativa/comentários |
| --- | --- | --- | --- |
|**1.**|Versão prévia dos recursos |Para esta versão, os seguintes recursos: Azure Resource Manager locais, VMs, gerenciamento de nuvem de VMs, kubernetes Cloud Management, Azure Arc habilitado kubernetes, VPN para Azure Stack Edge pro R e Azure Stack Edge mini R, MPS (serviço de vários processos) para Azure Stack Edge pro GPU-estão todos disponíveis na visualização.  |Esses recursos estarão disponíveis para o público em geral em versões posteriores. |
|**2.**|VMs GPU |Antes desta versão, o ciclo de vida da VM da GPU não foi gerenciado no fluxo de atualização. Portanto, ao atualizar para a versão 2103, as VMs de GPU não são interrompidas automaticamente durante a atualização. Você precisará parar manualmente as VMs GPU usando um `stop-stayProvisioned` sinalizador antes de atualizar seu dispositivo. Para obter mais informações, consulte [suspender ou desligar a VM](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#suspend-or-shut-down-the-vm).<br> Todas as VMs de GPU que são mantidas em execução antes da atualização, são iniciadas após a atualização. Nessas instâncias, as cargas de trabalho em execução nas VMs não são encerradas normalmente. E as VMs podem acabar em um estado indesejável após a atualização. <br>Todas as VMs de GPU que são interrompidas por meio do `stop-stayProvisioned` antes da atualização são iniciadas automaticamente após a atualização. <br>Se você parar as VMs de GPU por meio do portal do Azure, será necessário iniciar manualmente a VM após a atualização do dispositivo.| Se estiver executando VMs de GPU com kubernetes, pare as VMs de GPU logo antes da atualização. <br>Quando as VMs da GPU forem interrompidas, o kubernetes assumirá as GPUs que foram usadas originalmente pelas VMs. <br>Quanto mais longas as VMs de GPU estiverem no estado parado, mais alta será a probabilidade de que o kubernetes assuma as GPUs. |
|**3.**|Extensão de VM de script personalizado |Há um problema conhecido nas VMs do Windows que foram criadas em uma versão anterior e o dispositivo foi atualizado para 2103. <br> Se você adicionar uma extensão de script personalizado nessas VMs, o agente convidado de VM do Windows (somente 2.7.41491.901 de versão) ficará preso na atualização, fazendo com que a implantação de extensão expire o tempo limite. | Para contornar este problema: <ul><li> Conecte-se à VM do Windows usando o protocolo RDP. </li><li> Verifique se o `waappagent.exe` está em execução no computador: `Get-Process WaAppAgent` . </li><li> Se o `waappagent.exe` não estiver em execução, reinicie o `rdagent` serviço: `Get-Service RdAgent` \| `Restart-Service` . Aguarde 5 minutos.</li><li> Enquanto o `waappagent.exe` estiver em execução, encerre o `WindowsAzureGuest.exe` processo. </li><li>Depois de encerrar o processo, o processo começa a ser executado novamente com a versão mais recente.</li><li>Verifique se a versão do agente convidado da VM do Windows é 2.7.41491.971 usando este comando: `Get-Process WindowsAzureGuestAgent` \| `fl ProductVersion` .</li><li>[Configure a extensão de script personalizado na VM do Windows](azure-stack-edge-gpu-deploy-virtual-machine-custom-script-extension.md). </li><ul> | 
|**4.**|MPS (serviço de vários processos) |Quando o software do dispositivo e o cluster kubernetes são atualizados, a configuração de MPS não é mantida para as cargas de trabalho.   |[Habilite novamente os MPS](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) e reimplante as cargas de trabalho que estavam usando MPS. |


## <a name="known-issues-from-previous-releases"></a>Problemas conhecidos de versões anteriores

A tabela a seguir fornece um resumo dos problemas conhecidos transmitidos das versões anteriores.

| Não. | Recurso | Problema | Solução alternativa/comentários |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge pro + SQL do Azure | A criação do banco de dados SQL requer acesso de administrador.   |Execute as etapas a seguir em vez das etapas de 1-2 no [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database) . <ul><li>Na interface do usuário local do seu dispositivo, habilite a interface de computação. Selecione **computação > porta n º > habilitar para computação > aplicar.**</li><li>Baixar `sqlcmd` no computador cliente de https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Conecte-se ao seu endereço IP da interface de computação (a porta que estava habilitada), adicionando um ", 1401" ao final do endereço.</li><li>O comando final terá a seguinte aparência: sqlcmd-S {interface IP}, 1401-U SA-P "Strong! Passw0rd".</li>Depois disso, as etapas 3-4 da documentação atual devem ser idênticas. </li></ul> |
| **2.** |Atualizar| Não há suporte para alterações incrementais em BLOBs restaurados via **atualização** |Para pontos de extremidade de BLOB, atualizações parciais de BLOBs após uma atualização, podem resultar em atualizações não sendo carregadas para a nuvem. Por exemplo, sequência de ações como:<ul><li>Criar blob na nuvem. Ou exclua um blob carregado anteriormente do dispositivo.</li><li>Atualize o blob da nuvem para o dispositivo usando a funcionalidade de atualização.</li><li>Atualize apenas uma parte do blob usando as APIs REST do SDK do Azure.</li></ul>Essas ações podem fazer com que as seções atualizadas do BLOB não sejam atualizadas na nuvem. <br>**Solução alternativa**: Use ferramentas como Robocopy ou cópia de arquivo regular por meio do Explorer ou da linha de comando para substituir BLOBs inteiros.|
|**3.**|Limitação|Durante a limitação, se novas gravações no dispositivo não forem permitidas, as gravações pelo cliente NFS falharão com um erro "permissão negada".| O erro será mostrado como abaixo:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: não é possível criar o diretório ' test ': permissão negada|
|**4.**|Ingestão de armazenamento de BLOBs|Ao usar o AzCopy versão 10 para ingestão de armazenamento de BLOBs, execute AzCopy com o seguinte argumento: `Azcopy <other arguments> --cap-mbps 2000`| Se esses limites não forem fornecidos para AzCopy, ele poderá enviar um grande número de solicitações para o dispositivo, resultando em problemas com o serviço.|
|**5.**|Contas de armazenamento em camadas|O seguinte se aplica ao usar contas de armazenamento em camadas:<ul><li> Somente há suporte para BLOBs de blocos. Blobs de página não têm suporte.</li><li>Não há suporte para instantâneo ou API de cópia.</li><li> Não há suporte para a ingestão de carga de trabalho do Hadoop `distcp` , pois ela usa a operação de cópia de forma intensiva.</li></ul>||
|**6.**|Conexão de compartilhamento NFS|Se vários processos estiverem sendo copiados para o mesmo compartilhamento e o `nolock` atributo não for usado, você poderá ver erros durante a cópia.|O `nolock` atributo deve ser passado para o comando mount para copiar arquivos para o compartilhamento NFS. Por exemplo: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**7.**|Cluster do Kubernetes|Ao aplicar uma atualização em seu dispositivo que está executando um cluster kubernetes, as máquinas virtuais kubernetes serão reiniciadas e reiniciadas. Nessa instância, somente os pods implantados com réplicas especificadas são automaticamente restaurados após uma atualização.  |Se você criou pods individuais fora de um controlador de replicação sem especificar um conjunto de réplicas, esses pods não serão restaurados automaticamente após a atualização do dispositivo. Você precisará restaurar esses pods.<br>Um conjunto de réplicas substitui os pods que são excluídos ou encerrados por qualquer motivo, como falha de nó ou atualização de nó de interrupção. Por esse motivo, recomendamos que você use um conjunto de réplicas mesmo que seu aplicativo exija apenas um único Pod.|
|**8.**|Cluster do Kubernetes|Só há suporte para kubernetes no Azure Stack Edge pro com o Helm V3 ou posterior. Para obter mais informações, acesse as perguntas frequentes [: remoção do gaveta](https://v3.helm.sh/docs/faq/).|
|**9.**|Kubernetes habilitado para Azure Arc |Para a versão GA, o kubernetes habilitado para Arc do Azure é atualizado da versão 0.1.18 para 0.2.9. Como a atualização kubernetes habilitada para o Arc do Azure não tem suporte no dispositivo Azure Stack Edge, você precisará reimplantar o kubernetes habilitado para Arc do Azure.|Siga estas etapas:<ol><li>[Aplique o software do dispositivo e as atualizações do kubernetes](azure-stack-edge-gpu-install-update.md).</li><li>Conecte-se à [interface do PowerShell do dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md).</li><li>Remova o agente de arco do Azure existente. Digite: `Remove-HcsKubernetesAzureArcAgent`.</li><li>Implante [o arco do Azure em um novo recurso](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). Não use um recurso de arco do Azure existente.</li></ol>|
|**254.**|Kubernetes habilitado para Azure Arc|Não há suporte para implantações de arco do Azure se o proxy Web estiver configurado em seu dispositivo Azure Stack Edge pro.||
|**11.**|Kubernetes |A porta 31000 é reservada para o painel do kubernetes. A porta 31001 está reservada para o registro de contêiner do Edge. Da mesma forma, na configuração padrão, os endereços IP 172.28.0.1 e 172.28.0.10, são reservados para o serviço kubernetes e o serviço DNS principal, respectivamente.|Não use IPs reservados.|
|**12.**|Kubernetes |O kubernetes atualmente não permite serviços de balanceador de vários protocolos. Por exemplo, um serviço DNS que teria que escutar em TCP e UDP. |Para solucionar essa limitação de kubernetes com MetalLB, dois serviços (um para TCP, um para UDP) podem ser criados no mesmo seletor de Pod. Esses serviços usam a mesma chave de compartilhamento e spec. loadBalancerIP para compartilhar o mesmo endereço IP. Os IPs também podem ser compartilhados se você tiver mais serviços do que os endereços IP disponíveis. <br> Para obter mais informações, consulte [compartilhamento de endereço IP](https://metallb.universe.tf/usage/#ip-address-sharing).|
|**13.**|Cluster do Kubernetes|Os módulos existentes do Marketplace Azure IoT Edge podem exigir modificações para serem executados em IoT Edge no dispositivo Azure Stack Edge.|Para obter mais informações, consulte modificar módulos de Azure IoT Edge do Marketplace para executar em Azure Stack dispositivo de borda.<!-- insert link-->|
|**140.**|Kubernetes |Não há suporte para montagens de associação baseadas em arquivo com Azure IoT Edge em kubernetes no dispositivo Azure Stack Edge.|IoT Edge usa uma camada de conversão para converter `ContainerCreate` opções em construções kubernetes. A criação de `Binds` mapas para o `hostpath` diretório e, portanto, montagens de associação baseadas em arquivo não podem ser associadas a caminhos em contêineres de IOT Edge. Se possível, mapeie o diretório pai.|
|**15.**|Kubernetes |Se você colocar seus próprios certificados para IoT Edge e adicionar esses certificados em seu dispositivo Azure Stack Edge depois que a computação estiver configurada no dispositivo, os novos certificados não serão selecionados.|Para contornar esse problema, você deve carregar os certificados antes de configurar a computação no dispositivo. Se a computação já estiver configurada, [Conecte-se à interface do PowerShell do dispositivo e execute IOT Edge comandos](azure-stack-edge-gpu-connect-powershell-interface.md#use-iotedge-commands). Reiniciar `iotedged` e `edgehub` pods.|
|**16.**|Certificados |Em determinadas instâncias, o estado do certificado na interface do usuário local pode levar vários segundos para ser atualizado. |Os cenários a seguir na interface do usuário local podem ser afetados.<ul><li>Coluna **status** na página **certificados** .</li><li>Bloco **segurança** na **página Introdução.**</li><li>Bloco **configuração** na página **visão geral** .</li></ul>  |
|**16.**|IoT Edge |Módulos implantados por meio de IoT Edge não podem usar a rede de host. | |
|**anos.**|Computação + kubernetes |Compute/kubernetes não oferece suporte a proxy da Web NTLM. ||
|**aprimora.**|Kubernetes + atualização |Versões anteriores do software, como versões 2008, têm um problema de atualização da condição de corrida que faz com que a atualização falhe com ClusterConnectionException. |O uso de compilações mais recentes deve ajudar a evitar esse problema. Se você ainda vir esse problema, a solução alternativa é tentar novamente a atualização e ela deve funcionar.|
|**20**|Internet Explorer|Se os recursos de segurança aprimorados estiverem habilitados, talvez você não consiga acessar páginas da interface do usuário da Web local. | Desabilite a segurança reforçada e reinicie o navegador.|
|**Abril.**|Painel do kubernetes | Não há suporte para o ponto de extremidade *https* do painel kubernetes com o certificado SSL. | |
|**22.**|Kubernetes |Kubernetes não dá suporte a ":" em nomes de variáveis de ambiente que são usados por aplicativos .NET. Isso também é necessário para a grade de eventos IoT Edge módulo para funcionar no dispositivo Azure Stack Edge e em outros aplicativos. Para obter mais informações, consulte a [documentação do ASP.NET Core](/aspnet/core/fundamentals/configuration/?tabs=basicconfiguration&view=aspnetcore-3.1&preserve-view=true#environment-variables).|Substitua ":" por duplo sublinhado. Para obter mais informações, consulte [kubernetes Issue](https://github.com/kubernetes/kubernetes/issues/53201)|
|**23.** |Azure Arc + cluster kubernetes |Por padrão, quando o recurso `yamls` é excluído do repositório git, os recursos correspondentes não são excluídos do cluster kubernetes.  |Para permitir a exclusão de recursos quando eles são excluídos do repositório git, defina `--sync-garbage-collection` em Arc OperatorParams. Para obter mais informações, consulte [excluir uma configuração](../azure-arc/kubernetes/use-gitops-connected-cluster.md#additional-parameters). |
|**24.**|NFS |Os aplicativos que usam montagens de compartilhamento NFS em seu dispositivo para gravar dados devem usar a gravação exclusiva. Isso garante que as gravações sejam gravadas no disco.| |
|**25.**|Configuração de computação |A configuração de computação falha em configurações de rede em que gateways ou comutadores ou roteadores respondem a solicitações de ARP (protocolo de resolução de endereço) para sistemas que não existem na rede.| |
|**26.**|Computação e kubernetes |Se kubernetes for configurado primeiro no seu dispositivo, ele reivindicará todas as GPUs disponíveis. Portanto, não é possível criar Azure Resource Manager VMs usando GPUs depois de configurar o kubernetes. |Se o dispositivo tiver duas GPUs, você poderá criar uma VM que usa a GPU e, em seguida, configurar o kubernetes. Nesse caso, o kubernetes usará a GPU 1 disponível restante. |


<!--|**18.**|Azure Private Edge Zone (Preview) |There is a known issue with Virtual Network Function VM if the VM was created on Azure Stack Edge device running earlier preview builds such as 2006/2007b and then the device was updated to 2009 GA release. The issue is that the VNF information can't be retrieved or any new VNFs can't be created unless the VNF VMs are deleted before the device is updated.  |Before you update Azure Stack Edge device to 2009 release, use the PowerShell command `get-mecvnf` followed by `remove-mecvnf <VNF guid>` to remove all Virtual Network Function VMs one at a time. After the upgrade, you will need to redeploy the same VNFs.|-->


## <a name="next-steps"></a>Próximas etapas

- [Atualizar seu dispositivo](azure-stack-edge-gpu-install-update.md)
