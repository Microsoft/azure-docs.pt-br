---
title: Tipos de unidades de teste, Marketplace comercial da Microsoft
description: Tipos de unidades de teste no Marketplace comercial
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/19/2020
ms.author: trkeya
author: trkeya
ms.openlocfilehash: fd3fa04d2d7e868476838788dd9cf0e27c07aeca
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461755"
---
# <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager test drive

Use este tipo se você tiver uma oferta no Azure Marketplace ou AppSource, mas quiser criar um test drive apenas com recursos do Azure. Um modelo de Azure Resource Manager (ARM) é um contêiner codificado de recursos do Azure que você cria para representar melhor sua solução. O test drive usa o modelo ARM fornecido e implanta todos os recursos necessários para um grupo de recursos. Essa é a única opção test drive para a máquina virtual ou ofertas de aplicativo do Azure.

Se você não estiver familiarizado com o que é um modelo ARM, leia [o que é Azure Resource Manager?](../azure-resource-manager/management/overview.md) e [entenda a estrutura e a sintaxe dos modelos ARM](../azure-resource-manager/templates/template-syntax.md) para entender melhor como criar e testar seus próprios modelos.

Para obter informações sobre um test drive de **aplicativo** **hospedado** ou lógico, consulte [o que é um test drive?](what-is-test-drive.md)

## <a name="technical-configuration"></a>Configurações técnicas

Um modelo de implantação contém todos os recursos do Azure que compõem sua solução. Os produtos adequados a esse cenário usam somente os recursos do Azure. Defina as seguintes propriedades no Partner Center:

- **Regiões** (obrigatório) – atualmente, há 26 regiões compatíveis com o Azure onde seu test drive pode ser disponibilizado. Normalmente, você desejará disponibilizar seu test drive nas regiões em que você prevê o maior número de clientes, para que eles possam selecionar a região mais próxima a fim de obter o melhor desempenho. Você precisará certificar-se de que sua assinatura tem permissão para implantar todos os recursos necessários em cada uma das regiões que você está selecionando.

- **Instâncias** – selecione o tipo (quente ou frio) e o número de instâncias disponíveis, que serão multiplicadas pelo número de regiões em que sua oferta está disponível.

  - **Quente** – esse tipo de instância está implantada e aguardando acesso pela região selecionada. Os clientes podem acessar esses instâncias *quentes* de um test drive instantaneamente em vez de ter que esperar por uma implantação. A desvantagem é que tais instâncias estão sempre em execução na sua assinatura do Azure; portanto, incorrerão em um maior custo de tempo de atividade. Recomendamos ter pelo menos uma instância *quente*, pois a maioria dos seus clientes não quer esperar pelo término de implantações completas, resultando em uma diminuição no uso pelos clientes caso nenhuma instância *quente* esteja disponível.

  - **Fria** – esse tipo de instância representa o número total de instâncias que é possível implantar por região. As instâncias *frias* exigem que o modelo do Resource Manager para test drive inteiro passe por uma implantação no momento em que um cliente solicita o test drive; portanto, carregam muito mais lentamente que as instâncias *quentes*. A compensação é que você só precisa pagar pela duração do test drive, ela *não* está sempre em execução em sua assinatura do Azure, como ocorre com uma instância *quente*.

