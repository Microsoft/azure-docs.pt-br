---
title: Gerenciar custos e uso do AWS no gerenciamento de custos do Azure
description: Este artigo ajuda você a entender como usar a análise de custos e os orçamentos no gerenciamento de custos para gerenciar os custos e o uso do AWS.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 05e2375998b3bce4320b2d66ab7fce44cd911dcc
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479139"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>Gerenciar os custos e o uso do AWS no Azure

Depois de configurar e configurar a integração de relatório de uso e custo do AWS para o gerenciamento de custos do Azure, você estará pronto para começar a gerenciar os custos e o uso do AWS. Este artigo ajuda você a entender como usar a análise de custos e os orçamentos no gerenciamento de custos para gerenciar os custos e o uso do AWS.

Se você ainda não tiver configurado a integração, consulte [configurar e configurar a integração do relatório de uso do AWS](aws-integration-set-up-configure.md).

_Antes de começar_: Se você não estiver familiarizado com a análise de custo, consulte o início rápido [explorar e analisar custos com a análise de custo](quick-acm-cost-analysis.md) . E, se você não estiver familiarizado com orçamentos no Azure, consulte o tutorial [criar e gerenciar orçamentos do Azure](tutorial-acm-create-budgets.md) .

## <a name="view-aws-costs-in-cost-analysis"></a>Exibir custos de AWS na análise de custo

Os custos de AWS estão disponíveis na análise de custo nos seguintes escopos:

- AWS contas vinculadas em um grupo de gerenciamento
- Custos da conta vinculada do AWS
- Custos de conta consolidada do AWS

As próximas seções descrevem como usar os escopos para que você veja dados de custo e de uso para cada um.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>Exibir contas vinculadas do AWS em um grupo de gerenciamento

A exibição de custos usando o escopo do grupo de gerenciamento é a única maneira de ver os custos agregados provenientes de assinaturas e contas vinculadas diferentes. O uso de um grupo de gerenciamento fornece uma exibição entre nuvens.

Em análise de custo, abra o seletor de escopo e selecione o grupo de gerenciamento que contém suas contas vinculadas do AWS. Aqui está um exemplo de imagem no portal do Azure:

![Exemplo da exibição selecionar escopo](./media/aws-integration-manage/select-scope01.png)



Veja um exemplo que mostra o custo do grupo de gerenciamento na análise de custo, agrupado por provedor (Azure e AWS).

