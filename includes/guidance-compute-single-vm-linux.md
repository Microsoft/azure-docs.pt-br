Este artigo descreve um conjunto de práticas comprovadas para a execução de uma VM (máquina virtual) do Linux no Azure, considerando a escalabilidade, disponibilidade, capacidade de gerenciamento e segurança. O Azure dá suporte à execução de várias distribuições Linux populares, incluindo CentOS, Debian, Red Hat Enterprise, Ubuntu e FreeBSD. Para obter mais informações, veja [Azure e Linux][azure-linux].

> [!NOTE]
> O Azure tem dois modelos de implantação diferentes: [Gerenciador de Recursos][resource-manager-overview] e clássico. Este artigo usa o Gerenciador de Recursos, recomendado pela Microsoft para novas implantações.
> 
> 

Não recomendamos usar uma única VM para cargas de trabalho críticas, pois elas criam um ponto único de falha. Para obter maior disponibilidade, é necessário implantar várias VMs em um [conjunto de disponibilidade][availability-set]. Para saber mais, confira [Executando várias VMs no Azure][multi-vm]. 

## <a name="architecture-diagram"></a>Diagrama da arquitetura
O provisionamento de uma VM no Azure envolve mais partes móveis do que apenas a própria VM. Há elementos de computação, rede e armazenamento que você precisa considerar.

> Um documento do Visio que inclui esse diagrama da arquitetura está disponível para download no [Centro de download da Microsoft][visio-download]. Este diagrama está na página "Computação - VM única".
> 
> 

![[0]][0]

* **Grupo de recursos.** Um [*grupo de recursos*][resource-manager-overview] é um contêiner que armazena os recursos relacionados. Crie um grupo de recursos para armazenar os recursos desta VM.
* **VM**. Você pode provisionar uma VM de uma lista de imagens publicadas ou de um arquivo VHD (disco rígido virtual) que carrega no Armazenamento de Blobs do Azure.
* **Disco do sistema operacional.** O disco do sistema operacional é um VHD armazenado no [Armazenamento do Azure][azure-storage]. Isso significa que ele persistirá mesmo se a máquina host falhar. O disco do sistema operacional é `/dev/sda1`.
* **Disco temporário.** A VM é criada com um disco temporário. Esse disco é armazenado em uma unidade física no computador host. *Não* é salvo no Armazenamento do Azure e pode ser excluído durante a reinicialização e outros eventos de ciclo de vida da VM. Use esse disco somente para dados temporários, como arquivos de paginação ou de permuta. O disco temporário é `/dev/sdb1` e é montado em `/mnt/resource` ou `/mnt`.
* **Discos de dados.** Um [disco de dados][data-disk] é um VHD persistente usado para dados de aplicativo. Os discos de dados são armazenados no Armazenamento do Azure, como o disco do sistema operacional.
* **Rede Virtual e sub-rede.** Cada VM no Azure é implantada em uma Rede Virtual, que é dividida em sub-redes.
* **Endereço IP público.** Um endereço IP público é necessário para se comunicar com a VM&mdash;, por exemplo, via SSH.
* **NIC (adaptador de rede)**. A NIC permite que a VM se comunique com a rede virtual.
* **NSG (grupo de segurança de rede)**. O [NSG][nsg] é usado para permitir/negar o tráfego de rede para a sub-rede. É possível associar um NSG a uma NIC individual ou a uma sub-rede. Se você associá-lo a uma sub-rede, as regras do NSG se aplicarão a todas as VMs na sub-rede.
* **Diagnósticos.** O registro de diagnóstico é crucial para o gerenciamento e solução de problemas da VM.

## <a name="recommendations"></a>Recomendações

As seguintes recomendações aplicam-se à maioria dos cenários. Siga estas recomendações, a menos que você tenha um requisito específico que as substitua. 

### <a name="vm-recommendations"></a>Recomendações de VM

O Azure oferece vários tamanhos de máquina virtual diferente, mas são recomendáveis as séries DS e GS, pois esses tamanhos de máquina dão suporte ao [Armazenamento Premium][premium-storage]. Selecione um desses tamanhos de máquina, a menos que você tenha uma carga de trabalho especializada, como a computação de alto desempenho. Para obter detalhes, confira [tamanhos das máquinas virtuais][virtual-machine-sizes].

Se você estiver movendo uma carga de trabalho existente para o Azure, deverá começar com o tamanho da VM que mais se aproxima de seus servidores locais. Em seguida, meça o desempenho da carga de trabalho real com relação à CPU, memória e operações de entrada/saída de disco e ajuste o tamanho, se necessário. Se precisar de várias NICs para sua VM, lembre-se de que o número máximo de NICs é uma função do [tamanho da VM][vm-size-tables].

