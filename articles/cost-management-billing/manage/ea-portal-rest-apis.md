---
title: APIs REST do Azure Enterprise
description: Este artigo descreve as APIs REST a serem usadas com o Registro Enterprise do Azure.
author: bandersmsft
ms.author: banders
ms.date: 01/21/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 1fdf64053a55eb33d80ed461c231e8c6dd84d63b
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98677724"
---
# <a name="azure-enterprise-rest-apis"></a>APIs REST do Azure Enterprise

Este artigo descreve as APIs REST a serem usadas com o Registro Enterprise do Azure. Ele também explica como resolver problemas comuns com APIs REST.

## <a name="consumption-and-usage-apis"></a>APIs de consumo e de uso

Os clientes do Microsoft Azure Enterprise podem obter informações de uso e cobrança por meio de APIs REST. O proprietário da função (administrador corporativo, administrador do departamento, proprietário da conta) precisa habilitar o acesso à API, gerando uma chave no Portal do EA do Azure. Então, qualquer pessoa que tenha o número de registro e a chave poderá acessar os dados por meio da API.

### <a name="available-apis"></a>APIs disponíveis

**Saldo e Resumo –** a [API Saldo e Resumo](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) fornece um resumo mensal de informações sobre saldos, novas compras, encargos de serviço do Azure Marketplace, ajustes e encargos de excedente. Para obter mais informações, confira [APIs de Relatórios para clientes Enterprise – Saldo e Resumo](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary).

**Detalhes de Uso –** a [API Detalhes de Uso](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) oferece um detalhamento diário das quantidades consumidas e de encargos estimados por um registro. O resultado também inclui informações sobre instâncias, medidores e departamentos. A API pode ser consultada por período de cobrança ou especificando-se uma data de início e de término. Para obter mais informações, confira [APIs de Relatórios para clientes Enterprise – detalhes de uso](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail).

**Encargo de Armazenamento do Marketplace**: a [API Encargo de Armazenamento do Marketplace](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) retorna o detalhamento dos encargos do Marketplace com base no uso por dia para as datas de início e término ou o período de cobrança especificados. Para obter mais informações, confira [APIs de Relatórios para clientes Enterprise – Custos de Armazenamento do Marketplace](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge).

**Tabela de preços –** a [API Tabela de Preços](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) fornece a taxa aplicável de cada medidor para um registro e um período de cobrança específicos. Para obter mais informações, confira [APIs de Relatórios para clientes Enterprise – Tabela de Preços](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet).

**Períodos de Cobrança –** a [API Períodos de Cobrança](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) retorna uma lista de períodos de cobrança que têm dados de consumo para um determinado registro em ordem cronológica inversa. Cada período contém uma propriedade que aponta para a rota da API dos quatro conjuntos de dados: BalanceSummary, UsageDetails, Encargos do Marketplace e PriceSheet. Para obter mais informações, confira [APIs de Relatórios para clientes Enterprise – períodos de cobrança](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods).

### <a name="enable-api-data-access"></a>Habilitar acesso a dados da API

Proprietários da função podem executar as etapas a seguir no Portal do EA do Azure. Navegar até **Relatórios** > **Uso de Download** > **Chave de Acesso da API**. Em seguida, eles podem:

- Gerar chaves de acesso primárias e secundárias.
- Desabilitar chaves de acesso.
- Exibir as datas inicial e final de chaves de acesso.

### <a name="generate-or-retrieve-the-api-key"></a>Gerar ou recuperar a chave de API

1. Entrar como um administrador corporativo.
2. Clique em **Relatórios** na janela de navegação à esquerda e clique na guia **Uso de Download**.
3. Clique em **Chave de Acesso da API**.
4. Em **Chaves de Acesso de Registro**, selecione o símbolo de gerar chave para gerar uma chave primária ou secundária.
5. Selecione **Expandir Chave** para exibir toda a chave de acesso da API gerada.
6. Selecione **Copiar** para obter a chave de acesso da API para uso imediato.

![Exemplo mostrando a página de chave de acesso da API](./media/ea-portal-rest-apis/ea-create-generate-or-retrieve-the-api-key.png)

Se você quiser fornecer as chaves de acesso à API para pessoas que não são administradores corporativos em seu registro, execute as seguintes etapas:

1. Na janela de navegação à esquerda, clique em **Gerenciar**.
2. Clique no símbolo de lápis ao lado de **Exibir encargos de DA** (exibir encargos de administrador do departamento).
3. Selecione **Habilitar** e, em seguida, clique em **Salvar**.
4. Clique no símbolo de lápis ao lado de **Exibir encargos de AO** (exibir encargos de proprietário da conta).
5. Selecione **Habilitar** e, em seguida, clique em **Salvar**.

![Exemplo mostrando exibição de encargos de DA e AO habilitada](./media/ea-portal-rest-apis/create-ea-generate-or-retrieve-api-key-enable-ao-do-view.png) As etapas anteriores fornecem a detentores da chave de acesso à API acesso a informações sobre preços e custos em relatórios de uso.

