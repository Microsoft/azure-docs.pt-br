---
title: Gerenciar usuários e funções de banco de dados no Azure Analysis Services | Microsoft Docs
description: Saiba como gerenciar usuários e funções de banco de dados em um servidor do Analysis Services no Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 31910e92ba4d5cbb1f133eaff6880fafb809b772
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99054086"
---
# <a name="manage-database-roles-and-users"></a>Gerenciar usuários e funções de banco de dados

No nível do modelo de banco de dados, todos os usuários devem pertencer a uma função. As funções definem os usuários com permissões específicas para o modelo de banco de dados. Qualquer usuário ou grupo de segurança adicionado a uma função deve ter uma conta em um locatário do Azure AD na mesma assinatura que o servidor. 

Como você define as funções é diferente dependendo da ferramenta usada, mas o efeito é o mesmo.

As permissões de função incluem:
*  **Administrador** – os usuários têm permissões completas para o banco de dados. Funções de banco de dados com permissões de administrador são diferentes dos administradores do servidor.
*  **Processar** – os usuários podem se conectar e executar operações de processo no banco de dados e analisar os dados do modelo de banco de dados.
*  **Leitura** – os usuários podem usar um aplicativo cliente para se conectar e analisar os dados do modelo de banco de dados.

Ao criar um projeto de modelo de tabela, você cria funções e adiciona usuários ou grupos a essas funções usando o Gerenciador de funções no Visual Studio com projetos Analysis Services. Quando implantado em um servidor, use SQL Server Management Studio (SSMS), [Analysis Services cmdlets do PowerShell](/analysis-services/powershell/analysis-services-powershell-reference)ou TMSL ( [linguagem de script de modelo de tabela](/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) ) para adicionar ou remover funções e membros do usuário.

Ao adicionar um **grupo de segurança**, use `obj:groupid@tenantid` .

Ao adicionar uma **entidade de serviço** , use `app:appid@tenantid` .

## <a name="to-add-or-manage-roles-and-users-in-visual-studio"></a>Para adicionar ou gerenciar funções e usuários no Visual Studio  
  
1.  No **Gerenciador de modelos de tabela**, clique com o botão direito do mouse em **funções**.  
  
2.  No **Gerenciador de funções**, clique em **novo**.  
  
3.  Digite um nome para a função.  
  
     Por padrão, o nome da função padrão será numerado incrementalmente para cada nova função. É recomendável que você digite um nome que claramente Identifique o tipo de membro, por exemplo, Gerentes Financeiros ou Especialistas de Recursos Humanos.  
  
4.  Selecione uma das seguintes permissões:  
  
    |Permissão|Descrição|  
    |----------------|-----------------|  
    |**Nenhuma**|Os membros não podem ler ou modificar o esquema de modelo e não podem consultar dados.|  
    |**Ler**|Os membros podem consultar dados (com base em filtros de linha), mas não podem modificar o esquema de modelo.|  
    |**Leitura e processo**|Os membros podem consultar dados (com base em filtros de nível de linha) e executar as operações Processar e Processar Tudo, mas não podem modificar o esquema de modelo.|  
    |**Processo**|Membros podem executar operações de Processar e Processar Tudo. Não é possível ler ou modificar o esquema de modelo e não é possível consultar dados.|  
    |**Administrador**|Os membros podem modificar o esquema de modelo e consultar todos os dados.|   
  
5.  Se a função que você está criando tem a permissão Ler ou Ler e Processar, é possível adicionar filtros de linha usando uma fórmula DAX. Clique na guia **Filtros de Linha**, selecione uma tabela, clique no campo **Filtro DAX** e digite uma fórmula DAX.
  
6.  Clique em **Membros**  >  **Adicionar externo**.  
  
