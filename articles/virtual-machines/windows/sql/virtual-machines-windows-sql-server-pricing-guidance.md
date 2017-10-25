---
title: "Gerenciar custos efetivamente para o SQL Server em máquinas virtuais do Azure | Microsoft Docs"
description: "Fornece práticas recomendadas para a escolha do modelo de preços certo de máquina virtual do SQL Server."
services: virtual-machines-windows
documentationcenter: na
author: luisherring
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/18/2017
ms.author: jroth
ms.openlocfilehash: 29a92f0c70bffedeb75c50b7fc3b687ee5ee227d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="pricing-guidance-for-sql-server-azure-vms"></a>Diretrizes de preços para VMs do Azure do SQL Server

Este tópico fornece diretrizes de preços para máquinas virtuais do SQL Server no Azure. Há várias opções que afetam o custo, e é importante escolher a imagem certa, que equilibre os custos com os requisitos de negócios.

## <a name="free-licensed-sql-server-editions"></a>Edições do SQL Server com licença gratuita

Se quiser desenvolver, testar ou criar uma prova de conceito, use o **SQL Server Developer Edition** com licença gratuita. Essa edição tem tudo o que o SQL Server Enterprise Edition tem, de modo que você pode usá-la para criar qualquer aplicativo que quiser. Ela só não pode ser executada no ambiente de produção. O custo de uma VM do SQL Server Developer será somente pela VM, não pelo licenciamento do SQL Server.

Se quiser executar uma carga de trabalho leve em produção (menos de quatro núcleos, menos de 1 GB de memória, menos de 10 GB/banco de dados), use o **SQL Server Express Edition** com licença gratuita. Uma VM do SQL Express será cobrada somente por seu custo, e não pelo licenciamento do SQL.

Para essas cargas de trabalho de produção leve ou desenvolvimento/teste, você também poderá economizar escolhendo uma VM menor que corresponda a essas cargas de trabalho. O DS1v2 pode ser uma boa opção para essas cargas de trabalho.

Para criar uma VM do Azure do SQL Server 2016 com uma destas imagens, acesse os seguintes links:

