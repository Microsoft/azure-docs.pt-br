---
title: 'Início Rápido: Criar um grupo de gerenciamento com o JavaScript'
description: Neste guia de início rápido, você usará o JavaScript para criar um grupo de gerenciamento para organizar seus recursos em uma hierarquia de recursos.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: 553133f30bd16543444356e8cda3e70458247b5c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536012"
---
# <a name="quickstart-create-a-management-group-with-javascript"></a>Início Rápido: Criar um grupo de gerenciamento com o JavaScript

Grupos de gerenciamento são contêineres que o ajudarão a gerenciar o acesso, a política e a conformidade entre várias assinaturas. Criar esses contêineres para criar uma hierarquia eficaz e eficiente que pode ser usada com o [Azure Policy](../policy/overview.md) e os [Controles de Acesso Baseados em Função do Azure](../../role-based-access-control/overview.md). Para obter mais informações sobre grupos de gerenciamento, consulte [Organizar seus recursos com grupos de gerenciamento do Azure](overview.md).

O primeiro grupo de gerenciamento criado no diretório pode levar até 15 minutos para ser concluído. Há processos que são executados pela primeira vez para configurar o serviço de grupos de gerenciamento no Azure para seu diretório. Você recebe uma notificação quando o processo é concluído. Para obter mais informações, confira [Configuração inicial dos grupos de gerenciamento](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- Antes de começar, verifique se, no mínimo, a versão 12 do [Node.js](https://nodejs.org/) está instalada.

- Qualquer usuário do Azure AD no locatário poderá criar um grupo de gerenciamento sem ter recebido a permissão de gravação do grupo de gerenciamento se a [proteção de hierarquia](./how-to/protect-resource-hierarchy.md#setting---require-authorization) não estiver habilitada. Esse novo grupo de gerenciamento passa a ser um filho do grupo de gerenciamento raiz ou o [grupo de gerenciamento padrão](./how-to/protect-resource-hierarchy.md#setting---default-management-group), e o criador recebe uma atribuição de função "Proprietário". O serviço de grupo de gerenciamento permite essa capacidade para que as atribuições de função não sejam necessárias no nível raiz. Nenhum usuário tem acesso ao grupo de gerenciamento raiz quando ele é criado. Para evitar problemas de localização dos administradores globais do Azure AD para começar a usar os grupos de gerenciamento, permitimos a criação dos grupos de gerenciamento iniciais no nível raiz.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>Instalação do aplicativo

Para habilitar o JavaScript a fim de gerenciar grupos de gerenciamento, o ambiente precisa ser configurado. Essa configuração funciona sempre que o JavaScript pode ser usado, incluindo o [Bash no Windows 10](/windows/wsl/install-win10).

1. Configure um novo projeto Node.js executando o comando a seguir.

   ```bash
   npm init -y
   ```

1. Adicione uma referência ao módulo yargs.

   ```bash
   npm install yargs
   ```

1. Adicione uma referência ao módulo do Azure Resource Graph.

   ```bash
   npm install @azure/arm-managementgroups
   ```

1. Adicione uma referência à biblioteca de autenticação do Azure.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Em _package.json_, confirme se a versão de `@azure/arm-managementgroups` é a **1.1.0** ou superior, e se a versão de `@azure/ms-rest-nodeauth` é a **3.0.5** ou superior.

## <a name="create-the-management-group"></a>Criar o grupo de gerenciamento

1. Crie um arquivo chamado _index.js_ e insira o código a seguir.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const managementGroups = require("@azure/arm-managementgroups");

   if (argv.groupID && argv.displayName) {
       const createMG = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new managementGroups.ManagementGroupsAPI(credentials);
          const result = await client.managementGroups.createOrUpdate(
             groupId: argv.groupID,
             {
                 displayName: argv.displayName
             }
          );
          console.log(result);
       };

       createMG();
   }
   ```

1. Insira o seguinte comando no terminal:

   ```bash
   node index.js --groupID "<NEW_MG_GROUP_ID>" --displayName "<NEW_MG_FRIENDLY_NAME>"
   ```

   Substitua o espaço reservado `<>` de cada token pela _ID do grupo de gerenciamento_ e pelo _nome amigável do grupo de gerenciamento_, respectivamente.

   À medida que o script tenta se autenticar, uma mensagem semelhante à seguinte é exibida no terminal:

   > Para se conectar, use um navegador da Web para abrir a página https://microsoft.com/devicelogin e insira o código FGB56WJUGK para se autenticar.

   Depois que você se autenticar no navegador, o script continuará sendo executado.

O resultado da criação do grupo de gerenciamento é a saída para o console.

## <a name="clean-up-resources"></a>Limpar os recursos

Caso deseje remover as bibliotecas instaladas do aplicativo, execute o comando a seguir.

```bash
npm uninstall @azure/arm-managementgroups @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um grupo de gerenciamento para organizar sua hierarquia de recursos. O grupo de gerenciamento pode conter assinaturas ou outros grupos de gerenciamento.

Para saber mais sobre grupos de gerenciamento e como gerenciar sua hierarquia de recursos, prossiga para:

> [!div class="nextstepaction"]
> [Gerenciar seus recursos com grupos de gerenciamento](./manage.md)
