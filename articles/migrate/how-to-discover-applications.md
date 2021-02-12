---
title: Descobrir aplicativos em servidores locais com as migrações para Azure
description: Saiba como descobrir aplicativos, funções e recursos em servidores locais com a avaliação de servidor de migrações para Azure.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/10/2020
ms.openlocfilehash: eb589c08122cd47747c005c13d12b336319fd558
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96751998"
---
# <a name="discover-machine-apps-roles-and-features"></a>Descobrir aplicativos, funções e recursos de computador

Este artigo descreve como descobrir aplicativos, funções e recursos em servidores locais, usando migrações para Azure: avaliação de servidor.

Descobrir o inventário de aplicativos, funções e recursos em execução em computadores locais ajuda a identificar e personalizar um caminho de migração para o Azure para suas cargas de trabalho. A descoberta de aplicativo usa o dispositivo de migrações para Azure para executar a descoberta, usando credenciais de convidado de VM. A descoberta de aplicativos é sem agente. Nada é instalado em VMs.

> [!NOTE]
> A descoberta de aplicativos está atualmente em visualização apenas para VMs VMware e é limitada apenas à descoberta. Ainda não oferecemos a avaliação baseada em aplicativo. 


## <a name="before-you-start"></a>Antes de começar

- Verifique se você:
    - [Criou](./create-manage-projects.md) um projeto de migrações para Azure.
    - [Adicionada](how-to-assess.md) a ferramenta migrações do Azure: Server Assessment para um projeto.
- Examine o [suporte e os requisitos de descoberta de aplicativos](migrate-support-matrix-vmware.md#vmware-requirements).
- Verifique se as VMs em que você está executando a descoberta de aplicativos têm o PowerShell versão 2,0 ou posterior instalado e as ferramentas do VMware (posterior à 10.2.0) estão instaladas.
- Verifique os [requisitos](migrate-appliance.md) para implantar o dispositivo de migrações para Azure.


## <a name="deploy-the-azure-migrate-appliance"></a>Implantar o dispositivo das Migrações para Azure

1. [Examine](migrate-appliance.md#appliance---vmware) os requisitos para implantar o dispositivo de migrações para Azure.
2. Examine as URLs do Azure que o dispositivo precisará acessar nas nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais](migrate-appliance.md#government-cloud-urls).
3. [Examine os dados](migrate-appliance.md#collected-data---vmware) que o dispositivo coleta durante a descoberta e a avaliação.
4. [Observe](migrate-support-matrix-vmware.md#port-access-requirements) os requisitos de acesso da porta para o dispositivo.
5. [Implante o dispositivo de migrações para Azure](how-to-set-up-appliance-vmware.md) para iniciar a descoberta. Para implantar o dispositivo, você baixa e importa um modelo OVA no VMware para criar o dispositivo como uma VM VMware. Você configura o dispositivo e o registra com as migrações para Azure.
6. Ao implantar o dispositivo, para iniciar a descoberta contínua, especifique o seguinte:
    - O nome do vCenter Server ao qual você deseja se conectar.
    - Credenciais que você criou para o dispositivo se conectar ao vCenter Server.
    - As credenciais de conta que você criou para o dispositivo se conectar a VMs do Windows/Linux.

Depois que o dispositivo é implantado e você forneceu credenciais, o dispositivo inicia a descoberta contínua de metadados de VM e dados de desempenho, juntamente com a descoberta de aplicativos, recursos e funções.  A duração da descoberta de aplicativos depende de quantas VMs você tem. Normalmente leva uma hora para a descoberta de aplicativos de 500 VMs.

## <a name="verify-permissions"></a>Verificar permissões

Você [criou um vCenter Server conta somente leitura](./tutorial-discover-vmware.md#prepare-vmware) para descoberta e avaliação. A conta somente leitura precisa de privilégios habilitados para operações de convidado de **máquinas virtuais**  >  **Guest Operations**, a fim de interagir com a VM para a descoberta de aplicativos.

### <a name="add-the-user-account-to-the-appliance"></a>Adicionar a conta de usuário ao dispositivo

Adicione a conta de usuário da seguinte maneira:

1. Abra o aplicativo de gerenciamento de dispositivo. 
2. Navegue até o painel **fornecer detalhes do vCenter** .
3. Em **descobrir aplicativo e dependências em VMs**, clique em **Adicionar credenciais**
3. Escolha o **sistema operacional**, forneça um nome amigável para a conta e a senha do **nome de usuário** / **Password**
6. Clique em **Save** (Salvar).
7. Clique em **salvar e iniciar descoberta**.

    ![Adicionar conta de usuário da VM](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)


## <a name="review-and-export-the-inventory"></a>Revisar e exportar o inventário

Após o término da descoberta, se você forneceu credenciais para a descoberta de aplicativos, poderá revisar e exportar o inventário de aplicativos no portal do Azure.

1. Em **migrações para Azure-servidores**  >  **migrações para Azure: avaliação do servidor**, clique na contagem exibida para abrir a página **servidores descobertos** .

    > [!NOTE]
    > Nesse estágio, opcionalmente, você também pode configurar a análise de dependência para computadores descobertos, para que você possa Visualizar dependências entre computadores que deseja avaliar. [Saiba mais](concepts-dependency-visualization.md) sobre a análise de dependência.

2. Em **aplicativos descobertos**, clique na contagem exibida.
3. No **inventário de aplicativos**, você pode examinar os aplicativos, funções e recursos descobertos.
4. Para exportar o inventário, em **servidores descobertos**, clique em **Exportar inventário de aplicativos**.

O inventário de aplicativos é exportado e baixado no formato do Excel. A folha de **inventário de aplicativos** exibe todos os aplicativos descobertos em todos os computadores.

## <a name="next-steps"></a>Próximas etapas

- [Crie uma avaliação](how-to-create-assessment.md) para servidores descobertos.
- Avaliar SQL Server bancos de dados usando as [migrações para Azure: avaliação de banco de dados](/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).