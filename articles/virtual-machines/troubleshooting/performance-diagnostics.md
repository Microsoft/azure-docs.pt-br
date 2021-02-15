---
title: Diagnóstico de desempenho de máquinas virtuais do Azure | Microsoft Docs
description: Apresenta o Diagnóstico de Desempenho do Azure para Windows.
services: virtual-machines-windows
documentationcenter: ''
author: anandhms
manager: cshepard
editor: przlplx
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 9/20/2018
ms.author: anandh
ms.openlocfilehash: 70d1a52ff3326fcd3a628cc7f0f9d91edc9274e7
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655383"
---
# <a name="performance-diagnostics-for-azure-virtual-machines"></a>Diagnóstico de desempenho de máquinas virtuais do Azure

A ferramenta de diagnóstico de desempenho ajuda a solucionar problemas de desempenho que podem afetar uma VM (máquina virtual) Windows ou Linux. Cenários de solução de problemas com suporte incluem verificações rápidas sobre problemas conhecidos e melhores práticas e problemas complexos que desempenho lento da VM ou alto uso de CPU, espaço em disco ou memória.

Você pode executar o diagnóstico de desempenho diretamente do portal do Azure, no qual também pode examinar informações e um relatório em vários logs, configuração avançada e dados de diagnóstico. É recomendável que você execute o diagnóstico de desempenho e examine os dados de insights e diagnóstico antes de entrar em contato com o Suporte da Microsoft.

> [!NOTE]
> Para o Windows, o diagnóstico de desempenho tem suporte atualmente em VMs que têm o SDK do .NET versão 4,5 ou uma versão posterior instalada. Para as etapas para executar o diagnóstico de desempenho em VMs clássicas, veja [extensão de VM de Diagnóstico de Desempenho do Azure](performance-diagnostics-vm-extension.md).

## <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

### <a name="windows"></a>Windows

* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows 10
* Windows 8.1
* Windows 8

### <a name="linux"></a>Linux

- Atualmente, há suporte para as seguintes distribuições:

    | Distribuição               | Versão                                         |
    |----------------------------|-------------------------------------------------|
    | Servidor de Oracle Linux        | 6,10 [ `*` ], 7,3, 7,5, 7,6, 7,7, 7,8 |
    | CentOS                     | 6,5 [ `*` ], 7,6, 7,7, 7,8                                    |
    | RHEL                       | 7,2, 7,5, 8,0 [ `*` ], 8,1, 8,2                               |
    | Ubuntu                     | 14, 4, 16, 4, 18, 4, 20, 4                               |
    | Debian                     | 8, 9, 10 [ `*` ]                                    |
    | SLES                       | 12 SP4 [ `*` ], 12 SP5 [ `*` ], 15 [ `*` ], 15 SP1 [ `*` ], 15 SP2 [ `*` ]                                      |
    |                            |                                                   |

