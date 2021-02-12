---
title: Alertas de segurança na Central de Segurança do Azure | Microsoft Docs
description: Este tópico explica o que são os alertas de segurança e os diferentes tipos disponíveis na Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 10/05/2020
ms.author: memildin
ms.openlocfilehash: b26d42ca32f0048bb2bedcb5fef337c3ac1d7d65
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807217"
---
# <a name="security-alerts-in-azure-security-center"></a>Alertas de segurança na Central de Segurança do Azure

Na central de segurança, há uma variedade de alertas para vários tipos de recursos diferentes. A Central de Segurança gera alertas para recursos implantados no Azure e também para recursos implantados em ambientes de nuvens híbrida e locais.

Os alertas de segurança são disparados por detecções avançadas e estão disponíveis somente com o Azure defender. Há uma avaliação gratuita disponível. Você pode atualizar da página de **configurações de & de preços** . Saiba mais sobre os [preços da central de segurança](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="respond-to-todays-threats"></a>Responder às ameaças <a name="respond-threats"></a> atuais

Houve alterações significativas no panorama de ameaças nos últimos 20 anos. Antigamente, as empresas normalmente só precisavam preocupar-se com a desfiguração do site por invasores individuais, que basicamente tinham interesse em ver “o que poderiam fazer”. Os hackers de hoje em dia são muito mais sofisticados e organizados. Eles geralmente têm objetivos estratégicos e financeiros específicos. Eles também têm mais recursos disponíveis, pois podem ser financiados por Estados da nação ou crimes organizados.

As mudanças nessa realidade levaram a um nível de profissionalismo nas classificações do invasor sem precedentes. Eles não estão mais interessados em desfiguração da Web. Agora, eles estão interessados em roubo de informações, de contas financeiras e de dados privados, que podem usar para gerar dinheiro no mercado aberto ou para se aproveitar de um negócio específico, para fins políticos ou militares. Ainda mais preocupante que esses invasores com um objetivo financeiro, são os invasores que violam as redes prejudicar a infraestrutura e pessoas.

Em resposta, as organizações geralmente implantam várias soluções pontuais, com foco em defender o perímetro ou os pontos de extremidade da empresa procurando assinaturas de ataques conhecidos. Essas soluções tendem a gerar um alto volume de alertas de baixa fidelidade, que exigem que um analista de segurança faça a triagem e investigue. A maioria das organizações não têm o tempo e o conhecimento necessário para responder a esses alertas; vários ficam sem investigação.  

Além disso, os invasores têm evoluído seus métodos para examinar as várias defesas baseadas em assinatura e [adaptar-se a ambientes em nuvem](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Novas abordagens são necessárias para identificar ameaças emergentes e agilizar a detecção e a resposta mais rapidamente.

## <a name="what-are-security-alerts-and-security-incidents"></a>O que são alertas de segurança e incidentes de segurança? 

**Alertas** são as notificações que a Central de Segurança gera quando detecta ameaças em seus recursos. A Central de Segurança prioriza e lista os alertas, juntamente com as informações necessárias para que você investigue rapidamente o problema. A Central de Segurança também fornece recomendações sobre como você pode corrigir um ataque.

**Um incidente de segurança** é uma coleção de alertas relacionados, em vez de listar cada alerta individualmente. A Central de Segurança usa [Correlação de alertas inteligentes de nuvem](security-center-alerts-cloud-smart.md) para correlacionar alertas diferentes e sinais de baixa fidelidade em incidentes de segurança.

Usando incidentes, a Central de Segurança fornece uma única exibição de uma campanha de ataque e todos os alertas relacionados. Essa exibição permite que você entenda rapidamente as ações tomadas pelo invasor e quais recursos foram afetados. Para saber mais, confira [Correlação de alertas inteligentes de nuvem](security-center-alerts-cloud-smart.md).



## <a name="how-does-security-center-detect-threats"></a>Como a Central de Segurança detecta ameaças? <a name="detect-threats"> </a>

Os pesquisadores de segurança da Microsoft estão constantemente à procura de ameaças. Devido à nossa presença global na nuvem e no local, temos acesso a um conjunto extenso de telemetria. A coleção de grandes e diversificações de conjuntos de clientes nos permite descobrir novos padrões de ataque e tendências em nossos produtos de negócios e empresas locais, bem como nossa serviços online. Como resultado, a Central de Segurança pode atualizar rapidamente seus algoritmos de detecção conforme os invasores lançam explorações novas e cada vez mais sofisticadas. Isso ajuda a acompanhar o ritmo de um ambiente de ameaças que muda rapidamente.

A fim de detectar ameaças e reduzir os falsos positivos, a Central de Segurança coleta, analisa e integra os dados de registro dos recursos do Azure e da rede. Ele também funciona com soluções de parceiros conectadas, como firewall e soluções de proteção de ponto de extremidade. A Central de Segurança analisa essas informações geralmente correlacionando informações de várias fontes para identificar ameaças.

![Apresentação e coleta de dados da Central de Segurança](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

A Central de Segurança emprega análise de segurança avançada, que vai além das abordagens baseadas em assinatura. Inovações em tecnologias de big data e [aprendizado de máquina](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) são usadas para aproveitar os eventos de avaliação em toda a malha de nuvem, detectando ameaças que seriam impossíveis de identificar usando abordagens manuais e prevendo a evolução de ataques. Essas análises de segurança incluem:

* **Inteligência integrada contra ameaças**: A Microsoft tem uma grande quantidade de inteligência contra ameaças globais. A telemetria flui de várias fontes, como Azure, Microsoft 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, unidade de crimes digitais da Microsoft (DCU) e Microsoft Security Response Center (MSRC). Os pesquisadores também recebem informações de inteligência de ameaça que são compartilhadas entre os principais provedores de serviços de nuvem e feeds de terceiros. A Central de Segurança do Azure pode usar essas informações para alertá-lo de ameaças vindas de maus atores conhecidos.

* **Análise comportamental**: A análise de comportamento é uma técnica que analisa e compara dados em uma coleção de padrões conhecidos. No entanto, esses padrões não são assinaturas simples. Eles são determinados por meio de algoritmos de aprendizado de máquina complexos que são aplicados a grandes conjuntos de dados. Eles também são determinados pela análise cuidadosa de comportamentos mal-intencionados por analistas especialistas. A Central de Segurança do Azure pode usar a análise de comportamento para identificar recursos comprometidos baseado na análise dos logs de máquina virtual, dos logs de dispositivo de rede virtual, dos logs da malha e de outras fontes.

* **Detecção de anomalias**: A Central de Segurança do Azure também usa detecção de anomalias para identificar ameaças. Ao contrário da análise de comportamento (que depende de padrões conhecidos derivados de grandes conjuntos de dados), a detecção de anomalias é mais "personalizada" e se concentra nas linhas de base que são específicas das suas implantações. O aprendizado de máquina é aplicado para determinar a atividade normal das implantações e, em seguida, as regras são geradas para definir condições de exceção que possam representar um evento de segurança.

## <a name="how-are-alerts-classified"></a>Como os alertas são classificados?

A Central de Segurança atribui uma severidade aos alertas, para ajudá-lo a priorizar a ordem em que você participa de cada alerta, para que, quando um recurso for comprometido, você possa acessá-lo imediatamente. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta.

> [!NOTE]
> A severidade do alerta é exibida diferente no portal e nas versões da API REST antes de 01-01-2019. Se você estiver usando uma versão mais antiga da API, atualize para a experiência consistente descrita abaixo.

- **Alta:** Há uma grande probabilidade de que o recurso seja comprometido. Você deve analisá-lo imediatamente. A Central de Segurança tem alta confiança em ambas as más intenções e em descobertas usadas para emitir o alerta. Por exemplo, um alerta que detecta a execução de uma ferramenta mal-intencionada conhecida como Mimikatz, uma ferramenta comum usada para roubo de credenciais.
- **Médio:** Isso provavelmente é uma atividade suspeita que pode indicar que um recurso está comprometido.
A confiança da Central de Segurança na análise ou localização é média e a confiança de más intenções é média a alta. Normalmente, essas seriam machine learning ou detecções baseadas em anomalia. Por exemplo, uma tentativa de conexão de um local anômalo.
- **Baixa:** Isso pode ser um positivo benigno ou um ataque bloqueado.
   * A central de segurança não tem confiança suficiente de que a intenção é mal-intencionada e a atividade pode ser inocente. Por exemplo, o log Clear é uma ação que pode acontecer quando um invasor tenta ocultar suas faixas, mas em muitos casos é uma operação de rotina executada pelos administradores.
   * A Central de Segurança geralmente não informa quando ataques foram bloqueados, a menos que seja um caso interessante que sugerimos que você examine. 
- **Informacional:** Você só verá alertas informativos ao fazer busca detalhada em um incidente de segurança ou se você usar a API REST com uma determinada ID de alerta. Um incidente normalmente é composto por vários alertas, alguns dos quais podem aparecer por conta própria para serem apenas informativos, mas no contexto dos outros alertas podem ser mais valiosos. 

## <a name="continuous-monitoring-and-assessments"></a>Monitoramento e avaliações contínuos

A Central de Segurança do Azure aproveita o fato de ter equipes de pesquisas de segurança e de ciência de dados em toda a Microsoft, que monitoram continuamente em busca de alterações no panorama de ameaças. Isso inclui as seguintes iniciativas:

* **Monitoramento de inteligência contra ameaças**: A inteligência contra ameaças inclui mecanismos, indicadores, implicações e recomendações acionáveis sobre ameaças existentes ou iminentes. Essas informações são compartilhadas na comunidade de segurança e a Microsoft monitora continuamente os feeds de inteligência contra ameaças de fontes internas e externas.
* **Compartilhamento de sinal**: Ideias de equipes de segurança de todo o amplo portfólio da Microsoft de serviços locais e de nuvem, servidores e dispositivos de ponto de extremidade cliente são compartilhadas e analisadas.
* **Especialistas de segurança da Microsoft**: Comprometimento contínuo com as equipes da Microsoft que trabalham em campos de segurança especializada, como computação forense e detecção de ataque à Web.
* **Ajuste de detecção**: Algoritmos são executados em conjuntos de dados de clientes reais e os pesquisadores de segurança trabalham com os clientes para validar os resultados. Verdadeiros e falsos positivos são usados para refinar os algoritmos de aprendizado de máquina.

Esses esforços combinados culminam em detecções novas e aprimoradas de que você pode se beneficiar instantaneamente. Não há nenhuma ação a ser tomada.

## <a name="export-alerts"></a>Exportar alertas

Você tem uma variedade de opções para exibir seus alertas fora da central de segurança, incluindo:

- **Baixar o relatório CSV** no painel alertas fornece uma única exportação para CSV.
- A **exportação contínua** de configurações de & de preços permite que você configure fluxos de alertas de segurança e recomendações para log Analytics espaços de trabalho e hubs de eventos. [Saiba mais sobre a exportação contínua](continuous-export.md)
- O **conector do Azure Sentinel** transmite alertas de segurança da central de segurança do Azure para o Azure Sentinel. [Saiba mais sobre como conectar a central de segurança do Azure com o Azure Sentinel](../sentinel/connect-azure-security-center.md)


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre os diferentes tipos de alertas disponíveis na Central de Segurança. Para obter mais informações, consulte:

- **Alertas de segurança no Log de Atividades do Azure** - além de estarem disponíveis no portal do Azure ou programaticamente, os alertas de segurança e incidentes são auditados como eventos no [Log de Atividades do Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log). Para saber mais sobre o esquema de eventos, confira [Alertas de segurança no log de atividades do Azure](https://go.microsoft.com/fwlink/?linkid=2114113)