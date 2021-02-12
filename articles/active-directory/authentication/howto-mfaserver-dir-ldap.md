---
title: Autenticação LDAP e servidor MFA do Azure-Azure Active Directory
description: Implantação da Autenticação LDAP e Servidor de Autenticação Multifator do Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8961cccbd57294e477f0d33202fe91cd292b7814
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742214"
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>Autenticação LDAP e Servidor de Autenticação Multifator do Azure

Por padrão, o Servidor de Autenticação Multifator do Azure é configurado para importar ou sincronizar usuários do Active Directory. No entanto, ele pode ser configurado para associar a diferentes diretórios LDAP, como um diretório ADAM ou controlador de domínio específico do Active Directory. Quando conectado a um diretório via LDAP, o Servidor de Autenticação Multifator do Azure pode atuar como um proxy LDAP e realizar autenticações. Ele também permite o uso da associação LDAP como um destino RADIUS para pré-autenticação dos usuários com a Autenticação do IIS ou autenticação principal no portal de usuário do Azure MFA.

Para usar a Autenticação Multifator do Azure como um proxy LDAP, insira o Servidor de Autenticação Multifator do Azure entre o cliente LDAP (por exemplo, aplicativo, dispositivo VPN) e o servidor do diretório LDAP. O Servidor de Autenticação Multifator do Azure deve ser configurado para se comunicar com servidores do cliente e com o diretório LDAP. Nessa configuração, o Servidor de Autenticação Multifator do Azure aceita solicitações LDAP dos aplicativos e servidores clientes e os encaminha para o servidor de diretório LDAP de destino para validar as credenciais principais. Se o diretório LDAP validar as credenciais primárias, a Autenticação Multifator do Azure executará a segunda verificação de identidade e enviará uma resposta de volta ao cliente LDAP. Toda a autenticação só será bem-sucedida se a autenticação do servidor LDAP e a verificação de segunda etapa forem bem-sucedidas.

> [!IMPORTANT]
> A partir de 1º de julho de 2019, a Microsoft não oferece mais o servidor MFA para novas implantações. Novos clientes que desejam exigir a MFA (autenticação multifator) durante eventos de entrada devem usar a autenticação multifator do Azure AD baseada em nuvem.
>
> Para começar a usar a MFA baseada em nuvem, consulte [tutorial: proteger eventos de entrada de usuário com a autenticação multifator do Azure ad](tutorial-enable-azure-mfa.md).
>
> Os clientes existentes que ativaram o servidor MFA antes de 1º de julho de 2019 podem baixar a versão mais recente, atualizações futuras e gerar credenciais de ativação como de costume.

## <a name="configure-ldap-authentication"></a>Configurar a autenticação LDAP

Para configurar a autenticação LDAP, instale o Servidor de Autenticação Multifator do Azure em um servidor Windows. Use este procedimento:

### <a name="add-an-ldap-client"></a>Adicionar um cliente LDAP

1. No Servidor de Autenticação Multifator do Azure, selecione o ícone Autenticação LDAP no menu à esquerda.
2. Marque a caixa de seleção **habilitar autenticação LDAP** .

   ![Autenticação LDAP no servidor MFA](./media/howto-mfaserver-dir-ldap/ldap2.png)

3. Na guia clientes, altere a porta TCP e a porta SSL (TLS) se o serviço LDAP da autenticação multifator do Azure deve se associar a portas não padrão para escutar solicitações LDAP.
4. Se você planeja usar LDAPs do cliente para a Servidor de Autenticação Multifator do Azure, um certificado TLS/SSL deve ser instalado no mesmo servidor que o servidor MFA. Clique em **procurar** ao lado da caixa certificado SSL (TLS) e selecione um certificado a ser usado para a conexão segura.
5. Clique em **Adicionar**.
6. Na caixa de diálogo Adicionar Cliente LDAP, digite o endereço IP do dispositivo, do servidor ou do aplicativo que fará a autenticação para o Servidor e um nome de Aplicativo (opcional). O nome do aplicativo aparece nos relatórios da Autenticação Multifator do Azure e pode ser exibido nas mensagens de autenticação por SMS ou Aplicativo Móvel.
7. Marque a caixa correspondente a **Exigir correspondência de usuário de Autenticação Multifator do Microsoft Azure** se todos os usuários tiverem sido ou forem importados para o servidor e estiverem sujeitos à verificação em duas etapas. Se um número significativo de usuários ainda não tiver sido importado para o Servidor e/ou for isentado da verificação em duas etapas, deixe a caixa desmarcada. Consulte o arquivo de ajuda Servidor de MFA para obter informações adicionais sobre esse recurso.

