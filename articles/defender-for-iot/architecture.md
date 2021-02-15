---
title: Arquitetura da solução sem agente
description: Saiba mais sobre o fluxo de informações e a arquitetura sem agente do Azure defender para IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/25/2021
ms.author: shhazam
ms.openlocfilehash: 28449c4e80e22548d2f727e5ad330ab487528847
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100521893"
---
# <a name="azure-defender-for-iot-architecture"></a>Arquitetura do Azure defender para IoT

Este artigo descreve a arquitetura do sistema funcional da solução do defender for IoT sem agente. O Azure defender para IoT oferece dois conjuntos de recursos para atender às necessidades do seu ambiente, solução sem agente para organizações e solução baseada em agente para criadores de dispositivos.

## <a name="agentless-solution-for-organizations"></a>Solução sem agente para organizações
### <a name="defender-for-iot-components"></a>Componentes do defender para IoT

O defender para IoT conecta-se à nuvem do Azure e a componentes locais. A solução foi projetada para escalabilidade em ambientes grandes e geograficamente distribuídos com vários locais remotos. Essa solução permite uma arquitetura distribuída de várias camadas por país, região, unidade de negócios ou zona. 

O Azure defender para IoT inclui os seguintes componentes: 

**Implantações conectadas na nuvem**

- VM ou dispositivo de sensor do Azure defender para IoT
- portal do Azure para gerenciamento de nuvem e integração ao Azure Sentinel
- Console de gerenciamento local para gerenciamento de sites locais
- Um agente de segurança inserido (opcional)

**Implantações de ar-gapped (offline)**

- VM ou dispositivo de sensor do Azure defender para IoT
- Console de gerenciamento local para gerenciamento de sites locais

:::image type="content" source="./media/architecture/defender-iot-security-architecture-v3.png" alt-text="A arquitetura do defender para IoT.":::

### <a name="azure-defender-for-iot-sensors"></a>Sensores do Azure defender para IoT

Os sensores do defender for IoT detectam e monitoram continuamente os dispositivos de rede. Os sensores coletam o tráfego de rede do ICS usando o monitoramento passivo (sem agente) nos dispositivos IoT e OT. 
 
Desenvolvido especificamente para IoT e para redes, a tecnologia sem agente oferece visibilidade profunda do IoT e tem risco em minutos de serem conectados à rede. Ele tem impacto zero no desempenho na rede e nos dispositivos de rede devido à sua abordagem não invasiva de NTA (análise de tráfego de rede). 
 
Aplicando a análise de comportamento patenteado, de IoT e de OT e a inspeção de pacotes (DPI) de camada 7, ele permite que você analise além das soluções tradicionais baseadas em assinatura para detectar imediatamente as ameaças avançadas de IoT e de OT (como malware sem arquivo) com base em atividades anormais ou não autorizadas. 
  
Os sensores do defender for IoT se conectam a uma porta de SPAN ou toque de rede e imediatamente começa a executar DPI no IoT e no tráfego de rede. 
 
A coleta de dados, o processamento, a análise e os alertas ocorrem diretamente no sensor. Esse processo o torna ideal para locais com baixa largura de banda ou conectividade de alta latência, pois apenas os metadados são transferidos para o console de gerenciamento.

O sensor inclui cinco mecanismos de detecção de análise. Os mecanismos acionam alertas com base na análise de tráfego em tempo real e gravados previamente. Os seguintes mecanismos estão disponíveis: 

#### <a name="protocol-violation-detection-engine"></a>Mecanismo de detecção de violação de protocolo
O mecanismo de detecção de violação de protocolo identifica o uso de estruturas de pacote e valores de campo que violam as especificações do protocolo ICS, por exemplo: exceção Modbus e inicialização de um alerta de código de função obsoleto.

#### <a name="policy-violation-detection-engine"></a>Mecanismo de detecção de violação de política
Usando o Machine Learning, o mecanismo de detecção de violação de política alerta os usuários de qualquer desvio do comportamento de linha de base, como o uso não autorizado de códigos de função específicos, o acesso a objetos específicos ou alterações na configuração do dispositivo. Por exemplo: DeltaV versão de software alterada e alertas de programação PLC não autorizados. Especificamente, o mecanismo de violação de política modela as redes ICS como sequências determinísticas de Estados e transições — usando uma técnica patenteada chamada modelagem de estado finito industrial (IFSM). O mecanismo de detecção de violação de política estabelece uma linha de base das redes ICS, para que a plataforma exija um período de aprendizado mais curto para criar uma linha de base da rede do que abordagens matemáticas genéricas ou análises, que foram originalmente desenvolvidas para ela em vez de se ter redes.

