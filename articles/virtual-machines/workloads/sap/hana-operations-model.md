---
title: Modelo de operações do SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Modelo de operações do SAP HANA no Azure (Instâncias Grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: da033232209c304e82f3fbe7dac164bd7222f557
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707363"
---
# <a name="operations-model-and-responsibilities"></a>Responsabilidades e modelo de operações

O serviço fornecido com SAP HANA no Azure (Instâncias Grandes) é alinhado com os serviços do Azure IaaS. Você obtém uma instância de um SAP HANA em Instâncias Grandes com um sistema operacional instalado que é otimizado para o SAP HANA. Assim como com as VMs da IaaS do Azure, a maioria das tarefas de proteção do SO, a instalação de software adicional, instalação do HANA, operação do SO e do HANA e a atualização do SO e do HANA são de sua responsabilidade. A Microsoft não força atualizações do SO nem atualizações do HANA para você.

![Responsabilidades do SAP HANA no Azure (Instâncias Grandes)](./media/hana-overview-architecture/image2-responsibilities.png)

Conforme mostrado no diagrama, o SAP HANA do Azure (Instâncias Grandes) é uma oferta de IaaS com vários locatários. Na maior parte, a divisão de responsabilidades está no limite da infraestrutura do SO. A Microsoft é responsável por todos os aspectos do serviço abaixo da linha do sistema operacional. Você é responsável por todos os aspectos do serviço acima da linha. O SO é de sua responsabilidade. Você pode continuar usando métodos locais mais atuais que podem ser empregados para conformidade, segurança, gerenciamento de aplicativos, base e gerenciamento do SO. Os sistemas aparecem como se estivessem em sua rede em todos os aspectos.

Esse serviço é otimizado para o SAP HANA, portanto, há áreas em que você precisa trabalhar com a Microsoft para usar os recursos de infraestrutura subjacentes para obter melhores resultados.

A lista a seguir fornece mais detalhes sobre cada uma das camadas e suas responsabilidades:

**Rede**: todas as redes internas do carimbo da Instância Grande que executam o SAP HANA. Sua responsabilidade inclui o acesso ao armazenamento, a conectividade entre as instâncias (para escalar horizontalmente e outras funções), a conectividade com a paisagem e conectividade com o Azure, onde a camada de aplicativo do SAP está hospedada nas VMs. Além disso, inclui a conectividade da WAN entre os Data Centers do Azure para fins de replicação de recuperação de desastre. Todas as redes são particionadas pelo locatário e têm qualidade de serviço aplicada.

**Armazenamento**: o armazenamento particionado virtualizado para todos os volumes necessários aos servidores do SAP HANA, bem como para os instantâneos. 

**Servidores**: os servidores físicos dedicados para executar os bancos de dados do SAP HANA atribuídos aos locatários. Os servidores de SKUs de classe do Tipo I são abstraídos do hardware. Com esses tipos de servidores, a configuração do servidor é coletada e mantida em perfis, que podem ser movidos de um hardware físico para outro hardware físico. Uma movimentação desse tipo (manual) de um perfil por operações pode ser comparada um pouco à recuperação de serviço do Azure. Os servidores das SKUs da classe do Tipo II não oferecem essa capacidade.

**SDDC**: o software de gerenciamento usado para gerenciar data centers como entidades definidas por software. Ele permite que a Microsoft reserve recursos em pool por motivos de desempenho, disponibilidade e escala.

**O/S**: SO escolhido (SUSE Linux ou Red Hat Linux) em execução nos servidores. As imagens do SO oferecidas são fornecidas pelo fornecedor individual do Linux à Microsoft para executar o SAP HANA. É necessário ter uma assinatura com o fornecedor do Linux para a imagem específica otimizada do SAP HANA. Você é responsável por registrar as imagens no fornecedor do SO. 

Depois da entrega feita pela Microsoft, você será responsável por qualquer aplicação de patch no sistema operacional Linux. Essa aplicação de patch inclui pacotes adicionais que podem ser necessários para uma instalação com êxito do SAP HANA e que não foram incluídos pelo fornecedor específico do Linux nas imagens do SO otimizadas do SAP HANA. (Para obter mais informações, consulte a documentação de instalação do HANA do SAP e as Notas SAP.) 

Você é responsável pela aplicação de patch do SO em consequência de mau funcionamento ou otimização do SO e dos drivers em relação ao hardware do servidor específico. Adicionalmente, você é responsável pela aplicação de patch funcional e de segurança do SO. 

Sua responsabilidade também inclui monitoramento e planejamento da capacidade de:

- Consumo de recursos de CPU.
- Consumo de memória.
- Volumes de disco relacionados ao espaço livre, IOPS e latência.
- Tráfego de volume de rede entre o SAP HANA em Instâncias Grandes e a camada de aplicativo do SAP.

A infraestrutura subjacente do HANA em Instâncias Grandes fornece a funcionalidade de backup e restauração do volume do sistema operacional. Usar essa funcionalidade também é sua responsabilidade.

**Middleware**: instância do SAP HANA, principalmente. Administração, operações e monitoramento são de sua responsabilidade. Você pode usar a funcionalidade fornecida para usar instantâneos de armazenamento para fins de backup, restauração e recuperação de desastre. Essas funcionalidades são fornecidas pela infraestrutura. Suas responsabilidades também incluem o projeto de alta disponibilidade ou recuperação de desastre com essas funcionalidade, aproveitando-as e monitorando para determinar se os instantâneos de armazenamento foram executados com êxito.

**Data**: seus dados gerenciados pelo SAP HANA e outros dados, como arquivos de backups localizados em volumes ou compartilhamentos de arquivos. Suas responsabilidades incluem monitorar o espaço livre em disco e gerenciar o conteúdo nos volumes. Você também é responsável por monitorar a execução com êxito de backups de volumes de disco e instantâneos de armazenamento. A execução com êxito da replicação de dados em sites de recuperação de desastre é de responsabilidade da Microsoft.

**Aplicativos:** as instâncias de aplicativos do SAP ou, no caso de aplicativos não SAP, a camada de aplicativos desses aplicativos. Suas responsabilidades incluem implantação, administração, operações e monitoramento desses aplicativos. Você é responsável pelo planejamento da capacidade de consumo de recursos de CPU, consumo de memória, consumo de armazenamento do Azure e consumo de largura de banda de rede dentro de redes virtuais. Além disso, você é responsável pelo planejamento da capacidade para consumo de recursos de redes virtuais para o SAP HANA no Azure (Instâncias Grandes).

**WANs**: as conexões que você estabelece do local para implantações do Azure para cargas de trabalho. Todos os clientes com o SAP HANA em Instâncias Grandes usam o Azure ExpressRoute para conectividade. Essa conexão não faz parte da solução do SAP HANA do Azure (Instâncias Grandes). Você é responsável pela configuração dessa conexão.

**Arquivar**: talvez você queira arquivar cópias de dados usando seus próprios métodos nas contas de armazenamento. O arquivamento requer gerenciamento, conformidade, custos e operações. Você é responsável por gerar cópias e backups de arquivos no Azure e armazená-los de maneira compatível.

Configurações para o SAP HANA Veja o [SLA para o SAP HANA no Azure (Instâncias Grandes)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/).

**Próximas etapas**
- Confira [Arquitetura do SAP HANA (Instâncias Grandes) no Azure](hana-architecture.md)
