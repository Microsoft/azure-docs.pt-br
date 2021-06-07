---
title: Descobrir o inventário de software em servidores locais com as migrações para Azure
description: Saiba como descobrir o inventário de software em servidores locais com a descoberta e avaliação de migrações para Azure.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: d68c3729e8a63f8342cd51b62413aec3276c6165
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871003"
---
# <a name="discover-installed-software-inventory-and-sql-server-instances-and-databases"></a>Descobrir o inventário de software instalado e SQL Server instâncias e bancos de dados

Este artigo descreve como descobrir o inventário de software instalado e SQL Server instâncias e bancos de dados em servidores executados em seu ambiente VMware, usando migrações para Azure: ferramenta de descoberta e avaliação.

Executar o inventário de software ajuda a identificar e adaptar um caminho de migração para o Azure para suas cargas de trabalho. O inventário de software usa o dispositivo de migrações para Azure para executar a descoberta, usando credenciais do servidor. Ele é completamente sem agente – nenhum agente está instalado nos servidores para coletar esses dados.

## <a name="before-you-start"></a>Antes de começar

- Verifique se você [criou um projeto](./create-manage-projects.md) com a ferramenta migrações para Azure: descoberta e avaliação adicionada a ele.
- Examine [os requisitos do VMware](migrate-support-matrix-vmware.md#vmware-requirements) para executar o inventário de software.
- Examine [os requisitos do dispositivo](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) antes de configurar o dispositivo.
- Examine [os requisitos de descoberta de aplicativos](migrate-support-matrix-vmware.md#application-discovery-requirements) antes de iniciar o inventário de software nos servidores.

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>Implantar e configurar o dispositivo de migrações para Azure

1. [Examine](migrate-appliance.md#appliance---vmware) os requisitos para implantar o dispositivo de migrações para Azure.
2. Examine as URLs do Azure que o dispositivo precisará acessar nas nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais](migrate-appliance.md#government-cloud-urls).
3. [Examine os dados](migrate-appliance.md#collected-data---vmware) que o dispositivo coleta durante a descoberta e a avaliação.
4. [Observe](migrate-support-matrix-vmware.md#port-access-requirements) os requisitos de acesso da porta para o dispositivo.
5. [Implante o dispositivo de migrações para Azure](how-to-set-up-appliance-vmware.md) para iniciar a descoberta. Para implantar o dispositivo, você baixa e importa um modelo OVA no VMware para criar um servidor em execução no seu vCenter Server. Depois de implantar o dispositivo, você precisa registrá-lo no projeto e configurá-lo para iniciar a descoberta.
6. Ao configurar o dispositivo, você precisa especificar o seguinte no Gerenciador de configuração de dispositivo:
    - Os detalhes do vCenter Server ao qual você deseja se conectar.
    - vCenter Server credenciais com escopo para descobrir os servidores em seu ambiente VMware.
    - As credenciais do servidor, que podem ser credenciais de domínio/Windows (não domínio)/Linux (não domínio). [Saiba mais](add-server-credentials.md) sobre como fornecer credenciais e como as manipulamos.​

## <a name="verify-permissions"></a>Verificar permissões

- Você precisa [criar um vCenter Server conta somente leitura](./tutorial-discover-vmware.md#prepare-vmware) para descoberta e avaliação. A conta somente leitura precisa de privilégios habilitados para operações de convidado de **máquinas virtuais**  >  , a fim de interagir com os servidores para executar o inventário de software.
- Você pode adicionar várias credenciais de domínio e não domínio (Windows/Linux) no Gerenciador de configuração do dispositivo para descoberta de aplicativos. Você precisa de uma conta de usuário convidado para servidores Windows e uma conta de usuário regular/normal (acesso não sudo) para todos os servidores Linux. [Saiba mais](add-server-credentials.md) sobre como fornecer credenciais e como tratá-las.

### <a name="add-credentials-and-initiate-discovery"></a>Adicionar credenciais e iniciar a descoberta

1. Abra o Gerenciador de configuração do dispositivo, conclua as verificações de pré-requisitos e o registro do dispositivo.
2. Navegue até o painel **gerenciar credenciais e fontes de descoberta** .
1.  Na **etapa 1: fornecer credenciais de vCenter Server**, clique em **Adicionar credenciais** para fornecer credenciais para a conta de vCenter Server que o dispositivo usará para descobrir servidores em execução no vCenter Server.
1. Na **etapa 2: fornecer vCenter Server detalhes**, clique em **Adicionar origem de descoberta** para selecionar o nome amigável para credenciais na lista suspensa, especifique o **endereço IP/FQDN** do painel de instância vCenter Server :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="3 no Gerenciador de configuração de dispositivo para obter detalhes de vCenter Server":::
1. Na **etapa 3: fornecer credenciais de servidor para executar o inventário de software, a análise de dependência sem agente e a descoberta de instâncias de SQL Server e bancos de dados**, clique em **Adicionar credenciais** para fornecer várias credenciais de servidor para iniciar o inventário de software.
1. Clique em **Iniciar descoberta** para iniciar a descoberta do vCenter Server.

 Após a conclusão da descoberta de vCenter Server, o dispositivo inicia a descoberta de aplicativos, funções e recursos instalados (inventário de software). A duração depende do número de servidores descobertos. Para 500 servidores, leva aproximadamente uma hora para o inventário de aplicativos aparecer no portal das Migrações para Azure.

## <a name="review-and-export-the-inventory"></a>Revisar e exportar o inventário

Após a conclusão do inventário de software, você pode revisar e exportar o inventário no portal do Azure.

1. Em **migrações para Azure-Windows, Linux e SQL Servers**  >  **migrações do Azure: descoberta e avaliação**, clique na contagem exibida para abrir a página **servidores descobertos** .

    > [!NOTE]
    > Nesse estágio, opcionalmente, você também pode habilitar a análise de dependência para os servidores descobertos, para que você possa visualizar as dependências entre os servidores que deseja avaliar. [Saiba mais](concepts-dependency-visualization.md) sobre a análise de dependência.

2. Na coluna **inventário de software** , clique na contagem exibida para examinar os aplicativos, as funções e os recursos descobertos.
4. Para exportar o inventário, em **servidores descobertos**, clique em **Exportar inventário de aplicativos**.

O inventário de software é exportado e baixado no formato do Excel. A folha de **inventário de software** exibe todos os aplicativos descobertos em todos os servidores.

## <a name="discover-sql-server-instances-and-databases"></a>Descobrir SQL Server instâncias e bancos de dados

- O inventário de software também identifica as instâncias de SQL Server em execução em seu ambiente VMware.
- Se você não tiver fornecido autenticação do Windows nem SQL Server credenciais de autenticação no Gerenciador de configuração de dispositivo, adicione as credenciais para que o dispositivo possa usá-las para se conectar às respectivas instâncias de SQL Server.

Uma vez conectado, o dispositivo coleta dados de desempenho e configuração de SQL Server instâncias e bancos de dado. Os dados de configuração do SQL Server são atualizados uma vez a cada 24 horas e os dados de desempenho são capturados a cada 30 segundos. Portanto, qualquer alteração nas propriedades da SQL Server instância e dos bancos de dados, como status do banco de dados, nível de compatibilidade, etc. pode levar até 24 horas para ser atualizada no Portal.

## <a name="next-steps"></a>Próximas etapas

- [Crie uma avaliação](how-to-create-assessment.md) para servidores descobertos.
- [Avaliar os SQL Servers](./tutorial-assess-sql.md) para migração para o SQL do Azure.
