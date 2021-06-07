---
title: Tipos de alertas e descrições
description: Examine as descrições de alertas do Defender para IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 03/29/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 42a922dab2237925c2294245e112aab70b4245f0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727715"
---
# <a name="alert-types-and-descriptions"></a>Tipos de alertas e descrições

Este artigo descreve todos os tipos de alertas que podem ser gerados dos mecanismos do Defender para IoT. Os alertas são exibidos na janela Alertas, o que permite gerenciar o evento de alerta. 

## <a name="policy-engine-alerts"></a>Alertas do mecanismo de política

Os alertas do mecanismo de política descrevem os desvios do comportamento da rede de linha de base aprendido.

| Título  | Descrição | Severidade |
|--|--|--|
| Uso anormal de endereços MAC | Um novo dispositivo de origem foi detectado na rede, mas não foi autorizado. | Secundária |
| Software Beckhoff alterado | O firmware foi atualizado em um dispositivo de origem. Isso pode ser uma atividade autorizada, por exemplo, um procedimento de manutenção planejada. | Principal |
| Falha no logon do banco de dados | Foi detectada uma tentativa de logon com falha de um dispositivo de origem para um servidor de destino. Isso pode ser o resultado de um erro humano, mas também pode indicar uma tentativa mal-intencionada de comprometer o servidor ou os dados contidos nele. | Principal |
| Versão do firmware Emerson ROC alterada | O firmware foi atualizado em um dispositivo de origem. Isso pode ser uma atividade autorizada, por exemplo, um procedimento de manutenção planejada. | Principal |
| Comunicação do endereço externo na rede com a Internet | Um dispositivo de origem definido como parte da sua rede está se comunicando com os endereços na Internet. A origem não está autorizada a se comunicar com os endereços na Internet. | Crítico |
| Dispositivo de campo descoberto inesperadamente | Um novo dispositivo de origem foi detectado na rede, mas não foi autorizado. | Principal |
| Alteração de firmware detectada | O firmware foi atualizado em um dispositivo de origem. Isso pode ser uma atividade autorizada, por exemplo, um procedimento de manutenção planejada. | Principal |
| Versão do firmware alterada | O firmware foi atualizado em um dispositivo de origem. Isso pode ser uma atividade autorizada, por exemplo, um procedimento de manutenção planejada. | Principal |
| Operação não autorizada de Foxboro I/A | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Falha no logon do FTP | Foi detectada uma tentativa de logon com falha de um dispositivo de origem para um servidor de destino. Isso pode ser o resultado de um erro humano, mas também pode indicar uma tentativa mal-intencionada de comprometer o servidor ou os dados contidos nele. | Principal |
| O código de função gerou uma exceção não autorizada | Um dispositivo de origem (subordinado) retornou uma exceção para um dispositivo de destino (mestre). | Principal |
| Configurações de tipo de mensagem GOOSE | As configurações da mensagem (identificada pela ID de protocolo) foram alteradas em um dispositivo de origem. | Aviso |
| Versão do firmware Honeywell alterada | O firmware foi atualizado em um dispositivo de origem. Isso pode ser uma atividade autorizada, por exemplo, um procedimento de manutenção planejada. | Principal |
| Comunicação HTTP inválida | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Acesso à Internet detectado | Um dispositivo de origem definido como parte da sua rede está se comunicando com os endereços na Internet. A origem não está autorizada a se comunicar com os endereços na Internet. | Principal |
| Versão do firmware Mitsubishi alterada | O firmware foi atualizado em um dispositivo de origem. Isso pode ser uma atividade autorizada, por exemplo, um procedimento de manutenção planejada. | Principal |
| Violação de intervalo de endereços Modbus | Um dispositivo mestre solicitou acesso a um novo endereço de memória subordinada. | Principal |
| Versão do firmware Modbus alterada | O firmware foi atualizado em um dispositivo de origem. Isso pode ser uma atividade autorizada, por exemplo, um procedimento de manutenção planejada. | Principal |
| Nova atividade detectada – Classe CIP | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada – Serviço de classe CIP | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada – Comando CIP PCCC | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada – Símbolo CIP | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada – Conexão de E/S de Ethernet/IP | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada – Comando do protocolo Ethernet/IP | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada – Código de mensagem GSM | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada – Códigos de comando LonTalk | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Descoberta de novas portas | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Aviso |
| Nova atividade detectada – Variável de rede LonTalk | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada – Solicitação de dados de Ovation | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada – Comando de leitura/gravação (grupo de índice do AMS) | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada – Comando de leitura/gravação (deslocamento do índice do AMS) | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada – Tipo de mensagem DeltaV não autorizado | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada – Operação DeltaV ROC não autorizada | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada – Tipo de mensagem RPC não autorizado | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada – Invocação de procedimento RPC não autorizada | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada – Uso do comando do protocolo AMS | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada – Uso do comando do Siemens SICAM | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada – Uso do comando do protocolo Suitelink | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada – Uso de sessões do protocolo Suitelink | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Nova atividade detectada – Uso do comando Yokogawa VNetIP | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Novo ativo detectado | Um novo dispositivo de origem foi detectado na rede, mas não foi autorizado. | Principal |
| Nova configuração de dispositivo LLDP | Um novo dispositivo de origem foi detectado na rede, mas não foi autorizado. | Principal |
| Descoberta de novas portas | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Aviso |
| Comando não autorizado do Omron FINS | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Firmware S7 Plus PLC alterado | O firmware foi atualizado em um dispositivo de origem. Isso pode ser uma atividade autorizada, por exemplo, um procedimento de manutenção planejada. | Principal |
| Configurações de amostra de tipo de mensagem de valores | As configurações da mensagem (identificada pela ID de protocolo) foram alteradas em um dispositivo de origem. | Aviso |
| Suspeita de verificação de integridade inválida | Uma verificação foi detectada em um dispositivo de origem DNP3 (outstation). Essa verificação não foi autorizada como tráfego aprendido na sua rede. | Principal |
| Comando não autorizado de link de computador Toshiba | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Secundária |
| Operação de arquivo ABB Totalflow não autorizada | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Operação de registro Totalflow ABB não autorizada | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Acesso não autorizado ao bloco de dados do Siemens S7 | Um dispositivo de origem tentou acessar um recurso em outro dispositivo. Uma tentativa de acesso a esse recurso entre esses dois dispositivos não foi autorizada como tráfego aprendido na sua rede. | Aviso |
| Acesso não autorizado ao objeto do Siemens S7 Plus | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Acesso não autorizado à marca de Wonderware | Um dispositivo de origem tentou acessar um recurso em outro dispositivo. Uma tentativa de acesso a esse recurso entre esses dois dispositivos não foi autorizada como tráfego aprendido na sua rede. | Principal |
| Acesso a objeto BACNet não autorizado | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Rota do BACNet não autorizada | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Logon de banco de dados não autorizado | Foi detectada uma tentativa de logon entre um cliente de origem e um servidor de destino. A comunicação entre esses dispositivos não foi autorizada como tráfego aprendido na sua rede. | Principal |
| Operação de banco de dados não autorizada | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Operação de Emerson ROC não autorizada | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Acesso a arquivo do GE SRTP não autorizado | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Comando de protocolo GE SRTP não autorizado | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Operação de memória do sistema GE SRTP não autorizada | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Atividade HTTP não autorizada | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Servidor HTTP não autorizado | Um aplicativo não autorizado foi detectado em um dispositivo de origem. O aplicativo não foi autorizado como um aplicativo aprendido na sua rede. | Principal |
| Ação SOAP HTTP não autorizada | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Agente do usuário HTTP não autorizado | Um aplicativo não autorizado foi detectado em um dispositivo de origem. O aplicativo não foi autorizado como um aplicativo aprendido na sua rede. | Principal |
| Conectividade com a Internet não autorizada detectada | Um dispositivo de origem definido como parte da sua rede está se comunicando com os endereços na Internet. A origem não está autorizada a se comunicar com os endereços na Internet. | Crítico |
| Comando Mitsubishi MELSEC não autorizado | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Acesso de programa MMS não autorizado | Um dispositivo de origem tentou acessar um recurso em outro dispositivo. Uma tentativa de acesso a esse recurso entre esses dois dispositivos não foi autorizada como tráfego aprendido na sua rede. | Principal |
| Serviço MMS não autorizado | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Conexão de multicast/difusão não autorizada | Uma conexão multicast/difusão foi detectada entre um dispositivo de origem e outros dispositivos. A comunicação multicast/difusão não é autorizada. | Crítico |
| Consulta de nome não autorizada | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Atividade de agente do usuário OPC não autorizada | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Solicitação/resposta de agente do usuário OPC não autorizada | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Uma operação não autorizada foi detectada por uma regra definida pelo usuário | Foi detectado o tráfego entre dois dispositivos. Essa atividade não é autorizada com base em uma regra de alerta personalizada definida por um usuário. | Principal |
| Leitura de configuração de PLC não autorizada | O dispositivo de origem não está definido como um dispositivo de programação, mas realizou uma operação de leitura/gravação em um controlador de destino. As alterações de programação só devem ser executadas por dispositivos de programação. Um aplicativo de programação pode ter sido instalado neste dispositivo. | Aviso |
| Gravação de configuração de PLC não autorizada | O dispositivo de origem enviou um comando para ler/gravar o programa de um controlador de destino. Essa atividade não foi observada anteriormente. | Principal |
| Upload de programa PLC não autorizado | O dispositivo de origem enviou um comando para ler/gravar o programa de um controlador de destino. Essa atividade não foi observada anteriormente. | Principal |
| Programação de PLC não autorizada | O dispositivo de origem não está definido como um dispositivo de programação, mas realizou uma operação de leitura/gravação em um controlador de destino. As alterações de programação só devem ser executadas por dispositivos de programação. Um aplicativo de programação pode ter sido instalado neste dispositivo. | Crítico |
| Tipo de quadro do Profinet não autorizado | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Comando SAIA S-Bus não autorizado | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Execução não autorizada da função de controle do Siemens S7 | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Execução não autorizada da função definida pelo usuário do Siemens S7 | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Acesso de bloqueio não autorizado do Siemens S7 Plus | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Operação não autorizada do Siemens S7 Plus | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Logon do SMB não autorizado | Foi detectada uma tentativa de logon entre um cliente de origem e um servidor de destino. A comunicação entre esses dispositivos não foi autorizada como tráfego aprendido na sua rede. | Principal |
| Operação do SNMP não autorizada | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Acesso do SSH não autorizado | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Processo do Windows não autorizado | Um aplicativo não autorizado foi detectado em um dispositivo de origem. O aplicativo não foi autorizado como um aplicativo aprendido na sua rede. | Principal |
| Serviço Windows não autorizado | Um aplicativo não autorizado foi detectado em um dispositivo de origem. O aplicativo não foi autorizado como um aplicativo aprendido na sua rede. | Principal |
| Uma operação não autorizada foi detectada por uma regra definida pelo usuário | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros viola uma regra definida pelo usuário | Principal |
| Extensão do Modbus Schneider Electric não permitida | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Uso não permitido de tipos ASDU | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Uso não permitido de código de função DNP3 | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |
| Uso não permitido de IIN (indicação interna) | Um dispositivo de origem DNP3 (outstation) relatou uma IIN (indicação interna) que não foi autorizada como tráfego aprendido na sua rede. | Principal |
| Uso não permitido de código de função Modbus | Novos parâmetros de tráfego foram detectados. Essa combinação de parâmetros não foi autorizada como tráfego aprendido na sua rede. A combinação a seguir não é autorizada. | Principal |

