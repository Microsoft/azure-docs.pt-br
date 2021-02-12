---
title: Unidades de instância BareMetal no Azure
description: Saiba como identificar e interagir com as unidades de instância do BareMetal por meio do portal do Azure.
ms.topic: how-to
ms.date: 1/4/2021
ms.openlocfilehash: b089b45c35ff05f10ae59f8ce793645361be1e9b
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733256"
---
# <a name="manage-baremetal-instances-through-the-azure-portal"></a>Gerenciar Instâncias de BareMetal por meio do portal do Azure
 
Este artigo mostra como o [portal do Azure](https://portal.azure.com/) exibe [instâncias BareMetal](baremetal-overview-architecture.md). Este artigo também mostra as atividades que você pode fazer no portal do Azure com suas unidades de instância BareMetal implantadas. 
 
## <a name="register-the-resource-provider"></a>Registre o provedor de recursos
Um provedor de recursos do Azure para instâncias BareMetal fornece visibilidade das instâncias no portal do Azure, atualmente em visualização pública. Por padrão, a assinatura do Azure usada para implantações de instância BareMetal registra o provedor de recursos *BareMetalInfrastructure* . Se você não vir suas unidades de instância de BareMetal implantadas, deverá registrar o provedor de recursos com sua assinatura. 

Há duas maneiras de registrar o provedor de recursos de instância de BareMetal:
 
* [CLI do Azure](#azure-cli)
 
* [Portal do Azure](#azure-portal)
 
### <a name="azure-cli"></a>CLI do Azure
 
Entre na assinatura do Azure que você usa para a implantação da instância do BareMetal por meio do CLI do Azure. Você pode registrar o provedor de recursos BareMetalInfrastructure com:

```azurecli-interactive
az provider register --namespace Microsoft.BareMetalInfrastructure
```
 
Para obter mais informações, consulte o artigo [provedores de recursos e tipos do Azure](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell).
 
### <a name="azure-portal"></a>Portal do Azure
 
Você pode registrar o provedor de recursos BareMetalInfrastructure por meio do portal do Azure.
 
Você precisará listar sua assinatura no portal do Azure e clicar duas vezes na assinatura usada para implantar suas unidades de instância do BareMetal.
 
1. Entre no [portal do Azure](https://portal.azure.com).

1. No menu do portal do Azure, selecione **Todos os serviços**.

1. Na caixa **Todos os serviços** , digite **assinatura** e, em seguida, selecione **Assinaturas**.

1. Selecione a assinatura na lista de assinaturas para visualizá-la.

1. Selecione **provedores de recursos** e insira **BareMetalInfrastructure** na pesquisa. O provedor de recursos deve ser **registrado**, como mostra a imagem.
 
>[!NOTE]
>Caso o provedor de recursos não esteja registrado, clique em **Registrar**.
 
:::image type="content" source="media/baremetal-infrastructure-portal/register-resource-provider-azure-portal.png" alt-text="Captura de tela que mostra a unidade de instância BareMetal registrada":::
 
## <a name="baremetal-instance-units-in-the-azure-portal"></a>BareMetal as unidades de instância no portal do Azure
 
Ao enviar uma solicitação de implantação de instância BareMetal, você especificará a assinatura do Azure que está se conectando às instâncias de BareMetal. Use a mesma assinatura usada para implantar a camada de aplicativo que funciona em relação às unidades de instância do BareMetal.
 
Durante a implantação de suas instâncias do BareMetal, um novo [grupo de recursos do Azure](../../../azure-resource-manager/management/manage-resources-portal.md) é criado na assinatura do Azure que você usou na solicitação de implantação. Esse novo grupo de recursos lista todas as unidades de instância do BareMetal que você implantou na assinatura específica.

1. Na assinatura do BareMetal, na portal do Azure, selecione **grupos de recursos**.
 
   :::image type="content" source="media/baremetal-infrastructure-portal/view-baremetal-instance-units-azure-portal.png" alt-text="Captura de tela que mostra a lista de grupos de recursos":::

1. Na lista, localize o novo grupo de recursos.
 
   :::image type="content" source="media/baremetal-infrastructure-portal/filter-resource-groups.png" alt-text="Captura de tela que mostra a unidade de instância BareMetal em uma lista de grupos de recursos filtrados" lightbox="media/baremetal-infrastructure-portal/filter-resource-groups.png":::
   
   >[!TIP]
   >Você pode filtrar a assinatura usada para implantar a instância BareMetal. Depois de filtrar para a assinatura apropriada, você pode ter uma longa lista de grupos de recursos. Procure um com um post-Fix de **-TXXX,** em que xxx tem três dígitos como **-T250**.

1. Selecione o novo grupo de recursos para mostrar os detalhes dele. A imagem mostra uma unidade de instância de BareMetal implantada.
   
   >[!NOTE]
   >Se você implantou vários locatários da instância do BareMetal na mesma assinatura do Azure, você veria vários grupos de recursos do Azure.
 
## <a name="view-the-attributes-of-a-single-instance"></a>Exibir os atributos de uma única instância
 
Você pode exibir os detalhes de uma única unidade. Na lista da instância de BareMetal, selecione a instância única que você deseja exibir.
 
:::image type="content" source="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png" alt-text="Captura de tela que mostra os atributos de unidade da instância BareMetal de uma única instância" lightbox="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png":::
 
Os atributos na imagem não parecem muito diferentes dos atributos da VM (máquina virtual) do Azure. À esquerda, você verá o grupo de recursos, a região do Azure e o nome e a ID da assinatura. Se você atribuiu marcas, você as verá aqui também. Por padrão, as unidades da instância BareMetal não têm marcas atribuídas.
 
À direita, você verá o nome da unidade, o sistema operacional (SO), o endereço IP e a SKU que mostra o número de threads de CPU e memória. Você também verá o estado de energia e a versão de hardware (revisão do carimbo de instância BareMetal). O estado de energia indica se a unidade de hardware está ligada ou desativada. Os detalhes do sistema operacional, no entanto, não indicam se ele está ativo e em execução.
 
As possíveis revisões de hardware são:

* Revisão 3 (Rev. 3)

* Revisão 4 (Rev. 4)
 
* Revisão 4,2 (Rev 4,2)
 
>[!NOTE]
>A Rev 4,2 é a mais recente estrutura de BareMetal de marca que usa a arquitetura Rev 4 existente. A Rev 4 fornece mais proximidade com os hosts da VM (máquina virtual) do Azure. Ele tem melhorias significativas na latência de rede entre as VMs do Azure e as unidades de instância BareMetal implantadas em carimbos de Rev 4 ou linhas. Você pode acessar e gerenciar suas instâncias do BareMetal por meio do portal do Azure. Para obter mais informações, consulte [infraestrutura do BareMetal no Azure](baremetal-overview-architecture.md).
 
Além disso, no lado direito, você encontrará o nome [do grupo de posicionamento de proximidade do Azure](../../../virtual-machines/co-location.md) , que é criado automaticamente para cada unidade de instância de BareMetal implantada. Referencie o grupo de posicionamento de proximidade ao implantar as VMs do Azure que hospedam a camada de aplicativo. Ao usar o grupo de posicionamento de proximidade associado à unidade de instância BareMetal, você garante que as VMs do Azure sejam implantadas perto da unidade de instância BareMetal.
 
>[!TIP]
>Para localizar a camada de aplicativo no mesmo datacenter do Azure que a revisão 4. x, confira [grupos de posicionamento de proximidade do Azure para latência de rede ideal](../../../virtual-machines/workloads/sap/sap-proximity-placement-scenarios.md).
 
## <a name="check-activities-of-a-single-instance"></a>Verificar atividades de uma única instância
 
Você pode verificar as atividades de uma única unidade. Uma das principais atividades registradas é reinicializações da unidade. Os dados listados incluem o status da atividade, carimbo de data/hora a atividade disparada, ID da assinatura e o usuário do Azure que disparou a atividade.
 
:::image type="content" source="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png" alt-text="Captura de tela que mostra as atividades de unidade da instância BareMetal" lightbox="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png":::
 
As alterações nos metadados da unidade no Azure também são registradas no log de atividades. Além da reinicialização iniciada, você pode ver a atividade de **gravação BareMetallnstances**. Essa atividade não faz nenhuma alteração na própria unidade de instância BareMetal, mas documenta as alterações nos metadados da unidade no Azure.
 
Outra atividade que é registrada é quando você adiciona ou exclui uma [marca](../../../azure-resource-manager/management/tag-resources.md) para uma instância.
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>Adicionar e excluir uma marca do Azure para uma instância
 
Você pode adicionar marcas do Azure a uma unidade de instância BareMetal ou excluí-las. A maneira como as marcas são atribuídas não diferem da atribuição de marcas às VMs. Assim como acontece com as VMs, as marcas existem nos metadados do Azure e, para instâncias BareMetal, elas têm as mesmas restrições que as marcações para VMs.
 
A exclusão de marcas funciona da mesma maneira que as VMs. A aplicação e a exclusão de uma marca são listadas no log de atividades da unidade de instância do BareMetal.
 
## <a name="check-properties-of-an-instance"></a>Verificar Propriedades de uma instância
 
Ao adquirir as instâncias, você pode ir para a seção Propriedades para exibir os dados coletados sobre as instâncias. Os dados coletados incluem a conectividade do Azure, o back-end de armazenamento, a ID de circuito do ExpressRoute, a ID de recurso exclusiva e a ID da assinatura. Você usará essas informações em solicitações de suporte ou ao configurar a configuração de instantâneo de armazenamento.
 
Outra informação importante que você verá é o endereço IP do armazenamento NFS. Ele isola o armazenamento para seu **locatário** na pilha da instância de BareMetal. Você usará esse endereço IP ao editar o [arquivo de configuração para backups de instantâneo de armazenamento](../../../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots).
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-properties.png" alt-text="Captura de tela que mostra as configurações de propriedade da instância BareMetal" lightbox="media/baremetal-infrastructure-portal/baremetal-instance-properties.png":::
 
## <a name="restart-a-unit-through-the-azure-portal"></a>Reiniciar uma unidade por meio do portal do Azure
 
Há várias situações em que o sistema operacional não concluirá uma reinicialização, o que requer uma reinicialização de energia da unidade de instância BareMetal. Você pode reiniciar a energia da unidade diretamente do portal do Azure:
 
Selecione **reiniciar** e **Sim** para confirmar a reinicialização da unidade.
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-restart.png" alt-text="Captura de tela que mostra como reiniciar a unidade de instância BareMetal":::
 
Ao reiniciar uma unidade de instância do BareMetal, você experimentará um atraso. Durante esse atraso, o estado de energia passa do **início** ao **início**, o que significa que o sistema operacional foi iniciado completamente. Como resultado, após uma reinicialização, você não poderá fazer logon na unidade assim que o estado mudar para **iniciado**.
 
>[!IMPORTANT]
>Dependendo da quantidade de memória em sua unidade de instância do BareMetal, uma reinicialização e uma reinicialização do hardware e do sistema operacional podem levar até uma hora.
 
## <a name="open-a-support-request-for-baremetal-instances"></a>Abrir uma solicitação de suporte para instâncias do BareMetal
 
Você pode enviar solicitações de suporte especificamente para uma unidade de instância de BareMetal.
1. No portal do Azure, em **ajuda + suporte**, crie uma **[nova solicitação de suporte](https://rc.portal.azure.com/#create/Microsoft.Support)** e forneça as seguintes informações para o tíquete:
 
   - **Tipo de problema:** Selecione um tipo de problema
 
   - **Assinatura:** Selecione sua assinatura
 
   - **Serviço:** Infraestrutura de BareMetal
 
   - **Recurso:** Forneça o nome da instância
 
   - **Resumo:** Forneça um resumo de sua solicitação
 
   - **Tipo de problema:** Selecione um tipo de problema
 
   - **Subtipo de problema:** Selecione um subtipo para o problema

1. Selecione a guia **soluções** para encontrar uma solução para o problema. Se você não encontrar uma solução, vá para a próxima etapa.

1. Selecione a guia **detalhes** e selecione se o problema é com VMs ou as unidades de instância BareMetal. Essas informações ajudam a direcionar a solicitação de suporte para os especialistas corretos.

1. Indique quando o problema começou e selecione a região da instância.

1. Forneça mais detalhes sobre a solicitação e carregue um arquivo, se necessário.

1. Selecione **revisão + criar** para enviar a solicitação.
 
Leva até cinco dias úteis para que um representante de suporte confirme sua solicitação.

## <a name="next-steps"></a>Próximas etapas

Se você quiser saber mais sobre as cargas de trabalho, consulte [tipos de carga de trabalho BareMetal](../../../virtual-machines/workloads/sap/get-started.md).