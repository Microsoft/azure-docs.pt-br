---
title: Configurar regras de aplicativo de firewall do Azure com FQDNs do SQL
description: Neste artigo, você aprenderá a configurar os FQDNs do SQL nas regras de aplicativo do firewall do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 07/19/2019
ms.author: victorh
ms.openlocfilehash: 239998f29ac9a578174c5dba547bb24ba0755505
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68318181"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Configurar regras de aplicativo de firewall do Azure com FQDNs do SQL

> [!IMPORTANT]
> As regras de aplicativo de firewall do Azure com FQDNs do SQL estão atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Agora você pode configurar regras de aplicativo de firewall do Azure com FQDNs do SQL. Isso permite que você limite o acesso de suas redes virtuais somente às instâncias do SQL Server especificadas.

Com os FQDNs do SQL, você pode filtrar o tráfego:

- Do seu VNets para um banco de dados SQL do Azure ou um SQL Data Warehouse do Azure. Por exemplo: Permitir acesso somente a *SQL-Server1.Database.Windows.net*.
- Do local para instâncias gerenciadas do SQL do Azure ou do SQL IaaS em execução no seu VNets.
- Do spoke para o spoke para instâncias gerenciadas do SQL do Azure ou a IaaS do SQL em execução no seu VNets.

Durante a visualização pública, a filtragem de FQDN do SQL tem suporte somente no [modo proxy](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) (porta 1433). Se você usar o SQL no modo de redirecionamento padrão, poderá filtrar o acesso usando a marca de serviço do SQL como parte das [regras de rede](overview.md#network-traffic-filtering-rules).
Se você usar portas não padrão para o tráfego de IaaS do SQL, poderá configurar essas portas nas regras de aplicativo de firewall.

As regras de aplicativo com FQDNs do SQL estão disponíveis atualmente em todas as regiões por meio do portal do Azure, CLI do Azure, REST e modelos.

## <a name="configure-using-azure-cli"></a>Configurar usando CLI do Azure

1. Implante um [Firewall do Azure usando CLI do Azure](deploy-cli.md).
2. Se você filtrar o tráfego para o banco de dados SQL do Azure, SQL Data Warehouse ou SQL Instância Gerenciada, verifique se o modo de conectividade SQL está definido como **proxy**. Para saber como alternar o modo de conectividade do SQL, consulte [arquitetura de conectividade do SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#change-azure-sql-database-connection-policy). 

   > [!NOTE]
   > O modo SQL *proxy* pode resultar em mais latência em comparação com o redirecionamento. Se você quiser continuar usando o modo de redirecionamento, que é o padrão para clientes que se conectam no Azure, você pode filtrar o acesso usando a [marca de serviço](service-tags.md) do SQL nas [regras de rede](tutorial-firewall-deploy-portal.md#configure-a-network-rule)do firewall.

3. Configurar uma regra de aplicativo com o FQDN do SQL para permitir o acesso a um SQL Server:

   ```azurecli
   az extension add -n azure-firewall

   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>Configurar usando o portal do Azure
1. Implante um [Firewall do Azure usando CLI do Azure](deploy-cli.md).
2. Se você filtrar o tráfego para o banco de dados SQL do Azure, SQL Data Warehouse ou SQL Instância Gerenciada, verifique se o modo de conectividade SQL está definido como **proxy**. Para saber como alternar o modo de conectividade do SQL, consulte [arquitetura de conectividade do SQL do Azure](../sql-database/sql-database-connectivity-architecture.md#change-azure-sql-database-connection-policy). 

   > [!NOTE]
   > O modo SQL *proxy* pode resultar em mais latência em comparação com o redirecionamento. Se você quiser continuar usando o modo de redirecionamento, que é o padrão para clientes que se conectam no Azure, você pode filtrar o acesso usando a [marca de serviço](service-tags.md) do SQL nas [regras de rede](tutorial-firewall-deploy-portal.md#configure-a-network-rule)do firewall.
3. Adicione a regra de aplicativo com o protocolo, a porta e o FQDN do SQL apropriados e, em seguida, selecione **salvar**.
   ![regra de aplicativo com FQDN do SQL](media/sql-fqdn-filtering/application-rule-sql.png)
4. Acesse o SQL de uma máquina virtual em uma VNet que filtra o tráfego por meio do firewall. 
5. Valide se os [logs de firewall do Azure](log-analytics-samples.md) mostram o tráfego permitido.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o SQL proxy e modos de redirecionamento, consulte [arquitetura de conectividade do banco de dados SQL do Azure](../sql-database/sql-database-connectivity-architecture.md).