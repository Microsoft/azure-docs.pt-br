---
title: Criar um. Arquivo PFX, com o certificado LDAPS (LDAP) seguro para um domínio do Azure AD Domain Services
description: Criar um certificado LDAP seguro para um Azure AD Domain Services gerenciar o domínio
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: iainfou
ms.openlocfilehash: d89034610eb22108efc832d32ed9d8f593e9f12a
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67474390"
---
# <a name="create-a-pfx-file-with-the-secure-ldap-ldaps-certificate-for-a-managed-domain"></a>Criar um. Arquivo PFX, com o certificado LDAPS (LDAP) seguro para um domínio gerenciado

## <a name="before-you-begin"></a>Antes de começar

Concluir [Tarefa 1: obter um certificado para LDAP seguro](configure-ldaps.md).

## <a name="task-2-export-the-secure-ldap-certificate-to-a-pfx-file"></a>Tarefa 2: Exportar o certificado LDAP seguro para um arquivo .PFX

Antes de iniciar esta tarefa,obtenha o certificado LDAP seguro de sua autoridade de certificação pública ou se criou um certificado autoassinado.

Para exportar o certificado LDAPS para um. arquivo .PFX:

1. Pressione o botão **Iniciar** e digite **R**. Na caixa de diálogo **Executar**, digite **mmc** e clique em **OK**.

    ![Iniciar o console do MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)
2. No prompt **Controle de Conta de Usuário**, clique em **SIM** para iniciar o MMC (Console de Gerenciamento Microsoft) como administrador.
3. No menu **Arquivo**, clique em **Adicionar/Remover Snap-in...** .

    ![Adicionar snap-in ao console do MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)
4. Na caixa de diálogo **Adicionar ou Remover Snap-ins**, escolha o snap-in **Certificados** e clique no botão **Adicionar >** .

    ![Adicionar snap-in de certificados ao console do MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)
5. No assistente do **Snap-in de certificados**, escolha **Conta de computador** e clique em **Avançar**.

    ![Adicionar snap-in de certificados à conta do computador](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)
6. Na página **Selecionar Computador**, escolha **Computador local: (o computador no qual este console está sendo executado)** e clique em **Concluir**.

    ![Adicionar snap-in de certificados - escolher computador](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)
7. Na caixa de diálogo **Adicionar ou Remover Snap-ins**, clique em **OK** para adicionar o snap-in de certificados ao MMC.

    ![Adicionar snap-in de certificados ao MMC - concluído](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)
8. Na janela do MMC, clique para expandir **Raiz do Console**. Você deve ver o snap-in de Certificados carregado. Clique em **Certificados (Computador Local)** para expandir. Clique para expandir o nó **Pessoal**, seguido pelo nó **Certificados**.

    ![Abrir repositório de certificados pessoais](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)
9. Você deve ver o certificado autoassinado que acabamos de criar. É possível examinar as propriedades do certificado para verificar que a impressão digital corresponda àquela relatada nas janelas do PowerShell quando você criou o certificado.
10. Escolha o certificado autoassinado e **clique com o botão direito do mouse**. No menu de clique com o botão direito do mouse, escolha **Todas as Tarefas** e **Exportar...** .

    ![Exportar o certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)
11. No **Assistente para Exportação de Certificados**, clique em **Avançar**.

    ![Assistente de Exportar certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)
12. Na página **Exportar Chave Privada**, escolha **Sim, exporte a chave privada** e clique em **Avançar**.

    ![Chave privada de Exportar certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [!WARNING]
    > Você DEVE exportar a chave privada junto com o certificado. Se você fornecer um PFX que não contém a chave privada do certificado, a habilitação do LDAP seguro para seu domínio gerenciado falhará.
    >
    >

13. Na página **Exportar Formato de Arquivo**, escolha **Personal Information Exchange – PKCS #12 (.PFX)** como o formato de arquivo para o certificado exportado.

    ![Formato de arquivo de Exportar certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

    > [!NOTE]
    > Somente o formato de arquivo .PFX tem suporte. Não exporte o certificado para o formato de arquivo .CER.
    >
    >

14. Na página **Segurança**, escolha a opção **Senha** e digite uma senha para proteger o arquivo .PFX. Guarde essa senha, pois ela será necessária na próxima tarefa. Clique em **Avançar**.

    ![Senha para exportação de certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

    > [!NOTE]
    > Anote essa senha. Você precisará dela ao habilitar o LDAP seguro para este domínio gerenciado na [Tarefa 3 – habilitar o LDAP seguro para o domínio gerenciado](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
    >
    >

15. Na página **Arquivo a ser Exportado** , especifique o nome do arquivo e o local para onde você deseja exportar o certificado.

    ![Caminho para exportação de certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)
16. Na página seguinte, clique em **Concluir** para exportar o certificado para um arquivo PFX. Você verá o diálogo de confirmação quando o certificado tiver sido exportado.

    ![Exportar certificado concluído](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)

## <a name="next-step"></a>Próxima etapa

[Tarefa 3 – habilitar o LDAP seguro para o domínio gerenciado](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
