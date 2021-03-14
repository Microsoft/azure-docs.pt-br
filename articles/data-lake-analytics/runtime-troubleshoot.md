---
title: Como solucionar problemas de falhas de tempo de execução do Azure Data Lake Analytics U-SQL
description: Saiba como solucionar falhas de tempo de execução do U-SQL.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.date: 10/10/2019
ms.openlocfilehash: 1236b83b410057e55015391772e37bd461a448d0
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030606"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>Saiba como solucionar falhas de tempo de execução do U-SQL devido a alterações no tempo de execução

O runtime U-SQL do Azure Data Lake, incluindo o compilador, o otimizador e o gerenciador de trabalhos, é o que processa seu código U-SQL.

## <a name="choosing-your-u-sql-runtime-version"></a>Escolhendo sua versão de tempo de execução do U-SQL

Quando você envia trabalhos do U-SQL do Visual Studio, do SDK do ADL ou do portal de Azure Data Lake Analytics, seu trabalho usará o tempo de execução padrão disponível no momento. Novas versões do tempo de execução do U-SQL são lançadas regularmente e incluem correções de segurança e atualizações secundárias.

Você também pode escolher uma versão de tempo de execução personalizada; Porque você deseja experimentar uma nova atualização, precisa permanecer em uma versão mais antiga de um tempo de execução ou ser fornecido com um hotfix para um problema relatado em que não é possível esperar pela nova atualização regular.

> [!CAUTION]
> A escolha de um runtime diferente do padrão apresenta o potencial de interromper seus trabalhos do U-SQL. Use essas outras versões somente para teste.

Em casos raros, Suporte da Microsoft pode fixar uma versão diferente de um tempo de execução como o padrão para sua conta. Verifique se você reverta esse PIN assim que possível. Se você permanecer fixado a essa versão, ela expirará em alguma data posterior.

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>Monitorando sua versão de tempo de execução U-SQL de trabalhos

Você pode ver o histórico de qual versão de tempo de execução seus trabalhos anteriores usaram no histórico de trabalho da sua conta por meio do navegador de trabalho do Visual Studio ou do histórico de trabalho do portal do Azure.

1. No portal do Azure, acesse sua conta do Data Lake Analytics.
2. Selecione **Exibir todos os trabalhos**. Uma lista de todos os trabalhos ativos e concluídos recentemente na conta é exibida.
3. Opcionalmente, clique em **Filtrar** para ajudá-lo a localizar os trabalhos por valores de **Intervalo de Tempo**, **Nome do Trabalho** e **Autor**.
4. Você pode ver o tempo de execução usado nos trabalhos concluídos.

![Exibindo a versão de tempo de execução de um trabalho passado](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

As versões de tempo de execução disponíveis mudam com o passar do tempo. O tempo de execução padrão é sempre chamado de "padrão" e mantemos pelo menos o tempo de execução anterior disponível por algum tempo, além de tornar os tempos de execução especiais disponíveis por vários motivos. Tempos de execução explicitamente nomeados geralmente seguem o seguinte formato (os itálicos são usados para partes variáveis e [] indicam partes opcionais):

release_YYYYMMDD_adl_buildno [_modifier]

Por exemplo, release_20190318_adl_3394512_2 significa a segunda versão do Build 3394512 da versão de tempo de execução de 18 2019 de março e release_20190318_adl_3394512_private significa uma compilação particular da mesma versão. Observação: a data está relacionada a quando o último check-in foi realizado para essa versão e não necessariamente a data de lançamento oficial.


## <a name="troubleshooting-u-sql-runtime-version-issues"></a>Solucionando problemas de versão do tempo de execução do U-SQL

Há dois possíveis problemas de versão de tempo de execução que você pode encontrar:

1. Um script ou algum código de usuário está alterando o comportamento de uma versão para outra. Essas alterações significativas normalmente são comunicadas antecipadamente com a publicação de notas de versão. Se você encontrar essa alteração significativa, entre em contato com Suporte da Microsoft para relatar esse comportamento de interrupção (caso ainda não tenha sido documentado) e envie seus trabalhos para a versão de tempo de execução mais antiga.

2. Você esteve usando um tempo de execução não padrão de forma explícita ou implícita quando foi fixado em sua conta e esse tempo de execução foi removido após algum tempo. Se você encontrar tempos de execução ausentes, atualize seus scripts para executar com o tempo de execução padrão atual. Se você precisar de mais tempo, entre em contato com Suporte da Microsoft

## <a name="known-issues"></a>Problemas conhecidos

1. A referência a Newtonsoft.Jsna versão do arquivo 12.0.3 ou em diante em um script USQL causará a seguinte falha de compilação:

    *"Lamentamos; os trabalhos em execução na sua conta de Data Lake Analytics provavelmente serão executados mais lentamente ou não serão concluídos. Um problema inesperado está nos impedindo de restaurar automaticamente essa funcionalidade para sua conta de Azure Data Lake Analytics. Os engenheiros de Azure Data Lake foram contatados para investigar. "*  

    Onde a pilha de chamadas conterá:  
    `System.IndexOutOfRangeException: Index was outside the bounds of the array.`  
    `at Roslyn.Compilers.MetadataReader.PEFile.CustomAttributeTableReader.get_Item(UInt32 rowId)`  
    `...`

    **Solução**: Use Newtonsoft.Jsno arquivo v 12.0.2 ou inferior.
2. Os clientes podem ver arquivos e pastas temporários em seu armazenamento. Elas são produzidas como parte da execução normal do trabalho, mas geralmente são excluídas antes que os clientes as vejam. Em determinadas circunstâncias, que são raras e aleatórias, elas podem permanecer visíveis por um período de tempo. Eles são eventualmente excluídos e nunca são contados como parte do armazenamento do usuário ou geram qualquer forma de cobranças. Dependendo da lógica de trabalho dos clientes, eles podem causar problemas. Por exemplo, se o trabalho enumerar todos os arquivos na pasta e, em seguida, comparar as listas de arquivos, ele poderá falhar devido à presença dos arquivos temporários inesperados. Da mesma forma, se um trabalho downstream enumerar todos os arquivos de uma determinada pasta para processamento adicional, ele também poderá enumerar os arquivos temporários.  

    **Solução**: uma correção é identificada no tempo de execução em que os arquivos temporários serão armazenados na pasta temporária de nível de conta do que a pasta de saída atual. Os arquivos temporários serão gravados nessa nova pasta Temp e serão excluídos no final da execução do trabalho.  
    Como essa correção está lidando com os dados do cliente, é extremamente importante que essa correção seja bem validada no MSFT antes de ser liberada. Espera-se que essa correção esteja disponível como tempo de execução beta no meio do ano 2021 e como tempo de execução padrão na segunda metade do ano 2021. 


## <a name="see-also"></a>Confira também

- [Visão geral da Análise Azure Data Lake](data-lake-analytics-overview.md)
- [Gerenciar Azure Data Lake Analytics usando portal do Azure](data-lake-analytics-manage-use-portal.md)
- [Monitoramento no Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
