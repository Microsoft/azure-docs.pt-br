---
title: Soluções Oracle em máquinas virtuais do Azure | Microsoft Docs
description: Saiba mais sobre as configurações com suporte e as limitações das imagens de máquina virtual da Oracle no Microsoft Azure.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 05/12/2020
ms.author: kegorman
ms.openlocfilehash: 8bcd45ab1270d478b05b3929d7b8914976612294
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645302"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Imagens da VM Oracle e sua implantação no Microsoft Azure

Este artigo aborda informações sobre soluções Oracle baseadas em imagens de máquinas virtuais publicadas pela Oracle no Azure Marketplace. Se você estiver interessado em soluções de aplicativos entre nuvens com a infraestrutura de nuvem da Oracle, consulte [Soluções de aplicativos Oracle integrando o Microsoft Azure e o Oracle Cloud Infrastructure](oracle-oci-overview.md).

Para obter uma lista de imagens disponíveis no momento, execute o seguinte comando:

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

A partir de junho de 2020, as seguintes imagens estão disponíveis:

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
oracle-database-19-3    Oracle       oracle-db-19300         Oracle:oracle-database-19-3:oracle-db-19300:19.3.0           19.3.0
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.00                             6.10.00
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.0                                6.8.0
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190529                         6.8.20190529
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.0                                6.9.0
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190529                         6.9.20190529
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.0                                7.3.0
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190529                         7.3.20190529
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.1                                7.4.1
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190529                         7.4.20190529
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.1                                7.5.1
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.2                                7.5.2
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190529                         7.5.20190529
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.2                                7.6.2
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.3                                7.6.3
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.4                                7.6.4
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.1                                 7.7.1
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.2                                 7.7.2
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.3                                 7.7.3
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.4                                 7.7.4
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.5                                 7.7.5
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.01                             7.7.01
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.02                             7.7.02
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.03                             7.7.03
Oracle-Linux            Oracle       78                      Oracle:Oracle-Linux:78:7.8.01                                7.8.01
Oracle-Linux            Oracle       8                       Oracle:Oracle-Linux:8:8.0.2                                  8.0.2
Oracle-Linux            Oracle       8-ci                    Oracle:Oracle-Linux:8-ci:8.0.11                              8.0.11
Oracle-Linux            Oracle       81                      Oracle:Oracle-Linux:81:8.1.0                                 8.1.0
Oracle-Linux            Oracle       81                      Oracle:Oracle-Linux:81:8.1.2                                 8.1.2
Oracle-Linux            Oracle       81-ci                   Oracle:Oracle-Linux:81-ci:8.1.0                              8.1.0
Oracle-Linux            Oracle       81-gen2                 Oracle:Oracle-Linux:81-gen2:8.1.11                           8.1.11
Oracle-Linux            Oracle       ol77-ci-gen2            Oracle:Oracle-Linux:ol77-ci-gen2:7.7.1                       7.7.1
Oracle-Linux            Oracle       ol77-gen2               Oracle:Oracle-Linux:ol77-gen2:7.7.01                         7.7.01
Oracle-Linux            Oracle       ol77-gen2               Oracle:Oracle-Linux:ol77-gen2:7.7.02                         7.7.02
Oracle-Linux            Oracle       ol78-gen2               Oracle:Oracle-Linux:ol78-gen2:7.8.11                         7.8.11
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
weblogic-122130-jdk8u3  Oracle       owls-122130-8u131-ol73  Oracle:weblogic-122130-jdk8u131-ol73:owls-122130-8u131-ol7   1.1.6
weblogic-122130-jdk8u4  Oracle       owls-122130-8u131-ol74  Oracle:weblogic-122130-jdk8u131-ol74:owls-122130-8u131-ol7   1.1.1
weblogic-122140-jdk8u6  Oracle       owls-122140-8u251-ol76  Oracle:weblogic-122140-jdk8u251-ol76:owls-122140-8u251-ol7   1.1.1
weblogic-141100-jdk116  Oracle       owls-141100-11_07-ol76  Oracle:weblogic-141100-jdk11_07-ol76:owls-141100-11_07-ol7   1.1.1
weblogic-141100-jdk8u6  Oracle       owls-141100-8u251-ol76  Oracle:weblogic-141100-jdk8u251-ol76:owls-141100-8u251-ol7   1.1.1
```

Essas imagens são consideradas "Traga sua própria licença" e como tal, você só será cobrado pelos custos de computação, armazenamento e rede incorridos na execução de uma VM.  Presume-se que você esteja devidamente licenciado para usar o software da Oracle e que tem um contrato de suporte em vigor com a Oracle. A Oracle tem garantia de mobilidade de licença do local para o Azure. Consulte a observação [Oracle e Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) publicada para obter detalhes sobre a mobilidade da licença.

Pessoas também podem optar por basear suas soluções em uma imagem personalizada, elas criam imagens do zero no Azure ou fazem upload da imagem personalizada de seu ambiente local.

## <a name="oracle-database-vm-images"></a>Imagens de VM do banco de dados Oracle

A Oracle oferece suporte às edições Standard e Enterprise do Oracle Database 12.1 e superior em execução no Azure em imagens de máquina virtual com base no Oracle Linux.  Para obter o melhor desempenho para cargas de trabalho do Oracle Database no Azure, certifique-se de dimensionar corretamente a imagem da VM e usar os Managed Disks SSD Premium ou SSD Ultra. Para obter instruções sobre como obter rapidamente um Oracle Database em execução no Azure usando a imagem da VM publicada pela Oracle, [repita o passo a passo do Início Rápido do Oracle Database](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Opções de configuração de disco anexado

Os discos anexados se baseiam no serviço de armazenamento de Blobs do Azure. Cada disco padrão pode fornecer uma velocidade máxima teórica de aproximadamente 500 IOPS (operações de entrada/saída por segundo). Nossa oferta de disco premium é preferencial para cargas de trabalho de banco de dados de alto desempenho e pode alcançar até 5000 IOPS por disco. Você pode usar um único disco se atender às suas necessidades de desempenho. No entanto, é possível melhorar o desempenho do IOPS se você usar vários discos anexados, espalhar os dados do banco de dados entre eles e usar o ASM (Automatic Storage Management) do Oracle. Veja [Visão geral do armazenamento automático da Oracle](https://www.oracle.com/technetwork/database/index-100339.html) para obter mais informações específicas do Oracle ASM. Para obter um exemplo de como instalar e configurar o Oracle ASM em uma VM do Azure no Linux, consulte o tutorial [Instalando e configurando o Oracle Automated Storage Management](configure-oracle-asm.md).

### <a name="shared-storage-configuration-options"></a>Opções de configuração de armazenamento compartilhado

O Azure NetApp Files foi projetado para atender aos principais requisitos de execução de cargas de trabalho de alto desempenho, como bancos de dados na nuvem, e fornece;

- Serviço de armazenamento NFS compartilhado nativo do Azure para executar cargas de trabalho Oracle por meio de cliente NFS nativo de VM ou Oracle dNFS
- Níveis de desempenho escalonáveis que refletem o intervalo real de demandas de IOPS
- Baixa latência
- Alta disponibilidade, alta durabilidade e capacidade de gerenciamento em escala, normalmente exigidas por cargas de trabalho de missão crítica corporativas (como SAP e Oracle)
- Backup e recuperação rápidos e eficientes, para atingir os SLAs de RTO e RPO mais agressivos

Esses recursos são possíveis porque o Azure NetApp Files se baseia nos sistemas totalmente flash NetApp® ONTAP® em execução no ambiente de data center do Azure – como um serviço nativo do Azure. O resultado é uma tecnologia de armazenamento de banco de dados ideal, que pode ser provisionada e consumida da mesma forma que outras opções de armazenamento do Azure. Consulte a [documentação do Azure NetApp Files](../../../azure-netapp-files/index.yml) para obter mais informações sobre como implantar e acessar os volumes de NFS no Azure NetApp Files. Consulte o [Guia de melhores práticas de implantação do Oracle no Azure usando o Azure NetApp Files](https://www.netapp.com/us/media/tr-4780.pdf) para obter recomendações de melhores práticas para operar um banco de dados Oracle no Azure NetApp Files.

## <a name="licensing-oracle-database--software-on-azure"></a>Licenciamento do Oracle Database e software no Azure

O Microsoft Azure é um ambiente de nuvem autorizado para execução do Oracle Database. A tabela de fatores de núcleo Oracle não é aplicável ao licenciar bancos de dados Oracle na nuvem. Em vez disso, ao usar VMs com a tecnologia Hyper-Threading habilitada para bancos de dados Enterprise Edition, conte duas vCPUs como equivalentes a uma licença de processador Oracle se o hyperthreading estiver habilitado (conforme indicado no documento da política). Os detalhes da política podem ser encontrados [aqui](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf).
Os bancos de dados da Oracle geralmente exigem mais memória e e/s. Por esse motivo, são recomendadas [VMs com otimização de memória](../../sizes-memory.md) para essas cargas de trabalho. Para otimizar suas cargas de trabalho ainda mais, são recomendadas [vCPUs de Núcleos Restritas](../../constrained-vcpu.md) para cargas de trabalho do Oracle Database que exigem alta largura de banda de memória, armazenamento e e/s, mas não uma contagem de núcleo alto.

Ao migrar o software e as cargas de trabalho do Oracle do local para o Microsoft Azure, a Oracle fornece mobilidade de licenças conforme indicado nas [Perguntas frequentes sobre o Oracle no Azure](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)

## <a name="high-availability-and-disaster-recovery-considerations"></a>Considerações sobre alta disponibilidade e recuperação de desastres

Ao usar o banco de dados da Oracle no Azure, você será responsável por implementar uma solução de recuperação de desastre e alta disponibilidade para evitar que haja tempo de inatividade.

É possível obter alta disponibilidade e recuperação de desastre para o Oracle Database Enterprise Edition (sem precisar do Oracle RAC) no Azure usando o [Data Guard, Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) ou o [Oracle Golden Gate](https://www.oracle.com/technetwork/middleware/goldengate), com dois bancos de dados em duas máquinas virtuais separadas. As duas máquinas virtuais devem estar na mesma [rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/) para garantir que podem se acessar mutuamente por um endereço IP privado persistente.  Além disso, é recomendável colocar as máquinas virtuais no mesmo conjunto de disponibilidade para permitir que o Azure as coloque em domínios de falha e domínios de atualização separados. Se você quiser obter redundância geográfica, configure os dois bancos de dados para replicar entre duas regiões diferentes e conecte as duas instâncias com um Gateway de VPN.

O tutorial [Implementar o Oracle Data Guard no Azure](configure-oracle-dataguard.md) orienta você pelo procedimento de configuração básica no Azure.  

Com o Oracle Data Guard, a alta disponibilidade pode ser obtida com um banco de dados primário em uma máquina virtual, um banco de dados secundário (em espera) em outra máquina virtual e replicação unidirecional entre eles. O resultado é o acesso de leitura à cópia do banco de dados. Com o Oracle GoldenGate, você pode configurar a replicação bidirecional entre dois bancos de dados. Para saber como configurar uma solução de alta disponibilidade para seus bancos de dados usando essas ferramentas, veja a documentação do [Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) e do [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) no site da Oracle. Se precisar de acesso de leitura-gravação à cópia do banco de dados, você poderá usar o [Oracle Active Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

O tutorial [Implementar o Oracle GoldenGate no Azure](configure-oracle-golden-gate.md) orienta você pelo procedimento de configuração básica no Azure.

Além de ter uma solução de alta disponibilidade e recuperação de desastre projetada no Azure, você deve ter uma estratégia de backup em vigor para restaurar seu banco de dados. O tutorial [Backup e recuperação de um Oracle Database](./oracle-overview.md) orienta você pelo procedimento básico para estabelecer um backup consistente.

## <a name="support-for-jd-edwards"></a>Suporte para JD Edwards

De acordo com a anotação de Suporte da Oracle [Doc ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4), as versões 9.2 e superiores do JD Edwards EnterpriseOne têm suporte em **quaisquer ofertas de nuvem pública** que atendam aos seus `Minimum Technical Requirements` (MTR) específicos.  Você precisa criar imagens personalizadas que atendam às suas especificações MTR para o sistema operacional e à compatibilidade do aplicativo de software.

## <a name="oracle-weblogic-server-virtual-machine-offers"></a>Ofertas de máquina virtual do Oracle WebLogic Server

A Oracle e a Microsoft estão colaborando para levar o WebLogic Server para o Azure Marketplace na forma de uma coleção de ofertas do Aplicativo Azure.  Essas ofertas são descritas no artigo [Aplicativos Azure do Oracle WebLogic Server](oracle-weblogic.md).

### <a name="oracle-weblogic-server-virtual-machine-images"></a>Imagens de máquina virtual do Oracle WebLogic Server

- **Há suporte para clustering apenas na Enterprise Edition.** Você está licenciado para usar o clustering do WebLogic somente quando usar a Enterprise Edition do Oracle WebLogic Server. Não use clustering com a Standard Edition do Oracle WebLogic Server Standard.
- **O multicast de protocolo UDP não tem suporte.** O Azure dá suporte a unicast UDP, mas não a multicast ou difusão. O Oracle WebLogic Server pode se basear nos recursos de unicast UDP do Azure. Para obter melhores resultados com o unicast UDP, é recomendável que o tamanho do cluster WebLogic seja mantido estático ou que tenha no máximo 10 servidores gerenciados.
- **O Oracle WebLogic Server espera que as portas públicas e privadas sejam as mesmas para o acesso T3 (por exemplo, ao usar o Enterprise JavaBeans).** Considere um cenário de várias camadas em que um aplicativo de serviço de camada (EJB) está em execução em um cluster do Oracle WebLogic Server que consiste em duas ou mais VMs, em uma rede virtual chamada *SLWLS*. A camada do cliente está em uma sub-rede diferente na mesma rede virtual, executando um programa Java simples, tentando chamar o EJB na camada de serviço. Como é necessário balancear a carga da camada de serviço, um ponto de extremidade público com balanceamento de carga precisa ser criado para máquinas virtuais no cluster do Oracle WebLogic Server. Se a porta privada que você especificar for diferente da porta pública (por exemplo, 7006:7008), ocorrerá um erro como o seguinte:

```bash
   [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

   Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]
