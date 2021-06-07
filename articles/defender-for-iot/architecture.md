---
title: O que á a arquitetura da solução sem agente
description: Saiba mais sobre o fluxo de informações e a arquitetura sem agente do Azure Defender para IoT.
ms.topic: overview
ms.date: 1/25/2021
ms.author: shhazam
ms.openlocfilehash: bd639ca2656b901bd87fccf36fb4a5d90d3bf918
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383534"
---
# <a name="azure-defender-for-iot-architecture"></a>Arquitetura do Azure Defender para IoT

Este artigo descreve a arquitetura do sistema funcional da solução sem agente do Defender para IoT. O Azure Defender para IoT oferece dois conjuntos de funcionalidades para atender às necessidades do seu ambiente: uma solução sem agente para organizações e uma solução baseada em agente para criadores de dispositivos.

## <a name="agentless-solution-for-organizations"></a>Solução sem agente para organizações
### <a name="defender-for-iot-components"></a>Componentes do Defender para IoT

O Defender para IoT conecta-se à nuvem do Azure e aos componentes locais. A solução foi projetada para escalabilidade em ambientes grandes e geograficamente distribuídos com várias localizações remotas. Essa solução permite uma arquitetura distribuída de várias camadas por país, região, unidade de negócios ou zona. 

O Azure Defender para IoT inclui os seguintes componentes: 

**Implantações conectadas na nuvem**

- VM ou dispositivo de sensor do Azure Defender para IoT
- Portal do Azure para gerenciamento de nuvem e integração ao Azure Sentinel
- Console de gerenciamento local para gerenciamento de sites locais
- Um agente de segurança inserido (opcional)

**Implantações desconectadas (offline)**

- VM ou dispositivo de sensor do Azure Defender para IoT
- Console de gerenciamento local para gerenciamento de sites locais

:::image type="content" source="./media/architecture/defender-iot-security-architecture-v3.png" alt-text="A arquitetura do Defender para IoT.":::

### <a name="azure-defender-for-iot-sensors"></a>Sensores do Azure Defender para IoT

Os sensores do Defender para IoT detectam e monitoram continuamente os dispositivos de rede. Os sensores coletam o tráfego de rede do ICS usando o monitoramento passivo (sem agente) nos dispositivos IoT e OT. 
 
Desenvolvida especificamente para redes IoT e OT, a tecnologia sem agente oferece visibilidade profunda dos riscos de IoT e OT em minutos de serem conectados à rede. Ela tem impacto zero no desempenho na rede e nos dispositivos de rede devido à abordagem não invasiva de NTA (análise de tráfego de rede). 
 
Aplicando a análise de comportamento patenteado com reconhecimento de IoT e OT e a DPI (Inspeção Profunda de Pacotes) de Camada 7, ela permite que você faça análises além das soluções tradicionais baseadas em assinatura para detectar imediatamente as ameaças avançadas de IoT e de OT (como malware sem arquivos) com base em atividades anormais ou não autorizadas. 
  
Os sensores do Defender para IoT se conectam a uma porta SPAN ou a um TAP de rede e começam imediatamente a executar a DPI no tráfego de rede de IoT e OT. 
 
A coleta de dados, o processamento, a análise e os alertas ocorrem diretamente no sensor. Esse processo o torna ideal para localizações com baixa largura de banda ou conectividade de alta latência, pois apenas os metadados são transferidos para o console de gerenciamento.

O sensor inclui cinco mecanismos de detecção de análise. Os mecanismos disparam alertas com base na análise do tráfego em tempo real e pré-gravado. Os seguintes mecanismos estão disponíveis: 

#### <a name="protocol-violation-detection-engine"></a>Mecanismo de detecção de violação de protocolo
O mecanismo de detecção de violação de protocolo identifica o uso de estruturas de pacote e valores de campo que violam as especificações do protocolo ICS, por exemplo: exceção do Modbus e inicialização de um alerta de código de função obsoleto.

#### <a name="policy-violation-detection-engine"></a>Mecanismo de detecção de violação de política
Usando o machine learning, o mecanismo de detecção de violação de política alerta os usuários de qualquer desvio do comportamento de linha de base, como o uso não autorizado de códigos de função específicos, o acesso a objetos específicos ou alterações na configuração do dispositivo. Por exemplo: alteração da versão do software DeltaV e alertas de programação PLC não autorizados. Especificamente, o mecanismo de violação de política modela as redes ICS como sequências determinísticas de estados e transições usando uma técnica patenteada chamada IFSM (Modelagem de Estado Finito Industrial). O mecanismo de detecção de violação de política estabelece uma linha de base das redes ICS, de modo que a plataforma exija um período de aprendizado mais curto para criar uma linha de base da rede do que as abordagens ou as análises matemáticas genéricas, originalmente desenvolvidas para a TI em vez das redes OT.

