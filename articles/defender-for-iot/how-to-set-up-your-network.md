---
title: Configurar sua rede
description: Saiba mais sobre a arquitetura da solução, a preparação da rede, os pré-requisitos e outras informações necessárias para garantir que você configurou com êxito sua rede para trabalhar com o Azure defender para dispositivos IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/03/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 4ceedf4d05c6f92e5c32da95f8b54fc6c95de01c
ms.sourcegitcommit: e3151d9b352d4b69c4438c12b3b55413b4565e2f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100526503"
---
# <a name="about-azure-defender-for-iot-network-setup"></a>Sobre a configuração de rede do Azure Defender para IoT

O Azure defender para IoT fornece monitoramento de ameaças e descoberta de dispositivo contínuas do ICS. A plataforma inclui os seguintes componentes:

**Sensores do defender para IOT:** Os sensores coletam o tráfego de rede do ICS usando o monitoramento passivo (sem agente). Passivo e não intrusivo, os sensores têm impacto zero sobre o desempenho em redes e dispositivos de e/ou para IoT. O sensor se conecta a uma porta de SPAN ou toque de rede e imediatamente começa a monitorar a rede. As detecções são exibidas no console do sensor. Lá, você pode exibir, investigar e analisá-los em um mapa de rede, em um inventário de dispositivos e em uma ampla gama de relatórios. Os exemplos incluem relatórios de avaliação de risco, consultas de Data Mining e vetores de ataque. 

**Console de gerenciamento local do defender para IOT**: o console de gerenciamento local fornece uma exibição consolidada de todos os dispositivos de rede. Ele fornece uma visão em tempo real dos indicadores de risco e dos alertas de chave de OT e de todos os seus recursos. Totalmente integrado aos seus fluxos de trabalho e guias estratégicos do SOC, ele permite uma priorização fácil de atividades de mitigação e correlação entre sites de ameaças. 

**Defender para IOT para o portal de IOT:** O aplicativo defender para IoT pode ajudá-lo a adquirir dispositivos de solução, instalar e atualizar software e atualizar pacotes de TI. 

Este artigo fornece informações sobre a arquitetura da solução, a preparação da rede, os pré-requisitos e muito mais para ajudá-lo a configurar com êxito sua rede para trabalhar com o defender para dispositivos IoT. Os leitores que trabalham com as informações neste artigo devem ter experiência em operar e gerenciar redes de OT e IoT. Os exemplos incluem engenheiros de automação, gerentes de fábrica, como provedores de serviços de infraestrutura de rede, equipes segurança cibernéticas, CISOs ou CIOs.

