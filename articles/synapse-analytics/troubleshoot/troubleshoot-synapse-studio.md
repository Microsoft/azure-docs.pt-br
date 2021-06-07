---
title: Solucionar problemas do Synapse Studio
description: Solucionar problemas do Azure Synapse Studio
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 7d91001024ad547e52fe48ee30749fee9a4fb4a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98116170"
---
# <a name="azure-synapse-studio-troubleshooting"></a>Solução de problemas do Azure Synapse Studio

Este guia de solução de problemas fornece instruções sobre quais informações fornecer ao abrir um tíquete de suporte em problemas de conectividade de rede. Com as informações apropriadas, possivelmente podemos resolver o problema mais rapidamente.

## <a name="serverless-sql-pool-service-connectivity-issue"></a>Problema de conectividade do serviço do pool SQL sem servidor

### <a name="symptom-1"></a>Sintoma 1

A opção "pool de SQL sem servidor" está esmaecida na lista suspensa "conectar-se a".

![symptom1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>Sintoma 2

A execução da consulta com "pool de SQL sem servidor" fornece a mensagem de erro "falha ao estabelecer conexão com o servidor".

![sintoma 2](media/troubleshooting-synapse-studio/symptom2.png)
 

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

> [!NOTE] 
>    As seguintes etapas de solução de problemas são para o Chromium Edge e o Chrome. Você pode usar outros navegadores (como o FireFox) com as mesmas etapas de solução de problemas, mas a janela "ferramenta de desenvolvedor" pode ter um layout diferente das capturas de tela neste TSG. Se possível, não use a borda clássica para solução de problemas, pois ele pode mostrar informações imprecisas em determinada situação.

Abra o painel "informações de diagnóstico", selecione o botão "baixar diagnóstico". Mantenha as informações baixadas para o relatório de erros. Em vez disso, você pode copiar a "ID da sessão" e anexá-la ao abrir o tíquete de suporte.

![diagnóstico-informações](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

Para iniciar a solução de problemas, repita a operação executada no Azure Synapse Studio.

- Para o sintoma 1, selecione o botão "atualizar" à direita da lista suspensa "usar banco de dados" na guia "script SQL" e verifique se você pode ver "pool de SQL sem servidor".
- Para o sintoma 2, tente executar a consulta novamente para ver se ela é executada com êxito.

Se o problema ainda existir, pressione F12 no navegador para abrir "Ferramentas para Desenvolvedores" (DevTools).

Na janela "Ferramentas para Desenvolvedores", alterne para o painel "rede". Selecione o botão "limpar" na barra de ferramentas no painel "rede", se necessário.
Verifique se a opção "desabilitar cache" no painel "rede" está marcada.

Repita a operação que você executou no Azure Synapse Studio. Você poderá ver novos itens mostrados na lista "rede" em "Ferramentas para Desenvolvedores". Anote a hora atual do sistema para fornecer no tíquete de suporte.

![rede-painel 1](media/troubleshooting-synapse-studio/network-panel.png)

Localize o item cuja coluna de URL corresponde ao seguinte padrão:

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

Onde [*A*] é o nome do espaço de trabalho e "-OnDemand" poderia ser "-sqlod" e onde [*B*] deve ser um nome de banco de dados, como "mestre". Deve haver no máximo dois itens com o mesmo valor de URL, mas com valores de método diferentes; OPÇÕES e POST. Verifique se esses dois itens têm "200" ou "20x" na coluna status, em que "x" pode ser qualquer dígito único.

Se um deles tiver algo diferente de "20x" e:

- O status começa com "(Failed)", expanda a coluna "status" ou focalize o ponteiro sobre o texto de status para ver o texto completo. Inclua o texto e/ou captura de tela ao abrir o tíquete de suporte.

    ![texto de status](media/troubleshooting-synapse-studio/status-text.png)

    - Se você vir ERR_NAME_NOT_RESOLVED e criou seu espaço de trabalho em 10 minutos, aguarde 10 minutos e tente ver se o problema ainda existe.
    - Se você vir ERR_INTERNET_DISCONNECTED ou ERR_NETWORK_CHANGED, isso poderá indicar que a conexão de rede do PC está tendo problemas. Verifique sua conexão de rede e repita a operação.
    - Se você vir ERR_CONNECTION_RESET, ERR_SSL_PROTOCOL_ERROR ou outros códigos de erro que contenham "SSL", isso poderá indicar que a configuração de SSL local está tendo problemas ou o administrador de rede bloqueou o acesso ao servidor do pool SQL sem servidor. Abra um tíquete de suporte e anexe o código de erro na descrição.
    - Se você vir ERR_NETWORK_ACCESS_DENIED, talvez seja necessário verificar com o administrador se sua política de firewall local bloqueou o acesso ao domínio *. database.windows.net ou à porta remota 1443.
    - Opcionalmente, tente a mesma operação imediatamente em um ambiente de rede e/ou computador diferente para eliminar um problema de configuração de rede em seu PC.

- Status é "40x", "50 vezes" ou outros números, selecione no (s) item (ns) para ver os detalhes. Você deve ver os detalhes do item à direita. Localizar a seção "cabeçalho de resposta"; em seguida, verifique se um item chamado "Access-Control-Allow-Origin" existe. Nesse caso, verifique se ele tem um dos seguintes valores:

    - `*` (asterisco único)
    - https://web.azuresynapse.net/ (ou outro valor no qual o texto em sua barra de endereços do navegador começa)

Se o cabeçalho de resposta contiver um dos valores acima, isso significa que já deve ter coletado as informações de falha. Você pode abrir um tíquete de suporte, se necessário, e, opcionalmente, anexar a captura de tela dos detalhes do item.

Se você não conseguir ver o cabeçalho ou se o cabeçalho não tiver um dos valores listados acima, anexe uma captura de tela dos detalhes do item ao abrir o tíquete.

 
![detalhes do item](media/troubleshooting-synapse-studio/item-details.png)
 
Se as etapas acima não resolverem o problema, talvez seja necessário abrir um tíquete de suporte. Ao enviar seu tíquete de suporte, inclua a "ID da sessão" ou "informações de diagnóstico" baixadas no início deste guia.

Ao relatar o problema, você pode, opcionalmente, tirar uma captura de tela da guia "console" no "Ferramentas para Desenvolvedores" e anexá-la ao tíquete de suporte. Role o conteúdo e leve mais de uma captura de tela, se necessário, para capturar a mensagem inteira.

![console de ferramentas de desenvolvedor](media/troubleshooting-synapse-studio/developer-tool-console.png)

Se você estiver anexando capturas de tela, forneça o tempo (ou um intervalo de tempo estimado) de quando você tirou as capturas de tela. Ele nos ajudará ao olhar o problema.

Determinados navegadores dão suporte à exibição de carimbos de data/hora na guia "console". Para o Chromium Edge/Chrome, abra a caixa de diálogo "configurações" em "Ferramentas para Desenvolvedores" e marque "mostrar carimbos de data/hora" na guia "Preferências".

![configurações do console da ferramenta de desenvolvedor](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![Mostrar carimbo de data/hora](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>Próximas etapas
Se as etapas anteriores não ajudarem a resolver seu problema, [crie um tíquete de suporte](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)