- **Modelo do Azure Resource Manager para test drive** – carregue o .zip que contém o modelo do Azure Resource Manager. Saiba mais sobre como criar um modelo de Azure Resource Manager no artigo de início rápido [Criar e implantar modelos de Azure Resource Manager usando o portal do Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

    > [!note]
    > Para publicar com êxito, é importante validar a formatação do modelo ARM. Duas maneiras de fazer isso são (1) usando uma [ferramenta de API online](/rest/api/resources/deployments/validate) ou (2) com uma [implantação de teste](../azure-resource-manager/templates/deploy-portal.md).

- **Duração do test drive** (obrigatório): insira o número de horas pelo qual o test drive permanecerá ativo. O test drive é encerrado automaticamente após o término desse período de tempo. Use apenas números inteiros (por exemplo, "2" horas é válido, "1,5" não é).

## <a name="write-the-test-drive-template"></a>Gravar o modelo de test drive

Depois de criar o pacote de recursos desejado, grave e crie o modelo de test drive ARM. Como test drive executa implantações em um modo totalmente automatizado, os modelos de test drive têm algumas restrições:

### <a name="parameters"></a>Parâmetros

A maioria dos modelos tem um conjunto de parâmetros que definem nomes de recursos, tamanhos de recursos (como tipos de contas de armazenamento ou tamanhos de máquina virtual), nomes de usuário e senhas, nomes DNS e assim por diante. Quando você implanta soluções usando o portal do Azure, pode preencher manualmente todos esses parâmetros, escolher nomes de DNS disponíveis ou nomes de conta de armazenamento e assim por diante.

![Lista de parâmetros em um Azure Resource Manager](media/test-drive/resource-manager-parameters.png)

No entanto, test drive funciona automaticamente, sem interação humana, para que ele só dê suporte a um conjunto limitado de categorias de parâmetro. Se um parâmetro no modelo ARM test drive não se enquadrar em uma das categorias com suporte, você deverá substituir esse parâmetro por uma variável ou um valor constante.

Você pode usar qualquer nome válido para seus parâmetros; test drive reconhece a categoria de parâmetro usando um valor de tipo de metadados. Especifique o tipo de metadados para cada parâmetro de modelo, caso contrário, seu modelo não passará na validação:

```JSON
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username"
    }
  },
  ...
}
```

> [!NOTE]
> Todos os parâmetros são opcionais, portanto, se você não quiser usar nenhum, não precisará.

### <a name="accepted-parameter-metadata-types"></a>Tipos de metadados de parâmetros aceitos

| Tipo de metadados   | Tipo de parâmetro  | Descrição     | Valor de exemplo    |
|---|---|---|---|
| **baseuri**     | string          | URI base do seu pacote de implantação| `https:\//\<\..\>.blob.core.windows.net/\<\..\>` |
| **username**    | string          | Novo nome de usuário aleatório.| admin68876      |
| **password**    | cadeia de caracteres segura    | Nova senha aleatória | Lp!ACS\^2kh     |
| **ID da sessão**   | string          | ID de sessão de test drive exclusiva (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="baseuri"></a>baseuri

Test Drive inicializa esse parâmetro com um **URI base** do seu pacote de implantação para que você possa usar esse parâmetro para construir um URI de qualquer arquivo incluído em seu pacote.

```JSON
"parameters": {
  ...
  "baseuri": {
    "type": "string",
    "metadata": {
      "type": "baseuri",
      "description": "Base Uri of the deployment package."
    }
  },
  ...
}
```

Use esse parâmetro dentro de seu modelo para construir um URI de qualquer arquivo do seu pacote de implantação test drive. O exemplo a seguir mostra como construir um URI do modelo vinculado:

```JSON
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>Nome de Usuário

Test Drive inicializa esse parâmetro com um novo nome de usuário aleatório:

```JSON
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username",
      "description": "Solution admin name."
    }
  },
  ...
}
```

Valor de exemplo: `admin68876`

É possível usar nomes de usuário aleatórios ou constantes para sua solução.

#### <a name="password"></a>password

Test Drive inicializa esse parâmetro com uma nova senha aleatória:

```JSON
"parameters": {
  ...
  "password": {
    "type": "securestring",
    "metadata": {
      "type": "password",
      "description": "Solution admin password."
    }
  },
  ...
}
```

Valor de exemplo:  `Lp!ACS^2kh`

É possível usar senhas aleatórias ou constantes para sua solução.

#### <a name="session-id"></a>ID da sessão

Test Drive inicializa esse parâmetro com um GUID exclusivo que representa a ID da sessão do Test Drive:

```JSON
"parameters": {
  ...
  "sessionid": {
    "type": "string",
    "metadata": {
      "type": "sessionid",
      "description": "Unique test drive session id."
    }
  },
  ...
}
```

Valor de exemplo: `b8c8693e-5673-449c-badd-257a405a6dee`

Você pode usar esse parâmetro para identificar exclusivamente a sessão de test drive, se for necessário.

### <a name="unique-names"></a>Nomes exclusivos

Alguns recursos do Azure, como contas de armazenamento ou nomes DNS, requerem nomes globalmente exclusivos. Isso significa que sempre que test drive implantar o modelo ARM, ele criará um novo grupo de recursos com um nome exclusivo para todos os seus recursos. Portanto, você deve usar a função [uniquestring](../azure-resource-manager/templates/template-functions.md) concatenada com seus nomes de variáveis em IDs de grupo de recursos para gerar valores exclusivos aleatórios:

```JSON
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

Certifique-se de concatenar suas cadeias de caracteres de parâmetro/variável ( `contosovm` ) com uma saída de cadeia exclusiva ( `resourceGroup().id` ), pois isso garante a exclusividade e a confiabilidade de cada variável.

Por exemplo, a maioria dos nomes de recursos não pode começar com um dígito, mas a função de cadeia de caracteres exclusiva pode devolver uma cadeia de caracteres que começa com um dígito. Portanto, se usar uma saída de cadeia de caracteres exclusiva bruta, suas implantações falharão.

