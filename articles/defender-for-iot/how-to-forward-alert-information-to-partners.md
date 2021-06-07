---
title: Encaminhar informações de alertas
description: Você pode enviar informações de alerta para sistemas de parceiros trabalhando com regras de encaminhamento.
ms.date: 12/02/2020
ms.topic: how-to
ms.openlocfilehash: bc405f7d4837bf81d9cfcd859d562b7152cfc54b
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778790"
---
# <a name="forward-alert-information"></a>Encaminhar informações de alertas

Você pode enviar informações de alerta aos parceiros que estão se integrando ao Azure defender para IoT, a servidores de syslog, a endereços de email e muito mais. Trabalhar com regras de encaminhamento permite que você forneça rapidamente informações de alerta para os participantes da segurança.  

Syslog e outras ações de encaminhamento padrão são entregues com o seu sistema. Mais ações de encaminhamento podem ser disponibilizadas quando você se integra a fornecedores de parceiros, como Microsoft Azure Sentinel, ServiceNow ou Splunk.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-information-screen.png" alt-text="Informações de alerta.":::

Os administradores do defender para IoT têm permissão para usar regras de encaminhamento.

## <a name="about-forwarded-alert-information"></a>Sobre informações de alerta encaminhado

Os alertas fornecem informações sobre uma ampla variedade de eventos operacionais e de segurança. Por exemplo:

  - Data e hora do alerta

  - Mecanismo que detectou o evento

  - Título do alerta e mensagem descritiva

  - Severidade do alerta

  - Nome de origem e de destino e endereço IP

  - Tráfego suspeito detectado

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="Exame de endereço detectado.":::

As informações relevantes são enviadas aos sistemas de parceiros quando as regras de encaminhamento são criadas.

## <a name="create-forwarding-rules"></a>Criar regras de encaminhamento

Para criar uma nova regra de encaminhamento:

1. Selecione **encaminhamento** no menu lateral.

   :: Image Type = "content" origem = "mídia/instruções de trabalho-com-alertas-sensor/create-forwarding-rule-screen.png" alt-text = "criar um ícone de regra de encaminhamento.":::

2. Selecione **criar regra de encaminhamento**.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/create-a-forwardong-rule.png" alt-text="Crie uma nova regra de encaminhamento.":::

3. Insira o nome da regra de encaminhamento.

### <a name="forwarding-rule-criteria"></a>Critérios de regra de encaminhamento 

Defina critérios pelos quais disparar uma regra de encaminhamento. Trabalhar com critérios de regra de encaminhamento ajuda a identificar e gerenciar o volume de informações enviadas do sensor para sistemas externos. As seguintes opções estão disponíveis:

**Protocolos**: somente disparará a regra de encaminhamento se o tráfego detectado estiver em execução em protocolos específicos. Selecione os protocolos necessários na lista suspensa ou escolha todos eles.

**Mecanismos**: selecione os mecanismos necessários ou escolha todos eles. Os alertas dos mecanismos selecionados serão enviados.

**Níveis de severidade**: esse é o incidente mínimo a ser encaminhado, em termos de nível de severidade. Por exemplo, se **você selecionar alertas secundários**, secundários e qualquer alerta acima desse nível de severidade será encaminhado. Os níveis são predefinidos.

### <a name="forwarding-rule-actions"></a>Ações de regras de encaminhamento

As ações de regra de encaminhamento instruem o sensor a encaminhar informações de alerta para fornecedores ou servidores parceiros. Você pode criar várias ações para cada regra de encaminhamento.

Além das ações de encaminhamento entregues com o sistema, outras ações podem ficar disponíveis quando você se integra aos fornecedores de parceiros. 

#### <a name="email-address-action"></a>Ação de endereço de email

Envie um email que inclua as informações de alerta. Você pode inserir um endereço de email por regra.

Para definir o email para a regra de encaminhamento:

1. Insira um único endereço de email. Se mais de um email precisar ser enviado, crie outra ação.

2. Insira o fuso horário para o carimbo de data/hora para a detecção de alerta no SIEM.

3. Selecione **Enviar**.

#### <a name="syslog-server-actions"></a>Ações do servidor syslog

