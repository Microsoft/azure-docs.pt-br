---
title: Sincronização de hash de senha seletiva para Azure AD Connect
description: Este artigo descreve como configurar e configurar a sincronização de hash de senha seletiva para usar com Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2021
ms.subservice: hybrid
ms.author: billmath
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 248d5e163eb046edd130d69307a1c553d434b92d
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604661"
---
# <a name="selective-password-hash-synchronization-configuration-for-azure-ad-connect"></a>Configuração de sincronização de hash de senha seletiva para Azure AD Connect

A [sincronização de hash de senha](whatis-phs.md) é um dos métodos de entrada usados para realizar a identidade híbrida. O Azure AD Connect sincroniza um hash, do hash, da senha de um usuário de uma instância do Active Directory local para uma instância do Azure AD baseada em nuvem.  Por padrão, após a instalação, a sincronização de hash de senha ocorrerá em todos os usuários que você estiver sincronizando.

Se desejar que um subconjunto de usuários seja excluído da sincronização de seu hash de senha para o Azure AD, você poderá configurar a sincronização de hash de senha seletiva usando as etapas guiadas fornecidas neste artigo.

>[!Important]
> A Microsoft não oferece suporte à modificação ou à operação da sincronização do Azure AD Connect fora das configurações ou ações formalmente documentadas. Qualquer uma dessas configurações ou ações pode resultar em um estado inconsistente ou sem suporte de Azure AD Connect sincronização. Como resultado, a Microsoft não poderá garantir que possamos fornecer suporte técnico eficiente para tais implantações. 