![Exemplo mostrando os custos do Azure e do AWS para um trimestre na análise de custo](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>Exibir os custos da conta vinculada do AWS

Para exibir os custos da conta do AWS link, abra o seletor de escopo e selecione a conta vinculada AWS. Observe que as contas vinculadas estão associadas a um grupo de gerenciamento, conforme definido no conector do AWS.

Aqui está um exemplo que mostra a seleção de um escopo de conta vinculada AWS.

![Exemplo da exibição selecionar escopo](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>Exibir os custos da conta consolidada do AWS

Para exibir os custos da conta consolidada do AWS, abra o seletor de escopo e selecione a conta consolidada AWS. Aqui está um exemplo que mostra a seleção de um escopo de conta consolidada AWS.

![Exemplo da exibição selecionar escopo](./media/aws-integration-manage/select-scope03.png)



Esse escopo fornece uma exibição agregada de todas as contas vinculadas do AWS associadas à conta consolidada do AWS. Veja um exemplo que mostra os custos de uma conta consolidada AWS, agrupada por nome de serviço.

![Exemplo mostrando os custos consolidados do AWS na análise de custo](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>Dimensões disponíveis para filtragem e agrupamento

A tabela a seguir descreve as dimensões disponíveis para agrupar e filtrar por na análise de custo.

| Dimensão | Cabeçalho do Amazon CUR | Escopos | Comentários |
| --- | --- | --- | --- |
| Zona de disponibilidade | lineitem/AvailabilityZone | Todas |   |
| Location | produto/região | Todas |   |
| Medidor |   | Todas |   |
| Categoria do medidor | lineItem/ProductCode | Todas |   |
| Subcategoria do medidor | LineItem/UsageType | Todas |   |
| Operação | lineItem/operação | Todas |   |
| Recurso | lineItem/ResourceId | Todas |   |
| Tipo de recurso | produto/InstanceType | Todas | Se Product/InstanceType for NULL, lineItem/UsageType será usado. |
| ResourceGuid | N/D | Todas | GUID do medidor do Azure. |
| Nome do serviço | produto/NomeDoProduto | Todas | Se Product/ProductName for NULL, lineItem/ProductCode será usado. |
| Camada de serviço |   |   |   |
| ID da assinatura | lineItem/UsageAccountId | Conta consolidada e grupo de gerenciamento |   |
| Nome de assinatura | N/D | Conta consolidada e grupo de gerenciamento | Os nomes de conta são coletados usando a API da organização AWS. |
| Marca | resourceTags/\* | Todas | O prefixo _User:_ é removido das marcas definidas pelo usuário para permitir marcas de nuvem cruzada. O prefixo _AWS:_ permanece intacto. |
| ID da conta de cobrança | bill/PayerAccountId | Grupo de gerenciamento |   |
| Nome da conta de cobrança | N/D | Grupo de gerenciamento | Os nomes de conta são coletados usando a API da organização AWS. |
| Provedor | N/D | Grupo de gerenciamento | AWS ou o Azure. |

## <a name="set-budgets-on-aws-scopes"></a>Definir orçamentos em escopos AWS

Use orçamentos para gerenciar de forma proativa os custos e gerar responsabilidade em sua organização. Os orçamentos são definidos na conta consolidada AWS e nos escopos de conta vinculada AWS. Veja um exemplo de orçamentos para uma conta consolidada do AWS mostrada no gerenciamento de custos:

![Exemplo mostrando orçamentos para uma conta consolidada AWS](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>Processo de coleta de dados AWS

Depois de configurar o conector do AWS, a coleta de dados e os processos de descoberta são iniciados. Pode levar algumas horas para coletar todos os dados de uso. A duração depende de:

- O tempo necessário para processar os arquivos de CUR que estão no Bucket AWS S3.
- O tempo necessário para criar a conta consolidada AWS e escopos de conta vinculada AWS.
- A hora e a frequência de AWS estão gravando os arquivos de relatório de custo e uso no Bucket S3

## <a name="aws-integration-pricing"></a>Preços de integração do AWS

Cada conector do AWS recebe 90 dias de avaliação gratuita. Durante a visualização pública, não há nenhum encargo.

O preço da lista é de 1% dos seus custos mensais do AWS. Cada mês, você é cobrado com base nos custos faturados do mês anterior.

O acesso às APIs do AWS pode incorrer em custos adicionais.

## <a name="aws-integration-limitations"></a>Limitações de integração do AWS

- O gerenciamento de custos não dá suporte a relatórios de custo que contenham vários tipos de moeda. Uma mensagem de erro será exibida se você selecionar um escopo que tenha várias moedas.
- Os conectores de nuvem não dão suporte a AWS GovCloud (US), AWS gov ou AWS China.
- O gerenciamento de custos mostra apenas _os custos de uso_ de AWS. O imposto, o suporte, os reembolsos, a RI, os créditos ou quaisquer outros tipos de cobrança ainda não têm suporte.

## <a name="troubleshooting-aws-integration"></a>Solução de problemas de integração do AWS

Use as informações de solução de problemas a seguir para resolver problemas comuns.

### <a name="no-permission-to-aws-linked-accounts"></a>Sem permissão para contas vinculadas do AWS

**Código de erro:** _Não autorizado_

Há duas maneiras de obter permissões para acessar os custos de contas vinculadas do AWS:

- Obtenha acesso ao grupo de gerenciamento que tem as contas vinculadas do AWS.
- Peça que alguém lhe conceda permissão para a conta vinculada do AWS.

Por padrão, o criador do conector do AWS é o proprietário de todos os objetos criados pelo conector. Incluindo, a conta consolidada do AWS e a conta vinculada do AWS.

Para poder verificar as configurações do conector, você precisará de pelo menos uma função do Contributor, o leitor não pode verificar as configurações do conector

### <a name="collection-failed-with-assumerole"></a>Falha na coleta com AssumeRole

**Código de erro:** _FailedToAssumeRole_

Esse erro significa que o gerenciamento de custos não pode chamar a API AWS AssumeRole. Esse problema pode ocorrer devido a um problema com a definição de função. Verifique se as seguintes condições são verdadeiras:

- A ID externa é igual à da definição de função e à definição de conector.
- O tipo de função é definido como **outra conta do AWS que pertence a você ou a terceiros.**
- A opção **exigir MFA** está desmarcada.
- A conta AWS confiável na função AWS é _432263259397_.

### <a name="collection-failed-with-access-denied---cur-report-definitions"></a>Falha na coleta com acesso negado-definições de relatório CUR

**Código de erro:** _AccessDeniedReportDefinitions_ 

Esse erro significa que o gerenciamento de custos não consegue ver as definições de relatório de custo e uso. Essa permissão é usada para validar que a CUR é definida como esperado pelo gerenciamento de custos do Azure. Confira [criar um relatório de custo e uso em AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws).

### <a name="collection-failed-with-access-denied---list-reports"></a>Falha na coleta com relatórios de lista de acesso negado

**Código de erro:** _AccessDeniedListReports_ 

Esse erro significa que o gerenciamento de custos não pode listar o objeto no Bucket S3 em que a CUR está localizada. A política IAM AWS requer uma permissão no Bucket e nos objetos no Bucket. Consulte [criar uma função e uma política no AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="collection-failed-with-access-denied---download-report"></a>Falha na coleta com acesso negado-relatório de download 

**Código de erro:** _AccessDeniedDownloadReport_ 

Esse erro significa que o gerenciamento de custos não pode acessar e baixar os arquivos de CUR armazenados no Bucket S3 da Amazon. Verifique se a política JSON AWS anexada à função é semelhante ao exemplo mostrado na parte inferior da seção [criar uma função e uma política no AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) .

### <a name="collection-failed-since-we-did-not-find-the-cost-and-usage-report"></a>Falha na coleta porque não encontramos o relatório de custos e uso

**Código de erro:** _FailedToFindReport_

Esse erro significa que o gerenciamento de custos não pode localizar o relatório de custo e uso que foi definido no conector. Verifique se ele não foi excluído e se a política JSON AWS anexada à função é semelhante ao exemplo mostrado na parte inferior da seção [criar uma função e uma política no AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) .

### <a name="unable-to-create-or-verify-connector-due-to-cost-and-usage-report-definitions-mismatch"></a>Não é possível criar ou verificar o conector devido à incompatibilidade das definições de relatório de uso e custo

**Código de erro:** _ReportIsNotValid_

Esse erro está relacionado à definição do relatório de uso e custo de AWS, exigimos configurações específicas para esse relatório, consulte os requisitos em [criar um relatório de custo e uso em AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws)

## <a name="next-steps"></a>Próximas etapas

- Se você ainda não tiver configurado seu ambiente do Azure com grupos de gerenciamento, consulte [configuração inicial dos grupos de gerenciamento](../governance/management-groups/index.md#initial-setup-of-management-groups).
