---
title: Implantar o Azure Monitor para soluções SAP com o portal do Azure
description: Implantar o Azure Monitor para soluções SAP com o portal do Azure
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.date: 08/17/2020
ms.openlocfilehash: 02c0801aa0425db96a1e6f71f248c795e81b5ddf
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554052"
---
# <a name="deploy-azure-monitor-for-sap-solutions-with-azure-portal"></a>Implantar o Azure Monitor para Soluções SAP com o portal do Azure

Os recursos do Azure Monitor para Soluções SAP podem ser criados por meio do [portal do Azure](https://azure.microsoft.com/features/azure-portal). Esse método oferece uma interface do usuário baseada em navegador para implantar o Azure Monitor para Soluções SAP e configurar provedores.

## <a name="sign-in-to-azure-portal"></a>Entre no portal do Azure

Entre no portal do Microsoft Azure em https://portal.azure.com

## <a name="create-monitoring-resource"></a>Criar recurso de monitoramento

1. Selecione **Azure Monitor para Soluções SAP** no **Azure Marketplace**.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="Imagem mostra a seleção da oferta do Azure Monitor para Soluções SAP no Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. Na guia **Básico**, forneça os valores necessários. Se aplicável, você pode usar um workspace do Log Analytics existente.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="Exibição das opções de configuração do portal do Azure." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

3. Ao selecionar uma rede virtual, verifique se os sistemas que você deseja monitorar estão acessíveis de dentro dessa VNET. 

   > [!IMPORTANT]
   > A seleção de **Compartilhar** para compartilhamento de dados com a Microsoft permite que nossas equipes de suporte forneçam suporte adicional.

## <a name="configure-providers"></a>Configurar provedores

### <a name="sap-hana-provider"></a>Provedor do SAP HANA 

1. Selecione a guia **Provedor** para adicionar os provedores que você deseja configurar. Você pode adicionar vários provedores, um após o outro, ou adicioná-los depois de implantar o recurso de monitoramento. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="Mostra a guia provedor para adicionar outros provedores ao Azure Monitor para Soluções SAP." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

2. Selecione **Adicionar provedor** e escolha **SAP HANA** na lista suspensa. 

   > [!IMPORTANT]
   > Verifique se o provedor do SAP HANA está configurado para o nó "mestre" do SAP HANA.

3. Insira o IP privado do servidor do HANA.

4. Insira o nome do locatário do banco de dados que você deseja usar. Você pode escolher qualquer locatário, no entanto, é recomendável usar o **SYSTEMDB**, pois ele permite uma matriz mais ampla de áreas de monitoramento. 

5. Insira o número da porta SQL associada ao seu banco de dados HANA. O número da porta deve estar no formato **[3]**  +  **[instance#]**  +  **[13]** . Por exemplo, 30013. 

6. Insira o nome de usuário do banco de dados que você deseja usar. Verifique se o usuário do banco de dados tem as funções de **monitoramento** e **leitura de catálogo** atribuídas. 

7. Quando terminar, selecione **Adicionar provedor**. Continue a adicionar mais provedores conforme necessário ou selecione **Examinar + criar** para concluir a implantação.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="Imagem de opções de configuração ao adicionar informações do provedor." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

### <a name="high-availability-cluster-pacemaker-provider"></a>Provedor de cluster de alta disponibilidade (Pacemaker)

1. Selecione **Cluster de alta disponibilidade (Pacemaker)** na lista suspensa. 

   > [!IMPORTANT]
   > Para configurar o Provedor de cluster de alta disponibilidade (Pacemaker), verifique se ha_cluster_provider está instalado em cada nó. Para obter mais informações, confira [Exportador de cluster de alta disponibilidade](https://github.com/ClusterLabs/ha_cluster_exporter#installation)

2. Insira o ponto de extremidade Prometheus no formato http://IP:9664/metrics. 
 
3. Insira a SID (ID do sistema), o nome do host e do cluster.

4. Quando terminar, selecione **Adicionar provedor**. Continue a adicionar mais provedores conforme necessário ou selecione **Examinar + criar** para concluir a implantação.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-5.png" alt-text="Imagem mostra opções relacionadas ao provedor Pacemaker de cluster de alta disponibilidade." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-5.png":::


### <a name="os-linux-provider"></a>Provedor do sistema operacional (Linux) 

1. Selecione SO (Linux) na lista suspensa 

>[!IMPORTANT]
> Para configurar o provedor do sistema operacional (Linux), verifique se a versão mais recente do Node_Exporter está instalada em cada host (BareMetal ou VM) que você deseja monitorar. Use este [link] https://prometheus.io/download/#node_exporter) para encontrar a versão mais recente. Para obter mais informações, confira  [Node_Exporter](https://github.com/prometheus/node_exporter)

2. Insira um nome, que será o identificador da instância BareMetal.
3. Insira o ponto de extremidade do exportador de nó no formato http://IP:9100/metrics.

>[!IMPORTANT]
> Use o endereço IP privado do host do Linux. Verifique se o host e o recurso do AMS estão na mesma VNET. 

>[!Note]
> A porta do firewall "9100" deve ser aberta no host do Linux.
>Se estiver usando firewall-cmd: firewall-cmd --permanent --add-port=9100/tcp firewall-cmd --reload Se estiver usando ufw: ufw allow 9100/tcp ufw reload

>[!Tip]
> Se o host do Linux for uma VM do Azure, verifique se todos os NSGs aplicáveis permitem o tráfego de entrada na porta 9100 proveniente de "VirtualNetwork" como a origem.
 
5. Quando terminar, selecione **Adicionar provedor**. Continue a adicionar mais provedores conforme necessário ou selecione **Examinar + criar** para concluir a implantação. 


### <a name="microsoft-sql-server-provider"></a>Provedor do Microsoft SQL Server

1. Antes de adicionar o provedor do Microsoft SQL Server, você deve executar o script a seguir no SQL Server Management Studio para criar um usuário com as permissões apropriadas necessárias para configurar o provedor.

   ```sql
   USE [<Database to monitor>]
   DROP USER [AMS]
   GO
   USE [master]
   DROP USER [AMS]
   DROP LOGIN [AMS]
   GO
   CREATE LOGIN [AMS] WITH PASSWORD=N'<password>', DEFAULT_DATABASE=[<Database to monitor>], DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
   CREATE USER AMS FOR LOGIN AMS
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GRANT CONNECT TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW ANY DEFINITION TO AMS
   GRANT EXEC ON xp_readerrorlog TO AMS
   GO
   USE [<Database to monitor>]
   CREATE USER [AMS] FOR LOGIN [AMS]
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GO
   ``` 

2. Selecione **Adicionar provedor** e escolha **Microsoft SQL Server** na lista suspensa. 

3. Preencha os campos usando as informações associadas ao Microsoft SQL Server. 

4. Quando terminar, selecione **Adicionar provedor**. Continue a adicionar mais provedores conforme necessário ou selecione **Examinar + criar** para concluir a implantação.

     :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="Imagem mostra informações relacionadas à adição do provedor do Microsoft SQL Server." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [Azure Monitor para Soluções SAP](azure-monitor-overview.md)
