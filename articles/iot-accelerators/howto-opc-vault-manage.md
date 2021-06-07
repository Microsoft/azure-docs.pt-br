---
title: Como gerenciar o serviço de certificado do cofre OPC – Azure | Microsoft Docs
description: Gerencie os certificados de autoridade de certificação raiz do cofre OPC e as permissões de usuário.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 0829d4b3fca068ddb0db2df53dd635ab7ad80bed
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645993"
---
# <a name="manage-the-opc-vault-certificate-service"></a>Gerenciar o serviço de certificado do cofre do OPC

> [!IMPORTANT]
> Enquanto atualizamos este artigo, confira [IoT Industrial do Azure](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

Este artigo explica as tarefas administrativas para o serviço de gerenciamento de certificados do compartimento OPC no Azure. Ele inclui informações sobre como renovar certificados de autoridade de certificação do emissor, como renovar a CRL (lista de certificados revogados) e como conceder e revogar o acesso do usuário.

## <a name="create-or-renew-the-root-ca-certificate"></a>Criar ou renovar o certificado de autoridade de certificação raiz

Depois de implantar o cofre OPC, você deve criar o certificado de autoridade de certificação raiz. Sem um certificado de autoridade de certificação do emissor válido, você não pode assinar ou emitir certificados de aplicativo. Consulte [certificados](howto-opc-vault-secure-ca.md#certificates) para gerenciar seus certificados com tempos de vida seguros e razoáveis. Renove um certificado de autoridade de certificação do emissor após metade de seu tempo de vida. Ao renovar, considere também que o tempo de vida configurado de um certificado de aplicativo recém assinado não deve exceder o tempo de vida do certificado de autoridade de certificação do emissor.
> [!IMPORTANT]
> A função Administrador é necessária para criar ou renovar o certificado de autoridade de certificação do emissor.

1. Abra seu serviço de certificado em `https://myResourceGroup-app.azurewebsites.net` e entre.
2. Vá para **grupos de certificados**.
3. Há um grupo de certificados padrão listado. Selecione **Editar**.
4. Em **Editar detalhes do grupo de certificados**, você pode modificar o nome da entidade e o tempo de vida da autoridade de certificação e dos certificados de aplicativo. O assunto e os tempos de vida devem ser definidos apenas uma vez antes que o primeiro certificado de autoridade de certificação seja emitido. As alterações de tempo de vida durante as operações podem resultar em tempos de vida inconsistentes de certificados e CRLs emitidos.
5. Insira um assunto válido (por exemplo, `CN=My CA Root, O=MyCompany, OU=MyDepartment` ).<br>
   > [!IMPORTANT]
   > Se você alterar o assunto, deverá renovar o certificado do emissor ou o serviço falhará ao assinar certificados de aplicativo. O assunto da configuração é verificado em relação ao assunto do certificado do emissor ativo. Se os assuntos não corresponderem, a assinatura de certificado será recusada.
6. Selecione **Salvar**.
7. Se você encontrar um erro "proibido" neste ponto, suas credenciais de usuário não terão a permissão de administrador para modificar ou criar um novo certificado raiz. Por padrão, o usuário que implantou o serviço tem funções de administrador e assinatura com o serviço. Outros usuários precisam ser adicionados às funções aprovador, gravador ou administrador, conforme apropriado no registro do aplicativo Azure Active Directory (AD do Azure).
8. Selecione **Detalhes**. Isso deve mostrar as informações atualizadas.
9. Selecione **renovar certificado de autoridade de certificação** para emitir o primeiro certificado de autoridade de certificação do emissor ou para renovar o certificado do emissor. Depois, selecione **OK**.
10. Depois de alguns segundos, você verá os **detalhes do certificado**. Para baixar o certificado de autoridade de certificação e a CRL mais recentes para distribuição para seus aplicativos OPC UA, selecione **emissor** ou **CRL**.

Agora o serviço de gerenciamento de certificados do OPC UA está pronto para emitir certificados para aplicativos OPC UA.

## <a name="renew-the-crl"></a>Renovar a CRL

A renovação da CRL é uma atualização, que deve ser distribuída para os aplicativos em intervalos regulares. Os dispositivos OPC UA, que dão suporte à extensão X509 do ponto de distribuição da CRL, podem atualizar diretamente a CRL do ponto de extremidade de microatendimento. Outros dispositivos OPC UA podem exigir atualizações manuais ou podem ser atualizados usando o GDS Server Push Extensions (*) para atualizar as listas de confiança com os certificados e as CRLs.

No fluxo de trabalho a seguir, todas as solicitações de certificado nos Estados excluídos são revogadas nas CRLs, que correspondem ao certificado de autoridade de certificação do emissor para o qual foram emitidas. O número de versão da CRL é incrementado em 1. <br>
> [!NOTE]
> Todas as CRLs emitidas são válidas até a expiração do certificado de autoridade de certificação do emissor. Isso ocorre porque a especificação OPC UA não requer um modelo de distribuição determinístico obrigatório para CRL.

> [!IMPORTANT]
> A função de administrador é necessária para renovar a CRL do emissor.

1. Abra seu serviço de certificado em `https://myResourceGroup.azurewebsites.net` e entre.
2. Vá para a página **grupos de certificados** .
3. Selecione **Detalhes**. Isso deve mostrar as informações atuais de certificado e CRL.
4. Selecione **Atualizar CRL (lista de revogação de CRL)** para emitir uma CRL atualizada para todos os certificados de emissor ativos no armazenamento do cofre OPC.
5. Depois de alguns segundos, você verá os **detalhes do certificado**. Para baixar o certificado de autoridade de certificação e a CRL mais recentes para distribuição para seus aplicativos OPC UA, selecione **emissor** ou **CRL**.

## <a name="manage-user-roles"></a>Gerenciar funções de usuário

Você gerencia funções de usuário para o microserviço do cofre OPC no aplicativo do Azure AD Enterprise. Para obter uma descrição detalhada das definições de função, consulte [funções](howto-opc-vault-secure-ca.md#roles).

Por padrão, um usuário autenticado no locatário pode entrar no serviço como um leitor. Funções com privilégios mais altos exigem gerenciamento manual no portal do Azure ou usando o PowerShell.

### <a name="add-user"></a>Adicionar usuário

1. Abra o portal do Azure.
2. Vá para **Azure Active Directory**  >  **aplicativos empresariais**.
3. Escolha o registro do microserviço do cofre OPC (por padrão, seu `resourceGroupName-service` ).
4. Vá para **usuários e grupos**.
5. Selecione **Adicionar usuário**.
6. Selecione ou convide o usuário para atribuição a uma função específica.
7. Selecione a função para os usuários.
8. Selecione **Atribuir**.
9. Para usuários na função Administrador ou aprovador, continue a adicionar políticas de acesso de Azure Key Vault.

### <a name="remove-user"></a>Remover usuário

1. Abra o portal do Azure.
2. Vá para **Azure Active Directory**  >  **aplicativos empresariais**.
3. Escolha o registro do microserviço do cofre OPC (por padrão, seu `resourceGroupName-service` ).
4. Vá para **usuários e grupos**.
5. Selecione um usuário com uma função a ser removida e, em seguida, selecione **remover**.
6. Para usuários removidos na função Administrador ou aprovador, também remova-os das políticas de Azure Key Vault.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Adicionar política de acesso de usuário a Azure Key Vault

Políticas de acesso adicionais são necessárias para Aprovadores e administradores.

Por padrão, a identidade do serviço tem apenas permissões limitadas para acessar Key Vault, para impedir que operações elevadas ou alterações ocorram sem a representação do usuário. As permissões básicas de serviço são obter e listar, para os segredos e certificados. Para segredos, há apenas uma exceção: o serviço pode excluir uma chave privada do repositório secreto depois de ser aceita por um usuário. Todas as outras operações exigem permissões representadas pelo usuário.

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>Para uma função de aprovador, as permissões a seguir devem ser adicionadas a Key Vault

1. Abra o portal do Azure.
2. Vá para o cofre do OPC `resourceGroupName` , usado durante a implantação.
3. Vá para a Key Vault `resourceGroupName-xxxxx` .
4. Acesse **políticas de acesso**.
5. Selecione **Adicionar nova**.
6. Ignore o modelo. Não há modelo que corresponda aos requisitos.
7. Escolha **selecionar entidade de segurança** e selecione o usuário a ser adicionado ou convide um novo usuário para o locatário.
8. Selecione as seguintes **permissões de chave**: **obter**, **listar** e **assinar**.
9. Selecione as seguintes **permissões de segredo**: **obter**, **listar**, **definir** e **excluir**.
10. Selecione as seguintes **permissões de certificado**: **obter** e **listar**.
11. Selecione **OK** e selecione **salvar**.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>Para uma função de administrador, as permissões a seguir devem ser adicionadas a Key Vault

1. Abra o portal do Azure.
2. Vá para o cofre do OPC `resourceGroupName` , usado durante a implantação.
3. Vá para a Key Vault `resourceGroupName-xxxxx` .
4. Acesse **políticas de acesso**.
5. Selecione **Adicionar nova**.
6. Ignore o modelo. Não há modelo que corresponda aos requisitos.
7. Escolha **selecionar entidade de segurança** e selecione o usuário a ser adicionado ou convide um novo usuário para o locatário.
8. Selecione as seguintes **permissões de chave**: **obter**, **listar** e **assinar**.
9. Selecione as seguintes **permissões de segredo**: **obter**, **listar**, **definir** e **excluir**.
10. Selecione as seguintes **permissões de certificado**: **obter**, **listar**, **Atualizar**, **criar** e **importar**.
11. Selecione **OK** e selecione **salvar**.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Remover política de acesso do usuário da Azure Key Vault

1. Abra o portal do Azure.
2. Vá para o cofre do OPC `resourceGroupName` , usado durante a implantação.
3. Vá para a Key Vault `resourceGroupName-xxxxx` .
4. Acesse **políticas de acesso**.
5. Localize o usuário a ser removido e selecione **excluir**.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a gerenciar os certificados e usuários do cofre do OPC, você pode:

> [!div class="nextstepaction"]
> [Comunicação segura de dispositivos OPC](howto-opc-vault-secure.md)
