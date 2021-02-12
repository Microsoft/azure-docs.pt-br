---
title: Gerenciar custos e uso da AWS no Gerenciamento de Custos do Azure
description: Este artigo ajuda você a entender como usar a análise de custos e os orçamentos no Gerenciamento de Custos para gerenciar os custos e o uso da AWS.
author: bandersmsft
ms.author: banders
ms.date: 10/16/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: matrive
ms.custom: ''
ms.openlocfilehash: 5fed70ccdbebbd178412c416f37c2e9001a81f38
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188098"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>Gerenciar o uso e os custos da AWS no Azure

Depois de preparar e configurar a integração de relatório de Uso e Custo da AWS para o Gerenciamento de Custos do Azure, você estará pronto para começar a gerenciar os custos e o uso do seu AWS. Este artigo ajuda você a entender como usar a análise de custos e os orçamentos no Gerenciamento de Custos para gerenciar os custos e o uso da AWS.

Se você ainda não tiver configurado a integração, veja [Preparar e configurar a integração de relatório de uso da AWS](aws-integration-set-up-configure.md).

_Antes de começar:_ Se você ainda não estiver familiarizado com a análise de custo, veja o início rápido [Explorar e analisar os custos com a análise de custo](quick-acm-cost-analysis.md). E, se você não estiver familiarizado com orçamentos no Azure, veja o tutorial [Criar e gerenciar orçamentos do Azure](tutorial-acm-create-budgets.md).

## <a name="view-aws-costs-in-cost-analysis"></a>Examine os custos da AWS na análise de custo

Os custos da AWS estão disponíveis na Análise de Custo nos seguintes escopos:

- Contas vinculadas da AWS em um grupo de gerenciamento
- Custos da conta vinculada da AWS
- Custos da conta consolidada da AWS

As próximas seções descrevem como usar os escopos para que você veja dados de custo e de uso para cada um.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>Exibir as contas vinculadas da AWS em um grupo de gerenciamento

A exibição de custos usando o escopo do grupo de gerenciamento é a única maneira de ver os custos agregados provenientes de assinaturas do Azure e contas vinculadas da AWS. O uso de um grupo de gerenciamento fornece uma exibição entre nuvens dos custos do Azure e da AWS juntos.

Em análise de custo, abra o seletor de escopo e selecione o grupo de gerenciamento que contém suas contas vinculadas da AWS. Aqui está uma imagem de exemplo no portal do Azure:

:::image type="content" source="./media/aws-integration-manage/select-scope01.png" alt-text="Exemplo da exibição Selecionar escopo com contas vinculadas em um grupo de gerenciamento" :::

Veja um exemplo que mostra o custo do grupo de gerenciamento na análise de custo, agrupado por Provedor (Azure e AWS).

:::image type="content" source="./media/aws-integration-manage/cost-analysis-aws-azure.png" alt-text="Exemplo mostrando os custos do Azure e da AWS para um trimestre na análise de custo" lightbox="./media/aws-integration-manage/cost-analysis-aws-azure.png" :::

> [!NOTE]
> Atualmente, não há suporte para os grupos de gerenciamento nos clientes do MCA (Contrato de Cliente da Microsoft). Os clientes do MCA podem criar o conector e ver os respectivos dados da AWS. No entanto, os clientes do MCA não podem ver os custos do Azure e os custos da AWS juntos em um grupo de gerenciamento.

### <a name="view-aws-linked-account-costs"></a>Exibir os custos da conta vinculada da AWS

Para exibir os custos da conta vinculada da AWS, abra o seletor de escopo e selecione a conta vinculada da AWS. Observe que as contas vinculadas estão associadas a um grupo de gerenciamento, conforme definido no conector da AWS.

Aqui está um exemplo que mostra a seleção de um escopo de conta vinculada da AWS.

:::image type="content" source="./media/aws-integration-manage/select-scope02.png" alt-text="Exemplo da exibição Selecionar escopo que mostra contas vinculadas da AWS" :::

### <a name="view-aws-consolidated-account-costs"></a>Exibir os custos da conta consolidada da AWS

Para exibir os custos da conta consolidada da AWS, abra o seletor de escopo e selecione a conta consolidada da AWS. Aqui está um exemplo que mostra a seleção de um escopo de conta consolidada da AWS.

:::image type="content" source="./media/aws-integration-manage/select-scope03.png" alt-text="Exemplo da exibição Selecionar escopo com contas consolidadas" :::

Esse escopo fornece uma exibição agregada de todas as contas vinculadas da AWS associadas à conta consolidada da AWS. Veja um exemplo que mostra os custos de uma conta consolidada da AWS, agrupada por nome de serviço.

