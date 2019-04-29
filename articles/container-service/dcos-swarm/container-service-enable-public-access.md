---
title: (PRETERIDO) Habilitar o acesso ao aplicativo de contêiner de DC/SO do Azure
description: Como habilitar o acesso público aos contêineres de DC/OS no Serviço de Contêiner do Azure.
services: container-service
author: sauryadas
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 08/26/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 3e4ba15fa1925ca40ad7760acbd14331fbdd1343
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61457160"
---
# <a name="deprecated-enable-public-access-to-an-azure-container-service-application"></a>(PRETERIDO) Habilitar acesso público a um aplicativo do Serviço de Contêiner do Azure

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Todos os contêineres DC/OS no [pool de agentes públicos](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) do ACS são automaticamente expostos à Internet. Por padrão, as portas **80**, **443**, **8080** estão abertas e qualquer contêiner (público) que esteja ouvindo nessas portas estará acessível. Este artigo mostra como abrir mais portas para seus aplicativos no Serviço de Contêiner do Azure.

## <a name="open-a-port-portal"></a>Abrir uma porta (portal)
Primeiro, é necessário abrir a porta que queremos.

1. Faça logon no portal.
2. Localize o grupo de recursos em que você implantou o Serviço de Contêiner do Azure.
3. Selecione o balanceador de carga do agente (que tem um nome semelhante a **XXXX-agent-lb-XXXX**).
   
    ![Balanceador de carga do serviço de contêiner do Azure](./media/container-service-enable-public-access/agent-load-balancer.png)
4. Clique em **Investigações** e então em **Adicionar**.
   
    ![Investigações do balanceador de carga do serviço de contêiner do Azure](./media/container-service-enable-public-access/add-probe.png)
5. Preencha o formulário de investigação e clique em **OK**.
   
   | Campo | DESCRIÇÃO |
   | --- | --- |
   | NOME |Um nome descritivo da investigação. |
   | Porta |A porta do contêiner a ser testado. |
   | Caminho |(Quando em modo HTTP) O caminho relativo do site para investigação. Não há suporte para HTTPS. |
   | Interval |O tempo entre as tentativas de investigação, em segundos. |
   | Limite não íntegro |Número de tentativas consecutivas de investigação antes de considerar o contêiner não íntegro. |
6. De volta às propriedades do balanceador de carga do agente, clique em **Regras de balanceamento de carga** e em **Adicionar**.
   
    ![Regras do balanceador de carga do serviço de contêiner do Azure](./media/container-service-enable-public-access/add-balancer-rule.png)
7. Preencha o formulário do balanceador de carga e clique em **OK**.
   
   | Campo | DESCRIÇÃO |
   | --- | --- |
   | NOME |Um nome descritivo do balanceador de carga. |
   | Porta |A porta de entrada pública. |
   | Porta de back-end |A porta pública interna do contêiner para o qual o tráfego será roteado. |
   | Pool de back-end |Os contêineres nesse pool serão o destino para este balanceador de carga. |
   | Investigação |A investigação usada para determinar se um destino no **Pool de back-end** está íntegro. |
   | Persistência de sessão |Determina como o tráfego de um cliente deve ser tratado durante a sessão.<br><br>**Nenhum**: solicitações sucessivas do mesmo cliente podem ser manipuladas por qualquer contêiner.<br>**IP do cliente**: solicitações sucessivas do mesmo IP do cliente são manipuladas pelo mesmo contêiner.<br>**IP do cliente e protocolo**: solicitações sucessivas da mesma combinação de IP e o protocolo de cliente são manipuladas pelo mesmo contêiner. |
   | Tempo limite de ociosidade |(Somente TCP) Em minutos, o tempo para manter um cliente TCP/HTTP aberto sem depender de mensagens *keep-alive* . |

## <a name="add-a-security-rule-portal"></a>Adicionar uma regra de segurança (portal)
Em seguida, precisamos adicionar uma regra de segurança que encaminhe o tráfego da nossa porta aberta por meio do firewall.

1. Faça logon no portal.
2. Localize o grupo de recursos em que você implantou o Serviço de Contêiner do Azure.
3. Selecione o grupo de segurança de rede de agente **público** (que tem um nome semelhante a **XXXX-agent-public-nsg-XXXX**).
   
    ![Grupo de segurança de rede do serviço de contêiner do Azure](./media/container-service-enable-public-access/agent-nsg.png)
4. Selecione **Regras de segurança de entrada** e **Adicionar**.
   
    ![Regras do grupo de segurança de rede do serviço de contêiner do Azure](./media/container-service-enable-public-access/add-firewall-rule.png)
5. Preencha a regra de firewall para permitir sua porta pública e clique em **OK**.
   
   | Campo | DESCRIÇÃO |
   | --- | --- |
   | NOME |Um nome descritivo da regra do firewall. |
   | Prioridade |Classificação de prioridade para a regra. Quanto menor o número, maior a prioridade. |
   | Fonte |Restrinja o intervalo de endereços IP de entrada a ser permitido ou negado por essa regra. Use **Qualquer** para não especificar uma restrição. |
   | O Barramento de |Selecione um conjunto de serviços predefinidos para os quais foi definida essa regra de segurança. Caso contrário, use **Personalizada** para criar suas próprias. |
   | Protocolo |Restrinja o tráfego baseado em **TCP** ou **UDP**. Use **Qualquer** para não especificar uma restrição. |
   | Intervalo de portas |Quando **Serviço** for **Personalizado**, especifica o intervalo de portas afetadas por essa regra. Você pode usar uma única porta, como **80** ou um intervalo, como **1024–1500**. |
   | Ação |Permita ou negue o tráfego que atenda aos critérios. |

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a diferença entre [agentes DC/OS públicos e privados](container-service-dcos-agents.md).

Leia mais informações sobre [como gerenciar seus contêineres de DC/OS](container-service-mesos-marathon-ui.md).

