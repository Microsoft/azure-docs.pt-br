---
title: Conector do Azure IoT para FHIR (visualização)-guia de solução de problemas e instruções
description: Como solucionar problemas de mensagens e condições de erro comuns do conector do Azure IoT para FHIR (versão prévia) e copiar arquivos de mapeamento
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 403b6656a47f56508682dcda2438a85d513fbfb1
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630491"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>Guia de solução de problemas do conector do Azure IoT para FHIR (versão prévia)

Este artigo fornece etapas para solucionar problemas do conector IoT do Azure comum para recursos de interoperabilidade do Fast Healthcare (FHIR&#174;) * mensagens de erro e condições.  

Você também aprenderá a criar cópias do conector do Azure IoT para mapeamentos de conversão do FHIR JSON (por exemplo: Device e FHIR).  

Você pode usar as cópias JSON de mapeamento de conversão para edição e arquivamento fora do portal do Azure.  

> [!TIP]
> Se você estiver abrindo um tíquete de [suporte técnico do Azure](https://azure.microsoft.com/support/create-ticket/) para o conector do Azure IOT para FHIR, certifique-se de incluir cópias do JSON de mapeamento de conversão para ajudar com o processo de solução de problemas.

## <a name="device-and-fhir-conversion-mapping-json-template-validations-for-azure-iot-connector-for-fhir-preview"></a>Validações de modelo JSON de mapeamento de conversão de dispositivo e FHIR para o conector do Azure IoT para FHIR (versão prévia)
Nesta seção, você aprenderá sobre o processo de validação que o conector IoT do Azure para FHIR executa para validar os modelos JSON de mapeamento de conversão de dispositivo e FHIR antes de permitir que eles sejam salvos para uso.  Esses elementos são necessários no dispositivo e no mapeamento de conversão de FHIR JSON.

**Mapeamento de dispositivo**

|Elemento|Obrigatório|
|:-------|:------|
|TypeName|Verdadeiro|
|TypeMatchExpression|Verdadeiro|
|DeviceIdExpression|Verdadeiro|
|Carimbo de data/hora|Verdadeiro|
|Valores []. ValueName|Verdadeiro|
|Valores []. Valor da|Verdadeiro|

> [!NOTE]
> Valores []. ValueName e valores []. Valor da
>
> Esses elementos só serão necessários se você tiver uma entrada de valor na matriz-é válido que não tenha nenhum valor mapeado. Isso é usado quando a telemetria que está sendo enviada é um evento. Por exemplo: quando um dispositivo portátil IoMT é colocado ou removido. Os elementos não têm nenhum valor, exceto para um nome que o conector IoT do Azure para FHIR corresponde e emite. Na conversão FHIR, o conector IoT do Azure para FHIR o mapeia para um conceito de código com base no tipo semântico – nenhum valor real é populado.

**Mapeamento de FHIR**

|Elemento|Obrigatório|
|:------|:-------|
|TypeName|Verdadeiro|

> [!NOTE]
> Esse é o único elemento de mapeamento FHIR necessário validado neste momento.

## <a name="error-messages-and-fixes-for-azure-iot-connector-for-fhir-preview"></a>Mensagens de erro e correções para o conector do Azure IoT para FHIR (versão prévia)

|Mensagem|Visível|Condição|Fix| 
|-------|---------|---------|---|
|Nome de mapeamento inválido, o nome de mapeamento deve ser Device ou FHIR.|API|O tipo de mapeamento fornecido não é dispositivo ou FHIR.|Use um dos dois tipos de mapeamento com suporte (por exemplo: Device ou FHIR).|
|Falha na validação. As informações necessárias estão ausentes ou não são válidas.|API e portal do Azure|Tentativa de salvar um mapeamento de conversão que não tem informações ou elemento necessário.|Adicione informações ou elemento de mapeamento de conversão ausente e tente salvar o mapeamento de conversão novamente.|
|Regenerar parâmetros de chave não definidos.|API|Regenerar solicitação de chave.|Inclua os parâmetros na solicitação de chave de regeneração.|
|Foi atingido o número máximo de instâncias do conector IoT que podem ser provisionadas nesta assinatura.|API e portal do Azure|Conector de IoT do Azure para cota de assinatura do FHIR atingido (o padrão é (2) por assinatura).|Exclua uma das instâncias existentes do conector IoT do Azure para FHIR.  Use uma assinatura diferente que não tenha atingido a cota de assinatura.  Solicite um aumento de cota de assinatura.|
|O recurso de movimentação não tem suporte para o conector IoT com a API do Azure habilitada para o recurso FHIR.|API e portal do Azure|Tentativa de fazer uma operação de movimentação em uma API do Azure para o recurso FHIR que tem uma ou mais instâncias do conector IoT do Azure para FHIR.|Exclua as instâncias existentes do conector IoT do Azure para FHIR para fazer a operação de movimentação.|
|Conector IoT não provisionado.|API|Tentar usar serviços filho (conexões & mapeamentos) quando o pai (conector do Azure IoT para FHIR) não tiver sido provisionado.|Provisionar um conector do Azure IoT para FHIR.|
|A solicitação não terá suporte.|API|Não há suporte para a solicitação de API específica.|Use a solicitação de API correta.|
|A conta não existe.|API|A tentativa de adicionar um conector do Azure IoT para FHIR e a API do Azure para o recurso FHIR não existe.|Crie a API do Azure para o recurso FHIR e tente a operação novamente.|
|A API do Azure para a versão FHIR do recurso FHIR não tem suporte para o conector IoT.|API|Tentando usar um conector do Azure IoT para FHIR com uma versão incompatível da API do Azure para o recurso FHIR.|Crie uma nova API do Azure para o recurso FHIR (versão R4) ou use uma API do Azure existente para o recurso FHIR (versão R4).

## <a name="why-is-my-azure-iot-connector-for-fhir-preview-data-not-showing-up-in-azure-api-for-fhir"></a>Por que o meu conector do Azure IoT para dados FHIR (versão prévia) não aparece na API do Azure para FHIR?

|Possíveis problemas|Correções|
|----------------|-----|
|Os dados ainda estão sendo processados.|Os dados são redados à API do Azure para FHIR em lotes (a cada ~ 15 minutos).  É possível que os dados ainda estejam sendo processados e o tempo adicional seja necessário para que os dados sejam persistidos na API do Azure para FHIR.|
|O mapeamento de conversão de dispositivo JSON não foi configurado.|Configure e salve o JSON de mapeamento de conversão de dispositivo em conformidade.|
|O mapeamento de conversão de FHIR JSON não foi configurado.|Configure e salve o JSON de mapeamento de conversão de FHIR em conformidade.|
|A mensagem do dispositivo não contém uma expressão esperada definida no mapeamento do dispositivo.|Verifique se as expressões JsonPath definidas no mapeamento de dispositivos correspondem aos tokens definidos na mensagem do dispositivo.|
|Um recurso de dispositivo não foi criado na API do Azure para FHIR (tipo de resolução: somente pesquisa) *.|Crie um recurso de dispositivo válido na API do Azure para FHIR. Verifique se o recurso de dispositivo contém um identificador que corresponde ao identificador de dispositivo fornecido na mensagem de entrada.|
|Um recurso de paciente não foi criado na API do Azure para FHIR (tipo de resolução: somente pesquisa) *.|Crie um recurso de paciente válido na API do Azure para FHIR.|
|A referência do dispositivo. paciente não está definida ou a referência é inválida (tipo de resolução: somente pesquisa) *.|Verifique se o recurso do dispositivo contém uma [referência](https://www.hl7.org/fhir/device-definitions.html#Device.patient) válida a um recurso do paciente.| 

* Guia de [início rápido: implantar o conector do Azure IOT (versão prévia) usando portal do Azure](iot-fhir-portal-quickstart.md#create-new-azure-iot-connector-for-fhir-preview) para obter uma descrição funcional do conector IOT do Azure para tipos de resolução FHIR (por exemplo: Lookup ou CREATE).

## <a name="use-metrics-to-troubleshoot-issues-in-azure-iot-connector-for-fhir-preview"></a>Usar métricas para solucionar problemas no conector do Azure IoT para FHIR (versão prévia)

O conector do Azure IoT para FHIR gera várias métricas para fornecer informações sobre o processo de fluxo de dados. Uma das métricas com suporte é chamada *total de erros* , que fornece a contagem de todos os erros que ocorrem dentro de uma instância do conector do Azure IOT para FHIR.

Cada erro é registrado com um número de propriedades associadas. Cada propriedade fornece um aspecto diferente sobre o erro, que pode ajudá-lo a identificar e solucionar problemas. Esta seção lista propriedades diferentes capturadas para cada erro na métrica *total de erros* e possíveis valores para essas propriedades.

> [!NOTE]
> Você pode navegar até a métrica *total de erros* de uma instância do conector do Azure IOT para FHIR (versão prévia) conforme descrito na página de [métricas do conector do Azure IOT para FHIR (versão prévia)](iot-metrics-display.md).

Clique no gráfico *total de erros* e, em seguida, clique no botão *Adicionar filtro* para dividir e refinar a métrica de erro usando qualquer uma das propriedades mencionadas abaixo.

### <a name="the-operation-performed-by-the-azure-iot-connector-for-fhir-preview"></a>A operação executada pelo conector do Azure IoT para FHIR (versão prévia)

Essa propriedade representa a operação que está sendo executada pelo conector IoT quando o erro ocorreu. Uma operação geralmente representa o estágio de fluxo de dados durante o processamento de uma mensagem de dispositivo. Aqui está a lista de valores possíveis para essa propriedade.

> [!NOTE]
> Você pode ler mais sobre diferentes estágios de fluxo de dados no conector IoT do Azure para FHIR (versão prévia) [aqui](iot-data-flow.md).

|Estágio de fluxo de dados|Description|
|---------------|-----------|
|Instalação|Operação específica para configurar sua instância do conector IoT|
|Normalização|Estágio de fluxo de dados em que os dados do dispositivo são normalizados|
|Agrupamento|Estágio de fluxo de dados em que os dados normalizados são agrupados|
|FHIRConversion|Estágio de fluxo de dados em que os dados normalizados agrupados são transformados em um recurso FHIR|
|Desconhecido|O tipo de operação é desconhecido quando ocorreu um erro|

### <a name="the-severity-of-the-error"></a>A severidade do erro

Essa propriedade representa a severidade do erro ocorrido. Aqui está a lista de valores possíveis para essa propriedade.

|Severidade|Descrição|
|---------------|-----------|
|Aviso|Existe algum problema secundário no processo de fluxo de dados, mas o processamento da mensagem do dispositivo não é interrompido|
|Erro|O processamento de uma mensagem de dispositivo específica encontrou um erro e outras mensagens podem continuar a ser executadas conforme o esperado|
|Crítico|Existe algum problema no nível do sistema com o conector IoT e nenhuma mensagem é esperada para processar|

### <a name="the-type-of-the-error"></a>O tipo do erro

Essa propriedade significa uma categoria para um determinado erro, que basicamente representa um agrupamento lógico para tipos de erros semelhantes. Aqui está a lista de possíveis valores para essa propriedade.

|Tipo de erro|Description|
|----------|-----------|
|DeviceTemplateError|Erros relacionados aos modelos de mapeamento de dispositivo|
|DeviceMessageError|Ocorreram erros ao processar uma mensagem de dispositivo específica|
|FHIRTemplateError|Erros relacionados a modelos de mapeamento FHIR|
|FHIRConversionError|Ocorreram erros ao transformar uma mensagem em um recurso FHIR|
|FHIRResourceError|Erros relacionados a recursos existentes no servidor FHIR referenciado pelo conector IoT|
|FHIRServerError|Erros que ocorrem ao se comunicar com o servidor FHIR|
|GeneralError|Todos os outros tipos de erros|

### <a name="the-name-of-the-error"></a>O nome do erro

Essa propriedade fornece o nome para um erro específico. Aqui está a lista de todos os nomes de erro com sua descrição e os tipos de erro associados, severidade e estágio (s) de fluxo de dados.

|Nome do erro|Description|Tipo (s) de erro|Severidade do erro|Estágio (s) de fluxo de dados|
|----------|-----------|-------------|--------------|------------------|
|MultipleResourceFoundException|Ocorreu um erro quando vários recursos do paciente ou do dispositivo são encontrados no servidor FHIR para os respectivos identificadores presentes na mensagem do dispositivo|FHIRResourceError|Erro|FHIRConversion|
|TemplateNotFoundException|Um modelo de mapeamento de dispositivo ou FHIR não está configurado com a instância do conector IoT|DeviceTemplateError, FHIRTemplateError|Crítico|Normalização, FHIRConversion|
|CorrelationIdNotDefinedException|A ID de correlação não está especificada no modelo de mapeamento de dispositivo. CorrelationIdNotDefinedException é um erro condicional que ocorre somente quando a observação FHIR deve agrupar medidas de dispositivo usando uma ID de correlação, mas ela não está configurada corretamente|DeviceMessageError|Erro|Normalização|
|PatientDeviceMismatchException|Esse erro ocorre quando o recurso do dispositivo no servidor FHIR tem uma referência a um recurso do paciente, que não corresponde ao identificador do paciente presente na mensagem|FHIRResourceError|Erro|FHIRConversionError|
|PatientNotFoundException|Nenhum recurso de FHIR do paciente é referenciado pelo recurso FHIR do dispositivo associado ao identificador do dispositivo presente na mensagem do dispositivo. Observação esse erro ocorrerá somente quando a instância do conector IoT estiver configurada com o tipo de resolução de *pesquisa*|FHIRConversionError|Erro|FHIRConversion|
|DeviceNotFoundException|Não existe nenhum recurso de dispositivo no servidor FHIR associado ao identificador de dispositivo presente na mensagem do dispositivo|DeviceMessageError|Erro|Normalização|
|PatientIdentityNotDefinedException|Esse erro ocorre quando a expressão para analisar o identificador do paciente da mensagem do dispositivo não está configurada no modelo de mapeamento de dispositivo ou o identificador de paciente não está presente na mensagem do dispositivo. Observe que esse erro ocorre somente quando o tipo de resolução do conector IoT é definido como *criar*|DeviceTemplateError|Crítico|Normalização|
|DeviceIdentityNotDefinedException|Esse erro ocorre quando a expressão para analisar o identificador de dispositivo da mensagem do dispositivo não está configurada no modelo de mapeamento de dispositivo ou o identificador de dispositivo não está presente na mensagem do dispositivo|DeviceTemplateError|Crítico|Normalização|
|NotSupportedException|Ocorreu um erro quando a mensagem do dispositivo com formato sem suporte é recebida|DeviceMessageError|Erro|Normalização|

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-conversion-mapping-json"></a>Criando cópias do Azure IoT Connector para FHIR (visualização) mapeamento de conversão JSON

A cópia do conector do Azure IoT para arquivos de mapeamento FHIR pode ser útil para edição e arquivamento fora do site portal do Azure.

As cópias de arquivos de mapeamento devem ser fornecidas ao suporte técnico do Azure ao abrir um tíquete de suporte para auxiliar na solução de problemas.

> [!NOTE]
> JSON é o único formato com suporte para arquivos de mapeamento de dispositivo e FHIR no momento.

> [!TIP]
> Saiba mais sobre o conector do Azure IoT para o [dispositivo FHIR e o mapeamento de conversão FHIR JSON](./iot-mapping-templates.md)

1. Selecione **"conector IOT (versão prévia)"** no canto inferior esquerdo da API do Azure para o painel de recursos FHIR na seção **"suplementos"** .

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="Connector1 IoT" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Selecione o **"conector"** do qual você copiará o mapeamento de conversão JSON.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="Connector2 IoT" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> Esse processo também pode ser usado para copiar e salvar o conteúdo do JSON **"Configurar mapeamento de FHIR"** .

3. Selecione **"Configurar mapeamento de dispositivo"**.

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="Connector3 IoT" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. Selecione o conteúdo do JSON e faça uma operação de cópia (por exemplo: selecione CTRL + c). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="Connector4 IoT" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Faça uma operação de colagem (por exemplo: selecione CTRL + v) em um novo arquivo dentro de um editor (por exemplo: Visual Studio Code, bloco de notas) e salve o arquivo com uma extensão *. JSON.

> [!TIP]
> Se você estiver abrindo um tíquete de [suporte técnico do Azure](https://azure.microsoft.com/support/create-ticket/) para o conector do Azure IOT para FHIR, certifique-se de incluir cópias do JSON de mapeamento de conversão para ajudar com o processo de solução de problemas.

## <a name="next-steps"></a>Próximas etapas

Confira as perguntas frequentes sobre o conector do Azure IoT para FHIR.

>[!div class="nextstepaction"]
>[Azure IoT Connector para FHIR FAQs](fhir-faq.md)

* No portal do Azure, o conector do IoT do Azure para FHIR é conhecido como conector IoT (versão prévia). FHIR é uma marca registrada de HL7 e é usada com a permissão de HL7.