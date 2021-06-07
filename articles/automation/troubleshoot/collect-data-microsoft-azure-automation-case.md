---
title: Dados a serem coletados ao abrir um caso para a Automação do Microsoft Azure | Microsoft Docs
description: Este artigo descreve as informações a serem coletadas antes de abrir um caso para a Automação do Azure com suporte do Microsoft Azure.
services: automation
ms.subservice: ''
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.openlocfilehash: 3d2c0cf780b992d7bb464969c50d60ffa5093e8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896555"
---
# <a name="data-to-collect-when-opening-a-case-for-microsoft-azure-automation"></a>Dados a serem coletados ao abrir um caso para a Automação do Microsoft Azure

Este artigo descreve algumas informações a serem coletadas antes de abrir um caso para a Automação do Azure com suporte do Microsoft Azure. Essas informações não são necessárias para abrir o caso. No entanto, elas podem ajudar a Microsoft a solucionar seu problema mais rapidamente. Além disso, você pode ser solicitado a fornecer esses dados pelo engenheiro de suporte depois de abrir o caso.

## <a name="basic-data"></a>Dados básicos

Colete os dados básicos descritos no artigo da Base de Dados de Conhecimento [4034605 - Como capturar diagnóstico com script da Automação do Azure](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

## <a name="data-for-update-management-issues-on-linux"></a>Dados para problemas no Gerenciamento de Atualizações no Linux

1. Além dos itens listados em KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), execute a seguinte ferramenta de coleta de logs:

   [Coletor de logs do agente do OMS para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Compacte o conteúdo da pasta **/var/opt/microsoft/omsagent/run/automationworker/** e envie o arquivo compactado para o Suporte do Azure.
 
3. Verifique se a ID do espaço de trabalho para a qual o agente do Log Analytics para Linux se reporta é igual à ID do espaço de trabalho que está sendo monitorado para atualizações.

## <a name="data-for-update-management-issues-on-windows"></a>Dados para problemas no Gerenciamento de Atualizações no Windows

1. Colete dados para os itens listados em [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Exporte os seguintes logs de eventos para o formato EVTX:

   * Sistema
   * Aplicativo
   * Segurança
   * Operations Manager
   * Microsoft-SMA/Operational

3. Verifique se a ID do espaço de trabalho para a qual o agente se reporta é igual à ID do espaço de trabalho que está sendo monitorado por Atualizações do Windows.

## <a name="data-for-job-issues"></a>Dados para problemas de trabalho

1. Colete dados para os itens listados em [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Colete a ID do trabalho que tem um problema:

   1. No portal do Azure, acesse as **Contas de automação**.
   2. Selecione a Conta de automação para a qual você está solucionando problemas e anote o nome.
   3. Selecione **Trabalhos**.
   4. Escolha o trabalho que você está solucionando problemas.
   5. No painel Resumo do trabalho, procure o valor GUID em **ID do trabalho**.

   ![ID do trabalho no painel Resumo do trabalho](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. Colete uma amostra do script que você está executando.

4. Colete os arquivos de log:

   1. No portal do Azure, acesse as **Contas de automação**.
   2. Selecione a Conta de automação para a qual você está solucionando problemas.
   3. Selecione **Trabalhos**.
   4. Escolha o trabalho que você está solucionando problemas.
   5. Selecione **Todos os logs**.
   6. No painel resultante, colete os dados.

   ![Dados listados em Todos os logs](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>Dados de problemas de módulo

Além dos [itens de dados básicos](#basic-data), reúna as seguintes informações:

* As etapas que você seguiu, para que o problema possa ser reproduzido.
* Capturas de tela de qualquer mensagem de erro.
* Capturas de tela dos módulos atuais e seus números de versão.

## <a name="next-steps"></a>Próximas etapas

Se precisar de mais:

* Obtenha respostas de especialistas do Azure nos [Fóruns do Azure](https://azure.microsoft.com/support/forums/).
* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.
* Registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
