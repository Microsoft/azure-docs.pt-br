---
title: Soluções da Oracle em máquinas virtuais do Azure | Microsoft Docs
description: Saiba mais sobre configurações com suporte e as limitações das imagens de máquinas virtuais do Oracle no Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: rogirdh
ms.custom: seodec18
ms.openlocfilehash: 70e87a38373688c1b364a079cd07934309662e3e
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707431"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Imagens de VM do Oracle e sua implantação no Microsoft Azure

Este artigo aborda informações sobre soluções da Oracle com base em imagens de máquina virtual publicadas pela Oracle no Azure Marketplace. Se você estiver interessado em soluções de aplicativos de nuvem com a infraestrutura de nuvem da Oracle, consulte [soluções de aplicativos Oracle a integração do Microsoft Azure e infraestrutura de nuvem Oracle](oracle-oci-overview.md).

Para obter uma lista de imagens disponíveis no momento, execute o seguinte comando:

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

A partir de maio de 2019 as imagens a seguir estão disponíveis:

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.20190506                       6.10.20190506
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190506                         6.8.20190506
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190506                         6.9.20190506
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190506                         7.3.20190506
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190506                         7.4.20190506
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190506                         7.5.20190506
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20181207                         7.6.20181207
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20190506                         7.6.20190506
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

Essas imagens são consideradas "Traga sua própria licença" e como tal, você só será cobrado pelos custos de computação, armazenamento e rede incorridos na execução de uma VM.  Presume-se que você esteja devidamente licenciado para usar o software da Oracle e que tem um contrato de suporte em vigor com a Oracle. A Oracle tem garantia de mobilidade de licença do local para o Azure. Consulte a observação [Oracle e Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) publicada para obter detalhes sobre a mobilidade da licença. 

Pessoas também podem optar por basear suas soluções em uma imagem personalizada, elas criam imagens do zero no Azure ou fazem upload da imagem personalizada de seu ambiente local.

## <a name="support-for-jd-edwards"></a>Suporte para JD Edwards
De acordo com a observação de suporte da Oracle [Doc ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4), JD Edwards EnterpriseOne versões 9.2 e têm suporte em **ofertas de nuvem qualquer pública** que atenda aos seus específicas `Minimum Technical Requirements` (MTR).  Você precisa criar imagens personalizadas que atendam às suas especificações MTR para o sistema operacional e à compatibilidade do aplicativo de software. 