:::image type="content" source="./media/aws-integration-manage/cost-analysis-aws-consolidated.png" alt-text="Exemplo mostrando os custos consolidados da AWS na análise de custo" lightbox="./media/aws-integration-manage/cost-analysis-aws-consolidated.png" :::

### <a name="dimensions-available-for-filtering-and-grouping"></a>Dimensões disponíveis para filtragem e agrupamento

A tabela a seguir descreve as dimensões disponíveis para uso como parâmetro de agrupamento e filtragem na análise de custo.

| Dimensão | Cabeçalho CUR da Amazon | Escopos | Comentários |
| --- | --- | --- | --- |
| Zona de disponibilidade | lineitem/AvailabilityZone | Todos |   |
| Location | produto/Região | Tudo |   |
| Medidor |   | Todos |   |
| Categoria de medidor | lineItem/ProductCode | Todos |   |
| Subcategoria de medidor | lineitem/UsageType | Todos |   |
| Operação | lineItem/Operação | Todos |   |
| Recurso | lineItem/ResourceId | Todos |   |
| Tipo de recurso | produto/instanceType | Tudo | Se produto/instanceType for nulo, lineItem/UsageType será usado. |
| ResourceGuid | N/D | Tudo | GUID do medidor do Azure. |
| Nome do serviço | produto/ProductName | Tudo | Se produto/ProductName for nulo, lineItem/ProductCode será usado. |
| Camada de serviço |   |   |   |
| ID da assinatura | lineItem/UsageAccountId | Conta consolidada e grupo de gerenciamento |   |
| Nome da assinatura | N/D | Conta consolidada e grupo de gerenciamento | Os nomes de conta são coletados usando a API da Organização da AWS. |
| Marca | resourceTags | Tudo | O prefixo _user:_ é removido das marcas definidas pelo usuário para permitir marcas entre nuvens. O prefixo _aws:_ é mantido intacto. |
| ID da conta de cobrança | fatura/PayerAccountId | Grupo de gerenciamento |   |
| Nome da conta de cobrança | N/D | Grupo de gerenciamento | Os nomes de conta são coletados usando a API da Organização da AWS. |
| Provedor | N/D | Grupo de gerenciamento | AWS ou Azure. |

## <a name="set-budgets-on-aws-scopes"></a>Definir orçamentos em escopos da AWS

Use orçamentos para gerenciar os custos e gerar responsabilidade em sua organização de maneira proativa. Os orçamentos são definidos na conta consolidada da AWS e nos escopos de conta vinculada da AWS. Veja um exemplo de orçamentos para uma conta consolidada da AWS mostrada no Gerenciamento de Custos:

:::image type="content" source="./media/aws-integration-manage/budgets-aws-consolidated-account01.png" alt-text="Exemplo mostrando orçamentos para uma conta consolidada da AWS" :::

## <a name="aws-data-collection-process"></a>Processo de coleta de dados da AWS

Depois de configurar o conector da AWS, a coleta de dados e os processos de descoberta são iniciados. Pode levar algumas horas para coletar todos os dados de uso. A duração depende de:

- O tempo necessário para processar os arquivos CUR que estão no bucket da AWS S3.
- A tempo necessário para criar a conta consolidada da AWS e os escopos de conta vinculada da AWS.
- A hora e a frequência com que a AWS está gravando os arquivos de relatório de custo e uso no Bucket do S3

## <a name="aws-integration-pricing"></a>Preços de integração da AWS

Para cada conector da AWS, você recebe 90 dias de avaliação gratuita.

O preço de lista é de 1% dos seus custos mensais da AWS. A cada mês, você é cobrado com base nos custos faturados do mês anterior.

O acesso às APIs da AWS pode incorrer em custos adicionais na AWS.

## <a name="aws-integration-limitations"></a>Limitações de integração da AWS

- Os orçamentos no Gerenciamento de Custos não dão suporte a grupos de gerenciamento com várias moedas. Nenhuma avaliação de orçamento será mostrada nos grupos de gerenciamento com várias moedas. Uma mensagem de erro será exibida se você selecionar um grupo de gerenciamento que tenha várias moedas ao criar um orçamento.
- Os conectores de nuvem não dão suporte a AWS GovCloud (US), AWS Gov ou AWS China.
- O Gerenciamento de Custos mostra apenas os _custos de uso_ da AWS. O imposto, o suporte, os reembolsos, a RI, os créditos ou outros tipos de cobrança ainda não são compatíveis.

