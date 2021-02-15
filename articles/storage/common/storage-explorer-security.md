---
title: Guia de segurança do Gerenciador de Armazenamento do Azure | Microsoft Docs
description: Diretrizes de segurança para Gerenciador de Armazenamento do Azure
services: storage
author: cralvord
ms.service: storage
ms.topic: best-practice
ms.date: 07/30/2020
ms.author: cralvord
ms.openlocfilehash: 283ec9999f9b4362035b6770383984efb0879d49
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783752"
---
# <a name="azure-storage-explorer-security-guide"></a>Guia de segurança do Gerenciador de Armazenamento do Azure

O Gerenciador de Armazenamento do Microsoft Azure permite que você trabalhe facilmente com os dados do armazenamento do Azure com segurança e segurança no Windows, no macOS e no Linux. Seguindo essas diretrizes, você pode garantir que seus dados permaneçam protegidos.

## <a name="general"></a>Geral

- **Sempre use a versão mais recente do Gerenciador de Armazenamento.** As versões Gerenciador de Armazenamento podem conter atualizações de segurança. Manter-se atualizado ajuda a garantir a segurança geral.
- **Conecte-se somente a recursos confiáveis.** Os dados baixados de fontes não confiáveis podem ser mal-intencionados e o carregamento de dados em uma fonte não confiável pode resultar em dados perdidos ou roubados.
- **Use HTTPS sempre que possível.** O Gerenciador de Armazenamento usa HTTPS por padrão. Alguns cenários permitem que você use HTTP, mas HTTP deve ser usado somente como último recurso.
- **Certifique-se de que apenas as permissões necessárias sejam dadas às pessoas que precisam delas.** Evite ser excessivamente permissivos ao conceder a qualquer pessoa acesso aos seus recursos.
- **Tome cuidado ao executar operações críticas.** Determinadas operações, como excluir e substituir, são irreversíveis e podem causar perda de dados. Verifique se você está trabalhando com os recursos corretos antes de executar essas operações.

## <a name="choosing-the-right-authentication-method"></a>Escolhendo o método de autenticação correto

Gerenciador de Armazenamento fornece várias maneiras de acessar seus recursos de armazenamento do Azure. Seja qual for o método que você escolher, aqui estão nossas recomendações.

### <a name="azure-ad-authentication"></a>Autenticação do Azure AD

A maneira mais fácil e segura de acessar seus recursos de armazenamento do Azure é entrar com sua conta do Azure. Entrar usa a autenticação do Azure AD, que permite:

- Conceder acesso a usuários e grupos específicos.
- Revogar o acesso a usuários e grupos específicos a qualquer momento.
- Impor condições de acesso, como exigir a autenticação multifator.

É recomendável usar a autenticação do Azure AD sempre que possível.

Esta seção descreve as duas tecnologias baseadas no Azure AD que podem ser usadas para proteger seus recursos de armazenamento.

#### <a name="azure-role-based-access-control-azure-rbac"></a>RBAC do Azure (controle de acesso baseado em função do Azure)

O Azure [RBAC (controle de acesso baseado em função) do](../../role-based-access-control/overview.md) Azure fornece controle de acesso refinado sobre os recursos do Azure. As funções e permissões do Azure podem ser gerenciadas no portal do Azure.

O Gerenciador de Armazenamento dá suporte ao acesso RBAC do Azure a contas de armazenamento, BLOBs e filas. Se precisar de acesso a compartilhamentos de arquivos ou tabelas, você precisará atribuir funções do Azure que concedem permissão para listar chaves de conta de armazenamento.

#### <a name="access-control-lists-acls"></a>ACLs (listas de controle de acesso)

As [ACLs (listas de controle de acesso)](../blobs/data-lake-storage-access-control.md) permitem controlar o acesso ao nível de arquivo e pasta em ADLS Gen2 contêineres de BLOB. Você pode gerenciar suas ACLs usando Gerenciador de Armazenamento.

### <a name="shared-access-signatures-sas"></a>SAS (assinaturas de acesso compartilhado)

Se você não puder usar a autenticação do Azure AD, recomendamos o uso de assinaturas de acesso compartilhado. Com as assinaturas de acesso compartilhado, você pode:

