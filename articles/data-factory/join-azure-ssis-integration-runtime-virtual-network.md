---
title: "Unir o tempo de execução de integração do Azure-SSIS a uma VNET | Microsoft Docs"
description: "Saiba como unir o tempo de execução de integração do Azure-SSIS a uma rede virtual do Azure."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: spelluru
ms.openlocfilehash: 02d9102bfc9b0d8084988609282f2c78b5789e7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Unir o tempo de execução de integração do Azure-SSIS a uma rede virtual
Você deve unir o tempo de execução de integração (IR) do Azure-SSIS a uma rede virtual (VNet) do Azure se uma das seguintes condições for verdadeira: 

- Você hospeda o banco de dados de Catálogo do SSIS em uma instância gerenciada do SQL Server (versão prévia privada) que faz parte de uma VNet.
- Você deseja se conectar a armazenamentos de dados locais de pacotes SSIS em execução em um tempo de execução de integração do Azure-SSIS.

 O Azure Data Factory versão 2 (versão prévia) permite que você una o tempo de execução de integração do Azure-SSIS a uma VNet clássica. Atualmente, a VNet do Azure Resource Manager ainda não é compatível. No entanto, você pode contornar essa situação conforme mostrado na seção a seguir. 

Se os pacotes do SSIS acessam apenas armazenamentos de dados de nuvem pública, você não precisa unir o IR do Azure-SSIS a uma rede virtual. Se os pacotes do SSIS acessam armazenamentos de dados locais, você deve unir o IR do Azure-SSIS a uma VNet que esteja conectada à rede local. Se o catálogo do SSIS estiver hospedado em um banco de dados SQL do Azure que não esteja na VNet, você precisará abrir as portas apropriadas. Se o catálogo do SSIS estiver hospedado na instância gerenciada do SQL edo Azure em uma VNet clássica, você poderá unir o IR do Azure-SSIS à mesma VNet clássica (ou) a uma VNet clássica diferente que tenha conexão entre VNets clássicas com a que tem a instância gerenciada do SQL do Azure. As seções a seguir apresentam mais detalhes.  

## <a name="access-on-premises-data-stores"></a>Acessar armazenamentos de dados locais
Se os pacotes do SSIS acessam armazenamentos de dados locais, una o tempo de execução de integração do Azure-SSIS a uma VNet que esteja conectada à rede local. Alguns pontos importantes a serem considerados: 

