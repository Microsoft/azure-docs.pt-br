---
title: Quais são as soluções para executar o Oracle WebLogic Server em máquinas virtuais do Azure
description: Saiba como executar o Oracle WebLogic Server em Máquinas Virtuais do Microsoft Azure.
author: rezar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 09/23/2020
ms.author: rezar
ms.reviewer: cynthn
ms.openlocfilehash: 277e9049d2069190a68dd98c22acd60212c1b99d
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486684"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-azure-virtual-machines"></a>Quais são as soluções para executar o Oracle WebLogic Server em Máquinas Virtuais do Azure?

Esta página descreve as soluções para executar o Oracle WebLogic Server (WLS) em máquinas virtuais do Azure. Essas soluções são desenvolvidas e suportadas em conjunto pela Oracle e pela Microsoft.

Também é possível executar o WLS no serviço kubernetes do Azure. As soluções para fazer isso são descritas neste [artigo da Microsoft](./weblogic-aks.md).

O WLS é um servidor de aplicativos Java líder que executa alguns dos aplicativos Java empresariais de missão crítica em todo o mundo. O WLS constitui a base de middleware para o Oracle Software Suite. A Oracle e a Microsoft estão comprometidas em capacitar os clientes do WLS com opções e flexibilidade para executar cargas de trabalho no Azure como uma plataforma de nuvem líder.

As soluções do Azure WLS são destinadas a torná-las tão fáceis quanto possível de mover e deslocar seus aplicativos Java EE para máquinas virtuais do Azure, automatizando a maioria das operações de texto clichê. As soluções provisionam automaticamente os recursos de rede virtual, armazenamento, Java e Linux. Com o mínimo de esforço, o WebLogic Server está instalado. As soluções podem configurar a segurança com um grupo de segurança de rede, o balanceamento de carga com Azure App gateway e a autenticação com Azure Active Directory. Você também pode se conectar automaticamente ao banco de dados existente, incluindo o PostgreSQL do Azure, o SQL Azure e o Oracle DB no Oracle Cloud ou no Azure. O mapa de estrada para as soluções inclui a capacidade de habilitar o registro em log distribuído e o Caching distribuído via Oracle coerência.  

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="Você pode usar o portal do Azure para implantar o WebLogic Server no Azure":::

Há quatro ofertas disponíveis para atender a diferentes cenários: [nó único sem um servidor de administração](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls), [nó único com um servidor de administração](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin), [cluster](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)e [cluster dinâmico](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster). As ofertas estão disponíveis gratuitamente. Essas ofertas são descritas e vinculadas abaixo.

_Essas ofertas são do tipo traga sua própria licença_. Eles supõem que você já tenha as licenças apropriadas com o Oracle e que estejam devidamente licenciados para executar ofertas no Azure.

As ofertas dão suporte a uma variedade de versões de sistema operacional, Java e WLS por meio de imagens base (como WebLogic Server 14 e JDK 11 em Oracle Linux 7,6). Essas imagens básicas também estão disponíveis no Azure por conta própria. As imagens base são adequadas para clientes que exigem implantações personalizadas do Azure complexas. O conjunto atual de imagens base está disponível [aqui](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?search=WebLogic%20Server%20Base%20Image&page=1).

_Se você estiver interessado em trabalhar de acordo com seus cenários de migração com a equipe de engenharia desenvolvendo essas ofertas, selecione o botão [entrar em contato comigo](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)_ na [página Visão geral da oferta do Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview). Gerentes de programas, arquitetos e engenheiros entrarão em contato novamente em breve e começarão a colaboração. A oportunidade de colaborar em um cenário de migração é gratuita enquanto as ofertas estão sob o desenvolvimento inicial ativo.

## <a name="oracle-weblogic-server-single-node"></a>Nó único do Oracle WebLogic Server

[Essa oferta](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls) provisiona uma única máquina virtual e instala o WLS nela. Ele não cria um domínio ou inicia o servidor de administração. A oferta de nó único é útil para cenários com configuração de domínio altamente personalizada.

## <a name="oracle-weblogic-server-with-admin-server"></a>Servidor do Oracle WebLogic com Servidor de Administração

[Essa oferta](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin) provisiona uma única máquina virtual e instala o WLS nela. Ele cria um domínio e inicia o servidor de administração. Você pode gerenciar o domínio e começar a usar implantações de aplicativo imediatamente.

## <a name="oracle-weblogic-server-cluster"></a>Cluster do Oracle WebLogic Server

[Esta oferta](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster) cria um cluster altamente disponível de máquinas virtuais do WLS. O servidor de administração e todos os servidores gerenciados são iniciados por padrão. Você pode gerenciar o cluster e começar a usar aplicativos altamente disponíveis imediatamente.

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Cluster Dinâmico do Oracle WebLogic Server

[Esta oferta](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster) cria um cluster dinâmico altamente disponível e escalonável de máquinas virtuais do WLS. O servidor de administração e todos os servidores gerenciados são iniciados por padrão.

As soluções permitirão uma ampla gama de arquiteturas de implantação prontas para produção com uma facilidade relativa. Você pode atender à maioria dos casos de migração da maneira mais produtiva possível, permitindo um foco no desenvolvimento de aplicativos de negócios.

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-vms.png" alt-text="Implantações complexas do WebLogic Server estão habilitadas no Azure":::

Além do que é provisionado automaticamente pelas soluções, os clientes têm flexibilidade total para personalizar ainda mais suas implantações. É provável que, na parte superior da implantação de aplicativos, os clientes integrem outros recursos do Azure às suas implantações. Os clientes são incentivados a fornecer comentários sobre como melhorar ainda mais as soluções.

## <a name="next-steps"></a>Próximas etapas

Explore as ofertas no Azure.

> [!div class="nextstepaction"]
> [Nó único do Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [Servidor do Oracle WebLogic com Servidor de Administração](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Cluster do Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Cluster Dinâmico do Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)