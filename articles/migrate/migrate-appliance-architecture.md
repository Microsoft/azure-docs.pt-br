---
title: Arquitetura do dispositivo de Migrações para Azure
description: Fornece uma visão geral do dispositivo de Migrações para Azure usado na avaliação e migração de servidor.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: 42d4a722be25eec4b3e27012350346018fdba0f3
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754106"
---
# <a name="azure-migrate-appliance-architecture"></a>Arquitetura do dispositivo de Migrações para Azure

Este artigo descreve a arquitetura e os processos do dispositivo de migração do Azure. O dispositivo de migrações para Azure é um dispositivo leve que é implantado localmente, para descobrir VMs e servidores físicos para migração para o Azure. 

## <a name="deployment-scenarios"></a>Cenários de implantação

O dispositivo de Migrações para Azure é usado nos cenários a seguir.

**Cenário** | **Ferramenta** | **Usada para** 
--- | --- | ---
**Avaliação da VM do VMware** | Migrações para Azure: Avaliação de Servidor | Descubra VMs VMware.<br/><br/> Descubra aplicativos e dependências de computador.<br/><br/> Coletar metadados de computador e metadados de desempenho e enviar para o Azure.
**Migração de VM VMware (sem agente)** | Migrações para Azure: Migração de Servidor | Descobrir as VMs do VMware<br/><br/>  Replique VMs VMware com [migração sem agente](server-migrate-overview.md).
**Avaliação da VM do Hyper-V** | Migrações para Azure: Avaliação de Servidor | Descubra as VMs do Hyper-V.<br/><br/> Coletar metadados de computador e metadados de desempenho e enviar para o Azure.
**Computador físico** |  Migrações para Azure: Avaliação de Servidor |  Descobrir servidores físicos.<br/><br/> Coletar metadados de computador e metadados de desempenho e enviar para o Azure.

## <a name="appliance-components"></a>Componentes do dispositivo

O dispositivo tem vários componentes.

- **Aplicativo de gerenciamento**: é um aplicativo Web para entrada do usuário durante a implantação do dispositivo. Usado ao avaliar computadores para migração para o Azure.
- **Agente de descoberta**: o agente coleta dados de configuração do computador. Usado ao avaliar computadores para migração para o Azure. 
- **Agente coletor**: o agente coleta dados de desempenho. Usado ao avaliar computadores para migração para o Azure.
- **Agente DRA**: orquestra a replicação da VM e coordena a comunicação entre computadores replicados e o Azure. Usado somente ao replicar as VMs do VMware para o Azure usando a migração sem agente.
- **Gateway**: envia dados replicados para o Azure. Usado somente ao replicar as VMs do VMware para o Azure usando a migração sem agente.
- **Serviço de atualização automática**: atualiza os componentes do dispositivo (executado a cada 24 horas).



## <a name="appliance-deployment"></a>Implantação do dispositivo

- O dispositivo de migrações para Azure pode ser configurado usando um modelo para [Hyper-v](how-to-set-up-appliance-hyper-v.md) ou [VMware](how-to-set-up-appliance-vmware.md) ou usando um instalador de script do PowerShell para [VMware/Hyper-V](deploy-appliance-script.md)e para [servidores físicos](how-to-set-up-appliance-physical.md). 
- Os requisitos de suporte do dispositivo e os pré-requisitos de implantação são resumidos na [matriz de suporte do dispositivo](migrate-appliance.md).


## <a name="appliance-registration"></a>Registro de dispositivo

Durante a configuração do dispositivo, você registra o dispositivo com as migrações para Azure e as ações resumidas na tabela ocorrem.