## <a name="consider-your-implementation"></a>Considere sua implementação  
Para reduzir o esforço administrativo de configuração, primeiro você deve considerar o número de objetos de usuário que deseja excluir da sincronização de hash de senha. Verifique quais dos cenários abaixo, que são mutuamente exclusivos, se alinha com seus requisitos para selecionar a opção de configuração correta para você.
- Se o número de usuários a serem **excluídos** for **menor** do que o número de usuários a serem **incluídos**, siga as etapas nesta [seção](#excluded-users-is-smaller-than-included-users).
- Se o número de usuários a serem **excluídos** for **maior** que o número de usuários a serem **incluídos**, siga as etapas nesta [seção](#excluded-users-is-larger-than-included-users).

> [!Important]
> Com qualquer opção de configuração escolhida, uma sincronização inicial necessária (sincronização completa) para aplicar as alterações será executada automaticamente no próximo ciclo de sincronização.

> [!Important]
> Configurar a sincronização de hash de senha seletiva influencia diretamente o Write-back de senha. As alterações de senha ou redefinições de senha iniciadas no Azure Active Directory write-back para o local Active Directory somente se o usuário estiver no escopo para a sincronização de hash de senha. 

### <a name="the-admindescription-attribute"></a>O atributo adminDescription
Ambos os cenários dependem da definição do atributo adminDescription de usuários para um valor específico.  Isso permite que as regras sejam aplicadas e é o que faz o PHS seletivo funcionar.

|Cenário|valor de adminDescription|
|-----|-----|
|Usuários excluídos são menores do que os usuários incluídos|PHSFiltered|
|Usuários excluídos são maiores do que os usuários incluídos|PHSIncluded|

Esse atributo pode ser definido como:

- usando a interface do usuário do Active Directory usuários e computadores
- usando o `Set-ADUser` cmdlet do PowerShell.  Para obter mais informações, consulte [set-ADUser](https://docs.microsoft.com/powershell/module/addsadministration/set-aduser).

 


### <a name="disable-the-synchronization-scheduler"></a>Desabilite o Agendador de sincronização:
Antes de iniciar qualquer cenário, você deve desabilitar o Agendador de sincronização ao fazer alterações nas regras de sincronização.
 1. Inicie o Windows PowerShell Enter.

     ```Set-ADSyncScheduler -SyncCycleEnabled $false``` 
 
2.  Confirme se o Agendador está desabilitado executando o seguinte cmdlet:
     
    ```Get-ADSyncScheduler```

Para obter mais informações sobre o Agendador, consulte [Azure ad Connect o Agendador de sincronização](how-to-connect-sync-feature-scheduler.md).




## <a name="excluded-users-is-smaller-than-included-users"></a>Usuários excluídos são menores do que os usuários incluídos
A seção a seguir descreve como habilitar a sincronização de hash de senha seletiva quando o número de usuários a serem **excluídos** é **menor** do que o número de usuários a serem **incluídos**.

>[!Important]
> Antes de continuar, verifique se o Agendador de sincronização está desabilitado conforme descrito acima.

- Crie uma cópia editável do **no AD – usuário AccountEnabled** com a opção de habilitar a **sincronização de hash de senha desmarcada** e definir seu filtro de escopo 
- Crie outra cópia editável do padrão **no do AD – usuário AccountEnabled** com a opção de **habilitar a sincronização de hash de senha selecionada** e definir seu filtro de escopo 
- Reabilitar o Agendador de sincronização 
- Defina o valor do atributo, no Active Directory, que foi definido como atributo de escopo nos usuários que você deseja permitir na sincronização de hash de senha. 

>[!Important]
>As etapas fornecidas para configurar a sincronização de hash de senha seletiva só afetarão os objetos do usuário que tenham o atributo **adminDescription** populado em Active Directory com o valor de **PHSFiltered**.
Se esse atributo não for preenchido ou o valor for algo diferente de **PHSFiltered** , essas regras não serão aplicadas aos objetos de usuário.


### <a name="configure-the-necessary-synchronization-rules"></a>Configure as regras de sincronização necessárias:

 1. Inicie o editor de regras de sincronização e defina os filtros **sincronização de senha** como **ativado** e **tipo de regra** como **padrão**.
     ![Iniciar o editor de regras de sincronização](media/how-to-connect-selective-password-hash-synchronization/exclude-1.png)
 2. Selecione a regra **entrada do AD – usuário AccountEnabled** para o conector de floresta Active Directory para o qual você deseja configurar a senha seletiva tinha sincronização de hash no e clique em **Editar**. Selecione **Sim** na caixa de diálogo avançar para criar uma cópia editável da regra original.
     ![Selecionar regra](media/how-to-connect-selective-password-hash-synchronization/exclude-2.png)
 3. A primeira regra desabilitará a sincronização de hash de senha. Forneça o seguinte nome para a nova regra personalizada: **in from ad-usuário AccountEnabled-Filter users from PHS**.
 Altere o valor de precedência para um número inferior a 100 (por exemplo, **90** ou o que for o menor valor disponível em seu ambiente).
 Verifique se as caixas de seleção **habilitam a sincronização de senha** e **desabilitadas** estão desmarcadas e c.
 Clique em **Avançar**.
  ![Editar entrada](media/how-to-connect-selective-password-hash-synchronization/exclude-3.png)
 4. Em **filtro de escopo**, clique em **Adicionar cláusula**.
 Selecione **adminDescription** na coluna atributo, **igual** na coluna operador e digite **PHSFiltered** como o valor.
     ![Filtro de escopo](media/how-to-connect-selective-password-hash-synchronization/exclude-4.png)
 5. Nenhuma alteração adicional é necessária. **As regras** e **transformações** de junção devem ser deixadas com as configurações padrão copiadas para que você possa clicar em **salvar** agora.
 Clique em **OK** na caixa de diálogo de aviso informando que uma sincronização completa será executada no próximo ciclo de sincronização do conector.
     ![Salvar regra](media/how-to-connect-selective-password-hash-synchronization/exclude-5.png)
 6. Em seguida, crie outra regra personalizada com a sincronização de hash de senha habilitada. Selecione novamente a regra padrão **no AD – usuário AccountEnabled** para a floresta Active Directory na qual você deseja configurar a senha seletiva tinha sincronização e clique em **Editar**. Selecione **Sim** na caixa de diálogo avançar para criar uma cópia editável da regra original.
     ![Regra personalizada](media/how-to-connect-selective-password-hash-synchronization/exclude-6.png)
 7. Forneça o seguinte nome para a nova regra personalizada: **in from ad-User AccountEnabled-Users inclusos para PHS**.
 Altere o valor de precedência para um número inferior ao da regra criada anteriormente (neste exemplo, que será **89**).
 Verifique se a caixa de seleção **habilitar Sincronização de senha** está marcada e se a caixa de seleção **desabilitado** está desmarcada.
 Clique em **Avançar**.  
     ![Editar nova regra](media/how-to-connect-selective-password-hash-synchronization/exclude-7.png)
 8. Em **filtro de escopo**, clique em **Adicionar cláusula**.
 Selecione **adminDescription** na coluna atributo, não **igual** na coluna operador e digite **PHSFiltered** como o valor.
     ![Regra de escopo](media/how-to-connect-selective-password-hash-synchronization/exclude-8.png)
 9. Nenhuma alteração adicional é necessária. **As regras** e **transformações** de junção devem ser deixadas com as configurações padrão copiadas para que você possa clicar em **salvar** agora.
 Clique em **OK** na caixa de diálogo de aviso informando que uma sincronização completa será executada no próximo ciclo de sincronização do conector.
     ![Regras de junção](media/how-to-connect-selective-password-hash-synchronization/exclude-9.png)
 10. Confirme a criação de regras. Remova a **sincronização de senha** de filtros em e o tipo **de** **regra** **Standard**. E você deverá ver as novas regras que acabou de criar.
     ![Confirmar regras](media/how-to-connect-selective-password-hash-synchronization/exclude-10.png) 


### <a name="re-enable-synchronization-scheduler"></a>Reabilitar o Agendador de sincronização:  
Depois de concluir as etapas para configurar as regras de sincronização necessárias, habilite novamente o Agendador de sincronização com as seguintes etapas:
 1. Na execução do Windows PowerShell:

     ```Set-ADSyncScheduler -SyncCycleEnabled $true```
 2. Em seguida, confirme se ele foi habilitado com êxito executando:

     ```Get-ADSyncScheduler```

Para obter mais informações sobre o Agendador, consulte [Azure ad Connect o Agendador de sincronização](how-to-connect-sync-feature-scheduler.md).

### <a name="edit-users-admindescription-attribute"></a>Editar usuários **adminDescription** atributo:
Depois que todas as configurações forem concluídas, você precisará editar o atributo **adminDescription** para todos os usuários que deseja **excluir** da sincronização de hash de senha em Active Directory e adicionar a cadeia de caracteres usada no filtro de escopo: **PHSFiltered**.
   
  ![Editar atributo](media/how-to-connect-selective-password-hash-synchronization/exclude-11.png)

Você também pode usar o seguinte comando do PowerShell para editar o atributo **adminDescription** de um usuário:

```Set-ADUser myuser -Replace @{adminDescription="PHSFiltered"}```

## <a name="excluded-users-is-larger-than-included-users"></a>Usuários excluídos são maiores do que os usuários incluídos
A seção a seguir descreve como habilitar a sincronização de hash de senha seletiva quando o número de usuários a serem **excluídos** é **maior** do que o número de usuários a serem **incluídos**.

>[!Important]
> Antes de continuar, verifique se o Agendador de sincronização está desabilitado conforme descrito acima.

Veja a seguir um resumo das ações que serão executadas nas etapas abaixo:

- Crie uma cópia editável do **no AD – usuário AccountEnabled** com a opção de habilitar a **sincronização de hash de senha desmarcada** e definir seu filtro de escopo 
- Crie outra cópia editável do padrão **no do AD – usuário AccountEnabled** com a opção de **habilitar a sincronização de hash de senha selecionada** e definir seu filtro de escopo 
- Reabilitar o Agendador de sincronização 
- Defina o valor do atributo, no Active Directory, que foi definido como atributo de escopo nos usuários que você deseja permitir na sincronização de hash de senha. 

>[!Important]
>As etapas fornecidas para configurar a sincronização de hash de senha seletiva só afetarão os objetos do usuário que tenham o atributo **adminDescription** populado em Active Directory com o valor de **PHSIncluded**.
Se esse atributo não for preenchido ou o valor for algo diferente de **PHSIncluded** , essas regras não serão aplicadas aos objetos de usuário.


### <a name="configure-the-necessary-synchronization-rules"></a>Configure as regras de sincronização necessárias:

 1. Inicie o editor de regras de sincronização e defina a **sincronização de senha** **de filtros no e no** tipo de **regra** **Standard**.
     ![Tipo de regra](media/how-to-connect-selective-password-hash-synchronization/include-1.png)
 2. Selecione a regra **entrada do AD – usuário AccountEnabled** para a floresta Active Directory na qual você deseja configurar a senha seletiva tinha sincronização e clique em **Editar**. Selecione **Sim** na caixa de diálogo avançar para criar uma cópia editável da regra original.
     ![Entrada do AD](media/how-to-connect-selective-password-hash-synchronization/include-2.png)
 3. A primeira regra desabilitará a sincronização de hash de senha. Forneça o seguinte nome para a nova regra personalizada: **in from ad-usuário AccountEnabled-Filter users from PHS**.
 Altere o valor de precedência para um número inferior a 100 (por exemplo, **90** ou o que for o menor valor disponível em seu ambiente).
 Verifique se as caixas de seleção **habilitar Sincronização de senha** e **desabilitado** estão desmarcadas.
 Clique em **Avançar**.
  ![Definir precedência](media/how-to-connect-selective-password-hash-synchronization/include-3.png)
 4. Em **filtro de escopo**, clique em **Adicionar cláusula**.
Selecione **adminDescription** na coluna atributo, não **igual** na coluna operador e digite **PHSIncluded** como o valor.
     ![Adicionar cláusula](media/how-to-connect-selective-password-hash-synchronization/include-4.png)
 5. Nenhuma alteração adicional é necessária. **As regras** e **transformações** de junção devem ser deixadas com as configurações padrão copiadas para que você possa clicar em **salvar** agora.
 Clique em **OK** na caixa de diálogo de aviso informando que uma sincronização completa será executada no próximo ciclo de sincronização do conector.
     ![Transformação](media/how-to-connect-selective-password-hash-synchronization/include-5.png)
 6. Em seguida, crie outra regra personalizada com a sincronização de hash de senha habilitada. Selecione novamente a regra padrão **no AD – usuário AccountEnabled** para a floresta Active Directory na qual você deseja configurar a senha seletiva tinha sincronização e clique em **Editar**. Selecione **Sim** na caixa de diálogo avançar para criar uma cópia editável da regra original.
     ![AccountEnabled do usuário](media/how-to-connect-selective-password-hash-synchronization/include-6.png)
 7. Forneça o seguinte nome para a nova regra personalizada: **in from ad-User AccountEnabled-Users inclusos para PHS**.
 Altere o valor de precedência para um número inferior ao da regra criada anteriormente (neste exemplo, que será **89**).
 Verifique se a caixa de seleção **habilitar Sincronização de senha** está marcada e se a caixa de seleção **desabilitado** está desmarcada.
 Clique em **Avançar**.
     ![Habilitar sincronização de senha](media/how-to-connect-selective-password-hash-synchronization/include-7.png)
 8. Em **filtro de escopo**, clique em **Adicionar cláusula**.
 Selecione **adminDescription** na coluna atributo, **igual** na coluna operador e digite **PHSIncluded** como o valor.
     ![PHSIncluded](media/how-to-connect-selective-password-hash-synchronization/include-8.png)
 9. Nenhuma alteração adicional é necessária. **As regras** e **transformações** de junção devem ser deixadas com as configurações padrão copiadas para que você possa clicar em **salvar** agora.
 Clique em **OK** na caixa de diálogo de aviso informando que uma sincronização completa será executada no próximo ciclo de sincronização do conector.
     ![Salvar agora](media/how-to-connect-selective-password-hash-synchronization/include-9.png)
 10.    Confirme a criação de regras. Remova a **sincronização de senha** de filtros em e o tipo **de** **regra** **Standard**. E você deverá ver as novas regras que acabou de criar.
     ![Sincronizar em](media/how-to-connect-selective-password-hash-synchronization/include-10.png)

### <a name="re-enable-synchronization-scheduler"></a>Reabilitar o Agendador de sincronização:  
Depois de concluir as etapas para configurar as regras de sincronização necessárias, habilite novamente o Agendador de sincronização com as seguintes etapas:
 1. Na execução do Windows PowerShell:

     ```Set-ADSyncScheduler -SyncCycleEnabled $true```
 2. Em seguida, confirme se ele foi habilitado com êxito executando:

     ```Get-ADSyncScheduler```

Para obter mais informações sobre o Agendador, consulte [Azure ad Connect o Agendador de sincronização](how-to-connect-sync-feature-scheduler.md).

### <a name="edit-users-admindescription-attribute"></a>Editar usuários **adminDescription** atributo:
Depois que todas as configurações forem concluídas, você precisará editar o atributo **adminDescription** para todos os usuários que deseja **incluir** para a sincronização de hash de senha em Active Directory e adicionar a cadeia de caracteres usada no filtro de escopo: **PHSIncluded**.

  ![Editar atributos](media/how-to-connect-selective-password-hash-synchronization/include-11.png)
 
 Você também pode usar o seguinte comando do PowerShell para editar o atributo **adminDescription** de um usuário:

 ```Set-ADUser myuser -Replace @{adminDescription="PHSIncluded"}``` 

## <a name="next-steps"></a>Próximas etapas
- [O que é sincronização de hash da senha?](whatis-phs.md)
- [Como funciona a sincronização de hash de senha](how-to-connect-password-hash-synchronization.md)