Quando você provisiona a VM e outros recursos, é necessário especificar uma região. Em geral, escolha uma região mais próxima de seus usuários internos ou de seus clientes. No entanto, nem todos os tamanhos de VM estão disponíveis em todas as regiões. Para obter detalhes, confira [Serviços por região][services-by-region]. Para listar os tamanhos de VM disponíveis em determinada região, execute o seguinte comando da CLI (interface de linha de comando) do Azure:

```
azure vm sizes --location <location>
```

Para obter informações sobre como escolher uma imagem da VM publicada, confira [Selecionar imagens de VM do Linux com a CLI do Azure][select-vm-image].

### <a name="disk-and-storage-recommendations"></a>Recomendações de disco e de armazenamento

Para um melhor desempenho de E/S de disco, recomendamos o [Armazenamento Premium][premium-storage], que armazena dados em SSDs (unidades de estado sólido). O custo se baseia no tamanho do disco provisionado. O IOPS e a taxa de transferência (ou seja, a taxa de transferência de dados) também dependem do tamanho do disco. Portanto, ao provisionar um disco, considere todos os três fatores (capacidade, IOPS e taxa de transferência). 

Crie contas de Armazenamento do Azure separadas para cada VM conter os discos rígidos virtuais (VHDs), demodo a evitar atingir os limites de IOPS para contas de armazenamento. 

Adicione um ou mais discos de dados. Quando você cria um VHD, ele não está formatado. Faça logon na VM para formatar o disco. No shell do Linux, os discos de dados são exibidos como `/dev/sdc`, `/dev/sdd` e assim por diante. Você pode executar `lsblk` para listar os dispositivos de bloco, incluindo os discos. Para usar um disco de dados, crie uma partição e o sistema de arquivos e monte o disco. Por exemplo:

```bat
# Create a partition.
sudo fdisk /dev/sdc     # Enter 'n' to partition, 'w' to write the change.     

# Create a file system.
sudo mkfs -t ext3 /dev/sdc1

# Mount the drive.
sudo mkdir /data1
sudo mount /dev/sdc1 /data1
```

Se você tiver uma grande quantidade de discos de dados, esteja ciente dos limites totais de E/S da conta de armazenamento. Para saber mais, confira [limites de disco da máquina virtual][vm-disk-limits].

Quando você adiciona um disco de dados, uma ID de LUN (número de unidade lógica) é atribuída ao disco. Opcionalmente, você poderá especificar a ID do LUN &mdash; por exemplo, se estiver substituindo um disco e quiser manter a mesma ID de LUN ou tiver um aplicativo que está procurando uma ID de LUN específica. No entanto, lembre-se de que as IDs de LUN devem ser exclusivas para cada disco.

Convém alterar o agendador de E/S para otimizar o desempenho em SSDs, pois os discos para VMs com contas de armazenamento premium são SSDs. Uma recomendação comum é usar o agendador NOOP para SSDs, mas é necessário usar uma ferramenta como [iostat] para monitorar o desempenho de E/S de disco para sua carga de trabalho específica.

Para obter o melhor desempenho, crie uma conta de armazenamento separada para armazenar logs de diagnóstico. Uma conta LRS (armazenamento com redundância local) padrão é suficiente para os logs de diagnóstico.

### <a name="network-recommendations"></a>Recomendações de rede

Esse endereço IP público pode ser dinâmico ou estático. O padrão é dinâmico.

* Reserve um [endereço IP estático][static-ip] se precisar de um endereço IP fixo que não mudará &mdash; por exemplo, se precisar criar um registro A no DNS ou se precisar adicionar o endereço IP a uma lista segura.
* Você também pode criar um FQDN (nome de domínio totalmente qualificado) para o endereço IP. Em seguida, é possível registrar um [registro CNAME][cname-record] no DNS que aponta para o FQDN. Para saber mais, confira [criar um nome de domínio totalmente qualificado no portal do Azure][fqdn].

Todos os NSGs contêm um conjunto de [regras padrão][nsg-default-rules], incluindo uma regra que bloqueia todo o tráfego de Internet de entrada. As regras padrão não podem ser excluídas, mas outras regras podem substituí-las. Para habilitar o tráfego de Internet, crie regras que permitam o tráfego de entrada em portas específicas &mdash; por exemplo, a porta 80 para HTTP.  

Para habilitar o SSH, adicione uma regra ao NSG que permita o tráfego de entrada na porta TCP 22.

