---
title: Solucionar problemas de cobrança do MCA do Azure com tabelas dinâmicas de arquivo de uso
description: Este artigo ajuda a solucionar problemas de cobrança relacionados ao MCA (Contrato de Cliente da Microsoft) usando tabelas dinâmicas criadas com base em seus arquivos de uso CSV.
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/09/2020
ms.author: banders
ms.openlocfilehash: 32c0779f4086574aeaf8d38ea675f80fbd2c1ec7
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132220"
---
# <a name="troubleshoot-mca-billing-issues-with-usage-file-pivot-tables"></a>Solucionar problemas de cobrança do MCA com tabelas dinâmicas de arquivo de uso

Este artigo ajuda você a solucionar problemas de cobrança relacionados ao MCA (Contrato de Cliente da Microsoft) usando tabelas dinâmicas em seus arquivos de uso. Os arquivos de uso do Azure contêm todas as informações sobre seu consumo e seu uso do Azure. As informações no arquivo podem ajudar você a:

- Entender como as reservas do Azure estão sendo usadas e aplicadas
- Reconciliar informações no Gerenciamento de Custos do Azure com sua fatura cobrada
- Solucionar problemas de um pico de custo
- Calcular o valor de um reembolso para um SLA

Usando as informações em seus arquivos de uso, você pode ter uma melhor compreensão sobre os problemas de uso e diagnosticá-los. Os arquivos de uso são gerados em formato CSV (delimitado por vírgula). Como esses arquivos podem ser arquivos CSV grandes, é mais fácil manipular e exibi-los como tabelas dinâmicas em um aplicativo de planilha como o Excel. Os exemplos neste artigo usam o Excel, mas você pode usar o aplicativo de planilha de sua preferência.

Somente Proprietários do perfil de cobrança, Colaboradores, Leitores ou Gerenciadores de Faturas têm acesso para baixar arquivos de uso. Para saber mais, confira [Baixar o uso de seu Contrato de Cliente da Microsoft](./download-azure-invoice-daily-usage-date.md#download-usage-for-your-microsoft-customer-agreement). 

## <a name="get-the-data-and-format-it"></a>Obter os dados e formatá-los

Como os arquivos de uso do Azure estão no formato CSV, você precisa preparar os dados para uso no Excel. Use as etapas a seguir para formatar os dados como uma tabela.

1. Baixe o arquivo de uso seguindo as instruções em [Baixar o uso no portal do Azure](./download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal).
1. Abra o arquivo no Excel.
1. Os dados não formatados são semelhantes ao exemplo a seguir.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/raw-csv-data-mca.png" alt-text="Exemplo mostrando dados não formatados" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/raw-csv-data-mca.png" :::
1. Selecione o primeiro campo na tabela, **invoiceID**.
1. Pressione Ctrl + Shift + Seta para baixo e, depois, Ctrl + Shift + Seta para a direita para selecionar todas as informações na tabela.
1. No menu superior, selecione **Inserir** > **Tabela**. Na caixa Criar tabela, selecione **Minha tabela tem cabeçalhos** e escolha **OK**.  
:::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Exemplo mostrando dados não formatados" :::
1. No menu superior, selecione **Inserir** > **Tabela Dinâmica** e selecione **OK**. Essa ação cria uma planilha no arquivo e leva você para a área de tabela dinâmica no lado direito da planilha.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="Exemplo mostrando dados não formatados" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

A área Campos da Tabela Dinâmica é uma área do tipo "arrastar e soltar". Prossiga para a próxima seção para criar a tabela dinâmica.

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>Criar tabela dinâmica para exibir os custos do Azure de acordo com os recursos

Nesta seção, você cria uma tabela dinâmica na qual pode solucionar problemas gerais de uso do Azure. A tabela de exemplo pode ajudar você a investigar qual serviço consome mais recursos. Ou você pode ver os recursos que geram mais custos e como um serviço está sendo cobrado.

1. Na área Campos da Tabela Dinâmica, arraste **Categoria de Medidor** e **Produto** para a seção **Linhas**. Coloque **Produto** abaixo **Categoria de Medidor**.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="Exemplo mostrando dados não formatados" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. Em seguida, adicione a coluna **costInBillingCurrenty** à seção **Valores**. Também é possível usar a coluna **Quantidade** para obter informações sobre transações e unidades de consumo. Por exemplo, GB e Horas. Ou para ver as transações em vez do custo em moedas diferentes, como USD, EUR e INR.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="Exemplo mostrando dados não formatados" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. Agora, você tem um painel para investigar o consumo de maneira geral. Você pode filtrar segundo um serviço específico usando as opções de filtragem na tabela dinâmica.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="Exemplo mostrando dados não formatados" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    Para filtrar um segundo nível em uma tabela dinâmica, por exemplo, um recurso, selecione um item de segundo nível na tabela.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="Exemplo mostrando dados não formatados" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. Arraste a coluna **ResourceID** para a área **Linhas** em **Produto** para ver o custo de cada serviço por recurso.
1. Adicione a coluna **data** à área **Colunas** para ver o consumo diário do produto.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-date.png" alt-text="Exemplo mostrando dados não formatados" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-date.png" :::
1. Expanda e recolha os meses usando os símbolos **+** para a coluna de cada mês.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="Exemplo mostrando dados não formatados" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" :::

Adicionar as colunas **Custo** e **Quantidade** na área **Valores** é opcional. Isso cria duas colunas para cada seção de dados abaixo de cada mês e dia quando a coluna Data está na seção Colunas da tabela dinâmica.

Para filtros adicionais, você pode adicionar InvoiceSection, costCenter, SubscriptionID, ResourceGroupName ou Marcas à seção filtros e selecionar o escopo desejado.

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>Criar tabela dinâmica para exibir o custo de um recurso específico

Um recurso pode gerar vários encargos para diferentes serviços. Por exemplo, uma máquina virtual pode gerar encargos de Computação, Licenciamento do SO, Largura de Banda (transferências de dados), Uso de RI e armazenamento para instantâneos. Para você examinar o uso geral de recursos específicos, as etapas a seguir orientam quanto à criação de um painel para exibir o uso geral com seus arquivos de uso.

1. No menu à direita, arraste **ResourceID** para a seção **Filtro** no menu de tabela dinâmica.
1. Selecione o recurso cujos custos deseja ver. Digite na caixa **Pesquisa** para localizar o nome de um recurso.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/resource-id-search.png" alt-text="Exemplo mostrando dados não formatados" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/resource-id-search.png" :::
1. Adicione **meterCategory** e **Produto** à área **Linhas**. Coloque **Produto** abaixo de **meterCategory**.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" alt-text="Exemplo mostrando dados não formatados" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" :::
1. Em seguida, adicione a coluna **Custo Estendido** à seção **Valores**. Também é possível usar a coluna Quantidade Consumida para obter informações sobre transações e unidades de consumo. Por exemplo, GB e Horas. Ou para ver as transações em vez do custo em moedas diferentes, como USD, EUR e INR. Agora, você tem um painel que mostra todos os serviços que o recurso consome.
1. Adicione a coluna **Data** à seção **Colunas**. Ela mostra o consumo diário.
1. Você pode expandir e reduzir usando os ícones **+** na coluna de cada mês.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="Exemplo mostrando dados não formatados" :::

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

## <a name="next-steps"></a>Próximas etapas

- [Explorar e analisar custos com a análise de custos](../costs/quick-acm-cost-analysis.md).