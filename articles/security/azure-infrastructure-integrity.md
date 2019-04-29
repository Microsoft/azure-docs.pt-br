---
title: Integridade da infraestrutura do Azure
description: Este artigo aborda a integridade da infraestrutura do Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 24d54fa7a8985a6af58cddfc969b8023485c73c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587114"
---
# <a name="azure-infrastructure-integrity"></a>Integridade da infraestrutura do Azure

## <a name="software-installation"></a>Instalação de software
Todos os componentes da pilha de software instalados no ambiente do Azure são personalizados, seguindo o processo de SDL (Microsoft Security Development Lifecycle). Todos os componentes de software, incluindo imagens do sistema operacional (SO) e banco de dados SQL, são implantados como parte do processo de gerenciamento de alterações e gerenciamento de versões. O sistema operacional que é executado em todos os nós é uma versão personalizada do Windows Server 2008 ou do Windows Server 2012. A versão exata é escolhida pelo controlador de malha (FC) de acordo com a função que pretende que o sistema operacional execute. Além disso, o sistema operacional host não permite a instalação de componentes de software não autorizados.

Alguns componentes do Azure são implantados como clientes do Azure em uma VM guest em execução em um sistema operacional convidado.

## <a name="virus-scans-on-builds"></a>Verificações de vírus em compilações
As compilações do componente de software do Azure (incluindo o SO) precisam passar por uma verificação de vírus que usa a ferramenta antivírus do Endpoint Protection. Cada verificação de vírus cria um log no diretório de criação associado, detalhando o que foi verificado e os resultados da verificação. A verificação de vírus faz parte do código-fonte do build para todos os componentes do Azure. O código não é movido para produção sem uma verificação de vírus limpa e bem-sucedida. Se algum problema for observado, a compilação será congelada e, em seguida, será encaminhada às equipes de segurança do Microsoft Security para identificar onde o código "invasor" entrou na compilação.

## <a name="closed-and-locked-environment"></a>Ambiente fechado e bloqueado
Por padrão, os nós de infraestrutura do Azure e as VMs convidadas não têm contas de usuário criadas neles. Além disso, as contas de administrador do Windows padrão também são desabilitadas. Os administradores do suporte ao vivo do Azure podem, com a autenticação adequada, fazer logon nessas máquinas e administrar a rede de produção do Azure para reparos de emergência.

## <a name="azure-sql-database-authentication"></a>Autenticação do Banco de Dados SQL do Azure
Assim como acontece com qualquer implementação do SQL Server, o gerenciamento de conta de usuário precisa ser controlado rigorosamente. O Banco de Dados SQL do Azure oferece suporte apenas à autenticação do SQL Server. Para complementar o modelo de segurança de dados de um cliente, as contas de usuário com senhas fortes e configuradas com direitos específicos também devem ser usadas.

## <a name="acls-and-firewalls-between-the-microsoft-corporate-network-and-an-azure-cluster"></a>ACLs e firewalls entre a rede corporativa da Microsoft e um cluster do Azure
As listas de controle de acesso (ACLs) e os firewalls entre a plataforma de serviço e a rede corporativa da Microsoft protegem as instâncias do Banco de Dados SQL contra acesso privilegiado não autorizado. Além disso, somente usuários de intervalos de endereços IP da rede corporativa da Microsoft podem acessar o ponto de extremidade de gerenciamento de plataforma do Windows Fabric.

## <a name="acls-and-firewalls-between-nodes-in-a-sql-database-cluster"></a>ACLs e firewalls entre nós em um cluster do banco de dados SQL
Como uma proteção adicional, como parte da estratégia de defesa em profundidade, ACLs e um firewall foram implementados entre nós em um cluster do Banco de Dados SQL. Toda a comunicação dentro do cluster da plataforma Windows Fabric, bem como todo o código em execução, é confiável.

## <a name="custom-monitoring-agents"></a>Agentes de monitoramento personalizado
Banco de dados SQL emprega monitoramento agentes personalizados (SMA), também chamados de watchdogs, para monitorar a integridade do cluster de banco de dados SQL.

## <a name="web-protocols"></a>Protocolos da Web

### <a name="role-instance-monitoring-and-restart"></a>Monitoramento e reinicialização de instância de função
O Azure garante que todas as funções implantadas e em execução (funções de processamento de back-end ou da Web voltada para a Internet) sejam sujeitas a um monitoramento de integridade sustentado para garantir que eles forneçam de forma eficiente os serviços para os quais foram provisionados. Se uma função tornar-se não íntegra, seja por uma falha crítica no aplicativo que está sendo hospedado ou por um problema de configuração subjacente na própria instância de função, o FC detecta o problema na instância de função e inicia um estado corretivo.

### <a name="compute-connectivity"></a>Conectividade de computação
O Azure garante que o aplicativo ou serviço implantado seja acessível por meio de protocolos padrão baseados na Web. As instâncias virtuais de funções da Web voltadas para a Internet têm conectividade externa à Internet e podem ser acessadas diretamente pelos usuários da Web. Para proteger a sensibilidade e integridade das operações que as funções de trabalho executam em nome das instâncias virtuais de função da Web acessíveis ao público, as instâncias virtuais de funções de trabalho de processamento de backend têm conectividade de Internet externa, mas não podem ser acessadas diretamente por usuários da Web externos.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, confira:

- [Recursos, local e segurança física do Azure](azure-physical-security.md)
- [Disponibilidade da infraestrutura do Azure](azure-infrastructure-availability.md)
- [Limites e componentes do sistema de informações do Azure](azure-infrastructure-components.md)
- [Arquitetura de rede do Azure](azure-infrastructure-network.md)
- [Rede de produção do Azure](azure-production-network.md)
- [Recursos de segurança do Banco de Dados SQL do Azure](azure-infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Monitoramento de infraestrutura do Microsoft Azure](azure-infrastructure-monitoring.md)
- [Proteção de dados do cliente do Microsoft Azure](azure-protection-of-customer-data.md)
