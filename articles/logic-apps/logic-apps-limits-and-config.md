---
title: Limites e configuração
description: Limites de serviço, como duração, taxa de transferência e capacidade, além de valores da configuração, como endereços IP para permitir, para Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 02/05/2021
ms.openlocfilehash: 6c064acc44e180d3e99bdcf68d2e1e129d52fd5d
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805927"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Informações de limites e configuração para os Aplicativos Lógicos do Azure

Este artigo descreve os limites e os detalhes de configuração para criar e executar fluxos de trabalho automatizados com os Aplicativos Lógicos do Azure. Para o Power Automate, consulte [Limites e configuração no Power Automate](/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="logic-app-definition-limits"></a>Limites de definição de aplicativos lógicos

Estes são os limites de definição de um único aplicativo lógico:

| Nome | Limite | Observações |
| ---- | ----- | ----- |
| Ações por fluxo de trabalho | 500 | Para estender esse limite, adicione fluxos de trabalho aninhados conforme necessário. |
| Profundidade de aninhamento permitido para ações | 8 | Para estender esse limite, adicione fluxos de trabalho aninhados conforme necessário. |
| Fluxos de trabalho por região e assinatura | 1,000 | |
| Gatilhos por fluxo de trabalho | 10 | Ao trabalhar no modo de exibição de código, não no designer |
| Limite de casos de escopo do comutador | 25 | |
| Variáveis por fluxo de trabalho | 250 | |
| Nome para `action` ou `trigger` | 80 caracteres | |
| Caracteres por expressão | 8\.192 | |
| Comprimento de `description` | 256 caracteres | |
| Número máximo de `parameters` | 50 | |
| Número máximo de `outputs` | 10 | |
| Tamanho máximo para `trackedProperties` | 16.000 caracteres |
| Ação de código embutido-número máximo de caracteres de código | 1.024 caracteres | Para estender esse limite para 100.000 caracteres, crie seus aplicativos lógicos com o tipo de recurso **aplicativo lógico (versão prévia)** , seja [usando o portal do Azure](create-stateful-stateless-workflows-azure-portal.md) ou [usando Visual Studio Code e a extensão **aplicativos lógicos do Azure (versão prévia)**](create-stateful-stateless-workflows-visual-studio-code.md). |
| Ação de código embutido-duração máxima para execução de código | 5 segundos | Para estender esse limite para 15 segundos, crie seus aplicativos lógicos com o tipo de recurso **aplicativo lógico (versão prévia)** , seja [usando o portal do Azure](create-stateful-stateless-workflows-azure-portal.md) ou [usando Visual Studio Code e a extensão **aplicativos lógicos do Azure (versão prévia)**](create-stateful-stateless-workflows-visual-studio-code.md). |
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-history-limits"></a>Duração da execução e limites do histórico de retenção

Estes são os limites de execução de um único aplicativo lógico:

| Nome | Limite de multilocatários | Limite do ambiente do serviço de integração | Observações |
|------|--------------------|---------------------------------------|-------|
| Duração da execução | 90 dias | 366 dias | A duração da execução é calculada usando a hora de início de uma execução e o limite especificado na configuração do fluxo de trabalho, [**retenção do histórico de execução em dias**](#change-duration) na hora de início. <p><p>Para alterar o limite padrão, consulte [duração da execução de alteração e retenção de histórico no armazenamento](#change-duration). |
| Retenção de histórico de execução no armazenamento | 90 dias | 366 dias | Se a duração de uma execução exceder o limite de retenção do histórico de execução atual, a execução será removida do histórico de execuções no armazenamento. Se a execução for concluída ou expirar, a retenção do histórico de execução será sempre calculada usando a hora de início da execução e o limite atual especificado na configuração de fluxo de trabalho, [**retenção de histórico de execução em dias**](#change-retention). Não importa o limite anterior, o limite atual é sempre usado para calcular a retenção. <p><p>Para alterar o limite padrão e obter mais informações, consulte [alterar duração e retenção do histórico de execução no armazenamento](#change-retention). Para aumentar o limite máximo, [entre em contato com a equipe de Aplicativos Lógicos](mailto://logicappsemail@microsoft.com) para ter ajuda com seus requisitos. |
| Intervalo de recorrência mínimo | 1 segundo | 1 segundo ||
| Intervalo de recorrência máximo | 500 dias | 500 dias ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-history-retention-in-storage"></a>Duração da execução de alteração e retenção de histórico no armazenamento

A mesma configuração controla o número máximo de dias que um fluxo de trabalho pode executar e para manter o histórico de execução no armazenamento. Para alterar o limite padrão ou atual para essas propriedades, siga estas etapas.

* Para os aplicativos lógicos no Azure com multilocatário, o limite padrão de 90 dias é igual ao limite máximo. Você só pode diminuir esse valor.

* Para os aplicativos lógicos em um ambiente do serviço de integração, você pode diminuir ou aumentar o limite padrão de 90 dias.

Por exemplo, suponha que você reduza o limite de retenção de 90 dias para 30 dias. Uma execução de 60 dias-antiga é removida do histórico de execuções. Se você aumentar o período de retenção de 30 dias para 60 dias, uma execução de 20 dias atrás permanecerá no histórico de execuções por mais 40 dias.

> [!IMPORTANT]
> Se a duração de uma execução exceder o limite de retenção do histórico de execução atual, a execução será removida do histórico de execuções no armazenamento. Para evitar a perda do histórico de execuções, verifique se o limite de retenção é *sempre* mais do que a duração mais longa possível da execução.

1. Na caixa de pesquisa [portal do Azure](https://portal.azure.com) , localize e selecione **aplicativos lógicos**.

1. Localize e selecione seu aplicativo lógico. Abra seu aplicativo lógico no designer de aplicativo lógico.

1. No menu do aplicativo lógico, selecione **Configurações do fluxo de trabalho**.

1. Em **Opções de runtime**, na lista **Retenção do histórico de execuções em dias**, escolha **Personalizar**.

1. Arraste o controle deslizante para mudar o número de dias que você deseja.

1. Quando terminar, selecione **Salvar** na barra de ferramentas **Configurações do fluxo de trabalho**.

Se você gerar um modelo de Azure Resource Manager para seu aplicativo lógico, essa configuração aparecerá como uma propriedade na definição de recurso do fluxo de trabalho, que é descrita na [referência do modelo de fluxos de trabalho Microsoft. Logic](/azure/templates/microsoft.logic/workflows):

```json
{
   "name": "{logic-app-name}",
   "type": "Microsoft.Logic/workflows",
   "location": "{Azure-region}",
   "apiVersion": "2019-05-01",
   "properties": {
      "definition": {},
      "parameters": {},
      "runtimeConfiguration": {
         "lifetime": {
            "unit": "day",
            "count": {number-of-days}
         }
      }
   }
}
```

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Simultaneidade, um loop e debatching de limites

Estes são os limites de execução de um único aplicativo lógico:

### <a name="loops"></a>Loops

| Nome | Limite | Observações |
| ---- | ----- | ----- |
| Itens da matriz de foreach | 100.000 | Esse limite descreve o número máximo de itens de matriz que um loop "para cada" pode processar. <p><p>Para filtrar matrizes maiores, você pode usar o [ação de consulta](logic-apps-perform-data-operations.md#filter-array-action). |
| Simultaneidade de foreach | Com simultaneidade desativada: 20 <p><p>Com simultaneidade em: <p><p>-Padrão: 20 <br>-Mín.: 1 <br>-Máx.: 50 | Esse limite é o número máximo de iterações de loop "for each" que podem ser executadas ao mesmo tempo ou em paralelo. <p><p>Para alterar esse limite, consulte [alterar "para cada" limite de simultaneidade](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) ou [executar "para cada loop" em sequência](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). |
| Iterações Until | - Padrão: 60 <br>-Mín.: 1 <br>-Máx.: 5.000 | O número máximo de ciclos que um loop "until" pode ter durante uma execução de aplicativo lógico. <p><p>Para alterar esse limite, na forma de loop "until", selecione **alterar limites** e especifique o valor para a propriedade **Count** . |
| Até o tempo limite | -Padrão: PT1H (1 hora) | A maior quantidade de tempo que o loop "until" pode ser executado antes de sair e é especificado no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). O valor de tempo limite é avaliado para cada ciclo de loop. Se qualquer ação no loop demorar mais do que o tempo limite, o ciclo atual não parará. No entanto, o próximo ciclo será iniciado porque a condição de limite não foi atendida. <p><p>Para alterar esse limite, na forma de loop "until", selecione **alterar limites** e especifique o valor para a propriedade **Timeout** . |
||||

### <a name="concurrency-and-debatching"></a>Simultaneidade e deloteing

| Nome | Limite | Observações |
| ---- | ----- | ----- |
| Simultaneidade do gatilho | Com simultaneidade desativada: ilimitada <p><p>Com simultaneidade ativada, que não pode ser desfeita após habilitar: <p><p>-Padrão: 25 <br>-Mín.: 1 <br>-Máx.: 50 | Esse limite é o número máximo de instâncias de aplicativo lógico que podem ser executadas ao mesmo tempo ou em paralelo. <p><p>**Observação**: Quando a simultaneidade é ativada, o limite SplitOn é reduzido para 100 itens para [desfazer o lote nas matrizes](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). <p><p>Para alterar esse limite, consulte [alterar o limite de simultaneidade de gatilho](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) ou [instâncias de gatilho sequencialmente](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). |
| Execuções de espera máximo | Com simultaneidade desativada: <p><p>-Mín.: 1 <br>-Máx.: 50 <p><p>Com simultaneidade em: <p><p>-Min: 10 mais o número de execuções simultâneas (disparo de simultaneidade) <br>-Máx.: 100 | Esse limite é o número máximo de instâncias de aplicativo lógico que podem esperar para serem executadas quando seu aplicativo lógico já estiver executando o máximo de instâncias simultâneas. <p><p>Para alterar esse limite, confira [alterar limite de execuções em espera](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). |
| Itens SplitOn | Com simultaneidade desativada: 100.000 <p><p>Com simultaneidade em: 100 | Para gatilhos que retornam uma matriz, você pode especificar uma expressão que usa uma propriedade 'SplitOn' que [divide ou retira de lote os itens da matriz em várias instâncias de fluxo de trabalho](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) para processamento, em vez de usar um loop "Foreach". Essa expressão referencia a matriz a ser usada para criar e executar uma instância de fluxo de trabalho para cada item da matriz. <p><p>**Observação**: Quando a simultaneidade é ativada, o limite SplitOn é reduzido para 100 itens. |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Limites de taxa de transferência

Estes são os limites de definição de um único aplicativo lógico:

### <a name="multi-tenant-logic-apps-service"></a>Serviço de Aplicativos Lógicos com multilocatário

| Nome | Limite | Observações |
| ---- | ----- | ----- |
| Ação: Execuções a cada 5 minutos | 100.000 é o limite padrão, mas 300.000 é o limite máximo. | Para aumentar o limite padrão para o máximo de seu aplicativo lógico, consulte [executar no modo de alta taxa de transferência](#run-high-throughput-mode), que está em versão prévia. Ou, você pode [distribuir a carga de trabalho entre mais de um aplicativo lógico](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling) , conforme necessário. |
| Ação: chamadas de saída simultâneas | ~2.500 | Você pode reduzir o número de solicitações simultâneas ou reduzir a duração conforme necessário. |
| Ponto de extremidade de tempo de execução: chamadas de entrada simultâneas | ~1,000 | Você pode reduzir o número de solicitações simultâneas ou reduzir a duração conforme necessário. |
| Ponto de extremidade de runtime: Chamadas de leitura a cada 5 minutos  | 60.000 | Esse limite se aplica a chamadas que obtêm as entradas e saídas brutas do histórico de execução de um aplicativo lógico. Você pode distribuir a carga de trabalho entre mais de um aplicativo, conforme necessário. |
| Ponto de extremidade de runtime: Chamadas de invocação a cada 5 minutos | 45,000 | Você pode distribuir a carga de trabalho entre mais de um aplicativo, conforme necessário. |
| Taxa de transferência de conteúdo por 5 minutos | 600 MB | Você pode distribuir a carga de trabalho entre mais de um aplicativo, conforme necessário. |
||||

<a name="run-high-throughput-mode"></a>

#### <a name="run-in-high-throughput-mode"></a>Executar no modo de alta taxa de transferência

Para uma definição de aplicativo lógico único, o número de ações executadas a cada 5 minutos tem um [limite padrão](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Para aumentar o limite padrão para o máximo de seu aplicativo lógico, você pode habilitar o modo de alta taxa de transferência, que está em versão prévia. Ou, você pode [distribuir a carga de trabalho entre mais de um aplicativo lógico](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling) , conforme necessário.

1. No portal do Azure, no menu do aplicativo lógico, em **configurações**, selecione **configurações de fluxo de trabalho**.

1. Em **Opções de tempo de execução**  >  **alta taxa de transferência**, altere a configuração para **ativado**.

   ![Captura de tela que mostra o menu do aplicativo lógico no portal do Azure com "configurações de fluxo de trabalho" e "alta taxa de transferência" definida como "ativada".](./media/logic-apps-limits-and-config/run-high-throughput-mode.png)

Para habilitar essa configuração em um modelo ARM para implantar seu aplicativo lógico, no `properties` objeto para a definição de recurso do aplicativo lógico, adicione o `runtimeConfiguration` objeto com a `operationOptions` propriedade definida como `OptimizedForHighThroughput` :

```json
{
   <template-properties>
   "resources": [
      // Start logic app resource definition
      {
         "properties": {
            <logic-app-resource-definition-properties>,
            <logic-app-workflow-definition>,
            <more-logic-app-resource-definition-properties>,
            "runtimeConfiguration": {
               "operationOptions": "OptimizedForHighThroughput"
            }
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {},
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

Para obter mais informações sobre a definição de recurso de aplicativo lógico, consulte [visão geral: automatizar a implantação para aplicativos lógicos do Azure usando modelos de Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition).

### <a name="integration-service-environment-ise"></a>Ambiente do serviço de integração (ISE)

Estes são os limites da taxa de transferência para o [SKU do ISE Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level):

| Nome | Limite | Observações |
|------|-------|-------|
| Limite de execução da unidade base | Sistema limitado quando a capacidade da infraestrutura atinge 80% | Fornece ~4.000 execuções de ação por minuto, que são ~160 milhões execuções de ação por mês | |
| Limite de execução da unidade de escala | Sistema limitado quando a capacidade da infraestrutura atinge 80% | Cada unidade de escala pode fornecer ~2.000 execuções de ação adicionais por minuto, que são mais ~80 milhões de execuções de ação por mês | |
| Unidades de escala máximas que você pode adicionar | 10 | |
||||

Para ultrapassar esses limites no processamento normal ou executar um teste de carga que possa ultrapassar esses limites, [entre em contato com a equipe de Aplicativos Lógicos](mailto://logicappsemail@microsoft.com) para que possam ajudá-lo com suas necessidades.

> [!NOTE]
> O [SKU do ISE do Desenvolvedor](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) não tem limites publicados, nenhum recurso para escalar verticalmente, nem SLA (contrato de nível de serviço). Use esse SKU somente para experimentar, desenvolver e testar, não para produção ou teste de desempenho.

<a name="gateway-limits"></a>

## <a name="gateway-limits"></a>Limites do gateway

Os Aplicativos Lógicos do Azure dão suporte a operações de gravação, incluindo inserções e atualizações, por meio do gateway. No entanto, essas operações têm [limites no tamanho da carga](/data-integration/gateway/service-gateway-onprem#considerations).

<a name="http-limits"></a>

## <a name="http-limits"></a>Limites do HTTP

Aqui estão os limites para uma única chamada de entrada ou de saída:

<a name="http-timeout-limits"></a>

#### <a name="timeout-duration"></a>Duração do tempo limite

Algumas operações de conector fazem chamadas assíncronas ou escutam solicitações de webhook, portanto o tempo limite para essas operações pode ter mais do que esses limites. Para obter mais informações, consulte os detalhes técnicos para o conector específico e também [Gatilhos e ações de fluxo de trabalho](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| Nome | Aplicativos lógicos (multilocatário) | Aplicativos lógicos (visualização) | Ambiente do serviço de integração | Observações |
|------|---------------------------|----------------------|---------------------------------|-------|
| Solicitação de saída | 120 segundos <br>(2 minutos) | 230 segundos <br>(3,9 minutos) | 240 segundos <br>(4 minutos) | Exemplos de solicitações de saída incluem chamadas feitas pelo gatilho ou ação HTTP. Para obter mais informações sobre a versão de visualização, consulte [visualização de aplicativos lógicos do Azure](logic-apps-overview-preview.md). <p><p>**Dica**: Para operações com execução mais longa, use um [padrão de sondagem assíncrona](../logic-apps/logic-apps-create-api-app.md#async-pattern) ou um [até que o loop](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). Para solucionar os limites de tempo ao chamar outro aplicativo lógico que tem um [ponto de extremidade de chamada](logic-apps-http-endpoint.md), você pode usar a ação predefinida dos Aplicativos Lógicos do Azure, que pode ser encontrada no seletor do conector em **Predefinido**. |
| Solicitação de entrada | 120 segundos <br>(2 minutos) | 230 segundos <br>(3,9 minutos) | 240 segundos <br>(4 minutos) | Exemplos de solicitações de entrada incluem chamadas recebidas pelo gatilho de solicitação, gatilho de webhook HTTP e ação de webhook HTTP. Para obter mais informações sobre a versão de visualização, consulte [visualização de aplicativos lógicos do Azure](logic-apps-overview-preview.md). <p><p>**Observação**: Para o chamador original obter a resposta, todas as etapas na resposta devem ser finalizadas dentro do limite, a menos que você chame outro aplicativo lógico como um fluxo de trabalho aninhado. Para obter mais informações, consulte [Chamar, acionar ou aninhar aplicativos lógicos](../logic-apps/logic-apps-http-endpoint.md). |
||||||

<a name="message-size-limits"></a>

#### <a name="message-size"></a>Tamanho da mensagem

| Nome | Limite de multilocatários | Limite do ambiente do serviço de integração | Observações |
|------|--------------------|---------------------------------------|-------|
| Tamanho da mensagem | 100 MB | 200 MB | Para contornar esse limite, consulte [Tratar mensagens grandes com agrupamento](../logic-apps/logic-apps-handle-large-messages.md). No entanto, alguns conectores e APIs podem não oferecer suporte a agrupamento ou até o limite padrão. <p><p>- Conectores como AS2, X12 e EDIFACT têm seus próprios [limites de mensagem B2B](#b2b-protocol-limits). <br>- Os conectores do ISE usam o limite do ISE, não os limites do conector diferentes do ISE. |
| Tamanho da mensagem com agrupamento | 1 GB | 5 GB | Esse limite se aplica a ações com compatibilidade nativa com o agrupamento ou que permitem que você habilite o agrupamento na configuração de runtime. <p><p>Se você estiver usando um ISE, o mecanismo dos Aplicativos Lógicos dá suporte a esse limite, mas os conectores têm seus próprios limites de agrupamento até o limite do mecanismo; por exemplo, consulte a [Referência de API do conector do Armazenamento de Blobs do Azure](/connectors/azureblob/). Para obter mais informações sobre o agrupamento, consulte [Tratar mensagens grandes com agrupamento](../logic-apps/logic-apps-handle-large-messages.md). |
|||||

#### <a name="character-limits"></a>Limites de caractere

| Nome | Observações |
|------|-------|
| Limite de avaliação da expressão | 131.072 caracteres | As expressões `@concat()`, `@base64()`, `@string()` não podem ser maiores do que esse limite. |
| Limite de caracteres da URL de solicitação | 16.384 caracteres |
|||

<a name="retry-policy-limits"></a>

#### <a name="retry-policy"></a>Política de repetição

| Nome | Limite | Observações |
| ---- | ----- | ----- |
| Tentativas de repetição | 90 | O padrão é 4. Para alterar o padrão, use o [Parâmetro de política de repetição](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Atraso máximo de nova tentativa | 1 dia | Para alterar o padrão, use o [Parâmetro de política de repetição](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Atraso mínimo de nova tentativa | 5 segundos | Para alterar o padrão, use o [Parâmetro de política de repetição](../logic-apps/logic-apps-workflow-actions-triggers.md). |
||||

<a name="authentication-limits"></a>

### <a name="authentication-limits"></a>Limites da autenticação

Veja os limites de um aplicativo lógico que começa com um Gatilho de solicitação e permite a [Open Authorization do Azure Active Directory](../active-directory/develop/index.yml) (OAuth do Azure AD) para autorizar chamadas de entrada para esse gatilho:

| Nome | Limite | Observações |
| ---- | ----- | ----- |
| Políticas de autorização do Azure AD | 5 | |
| Declarações por política de autorização | 10 | |
| Valor da declaração – número máximo de caracteres | 150 |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Limites do conector personalizado

Aqui estão os limites para conectores personalizados que você pode criar de APIs da Web.

| Nome | Limite de multilocatários | Limite do ambiente do serviço de integração | Observações |
|------|--------------------|---------------------------------------|-------|
| Número de conectores personalizados | 1\.000 por assinatura do Azure | 1\.000 por assinatura do Azure ||
| Número de solicitações por minuto para um conector personalizado | 500 solicitações por minuto e por conexão | 2\.000 solicitações por minuto e por *conector personalizado* ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Identidades gerenciadas

| Nome | Limite |
|------|-------|
| Identidades gerenciadas por aplicativo lógico | Identidade atribuída pelo sistema ou 1 identidade atribuída pelo usuário |
| Número de aplicativos lógicos que têm uma identidade gerenciada em uma assinatura do Azure por região | 1,000 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Limites da conta de integração

Cada assinatura do Azure tem estes limites da conta de integração:

* Uma conta de integração de [camada gratuita](../logic-apps/logic-apps-pricing.md#integration-accounts) por região do Azure. Essa camada está disponível somente para regiões públicas no Azure, por exemplo, oeste dos EUA ou sudeste asiático, mas não para o [Azure China 21vianet](/azure/china/overview-operations) ou [Azure governamental](../azure-government/documentation-government-welcome.md).

* 1\.000 contas de integração no total, incluindo contas de integração em qualquer [ISE (ambiente do serviço de integração)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) em [SKUs do Desenvolvedor e Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level).

* Cada ISE, seja [desenvolvedor ou Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level), pode usar uma única conta de integração sem custo adicional, embora o tipo de conta incluído varie de acordo com a SKU do ISE. Você pode criar mais contas de integração para o ISE até o limite total de um [custo adicional](logic-apps-pricing.md#fixed-pricing):

  | SKU do ISE | Limites da conta de integração |
  |---------|----------------------------|
  | **Premium** | 20 contas no total, incluindo uma conta padrão sem custo adicional. Com essa SKU, você pode ter apenas contas [padrão](../logic-apps/logic-apps-pricing.md#integration-accounts) . Nenhuma conta Gratuita ou Básica é permitida. |
  | **Desenvolvedor** | 20 contas no total, incluindo uma conta [gratuita](../logic-apps/logic-apps-pricing.md#integration-accounts) (limitada a 1). Com essa SKU, você pode ter qualquer uma das combinações: <p>-Uma conta gratuita e até 19 contas [padrão](../logic-apps/logic-apps-pricing.md#integration-accounts) . <br>-Nenhuma conta gratuita e até 20 contas padrão. <p>Nenhuma conta básica ou adicional é permitida. <p><p>**Importante**: Use a [SKU do desenvolvedor](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) para experimentação, desenvolvimento e teste, mas não para teste de produção ou de desempenho. |
  |||

Para saber como funcionam o preço e a cobrança nos ISEs, consulte o [Modelo de preços dos Aplicativos Lógicos](../logic-apps/logic-apps-pricing.md#fixed-pricing). Para obter os valores, consulte [Preços dos Aplicativos Lógicos](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Limites de artefato por conta de integração

Estes são os limites no número de artefatos para cada camada da conta de integração. Para obter os valores, consulte [Preços dos Aplicativos Lógicos](https://azure.microsoft.com/pricing/details/logic-apps/). Para saber como funcionam o preço e a cobrança nas contas de integração, consulte o [Modelo de preços dos Aplicativos Lógicos](../logic-apps/logic-apps-pricing.md#integration-accounts).

> [!NOTE]
> Use a camada Gratuita somente para cenários exploratórios, não cenários de produção. Esta camada restringe o uso e a taxa de transferência e não tem nenhum SLA (Contrato de Nível de Serviço).

| Artefato | Grátis | Basic | Standard |
|----------|------|-------|----------|
| Contratos comerciais de EDI | 10 | 1 | 1,000 |
| Parceiros comerciais de EDI | 25 | 2 | 1,000 |
| Mapas | 25 | 500 | 1,000 |
| Esquemas | 25 | 500 | 1,000 |
| Assemblies | 10 | 25 | 1,000 |
| Certificados | 25 | 2 | 1,000 |
| Configurações de lote | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Limites de capacidade do artefato

| Artefato | Limite | Observações |
| -------- | ----- | ----- |
| Assembly | 8 MB | Para carregar arquivos maiores que 2 MB, use um [contêiner de blob e uma conta de armazenamento do Azure](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
| Mapa (arquivo XSLT) | 8 MB | Para carregar arquivos maiores que 2 MB, use a [API REST de Aplicativos Lógicos do Azure – Mapas](/rest/api/logic/maps/createorupdate). <p><p>**Observação**: A quantidade de dados ou registros que um mapa pode processar com êxito baseia-se no tamanho da mensagem e nos limites de tempo da ação nos Aplicativos Lógicos do Azure. Por exemplo, se você usar uma ação HTTP, baseada no [tamanho da mensagem HTTP e nos limites de tempo](#http-limits), um mapa poderá processar dados até o limite de tamanho da mensagem HTTP se a operação for concluída dentro do limite de tempo do HTTP. |
| Esquema | 8 MB | Para carregar arquivos maiores que 2 MB, use um [contêiner de blob e uma conta de armazenamento do Azure](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
||||

<a name="integration-account-throughput-limits"></a>

### <a name="throughput-limits"></a>Limites de taxa de transferência

| Ponto de extremidade de runtime | Grátis | Basic | Standard | Observações |
|------------------|------|-------|----------|-------|
| Chamadas de leitura a cada 5 minutos | 3\.000 | 30,000 | 60.000 | Esse limite se aplica a chamadas que obtêm as entradas e saídas brutas do histórico de execução de um aplicativo lógico. Você pode distribuir a carga de trabalho entre mais de uma conta conforme necessário. |
| Chamadas de invocação a cada 5 minutos | 3\.000 | 30,000 | 45,000 | Você pode distribuir a carga de trabalho entre mais de uma conta conforme necessário. |
| Chamadas de acompanhamento a cada 5 minutos | 3\.000 | 30,000 | 45,000 | Você pode distribuir a carga de trabalho entre mais de uma conta conforme necessário. |
| Chamadas simultâneas de bloqueio | ~1,000 | ~1,000 | ~1,000 | O mesmo para todos os SKUs. Você pode reduzir o número de solicitações simultâneas ou reduzir a duração conforme necessário. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Tamanho da mensagem do protocolo B2B (AS2, X12, EDIFACT)

Estes são os limites da mensagem que se aplicam aos protocolos B2B:

| Nome | Limite de multilocatários | Limite do ambiente do serviço de integração | Observações |
|------|--------------------|---------------------------------------|-------|
| AS2 | v2 - 100MB<br>v1-25 MB | v2 - 200MB <br>v1-25 MB | Aplicável ao decodificar e codificar |
| X12 | 50 MB | 50 MB | Aplicável ao decodificar e codificar |
| EDIFACT | 50 MB | 50 MB | Aplicável ao decodificar e codificar |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Desabilitar ou excluir aplicativos lógicos

Ao desabilitar um aplicativo lógico, nenhuma nova execução será instanciada. Todas as execuções em andamento e pendentes continuam até que sejam concluídas, e isso pode demorar algum tempo.

Ao excluir um aplicativo lógico, nenhuma nova execução será instanciada. Todas as execuções em andamento e pendentes serão canceladas. Se você tiver milhares de execuções, o cancelamento poderá demorar um tempo significativo até ser concluído.

<a name="configuration"></a>
<a name="firewall-ip-configuration"></a>

## <a name="firewall-configuration-ip-addresses-and-service-tags"></a>Configuração do firewall: Endereços IP e marcas de serviço

Quando seu aplicativo lógico precisa se comunicar por meio de um firewall que limita o tráfego a endereços IP específicos, esse firewall precisa permitir *o acesso* para os endereços IP de [entrada](#inbound) e [saída](#outbound) usados pelo serviço de aplicativos lógicos ou tempo de execução na região do Azure onde o aplicativo lógico existe. *Todos* os aplicativos lógicos na mesma região usam os mesmos intervalos de endereço IP.

Por exemplo, para dar suporte a chamadas que os aplicativos lógicos na região oeste dos EUA enviam ou recebem por meio de gatilhos e ações internas, como o [gatilho http ou a ação](../connectors/connectors-native-http.md), o firewall precisa permitir o acesso a *todos* os endereços IP de entrada *e* endereços IP de saída do serviço de aplicativos lógicos que existem na região oeste dos EUA.

Se seu aplicativo lógico também usar [conectores gerenciados](../connectors/apis-list.md#managed-api-connectors), como o conector do Outlook do Office 365 ou o conector do SQL, ou usar [conectores personalizados](/connectors/custom-connectors/), o firewall também precisará permitir o acesso para *todos* os [endereços IP de saída do conector gerenciado](#outbound) na região do Azure do aplicativo lógico. Além disso, se você usar conectores personalizados que acessam recursos locais por meio do [recurso de gateway de dados local no Azure](logic-apps-gateway-connection.md), será necessário configurar a instalação do gateway para permitir o acesso aos *[endereços IP de saída](#outbound)dos conectores gerenciados* correspondentes.

Para obter mais informações sobre como definir as configurações de comunicação no gateway, consulte estes tópicos:

* [Ajustar configurações de comunicação para gateway de dados local](/data-integration/gateway/service-gateway-communication)
* [Definir configurações de proxy para o gateway de dados local](/data-integration/gateway/service-gateway-proxy)

<a name="ip-setup-considerations"></a>

### <a name="firewall-ip-configuration-considerations"></a>Considerações de configuração de IP de firewall

Antes de configurar o firewall com endereços IP, examine estas considerações:

* Se você estiver usando a [automatização de energia](/power-automate/getting-started), algumas ações, como **http** e **http + openapi**, vão diretamente por meio do serviço aplicativos lógicos do Azure e vêm dos endereços IP listados aqui. Para obter mais informações sobre os endereços IP usados pela automatização de energia, consulte [limites e configuração para automatizar a energia](/flow/limits-and-config#ip-address-configuration).

* Para o [Azure China 21vianet](/azure/china/), os endereços IP fixos ou reservados não estão disponíveis para [conectores personalizados](../logic-apps/custom-connector-overview.md) e para [conectores gerenciados](../connectors/apis-list.md#managed-api-connectors), como o armazenamento do Azure, SQL Server, Outlook do Office 365 e assim por diante.

* Se seus aplicativos lógicos forem executados em um [ambiente do serviço de integração (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md), certifique-se de [abrir essas portas também](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise).

* Para ajudá-lo a simplificar as regras de segurança que você deseja criar, você pode opcionalmente usar [marcas de serviço](../virtual-network/service-tags-overview.md) , em vez de especificar prefixos de endereço IP para cada região. Essas marcas funcionam nas regiões em que o serviço de Aplicativos Lógicos está disponível:

  * **LogicAppsManagement**: Representa os prefixos do endereço IP de entrada para o serviço de Aplicativos Lógicos.

  * **LogicApps**: Representa os prefixos do endereço IP de saída para o serviço de Aplicativos Lógicos.

  * **AzureConnectors**: representa os prefixos de endereço IP para conectores gerenciados que fazem retornos de chamada de webhook de entrada para o serviço de aplicativos lógicos e chamadas de saída para seus respectivos serviços, como o armazenamento do Azure ou hubs de eventos do Azure.

* Se seus aplicativos lógicos tiverem problemas para acessar contas de armazenamento do Azure que usam [firewalls e regras de firewall](../storage/common/storage-network-security.md), você terá [várias outras opções para habilitar o acesso](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

  Por exemplo, os aplicativos lógicos não podem acessar diretamente as contas de armazenamento que usam regras de firewall e existem na mesma região. No entanto, se você permitir [endereços IP de saída para os conectores gerenciados em sua região](../logic-apps/logic-apps-limits-and-config.md#outbound), seus aplicativos lógicos poderão acessar as contas de armazenamento que estão em uma região diferente, exceto quando usar os conectores de Armazenamento de Tabelas do Azure ou Armazenamento de Filas do Azure. Para acessar o Armazenamento de Tabelas ou o Armazenamento de Filas, você pode usar o gatilho e as ações HTTP. Para ver outras opções, consulte [Acessar contas de armazenamento por trás de firewalls](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

<a name="inbound"></a>

### <a name="inbound-ip-addresses"></a>Endereços IP de entrada

Esta seção lista os endereços IP de entrada para o serviço de Aplicativos Lógicos do Azure somente. Se você tem o Azure Governamental, consulte os [endereços IP de entrada do Azure Governamental](#azure-government-inbound).

> [!TIP]
> Para ajudar a reduzir a complexidade ao criar regras de segurança, você pode, opcionalmente, usar a [marca de serviço](../virtual-network/service-tags-overview.md), **LogicAppsManagement**, em vez de especificar os endereços IP dos Aplicativos Lógicos de entrada para cada região. Opcionalmente, você também pode usar a marca de serviço **AzureConnectors** para conectores gerenciados que fazem retornos de chamada de webhook de entrada para o serviço de aplicativos lógicos, em vez de especificar prefixos de endereço IP do conector gerenciado de entrada para cada região. Essas marcas funcionam nas regiões em que o serviço de aplicativos lógicos está disponível.
>
> Os conectores a seguir fazem retornos de chamada de webhook de entrada para o serviço de aplicativos lógicos:
>
> Adobe Creative Cloud, Adobe Sign, demonstração do Adobe Sign, visualização do Adobe Sign, estágio do Adobe Sign, Azure Sentinel, Business central, Calendly, Common Data Service, DocuSign, DocuSign demo, Dynamics 365 for Fin & Ops, LiveChat, Office 365 Outlook, Outlook.com, Parserr, SAP *, turnos para Microsoft Teams, projetos de trabalho em equipe, Typeform
>
> \***SAP**: o chamador de retorno depende se o ambiente de implantação é um Azure ou ISE multilocatário. No ambiente multilocatário, o gateway de dados local faz a chamada de volta para o serviço de aplicativos lógicos. Em um ISE, o conector SAP faz a chamada de volta para o serviço de aplicativos lógicos.

<a name="multi-tenant-inbound"></a>

#### <a name="multi-tenant-azure---inbound-ip-addresses"></a>Endereços IP de entrada do Azure com multilocatário

| Região com multilocatário | IP |
|---------------------|----|
| Leste da Austrália | 13.75.153.66, 104.210.89.222, 104.210.89.244, 52.187.231.161 |
| Sudeste da Austrália | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Sul do Brasil | 191.235.86.199, 191.235.95.229, 191.235.94.220, 191.234.166.198 |
| Canadá Central | 13.88.249.209, 52.233.30.218, 52.233.29.79, 40.85.241.105 |
| Leste do Canadá | 52.232.129.143, 52.229.125.57, 52.232.133.109, 40.86.202.42 |
| Índia Central | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| Centro dos EUA | 13.67.236.76, 40.77.111.254, 40.77.31.87, 104.43.243.39 |
| Leste da Ásia | 168.63.200.173, 13.75.89.159, 23.97.68.172, 40.83.98.194 |
| Leste dos EUA | 137.135.106.54, 40.117.99.79, 40.117.100.228, 137.116.126.165 |
| Leste dos EUA 2 | 40.84.25.234, 40.79.44.7, 40.84.59.136, 40.70.27.253 |
| França Central | 52.143.162.83, 20.188.33.169, 52.143.156.55, 52.143.158.203 |
| Sul da França | 52.136.131.145, 52.136.129.121, 52.136.130.89, 52.136.131.4 |
| Norte da Alemanha | 51.116.211.29, 51.116.208.132, 51.116.208.37, 51.116.208.64 |
| Centro-Oeste da Alemanha | 51.116.168.222, 51.116.171.209, 51.116.233.40, 51.116.175.0 |
| Leste do Japão | 13.71.146.140, 13.78.84.187, 13.78.62.130, 13.78.43.164 |
| Oeste do Japão | 40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.68.85 |
| Coreia Central | 52.231.14.182, 52.231.103.142, 52.231.39.29, 52.231.14.42 |
| Sul da Coreia | 52.231.166.168, 52.231.163.55, 52.231.163.150, 52.231.192.64 |
| Centro-Norte dos EUA | 168.62.249.81, 157.56.12.202, 65.52.211.164, 65.52.9.64 |
| Norte da Europa | 13.79.173.49, 52.169.218.253, 52.169.220.174, 40.112.90.39 |
| Leste da Noruega | 51.120.88.93, 51.13.66.86, 51.120.89.182, 51.120.88.77 |
| Norte da África do Sul | 102.133.228.4, 102.133.224.125, 102.133.226.199, 102.133.228.9 |
| Oeste da África do Sul | 102.133.72.190, 102.133.72.145, 102.133.72.184, 102.133.72.173 |
| Centro-Sul dos Estados Unidos | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Sul da Índia | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Sudeste Asiático | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Norte da Suíça | 51.103.128.52, 51.103.132.236, 51.103.134.138, 51.103.136.209 |
| Oeste da Suíça | 51.107.225.180, 51.107.225.167, 51.107.225.163, 51.107.239.66 |
| EAU Central | 20.45.75.193, 20.45.64.29, 20.45.64.87, 20.45.71.213 |
| Norte dos EAU | 20.46.42.220, 40.123.224.227, 40.123.224.143, 20.46.46.173 |
| Sul do Reino Unido | 51.140.79.109, 51.140.78.71, 51.140.84.39, 51.140.155.81 |
| Oeste do Reino Unido | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| Centro-Oeste dos EUA | 52.161.26.172, 52.161.8.128, 52.161.19.82, 13.78.137.247 |
| Europa Ocidental | 13.95.155.53, 52.174.54.218, 52.174.49.6 |
| Oeste da Índia | 104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.157.237 |
| Oeste dos EUA | 52.160.90.237, 138.91.188.137, 13.91.252.184, 157.56.160.212 |
| Oeste dos EUA 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67, 13.66.128.68 |
|||

<a name="azure-government-inbound"></a>

#### <a name="azure-government---inbound-ip-addresses"></a>Endereços IP de entrada do Azure Governamental

| Região do Azure Governamental | IP |
|-------------------------|----|
| Governo dos EUA do Arizona | 52.244.67.164, 52.244.67.64, 52.244.66.82 |
| Governo dos EUA do Texas | 52.238.119.104, 52.238.112.96, 52.238.119.145 |
| Gov. dos EUA – Virgínia | 52.227.159.157, 52.227.152.90, 23.97.4.36 |
| DoD Central dos EUA | 52.182.49.204, 52.182.52.106 |
|||

<a name="outbound"></a>

### <a name="outbound-ip-addresses"></a>Endereços IP de saída

Esta seção lista os endereços IP de saída para o serviço de Aplicativos Lógicos do Azure e conectores gerenciados. Se você tem o Azure Governamental, consulte os [endereços IP de saída do Azure Governamental](#azure-government-outbound).

> [!TIP]
> Para ajudar a reduzir a complexidade ao criar regras de segurança, você pode, opcionalmente, usar a [marca de serviço](../virtual-network/service-tags-overview.md), **LogicApps**, em vez de especificar os endereços IP dos Aplicativos Lógicos de saída para cada região. Opcionalmente, você também pode usar a marca de serviço **AzureConnectors** para conectores gerenciados que fazem chamadas de saída para seus respectivos serviços, como o armazenamento do Azure ou hubs de eventos do Azure, em vez de especificar prefixos de endereço IP do conector gerenciado de saída para cada região. Essas marcas funcionam nas regiões em que o serviço de aplicativos lógicos está disponível.

<a name="multi-tenant-outbound"></a>

#### <a name="multi-tenant-azure---outbound-ip-addresses"></a>Endereços IP de saída do Azure com multilocatário

| Região com multilocatário | IP de Aplicativos Lógicos | IP de conectores gerenciados |
|---------------------|---------------|-----------------------|
| Leste da Austrália | 13.75.149.4, 104.210.91.55, 104.210.90.241, 52.187.227.245, 52.187.226.96, 52.187.231.184, 52.187.229.130, 52.187.226.139 | 52.237.214.72, 13.72.243.10, 13.70.72.192 - 13.70.72.207, 13.70.78.224 - 13.70.78.255 |
| Sudeste da Austrália | 13.73.114.207, 13.77.3.139, 13.70.159.205, 52.189.222.77, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75 | 52.255.48.202, 13.70.136.174, 13.77.50.240 - 13.77.50.255, 13.77.55.160 - 13.77.55.191 |
| Sul do Brasil | 191.235.82.221, 191.235.91.7, 191.234.182.26, 191.237.255.116, 191.234.161.168, 191.234.162.178, 191.234.161.28, 191.234.162.131 | 191.232.191.157, 104.41.59.51, 191.233.203.192 - 191.233.203.207, 191.233.207.160 - 191.233.207.191 |
| Canadá Central | 52.233.29.92, 52.228.39.244, 40.85.250.135, 40.85.250.212, 13.71.186.1, 40.85.252.47, 13.71.184.150 | 52.237.32.212, 52.237.24.126, 13.71.170.208 - 13.71.170.223, 13.71.175.160 - 13.71.175.191 |
| Leste do Canadá | 52.232.128.155, 52.229.120.45, 52.229.126.25, 40.86.203.228, 40.86.228.93, 40.86.216.241, 40.86.226.149, 40.86.217.241 | 52.242.30.112, 52.242.35.152, 40.69.106.240 - 40.69.106.255, 40.69.111.0 - 40.69.111.31 |
| Índia Central | 52.172.154.168, 52.172.186.159, 52.172.185.79, 104.211.101.108, 104.211.102.62, 104.211.90.169, 104.211.90.162, 104.211.74.145 | 52.172.212.129, 52.172.211.12, 20.43.123.0 - 20.43.123.31, 104.211.81.192 - 104.211.81.207 |
| Centro dos EUA | 13.67.236.125, 104.208.25.27, 40.122.170.198, 40.113.218.230, 23.100.86.139, 23.100.87.24, 23.100.87.56, 23.100.82.16 | 52.173.241.27, 52.173.245.164, 13.89.171.80 - 13.89.171.95, 13.89.178.64 - 13.89.178.95 |
| Leste da Ásia | 13.75.94.173, 40.83.127.19, 52.175.33.254, 40.83.73.39, 65.52.175.34, 40.83.77.208, 40.83.100.69, 40.83.75.165 | 13.75.110.131, 52.175.23.169, 13.75.36.64 - 13.75.36.79, 104.214.164.0 - 104.214.164.31 |
| Leste dos EUA | 13.92.98.111, 40.121.91.41, 40.114.82.191, 23.101.139.153, 23.100.29.190, 23.101.136.201, 104.45.153.81, 23.101.132.208 | 40.71.249.139, 40.71.249.205, 40.114.40.132, 40.71.11.80 - 40.71.11.95, 40.71.15.160 - 40.71.15.191 |
| Leste dos EUA 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174, 104.208.140.40, 40.70.131.151, 40.70.29.214, 40.70.26.154, 40.70.27.236 | 52.225.129.144, 52.232.188.154, 104.209.247.23, 40.70.146.208 - 40.70.146.223, 40.70.151.96 - 40.70.151.127 |
| França Central | 52.143.164.80, 52.143.164.15, 40.89.186.30, 20.188.39.105, 40.89.191.161, 40.89.188.169, 40.89.186.28, 40.89.190.104 | 40.89.186.239, 40.89.135.2, 40.79.130.208 - 40.79.130.223, 40.79.148.96 - 40.79.148.127 |
| Sul da França | 52.136.132.40, 52.136.129.89, 52.136.131.155, 52.136.133.62, 52.136.139.225, 52.136.130.144, 52.136.140.226, 52.136.129.51 | 52.136.142.154, 52.136.133.184, 40.79.178.240 - 40.79.178.255, 40.79.180.224 - 40.79.180.255 |
| Norte da Alemanha | 51.116.211.168, 51.116.208.165, 51.116.208.175, 51.116.208.192, 51.116.208.200, 51.116.208.222, 51.116.208.217, 51.116.208.51 | 51.116.60.192, 51.116.211.212, 51.116.59.16 - 51.116.59.31, 51.116.60.192 - 51.116.60.223 |
| Centro-Oeste da Alemanha | 51.116.233.35, 51.116.171.49, 51.116.233.33, 51.116.233.22, 51.116.168.104, 51.116.175.17, 51.116.233.87, 51.116.175.51 | 51.116.158.97, 51.116.236.78, 51.116.155.80 - 51.116.155.95, 51.116.158.96 - 51.116.158.127 |
| Leste do Japão | 13.71.158.3, 13.73.4.207, 13.71.158.120, 13.78.18.168, 13.78.35.229, 13.78.42.223, 13.78.21.155, 13.78.20.232 | 13.73.21.230, 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.79.189.64 - 40.79.189.95 |
| Oeste do Japão | 40.74.140.4, 104.214.137.243, 138.91.26.45, 40.74.64.207, 40.74.76.213, 40.74.77.205, 40.74.74.21, 40.74.68.85 | 104.215.27.24, 104.215.61.248, 40.74.100.224 - 40.74.100.239, 40.80.180.64 - 40.80.180.95 |
| Coreia Central | 52.231.14.11, 52.231.14.219, 52.231.15.6, 52.231.10.111, 52.231.14.223, 52.231.77.107, 52.231.8.175, 52.231.9.39 | 52.141.1.104, 52.141.36.214, 20.44.29.64 - 20.44.29.95, 52.231.18.208 - 52.231.18.223 |
| Sul da Coreia | 52.231.204.74, 52.231.188.115, 52.231.189.221, 52.231.203.118, 52.231.166.28, 52.231.153.89, 52.231.155.206, 52.231.164.23 | 52.231.201.173, 52.231.163.10, 52.231.147.0 - 52.231.147.15, 52.231.148.224 - 52.231.148.255 |
| Centro-Norte dos EUA | 168.62.248.37, 157.55.210.61, 157.55.212.238, 52.162.208.216, 52.162.213.231, 65.52.10.183, 65.52.9.96, 65.52.8.225 | 52.162.126.4, 52.162.242.161, 52.162.107.160 - 52.162.107.175, 52.162.111.192 - 52.162.111.223 |
| Norte da Europa | 40.113.12.95, 52.178.165.215, 52.178.166.21, 40.112.92.104, 40.112.95.216, 40.113.4.18, 40.113.3.202, 40.113.1.181 | 52.169.28.181, 52.178.150.68, 94.245.91.93, 13.69.227.208 - 13.69.227.223, 13.69.231.192 - 13.69.231.223 |
| Leste da Noruega | 51.120.88.52, 51.120.88.51, 51.13.65.206, 51.13.66.248, 51.13.65.90, 51.13.65.63, 51.13.68.140, 51.120.91.248 | 51.120.100.192, 51.120.92.27, 51.120.98.224 - 51.120.98.239, 51.120.100.192 - 51.120.100.223 |
| Norte da África do Sul | 102.133.231.188, 102.133.231.117, 102.133.230.4, 102.133.227.103, 102.133.228.6, 102.133.230.82, 102.133.231.9, 102.133.231.51 | 102.133.168.167, 40.127.2.94, 102.133.155.0 - 102.133.155.15, 102.133.253.0 - 102.133.253.31 |
| Oeste da África do Sul | 102.133.72.98, 102.133.72.113, 102.133.75.169, 102.133.72.179, 102.133.72.37, 102.133.72.183, 102.133.72.132, 102.133.75.191 | 102.133.72.85, 102.133.75.194, 102.37.64.0 - 102.37.64.31, 102.133.27.0 - 102.133.27.15 |
| Centro-Sul dos Estados Unidos | 104.210.144.48, 13.65.82.17, 13.66.52.232, 23.100.124.84, 70.37.54.122, 70.37.50.6, 23.100.127.172, 23.101.183.225 | 52.171.130.92, 13.65.86.57, 13.73.244.224 - 13.73.244.255, 104.214.19.48 - 104.214.19.63 |
| Sul da Índia | 52.172.50.24, 52.172.55.231, 52.172.52.0, 104.211.229.115, 104.211.230.129, 104.211.230.126, 104.211.231.39, 104.211.227.229 | 13.71.127.26, 13.71.125.22, 20.192.184.32 - 20.192.184.63, 40.78.194.240 - 40.78.194.255 |
| Sudeste Asiático | 13.76.133.155, 52.163.228.93, 52.163.230.166, 13.76.4.194, 13.67.110.109, 13.67.91.135, 13.76.5.96, 13.67.107.128 | 52.187.115.69, 52.187.68.19, 13.67.8.240 - 13.67.8.255, 13.67.15.32 - 13.67.15.63 |
| Norte da Suíça | 51.103.137.79, 51.103.135.51, 51.103.139.122, 51.103.134.69, 51.103.138.96, 51.103.138.28, 51.103.136.37, 51.103.136.210 | 51.103.142.22, 51.107.86.217, 51.107.59.16 - 51.107.59.31, 51.107.60.224 - 51.107.60.255 |
| Oeste da Suíça | 51.107.239.66, 51.107.231.86, 51.107.239.112, 51.107.239.123, 51.107.225.190, 51.107.225.179, 51.107.225.186, 51.107.225.151, 51.107.239.83 | 51.107.156.224, 51.107.231.190, 51.107.155.16 - 51.107.155.31, 51.107.156.224 - 51.107.156.255 |
| EAU Central | 20.45.75.200, 20.45.72.72, 20.45.75.236, 20.45.79.239, 20.45.67.170, 20.45.72.54, 20.45.67.134, 20.45.67.135 | 20.45.67.45, 20.45.67.28, 20.37.74.192 - 20.37.74.207, 40.120.8.0 - 40.120.8.31 |
| Norte dos EAU | 40.123.230.45, 40.123.231.179, 40.123.231.186, 40.119.166.152, 40.123.228.182, 40.123.217.165, 40.123.216.73, 40.123.212.104 | 65.52.250.208, 40.123.224.120, 40.120.64.64 - 40.120.64.95, 65.52.250.208 - 65.52.250.223 |
| Sul do Reino Unido | 51.140.74.14, 51.140.73.85, 51.140.78.44, 51.140.137.190, 51.140.153.135, 51.140.28.225, 51.140.142.28, 51.140.158.24 | 51.140.74.150, 51.140.80.51, 51.140.61.124, 51.105.77.96 - 51.105.77.127, 51.140.148.0 - 51.140.148.15 |
| Oeste do Reino Unido | 51.141.54.185, 51.141.45.238, 51.141.47.136, 51.141.114.77, 51.141.112.112, 51.141.113.36, 51.141.118.119, 51.141.119.63 | 51.141.52.185, 51.141.47.105, 51.141.124.13, 51.140.211.0 - 51.140.211.15, 51.140.212.224 - 51.140.212.255 |
| Centro-Oeste dos EUA | 52.161.27.190, 52.161.18.218, 52.161.9.108, 13.78.151.161, 13.78.137.179, 13.78.148.140, 13.78.129.20, 13.78.141.75 | 52.161.101.204, 52.161.102.22, 13.78.132.82, 13.71.195.32 - 13.71.195.47, 13.71.199.192 - 13.71.199.223 |
| Europa Ocidental | 40.68.222.65, 40.68.209.23, 13.95.147.65, 23.97.218.130, 51.144.182.201, 23.97.211.179, 104.45.9.52, 23.97.210.126 | 52.166.78.89, 52.174.88.118, 40.91.208.65, 13.69.64.208 - 13.69.64.223, 13.69.71.192 - 13.69.71.223 |
| Oeste da Índia | 104.211.164.80, 104.211.162.205, 104.211.164.136, 104.211.158.127, 104.211.156.153, 104.211.158.123, 104.211.154.59, 104.211.154.7 | 104.211.189.124, 104.211.189.218, 20.38.128.224 - 20.38.128.255, 104.211.146.224 - 104.211.146.239 |
| Oeste dos EUA | 52.160.92.112, 40.118.244.241, 40.118.241.243, 157.56.162.53, 157.56.167.147, 104.42.49.145, 40.83.164.80, 104.42.38.32 | 13.93.148.62, 104.42.122.49, 40.112.195.87, 13.86.223.32 - 13.86.223.63, 40.112.243.160 - 40.112.243.175 |
| Oeste dos EUA 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132, 13.66.210.167, 13.66.201.169, 13.77.149.159, 52.175.198.132, 13.66.246.219 | 52.191.164.250, 52.183.78.157, 13.66.140.128 - 13.66.140.143, 13.66.145.96 - 13.66.145.127 |
||||

<a name="azure-government-outbound"></a>

#### <a name="azure-government---outbound-ip-addresses"></a>Endereços IP de saída do Azure Governamental

| Região | IP de Aplicativos Lógicos | IP de conectores gerenciados |
|--------|---------------|-----------------------|
| DoD Central dos EUA | 52.182.48.215, 52.182.92.143 | 52.127.58.160 - 52.127.58.175, 52.182.54.8, 52.182.48.136, 52.127.61.192 - 52.127.61.223 |
| Governo dos EUA do Arizona | 52.244.67.143, 52.244.65.66, 52.244.65.190 | 52.127.2.160 - 52.127.2.175, 52.244.69.0, 52.244.64.91, 52.127.5.224 - 52.127.5.255 |
| Governo dos EUA do Texas | 52.238.114.217, 52.238.115.245, 52.238.117.119 | 52.127.34.160 - 52.127.34.175, 40.112.40.25, 52.238.161.225, 20.140.137.128 - 20.140.137.159 |
| Gov. dos EUA – Virgínia | 13.72.54.205, 52.227.138.30, 52.227.152.44 | 52.127.42.128 - 52.127.42.143, 52.227.143.61, 52.227.162.91 |
||||

## <a name="next-steps"></a>Próximas etapas

* Saiba como [criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* Saiba mais sobre [exemplos e cenários comuns](../logic-apps/logic-apps-examples-and-scenarios.md)
