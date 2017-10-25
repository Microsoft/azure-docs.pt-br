
Se o problema do Azure não for resolvido neste artigo, visite os [Fóruns do Azure no MSDN e Excedente de Pilha](https://azure.microsoft.com/support/forums/). Você pode postar seu problema nesses fóruns ou usando @AzureSupport no Twitter. Além disso, você pode registrar uma solicitação de suporte do Azure selecionando **Obter suporte** no site de [suporte do Azure](https://azure.microsoft.com/support/options/) .

## <a name="general-troubleshooting-steps"></a>Etapas gerais de solução de problemas
### <a name="troubleshoot-common-allocation-failures-in-the-classic-deployment-model"></a>Solução de problemas de falhas de alocação comuns no modelo de implantação clássico
Essas etapas podem ajudar a resolver diversas falhas de alocação em máquinas virtuais:

* Redimensione a VM para um tamanho de VM diferente.<br>
    Clique em **Procurar tudo** > **Máquinas virtuais (clássicas)** > sua máquina virtual > **Configurações** > **Tamanho**. Para obter as etapas detalhadas, consulte [Redimensionar a máquina virtual](https://msdn.microsoft.com/library/dn168976.aspx).
* Exclua todas as VMs do serviço de nuvem e recrie-as.<br>
    Clique em **Procurar tudo** > **Máquinas virtuais (clássicas)** > sua máquina virtual > **Excluir**. Em seguida, clique em **Novo** > **Computação** > [imagem de máquina virtual].

### <a name="troubleshoot-common-allocation-failures-in-the-azure-resource-manager-deployment-model"></a>Solução de problemas de falhas de alocação comuns no modelo de implantação do Gerenciador de Recursos do Azure
Essas etapas podem ajudar a resolver diversas falhas de alocação em máquinas virtuais:

* Pare (desaloque) todas as VMs no mesmo conjunto de disponibilidade e reinicie cada uma delas.<br>
    Para parar: clique em **Grupos de recursos** > seu grupo de recursos > **Recursos** > seu conjunto de disponibilidade > **Máquinas Virtuais** > sua máquina virtual > **Parar**.
  
    Depois de parar todas as VMs, selecione a primeira VM e clique em **Iniciar**.

## <a name="background-information"></a>Informações básicas
### <a name="how-allocation-works"></a>Como funciona a alocação
Os servidores são particionados em clusters nos datacenters do Microsoft Azure. Normalmente, uma tentativa de solicitação de alocação é feita em vários clusters, mas é possível que determinadas restrições da solicitação de alocação forcem a plataforma do Azure a repeti-la em um único cluster. Neste artigo, chamaremos isso de "fixada a um cluster". O diagrama 1 a seguir ilustra o caso de uma tentativa de alocação normal feita em vários clusters. O diagrama 2 ilustra o caso de uma alocação que foi fixada ao Cluster 2 por se tratar do local de hospedagem do Serviço de Nuvem CS_1 ou do conjunto de disponibilidade existente.
![Diagrama de alocação](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Motivos das falhas de alocação
Quando a solicitação de alocação é fixada a um cluster, é mais provável que haja falha na localização de recursos gratuitos, pois o pool de recursos disponíveis é menor. Além disso, quando a solicitação de alocação é fixada a um cluster, mas não há suporte para o tipo de recurso solicitado ao cluster, a solicitação falha mesmo que o cluster tenha recursos livres. O diagrama 3 a seguir demonstra o caso em que uma alocação fixada falha porque o único cluster candidato não dispõe de recursos gratuitos. O diagrama 4 demonstra o caso em que uma alocação fixada falha porque o único cluster candidato não é compatível com o tamanho da VM solicitado, mesmo que o cluster disponha de recursos livres.

![Falha na alocação fixada](./media/virtual-machines-common-allocation-failure/Allocation2.png)

## <a name="detailed-troubleshoot-steps-specific-allocation-failure-scenarios-in-the-classic-deployment-model"></a>As etapas de solução de problemas detalhadas de cenários de falha de alocação específica no modelo de implantação clássico
Veja os cenários comuns de alocação que causam a fixação de uma solicitação de alocação. Vamos nos aprofundar em cada cenário posteriormente neste artigo.

* Redimensionar uma VM ou adicionar VMs ou instâncias de função a um serviço de nuvem existente
* Reiniciar VMs parcialmente paradas (desalocadas)
* Reiniciar VMs totalmente paradas (desalocadas)
* Implantações de preparo/produção (apenas plataforma como serviço)
* Grupo de afinidades (proximidade de serviço/VM)
* Rede virtual com base em grupo de afinidade

Caso receba um erro de alocação, verifique se os cenários descritos se aplicam ao seu erro. Use o erro de alocação retornado pela plataforma do Azure para identificar o cenário correspondente. Caso a solicitação seja fixada, remova algumas das restrições de fixação para abrir a solicitação para outros clusters e aumentar a probabilidade de êxito da alocação.

Em geral, se o erro não indicar "não há suporte para o tamanho de VM solicitado", você pode sempre tentar novamente mais tarde, já que os recursos necessários podem ter sido liberados no cluster para atender à sua solicitação. Se o problema for que o tamanho da VM solicitada não tem suporte, tente usar um tamanho de VM diferente. Caso contrário, a única opção é remover a restrição de fixação.

Dois cenários de falhas comuns estão relacionados aos grupos de afinidades. Anteriormente, o grupo de afinidades era usado para proporcionar uma estreita proximidade com as instâncias de serviço/VMs ou para permitir a criação de uma rede virtual. Com a introdução das redes virtuais regionais, não é mais necessário usar o grupo de afinidades para criar uma rede virtual. Com a redução da latência da rede na infraestrutura do Azure, a recomendação de uso de grupos de afinidades a proximidade de serviço/VM foi alterada.

O diagrama 5 a seguir apresenta a taxonomia dos cenários de alocação (afixados).
![Taxonomia de alocação fixada](./media/virtual-machines-common-allocation-failure/Allocation3.png)

> [!NOTE]
> Descrevemos o erro de forma reduzida em cada cenário de alocação. Confira [Pesquisa de cadeia de caracteres de erro](#Error string lookup) para obter cadeias de caracteres de erro detalhadas.
> 
> 

## <a name="allocation-scenario-resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Cenário de alocação: redimensionar uma VM ou adicionar VMs ou instâncias de função a um serviço de nuvem existente
**Erro**

Upgrade_VMSizeNotSupported ou GeneralError

**Causa de fixação de cluster**

A solicitação para redimensionar uma VM ou adicionar uma VM ou instância de função a um serviço de nuvem existente deve ser tentada no cluster original que hospeda o serviço de nuvem existente. A criação de um novo serviço de nuvem permite que a plataforma do Azure localize outro cluster com recursos livres ou que dê suporte para o tamanho de VM solicitado.

**Solução alternativa**

Se o erro for Upgrade_VMSizeNotSupported*, tente usar um tamanho de VM diferente. Quando não for possível usar um tamanho de VM diferente, mas for aceitável usar um endereço VIP (IP virtual) diferente, crie um novo serviço de nuvem para hospedar a nova VM e adicione-o à rede virtual regional na qual as VMs existentes estão em execução. Caso o serviço de nuvem existente não use a rede virtual regional, ainda é possível criar uma nova rede virtual para o novo serviço de nuvem e conectar sua [rede virtual existente à nova rede virtual](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Saiba mais sobre as [redes virtuais regionais](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Se o erro for GeneralError*, é provável que o cluster tenha suporte para o tipo de recurso (como um determinado tamanho de VM), embora não tenha recursos livres no momento. Semelhante ao cenário acima, adicione o recurso de computação desejado criando um novo serviço de nuvem (observe que o novo serviço de nuvem deve usar um VIP diferente) e use a rede virtual regional para conectar seus serviços de nuvem.

## <a name="allocation-scenario-restart-partially-stopped-deallocated-vms"></a>Cenário de alocação: reiniciar VMs parcialmente paradas (desalocadas)
**Erro**

GeneralError*

**Causa de fixação de cluster**

A desalocação parcial significa que você parou (desalocou) uma ou mais, mas não todas, VMs no serviço de nuvem. Quando você para (desaloca) uma VM, os recursos associados são liberados. A renicialização da VM parada (desalocada) é, portanto, uma nova solicitação de alocação. Reiniciar as VMs em um serviço de nuvem parcialmente desalocado é equivalente a adicionar VMs a um serviço de nuvem existente. A solicitação de alocação deve ser tentada no cluster original que hospeda o serviço de nuvem existente. A criação de um serviço de nuvem diferente permite que a plataforma do Azure localize outro cluster que tem recursos livres ou que dê suporte para o tamanho de VM solicitado.

**Solução alternativa**

Se for aceitável usar um VIP diferente, exclua as VMs paradas (desalocadas) (mas mantenha os discos associados) e adicione-as novamente através de um serviço de nuvem diferente. Use a rede virtual regional para se conectar aos serviços de nuvem:

* Caso o serviço de nuvem existente use uma rede virtual regional, basta adicionar o novo serviço de nuvem à mesma rede virtual.
* Caso o serviço de nuvem existente não use a rede virtual regional, crie uma nova rede virtual para o novo serviço de nuvem e [conecte sua rede virtual existente à nova rede virtual](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Saiba mais sobre as [redes virtuais regionais](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="allocation-scenario-restart-fully-stopped-deallocated-vms"></a>Cenário de alocação: reiniciar VMs totalmente paradas (desalocadas)
**Erro**

GeneralError*

**Causa de fixação de cluster**

A desalocação total significa que você parou (desalocou) todas as VMs de um serviço de nuvem. As solicitações de alocação para reiniciar essas VMs devem ser tentadas no cluster original que hospeda o serviço de nuvem. A criação de um novo serviço de nuvem permite que a plataforma do Azure localize outro cluster com recursos livres ou que dê suporte para o tamanho de VM solicitado.

**Solução alternativa**

Se for aceitável usar um VIP diferente, exclua as VMs originais paradas (desalocadas) (mas mantenha os discos associados) e exclua o serviço de nuvem correspondente (os recursos de computação associados já foram liberados quando você parou ou desalocou as VMs). Crie um novo serviço de nuvem para adicionar novamente as VMs.

## <a name="allocation-scenario-stagingproduction-deployments-platform-as-a-service-only"></a>Cenário de alocação: implantações de preparo/produção (apenas plataforma como serviço)
**Erro**

New_General* ou New_VMSizeNotSupported*

**Causa de fixação de cluster**

A implantação de preparo e a implantação de produção de um serviço de nuvem são hospedadas no mesmo cluster. Quando você adiciona a segunda implantação, a solicitação de alocação correspondente é tentada no mesmo cluster que hospeda a primeira implantação.

**Solução alternativa**

Exclua a primeira implantação e o serviço de nuvem original e reimplante o serviço de nuvem. Esta ação pode direcionar a primeira implantação para um cluster que dispõe de recursos livres adequados para as duas implantações ou que dê suporte para os tamanhos de VM solicitados.

## <a name="allocation-scenario-affinity-group-vmservice-proximity"></a>Cenário de alocação: grupo de afinidades (proximidade de serviço/VM)
**Erro**

New_General* ou New_VMSizeNotSupported*

**Causa de fixação de cluster**

Os recursos de computação atribuídos a um grupo de afinidades estão vinculados a um único cluster. As novas solicitações de recursos de computação desse grupo de afinidades são tentadas no mesmo cluster que hospeda os recursos existentes. Isso ocorre se os novos recursos forem criados por meio de um novo serviço de nuvem ou um serviço de nuvem existente.

**Solução alternativa**

Se um grupo de afinidades não for necessário, não use o grupo de afinidades ou agrupe os recursos de computação em vários grupos de afinidades.

## <a name="allocation-scenario-affinity-group-based-virtual-network"></a>Cenário de alocação: rede virtual com base em grupo de afinidades
**Erro**

New_General* ou New_VMSizeNotSupported*

**Causa de fixação de cluster**

Antes de as redes virtuais regionais serem introduzidas, era necessário associar uma rede virtual ao grupo de afinidades. Como resultado, os recursos de computação colocados em um grupo de afinidades são vinculados pelas mesmas restrições descritas na seção anterior, "Cenário de alocação: grupo de afinidades (proximidade de serviço/VM)". Os recursos de computação são vinculados a um cluster.

**Solução alternativa**

Caso você não precise de um grupo de afinidades, crie uma nova rede virtual regional para os novos recursos que está adicionando e [conecte sua rede virtual existente à nova rede virtual](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Saiba mais sobre as [redes virtuais regionais](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Como alternativa, é possível [migrar a rede virtual baseada em grupo de afinidades para uma rede virtual regional](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)e depois adicionar novamente os recursos desejados.

## <a name="detailed-troubleshooting-steps-specific-allocation-failure-scenarios-in-the-azure-resource-manager-deployment-model"></a>As etapas de solução de problemas detalhados em cenários específicos de falha na alocação no modelo de implantação do Azure Resource Manager
Veja os cenários comuns de alocação que causam a fixação de uma solicitação de alocação. Vamos nos aprofundar em cada cenário posteriormente neste artigo.

* Redimensionar uma VM ou adicionar VMs ou instâncias de função a um serviço de nuvem existente
* Reiniciar VMs parcialmente paradas (desalocadas)
* Reiniciar VMs totalmente paradas (desalocadas)

Caso receba um erro de alocação, verifique se os cenários descritos se aplicam ao seu erro. Use o erro de alocação retornado pela plataforma do Azure para identificar o cenário correspondente. Caso a solicitação seja fixada em um cluster existente, remova algumas das restrições de fixação para abrir a solicitação para outros clusters e aumentar a probabilidade de êxito da alocação.

Em geral, se o erro não indicar "não há suporte para o tamanho de VM solicitado", você pode sempre tentar novamente mais tarde, já que os recursos necessários podem ter sido liberados no cluster para atender à sua solicitação. Se o problema for que o tamanho da VM solicitada não tem suporte, consulte as soluções alternativas abaixo.

## <a name="allocation-scenario-resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Cenário de alocação: redimensionar uma VM ou adicionar VMs a um conjunto de disponibilidade existente
**Erro**

Upgrade_VMSizeNotSupported* ou GeneralError*

**Causa de fixação de cluster**

A solicitação de redimensionar ou adicionar uma VM a um conjunto de disponibilidade existente deve ser tentada no cluster original que hospeda este conjunto. A criação de um novo conjunto de disponibilidade permite que a plataforma do Microsoft Azure localize outro cluster com recursos livres ou que dê suporte para os tamanhos de VM solicitados.

**Solução alternativa**

Se o erro for Upgrade_VMSizeNotSupported*, tente usar um tamanho de VM diferente. Se não for possível usar um tamanho de VM diferente, pare todas as VMs no conjunto de disponibilidade. Em seguida, você pode alterar o tamanho da máquina virtual que alocará a VM a um cluster que dê suporte para o tamanho de VM desejado.

Se o erro for GeneralError*, é provável que o cluster tenha suporte para o tipo de recurso (como um determinado tamanho de VM), embora não tenha recursos livres no momento. Quando a VM puder fazer parte de um conjunto de disponibilidade diferente, crie uma nova VM em um conjunto de disponibilidade diferente (na mesma região). Essa nova VM pode ser adicionada à mesma rede virtual.  

## <a name="allocation-scenario-restart-partially-stopped-deallocated-vms"></a>Cenário de alocação: reiniciar VMs parcialmente paradas (desalocadas)
**Erro**

GeneralError*

**Causa de fixação de cluster**

A desalocação parcial significa que você parou (desalocou) uma ou mais, mas não todas, VMs em um conjunto de disponibilidade. Quando você para (desaloca) uma VM, os recursos associados são liberados. A renicialização da VM parada (desalocada) é, portanto, uma nova solicitação de alocação. Reiniciar as VMs em um conjunto de disponibilidade parcialmente desalocado é equivalente a adicionar VMs a um conjunto de disponibilidade existente. A solicitação de alocação deve ser tentada no cluster original que hospeda o conjunto de disponibilidade existente.

**Solução alternativa**

Pare todas as VMs no conjunto de disponibilidade antes de reiniciar a primeira. Isso garante que uma nova tentativa de alocação seja executada e que um novo cluster com capacidade disponível possa ser selecionado.

## <a name="allocation-scenario-restart-fully-stopped-deallocated"></a>Cenário de alocação: reiniciar a parada total (desalocada)
**Erro**

GeneralError*

**Causa de fixação de cluster**

A desalocação total significa que você parou (desalocou) todas as VMs em um conjunto de disponibilidade. A solicitação de alocação para reiniciar essas VMs se destina a todos os clusters compatíveis com o tamanho desejado.

**Solução alternativa**

Selecione um novo tamanho de VM para alocar. Se isso não funcionar, tente novamente mais tarde.

<a name="Error string lookup"></a>

## <a name="error-string-lookup"></a>Pesquisa de cadeia de caracteres de erro
**New_VMSizeNotSupported***

"O tamanho de VM (ou combinação de tamanhos de VM) necessário para essa implantação não pode ser provisionado devido a restrições de solicitação de implantação. Se possível, tente relaxar as restrições, como associações de rede virtual, implantação em um serviço hospedado sem nenhuma outra implantação nele e em um grupo de afinidades diferente ou sem nenhum grupo de afinidades ou tente implantar em uma região diferente."

**New_General***

"Falha na alocação; não é possível atender às restrições na solicitação. A implantação solicitada do novo serviço está associada a um grupo de afinidades ou é destinada a uma rede virtual, ou há uma implantação existente para esse serviço hospedado. Essas condições restringem a nova implantação para os recursos específicos do Microsoft Azure. Tente novamente mais tarde ou tente reduzir o tamanho da VM ou o número de instâncias de função. De forma alternativa, remova as restrições mencionadas acima ou tente implantar em uma região diferente, se possível."

**Upgrade_VMSizeNotSupported***

"Não é possível atualizar a implantação. O tamanho de VM XXX solicitado pode não estar disponível nos recursos que são compatíveis com a implantação existente. Tente novamente mais tarde, tente com um tamanho de VM diferente ou com um número de instâncias de função menor, ou crie uma implantação em um serviço hospedado vazio com um novo grupo de afinidades ou sem nenhuma associação com grupo de afinidades."

**GeneralError***

"O servidor encontrou um erro interno. Repita a solicitação." Ou "Falha na produção de uma alocação para o serviço."

