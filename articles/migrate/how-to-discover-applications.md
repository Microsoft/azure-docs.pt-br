---
title: Descobrir aplicativos em servidores locais com as migrações para Azure
description: Saiba como descobrir aplicativos, funções e recursos em servidores locais com a avaliação de servidor de migrações para Azure.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/10/2020
ms.openlocfilehash: 8266b585881546b37bbb21b82780ab26d85dada7
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048073"
---
# <a name="discover-installed-applications-roles-and-features-software-inventory-and-sql-server-instances-and-databases"></a>Descobrir aplicativos, funções e recursos instalados (inventário de software) e instâncias de SQL Server e bancos de dados

Este artigo descreve como descobrir aplicativos, funções e recursos instalados (inventário de software) e SQL Server instâncias e bancos de dados em servidores executados em seu ambiente VMware, usando o migrações para Azure: ferramenta de avaliação do servidor.

Executar o inventário de software ajuda a identificar e adaptar um caminho de migração para o Azure para suas cargas de trabalho. O inventário de software usa o dispositivo de migrações para Azure para executar a descoberta, usando credenciais do servidor. Ele é completamente sem agente – nenhum agente está instalado nos servidores para coletar esses dados.

> [!NOTE]
> O inventário de software está atualmente em versão prévia para servidores executados somente no ambiente VMware e é limitado apenas à descoberta. Atualmente, não oferecemos avaliação baseada em aplicativos.<br/> A descoberta e a avaliação de instâncias de SQL Server e bancos de dados em execução em seu ambiente VMware agora estão em versão prévia. Para experimentar esse recurso, use [**este link**](https://aka.ms/AzureMigrate/SQL) para criar um projeto na região **leste da Austrália** . Se você já tiver um projeto no leste da Austrália e quiser experimentar esse recurso, verifique se você concluiu esses [**pré-requisitos**](how-to-discover-sql-existing-project.md) no Portal.

## <a name="before-you-start"></a>Antes de começar

- Certifique-se de ter [criado um projeto de migrações para Azure](./create-manage-projects.md) com a ferramenta migrações para Azure: Server Assessment adicionada a ele.
- Examine [os requisitos do VMware](migrate-support-matrix-vmware.md#vmware-requirements) para executar o inventário de software.
- Examine [os requisitos do dispositivo](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) antes de configurar o dispositivo.
- Examine [os requisitos de descoberta de aplicativos](migrate-support-matrix-vmware.md#application-discovery-requirements) antes de iniciar o inventário de software nos servidores.

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>Implantar e configurar o dispositivo de migrações para Azure

1. [Examine](migrate-appliance.md#appliance---vmware) os requisitos para implantar o dispositivo de migrações para Azure.
2. Examine as URLs do Azure que o dispositivo precisará acessar nas nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais](migrate-appliance.md#government-cloud-urls).
3. [Examine os dados](migrate-appliance.md#collected-data---vmware) que o dispositivo coleta durante a descoberta e a avaliação.
4. [Observe](migrate-support-matrix-vmware.md#port-access-requirements) os requisitos de acesso da porta para o dispositivo.
5. [Implante o dispositivo de migrações para Azure](how-to-set-up-appliance-vmware.md) para iniciar a descoberta. Para implantar o dispositivo, você baixa e importa um modelo OVA no VMware para criar um servidor em execução no seu vCenter Server. Depois de implantar o dispositivo, você precisa registrá-lo com o projeto de migrações para Azure e configurá-lo para iniciar a descoberta.
6. Ao configurar o dispositivo, você precisa especificar o seguinte no Gerenciador de configuração de dispositivo:
    - Os detalhes do vCenter Server ao qual você deseja se conectar.
    - vCenter Server credenciais com escopo para descobrir os servidores em seu ambiente VMware.
    - Credenciais do servidor que podem ser credenciais de domínio/Windows (não domínio)/Linux (não domínio). [Saiba mais](add-server-credentials.md) sobre como fornecer credenciais e como tratá-las.

## <a name="verify-permissions"></a>Verificar permissões

- Você precisa [criar um vCenter Server conta somente leitura](./tutorial-discover-vmware.md#prepare-vmware) para descoberta e avaliação. A conta somente leitura precisa de privilégios habilitados para operações de convidado de **máquinas virtuais**  >  , a fim de interagir com os servidores para executar o inventário de software.
- Você pode adicionar várias credenciais de domínio e não domínio (Windows/Linux) no Gerenciador de configuração do dispositivo para descoberta de aplicativos. Você precisa de uma conta de usuário convidado para servidores Windows e uma conta de usuário regular/normal (acesso não sudo) para todos os servidores Linux. [Saiba mais](add-server-credentials.md) sobre como fornecer credenciais e como tratá-las.

### <a name="add-credentials-and-initiate-discovery"></a>Adicionar credenciais e iniciar a descoberta

1. Abra o Gerenciador de configuração do dispositivo, conclua as verificações de pré-requisitos e o registro do dispositivo.
2. Navegue até o painel **gerenciar credenciais e fontes de descoberta** .
1.  Na **etapa 1: fornecer credenciais de vCenter Server**, clique em **Adicionar credenciais** para fornecer credenciais para a conta de vCenter Server que o dispositivo usará para descobrir servidores em execução no vCenter Server.
1. Na **etapa 2: fornecer vCenter Server detalhes**, clique em **Adicionar origem de descoberta** para selecionar o nome amigável para credenciais na lista suspensa, especifique o **endereço IP/FQDN** do painel de instância vCenter Server :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="3 no Gerenciador de configuração de dispositivo para obter detalhes de vCenter Server":::
1. Na **etapa 3: fornecer credenciais de servidor para executar o inventário de software, a análise de dependência sem agente e a descoberta de instâncias de SQL Server e bancos de dados**, clique em **Adicionar credenciais** para fornecer várias credenciais de servidor para iniciar o inventário de software.
1. Clique em **Iniciar descoberta** para começar a descoberta de vCenter Server.

 Após a conclusão da descoberta de vCenter Server, o dispositivo inicia a descoberta de aplicativos, funções e recursos instalados (inventário de software). A duração depende do número de servidores descobertos. Para servidores 500, leva aproximadamente uma hora para o inventário descoberto aparecer no portal de migrações para Azure.

## <a name="review-and-export-the-inventory"></a>Revisar e exportar o inventário

Após a conclusão do inventário de software, você pode revisar e exportar o inventário no portal do Azure.

1. Em **migrações para Azure-servidores**  >  **migrações para Azure: avaliação do servidor**, clique na contagem exibida para abrir a página **servidores descobertos** .

    > [!NOTE]
    > Nesse estágio, opcionalmente, você também pode habilitar a análise de dependência para os servidores descobertos, para que você possa visualizar as dependências entre os servidores que deseja avaliar. [Saiba mais](concepts-dependency-visualization.md) sobre a análise de dependência.

2. Na coluna **aplicativos descobertos** , clique na contagem exibida para examinar os aplicativos, funções e recursos descobertos.
4. Para exportar o inventário, em **servidores descobertos**, clique em **Exportar inventário de aplicativos**.

O inventário de aplicativos é exportado e baixado no formato do Excel. A folha de **inventário de aplicativos** exibe todos os aplicativos descobertos em todos os servidores.

## <a name="discover-sql-server-instances-and-databases"></a>Descobrir SQL Server instâncias e bancos de dados

- A descoberta de aplicativos também identifica as instâncias de SQL Server em execução em seu ambiente VMware.
- Se você não tiver fornecido autenticação do Windows nem SQL Server credenciais de autenticação no Gerenciador de configuração de dispositivo, adicione as credenciais para que o dispositivo possa usá-las para se conectar às respectivas instâncias de SQL Server.

Uma vez conectado, o dispositivo coleta dados de desempenho e configuração de SQL Server instâncias e bancos de dado. Os dados de configuração do SQL Server são atualizados uma vez a cada 24 horas e os dados de desempenho são capturados a cada 30 segundos. Portanto, qualquer alteração nas propriedades da SQL Server instância e dos bancos de dados, como status do banco de dados, nível de compatibilidade, etc. pode levar até 24 horas para ser atualizada no Portal.

## <a name="next-steps"></a>Próximas etapas

- [Crie uma avaliação](how-to-create-assessment.md) para servidores descobertos.
- [Avalie os SQL Servers](./tutorial-assess-sql.md) para migração para o SQL do Azure.
