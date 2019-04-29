---
title: Conexão da VM (máquina virtual) do SQL do Azure para indexação de pesquisa – Azure Search
description: Habilite conexões criptografadas e configure o firewall para permitir conexões com o SQL Server em uma VM (máquina virtual) do Azure de um indexador no Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/04/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 90e5a133bac519cbc5ab2d7b112d51a019e8f698
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871273"
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-server-on-an-azure-vm"></a>Configurar uma conexão de um indexador do Azure Search ao SQL Server em uma VM do Azure
Conforme observado em [Conectando o Banco de Dados SQL do Azure ao Azure Search usando indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), é possível criar indexadores no **SQL Server em VMs do Azure** (ou **VMs do SQL Azure**) usando o Azure Search, mas há alguns pré-requisitos relacionados à segurança que precisam ser verificados primeiro. 

Conexões do Azure Search ao SQL Server em uma máquina virtual é uma conexão de Internet pública. Todas as medidas de segurança que você normalmente seguiria para essas conexões se aplicam aqui também:

+ Obter um certificado de um [provedor de Autoridade de Certificação](https://en.wikipedia.org/wiki/Certificate_authority#Providers) para o nome de domínio totalmente qualificado da instância do SQL Server na VM do Azure.
+ Instalar o certificado na VM e, em seguida, habilitar e configurar conexões criptografadas na VM usando as instruções neste artigo.

## <a name="enable-encrypted-connections"></a>Habilitar conexões criptografadas
O Azure Search requer um canal criptografado para todas as solicitações de indexador em uma conexão de Internet pública. Esta seção lista as etapas para fazer isso funcionar.

1. Verifique as propriedades do certificado para verificar se o nome da entidade é o nome de domínio totalmente qualificado (FQDN) da VM do Azure. Você pode usar uma ferramenta como CertUtils ou o snap-in de certificados para exibir as propriedades. Você pode obter o FQDN na seção Essentials da folha de serviço da VM, no campo **rótulo de nome de DNS/endereço IP público** , no [portal do Azure](https://portal.azure.com/).
   
   * Para VMs criadas usando o mais recente modelo do **Resource Manager**, o FQDN é formatado como `<your-VM-name>.<region>.cloudapp.azure.com`
   * Para VMs antigas criadas como uma VM **clássica**, o FQDN é formatado como `<your-cloud-service-name.cloudapp.net>`.

2. Configure o SQL Server para usar o certificado usando o Editor do Registro (regedit). 
   
    Embora o SQL Server Configuration Manager seja geralmente usado para essa tarefa, você não pode usá-lo para esse cenário. Ele não localizará o certificado importado porque o FQDN da VM no Azure não corresponde ao FQDN conforme determinado pela VM (ele identifica o domínio como o computador local ou domínio de rede ao qual ele está associado). Quando os nomes não corresponderem, use regedit para especificar o certificado.
   
   * No regedit, navegue até esta chave do registro: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.
     
     A parte `[MSSQL13.MSSQLSERVER]` varia conforme o nome de da instância e da versão. 
   * Defina o valor da chave do **Certificado** para a **impressão digital** do certificado SSL que você importou para a VM.
     
     Há várias maneiras de obter a impressão digital, algumas melhores do que outras. Se você copiá-la a partir do snap-in de **certificados** do MMC, você provavelmente selecionará um caractere à esquerda invisível [conforme descrito neste artigo de suporte](https://support.microsoft.com/kb/2023869/), que resulta em um erro quando tentar uma conexão. Existem várias soluções alternativas para corrigir esse problema. A maneira mais fácil é a apagar e digitar novamente o primeiro caractere da impressão digital para remover o caractere à esquerda no campo de valor de chave no regedit. Como alternativa, você pode usar outra ferramenta para copiar a impressão digital.

3. Conceder permissões à conta de serviço. 
   
    Verifique se a conta de serviço do SQL Server recebeu permissão apropriada na chave privada do certificado SSL. Se você ignorar essa etapa, o SQL Server não será iniciado. Você pode usar o snap-in **Certificados** ou o **CertUtils** para essa tarefa.
    
4. Reinicie o serviço SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Configurar a conectividade do SQL Server na VM
Depois de configurar a conexão criptografada exigida pelo Azure Search, existem etapas de configuração adicionais intrínsecas ao SQL Server nas VMs do Azure. Se você ainda não fez isso, a próxima etapa é concluir a configuração usando qualquer um destes artigos:

* Para uma VM do **Resource Manager** , veja [Conectar-se a uma Máquina Virtual do SQL Server no Azure usando o Resource Manager](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md). 
* Para uma VM **Clássica** , consulte [Conectar-se a uma Máquina Virtual do SQL Server no Azure clássico](../virtual-machines/windows/classic/sql-connect.md).

Em particular, consulte a seção em cada artigo para "conectando pela internet".

## <a name="configure-the-network-security-group-nsg"></a>Configure o Grupo de Segurança de Rede (NSG)
É comum configurar o NSG e o ponto de extremidade ou a ACL (Lista de Controle de Acesso) do Azure correspondente para dar acesso à sua VM do Azure a outras pessoas. É provável que você já tenha feito isso para permitir que a lógica do seu próprio aplicativo se conecte à VM do SQL Azure. Uma conexão do Azure Search com sua VM do SQL Azure não é diferente. 

Os links a seguir fornecem instruções sobre a configuração de NSG para implantações de VM. Use estas instruções para a ACL de um ponto de extremidade do Azure Search com base em seu endereço IP.

> [!NOTE]
> Para saber mais, consulte [O que é um Grupo de Segurança de Rede?](../virtual-network/security-overview.md)
> 
> 

* Para uma VM do **Gerenciador de Recursos** , consulte [Como criar NSGs para implantações de ARM](../virtual-network/tutorial-filter-network-traffic.md). 
* Para uma VM **Clássica** , consulte [Como criar NSGs para implantações Clássicas](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

O endereçamento IP pode apresentar alguns desafios que são facilmente superados se você estiver ciente do problema e possíveis soluções alternativas. As seções restantes fornecem recomendações para lidar com problemas relacionados a endereços IP na ACL.

#### <a name="restrict-access-to-the-search-service-ip-address"></a>Restringir o acesso ao endereço IP do serviço de pesquisa
É altamente recomendável restringir o acesso ao endereço IP do seu serviço de pesquisa na ACL, em vez de abrir totalmente as VMs do SQL Azure para todas as solicitações de conexão. Você pode descobrir facilmente o endereço IP fazendo ping do FQDN (por exemplo, `<your-search-service-name>.search.windows.net`) do seu serviço de pesquisa.

#### <a name="managing-ip-address-fluctuations"></a>Gerenciando flutuações de endereço IP
Se o seu serviço de pesquisa tiver apenas uma unidade de pesquisa (isto é, uma réplica e uma partição), o endereço IP poderá mudar durante a reinicialização do serviço de rotina, invalidando uma ACL existente com o endereço IP do seu serviço de pesquisa.

Uma maneira de evitar o erro de conectividade subsequente é usar mais de uma réplica e uma partição no Azure Search. Isso aumenta o custo, mas também resolve o problema de endereço IP. No Azure Search, os endereços IP não são alterados quando você tiver mais de uma unidade de pesquisa.

Uma segunda abordagem é permitir que a conexão falhe e, em seguida, reconfigurar as ACLs no NSG. Em média, você pode esperar que os endereços IP sejam alterados todas as semanas. Para clientes que fazem a indexação controlada raramente, essa abordagem pode ser viável.

Uma terceira abordagem viável (mas não particularmente segura) é especificar o intervalo de endereços IP da região do Azure em que o serviço de pesquisa é provisionado. A lista de intervalos IP do qual os endereços IP públicos são alocados a recursos do Azure é publicada em [intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-search-portal-ip-addresses"></a>Incluir endereços IP do portal do Azure Search
Se você estiver usando o portal do Azure para criar um indexador, a lógica do portal do Azure Search também precisará acessar a VM do SQL Azure durante a criação. Os endereços IP do portal do Azure Search podem ser encontrados executando ping de `stamp2.search.ext.azure.com`.

## <a name="next-steps"></a>Próximas etapas
Com a configuração acima resolvido, agora você pode especificar um SQL Server na VM do Azure como a fonte de dados de um indexador do Azure Search. Confira [Conectando o banco de dados SQL do Azure ao Azure Search usando indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) para obter mais informações.

