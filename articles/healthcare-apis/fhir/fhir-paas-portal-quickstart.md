---
title: 'Início Rápido: Implantar a API do Azure para FHIR usando o portal do Azure'
description: Neste guia de início rápido, você aprenderá a implantar a API do Azure para FHIR e definir as configurações usando o portal do Azure.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 03/15/2020
ms.author: cavoeg
ms.openlocfilehash: cabacd5204f4a9ac5c17c7bd66924482b5bf688a
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017483"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-portal"></a>Início Rápido: Implantar a API do Azure para FHIR usando o portal do Azure

Neste guia de início rápido, você aprenderá a implantar a API do Azure para FHIR usando o portal do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-new-resource"></a>Criar novo recurso

Abra o [portal do Azure](https://portal.azure.com) e clique em **Criar um recurso**

![Criar um recurso](media/quickstart-paas-portal/portal-create-resource.png)

## <a name="search-for-azure-api-for-fhir"></a>Pesquisar a API do Azure para FHIR

Você pode encontrar a API do Azure para FHIR digitando "FHIR" na caixa de pesquisa:

:::image type="content" source="media/quickstart-paas-portal/portal-search-healthcare-apis.png" alt-text="Pesquisar APIs de Saúde":::

## <a name="create-azure-api-for-fhir-account"></a>Criar conta da API do Azure para FHIR

Selecione **Criar** para criar uma conta da API do Azure para FHIR:

:::image type="content" source="media/quickstart-paas-portal/portal-create-healthcare-apis.png" alt-text="Criar conta da API do Azure para FHIR":::

## <a name="enter-account-details"></a>Inserir detalhes da conta

Selecione um grupo de recursos ou crie um, escolha um nome para a conta e, por fim, clique em **Examinar + criar**:

:::image type="content" source="media/quickstart-paas-portal/portal-new-healthcareapi-details.png" alt-text="Novos detalhes da API de saúde":::

Confirme a criação e aguarde a implantação da API do FHIR.

## <a name="additional-settings-optional"></a>Configurações adicionais (opcional)

Você também pode clicar em **Avançar: configurações adicionais** para exibir as configurações de autenticação. A configuração padrão para a API do Azure para FHIR é [usar o RBAC do Azure para atribuir funções de plano de dados](configure-azure-rbac.md). Quando configurado nesse modo, a "Autoridade" do serviço FHIR será definida como o locatário do Azure Active Directory da assinatura:

:::image type="content" source="media/rbac/confirm-azure-rbac-mode-create.png" alt-text="Configurações de autenticação padrão":::

Observe que a caixa para inserir as IDs de objeto permitidas está esmaecida, já que usamos o RBAC do Azure para configurar atribuições de função nesse caso.

Se desejar configurar o serviço FHIR para usar um locatário de Azure Active Directory externo ou secundário, altere a autoridade e insira as IDs de objeto para usuários e grupos que devem ter acesso permitido ao servidor. Para obter mais informações, confira o guia de [configuração do RBAC local](configure-local-rbac.md).

## <a name="fetch-fhir-api-capability-statement"></a>Instrução de funcionalidade Buscar API de FHIR

Para validar que a nova conta da API do FHIR é provisionada, busque uma declaração de capacidade apontando um navegador para `https://<ACCOUNT-NAME>.azurehealthcareapis.com/metadata`.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando ele não for mais necessário, você poderá excluir o grupo de recursos, API do Azure para FHIR e todos os recursos relacionados. Para fazer isso, selecione o grupo de recursos que contém a conta da API do Azure para FHIR, selecione **Excluir grupo de recursos** e confirme o nome do grupo de recursos a ser excluído.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você implantou a API do Azure para FHIR em sua assinatura. Para definir configurações adicionais em sua API do Azure para FHIR, vá para o guia de instruções de configurações adicionais. Se você estiver pronto para começar a usar a API do Azure para FHIR, leia mais sobre como registrar aplicativos.

>[!div class="nextstepaction"]
>[Configurações adicionais na API do Azure para FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Visão geral sobre como Registrar os Aplicativos](fhir-app-registration.md)