#### <a name="industrial-malware-detection-engine"></a>Mecanismo de detecção de malware industrial
O mecanismo de detecção de malware industrial identifica comportamentos que indicam a presença de malware conhecido, como Conficker, energia preta, Havex, WannaCry, NotPetya e Triton. 

#### <a name="anomaly-detection-engine"></a>Mecanismo de detecção de anomalias
O mecanismo de detecção de anomalias detecta comportamentos e comunicações M2M (máquina a máquina) incomuns. Ao modelar redes ICS como sequências determinísticas de Estados e transições, a plataforma requer um período de aprendizado mais curto do que abordagens matemáticas genéricas ou análises originalmente desenvolvidas para ela em vez de OT. Ele também detecta anomalias mais rapidamente, com um mínimo de falsos positivos. Os alertas do mecanismo de detecção de anomalias incluem tentativas de entrada de SMB excessivas e alertas de verificação de PLC detectados.

#### <a name="operational-incident-detection"></a>Detecção de incidente operacional
A detecção de incidentes operacionais detecta problemas operacionais, como conectividade intermitente que pode indicar sinais de falha de equipamentos antecipados. Por exemplo, o dispositivo é considerado como desconectado (sem resposta) e o comando Siemens S7 Stop PLC recebeu alertas.

### <a name="management-consoles"></a>Consoles de gerenciamento
O gerenciamento do Azure defender para IoT em ambientes híbridos é realizado por meio de dois portais de gerenciamento: 
- Console do sensor
- O console de gerenciamento local
- O portal do Azure

### <a name="sensor-console"></a>Console do sensor
As detecções de sensor são exibidas no console do sensor, onde podem ser exibidas, investigadas e analisadas em um mapa de rede, inventário de dispositivos e em uma ampla gama de relatórios, por exemplo, relatórios de avaliação de risco, Data Mining consultas e vetores de ataque. Você também pode usar o console do para exibir e lidar com ameaças detectadas por mecanismos de sensor, encaminhar informações para sistemas de parceiros, gerenciar usuários e muito mais.

:::image type="content" source="./media/architecture/sensor-console-v2.png" alt-text="Console do sensor do defender para IoT":::

### <a name="on-premises-management-console"></a>Console de gerenciamento local
O console de gerenciamento local permite que os operadores da SOC (central de operações de segurança) gerenciem e analisem alertas agregados de vários sensores em um único painel e fornece uma visão geral da integridade das redes de OT.

Essa arquitetura fornece uma exibição unificada abrangente da rede em um nível de SOC, tratamento otimizado de alertas e o controle da segurança de rede operacional, garantindo que a tomada de decisões e o gerenciamento de riscos permaneçam sem falhas.

Além de multilocação, monitoramento, análise de dados e controle remoto de sensor centralizado, o console de gerenciamento fornece ferramentas de manutenção de sistema extras (como exclusão de alertas) e recursos de relatório totalmente personalizados para cada um dos dispositivos remotos. Essa arquitetura dá suporte ao gerenciamento local em nível de site, nível de zona e gerenciamento global no SOC.

O console de gerenciamento pode ser implantado para configuração de alta disponibilidade, que fornece um console de backup que recebe periodicamente backups de todos os arquivos de configuração necessários para a recuperação. Se o console primário falhar, os dispositivos de gerenciamento de site local realizarão failover automaticamente para sincronizar com o console de backup a fim de manter a disponibilidade sem interrupção.

Totalmente integrado aos seus fluxos de trabalho do SOC e execução de livros, ele permite uma priorização fácil de atividades de mitigação e correlação entre sites de ameaças.

- Holística – reduza a complexidade com uma única plataforma unificada para gerenciamento de dispositivos, risco e gestão de vulnerabilidades e monitoramento de ameaças com resposta a incidentes.

- Agregação e correlação – exibem, agregam e analisam dados e alertas coletados de todos os sites.

- Controlar todos os sensores – configure e monitore todos os sensores de um único local.

   :::image type="content" source="media/updates/alerts-and-site-management-v2.png" alt-text="Gerencie todos os seus alertas e informações.":::

### <a name="azure-portal"></a>Portal do Azure

O portal do defender para IoT no Azure é usado para ajudá-lo:

- Comprar dispositivos de solução

- Instalar e atualizar software

- Sensores integrados ao Azure

- Atualizar pacotes de inteligência contra ameaças

## <a name="next-steps"></a>Próximas etapas

[FAQ do defender for IoT](resources-frequently-asked-questions.md)

[Pré-requisitos do sistema](quickstart-system-prerequisites.md)