## <a name="oracle-database-vm-images"></a>Imagens de VM do banco de dados Oracle
A Oracle oferece suporte às edições Oracle DB 12.1 Standard e Enterprise em execução no Azure em imagens de máquina virtual com base no Oracle Linux.  Para obter o melhor desempenho para cargas de trabalho de produção do Banco de dados da Oracle no Azure, certifique-se de dimensionar corretamente a imagem da VM e usar o Managed Disks que teve seu backup realizado pelo armazenamento Premium. Para obter instruções sobre como obter rapidamente um Banco de dados da Oracle em execução no Azure usando a imagem da VM publicada pela Oracle, [repita o passo a passo do início rápido do Banco de dados da Oracle](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Opções de configuração de disco anexado

Os discos anexados se baseiam no serviço de armazenamento de Blobs do Azure. Cada disco padrão pode fornecer uma velocidade máxima teórica de aproximadamente 500 IOPS (operações de entrada/saída por segundo). Nossa oferta de disco premium é preferencial para cargas de trabalho de banco de dados de alto desempenho e pode alcançar até 5000 IOPS por disco. Você pode usar um único disco se isso atender às suas necessidades de desempenho. No entanto, você pode melhorar o desempenho do IOPS se usar vários discos anexados, distribuir dados do banco de dados entre eles e, em seguida, usar o Oracle ASM Automatic Storage Management (). Veja [Visão geral do armazenamento automático da Oracle](https://www.oracle.com/technetwork/database/index-100339.html) para obter mais informações específicas do Oracle ASM. Para obter um exemplo de como instalar e configurar o Oracle ASM em uma VM do Linux do Azure, consulte a [instalando e configurando o Oracle Automated Storage Management](configure-oracle-asm.md) tutorial.

### <a name="shared-storage-configuration-options"></a>Opções de configuração de armazenamento compartilhado

Os arquivos do Azure do NetApp foi projetado para atender aos requisitos básicos de executar cargas de trabalho de alto desempenho, como bancos de dados na nuvem e fornece;
- Serviço de armazenamento NFS para a execução de cargas de trabalho Oracle tanto por meio do cliente NFS nativo de VM ou Oracle dNFS de compartilhado nativa do Azure
- Níveis de desempenho escalonável que refletem as demandas de intervalo do mundo real de IOPS
- Baixa latência
- Alta disponibilidade, alta durabilidade e a capacidade de gerenciamento em escala, normalmente exigida por cargas de trabalho de missão críticos da empresa (como SAP e Oracle)
- Rápido e eficiente de backup e recuperação, para alcançar o RTO e o RPO SLA mais agressivo

Esses recursos são possíveis porque os arquivos NetApp Azure baseia-se em sistemas de todos os flash® de NetApp ONTAP® em execução dentro do ambiente do Centro de dados do Azure – como um serviço nativo do Azure. O resultado é uma tecnologia de armazenamento do banco de dados ideal que pode ser provisionada e consumida assim como outras opções de armazenamento do Azure. Ver [documentação de arquivos do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/) para obter mais informações sobre como implantar e acessar volumes NFS de arquivos do Azure NetApp. Ver [Oracle no Azure implantação melhor prática guia usando arquivos do Azure NetApp](https://www.netapp.com/us/media/tr-4780.pdf) para práticas recomendadas para a operação de banco de dados Oracle no Azure NetApp arquivos.


## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle Real Application Cluster (Oracle RAC)
O RAC da Oracle foi projetado para atenuar a falha de um único nó em uma configuração de cluster de vários nós local. Ele se baseia em duas tecnologias locais que não são nativas em ambientes de nuvem pública de hiperescala: multicast de rede e disco compartilhado. Se sua solução de banco de dados requer o Oracle RAC no Azure, você precisa de outro software de terceiros para habilitar essas tecnologias. Para obter mais informações sobre o Oracle RAC, consulte o [FlashGrid SkyCluster página](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="high-availability-and-disaster-recovery-considerations"></a>Considerações sobre alta disponibilidade e recuperação de desastres
Ao usar bancos de dados Oracle no Azure, você é responsável por implementar uma solução de recuperação de desastres e disponibilidade alta para evitar qualquer tempo de inatividade. 

Alta disponibilidade e recuperação de desastres para o Oracle Database Enterprise Edition (sem depender do Oracle RAC) pode ser obtida no Azure usando [Data Guard, Active Data Guard](https://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html), ou [Oracle GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate), com dois bancos de dados em duas máquinas virtuais separadas. As duas máquinas virtuais devem estar na mesma [rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/) para garantir que podem se acessar mutuamente por um endereço IP privado persistente.  Além disso, é recomendável colocar as máquinas virtuais no mesmo conjunto de disponibilidade para permitir que o Azure as coloque em domínios de falha e domínios de atualização separados. Você deseja ter a redundância geográfica, configure dois bancos de dados para replicar entre duas regiões diferentes e conectar as duas instâncias com um Gateway de VPN.

O tutorial [implementar o Oracle Data Guard no Azure](configure-oracle-dataguard.md) orienta você por meio do procedimento de configuração básica no Azure.  

Com o Oracle Data Guard, a alta disponibilidade pode ser obtida com um banco de dados primário em uma máquina virtual, um banco de dados secundário (em espera) em outra máquina virtual e replicação unidirecional entre eles. O resultado é o acesso de leitura à cópia do banco de dados. Com o Oracle GoldenGate, você pode configurar a replicação bidirecional entre dois bancos de dados. Para saber como configurar uma solução de alta disponibilidade para seus bancos de dados usando essas ferramentas, veja a documentação do [Active Data Guard](https://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) e do [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) no site da Oracle. Se precisar de acesso de leitura-gravação à cópia do banco de dados, você poderá usar o [Oracle Active Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

O tutorial [implementar o Oracle GoldenGate no Azure](configure-oracle-golden-gate.md) orienta você por meio do procedimento de configuração básica no Azure.

Além de ter uma solução de alta disponibilidade e recuperação de Desastre projetada no Azure, você deve ter uma estratégia de backup em vigor para restaurar seu banco de dados. O tutorial [fazer Backup e recuperar um banco de dados Oracle](oracle-backup-recovery.md) orienta o procedimento básico para estabelecer um backup consistente.

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Imagens de máquina virtual do Oracle WebLogic Server

* **Há suporte para clustering apenas na Enterprise Edition.** Você está licenciado para usar o clustering do WebLogic somente quando usar a Enterprise Edition do WebLogic Server. Não use clustering com a Standard Edition do WebLogic Server.
* **O multicast de protocolo UDP não tem suporte.** O Azure dá suporte a unicast UDP, mas não a multicast ou difusão. O WebLogic Server pode se basear nos recursos de unicast UDP do Azure. Para melhores resultados com base no unicast UDP, é recomendável que o tamanho do cluster WebLogic é mantido estático ou mantido com não mais de 10 servidores gerenciados.
* **O WebLogic Server espera que as portas públicas e privadas sejam as mesmas para o acesso T3 (por exemplo, ao usar o Enterprise JavaBeans).** Considere um cenário de várias camado em que um aplicativo de serviço camada (EJB) está em execução em um cluster do WebLogic Server consiste em duas ou mais VMs em uma rede virtual denominada *SLWLS*. A camada do cliente está em uma sub-rede diferente na mesma rede virtual, executando um programa Java simples tentando chamar o EJB na camada de serviço. Porque é necessário para a camada de serviço de balanceamento de carga, um ponto de extremidade público com balanceamento de carga precisa ser criada para as máquinas virtuais no cluster do WebLogic Server. Se a porta privada que você especificar for diferente da porta pública (por exemplo, 7006:7008), ocorrerá um erro como o seguinte:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   Isso ocorre porque para qualquer acesso remoto do T3, o WebLogic Server espera que a porta do balanceador de carga e a porta do servidor gerenciado WebLogic sejam iguais. No caso anterior, o cliente está acessando a porta 7006 (a porta do balanceador de carga) e o servidor gerenciado está escutando na 7008 (a porta privada). Essa restrição se aplica somente ao acesso T3, não HTTP.

   Para evitar esse problema, use uma das seguintes alternativas:

  * Use os mesmos números de porta pública e privada para pontos de extremidade com balanceamento de carga dedicados ao acesso T3.
  * Inclua o seguinte parâmetro JVM ao iniciar o WebLogic Server:

    ```
    -Dweblogic.rjvm.enableprotocolswitch=true
    ```

Para obter informações relacionadas, consulte o artigo KB **860340.1** em <https://support.oracle.com>.

* **Limitações de balanceamento de carga e clustering dinâmico.** Suponha que você queira usar um cluster dinâmico no WebLogic Server e expô-lo por meio de um ponto de extremidade único, com balanceamento de carga, no Azure. Isso pode ser feito desde que você use um número de porta fixa para cada um dos servidores gerenciados (atribuídos dinamicamente dentro de um intervalo) e não serão iniciados mais servidores gerenciados que há máquinas monitoradas pelo administrador. Ou seja, há não mais de um servidor gerenciado por máquina virtual). Se sua configuração resulta em mais servidores WebLogic sendo iniciados do que há máquinas virtuais (isto é, em que várias instâncias do WebLogic Server compartilharão a mesma máquina virtual), então não é possível que mais de uma dessas instâncias do WebLogic Server para associar a um determinado número da porta. As outras na máquina virtual falharem.

   Se você configurar o servidor de administração para atribuir automaticamente números de porta exclusivos para os servidores gerenciados, o balanceamento de carga não será possível porque o Azure não dá suporte ao mapeamento de uma única porta pública para várias portas privadas, como seria necessário para esta configuração.
* **Várias instâncias do WebLogic Server em uma máquina virtual.** Dependendo dos requisitos da implantação, você pode considerar executar várias instâncias do WebLogic Server na mesma máquina virtual, se a máquina virtual é grande o suficiente. Por exemplo, em uma máquina virtual de tamanho médio, que contém dois núcleos, você poderia optar por executar duas instâncias do WebLogic Server. No entanto, ainda é recomendável que você evite introduzir pontos únicos de falha em sua arquitetura, como seria o caso se você usou apenas uma máquina virtual que esteja executando várias instâncias do WebLogic Server. Usar pelo menos duas máquinas virtuais pode ser uma abordagem melhor, e cada máquina virtual poderia então executar várias instâncias do WebLogic Server. Cada instância do WebLogic Server ainda pode fazer parte do mesmo cluster. No entanto, atualmente, não é possível usar o Azure para pontos de extremidade de balanceamento de carga que são expostos por essas implantações do WebLogic Server na mesma máquina virtual, pois o balanceador de carga do Azure exige que os servidores com balanceamento de carga sejam distribuídos entre exclusivo máquinas virtuais.

## <a name="oracle-jdk-virtual-machine-images"></a>Imagens de máquina virtual no JDK do Oracle
* **Atualizações mais recentes do JDK 6 e 7.** Embora seja recomendável usar a versão mais recente com suporte público do Java (atualmente, o Java 8), o Azure também disponibiliza imagens do JDK 6 e 7. Eles são voltados a aplicativos herdados que ainda não estão prontos para serem atualizado para o JDK 8. Enquanto atualizações para imagens do JDK anteriores podem não estar disponíveis para público em geral, devido à parceria da Microsoft com a Oracle, as imagens do JDK 6 e 7 fornecidas pelo Azure devem conter uma atualização mais recente, não pública, que normalmente é oferecida pela Oracle somente para um grupo selecionado de clientes com suporte da Oracle. As novas versões das imagens do JDK ficarão disponíveis com o passar do tempo, com versões atualizadas do JDK 6 e 7.

   O JDK disponível o JDK 6 e 7 imagens e as máquinas virtuais e imagens derivadas deles só pode ser usado dentro do Azure.
* **JDK de 64 bits.** As imagens de máquina virtual do Oracle WebLogic Server e as imagens de máquina virtual do Oracle JDK fornecidas pelo Azure contêm as versões de 64 bits do Windows Server e o JDK.

## <a name="next-steps"></a>Próximas etapas
Agora você tem uma visão geral das soluções atuais da Oracle com base em imagens de máquina virtual no Microsoft Azure. A próxima etapa é implantar seu primeiro banco de dados Oracle no Azure.

> [!div class="nextstepaction"]
> [Criar um banco de dados Oracle no Azure](oracle-database-quick-create.md)
