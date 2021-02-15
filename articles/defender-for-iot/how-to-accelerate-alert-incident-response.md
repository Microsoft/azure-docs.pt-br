---
title: Acelerar fluxos de trabalho de alerta
description: Melhorar fluxos de trabalho de incidentes e alertas.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: 0a445abe0f448c28742282aedd1a886fae0f5a43
ms.sourcegitcommit: e3151d9b352d4b69c4438c12b3b55413b4565e2f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100526282"
---
# <a name="accelerate-alert-workflows"></a>Acelerar fluxos de trabalho de alerta

Este artigo descreve como acelerar fluxos de trabalho de alerta usando comentários de alerta, grupos de alertas e regras de alerta personalizadas no Azure defender para IoT.  Essas ferramentas ajudam você a:

- Analise e gerencie o grande volume de eventos de alerta detectados em sua rede.

- Identifique e manipule uma atividade de rede específica.

## <a name="accelerate-incident-workflows-by-using-alert-comments"></a>Acelerar fluxos de trabalho de incidentes usando comentários de alerta

Trabalhe com comentários de alerta para melhorar a comunicação entre indivíduos e equipes durante a investigação de um evento de alerta.

:::image type="content" source="media/how-to-work-with-alerts-sensor/suspicion-of malicious-activity-screen.png" alt-text="Captura de tela que mostra atividades mal-intencionadas.":::

Use comentários de alerta para melhorar:

- **Etapas do fluxo de trabalho**: forneça etapas de mitigação de alerta.

- **Acompanhamento do fluxo de trabalho**: notifique que as etapas foram executadas.

- **Diretrizes de fluxo de trabalho**: forneça recomendações, ideias ou avisos sobre o evento.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-comment-screen.png" alt-text="Captura de tela que mostra comentários de alerta.":::

A lista de opções disponíveis aparece em cada alerta. Os usuários podem selecionar uma ou várias mensagens.

Para adicionar comentários de alerta:

1. No menu lateral, selecione **configurações do sistema**.

2. Na janela **configuração do sistema** , selecione **comentários de alerta**.

3. Na caixa **adicionar comentários** , insira o texto do comentário. Use até 50 caracteres. Vírgulas não são permitidas.

4. Selecione **Adicionar**.

## <a name="accelerate-incident-workflows-by-using-alert-groups"></a>Acelerar fluxos de trabalho de incidentes usando grupos de alertas

Os grupos de alertas permitem que as equipes da SOC exibam e filtrem alertas em suas soluções SIEM e gerenciem esses alertas com base nas políticas de segurança corporativa e nas prioridades de negócios. Por exemplo, alertas sobre novas detecções são organizados em um grupo de descoberta. Esse grupo inclui alertas que lidam com a detecção de novos dispositivos, novas VLANs, novas contas de usuário, novos endereços MAC e muito mais.

Os grupos de alertas são aplicados quando você cria regras de encaminhamento para as seguintes soluções de parceiros:

  - Servidores syslog

  - QRadar

  - ArcSight

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-forwarding-rule.png" alt-text="Captura de tela da criação de uma regra de encaminhamento.":::

O grupo de alertas relevante aparece nas soluções de saída do parceiro. 

### <a name="requirements"></a>Requisitos

O grupo de alertas aparecerá em soluções de parceiros com suporte com os seguintes prefixos:

- **Cat** para QRadar, ArcSight, syslog CEF, syslog LEEF

- **Grupo de alertas** para mensagens de texto do syslog

- **alert_group** para objetos syslog

Esses campos devem ser configurados na solução de parceiro para exibir o nome do grupo de alertas. Se não houver nenhum alerta associado a um grupo de alertas, o campo na solução de parceiro será exibido **na**.

### <a name="default-alert-groups"></a>Grupos de alertas padrão