>[!Note]
>[ `*` ] Consulte os [problemas conhecidos](how-to-use-perfinsights-linux.md#known-issues)

## <a name="install-and-run-performance-diagnostics-on-your-vm"></a>Instalar e executar o diagnóstico de desempenho em sua VM

O diagnóstico de desempenho instala uma extensão de VM que executa uma ferramenta de diagnóstico denominada PerfInsights. O PerfInsights está disponível para [Windows](./how-to-use-perfinsights.md) e [Linux](./how-to-use-perfinsights-linux.md). Para instalar e executar o diagnóstico de desempenho, siga estas etapas:

1. Na coluna de comandos esquerda, selecione **Máquinas virtuais**.
1. Na lista de nomes de VM, selecione a VM em que você deseja executar o diagnóstico.
1. Na coluna de comandos direita, selecione **Diagnóstico de desempenho**.

    ![Captura de tela do portal do Azure com o botão de diagnóstico de desempenho de instalação realçado](media/performance-diagnostics/performance-diagnostics-install.png)

    > [!NOTE]
    > Nesta captura de tela, a folha de nomes de VM é oculta.
1. Selecione uma conta de armazenamento (opcional)

    Se você quer usar uma única conta de armazenamento para armazenar os resultados do diagnóstico de desempenho de várias VMs, pode selecionar uma conta de armazenamento clicando no botão **Configurações** na barra de ferramentas. Clique no botão **OK** depois de selecionar a conta de armazenamento.

    Se você não especificar uma conta de armazenamento, uma nova conta de armazenamento será criada por padrão.

    ![Folha de diagnóstico de captura de tela de desempenho com o botão de barra de ferramentas de configurações realçado](media/performance-diagnostics/settings-button.png)

    ![Captura de tela da seleção de conta de armazenamento na folha de configurações de diagnóstico de desempenho](media/performance-diagnostics/select-storage-account.png)

1. Selecione o botão **Instalar o diagnóstico de desempenho**.
1. Marque a caixa de seleção **Executar diagnóstico** se você quiser executar um diagnóstico após a instalação ser concluída. Se você fizer essa seleção, poderá escolher o cenário de análise de desempenho e as opções relacionadas.

    ![Captura de tela do botão de Instalação de diagnóstico de desempenho](media/performance-diagnostics/install-diagnostics-button.png)

## <a name="select-an-analysis-scenario-to-run"></a>Selecione um cenário de análise para executar

Os cenários de análise a seguir estão disponíveis no portal do Azure. Selecione uma análise dependendo do problema de desempenho que você está enfrentando. Selecione as opções de rastreamento e de duração conforme necessário para a análise.

* **Análise rápida de desempenho**  
    Verifica se há problemas conhecidos, analisa as práticas recomendadas e coleta dados de diagnóstico. Essa análise leva vários minutos para ser executada. Saiba mais [Windows](./how-to-use-perfinsights.md) ou [Linux](./how-to-use-perfinsights-linux.md)

* **Análise de desempenho**  
    Inclui todas as verificações na análise de desempenho rápido e monitora o alto consumo de recursos. Use esta versão para solucionar problemas de desempenho geral, como alta utilização da CPU, memória e uso do disco. Essa análise demora 30 segundos para 15 minutos, dependendo da duração selecionada. Saiba mais [Windows](./how-to-use-perfinsights.md) ou [Linux](./how-to-use-perfinsights-linux.md)

* **Análise de desempenho avançada**`*`  
    Inclui todas as verificações na análise de desempenho e coleta de um ou mais dos rastreamentos conforme listado nas seções a seguir. Use este cenário para solucionar problemas complexos que exigem rastreamentos adicionais. Executar esse cenário por períodos mais longos aumentará o tamanho geral da saída de diagnóstico, dependendo do tamanho da VM e das opções de rastreamento selecionadas. Essa análise demora 30 segundos para 15 minutos para ser executada, dependendo da duração selecionada. [Saiba mais](./how-to-use-perfinsights.md)

* **Análise de arquivos do Azure**`*`  
    Inclui todas as verificações na análise de desempenho e captura um rastreamento de rede e contadores de SMB. Use este cenário para solucionar problemas de desempenho de Arquivos do Azure. Essa análise demora 30 segundos para 15 minutos para ser executada, dependendo da duração selecionada. [Saiba mais](./how-to-use-perfinsights.md)

>[!Note]
>[ `*` ] Esses cenários de análise só têm suporte no Windows.

![Captura de tela do painel Executar diagnóstico na folha Diagnóstico de desempenho](media/performance-diagnostics/run-diagnostics-pane.png)

### <a name="provide-symptoms-optional"></a>Fornecer sintomas (opcional)

Selecione qualquer sintoma pré-selecionado na lista ou adicione novos sintomas. Isso nos ajudará a melhorar a análise no futuro.

### <a name="provide-support-request-number-if-available-optional"></a>Forneça o número de solicitação de suporte, se disponível (opcional)

Se você estiver trabalhando com um engenheiro de suporte da Microsoft em um tíquete de suporte existente, forneça o número do tíquete de suporte.

### <a name="review-the-privacy-policy-and-legal-terms-and-select-the-check-box-to-acknowledge-required"></a>Examine a política de privacidade e os termos legais e marque a caixa de seleção para reconhecer (obrigatório)

Para executar o diagnóstico, você deve concordar com os termos legais e aceitar a política de privacidade.

### <a name="select-ok-to-run-the-diagnostics"></a>Selecione OK para executar o diagnóstico

Uma notificação é exibida quando o diagnóstico de desempenho começa a ser instalado. Depois que a instalação for concluída, você verá uma notificação indicando que a instalação foi bem-sucedida. A análise selecionada é executada pela duração especificada. Este é um bom momento para reproduzir o problema de desempenho para que os dados de diagnóstico possam ser capturados no momento certo.

Depois que a análise for concluída, os seguintes itens serão carregados para tabelas do Azure e um contêiner de BLOB (objeto binário grande) na conta de armazenamento especificada:

* Todos os insights e informações relacionadas sobre a execução
* Um arquivo compactado de saída (. zip) (chamado **PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.zip** ) no Windows e um arquivo de bola tar (chamado **PerformanceDiagnostics_yyyy-mm-dd_hh-mm-SS-FFF. tar. gz** ) no Linux que contém arquivos de log
* Um relatório HTML

Após o upload, um novo relatório de diagnóstico é listado no portal do Azure.

![Captura de tela de uma lista do relatório de diagnóstico na folha de diagnóstico de Desempenho](media/performance-diagnostics/diagnostics-report-list.png)

## <a name="how-to-change-performance-diagnostics-settings"></a>Como alterar as configurações de diagnóstico de desempenho

Use o botão de barra de ferramentas **Configurações** para alterar a conta de armazenamento na qual os insights e a saída de diagnóstico podem ser armazenados. Você pode usar a mesma conta de armazenamento para várias VMs que usam o diagnóstico de desempenho. Quando você altera a conta de armazenamento, os relatórios e insights antigos não são excluídos. No entanto, não serão exibidos na lista de relatórios de diagnóstico.

## <a name="review-insights-and-performance-diagnostics-report"></a>Examinar insights e relatório de diagnóstico de desempenho

Cada execução de diagnóstico contém uma lista de insights e recomendações, recursos afetados, arquivos de log e outras informações de diagnóstico avançadas coletadas, além de um relatório para exibição offline. Para obter uma lista completa de todos os dados de diagnóstico coletados, consulte **que tipo de informação é coletada pelo PerfInsights?** no [Windows](how-to-use-perfinsights.md#what-kind-of-information-is-collected-by-perfinsights) ou no [Linux](how-to-use-perfinsights-linux.md#what-kind-of-information-is-collected-by-perfinsights).

### <a name="select-a-performance-diagnostics-report"></a>Selecionar um relatório de diagnóstico de desempenho

Você pode usar a lista de relatórios de diagnóstico para localizar todos os relatórios de diagnóstico que foram executados. A lista inclui detalhes sobre a análise usada, insights encontrados e seus níveis de impacto. Selecione uma linha para exibir mais detalhes.

![Captura de tela da seleção de um relatório de diagnóstico na folha de diagnóstico de desempenho](media/performance-diagnostics/select-report.png)

### <a name="review-a-performance-diagnostics-report"></a>Examinar um relatório de diagnóstico de desempenho

Cada relatório de diagnóstico de desempenho pode conter várias informações e indicar um nível de impacto de alto, médio ou baixo. Cada insight também contém recomendações para ajudar a diminuir a preocupação. Os insights são agrupados para filtragem fácil.

Níveis de impacto representam a possibilidade de problemas de desempenho com base em fatores como configuração incorreta, problemas conhecidos ou os problemas relatados por outros usuários. Talvez você ainda não esteja encontrando um ou mais dos problemas indicados. Por exemplo, você pode ter arquivos de log do SQL e arquivos de banco de dados no mesmo disco de dados. Esta condição tem um alto potencial de gargalos e outros problemas de desempenho se o uso do banco de dados for alto, porém, você poderá não observar um problema se a utilização for baixa.

![Captura de tela da folha Visão geral do relatório de diagnóstico de desempenho](media/performance-diagnostics/performance-diagnostics-report-overview.png)

### <a name="reviewing-performance-diagnostics-insights-and-recommendations"></a>Examinar recomendações e insights de diagnóstico de desempenho

Você pode selecionar um insight para exibir mais detalhes sobre os recursos afetados, atenuações sugeridas e links de referência.

![Captura de tela de um detalhe de insight de diagnóstico de Desempenho](media/performance-diagnostics/insight-detail.png)

### <a name="download-and-review-the-full-performance-diagnostics-report"></a>Baixe e examine o relatório de diagnóstico de desempenho completo

Você pode usar o botão **Baixar relatório** para baixar um relatório HTML que contém informações adicionais de diagnóstico avançado, como configuração de rede e de armazenamento, contadores de desempenho, rastreamentos, lista de processos e logs. O conteúdo depende da análise selecionada. Para solução de problemas avançada, o relatório pode conter informações adicionais e gráficos interativos relacionados a alto uso da CPU, uso excessivo do disco e processos que consomem memória excessiva. Para obter mais informações sobre o relatório de diagnóstico de desempenho, consulte [Windows](how-to-use-perfinsights.md#review-the-diagnostics-report) ou [Linux](how-to-use-perfinsights-linux.md#review-the-diagnostics-report).

## <a name="manage-performance-diagnostics-reports"></a>Gerenciar relatórios de diagnóstico de desempenho

Você pode excluir um ou mais relatórios de diagnóstico de desempenho usando o botão **Excluir relatório**.

## <a name="how-to-uninstall-performance-diagnostics"></a>Como desinstalar o diagnóstico de desempenho

Você pode desinstalar o diagnóstico de desempenho de uma VM. Essa ação remove a extensão de VM, mas não afeta os dados de diagnóstico que estão na conta de armazenamento.

![Captura de tela da barra de ferramentas de folha de Diagnóstico de desempenho com o botão Desinstalar realçado](media/performance-diagnostics/uninstal-button.png)

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="where-is-the-diagnostics-data-from-my-vm-stored"></a>Onde estão os dados de diagnóstico da minha VM armazenada

Todos os relatórios e análises de desempenho de diagnóstico são armazenados em sua própria conta de armazenamento. Insights são armazenados dentro de tabelas do Azure. O arquivo compactado de relatórios é armazenado em um contêiner de BLOB (objeto binário grande) denominado azdiagextnresults.

Você pode exibir as informações da conta de armazenamento usando o botão Configurações na barra de ferramentas.

### <a name="how-do-i-share-this-data-with-microsoft-customer-support"></a>Como fazer compartilhar esses dados com o atendimento ao cliente da Microsoft

Há várias maneiras de compartilhar o relatório de diagnóstico com a Microsoft.

**Opção 1:** compartilhar automaticamente o relatório mais recente  
Quando você abre um tíquete de suporte com a Microsoft, é importante compartilhar o relatório de diagnóstico de desempenho. Se você tiver optado por compartilhar essas informações com a Microsoft enquanto executa o diagnóstico (marcando a caixa de seleção "**Concordo compartilhar informações de diagnóstico com a Microsoft**"), a Microsoft poderá acessar o relatório da sua conta de armazenamento usando um link SAS para o arquivo zip de saída por até 30 dias da data de execução. Somente o relatório mais recente estará disponível para o engenheiro de suporte.

**Opção 2:** gerar uma assinatura de acesso compartilhado para o arquivo compactado de relatório de diagnóstico  
Você pode compartilhar um link para o arquivo compactado de relatórios usando Assinaturas de Acesso Compartilhado. Para fazer isso, execute estas etapas:

1. No portal do Azure, navegue até a conta de armazenamento na qual os dados de diagnóstico são armazenados.
1. Selecione **Blobs** na seção **Serviço Blob**.
1. Selecione o contêiner **azdiagextnresults**.
1. Selecione o arquivo compactado de saída de diagnóstico de desempenho que você deseja compartilhar.
1. Na guia **Gerar SAS**, selecione os critérios para compartilhamento.
1. Clique em **Gerar URL e token SAS do blob**.
1. Copie a **URL de SAS do Blob** e compartilhe-a com o engenheiro de suporte.

**Opção 3:** baixar o relatório da conta de armazenamento

Você também pode localizar o arquivo compactado do relatório de diagnóstico de desempenho usando as etapas 1 a 4 na Opção 2. Selecione esta opção para baixar o arquivo e, em seguida, compartilhá-lo por email ou pedir ao engenheiro de suporte instruções para carregar o arquivo.  

### <a name="how-do-i-capture-the-diagnostics-data-at-the-correct-time"></a>Como fazer capturar os dados de diagnóstico no horário correto

Cada execução de diagnóstico de desempenho tem duas etapas:

1. Instalar ou atualizar a extensão de VM de diagnóstico de desempenho.
1. Executar o diagnóstico pela duração especificada.

No momento, não há nenhuma maneira fácil de saber exatamente quando a instalação da extensão da VM está concluída. Geralmente, são necessários cerca de 45 segundos a 1 minuto para instalar a extensão de VM. Depois de instalar a extensão de VM, você pode executar as etapas de reprodução para que o diagnóstico de desempenho capture o conjunto correto de dados para solução de problemas.

## <a name="next-steps"></a>Próximas etapas

Depois de examinar os insights e o relatório de diagnóstico de desempenho, se você ainda não conseguir determinar a causa do problema e precisar de mais ajuda, poderá abrir um tíquete de suporte com o atendimento ao cliente Microsoft.

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione **obter suporte**. Para obter informações sobre como usar o suporte do Azure, leia as [perguntas frequentes sobre suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).
