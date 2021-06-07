---
title: Solucionar problemas de extensão de VM dos servidores habilitados para Arc do Azure
description: Este artigo informa como solucionar problemas e resolver questões com extensões de VM do Azure que surgem com os servidores habilitados para Arc do Azure.
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: 799e5c8ec00a894c6a54c64494edd8f259faf2dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "100584667"
---
# <a name="troubleshoot-arc-enabled-servers-vm-extension-issues"></a>Solucionar problemas de extensão de VM dos servidores habilitados para Arc

Este artigo fornece informações sobre como solucionar problemas e resolver questões que podem ocorrer durante a tentativa de implantar ou remover extensões de VM do Azure em servidores habilitados para Arc. Para obter informações gerais, consulte [gerenciar e usar extensões de VM do Azure](./manage-vm-extensions.md).

## <a name="general-troubleshooting"></a>Solução de problemas gerais

Os dados sobre o estado das implantações de extensão podem ser recuperados do portal do Azure.

As seguintes etapas de solução de problemas aplicam-se a todas as extensões da VM.

1. Para verificar o log do agente convidado, examine a atividade quando sua extensão estava sendo provisionada no `%SystemDrive%\ProgramData\GuestConfig\ext_mgr_logs` para Windows e para Linux em `/var/lib/GuestConfig/ext_mgr_logs` .

2. Verifique os logs de extensão para obter a extensão específica para obter mais detalhes no `%SystemDrive%\ProgramData\GuestConfig\extension_logs\<Extension>` Windows. A saída da extensão é registrada em um arquivo para cada extensão instalada no Linux em `/var/lib/GuestConfig/extension_logs` .

3. Verifique as seções de solução de problemas de documentação específica da extensão para obter códigos de erro, problemas conhecidos etc. Informações adicionais de solução de problemas para cada extensão podem ser encontradas na seção **solução de problemas e suporte** na visão geral da extensão. Isso inclui a descrição dos códigos de erro gravados no log. Os artigos de extensão são vinculados na [tabela de extensões](manage-vm-extensions.md#extensions).

4. Examine os logs do sistema. Verifique se há outras operações que podem ter interferido na extensão, como uma instalação de longa execução de outro aplicativo que exigia acesso exclusivo ao gerenciador de pacotes.

## <a name="troubleshooting-specific-extension-scenarios"></a>Solucionando problemas de cenários de extensão específicos

### <a name="vm-insights"></a>VM Insights

- Ao habilitar as informações de VM para um servidor habilitado para Arc do Azure, ele instala a dependência e o agente de Log Analytics. Em um computador lento ou um com uma conexão de rede lenta, é possível ver os tempos limite durante o processo de instalação. A Microsoft está realizando etapas para resolver isso no agente do computador conectado para ajudar a melhorar essa condição. No interim, uma nova tentativa da instalação pode ser realizada com sucesso.

### <a name="log-analytics-agent-for-linux"></a>Agente do Log Analytics para Linux

- A versão do agente de Log Analytics 1.13.9 (versão de extensão correspondente é 1.13.15) não está marcando corretamente os dados carregados com a ID de recurso do servidor habilitado para Arc do Azure. Embora os logs estejam sendo enviados para o serviço, quando você tenta exibir os dados do servidor habilitado selecionado depois de selecionar **logs** ou **insights**, nenhum dado é retornado. Você pode exibir seus dados executando consultas de logs de Azure Monitor ou de Azure Monitor para VMs, que têm como escopo o espaço de trabalho.

- No momento, não há suporte para algumas distribuições no agente de Log Analytics para Linux. O agente requer dependências adicionais a serem instaladas, incluindo o Python 2. Examine a matriz de suporte e os pré-requisitos [aqui](../../azure-monitor/agents/agents-overview.md#supported-operating-systems).

- O código de erro 52 na mensagem de status indica uma dependência ausente. Verifique a saída e os logs para obter mais informações sobre qual dependência está faltando.

- Se uma instalação falhar, examine a seção **solução de problemas e suporte** na visão geral da extensão. Na maioria dos casos, há um código de erro incluído na mensagem de status. Para o agente de Log Analytics para Linux, as mensagens de status são explicadas [aqui](../../virtual-machines/extensions/oms-linux.md#troubleshoot-and-support), juntamente com informações gerais de solução de problemas para essa extensão de VM.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrar seu problema aqui ou não conseguir resolvê-lo, visite um dos seguintes canais para obter mais suporte:

- Obtenha respostas de especialistas do Azure por meio do [Microsoft Q&a](/answers/topics/azure-arc.html).

- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente. O Suporte do Azure conecta a Comunidade do Azure a respostas, suporte e especialistas.

- Registrar um incidente de suporte do Azure. Acesse o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter suporte**.
