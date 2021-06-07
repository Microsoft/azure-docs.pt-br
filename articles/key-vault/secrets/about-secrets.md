---
title: Sobre os segredos do Azure Key Vault – Azure Key Vault
description: Visão geral dos segredos do Azure Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 6d4f3f744a85c14c42ffef1c894b237081e871f8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752418"
---
# <a name="about-azure-key-vault-secrets"></a>Sobre os segredos do Azure Key Vault

O [Key Vault](../general/overview.md) fornece armazenamento seguro de segredos genéricos, como senhas e cadeias de conexão de banco de dados.

Da perspectiva do desenvolvedor, APIs Key Vault aceitam e retornam valores do segredo como cadeias de caracteres. Internamente, o Key Vault armazena e gerencia os segredos como sequências de octetos (bytes de 8 bits), com um tamanho máximo de 25k bytes cada. O serviço Key Vault não fornece a semântica para segredos. Ele simplesmente aceita os dados, criptografa-os, armazena-os e retorna um identificador secreto ("id"). O identificador pode ser usado para recuperar o segredo em um momento posterior.  

Para dados altamente confidenciais, os clientes devem considerar camadas adicionais de proteção de dados. Criptografar dados usando uma chave de proteção separada antes do armazenamento no Key Vault é um exemplo.  

O Key Vault também oferece suporte a um campo contentType para segredos. Os clientes podem especificar o tipo de conteúdo de um segredo para ajudar a interpretar os dados de segredo quando são recuperados. O comprimento máximo deste campo é de 255 caracteres. Não existem valores predefinidos. O uso sugerido é como uma dica para interpretar os dados de secredos. Por exemplo, uma implementação pode armazenar senhas e certificados como segredos e usar esse campo para fazer a diferenciação. Não existem valores predefinidos.  

## <a name="encryption"></a>Criptografia

Todos os segredos em seu Key Vault são armazenados criptografados. Essa criptografia é transparente e não requer nenhuma ação do usuário. O serviço do Azure Key Vault criptografa seus segredos quando você os adiciona e os descriptografa automaticamente ao lê-los. A chave de criptografia é exclusiva para cada cofre de chaves.

## <a name="secret-attributes"></a>Atributos de segredos

Além dos dados de segredo, os seguintes atributos podem ser especificados:  