## <a name="anomaly-engine-alerts"></a>Alertas do mecanismo de anomalias

| Título | Descrição | Severidade |
|--|--|--|
| Padrão de exceção anormal no subordinado | Um número excessivo de erros foi detectado em um dispositivo de origem. Isso pode ser o resultado de um problema operacional. | Secundária |
| Comprimento anormal de cabeçalho HTTP | O dispositivo de origem enviou uma mensagem anormal. Isso pode indicar uma tentativa de atacar o dispositivo de destino. | Crítico |
| Número anormal de parâmetros no cabeçalho HTTP | O dispositivo de origem enviou uma mensagem anormal. Isso pode indicar uma tentativa de atacar o dispositivo de destino. | Crítico |
| Comportamento periódico anormal no canal de comunicação | Foi detectada uma alteração na frequência de comunicação entre os dispositivos de origem e de destino. | Secundária |
| Término anormal de aplicativos | Um número excessivo de comandos de parada foi detectado em um dispositivo de origem. Isso pode ser resultado de um problema operacional ou uma tentativa de manipular o dispositivo. | Principal |
| Largura de banda de tráfego anormal | Largura de banda anormal detectada em um canal. A largura de banda parece ser significativamente menor/maior do que a detectada anteriormente. Para obter detalhes, trabalhe com o widget de Largura de Banda Total. | Aviso |
| Largura de banda de tráfego anormal entre dispositivos | Largura de banda anormal detectada em um canal. A largura de banda parece ser significativamente menor/maior do que a detectada anteriormente. Para obter detalhes, trabalhe com o widget de Largura de Banda Total. | Aviso |
| Verificação de endereço detectada | Um dispositivo de origem foi detectado verificando os dispositivos de rede. Este dispositivo não foi autorizado como um dispositivo de verificação de rede. | Crítico |
| Verificação de endereço ARP detectada | Um dispositivo de origem foi detectado verificando dispositivos de rede usando o protocolo ARP. O endereço desse dispositivo não foi autorizado como um endereço de verificação de ARP válido. | Crítico |
| Verificação de endereço ARP detectada | Um dispositivo de origem foi detectado verificando dispositivos de rede usando o protocolo ARP. O endereço desse dispositivo não foi autorizado como um endereço de verificação de ARP válido. | Crítico |
| Falsificação de ARP | Um volume anormal de pacotes foi detectado na rede. Isso pode indicar um ataque, por exemplo, uma falsificação de ARP ou um ataque de inundação por ICMP. | Aviso |
| Número de excesso de tentativas de logon | Um dispositivo de origem foi observado executando tentativas de logon excessivas em um servidor de destino. Isso pode ser um ataque de força bruta. O servidor pode estar comprometido por um ator mal-intencionado. | Crítico |
| Número excessivo de sessões | Um dispositivo de origem foi observado executando tentativas de logon excessivas em um servidor de destino. Isso pode ser um ataque de força bruta. O servidor pode estar comprometido por um ator mal-intencionado. | Crítico |
| Número excessivo da taxa de reinicialização de uma outstation | Um número excessivo de comandos de reinicialização foi detectado em um dispositivo de origem. Isso pode ser resultado de um problema operacional ou uma tentativa de manipular o dispositivo. | Principal |
| Número excessivo de tentativas de logon SMB | Um dispositivo de origem foi observado executando tentativas de logon excessivas em um servidor de destino. Isso pode ser um ataque de força bruta. O servidor pode estar comprometido por um ator mal-intencionado. | Crítico |
| Inundação por ICMP | Um volume anormal de pacotes foi detectado na rede. Isso pode indicar um ataque, por exemplo, uma falsificação de ARP ou um ataque de inundação por ICMP. | Aviso |
| Conteúdo de cabeçalho HTTP inválido | O dispositivo de origem iniciou uma solicitação inválida. | Crítico |
| Canal de comunicação inativo | Um canal de comunicação entre dois dispositivos estava inativo durante um período em que a atividade geralmente é observada. Isso pode indicar que o programa que gera esse tráfego foi alterado ou o programa pode estar não disponível. Recomendamos examinar a configuração do programa instalado e verificar se ele está configurado corretamente. | Aviso |
| Verificação de endereço de longa duração detectada | Um dispositivo de origem foi detectado verificando os dispositivos de rede. Este dispositivo não foi autorizado como um dispositivo de verificação de rede. | Crítico |
| Tentativa de detecção de senha detectada | Um dispositivo de origem foi observado executando tentativas de logon excessivas em um servidor de destino. Isso pode ser um ataque de força bruta. O servidor pode estar comprometido por um ator mal-intencionado. | Crítico |
| Verificação de PLC detectada | Um dispositivo de origem foi detectado verificando os dispositivos de rede. Este dispositivo não foi autorizado como um dispositivo de verificação de rede. | Crítico |
| Verificação de porta detectada | Um dispositivo de origem foi detectado verificando os dispositivos de rede. Este dispositivo não foi autorizado como um dispositivo de verificação de rede. | Crítico |
| Comprimento de mensagem inesperado | O dispositivo de origem enviou uma mensagem anormal. Isso pode indicar uma tentativa de atacar o dispositivo de destino. | Crítico |
| Tráfego inesperado para a porta padrão | Foi detectado o tráfego em um dispositivo usando uma porta reservada para outro protocolo. | Principal |

