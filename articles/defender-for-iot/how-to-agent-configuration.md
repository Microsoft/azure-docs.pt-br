---
title: Configurar agentes de segurança
description: Saiba como configurar o defender para agentes de segurança de IoT para uso com o serviço de segurança do defender para IoT.
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: 5ac9a5cdb4cc13ed91c0e5e29447b9cdbe71bcce
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784400"
---
# <a name="tutorial-configure-security-agents"></a>Tutorial: configurar agentes de segurança

Este artigo explica os agentes de segurança do defender para IoT e detalha como alterá-los e configurá-los.

> [!div class="checklist"]
> * Configurar agentes de segurança
> * Alterar o comportamento do agente editando propriedades de entrelaçamento
> * Descobrir configuração padrão

## <a name="agents"></a>Agentes

Os agentes de segurança do defender para IoT coletam dados de dispositivos IoT e executam ações de segurança para atenuar as vulnerabilidades detectadas. A configuração do agente de segurança é controlável com o uso de um conjunto de propriedades de módulo de propriedade que você pode personalizar. Em geral, as atualizações secundárias dessas propriedades não são frequentes.

O objeto de configuração do agente de segurança do defender para IoT é um objeto de formato JSON. O objeto de configuração é um conjunto de propriedades controláveis que você pode definir para controlar o comportamento do agente.

Essas configurações ajudam a personalizar o agente para cada cenário necessário. Por exemplo, excluir automaticamente alguns eventos ou manter o consumo de energia em um nível mínimo é possível Configurando essas propriedades.

