As organizações têm necessidades de computação em larga escala. Essas cargas de trabalho de computação intensa incluem o design e a análise de engenharia, cálculos de riscos financeiros, renderização de imagem, modelagens complexas, simulações Monte Carlo e muito mais. 

Use a nuvem do Azure para executar com eficiência em cargas de trabalho Linux e Windows, de trabalhos em lote paralelos a simulações de HPC tradicionais. Execute o HPC e cargas de trabalho em lotes na infraestrutura do Azure, com sua opção de computação serviços, gerenciadores de grade, soluções do Marketplace e hospedado pelo fornecedor aplicativos SaaS (). O Azure fornece soluções flexíveis para distribuir o trabalho e é dimensionado para milhares de máquinas virtuais ou núcleos e então reduzido quando menos recursos forem necessários. 



## <a name="solution-options"></a>Opções de solução



* **Soluções do tipo faça você mesmo**
    * Configurar seu próprio ambiente de cluster em máquinas virtuais do Azure ou [conjuntos de escala de máquina virtual](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). 
    * Faça lift-and-shift de um cluster local ou implante um novo cluster no Azure para capacidade adicional. 
    * Use modelos do Azure Resource Manager para implantar [gerenciadores de carga de trabalho](#workload-managers), infraestrutura, e [aplicativos](#hpc-applications) iniciais. 
    * Escolha [tamanhos de VM de GPU e HPC](#hpc-and-gpu-sizes) que incluem conexões de rede e de hardware especializados para cargas de trabalho MPI ou GPU. 
    * Adicione [armazenamento de alto desempenho](#hpc-storage) para cargas de trabalho com E/S alta.
* **Soluções híbridas**
    * Estender a sua solução local para descarregar cargas de trabalho de pico de ("disparar") para a infraestrutura do Azure
    * Use a computação de nuvem sob demanda com o [gerenciador de cargas de trabalho](#workload-manager) existente.
    * Aproveite os [tamanhos de VM de GPU e HPC](#hpc-and-gpu-sizes) para cargas de trabalho MPI ou GPU.
* **Soluções de Big Compute como serviço**
    * Desenvolva soluções personalizadas de Big Compute e fluxos de trabalho usando o [Lote do Azure](#azure-batch) e os [serviços do Azure](#related-azure-services).
    * Execute soluções de engenharia e simulação habilitadas para o Azure de fornecedores como [Altair](http://www.altair.com/), [Rescale](https://www.rescale.com/azure/) e [Cycle Computing](https://cyclecomputing.com/) (agora [associada à Microsoft](https://blogs.microsoft.com/blog/2017/08/15/microsoft-acquires-cycle-computing-accelerate-big-computing-cloud/)).
* **Soluções do Marketplace**
    * Use a escala de [aplicativos HPC](#hpc-applications) e [soluções](#marketplace-solutions) oferecidas no [Azure Marketplace](https://azuremarketplace.microsoft.com/). 
    


As seções a seguir fornecem mais informações sobre as tecnologias de suporte e links para diretrizes.



## <a name="marketplace-solutions"></a>Soluções do Marketplace

Visite o [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) para obter imagens e soluções de VM Windows e Linux projetadas para HPC. Os exemplos incluem:

* [HPC baseada em CentOS RogueWave](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased73HPC?tab=Overview)
* [SUSE Linux Enterprise Server para HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)
*  [Mecanismo de servidor de grade TIBCO](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/tibco-software.gridserverlinuxengine?tab=Overview)
* [VM de Ciência de Dados do Azure para Windows e Linux](../articles/machine-learning/machine-learning-data-science-virtual-machine-overview.md)
* [D3View](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/xfinityinc.d3view-v5?tab=Overview)
* [UberCloud](https://azure.microsoft.com/search/marketplace/?q=ubercloud)
* [Intel Cloud Edition para Lustre](https://azuremarketplace.microsoft.com/marketplace/apps/intel.lustre-cloud-edition-eval?tab=Overview)


 
## <a name="hpc-applications"></a>Aplicativos de HPC

Execute aplicativos de HPC comerciais ou personalizados no Azure. Vários exemplos desta seção são submetidos a benchmark para dimensionar com eficiência em caso de VMs ou núcleos de computação adicionais. Visite o [Azure Marketplace](https://marketplace.azure.com) para obter soluções prontas para uso.

> [!NOTE]
> Verifique com o fornecedor de qualquer aplicativo comercial quanto ao licenciamento ou outras restrições de execução na nuvem. Nem todos os fornecedores oferecem licenciamento pré-pago. Você talvez precise de um servidor de licenciamento na nuvem para sua solução, ou de uma conexão com um servidor de licença local.

### <a name="engineering-applications"></a>Aplicativos de engenharia


* [Altair RADIOSS](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/)
* [ANSYS CFD](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)
* [MATLAB Distributed Computing Server](../articles/virtual-machines/windows/matlab-mdcs-cluster.md)
* [StarCCM +](https://blogs.msdn.microsoft.com/azurecat/2017/07/07/run-star-ccm-in-an-azure-hpc-cluster/)
* [OpenFOAM](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)



### <a name="graphics-and-rendering"></a>Gráficos e renderização

* [Autodesk Maya, 3ds Max e Arnold](../articles/batch/batch-rendering-service.md) no Lote do Azure (versão prévia)

### <a name="ai-and-deep-learning"></a>IA e deep learning

* [Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/cntk-on-azure)
* [Kit de ferramentas de deep learning para VM de Ciência de Dados](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
* [Receitas do Shipyard do Lote para deep learning](https://github.com/Azure/batch-shipyard/tree/master/recipes#deeplearning)






## <a name="hpc-and-gpu-vm-sizes"></a>Tamanhos de VM de GPU e HPC
O Azure oferece uma variedade de tamanhos de VMs [Linux](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [Windows](../articles/virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), incluindo tamanhos projetados para cargas de trabalho de computação intensa. Por exemplo, as VMs H16r e H16mr podem se conectar a uma rede RDMA de back-end de alta taxa de transferência. Essa rede de nuvem pode melhorar o desempenho de aplicativos em paralelo firmemente acoplados em execução no [Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) ou no Intel MPI. 

As VMs da série N têm GPUs NVIDIA projetados para uso de computação intensa ou gráficos intensivos, incluindo visualização e aprendizagem de IA (inteligência artificial). 

Saiba mais:

* Tamanhos de computação de alto desempenho para VMs [Linux](../articles/virtual-machines/linux/sizes-hpc.md) e [Windows](../articles/virtual-machines/windows/sizes-hpc.md) 
* Tamanhos habilitados para GPU de VMs [Linux](../articles/virtual-machines/linux/sizes-gpu.md) e [Windows](../articles/virtual-machines/windows/sizes-gpu.md) 

Saiba como:

* [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](../articles/virtual-machines/linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Configurar um cluster de RDMA do Windows com o Microsoft HPC Pack para executar aplicativos MPI](../articles/virtual-machines/windows/classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Usar VMs de computação intensa em pools do Lote](../articles/batch/batch-pool-compute-intensive-sizes.md)



## <a name="azure-batch"></a>Lote do Azure
O [Lote](../articles/batch/batch-technical-overview.md) é um serviço de plataforma para execução de aplicativos paralelos em grande escala e aplicativos HPC (computação de alto desempenho) com eficiência na nuvem. O Lote do Azure agenda o trabalho de computação intensiva para execução em um pool gerenciado de máquinas virtuais e pode dimensionar automaticamente os recursos de computação para atender às necessidades de seus trabalhos. 

Os provedores ou desenvolvedores SaaS podem usar os SDKs e as ferramentas do Lote para integrar aplicativos ou cargas de trabalho de contêiner de HPC ao Azure, transferir dados para o Azure e criar pipelines de execução do trabalho. 

Saiba como:

* [Introdução ao desenvolvimento com o Lote](../articles/batch/batch-dotnet-get-started.md)
* [Usar exemplos de código do Lote do Azure](https://github.com/Azure/azure-batch-samples)
* [Usar VMs de baixa prioridade com o Lote (versão prévia)](../articles/batch/batch-low-pri-vms.md)
* [Executar cargas de trabalho de HPC em contêineres com Shipyard do Lote](https://github.com/Azure/batch-shipyard)
* [Usar a linguagem R com o Lote](https://github.com/Azure/doAzureParallel)

## <a name="workload-managers"></a>Gerenciadores de carga de trabalho

A seguir, os exemplos de gerenciadores de cluster e de carga de trabalho que podem ser executados na infraestrutura do Azure. Crie clusters autônomos em VMs do Azure ou se espalhe para máquinas virtuais do Azure de um cluster local. 
* [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/) 
* [Bright Cluster Manager](http://www.brightcomputing.com/technology-partners/microsoft)
* [IBM Spectrum Symphony e Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/)
* [PBS Pro](http://pbspro.org)
* [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029(v=ws.11).aspx) -veja as opções para execução em VMS do [Windows](../articles/virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [Linux](../articles/virtual-machines/linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 



## <a name="hpc-storage"></a>Armazenamento HPC

Cargas de trabalho do Lote e de HPC em larga escala têm exigências de armazenamento e acesso a dados que excedem os recursos de sistemas de arquivos em nuvem tradicionais. Implemente soluções de sistema de arquivos paralelos no Azure, como o [Lustre](http://lustre.org/) e o [BeeGFS](http://www.beegfs.com/content/).

Saiba mais:

* [Sistemas de arquivos paralelos para o armazenamento HPC no Azure](https://blogs.msdn.microsoft.com/azurecat/2017/03/17/parallel-file-systems-for-hpc-storage-on-azure/)


## <a name="related-azure-services"></a>Serviços do Azure relacionados

As máquinas virtuais do Azure, os conjuntos de dimensionamento de máquinas virtuais, o Lote e os serviços de computação relacionados são a base da maioria das soluções de HPC do Azure. No entanto, sua solução pode tirar proveito de muitos serviços relacionados do Azure. Veja uma lista parcial:

### <a name="storage"></a>Armazenamento

* [Armazenamento de blobs, tabelas e filas](../articles/storage/storage-introduction.md)
* [Armazenamento de arquivos](../articles/storage/storage-files-introduction.md)

### <a name="data-and-analytics"></a>Dados e análises
* [HDInsight](../articles/hdinsight/hdinsight-hadoop-introduction.md) para clusters Hadoop no Azure
* [Fábrica de dados](../articles/data-factory/introduction.md)
* [Data Lake Store](../articles/data-lake-store/data-lake-store-overview.md)
* [Machine Learning](../articles/machine-learning/machine-learning-what-is-machine-learning.md)
* [Banco de Dados SQL](../articles/sql-database/sql-database-technical-overview.md)

### <a name="networking"></a>Rede
* [Rede Virtual](../articles/virtual-network/virtual-networks-overview.md)
* [ExpressRoute](../articles/expressroute/expressroute-introduction.md)

### <a name="containers"></a>Contêineres
* [Serviço de Contêiner](../articles/container-service/dcos-swarm/container-service-intro.md)
* [Registro de Contêiner](../articles/container-registry/container-registry-intro.md)



## <a name="customer-stories"></a>Relatos de clientes

Aqui estão exemplos de clientes que solucionaram problemas de negócios com soluções de HPC do Azure:

* [ANEO](https://customers.microsoft.com/story/it-provider-finds-highly-scalable-cloud-based-hpc-redu) 
* [AXA Global P&C](https://customers.microsoft.com/story/axa-global-p-and-c)
* [Axioma](https://customers.microsoft.com/story/axioma-delivers-fintechs-first-born-in-the-cloud-multi-asset-class-enterprise-risk-solution)
* [d3View](https://customers.microsoft.com/story/big-data-solution-provider-adopts-new-cloud-gains-thou)
* [Hymans Robertson](https://customers.microsoft.com/story/hymans-robertson)
* [MetLife](https://enterprise.microsoft.com/en-us/customer-story/industries/insurance/metlife/)
* [Microsoft Research](https://customers.microsoft.com/doclink/fast-lmm-and-windows-azure-put-genetics-research-on-fa)
* [Milliman](https://customers.microsoft.com/story/actuarial-firm-works-to-transform-insurance-industry-w)
* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/story/powering-risk-compute-grids-in-the-cloud)
* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Towers Watson](https://customers.microsoft.com/story/insurance-tech-provider-delivers-disruptive-solutions)


## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre as soluções de computação intensa para [simulação de engenharia](https://simulation.azure.com/), [renderização](https://simulation.azure.com/), [bancos e mercados de capital](https://finance.azure.com/) e [genômicas](https://enterprise.microsoft.com/en-us/industries/health/genomics/).
* Para os anúncios mais recentes, consulte o [blog da equipe do Microsoft HPC e Lote](http://blogs.technet.com/b/windowshpc/) e o [blog do Azure](https://azure.microsoft.com/blog/tag/hpc/).

* Usar o serviço [Lote](https://azure.microsoft.com/services/batch/) do Azure gerenciável e escalável para executar cargas de trabalho de computação intensa sem ter que gerenciar a infraestrutura subjacente [Saiba mais](https://azure.microsoft.com/en-us/solutions/architecture/hpc-big-compute-saas/)