- [VM do Azure do SQL Server 2016 Developer](https://ms.portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP1DeveloperWindowsServer2016-ARM)
- [VM do Azure do SQL Server 2016 Express](https://ms.portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP1ExpressWindowsServer2016-ARM)

## <a name="paid-sql-server-editions"></a>Edições pagas do SQL Server

Se você tiver uma carga de trabalho de produção não leve, use uma das seguintes edições do SQL Server:

| Edição do SQL Server | Carga de trabalho |
|-----|-----|
| Web | Sites pequenos |
| Standard | Cargas de trabalho pequenas a médias |
| Enterprise | Cargas de trabalho grandes ou críticas|

Você tem duas opções de pagamento para licenciamento dessas edições do SQL Server: *pagamento por uso* ou *traga sua própria licença*.

### <a name="pay-per-usage"></a>Pagamento por uso

**Pagar a licença do SQL Server por uso** significa que o custo por minuto da execução da VM do Azure inclui o custo da licença do SQL Server. Você pode ver os preços para as diferentes edições do SQL Server (Web, Standard e Enterprise) na [página de preços de VMs do Azure](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-standard). O custo é o mesmo para todas as versões do SQL Server (2008 R2 a 2016). Assim como o licenciamento geral do SQL Server, o custo do licenciamento por minuto depende do número de núcleos da VM.

O pagamento do licenciamento do SQL Server por uso é recomendável para:

- Cargas de trabalho temporárias ou periódicas. Por exemplo, um aplicativo que precisa dar suporte a um evento de alguns meses a cada ano ou à análise comercial às segundas-feiras.
- Cargas de trabalho com tempo de vida ou escala desconhecidos. Por exemplo, um aplicativo que pode não ser necessário por alguns meses, ou que pode exigir mais, ou menos capacidade de computação, dependendo da demanda.

Para criar uma VM do Azure do SQL Server 2016 com uma dessas imagens de pagamento por uso, acesse os seguintes links:

- [VM do Azure do SQL Server 2016 Web](https://ms.portal.azure.com/#create/Microsoft.SQLServer2016SP1WebWindowsServer2016)
- [VM do Azure do SQL Server 2016 Standard](https://ms.portal.azure.com/#create/Microsoft.SQLServer2016SP1StandardWindowsServer2016)
- [VM do Azure do SQL Server 2016 Enterprise](https://ms.portal.azure.com/#create/Microsoft.SQLServer2016SP1EnterpriseWindowsServer2016)

> [!IMPORTANT]
> Quando você cria uma máquina virtual do SQL Server no portal do Azure, o custo mensal estimado exibido na folha **Escolher um tamanho** não inclui os custos de licenciamento do SQL Server. Esse é o custo da VM sozinho.
>
> ![Folha Escolher tamanho da VM](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Para as edições Express e Developer do SQL Server com licença gratuita, esse é o custo total estimado. Mas para as edições Web, Standard e Enterprise, encontre os custos de licenciamento adicional do SQL na [página de preços de Máquinas Virtuais do Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Na página de preços, selecione sua edição de destino do SQL Server.

### <a name="bring-your-own-license-byol"></a>BYOL (Traga sua própria licença)

**Trazer sua própria licença do SQL Server por meio da Mobilidade de Licença**, também conhecida como **BYOL**, significa usar uma Licença de Volume existente do SQL Server com Software Assurance em uma VM do Azure. Uma VM do SQL Server usando BYOL será cobrada somente pelo custo da execução da VM, não pelo licenciamento do SQL Server, considerando que você já tenha adquirido licenças e o Software Assurance por meio de um programa de Licenciamento por Volume.

Trazer seu próprio licenciamento do SQL por meio da Mobilidade de Licença é recomendável para:

- Cargas de trabalho contínuas. Por exemplo, um aplicativo que precisa dar suporte a operações de negócios 24 horas por dia, sete dias por semana.
- Cargas de trabalho com tempo de vida e escala conhecidos. Por exemplo, um aplicativo que será necessário o ano todo e cuja demanda foi prevista.

Para usar BYOL com uma VM do SQL Server, você deve ter uma licença para o SQL Server Standard ou Enterprise e o [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default.aspx#tab=1), que é uma opção obrigatória em alguns programas de [Licenciamento por Volume](https://www.microsoft.com/en-us/download/details.aspx?id=10585) e uma compra adicional com outros.  Os níveis de preço fornecidos pelos programas de Licenciamento por Volume variam, com base no tipo de contrato e na quantidade e/ou no compromisso com o SQL Server. Porém, como uma regra geral, trazer sua própria licença para cargas de trabalho de produção contínuas agrega os seguintes benefícios:

| Benefício do método BYOL | Descrição |
|-----|-----|
| **Economia de custos** | Trazer usa própria licença do SQL Server é mais econômico do que pagar pelo uso se uma carga de trabalho for executar continuamente o SQL Server Standard ou Enterprise por *mais de 10 meses*. |
| **Economias de longo prazo** | Em média, é *30% mais barato por ano* comprar ou renovar uma licença do SQL Server pelos três primeiros anos. Além disso, depois de três anos, você não precisa mais renovar a licença, apenas pagar pelo Software Assurance. Nesse ponto, *a economia é de 200%*. |
| **Réplica secundária passiva gratuita** | Outro benefício de trazer sua própria licença é o [licenciamento gratuito para uma réplica secundária passiva](https://azure.microsoft.com/pricing/licensing-faq/) por SQL Server para fins de alta disponibilidade. Isso reduz pela metade o custo de licenciamento de uma implantação do SQL Server altamente disponível (por exemplo, usar os Grupos de Disponibilidade Always On). Os direitos para executar a réplica secundária passiva são fornecidos pelo benefício do Software Assurance para Servidores de Failover. |

Para criar uma VM do Azure do SQL Server 2016 com uma dessas imagens traga sua própria licença, veja as VMs com o prefixo "{BYOL}":

- [VM do Azure do SQL Server 2016 Enterprise](https://ms.portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1EnterpriseWindowsServer2016)
- [VM do Azure do SQL Server 2016 Standard](https://ms.portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016)

> [!NOTE]
> Informe-nos em até 10 dias quantas licenças do SQL Server você usará no Azure. Os links para as imagens anteriores têm instruções de como fazer isso.

## <a name="avoid-unecessary-costs"></a>Evitar custos desnecessários

Se você estiver usando alguma carga de trabalho que não seja executada continuamente, considere desligar a máquina virtual durante os períodos inativos. Você paga apenas pelo que usa.

Por exemplo, se estiver apenas testando o SQL Server em uma VM do Azure, você não desejaria incorrer em cobranças por deixá-la acidentalmente em execução por semanas. Uma solução é usar o [recurso desligamento automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Desligamento automático da VM do SQL](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

O desligamento automático faz parte de um conjunto maior de recursos semelhantes fornecido pelo [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab).

Para outros fluxos de trabalho, considere o desligamento e a reinicialização automáticos das VMs do Azure com uma solução por script, como a [Automação do Azure](https://azure.microsoft.com/services/automation/).

> [!IMPORTANT]
> Desligar e desalocar a VM é a única maneira de evitar cobranças. Simplesmente interromper ou usar as opções de energia para desligar a VM ainda incorrerá em cobranças por uso.

## <a name="next-steps"></a>Próximas etapas

Para obter diretrizes gerais de preços do Azure, confira [Evitar custos inesperados com o gerenciamento de custo e cobrança do Azure](../../../billing/billing-getting-started.md).

Para obter os preços das Máquinas Virtuais mais recentes, incluindo o SQL Server, veja a [página de preços de VM do Azure](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-standard).

Examine outros tópicos sobre Máquinas Virtuais do SQL Server em [Visão geral do SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
