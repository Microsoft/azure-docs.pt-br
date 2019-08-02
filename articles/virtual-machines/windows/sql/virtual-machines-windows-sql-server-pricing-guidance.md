---
title: Gerenciar custos efetivamente para o SQL Server em máquinas virtuais do Azure | Microsoft Docs
description: Fornece práticas recomendadas para a escolha do modelo de preços certo de máquina virtual do SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/09/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: a872b8c34011247e68b0d459482c0599ac0426f2
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68228399"
---
# <a name="pricing-guidance-for-sql-server-azure-vms"></a>Diretrizes de preços para VMs do Azure do SQL Server

Este artigo fornece diretrizes de preços para [máquinas virtuais do SQL Server](virtual-machines-windows-sql-server-iaas-overview.md) no Azure. Há várias opções que afetam o custo, e é importante escolher a imagem certa, que equilibre os custos com os requisitos de negócios.

> [!TIP]
> Se você precisar apenas descobrir uma estimativa de custo para uma combinação específica de edição do SQL Server e de tamanho da máquina virtual, visite a página de preços para [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) ou [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux). Selecione a plataforma e a edição do SQL Server na lista **Sistema Operacional/Software**.
>
> ![Interface do usuário na página Preços de VM](./media/virtual-machines-windows-sql-server-pricing-guidance/virtual-machines-pricing-ui.png)
>
> Se preferir, use a [calculadora de preços](https://azure.microsoft.com/pricing/#explore-cost) para adicionar e configurar uma máquina virtual. 

## <a name="free-licensed-sql-server-editions"></a>Edições do SQL Server com licença gratuita

Se quiser desenvolver, testar ou criar uma prova de conceito, use o **SQL Server Developer Edition** com licença gratuita. Esta edição tem todos os recursos da edição SQL Server Enterprise, permitindo que você compile e teste qualquer tipo de aplicativo. No entanto, você não pode executar a edição de Desenvolvedor na produção. Uma VM da edição SQL Server Developer somente incorre em encargos para o custo da VM, porque não há nenhum custo de licenciamento do SQL Server associado.

Se quiser executar uma carga de trabalho leve em produção (menos de quatro núcleos, menos de 1 GB de memória, menos de 10 GB/banco de dados), use o **SQL Server Express Edition** com licença gratuita. Uma edição do SQL Server Express VM só incorre em encargos para o custo da VM.

Para essas cargas de trabalho de produção leve e desenvolvimento/teste, você também poderá economizar escolhendo uma VM menor que corresponda a essas cargas de trabalho. O DS1v2 pode ser uma boa opção em alguns cenários.

Para criar uma VM do Azure do SQL Server 2017 com uma destas imagens, acesse os seguintes links:

| Plataforma | Imagens licenciadas gratuitamente |
|---|---|
| Windows Server 2016 | [VM do Azure do SQL Server 2017 Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[VM do Azure do SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [VM do Azure do SQL Server 2017 Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[VM do Azure do SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [VM do Azure do SQL Server 2017 Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[VM do Azure do SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [VM do Azure do SQL Server 2017 Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[VM do Azure do SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Edições pagas do SQL Server

Se você tiver uma carga de trabalho de produção não leve, use uma das seguintes edições do SQL Server:

| Edição do SQL Server | Carga de trabalho |
|-----|-----|
| Web | Sites pequenos |
| Standard | Cargas de trabalho pequenas a médias |
| Enterprise | Cargas de trabalho grandes ou críticas|

Você tem duas opções de pagamento para licenciamento dessas edições do SQL Server: *pagamento por uso* ou *BYOL (traga sua própria licença)* .

## <a name="pay-per-usage"></a>Pagamento por uso

**Pagar a licença do SQL Server por uso** significa que o custo por segundo da execução da VM do Azure inclui o custo da licença do SQL Server. Você pode ver os preços para as diferentes edições do SQL Server (Web, Standard e Enterprise) na página de preços de VMs do Azure para [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) ou [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux).

O custo é o mesmo para todas as versões do SQL Server (2012 SP3 a 2017). O custo de licenciamento por segundo depende do número de vCPUs da VM.

O pagamento do licenciamento do SQL Server por uso é recomendável para:

- **Cargas de trabalho temporárias ou periódicas**. Por exemplo, um aplicativo que precisa dar suporte a um evento de alguns meses a cada ano ou à análise comercial às segundas-feiras.

- **Cargas de trabalho com tempo de vida ou escala desconhecidos**. Por exemplo, um aplicativo que pode não ser necessário por alguns meses, ou que pode exigir mais, ou menos capacidade de computação, dependendo da demanda.

Para criar uma VM do Azure do SQL Server 2017 com uma dessas imagens de pagamento por uso, acesse os seguintes links:

| Plataforma | Imagens licenciadas |
|---|---|
| Windows Server 2016 | [VM do Azure do SQL Server 2017 Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[VM do Azure do SQL Server 2017 Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[VM do Azure do SQL Server 2017 Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [VM do Azure do SQL Server 2017 Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[VM do Azure do SQL Server 2017 Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[VM do Azure do SQL Server 2017 Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [VM do Azure do SQL Server 2017 Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[VM do Azure do SQL Server 2017 Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[VM do Azure do SQL Server 2017 Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [VM do Azure do SQL Server 2017 Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[VM do Azure do SQL Server 2017 Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[VM do Azure do SQL Server 2017 Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> Quando você cria uma máquina virtual SQL Server no portal, a janela **Escolher um tamanho** mostra um custo estimado. É importante observar que essa estimativa é apenas dos custos de computação para executar a VM juntamente com quaisquer custos de licenciamento do sistema operacional (Windows ou sistema operacionais Linux de terceiros).
>
> ![Folha Escolher tamanho da VM](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Ele não inclui os custos de licenciamento adicionais do SQL Server para as edições Enterprise, Standard e Web. Para obter a estimativa de preços mais precisa, selecione o sistema operacional e a edição do SQL Server na página de preços para [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) ou [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

> [!NOTE]
> Agora, é possível alterar o modelo de licenciamento do pagamento por uso para levar sua própria licença (BYOL) e retornar. Para saber mais, confira [Como alterar o modelo de licenciamento de uma VM do SQL](virtual-machines-windows-sql-ahb.md). 

## <a id="byol"></a> Traga sua própria licença (BYOL)

**Trazer sua própria licença do SQL Server por meio da Mobilidade de Licença**, também conhecida como **BYOL**, significa usar uma Licença de Volume existente do SQL Server com Software Assurance em uma VM do Azure. Uma VM do SQL Server usando BYOL é cobrada somente pelo custo da respectiva execução e não pelo licenciamento do SQL Server, considerando que você já tenha adquirido licenças e o Software Assurance por meio de um programa de Licenciamento por Volume.

> [!IMPORTANT]
> As imagens BYOL requeremum Enterprise Agreement com Software Assurance. Não estão disponíveis como parte do Parceiro de Solução de Nuvem (CSP) o Azure neste momento. Os clientes do CSP podem trazer sua própria licença implantando uma imagem paga conforme o uso e, em seguida, habilitando o [benefício híbrido do Azure](virtual-machines-windows-sql-ahb.md).

> [!NOTE]
> Atualmente, as imagens BYOL só estão disponíveis para máquinas virtuais do Windows. No entanto, você pode instalar manualmente do SQL Server em uma VM somente Linux. Consulte as diretrizes nas [Perguntas Frequentes de VM Linux do SQL](../../linux/sql/sql-server-linux-faq.md).

Trazer seu próprio licenciamento do SQL por meio da Mobilidade de Licença é recomendável para:

- **Cargas de trabalho contínuas**. Por exemplo, um aplicativo que precisa dar suporte a operações de negócios 24 horas por dia, sete dias por semana.

- **Cargas de trabalho com tempo de vida e escala conhecidos**. Por exemplo, um aplicativo que é necessário o ano todo e cuja demanda foi prevista.

Para usar BYOL com uma VM do SQL Server, você deve ter uma licença para o SQL Server Standard ou Enterprise e o [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default.aspx#tab=1), que é uma opção obrigatória em alguns programas de licenciamento por volume e uma compra adicional com outros. O nível de preço fornecido pelos programas de Licenciamento por Volume varia, com base no tipo de contrato e na quantidade e/ou no compromisso com o SQL Server. Porém, como uma regra geral, trazer sua própria licença para cargas de trabalho de produção contínuas agrega os seguintes benefícios:

| Benefício do método BYOL | DESCRIÇÃO |
|-----|-----|
| **Economia de custos** | A [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) oferece economia de até 55%. Para obter mais informações, consulte [alternar modelo de licenciamento] (Virtual-Machines-Windows-SQL-ahb.md |
| **Réplica secundária passiva gratuita** | Outro benefício de trazer sua própria licença é o [licenciamento gratuito para uma réplica secundária passiva](https://azure.microsoft.com/pricing/licensing-faq/) por SQL Server para fins de alta disponibilidade. Isso reduz pela metade o custo de licenciamento de uma implantação do SQL Server altamente disponível (por exemplo, usar os Grupos de Disponibilidade Always On). Os direitos para executar a réplica secundária passiva são fornecidos pelo benefício do Software Assurance para Servidores de Failover. |

Para criar uma VM do Azure do SQL Server 2017 com uma dessas imagens traga sua própria licença, veja as VMs com o prefixo "{BYOL}":

- [VM do Azure do SQL Server 2017 Enterprise](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [VM do Azure do SQL Server 2017 Standard](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> Informe-nos em até 10 dias quantas licenças do SQL Server você usará no Azure. Os links para as imagens anteriores têm instruções de como fazer isso.

> [!NOTE]
> Agora, é possível alterar o modelo de licenciamento do pagamento por uso para levar sua própria licença (BYOL) e retornar. Para saber mais, confira [Como alterar o modelo de licenciamento de uma VM do SQL](virtual-machines-windows-sql-ahb.md). 



## <a name="reduce-costs"></a>Reduzir custos

Para evitar custos desnecessários, escolha um tamanho de máquina virtual ideal e considere desligamentos intermitentes para cargas de trabalho não contínuas.

### <a id="machinesize"></a> Dimensionar corretamente a VM

O custo de licenciamento do SQL Server está diretamente relacionado ao número de vCPUs. Escolha um tamanho de VM que corresponda às necessidades esperadas para CPU, memória, armazenamento e largura de banda de E/S. Para obter uma lista completa das opções de tamanho de computador, consulte [Tamanhos de VM do Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) e [Tamanhos de VM Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Há novos tamanhos de computador que funcionam bem com certos tipos de cargas de trabalho do SQL Server. Esses tamanhos de máquinas mantêm altos níveis de memória, armazenamento e largura de banda de E/S, mas eles têm uma contagem inferior de núcleos virtualizados. Por exemplo, considere o exemplo a seguir:

| Tamanho da VM | vCPUs | Memória | Máx. de discos | Taxa máxima de transferência de E/S | Custos de licenciamento de SQL | Custo total (computação + licenciamento) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51.200 IOPS ou 768 MB/s | | |
| **Standard_DS14-4v2** | 4 | 112 GB | 32 | 51.200 IOPS ou 768 MB/s | 75% inferior | 57% inferior |

> [!IMPORTANT]
> Este é um exemplo pontual. Para as especificações mais recentes, consulte os artigos sobre tamanhos de computador e página de preços do Azure para [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) e [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

No exemplo anterior, você pode ver que as especificações de **Standard_DS14v2** e **Standard_DS14-4v2** são idênticos, exceto para vCPUs. O sufixo **-4v2** no final do tamanho de computador **Standard_DS14-4v2** indica o número de vCPUs ativas. Já que os custos de licenciamento do SQL Server estão vinculados ao número de vCPUs, isso reduz significativamente o custo da VM em cenários em que as vCPUs extras não são necessárias. Este é um exemplo e há vários tamanhos de computador com vCPUs restritas que são identificados com esse padrão de sufixo. Para obter mais informações, consulte a postagem de blog [Anunciando novos tamanhos de VM do Azure para um trabalho de banco de dados mais econômico](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).

### <a name="shut-down-your-vm-when-possible"></a>Desligue a VM quando possível

Se você estiver usando alguma carga de trabalho que não seja executada continuamente, considere desligar a máquina virtual durante os períodos inativos. Você paga apenas pelo que usa.

Por exemplo, se estiver apenas testando o SQL Server em uma VM do Azure, você não desejaria incorrer em cobranças por deixá-la acidentalmente em execução por semanas. Uma solução é usar o [recurso desligamento automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Desligamento automático da VM do SQL](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

O desligamento automático faz parte de um conjunto maior de recursos semelhantes fornecido pelo [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab).

Para outros fluxos de trabalho, considere o desligamento e a reinicialização automáticos das VMs do Azure com uma solução por script, tal como a [Automação do Azure](https://azure.microsoft.com/services/automation/).

> [!IMPORTANT]
> Desligar e desalocar a VM é a única maneira de evitar cobranças. Simplesmente interromper ou usar as opções de energia para desligar a VM ainda incorrerá em cobranças por uso.

## <a name="next-steps"></a>Próximas etapas

Para obter diretrizes gerais de preços do Azure, confira [Evitar custos inesperados com o gerenciamento de custo e cobrança do Azure](../../../billing/billing-getting-started.md). Para obter os preços das Máquinas Virtuais mais recentes, incluindo o SQL Server, veja a página de preços de VM do Azure para [VMs do Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) e [VMs do Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Para obter uma visão geral do SQL Server em execução em Máquinas Virtuais do Azure, consulte os seguintes artigos:

- [Visão geral do SQL Server em VMs do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
- [Visão geral do SQL Server em VMs do Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
