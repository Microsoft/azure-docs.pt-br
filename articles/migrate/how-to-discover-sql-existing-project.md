---
title: Descobrir SQL Server instâncias em um projeto existente de migrações para Azure
description: Saiba como descobrir SQL Server instâncias em um projeto atual de migrações para Azure.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2021
ms.openlocfilehash: 2de60880b511e43ffb2949a15fec2cf2a94f62fa
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567145"
---
# <a name="discover-sql-server-instances-in-an-existing-project"></a>Descobrir SQL Server instâncias em um projeto existente 

Este artigo descreve como descobrir SQL Server instâncias e bancos de dados em um projeto de [migrações para Azure](./migrate-services-overview.md) que foi criado antes da versão prévia do recurso de avaliação do SQL do Azure.

Descobrir SQL Server instâncias e bancos de dados em execução em computadores locais ajuda a identificar e personalizar um caminho de migração para o SQL do Azure. O dispositivo de migração do Azure executa essa descoberta usando as credenciais de domínio ou SQL Server credenciais de autenticação que têm acesso às instâncias de SQL Server e bancos de dados em execução nos servidores de destino. Esse processo de descoberta é sem agente, ou seja, nada é instalado nos servidores de destino.

## <a name="before-you-start"></a>Antes de começar

- Verifique se você:
    - Criou um [projeto de migrações para Azure](./create-manage-projects.md) antes do anúncio do recurso de avaliação do SQL para sua região
    - Adicionada a ferramenta [migrações do Azure: descoberta e avaliação](./how-to-assess.md) para um projeto
- Examine o [suporte e os requisitos de descoberta de aplicativos](./migrate-support-matrix-vmware.md#vmware-requirements).
-  Verifique se os servidores em que você está executando a descoberta de aplicativos têm o PowerShell versão 2,0 ou posterior instalado, e as ferramentas do VMware (posterior à 10.2.0) estão instaladas.
- Verifique os [requisitos](./migrate-appliance.md) para implantar o dispositivo de migrações para Azure.
- Verifique se você tem as [funções necessárias](./create-manage-projects.md#verify-permissions) na assinatura para criar recursos.
- Verifique se seu dispositivo tem acesso à Internet

## <a name="enable-discovery-of-sql-server-instances-and-databases"></a>Habilitar a descoberta de instâncias de SQL Server e bancos de dados

1. Em seu projeto de migrações para Azure, seja
    - Selecione **não habilitado** no bloco do Hub ou no   :::image type="content" source="./media/how-to-discover-sql-existing-project/hub-not-enabled.png" alt-text="bloco do Hub migrações para Azure com a descoberta do SQL não habilitada":::
    - Selecione **não habilitado** em nenhuma entrada na página de descoberta do servidor na coluna instâncias SQL   :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-not-enabled.png" alt-text="migrar servidores descobertos folha com descoberta SQL não habilitada":::
2. Em descobrir SQL Server instâncias e bancos de dados, siga as etapas encaudadas:
    - Selecione **Atualizar** para criar o recurso necessário.
        :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-upgrade-appliance.png" alt-text="Botão para atualizar o dispositivo de migrações para Azure":::
    - Valide se os serviços em execução no dispositivo foram atualizados para as versões mais recentes. Para fazer isso, inicie o Gerenciador de configuração do dispositivo do seu servidor de dispositivo e selecione Exibir serviços de dispositivo no painel pré-requisitos de instalação.
        - O dispositivo e seus componentes são atualizados automaticamente :::image type="content" source="./media/how-to-discover-sql-existing-project/appliance-services-version.png" alt-text="Verifique a versão do dispositivo":::
    - No painel gerenciar credenciais e fontes de descoberta do Gerenciador de configurações do dispositivo, adicione domínio ou SQL Server credenciais de autenticação que têm acesso sysadmin na instância do SQL Server e os bancos de dados a serem descobertos.
    Você pode aproveitar o recurso de mapeamento automático de credenciais do dispositivo ou mapear manualmente as credenciais para o respectivo servidor conforme realçado [aqui](./tutorial-discover-vmware.md#start-continuous-discovery).

    Alguns pontos a serem observados:
    - Verifique se o inventário de software já está habilitado ou forneça credenciais de domínio ou não de domínio para habilitar o mesmo. O inventário de software deve ser executado para descobrir SQL Server instâncias.
    - O dispositivo tentará validar as credenciais de domínio com o AD, à medida que elas forem adicionadas. Verifique se o servidor de dispositivo tem a linha de visão de rede para o servidor do AD associado às credenciais. As credenciais associadas à autenticação SQL Server não são validadas.

3. Depois que as credenciais desejadas forem adicionadas, selecione Iniciar descoberta para iniciar a verificação.

> [!Note]
>Permita que a descoberta de SQL seja executada por algum tempo antes de criar Avaliações para o SQL do Azure. Se a descoberta de instâncias de SQL Server e bancos de dados não tiver permissão para ser concluída, as respectivas instâncias serão marcadas como **desconhecidas** no relatório de avaliação.

## <a name="next-steps"></a>Próximas etapas

- Saiba como criar uma [avaliação do SQL do Azure](./how-to-create-azure-sql-assessment.md)
- Saiba mais sobre as [avaliações do SQL Azure](./concepts-azure-sql-assessment-calculation.md)