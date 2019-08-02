---
title: Configurar a Central de segurança do Azure para IoT agente versão prévia | Microsoft Docs
description: Saiba como configurar agentes para uso com a Central de segurança do Azure para IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 39539bb14877208e5f6af957e735a136b077f16a
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618274"
---
# <a name="tutorial-configure-security-agents"></a>Tutorial: Configurar agentes de segurança

> [!IMPORTANT]
> A Central de Segurança do Azure para IoT está em versão prévia pública no momento.
> Esta versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica a Central de segurança do Azure (ASC) para o agente de segurança de IoT, como alterá-los configurar ASC para agentes de segurança de IoT.

> [!div class="checklist"]
> * Configurar agentes de segurança
> * Alterar o comportamento do agente, editando as propriedades do gêmeo
> * Descobrir a configuração padrão

## <a name="agents"></a>Agentes

ASC para agentes de segurança de IoT coletam dados de dispositivos IoT e realizar ações de segurança para atenuar as vulnerabilidades detectadas. Configuração do agente de segurança é controlável usando um conjunto de propriedades de gêmeo de módulo, que você pode personalizar. Em geral, as atualizações secundárias para essas propriedades são pouco frequentes.  

ASC para objeto de configuração gêmeo do agente de segurança do IoT é um objeto no formato. JSON. O objeto de configuração é um conjunto de propriedades controláveis que você pode definir para controlar o comportamento do agente. 

Essas configurações ajudam a personalizar o agente para cada cenário exigido. Por exemplo, automaticamente excluindo alguns eventos ou manter o consumo de energia para um nível mínimo são possíveis por meio da configuração dessas propriedades.  

