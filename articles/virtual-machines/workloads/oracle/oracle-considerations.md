---
title: "Soluções da Oracle no Microsoft Azure | Microsoft Docs"
description: "Saiba mais sobre configurações com suporte e as limitações das soluções da Oracle no Microsoft Azure."
services: virtual-machines-linux
documentationcenter: 
manager: timlt
author: rickstercdn
tags: azure-resource-management
ms.assetid: 5d71886b-463a-43ae-b61f-35c6fc9bae25
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/15/2017
ms.author: rclaus
ms.openlocfilehash: 9174f7c8d16ff311312980fbe4d35996ec7ac832
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="oracle-solutions-and-their-deployment-on-microsoft-azure"></a>Soluções da Oracle e sua implantação no Microsoft Azure
Este artigo aborda informações necessárias para implantar com êxito várias soluções da Oracle no Microsoft Azure. Essas soluções são baseadas em imagens de Máquina Virtual publicadas pela Oracle no Azure Marketplace. Para obter uma lista de imagens disponíveis no momento, execute o seguinte comando:
```azurecli-interactive
az vm image list --publisher oracle -o table --all
```
A partir de 16/06/2017, a lista de imagens será a seguinte:
```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170202             12.1.20170202
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170202             12.1.20170202
Oracle-Linux            Oracle       6.4                     Oracle:Oracle-Linux:6.4:6.4.20141206                         6.4.20141206
Oracle-Linux            Oracle       6.7                     Oracle:Oracle-Linux:6.7:6.7.20161007                         6.7.20161007
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20161020                         6.8.20161020
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20170406                         6.9.20170406
Oracle-Linux            Oracle       7.0                     Oracle:Oracle-Linux:7.0:7.0.20141217                         7.0.20141217
Oracle-Linux            Oracle       7.2                     Oracle:Oracle-Linux:7.2:7.2.20161020                         7.2.20161020
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20170320                         7.3.20170320
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

Essas imagens são consideradas "Traga sua própria licença" e como tal, você só será cobrado pelos custos de computação, armazenamento e rede incorridos na execução de uma VM.  Presume-se que você esteja devidamente licenciado para usar o software da Oracle e que tem um contrato de suporte em vigor com a Oracle. A Oracle tem garantia de mobilidade de licença do local para o Azure. Consulte a observação [Oracle e Microsoft](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) publicada para obter detalhes sobre a mobilidade da licença. 

Pessoas também podem optar por basear suas soluções em imagens personalizadas, elas criam imagens do zero no Azure ou fazem upload das imagens personalizadas de seus ambientes locais.

## <a name="support-for-jd-edwards"></a>Suporte para JD Edwards
De acordo com a anotação de Suporte da Oracle [Doc ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4), as versões 9.2 e superiores do JD Edwards EnterpriseOne têm suporte em **quaisquer ofertas de nuvem pública** que atendam aos seu `Minimum Technical Requirements` (MTR) específico.  Você precisará criar imagens personalizadas que atendem às suas especificações MTR para o sistema operacional e a compatibilidade do aplicativo de software. 

## <a name="oracle-database-virtual-machine-images"></a>Imagens de máquina virtual no Oracle Database
A Oracle oferece suporte às edições Oracle DB 12.1 Standard e Enterprise em execução no Azure em imagens de máquina virtual com base no Oracle Linux.  Para obter o melhor desempenho para cargas de trabalho de produção do Banco de dados da Oracle no Azure, certifique-se de dimensionar corretamente a imagem da VM e usar o Managed Disks que teve seu backup realizado pelo armazenamento Premium. Para obter instruções sobre como obter rapidamente um Banco de dados da Oracle em execução no Azure usando a imagem da VM publicada pela Oracle, [repita o passo a passo do início rápido do Banco de dados da Oracle](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Opções de configuração de disco anexado

Os discos anexados se baseiam no serviço de armazenamento de Blobs do Azure. Cada disco padrão pode fornecer uma velocidade máxima teórica de aproximadamente 500 IOPS (operações de entrada/saída por segundo). Nossa oferta de disco premium é preferencial para cargas de trabalho de banco de dados de alto desempenho e pode alcançar até 5.000 IOPS por disco. Embora você possa usar um único disco se isso atender às suas necessidades de desempenho, você poderá melhorar o desempenho efetivo do IOPS se usar vários discos anexados, distribuir os dados do banco de dados entre eles e, em seguida, usar o ASM (Automatic Storage Management) da Oracle. Veja [Visão geral do armazenamento automático da Oracle](http://www.oracle.com/technetwork/database/index-100339.html) para obter mais informações específicas do Oracle ASM. Para obter um exemplo de como instalar e configurar o Oracle ASM em uma VM do Azure Linux - você pode tentar o tutorial [Instalando e configurando o Oracle Automated Storage Management](configure-oracle-asm.md).

### <a name="oracle-realtime-application-cluster-rac"></a>Cluster de aplicativo em tempo real da Oracle (RAC)
O RAC da Oracle foi projetado para atenuar a falha de um único nó em uma configuração de cluster de vários nós local.  Ele se baseia em duas tecnologias locais que não são nativas em ambientes de nuvem pública de hiperescala: multicast de rede e disco compartilhado. Há soluções de terceiros criadas por outras empresas, [como FlashGrid](https://www.flashgrid.io/oracle-rac-in-azure/), que emulam essas tecnologias se você precisar implantar o Oracle RAC no Azure. 

### <a name="high-availability-and-disaster-recovery-considerations"></a>Considerações sobre alta disponibilidade e recuperação de desastres
Ao usar o Banco de Dados da Oracle no Azure, você será responsável por implementar uma solução de recuperação de desastre e alta disponibilidade para evitar que haja tempo de inatividade. 

É possível obter alta disponibilidade e recuperação de desastre para o Oracle Database Enterprise Edition (sem RAC) no Azure usando o [Data Guard, Active Data Guard](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html) ou o [Oracle Golden Gate](http://www.oracle.com/technetwork/middleware/goldengate), com dois bancos de dados em duas máquinas virtuais separadas. As duas máquinas virtuais devem estar na mesma [rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/) para garantir que podem se acessar mutuamente por um endereço IP privado persistente.  Além disso, é recomendável colocar as máquinas virtuais no mesmo conjunto de disponibilidade para permitir que o Azure as coloque em domínios de falha e domínios de atualização separados.  Você deve ter a redundância geográfica - é possível ter esses dois bancos de dados replicados entre duas regiões diferentes e conectar as duas instâncias com um Gateway de VPN.

Temos um tutorial "[Implementar o Oracle DataGuard no Azure](configure-oracle-dataguard.md)" que orienta você por meio do procedimento de configuração básica para a versão de avaliação dele no Azure.  

Com o Oracle Data Guard, a alta disponibilidade pode ser obtida com um banco de dados primário em uma máquina virtual, um banco de dados secundário (em espera) em outra máquina virtual e replicação unidirecional entre eles. O resultado é o acesso de leitura à cópia do banco de dados. Com o Oracle GoldenGate, você pode configurar a replicação bidirecional entre dois bancos de dados. Para saber como configurar uma solução de alta disponibilidade para seus bancos de dados usando essas ferramentas, veja a documentação do [Active Data Guard](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) e do [GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) no site da Oracle. Se precisar de acesso de leitura-gravação à cópia do banco de dados, você poderá usar o [Oracle Active Data Guard](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

Temos um tutorial "[Implementar o Oracle GoldenGate no Azure](configure-oracle-golden-gate.md)" que orienta você por meio do procedimento de configuração básica para a versão de avaliação dele no Azure.

Apesar de ter uma solução de alta disponibilidade e recuperação de desastre projetada no Azure, você desejará garantir que tem uma estratégia de backup em vigor para restaurar seu banco de dados.  Temos um tutorial [Backup e recuperação de um Banco de dados da Oracle](oracle-backup-recovery.md) que orienta o procedimento básico para estabelecer um backup consistente.

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Imagens de máquina virtual do Oracle WebLogic Server
* **Há suporte para clustering apenas na Enterprise Edition.** Você está licenciado para usar o clustering do WebLogic somente quando usar a Enterprise Edition do WebLogic Server. Não use clustering com a Standard Edition do WebLogic Server.
* **O multicast de protocolo UDP não tem suporte.** O Azure dá suporte a unicast UDP, mas não a multicast ou difusão. O WebLogic Server pode se basear nos recursos de unicast UDP do Azure. Para obter melhores resultados com o unicast UDP, é recomendável que o tamanho do cluster WebLogic seja mantido estático ou que no máximo 10 servidores gerenciados sejam incluídos no cluster.
* **O WebLogic Server espera que as portas públicas e privadas sejam as mesmas para o acesso T3 (por exemplo, ao usar o Enterprise JavaBeans).** Considere um cenário de várias camadas em que um aplicativo de serviço de camada (EJB) está em execução em um cluster do WebLogic Server que consiste em duas ou mais VMs, em uma vNet chamada **SLWLS**. A camada do cliente está em uma sub-rede diferente na mesma rede virtual, executando um programa Java simples, tentando chamar o EJB na camada de serviço. Como é necessário balancear a carga da camada de serviço, um ponto de extremidade público com balanceamento de carga precisa ser criado para Máquinas Virtuais no cluster do WebLogic Server. Se a porta privada que você especificar for diferente da porta pública (por exemplo, 7006:7008), ocorrerá um erro como o seguinte:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   Isso ocorre porque para qualquer acesso remoto do T3, o WebLogic Server espera que a porta do balanceador de carga e a porta do servidor gerenciado WebLogic sejam iguais. No caso acima, o cliente está acessando a porta 7006 (a porta do balanceador de carga) e o servidor gerenciado está escutando na 7008 (a porta privada). Essa restrição se aplica somente ao acesso T3, não HTTP.

   Para evitar esse problema, use uma das seguintes alternativas:

  * Use os mesmos números de porta pública e privada para pontos de extremidade com balanceamento de carga dedicados ao acesso T3.
  * Inclua o seguinte parâmetro JVM ao iniciar o WebLogic Server:

         -Dweblogic.rjvm.enableprotocolswitch=true

Para obter informações relacionadas, veja o artigo **860340.1** da Base de Conhecimentos em <http://support.oracle.com>.

* **Limitações de balanceamento de carga e clustering dinâmico.** Suponha que você queira usar um cluster dinâmico no WebLogic Server e expô-lo por meio de um ponto de extremidade único, com balanceamento de carga, no Azure. Isso poderá ser feito, desde que você use um número da porta fixa para cada um dos servidores gerenciados (atribuídos dinamicamente dentro de um intervalo) e não inicie mais servidores gerenciados do que o número de máquinas monitoradas pelo administrador (ou seja, não mais que um servidor gerenciado por máquina virtual). Se sua configuração fizer com que sejam iniciados mais servidores WebLogic do que há máquinas virtuais (isto é, várias instâncias do WebLogic Server compartilharão a mesma máquina virtual), não será possível que mais de uma dessas instâncias do WebLogic Server se associe a um determinado número da porta e as outras na máquina virtual falharão.

   Por outro lado, se você configurar o servidor de administração para atribuir automaticamente números de porta exclusivos para os servidores gerenciados, o balanceamento de carga não será possível porque o Azure não dá suporte ao mapeamento de uma única porta pública para várias portas privadas, como seria necessário para esta configuração.
* **Várias instâncias do Weblogic Server em uma máquina virtual.** Dependendo dos requisitos da sua implantação, você pode considerar a opção de executar várias instâncias do WebLogic Server na mesma máquina virtual, se ela for grande o suficiente. Por exemplo, em uma máquina virtual de tamanho médio, que contém dois núcleos, você poderia optar por executar duas instâncias do WebLogic Server. No entanto, observe que ainda é recomendado que você evite introduzir pontos únicos de falha em sua arquitetura, como seria o caso se você usasse apenas uma máquina virtual que esteja executando várias instâncias do WebLogic Server. Usar pelo menos duas máquinas virtuais poderia ser uma abordagem melhor, e cada uma dessas máquinas virtuais poderia então executar várias instâncias do WebLogic Server. Cada uma dessas instâncias de WebLogic Server ainda pode fazer parte do mesmo cluster. Observe, no entanto, que atualmente não é possível usar o Azure para balancear a carga dos pontos de extremidade expostos por essas implantações do WebLogic Server na mesma máquina virtual, pois o balanceador de carga do Azure exige que os servidores com balanceamento de carga sejam distribuídos entre máquinas virtuais exclusivas.

## <a name="oracle-jdk-virtual-machine-images"></a>Imagens de máquina virtual no JDK do Oracle
* **Atualizações mais recentes do JDK 6 e 7.** Embora seja recomendável usar a versão mais recente com suporte público do Java (atualmente, o Java 8), o Azure também disponibiliza imagens do JDK 6 e 7. Eles são voltados a aplicativos herdados que ainda não estão prontos para serem atualizado para o JDK 8. Enquanto atualizações para imagens do JDK anteriores podem não estar disponíveis para público em geral, devido à parceria da Microsoft com a Oracle, as imagens do JDK 6 e 7 fornecidas pelo Azure devem conter uma atualização mais recente, não pública, que normalmente é oferecida pela Oracle somente para um grupo selecionado de clientes com suporte da Oracle. As novas versões das imagens do JDK ficarão disponíveis com o passar do tempo, com versões atualizadas do JDK 6 e 7.

   O JDK disponível nesse nessas imagens do JDK 6 e 7, as máquinas virtuais e imagens derivadas deles só podem ser usados dentro do Azure.
* **JDK de 64 bits.** As imagens de máquina virtual do Oracle WebLogic Server e as imagens de máquina virtual do Oracle JDK fornecidas pelo Azure contêm as versões de 64 bits do Windows Server e o JDK.

## <a name="next-steps"></a>Próximas etapas
Agora, você tem uma visão geral das soluções atuais da Oracle no Microsoft Azure. A próxima etapa é para ir e implantar seu primeiro Banco de dados da Oracle no Azure.
- Tente o tutorial [Criar um Banco de dados da Oracle no Azure](oracle-database-quick-create.md) para começar.