- Se não houver nenhuma VNet existente conectada à sua rede local, primeiro crie uma [VNet clássica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para unir o tempo de execução de integração do Azure SSIS a ela. Em seguida, configure uma [conexão de gateway VPN site a site](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)/[conexão de ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) dessa VNet à sua rede local.
- Se já houver uma VNet clássica conectada à sua rede local no mesmo local que o tempo de execução de integração do Azure-SSIS, você poderá unir o tempo de execução de integração do Azure-SSIS a ela.
- Se já houver uma VNet clássica conectada à sua rede local em um local diferente do tempo de execução de integração do Azure-SSIS, você primeiro tem que criar uma [VNet clássica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para unir o tempo de execução de integração do Azure-SSIS a ela. Em seguida, configure uma conexão entre [VNets clássicas](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md).
- Se já houver uma VNet do Azure Resource Manager conectada à sua rede local, primeiro crie uma [VNet clássica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para unir o tempo de execução de integração do Azure SSIS a ela. Em seguida, configure uma conexão entre [VNet clássica e Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="domain-name-services-server"></a>Servidor de Serviços de Nome de Domínio 
Se você precisar usar seu próprio servidor de Serviços de Nome de Domínio (DNS) em uma VNet ingressada por seu tempo de execução de integração do Azure-SSIS, siga a diretriz para [certificar-se de que os nós de seu tempo de execução de integração do Azure-SSIS na VNet possam resolver pontos de extremidade do Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="network-security-group"></a>Grupo de Segurança de Rede
Se você precisar implementar o Grupo de Segurança de Rede (NSG) em uma VNet unida por seu tempo de execução de integração do Azure-SSIS, permita o tráfego de entrada/saída pelas seguintes portas:

| Portas | Direção | Protocolo de Transporte | Finalidade | Origem da entrada/destino da saída |
| ---- | --------- | ------------------ | ------- | ----------------------------------- |
| 10100<br/>20100<br/>30100  | Entrada | TCP | Os serviços do Azure usam essas portas para se comunicar com os nós de seu tempo de execução de integração do Azure-SSIS na VNet. | Internet | 
| 443 | Saída | TCP | Os nós de seu tempo de execução de integração do Azure-SSIS na VNet usam essa porta para acessar os serviços do Azure como, por exemplo, o Armazenamento do Microsoft Azure, Hub de eventos, etc. | INTERNET | 
| 1433<br/>11000-11999<br/>14000-14999  | Saída | TCP | Os nós de seu tempo de execução de integração do Azure-SSIS na VNet usam essas portas para acessar o SSISDB hospedado pelo servidor de banco de dados SQL do Azure (não aplicável ao SSISDB hospedado pela instância gerenciada do SQL do Azure). | Internet | 

## <a name="script-to-configure-vnet"></a>Script para configurar a VNet 
Você pode usar o script do PowerShell guiado [deste artigo](create-azure-ssis-integration-runtime.md) para provisionar um tempo de execução de integração do Azure-SSIS em uma VNet. O script configura automaticamente as permissões e as configurações da VNet para que você possa unir o seu tempo de execução de integração do Azure-SSIS à VNet.  


## <a name="use-portal-to-configure-vnet"></a>Usar o portal para configurar a VNet
A execução do script é a maneira mais fácil de configurar a VNet. Se você não tiver acesso para configurar essa VNet/a configuração automática falhar, o proprietário dessa VNet/você pode tentar configurá-la manualmente nas etapas a seguir:

### <a name="find-the-resource-id-for-your-azure-vnet"></a>Localize a ID de recurso da VNet do Azure.
 
1. Faça logon no [portal do Azure](https://portal.azure.com).
2. Clique em **Mais serviços**. Filtre e selecione **Redes virtuais (clássicas)**.
3. Filtre e selecione a sua **rede virtual** na lista. 
4. Na página Rede virtual (clássica), selecione **Propriedades**. 

    ![ID de recurso da rede virtual clássica](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
5. Clique no botão de copiar para que a **ID DE RECURSO** copie a ID de recurso da rede clássica para a área de transferência. Salve a ID da área de transferência no OneNote ou em um arquivo.
6. Clique em **Sub-redes** no menu à esquerda e certifique-se de que o número de **endereços disponíveis** seja maior do que os nós em seu tempo de execução de integração do Azure-SSIS.

    ![Número de endereços disponíveis na VNet](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
7. Una o **MicrosoftAzureBatch** à função **Colaborador de Máquina Virtual Clássica** da VNet. 
    1. Clique em Controle de acesso (IAM) no menu à esquerda e, em seguida, clique em **Adicionar** na barra de ferramentas.
    
        ![Controle de acesso -> Adicionar](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png) 
    2. Na página **Adicionar permissões**, selecione **Colaborador de Máquina Virtual Clássica** como **Função**. Digite **MicrosoftAzureBatch** na caixa de texto **Selecionar** e, em seguida, selecione **MicrosoftAzureBatch** na lista de resultados da pesquisa. 
    
        ![Adicionar permissões - pesquisa](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)
    3. Clique em Salvar para salvar as configurações e para fechar a página.
    
        ![Salvar as configurações de acesso](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)
    4. Confirme que você está vendo **MicrosoftAzureBatch** na lista de colaboradores.
    
        ![Confirmar o acesso do AzureBatch](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)
5. Verifique se o provedor do lote do Azure está registrado na assinatura do Azure que tem a VNet ou registre o provedor de lote do Azure. Se você já tiver uma conta de lote do Azure em sua assinatura, sua assinatura está registrada para o lote do Azure.
    1. No portal do Azure, clique em **Assinaturas** no menu à esquerda. 
    2. Selecione sua **assinatura**. 
    3. Clique em **Provedores de recursos** à esquerda e confirme se `Microsoft.Batch` é um provedor registrado. 
    
        ![confirmation-batch-registered](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

    Se você não vir `Microsoft.Batch` na lista, [crie uma conta vazia de lote do Azure](../batch/batch-account-create-portal.md) em sua assinatura para registrá-lo. Você pode excluí-lo depois. 
         


## <a name="next-steps"></a>Próximas etapas
Confira estes tópicos para obter mais informações sobre o tempo de execução do Azure-SSIS: 

- [Tempo de execução de integração do Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações conceituais sobre tempos de execução de integração em geral, incluindo o IR do Azure-SSIS. 
- [Tutorial: implantar pacotes do SSIS para o Azure](tutorial-deploy-ssis-packages-azure.md). Este artigo fornece instruções passo a passo para criar um IR do Azure-SSIS e usa um banco de dados SQL do Azure para hospedar o catálogo do SSIS. 
- [Como: Criar um tempo de execução de integração do Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo expande o tutorial e fornece instruções sobre como usar a Instância Gerenciada do SQL do Azure (visualização particular) e unir o IR para uma VNet. 
- [Monitore um IR do Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime). Este artigo mostra como recuperar informações sobre um IR do Azure-SSIS e descrições de status nas informações retornadas. 
- [Gerencie um IR do Azure-SSIS](manage-azure-ssis-integration-runtime.md). Este artigo mostra como parar, iniciar ou remover um IR do Azure-SSIS. Ele também mostra como expandir o IR do Azure-SSIS adicionando mais nós ao IR. 
