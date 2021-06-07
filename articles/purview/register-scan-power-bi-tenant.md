---
title: Registrar e verificar um locatário Power BI (versão prévia)
description: Saiba como usar o portal de alcance do Azure para registrar e verificar um locatário Power BI.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: 2ecc5df9db51bb6c923b9e0f47163e492bd76cfa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101695730"
---
# <a name="register-and-scan-a-power-bi-tenant-preview"></a>Registrar e verificar um locatário Power BI (versão prévia)

Este artigo mostra como usar o portal alcance do Azure para registrar e verificar um locatário Power BI.

> [!Note]
> Se a instância alcance e o locatário Power BI estiverem no mesmo locatário do Azure, você só poderá usar a autenticação de identidade gerenciada (MSI) para configurar uma verificação de um locatário Power BI. 

## <a name="create-a-security-group-for-permissions"></a>Criar um grupo de segurança para permissões

Para configurar a autenticação, crie um grupo de segurança e adicione a identidade gerenciada alcance a ele.

1. Na [portal do Azure](https://portal.azure.com), procure **Azure Active Directory**.
1. Crie um novo grupo de segurança em seu Azure Active Directory, seguindo [criar um grupo básico e adicionar membros usando Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    > [!Tip]
    > Você pode ignorar esta etapa se já tiver um grupo de segurança que deseja usar.

1. Selecione **segurança** como o **tipo de grupo**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Tipo de grupo de segurança":::

1. Adicione sua identidade gerenciada do alcance a esse grupo de segurança. Selecione **Membros** e, em seguida, selecione **+ adicionar membros**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Adicione a instância gerenciada do catálogo ao grupo.":::

1. Pesquise sua identidade gerenciada do alcance e selecione-a.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Adicionar Catálogo pesquisando-o":::

    Você deverá ver uma notificação de êxito mostrando que ela foi adicionada.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="Êxito ao adicionar catálogo MSI":::

## <a name="associate-the-security-group-with-the-tenant"></a>Associar o grupo de segurança ao locatário

1. Faça logon no [portal de administração do Power bi](https://app.powerbi.com/admin-portal/tenantSettings).
1. Selecione a página **configurações de locatário** .

    > [!Important]
    > Você precisa ser um administrador de Power BI para ver a página de configurações de locatário.

1. Selecione **admin API configurações**  >  **permitir que as entidades de serviço usem somente leitura Power bi APIs de administração (versão prévia)**.
1. Selecione **grupos de segurança específicos**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Imagem mostrando como permitir que as entidades de serviço obtenham somente leitura Power BI permissões de API de administração":::

    > [!Caution]
    > Quando você permite que o grupo de segurança criado (que tem sua identidade gerenciada do alcance como membro) Use APIs de administração Power BI somente leitura, você também permite que ele acesse os metadados (por exemplo, nomes de painel e relatório, proprietários, descrições, etc.) para todos os seus artefatos de Power BI nesse locatário. Depois que os metadados tiverem sido extraídos para as alcance do Azure, permissões do alcance, não Power BI permissões, determine quem pode ver os metadados.

    > [!Note]
    > Você pode remover o grupo de segurança das configurações do desenvolvedor, mas os metadados extraídos anteriormente não serão removidos da conta do alcance. Você pode excluí-lo separadamente, se desejar.

## <a name="register-your-power-bi-and-set-up-a-scan"></a>Registrar seu Power BI e configurar uma verificação

Agora que você recebeu as permissões de identidade gerenciada do alcance para se conectar à API de administração do seu locatário do Power BI, você pode configurar sua verificação no Azure alcance Studio.

Primeiro, adicione um sinalizador de recurso especial à URL do alcance 

1. Selecione o ícone **centro de gerenciamento** .

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/management-center.png" alt-text="Ícone do centro de gerenciamento.":::

1. Em seguida, selecione **+ novo** em **fontes de dados**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/data-sources.png" alt-text="Imagem do botão nova fonte de dados":::

    Selecione **Power bi** como sua fonte de dados.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="Imagem mostrando a lista de fontes de dados disponíveis para escolher":::

3. Dê um nome amigável à instância de Power BI.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Imagem mostrando Power BI nome amigável da fonte de dados":::

    O nome deve ter entre 3-63 e caracteres de comprimento e deve conter apenas letras, números, sublinhados e hifens.  Não são permitidos espaços.

    Por padrão, o sistema encontrará o locatário Power BI que existe na mesma assinatura do Azure.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="Power BI fonte de dados registrada":::

    > [!Note]
    > Por Power BI, o registro e a verificação da fonte de dados são permitidos para apenas uma instância.


4. Dê um nome à sua verificação. Em seguida, selecione a opção para incluir ou excluir os espaços de trabalho pessoais. Observe que o único método de autenticação com suporte é de **identidade gerenciada**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="Imagem mostrando a configuração da verificação de Power BI":::

    > [!Note]
    > * Alternar a configuração de uma verificação para incluir ou excluir um espaço de trabalho pessoal irá disparar uma verificação completa da fonte do PowerBI
    > * O nome da verificação deve ter entre 3-63 e caracteres de comprimento e deve conter apenas letras, números, sublinhados e hifens. Não são permitidos espaços.

5. Configurar um gatilho de verificação. Suas opções são **uma vez**, a **cada 7 dias** e a **cada 30 dias**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="Imagem do gatilho de verificação":::

6. Em **examinar nova verificação**, selecione **salvar e executar** para iniciar a verificação.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Salvar e executar Power BI imagem da tela":::

## <a name="next-steps"></a>Próximas etapas

- [Navegar pelo Catálogo de Dados do Azure Purview](how-to-browse-catalog.md)
- [Pesquisar no Catálogo de Dados do Azure Purview](how-to-search-catalog.md)
