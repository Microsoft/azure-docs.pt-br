---
title: Configurar o acesso Azure Active Directory-serviço Blockchain do Azure
description: Como configurar o serviço Blockchain do Azure com acesso Azure Active Directory
ms.date: 11/22/2019
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: 337d01abc51d310d06aeea3427b770132be4824c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85208766"
---
# <a name="how-to-configure-azure-active-directory-access-for-azure-blockchain-service"></a>Como configurar o acesso de Azure Active Directory para o serviço Blockchain do Azure

Neste artigo, você aprende a conceder acesso e conectar-se aos nós do serviço Blockchain do Azure usando o usuário do Azure Active Directory (Azure AD), o grupo ou as IDs de aplicativo.

O Azure AD fornece gerenciamento de identidade baseado em nuvem e permite que você use uma única identidade em toda a empresa e acesse aplicativos no Azure. O serviço Blockchain do Azure é integrado ao Azure AD e oferece benefícios como a Federação de ID, logon único e autenticação multifator.

## <a name="prerequisites"></a>Pré-requisitos

* [Criar um membro do blockchain usando o portal do Azure](create-member.md)

## <a name="grant-access"></a>Conceder acesso

Você pode conceder acesso tanto no nível de membro quanto no nível de nó. Conceder direitos de acesso no nível de membro, por sua vez, concederá acesso a todos os nós sob o membro.

### <a name="grant-member-level-access"></a>Conceder acesso ao nível de membro

Para conceder permissão de acesso no nível de membro.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Navegue até **controle de acesso (iam) > adicionar > adicionar atribuição de função**.
1. Selecione a função **acesso ao nó do membro do Blockchain (visualização)** e adicione o objeto de ID do Azure AD ao qual você deseja conceder acesso. O objeto de ID do Azure AD pode ser:

    | Objeto do Azure AD | Exemplo |
    |-----------------|---------|
    | Usuário do Azure AD   | `kim@contoso.onmicrosoft.com` |
    | Grupo do Azure AD  | `sales@contoso.onmicrosoft.com` |
    | ID do aplicativo  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Adicionar atribuição de função](./media/configure-aad/add-role-assignment.png)

1. Clique em **Salvar**.

### <a name="grant-node-level-access"></a>Conceder acesso ao nível do nó

Você pode conceder acesso no nível do nó navegando até segurança do nó e clicando no nome do nó que você deseja conceder acesso.

Selecione a função acesso ao nó do membro do Blockchain (visualização) e adicione o objeto de ID do Azure AD ao qual você deseja conceder acesso.

Para obter mais informações, consulte [Configurar nós de transação do serviço Blockchain do Azure](configure-transaction-nodes.md#azure-active-directory-access-control).

## <a name="connect-using-azure-blockchain-connector"></a>Conectar usando o conector do Blockchain do Azure

Baixe ou clone o [conector do Azure Blockchain do GitHub](https://github.com/Microsoft/azure-blockchain-connector/).

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

O seguir a seção de início rápido no **Leiame** para criar o conector do código-fonte.

### <a name="connect-using-an-azure-ad-user-account"></a>Conectar-se usando uma conta de usuário do Azure AD

1. Execute o comando a seguir para autenticar usando uma conta de usuário do Azure AD. Substituir \<myAADDirectory\> por um domínio do Azure AD. Por exemplo, `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. O Azure AD solicita credenciais.
1. Entre com seu nome de usuário e senha.
1. Após a autenticação bem-sucedida, o proxy local se conecta ao nó blockchain. Agora você pode anexar o cliente Geth com o ponto de extremidade local.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Conectar usando uma ID de aplicativo

Muitos aplicativos são autenticados com o Azure AD usando uma ID de aplicativo em vez de uma conta de usuário do Azure AD.

Para se conectar ao seu nó usando uma ID de aplicativo, substitua **aadauthcode** por **aadclient**.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Parâmetro | Descrição |
|-----------|-------------|
| ID do locatário | Domínio do Azure AD, por exemplo, `yourdomain.onmicrosoft.com`
| ID do cliente | ID do cliente do aplicativo registrado no Azure AD
| segredo do cliente | Segredo do cliente do aplicativo registrado no Azure AD

Para obter mais informações sobre como registrar um aplicativo no Azure AD, consulte [como: usar o portal para criar um aplicativo do Azure AD e uma entidade de serviço que pode acessar recursos](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>Conectar um dispositivo móvel ou um navegador de texto

Para um dispositivo móvel ou navegador baseado em texto em que a exibição pop-up de autenticação do Azure AD não é possível, o Azure AD gera uma senha de uso único. Você pode copiar a senha e prosseguir com a autenticação do Azure AD em outro ambiente.

Para gerar a senha, substitua **aadauthcode** por **aaddevice**. Substituir \<myAADDirectory\> por um domínio do Azure AD. Por exemplo, `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a segurança de dados no serviço Blockchain do Azure, consulte [segurança do serviço Blockchain do Azure](data-security.md).