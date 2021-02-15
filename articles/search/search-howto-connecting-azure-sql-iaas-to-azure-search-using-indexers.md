---
title: Conexão de VM do Azure SQL para indexação de pesquisa
titleSuffix: Azure Cognitive Search
description: Habilite conexões criptografadas e configure o firewall para permitir conexões a SQL Server em uma VM (máquina virtual) do Azure de um indexador no Pesquisa Cognitiva do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.openlocfilehash: ec1e74c6a029ab0f8defc3ae783c9e974f387289
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88922966"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-server-on-an-azure-vm"></a>Configurar uma conexão de um indexador Pesquisa Cognitiva do Azure para SQL Server em uma VM do Azure

Conforme observado na [conexão do banco de dados SQL do Azure ao azure pesquisa cognitiva usando indexadores, a](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)criação de indexadores em relação a **SQL Server em VMs do azure** (ou **SQL Azure VMs** para curto) é suportada pelo Azure pesquisa cognitiva, mas há alguns pré-requisitos relacionados à segurança que devem ser considerados primeiro. 

As conexões do Pesquisa Cognitiva do Azure para SQL Server em uma VM são uma conexão de Internet pública. Todas as medidas de segurança que você normalmente seguiria para essas conexões se aplicam aqui também:

+ Obter um certificado de um [provedor de Autoridade de Certificação](https://en.wikipedia.org/wiki/Certificate_authority#Providers) para o nome de domínio totalmente qualificado da instância do SQL Server na VM do Azure.
+ Instalar o certificado na VM e, em seguida, habilitar e configurar conexões criptografadas na VM usando as instruções neste artigo.

## <a name="enable-encrypted-connections"></a>Habilitar conexões criptografadas
O Pesquisa Cognitiva do Azure requer um canal criptografado para todas as solicitações do indexador em uma conexão de Internet pública. Esta seção lista as etapas para fazer isso funcionar.

1. Verifique as propriedades do certificado para verificar se o nome da entidade é o nome de domínio totalmente qualificado (FQDN) da VM do Azure. Você pode usar uma ferramenta como CertUtils ou o snap-in de certificados para exibir as propriedades. Você pode obter o FQDN na seção Essentials da folha de serviço da VM, no campo **rótulo de nome de DNS/endereço IP público** , no [portal do Azure](https://portal.azure.com/).
   
   * Para VMs criadas usando o mais recente modelo do **Resource Manager**, o FQDN é formatado como `<your-VM-name>.<region>.cloudapp.azure.com`
   * Para VMs antigas criadas como uma VM **clássica**, o FQDN é formatado como `<your-cloud-service-name.cloudapp.net>`.

2. Configure o SQL Server para usar o certificado usando o Editor do Registro (regedit). 
   
    Embora o SQL Server Configuration Manager seja geralmente usado para essa tarefa, você não pode usá-lo para esse cenário. Ele não localizará o certificado importado porque o FQDN da VM no Azure não corresponde ao FQDN conforme determinado pela VM (ele identifica o domínio como o computador local ou domínio de rede ao qual ele está associado). Quando os nomes não corresponderem, use regedit para especificar o certificado.
   
   * No regedit, navegue até esta chave do registro: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.
     
     A parte `[MSSQL13.MSSQLSERVER]` varia conforme o nome de da instância e da versão. 
   * Defina o valor da chave do **certificado** para a **impressão digital** do certificado TLS/SSL que você importou para a VM.
     
     Há várias maneiras de obter a impressão digital, algumas melhores do que outras. Se você copiá-la a partir do snap-in de **certificados** do MMC, você provavelmente selecionará um caractere à esquerda invisível [conforme descrito neste artigo de suporte](https://support.microsoft.com/kb/2023869/), que resulta em um erro quando tentar uma conexão. Existem várias soluções alternativas para corrigir esse problema. A maneira mais fácil é a apagar e digitar novamente o primeiro caractere da impressão digital para remover o caractere à esquerda no campo de valor de chave no regedit. Como alternativa, você pode usar outra ferramenta para copiar a impressão digital.

3. Conceder permissões à conta de serviço. 
   
    Verifique se a conta de serviço SQL Server recebeu a permissão apropriada na chave privada do certificado TLS/SSL. Se você ignorar essa etapa, o SQL Server não será iniciado. Você pode usar o snap-in **Certificados** ou o **CertUtils** para essa tarefa.
    
4. Reinicie o serviço SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Configurar a conectividade do SQL Server na VM
Depois de configurar a conexão criptografada exigida pelo Azure Pesquisa Cognitiva, há etapas de configuração adicionais intrínsecas a SQL Server em VMs do Azure. Se você ainda não fez isso, a próxima etapa é concluir a configuração usando qualquer um destes artigos:

* Para uma VM do **Resource Manager** , veja [Conectar-se a uma Máquina Virtual do SQL Server no Azure usando o Resource Manager](../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md). 
* Para uma VM **Clássica** , consulte [Conectar-se a uma Máquina Virtual do SQL Server no Azure clássico](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect).

Em particular, consulte a seção em cada artigo para "conectando pela internet".

## <a name="configure-the-network-security-group-nsg"></a>Configure o Grupo de Segurança de Rede (NSG)
É comum configurar o NSG e o ponto de extremidade ou a ACL (Lista de Controle de Acesso) do Azure correspondente para dar acesso à sua VM do Azure a outras pessoas. É provável que você já tenha feito isso para permitir que a lógica do seu próprio aplicativo se conecte à VM do SQL Azure. Não é diferente para uma conexão de Pesquisa Cognitiva do Azure com sua VM SQL Azure. 

Os links a seguir fornecem instruções sobre a configuração de NSG para implantações de VM. Use estas instruções para obter uma ACL de um ponto de extremidade de Pesquisa Cognitiva do Azure com base em seu endereço IP.

> [!NOTE]
> Para saber mais, consulte [O que é um Grupo de Segurança de Rede?](../virtual-network/security-overview.md)
> 
> 

* Para uma VM do **Gerenciador de Recursos** , consulte [Como criar NSGs para implantações de ARM](../virtual-network/tutorial-filter-network-traffic.md). 
* Para uma VM **Clássica** , consulte [Como criar NSGs para implantações Clássicas](/previous-versions/azure/virtual-network/virtual-networks-create-nsg-classic-ps).

O endereçamento IP pode apresentar alguns desafios que são facilmente superados se você estiver ciente do problema e possíveis soluções alternativas. As seções restantes fornecem recomendações para lidar com problemas relacionados a endereços IP na ACL.

#### <a name="restrict-access-to-the-azure-cognitive-search"></a>Restringir o acesso ao Pesquisa Cognitiva do Azure
É altamente recomendável que você restrinja o acesso ao endereço IP do serviço de pesquisa e o intervalo de endereços IP da `AzureCognitiveSearch` [marca de serviço](../virtual-network/service-tags-overview.md#available-service-tags) na ACL em vez de tornar suas SQL Azure VMs abertas para todas as solicitações de conexão.

Você pode descobrir o endereço IP executando ping no FQDN (por exemplo, `<your-search-service-name>.search.windows.net` ) do seu serviço de pesquisa.

Você pode descobrir o intervalo de endereços IP da `AzureCognitiveSearch` [marca de serviço](../virtual-network/service-tags-overview.md#available-service-tags) usando [arquivos JSON baixáveis](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) ou por meio da [API de descoberta de marca de serviço](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview). O intervalo de endereços IP é atualizado semanalmente.

#### <a name="managing-ip-address-fluctuations"></a>Gerenciando flutuações de endereço IP
Se o seu serviço de pesquisa tiver apenas uma unidade de pesquisa (isto é, uma réplica e uma partição), o endereço IP poderá mudar durante a reinicialização do serviço de rotina, invalidando uma ACL existente com o endereço IP do seu serviço de pesquisa.

Uma maneira de evitar o erro de conectividade subsequente é usar mais de uma réplica e uma partição no Azure Pesquisa Cognitiva. Isso aumenta o custo, mas também resolve o problema de endereço IP. No Azure Pesquisa Cognitiva, os endereços IP não são alterados quando você tem mais de uma unidade de pesquisa.

Uma segunda abordagem é permitir que a conexão falhe e, em seguida, reconfigurar as ACLs no NSG. Em média, você pode esperar que os endereços IP sejam alterados todas as semanas. Para clientes que fazem a indexação controlada raramente, essa abordagem pode ser viável.

Uma terceira abordagem viável (mas não particularmente segura) é especificar o intervalo de endereços IP da região do Azure em que o serviço de pesquisa é provisionado. A lista de intervalos IP do qual os endereços IP públicos são alocados a recursos do Azure é publicada em [intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-cognitive-search-portal-ip-addresses"></a>Incluir os endereços IP do portal de Pesquisa Cognitiva do Azure
Se você estiver usando o portal do Azure para criar um indexador, a lógica do portal de Pesquisa Cognitiva do Azure também precisará acessar a VM do SQL Azure durante a criação. Os endereços IP do portal de Pesquisa Cognitiva do Azure podem ser encontrados por meio do ping `stamp2.search.ext.azure.com` .

## <a name="next-steps"></a>Próximas etapas
Com a configuração fora do caminho, agora você pode especificar um SQL Server na VM do Azure como a fonte de dados para um indexador de Pesquisa Cognitiva do Azure. Consulte [conectando o banco de dados SQL do Azure ao azure pesquisa cognitiva usando indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) para obter mais informações.