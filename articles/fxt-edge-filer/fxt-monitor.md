---
title: Monitorar o servidor de borda dados FXT do Azure
description: Como monitorar o status de hardware para o cache de armazenamento do servidor de dados do Azure FXT borda híbrido
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: e6afd90c4e5a1b0759b3de7789ec37db4c04f2c7
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827451"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>Monitorar o status de hardware de servidor de dados do Azure FXT Edge

O sistema de cache de armazenamento do servidor de dados do Azure FXT borda híbrido tem várias luzes de status dentro do chassi para ajudar os administradores a entender como o hardware está funcionando.

## <a name="system-health-status"></a>Status de integridade do sistema

Para monitorar as operações de cache em um nível superior, use o painel de controle do software **Dashboard** página, conforme descrito no [guia do painel de controle de painel](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)

## <a name="hardware-status-leds"></a>LEDs de status de hardware

Esta seção explica as várias luzes de status integradas para o hardware de servidor de dados do Azure FXT Edge.

### <a name="hard-drive-status-leds"></a>LEDs de status do disco rígido

![imagem da frente do disco rígido, horizontal, com rótulos de texto explicativo 2 (canto superior esquerdo), 1 (canto inferior esquerdo) e 3 (lado direito)](media/fxt-monitor/fxt-drive-callouts.png)

Cada companhia de unidade tem dois LEDs de status: um indicador de atividade (1) e um indicador de status (2). 

* A atividade de LED luzes (1) quando a unidade está em uso.  
* O status do LED (2) indica a condição da unidade usando os códigos na tabela a seguir.

| Estado do LED de status da unidade              | Significado  |
|-------------------------------------|----------------------------------------------------------|
| Pisca verde duas vezes por segundo      | Identifica a unidade *ou* <br> Preparando a unidade para remoção  |
| Desativado (apagada)                         | Sistema não terminou de inicialização *ou* <br>Unidade está pronta para ser removido |
| Pisca verde, âmbar e desativado       | Falha da unidade é prevista   |
| Âmbar pisca quatro vezes por segundo | Falhado de unidade   |
| Verde estável                         | Unidade está online |

O lado direito da unidade (3) é rotulado com a capacidade da unidade e outras informações.

Números de unidade são impressos no espaço entre unidades. No Azure FXT borda arquivista, unidade 0 é a parte superior esquerda da unidade e a unidade 1 é diretamente abaixo dela. A numeração continua nesse padrão. 

![foto de um compartimento no gabinete FXT, exibindo os rótulos de números e a capacidade de unidade de disco rígido](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>Painel de controle à esquerda

O painel de controle frontal esquerdo tem vários indicadores de status de LED (1) e um indicador de integridade do sistema iluminada grande (2). 

![Painel de status no lado esquerdo, com 1 rotulagem indicadores de status à esquerda e 2 para rotular o indicador de integridade de sistema grande leve à direita](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>Indicadores de status do painel de controle 

Os indicadores de status na parte esquerda mostram uma luz âmbar se há um erro no sistema. A tabela a seguir descreve as possíveis causas e soluções para os erros. 

Se você ainda terá o erro depois de tentar essas soluções [entre em contato com o suporte](fxt-support-ticket.md) para obter ajuda. 

| ícone | DESCRIÇÃO | Condição de erro | Soluções possíveis |
|----------------|---------------|--------------------|----------------------|
| ![ícone da unidade](media/fxt-monitor/fxt-hd-icon.jpg) | Status da unidade | Erro de disco | Verifique o Log de eventos do sistema para determinar se a unidade tiver um erro, ou <br>Executar o teste de diagnóstico online apropriado; Reinicie o sistema e execute o diagnóstico incorporado (ePSA), ou <br>Se as unidades são configuradas em uma matriz RAID, reinicie o sistema e insira o programa utilitário de configuração de adaptador de host |
|![ícone de temperatura](media/fxt-monitor/fxt-temp-icon.jpg) | Status da temperatura | Erro térmico - por exemplo, um falha no ventilador ou temperatura ambiente está fora do intervalo | Verifique as seguintes condições endereçável: <br>Um ventilador de resfriamento está ausente ou falhou <br>A tampa do sistema, cobertura de ar, módulo de memória em branco ou back-suporte de preenchimento é removido <br>A temperatura ambiente é muito alta <br>Fluxo de ar externo é obstruído |
|![ícone de eletricidade](media/fxt-monitor/fxt-electric-icon.jpg) | Status de energia | Erro elétrico - por exemplo, a tensão fora do intervalo, falha PSU ou um regulador com falha |  Verifique o log de eventos do sistema ou mensagens de sistema para o problema específico. Se houver um problema com a fonte de alimentação, verifique o LED de status da fonte de alimentação e recolocar a fonte de alimentação, se necessário. | 
|![ícone de memória](media/fxt-monitor/fxt-memory-icon.jpg) | Status da memória | Erro de memória | Verifique o log de eventos do sistema ou mensagens de sistema para o local da memória com falha; Recoloque o módulo de memória. |
|![Ícone de PCIe](media/fxt-monitor/fxt-pcie-icon.jpg) | Status de PCIe | Erro do cartão PCIe | Reinicie o sistema; atualizar drivers de cartão de PCIe; Reinstale a placa |


### <a name="system-health-status-indicator"></a>Indicador de status de integridade do sistema

O botão grande aceso à direita do painel de controle à esquerda indica o status geral do sistema e também é usado como uma luz de localizador de unidade no modo de ID do sistema.

Pressione o botão de ID para alternar entre o modo de ID do sistema e o modo de integridade do sistema e a integridade do sistema.

|Estado de status de integridade do sistema | Condição |
|-------------------------------------------|-----------------------------------------------|
| Azul sólido | A operação normal: o sistema está ligado, operando normalmente, e o modo de ID do sistema não está ativo. <br/>Pressione o botão de ID e a integridade do sistema, se você quiser alternar para o modo de ID do sistema. |
| Azul piscando | Modo da ID do sistema está ativo. Pressione o botão de ID de sistema e a integridade do sistema se você quiser alternar para o modo de integridade do sistema. |
| Âmbar | O sistema está no modo à prova de falhas. Se o problema persistir, [entre em contato com o suporte e atendimento ao cliente Microsoft](fxt-support-ticket.md). |
| Âmbar piscando | Falha no sistema. Verifique o log de eventos do sistema de mensagens de erro específicas. Para obter informações sobre as mensagens de erro e evento gerado pelo firmware do sistema e os agentes que monitora os componentes do sistema, consulte a página de pesquisa de código de erro em qrl.dell.com. |


