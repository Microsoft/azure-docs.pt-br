---
title: Configurar um laboratório para ensinar o gerenciamento de banco de dados para bancos de dados relacionais | Microsoft Docs
description: Saiba como configurar um laboratório para ensinar o gerenciamento de bancos de dados relacionais.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 32079d6dc31cf3fcad66976737630472129b4271
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96462421"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>Configurar um laboratório para ensinar o gerenciamento de bancos de dados relacionais

Este artigo descreve como configurar um laboratório para uma classe de gerenciamento de bancos de dados básico no Azure Lab Services. Os conceitos de bancos de dados são um dos cursos introdutórios ensinados na maioria dos departamentos de ciência da computação na faculdade. O linguagem SQL (SQL) é um padrão internacional. O SQL é a linguagem padrão para gerenciamento de banco de dados de relações, incluindo a adição, o acesso e o gerenciamento de conteúdo em um banco de dados.  Ele é mais observado para seu processamento rápido, confiabilidade comprovada, facilidade e flexibilidade de uso.

Neste artigo, mostraremos como configurar um modelo de máquina virtual em um laboratório com o servidor de banco de dados MySQL e o servidor SQL Server 2019.  O [MySQL](https://www.mysql.com/) é um RDBMS (sistema de gerenciamento de banco de dados relacional) de software livre disponível gratuitamente.  [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) é a versão mais recente do RDBMS da Microsoft.

## <a name="lab-configuration"></a>Configuração do laboratório

Para configurar este laboratório, você precisa de uma assinatura do Azure e uma conta de laboratório para começar. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Depois de obter uma assinatura do Azure, você poderá criar uma nova conta de laboratório no Azure Lab Services. Para obter mais informações sobre como criar uma nova conta de laboratório, consulte [tutorial para configurar uma conta de laboratório](tutorial-setup-lab-account.md).  Você também pode usar uma conta de laboratório existente.

### <a name="lab-account-settings"></a>Configurações de conta do laboratório

Habilite as configurações descritas na tabela a seguir para a conta do laboratório. Para obter mais informações sobre como habilitar imagens do Marketplace, consulte [especificar imagens do Marketplace disponíveis para criadores de laboratório](./specify-marketplace-images.md).

| Configuração da conta de laboratório | Instruções |
| ------------------- | ------------ |
|Imagem do Marketplace| Habilite a imagem ' SQL Server 2019 Standard no Windows Server 2019 ' para uso em sua conta de laboratório.|

### <a name="lab-settings"></a>Configurações do laboratório

Use as configurações na tabela abaixo ao configurar um laboratório de sala de aula.  Para obter mais informações sobre como criar um laboratório de sala de aula, consulte [configurar um tutorial de laboratório de sala de aula](tutorial-setup-classroom-lab.md).

| Configurações do laboratório | Valor/instruções |
| ------------ | ------------------ |
|Tamanho da Máquina Virtual| Média: Este tamanho é mais adequado a bancos de dados relacionais, cache na memória e análise.|
|Imagem de máquina virtual| SQL Server 2019 Standard no Windows Server 2019|

## <a name="template-machine-configuration"></a>Configuração da máquina de modelo

Para instalar o MySQL no Windows Server 2019, você pode seguir as etapas mencionadas em [instalar e executar o MySQL Community Server em uma máquina virtual](/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine).

SQL Server 2019 é pré-instalado na imagem de máquina virtual que escolhemos ao criar o novo laboratório.

## <a name="cost-estimate"></a>Estimativa de custo

Vamos abordar uma possível estimativa de custo para essa classe.  Usaremos uma classe de 25 alunos.  Há 20 horas de tempo de classe agendada.  Além disso, cada aluno recebe uma cota de 10 horas de casa ou atribuições fora do tempo de classe agendado.  O tamanho da máquina virtual que escolhemos era médio, que é 42 unidades de laboratório.

Aqui está um exemplo de uma possível estimativa de custo para esta classe:

25 alunos \* (20 horas agendadas + 10 horas de cota) \* 0,42 USD por hora = us $315, 0

Para obter mais detalhes sobre os preços, confira [Preço do Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusão

Este artigo descreveu as etapas necessárias para criar um laboratório para conceitos básicos de gerenciamento de banco de dados usando o MySQL e o SQL Server. Você pode usar uma configuração semelhante para outras classes de bancos de dados.

## <a name="next-steps"></a>Próximas etapas

As próximas etapas são comuns à configuração de qualquer laboratório.

- [Criar e gerenciar um modelo](how-to-create-manage-template.md)
- [Adicionar usuários](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um agendamento](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de registro de email para alunos](how-to-configure-student-usage.md#send-invitations-to-users)