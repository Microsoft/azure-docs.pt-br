---
title: Janela de Manutenção
description: Entenda como o banco de dados SQL do Azure e a janela de manutenção da instância gerenciada podem ser configurados.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.custom: references_regions
ms.date: 03/23/2021
ms.openlocfilehash: 9d7ab0498673ad7006087b66575eea9371b96d11
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565870"
---
# <a name="maintenance-window-preview"></a>Janela de manutenção (visualização)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

O recurso janela de manutenção permite configurar o agendamento de manutenção para o [banco de dados SQL do Azure](sql-database-paas-overview.md) e os recursos da [instância gerenciada do Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md) , tornando os eventos de manutenção de impacto previsíveis e menos interrupções para sua carga de trabalho. 

> [!Note]
> O recurso de janela de manutenção não protege contra eventos não planejados, como falhas de hardware, que podem causar interrupções de conexão curtas.

## <a name="overview"></a>Visão geral

Periodicamente, o Azure realiza a [manutenção planejada](planned-maintenance.md) do banco de dados SQL e recursos de instância gerenciada do SQL Durante o evento de manutenção do SQL do Azure, os bancos de dados estão totalmente disponíveis, mas podem estar sujeitos a reconfigurações curtas nos respectivos SLAs de disponibilidade para o [banco de dados SQL](https://azure.microsoft.com/support/legal/sla/sql-database) e a [instância gerenciada do SQL](https://azure.microsoft.com/support/legal/sla/azure-sql-sql-managed-instance).

A janela de manutenção destina-se a cargas de trabalho de produção que não são resilientes a reconfigurações de banco de dados ou instância e não podem absorver interrupções de conexão curtas causadas por eventos de manutenção planejados Ao escolher uma janela de manutenção que você preferir, você pode minimizar o impacto da manutenção planejada, pois ela estará ocorrendo fora do horário comercial de pico. Cargas de trabalho resilientes e cargas de trabalho de não produção podem depender da política de manutenção padrão do Azure SQL.

A janela de manutenção pode ser configurada na criação ou para recursos existentes do SQL do Azure. Ele pode ser configurado usando o portal do Azure, PowerShell, CLI ou API do Azure.

> [!Important]
> A configuração da janela de manutenção é uma operação assíncrona de execução longa, semelhante à alteração da camada de serviço do recurso SQL do Azure. O recurso está disponível durante a operação, exceto uma pequena reconfiguração que ocorre no final da operação e normalmente dura até 8 segundos, mesmo no caso de transações de longa execução interrompidas. Para minimizar o impacto da reconfiguração, você deve executar a operação fora do horário de pico.

### <a name="gain-more-predictability-with-maintenance-window"></a>Obter mais previsibilidade com a janela de manutenção

Por padrão, a política de manutenção do SQL do Azure bloqueia as atualizações impactantes durante o período de **8:00 a 17:00 horas locais todos os dias** para evitar qualquer interrupção durante o horário de pico normal de negócios. A hora local é determinada pelo local da [região do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) que hospeda o recurso e pode observar o horário de Verão de acordo com a definição de fuso horário local. 

Você pode ajustar ainda mais as atualizações de manutenção para um horário adequado aos seus recursos SQL do Azure escolhendo entre dois slots de janela de manutenção adicionais:
 
* Janela da semana, 19:10 para 6h hora local segunda-feira
* Janela de fim de semana, 19:10 até 6h hora local sexta-feira

Depois que a seleção da janela de manutenção for feita e a configuração do serviço for concluída, a manutenção planejada ocorrerá somente durante a janela de sua escolha.   

> [!Important]
> Em circunstâncias muito raras em que qualquer adiamento de ação pode causar um impacto sério, como aplicar um patch de segurança crítico, a janela de manutenção configurada pode ser temporariamente substituída. 

### <a name="cost-and-eligibility"></a>Custo e qualificação

Configurar e usar a janela de manutenção é gratuito para todos os [tipos de oferta](https://azure.microsoft.com/support/legal/offer-details/)qualificados: pré-pago, provedor de soluções de nuvem (CSP), Microsoft contrato Enterprise ou contrato de cliente da Microsoft.

> [!Note]
> Uma oferta do Azure é o tipo de assinatura do Azure que você tem. Por exemplo, uma assinatura com [tarifas pagas conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure via Open](https://azure.microsoft.com/offers/ms-azr-0111p/)e [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) são ofertas do Azure. Cada oferta ou plano tem diferentes termos e benefícios. Sua oferta ou plano é mostrado na visão geral da assinatura. Para obter mais informações sobre como alternar sua assinatura para uma oferta diferente, consulte [alterar sua assinatura do Azure para uma oferta diferente](../../cost-management-billing/manage/switch-azure-offer.md).

## <a name="advance-notifications"></a>Notificações antecipadas

As notificações de manutenção podem ser configuradas para alertá-lo sobre futuros eventos de manutenção planejada para o banco de dados SQL do Azure 24 horas de antecedência, no momento da manutenção e quando a manutenção for concluída. Para obter mais informações, consulte [notificações antecipadas](advance-notifications.md).

## <a name="availability"></a>Disponibilidade

### <a name="supported-service-level-objectives"></a>Objetivos de nível de serviço com suporte

A escolha de uma janela de manutenção que não seja a padrão está disponível em todos os SLOs **, exceto para**:
* Hiperescala 
* Pools da instância
* VCore Gen4 herdado
* Básico, S0 e S1 
* DC, Fsv2, série M

### <a name="azure-region-support"></a>Suporte de região do Azure

A escolha de uma janela de manutenção diferente do padrão está disponível atualmente nas seguintes regiões:

- Leste da Austrália
- Sudeste da Austrália
- Sul do Brasil
- Canadá Central
- Centro dos EUA
- Leste dos EUA
- Leste dos EUA 2
- Leste da Ásia
- Leste do Japão
- NorthCentral nós
- Norte da Europa
- Centro nós
- Sudeste da Ásia
- Sul do Reino Unido
- Europa Ocidental
- Oeste dos EUA
- Oeste dos EUA 2

## <a name="gateway-maintenance-for-azure-sql-database"></a>Manutenção de gateway para banco de dados SQL do Azure

Para obter o benefício máximo das janelas de manutenção, verifique se os aplicativos cliente estão usando a política de conexão de redirecionamento. Redirecionar é a política de conexão recomendada, em que os clientes estabelecem conexões diretamente com o nó que hospeda o banco de dados, levando à latência reduzida e à taxa de transferência aprimorada.  

* No banco de dados SQL do Azure, todas as conexões que usam a política de conexão proxy podem ser afetadas pela janela de manutenção escolhida e uma janela de manutenção de nó de gateway. No entanto, as conexões de cliente que usam a política de conexão de redirecionamento recomendada não são afetadas por uma reconfiguração de manutenção de nó de gateway. 

* Na instância gerenciada do SQL do Azure, os nós de gateway são hospedados [no cluster virtual](../../azure-sql/managed-instance/connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) e têm a mesma janela de manutenção que a instância gerenciada, mas usar a política de conexão de redirecionamento ainda é recomendado para minimizar o número de interrupções durante o evento de manutenção.

Para saber mais sobre a política de conexão do cliente no banco de dados SQL do Azure, consulte [política de conexão do banco de dados SQL do Azure](../database/connectivity-architecture.md#connection-policy). 

Para obter mais informações sobre a política de conexão do cliente na instância gerenciada do SQL Azure, consulte [tipos de conexão da instância gerenciada do Azure SQL](../../azure-sql/managed-instance/connection-types-overview.md).

## <a name="considerations-for-azure-sql-managed-instance"></a>Considerações sobre a instância gerenciada do SQL do Azure

A instância gerenciada do SQL do Azure consiste em componentes de serviço hospedados em um conjunto dedicado de máquinas virtuais isoladas que são executadas dentro da sub-rede da rede virtual do cliente. Essas máquinas virtuais formam [cluster (s) virtual (es)](../managed-instance/connectivity-architecture-overview.md#high-level-connectivity-architecture) que podem hospedar várias instâncias gerenciadas. A janela de manutenção configurada em instâncias de uma sub-rede pode influenciar o número de clusters virtuais na sub-rede e a distribuição de instâncias entre clusters virtuais. Isso pode exigir uma consideração de alguns efeitos.

### <a name="maintenance-window-configuration-is-long-running-operation"></a>A configuração da janela de manutenção é uma operação de longa execução 
Todas as instâncias hospedadas em um cluster virtual compartilham a janela de manutenção. Por padrão, todas as instâncias gerenciadas são hospedadas no cluster virtual com a janela de manutenção padrão. A especificação de outra janela de manutenção para a instância gerenciada durante sua criação ou depois significa que ela deve ser colocada no cluster virtual com a janela de manutenção correspondente. Se não houver tal cluster virtual na sub-rede, um novo deverá ser criado primeiro para acomodar a instância. Acomodar a instância adicional no cluster virtual existente pode exigir o redimensionamento do cluster. Ambas as operações contribuem para a duração da configuração da janela de manutenção para uma instância gerenciada.
A duração esperada da configuração da janela de manutenção na instância gerenciada pode ser calculada usando a [duração estimada das operações de gerenciamento de instância](../managed-instance/management-operations-overview.md#duration).

> [!Important]
> Uma reconfiguração curta acontece ao final da operação de manutenção e normalmente dura até 8 segundos, mesmo no caso de transações de longa execução interrompidas. Para minimizar o impacto da reconfiguração, você deve agendar a operação fora do horário de pico.

### <a name="ip-address-space-requirements"></a>Requisitos de espaço de endereço IP
Cada novo cluster virtual na sub-rede requer endereços IP adicionais de acordo com a [alocação de endereço IP do cluster virtual](../managed-instance/vnet-subnet-determine-size.md#determine-subnet-size). A alteração da janela de manutenção para a instância gerenciada existente também requer uma [capacidade IP adicional temporária](../managed-instance/vnet-subnet-determine-size.md#address-requirements-for-update-scenarios) como no cenário de dimensionamento de vCores para a camada de serviço correspondente.

### <a name="ip-address-change"></a>Alteração de endereço IP
A configuração e a alteração da janela de manutenção causam a alteração do endereço IP da instância, dentro do intervalo de endereços IP da sub-rede.

> [!Important]
>  Certifique-se de que as regras de firewall e NSG não bloquearão o tráfego de dados após a alteração do endereço IP. 

## <a name="next-steps"></a>Próximas etapas

* [Notificações antecipadas](advance-notifications.md)
* [Configurar janela de manutenção](maintenance-window-configure.md)

## <a name="learn-more"></a>Saiba mais

* [Perguntas frequentes sobre a janela de manutenção](maintenance-window-faq.yml)
* [Banco de Dados SQL do Azure](sql-database-paas-overview.md) 
* [Instância gerenciada do SQL](../managed-instance/sql-managed-instance-paas-overview.md)
* [Planejar eventos de manutenção do Azure no banco de dados SQL do Azure e instância gerenciada do SQL do Azure](planned-maintenance.md)