## <a name="troubleshooting-aws-integration"></a>Solução de problemas de integração da AWS

Para resolver problemas comuns, use as informações de solução de problemas a seguir.

### <a name="no-permission-to-aws-linked-accounts"></a>Sem permissão para contas vinculadas da AWS

**Código de erro:** _Não autorizado_

Há duas maneiras de obter permissões para acessar os custos de contas vinculadas da AWS:

- Obtenha acesso ao grupo de gerenciamento que tem as contas vinculadas da AWS.
- Peça que alguém lhe conceda permissão para a conta vinculada da AWS.

Por padrão, o criador do conector da AWS é o proprietário de todos os objetos criados pelo conector. Isso inclui a conta consolidada da AWS e a conta vinculada da AWS.

Para poder verificar as configurações do conector, você precisará de pelo menos uma função de colaborador, já que a função de leitor não permite fazê-lo

### <a name="collection-failed-with-assumerole"></a>Falha na coleta com AssumeRole

**Código de erro:** _FailedToAssumeRole_

Esse erro significa que o Gerenciamento de Custos não consegue chamar a API AssumeRole da AWS. Esse problema pode ocorrer devido a alguma questão na definição de função. Verifique se as seguintes condições são verdadeiras:

- A ID externa é igual à da definição de função e à definição de conector.
- O tipo de função é definido como **Outra conta da AWS que pertence a você ou a terceiros**.
- A opção **Exigir MFA** está desmarcada.
- A conta AWS confiável na função da AWS é _432263259397_.

### <a name="collection-failed-with-access-denied---cur-report-definitions"></a>Falha na coleta com acesso negado – definições de relatório CUR

**Código de erro:** _AccessDeniedReportDefinitions_

Esse erro significa que o Gerenciamento de Custos não consegue ver as definições de relatório de custo e uso. Essa permissão é usada para validar que o CUR é definido como esperado pelo Gerenciamento de Custos do Azure. Veja [Criar um relatório de custo e uso na AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws).

### <a name="collection-failed-with-access-denied---list-reports"></a>Falha na coleta com acesso negado – listar relatórios

**Código de erro:** _AccessDeniedListReports_

Esse erro significa que o Gerenciamento de Custos não pode listar o objeto no bucket S3 em que o CUR está localizado. A política de IAM da AWS requer uma permissão no bucket e nos objetos nele contidos. Confira [Criar uma função e uma política na AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="collection-failed-with-access-denied---download-report"></a>Falha na coleta com acesso negado – Baixar relatório

**Código de erro:** _AccessDeniedDownloadReport_

Esse erro significa que o Gerenciamento de Custos não pode acessar e baixar os arquivos CUR armazenados no bucket do Amazon S3. Verifique se a política JSON da AWS anexada à função é semelhante ao exemplo mostrado na parte inferior da seção [Criar uma função e uma política na AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="collection-failed-since-we-did-not-find-the-cost-and-usage-report"></a>Falha na coleta porque não encontramos o relatório de custos e uso

**Código de erro:** _FailedToFindReport_

Esse erro significa que o Gerenciamento de Custos não pode localizar o relatório de custo e uso que foi definido no conector. Assegure que o relatório não tenha sido excluído e que a política JSON da AWS anexada à função seja semelhante ao exemplo mostrado na parte inferior da seção [Criar uma função e uma política na AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="unable-to-create-or-verify-connector-due-to-cost-and-usage-report-definitions-mismatch"></a>Não é possível criar ou verificar o conector devido à incompatibilidade das definições de relatório de uso e custo

**Código de erro:** _ReportIsNotValid_

Esse erro está relacionado à definição do Relatório de Uso e Custo da AWS, para o qual exigimos configurações específicas. Confira os requisitos em [Criar um relatório de Custo e Uso na AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws).

### <a name="internal-error-when-creating-connector"></a>Erro interno ao criar o conector

**Código de erro:** _Criar conector – Falha ao criar conector &lt;ConnectorName&gt;. Motivo: Erro interno. Verifique se as propriedades corretas da AWS foram fornecidas._

Esse erro pode ocorrer quando a assinatura e o conector da AWS estão em grupos de gerenciamento diferentes. A assinatura e o conector da AWS precisam estar no mesmo grupo de gerenciamento.

## <a name="next-steps"></a>Próximas etapas

- Se você ainda não tiver configurado seu ambiente do Azure com grupos de gerenciamento, veja [Configuração inicial dos grupos de gerenciamento](../../governance/management-groups/overview.md#initial-setup-of-management-groups).
