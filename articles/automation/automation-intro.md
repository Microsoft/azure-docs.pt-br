---
title: Uma introdução à Automação do Azure
description: Este artigo mostra o que é a Automação do Azure e como usá-la para automatizar o ciclo de vida da infraestrutura e dos aplicativos.
services: automation
ms.subservice: process-automation
keywords: azure automation, DSC, powershell, state configuration, update management, change tracking, DSC, inventory, runbooks, python, graphical
ms.date: 10/18/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: e3986b7e8fc70f8662bed40b076897caa6165744
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182811"
---
# <a name="an-introduction-to-azure-automation"></a>Uma introdução à Automação do Azure

A Automação do Azure oferece um serviço de configuração e de automação baseado em nuvem que compatível com o gerenciamento consistente em seus ambientes Azure e não Azure. Ela é composta pela automação de processos, pelo gerenciamento de configuração, pelo gerenciamento de atualizações, por funcionalidades compartilhadas e por recursos heterogêneos. A Automação oferece controle completo durante a implantação, operações e o encerramento de cargas de trabalho e de recursos.

![Funcionalidades da Automação](media/automation-overview/automation-overview.png)

## <a name="process-automation"></a>Automação de processos

A Automação de processos na Automação do Azure permite automatizar tarefas de gerenciamento de nuvem frequentes, demoradas e propensas a erros. Esse serviço ajuda você a se concentrar em trabalhos que agregam valor. Ao reduzir erros e melhorar a eficiência, também ajuda a diminuir os custos operacionais. O ambiente operacional da automação de processos é detalhado na [Execução de runbook na Automação do Azure](automation-runbook-execution.md).

A automação de processos dá suporte à integração de serviços do Azure e outros sistemas públicos necessários para implantar, configurar e gerenciar seus processos de ponta a ponta. O serviço permite criar [runbooks](automation-runbook-types.md) graficamente, no PowerShell ou usando o Python. Usando um [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md), é possível unificar o gerenciamento por meio da orquestração entre ambientes locais. [Webhooks](automation-webhooks.md) permitem que você atenda a solicitações e garanta a entrega e operações contínuas disparando automação do ITSM, DevOps e sistemas de monitoramento. 

## <a name="configuration-management"></a>Gerenciamento de configuração

O Gerenciamento de Configuração na Automação do Azure permite o acesso a dois recursos:

* Controle de Alterações e Inventário
* State Configuration da Automação do Azure

### <a name="change-tracking-and-inventory"></a>Controle de Alterações e Inventário

O Controle de Alterações e Inventário combinam as funções de controle de alterações e inventário para permitir que você rastreie as alterações de infraestrutura de máquina virtual e de servidor. O serviço dá suporte ao controle de alterações entre serviços, daemons, software, Registro e arquivos em seu ambiente para ajudar a diagnosticar alterações indesejadas e a gerar alertas. O suporte a inventário permite consultar recursos no convidado para obter visibilidade sobre aplicativos instalados e outros itens de configuração. Para obter detalhes desse recurso, consulte [Controle de Alterações e Inventário](change-tracking/overview.md).

### <a name="azure-automation-state-configuration"></a>State Configuration da Automação do Azure

O [State Configuration da Automação do Azure](automation-dsc-overview.md) é um recurso baseado em nuvem para DSC (Desired State Configuration) do PowerShell que fornece serviços necessários para ambientes empresariais. Usando esse recurso, é possível gerenciar recursos de DSC na Automação do Azure e aplicar as configurações a máquinas virtuais ou físicas de um servidor de pull de DSC na nuvem do Azure. 

## <a name="update-management"></a>Gerenciamento de atualizações

A Automação do Azure inclui o recurso de [Gerenciamento de Atualizações](./update-management/overview.md) para sistemas Windows e Linux em ambientes híbridos. O Gerenciamento de Atualizações oferece visibilidade sobre a conformidade de atualizações no Azure, em outras nuvens e localmente. O recurso permite criar implantações agendadas que orquestram a instalação de atualizações em uma janela de manutenção definida. Se uma atualização não deve ser instalada em um computador, você pode usar a funcionalidade Gerenciamento de Atualizações para excluí-la de uma implantação.

## <a name="shared-capabilities"></a>Funcionalidades compartilhadas

A Automação do Azure oferece uma série de funcionalidades compartilhadas, incluindo recursos compartilhados, controle de acesso baseado em função, agendamento flexível, integração de controle do código-fonte, auditoria e marcação.

### <a name="shared-resources"></a><a name="shared-resources"></a>Recursos compartilhados