**Ação** | **Detalhes** | **Permissões**
--- | --- | ---
**Registrar provedores de origem** | Esses provedores de recursos são registrados na assinatura que você escolhe durante a instalação do dispositivo: Microsoft. OffAzure, Microsoft. migrar e Microsoft. keyvault.<br/><br/> O registro de um provedor de recursos configura sua assinatura para trabalhar com o provedor de recursos. | Para registrar os provedores de recursos, você precisa de uma função de Colaborador ou Proprietário na assinatura.
**Criar aplicativo do Azure AD – comunicação** | As migrações para Azure criam um aplicativo Azure Active Directory (Azure AD) para comunicação (autenticação e autorização) entre os agentes em execução no dispositivo e seus respectivos serviços em execução no Azure.<br/><br/> Este aplicativo não tem privilégios para fazer chamadas Azure Resource Manager ou o acesso RBAC do Azure em qualquer recurso. | Você precisa [dessas permissões](./tutorial-discover-vmware.md#prepare-an-azure-user-account) para a migração do Azure para criar o aplicativo.
**Criar aplicativos do Azure AD – cofre de chaves** | Esse aplicativo é criado somente para a migração sem agente de VMs do VMware para o Azure.<br/><br/> Ele é usado exclusivamente para acessar o cofre de chaves criado na assinatura do usuário para a migração sem agente.<br/><br/> Ele tem acesso RBAC do Azure no cofre de chaves do Azure (criado no locatário do cliente) quando a descoberta é iniciada do dispositivo. | Você precisa [dessas permissões](./tutorial-discover-vmware.md#prepare-an-azure-user-account) para a migração do Azure para criar o aplicativo.



## <a name="collected-data"></a>Dados coletados

Os dados coletados pelo cliente para todos os cenários de implantação são resumidos na [matriz de suporte do dispositivo](migrate-appliance.md).

## <a name="discovery-and-collection-process"></a>Processo de descoberta e coleta

![Arquitetura](./media/migrate-appliance-architecture/architecture1.png)

O dispositivo se comunica com servidores vCenter e hosts/cluster do Hyper-V usando o processo a seguir.

1. **Iniciar descoberta**:
    - Quando você inicia a descoberta no dispositivo Hyper-V, ele se comunica com os hosts Hyper-V na porta WinRM 5985 (HTTP).
    - Quando você inicia a descoberta no dispositivo VMware, ela se comunica com o servidor vCenter na porta TCP 443 por padrão. Se o servidor vCenter escuta em uma porta diferente, você pode configurá-lo no aplicativo Web do dispositivo.
2. **Coletar metadados e dados de desempenho**:
    - O dispositivo usa uma sessão de modelo CIM (CIM) para coletar dados de VM do Hyper-V do host Hyper-V na porta 5985.
    - O dispositivo se comunica com a porta 443 por padrão, para coletar dados de VM do VMware do vCenter Server.
3. **Enviar dados**: o dispositivo envia os dados coletados para a avaliação de servidor de migrações para Azure e migração de servidor de migrações para Azure por meio da porta SSL 443. O dispositivo pode se conectar ao Azure pela Internet ou via ExpressRoute (requer emparelhamento da Microsoft).
    - Para dados de desempenho, o dispositivo coleta dados de utilização em tempo real.
        - Os dados de desempenho são coletados a cada 20 segundos para VMware e a cada 30 segundos para o Hyper-V, para cada métrica de desempenho.
        - Os dados coletados são acumulados para criar um único ponto de dados por 10 minutos.
        - O valor de pico de utilização é selecionado de todos os pontos de dados de 20/30 segundos e enviado ao Azure para cálculo da avaliação.
        - Com base no valor de percentil especificado nas propriedades de avaliação (50 º/90 º/95 º/99 º), os pontos de dez minutos são classificados em ordem crescente e o valor de percentil apropriado é usado para calcular a avaliação
    - Para a migração de servidor, o dispositivo inicia a coleta de dados da VM e o Replica no Azure.
4. **Avaliar e migrar**: agora você pode criar avaliações dos metadados coletados pelo dispositivo usando a avaliação do servidor de migrações para Azure. Além disso, você também pode começar a migrar VMs VMware usando a migração de servidor de migração do Azure para orquestrar a replicação de VM sem agente.

## <a name="appliance-upgrades"></a>Atualizações de dispositivo

O dispositivo é atualizado conforme os agentes de Migrações para Azure em execução no dispositivo são atualizados. Isso ocorre automaticamente porque a atualização automática está habilitada no dispositivo por padrão. Você pode alterar essa configuração padrão para atualizar os agentes manualmente.

Desative a atualização automática no registro definindo a chave "AutoUpdate" do HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance como 0 (DWORD).


## <a name="next-steps"></a>Próximas etapas

[Examine](migrate-appliance.md) a matriz de suporte do dispositivo.