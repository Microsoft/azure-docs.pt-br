---
title: Conectar-se ao Azure Analysis Services com Excel | Microsoft Docs
description: Saiba como se conectar a um servidor Azure Analysis Services usando Excel. Uma vez conectado, os usuários podem criar tabelas dinâmicas para explorar dados.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: fccb919a7eac79080fefeac70326b8aa7a967622
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96499463"
---
# <a name="connect-with-excel"></a>Conectar com o Excel

Após criar um servidor e implantar um modelo tabular, os clientes poderão conectar e começar a explorar os dados. 

## <a name="before-you-begin"></a>Antes de começar

A conta de logon precisa pertencer a uma função de modelo de banco de dados com, pelo menos, permissões de leitura. Para obter mais informações, confira [Autenticação e permissões de usuário](analysis-services-manage-users.md). 

## <a name="connect-in-excel"></a>Conectar-se no Excel

A conexão com um servidor no Excel usando Obter Dados no Excel 2016 e posterior é compatível. Não há suporte para a conexão usando o Assistente para Importar tabela no Power Pivot. 

1. No Excel, na faixa de, **dados** , clique em **obter dados**  >  **do banco**  >  **de Analysis Services**.

2. No Assistente de Conexão de Dados, em **Nome do servidor**, insira o nome do servidor, incluindo protocolo e URI. Por exemplo, asazure://westcentralus.asazure.windows.net/advworks. Em seguida, em **Credenciais de logon**, selecione **Usar o seguinte Nome de Usuário e Senha** e digite o nome de usuário da organização, por exemplo nancy@adventureworks.com, e a senha.

    > [!IMPORTANT]
    > Se você entrar com uma Conta da Microsoft, Live ID, Yahoo, Gmail e etc., ou precisar entrar com autenticação multifator, deixe o campo de senha em branco. Depois de clicar em Avançar, uma senha será solicitada. 

    ![Conectar a partir do logon do Excel](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. Em **Selecionar Banco de Dados e Tabela**, selecione o banco de dados e um modelo ou perspectiva e, em seguida, clique em **Concluir**.
   
    ![Conectar a partir do modelo de seleção do Excel](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Confira também

[Bibliotecas do cliente](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true)   
[Gerenciar seu serviço](analysis-services-manage.md)