Para obter assistência ou suporte, entre em contato com [suporte da Microsoft](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="on-site-deployment-tasks"></a>Tarefas de implantação no local

As tarefas de implantação de site incluem:

- [Coletar informações do site](#collect-site-information)

- [Preparar uma estação de trabalho de configuração](#prepare-a-configuration-workstation)

- [Planejando a instalação do rack](#planning-rack-installation)

### <a name="collect-site-information"></a>Coletar informações do site

Registrar informações do site, como:

- Informações de rede de gerenciamento de sensor.

- Arquitetura de rede do site.

- Ambiente físico.

- Integrações do sistema.

- Credenciais de usuário planejadas.

- Estação de trabalho de configuração.

- Certificados SSL (opcional, mas recomendado).

- Autenticação SMTP (opcional). Para usar o servidor SMTP com autenticação, prepare as credenciais necessárias para o servidor.

- Servidores DNS (opcional). Prepare o IP do servidor DNS e o nome do host.

Para obter uma lista detalhada e uma descrição das informações importantes do site, consulte [exemplo de catálogo de sites](#example-site-book).

#### <a name="successful-monitoring-guidelines"></a>Diretrizes de monitoramento bem-sucedidas

Para encontrar o local ideal para conectar o dispositivo em cada uma de suas redes de produção, recomendamos que você siga este procedimento:

:::image type="content" source="media/how-to-set-up-your-network/production-network-diagram.png" alt-text="Exemplo de diagrama de configuração de rede de produção.":::

### <a name="prepare-a-configuration-workstation"></a>Preparar uma estação de trabalho de configuração

Prepare uma estação de trabalho do Windows, incluindo o seguinte:

- Conectividade com a interface de gerenciamento do sensor.

- Um navegador com suporte

- Software de terminal, como reproduzida.

Verifique se as regras de firewall necessárias estão abertas na estação de trabalho. Consulte [requisitos de acesso à rede](#network-access-requirements) para obter detalhes.

#### <a name="supported-browsers"></a>Navegadores com suporte

Os navegadores a seguir têm suporte para os sensores e os aplicativos Web do console de gerenciamento local:

- Chrome 32 +

- Microsoft Edge 86 +

- Internet Explorer 10+

### <a name="network-access-requirements"></a>Requisitos de acesso à rede

Verifique se a política de segurança organizacional permite o acesso ao seguinte:

| Protocolo | Transport | Entrada/saída | Porta | Usado | Finalidade | Fonte | Destino |
|--|--|--|--|--|--|--|--|
| HTTPS | TCP | ENTRADA/SAÍDA | 443 | Console Web do console de gerenciamento local e do sensor | Acesso ao console Web | Cliente | Sensor e console de gerenciamento local |
| SSH | TCP | ENTRADA/SAÍDA | 22 | CLI | Acesso à CLI | Cliente | Sensor e console de gerenciamento local |
| SSL | TCP | ENTRADA/SAÍDA | 443 | Sensor e console de gerenciamento local | Conexão entre a plataforma CyberX e a plataforma de gerenciamento central | sensor | Console de gerenciamento local |
| NTP | UDP | IN | 123 | Sincronização de horário | O console de gerenciamento local é usado como NTP para sensor | sensor | console de gerenciamento local |
| NTP | UDP | ENTRADA/SAÍDA | 123 | Sincronização de horário | Sensor conectado ao servidor NTP externo, quando não há um console de gerenciamento local instalado | sensor | NTP |
| SMTP | TCP | OUT | 25 | Email | A conexão entre a plataforma CyberX e a plataforma de gerenciamento e o servidor de email | Sensor e console de gerenciamento local | Servidor de email |
| syslog | UDP | OUT | 514 | LEEF | Logs que enviam do console de gerenciamento local para o servidor syslog | Console de gerenciamento local e sensor | Servidor syslog |
| DNS |  | ENTRADA/SAÍDA | 53 | DNS | Porta do servidor DNS | Console de gerenciamento local e sensor | Servidor DNS |
| LDAP | TCP | ENTRADA/SAÍDA | 389 | Active Directory | A conexão entre a plataforma CyberX e a plataforma de gerenciamento para o Active Directory | Console de gerenciamento local e sensor | Servidor LDAP |
| LDAPS | TCP | ENTRADA/SAÍDA | 636 | Active Directory | A conexão entre a plataforma CyberX e a plataforma de gerenciamento para o Active Directory | Console de gerenciamento local e sensor | Servidor LDAPs |
| SNMP | UDP | OUT | 161 | Monitoramento | Coletores SNMP remotos. | Console de gerenciamento local e sensor | Servidor SNMP |
| WMI | UDP | OUT | 135 | monitoramento | Monitoramento de ponto de extremidade do Windows | Sensor | Elemento de rede relevante |
| Túnel | TCP | IN | 9000 <br /><br />-acima da porta 443 <br /><br />Do usuário final ao console de gerenciamento local. <br /><br />-Porta 22 do sensor para o console de gerenciamento local  | monitoramento | Túnel | Sensor | Console de gerenciamento local |

### <a name="planning-rack-installation"></a>Planejando a instalação do rack

Para planejar a instalação do rack:

1. Prepare um monitor e um teclado para as configurações de rede do seu dispositivo.

1. Aloque o espaço em rack para o dispositivo.

1. Ter energia CA disponível para o dispositivo.
1. Prepare o cabo da LAN para conectar o gerenciamento ao comutador de rede.
1. Prepare os cabos de LAN para conectar portas de switch SPAN (espelho) e ou toques de rede para o dispositivo defender para IoT. 
1. Configure, conecte e valide portas SPAN nos comutadores espelhados, conforme descrito na sessão de revisão da arquitetura.
1. Conecte a porta de SPAN configurada a um computador executando o Wireshark e verifique se a porta está configurada corretamente.
1. Abra todas as portas de firewall relevantes.

## <a name="about-passive-network-monitoring"></a>Sobre o monitoramento de rede passiva

O dispositivo recebe o tráfego de várias fontes, seja alternando portas de espelhamento (portas SPAN) ou por toques de rede. A porta de gerenciamento está conectada à rede de gerenciamento corporativo, corporativa ou de sensor com conectividade a um console de gerenciamento local ou ao portal do defender para IoT.

:::image type="content" source="media/how-to-set-up-your-network/switch-with-port-mirroring.png" alt-text="Diagrama de um comutador gerenciado com espelhamento de porta.":::

### <a name="purdue-model"></a>Modelo Purdue

As seções a seguir descrevem os níveis de Purdue.

:::image type="content" source="media/how-to-set-up-your-network/purdue-model.png" alt-text="Diagrama do modelo Purdue.":::

####  <a name="level-0-cell-and-area"></a>Nível 0: célula e área  

O nível 0 consiste em uma ampla variedade de sensores, atuadores e dispositivos envolvidos no processo de fabricação básico. Esses dispositivos executam as funções básicas do sistema de automação e controle industrial, como:

- Impulsionando um motor.

- Medindo variáveis.
- Definindo uma saída.
- Executar funções-chave, como pintura, soldagem e dobra.

#### <a name="level-1-process-control"></a>Nível 1: controle de processo

O nível 1 consiste em controladores incorporados que controlam e manipulam o processo de fabricação cuja função de chave é se comunicar com os dispositivos de nível 0. Na fabricação discreta, esses dispositivos são PLCs (controladores lógicos programáveis) ou RTUs (unidades de telemetria remotas). Na fabricação do processo, o controlador básico é chamado de DCS (sistema de controle distribuído).

#### <a name="level-2-supervisory"></a>Nível 2: supervisão

O nível 2 representa os sistemas e funções associados à supervisão de tempo de execução e à operação de uma área de um recurso de produção. Isso geralmente inclui o seguinte: 

- Interfaces de operador ou HMIs

- Alarmes ou sistemas de alerta

- Historian de processos e sistemas de gerenciamento de lote

- Estações de trabalho da sala de controle

Esses sistemas se comunicam com o PLCs e o RTUs no nível 1. Em alguns casos, eles se comunicam ou compartilham dados com os sistemas e aplicativos corporativos (nível 4 e nível 5). Esses sistemas são baseados principalmente em equipamentos de computação e sistemas operacionais padrão (UNIX ou Microsoft Windows).

#### <a name="levels-3-and-35-site-level-and-industrial-perimeter-network"></a>Níveis 3 e 3,5: rede de perímetro industrial e em nível de site

O nível do site representa o nível mais alto de sistemas de automação e controle industrial. Os sistemas e aplicativos que existem nesse nível gerenciam funções de controle e automação industrial em todo o site. Os níveis de 0 a 3 são considerados críticos para as operações do site. Os sistemas e funções que existem nesse nível podem incluir o seguinte:

- Relatório de produção (por exemplo, tempos de ciclo, índice de qualidade, manutenção preditiva)

- Historian de fábrica

- Agendamento detalhado de produção

- Gerenciamento de operações no nível do site

- Gerenciamento de dispositivo e material

- Servidor de inicialização de patch

- Servidor de arquivos

- Domínio industrial, Active Directory, Terminal Server

Esses sistemas se comunicam com a zona de produção e compartilham dados com os sistemas e aplicativos corporativos (nível 4 e nível 5).  

#### <a name="levels-4-and-5-business-and-enterprise-networks"></a>Níveis 4 e 5: redes corporativas e empresariais

O nível 4 e nível 5 representam o site ou a rede empresarial onde existem os sistemas e funções de ti centralizados. A organização de ti gerencia diretamente os serviços, sistemas e aplicativos nesses níveis.

## <a name="planning-for-network-monitoring"></a>Planejando o monitoramento de rede

Os exemplos a seguir apresentam diferentes tipos de topologias para redes de controle industrial, juntamente com considerações para o monitoramento e o posicionamento ideais de sensores.

### <a name="what-should-be-monitored"></a>O que deve ser monitorado?

O tráfego que passa pelas camadas 1 e 2 deve ser monitorado.

### <a name="what-should-the-defender-for-iot-appliance-connect-to"></a>O que o dispositivo defender para IoT deve se conectar?

O dispositivo defender para IoT deve se conectar aos comutadores gerenciados que veem as comunicações industriais entre as camadas 1 e 2 (em alguns casos também a camada 3).

O diagrama a seguir é uma abstração geral de uma rede multilocatário de várias camadas, com um ecossistema segurança cibernética extenso, normalmente operado por um SOC e MSSP.

Normalmente, os sensores NTA são implantados nas camadas 0 a 3 do modelo OSI.

:::image type="content" source="media/how-to-set-up-your-network/osi-model.png" alt-text="Diagrama do modelo OSI.":::

#### <a name="example-ring-topology"></a>Exemplo: topologia de anel

A rede de anéis é uma topologia na qual cada switch ou nó se conecta exatamente a duas outras opções, formando um único caminho contínuo para o tráfego.

:::image type="content" source="media/how-to-set-up-your-network/ring-topology.PNG" alt-text="Diagrama da topologia de anel.":::

#### <a name="example-linear-bus-and-star-topology"></a>Exemplo: barramento linear e topologia em estrela

Em uma rede em estrela, cada host é conectado a um hub central. Em sua forma mais simples, um hub central atua como um canal para transmitir mensagens. No exemplo a seguir, as opções inferiores não são monitoradas e o tráfego que permanece local para essas opções não será visto. Os dispositivos podem ser identificados com base em mensagens ARP, mas as informações de conexão estarão ausentes.

:::image type="content" source="media/how-to-set-up-your-network/linear-bus-star-topology.PNG" alt-text="Diagrama do barramento linear e da topologia em estrela.":::

#### <a name="multisensor-deployment"></a>Implantação de multisensor

Aqui estão algumas recomendações para implantar vários sensores:

| **Número** | **Medidores** | **Dependência** | **Número de sensores** |
|--|--|--|--|
| A distância máxima entre as opções | 80 metros | Cabo Ethernet preparado | Mais de 1 |
| Número de redes de OT | Mais de 1 | Sem conectividade física | Mais de 1 |
| Número de opções | Pode usar a configuração do RSPAN | Até oito comutadores com span local perto do sensor por distância do cabeamento | Mais de 1 |

#### <a name="traffic-mirroring"></a>Espelhamento de tráfego  

Para ver apenas informações relevantes para análise de tráfego, você precisa conectar a plataforma defender para IoT a uma porta de espelhamento em um comutador ou um toque que inclua somente o tráfego industrial e SCADA. 

:::image type="content" source="media/how-to-set-up-your-network/switch.jpg" alt-text="Use essa opção para a sua configuração.":::

Você pode monitorar o tráfego de comutador usando os seguintes métodos:

- [Porta do switch SPAN](#switch-span-port)

- [SPAN remoto (RSPAN)](#remote-span-rspan)

- [TOQUE de agregação ativa e passiva](#active-and-passive-aggregation-tap)

SPAN e RSPAN são terminologias da Cisco. Outras marcas de opções têm funcionalidade semelhante, mas podem usar uma terminologia diferente.

#### <a name="switch-span-port"></a>Porta do switch SPAN

Um analisador de porta de switch espelha o tráfego local de interfaces no comutador para a interface no mesmo comutador. Aqui estão algumas considerações:

- Verifique se a opção relevante dá suporte à função de espelhamento de porta.  

- A opção de espelhamento é desabilitada por padrão.

- Recomendamos que você configure todas as portas do comutador, mesmo se nenhum dado estiver conectado a elas. Caso contrário, um dispositivo não autorizado poderá estar conectado a uma porta não monitorada e ele não será alertado no sensor.

- Em redes que utilizam mensagens de difusão ou multicast, configure a opção para espelhar somente as transmissões RX (recebimento). Caso contrário, as mensagens multicast serão repetidas para tantas portas ativas e a largura de banda será multiplicada.

Os exemplos de configuração a seguir são apenas para referência e são baseados em um comutador Cisco 2960 (24 portas) que executa o IOS. Eles são apenas exemplos típicos e, portanto, não os usam como instruções. As portas de espelho em outros sistemas operacionais Cisco e outras marcas de comutadores são configuradas de forma diferente.

:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-terminal-v2.png" alt-text="Diagrama de um terminal de configuração de porta SPAN.":::
:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-mode-v2.png" alt-text="Diagrama de modo de configuração de porta SPAN.":::

##### <a name="monitoring-multiple-vlans"></a>Monitorando várias VLANs

O defender para IoT permite o monitoramento de VLANs configuradas em sua rede. Nenhuma configuração do sistema defender para IoT é necessária. O usuário precisa garantir que a opção em sua rede esteja configurada para enviar marcas de VLAN para o defender para IoT.

O exemplo a seguir mostra os comandos necessários que devem ser configurados no comutador Cisco para habilitar o monitoramento de VLANs no defender para IoT:

**Sessão de monitor**: esse comando é responsável pelo processo de envio de VLANs para a porta SPAN.

- monitorar a interface de origem da sessão 1 Gi1/2

- monitorar o tipo de pacote de filtro da sessão 1 bom RX

- monitorar a interface de destino da sessão 1 fastEthernet1/1 encapsulamento dot1q

**Monitorar a porta de tronco F.E. Gi1/1**: as VLANs estão configuradas na porta de tronco.

- interface GigabitEthernet1/1

- encapsulamento de tronco switchport dot1q

- tronco do modo switchport

#### <a name="remote-span-rspan"></a>SPAN remoto (RSPAN)

A sessão de SPAN remoto espelha o tráfego de várias portas de origem distribuídas em uma VLAN remota dedicada.  

:::image type="content" source="media/how-to-set-up-your-network/remote-span.png" alt-text="Diagrama de alcance remoto.":::

Os dados na VLAN são então entregues por meio de portas de tronco em vários comutadores para um comutador específico que contém a porta de destino física. Essa porta se conecta à plataforma defender para IoT.  

##### <a name="more-about-rspan"></a>Mais sobre o RSPAN

- O RSPAN é um recurso avançado que requer uma VLAN especial para transportar o tráfego que abrange os monitores entre os comutadores. Não há suporte para o RSPAN em todos os comutadores. Verifique se a opção oferece suporte à função RSPAN.

- A opção de espelhamento é desabilitada por padrão.

- A VLAN remota deve ser permitida na porta de tronco entre os comutadores de origem e de destino.

- Todas as opções que conectam a mesma sessão de RSPAN devem ser do mesmo fornecedor.

> [!NOTE]
> Verifique se a porta de tronco que está compartilhando a VLAN remota entre as opções não está definida como uma porta de origem de sessão espelho.
>
> A VLAN remota aumenta a largura de banda na porta de tronco pelo tamanho da largura de banda da sessão espelhada. Verifique se a porta de tronco do comutador oferece suporte a isso.  

:::image type="content" source="media/how-to-set-up-your-network/remote-vlan.jpg" alt-text="Diagrama de VLAN remota.":::

#### <a name="rspan-configuration-examples"></a>Exemplos de configuração do RSPAN

RSPAN: com base no Cisco Catalyst 2960 (24 portas).

**Exemplo de configuração do comutador de origem:**

:::image type="content" source="media/how-to-set-up-your-network/rspan-configuration.png" alt-text="Captura de tela da configuração do RSPAN.":::

1. Insira o modo de configuração global.

1. Crie uma VLAN dedicada.

1. Identifique a VLAN como a VLAN RSPAN.

1. Retorne ao modo "configurar terminal".

1. Configure todas as 24 portas como fontes de sessão.

1. Configure a VLAN RSPAN para ser o destino da sessão.

1. Retornar ao modo EXEC privilegiado.

1. Verifique a configuração de espelhamento de porta.

**Exemplo de configuração do comutador de destino:**

1. Insira o modo de configuração global.

1. Configure a VLAN RSPAN para ser a origem da sessão.

1. Configure a porta física 24 para ser o destino da sessão.

1. Retornar ao modo EXEC privilegiado.

1. Verifique a configuração de espelhamento de porta.

1. Salve a configuração.

#### <a name="active-and-passive-aggregation-tap"></a>TOQUE de agregação ativa e passiva

Um toque de agregação ativo ou passivo é instalado embutido no cabo de rede. Ele duplica RX e TX para o sensor de monitoramento.

O ponto de acesso ao terminal (toque) é um dispositivo de hardware que permite que o tráfego de rede flua da porta A para a porta B e da porta B para a porta A, sem interrupção. Ele cria uma cópia exata de ambos os lados do fluxo de tráfego, continuamente, sem comprometer a integridade da rede. Alguns tocam a transmissão agregada e recebem o tráfego usando as configurações do comutador, se desejado. Se não houver suporte para agregação, cada toque usará duas portas de sensor para monitorar o tráfego de envio e recebimento.

Os toques são vantajosos por vários motivos. Eles são baseados em hardware e não podem ser comprometidos. Eles passam todo o tráfego, até mesmo mensagens danificadas, que comutadores geralmente descartam. Eles não são sensíveis ao processador, portanto, o tempo de pacotes é exato onde as opções manipulam a função de espelho como uma tarefa de baixa prioridade que pode afetar o tempo dos pacotes espelhados. Para fins forenses, um toque é o melhor dispositivo.

Os agregadores TAP também podem ser usados para o monitoramento de porta. Esses dispositivos são baseados em processador e não são tão intrinsecamente seguros como toques de hardware. Eles podem não refletir o tempo exato do pacote.

:::image type="content" source="media/how-to-set-up-your-network/active-passive-tap-v2.PNG" alt-text="Diagrama de toques ativos e passivos.":::

##### <a name="common-tap-models"></a>Modelos de toque comuns

Esses modelos foram testados quanto à compatibilidade. Outros fornecedores e modelos também podem ser compatíveis.

| Imagem | Modelar |
|--|--|
| :::image type="content" source="media/how-to-set-up-your-network/garland-p1gccas-v2.png" alt-text="Captura de tela de Garland P1GCCAS."::: | Garland P1GCCAS |
| :::image type="content" source="media/how-to-set-up-your-network/ixia-tpa2-cu3-v2.png" alt-text="Captura de tela de IXIA TPA2-CU3."::: | IXIA TPA2-CU3 |
| :::image type="content" source="media/how-to-set-up-your-network/us-robotics-usr-4503-v2.png" alt-text="Captura de tela de US Robotics USR 4503."::: | US Robotics USR 4503 |

##### <a name="special-tap-configuration"></a>Configuração especial de toque

| TOQUE Garland | Toque em US Robotics |
| ----------- | --------------- |
| Verifique se os jumpers estão definidos da seguinte maneira:<br />:::image type="content" source="media/how-to-set-up-your-network/jumper-setup-v2.jpg" alt-text="Captura de tela do comutador US Robotics.":::| Verifique se o modo de agregação está ativo. |

## <a name="deployment-validation"></a>Validação da implantação

### <a name="engineering-self-review"></a>Análise automática de engenharia  

Revisar seu diagrama de rede de OT e ICS é a maneira mais eficiente de definir o melhor local para se conectar, onde você pode obter o tráfego mais relevante para o monitoramento.

Os engenheiros de site sabem de que forma sua rede se parece. Ter uma sessão de revisão com a rede local e as equipes operacionais geralmente esclarecerá as expectativas e definirá o melhor local para conectar o dispositivo.

Informações relevantes:

- Lista de dispositivos conhecidos (planilha)  

- Número estimado de dispositivos  

- Fornecedores e protocolos industriais

- Modelo de comutadores, para verificar se a opção de espelhamento de porta está disponível

- Informações sobre quem gerencia os comutadores (por exemplo,) e se são recursos externos

- Lista de redes no site

#### <a name="common-questions"></a>Perguntas comuns

- Quais são os objetivos gerais da implementação? Um inventário completo e um mapa de rede preciso são importantes?

- Existem redes múltiplas ou redundantes no ICS? Todas as redes estão sendo monitoradas?

- Há comunicações entre o ICS e a rede corporativa (Business)? Essas comunicações estão sendo monitoradas?

- As VLANs estão configuradas no design de rede?

- Como a manutenção do ICS é executada, com dispositivos fixos ou transitórios?

- Onde os firewalls são instalados nas redes monitoradas?

- Há algum roteamento nas redes monitoradas?

- O que acontece se os protocolos estão ativos nas redes monitoradas?

- Se conectarmos a esse comutador, veremos a comunicação entre o HMI e o PLCs?

- Qual é a distância física entre os comutadores ICS e o firewall corporativo? 

- As opções não gerenciadas podem ser substituídas por comutadores gerenciados ou o uso da rede toca em uma opção?

- Há alguma comunicação serial na rede? Em caso afirmativo, mostre-o no diagrama de rede.

- Se o dispositivo defender para IoT deve estar conectado a esse comutador, há espaço físico disponível no rack nesse gabinete?

#### <a name="other-considerations"></a>Outras considerações

A finalidade do dispositivo defender para IoT é monitorar o tráfego das camadas 1 e 2.

Para algumas arquiteturas, o dispositivo defender para IoT também monitorará a camada 3, se houver tráfego nessa camada. Enquanto você estiver examinando a arquitetura do site e decidindo se deseja monitorar um comutador, considere as seguintes variáveis:

- Qual é o custo/benefício em relação à importância de monitorar esse comutador?

- Se uma opção não for gerenciada, será possível monitorar o tráfego de um comutador de nível superior?

  Se a arquitetura do ICS for uma topologia de anel, apenas uma opção nesse anel precisará ser monitorada.

- Qual é a segurança ou o risco operacional nesta rede?

- É possível monitorar a VLAN do comutador? A VLAN está visível em outra opção que pode ser monitorada?

#### <a name="technical-validation"></a>Validação técnica

O recebimento de um exemplo de tráfego gravado (arquivo PCAP) da porta switch SPAN (ou espelho) pode ajudar a:

- Valide se a opção está configurada corretamente.

- Confirme se o tráfego que passa pelo comutador é relevante para o monitoramento (no tráfego).

- Identifique a largura de banda e o número estimado de dispositivos nesse comutador.

Você pode gravar um arquivo PCAP de exemplo (alguns minutos) conectando um laptop a uma porta de SPAN já configurada por meio do aplicativo Wireshark.

:::image type="content" source="media/how-to-set-up-your-network/laptop-connected-to-span.jpg" alt-text="Captura de tela de um laptop conectado a uma porta SPAN.":::
:::image type="content" source="media/how-to-set-up-your-network/sample-pcap-file.PNG" alt-text="Captura de tela da gravação de um arquivo PCAP de exemplo.":::

#### <a name="wireshark-validation"></a>Validação do Wireshark

- Verifique se os *pacotes unicast* estão presentes no tráfego de gravação. Unicast é de um endereço para outro. Se a maior parte do tráfego for mensagens ARP, a configuração do comutador estará incorreta.

- Vá para **estatísticas**  >  **hierarquia de protocolos**. Verifique se os protocolos industriais estão presentes.

:::image type="content" source="media/how-to-set-up-your-network/wireshark-validation.png" alt-text="Captura de tela da validação do Wireshark.":::

## <a name="troubleshooting"></a>Solução de problemas

Use estas seções para solucionar problemas:

- [Não é possível conectar usando uma interface da Web](#cant-connect-by-using-a-web-interface)

- [O dispositivo não está respondendo](#appliance-is-not-responding)

### <a name="cant-connect-by-using-a-web-interface"></a>Não é possível conectar usando uma interface da Web

1. Verifique se o computador que você está tentando conectar está na mesma rede que o dispositivo.

2. Verifique se a rede de GUI está conectada à porta de gerenciamento no sensor.

3. Execute ping no endereço IP do dispositivo. Se não houver resposta ao ping:

    1. Conecte um monitor e um teclado ao dispositivo.

    1. Use o usuário e a senha de **suporte** para entrar.

    1. Use a **lista rede** de comandos para ver o endereço IP atual.

    :::image type="content" source="media/how-to-set-up-your-network/list-of-network-commands.png" alt-text="Captura de tela do comando Network List.":::

4. Se os parâmetros de rede estiverem configurados incorretamente, use o seguinte procedimento para alterá-lo:

    1. Use o comando **rede Edit-settings**.

    1. Para alterar o endereço IP da rede de gerenciamento, selecione **Y**.

    1. Para alterar a máscara de sub-rede, selecione **Y**.

    1. Para alterar o DNS, selecione **Y**.

    1. Para alterar o endereço IP do gateway padrão, selecione **Y**.

    1. Para a alteração da interface de entrada (somente para o sensor), selecione **Y**.

    1. Para a interface de ponte, selecione **N**.

    1. Para aplicar as configurações, selecione **Y**.

5. Depois de reiniciar, conecte-se com o suporte ao usuário e use o comando **Network List** para verificar se os parâmetros foram alterados.

6. Tente executar o ping e se conectar novamente a partir da GUI.

### <a name="appliance-is-not-responding"></a>O dispositivo não está respondendo

1. Conecte-se com um monitor e um teclado ao dispositivo ou use a saída para se conectar remotamente à CLI.

2. Use as credenciais de suporte para entrar.

3. Use o comando **System Sanity** e verifique se todos os processos estão em execução.

    :::image type="content" source="media/how-to-set-up-your-network/system-sanity-command.png" alt-text="Captura de tela do comando de sanidade do sistema.":::

Para quaisquer outros problemas, entre em contato com [suporte da Microsoft](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="example-site-book"></a>Exemplo de catálogo de sites

Use o exemplo de catálogo de sites para recuperar e examinar informações importantes necessárias para a configuração de rede.

### <a name="site-checklist"></a>Lista de verificação do site

Examine esta lista antes da implantação do site:

| **#** | **Tarefa ou atividade** | **Status** | **Comentários** |
|--|--|--|--|
| 1 | Solicitar dispositivos. | ☐ |  |
| 2 | Prepare uma lista de sub-redes na rede. | ☐ |  |
| 3 | Forneça uma lista de VLANs das redes de produção. | ☐ |  |
| 4 | Forneça uma lista de modelos de comutador na rede. | ☐ |  |
| 5 | Forneça uma lista de fornecedores e protocolos do equipamento industrial. | ☐ |  |
| 6 | Forneça detalhes da rede para sensores (endereço IP, sub-rede, D-GW, DNS). | ☐ |  |
| 7 | Crie as regras de firewall necessárias e a lista de acesso. | ☐ |  |
| 8 | Crie portas de abrangência em switches para monitoramento de porta ou configure toques de rede conforme desejado. | ☐ |  |
| 9 | Prepare o espaço do rack para dispositivos de sensor. | ☐ |  |
| 10 | Prepare uma estação de trabalho para a equipe. | ☐ |  |
| 11 | Forneça um teclado, monitor e mouse para os dispositivos de rack do defender para IoT. | ☐ |  |
| 12 | Rack e cabeamento dos dispositivos. | ☐ |  |
| 13 | Aloque recursos do site para dar suporte à implantação. | ☐ |  |
| 14 | Crie grupos de Active Directory ou usuários locais. | ☐ |  |
| 15 | Treinamento de configuração (auto-learning). | ☐ |  |
| 16 | Ir ou não ir. | ☐ |  |
| 17 | Agendar a data de implantação. | ☐ |  |


| **Data** | **Observação** | **Data de implantação** | **Observação** |
|--|--|--|--|
| Defender para IoT |  | Nome do site * |  |
| Nome |  | Nome |  |
| Posição |  | Posição |  |

#### <a name="architecture-review"></a>Revisão da arquitetura

Uma visão geral do diagrama de rede industrial permitirá que você defina o local apropriado para o equipamento do defender para IoT.

1.  Exiba um diagrama de rede global do ambiente industrial de OT. Por exemplo:

    :::image type="content" source="media/how-to-set-up-your-network/ot-global-network-diagram.png" alt-text="Diagrama do ambiente industrial de OT para a rede global.":::

    > [!NOTE]
    > O dispositivo defender para IoT deve ser conectado a uma opção de nível inferior que vê o tráfego entre as portas no comutador.  

2. Forneça o número aproximado de dispositivos de rede que serão monitorados. Você precisará dessas informações ao integrar sua assinatura ao Azure defender para o portal de IoT. Durante o processo de integração, você será solicitado a inserir o número de dispositivos em incrementos de 1000.

3. Forneça uma lista de sub-redes para as redes de produção e uma descrição (opcional). 

    |  **#**  | **Nome da sub-rede** | **Descrição** |
    |--| --------------- | --------------- |
    | 1  | |
    | 2  | |
    | 3  | |
    | 4  | |

4. Forneça uma lista de VLANs das redes de produção.

    | **#** | **Nome da VLAN** | **Descrição** |
    |--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

5. Para verificar se os comutadores têm o recurso de espelhamento de porta, forneça os números de modelo de comutação aos quais a plataforma defender para IoT deve se conectar:

    | **#** | **Switch** | **Modelo** | **Suporte ao espelhamento de tráfego (SPAN, RSPAN ou None)** |
    |--|--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

    Uma terceira parte gerencia os comutadores? Y ou N 

    Em caso afirmativo, quem? __________________________________ 

    Qual é sua política? __________________________________ 

    Por exemplo:

    - Siemens

    - Automação do Rockwell – Ethernet ou IP

    - Emerson – DeltaV, Ovation
    
6. Há dispositivos que se comunicam por meio de uma conexão serial na rede? Sim ou não 

    Em caso afirmativo, especifique qual protocolo de comunicação serial: ________________ 

    Em caso afirmativo, marque no diagrama de rede quais dispositivos se comunicam com protocolos seriais e onde eles são: 
 
    <Add your network diagram with marked serial connection> 

7. Para a QoS, a configuração padrão do sensor é 1,5 Mbps. Especifique se você deseja alterá-lo: ________________ 

   Unidade de negócios (BU): ________________ 

### <a name="specifications-for-site-equipment"></a>Especificações para equipamentos do site

#### <a name="network"></a>Rede  

O dispositivo sensor está conectado à porta switch SPAN por meio de um adaptador de rede. Ele está conectado à rede corporativa do cliente para gerenciamento por meio de outro adaptador de rede dedicado.

Forneça detalhes de endereço para a NIC do sensor que será conectada na rede corporativa: 

|  Item               | Dispositivo 1 | Dispositivo 2 | Dispositivo 3 |
| --------------- | ------------- | ------------- | ------------- |
| Endereço IP do dispositivo    |               |               |               |
| Sub-rede          |               |               |               |
| Gateway padrão |               |               |               |
| DNS             |               |               |               |
| Nome do host       |               |               |               |

#### <a name="idraciloserver-management"></a>gerenciamento de iDRAC/iLO/servidor

|       Item          | Dispositivo 1 | Dispositivo 2 | Dispositivo 3 |
| --------------- | ------------- | ------------- | ------------- |
| Endereço IP do dispositivo     |               |               |               |
| Sub-rede          |               |               |               |
| Gateway padrão |               |               |               |
| DNS             |               |               |               |

#### <a name="on-premises-management-console"></a>Console de gerenciamento local  

|       Item          | Ativo | Passivo (ao usar HA) |
| --------------- | ------ | ----------------------- |
| Endereço IP             |        |                         |
| Sub-rede          |        |                         |
| Gateway padrão |        |                         |
| DNS             |        |                         |

#### <a name="snmp"></a>SNMP  

|   Item              | Detalhes |
| --------------- | ------ |
| IP              |        |
| Endereço IP | |
| Nome de Usuário | |
| Senha | |
| Tipo de autenticação | MD5 ou SHA |
| Criptografia | DES ou AES |
| Chave secreta | |
| Cadeia de caracteres da comunidade SNMP v2 |

### <a name="on-premises-management-console-ssl-certificate"></a>Certificado SSL do console de gerenciamento local

Você está planejando usar um certificado SSL? Sim ou não

Em caso afirmativo, qual serviço será usado para gerá-lo? Quais atributos serão incluídos no certificado (por exemplo, domínio ou endereço IP)?

### <a name="smtp-authentication"></a>Autenticação SMTP

Você pretende usar o SMTP para encaminhar alertas para um servidor de email? Sim ou não

Em caso afirmativo, qual método de autenticação será usado?  

### <a name="active-directory-or-local-users"></a>Active Directory ou usuários locais

Contate um administrador de Active Directory para criar um grupo de usuários de Active Directory site ou criar usuários locais. Certifique-se de que seus usuários estejam prontos para o dia de implantação. 

### <a name="iot-device-types-in-the-network"></a>Tipos de dispositivo IoT na rede

| Tipo de dispositivo | Número de dispositivos na rede | Largura de banda média |
| --------------- | ------ | ----------------------- |
| Câmera | |
| Computador com Ray X | |

## <a name="see-also"></a>Consulte também

[Sobre a instalação do defender para IoT](how-to-install-software.md)
