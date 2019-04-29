---
title: Gerenciar serviços Web
titleSuffix: Azure Machine Learning Studio
description: Gerencie seus serviços Web clássicos e novos de aprendizado de máquina usando o portal de serviços Web do Microsoft Azure Machine Learning. Como os serviços Web clássicos e os novos serviços Web têm base em tecnologias subjacentes diferentes, você tem recursos de gerenciamento um pouco diferentes para cada um deles.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/28/2017
ms.openlocfilehash: 711cb674cb00a880eadda11b03da87631df90b0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60861728"
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-studio-web-services-portal"></a>Gerenciar um serviço Studio Web usando o portal Microsoft Azure Machine Learning Studio
Com o portal de Serviços Web do Microsoft Azure Machine Learning, você pode gerenciar seus serviços Web novos e clássicos de Machine Learning. Como os serviços Web clássicos e os novos serviços Web têm base em tecnologias subjacentes diferentes, você tem recursos de gerenciamento um pouco diferentes para cada um deles.

No portal de Serviços Web do Azure Machine Learning você pode:

* Monitorar como o serviço Web está sendo usado.
* Configurar a descrição, atualizar as chaves para o serviço Web (somente Novo), atualizar sua chave de conta de armazenamento (somente Novo), habilitar o registro em log (somente Clássico) e habilitar ou desabilitar dados de exemplo.
* Excluir o serviço Web.
* Criar, excluir ou atualizar planos de cobranças (somente Novo).
* Adicionar e excluir pontos de extremidade (somente Clássico)

