<!-- A-series - compute-intensive instances, H-series -->

Os tamanhos A8-A11 e série H também são conhecidos como *instâncias de computação intensiva*. O hardware de datacenter do Azure que executa esses tamanhos é projetado e otimizado para aplicativos de uso intensivo de computação e rede, incluindo aplicativos, modelagem e simulações de cluster HPC (computação de alto desempenho). A série de A8-A11 usa Intel Xeon E5-2670 a 2,6 GHz e a série H usa Intel Xeon E5-2667 v3 a 3,2 GHz. 

As máquinas virtuais da série H do Azure são a próxima geração de VMs de computação de alto desempenho voltadas para as necessidades computacionais de alto nível, como modelagem molecular e dinâmica de fluido computacional. Essas VMs de 8 e 16 vCPUs baseiam-se na tecnologia do processador Intel Haswell E5-2667 V3 apresentando memória DDR4 e armazenamento temporário baseado em SSD. 

Além de potência de CPU considerável, a série H oferece diversas opções para RDMA e rede de baixa latência usando FDR InfiniBand e várias configurações de memória para dar suporte a requisitos computacionais com uso intensivo de memória.



## <a name="h-series"></a>Série H

ACU: 290-300

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima do disco: IOPS | Máximo de NICs |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |16 |16 x 500 |2  |
| Standard_H16 |16 |112 |2000 |32 |32 x 500 |4 |
| Standard_H8m |8 |112 |1000 |16 |16 x 500 |2  |
| Standard_H16m |16 |224 |2000 |32 |32 x 500 |4  |
| Standard_H16r* |16 |112 |2000 |32 |32 x 500 |4  |
| Standard_H16mr* |16 |224 |2000 |32 |32 x 500 |4 |

*Para os aplicativos MPI, a rede de back-end RDMA dedicada é habilitada pela rede InfiniBand FDR, que fornece latência ultrabaixa e largura de banda alta.

<br>



## <a name="a-series---compute-intensive-instances"></a>Série A – Instâncias de computação intensiva

ACU: 225

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (HDD): GiB | Discos de dados máximos | Taxa de transferência máxima do disco de dados: IOPS | Máximo de NICs|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8* |8 |56 |382 |16 |16 x 500 |2 |
| Standard_A9* |16 |112 |382 |16 |16 x 500 |4 |
| Standard_A10 |8 |56 |382 |16 |16 x 500 |2  |
| Standard_A11 |16 |112 |382 |16 |16 x 500 |4 |

*Para os aplicativos MPI, a rede de back-end RDMA dedicada é habilitada pela rede InfiniBand FDR, que fornece latência ultrabaixa e largura de banda alta.

<br>



