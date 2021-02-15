---
title: Solucionar problemas de cobrança do CSP do Azure com tabelas dinâmicas de arquivo de uso
description: Este artigo ajuda a solucionar problemas de cobrança relacionados ao CSP (Provedor de Soluções de Nuvem) do Azure usando tabelas dinâmicas criadas com base em seus arquivos de uso CSV.
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/09/2020
ms.author: banders
ms.openlocfilehash: 6525d809805da3a19a0efe423306f18d8e67a646
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026216"
---
# <a name="troubleshoot-csp-billing-issues-with-usage-file-pivot-tables"></a>Solucionar problemas de cobrança do CSP com tabelas dinâmicas de arquivo de uso

Este artigo ajuda a solucionar problemas de cobrança relacionados ao CSP (Provedor de Soluções de Nuvem) usando tabelas dinâmicas em seus arquivos de reconciliação (uso) do Partner Center. Os arquivos de uso do Azure contêm todas as informações sobre seu consumo e seu uso do Azure. As informações no arquivo podem ajudar você a:

- Entender como as reservas do Azure estão sendo usadas e aplicadas
- Reconciliar informações no Gerenciamento de Custos do Azure com sua fatura cobrada
- Solucionar problemas de um pico de custo
- Calcular o valor de um reembolso para um SLA

Usando as informações em seus arquivos de uso, você pode ter uma melhor compreensão sobre os problemas de uso e diagnosticá-los. Os arquivos de uso são gerados em formato CSV (delimitado por vírgula). Como esses arquivos podem ser arquivos CSV grandes, é mais fácil manipular e exibi-los como tabelas dinâmicas em um aplicativo de planilha como o Excel. Os exemplos neste artigo usam o Excel, mas você pode usar o aplicativo de planilha de sua preferência.

Somente Administradores de cobrança e Administradores globais têm acesso para baixar arquivos de reconciliação. Para saber mais, confira [Saiba como ler os itens de linha em seus arquivos de reconciliação do Partner Center](/partner-center/use-the-reconciliation-files).

## <a name="get-the-data-and-format-it"></a>Obter os dados e formatá-los

Como os arquivos de uso do Azure estão no formato CSV, você precisa preparar os dados para uso no Excel. Use as etapas a seguir para formatar os dados como tabela.

