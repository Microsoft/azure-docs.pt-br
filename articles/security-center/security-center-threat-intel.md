---
title: "Inteligência contra Ameaças da Central de Segurança do Azure | Microsoft Docs"
description: "Saiba como usar o recurso de inteligência contra ameaças na Central de Segurança do Azure para identificar possíveis ameaças nas VMs e nos computadores."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: b5bdeb1c70893c2477413df02c7b1a4a07999e93
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="threat-intelligence-in-azure-security-center"></a>Inteligência contra Ameaças da Central de Segurança do Azure
Este artigo ensina a usar a inteligência contra ameaças da Central de Segurança do Azure para tratar de problemas de segurança.

## <a name="what-is-threat-intelligence"></a>O que é inteligência contra ameaças?
Ao usar a opção Inteligência contra Ameaças disponível na Central de Segurança, os administradores de TI poderão identificar as ameaças à segurança no ambiente. Por exemplo, eles podem identificar se determinado computador faz parte de um botnet. Os computadores podem se tornar nós em um botnet quando os invasores instalam de forma ilícita malware que conecta secretamente o computador ao comando e ao controle. A inteligência contra ameaças também pode identificar possíveis ameaças recebidas de canais de comunicação escondidos, como a dark Web.

Para criar essa inteligência contra ameaças, a Central de Segurança usa dados recebidos de várias fontes da Microsoft. A Central de Segurança usa esses dados para identificar possíveis ameaças ao seu ambiente. O painel **Inteligência contra ameaças** é formado por três opções principais:

- Tipos de ameaças detectados
- Origem da ameaça
- Mapa de inteligência contra ameaças


## <a name="when-should-you-use-threat-intelligence"></a>Quando devo usar a inteligência contra ameaças?
Uma das etapas de um processo de [resposta a incidentes de segurança](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) é identificar a gravidade dos sistemas comprometidos. Nesta fase, você deverá realizar as seguintes tarefas:

- Determinar a natureza do ataque.
- Determinar o ponto de ataque de origem.
- Determinar o objetivo do ataque. O ataque foi direcionado à sua organização para adquirir informações específicas ou foi aleatório?
- Identificar os sistemas que foram comprometidos.
- Identificar os arquivos que foram acessados e determinar a confidencialidade desses arquivos.

Você pode usar informações de inteligência contra ameaças na Central de Segurança para ajudar nessas tarefas. 

## <a name="access-the-threat-intelligence"></a>Acessar a inteligência contra ameaças
Para visualizar a inteligência contra ameaças atual do seu ambiente, você deve primeiro selecionar o espaço de trabalho onde estão suas informações. Se você não tiver vários espaços de trabalho, ignore o seletor de espaço de trabalho e vá diretamente para o painel **Inteligência contra ameaças**. Para acessar o painel:

1. Abra o painel **Central de Segurança**.

2. No painel esquerdo, em **Detecção** selecione **Inteligência contra ameaças**. O painel **Inteligência contra ameaças** é exibido.

    ![Painel Inteligência contra ameaças](./media/security-center-threat-intel/security-center-threat-intel-fig1.png)

    > [!NOTE]
    > Se a coluna à direita mostra **ATUALIZAR PLANO**, esse espaço de trabalho está usando a assinatura gratuita. Atualize para Standard a fim de usar esse recurso. Se a coluna à direita mostra **REQUER ATUALIZAÇÃO**, atualize o [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) para usar esse recurso. Para saber mais sobre o plano de preço, leia Preços da Central de Segurança do Azure. 
    > 
3. Se você tem mais de um espaço de trabalho para investigar, priorize a investigação de acordo com a coluna **IP Mal-intencionado**. Ela mostra o número atual de IPs mal-intencionadas no espaço de trabalho. Selecione o espaço de trabalho que você deseja usar e o painel **Inteligência contra ameaças** será exibido.

    ![Informações de inteligência contra ameaças](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

4. O painel é dividido em quatro blocos:

    a.  **Tipos de ameaça**. Resume os tipos de ameaça que foram detectados no espaço de trabalho selecionado.

    b.  **País de origem**. Agrega a quantidade de tráfego de acordo com seu local de origem.

    c.  **Local da ameaça**. Ajuda a identificar os locais atuais em todo o mundo que se comunicam com o seu ambiente. No mapa mostrado, setas em laranja (entrada) e vermelho (saída) identificam o sentido do tráfego. Se você seleciona uma essas setas, o tipo de ameaça e o sentido do tráfego são exibidos.

    d.  **Detalhes das ameaças**. Mostra mais detalhes sobre a ameaça que você selecionou no mapa.

Independentemente do bloco de opções selecionado, o painel exibido se baseia na consulta da Pesquisa de Logs. A única diferença é o tipo de consulta e o resultado.

### <a name="threat-types"></a>Tipos de ameaça
Selecione o bloco **Tipos de ameaça** para abrir o painel **Pesquisa de Logs**. As opções de filtro aparecem à esquerda e os resultados da consulta aparecem à direita.

![Pesquisa de log](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

O resultado da consulta mostra as ameaças por nome. Você pode usar o painel esquerdo para selecionar o atributo que deseja filtrar. Por exemplo, para ver apenas as ameaças que estão conectadas às máquinas no momento, em **ESTADO DE SESSÃO**, selecione **Conectado** > **Aplicar**.

![Estado de sessão](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

Para VMs do Azure, somente os dados de rede que fluem pelo agente aparecem no painel **Inteligência contra ameaças**. Os seguintes tipos de dados também são usados pela inteligência contra ameaças:

- Dados CEF (Type=CommonSecurityLog)
- WireData (Type= WireData)
- Logs do IIS (Type=W3CIISLog)
- Firewall do Windows (Type=WindowsFirewall)
- Eventos do DNS (Type=DnsEvents)


## <a name="see-also"></a>Consulte também
Neste artigo, você aprendeu a usar a inteligência contra ameaças na Central de Segurança para ajudar a identificar atividades suspeitas. Para saber mais sobre a Central de Segurança, confira estes artigos:

* [Gerencie e responda a alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerenciar alertas e responder a incidentes de segurança na Central de Segurança.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md). Saiba como monitorar a integridade dos recursos do Azure.
* [Noções básicas de alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de solução de problemas da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como solucionar problemas comuns na Central de Segurança. 
* [Perguntas Frequentes sobre a Central de Segurança do Azure](security-center-faq.md). Encontre respostas para perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/). Encontre postagens no blog sobre a conformidade e segurança do Azure.