## <a name="protocol-violation-engine-alerts"></a>Alertas do mecanismo de violação de protocolo

| Título | Descrição | Severidade |
|--|--|--|
| Número excessivo de pacotes malformados em uma só sessão | Um número anormal de pacotes malformados enviados do dispositivo de origem para o dispositivo de destino. Isso pode indicar comunicações incorretas ou uma tentativa de manipular o dispositivo de destino. | Principal |
| Atualizações de firmware | Um dispositivo de origem enviou um comando para atualizar o firmware em um dispositivo de destino. Verifique se as atualizações recentes de programação, configuração e firmware feitas no dispositivo de destino são válidas. | Aviso |
| Código de função sem suporte na outstation | O dispositivo de destino recebeu uma solicitação inválida. | Principal |
| Mensagem do BACNet inválida | O dispositivo de origem iniciou uma solicitação inválida. | Principal |
| Tentativa de conexão inválida na porta 0 | Um dispositivo de origem tentou se conectar ao dispositivo de destino no número da porta 0 (zero). Para o TCP, a porta 0 é reservada e não pode ser usada. Para o UDP, a porta é opcional e um valor igual a 0 significa nenhuma porta. Normalmente, não há nenhum serviço em um sistema que escute a porta 0. Esse evento pode indicar uma tentativa de atacar o dispositivo de destino ou indicar que um aplicativo foi programado incorretamente. | Secundária |
| Operação do DNP3 inválida | O dispositivo de origem iniciou uma solicitação inválida. | Principal |
| Operação do Modbus inválida (exceção gerada pelo mestre) | O dispositivo de origem iniciou uma solicitação inválida. | Principal |
| Operação do Modbus inválida (código de função zero) | O dispositivo de origem iniciou uma solicitação inválida. | Principal |
| Versão de protocolo inválida | O dispositivo de origem iniciou uma solicitação inválida. | Principal |
| Parâmetro incorreto enviado para a outstation | O dispositivo de destino recebeu uma solicitação inválida. | Principal |
| Inicialização de um código de função obsoleto (inicializar dados) | O dispositivo de origem iniciou uma solicitação inválida. | Secundária |
| Inicialização de um código de função obsoleto (salvar configuração) | O dispositivo de origem iniciou uma solicitação inválida. | Secundária |
| O mestre solicitou uma confirmação de camada de aplicativo | O dispositivo de origem iniciou uma solicitação inválida. | Aviso |
| Exceção de Modbus | Um dispositivo de origem (subordinado) retornou uma exceção para um dispositivo de destino (mestre). | Principal |
| O dispositivo subordinado recebeu um tipo de ASDU inválido | O dispositivo de destino recebeu uma solicitação inválida. | Principal |
| O dispositivo subordinado recebeu uma causa de comando de transmissão inválida | O dispositivo de destino recebeu uma solicitação inválida. | Principal |
| O dispositivo subordinado recebeu um endereço comum inválido | O dispositivo de destino recebeu uma solicitação inválida. | Principal |
| O dispositivo subordinado recebeu um parâmetro de endereço de dados inválido | O dispositivo de destino recebeu uma solicitação inválida. | Principal |
| O dispositivo subordinado recebeu um parâmetro de valor de dados inválido | O dispositivo de destino recebeu uma solicitação inválida. | Principal |
| O dispositivo subordinado recebeu um código de função inválido | O dispositivo de destino recebeu uma solicitação inválida. | Principal |
| O dispositivo subordinado recebeu um endereço de objeto de informações inválido | O dispositivo de destino recebeu uma solicitação inválida. | Principal |
| Objeto desconhecido enviado para o outstation | O dispositivo de destino recebeu uma solicitação inválida. | Principal |
| Uso de um código de função reservado | O dispositivo de origem iniciou uma solicitação inválida. | Principal |
| Uso de formatação imprópria pela outstation | O dispositivo de origem iniciou uma solicitação inválida. | Aviso |
| Uso de sinalizadores de status reservados (IIN) | Um dispositivo de origem DNP3 (outstation) usou o indicador interno reservado 2.6. Recomendamos verificar a configuração do dispositivo. | Aviso |

