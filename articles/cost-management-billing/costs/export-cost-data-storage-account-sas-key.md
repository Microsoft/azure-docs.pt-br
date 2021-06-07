---
title: Exportar dados de custo com uma chave de SAS da conta de armazenamento do Azure
description: Este artigo ajuda os parceiros a criar uma chave de SAS e configurar as exportações de Gerenciamento de Custos.
author: bandersmsft
ms.author: banders
ms.date: 03/08/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 16302a6bcc3bf41432500d2fdaa4ec27c28dd5b3
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509606"
---
# <a name="export-cost-data-with-an-azure-storage-account-sas-key"></a>Exportar dados de custo com uma chave de SAS da conta de armazenamento do Azure

As informações a seguir se aplicam apenas aos parceiros da Microsoft.

Geralmente, os parceiros não têm suas próprias assinaturas do Azure no locatário associado ao seu Contrato de Parceiro da Microsoft. Parceiros com um plano de Contrato de Parceiro da Microsoft que são administradores globais da conta de cobrança podem exportar e copiar dados de custo para uma conta de armazenamento em um locatário diferente usando uma chave de SAS (serviço de acesso compartilhado). Em outras palavras, uma conta de armazenamento com uma chave de SAS permite que o parceiro use uma conta de armazenamento fora do seu contrato de parceiro para receber informações exportadas. Este artigo ajuda os parceiros a criar uma chave de SAS e configurar as exportações de Gerenciamento de Custos.

## <a name="requirements"></a>Requisitos

- Você deve ser um parceiro com um Contrato de Parceiro da Microsoft e ter clientes no plano do Azure.
- Você deve ser administrador global da conta de cobrança de sua organização parceira.
- Você deve ter acesso para configurar uma conta de armazenamento que esteja em um locatário diferente da sua organização parceira. Você será responsável por manter permissões e acesso a dados ao exportar dados para sua conta de armazenamento.

## <a name="configure-azure-storage-with-a-sas-key"></a>Configurar o Armazenamento do Azure com uma chave de SAS

Obtenha um token SAS da conta de armazenamento ou crie um usando o portal do Azure. Para criar no portal do Azure, use as etapas a seguir. Para saber mais sobre chaves de SAS, confira [Conceder acesso limitado a dados com SAS (assinaturas de acesso compartilhado).](../../storage/common/storage-sas-overview.md)

1. Navegue até a conta de armazenamento no portal do Azure.
    - Se a sua conta tiver acesso a vários locatários, alterne os diretórios para acessar a conta de armazenamento. Selecione sua conta no canto superior direito do portal do Azure e escolha **Alternar diretórios**.
    - Talvez seja necessário entrar no portal do Azure com a conta de locatário correspondente para acessar a conta de armazenamento.
1. No menu à esquerda, selecione **Assinatura de acesso compartilhado**.  
    :::image type="content" source="./media/export-cost-data-storage-account-sas-key/storage-shared-access-signature.png" alt-text="Captura de tela mostrando uma assinatura de acesso compartilhado do armazenamento do Azure configurada." lightbox="./media/export-cost-data-storage-account-sas-key/storage-shared-access-signature.png" :::
1. Defina o token com as mesmas configurações, conforme identificado na imagem anterior.
    1. Selecione **Blob** para _Serviços permitidos_.
    1. Selecione **Serviço**, **Contêiner** e **Objeto** para _Tipos de recursos permitidos_.
    1. Selecione **Leitura**, **Gravação**, **Excluir**, **Listar**, **Adicionar** e **Criar** para _Permissões concedidas_.
    1. Escolha a validade e as datas. Atualize seu token SAS de exportação antes que ele expire. Quanto maior o período que você configurar antes da expiração, mais longa será a execução de sua exportação antes de precisar de um novo token SAS.
1. Selecione **Somente HTTPS** para _Protocolos permitidos_.
1. Selecione **Básico** para _Camada de roteamento preferencial_.
1. Selecione **key1** para _Chave de assinatura_. Se você rotear ou atualizar a chave usada para assinar o token SAS, precisará gerar um novo token SAS para a exportação.
1. Selecione **Gerar SAS e cadeia de conexão**.
    O valor **Token SAS** mostrado é o token de que você precisa para configurar as exportações.

## <a name="create-a-new-export-with-a-sas-token"></a>Criar uma exportação com um token SAS

Navegue até **Exportações** no escopo da conta de cobrança e crie uma exportação usando as etapas a seguir.

1. Selecione **Criar**.
1. Configure os detalhes da exportação como você faria para uma exportação normal. Você pode configurar a exportação para usar um diretório ou contêiner existente ou pode especificar um novo diretório ou contêiner, e as exportações os criarão para você.
1. Ao configurar o armazenamento, selecione **Usar um token SAS**.  
    :::image type="content" source="./media/export-cost-data-storage-account-sas-key/new-export.png" alt-text="Captura de tela mostrando a nova exportação na qual você seleciona o token SAS." lightbox="./media/export-cost-data-storage-account-sas-key/new-export.png" :::
1. Insira o nome da conta de armazenamento e cole o token SAS.
1. Especifique um contêiner ou diretório existente ou identifique novos a serem criados.
1. Selecione **Criar**.

A exportação baseada em token SAS só funciona enquanto o token permanece válido. Redefina o token antes que o atual expire, senão a exportação deixará de funcionar. Como o token fornece acesso à sua conta de armazenamento, proteja-o com a mesma atenção que você teria com outras informações confidenciais. Você será responsável por manter permissões e acesso a dados ao exportar dados para sua conta de armazenamento.

## <a name="troubleshoot-exports-using-sas-tokens"></a>Solucionar problemas de exportações usando tokens SAS

Veja a seguir os problemas comuns que podem ocorrer quando você configura ou usa exportações baseadas em token SAS.

- Você não vê a opção de chave SAS no portal do Azure.
  - Verifique se você é um parceiro que tem um Contrato de Parceiro da Microsoft e se tem permissão de administrador global para a conta de cobrança. Eles são as únicas pessoas que podem exportar com uma chave SAS.

- Você recebe a seguinte mensagem de erro ao tentar configurar sua exportação:

    **Garanta que o token SAS seja válido para o serviço do blob, seja válido para os tipos de recurso de objeto e contêiner e tenha as permissões: adicionar, criar, ler, gravar e excluir. (Código de erro do serviço de armazenamento: AuthorizationResourceTypeMismatch)**

    - Verifique se você está configurando e gerando a chave SAS corretamente no Armazenamento do Azure.

- Você não poderá ver a chave de SAS completa depois de criar uma exportação.
  - Não ver a chave é o comportamento esperado. Depois que a exportação de SAS for configurada, a chave ficará oculta por motivos de segurança.

- Você não pode acessar a conta de armazenamento do locatário onde a exportação está configurada.
  - É o comportamento esperado. Se a conta de armazenamento estiver em outro locatário, você primeiro precisará navegar até esse locatário no portal do Azure para localizar a conta.

- A exportação falha devido a um erro relacionado ao token SAS.
  - A exportação funciona somente enquanto o token SAS permanece válido. Crie uma chave e execute a exportação.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre como exportar dados de Gerenciamento de Custos, confira [Criar e exportar dados](tutorial-export-acm-data.md).
- Para obter informações sobre como exportar grandes quantidades de dados de uso, confira [Recuperar grandes conjuntos de dados com exportações](ingest-azure-usage-at-scale.md).