Há suporte para os seguintes formatos:

- Mensagens de texto

- Mensagens CEF

- Mensagens LEEF

- Mensagens de objeto

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-actions-rule.png" alt-text="Criar ações de regra de encaminhamento.":::

Insira os parâmetros s seguir:

- Nome e porta do host syslog.

- Protocolo TCP e UDP.

- Fuso horário do carimbo de data/hora para a detecção de alerta no SIEM.

- Arquivo de certificado de criptografia TLS e arquivo de chave para servidores CEF (opcional).
    
:::image type="content" source="media/how-to-work-with-alerts-sensor/configure-encryption.png" alt-text="Configure sua criptografia para sua regra de encaminhamento.":::

| Campos de saída de mensagem de texto do syslog | Descrição |
|--|--|
| Data e Hora | Data e hora em que a máquina do servidor syslog recebeu as informações. |
| Prioridade | User. Alert |
| Nome do host | Endereço IP do sensor |
| Protocolo | TCP ou UDP |
| Mensagem | Sensor: o nome do sensor.<br /> Alerta: o título do alerta.<br /> Tipo: o tipo do alerta. Pode ser **violação de protocolo**, **violação de política**, **malware**, **anomalia** ou **operacional**.<br /> Severidade: a severidade do alerta. Pode ser **aviso**, **secundário**, **principal** ou **crítico**.<br /> Origem: o nome do dispositivo de origem.<br /> IP de origem: o endereço IP do dispositivo de origem.<br /> Destino: o nome do dispositivo de destino.<br /> IP de destino: o endereço IP do dispositivo de destino.<br /> Mensagem: a mensagem do alerta.<br /> Grupo de alertas: o grupo de alertas associado ao alerta. |


| Saída do objeto syslog | Descrição |
|--|--|
| Data e hora |   Data e hora em que a máquina do servidor syslog recebeu as informações. |  
| Prioridade |    User. Alert | 
| Nome do host |    IP do sensor | 
| Mensagem | Nome do sensor: o nome do dispositivo. <br /> Hora do alerta: a hora em que o alerta foi detectado: pode variar a partir da hora do computador do servidor syslog e depende da configuração de fuso horário da regra de encaminhamento. <br /> Título do alerta: o título do alerta. <br /> Mensagem de alerta: a mensagem do alerta. <br /> Severidade do alerta: a severidade do alerta: **aviso**, **secundário**, **principal** ou **crítico**. <br /> Tipo de alerta **: violação de protocolo**, violação de **política**, **malware**, **anomalia** ou **operacional**. <br /> Protocolo: o protocolo do alerta.  <br /> **Source_MAC**: endereço IP, nome, fornecedor ou sistema operacional do dispositivo de origem. <br /> Destination_MAC: endereço IP, nome, fornecedor ou sistema operacional do destino. Se os dados estiverem ausentes, o valor será **N/a**. <br /> alert_group: o grupo de alertas associado ao alerta. |


| Formato de saída CEF do syslog | Descrição |
|--|--|
| Data e Hora | Data e hora em que a máquina do servidor syslog recebeu as informações. |
| Prioridade | User. Alert | 
| Nome do host | Endereço IP do sensor |
| Mensagem | CEF: 0 <br />Azure Defender para IoT <br />Nome do sensor: o nome do dispositivo de sensor. <br />Versão do sensor <br />Título do alerta: o título do alerta. <br />msg: a mensagem do alerta. <br />Protocolo: o protocolo do alerta. <br />severidade: **aviso**, **secundário**, **principal** ou **crítico**. <br />tipo: **violação de protocolo**, **violação de política**, **malware**, **anomalia** ou **operacional**. <br /> Início: a hora em que o alerta foi detectado. <br />Pode variar a partir da hora do computador do servidor syslog e depende da configuração de fuso horário da regra de encaminhamento. <br />src_ip: endereço IP do dispositivo de origem.  <br />dst_ip: endereço IP do dispositivo de destino.<br />Cat: o grupo de alertas associado ao alerta.  |

