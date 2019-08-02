---
title: Diagnosticando problemas de desempenho de gráficos na área de trabalho remota – Azure
description: Este artigo descreve como usar os contadores gráficos do RemoteFX em sessões de protocolo de área de trabalho remota para diagnosticar problemas de desempenho com elementos gráficos na área de trabalho virtual do Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: v-chjenk
ms.openlocfilehash: 8cd24861b9d7432a582d1b635b8ffcf0d8d2b9e6
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68233636"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>Diagnosticar problemas de desempenho de gráficos no Área de Trabalho Remota

Para diagnosticar problemas de qualidade de experiência com suas sessões remotas, os contadores foram fornecidos na seção elementos gráficos do RemoteFX do monitor de desempenho. Este artigo ajuda você a identificar e corrigir gargalos de desempenho relacionados a gráficos durante sessões de protocolo RDP (RDP) usando esses contadores.

## <a name="find-your-remote-session-name"></a>Localizar o nome da sessão remota

Você precisará do nome da sessão remota para identificar os contadores de desempenho de gráficos. Siga as instruções nesta seção para identificar sua instância de cada contador.

1. Abra o prompt de comando do Windows em sua sessão remota.
2. Execute o comando **Qwinsta** e localize o nome da sessão.
    - Se sua sessão estiver hospedada em uma VM (máquina virtual) de várias sessões: A instância de cada contador é sufixada pelo mesmo número que sufixos pelo nome da sessão, como "RDP-TCP 37".
    - Se sua sessão estiver hospedada em uma VM que dá suporte a vGPU (unidades de processamento gráfico virtual): A instância de cada contador é armazenada no servidor em vez de em sua VM. As instâncias do contador incluem o nome da VM em vez do número no nome da sessão, como "VM do win8 Enterprise".

>[!NOTE]
> Embora os contadores tenham o RemoteFX em seus nomes, eles incluem gráficos de área de trabalho remota em cenários de vGPU também.

## <a name="access-performance-counters"></a>Contadores de desempenho de acesso

Depois de determinar o nome da sessão remota, siga estas instruções para coletar os contadores de desempenho de gráficos do RemoteFX para sua sessão remota.

1. Selecione **Iniciar** > **ferramentas**administrativas monitor de desempenho. > 
2. Na caixa de diálogo **Monitor de desempenho** , expanda **ferramentas de monitoramento**, selecione **Monitor de desempenho**e, em seguida, selecione **Adicionar**.
3. Na caixa de diálogo **Adicionar contadores** , na lista **contadores disponíveis** , expanda a seção para gráficos do RemoteFX.
4. Selecione os contadores a serem monitorados.
5. Na lista **instâncias do objeto selecionado** , selecione as instâncias específicas a serem monitoradas para os contadores selecionados e, em seguida, selecione **Adicionar**. Para selecionar todas as instâncias de contador disponíveis, selecione **todas as instâncias**.
6. Depois de adicionar os contadores, selecione **OK**.

Os contadores de desempenho selecionados aparecerão na tela do monitor de desempenho.

>[!NOTE]
>Cada sessão ativa em um host tem sua própria instância de cada contador de desempenho.

## <a name="diagnose-issues"></a>Diagnosticar problemas

Os problemas de desempenho relacionados a gráficos geralmente se enquadram em quatro categorias:

- Taxa de quadros baixa
- Interrupções aleatórias
- Alta latência de entrada
- Qualidade de quadro ruim

### <a name="addressing-low-frame-rate-random-stalls-and-high-input-latency"></a>Abordando a taxa de quadros baixa, paralisações aleatórias e alta latência de entrada

Primeiro, verifique o contador de quadros de saída/segundo. Ele mede o número de quadros disponibilizados para o cliente. Se esse valor for menor que o contador de quadros de entrada/segundo, os quadros serão ignorados. Para identificar o afunilamento, use os contadores de quadros ignorados/segundo.

Há três tipos de quadros ignorados/segundo contadores:

- Quadros ignorados/segundo (recursos de servidor insuficientes)
- Quadros ignorados/segundo (recursos de rede insuficientes)
- Quadros ignorados/segundo (recursos insuficientes do cliente)

Um valor alto para qualquer um dos quadros ignorados/segundo os contadores implica que o problema está relacionado ao recurso que o contador rastreia. Por exemplo, se o cliente não decodificar e apresentar quadros na mesma taxa em que o servidor fornece os quadros, o contador de quadros ignorados/segundo (recursos de cliente insuficientes) será alto.

Se o contador de quadros/segundos de saída corresponder ao contador de quadros de entrada/segundo, ainda assim você perceberá um atraso ou parada incomum, o tempo médio de codificação poderá ser o culpado. A codificação é um processo síncrono que ocorre no servidor no cenário de vGPU (única sessão) e na VM no cenário de várias sessões. O tempo médio de codificação deve ser inferior a 33 MS. Se o tempo médio de codificação estiver abaixo de 33 MS, mas você ainda tiver problemas de desempenho, poderá haver um problema com o aplicativo ou o sistema operacional que você está usando.

Para obter mais informações sobre como diagnosticar problemas relacionados ao aplicativo, consulte [contadores de desempenho de atraso de entrada do usuário](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters).

Como o RDP dá suporte a um tempo médio de codificação de 33 MS, ele dá suporte a uma taxa de quadros de entrada de até 30 quadros/segundo. Observe que 33 MS é a taxa de quadros máxima com suporte. Em muitos casos, a taxa de quadros experimentada pelo usuário será menor, dependendo da frequência com que um quadro é fornecido ao RDP pela origem. Por exemplo, tarefas como assistir a um vídeo exigem uma taxa de quadros de entrada completa de 30 quadros/segundo, mas menos tarefas computacionalmente intensivas, como a edição infrequente de um documento, resultam em um valor muito menor para quadros de entrada/segundo sem degradação no qualidade da experiência.

### <a name="addressing-poor-frame-quality"></a>Lidando com uma qualidade de quadro ruim

Use o contador de qualidade do quadro para diagnosticar problemas de qualidade do quadro. Esse contador expressa a qualidade do quadro de saída como uma porcentagem da qualidade do quadro de origem. A perda de qualidade pode ser devido ao RemoteFX ou pode ser inerente à fonte de gráficos. Se o RemoteFX causou a perda de qualidade, o problema pode ser uma falta de recursos de rede ou de servidor para enviar conteúdo de alta fidelidade.

## <a name="mitigation"></a>Redução

Se os recursos do servidor estiverem causando o afunilamento, tente uma das seguintes abordagens para melhorar o desempenho:

- Reduza o número de sessões por host.
- Aumente a memória e os recursos de computação no servidor.
- Remova a resolução da conexão.

Se os recursos de rede estiverem causando o afunilamento, tente uma das seguintes abordagens para melhorar a disponibilidade da rede por sessão:

- Reduza o número de sessões por host.
- Use uma rede de largura de banda maior.
- Remova a resolução da conexão.

Se os recursos do cliente estiverem causando o afunilamento, tente uma das seguintes abordagens para melhorar o desempenho:

- Instale o cliente de Área de Trabalho Remota mais recente.
- Aumente a memória e os recursos de computação no computador cliente.

> [!NOTE]
> No momento, não há suporte para o contador de quadros de origem/segundo. Por enquanto, o contador de quadros/segundos de origem sempre exibirá 0.

## <a name="next-steps"></a>Próximas etapas

- Para criar uma máquina virtual do Azure otimizada para GPU, consulte [Configurar a aceleração GPU (unidade de processamento gráfico) para o ambiente de visualização de área de trabalho virtual do Windows](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu).
- Para obter uma visão geral das faixas de solução de problemas e escalonamento, consulte [visão geral da solução de problemas, comentários e suporte](https://docs.microsoft.com/azure/virtual-desktop/troubleshoot-set-up-overview).
- Para saber mais sobre o serviço de visualização, consulte [ambiente do Windows Desktop Preview](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