## <a name="scalability-considerations"></a>Considerações sobre escalabilidade

Para expandir ou reduzir, [altere o tamanho da VM][vm-resize]. 

Para escalar horizontalmente, coloque duas ou mais VMs em um conjunto de disponibilidade atrás de um balanceador de carga. Para saber mais, veja [Executando várias VMs no Azure][multi-vm].

## <a name="availability-considerations"></a>Considerações sobre disponibilidade

Para obter maior disponibilidade, é necessário implantar várias VMs em um conjunto de disponibilidade. Isso também fornece um maior [contrato de nível de serviço][vm-sla] (SLA). 

Sua VM pode ser afetada por uma [manutenção planejada][planned-maintenance] ou [manutenção não planejada][manage-vm-availability]. Você pode usar os[ logs de reinicialização da VM][reboot-logs] para determinar se uma reinicialização da VM foi causada por manutenção planejada.

VHDs são armazenados no [armazenamento do Azure][azure-storage] e o armazenamento do Azure é replicado para durabilidade e disponibilidade.

Para se proteger contra perda acidental de dados durante operações normais (por exemplo, devido ao erro do usuário), você também deve implementar backups pontuais usando [instantâneos de blob][blob-snapshot] ou outra ferramenta.

## <a name="manageability-considerations"></a>Considerações sobre capacidade de gerenciamento

**Grupos de recursos.** Coloque recursos acoplados rigidamente que compartilhem o mesmo ciclo de vida no mesmo [grupo de recursos][resource-manager-overview]. Os grupos de recursos permitem implantar e monitorar recursos como um grupo, além de acumular custos de cobrança por grupo de recursos. Também é possível excluir recursos como um conjunto, o que é muito útil para implantações de teste. Dê nomes significativos aos recursos. Isso facilita a localização de um recurso específico e o entendimento de sua função. Confira [Convenções de nomenclatura recomendadas para recursos do Azure][naming conventions].

**SSH**. Antes de criar uma VM do Linux, gere um par de chaves pública-privada do RSA de 2048 bits. Use o arquivo de chave pública ao criar a VM. Para saber mais, veja [Como usar SSH com Linux e Mac no Azure][ssh-linux].

**Diagnóstico da VM.** Habilite o monitoramento e diagnóstico, incluindo métricas de integridade básicas, logs de infraestrutura de diagnóstico e [diagnóstico de inicialização][boot-diagnostics]. O diagnóstico de inicialização poderá ajudar a diagnosticar uma falha de inicialização se sua VM entrar em um estado não inicializável. Para saber mais, confira [Habilitar monitoramento e diagnóstico][enable-monitoring].  

O seguinte comando da CLI habilita o diagnóstico:

```
azure vm enable-diag <resource-group> <vm-name>
```

**Interrompendo uma VM.** O Azure faz uma distinção entre os estados "parado" e "desalocado". Você será cobrado quando o status da VM for interrompido, mas não quando a VM for desalocada.

Use o seguinte comando da CLI para desalocar uma VM:

```
azure vm deallocate <resource-group> <vm-name>
```

No Portal do Azure, o botão **Parar** desaloca a VM. No entanto, se você desligar por meio do sistema operacional enquanto estiver conectado, a VM será interrompida, mas *não* desalocada e, portanto, você ainda será cobrado.

**Excluindo uma VM.** Se você excluir uma VM, os VHDs não serão excluídos. Isso significa que você poderá excluir com segurança a VM sem perda de dados. No entanto, você ainda será cobrado pelo armazenamento. Para excluir o VHD, exclua o arquivo do [Armazenamento de blobs][blob-storage].

Para evitar a exclusão acidental, use um [bloqueio de recurso][resource-lock] para bloquear o grupo de recursos inteiro ou bloquear recursos individuais, como a VM. 

## <a name="security-considerations"></a>Considerações de segurança

Automatize as atualizações do sistema operacional usando a extensão de VM [OSPatching] . Instale essa extensão ao provisionar a VM. Você pode especificar a frequência de instalação dos patches e se haverá necessidade de reinicialização após a aplicação de patch.

Use o [RBAC (controle de acesso baseado em função)][rbac] para controlar o acesso aos recursos do Azure implantados. O RBAC permite atribuir funções de autorização aos membros de sua equipe de DevOps. Por exemplo, a função Leitor pode exibir os recursos do Azure, mas não criar, gerenciar nem excluí-los. Algumas funções são específicas a determinados tipos de recursos do Azure. Por exemplo, a função Colaborador da Máquina Virtual pode reiniciar ou desalocar uma VM, redefinir a senha de administrador, criar uma VM e assim por diante. Outras [funções RBAC internas][rbac-roles] que podem ser úteis para esta arquitetura de referência incluem [Usuário de DevTest Labs][rbac-devtest] e [Colaborador de Rede][rbac-network]. 