| Formato de saída de LEEF de syslog | Descrição |
|--|--|
| Data e Hora |   Data e hora em que a máquina do servidor syslog recebeu as informações. |  
| Prioridade |    User. Alert | 
| Nome do host |    IP do sensor |
| Mensagem | Nome do sensor: o nome do dispositivo Azure defender para IoT. <br />LEEF: 1.0 <br />Azure Defender para IoT <br />Sensor  <br />Versão do sensor <br />Alerta do Azure defender para IoT <br />Título: o título do alerta. <br />msg: a mensagem do alerta. <br />Protocolo: o protocolo do alerta.<br />severidade: **aviso**, **secundário**, **principal** ou **crítico**. <br />tipo: o tipo do alerta: **violação de protocolo**, **violação de política**, **malware**, **anomalia** ou **operacional**. <br />Início: a hora do alerta.Observe que ele pode ser diferente do horário do computador do servidor syslog. (Isso depende da configuração de fuso horário.) <br />src_ip: endereço IP do dispositivo de origem.<br />dst_ip: endereço IP do dispositivo de destino. <br />Cat: o grupo de alertas associado ao alerta. |

Depois de inserir todas as informações, selecione **Enviar**.

#### <a name="netwitness-action"></a>Ação do nettestemunha

Enviar informações de alerta para um servidor nettestemunha.

Para definir os parâmetros de encaminhamento do nettestemunha:

1. Insira as informações de **porta** e **nome de host do** nettestemunha.

2. Insira o fuso horário para o carimbo de data/hora para a detecção de alerta no SIEM.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/add-timezone.png" alt-text="Adicione um fuso horário à sua regra de encaminhamento.":::

3. Selecione **Enviar**.

#### <a name="integrated-vendor-actions"></a>Ações de fornecedor integradas

Você pode ter integrado seu sistema com uma segurança, gerenciamento de dispositivos ou outro fornecedor do setor. Essas integrações permitem que você:

  - Enviar informações de alerta.

  - Enviar informações de inventário do dispositivo.

  - Comunique-se com firewalls do lado do fornecedor.

As integrações ajudam a ligar soluções de segurança em silos anteriormente, aprimorar a visibilidade do dispositivo e acelerar a resposta em todo o sistema para reduzir os riscos mais rapidamente.

Use a seção ações para inserir as credenciais e outras informações necessárias para se comunicar com fornecedores integrados.

Para obter detalhes sobre como configurar regras de encaminhamento para as integrações, consulte os artigos de integração de parceiro relevantes.

### <a name="test-forwarding-rules"></a>Regras de encaminhamento de teste

Teste a conexão entre o sensor e o servidor parceiro definido em suas regras de encaminhamento:

1. Selecione a regra na caixa de diálogo **regra de encaminhamento** .

2. Selecione a caixa **mais** .

3. Selecione **Enviar mensagem de teste**.

4. Acesse o seu sistema parceiro para verificar se as informações enviadas pelo sensor foram recebidas.

### <a name="edit-and-delete-forwarding-rules"></a>Editar e excluir regras de encaminhamento 

Para editar uma regra de encaminhamento:

- Na tela **regra de encaminhamento** , selecione **Editar** no menu suspenso **mais** . Faça as alterações desejadas e selecione **Enviar**.

Para remover uma regra de encaminhamento:

- Na tela **regra de encaminhamento** , selecione **remover** no menu suspenso **mais** . Na caixa de diálogo de **aviso** , selecione **OK**.

### <a name="forwarding-rules-and-alert-exclusion-rules"></a>Regras de encaminhamento e regras de exclusão de alerta

O administrador pode ter definido regras de exclusão de alertas. Essas regras ajudam os administradores a obter um controle mais granular sobre o disparo de alertas instruindo o sensor a ignorar eventos de alerta com base em vários parâmetros. Esses parâmetros podem incluir endereços de dispositivo, nomes de alerta ou sensores específicos.

Isso significa que as regras de encaminhamento que você definir poderão ser ignoradas com base nas regras de exclusão que o administrador criou. As regras de exclusão são definidas no console de gerenciamento local.

## <a name="see-also"></a>Veja também

[Acelerar fluxos de trabalho de alerta](how-to-accelerate-alert-incident-response.md)