1. Baixe o arquivo de uso seguindo as instruções em [Localizar sua fatura](/partner-center/read-your-bill#find-your-bill).
1. Abra o arquivo no Excel.
1. Os dados não formatados são semelhantes ao exemplo a seguir.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" alt-text="Exemplo mostrando dados não formatados no Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" :::
1. Selecione o primeiro campo na tabela, **PartnerID**.
1. Pressione Ctrl + Shift + Seta para baixo e, depois, Ctrl + Shift + Seta para a direita para selecionar todas as informações na tabela.
1. No menu superior, selecione **Inserir** > **Tabela**. Na caixa Criar tabela, selecione **Minha tabela tem cabeçalhos** e escolha **OK**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Exemplo mostrando dados não formatados no Excel" :::
1. No menu superior, selecione **Inserir** > **Tabela Dinâmica** e selecione **OK**. Essa ação cria uma planilha no arquivo e leva você para a área de tabela dinâmica no lado direito da planilha.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="Exemplo mostrando dados não formatados no Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

A área Campos da Tabela Dinâmica é uma área do tipo "arrastar e soltar". Prossiga para a próxima seção para criar a tabela dinâmica.

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>Criar tabela dinâmica para exibir os custos do Azure de acordo com os recursos

Nesta seção, você cria uma tabela dinâmica na qual pode solucionar problemas gerais de uso do Azure. A tabela de exemplo pode ajudar você a investigar qual serviço consome mais recursos. Ou você pode ver os recursos que geram mais custos e como um serviço está sendo cobrado.

1. Na área dos campos da Tabela Dinâmica, arraste **Nome do Serviço** e **Recurso** para a área **Linhas**. Coloque **Recurso** abaixo de **Nome do Serviço**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="Exemplo mostrando dados não formatados no Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. Em seguida, coloque **Total Após o Imposto** na área **Valores**. Também é possível usar a coluna Quantidade Consumida para obter informações sobre transações e unidades de consumo. Por exemplo, GB e Horas. Ou para ver as transações em vez do custo em moedas diferentes, como USD, EUR e INR.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="Exemplo mostrando dados não formatados no Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. Agora, você tem um painel para investigar o consumo de maneira geral. Você pode filtrar segundo um serviço específico usando as opções de filtragem na tabela dinâmica.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="Exemplo mostrando dados não formatados no Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    Para filtrar um segundo nível em uma tabela dinâmica, por exemplo, um recurso, selecione um item de segundo nível na tabela.
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="Exemplo mostrando dados não formatados no Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. Para filtros adicionais, você pode adicionar **SubscriptionID** e **Nome da Empresa do Cliente** à área **Filtros** e selecionar o escopo desejado.

## <a name="create-a-pivot-table-to-view-azure-usage-by-date"></a>Criar uma tabela dinâmica para exibir o uso do Azure por data

Nesta seção, você cria uma tabela dinâmica na qual pode solucionar problemas gerais de uso do Azure de acordo com a data e a Quantidade Consumida. Isso é útil para identificar picos de cobrança por data e por serviço. Ou você pode ver os recursos que geram mais custos e como um serviço está sendo cobrado.

O arquivo de reconciliação tem duas tabelas. Uma fica na parte superior (a tabela principal) e a outra fica na parte inferior do documento. Essa segunda tabela tem grande parte das mesmas informações, mas não inclui detalhes de preços nem custos. Ele tem a data de uso e a quantidade consumida.

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" alt-text="Exemplo mostrando dados não formatados no Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" :::

1. Use as mesmas etapas da seção [Obter os dados e formatá-los](#get-the-data-and-format-it) para criar uma tabela do Excel com as informações na parte inferior do arquivo de reconciliação.
1. Quando a tabela estiver pronta e você tiver uma planilha de tabela dinâmica, use as mesmas etapas da seção create-pivot-table-to-view-azure-costs-by-resources para preparar o painel. Em vez de usar o total após o imposto, coloque **Quantidade consumida** na área **Valores**.
1. Adicione **Data de Uso** à seção colunas. A tabela dinâmica deve ser semelhante ao exemplo a seguir.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" alt-text="Exemplo mostrando dados não formatados no Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" :::
1. Agora, você tem um painel que mostra o uso por data. Você pode estender cada mês selecionando o símbolo de **+** .

O painel mostra a quantidade consumida em unidades como GB, Horas e Transferências.

Para exibir o preço por dia, você pode adicionar **GUID do Recurso** à área **Linhas**. Na tabela superior, adicione o preço unitário (**ListPrice**) do recurso. Multiplique **ListPrice** pela **Quantidade consumida** para calcular seus encargos antes do imposto. Os valores devem ser correspondentes.

Alguns recursos (serviços) têm os preços dimensionados pela quantidade consumida. Por exemplo, alguns recursos têm um preço mais alto para os primeiros 100 GB consumidos e um preço mais baixo para os GB usados posteriormente. Tenha em mente os preços dimensionados ao calcular os custos manualmente.

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>Criar tabela dinâmica para exibir o custo de um recurso específico

Um recurso pode gerar vários encargos para diferentes serviços. Por exemplo, uma máquina virtual pode gerar encargos de Computação, Licenciamento do SO, Largura de Banda (Transferências de dados), Uso de RI e armazenamento para instantâneos. Para você examinar o uso geral de recursos específicos, as etapas a seguir orientam quanto à criação de um painel para exibir o uso geral com seus arquivos de uso.

Os arquivos de reconciliação não contêm detalhes específicos dos recursos. Sendo assim, você usa o arquivo de uso agregado. Entre em contato com o [Suporte de Cobrança do Azure](https://go.microsoft.com/fwlink/?linkid=2083458) para solicitar que forneçam o arquivo de uso agregado de sua assinatura. Os arquivos agregados são gerados no nível da assinatura. Os dados não formatados são semelhantes ao exemplo a seguir.

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" alt-text="Exemplo mostrando dados não formatados no Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" :::

O arquivo contém as colunas a seguir.

- **UsageStart** e **UsageEnd** – data de cada item de linha (cada unidade de uso). Por exemplo, cada dia.
- **MeteredResourceID** – no Azure, corresponde à ID do medidor.
- **Propriedades** – contém a ID da Instância (nome do recurso) com outros detalhes, como a localização.
- **Quantidade** – a quantidade consumida no arquivo de reconciliação.

1. Selecione o primeiro campo na tabela, **PartnerID**.  
1. Pressione Ctrl + Shift + Seta para baixo e, depois, Ctrl + Shift + Seta para a direita para selecionar todas as informações na tabela.
1. No menu superior, selecione **Inserir** > **Tabela**. Na caixa Criar tabela, selecione **Minha tabela tem cabeçalhos** e escolha **OK**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Exemplo mostrando dados não formatados no Excel" :::
1. No menu superior, selecione **Inserir** > **Tabela Dinâmica** e selecione **OK**. Essa ação cria uma planilha no arquivo e leva você para a área de tabela dinâmica no lado direito da planilha.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" alt-text="Exemplo mostrando dados não formatados no Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" :::
1. Em seguida, adicione **MeteredResourceID** à área **Linhas** e **Quantidade** a **Valores**. Os resultados mostram as informações de uso geral. Para obter detalhes adicionais, coloque **UsageEndDateTime** na área **Colunas**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" alt-text="Exemplo mostrando dados não formatados no Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" :::
1. Para ver um relatório geral, adicione **Propriedades** a **Linhas** em **MeteredResourceID**. É mostrado um painel completo de seu uso.
1. Para filtrar segundo um recurso específico, adicione **Propriedades** à área **Filtros** e selecione o uso desejado. Você pode usar a Pesquisa para localizar um nome de recurso.
    Para ver o custo do recurso, localize a quantidade total consumida e multiplique o valor pelo preço de lista. O preço de lista é específico para cada GUID do Recurso (MeteredResourceID). Se um recurso estiver consumindo vários MeteredResourceIDs, você precisará observar o valor total referente a cada ID.

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Gerenciamento de Custos do Azure para parceiros](../costs/get-started-partners.md).