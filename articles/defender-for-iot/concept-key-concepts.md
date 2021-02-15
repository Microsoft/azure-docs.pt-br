---
title: Principais vantagens
description: Saiba mais sobre os conceitos básicos do defender para IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/13/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: cb984ca1a74d8b3838b857f2f5679264d1445187
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508601"
---
# <a name="basic-concepts"></a>Conceitos básicos 

Este artigo descreve as principais vantagens do Azure defender para IoT.

## <a name="rapid-non-invasive-deployment-and-passive-monitoring"></a>Implantação rápida não invasiva e monitoramento passivo

Os sensores do defender for IoT se conectam a portas de switch SPAN (espelho) e toques de rede e imediatamente começam a coletar tráfego de rede ICS via monitoramento passivo (sem agente). A inspeção de pacote profunda (DPI) é usada para dissecar o tráfego do equipamento de rede de controle serial e Ethernet. O defender para IoT tem impacto zero sobre as redes, pois não é colocado no caminho de dados e não examina ativamente os dispositivos. 

Para fornecer instantâneos instantâneos de informações detalhadas do dispositivo Windows, o defender for IoT sensor pode ser configurado para complementar o monitoramento passivo com um componente ativo opcional. Esse componente usa comandos seguros e aprovados pelo fornecedor para consultar os dispositivos do Windows em busca de detalhes do dispositivo, com frequência ou com a frequência desejada.

## <a name="embedded-knowledge-of-ics-protocols-devices-and-applications"></a>Conhecimento incorporado de protocolos, dispositivos e aplicativos do ICS

Somente DPI não é suficiente para identificar anomalias de protocolo e identificar o dispositivo em um nível granular. O sensor do defender for IoT trata de alguns dos ambientes maiores e mais complexos. Mais de 1.300 que redes foram analisadas até o momento, em todos os setores industriais.

## <a name="analytics-and-self-learning-engines"></a>Mecanismos de análise e autoaprendizagem

Os mecanismos identificam problemas de segurança por meio de monitoramento contínuo e cinco mecanismos de análise que incorporam autoaprendizado para eliminar a necessidade de atualizar assinaturas ou definir regras. Os mecanismos usam a análise comportamental específica do ICS e a ciência de dados para analisar continuamente o tráfego de rede para anomalias. Os cinco mecanismos são:

- **Detecção de violação de protocolo**: identifica o uso de estruturas de pacote e valores de campo que violam as especificações do protocolo ICS.

- **Detecção de violação de política**: identifica violações de política, como uso não autorizado de códigos de função, acesso a objetos específicos ou alterações na configuração do dispositivo.

- **Detecção de malware industrial**: identifica comportamentos que indicam a presença de malware conhecido, como Conficker, Black Energy, Havex, WannaCry e NotPetya.

- **Detecção de anomalias**: detecta comportamentos e comunicações M2M (máquina a máquina) incomuns. Ao modelar redes ICS como sequências determinísticas de Estados e transições, o mecanismo usa uma técnica patenteada chamada IFSM (modelagem de estado finito industrial). A solução requer um período de aprendizado mais curto do que as abordagens matemáticas genéricas ou a análise, que foram originalmente desenvolvidas para ela, em vez de OT. Ele também detecta anomalias mais rapidamente, com um mínimo de falsos positivos.

- **Detecção de incidente operacional**: identifica problemas operacionais, como conectividade intermitente que pode indicar sinais de falha de equipamentos antecipados.

## <a name="network-traffic-analysis-for-risk-and-vulnerability-assessment"></a>Análise de tráfego de rede para avaliação de risco e vulnerabilidade

Exclusivo no setor, o defender para IoT usa algoritmos de NTA (análise de tráfego de rede) para identificar passivamente todas as vulnerabilidades de rede e ponto de extremidade, como:

- Conexões de acesso remoto não autorizadas
- Dispositivos não autorizados ou não documentados
- Autenticação fraca
- Dispositivos vulneráveis (com base em CVEs sem patch)
- Pontes não autorizadas entre sub-redes
- Regras de firewall fracas

## <a name="data-mining-for-investigations-forensics-and-threat-hunting"></a>Mineração de dados para investigações, perícia e busca de ameaças

A plataforma fornece uma interface de mineração de dados intuitiva para a pesquisa granular de tráfego histórico em todas as dimensões relevantes. Os exemplos incluem período de tempo, endereço IP, endereço MAC e portas. Você também pode fazer consultas específicas de protocolo com base em códigos de função, serviços de protocolo e módulos. PCAPs de fidelidade total estão disponíveis para análise de busca detalhada adicional.

## <a name="sensor-cloud-management-mode"></a>Modo de gerenciamento de nuvem do sensor

O modo de gerenciamento de nuvem do sensor determina onde o dispositivo, o alerta e outras informações que o sensor detecta são exibidos.

Para **sensores conectados à nuvem**, as informações detectadas pelo sensor são exibidas no console do sensor. As informações de alerta são entregues por meio de um hub IoT e podem ser compartilhadas com outros serviços do Azure, como o Azure Sentinel.

Para **sensores conectados localmente**, as informações detectadas pelo sensor são exibidas no console do sensor. As informações de detecção também são compartilhadas com o console de gerenciamento local se o sensor está conectado a ele.