8.  Em **Adicionar Membro Externo**, insira usuários ou grupos no seu locatário do Azure AD pelo endereço de email. Depois que você clicar em OK e fechar o Gerenciador de Funções, as funções e membros da função aparecem no Gerenciador de Modelos Tabular. 
 
     ![Funções e usuários no Gerenciador de Modelos Tabular](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Implante em seu servidor do Azure Analysis Services.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>Para adicionar ou gerenciar funções e usuários no SSMS

Para adicionar funções e usuários a um modelo de banco de dados implantado, você deve estar conectado ao servidor como Administrador do servidor ou já estar em uma função de banco de dados com permissões de administrador.

1. Em objeto explorador, clique com o botão direito do mouse em **funções**  >  **nova função**.

2. Em **Criar Função**, insira um nome de função e uma descrição.

3. Selecione uma permissão.

   |Permissão|Descrição|  
   |----------------|-----------------|  
   |**Controle total (Administrador)**|Os membros podem modificar o esquema de modelo, processar e consultar todos os dados.| 
   |**Processar Banco de Dados**|Membros podem executar operações de Processar e Processar Tudo. Não é possível modificar o esquema modelo e não é possível consultar dados.|  
   |**Ler**|Os membros podem consultar dados (com base em filtros de linha), mas não podem modificar o esquema de modelo.|  
  
4. Clique em **Associação**, em seguida, insira um usuário ou grupo no seu locatário do Azure AD pelo endereço de email.

     ![Adicionar usuário](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. Se a função que você está criando tem a permissão Ler, é possível adicionar filtros de linha usando uma fórmula DAX. Clique em **Filtros de Linha**, selecione uma tabela e, em seguida, digite uma fórmula DAX no campo **Filtro DAX**. 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>Para adicionar funções e usuários usando um script TMSL

Você pode executar um script TMSL na janela XMLA no SSMS ou usando o PowerShell. Use o comando [CreateOrReplace](/analysis-services/tmsl/createorreplace-command-tmsl) e o objeto [Roles](/analysis-services/tmsl/roles-object-tmsl).

**Exemplo de script TMSL**

Neste exemplo, um usuário externo B2B e um grupo são adicionados à função Analista com permissões de leitura para o banco de dados SalesBI. O usuário externo e o grupo devem estar no mesmo locatário do Azure AD.

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Analyst"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@adventureworks.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="to-add-roles-and-users-by-using-powershell"></a>Para adicionar funções e usuários usando o PowerShell

O módulo [SqlServer](/analysis-services/powershell/analysis-services-powershell-reference) fornece cmdlets de gerenciamento de banco de dados de tarefas específicas e o cmdlet Invoke-ASCmd de uso geral, que aceita um script ou consulta de TMSL (Linguagem de Script de Modelo Tabular). Os cmdlets a seguir são usados para gerenciar usuários e funções de banco de dados.
  
|Cmdlet|Descrição|
|------------|-----------------| 
|[Add-RoleMember](/powershell/module/sqlserver/Add-RoleMember)|Adicionar um membro a uma função de banco de dados.| 
|[Remove-RoleMember](/powershell/module/sqlserver/remove-rolemember)|Remover um membro de uma função de banco de dados.|   
|[Invoke-ASCmd](/powershell/module/sqlserver/invoke-ascmd)|Executar um script TMSL.|

## <a name="row-filters"></a>Filtros de linha  

Os filtros de linha definem quais linhas em uma tabela podem ser consultadas por membros de uma função específica. Os filtros de linha são definidos para cada tabela em um modelo usando fórmulas DAX.  
  
Os filtros de linha podem ser definidos somente para funções com as permissões Ler e Ler e Processar. Por padrão, se um filtro de linha não está definido para uma tabela específica, os membros podem consultar todas as linhas na tabela, a menos que a filtragem cruzada se aplique de outra tabela.
  
 Os filtros de linha exigem uma fórmula DAX, que deve ser avaliada como um valor TRUE/FALSE, para definir as linhas que podem ser consultadas por membros daquela função específica. Não é possível consultar linhas não incluídas na fórmula DAX. Por exemplo, a tabela Customers com a seguinte expressão de filtros de linha, *= Customers [Country] = "usa"*, os membros da função Sales só podem ver os clientes nos EUA.  
  
Os filtros de linha são aplicados às linhas especificadas e às linhas relacionadas. Quando uma tabela tem várias relações, os filtros aplicam segurança para a relação ativa. Os filtros de linha são interseccionados com outros filtros de linha definidos para tabelas relacionadas, por exemplo:  
  
|Tabela|Expressão DAX|  
|-----------|--------------------|  
|Região|= Região [País] = "USA"|  
|ProductCategory|= ProductCategory [Name] = "Bicicletas"|  
|Transactions|=Transactions[Year]=2016|  
  
 O efeito líquido é que os membros podem consultar as linhas de dados em que o cliente está nos EUA, a categoria de produto é bicicletas e o ano é 2016. Os usuários não podem consultar transações fora dos EUA, transações que não são bicicletas ou transações que não são em 2016 amenos que sejam um membro de outra função que concede essas permissões.
  
 Você pode usar o filtro, *=FALSE()*, para negar o acesso a todas as linhas de uma tabela inteira.

## <a name="next-steps"></a>Próximas etapas

  [Gerenciar administradores de servidor](analysis-services-server-admins.md)   
  [Gerenciar o Azure Analysis Services com PowerShell](analysis-services-powershell.md)  
  [Referência de TMSL (Linguagem de Scripts do Modelo Tabular)](/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference)