## <a name="malware-engine-alerts"></a>Alertas do mecanismo de malware

| Título | Descrição| Severidade |
|--|--|--|
| Tentativa de conexão com IP mal-intencionado conhecido | Foi detectada uma atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por um malware conhecido. | Principal |
| Mensagem SMB inválida (implante de backdoor DoublePulsar) | Foi detectada uma atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por um malware conhecido. | Crítico |
| Solicitação de nome de domínio mal-intencionado | Foi detectada uma atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por um malware conhecido. | Principal |
| Arquivo de teste de malware detectado – êxito do EICAR AV | Um arquivo de teste do EICAR AV foi detectado no tráfego entre dois dispositivos. O arquivo não é um malware. Ele é usado para confirmar se o software antivírus está instalado corretamente, demonstra o que acontece quando um vírus é encontrado e verifica os procedimentos internos e as reações quando um vírus é encontrado. O software antivírus deve detectar o EICAR como se ele fosse um vírus real. | Principal |
| Suspeita de malware Conficker | Foi detectada uma atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por um malware conhecido. | Principal |
| Suspeita de ataque de negação de serviço | Um dispositivo de origem tentou iniciar um número excessivo de novas conexões com um dispositivo de destino. Isso pode ser um DoS (ataque de negação de serviço) contra o dispositivo de destino e pode interromper a funcionalidade dele, afetar o desempenho e a disponibilidade do serviço ou causar erros irrecuperáveis. | Crítico |
| Suspeita de atividade mal-intencionada | Foi detectada uma atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por um malware conhecido. | Principal |
| Suspeita de atividade mal-intencionada (BlackEnergy) | Foi detectada uma atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por um malware conhecido. | Crítico |
| Suspeita de atividade mal-intencionada (DarkComet) | Foi detectada uma atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por um malware conhecido. | Crítico |
| Suspeita de atividade mal-intencionada (Duqu) | Foi detectada uma atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por um malware conhecido. | Crítico |
| Suspeita de atividade mal-intencionada (Flame) | Foi detectada uma atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por um malware conhecido. | Crítico |
| Suspeita de atividade mal-intencionada (Havex) | Foi detectada uma atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por um malware conhecido. | Crítico |
| Suspeita de atividade mal-intencionada (Karagany) | Foi detectada uma atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por um malware conhecido. | Crítico |
| Suspeita de atividade mal-intencionada (LightsOut) | Foi detectada uma atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por um malware conhecido. | Crítico |
| Suspeita de atividade mal-intencionada (consultas de nome) | Foi detectada uma atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por um malware conhecido. | Principal |
| Suspeita de atividade mal-intencionada (Poison Ivy) | Foi detectada uma atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por um malware conhecido. | Crítico |
| Suspeita de atividade mal-intencionada (Regin) | Foi detectada uma atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por um malware conhecido. | Crítico |
| Suspeita de atividade mal-intencionada (Stuxnet) | Foi detectada uma atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por um malware conhecido. | Crítico |
| Suspeita de atividade mal-intencionada (WannaCry) | Foi detectada uma atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por um malware conhecido. | Principal |
| Suspeita de malware NotPetya – Parâmetros de SMB inválidos detectados | Foi detectada uma atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por um malware conhecido. | Crítico |
| Suspeita de malware NotPetya – Transação SMB inválida detectada | Foi detectada uma atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por um malware conhecido. | Crítico |
| Suspeita de execução remota de código com o PsExec | Foi detectada uma atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por um malware conhecido. | Principal |
| Suspeita de gerenciamento de serviços remotos do Windows | Foi detectada uma atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por um malware conhecido. | Principal |
| Arquivo executável suspeito detectado no ponto de extremidade | Foi detectada uma atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por um malware conhecido. | Principal |
| Tráfego suspeito detectado | Foi detectada uma atividade de rede suspeita. Essa atividade pode ser associada a um ataque que explora um método usado por um malware conhecido. | Crítico |