## <a name="air-gapped-networks"></a>Redes gapped

Se você estiver trabalhando em um ambiente de ar-gapped, o console de gerenciamento local no defender para IoT fornece uma exibição em tempo real dos indicadores de risco e alertas principais de IoT e de OT em todas as suas instalações. Totalmente integrado aos seus fluxos de trabalho e runbooks do SOC, ele permite uma priorização fácil de atividades de mitigação e correlação entre sites de ameaças.  

O defender para IoT fornece uma exibição consolidada de todos os seus dispositivos. Ele também fornece informações críticas sobre os dispositivos, como tipo (PLC, RTU, DCS e mais), fabricante, modelo e nível de revisão de firmware, bem como informações de alerta.  

O defender para IoT permite o gerenciamento eficaz de várias implantações e uma visão unificada abrangente da rede. O defender para IoT otimiza o tratamento de alertas e o controle da segurança de rede operacional.

O console de gerenciamento local é uma plataforma administrativa baseada na Web que permite monitorar e controlar as atividades de instalações de sensor global. Além de gerenciar os dados recebidos de sensores implantados, o console de gerenciamento local integra diretamente os dados de vários recursos comerciais: CMDBs, DNS, firewalls, APIs da Web e muito mais.

:::image type="content" source="media/concept-air-gapped-networks/site-management-alert-screen.png" alt-text="Exibição do console de gerenciamento local.":::

Recomendamos que você se familiarize com os conceitos, funcionalidades e recursos disponíveis para os sensores antes de trabalhar com o console de gerenciamento local.

## <a name="integrations"></a>Integrações

Você pode expandir os recursos do defender para IoT compartilhando informações de dispositivo e de alerta com sistemas de parceiros. As integrações ajudam as empresas a ligar soluções de segurança em silos anteriormente para melhorar significativamente a visibilidade do dispositivo e a inteligência contra ameaças. As integrações também ajudam as empresas a acelerar as respostas de todo o sistema e a reduzir os riscos com mais rapidez. 

As integrações reduzem a complexidade e as eliminam e têm silos integrando-as em seus fluxos de trabalho e pilha de segurança existentes do SOC. Por exemplo:

- SIEMs como IBM QRadar, Splunk, ArcSight, LogRhythm e RSA nettestemunha

- Sistemas de orquestração de segurança e de tíquetes, como ServiceNow e IBM resilientes

- Proteger soluções de acesso remoto, como CyberArk (Gerenciador de sessão privilegiada) e BeyondTrust

- Sistemas de controle de acesso à rede seguro (NAC), como Aruba ClearPass e Forescout.

- Firewalls como Fortinet e Check Point

## <a name="complete-protocol-support"></a>Suporte completo a protocolo

Além do suporte a protocolo incorporado, você pode proteger dispositivos IoT e ICS que executam protocolos proprietários e personalizados, ou protocolos que se desviam de qualquer padrão. Usando o SDK do ODE (ambiente de desenvolvimento aberto) Horizonte, os desenvolvedores podem criar plug-ins de dessetor que decodificam o tráfego de rede com base em protocolos definidos. Os serviços analisam o tráfego para fornecer monitoramento, alertas e relatórios completos. Use o horizonte para:

- Expanda visibilidade e controle sem a necessidade de atualizar para novas versões.

- Proteja as informações proprietárias desenvolvendo o site como um plug-in externo.

- Localize texto para alertas, eventos e parâmetros de protocolo.

Além disso, você pode usar alertas de protocolo patenteado para comunicar informações:

- Sobre detecções de tráfego com base em protocolos e protocolos subjacentes em um plug-in de horizonte proprietário.

- Sobre uma combinação de campos de protocolo de todas as camadas de protocolo. Por exemplo, em um ambiente que executa o MODBUS, talvez você queira gerar um alerta quando o sensor detectar um comando Write para um registro de memória em um endereço IP e destino Ethernet específicos. Ou talvez você queira gerar um alerta quando qualquer acesso for executado em um endereço IP específico.

Os alertas são disparados quando condições de regra de alerta de horizonte são atendidas.

Além disso, trabalhar com alertas personalizados de horizonte permite que você escreva seus próprios títulos e mensagens de alerta. Valores e campos de protocolo resolvidos podem ser inseridos no texto da mensagem de alerta.

Usar o gatilho de alerta e mensagens personalizadas, baseadas em condição, ajuda a identificar a atividade de rede específica e a atualizar efetivamente sua segurança, ti e equipes operacionais.


## <a name="high-availability"></a>Alta disponibilidade

Aumente a resiliência de sua implantação do defender para IoT instalando um dispositivo de alta disponibilidade no console de gerenciamento local. Implantações de alta disponibilidade garantem que os sensores gerenciados reportem continuamente para um console de gerenciamento local ativo.

Essa implantação é implementada com um par de console de gerenciamento local que inclui um dispositivo primário e um secundário.

## <a name="localization"></a>Localização

Muitos recursos de console dão suporte a uma ampla gama de linguagens.

## <a name="next-step"></a>Próxima etapa

[Introdução ao defender para IoT](getting-started.md)