- Forneça acesso limitado anônimo a recursos seguros.
- Revogar uma SAS imediatamente se gerada de uma política de acesso compartilhado (SAP).

No entanto, com as assinaturas de acesso compartilhado, você não pode:

- Restrinja quem pode usar uma SAS. Uma SAS válida pode ser usada por qualquer pessoa que a tenha.
- Revogar uma SAS se não for gerada a partir de uma política de acesso compartilhado (SAP).

Ao usar SAS no Gerenciador de Armazenamento, recomendamos as seguintes diretrizes:

- **Limite a distribuição de tokens SAS e URIs.** Distribua apenas tokens SAS e URIs para indivíduos confiáveis. Limitar a distribuição de SAS reduz a chance de que uma SAS possa ser usada.
- **Use apenas tokens SAS e URIs de entidades confiáveis.**
- **Use políticas de acesso compartilhado (SAP) ao gerar tokens SAS e URIs, se possível.** Uma SAS baseada em uma política de acesso compartilhado é mais segura do que uma SAS simples, pois a SAS pode ser revogada excluindo o SAP.
- **Gere tokens com acesso mínimo a recursos e permissões.** As permissões mínimas limitam o dano potencial que poderia ser feito se uma SAS for usada inparada.
- **Gere tokens que são válidos somente pelo tempo necessário.** Uma breve vida útil é especialmente importante para SAS simples, porque não há como revogá-las depois de geradas.

> [!IMPORTANT]
> Ao compartilhar tokens SAS e URIs para fins de solução de problemas, como em solicitações de serviço ou relatórios de bugs, o sempre redigirá pelo menos a parte de assinatura da SAS.

### <a name="storage-account-keys"></a>Chaves de conta de armazenamento

As chaves de conta de armazenamento concedem acesso irrestrito aos serviços e recursos em uma conta de armazenamento. Por esse motivo, é recomendável limitar o uso de chaves para acessar recursos no Gerenciador de Armazenamento. Use os recursos do RBAC do Azure ou SAS para fornecer acesso.

Algumas funções do Azure concedem permissão para recuperar chaves de conta de armazenamento. Indivíduos com essas funções podem efetivamente evitar permissões concedidas ou negadas pelo RBAC do Azure. Recomendamos não conceder essa permissão a menos que seja necessário.

Gerenciador de Armazenamento tentará usar chaves de conta de armazenamento, se disponível, para autenticar solicitações. Você pode desabilitar esse recurso em configurações ( **serviços > contas de armazenamento > desabilitar o uso de chaves** ). Alguns recursos não dão suporte ao RBAC do Azure, como trabalhar com contas de armazenamento clássicas. Esses recursos ainda exigem chaves e não são afetados por essa configuração.

Se você precisar usar chaves para acessar seus recursos de armazenamento, recomendamos as seguintes diretrizes:

- **Não compartilhe suas chaves de conta com ninguém.**
- **Trate suas chaves de conta de armazenamento como senhas.** Se você precisar tornar suas chaves acessíveis, use soluções de armazenamento seguro, como [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

> [!NOTE]
> Se você acreditar que uma chave de conta de armazenamento foi compartilhada ou distribuída por engano, poderá gerar novas chaves para sua conta de armazenamento a partir da portal do Azure.

### <a name="public-access-to-blob-containers"></a>Acesso público a contêineres de BLOB

Gerenciador de Armazenamento permite que você modifique o nível de acesso de seus contêineres de armazenamento de BLOBs do Azure. Os contêineres de BLOB não privados permitem que qualquer usuário anônimo acesso de leitura aos dados nesses contêineres.

Ao habilitar o acesso público para um contêiner de BLOB, recomendamos as seguintes diretrizes:

- **Não habilite o acesso público a um contêiner de BLOB que possa conter dados potencialmente confidenciais.** Verifique se o seu contêiner de BLOBs está livre de todos os dados privados.
- **Não carregue nenhum dado potencialmente confidencial para um contêiner de blob com acesso ao contêiner ou ao blob.** 

## <a name="next-steps"></a>Próximas etapas

- [Recomendações de segurança](../blobs/security-recommendations.md)