---
title: Controle de instâncias grandes do Azure HANA por meio de portal do Azure | Microsoft Docs
description: Descreve a maneira como você pode identificar e interagir com instâncias grandes do Azure HANA por meio do portal
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2d64fe6c244ffcb6da2926dfea6efaa6da315727
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234464"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Controle do HANA em Instâncias Grandes do Azure por meio do portal do Azure
Este documento aborda a maneira como as [instâncias grandes do Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) são apresentadas em [portal do Azure](https://portal.azure.com) e quais atividades podem ser conduzidas por meio de portal do Azure com unidades de instância grande do Hana que são implantadas para você. A visibilidade do HANA em instâncias grandes no portal do Azure é fornecida por meio de um provedor de recursos do Azure para instâncias grandes do HANA, que atualmente está em visualização pública

## <a name="register-hana-large-instance-resource-provider"></a>Registrar o provedor de recursos de instância grande do HANA
Normalmente, sua assinatura do Azure que você estava usando para implantações de instância grande do HANA está registrada para o provedor de recursos de instância grande do HANA. No entanto, se você não puder ver as unidades de instância grande do HANA implantadas, deverá registrar o provedor de recursos em sua assinatura do Azure. Há duas maneiras de registrar o provedor de recursos de instância grande do HANA

### <a name="register-through-cli-interface"></a>Registrar por meio da interface da CLI
Você precisa estar conectado à sua assinatura do Azure, usado para a implantação do SAP HANA em instâncias grandes por meio da interface CLI do Azure. Você pode (re-) registrar o provedor de instância grande do HANA com este comando:
    
    az provider register --namespace Microsoft.HanaOnAzure

Para obter mais informações, consulte o artigo [provedores de recursos e tipos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-cli)


### <a name="register-through-azure-portal"></a>Registrar-se por meio de portal do Azure
Você pode (re-) registrar o provedor de recursos de instância grande do HANA por meio de portal do Azure. Você precisa listar sua assinatura no portal do Azure e clicar duas vezes na assinatura, que foi usada para implantar suas unidades de instância grande do HANA. Você está na página de visão geral de sua assinatura, selecione "provedores de recursos", conforme mostrado abaixo e digite "HANA" na janela de pesquisa. 

![Registrar o HLI RP por meio de portal do Azure](./media/hana-li-portal/portal-register-hli-rp.png)

Na captura de tela mostrada, o provedor de recursos já estava registrado. Caso o provedor de recursos ainda não esteja registrado, pressione "registrar novamente" ou "registrar".

Para obter mais informações, consulte o artigo [provedores de recursos e tipos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-powershell)


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Exibição de unidades de instância grande do HANA no portal do Azure
Ao enviar uma solicitação de implantação de instância grande do HANA, você será solicitado a especificar a assinatura do Azure que você está se conectando às instâncias grandes do HANA também. É recomendável usar a mesma assinatura que você está usando para implantar a camada de aplicativo SAP que funciona em unidades de instância grande do HANA.
Como suas primeiras instâncias grandes do HANA estão sendo implantadas, um novo [grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) é criado na assinatura do Azure que você enviou na solicitação de implantação para suas instâncias grandes do Hana.  O novo grupo de recursos listará todas as unidades do SAP HANA em instâncias grandes implantadas na assinatura específica.

Para localizar o novo grupo de recursos do Azure, você lista o grupo de recursos em sua assinatura navegando pelo painel de navegação esquerdo da portal do Azure

![Painel de navegação no portal do Azure](./media/hana-li-portal/portal-resource-group.png)

Na lista de grupos de recursos, você está sendo listado, talvez seja necessário filtrar a assinatura que você usou para ter o HANA em instâncias grandes implantadas

![Filtrar grupos de recursos no portal do Azure](./media/hana-li-portal/portal-filtering-subscription.png)

Depois de filtrar a assinatura correta, você ainda poderá ter uma longa lista de grupos de recursos. Procure um com um post-Fix de **-TXXX,** onde "XXX" são três dígitos, como **-T050**. 

Como você encontrou o grupo de recursos, liste os detalhes dele. A lista que você recebeu poderia ser semelhante a:

![Lista de HLI no portal do Azure](./media/hana-li-portal/portal-hli-units-list.png)

Todas as unidades listadas representam uma única unidade de instância grande do HANA que foi implantada em sua assinatura. Nesse caso, você examina oito unidades de instância grande HANA diferentes, que foram implantadas em sua assinatura.

Se você implantou vários locatários de instância grande do HANA na mesma assinatura do Azure, encontrará vários grupos de recursos do Azure 


## <a name="look-at-attributes-of-single-hli-unit"></a>Examinar os atributos de uma unidade HLI única
Na lista de unidades de instância grande do HANA, você pode clicar em uma única unidade e obter os detalhes da única unidade de instância grande do HANA. 

Na tela Visão geral, depois de clicar em ' mostrar mais ', você está obtendo uma apresentação da unidade, que se parece com:

![Mostrar visão geral de uma unidade de HLI](./media/hana-li-portal/portal-show-overview.png)

Observando os diferentes atributos mostrados, esses atributos parecem ser praticamente diferentes dos atributos de VM do Azure. No cabeçalho do lado esquerdo, ele mostra o grupo de recursos, a região do Azure, o nome da assinatura e a ID, bem como algumas marcas que você adicionou. Por padrão, as unidades de instância grande do HANA não têm nenhuma marca atribuída. No lado direito do cabeçalho, o nome da unidade é listado como atribuído quando a implantação foi feita. O sistema operacional é mostrado, bem como o endereço IP. Assim como acontece com as VMs, o tipo de unidade de instância grande do HANA com o número de threads de CPU e memória também é mostrado. Mais detalhes sobre as diferentes unidades de instância grande do HANA são mostrados aqui:

- [SKUs disponíveis para HLI](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [Arquitetura de armazenamento de SAP HANA (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

Dados adicionais no lado inferior direito é a revisão do carimbo de instância grande do HANA. Os valores possíveis são:

- Revisão 3
- Revisão 4

A revisão 4 é a arquitetura mais recente liberada de instâncias grandes HANA com grandes melhorias na latência de rede entre as VMs do Azure e as unidades de instância grande do HANA implantadas na revisão 4 carimbos ou linhas.
Outra informação muito importante é encontrada no canto inferior direito da visão geral com o nome do grupo de posicionamento de proximidade do Azure criado automaticamente para cada unidade de instância grande do HANA implantada. Esse grupo de posicionamento de proximidade precisa ser referenciado ao implantar as VMs do Azure que hospedam a camada de aplicativo SAP. Usando o [grupo de posicionamento de proximidade do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) associado à unidade de instância grande do Hana, certifique-se de que as VMs do Azure sejam implantadas de perto à unidade de instância grande do Hana. O modo como os grupos de posicionamento de proximidade podem ser usados para localizar a camada de aplicativo SAP no mesmo datacenter do Azure, pois a revisão 4 unidades de instância grande do HANA hospedadas é descrita em [grupos de posicionamento de proximidade do Azure para latência de rede ideal com aplicativos SAP ](sap-proximity-placement-scenarios.md).

Um campo adicional na coluna à direita do cabeçalho informa sobre o estado de energia da unidade de instância grande do HANA.

> [!NOTE]
> O estado de energia descreve se a unidade de hardware está ligada ou desativada. Ele não fornece informações sobre o sistema operacional estar em execução. Ao reiniciar uma unidade de instância grande do HANA, você terá um pequeno tempo em que o estado da unidade muda para **começar** a passar para o estado de **iniciado**. Estar no estado **iniciado** significa que o sistema operacional está sendo inicializado ou que o sistema operacional foi iniciado completamente. Como resultado, após uma reinicialização da unidade, você não pode esperar fazer logon imediatamente na unidade assim que o estado muda para **iniciado**.
> 

Se você pressionar ' Ver mais ', serão mostradas informações adicionais. Uma informação adicional é exibir a revisão do carimbo de instância grande do HANA, a unidade foi implantada no. Consulte o artigo [o que é SAP Hana no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) para as diferentes revisões de carimbos de instância grande do Hana

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>Verificar atividades de uma única unidade de instância grande do HANA 
Além de fornecer uma visão geral das unidades de instância grande do HANA, você pode verificar as atividades da unidade específica. Um log de atividades poderia ser semelhante a:

![Painel de navegação no portal do Azure](./media/hana-li-portal/portal-activity-list.png)

Uma das principais atividades registradas é reinicializações de uma unidade. Os dados listados incluem o status da atividade, o carimbo de data/hora que a atividade foi disparada, a ID da assinatura da qual a atividade foi disparada e o usuário do Azure que disparou a atividade. 

Outra atividade que está sendo registrada é alterada para a unidade nos metadados do Azure. Além da reinicialização iniciada, você pode ver a atividade de **gravação HANAInstances**. Esse tipo de atividade não executa nenhuma alteração na própria unidade de instância grande do HANA, mas está documentando as alterações nos metadados da unidade no Azure. No caso listado, adicionamos e excluímos uma marca (consulte a próxima seção).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>Adicionar e excluir uma marca do Azure para uma unidade de instância grande do HANA
Outra possibilidade é adicionar uma [marca](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) a uma unidade de instância grande do Hana. A maneira como as marcas estão sendo atribuídas não difere da atribuição de marcas às VMs. Assim como ocorre com as VMs, as marcas existem nos metadados do Azure e, para instâncias grandes do HANA, têm as mesmas restrições que as marcas para VMs.

A exclusão de marcas funciona da mesma maneira que as VMs. As duas atividades, a aplicação e a exclusão de uma marca serão listadas no log de atividades da unidade de instância grande do HANA específica.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>Verifique as propriedades de uma unidade de instância grande do HANA
As **Propriedades** da seção incluem informações importantes que você obtém quando as instâncias são passadas para você. É uma seção em que você obtém todas as informações que você pode exigir em casos de suporte ou que você precisa ao configurar a configuração de instantâneo de armazenamento. Dessa forma, essa seção é uma coleção de dados em sua instância, a conectividade da instância do Azure e o back-end de armazenamento. A parte superior da seção é semelhante a:


![parte superior das propriedades de HLI no portal do Azure](./media/hana-li-portal/portal-properties-top.png)

Os primeiros itens de dados, você viu na tela Visão geral já. Mas uma parte importante dos dados é a ID do circuito do ExpressRoute, que você obteve à medida que as primeiras unidades implantadas foram passadas. Em alguns casos de suporte, você pode ser solicitado a fornecer esses dados. Uma entrada de dados importante é mostrada na parte inferior da captura de tela. Os dados exibidos são o endereço IP do cabeçalho de armazenamento NFS que isola o armazenamento para seu **locatário** na pilha de instância grande do Hana. Esse endereço IP também é necessário quando você edita o [arquivo de configuração para backups de instantâneo de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots). 

À medida que você rola para baixo no painel de propriedades, obtém dados adicionais como uma ID de recurso exclusiva para sua unidade de instância grande do HANA ou a ID da assinatura que foi atribuída à implantação.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Reiniciar uma unidade de instância grande do HANA por meio de portal do Azure
Iniciando uma reinicialização do sistema operacional Linux, havia várias situações em que o sistema operacional não pôde concluir uma reinicialização com êxito. Para forçar uma reinicialização, você precisava abrir uma solicitação de serviço para fazer com que as operações da Microsoft executem uma reinicialização de energia da unidade de instância grande do HANA. A funcionalidade de uma reinicialização de energia de uma unidade de instância grande do HANA agora está integrada à portal do Azure. Como você está na parte da visão geral da unidade de instância grande do HANA, você vê o botão para reiniciar na parte superior da seção de dados

![Reinicie a etapa #1 em portal do Azure](./media/hana-li-portal/portal-restart-first-step.png)

Quando você estiver pressionando o botão reiniciar, será perguntado se você realmente deseja reiniciar a unidade. Ao confirmar o pressionamento do botão "Sim", a unidade será reiniciada.

> [!NOTE]
> No processo de reinicialização, você terá um pequeno tempo em que o estado da unidade é alterado para **começar** a passar para o estado de **iniciado**. Estar no estado **iniciado** significa que o sistema operacional está sendo inicializado ou que o sistema operacional foi iniciado completamente. Como resultado, após uma reinicialização da unidade, você não pode esperar fazer logon imediatamente na unidade assim que o estado muda para **iniciado**.

> [!IMPORTANT]
> Dependendo da quantidade de memória em sua unidade de instância grande do HANA, uma reinicialização e reinicialização do hardware e do sistema operacional podem levar até uma hora


## <a name="open-a-support-request-for-hana-large-instances"></a>Abrir uma solicitação de suporte para instâncias grandes do HANA
Fora do portal do Azure exibição de unidades de instância grande do HANA, você pode criar solicitações de suporte especificamente para uma unidade de instância grande do HANA também. Ao seguir o link **nova solicitação de suporte** 

![Iniciar etapa de solicitação de serviço #1 em portal do Azure](./media/hana-li-portal/portal-initiate-support-request.png)

Para obter o serviço de SAP HANA em Instâncias Grandes listado na próxima tela, talvez seja necessário selecionar ' todos os serviços ', conforme mostrado abaixo

![Selecionar todos os serviços no portal do Azure](./media/hana-li-portal/portal-create-service-request.png)

Na lista de serviços, você pode encontrar o serviço **SAP Hana instância grande**. Ao escolher esse serviço, você pode selecionar tipos de problema específicos, conforme mostrado:


![Selecione a classe problemática no portal do Azure](./media/hana-li-portal/portal-select-problem-class.png)

Em cada um dos diferentes tipos de problema, é oferecida uma seleção de subtipos de problema que você precisa selecionar para caracterizar seu problema ainda mais. Depois de selecionar o subtipo, agora você pode nomear o assunto. Depois de concluir o processo de seleção, você poderá passar para a próxima etapa da criação. Na seção **soluções** , você é apontado para a documentação do SAP Hana em instâncias grandes, que pode dar um ponteiro para uma solução do seu problema. Se você não encontrar uma solução para o problema na documentação sugerida, vá para a próxima etapa. Na próxima etapa, você será perguntado se o problema é com as VMs ou com as unidades de instância grande do HANA. Essas informações ajudam a direcionar a solicitação de suporte para os especialistas corretos. 

![Detalhes do caso de suporte no portal do Azure](./media/hana-li-portal/portal-support-request-details.png)

Conforme você respondeu às perguntas e forneceu detalhes adicionais, pode ir para a próxima etapa para revisar a solicitação de suporte e enviá-la.

## <a name="next-steps"></a>Próximas etapas

- [Como monitorar SAP HANA (instâncias grandes) no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [Monitoramento e solução de problemas no lado do HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)

