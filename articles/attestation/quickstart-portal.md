---
title: 'Início rápido: Configurar o Atestado do Azure usando o portal do Azure'
description: Neste guia de início rápido, você aprenderá a configurar um provedor de atestado usando o portal do Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: d1310e3c4b4a56a27219cce613e8f6109d32c8c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101729378"
---
# <a name="quickstart-set-up-azure-attestation-by-using-the-azure-portal"></a>Início rápido: Configurar o Atestado do Azure usando o portal do Azure

Siga este guia de início rápido para começar a usar o Atestado do Azure. Saiba como gerenciar um provedor de atestado, um signatário de política e uma política usando o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="attestation-provider"></a>Provedor de atestado

Nesta seção, você vai criar um provedor de atestado e configurá-lo com políticas não assinadas ou assinadas. Você também aprenderá a exibir e excluir o provedor de atestado.

### <a name="create-and-configure-the-provider-with-unsigned-policies"></a>Criar e configurar o provedor com políticas não assinadas

1. Acesse o menu do portal do Azure ou a home page e selecione **Criar um recurso**.
1. Na caixa de pesquisa, insira **atestado**.
1. Na lista de resultados, selecione **Atestado do Microsoft Azure**.
1. Na página **Atestado do Microsoft Azure**, escolha **Criar**.
1. Na página **Criar provedor de atestado**, forneça as seguintes entradas:

   - **Assinatura**: Escolha uma assinatura.
   - **Grupo de Recursos**: Selecione um grupo de recursos existente ou escolha **Criar** e insira um nome para o grupo de recursos.
   - **Nome**: Insira um nome exclusivo.
   - **Localização**: Escolha um local.
   - **Arquivo de certificado de signatário de política**: Não carregue o arquivo de certificados de signatário de política para configurar o provedor com políticas não assinadas.

1. Depois de fornecer as entradas necessárias, selecione **Examinar + Criar**.
1. Se houver problemas de validação, corrija-os e selecione **Criar**.

### <a name="create-and-configure-the-provider-with-signed-policies"></a>Criar e configurar o provedor com políticas assinadas

1. Acesse o menu do portal do Azure ou a home page e selecione **Criar um recurso**.
1. Na caixa de pesquisa, insira **atestado**.
1. Na lista de resultados, selecione **Atestado do Microsoft Azure**.
1. Na página **Atestado do Microsoft Azure**, escolha **Criar**.
1. Na página **Criar provedor de atestado**, forneça as seguintes informações:

   - **Assinatura**: Escolha uma assinatura.
   - **Grupo de Recursos**: Selecione um grupo de recursos existente ou escolha **Criar** e insira um nome para o grupo de recursos.
   - **Nome**: Insira um nome exclusivo.
   - **Localização**: Escolha um local.
   - **Arquivo de certificado de signatário de política**: Carregue o arquivo de certificados de signatário de política para configurar o provedor de atestado com políticas assinadas. [Confira exemplos de certificados de signatário de política](./policy-signer-examples.md).

1. Depois de fornecer as entradas necessárias, selecione **Examinar + Criar**.
1. Se houver problemas de validação, corrija-os e selecione **Criar**.

### <a name="view-the-attestation-provider"></a>Exibir o provedor de atestado

1. Acesse o menu do portal do Azure ou a home page e selecione **Todos os recursos**.
1. Na caixa de filtro, insira o nome do provedor de atestado e selecione-o.

### <a name="delete-the-attestation-provider"></a>Excluir o provedor de atestado

Há duas maneiras de excluir o provedor de atestado. Você pode:

1. Acesse o menu do portal do Azure ou a home page e selecione **Todos os recursos**.
1. Na caixa de filtro, insira o nome do provedor de atestado.
1. Marque a caixa de seleção e selecione **Excluir**.
1. Insira **Sim** e selecione **Excluir**.

Ou você pode:

1. Acesse o menu do portal do Azure ou a home page e selecione **Todos os recursos**.
1. Na caixa de filtro, insira o nome do provedor de atestado.
1. Selecione o provedor de atestado e acesse a página de visão geral.
1. Escolha **Excluir** na barra de menus e selecione **Sim**.

## <a name="attestation-policy-signers"></a>Signatários de política de atestado

Siga as etapas desta seção para ver, adicionar e excluir certificados de signatário de política.

### <a name="view-the-policy-signer-certificates"></a>Exibir os certificados de signatário de política

1. Acesse o menu do portal do Azure ou a home page e selecione **Todos os recursos**.
1. Na caixa de filtro, insira o nome do provedor de atestado.
1. Selecione o provedor de atestado e acesse a página de visão geral.
1. Selecione **Certificados de signatário de política** no menu de recursos no lado esquerdo da janela ou no painel inferior. Você verá um prompt para selecionar o certificado para autenticação. Escolha a opção apropriada para continuar.
1. Escolha **Baixar certificados de signatário de política**. O botão será desabilitado para provedores de atestado criados sem o requisito de assinatura de política.
1. O arquivo de texto baixado terá todos os certificados em um formato JWS.
1. Verifique a contagem de certificados e os certificados baixados.

