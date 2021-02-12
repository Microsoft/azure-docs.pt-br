---
title: Azure Defender para Serviço de Aplicativo – Benefícios e recursos
description: Conheça as funcionalidades do Azure Defender para o Serviço de Aplicativo e saiba como habilitá-lo em sua assinatura
author: memildin
ms.author: memildin
ms.date: 01/25/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 99da711038e1fcea3e14584fde09ccd1f79feffa
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916689"
---
# <a name="introduction-to-azure-defender-for-app-service"></a>Introdução ao Azure Defender para Serviço de Aplicativo

O Serviço de Aplicativo do Azure é uma plataforma totalmente gerenciada para criar e hospedar seus aplicativos Web e APIs. Como a plataforma é totalmente gerenciada, você não precisa se preocupar com a infraestrutura. Ele fornece gerenciamento, monitoramento e informações operacionais para atender aos requisitos de desempenho, segurança e conformidade em nível empresarial. Para obter mais informações, confira [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/).

O **Azure Defender para Serviço de Aplicativo** usa a escala da nuvem para identificar os ataques direcionados a aplicativos em execução no Serviço de Aplicativo. Os invasores investigam aplicativos Web para encontrar e explorar pontos fracos. Antes de serem roteadas para ambientes específicos, as solicitações para aplicativos em execução no Azure passam por vários gateways, onde são inspecionadas e registradas. Esses dados são usados para identificar explorações e invasores e para aprender novos padrões que serão usados posteriormente.


## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (Disponibilidade Geral)|
|Preço:|O [Azure Defender para Serviço de Aplicativo](azure-defender.md) é cobrado conforme mostrado na [página de preços](security-center-pricing.md)<br>A página de preços e configurações lista o número de instâncias para sua **Quantidade de Recursos**. Esse número é o total de instâncias de computação, em todos os planos do Serviço de Aplicativo nessa assinatura, em execução no momento em que você abriu a página de tipo de preço.<br>Para validar a contagem, abra os **planos do Serviço de Aplicativo** no portal do Azure e confira o número de instâncias de computação usadas que cada plano usou.|
|Planos do Serviço de Aplicativo compatíveis:|![Sim](./media/icons/yes-icon.png) Básico, Padrão, Premium, Isolado ou Linux<br>![Não](./media/icons/no-icon.png) Gratuito, Compartilhado ou Consumo<br>[Saiba mais sobre os Planos do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/plans/)|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Não](./media/icons/no-icon.png) Nacionais/soberanas (US Gov, China Gov, outros Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-app-service"></a>Quais são os benefícios do Azure Defender para o Serviço de Aplicativo?

Quando você habilita o Azure Defender para o Serviço de Aplicativo, você se beneficia imediatamente dos seguintes serviços oferecidos por esse plano do Azure Defender:

- **Proteger** – a Central de Segurança avalia os recursos cobertos pelo Plano do Serviço de Aplicativo e gera recomendações de segurança com base nas descobertas. Use as instruções detalhadas nessas recomendações para proteger os recursos do Serviço de Aplicativo.

- **Detectar** – o Azure Defender detecta inúmeras ameaças em seus recursos do Serviço de Aplicativo monitorando:
    - a instância da VM na qual o Serviço de Aplicativo está sendo executado e a interface de gerenciamento dela
    - as solicitações e respostas trocadas entre os aplicativos do Serviço de Aplicativo
    - as áreas de proteção e VMs subjacentes
    - Logs internos do Serviço de Aplicativo – disponíveis graças à visibilidade que o Azure tem como provedor de nuvem

Como uma solução nativa de nuvem, o Azure Defender pode identificar as metodologias de ataque que são aplicadas a vários destinos. Por exemplo, em um host, seria difícil identificar um ataque distribuído de um pequeno subconjunto de IPs avançando para pontos de extremidade semelhantes em vários hosts.

Os dados de log e a infraestrutura podem contar a história: desde um novo ataque que está ocorrendo na prática até comprometimentos nos computadores dos clientes. Portanto, mesmo que a Central de Segurança seja implantada depois que um aplicativo Web for explorado, ela poderá detectar ataques contínuos.


## <a name="what-threats-can-azure-defender-for-app-service-detect"></a>Quais ameaças o Azure Defender para o Serviço de Aplicativo pode detectar?

### <a name="threats-by-mitre-attck-tactics"></a>Ameaças de táticas MITRE ATT&CK

O Azure Defender monitora muitas ameaças aos recursos do Serviço de Aplicativo. Os alertas abrangem quase toda a lista de táticas MITRE ATT&CK, desde pré-ataque até comando e controle. O Azure Defender pode detectar:

- **Ameaças de pré-ataque** – o Defender pode detectar a execução de vários tipos de scanners de vulnerabilidade que os invasores frequentemente usam para investigar os aplicativos em busca de pontos fracos.

- **Ameaças de acesso inicial** - [A Inteligência contra Ameaças da Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684) alimenta esses alertas que incluem um disparo quando um endereço IP mal-intencionado conhecido se conecta à interface FTP do Serviço de Aplicativo do Azure.

- **Ameaças à execução** – o Defender pode detectar tentativas de executar comandos de alto privilégio, comandos do Linux em um Serviço de Aplicativo do Windows, comportamento de ataque sem arquivos, ferramentas de mineração de moeda digital e muitas outras atividades suspeitas e mal-intencionadas de execução de código.

### <a name="dangling-dns-detection"></a>Detecção de DNS pendente

O Azure Defender para o Serviço de Aplicativo também identifica as entradas de DNS restantes em seu registrador DNS quando um site do Serviço de Aplicativo é desativado (elas são conhecidas como entradas de DNS pendente). Quando você remove um site da Web e não remove o domínio personalizado dele do seu registrador DNS, a entrada de DNS aponta para um recurso que não existe e seu subdomínio fica vulnerável a uma tomada de controle. O Azure Defender não verifica seu registrador DNS em busca de entradas de DNS pendente *existentes*; ele alerta você quando um site do Serviço de Aplicativo é desativado e o domínio personalizado dele (entrada de DNS) não é excluído.

As invasões de subdomínios são uma ameaça comum de gravidade alta para as organizações. Quando um ator da ameaça detecta a entrada de DNS pendente, ele cria o próprio site no endereço de destino. O tráfego pretendido para o domínio da organização é direcionado ao site do ator da ameaça, e ele pode usar esse tráfego para uma ampla gama de atividades mal-intencionadas.

A proteção de DNS pendente estará disponível caso seus domínios sejam gerenciados usando o DNS do Azure ou um registrador de domínios externo. Além disso, ela se aplica ao Serviço de Aplicativo no Windows e no Linux.

:::image type="content" source="media/defender-for-app-service-introduction/dangling-dns-alert.png" alt-text="Um exemplo de um alerta do Azure Defender sobre uma entrada de DNS pendente descoberta. Habilite o Azure Defender para o Serviço de Aplicativo para receber esse e outros alertas para o seu ambiente." lightbox="media/defender-for-app-service-introduction/dangling-dns-alert.png":::

Saiba mais sobre o DNS pendente e a ameaça de invasão de subdomínio em [Impedir entradas de DNS pendente e evitar invasão de subdomínio](../security/fundamentals/subdomain-takeover.md).

Para obter uma lista completa dos alertas do Serviço de Aplicativo do Azure, confira a [Tabela de referência de alertas](alerts-reference.md#alerts-azureappserv).

> [!NOTE]
> O Defender poderá não disparar alertas de DNS pendente se seu domínio personalizado não apontar diretamente para um recurso do Serviço de Aplicativo ou se o Defender não tiver monitorado o tráfego para seu site desde que a proteção de DNS pendente foi habilitada (porque não haverá logs para ajudar a identificar o domínio personalizado).

## <a name="how-to-protect-your-azure-app-service-web-apps-and-apis"></a>Como proteger os seus aplicativos Web e APIs do Serviço de Aplicativo do Azure

Para proteger seu Plano do Serviço de Aplicativo do Azure com o Azure Defender para Serviço de Aplicativo:

1. Verifique se você tem um Plano do Serviço de Aplicativo compatível, associado aos computadores dedicados. Os planos compatíveis são listados acima em [Disponibilidade](#availability).

2. Habilite o **Azure Defender** em sua assinatura conforme descrito em [Preços da Central de Segurança do Azure](security-center-pricing.md).

    Opcionalmente, você pode habilitar planos individuais no Azure Defender (como o Azure Defender para o Serviço de Aplicativo).

    A Central de Segurança é integrada de maneira nativa ao Serviço de Aplicativo, eliminando a necessidade de implantação e integração – A integração é transparente.


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o Azure Defender para Serviço de Aplicativo. 

Para obter material relacionado, consulte os seguintes artigos: 

- Para exportar seus alertas para o Azure Sentinel, qualquer SIEM de terceiros ou qualquer outra ferramenta externa, siga as instruções em [Transmitir alertas para uma solução de Gerenciamento de Serviço de TI, SOAR ou SIEM](export-to-siem.md).
- Para obter uma lista dos alertas do Azure Defender para o Serviço de Aplicativo, confira a [Tabela de referência de alertas](alerts-reference.md#alerts-azureappserv).
- Para saber mais sobre os planos do Serviço de Aplicativo, confira [Planos do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/plans/).
> [!div class="nextstepaction"]
> [Habilitar o Azure Defender](security-center-pricing.md#enable-azure-defender)