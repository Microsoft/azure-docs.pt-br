## <a name="deployment-considerations"></a>Considerações de implantação

* Para ver a disponibilidade das VMs da Série N, veja [Produtos disponíveis por região](https://azure.microsoft.com/en-us/regions/services/).

* As VMs da Série N só podem ser implantadas no modelo de implantação do Resource Manager.

* Ao criar uma VM da Série N usando o portal do Azure, na folha **Noções Básicas**, selecione um **Tipo de disco de VM** **HDD**. Para escolher um tamanho de Série N disponível na folha **Tamanho** folha, clique em **Exibir tudo**.

* As VMs da Série N não dão suporte a discos de VM incluídos em backup no armazenamento Premium do Azure.

* Se você quiser implantar mais do que algumas VMs da Série N, considere uma assinatura pré-paga ou outras opções de compra. Se estiver usando uma [conta gratuita do Azure](https://azure.microsoft.com/free/), você poderá usar apenas um número limitado de núcleos de computação do Azure.

* Convém aumentar a cota de núcleos (por região) na sua assinatura do Azure e aumentar a cota separada para núcleos NC ou NV. Para solicitar um aumento de cota, [abra uma solicitação de atendimento ao cliente online](../articles/azure-supportability/how-to-create-azure-support-request.md) gratuitamente. Os limites padrão podem variar dependendo de sua categoria de assinatura.

* Uma imagem VM que você pode implantar nas VMs da série N é a [Máquina Virtual de Ciência de Dados do Azure](../articles/machine-learning/data-science-virtual-machine/overview.md). A Máquina Virtual de Ciência de Dados pré-instala e configura muitas ferramentas conhecidas de ciência de dados e de aprendizado. Ele também pré-instala drivers NVIDIA Tesla GPU para instâncias de NC.





