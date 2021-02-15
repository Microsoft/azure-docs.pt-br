---
title: Exportar a configuração de provisionamento e reverter para um estado válido conhecido para recuperação de desastre
description: Saiba como exportar sua configuração de provisionamento e reverter para um estado válido conhecido para recuperação de desastre.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 03/19/2020
ms.author: kenwith
ms.openlocfilehash: c6af42c78bda66c4b397cbb99b26af7d6a5c7f07
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256366"
---
# <a name="how-to-export-provisioning-configuration-and-roll-back-to-a-known-good-state"></a>Como: exportar a configuração de provisionamento e reverter para um estado válido conhecido

Neste artigo, você aprenderá a:

- Exportar e importar sua configuração de provisionamento do portal do Azure
- Exportar e importar sua configuração de provisionamento usando a API de Microsoft Graph

## <a name="export-and-import-your-provisioning-configuration-from-the-azure-portal"></a>Exportar e importar sua configuração de provisionamento do portal do Azure

### <a name="export-your-provisioning-configuration"></a>Exportar sua configuração de provisionamento

Para exportar sua configuração:

1. No [portal do Azure](https://portal.azure.com/), no painel de navegação esquerdo, selecione **Azure Active Directory**.
1. No painel de **Azure Active Directory** , selecione **aplicativos empresariais** e escolha seu aplicativo.
1. No painel de navegação esquerdo, selecione **provisionamento**. Na página configuração de provisionamento, clique em **mapeamentos de atributo**, em seguida, **Mostrar opções avançadas** e, por fim, **examine o esquema**. Isso o levará para o editor de esquema.
1. Clique em baixar na barra de comandos na parte superior da página para baixar o esquema.

### <a name="disaster-recovery---roll-back-to-a-known-good-state"></a>Recuperação de desastre-reverta para um estado válido conhecido

Exportar e salvar sua configuração permite que você reverta para uma versão anterior da sua configuração. É recomendável exportar sua configuração de provisionamento e salvá-la para uso posterior sempre que você fizer uma alteração nos mapeamentos de atributo ou nos filtros de escopo. Tudo o que você precisa fazer é abrir o arquivo JSON que você baixou nas etapas acima, copiar todo o conteúdo do arquivo JSON, substituir todo o conteúdo da carga JSON no editor de esquema e, em seguida, salvar. Se houver um ciclo de provisionamento ativo, ele será concluído e o próximo ciclo usará o esquema atualizado. O próximo ciclo também será um ciclo inicial, que reavalia todos os usuários e grupos com base na nova configuração. Considere o seguinte ao reverter para uma configuração anterior:

- Os usuários serão avaliados novamente para determinar se devem estar no escopo. Se os filtros de escopo tiverem alterado um usuário não estiver no escopo, mais eles serão desabilitados. Embora esse seja o comportamento desejado na maioria dos casos, há ocasiões em que você pode querer evitar isso e pode usar a funcionalidade [ignorar exclusões de exclusão de escopo](./skip-out-of-scope-deletions.md) . 
- A alteração da configuração de provisionamento reinicia o serviço e dispara um [ciclo inicial](./how-provisioning-works.md#provisioning-cycles-initial-and-incremental).

## <a name="export-and-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>Exportar e importar sua configuração de provisionamento usando a API de Microsoft Graph

Você pode usar a API de Microsoft Graph e o Microsoft Graph Explorer para exportar mapeamentos e esquemas de atributos de provisionamento de usuário para um arquivo JSON e importá-los de volta ao Azure AD. Você também pode usar as etapas capturadas aqui para criar um backup de sua configuração de provisionamento.

### <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Etapa 1: recuperar a ID da entidade de serviço do aplicativo de provisionamento (ID do objeto)

1. Inicie o [portal do Azure](https://portal.azure.com)e navegue até a seção Propriedades do seu aplicativo de provisionamento. Por exemplo, se você quiser exportar seu *WORKDAY para* o mapeamento de aplicativo de provisionamento de usuário do AD, navegue até a seção de propriedades desse aplicativo.
1. Na seção Propriedades do seu aplicativo de provisionamento, copie o valor GUID associado ao campo *ID de Objeto*. Esse valor também é chamado de **servicePrincipalName** do seu aplicativo e será usado em operações do Microsoft Graph Explorer.

   ![ID da Entidade de Serviço de Aplicativo do Workday](./media/export-import-provisioning-configuration/wd_export_01.png)

### <a name="step-2-sign-into-microsoft-graph-explorer"></a>Etapa 2: entrar no Microsoft Graph Explorer

1. Abrir o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Clique no botão “Entrar com a Microsoft” e entre usando as credenciais de administrador do aplicativo ou de administrador global do Azure AD.

    ![Microsoft Graph entrar](./media/export-import-provisioning-configuration/wd_export_02.png)

1. Após entrar com êxito, você verá os detalhes da conta de usuário no painel esquerdo.

### <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>Etapa 3: recuperar a ID do trabalho de provisionamento do aplicativo de provisionamento

No Microsoft Graph Explorer, execute a seguinte consulta GET, substituindo [servicePrincipalId] pelo **ServicePrincipalId** extraído da [Etapa 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Você obterá uma resposta, conforme mostrado abaixo. Copie o “atributo de id” presente na resposta. Esse valor é o **ProvisioningJobId** e será usado para recuperar os metadados de esquema subjacentes.

   [![ID do trabalho de provisionamento](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

### <a name="step-4-download-the-provisioning-schema"></a>Etapa 4: baixar o esquema de provisionamento

No Microsoft Graph Explorer, execute a seguinte consulta GET, substituindo [servicePrincipalId] e [ProvisioningJobId] pelo ServicePrincipalId e o ProvisioningJobId recuperados nas etapas anteriores.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Copie o objeto JSON da resposta e salve-o em um arquivo para criar um backup do esquema.

### <a name="step-5-import-the-provisioning-schema"></a>Etapa 5: importar o esquema de provisionamento

> [!CAUTION]
> Execute esta etapa somente se você precisar modificar o esquema de configuração que não pode ser alterado usando o portal do Azure ou se precisar restaurar a configuração de um arquivo de backup anterior com um esquema válido e funcional.

No Microsoft Graph Explorer, configure a seguinte consulta PUT, substituindo [servicePrincipalId] e [ProvisioningJobId] pelo ServicePrincipalId e o ProvisioningJobId recuperados nas etapas anteriores.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Na guia “Corpo da solicitação”, copie o conteúdo do arquivo de esquema JSON.

   [![Corpo da solicitação](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

Na guia “Cabeçalhos de solicitação”, adicione o atributo de cabeçalho de Content-Type com o valor “application/json”

   [![Cabeçalhos de solicitação](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

Selecione **Executar consulta** para importar o novo esquema.