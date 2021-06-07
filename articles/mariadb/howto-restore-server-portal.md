---
title: Backup e restauração-portal do Azure-banco de dados do Azure para MariaDB
description: Este artigo descreve como restaurar um servidor no Banco de Dados do Azure para MariaDB usando o Portal do Azure.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/30/2020
ms.openlocfilehash: 4a5f2cc4cbf73f5c13533a94f1454022d3538880
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664810"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mariadb-using-the-azure-portal"></a>Como fazer backup e restaurar um servidor no Banco de Dados do Azure para MariaDB usando o Portal do Azure

## <a name="backup-happens-automatically"></a>O backup ocorre automaticamente
O backup dos servidores do Banco de Dados do Azure para MariaDB é realizado periodicamente para habilitar recursos de restauração. Com esse recurso de backup automático, você pode restaurar o servidor e todos os seus bancos de dados para um ponto anterior em um novo servidor.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de instruções, você precisa:
- Um [banco de dados e um servidor do Banco de Dados do Azure para MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="set-backup-configuration"></a>Definir configuração de backup

Escolha entre configurar o servidor para backups com redundância local ou backups com redundância geográfica na criação do servidor, na janela **Tipo de Preço**.

> [!NOTE]
> Depois que um servidor é criado, o tipo de redundância que ele tem, geográfica ou local, não pode ser alternado.
>

Ao criar um servidor por meio do portal do Azure, a janela **Tipo de Preço** é onde você seleciona backups **Com Redundância Local** ou **Com Redundância Geográfica** para o servidor. Essa janela também é onde você seleciona o **Período de Retenção de Backup**: quanto tempo (em número de dias) você deseja que os backups de servidor sejam armazenados.

   ![Tipo de preço - Escolher redundância de backup](./media/howto-restore-server-portal/pricing-tier.png)

Para saber mais sobre como definir esses valores de durante a criação, confira [início rápido do servidor do Banco de Dados do Azure para MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md).

O período de retenção de backup pode ser alterado em um servidor por meio das seguintes etapas:
1. Faça logon no [Portal do Azure](https://portal.azure.com/).

2. Selecione o servidor do Banco de Dados do Azure para MariaDB. Essa ação abre a página **Visão geral** do runbook.

3. Selecione **Tipo de Preço** no menu, em **CONFIGURAÇÕES**. Usando o controle deslizante, você pode alterar o **Período de Retenção de Backup** entre 7 e 35 dias, conforme a sua preferência.
Na captura de tela abaixo, ele foi aumentado para 35 dias.
![Período de retenção de backup aumentado](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Clique em **OK** para confirmar a alteração.

O período de retenção de backup determina até quando a restauração de pontos anteriores pode ser feita, já que ele se baseia em backups disponíveis. A Restauração pontual é descrita mais detalhadamente na seção a seguir. 

## <a name="point-in-time-restore"></a>Restauração em um momento determinado
O Banco de Dados do Azure para MariaDB permite a restauração pontual do servidor e em uma nova cópia do servidor. Você pode usar esse novo servidor para recuperar seus dados ou fazer seu aplicativo cliente apontar para esse novo servidor.

Por exemplo, se uma tabela for acidentalmente descartada ao meio-dia de hoje, você poderá restaurar em um momento logo antes do meio-dia e recuperar a tabela e os dados dessa nova cópia do servidor. A restauração pontual está no nível do servidor, não no nível do banco de dados.

As etapas a seguir restauram o exemplo de servidor para um ponto anterior:
1. No portal do Azure, selecione o servidor do Banco de Dados do Azure para MariaDB. 

2. Na barra de ferramentas da página **Visão geral** do servidor, selecione **Restaurar**.

   ![Banco de Dados do Azure para MariaDB – Visão geral – Botão Restaurar](./media/howto-restore-server-portal/2-server.png)

3. Preencha o formulário Restaurar com as informações necessárias:

   ![Banco de Dados do Azure para MariaDB – Informações sobre restauração](./media/howto-restore-server-portal/3-restore.png)
   - **Ponto de restauração**: selecione o ponto para o qual você deseja restaurar.
   - **Servidor de destino**: forneça um nome para o novo servidor.
   - **Local**: não é possível selecionar a região. Por padrão, é o mesmo que o servidor de origem.
   - **Tipo de preço**: você não pode alterar esses parâmetros ao fazer uma restauração pontual. Ele é igual ao servidor de origem. 

4. Clique em **OK** para restaurar o servidor em um ponto anterior. 

5. Após a conclusão da restauração, localize o novo servidor criado para verificar se os dados foram restaurados conforme o esperado.

O novo servidor criado pela restauração pontual tem o mesmo nome de logon e senha do administrador válidos para o servidor existente no ponto escolhido. Você pode alterar a senha na página **Visão geral** do novo servidor.

O servidor criado durante uma restauração não tem o ponto de extremidade de serviço VNet existentes no servidor original. Essas regras precisam ser configuradas separadamente para esse novo servidor. As regras de firewall do servidor original são restauradas.

## <a name="geo-restore"></a>Restauração geográfica

Se você configurou seu servidor para backups com redundância geográfica, um novo servidor pode ser criado do backup do servidor existente. Esse novo servidor pode ser criado em qualquer região em que o Banco de Dados do Azure para MariaDB está disponível.  

1. Selecione o botão **Criar um recurso** (+) no canto superior esquerdo do portal. Selecione **Bancos de Dados** > **Banco de Dados do Azure para MariaDB**.

   :::image type="content" source="./media/howto-restore-server-portal/2_navigate-to-mariadb.png" alt-text="Navegue até o banco de dados do Azure para MariaDB.":::
 
2. Forneça a assinatura, o grupo de recursos e o nome do novo servidor. 

3. Selecione **backup** como a **fonte de dados**. Essa ação carrega um menu suspenso que fornece uma lista de servidores que têm backups com redundância geográfica habilitada.
   
   :::image type="content" source="./media/howto-restore-server-portal/3-geo-restore.png" alt-text="Selecione a fonte de dados.":::
    
   > [!NOTE]
   > Quando um servidor é criado pela primeira vez, talvez não fique imediatamente disponível para restauração geográfica. Pode demorar algumas horas para que os metadados necessários sejam preenchidos.
   >

4. Selecione o menu suspenso **backup** .
   
   :::image type="content" source="./media/howto-restore-server-portal/4-geo-restore-backup.png" alt-text="Selecione lista suspensa de backup.":::

5. Selecione o servidor de origem do qual restaurar.
   
   :::image type="content" source="./media/howto-restore-server-portal/5-select-backup.png" alt-text="Selecione backup.":::

6. O servidor usará como padrão os valores para o número de **vCores**, o **período de retenção de backup**, a opção de redundância de **backup**, a **versão do mecanismo** e **as credenciais de administrador**. Selecione **Continuar**. 
   
   :::image type="content" source="./media/howto-restore-server-portal/6-accept-backup.png" alt-text="Continue com o backup.":::

7. Preencha o restante do formulário com suas preferências. Você pode selecionar qualquer **Local**.

    Depois de selecionar o local, você pode selecionar **Configurar servidor** para atualizar **a geração de computação** (se disponível na região que você escolheu), o número de **VCores**, o período de retenção de **backup** e a opção de **redundância de backup**. Não há suporte para a alteração do **Tipo de Preço** (Básico, Uso Geral ou Otimizado para Memória) ou do tamanho de **Armazenamento** durante a restauração.

   :::image type="content" source="./media/howto-restore-server-portal/7-create.png" alt-text="Preencher formulário."::: 

8. Selecione **Revisar + criar** para revisar suas seleções. 

9. Selecione **Criar** para provisionar o servidor. Esta operação pode levar alguns minutos.

O novo servidor criado pela restauração geográfica tem o mesmo nome de logon e senha do administrador válidos para o servidor existente no momento em que a restauração foi iniciada. A senha pode ser alterada na página **Visão geral** do servidor.

O servidor criado durante uma restauração não tem o ponto de extremidade de serviço VNet existentes no servidor original. Essas regras precisam ser configuradas separadamente para esse novo servidor. As regras de firewall do servidor original são restauradas.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre os [backups](concepts-backup.md) do serviço
- Saiba mais sobre [réplicas](concepts-read-replicas.md)
- Saiba mais sobre as opções de [continuidade dos negócios](concepts-business-continuity.md)