Use o ASC para configuração do agente de segurança de IoT [esquema](https://aka.ms/iot-security-github-module-schema) para fazer alterações.  

## <a name="configuration-objects"></a>Objetos de configuração 

Cada ASC para agente de segurança de IoT relacionados a propriedade está localizada no objeto de configuração de agente, dentro da seção de propriedades desejadas, do **azureiotsecurity** módulo. 

Para modificar a configuração, criar e modificar esse objeto dentro de **azureiotsecurity** identidade do módulo gêmeo. 

Se o objeto de configuração do agente não existe na **azureiotsecurity** módulo gêmeo, todos os valores de propriedade de agente de segurança são definidos como padrão. 

```json
"desired": {
  "azureiot*com^securityAgentConfiguration^1*0*0": {
  } 
}
```

Certifique-se de validar as alterações de configuração do agente em relação a este [esquema](https://aka.ms/iot-security-github-module-schema).
O agente não será iniciado se o objeto de configuração não coincide com o esquema.

## <a name="configuration-schema-and-validation"></a>Esquema de configuração e validação 

Certifique-se de validar a configuração do agente em relação a este [esquema](https://aka.ms/iot-security-github-module-schema). Um agente não serão iniciados se o objeto de configuração não coincide com o esquema.

 
Se, enquanto o agente é executado, o objeto de configuração é alterado para uma configuração inválida (a configuração não coincide com o esquema), o agente irá ignorar a configuração inválida e continuarão a usar a configuração atual. 

## <a name="editing-a-property"></a>Editar uma propriedade 

Todas as propriedades personalizadas devem ser definidas dentro do objeto de configuração do agente dentro de **azureiotsecurity** gêmeo do módulo.
Para usar um valor de propriedade padrão, remova a propriedade do objeto de configuração.

### <a name="setting-a-property"></a>Definir uma propriedade

1. No seu IoT Hub, localize e selecione o dispositivo que você deseja alterar.

1. Clique em seu dispositivo e, em seguida, na **azureiotsecurity** módulo.

1. Clique em **identidade de módulo gêmeo**.

1. Edite as propriedades desejadas do módulo de segurança.
   
   Por exemplo, para configurar eventos de conexão como prioridade alta e coletar eventos de alta prioridade a cada sete minutos, use a seguinte configuração.
   
   ```json
    "desired": {
      "azureiot*com^securityAgentConfiguration^1*0*0": {
        "highPriorityMessageFrequency": "PT7M",    
        "eventPriorityConnectionCreate": "High" 
      } 
    }, 
    ```

1. Clique em **Salvar**.

### <a name="using-a-default-value"></a>Usando um valor padrão

Para usar um valor de propriedade padrão, remova a propriedade do objeto de configuração.

## <a name="default-properties"></a>Propriedades padrão 

A tabela a seguir contém as propriedades controláveis do ASC para agentes de segurança de IoT.

Valores padrão estão disponíveis no esquema adequado no [Github](https://aka.ms/iot-security-module-default).

| NOME| Status | Valores válidos| Valores padrão| DESCRIÇÃO |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Obrigatório: false |Valores válidos: Duração em formato ISO 8601 |Valor padrão: PT7M |Tempo máximo antes das mensagens de alta prioridade são enviados.|
|lowPriorityMessageFrequency |Obrigatório: false|Valores válidos: Duração em formato ISO 8601 |Valor padrão: PT5H |Tempo máximo antes das mensagens de baixa prioridade são enviados.| 
|snapshotFrequency |Exigir: false|Valores: duração válido no formato ISO 8601 |Valor padrão PT13H |Intervalo de tempo para a criação de instantâneos de status do dispositivo.| 
|maxLocalCacheSizeInBytes |Obrigatório: false |Valores válidos: |Valor padrão: 2560000, maior que 8192 | Armazenamento máximo (em bytes) permitido para o cache de mensagens de um agente. Quantidade máxima de espaço permitido para armazenar mensagens do dispositivo antes que as mensagens são enviadas.| 
|maxMessageSizeInBytes |Obrigatório: false |Valores válidos: Um número positivo maior que 8192, menos 262144 |Valor padrão: 204800 |Máximo tamanho permitido de um agente de mensagem de nuvem. Essa configuração controla a quantidade máxima de dados enviada em cada mensagem. |
|eventPriority${EventName} |Obrigatório: false |Valores válidos: Alta, baixa, desativado |Valores padrão: |Prioridade de cada agente gerou o evento | 

### <a name="supported-security-events"></a>Eventos de segurança com suporte

|Nome do evento| PropertyName | Default Value| Evento de instantâneo| Detalhes de Status  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Evento de diagnóstico|eventPriorityDiagnostic| Desativar| False| Eventos de diagnóstico relacionadas ao agente. Use esse evento para o log detalhado.| 
|Erro de configuração |eventPriorityConfigurationError |Baixo |False |Falha ao analisar a configuração do agente. Verifique a configuração em relação ao esquema.| 
|Eventos ignorados estatísticas |eventPriorityDroppedEventsStatistics |Baixo |verdadeiro|Estatísticas de eventos relacionados ao agente. |
|Estatísticas de mensagem|eventPriorityMessageStatistics |Baixo |verdadeiro |Estatísticas de mensagens relacionadas ao agente. |
|Hardware conectado|eventPriorityConnectedHardware |Baixo |verdadeiro |Instantâneo de todos os hardwares conectados ao dispositivo.|
|Portas de escuta|eventPriorityListeningPorts |Alto |verdadeiro |Instantâneo de todas as portas de escuta no dispositivo.|
|Criar processo |eventPriorityProcessCreate |Baixo |False |Auditorias de processam de criação do dispositivo.|
|Encerrar processo|eventPriorityProcessTerminate |Baixo |False |Auditorias de processam o encerramento no dispositivo.| 
|Informações do sistema |eventPrioritySystemInformation |Baixo |verdadeiro |Um instantâneo de informações do sistema (por exemplo: Sistema operacional ou CPU).| 
|Usuários locais| eventPriorityLocalUsers |Alto |verdadeiro|Um instantâneo dos usuários registrados locais dentro do sistema. |
|Logon|  eventPriorityLogin |Alto|False|Auditoria de eventos de logon para o dispositivo (logons locais e remotos).|
|Criar Conexão |eventPriorityConnectionCreate|Baixo|False|Audita as conexões TCP criadas para e do dispositivo. |
|Configuração do firewall| eventPriorityFirewallConfiguration|Baixo|verdadeiro|Instantâneo da configuração de firewall de dispositivo (regras de firewall). |
|Linha de base do sistema operacional| eventPriorityOSBaseline| Baixo|verdadeiro|Verifique se o instantâneo de linha de base do dispositivo do sistema operacional.|
 

## <a name="next-steps"></a>Próximas etapas

- [Entender o ASC para recomendações de IoT](concept-recommendations.md)
- [Explore o ASC para alertas de IoT](concept-security-alerts.md)
- [Acessar os dados brutos de segurança](how-to-security-data-access.md)
