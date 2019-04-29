---
title: Segurança de rede no Azure Data Lake Storage Gen1 | Microsoft Docs
description: Entender como a integração de rede virtual funciona no Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/09/2018
ms.author: elsung
ms.openlocfilehash: 7d6c826df2a509ffb378809e3682073bd5ab1301
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60612603"
---
# <a name="virtual-network-integration-for-azure-data-lake-storage-gen1"></a>Integração de rede virtual para o Azure Data Lake Storage Gen1

Este artigo apresenta a integração de rede virtual para o Azure Data Lake Storage Gen1. Com a integração de rede virtual, você pode configurar suas contas para aceitar tráfego somente de redes virtuais e sub-redes específicas. 

Esse recurso ajuda a proteger sua conta do Data Lake Storage contra ameaças externas.

A integração de rede virtual para o Azure Data Lake Storage Gen1 usa a segurança do ponto de extremidade de serviço de rede virtual entre sua rede virtual e o Azure AD (Azure Active Directory) para gerar declarações de segurança adicionais no token de acesso. Essas declarações, em seguida, são usadas para autenticar sua rede virtual na conta do Data Lake Storage Gen1 e permitir o acesso.

> [!NOTE]
> Não há qualquer custo adicional associado ao uso desses recursos. Sua conta é cobrada pelo valor padrão para o Data Lake Storage Gen1. Para saber mais, confira os [preços](https://azure.microsoft.com/pricing/details/data-lake-store/?cdn=disable). Para todos os outros serviços do Azure que você usar, confira os [preços](https://azure.microsoft.com/pricing/#product-picker).

## <a name="scenarios-for-virtual-network-integration-for-data-lake-storage-gen1"></a>Cenários de integração de rede virtual para o Azure Data Lake Storage Gen1

Com a integração de rede virtual do Data Lake Storage Gen1, você pode restringir o acesso à sua conta do Data Lake Storage Gen1 em sub-redes e redes virtuais específicas. Depois que sua conta for bloqueada para a sub-rede de rede virtual especificada, outras redes virtuais/VMs no Azure não terão acesso. Funcionalmente, a integração da rede virtual do Data Lake Storage Gen1 permite o mesmo cenário dos [pontos de extremidade de serviço de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). Algumas diferenças importantes estão detalhadas nas seções a seguir. 

![Diagrama do cenário de integração de rede virtual do Data Lake Storage Gen1](media/data-lake-store-network-security/scenario-diagram.png)

> [!NOTE]
> As regras de firewall de IP existentes podem ser usadas além das regras da rede virtual para permitir também o acesso de redes locais. 

## <a name="optimal-routing-with-data-lake-storage-gen1-virtual-network-integration"></a>Roteamento ideal com a integração de rede virtual do Data Lake Storage Gen1

O principal benefício dos pontos de extremidade de serviço de rede virtual é o [roteamento ideal](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview#key-benefits) de sua rede virtual. Você pode executar a mesma otimização da rota das contas do Data Lake Storage Gen1. Use as [rotas definidas pelo usuário](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) a seguir, da rede virtual para a conta do Data Lake Storage Gen1.

**Endereço IP público do Data Lake Storage** – use o endereço IP público para suas contas do Data Lake Storage Gen1 de destino. Para identificar os endereços IP das contas do Data Lake Storage Gen1, [resolva os nomes DNS](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-connectivity-from-vnets#enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity) das contas. Crie uma entrada separada para cada endereço.

    ```azurecli
    # Create a route table for your resource group.
    az network route-table create --resource-group $RgName --name $RouteTableName
    
    # Create route table rules for Data Lake Storage public IP addresses.
    # There's one rule per Data Lake Storage public IP address. 
    az network route-table route create --name toADLSregion1 --resource-group $RgName --route-table-name $RouteTableName --address-prefix <ADLS Public IP Address> --next-hop-type Internet
    
    # Update the virtual network, and apply the newly created route table to it.
    az network vnet subnet update --vnet-name $VnetName --name $SubnetName --resource-group $RgName --route-table $RouteTableName
    ```

## <a name="data-exfiltration-from-the-customer-virtual-network"></a>Exfiltração de dados da rede virtual do cliente

Além de proteger as contas do Data Lake Storage no acesso a partir da rede virtual, convém também garantir que não haverá exfiltrações para uma conta não autorizada.

Use uma solução de firewall em sua rede virtual para filtrar o tráfego de saída com base na URL da conta de destino. Permita o acesso apenas às contas aprovadas para o Data Lake Storage Gen1.

Algumas opções disponíveis:
- [Firewall do Azure](https://docs.microsoft.com/azure/firewall/overview): [Implante e configure um firewall do Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) para sua rede virtual. Proteger o tráfego de saída do Data Lake Storage e restringi-lo à URL da conta conhecida e aprovada.
- Firewall de [solução de virtualização de rede](https://azure.microsoft.com/solutions/network-appliances/): O administrador pode permitir o uso apenas de determinados fornecedores de firewall comercial. Usar uma solução de firewall de virtualização de rede disponível no Azure Marketplace para realizar a mesma função.

> [!NOTE]
> O uso de firewalls no caminho de dados introduz mais um salto no caminho de dados. Isso pode afetar o desempenho da rede para troca de dados de ponta a ponta. A latência de conexão e a disponibilidade da taxa de transferência podem ser afetadas. 

## <a name="limitations"></a>Limitações

- Os clusters HDInsight que foram criados antes de o suporte à integração de rede virtual do Data Lake Storage Gen1 ficar disponível precisam ser recriados para dar suporte a esse novo recurso.
 
- Quando você cria um novo cluster HDInsight e seleciona uma conta do Data Lake Storage Gen1 com a integração de rede virtual habilitada, o processo falha. Primeiro, desabilite a regra da rede virtual. Ou, na folha **Redes virtuais e firewall** da conta do Data Lake Storage, selecione **Permitir o acesso de todas as redes e serviços**. Em seguida, crie o cluster HDInsight antes de, por fim, habilitar novamente a regra da rede virtual ou desmarcar a opção **Permitir o acesso de todas as redes e serviços**. Para obter mais informações, confira a seção [Exceções](#exceptions).

- A integração de rede virtual do Data Lake Storage Gen1 não funciona com [identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
  
- Dados de arquivos e pastas em sua conta do Data Lake Storage Gen1 habilitada para rede virtual não podem ser acessados do portal. Isso inclui o acesso de uma VM que esteja dentro da rede virtual e atividades como o uso do Data Explorer. As atividades de gerenciamento de conta continuam a funcionar. Dados de arquivos e pastas em sua conta do Data Lake Storage habilitada para rede virtual podem ser acessados por todos os recursos fora do portal. Esses recursos incluem acesso ao SDK, scripts do PowerShell e outros serviços do Azure quando eles não se originam no portal. 

## <a name="configuration"></a>Configuração

### <a name="step-1-configure-your-virtual-network-to-use-an-azure-ad-service-endpoint"></a>Etapa 1: Configurar a rede virtual para usar um ponto de extremidade de serviço do Azure AD

1.  Vá para o portal do Azure e entre em sua conta.
 
2.  [Crie uma rede virtual](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)em sua assinatura. Ou você pode ir para uma rede virtual existente. A rede virtual deve estar na mesma região que a conta do Data Lake Storage Gen 1.
 
3.  Na folha **Rede virtual**, escolha **Pontos de extremidade de serviço**.
 
4.  Selecione **Adicionar** para adicionar um novo ponto de extremidade de serviço.

    ![Adicionar um ponto de extremidade do serviço de rede virtual](media/data-lake-store-network-security/config-vnet-1.png)

5.  Selecione **Microsoft.AzureActiveDirectory** como o serviço do ponto de extremidade.

     ![Selecionar o ponto de extremidade de serviço Microsoft.AzureActiveDirectory](media/data-lake-store-network-security/config-vnet-2.png)

6.  Selecione as sub-redes para as quais você pretende permitir a conectividade. Selecione **Adicionar**.

    ![Escolher a sub-rede](media/data-lake-store-network-security/config-vnet-3.png)

7.  Pode levar até 15 minutos para o ponto de extremidade de serviço ser adicionado. Depois de adicionado, ele aparece na lista. Verifique se ele é exibido e se todos os detalhes estão configurados corretamente.
 
    ![Adição bem-sucedida do ponto de extremidade de serviço](media/data-lake-store-network-security/config-vnet-4.png)

### <a name="step-2-set-up-the-allowed-virtual-network-or-subnet-for-your-data-lake-storage-gen1-account"></a>Etapa 2: Configurar a rede virtual ou a sub-rede permitida para a conta do Data Lake Storage Gen1

1.  Depois de configurar sua rede virtual, [crie uma nova conta do Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account) em sua assinatura. Ou vá para uma conta existente do Data Lake Storage Gen1. A conta do Data Lake Storage Gen1 deve estar na mesma região da rede virtual.
 
2.  Selecione **Firewall e redes virtuais**.

    > [!NOTE]
    > Se você não vir **Firewall e redes virtuais** nas configurações, saia do portal. Feche o navegador e limpe seu cache. Reinicie o computador e tente novamente.

       ![Adicionar uma regra da rede virtual à conta do Data Lake Storage](media/data-lake-store-network-security/config-adls-1.png)

3.  Selecione **Redes selecionadas**.
 
4.  Selecione **Adicionar rede virtual existente**.

    ![Adicionar rede virtual existente](media/data-lake-store-network-security/config-adls-2.png)

5.  Selecione as redes virtuais e sub-redes que terão conectividade. Selecione **Adicionar**.

    ![Escolher a rede virtual e as sub-redes](media/data-lake-store-network-security/config-adls-3.png)

6.  Verifique se as redes virtuais e sub-redes aparecem corretamente na lista. Clique em **Salvar**.

    ![Salvar a nova regra](media/data-lake-store-network-security/config-adls-4.png)

    > [!NOTE]
    > Pode levar até cinco minutos para que as configurações entrem em vigor depois de salvas.

7.  [Opcional] Na página **Redes virtuais e firewall**, na seção **Firewall**, você pode permitir a conectividade de endereços IP específicos. 

## <a name="exceptions"></a>Exceções
Você pode habilitar a conectividade de serviços e máquinas virtuais do Azure fora das redes virtuais selecionadas. Na folha **Firewall e redes virtuais**, na área **Exceções**, selecione uma das duas opções:
 
- **Permitir que todos os serviços do Azure acessem esta conta do Data Lake Storage Gen1**. Essa opção permite que serviços do Azure como o Azure Data Factory, os Hubs de Eventos do Azure e todas as VMs do Azure se comuniquem com a conta do Data Lake Storage.

- **Permitir que o Azure Data Lake Analytics acesse essa conta do Data Lake Storage Gen1**. Essa opção permite a conectividade do Data Lake Analytics com essa conta do Data Lake Storage. 

  ![Exceções de firewall e rede virtual](media/data-lake-store-network-security/firewall-exceptions.png)

Recomendamos que você mantenha essas exceções desligadas. Ligue-as somente se você precisar de conectividade com esses outros serviços de fora da rede virtual.
