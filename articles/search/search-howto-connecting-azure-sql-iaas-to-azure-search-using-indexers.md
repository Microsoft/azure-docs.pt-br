---
title: Conexão de VM do Azure SQL para indexação de pesquisa
titleSuffix: Azure Cognitive Search
description: Habilite conexões criptografadas e configure o firewall para permitir conexões a SQL Server em uma VM (máquina virtual) do Azure de um indexador no Pesquisa Cognitiva do Azure.
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/19/2021
ms.openlocfilehash: 23c5d138463a52f4ff4c52b4a919b71a87b7fd6d
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802872"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-server-on-an-azure-vm"></a>Configurar uma conexão de um indexador Pesquisa Cognitiva do Azure para SQL Server em uma VM do Azure

Ao configurar um [indexador do SQL Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq) para extrair o conteúdo de um banco de dados em uma máquina virtual do Azure, são necessárias etapas adicionais para conexões seguras. 

Uma conexão do Pesquisa Cognitiva do Azure para SQL Server em uma máquina virtual é uma conexão de Internet pública. Para que as conexões seguras sejam bem sucedidos, conclua as seguintes etapas:

+ Obter um certificado de um [provedor de autoridade de certificação](https://en.wikipedia.org/wiki/Certificate_authority#Providers) para o nome de domínio totalmente qualificado da instância de SQL Server na máquina virtual

+ Instale o certificado na máquina virtual e, em seguida, habilite e configure as conexões criptografadas na VM usando as instruções neste artigo.

## <a name="enable-encrypted-connections"></a>Habilitar conexões criptografadas

O Pesquisa Cognitiva do Azure requer um canal criptografado para todas as solicitações do indexador em uma conexão de Internet pública. Esta seção lista as etapas para fazer isso funcionar.

1. Verifique as propriedades do certificado para verificar se o nome da entidade é o nome de domínio totalmente qualificado (FQDN) da VM do Azure. Você pode usar uma ferramenta como CertUtils ou o snap-in de certificados para exibir as propriedades. Você pode obter o FQDN na seção Essentials da folha de serviço da VM, no campo **rótulo de nome de DNS/endereço IP público** , no [portal do Azure](https://portal.azure.com/).
  
   + Para VMs criadas usando o mais recente modelo do **Resource Manager**, o FQDN é formatado como `<your-VM-name>.<region>.cloudapp.azure.com`

   + Para VMs antigas criadas como uma VM **clássica**, o FQDN é formatado como `<your-cloud-service-name.cloudapp.net>`.

1. Configure o SQL Server para usar o certificado usando o Editor do Registro (regedit). 

   Embora o SQL Server Configuration Manager seja geralmente usado para essa tarefa, você não pode usá-lo para esse cenário. Ele não localizará o certificado importado porque o FQDN da VM no Azure não corresponde ao FQDN conforme determinado pela VM (ele identifica o domínio como o computador local ou domínio de rede ao qual ele está associado). Quando os nomes não corresponderem, use regedit para especificar o certificado.

   + No regedit, navegue até esta chave do registro: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.

     A parte `[MSSQL13.MSSQLSERVER]` varia conforme o nome de da instância e da versão. 

   + Defina o valor da chave do **certificado** para a **impressão digital** do certificado TLS/SSL que você importou para a VM.

     Há várias maneiras de obter a impressão digital, algumas melhores do que outras. Se você copiá-la a partir do snap-in de **certificados** do MMC, você provavelmente selecionará um caractere à esquerda invisível [conforme descrito neste artigo de suporte](https://support.microsoft.com/kb/2023869/), que resulta em um erro quando tentar uma conexão. Existem várias soluções alternativas para corrigir esse problema. A maneira mais fácil é a apagar e digitar novamente o primeiro caractere da impressão digital para remover o caractere à esquerda no campo de valor de chave no regedit. Como alternativa, você pode usar outra ferramenta para copiar a impressão digital.

1. Conceder permissões à conta de serviço. 

    Verifique se a conta de serviço SQL Server recebeu a permissão apropriada na chave privada do certificado TLS/SSL. Se você ignorar essa etapa, o SQL Server não será iniciado. Você pode usar o snap-in **Certificados** ou o **CertUtils** para essa tarefa.

1. Reinicie o serviço SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Configurar a conectividade do SQL Server na VM

Depois de configurar a conexão criptografada exigida pelo Azure Pesquisa Cognitiva, há etapas de configuração adicionais intrínsecas a SQL Server em VMs do Azure. Se você ainda não fez isso, a próxima etapa é concluir a configuração usando um destes artigos:

+ Para uma VM do **Resource Manager** , veja [Conectar-se a uma Máquina Virtual do SQL Server no Azure usando o Resource Manager](../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md). 

+ Para uma VM **Clássica** , consulte [Conectar-se a uma Máquina Virtual do SQL Server no Azure clássico](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect).

Em particular, consulte a seção em cada artigo para "conectando pela internet".

## <a name="configure-the-network-security-group-nsg"></a>Configure o Grupo de Segurança de Rede (NSG)

É comum configurar o NSG e o ponto de extremidade ou a ACL (Lista de Controle de Acesso) do Azure correspondente para dar acesso à sua VM do Azure a outras pessoas. É provável que você já tenha feito isso para permitir que a lógica do seu próprio aplicativo se conecte à VM do SQL Azure. Não é diferente para uma conexão de Pesquisa Cognitiva do Azure com sua VM SQL Azure. 

Os links a seguir fornecem instruções sobre a configuração de NSG para implantações de VM. Use estas instruções para obter uma ACL de um ponto de extremidade de Pesquisa Cognitiva do Azure com base em seu endereço IP.

> [!NOTE]
> Para saber mais, consulte [O que é um Grupo de Segurança de Rede?](../virtual-network/network-security-groups-overview.md)

+ Para uma VM do **Gerenciador de Recursos** , consulte [Como criar NSGs para implantações de ARM](../virtual-network/tutorial-filter-network-traffic.md).

+ Para uma VM **Clássica** , consulte [Como criar NSGs para implantações Clássicas](/previous-versions/azure/virtual-network/virtual-networks-create-nsg-classic-ps).

O endereçamento IP pode apresentar alguns desafios que são facilmente superados se você estiver ciente do problema e possíveis soluções alternativas. As seções restantes fornecem recomendações para lidar com problemas relacionados a endereços IP na ACL.

### <a name="restrict-access-to-the-azure-cognitive-search"></a>Restringir o acesso ao Pesquisa Cognitiva do Azure

É altamente recomendável que você restrinja o acesso ao endereço IP do serviço de pesquisa e o intervalo de endereços IP da `AzureCognitiveSearch` [marca de serviço](../virtual-network/service-tags-overview.md#available-service-tags) na ACL em vez de tornar suas SQL Azure VMs abertas para todas as solicitações de conexão.

Você pode descobrir o endereço IP executando ping no FQDN (por exemplo, `<your-search-service-name>.search.windows.net` ) do seu serviço de pesquisa. Embora seja possível que o endereço IP do serviço de pesquisa seja alterado, é improvável que ele seja alterado. O endereço IP tende a ser estático durante o tempo de vida do serviço.

Você pode descobrir o intervalo de endereços IP da `AzureCognitiveSearch` [marca de serviço](../virtual-network/service-tags-overview.md#available-service-tags) usando [arquivos JSON baixáveis](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) ou por meio da [API de descoberta de marca de serviço](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview). O intervalo de endereços IP é atualizado semanalmente.

### <a name="include-the-azure-cognitive-search-portal-ip-addresses"></a>Incluir os endereços IP do portal de Pesquisa Cognitiva do Azure

Se você estiver usando o portal do Azure para criar um indexador, deverá conceder o acesso de entrada do portal à sua máquina virtual SQL Azure. Uma regra de entrada no Firewall requer que você forneça o endereço IP do Portal.

Para obter o endereço IP do portal, execute ping `stamp2.ext.search.windows.net` , que é o domínio do Gerenciador de tráfego. A solicitação atingirá o tempo limite, mas o endereço IP ficará visível na mensagem de status. Fo example, na mensagem "Pinging azsyrie.northcentralus.cloudapp.azure.com [52.252.175.48]", o endereço IP é "52.252.175.48".

> [!NOTE]
> Os clusters em regiões diferentes se conectam a diferentes gerenciadores de tráfego. Independentemente do nome de domínio, o endereço IP retornado do ping é o correto a ser usado ao definir uma regra de firewall de entrada para o portal do Azure em sua região.

## <a name="next-steps"></a>Próximas etapas

Com a configuração fora do caminho, agora você pode especificar um SQL Server na VM do Azure como a fonte de dados para um indexador de Pesquisa Cognitiva do Azure. Para obter mais informações, consulte [conectando o banco de dados SQL do Azure ao azure pesquisa cognitiva usando indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md).