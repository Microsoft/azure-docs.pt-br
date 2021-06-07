---
title: Personalizar a configuração de nó para pools de nós do AKS (serviço kubernetes do Azure) (versão prévia)
description: Saiba como personalizar a configuração nos nós de cluster do AKS (serviço de kubernetes do Azure) e pools de nós.
ms.service: container-service
ms.topic: article
ms.date: 12/03/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 7b39242a7d7208b33a070e86088b25e9414ead04
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101714622"
---
# <a name="customize-node-configuration-for-azure-kubernetes-service-aks-node-pools-preview"></a>Personalizar a configuração de nó para pools de nó do AKS (serviço de kubernetes do Azure) (versão prévia)

Personalizar a configuração de nó permite que você configure ou ajuste as configurações do sistema operacional (SO) ou os parâmetros kubelet para corresponder às necessidades das cargas de trabalho. Ao criar um cluster AKS ou adicionar um pool de nós ao cluster, você pode personalizar um subconjunto das configurações de so e kubelet usadas com frequência. Para definir configurações além desse subconjunto, [use um daemon definido para personalizar suas configurações necessárias sem o suporte do perder AKs para seus nós](support-policies.md#shared-responsibility).

## <a name="register-the-customnodeconfigpreview-preview-feature"></a>Registrar o `CustomNodeConfigPreview` recurso de visualização

Para criar um cluster do AKS ou um pool de nós que possa personalizar os parâmetros do kubelet ou as configurações do sistema operacional, você deve habilitar o `CustomNodeConfigPreview` sinalizador de recurso em sua assinatura.

Registre o `CustomNodeConfigPreview` sinalizador de recurso usando o comando [AZ Feature Register][az-feature-register] , conforme mostrado no exemplo a seguir:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "CustomNodeConfigPreview"
```

Demora alguns minutos para o status exibir *Registrado*. Verifique o status do registro usando o comando [AZ Feature List][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/CustomNodeConfigPreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registro do provedor de recursos *Microsoft. ContainerService* usando o comando [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI aks-preview

Para criar um cluster AKS ou um pool de nós que possa personalizar os parâmetros do kubelet ou as configurações do sistema operacional, você precisará da extensão de CLI do Azure mais recente do *AKs-Preview* . Instale a extensão de CLI do Azure *de AKs-Preview* usando o comando [AZ Extension Add][az-extension-add] . Ou instale todas as atualizações disponíveis usando o comando [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

## <a name="use-custom-node-configuration"></a>Usar configuração de nó personalizado

### <a name="kubelet-custom-configuration"></a>Configuração personalizada do Kubelet

Os parâmetros de Kubelet com suporte e os valores aceitos estão listados abaixo.

| Parâmetro | Valores permitidos/intervalo | Padrão | Descrição |
| --------- | ----------------------- | ------- | ----------- |
| `cpuManagerPolicy` | nenhum, estático | none | A política estática permite que contêineres em [pods garantidos](https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/) com CPU de inteiros solicitem acesso a CPUs exclusivas no nó. |
| `cpuCfsQuota` | verdadeiro, falso | true |  Habilitar/desabilitar a imposição de cota do CFS da CPU para contêineres que especificam limites de CPU. | 
| `cpuCfsQuotaPeriod` | Intervalo em milissegundos (MS) | `100ms` | Define o valor do período de cota do CFS da CPU. | 
| `imageGcHighThreshold` | 0-100 | 85 | A porcentagem de uso do disco após a qual a coleta de lixo da imagem é sempre executada. Uso mínimo do disco que **irá** disparar a coleta de lixo. Para desabilitar a coleta de lixo de imagem, defina como 100. | 
| `imageGcLowThreshold` | 0-100, não é maior que `imageGcHighThreshold` | 80 | A porcentagem de uso do disco antes da qual a coleta de lixo da imagem nunca é executada. Uso mínimo do disco que **pode** disparar a coleta de lixo. |
| `topologyManagerPolicy` | nenhum, melhor esforço, restrito, nó único | none | Otimizar o alinhamento de nó NUMA, veja mais [aqui](https://kubernetes.io/docs/tasks/administer-cluster/topology-manager/). Somente kubernetes v 18E +. |
| `allowedUnsafeSysctls` | `kernel.shm*`, `kernel.msg*`, `kernel.sem`, `fs.mqueue.*`, `net.*` | Nenhum | Lista permitida de padrões de sysctl sysctls ou inseguros. | 

### <a name="linux-os-custom-configuration"></a>Configuração personalizada do SO Linux

As configurações de so com suporte e os valores aceitos estão listados abaixo.

#### <a name="file-handle-limits"></a>Limites de identificador de arquivo

Quando você está servindo muito tráfego, é comum que o tráfego que você está servindo seja proveniente de um grande número de arquivos locais. Você pode ajustar as configurações de kernel abaixo e os limites internos para permitir que você manipule mais, com o custo de alguma memória do sistema.

| Configuração | Valores permitidos/intervalo | Padrão | Descrição |
| ------- | ----------------------- | ------- | ----------- |
| `fs.file-max` | 8192-12000500 | 709620 | Número máximo de identificadores de arquivo que o kernel do Linux alocará, aumentando esse valor, você pode aumentar o número máximo de arquivos abertos permitidos. |
| `fs.inotify.max_user_watches` | 781250-2097152 | 1048576 | Número máximo de inspeções de arquivo permitidas pelo sistema. Cada *inspeção* tem aproximadamente 90 bytes em um kernel de 32 bits e aproximadamente 160 bytes em um kernel de 64 bits. | 
| `fs.aio-max-nr` | 65536-6553500 | 65536 | O AIO-NR mostra o número atual do sistema de solicitações de e/s assíncronas. AIO-Max-NR permite que você altere o valor máximo AIO-NR pode aumentar para. |
| `fs.nr_open` | 8192-20000500 | 1048576 | O número máximo de identificadores de arquivo que um processo pode alocar. |


#### <a name="socket-and-network-tuning"></a>Ajuste de soquete e rede

Para nós de agente, que são esperados para lidar com números muito grandes de sessões simultâneas, você pode usar o subconjunto de opções de TCP e de rede abaixo que você pode ajustar por pool de nós. 

| Configuração | Valores permitidos/intervalo | Padrão | Descrição |
| ------- | ----------------------- | ------- | ----------- |
| `net.core.somaxconn` | 4096-3240000 | 16384 | Número máximo de solicitações de conexão que podem ser enfileiradas para qualquer soquete de escuta específico. Um limite superior para o valor do parâmetro backlog passado para a função [Listen (2)](http://man7.org/linux/man-pages/man2/listen.2.html) . Se o argumento da pendência for maior que o `somaxconn` , ele será silenciosamente truncado para esse limite.
| `net.core.netdev_max_backlog` | 1000-3240000 | 1000 | Número máximo de pacotes, em fila no lado de entrada, quando a interface recebe pacotes mais rápidos do que o kernel pode processá-los. |
| `net.core.rmem_max` | 212992-134217728 | 212992 | O tamanho máximo do buffer de soquete de recebimento em bytes. |
| `net.core.wmem_max` | 212992-134217728 | 212992 | O tamanho máximo do buffer de soquete de envio em bytes. | 
| `net.core.optmem_max` | 20480-4194304 | 20480 | Tamanho máximo de buffer auxiliar (buffer de memória de opção) permitido por soquete. A memória de opção de soquete é usada em alguns casos para armazenar estruturas extras relacionadas ao uso do soquete. | 
| `net.ipv4.tcp_max_syn_backlog` | 128-3240000 | 16384 | O número máximo de solicitações de conexão enfileiradas que ainda não receberam uma confirmação do cliente que está se conectando. Se esse número for excedido, o kernel começará a descartar as solicitações. |
| `net.ipv4.tcp_max_tw_buckets` | 8000-1440000 | 32768 | Número máximo de `timewait` soquetes mantidos pelo sistema simultaneamente. Se esse número for excedido, o soquete de espera de tempo será destruído imediatamente e o aviso será impresso. |
| `net.ipv4.tcp_fin_timeout` | 5 - 120 | 60 | O período de tempo que uma conexão órfã (não é mais referenciada por qualquer aplicativo) permanecerá no estado de FIN_WAIT_2 antes de ser anulada na extremidade local. |
| `net.ipv4.tcp_keepalive_time` | 30 - 432000 | 7200 | Com que frequência o TCP envia `keepalive` mensagens quando `keepalive` está habilitado. |
| `net.ipv4.tcp_keepalive_probes` | 1 - 15 | 9 | Quantas `keepalive` investigações o TCP envia, até que decida que a conexão foi interrompida. |
| `net.ipv4.tcp_keepalive_intvl` | 1 - 75 | 75 | Com que frequência as investigações são enviadas. Multiplicado por `tcp_keepalive_probes` ele torna-se o tempo para eliminar uma conexão que não está respondendo, após o início das investigações. | 
| `net.ipv4.tcp_tw_reuse` | 0 ou 1 | 0 | Permita a reutilização `TIME-WAIT` de soquetes para novas conexões quando ele estiver seguro do ponto de vista do protocolo. | 
| `net.ipv4.ip_local_port_range` | Primeiro: 1024-60999 e último: 32768-65000] | Primeiro: 32768 e último: 60999 | O intervalo de portas local que é usado pelo tráfego TCP e UDP para escolher a porta local. Composto de dois números: o primeiro número é a primeira porta local permitida para o tráfego TCP e UDP no nó do agente, o segundo é o último número da porta local. | 
| `net.ipv4.neigh.default.gc_thresh1`|  128-80000 | 4096 | Número mínimo de entradas que podem estar no cache ARP. A coleta de lixo não será disparada se o número de entradas estiver abaixo dessa configuração. | 
| `net.ipv4.neigh.default.gc_thresh2`|  512-90000 | 8192 | Número máximo flexível de entradas que podem estar no cache ARP. Essa configuração é, de forma imprecisa, a mais importante, pois a coleta de lixo ARP será disparada cerca de 5 segundos depois de atingir esse máximo flexível. |
| `net.ipv4.neigh.default.gc_thresh3`|  1024-100000 | 16384 | Número máximo de entradas no cache ARP. |
| `net.netfilter.nf_conntrack_max` | 131072-589824 | 131072 | `nf_conntrack` é um módulo que controla as entradas de conexão para NAT no Linux. O `nf_conntrack` módulo usa uma tabela de hash para registrar o registro de *conexão estabelecido* do protocolo TCP. `nf_conntrack_max` é o número máximo de nós na tabela de hash, ou seja, o número máximo de conexões com suporte no `nf_conntrack` módulo ou o tamanho da tabela de controle de conexão. | 
| `net.netfilter.nf_conntrack_buckets` | 65536-147456 | 65536 | `nf_conntrack` é um módulo que controla as entradas de conexão para NAT no Linux. O `nf_conntrack` módulo usa uma tabela de hash para registrar o registro de *conexão estabelecido* do protocolo TCP. `nf_conntrack_buckets` é o tamanho da tabela de hash. | 

#### <a name="worker-limits"></a>Limites de trabalho

Como os limites do descritor de arquivo, o número de trabalhos ou threads que um processo pode criar são limitados por uma configuração de kernel e limites de usuário. O limite de usuários em AKS é ilimitado. 

| Configuração | Valores permitidos/intervalo | Padrão | Descrição |
| ------- | ----------------------- | ------- | ----------- |
| `kernel.threads-max` | 20 - 513785 | 55601 | Os processos podem criar threads de trabalho. O número máximo de todos os threads que podem ser criados é definido com a configuração do kernel `kernel.threads-max` . | 

#### <a name="virtual-memory"></a>Memória virtual

As configurações abaixo podem ser usadas para ajustar a operação do subsistema de memória virtual (VM) do kernel do Linux e do `writeout` de dados sujos em disco.

| Configuração | Valores permitidos/intervalo | Padrão | Descrição |
| ------- | ----------------------- | ------- | ----------- |
| `vm.max_map_count` |  65530-262144 | 65530 | Esse arquivo contém o número máximo de áreas de mapa de memória que um processo pode ter. As áreas de mapa de memória são usadas como um efeito colateral de chamar `malloc` , diretamente por `mmap` ,, e e `mprotect` `madvise` também ao carregar bibliotecas compartilhadas. | 
| `vm.vfs_cache_pressure` | 1 - 500 | 100 | Esse valor de porcentagem controla a tendência do kernel de recuperar a memória, que é usada para armazenar em cache os objetos de diretório e inode. |
| `vm.swappiness` | 0–100 | 60 | Esse controle é usado para definir o quão agressivo o kernel vai trocar páginas de memória. Valores mais altos aumentarão a agressividade, os valores mais baixos diminuirão a quantidade de permuta. Um valor de 0 instrui o kernel a não iniciar a permuta até que a quantidade de páginas livres e com backup em arquivo seja menor que a marca d' água alta em uma zona. | 
| `swapFileSizeMB` | 1 MB-Tamanho do [disco temporário](../virtual-machines/managed-disks-overview.md#temporary-disk) (/dev/sdb) | Nenhum | SwapFileSizeMB especifica o tamanho em MB de um arquivo de permuta que será criado nos nós de agente deste pool de nós. | 
| `transparentHugePageEnabled` | `always`, `madvise`, `never` | `always` | O [Hugepages transparente](https://www.kernel.org/doc/html/latest/admin-guide/mm/transhuge.html#admin-guide-transhuge) é um recurso de kernel do Linux destinado a melhorar o desempenho, fazendo uso mais eficiente do hardware de mapeamento de memória do processador. Quando habilitada, o kernel tenta alocar `hugepages` sempre que possível e qualquer processo do Linux receberá páginas de 2 MB se a `mmap` região for 2 MB naturalmente alinhada. Em determinados casos em que o `hugepages` sistema é habilitado, os aplicativos podem acabar alocando mais recursos de memória. Um aplicativo pode `mmap` ser uma região grande, mas apenas toque 1 byte, nesse caso, uma página de 2 MB pode ser alocada em vez de uma página de 4K sem um bom motivo. Esse cenário é o motivo pelo qual é possível desabilitar `hugepages` todo o sistema ou apenas tê-los dentro de `MADV_HUGEPAGE madvise` regiões. | 
| `transparentHugePageDefrag` | `always`, `defer`, `defer+madvise`, `madvise`, `never` | `madvise` | Esse valor controla se o kernel deve fazer uso agressivo da compactação de memória para tornar mais `hugepages` disponível. | 

> [!IMPORTANT]
> Para facilitar a pesquisa e a legibilidade, as configurações do sistema operacional são exibidas neste documento por seu nome, mas devem ser adicionadas ao arquivo JSON de configuração ou à API AKS usando a [Convenção de capitalização CamelCase](/dotnet/standard/design-guidelines/capitalization-conventions).

Crie um `kubeletconfig.json` arquivo com o seguinte conteúdo:

```json
{
 "cpuManagerPolicy": "static",
 "cpuCfsQuota": true,
 "cpuCfsQuotaPeriod": "200ms",
 "imageGcHighThreshold": 90,
 "imageGcLowThreshold": 70,
 "topologyManagerPolicy": "best-effort",
 "allowedUnsafeSysctls": [
  "kernel.msg*",
  "net.*"
],
 "failSwapOn": false
}
```
Crie um `linuxosconfig.json` arquivo com o seguinte conteúdo:

```json
{
 "transparentHugePageEnabled": "madvise",
 "transparentHugePageDefrag": "defer+madvise",
 "swapFileSizeMB": 1500,
 "sysctls": {
  "netCoreSomaxconn": 163849,
  "netIpv4TcpTwReuse": true,
  "netIpv4IpLocalPortRange": "32000 60000"
 }
}
```

Crie um novo cluster especificando as configurações de kubelet e so usando os arquivos JSON criados na etapa anterior. 

> [!NOTE]
> Ao criar um cluster, você pode especificar a configuração do kubelet, a configuração do sistema operacional ou ambos. Se você especificar uma configuração ao criar um cluster, somente os nós no pool de nós inicial terão essa configuração aplicada. As configurações não configuradas no arquivo JSON manterão o valor padrão.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json --linux-os-config ./linuxosconfig.json
```

Adicione um novo pool de nós especificando os parâmetros Kubelet usando o arquivo JSON que você criou.

> [!NOTE]
> Ao adicionar um pool de nós a um cluster existente, você pode especificar a configuração do kubelet, a configuração do sistema operacional ou ambos. Se você especificar uma configuração ao adicionar um pool de nós, somente os nós no novo pool de nós terão essa configuração aplicada. As configurações não configuradas no arquivo JSON manterão o valor padrão.

```azurecli
az aks nodepool add --name mynodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json
```

## <a name="next-steps"></a>Próximas etapas

- Saiba [como configurar o cluster AKs](cluster-configuration.md).
- Saiba como [atualizar as imagens de nó](node-image-upgrade.md) no cluster.
- Confira [Atualizar um cluster do AKS (Serviço de Kubernetes do Azure)](upgrade-cluster.md) para saber como atualizar o cluster para a versão mais recente do Kubernetes.
- Confira a lista de [Perguntas frequentes sobre o AKS](faq.md) para encontrar respostas a algumas perguntas comuns sobre o AKS.

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-control-plane-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[upgrade-cluster]: upgrade-cluster.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade


<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why