```

   Isso ocorre porque para qualquer acesso remoto do T3, o Oracle WebLogic Server espera que a porta do balanceador de carga e a porta do servidor gerenciado WebLogic sejam iguais. No caso anterior, o cliente está acessando a porta 7006 (a porta do balanceador de carga) e o servidor gerenciado está escutando na 7008 (a porta privada). Essa restrição se aplica somente ao acesso T3, não HTTP.

   Para evitar esse problema, use uma das seguintes alternativas:

- Use os mesmos números de porta pública e privada para pontos de extremidade com balanceamento de carga dedicados ao acesso T3.
- Inclua o seguinte parâmetro JVM ao iniciar o Oracle WebLogic Server:

```bash
   -Dweblogic.rjvm.enableprotocolswitch=true
```

Para obter informações relacionadas, consulte o artigo KB **860340.1** em <https://support.oracle.com>.

- **Limitações de balanceamento de carga e clustering dinâmico.** Suponha que você queira usar um cluster dinâmico no Oracle WebLogic Server e expô-lo por meio de um ponto de extremidade único, com balanceamento de carga, no Azure. Isso poderá ser feito, desde que você use um número da porta fixa para cada um dos servidores gerenciados (atribuídos dinamicamente dentro de um intervalo) e não inicie mais servidores gerenciados do que o número de máquinas monitoradas pelo administrador. Ou seja, não há mais de um servidor gerenciado por máquina virtual. Se sua configuração fizer com que sejam iniciados mais Oracle WebLogic Servers do que há máquinas virtuais (isto é, várias instâncias do Oracle WebLogic Server compartilharão a mesma máquina virtual), não será possível que mais de uma dessas instâncias dos Oracle WebLogic Servers se associe a um determinado número da porta. Os outros nessa máquina virtual falharão.

   Se você configurar o servidor de administração para atribuir automaticamente números de porta exclusivos para os servidores gerenciados, o balanceamento de carga não será possível porque o Azure não dá suporte ao mapeamento de uma única porta pública para várias portas privadas, como seria necessário para esta configuração.
- **Várias instâncias do Oracle Weblogic Server em uma máquina virtual.** Dependendo dos requisitos da sua implantação, você pode considerar a execução de várias instâncias do Oracle WebLogic Server na mesma máquina virtual, se ela for grande o suficiente. Por exemplo, em uma máquina virtual de médio porte, que contém dois núcleos, você pode optar por executar duas instâncias do Oracle WebLogic Server. No entanto, ainda é recomendado que você evite introduzir pontos únicos de falha em sua arquitetura, como seria o caso se você usasse apenas uma máquina virtual que esteja executando várias instâncias do Oracle WebLogic Server. Usar pelo menos duas máquinas virtuais poderia ser uma abordagem melhor, e cada máquina virtual poderia então executar várias instâncias do Oracle WebLogic Server. Cada instância do Oracle WebLogic Server ainda pode fazer parte do mesmo cluster. No entanto, atualmente não é possível usar o Azure para balancear a carga dos pontos de extremidade expostos por essas implantações do Oracle WebLogic Server na mesma máquina virtual, pois o balanceador de carga do Azure exige que os servidores com balanceamento de carga sejam distribuídos entre máquinas virtuais exclusivas.

## <a name="oracle-jdk-virtual-machine-images"></a>Imagens de máquina virtual no JDK do Oracle

- **Atualizações mais recentes do JDK 6 e 7.** Embora seja recomendável usar a versão mais recente com suporte público do Java (atualmente, o Java 8), o Azure também disponibiliza imagens do JDK 6 e 7. Eles são voltados a aplicativos herdados que ainda não estão prontos para serem atualizado para o JDK 8. Enquanto atualizações para imagens do JDK anteriores podem não estar disponíveis para público em geral, devido à parceria da Microsoft com a Oracle, as imagens do JDK 6 e 7 fornecidas pelo Azure devem conter uma atualização mais recente, não pública, que normalmente é oferecida pela Oracle somente para um grupo selecionado de clientes com suporte da Oracle. As novas versões das imagens do JDK ficarão disponíveis com o passar do tempo, com versões atualizadas do JDK 6 e 7.

   O JDK disponível nas imagens do JDK 6 e 7, assim como as máquinas virtuais e imagens derivadas deles, só podem ser usados dentro do Azure.
- **JDK de 64 bits.** As imagens de máquina virtual do Oracle WebLogic Server e as imagens de máquina virtual do Oracle JDK fornecidas pelo Azure contêm as versões de 64 bits do Windows Server e o JDK.

## <a name="next-steps"></a>Próximas etapas

Agora, você tem uma visão geral das soluções atuais da Oracle nas imagens de máquina virtual no Microsoft Azure. A próxima etapa é implantar seu primeiro banco de dados Oracle no Azure.

> [!div class="nextstepaction"]
> [Criar um banco de dados Oracle no Azure](oracle-database-quick-create.md)