- *exp*: IntDate, opcional, o padrão é **infinito**. O atributo *exp* (tempo de expiração) identifica o tempo de expiração em que ou depois que os dados de segredo NÃO DEVEM ser recuperados, exceto em [determinadas situações](#date-time-controlled-operations). Esse campo é para fins **informativos** somente, visto que ele informa os usuários do serviço de cofre de que um segredo específico não pode ser usado. Seu valor DEVE ser um número que contenha um valor de IntDate.   
- *nbf*: IntDate, opcional, o padrão é **agora**. O atributo *nbf* (not before) identifica o tempo anterior que os dados de segredo NÃO DEVEM ser recuperados, exceto em [determinadas situações](#date-time-controlled-operations). Este documento serve apenas para fins **informativos**. Seu valor DEVE ser um número que contenha um valor de IntDate. 
- *habilitado*: booliano, opcional, o padrão é **true**. Esse atributo especifica se os dados do segredo podem ser recuperados. O atributo habilitado é usado em conjunto com *nbf* e *exp* quando ocorre uma operação entre *nbf* e *exp*, ele só será permitido se habilitado estiver definido como **true**. As operações fora da janela *nbf* e *exp* são automaticamente não permitidas, exceto em [determinadas situações](#date-time-controlled-operations).  

Há mais atributos somente leitura que são incluídos em qualquer resposta que inclui os atributos de segredo:  

- *criado*: IntDate, opcional. O atributo criado indica quando esta versão do segredo foi criada. Esse valor é nulo para segredos criados antes da adição deste atributo. Seu valor deve ser um número que contenha um valor de IntDate.  
- *atualizado*: IntDate, opcional. O atributo atualizado indica quando esta versão do segredo foi atualizada. Esse valor é nulo para segredos que foram atualizados antes da adição deste atributo. Seu valor deve ser um número que contenha um valor de IntDate.

Para obter informações sobre atributos comuns para cada tipo de objeto do cofre de chaves, confira a [Visão geral das chaves, dos segredos e dos certificados do Azure Key Vault](../general/about-keys-secrets-certificates.md)

### <a name="date-time-controlled-operations"></a>Operações de data e hora controladas

Uma operação **obter** do segredo funcionará para segredos ainda não válidos e expirados, fora da janela *nbf* / *exp*. Chamar uma operação **obter** do segredo, para um segredo ainda não válido, pode ser usada para fins de teste. Recuperar (**obter**) um segredo expirado, pode ser usado para operações de recuperação.

## <a name="secret-access-control"></a>Controle de acesso a segredo

O Controle de Acesso para segredos gerenciados pelo Key Vault é fornecido no nível do Key Vault que atua como o contêiner desses segredos. A política de controle de acesso para segredos é diferente da política de controle de acesso para chaves no mesmo Key Vault. Os usuários podem criar um ou mais cofres para armazenar segredos e são solicitados a manter a segmentação e gerenciamento de segredos apropriados do cenário.   

As seguintes permissões podem ser usadas, por entidade de segurança, na entrada de controle de acesso segredos em um cofre e refletem com maior exatidão as operações permitidas em um objeto de segredo:  

- Permissões para operações de gerenciamento de segredos
  - *obter*: Ler um segredo  
  - *lista*: Listar os segredos ou versões de um segredo armazenado em um Key Vault  
  - *set*: Criar um segredo  
  - *excluir*: Excluir um segredo  
  - *recuperar*: Recuperar um segredo excluído
  - *backup*: Fazer backup de um segredo em um Key Vault
  - *restaurar*: Restaura um backup do segredo em um Key Vault

- Permissões para operações com privilégio
  - *limpar*: Limpar (exclui permanentemente) um segredo excluído

Para obter mais informações sobre como trabalhar com segredos, veja [Operações de segredo na referência de API REST do Key Vault](/rest/api/keyvault). Para obter informações sobre como estabelecer permissões, confira [Cofres – criar ou atualizar](/rest/api/keyvault/vaults/createorupdate) e [Cofres – atualizar política de acesso](/rest/api/keyvault/vaults/updateaccesspolicy). 

Guias de instruções para controlar o acesso no Key Vault:
- [Atribuir uma política de acesso do Key Vault usando a CLI](../general/assign-access-policy-cli.md)
- [Atribuir uma política de acesso do Key Vault usando o PowerShell](../general/assign-access-policy-powershell.md)
- [Atribuir uma política de acesso do Key Vault usando o portal do Azure](../general/assign-access-policy-portal.md)
- [Fornecer acesso a chaves, certificados e segredos do Key Vault com um controle de acesso baseado em função do Azure](../general/rbac-guide.md)

## <a name="secret-tags"></a>Marcas de segredos  
Você pode especificar mais metadados específicos do aplicativo na forma de marcas. O Key Vault oferece suporte a até 15 marcas, cada uma delas pode ter um nome de 256 caracteres e um valor de 256 caracteres.  

>[!Note]
>As marcas são legíveis por um chamador se ele tem a permissão *Listar* ou *Obter*.

## <a name="azure-storage-account-key-management"></a>Gerenciamento da conta de armazenamento do Azure

O Key Vault pode gerenciar chaves de [conta de armazenamento do Azure](../../storage/common/storage-account-overview.md):

- Internamente, o Key Vault pode listar (sincronizar) chaves com uma conta de armazenamento do Azure. 
- O Key Vault gera novamente (gira) as chaves periodicamente.
- Os valores de chave nunca são retornados em resposta ao chamador.
- O Key Vault gerencia chaves tanto de contas de armazenamento quanto de contas de armazenamento clássicas.

Para obter mais informações, consulte:
- [Chaves de acesso da conta de armazenamento](../../storage/common/storage-account-keys-manage.md)
- [Gerenciamento de chaves de conta de armazenamento no Azure Key Vault](../secrets/overview-storage-keys.md)


## <a name="storage-account-access-control"></a>Controle de acesso da conta de armazenamento

As seguintes permissões podem ser usadas ao autorizar um usuário ou entidade de segurança de aplicativo para executar operações em uma conta de armazenamento gerenciada:  

- Permissões para operações de definição de SaS e conta de armazenamento gerenciada
  - *obter*: Obtém informações sobre uma conta de armazenamento 
  - *lista*: Lista contas de armazenamento gerenciadas por um Key Vault
  - *atualizar*: Atualizar uma conta de armazenamento
  - *excluir*: Excluir uma conta de armazenamento  
  - *recuperar*: Recuperar uma conta de armazenamento excluída
  - *backup*: Faz backup de uma conta de armazenamento
  - *restaurar*: Restaurar um backup de conta de armazenamento em um Key Vault
  - *set*: Criar ou atualizar uma conta de armazenamento
  - *regeneratekey*: Regenerar um valor de chave especificado para uma conta de armazenamento
  - *getsas*: Obtém informações sobre uma definição de SAS para uma conta de armazenamento
  - *listsas*: Listar as definições de SAS de armazenamento para uma conta de armazenamento
  - *deletesas*: Exclui uma definição de SAS de uma conta de armazenamento
  - *setsas*: Cria ou atualiza uma nova definição de SAS/atributos para uma conta de armazenamento

- Permissões para operações com privilégio
  - *limpar*: Limpa (exclui permanentemente) uma conta de armazenamento gerenciada

Para obter mais informações, confira [Operações de conta de armazenamento na referência de API REST do Key Vault](/rest/api/keyvault). Para obter informações sobre como estabelecer permissões, confira [Cofres – criar ou atualizar](/rest/api/keyvault/vaults/createorupdate) e [Cofres – atualizar política de acesso](/rest/api/keyvault/vaults/updateaccesspolicy).

Guias de instruções para controlar o acesso no Key Vault:
- [Atribuir uma política de acesso do Key Vault usando a CLI](../general/assign-access-policy-cli.md)
- [Atribuir uma política de acesso do Key Vault usando o PowerShell](../general/assign-access-policy-powershell.md)
- [Atribuir uma política de acesso do Key Vault usando o portal do Azure](../general/assign-access-policy-portal.md)
- [Fornecer acesso a chaves, certificados e segredos do Key Vault com um controle de acesso baseado em função do Azure](../general/rbac-guide.md)


## <a name="next-steps"></a>Próximas etapas

- [Sobre o Key Vault](../general/overview.md)
- [Sobre chaves, segredos e certificados](../general/about-keys-secrets-certificates.md)
- [Sobre as chaves](../keys/about-keys.md)
- [Sobre certificados](../certificates/about-certificates.md)
- [Proteger o acesso a um cofre de chaves](../general/security-overview.md)
- [Guia do Desenvolvedor do Cofre de Chaves](../general/developers-guide.md)