>[!NOTE]
>Você também pode gerenciar serviços Web clássicos no [Machine Learning Studio](https://studio.azureml.net), na guia **Serviços Web**.

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Permissões para gerenciar os Novos serviços Web baseados no Resource Manager

Os Novos serviços Web são implantados como recursos do Azure. Assim, você deve ter as permissões corretas para implantar e gerenciar Novos serviços Web.  Para implantar ou gerenciar Novos serviços Web, você deverá ser atribuído a uma função colaborador ou administrador na assinatura na qual o serviço Web é implantado. Se você convidar outro usuário para um workspace do Machine Learning, deverá atribuí-lo a uma função colaborador ou administrador na assinatura antes de implantar ou gerenciar os serviços Web. 

Se o usuário não tiver as permissões corretas para acessar os recursos no portal dos Serviços Web do Azure Machine Learning, ele receberá o seguinte erro ao tentar implantar um serviço Web:

*A implantação de serviço Web falhou. Essa conta não tem acesso suficiente à assinatura do Azure que contém o Workspace. Para implantar um serviço Web no Azure, a mesma conta deve ser convidada para o Workspace e receber acesso à assinatura do Azure que contém o Workspace.*

Para saber mais sobre como criar um workspace, confira [Criar e compartilhar um workspace do Azure Machine Learning Studio](create-workspace.md).

Para obter mais informações sobre como definir permissões de acesso, confira [Gerenciar o acesso usando o RBAC e o portal do Azure](../../role-based-access-control/role-assignments-portal.md).


## <a name="manage-new-web-services"></a>Gerenciar novos serviços Web
Para gerenciar seus novos serviços Web:

1. Entre no [Portal de Serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) usando sua conta do Microsoft Azure. Use a conta que está associada à assinatura do Azure.
2. No menu, clique em **serviços Web**.

Isso exibe uma lista de serviços Web implantados para sua assinatura. 

Para gerenciar um serviço Web, clique em Serviços Web. Na página Serviços Web, você pode:

* Clicar no serviço Web para gerenciá-lo.
* Clicar no Plano de Faturamento para o serviço Web para atualizá-lo.
* Excluir um serviço Web.
* Copiar um serviço Web e implantá-lo em outra região.

Quando você clica em um serviço Web, abre a página de início rápido do serviço Web. A página de início rápido do serviço Web tem duas opções de menu que permitem que você gerencie seu serviço Web:

* **PAINEL** – permite exibir o uso do serviço Web.
* **CONFIGURAR** – permite adicionar um texto descritivo, atualizar a chave da conta de armazenamento associada ao serviço Web e habilitar ou desabilitar dados de exemplo.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorando como o serviço Web está sendo usado
Clique na guia **PAINEL** .

No painel, você pode exibir o uso geral do serviço Web em um período de tempo. Você pode selecionar o período para exibir no menu suspenso Período no canto superior direito dos gráficos de uso. O painel mostra as seguintes informações:

* **Solicitações ao longo do tempo** exibe um grafo de etapa do número de solicitações ao longo do período selecionado. Ele pode ajudar a identificar se houver picos de uso.
* **Solicitações de solicitação-resposta** exibe o número total de chamadas de solicitação-resposta que o serviço recebeu no período de tempo selecionado e quantos deles falharam.
* **Tempo médio de computação de solicitação-resposta** exibe uma média do tempo necessário para executar as solicitações recebidas.
* **Solicitações de lote** exibe o número total de chamadas de lote que o serviço recebeu no período de tempo selecionado e quantos deles falharam.
* **Latência média de trabalho** exibe uma média do tempo necessário para executar as solicitações recebidas.
* **Erros** exibe o número total de erros que ocorreram em chamadas para o serviço Web.
* **Custos de serviços** exibe os encargos para o plano de faturamento associado ao serviço.

### <a name="configuring-the-web-service"></a>Configurando o serviço Web
Clique na opção de menu **CONFIGURAR** .

Você pode atualizar as seguintes propriedades:

* **Descrição** permite inserir uma descrição para o serviço Web.
* **Título** permite inserir um título para o serviço Web
* **Chaves** permite girar as chaves de API principais e secundárias.
* **Chave da conta de armazenamento** permite atualizar a chave da conta de armazenamento associada às alterações de serviço Web. 
* **Habilitar dados de Exemplo** permite que você forneça dados de exemplo que podem ser usados para testar o seu serviço de Solicitação-Resposta. Se você criou o serviço Web no Machine Learning Studio, os dados de exemplo são retirados dos dados usados para treinar seu modelo. Se você criou o serviço programaticamente, os dados foram extraídos dos dados de exemplo fornecidos como parte do pacote JSON.

### <a name="managing-billing-plans"></a>Gerenciando planos de cobranças
Clique na opção de menu **Planos** da página de início rápido de serviços Web. Você também pode clicar no plano associado com o serviço Web específico para gerenciar o plano.

* **Novo** permite que você crie um novo plano.
* **Adicionar/remover instância de plano** permite que você "escale horizontalmente "um plano existente para aumentar a capacidade.
* **Atualização/Downgrade** permite que você "escale verticalmente" um plano existente para aumentar a capacidade.
* **Excluir** permite que você exclua um plano.

Clique em um plano para exibir o painel. O painel fornece um instantâneo ou plano de uso durante um determinado período de tempo. Clique no menu suspenso **Período** na parte superior direita das informações do painel para selecionar o período de tempo. 

O painel de plano fornece as seguintes informações:

* **Descrição do plano** exibe informações sobre os custos e a capacidade associada ao plano.
* **Uso do plano** exibe o número de transações e as horas de computação que serão cobradas em relação ao plano.
* **Serviços Web** exibe o número de serviços Web que estão usando este plano.
* **Principais serviços Web por chamadas** exibe os quatro principais serviços Web que estão fazendo chamadas que são cobradas em relação ao plano.
* **Principais serviços Web por horas de computação** exibe os quatro principais serviços Web que estão usando recursos de computação que são cobrados em relação ao plano.

## <a name="manage-classic-web-services"></a>Gerenciar Serviços Web clássicos
> [!NOTE]
> Os procedimentos nesta seção são relevantes para o gerenciamento de serviços Web clássicos no portal de Serviços Web do Azure Machine Learning. Para saber mais sobre como gerenciar serviços Web clássicos por meio do Machine Learning Studio e do Portal do Azure, consulte [Gerenciar um workspace do Azure Machine Learning Studio](manage-workspace.md).
> 
> 

Para gerenciar seus serviços Web clássicos:

1. Entre no [Portal de Serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) usando sua conta do Microsoft Azure. Use a conta que está associada à assinatura do Azure.
2. No menu, clique em **Serviços Web Clássicos**.

Para gerenciar um serviço Web clássico, clique em **Serviços Web Clássicos**. Na página Serviços Web Clássicos, você pode:

* Clicar no serviço Web para exibir os pontos de extremidade associados.
* Excluir um serviço Web.

Ao gerenciar um serviço Web clássico, você gerencia cada um dos pontos de extremidade separadamente. Quando você clica em um serviço Web na página Serviços Web, a lista de pontos de extremidade associados ao serviço é aberta. 

Na página do ponto de extremidade do Serviço Web Clássico, você pode adicionar e excluir pontos de extremidade do serviço. Para saber mais sobre a adição de pontos de extremidade, veja [Criação de pontos de extremidade](create-endpoint.md).

Clique em um dos pontos de extremidade para abrir a página de Início Rápido do serviço Web. Na página de Início rápido há duas opções de menu que permitem que você gerencie seu serviço Web:

* **PAINEL** – permite exibir o uso do serviço Web.
* **CONFIGURAR** – permite adicionar um texto descritivo, ativar e desativar o log de erros, atualizar a chave da conta de armazenamento associada ao serviço Web e habilitar e desabilitar dados de exemplo.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorando como o serviço Web está sendo usado
Clique na guia **PAINEL** .

No painel, você pode exibir o uso geral do serviço Web em um período de tempo. Você pode selecionar o período para exibir no menu suspenso Período no canto superior direito dos gráficos de uso. O painel mostra as seguintes informações:

* **Solicitações ao longo do tempo** exibe um grafo de etapa do número de solicitações ao longo do período selecionado. Ele pode ajudar a identificar se houver picos de uso.
* **Solicitações de solicitação-resposta** exibe o número total de chamadas de solicitação-resposta que o serviço recebeu no período de tempo selecionado e quantos deles falharam.
* **Tempo médio de computação de solicitação-resposta** exibe uma média do tempo necessário para executar as solicitações recebidas.
* **Solicitações de lote** exibe o número total de chamadas de lote que o serviço recebeu no período de tempo selecionado e quantos deles falharam.
* **Latência média de trabalho** exibe uma média do tempo necessário para executar as solicitações recebidas.
* **Erros** exibe o número total de erros que ocorreram em chamadas para o serviço Web.
* **Custos de serviços** exibe os encargos para o plano de faturamento associado ao serviço.

### <a name="configuring-the-web-service"></a>Configurando o serviço Web
Clique na opção de menu **CONFIGURAR** .

Você pode atualizar as seguintes propriedades:

* **Descrição** permite inserir uma descrição para o serviço Web. Descrição é um campo obrigatório.
* **Registrar em log** permite habilitar ou desabilitar o registro de erros em log no ponto de extremidade. Para obter mais informações sobre Registrar em Log, veja Habilitar [registro em log de serviços Web do Machine Learning](web-services-logging.md).
* **Habilitar dados de Exemplo** permite que você forneça dados de exemplo que podem ser usados para testar o seu serviço de Solicitação-Resposta. Se você criou o serviço Web no Machine Learning Studio, os dados de exemplo são retirados dos dados usados para treinar seu modelo. Se você criou o serviço programaticamente, os dados foram extraídos dos dados de exemplo fornecidos como parte do pacote JSON.


