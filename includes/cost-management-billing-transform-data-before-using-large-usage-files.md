---
title: incluir arquivo
description: incluir arquivo
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: include
ms.date: 10/09/2020
ms.author: banders
ms.reviwer: ''
ms.custom: include file
ms.openlocfilehash: fb34b4ef1cf209a15c7e6ac9a59cc11dd4b0056e
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026177"
---
## <a name="transform-data-before-using-large-usage-files"></a>Transformar dados antes de usar arquivos de uso grandes

Às vezes, seu arquivo de uso ou de reconciliação é grande demais para ser aberto. Ou talvez você precise apenas de parte das informações para solucionar um problema. Por exemplo, talvez você queira apenas informações sobre um recurso específico ou apenas o consumo de alguns serviços ou grupos de recursos. Você pode transformar os dados a fim de resumi-los antes de criar tabelas dinâmicas.

1. Abra uma pasta de trabalho em branco no Excel.
1. No menu superior, selecione **Dados** > **De Texto/CSV**, selecione o arquivo de uso e escolha **Importar**.
1. Na parte inferior da janela, selecione **Transformar Dados**. Uma nova janela mostra um resumo dos dados.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/summarized-data.png" alt-text="Exemplo mostrando dados resumidos" lightbox="./media/cost-management-billing-transform-data-before-using-large-usage-files/summarized-data.png" :::
1. Se tiver um Contrato de Cliente da Microsoft, pule esta etapa e prossiga para a próxima, pois os arquivos de uso do MCA geralmente têm os títulos das colunas nas primeiras linhas. Prepare os dados criando a tabela. Remova as linhas superiores, deixando somente os títulos. Selecione **Remover Linhas** > **Remover Linhas Superiores**.  
     :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/remove-top-rows.png" alt-text="Exemplo mostrando dados resumidos" :::
1. Na janela Remover Linhas Superiores, insira o número de linhas a serem removidas na parte superior. Para EA, normalmente 2; para CSP, normalmente 1. Selecione **OK**.
1. Selecione **Usar a Primeira Linha como Cabeçalho**.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/use-first-row-as-header.png" alt-text="Exemplo mostrando dados resumidos" :::
    
    A exibição de tabela mostra os títulos das colunas na parte superior.
1. Em seguida, adicione um filtro. Use as setas o seletor à direita de cada título de coluna para filtrar. Os filtros sugeridos são ID da Assinatura, Nome do Serviço (Categoria de medidor), ID da Instância e grupo de recursos. Você pode usar vários filtros no mesmo documento. Recomendamos que você aplique todos os filtros possíveis para reduzir o tamanho do documento e ajudar no trabalho posterior.
1. Depois de aplicar os filtros, selecione **Fechar e Carregar**.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/close-and-load.png" alt-text="Exemplo mostrando dados resumidos" :::

O arquivo é carregado e mostra uma tabela com os dados de uso filtrados. Agora, você pode criar uma tabela dinâmica para solucionar problemas de uso.