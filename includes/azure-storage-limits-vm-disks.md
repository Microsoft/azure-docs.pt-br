As Máquinas Virtuais do Azure têm suporte para a anexação de vários discos de dados. Para obter o desempenho ideal, você deverá limitar a quantidade de discos altamente utilizados anexados à máquina virtual para evitar possíveis limitações. A conta de armazenamento pode ter suporte para um grande número de discos, caso eles não sejam altamente utilizados ao mesmo tempo.

* **Para Azure Managed Disks:** o limite de contagem de Managed Disks é regional e também depende do tipo de armazenamento. O padrão e também o limite máximo é 10.000 por assinatura, por região e por tipo de armazenamento. Por exemplo, você pode criar até 10.000 discos gerenciados standard e também 10.000 discos gerenciados premium em uma assinatura de uma região. 

    Os instantâneos e as imagens gerenciados são contados em relação ao limite de Managed Disks.

* **Para contas de armazenamento padrão:** uma conta de armazenamento padrão tem uma taxa de solicitação total máxima de 20.000 IOPS. O total de IOPS em todos os discos da máquina virtual de uma conta de armazenamento padrão não deve exceder esse limite.
  
    Basicamente, você calcula o número de discos altamente utilizados compatíveis com uma conta de armazenamento padrão com base no limite da taxa de solicitação. Por exemplo, para uma VM da Camada Basic, o número máximo de discos altamente utilizados é de aproximadamente 66 (20.000/300 IOPS por disco) e para uma VM da Camada Standard é de aproximadamente 40 (20.000/500 IOPS por disco), como mostrado na tabela a seguir. 
* **Para contas de armazenamento premium:** uma conta de armazenamento premium tem uma taxa de transferência total máxima de 50 Gbps. A taxa de transferência total de todos os discos da VM não deve exceder esse limite.