Você pode encontrar informações adicionais sobre regras e restrições de nomenclatura de recursos em [este artigo](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="deployment-location"></a>Local da implantação

Você pode torná-lo test drive disponível em diferentes regiões do Azure. A ideia é permitir que um usuário escolha a região mais próxima para proporcionar a melhor experiência do usuário.

Quando test drive cria uma instância do laboratório, ela sempre cria um grupo de recursos na região escolhida por um usuário e, em seguida, executa o modelo de implantação nesse contexto de grupo. Portanto, o modelo deve escolher o local da implantação no grupo de recursos:

```JSON
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

E, em seguida, usar esse local para todos os recursos para uma instância de laboratório específica:

```JSON
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/publicIPAddresses",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/virtualNetworks",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/networkInterfaces",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "location": "[variables('location')]",
    ...
  }
]
```

Verifique se sua assinatura tem permissão para implantar todos os recursos desejados em cada uma das regiões que você selecionar. Verifique também se suas imagens de máquina virtual estão disponíveis em todas as regiões que você habilitará, caso contrário, seu modelo de implantação não funcionará para algumas regiões.

### <a name="outputs"></a>Saídas

Normalmente, com modelos do Resource Manager, você pode implantar sem produzir nenhuma saída. Isso acontece porque você conhece todos os valores que usa para preencher parâmetros do modelo e pode sempre inspecionar manualmente as propriedades de qualquer recurso.

Para test drive modelos do Resource Manager, no entanto, é importante retornar a test drive todas as informações, o que é necessário para obter acesso ao laboratório (URIs de site, nomes de host de máquina virtual, nomes de usuário e senhas). Verifique se todos os nomes de saída são legíveis porque essas variáveis são apresentadas ao cliente.

Não existem restrições relacionadas às saídas do modelo. O Test Drive converte todos os valores de saída em cadeias de caracteres, portanto, se você enviar um objeto para a saída, um usuário verá uma cadeia de caracteres JSON.

Exemplo:

```JSON
"outputs": {
  "Host Name": {
    "type": "string",
    "value": "[reference(variables('pubIpId')).dnsSettings.fqdn]"
  },
  "User Name": {
    "type": "string",
    "value": "[parameters('adminName')]"
  },
  "Password": {
    "type": "string",
    "value": "[parameters('adminPassword')]"
  }
}
```

### <a name="subscription-limits"></a>Limites de assinatura

Não se esqueça da assinatura e dos limites de serviço. Por exemplo, se você quiser implantar até 10 4 máquinas virtuais de núcleo, precisará garantir que a assinatura usada para seu laboratório permite que você use núcleos de 40. Para obter mais informações sobre os limites de serviço e assinatura do Azure, consulte [assinatura do Azure e limites de serviço, cotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md). Como várias unidades de teste podem ser obtidas ao mesmo tempo, verifique se sua assinatura pode lidar com o número de núcleos multiplicado pelo número total de unidades de teste simultâneas que podem ser executadas.

### <a name="what-to-upload"></a>O que deve ser transferido por upload

O modelo ARM test drive é carregado como um arquivo zip, que pode incluir vários artefatos de implantação, mas deve ter um arquivo chamado `main-template.json` . Esse é o modelo de implantação do ARM que test drive usa para criar uma instância de um laboratório. Se você tiver recursos adicionais além desse arquivo, poderá referenciá-los como recursos externos dentro do modelo ou incluí-los no arquivo zip.

Durante a certificação de publicação, test drive descompacta seu pacote de implantação e coloca seu conteúdo em um contêiner de BLOBs test drive interno. A estrutura do contêiner reflete a estrutura do seu pacote de implantação:

| package.zip                       | Testar contêiner de BLOB         |
|---|---|
| `main-template.json`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/main-template.json`  |
| `templates/solution.json`           | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/templates/solution.json` |
| `scripts/warmup.ps1`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/scripts/warmup.ps1`  |
|||

Chamamos um URI desse contêiner de blob de URI base. Como cada revisão do seu laboratório tem seu próprio contêiner de BLOBs, cada revisão do seu laboratório tem seu próprio URI base. O Test Drive pode passar um URI de base do seu pacote de implantação descompactado para seu modelo por meio de parâmetros de modelo.

### <a name="transform-template-examples-for-test-drive"></a>Exemplos de modelo de transformação para test drive