Os seguintes grupos de alertas são definidos automaticamente:
|  |  |  |
|--|--|--|
| Comportamento de comunicação anormal | Alertas personalizados | Acesso remoto |
| Comportamento de comunicação HTTP anormal | Descoberta | Comandos de reiniciar e parar |
| Autenticação | Alteração de firmware | Verificação |
| Comportamento de comunicação não autorizado | Comandos ilegais | Tráfego do sensor |
| Anomalias de largura de banda | Acesso à Internet | Suspeita de malware |
| Estouro de buffer | Falhas de operação | Suspeita de atividade mal-intencionada |
| Falhas de comando | Problemas operacionais |  |
| Alterações de configuração | Programação |  |

Os grupos de alertas são predefinidos. Para obter detalhes sobre os alertas associados a grupos de alertas e sobre como criar grupos de alertas personalizados, entre em contato com [suporte da Microsoft](https://support.microsoft.com/supportforbusiness/productselection?sapId=82c8f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="customize-alert-rules"></a>Personalizar regras de alerta

Use regras de alerta personalizadas para identificar mais especificamente a atividade de seu interesse. 

Você pode adicionar regras de alerta personalizadas com base em:

- Uma categoria, por exemplo, um protocolo, uma porta ou um arquivo.
- Endereços de origem e de destino
- Uma condição baseada na categoria escolhida, por exemplo, uma função associada a um protocolo, um nome de arquivo, uma porta ou um número de transporte.
- Uma condição com base na referência de data e hora, por exemplo, se uma detecção foi feita em um dia específico ou em uma determinada parte do dia.

Se o sensor detectar a atividade descrita na regra, o alerta será enviado.
informações detectadas pelos sensores individuais. Por exemplo, defina uma regra que instrua um sensor a disparar um alerta com base em um IP de origem, IP de destino ou comando (dentro de um protocolo). Quando o sensor detecta o tráfego definido na regra, um alerta ou evento é gerado.

Você também pode usar ações de regra de alerta para instruir o defender para IoT a:

- Permitir que os usuários acessem o arquivo PCAP do alerta.
- Atribua uma severidade de alerta.
- Gerar um evento em vez de alertar. As informações detectadas serão exibidas na linha do tempo do evento.

:::image type="content" source="media/how-to-work-with-alerts-sensor/user-defined-rule.png" alt-text="Captura de tela que mostra uma regra definida pelo usuário.":::

A mensagem de alerta indica que uma regra definida pelo usuário disparou o alerta.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="Captura de tela que mostra alertas personalizados.":::

Para criar uma regra de alerta personalizada:

1. Selecione **alertas personalizados** no menu lateral de um sensor.
1. Selecione o sinal de adição ( **+** ) para criar uma regra.
1. Defina um nome de regra.
1. Selecione uma categoria ou um protocolo no painel **categorias** .
1. Defina um IP e um endereço MAC de origem e de destino específicos ou escolha qualquer endereço.
1. Defina uma ou várias condições de regra. Duas categorias de condições podem ser criadas:
    - Condições baseadas em valores exclusivos associados à categoria selecionada. Selecione Adicionar e defina os valores.
    - Condições baseadas no quando a atividade foi detectada. Na seção detecções, selecione um período de tempo e um dia em que a detecção deve ocorrer para enviar o alerta. Você pode optar por enviar o alerta se a atividade for detectada a qualquer momento, durante ou após o horário de trabalho. Use a opção definir horas de trabalho para instruir o defender para o horário de trabalho da IoT para sua organização.
1. Definir ações de regra: 
    - Indique se a regra dispara um **alarme** ou **evento**.
    - Atribua um nível de severidade ao alerta.
    - Indique se o alerta incluirá um arquivo PCAP.
1. Selecione **Salvar**.

A regra é adicionada à lista **regras de alertas personalizados** , em que você pode examinar os parâmetros básicos da regra, a última vez que a regra foi disparada e muito mais. Você também pode habilitar e desabilitar a regra na lista.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="Captura de tela de uma regra personalizada adicionada pelo usuário.":::

### <a name="see-also"></a>Consulte também

[Exibir informações fornecidas em alertas](how-to-view-information-provided-in-alerts.md)

[Gerenciar o evento de alertas](how-to-manage-the-alert-event.md)