A Automação do Azure é composta por um conjunto de recursos compartilhados que facilitam a automatização e configurar seu ambiente em escala.

* **[Agendas](./shared-resources/schedules.md)** – disparam operações da Automação em horários predefinidos.
* **[Módulos](./shared-resources/modules.md)** – gerenciam o Azure e outros sistemas. É possível importar módulos na conta de Automação da Microsoft, de terceiros, da comunidade ou de cmdlets definidos personalizados e recursos de DSC.
* **[Galeria de módulos](automation-runbook-gallery.md)** – dá suporte à integração nativa à Galeria do PowerShell para exibir runbooks e importá-los para a conta de Automação. Com a galeria, é possível começar rapidamente a integrar e criar seus processos da galeria do PowerShell e do Microsoft Script Center.
* **[Pacotes do Python 2](python-packages.md)** – dão suporte a runbooks do Python 2 para sua conta de Automação.
* **[Credenciais](./shared-resources/credentials.md)** – armazenam com segurança informações confidenciais que podem ser usadas por runbooks e configurações em runtime.
* **[Conexões](automation-connections.md)** – armazenam pares nome-valor de informações comuns para conexões com sistemas. O autor do módulo define conexões em runbooks e configurações para uso em runtime.
* **[Certificados](./shared-resources/certificates.md)** – definem informações a serem usadas na autenticação e na proteção de recursos implantados quando acessados por runbooks ou configurações de DSC em runtime. 
* **[Variáveis](./shared-resources/variables.md)** – mantêm o conteúdo que pode ser usado em runbooks e configurações. É possível alterar valores de variável sem precisar modificar nenhum runbook ou nenhuma configuração que os referencia.

### <a name="role-based-access-control"></a>Controle de acesso baseado em função

A Automação do Azure dá suporte ao RBAC do Azure (controle de acesso baseado em função do Azure) para regular o acesso à conta de Automação e aos recursos dela. Para saber mais sobre como configurar o RBAC do Azure na conta, nos runbooks e nos trabalhos de Automação, confira [Controle de acesso baseado em função para a Automação do Azure](automation-role-based-access-control.md).

### <a name="source-control-integration"></a>Integração de controle do código-fonte

A Automação do Azure é compatível com a [integração do controle do código-fonte](source-control-integration.md). Esse recurso promove a configuração como código em que o check-in de runbooks ou configurações pode ser feito em um sistema de controle do código-fonte.

## <a name="heterogeneous-support-windows-and-linux"></a>Suporte heterogêneo (Windows e Linux)

A Automação foi criada para funcionar em seu ambiente de nuvem híbrida e também em sistemas Windows e Linux. Ela oferece uma forma consistente de automatizar e configurar cargas de trabalho implantadas e os sistemas operacionais que as executam.

## <a name="common-scenarios-for-automation"></a>Cenários comuns de Automação

A Automação do Azure dá suporte ao gerenciamento durante todo o ciclo de vida de sua infraestrutura e de seus aplicativos. Cenários comuns incluem:

* **Escrever runbooks** – crie runbooks do PowerShell, Fluxo de Trabalho do PowerShell, gráficos, Python 2 e DSC em linguagens comuns. 
* **Criar e implantar recursos** – implante máquinas virtuais em um ambiente híbrido usando runbooks e modelos do Azure Resource Manager. Integre-se a ferramentas de desenvolvimento, como Jenkins e Azure DevOps.
* **Configurar VMs** – avalie e configure computadores Windows e Linux com as configurações para a infraestrutura e o aplicativo.
* **Compartilhar conhecimento** – transfira conhecimento para o sistema sobre como a organização oferece e mantém as cargas de trabalho. 
* **Recuperar inventário** – obtenha um inventário completo de recursos implantados para direcionamento, relatórios e conformidade. 
* **Localizar alterações** – identifique alterações que podem causar erros de configuração e aprimore a conformidade operacional.
* **Monitorar** – isole alterações no computador que estão causando problemas e corrija-as ou escalone-as para sistemas de gerenciamento.
* **Proteger** – coloque computadores em quarentena se forem gerados alertas de segurança. Defina requisitos no convidado.
* **Administrar** – Configure o RBAC do Azure para equipes. Recupere recursos não utilizados.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-azure-automation"></a>Preços da Automação do Azure

É possível examinar o preço associado à Automação do Azure na página [preços](https://azure.microsoft.com/pricing/details/automation/).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar uma conta da Automação](automation-quickstart-create-account.md)