O processo de transformar uma arquitetura de recursos em um modelo test drive Resource Manager pode ser assustador. Para obter ajuda adicional, consulte estes exemplos de como transformar melhor os modelos de implantação atuais em [o que é um test drive?](what-is-test-drive.md#transforming-examples).

## <a name="test-drive-deployment-subscription-details"></a>Detalhes da assinatura de implantação do Test Drive

A seção final a ser concluída é poder implantar as unidades de teste automaticamente conectando sua assinatura do Azure e Azure Active Directory (AD).

![Detalhes da assinatura de implantação do Test Drive](media/test-drive/deployment-subscription-details.png)

1. Obtenha uma **ID de assinatura do Azure**. Concede acesso aos serviços do Azure e ao portal do Azure. A assinatura é onde o uso de recursos é relatado e os serviços são cobrados. Se você ainda não tiver uma assinatura separada do Azure para apenas unidades de teste, crie uma. Você pode encontrar IDs de assinatura do Azure (como `1a83645ac-1234-5ab6-6789-1h234g764ghty1` ) entrando em portal do Azure e selecionando **assinaturas** no menu do nav esquerdo.

   ![Assinaturas do Azure](media/test-drive/azure-subscriptions.png)

2. Obtenha uma **ID de locatário do Azure ad**. Se você já tiver uma ID de locatário disponível, poderá encontrá-la em **Azure Active Directory**  >  **Properties**  >  **ID do diretório** de propriedades:

   ![Propriedades do Azure Active Directory](media/test-drive/azure-active-directory-properties.png)

   Se você não tiver uma ID de locatário, crie uma nova no Azure Active Directory. Para obter ajuda com a configuração de um locatário, consulte [início rápido: configurar um locatário](../active-directory/develop/quickstart-create-new-tenant.md).

3. **ID de aplicativo Azure ad** – criar e registrar um novo aplicativo. Usaremos esse aplicativo para executar operações em sua instância de test drive.

   1. Navegue até o diretório recém-criado ou o diretório já existente e selecione Azure Active Directory no painel de filtro.
   2. Pesquise **registros de aplicativo** e selecione **Adicionar**.
   3. Forneça o nome do aplicativo.
   4. Selecione o **tipo** de **aplicativo Web/API**.
   5. Forneça qualquer valor na URL de logon, esse campo não será usado.
   6. Selecione **Criar**.
   7. Depois que o aplicativo tiver sido criado, selecione **Propriedades**  >  **definir o aplicativo como multilocatário** e, em seguida, **salvar**.

4. Clique em **Salvar**.

5. Copie a ID do aplicativo para este aplicativo registrado e cole-a no campo test drive.

   ![Detalhes da ID do aplicativo do Azure AD](media/test-drive/azure-ad-application-id-detail.png)

6. Como estamos usando o aplicativo para implantar na assinatura, precisamos adicionar o aplicativo como um colaborador na assinatura:

   1. Selecione o tipo de **assinatura** que você está usando para o Test Drive.
   1. Selecione **IAM (Controle de acesso)** .
   1. Selecione a guia **atribuições de função** e, em seguida, **adicione atribuição de função**.

      ![Adicionar uma nova entidade de segurança para Controle de Acesso](media/test-drive/access-control-principal.jpg)

   1. Defina a **função** e **atribua acesso a** , conforme mostrado. No campo **selecionar** , insira o nome do aplicativo do Azure AD. Selecione o aplicativo ao qual você deseja atribuir a função de **colaborador** .

      ![Adicionar as permissões](media/test-drive/access-control-permissions.jpg)

   1. Clique em **Salvar**.

7. Gere uma chave de autenticação de **aplicativo Azure ad** . Em **chaves**, adicione uma **Descrição da chave**, defina a duração para **nunca expirar** (uma chave expirada interromperá o Test Drive em produção) e selecione **salvar**. Copie e cole esse valor em seu campo de test drive obrigatório.

![Mostra as chaves para o aplicativo Azure AD](media/test-drive/azure-ad-app-keys.png)

## <a name="republish"></a>Republicar

Agora que todos os campos de test drive estão concluídos, **Republique** sua oferta. Depois que seu test drive tiver passado para a certificação, teste a experiência do cliente na versão prévia da sua oferta:

1. Inicie um test drive na interface do usuário.
1. Abra sua assinatura do Azure dentro do portal do Azure.
1. Verifique se o test drive está sendo implantado corretamente.

   ![Portal do Azure](media/test-drive/azure-portal.png)

Não exclua nenhuma instância de test drive provisionada para seus clientes; o serviço de test drive limpará automaticamente esses grupos de recursos depois que um cliente for concluído com ele.

Quando estiver familiarizado com sua oferta de visualização, é hora de **entrar em funcionamento**! Há um processo final de revisão para verificar a experiência completa de ponta a ponta. Se rejeitarmos a oferta, enviaremos um email para o contato de engenharia para sua oferta, explicando o que precisa ser corrigido.

## <a name="next-steps"></a>Próximas etapas

- Se você estava seguindo as instruções para criar sua oferta no Partner Center, use a seta voltar para retornar a esse tópico.
- Saiba mais sobre outros tipos de unidades de teste em [o que é um test drive?](what-is-test-drive.md).