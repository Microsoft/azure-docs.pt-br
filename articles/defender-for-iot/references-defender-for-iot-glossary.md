---
title: Defender para glossário do IoT
description: Este glossário fornece uma breve descrição dos termos e conceitos importantes do defender for IoT Platform.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/09/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 13eb84e91e1809012a2ae1ad01397b20cb322f16
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104443"
---
# <a name="defender-for-iot-glossary"></a>Defender para glossário do IoT

Este glossário fornece uma breve descrição dos termos e conceitos importantes para a plataforma Azure defender para IoT. Selecione o link **saiba mais** links para ir para os termos relacionados no Glossário. Isso ajudará você a aprender e usar com mais rapidez as ferramentas de produto.

> [!Note]
> Qualquer termo com um `(AL)` listado em nome é um termo do construtor de dispositivos baseado em agente. 

<a name="glossary-a"></a>

## <a name="a"></a>Um

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Grupo de acesso** | Dê suporte aos requisitos de acesso do usuário para grandes organizações criando regras de grupo de acesso.<br /><br />As regras permitem que você controle o acesso de exibição e configuração ao console de gerenciamento local do defender para IoT para funções de usuário específicas em unidades de negócios, regiões, sites e zonas relevantes.<br /><br />Por exemplo, permita que analistas de segurança de um grupo de Active Directory acessem dados automotivados da Europa Ocidental, mas que impeçam o acesso a dados na África. | **[Console de gerenciamento local](#o)** <br /><br />**[Unidade de negócios](#b)** |
| **Tokens de acesso** | Gere tokens de acesso para acessar a API REST do defender para IoT. | **[API](#glossary-a)** |
| **Confirmar evento de alerta** | Instrua o defender para IoT a ocultar o alerta uma vez para o evento detectado. O alerta será disparado novamente se o evento for detectado novamente. | **[Alerta do](#glossary-a) <br /> <br /> [Aprender evento](#l) <br /> <br /> de alerta [Evento de alerta de mudo](#m)** |
| **Alerta** | Uma mensagem que um mecanismo do defender for IoT dispara sobre desvios do comportamento de rede autorizado, anomalias de rede ou atividade de rede e tráfego suspeitos. | **[](#f) <br /> Regra <br /> de encaminhamento [](#e) <br /> Regra <br /> de exclusão [Notificações do sistema](#s)** |
| **Comentário do alerta** | Comentários que os analistas de segurança e os administradores fazem em mensagens de alerta. Por exemplo, um comentário de alerta pode fornecer instruções sobre ações de mitigação a serem tomadas ou nomes de indivíduos a serem contatados em relação ao evento.<br /><br />Os usuários que estão revisando alertas podem escolher comentários que melhor refletem o status do evento ou etapas tomadas para investigar o alerta. | **[Alerta](#glossary-a)** |
| **Mecanismo de anomalias** | Um defender para o mecanismo de IoT que detecta comunicação e comportamento incomuns de M2M (máquina a máquina). Por exemplo, o mecanismo pode detectar tentativas de entrada excessivas do SMB. Os alertas de anomalias são disparados quando esses eventos são detectados. | **[Defender para mecanismos de IoT](#d)** |
| **API** | Permite que os sistemas externos acessem dados descobertos pelo defender para IoT e executem ações usando a API REST externa sobre conexões SSL. | **[Tokens de acesso](#glossary-a)** |
| **Relatório de vetor de ataque** | Uma representação gráfica em tempo real de cadeias de vulnerabilidade de pontos de extremidade exploráveis.<br /><br />Os relatórios permitem avaliar o efeito das atividades de mitigação na sequência de ataque para determinar. Por exemplo, você pode avaliar se uma atualização do sistema interrompe o caminho do invasor, dividindo a cadeia de ataques ou se um caminho de ataque alternativo permanece. Isso prioriza as atividades de remediação e mitigação. | **[Relatório de avaliação de risco](#r)** |

## <a name="b"></a>B

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Unidade de negócios** | Uma organização lógica de sua empresa de acordo com setores específicos.<br /><br />Por exemplo, uma empresa global que contém fábricas de vidro e fábricas de plástico pode ser gerenciada como duas unidades de negócios diferentes. Você pode controlar o acesso do defender para usuários de IoT a unidades de negócios específicas. | **[](#o) <br /> Console <br /> de gerenciamento local [](#glossary-a) <br /> Grupo <br /> de acesso [Site do](#s) <br /> <br /> [Zona](#z) do** |
| **Linha de base** | Tráfego de rede, protocolos, comandos e dispositivos aprovados. O defender para IoT identifica os desvios da linha de base da rede. Exiba o tráfego de linha de base aprovado gerando relatórios de mineração de dados. | **[](#d) <br /> Mineração <br /> de dados [Modo de aprendizado](#l)** |

## <a name="c"></a>C

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Comandos de CLI** | Opções de CLI (interface de linha de comando) para usuários administradores do defender para IoT. Os comandos da CLI estão disponíveis para recursos que não podem ser acessados por meio dos consoles do defender para IoT. | - |


## <a name="d"></a>D

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Mineração de dados** | Gere relatórios abrangentes e granulares sobre seus dispositivos de rede:<br /><br />- **Resposta a incidentes SOC**: relatórios em tempo real para ajudar a lidar com resposta imediata a incidentes. Por exemplo, um relatório pode listar os dispositivos que podem precisar de aplicação de patch.<br /><br />- **Análise forense**: relatórios baseados em dados históricos para relatórios investigativos.<br /><br />- **Integridade da rede de ti**: relatórios que ajudam a melhorar a segurança geral da rede. Por exemplo, um relatório pode listar dispositivos com credenciais de autenticação fracas.<br /><br />- **visibilidade**: relatórios que abrangem todos os itens de consulta para exibir todos os parâmetros de linha de base da sua rede.<br /><br />Salvar relatórios de mineração de dados para usuários somente leitura para exibir. | **[Linha de base](#b) <br /> <br /> [Relatórios](#r) do** |
| **Defender para mecanismos de IoT** | Os mecanismos de análise de autoaprendizado no defender para IoT eliminam a necessidade de atualizar assinaturas ou definir regras. Os mecanismos usam a análise comportamental específica do ICS e a ciência de dados para analisar continuamente o tráfego de rede para anomalias, malware, problemas operacionais, violações de protocolo e desvios da atividade de rede de linha de base.<br /><br />Quando um mecanismo detecta um desvio, um alerta é disparado. Os alertas podem ser exibidos e gerenciados na tela de **alertas** ou em um Siem. | **[Alerta](#glossary-a)** |
| **Defender para plataforma IoT** | A solução defender para IoT instalada no defender para sensores IoT e no console de gerenciamento local. | **[](#s) <br /> Sensor <br /> [Console de gerenciamento local](#o)** |
| **Mapa do dispositivo** | Uma representação gráfica dos dispositivos de rede que o defender for IoT detecta. Ele mostra as conexões entre os dispositivos e as informações sobre cada dispositivo. Use o mapa para:<br /><br />-Recuperar e controlar informações críticas do dispositivo.<br /><br />-Analisar fatias de rede.<br /><br />-Exportar detalhes e resumos do dispositivo. | **[Grupo de camadas Purdue](#p)** |
| **Inventário de dispositivo-sensor** | O inventário de dispositivo exibe uma ampla gama de atributos de dispositivo detectados pelo defender para IoT. As opções estão disponíveis para:<br /><br />-Filtrar informações exibidas.<br /><br />-Exporte essas informações para um arquivo CSV.<br /><br />-Importar detalhes do registro do Windows. | **[Group](#g)** <br /><br />**[Inventário de dispositivo – console de gerenciamento local](#d)** |
| **Inventário de dispositivo – console de gerenciamento local** | As informações do dispositivo de sensores conectados podem ser exibidas no console de gerenciamento local no inventário de dispositivos. Isso permite que os usuários do console de gerenciamento local tenham uma visão abrangente de todas as informações de rede. | **[Inventário de dispositivo-sensor](#d) <br /> <br /> [Inventário de dispositivos-integrador de dados](#d)** |
| **Inventário de dispositivos-integrador de dados** | Os recursos de integração de dados do console de gerenciamento local permitem aprimorar os dados no inventário de dispositivos com informações de outros recursos da empresa. Os recursos de exemplo são CMDBs, DNS, firewalls e APIs da Web. | **[Inventário de dispositivo – console de gerenciamento local](#d)** |
| **Dispositivo gêmeos**`(AL)` | Dispositivos gêmeos são documentos JSON que armazenam informações de estado do dispositivo, incluindo metadados, configurações e condições. | [Módulo...](#m) <br /> <br />[Módulo de segurança/a](#s) |

## <a name="e"></a>E

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Exibição empresarial** | Um mapa global que apresenta unidades de negócios, sites e zonas em que os defensores de sensores de IoT estão instalados. Exiba locais geográficos de alertas mal-intencionados, alertas operacionais e muito mais. | **[](#b) <br /> Unidade <br /> de negócios [Site do](#s) <br /> <br /> [Zona](#z) do** |
| **Linha do tempo do evento** | Uma linha do tempo de atividade detectada em sua rede, incluindo:<br /><br />-Alertas disparados.<br /><br />-Eventos de rede (informativo).<br /><br />-Operações de usuário, como entrada, exclusão de usuários e criação de usuários e operações de gerenciamento de alertas, como ativar mudo, aprender e confirmar. Disponível nos consoles do sensor. | - |
| **Regra de exclusão** | Instrua o defender para IoT a ignorar gatilhos de alerta com base no período de tempo, endereço do dispositivo e nome do alerta ou por um sensor específico.<br /><br />Por exemplo, se você souber que todos os dispositivos de OT monitorados por um sensor específico passarão por um procedimento de manutenção entre 6:30 e 10:15 na manhã, você poderá definir uma regra de exclusão que declara que esse sensor não deve enviar alertas no período predefinido. | **[Alerta do](#glossary-a) <br /> <br /> [Evento de alerta de mudo](#m)** |

## <a name="f"></a>F

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Regra de encaminhamento** | As regras de encaminhamento instruem o defender para IoT a enviar informações de alerta para fornecedores ou sistemas parceiros.<br /><br />Por exemplo, envie informações de alerta para um servidor Splunk ou um servidor syslog. | **[Alerta](#glossary-a)** |

## <a name="g"></a>G

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Grupo** | Grupos predefinidos ou personalizados de dispositivos que contêm atributos específicos, como dispositivos que executaram atividade de programação ou dispositivos localizados em uma sub-rede específica. Use grupos para ajudá-lo a exibir dispositivos e analisar dispositivos no defender para IoT.<br /><br />Os grupos podem ser exibidos e criados no mapa do dispositivo e no inventário do dispositivo. | **[](#d) <br /> Mapa <br /> do dispositivo [Inventário de dispositivo](#d)** |

## <a name="h"></a>H

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Ambiente de desenvolvimento aberto em horizonte** | Dispositivos IoT e ICS seguros que executam protocolos proprietários e personalizados ou protocolos que se desviam de qualquer padrão. Use o SDK do ODE (ambiente de desenvolvimento aberto) do horizonte para desenvolver plug-ins de dessetor que decodifiquem o tráfego de rede com base em protocolos definidos. Os defensores dos serviços de IoT analisam o tráfego para fornecer monitoramento, alertas e relatórios completos.<br /><br />Use o horizonte para:<br /><br />- **Expanda** visibilidade e controle sem a necessidade de atualizar o defender para versões da plataforma IOT.<br /><br />- **Proteja** as informações proprietárias desenvolvendo o site como um plug-in externo.<br /><br />- **Localize** texto para alertas, eventos e parâmetros de protocolo.<br /><br />Contate o representante de sucesso do cliente para obter detalhes. | **[](#p) <br /> Suporte <br /> de protocolo [Localização](#l)** |
| **Alerta personalizado de horizonte** | Aprimore o gerenciamento de alertas em sua empresa disparando alertas personalizados para qualquer protocolo (com base em dessetors de tráfego de estrutura de horizonte).<br /><br />Esses alertas podem ser usados para comunicar informações:<br /><br />-Sobre as detecções de tráfego com base em protocolos e protocolos subjacentes em um plug-in de horizonte proprietário.<br /><br />-Sobre uma combinação de campos de protocolo de todas as camadas de protocolo. | **[Suporte de protocolo](#p)** |

## <a name="i"></a>I

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Hub IoT** `(AL)` | Serviço gerenciado, hospedado na nuvem, que atua como um hub central de mensagens para comunicação bidirecional entre o aplicativo IoT e os dispositivos que ele gerencia.  |   |
| **Integrações** | Expanda o defender para recursos de IoT compartilhando informações de dispositivo com sistemas de parceiros. As organizações podem fazer a ponte de soluções de segurança, NAC, gerenciamento de incidentes e gerenciamento de dispositivos anteriormente encontradas para acelerar as respostas em todo o sistema e reduzir mais rapidamente os riscos. | **[Regra de encaminhamento](#f)** |
| **Sub-rede interna** | Configurações de sub-rede definidas pelo defender para IoT. Em alguns casos, como ambientes que usam intervalos públicos como intervalos internos, você pode instruir o defender para IoT a resolver todas as sub-redes como sub-redes internas. As sub-redes são exibidas no mapa e em vários relatórios do defender for IoT. | **[Sub-redes](#s)** |

## <a name="l"></a>L

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Aprender evento de alerta** | Instrua o defender para IoT a autorizar o tráfego detectado em um evento de alerta. | **[Alerta do](#glossary-a) <br /> <br /> [Confirmar evento](#glossary-a) <br /> <br /> de alerta [Evento de alerta de mudo](#m)** |
| **Modo de aprendizado** | O modo usado quando o defender para IoT aprende sua atividade de rede. Essa atividade torna-se a sua linha de base de rede. O defender para IoT permanece no modo por um período predefinido após a instalação. A atividade que se desvia da atividade aprendida após esse período irá disparar o defender para alertas de IoT. | **[](#s) <br /> Aprendizado <br /> inteligente de ti [Linha de base](#b)** |
| **Localização** | Localize texto para alertas, eventos e parâmetros de protocolo para plug-ins de dessetor desenvolvidos pelo horizonte. | **[Ambiente de desenvolvimento aberto em horizonte](#h)** |

## <a name="m"></a>M

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Micro Agent**`(AL)` | Fornece recursos de segurança de profundidade para dispositivos IoT, incluindo a postura de segurança e a detecção de ameaças.  |    |
| **Módulo Gêmeo** `(AL)` | Módulo gêmeo refere-se a são documentos JSON que armazenam informações do estado do módulo, incluindo metadados, configurações e condições. | [Dispositivo gêmeo](#d) <br /> <br />[Módulo de segurança/a](#s) |
| **Evento de alerta de mudo** | Instrua o defender para IoT a ignorar continuamente a atividade com dispositivos idênticos e o tráfego comparável. | **[Alerta do](#glossary-a) <br /> <br /> [](#e) <br /> Regra <br /> de exclusão [Confirmar evento](#glossary-a) <br /> <br /> de alerta [Aprender evento de alerta](#l)** |

## <a name="n"></a>N

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Notificações** | Informações sobre alterações de rede ou propriedades de dispositivo não resolvidas. As opções estão disponíveis para atualizar informações de dispositivo e rede com novos dados detectados. Responder às notificações aprimora o inventário do dispositivo, o mapa e vários relatórios. Disponível em consoles do sensor. | **[Alerta do](#glossary-a) <br /> <br /> [Notificações do sistema](#s)** |

## <a name="o"></a>O

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Console de gerenciamento local** | O console de gerenciamento local fornece uma exibição centralizada e o gerenciamento de dispositivos e ameaças que os defensores das implantações do sensor IoT detectam em sua organização. | **[Defender para plataforma IOT](#d) <br /> <br /> [Sensor](#s)** |
| **Alerta operacional** | Alertas que lidam com problemas de rede operacional, como um dispositivo que é suspeito de ser desconectado da rede. | **[Alerta do](#glossary-a) <br /> <br /> [Alerta de segurança](#s)** |

## <a name="p"></a>P

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Camada Purdue** | Mostra as interconexões e as interdependências de componentes principais de um ICS típico no mapa. |  |
| **Suporte de protocolo** | Além do suporte a protocolo incorporado, você pode proteger dispositivos IoT e ICS que executam protocolos proprietários e personalizados, ou protocolos que se desviam de qualquer padrão, usando o SDK do ambiente de desenvolvimento aberto do horizonte. | **[Ambiente de desenvolvimento aberto em horizonte](#h)** |

## <a name="r"></a>R

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Região** | Uma divisão lógica de uma organização global em regiões geográficas. Os exemplos são América do Norte, Europa Ocidental e Europa Oriental.<br /><br />América do Norte pode ter fábricas de várias unidades de negócios. | **[](#glossary-a) <br /> Grupo <br /> de acesso [](#b) <br /> Unidade <br /> de negócios [](#o) <br /> Console <br /> de gerenciamento local [Site do](#s) <br /> <br /> [Zona](#z) do** |
| **Relatórios** | Os relatórios refletem informações geradas pelos resultados da consulta de mineração de dados. Isso inclui os resultados padrão de mineração de dados, que estão disponíveis no modo de exibição **relatórios** . Administradores e analistas de segurança também podem gerar consultas de mineração de dados personalizadas e salvá-las como relatórios. Esses relatórios também estarão disponíveis para usuários somente leitura. | **[Mineração de dados](#d)** |
| **Relatório de avaliação de risco** | Os relatórios de avaliação de risco permitem gerar uma pontuação de segurança para cada dispositivo de rede, juntamente com uma pontuação geral de segurança de rede. A pontuação geral representa a porcentagem de segurança de 100 por cento. O relatório fornece recomendações de mitigação que ajudarão você a melhorar sua pontuação de segurança atual. | - |

## <a name="s"></a>S

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Alerta de segurança** | Alertas que lidam com problemas de segurança, como tentativas excessivas de entrada de SMB ou detecções de malware. | **[Alerta do](#glossary-a) <br /> <br /> [Alerta operacional](#o)** |
| **Módulo de segurança** /a `(AL)` | O módulo de segurança de tudo contém todas as informações relevantes para a segurança do dispositivo, para cada dispositivo específico em sua solução. | [Dispositivo gêmeo](#d) <br /> <br />[Módulo...](#m)  |
| **Investigação seletiva** | O defender para IoT o inspeciona passivamente e se torna o tráfego e detecta informações relevantes sobre dispositivos, seus atributos, seus comportamentos e muito mais. Em determinados casos, algumas informações podem não estar visíveis em análises de rede passivas.<br /><br />Quando isso acontece, você pode usar as ferramentas de investigação seguras e granulares no defender for IoT para descobrir informações importantes sobre dispositivos inacessíveis anteriormente. | - |
| **Sensor** | A máquina virtual ou física na qual a plataforma defender para IoT está instalada. | **[Console de gerenciamento local](#o)** |
| **Site** | Um local que uma fábrica ou outra entidade. O site deve conter uma zona ou várias zonas nas quais um sensor está instalado. | **[Zona](#z)** |
| **Gerenciamento de site** | A opção do console de gerenciamento local que permite que você gerencie sensores empresariais. | - |
| **Aprendizado inteligente de ti** | Depois que o período de aprendizado for concluído e o modo de aprendizado estiver desabilitado, o defender para IoT poderá detectar um nível incomum de alterações de linha de base que são o resultado da atividade normal de ti, como solicitações de DNS e HTTP. Esse tráfego pode disparar alertas de violação de política e notificações do sistema desnecessários. Para reduzir esses alertas e notificações, você pode habilitar o aprendizado inteligente de ti. | **[](#l) <br /> Modo <br /> de aprendizado [Linha de base](#b)** |
| **Sub-redes** | Para habilitar o foco nos dispositivos OT, os dispositivos de ti são agregados automaticamente por sub-rede no mapa do dispositivo. Cada sub-rede é apresentada como uma única entidade no mapa, incluindo um recurso interativo de recolhimento ou expansão para se concentrar em uma sub-rede de ti e vice-versa. | **[Mapa do dispositivo](#d)** |
| **Notificações de sistema** | Notificações do console de gerenciamento local de comprometimento:<br /><br />-Status de conexão do sensor.<br /><br />-Falhas de backup remoto. | **[Notificações do](#n) <br /> <br /> [Alerta](#glossary-a) do** |

## <a name="z"></a>Z

| Termo | Descrição | Saiba mais |
|--|--|--|
| **Zona** | Uma área dentro de um site em que um sensor ou sensores estão instalados. | **[Site do](#s) <br /> <br /> [](#b) <br /> Unidade <br /> de negócios [Região](#r) do** |