Use o [esquema](https://aka.ms/iot-security-github-module-schema) de configuração do agente de segurança do defender para IOT para fazer alterações.

## <a name="configuration-objects"></a>Objetos de configuração

As propriedades relacionadas a cada agente de segurança do defender para IoT estão localizadas no objeto de configuração do agente, na seção propriedades desejadas, do módulo **azureiotsecurity** .

Para modificar a configuração, crie e modifique esse objeto dentro da identidade de **azureiotsecurity** do módulo.

Se o objeto de configuração do agente não existir no módulo **azureiotsecurity** , todos os valores de Propriedade do agente de segurança serão definidos como padrão.

```json
"desired": {
  "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
  }
}
```

## <a name="configuration-schema-and-validation"></a>Esquema de configuração e validação

Certifique-se de validar a configuração do agente nesse [esquema](https://aka.ms/iot-security-github-module-schema). Um agente não será iniciado se o objeto de configuração não corresponder ao esquema.

Se, enquanto o agente está em execução, o objeto de configuração é alterado para uma configuração não válida (a configuração não corresponde ao esquema), o agente irá ignorar a configuração inválida e continuará usando a configuração atual.

### <a name="configuration-validation"></a>Validação de configuração

O agente de segurança do defender para IoT relata sua configuração atual na seção de propriedades relatadas da identidade de **azureiotsecurity** do módulo.
O agente relata todas as propriedades disponíveis, se uma propriedade não tiver sido definida pelo usuário, o agente relatará a configuração padrão.

Para validar sua configuração, compare os valores definidos na seção desejada com os valores relatados na seção relatada.

Se houver uma incompatibilidade entre as propriedades desejadas e as relatadas, o agente não conseguirá analisar a configuração.

Valide as propriedades desejadas no [esquema](https://aka.ms/iot-security-github-module-schema), corrija os erros e defina as propriedades desejadas novamente!

> [!NOTE]
> Um alerta de erro de configuração será acionado do agente caso o agente não possa analisar a configuração desejada.
> Compare a seção relatada e desejada para entender se o alerta ainda se aplica

## <a name="editing-a-property"></a>Editando uma propriedade

Todas as propriedades personalizadas devem ser definidas dentro do objeto de configuração do agente no módulo **azureiotsecurity** .
Para usar um valor de propriedade padrão, remova a propriedade do objeto de configuração.

### <a name="setting-a-property"></a>Definindo uma propriedade

1. No Hub IoT, localize e selecione o dispositivo que você deseja alterar.

1. Clique em seu dispositivo e, em seguida, no módulo **azureiotsecurity** .

1. Clique em **identidade do módulo**...

1. Edite as propriedades que você deseja alterar no defender-IoT-micro-Agent.

   Por exemplo, para configurar eventos de conexão como prioridade alta e coletar eventos de alta prioridade a cada 7 minutos, use a configuração a seguir.

    ```json
    "desired": {
        "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
            "highPriorityMessageFrequency": {
                "value": "PT7M"
            },
            "eventPriorityConnectionCreate": {
                "value": "High"
            }
        }
    }
    ```

1. Clique em **Save** (Salvar).

### <a name="using-a-default-value"></a>Usando um valor padrão

Para usar um valor de propriedade padrão, remova a propriedade do objeto de configuração.

## <a name="default-properties"></a>Propriedades padrão

A tabela a seguir contém as propriedades controláveis do defender para agentes de segurança de IoT.

Os valores padrão estão disponíveis no esquema adequado no [GitHub](https\://aka.ms/iot-security-module-default).

| Nome| Status | Valores válidos| Valores padrão| Descrição |
|----------|--------|--|-------|----|
|highPriorityMessageFrequency|Obrigatório: falso |Valores válidos: duração no formato ISO 8601 |Valor padrão: PT7M |Intervalo de tempo máximo antes que as mensagens de alta prioridade sejam enviadas.|
|lowPriorityMessageFrequency |Obrigatório: falso|Valores válidos: duração no formato ISO 8601 |Valor padrão: PT5H |Tempo máximo antes que as mensagens de baixa prioridade sejam enviadas.|
|snapshotFrequency |Exigir: falso|Valores válidos: duração no formato ISO 8601 |Valor padrão PT13H |Intervalo de tempo para a criação de instantâneos de status do dispositivo.|
|maxLocalCacheSizeInBytes |Obrigatório: falso |Valores válidos:  |Valor padrão: 2560000, maior que 8192 | Armazenamento máximo (em bytes) permitido para o cache de mensagens de um agente. Quantidade máxima de espaço permitido para armazenar mensagens no dispositivo, antes que as mensagens sejam enviadas.|
|maxMessageSizeInBytes |Obrigatório: falso |Valores válidos: um número positivo, maior que 8192, menor que 262144 |Valor padrão: 204800 |Tamanho máximo permitido de um agente para uma mensagem de nuvem. Essa configuração controla a quantidade de dados máximos enviados em cada mensagem. |
|eventPriority $ {EventName} |Obrigatório: falso |Valores válidos: alto, baixo, desativado |Valores padrão: |Prioridade de cada evento gerado por agente |

### <a name="supported-security-events"></a>Eventos de segurança com suporte

|Nome do evento| PropertyName | Valor padrão| Evento de instantâneo| Status de detalhes  |
|----------|-|---------|----|----|
|Evento de diagnóstico|eventPriorityDiagnostic| Desativado| Falso| Eventos de diagnóstico relacionados ao agente. Use esse evento para log detalhado.|
|Erro de configuração |eventPriorityConfigurationError |Baixo |Falso |O agente não pôde analisar a configuração. Verifique a configuração em relação ao esquema.|
|Estatísticas de eventos descartados |eventPriorityDroppedEventsStatistics |Baixo |Verdadeiro|Estatísticas de evento relacionadas ao agente. |
|Hardware conectado|eventPriorityConnectedHardware |Baixo |Verdadeiro |Instantâneo de todo o hardware conectado ao dispositivo.|
|Portas de escuta|eventPriorityListeningPorts |Alto |Verdadeiro |Instantâneo de todas as portas de escuta abertas no dispositivo.|
|Criar processo |eventPriorityProcessCreate |Baixo |Falso |Audita a criação do processo no dispositivo.|
|Término do processo|eventPriorityProcessTerminate |Baixo |Falso |Audita o encerramento do processo no dispositivo.|
|Informações do sistema |eventPrioritySystemInformation |Baixo |Verdadeiro |Um instantâneo das informações do sistema (por exemplo: sistema operacional ou CPU).|
|Usuários locais| eventPriorityLocalUsers |Alto |Verdadeiro|Um instantâneo dos usuários locais registrados no sistema. |
|Logon|  eventPriorityLogin |Alto|Falso|Auditar os eventos de logon para o dispositivo (logons locais e remotos).|
|Criação de conexão |eventPriorityConnectionCreate|Baixo|Falso|Audita as conexões TCP criadas de e para o dispositivo. |
|Configuração do firewall| eventPriorityFirewallConfiguration|Baixo|Verdadeiro|Instantâneo da configuração de firewall do dispositivo (regras de firewall). |
|Linha de base do so| eventPriorityOSBaseline| Baixo|Verdadeiro|Instantâneo da verificação de linha de base do so do dispositivo.|
|

## <a name="next-steps"></a>Próximas etapas

- [Entender o defender para obter recomendações de IoT](concept-recommendations.md)
- [Explorar o defender para alertas de IoT](concept-security-alerts.md)
- [Acessar dados de segurança brutos](how-to-security-data-access.md)
