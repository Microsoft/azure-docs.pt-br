---
title: "Solução de Problemas do Hybrid Runbook Workers da Automação do Azure | Microsoft Docs"
description: "Descreve os sintomas, causas e solução dos problemas mais comuns do Hybrid Runbook Worker na Automação do Azure."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 02c6606e-8924-4328-a196-45630c2255e9
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: magoedte
ms.openlocfilehash: 1a72fcf2493396b4744a49b01f3c6da8eed0de40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-tips-for-hybrid-runbook-worker"></a>Dicas de solução de problemas para o Hybrid Runbook Worker

Este artigo fornece ajuda para solucionar erros que podem ser encontrados no Hybrid Runbook Worker da Automação do Azure e sugere possíveis soluções para resolvê-los.

## <a name="a-runbook-job-terminates-with-a-status-of-suspended"></a>Um trabalho de runbook termina com o status suspenso

Seu runbook foi suspenso logo depois de tentar executá-lo três vezes. Existem condições que podem impedir o runbook de ser concluído com êxito e a mensagem de erro relacionada não inclui informações adicionais indicando o motivo. Este artigo fornece etapas de solução de problemas relacionados a falhas de execução de runbook do Hybrid Runbook Worker.

Se o problema do Azure não for resolvido neste artigo, visite os fóruns do Azure no [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Você pode postar seu problema nesses fóruns ou usando [@AzureSupport no Twitter](https://twitter.com/AzureSupport). Além disso, você pode registrar uma solicitação de suporte do Azure selecionando **Obter suporte** no site de [suporte do Azure](https://azure.microsoft.com/support/options/) .

### <a name="symptom"></a>Sintoma
Falha na execução de runbook e o erro retornado é "a ação do trabalho 'Activate' não pode ser executada porque o processo foi interrompido inesperadamente. A ação do trabalho foi tentada 3 vezes."

Há várias causas possíveis para o erro: 

1. O Hybrid Worker está usando um proxy ou firewall
2. O computador em que o Hybrid Worker está em execução não tem [requisitos de hardware](automation-offering-get-started.md#hybrid-runbook-worker) mínimos  
3. Os runbooks não podem ser autenticados com recursos locais

#### <a name="cause-1-hybrid-runbook-worker-is-behind-proxy-or-firewall"></a>Causa 1: o Hybrid Runbook Worker está protegido por proxy ou firewall
O computador em que o Hybrid Runbook Worker está em execução está protegido por um servidor proxy ou firewall e o acesso de rede de saída não pode ser permitido ou configurado corretamente.

#### <a name="solution"></a>Solução
Verifique se o computador tem acesso de saída para *.azure automation.net na porta 443. 

#### <a name="cause-2-computer-has-less-than-minimum-hardware-requirements"></a>Causa 2: o computador tem requisitos de hardware inferiores aos mínimos
Os computadores que executam o Hybrid Runbook Worker devem atender aos requisitos mínimos de hardware antes de serem designados para hospedar esse recurso. Caso contrário, dependendo da utilização de recursos de outros processos em segundo plano e da contenção provocada por runbooks durante a execução, o computador ficará sobrecarregados e causará atrasos de trabalho de runbook ou tempos limite. 

#### <a name="solution"></a>Solução
Confirme se o computador designado para executar o recurso Hybrid Runbook Worker atende aos requisitos mínimos de hardware.  Se isso acontecer, monitore a utilização de CPU e memória para determinar qualquer correlação entre o desempenho de processos do Hybrid Runbook Worker e o Windows.  Se não houver memória ou pressão da CPU, isso pode indicar a necessidade de atualizar ou adicionar mais processadores ou aumentar a memória para eliminar o afunilamento de recursos e resolver o erro. Como alternativa, selecione um recurso de computação diferente que consiga dar suporte aos requisitos mínimos e ajuste a escala quando a demanda da carga de trabalho indicar que um aumento é necessário.         

#### <a name="cause-3-runbooks-cannot-authenticate-with-local-resources"></a>Causa 3: os runbooks não podem ser autenticados com recursos locais

#### <a name="solution"></a>Solução
Verifique o log de eventos do **Microsoft SMA** para ver um evento correspondente com descrição *Processo Win32 Encerrado com o código [4294967295]*.  A causa desse erro é que você ainda não configurou a autenticação em seus runbooks ou especificou as credenciais Executar como para o grupo do Hybrid Worker.  Examine as [permissões de Runbook](automation-hrw-run-runbooks.md#runbook-permissions) para confirmar que você configurou corretamente a autenticação para seus runbooks.  

## <a name="next-steps"></a>Próximas etapas

Para ajudar a solucionar outros problemas de Automação, veja [Solucionar de problemas comuns da Automação do Azure](automation-troubleshooting-automation-errors.md) 