### <a name="pass-keys-in-the-api"></a>Passar chaves na API

Passe a chave de API para cada chamada para autenticação e autorização. Passe a seguinte propriedade para cabeçalhos HTTP:

| Chave de cabeçalho da solicitação | Valor |
| --- | --- |
| Autorização | Especifique o valor neste formato: **portador {API\_KEY}**
Exemplo: portador \&lt;APIKey\&gt; |

### <a name="swagger"></a>Swagger

Um ponto de extremidade do Swagger está disponível em [APIs do Enterprise Reporting v3](https://consumption.azure.com/swagger/ui/index) para as APIs a seguir. O Swagger ajuda a inspecionar a API. Use o Swagger para gerar SDKs de cliente usando o [AutoRest](https://github.com/Azure/AutoRest) ou o [Swagger CodeGen](https://swagger.io/swagger-codegen/). Os dados disponíveis após 1º de maio de 2014 estão disponíveis por meio da API.

### <a name="api-response-codes"></a>Códigos de resposta da API

Ao usar uma API, códigos de status de resposta são mostrados. Eles são descritos na tabela a seguir.

| Código de status de resposta | Mensagem | Descrição |
| --- | --- | --- |
| 200 | OK | Nenhum erro |
| 401 | Não Autorizado | Chave de API não encontrada, inválida, expirada, etc. |
| 404 | Indisponível | Ponto de extremidade de relatório não encontrado |
| 400 | Solicitação incorreta | Parâmetros inválidos – intervalos de datas, números de EA, etc. |
| 500 | Erro de servidor | Erro inesperado ao processar a solicitação |

### <a name="usage-and-billing-data-update-frequency"></a>Frequência de atualização de dados de cobrança e de uso

Os arquivos de dados de uso e de cobrança são atualizados a cada 24 horas para o mês de cobrança atual. No entanto, a latência de dados pode ocorrer por até três dias. Por exemplo, se o uso tiver ocorrido na segunda-feira, os dados poderão não aparecer no arquivo de dados até quinta-feira.

### <a name="azure-service-catalog"></a>Catálogo de serviços do Azure

Todos os serviços do Azure são postados em um catálogo em formato CSV no blog de armazenamento do Azure. O catálogo será útil se você precisar criar um catálogo organizado de todos os serviços do Azure para seu sistema. O catálogo atual está em [https://azurecatalog.blob.core.windows.net/catalog/AzureCatalog.csv](https://azurecatalog.blob.core.windows.net/catalog/AzureCatalog.csv).

### <a name="csv-data-file-details"></a>Detalhes de arquivos de dados CSV

As informações a seguir descrevem as propriedades de relatórios de API.

#### <a name="usage-summary"></a>Resumo de uso

O formato JSON é gerado do relatório CSV. Como resultado, o formato é o mesmo que o formato CSV de resumo. O nome da coluna é usado, de modo que você deve desserializá-lo em uma tabela de dados ao consumir os dados de resumo JSON.

| Nome da coluna CSV | Nome da coluna JSON | Coluna nova JSON | Comentário |
| --- | --- | --- | --- |
| AccountOwnerId | AccountOwnerLiveId | AccountOwnerLiveId |   |
| Nome da Conta | AccountName | AccountName |   |
| ServiceAdministratorId | ServiceAdministratorLiveId | ServiceAdministratorLiveId |   |
| SubscriptionId | SubscriptionId | SubscriptionId |   |
| SubscriptionGuid | MOCPSubscriptionGuid | SubscriptionGuid |   |
| Nome da assinatura | SubscriptionName | SubscriptionName |   |
| Data | Data | Data | Mostra a data em que o relatório do catálogo de serviços foi executado. O formato é uma cadeia de caracteres de datas sem um carimbo de data/hora. |
| Mês | Mês | Mês |   |
| Dia | Dia | Dia |   |
| Ano | Ano | Ano |   |
| Product | BillableItemName | Product |   |
| ID de Medidor | ResourceGUID | MeterId |   |
| Categoria de medidor | Serviço | MeterCategory | É útil para ajudar a localizar serviços. Relevante para serviços que têm vários ServiceType. Por exemplo, máquinas virtuais. |
| Subcategoria de Medidor | ServiceType | MeterSubCategory | Fornece um segundo nível de detalhes para um serviço. Por exemplo, VM A1 (não Windows).  |
| Região do Medidor | ServiceRegion | MeterRegion | O terceiro nível de detalhe necessário para um serviço. Útil para localizar o contexto de região do ResourceGUID. |
| Nome do Medidor | ServiceResource | MeterName | O nome do serviço. |
| Quantidade Consumida | ResourceQtyConsumed | ConsumedQuantity |   |
| ResourceRate | ResourceRate | ResourceRate |   |
| ExtendedCost | ExtendedCost | ExtendedCost |   |
| Local do Recurso | ServiceSubRegion | ResourceLocation |   |
| Serviço Consumido | ServiceInfo | ConsumedService |   |
| ID da Instância | Componente | InstanceId |   |
| ServiceInfo1 | ServiceInfo1 | ServiceInfo1 |   |
| ServiceInfo2 | ServiceInfo2 | ServiceInfo2 |   |
| AdditionalInfo | AdditionalInfo | AdditionalInfo |   |
| Marcas | Marcas | Marcas |   |
| Identificador de Serviço de Repositório   | OrderNumber | StoreServiceIdentifier   |   |
| Nome do Departamento | DepartmentName | DepartmentName |   |
| Centro de Custo | CostCenter | CostCenter |   |
| Unidade de medida | UnitOfMeasure | UnitOfMeasure | Valores de exemplo: Horas, GB, eventos, pushes, unidade, horas de unidade, MB, unidades diárias |
| ResourceGroup | ResourceGroup | ResourceGroup |   |

#### <a name="azure-marketplace-report"></a>Relatório do Azure Marketplace

| Nome da coluna CSV | Nome da coluna JSON | Coluna nova JSON |
| --- | --- | --- |
| AccountOwnerId | AccountOwnerId | AccountOwnerId |
| Nome da Conta | AccountName | AccountName |
| SubscriptionId | SubscriptionId | SubscriptionId |
| SubscriptionGuid | SubscriptionGuid | SubscriptionGuid |
| Nome da assinatura | SubscriptionName |  SubscriptionName |
| Data | BillingCycle |  Data (somente cadeia de caracteres de data. Sem carimbo de data/hora)
| Mês | Mês |  Mês |
| Dia | Dia |  Dia |
| Ano | Ano |  Ano |
| ID de Medidor | MeterResourceId |  MeterId |
| Nome do editor | PublisherFriendlyName |  PublisherName |
| Nome da oferta | OfferFriendlyName |  OfferName |
| Nome do Plano | PlanFriendlyName |  PlanName |
| Quantidade Consumida | BilledQty |  ConsumedQuantity |
| ResourceRate | ResourceRate | ResourceRate |
| ExtendedCost | ExtendedCost | ExtendedCost |
| Unidade de medida | UnitOfMeasure | UnitOfMeasure |
| ID da Instância | InstanceId | InstanceId |
| Informações adicionais | AdditionalInfo | AdditionalInfo |
| Marcas | Marcas | Marcas |
| Número do pedido | OrderNumber | OrderNumber |
| Nome do Departamento | DepartmentNames | DepartmentName |
| Centro de Custo | CostCenters |  CostCenter |
| Grupo de recursos | ResourceGroup |  ResourceGroup |

#### <a name="price-sheet"></a>Tabela de preços

| Nome da coluna CSV | Nome da coluna JSON | Comentário |
| --- | --- | --- |
| Serviço | Serviço |  Sem alteração no preço |
| Unidade de medida | UnitOfMeasure |   |
| Número da peça excedente | ConsumptionPartNumber |   |
| Preço unitário excedente | ConsumptionPrice |   |
| Código de moeda | CurrencyCode |     |

### <a name="common-api-issues"></a>Problemas comuns de API

Ao usar as APIs REST do Azure Enterprise, você pode encontrar qualquer um dos problemas comuns a seguir.

Você pode tentar usar uma chave de API que não tem o tipo de autorização correto. As chaves de API são geradas por:

- Administrador corporativo
- DA (administrador do departamento)
- AO (proprietário da conta)

Uma chave gerada pelo administrador de EA fornece acesso a todas as informações para esse registro. Um administrador de EA somente leitura não pode gerar uma chave de API.

Uma chave gerada por um DA ou AO não fornece acesso a informações de saldo, cobrança e tabela de preços.

Uma chave de API expira a cada seis meses. Se ela tiver expirado, você precisará gerá-la novamente.

Se você receber um erro de tempo limite, poderá resolvê-lo aumentando o limite de tempo limite.

Você pode receber um erro de expiração 401 (não autorizado). O erro normalmente é causado por uma chave expirada. Se a chave estiver expirada, você poderá regenerá-la.

Você pode receber erros 400 e 404 (não disponível) retornados de uma chamada à API quando não há dados atuais disponíveis para o intervalo de datas selecionado. Por exemplo, esse erro pode ocorrer porque uma transferência de registro foi iniciada recentemente. Os dados de uma data específica e posteriores a ela agora residem em um novo registro. Caso contrário, o erro poderá ocorrer se você estiver usando um novo número de registro para recuperar informações residentes em um registro antigo.

## <a name="next-steps"></a>Próximas etapas

- Os administradores do Portal do EA do Azure devem ler [Administração do Portal do EA do Azure](ea-portal-administration.md) para aprender sobre tarefas administrativas comuns.
- Se você precisar de ajuda para solucionar problemas do Portal do EA do Azure, confira [Solucionar problemas de acesso ao Portal do EA do Azure](ea-portal-troubleshoot.md).