Repita estas etapas para adicionar outros clientes LDAP.

### <a name="configure-the-ldap-directory-connection"></a>Configurar a conexão do diretório LDAP

Quando a Autenticação Multifator do Azure é configurado para receber autenticações LDAP, ele deve fazer o proxy dessas autenticações para o diretório LDAP. Portanto, o guia Destino exibe apenas uma única opção esmaecida para usar um destino LDAP.

> [!NOTE]
> Não há garantia de que a integração de diretórios funcione com diretórios diferentes de Active Directory Domain Services.

1. Para configurar a conexão do diretório LDAP, clique no ícone **integração de diretórios** .
2. Na guia Configurações, selecione o botão de opção **usar configuração LDAP específica** .
3. Selecione **Editar...**
4. Na caixa de diálogo Editar Configuração de LDAP, popule os campos com as informações exigidas para se conectar ao diretório LDAP. As descrições dos campos estão incluídas no arquivo de ajuda do Servidor de Autenticação Multifator do Azure.

    ![Configuração LDAP de integração de diretórios](./media/howto-mfaserver-dir-ldap/ldap.png)

5. Teste a conexão LDAP clicando no botão **testar** .
6. Se o teste de conexão LDAP tiver sido bem-sucedido, clique no botão **OK** .
7. Clique na guia **filtros** . O servidor está pré-configurado para carregar contêineres, grupos de segurança e usuários de Active Directory. Se estiver se associando a um diretório LDAP diferente, provavelmente será necessário editar os filtros exibidos. Clique no link **ajuda** para obter mais informações sobre filtros.
8. Clique na guia **atributos** . O servidor está pré-configurado para mapear atributos de Active Directory.
9. Se você estiver se associando a um diretório LDAP diferente ou para alterar os mapeamentos de atributos pré-configurados, clique no botão **Editar…**
10. Na caixa de diálogo Editar Atributos, modifique os mapeamentos de atributos LDAP para seu diretório. Os nomes de atributo podem ser digitados ou selecionados clicando em **...** ao lado de cada campo. Clique no link **ajuda** para obter mais informações sobre atributos.
11. Clique no botão **OK**.
12. Clique no ícone **configurações da empresa** e selecione a guia **resolução de nome de usuário** .
13. Se estiver se conectando ao Active Directory de um servidor ingressado em domínio, deixe o botão de opção **Usar os identificadores de segurança do Windows (SIDs) para nomes de usuário correspondentes** selecionado. Caso contrário, selecione o botão de opção **usar atributo de identificador exclusivo LDAP para correspondência de nomes de** User.

Quando o botão de opção **Usar atributo de identificador exclusivo LDAP para correspondência de nomes de usuário** selecionado, o Servidor de Autenticação Multifator do Azure tenta resolver cada nome de usuário para um identificador exclusivo no diretório LDAP. Uma pesquisa LDAP é executada nos atributos de nome de usuário definidos na guia integração de diretório-> atributos. Quando um usuário é autenticado, o nome de usuário é resolvido para o identificador exclusivo no diretório LDAP. O identificador exclusivo é usado para combinar ao usuário no arquivo de dados de Autenticação Multifator do Azure. Isso permite comparações que não diferenciam maiúsculas de minúsculas, bem como formatos de nome de usuário curtos e longos.

Depois de concluir essas etapas, o Servidor de MFA começará a escutar nas portas configuradas solicitações de acesso LDAP dos clientes configurados e atuará como proxy para essas solicitações ao diretório LDAP para autenticação.

## <a name="configure-ldap-client"></a>Configurar cliente LDAP

Para configurar o cliente LDAP, use as diretrizes:

* Configure seu dispositivo, servidor ou aplicativo para autenticar via LDAP para o Servidor de Autenticação Multifator do Azure como se fosse seu diretório LDAP. Use as mesmas configurações que normalmente usa para se conectar diretamente ao diretório LDAP, exceto o nome do servidor ou endereço IP que será o Servidor de Autenticação Multifator do Azure.
* Configure o tempo limite LDAP de 30-60 segundos para que haja tempo para validar as credenciais do usuário com o diretório LDAP, executar a verificação de segunda etapa, receber sua resposta e responder à solicitação de acesso LDAP.
* Se estiver usando LDAPs, o dispositivo ou o servidor que faz as consultas LDAP deve confiar no certificado TLS/SSL instalado na Servidor de Autenticação Multifator do Azure.