## <a name="operational-engine-alerts"></a>Alertas do mecanismo operacional

| Título | Descrição | Severidade |
|--|--|--|
| Um comando S7 Stop PLC foi enviado | O dispositivo de origem enviou um comando de parada para um controlador de destino. O controlador deixará de operar até que um comando de inicialização seja enviado. | Aviso |
| Falha na operação do BACNet | Um servidor retornou um código de erro. Isso indica um erro de servidor ou uma solicitação inválida por um cliente. | Principal |
| Estado do dispositivo MMS inválido | Um VMD (Dispositivo Virtual de Manufatura) MMS enviou uma mensagem de status. A mensagem indica que o servidor pode não estar configurado corretamente, pode estar parcialmente operacional ou não operacional. | Principal |
| Alteração da configuração do dispositivo | Uma alteração de configuração foi detectada em um dispositivo de origem. | Secundária |
| Estouro de buffer de eventos contínuo na outstation | Um evento de estouro de buffer foi detectado em um dispositivo de origem. O evento pode causar dados corrompidos, falhas do programa ou execução de código mal-intencionado. | Principal |
| Redefinição do controlador | Um dispositivo de origem enviou um comando de redefinição para um controlador de destino. O controlador parou de operar temporariamente e foi iniciado de modo automático. | Aviso |
| Parada do controlador | O dispositivo de origem enviou um comando de parada para um controlador de destino. O controlador deixará de operar até que um comando de inicialização seja enviado. | Aviso |
| O dispositivo não pôde receber um endereço IP dinâmico | O dispositivo de origem está configurado para receber um endereço IP dinâmico de um servidor DHCP, mas não recebeu um endereço. Isso indica um erro de configuração no dispositivo ou um erro operacional no servidor DHCP. Recomendamos notificar o administrador de rede do incidente | Principal |
| Suspeita de desconexão do dispositivo (sem resposta) | Um dispositivo de origem não respondeu a um comando enviado a ele. Ele pode ter sido desconectado quando o comando foi enviado. | Principal |
| Falha na solicitação de serviço CIP de Ethernet/IP | Um servidor retornou um código de erro. Isso indica um erro de servidor ou uma solicitação inválida por um cliente. | Principal |
| Falha no comando de protocolo de encapsulamento Ethernet/IP | Um servidor retornou um código de erro. Isso indica um erro de servidor ou uma solicitação inválida por um cliente. | Principal |
| Estouro de buffer de eventos na outstation | Um evento de estouro de buffer foi detectado em um dispositivo de origem. O evento pode causar dados corrompidos, falhas do programa ou execução de código mal-intencionado. | Principal |
| A operação de backup esperada não ocorreu | A atividade esperada de transferência de arquivo/backup não ocorreu entre dois dispositivos. Isso pode indicar erros no processo de transferência de arquivo/backup. | Principal |
| Falha do comando GE SRTP | Um servidor retornou um código de erro. Isso indica um erro de servidor ou uma solicitação inválida por um cliente. | Principal |
| O comando GE SRTP Stop PLC foi enviado | O dispositivo de origem enviou um comando de parada para um controlador de destino. O controlador deixará de operar até que um comando de inicialização seja enviado. | Aviso |
| O bloco de controle GOOSE exige configuração adicional | Um dispositivo de origem enviou uma mensagem GOOSE indicando que o dispositivo precisa de comissões. Isso significa que o bloco de controle GOOSE exige configuração adicional e que as mensagens GOOSE estão parcial ou completamente não operacionais. | Principal |
| A configuração do conjunto de dados GOOSE foi alterada | Um conjunto de dados da mensagem (identificada pela ID de protocolo) foi alterado em um dispositivo de origem. Isso significa que o dispositivo relatará um conjunto de dados diferente para essa mensagem. | Aviso |
| Status inesperado do controlador Honeywell | Um controlador Honeywell enviou uma mensagem de diagnóstico inesperada indicando uma alteração de status. | Aviso |
| Erro do cliente HTTP | O dispositivo de origem iniciou uma solicitação inválida. | Aviso |
| Endereço IP inválido | O sistema detectou o tráfego entre um dispositivo de origem e um endereço IP que é um endereço inválido. Isso pode indicar uma configuração incorreta ou uma tentativa de gerar um tráfego ilegal. | Secundária |
| Erro de autenticação de mestre/subordinado | Falha no processo de autenticação entre um dispositivo de origem DNP3 (mestre) e um dispositivo de destino (outstation). | Secundária |
| Falha na solicitação de serviço MMS | Um servidor retornou um código de erro. Isso indica um erro de servidor ou uma solicitação inválida por um cliente. | Principal |
| Nenhum tráfego detectado na interface do sensor | Um sensor parou de detectar o tráfego de rede em um adaptador de rede. | Crítico |
| O servidor OPC UA gerou um evento que exige a atenção do usuário | Um servidor OPC UA enviou uma notificação de eventos para um cliente. Esse tipo de evento exige a atenção do usuário | Principal |
| Falha na solicitação de serviço OPC UA | Um servidor retornou um código de erro. Isso indica um erro de servidor ou uma solicitação inválida por um cliente. | Principal |
| Outstation reiniciada | Foi detectada uma reinicialização a frio em um dispositivo de origem. Isso significa que o dispositivo foi fisicamente desligado e reconectado. | Aviso |
| Reinicializações frequentes da outstation | Um número excessivo de reinicializações a frio foi detectado em um dispositivo de origem. Isso significa que o dispositivo foi fisicamente desligado e reconectado um número excessivo de vezes. | Secundária |
| Configuração alterada da outstation | Uma alteração de configuração foi detectada em um dispositivo de origem. | Principal |
| Configuração corrompida da outstation detectada | Este dispositivo de origem DNP3 (outstation) relatou uma configuração corrompida. | Principal |
| Falha do comando de DCP do Profinet | Um servidor retornou um código de erro. Isso indica um erro de servidor ou uma solicitação inválida por um cliente. | Principal |
| Redefinição de fábrica de dispositivo do Profinet | Um dispositivo de origem enviou um comando de redefinição de fábrica para um dispositivo de destino Profinet. O comando de redefinição limpa as configurações de dispositivo Profinet e interrompe a operação dele. | Aviso |
| Falha na operação do RPC | Um servidor retornou um código de erro. Isso indica um erro de servidor ou uma solicitação inválida por um cliente. | Principal |
| Alteração nos valores de amostra da configuração do conjunto de dados da mensagem | Um conjunto de dados da mensagem (identificada pela ID de protocolo) foi alterado em um dispositivo de origem. Isso significa que o dispositivo relatará um conjunto de dados diferente para essa mensagem. | Aviso |
| Falha irrecuperável do dispositivo subordinado | Um erro de condição irrecuperável foi detectado em um dispositivo de origem. Esse tipo de erro geralmente indica uma falha de hardware ou uma falha ao executar um comando específico. | Principal |
| Suspeita de problemas de hardware na outstation | Um erro de condição irrecuperável foi detectado em um dispositivo de origem. Esse tipo de erro geralmente indica uma falha de hardware ou uma falha ao executar um comando específico. | Principal |
| Suspeita de dispositivo Modbus sem resposta | Um dispositivo de origem não respondeu a um comando enviado a ele. Ele pode ter sido desconectado quando o comando foi enviado. | Secundária |
| Tráfego detectado na interface do sensor | Um sensor retomou a detecção do tráfego de rede em um adaptador de rede. | Aviso |

## <a name="next-steps"></a>Próximas etapas

Você pode [Gerenciar eventos de alerta](how-to-manage-the-alert-event.md).
Saiba como [Encaminhar informações de alerta](how-to-forward-alert-information-to-partners.md).
