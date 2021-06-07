---
title: Mover um cofre de chaves para uma região diferente – Azure Key Vault | Microsoft Docs
description: Este artigo apresenta orientações sobre como mover um cofre de chaves para uma região diferente.
services: key-vault
author: msmbaldwin
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: ac2f6347776c2f5d230065b80b1c0336e21e181c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751788"
---
# <a name="move-an-azure-key-vault-across-regions"></a>Mover um cofre de chaves do Azure entre regiões

O Azure Key Vault não é compatível com operações de movimentação de recursos que permitam mover um cofre de chaves de uma região para outra. Este artigo aborda soluções alternativas para organizações que têm uma necessidade de negócios para mover um cofre de chaves para outra região. Cada solução alternativa tem limitações. É fundamental entender as implicações dessas soluções alternativas antes de tentar aplicá-las em um ambiente de produção.

Para mover um cofre de chaves para outra região, crie um cofre de chaves na outra região e, em seguida, copie manualmente cada segredo individual do cofre de chaves atual para o novo cofre de chaves. É possível fazer isso usando uma das duas opções a seguir.

## <a name="design-considerations"></a>Considerações sobre o design

Antes de começar, lembre-se dos seguintes conceitos:

* Os nomes do cofre de chaves são globalmente exclusivos. Não é possível reutilizar um nome de cofre.
* É preciso reconfigurar as políticas de acesso e as definições de configuração de rede no novo cofre de chaves.
* É preciso reconfigurar a exclusão reversível e limpar a proteção no novo cofre de chaves.
* A operação de backup e restauração não preservará as configurações de rotação automática. Talvez seja necessário redefinir as configurações.

## <a name="option-1-use-the-key-vault-backup-and-restore-commands"></a>Opção 1: Usar os comandos de backup e restauração do cofre de chaves

É possível fazer backup de cada segredo, chave e certificado individual em seu cofre usando o comando de backup. Seus segredos são baixados como um blob criptografado. Em seguida, é possível restaurar o blob em seu novo cofre de chaves. Para obter uma lista de comandos, consulte [Comandos do Azure Key Vault](/powershell/module/azurerm.keyvault#key_vault).

O uso dos comandos de backup e restauração tem duas limitações:

* Não é possível fazer backup de um cofre de chaves em uma região geográfica e restaurá-lo em outra região geográfica. Para obter mais informações, consulte [Geografias do Microsoft Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

* O comando de backup faz backup de todas as versões de cada segredo. Se você tiver um segredo com um grande número de versões anteriores (mais de 10), o tamanho da solicitação poderá exceder o máximo permitido, e a operação poderá falhar.

## <a name="option-2-manually-download-and-upload-the-key-vault-secrets"></a>Opção 2: Baixar e carregar manualmente os segredos do cofre de chaves

É possível baixar certos tipos de segredo manualmente. Por exemplo, é possível baixar certificados como um arquivo PFX. Essa opção elimina as restrições geográficas para alguns tipos de segredos, como certificados. É possível carregar os arquivos PFX em qualquer cofre de chaves em qualquer região. Os segredos são baixados em um formato não protegido por senha. Você é responsável por proteger seus segredos durante a movimentação.