Um usuário pode ser atribuído a várias funções, e você pode criar funções personalizadas para permissões ainda mais refinadas.

d> [!NOTE]
> O RBAC não limita as ações que podem ser executadas por um usuário conectado a uma VM. Essas permissões são determinadas pelo tipo de conta no SO convidado.   
> 
> 

Use os [logs de auditoria][audit-logs] para ver as ações de provisionamento e outros eventos da VM.

Considere o uso do [Azure Disk Encryption][disk-encryption] se você precisar criptografar os discos do sistema operacional e de dados. 

## <a name="solution-deployment"></a>Implantação da solução
Uma implantação para essa arquitetura de referência está disponível no [GitHub][github-folder]. Ele inclui uma VNet, NSG e uma única VM. Para implantar a arquitetura, siga estas etapas: 

1. Clique com o botão direito do mouse no botão abaixo e selecione "Abrir link em nova guia" ou "Abrir link em nova janela".
   [![Implantar no Azure](../articles/guidance/media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-single-vm%2Fazuredeploy.json)
2. Depois que o link for aberto no portal do Azure, você deve inserir valores para algumas das configurações: 
   
   * O nome do **Grupo de recursos** já está definido no arquivo de parâmetros, portanto, selecione **Criar novo** e digite `ra-single-vm-rg` na caixa de texto.
   * Selecione a região na caixa suspensa **Local**.
   * Não edite as caixas de texto **URI da raiz do modelo** ou **URI da raiz do parâmetro**.
.   * Selecione **linux** na caixa suspensa **Tipo de SO**.
   * Analise os termos e condições e clique na caixa de seleção **Concordo com os termos e condições declarados acima**.
   * Clique no botão **Comprar**.
3. Aguarde até que a implantação seja concluída.
4. Os arquivos de parâmetro incluem um nome de usuário administrador embutido e uma senha e é altamente recomendável que você altere imediatamente ambos. Clique na VM denominada `ra-single-vm0 `no portal do Azure. Em seguida, clique em **Redefinir senha** na seção **Suporte + solução de problemas**. Selecione **Redefinir senha** na caixa suspensa **Modo**, selecione um novo **Nome de usuário** e **Senha**. Clique no botão **Atualizar** para manter o novo nome de usuário e senha.

## <a name="next-steps"></a>Próximas etapas
Para maior disponibilidade, implante duas ou mais VMs atrás de um balanceador de carga. Para saber mais, confira [Executando várias VMs no Azure][multi-vm].

<!-- links -->

[audit-logs]: https://azure.microsoft.com/en-us/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]:../articles/virtual-machines/windows/create-availability-set.md
[azure-cli]: /cli/azure/get-started-with-az-cli2
[azure-linux]:../articles/virtual-machines/linux/overview.md
[azure-storage]:../articles/storage/common/storage-introduction.md
[blob-snapshot]:../articles/storage/blobs/storage-blob-snapshots.md
[blob-storage]:../articles/storage/common/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]:../articles/virtual-machines/linux/about-disks-and-vhds.md
[disk-encryption]: ../articles/security/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/monitoring-and-diagnostics/insights-how-to-use-diagnostics.md
[fqdn]:../articles/virtual-machines/linux/portal-create-fqdn.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/
[iostat]: https://en.wikipedia.org/wiki/Iostat
[manage-vm-availability]:../articles/virtual-machines/linux/manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[OSPatching]: https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching
[planned-maintenance]:../articles/virtual-machines/linux/planned-maintenance.md
[premium-storage]:../articles/storage/common/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-labs-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/en-us/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/en-us/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/en-us/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/azure-resource-manager/resource-group-overview.md
[select-vm-image]:../articles/virtual-machines/linux/cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[ssh-linux]:../articles/virtual-machines/linux/mac-create-ssh-keys.md
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-account-limits]: ../articles/azure-subscription-service-limits.md#storage-limits
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]:../articles/virtual-machines/linux/sizes.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]:../articles/virtual-machines/linux/change-vm-size.md
[vm-size-tables]:../articles/virtual-machines/windows/sizes.md#size-tables
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[readme]: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm
[components]: #Solution-components
[blocks]: https://github.com/mspnp/template-building-blocks
[0]: ./media/guidance-blueprints/compute-single-vm.png "Única arquitetura de VM do Linux no Azure"

