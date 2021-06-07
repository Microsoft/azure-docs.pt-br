---
title: Monitorar o Azure FXT Edge Filer
description: Como monitorar o status de hardware para o cache de armazenamento híbrido do Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 86e1d74d5e4ab9f6e799c73bcf0d807d0d874f21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219710"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>Monitorar o status de hardware do Azure FXT Edge Filer

O sistema de cache de armazenamento híbrido do Azure FXT Edge Filer tem várias luzes de status inseridas no chassi para ajudar os administradores a entender como o hardware está funcionando.

## <a name="system-health-status"></a>Status da integridade do sistema

Para monitorar as operações de cache em um nível superior, use o painel de controle **Painel** do software, conforme descrito no [Guia do painel do Painel de Controle](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)

## <a name="hardware-status-leds"></a>LEDS de status do hardware

Esta seção explica sobre as várias luzes de status embutidas no hardware do Azure FXT Edge Filer.

### <a name="hard-drive-status-leds"></a>LEDs de status do disco rígido

![imagem do disco rígido frontal, horizontal, com rótulos de texto explicativo 2 (canto superior esquerdo), 1 (canto inferior esquerdo) e 3 (lado direito)](media/fxt-monitor/fxt-drive-callouts.png)

Cada operadora de unidade tem dois LEDs de status: um indicador de atividade (1) e um indicador de status (2).

* O LED de atividade (1) acende quando a unidade está em uso.
* O LED de status (2) indica a condição da unidade usando os códigos na tabela a seguir.

| Estado do LED de status da unidade              | Significado  |
|-------------------------------------|----------------------------------------------------------|
| Pisca em verde duas vezes por segundo      | Identificando unidade *ou* <br> Preparando a unidade para remoção  |
| Desativada (apagada)                         | O sistema não concluiu a inicialização *ou* <br>A unidade está pronta para ser removida |
| Pisca em verde, âmbar e desliga       | A falha da unidade é prevista   |
| Pisca em âmbar quatro vezes por segundo | Falha na unidade   |
| Verde sólido                         | A unidade está online |

O lado direito da unidade (3) é rotulado com a capacidade da unidade e outras informações.

Os números das unidades estão impressos no espaço entre elas. No Azure FXT Edge Filer, a unidade 0 é aquela no canto superior esquerdo e a unidade 1, a que fica imediatamente abaixo dela. A numeração continua nesse padrão.

![foto de um compartimento de disco rígido no gabinete do FXT mostrando os números das unidades e os rótulos de capacidade](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>Painel de Controle esquerdo

O painel de controle frontal esquerdo tem vários indicadores de LED de status (1) e um indicador de integridade de sistema grande (2).

![painel de status esquerdo, com 1 rotulando indicadores de status à esquerda e 2 rotulando a luz do indicador de integridade do sistema grande à direita](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>Indicadores de status do painel de controle

Os indicadores de status à esquerda mostram uma luz âmbar sólida caso exista um erro nesse sistema. A tabela a seguir descreve as possíveis causas e soluções para os erros.

Se o erro persistir depois de tentar essas soluções, [entre em contato com o suporte](fxt-support-ticket.md) para obter ajuda.

| ícone | Descrição | Condição de erro | Soluções possíveis |
|----------------|---------------|--------------------|----------------------|
| ![ícone da unidade](media/fxt-monitor/fxt-hd-icon.jpg) | Status da unidade | Erros da unidade | Verifique o log de eventos do sistema para determinar se a unidade tem um erro ou <br>Execute o teste de diagnóstico online apropriado, então reinicie o sistema e execute o diagnóstico inserido (ePSA) ou <br>Se as unidades estiverem configuradas em uma matriz RAID, reinicie o sistema e insira o programa utilitário de configuração do adaptador de host |
|![ícone de temperatura](media/fxt-monitor/fxt-temp-icon.jpg) | Status da temperatura | Erro térmico - por exemplo, uma ventoinha falhou ou a temperatura ambiente está fora do alcance | Verifique as condições endereçáveis a seguir: <br>Uma ventoinha de resfriamento está ausente ou falhou <br>A tampa do sistema, a cobertura de ar, o módulo de memória em branco ou o colchete para preenchimento é removido <br>A temperatura ambiente é muito alta <br>O fluxo de ar externo está obstruído |
|![ícone da eletricidade](media/fxt-monitor/fxt-electric-icon.jpg) | Status elétrico | Erro elétrico - por exemplo, voltagem fora do alcance, PSU ou um regulador de voltagem com falha |  Verifique as mensagens do sistema ou do log de eventos do sistema para saber qual é o problema específico. Se houver um problema com o PSU, verifique o LED de status da PSU e recoloque a PSU, se necessário. |
|![ícone de memória](media/fxt-monitor/fxt-memory-icon.jpg) | Status da Memória | Erro de memória | Verifique o log de eventos do sistema ou as mensagens do sistema para saber o local da memória que falhou e recoloque o módulo de memória. |
|![Ícone do PCIe](media/fxt-monitor/fxt-pcie-icon.jpg) | Status do PCIe | Erro de placa PCIe | Reiniciar o sistema, atualizar os drivers da placa PCIe e reinstalar o cartão |

### <a name="system-health-status-indicator"></a>Indicador de status de integridade do sistema

O botão grande iluminado à direita do painel de controle esquerdo indica o status geral do sistema e também é usado como luz do localizador de unidade no modo ID do sistema.

Pressione o botão integridade do sistema e ID para alternar entre o modo ID do sistema e o modo de integridade do sistema.

|Estado da integridade do sistema | Condição |
|-------------------------------------------|-----------------------------------------------|
| Azul sólido | Operação normal: o sistema está ligado, funcionando normalmente e o modo ID do sistema não está ativo. <br/>Pressione o botão integridade do sistema e ID se desejar alternar para o modo ID do sistema. |
| Azul piscando | O modo de ID do sistema está ativo. Pressione o botão integridade do sistema e ID do sistema se desejar alternar para o modo de integridade. |
| Âmbar sólido | O sistema está no modo à prova de falhas. Se o problema persistir, [Serviço de Suporte e Atendimento ao Cliente Microsoft](fxt-support-ticket.md). |
| Âmbar piscando | Falha no sistema. Verifique o log de eventos do sistema para mensagens especificas de erro. Para saber mais sobre o evento e as mensagens de erro geradas pelo firmware do sistema e sobre os agentes que monitoram os componentes do sistema, consulte a página de Pesquisa de Código de Erro em qrl.dell.com. |