### <a name="add-the-policy-signer-certificate"></a>Adicionar o certificado de signatário de política

1.  Acesse o menu do portal do Azure ou a home page e selecione **Todos os recursos**.
1.  Na caixa de filtro, insira o nome do provedor de atestado.
1.  Selecione o provedor de atestado e acesse a página de visão geral.
1.  Selecione **Certificados de signatário de política** no menu de recursos no lado esquerdo da janela ou no painel inferior.
1.  Selecione **Adicionar** no menu superior. O botão será desabilitado para provedores de atestado criados sem o requisito de assinatura de política.
1.  Carregue o arquivo de certificado de signatário de política e selecione **Adicionar**. [Confira exemplos de certificados de signatário de política](./policy-signer-examples.md).

### <a name="delete-the-policy-signer-certificates"></a>Excluir os certificados de signatário de política

1.  Acesse o menu do portal do Azure ou a home page e selecione **Todos os recursos**.
1.  Na caixa de filtro, insira o nome do provedor de atestado.
1.  Selecione o provedor de atestado e acesse a página de visão geral.
1.  Selecione **Certificados de signatário de política** no menu de recursos no lado esquerdo da janela ou no painel inferior.
1.  Selecione **Excluir** no menu superior. O botão será desabilitado para provedores de atestado criados sem o requisito de assinatura de política.
1.  Carregue o arquivo de certificado de signatário de política e selecione **Excluir**. [Confira exemplos de certificados de signatário de política](./policy-signer-examples.md). 

## <a name="attestation-policy"></a>Política de atestado

Esta seção descreve como exibir uma política de atestado e como configurar políticas que foram criadas com e sem um requisito de assinatura de política.

### <a name="view-an-attestation-policy"></a>Exibir uma política de atestado

1.  Acesse o menu do portal do Azure ou a home page e selecione **Todos os recursos**.
1.  Na caixa de filtro, insira o nome do provedor de atestado.
1.  Selecione o provedor de atestado e acesse a página de visão geral.
1.  Selecione **Política** no menu de recursos no lado esquerdo da janela ou no painel inferior. Você verá um prompt para selecionar o certificado para autenticação. Escolha a opção apropriada para continuar.
1.  Selecione o **Tipo de Atestado** preferencial e veja a **Política atual**.

### <a name="configure-an-attestation-policy"></a>Configurar uma política de atestado

Siga estas etapas para carregar uma política em JWT ou no formato de texto se o provedor de atestado tiver sido criado sem um requisito de assinatura de política.

1. Acesse o menu do portal do Azure ou a home page e selecione **Todos os recursos**.
1. Na caixa de filtro, insira o nome do provedor de atestado.
1. Selecione o provedor de atestado e acesse a página de visão geral.
1. Selecione **Política** no menu de recursos no lado esquerdo da janela ou no painel inferior.
1. Selecione **Configurar** no menu superior.
1. Escolha **Formato de política** como **JWT** ou **Texto**.

   Se o provedor de atestado foi criado sem um requisito de assinatura de política, o usuário pode carregar uma política no formato **JWT** ou de **Texto**.

      - Se você escolheu o formato JWT, carregue o arquivo de política com o conteúdo da política no formato **JWT não assinado/assinado** e selecione **Salvar**. [Confira exemplos de políticas](./policy-examples.md).
      - Se você escolheu o formato de texto, carregue o arquivo de política com o conteúdo no formato de **Texto** ou insira o conteúdo da política na área de texto e selecione **Salvar**. [Confira exemplos de políticas](./policy-examples.md).

   Para a opção de upload de arquivo, a visualização da política é mostrada no formato de texto e não é editável.

1. Selecione **Atualizar** no menu superior para ver a política configurada.


Se o provedor de atestado tiver sido criado com um requisito de assinatura de política, siga estas etapas para carregar uma política no formato JWT.

1.  Acesse o menu do portal do Azure ou a home page e selecione **Todos os recursos**.
1.  Na caixa de filtro, insira o nome do provedor de atestado.
1.  Selecione o provedor de atestado e acesse a página de visão geral.
1.  Selecione **Política** no menu de recursos no lado esquerdo da janela ou no painel inferior.
1.  Selecione **Configurar** no menu superior.
1.  Carregue o arquivo de política no **formato JWT assinado** e selecione **Salvar**. [Confira exemplos de políticas](./policy-examples.md).

    Se o provedor de atestado foi criado sem um requisito de assinatura de política, o usuário pode carregar uma política somente no **formato JWT assinado**.

    Para a opção de upload de arquivo, a visualização da política é mostrada no formato de texto e não é editável.
    
1.  Selecione **Atualizar** para ver a política configurada.

## <a name="next-steps"></a>Próximas etapas

- [Como criar e assinar uma política de atestado](author-sign-policy.md)
- [Atestar um enclave do SGX usando exemplos de código](/samples/browse/?expanded=azure&terms=attestation)