#### <a name="industrial-malware-detection-engine"></a>Mecanismo de detecção de malware industrial
O mecanismo de detecção de malware industrial identifica comportamentos que indicam a presença de malwares conhecidos, como o Conficker, o Black Energy, o Havex, o WannaCry, o NotPetya e o Triton. 

#### <a name="anomaly-detection-engine"></a>Mecanismo de detecção de anomalias
O mecanismo de detecção de anomalias detecta comportamentos e comunicações M2M (entre computadores) incomuns. Ao modelar redes ICS como sequências determinísticas de estados e transições, a plataforma exige um período de aprendizado mais curto do que as abordagens ou análises matemáticas genéricas originalmente desenvolvidas para a TI, em vez da OT. Ela também detecta anomalias mais rapidamente, com um mínimo de falsos positivos. Os alertas do mecanismo de detecção de anomalias incluem tentativas de entrada de SMB excessivas e alertas de verificação de PLC detectada.

#### <a name="operational-incident-detection"></a>Detecção de incidentes operacionais
A detecção de incidentes operacionais detecta problemas operacionais, como conectividade intermitente que pode indicar sinais antecipados de falha de equipamentos. Por exemplo, o dispositivo é considerado desconectado (sem resposta), e o comando Siemens S7 stop PLC recebeu alertas.

### <a name="management-consoles"></a>Consoles de gerenciamento
O gerenciamento do Azure Defender para IoT em ambientes híbridos é realizado por meio de dois portais de gerenciamento: 
- Console do sensor
- O console de gerenciamento local
- O portal do Azure

### <a name="sensor-console"></a>Console do sensor
As detecções de sensor são exibidas no console do sensor, em que podem ser vistas, investigadas e analisadas em um mapa de rede, inventário de dispositivos e em uma ampla gama de relatórios, por exemplo, relatórios de avaliação de risco, consultas de mineração de dados e vetores de ataque. Use também o console para ver as ameaças detectadas por mecanismos de sensor e lidar com elas, encaminhar informações para sistemas de parceiros, gerenciar usuários, entre outros.

:::image type="content" source="./media/architecture/sensor-console-v2.png" alt-text="Console do sensor do Defender para IoT":::

### <a name="on-premises-management-console"></a>Console de gerenciamento local
O console de gerenciamento local permite que os operadores do SOC (centro de operações de segurança) gerenciem e analisem alertas agregados de vários sensores em um só painel e fornece uma visão geral da integridade das redes OT.

Essa arquitetura fornece uma exibição unificada abrangente da rede em um nível de SOC, o tratamento otimizado de alertas e o controle da segurança de rede operacional, garantindo que a tomada de decisões e o gerenciamento de riscos permaneçam sem falhas.

Além de multilocação, monitoramento, análise de dados e controle remoto de sensor centralizado, o console de gerenciamento fornece ferramentas extras de manutenção de sistema (como exclusão de alertas) e recursos de relatório totalmente personalizados para cada um dos dispositivos remotos. Essa arquitetura dá suporte ao gerenciamento local no nível de site e de zona e ao gerenciamento global no SOC.

O console de gerenciamento pode ser implantado para configuração de alta disponibilidade, que fornece um console de backup que recebe periodicamente backups de todos os arquivos de configuração necessários para a recuperação. Se o console primário falhar, os dispositivos de gerenciamento de sites locais realizarão failover automaticamente para sincronização com o console de backup a fim de manter a disponibilidade sem interrupção.

Totalmente integrado aos seus runbooks e seus fluxos de trabalho do SOC, ele permite uma priorização fácil de atividades de mitigação e correlação de ameaças entre sites.

- Holístico: reduza a complexidade com uma plataforma unificada para gerenciamento de dispositivos, gerenciamento de riscos e vulnerabilidades e monitoramento de ameaças com resposta a incidentes.

- Agregação e correlação: veja, agregue e analise dados e alertas coletados de todos os sites.

- Controle de todos os sensores: configure e monitore todos os sensores de uma só localização.

   :::image type="content" source="media/updates/alerts-and-site-management-v2.png" alt-text="Gerencie todos os seus alertas e as suas informações.":::

### <a name="azure-portal"></a>Portal do Azure

O portal do Defender para IoT no Azure é usado para ajudar você a:

- Comprar dispositivos de solução

- Instalar e atualizar software

- Integrar sensores ao Azure

- Atualizar pacotes de inteligência contra ameaças

## <a name="next-steps"></a>Próximas etapas

[Perguntas frequentes sobre o Defender para IoT](resources-frequently-asked-questions.md)

[Pré-requisitos do sistema](quickstart-system-prerequisites.md)
