---
title: Habilitar a restrição etária no Azure Active Directory B2C | Microsoft Docs
description: Saiba mais sobre como identificar menores de idade usando seu aplicativo.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/13/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6bd93f9062f8446ce20436a7a04e2054aaf5be71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85386124"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Habilitar a restrição etária no Azure Active Directory B2C

>[!IMPORTANT]
>Esse recurso está em uma versão prévia. Não use o recurso para aplicativos de produção.
>

A retenção de idade no Azure Active Directory B2C (Azure AD B2C) permite que você identifique os pequenos que desejam usar seu aplicativo. Você pode optar por bloquear o menor da conexão ao aplicativo. Os usuários também podem voltar para o aplicativo e identificar seus grupos de idade e seu status de consentimento dos pais. O Active Directory B2C pode bloquear os menores sem o consentimento dos pais. O Azure AD B2C também pode configurar para permitir que o aplicativo decida o que fazer com os menores.

Depois de habilitar a retenção de idade em seu [fluxo de usuário](user-flow-overview.md), os usuários são solicitados quando eles foram gerados e em qual país/região eles residem. Se um usuário que não inseriu as informações anterior entrar, será necessário inserir a próxima vez que entrar. As regras são aplicadas sempre que um usuário entrar.

O Azure AD B2C usa as informações que o usuário insere para identificar se são um menor. O campo **Grupoetário**, em seguida, é atualizado na sua conta. O valor pode ser `null`, `Undefined`, `Minor`, `Adult` ou `NotAdult`.  Os campos **Grupoetário** e **consentProvidedForMinor**, em seguida, são usados para calcular o valor de **legalAgeGroupClassification**.

A restrição etária envolve dois valores de idade: a idade que alguém não é mais considerado menor e a idade em que um menor deve ter consentimento dos pais. A tabela a seguir lista as regras de idade que são usadas para definir um menor e um consentimento de autorização de menor.

| País/Região | Nome do país/região | Idade de consentimento de menor | Idade menor |
| -------------- | ------------------- | ----------------- | --------- |
| Padrão | Nenhum | Nenhum | 18 |
| AE | Emirados Árabes Unidos | Nenhum | 21 |
| AT | Áustria | 14 | 18 |
| BE | Bélgica | 14 | 18 |
| BG | Bulgária | 16 | 18 |
| BH | Bahrein | Nenhum | 21 |
| CM | Camarões | Nenhum | 21 |
| CY | Chipre | 16 | 18 |
| CZ | República Tcheca | 16 | 18 |
| DE | Alemanha | 16 | 18 |
| DK | Dinamarca | 16 | 18 |
| EE | Estônia | 16 | 18 |
| EG | Egito | Nenhum | 21 |
| ES | Espanha | 13 | 18 |
| FR | França | 16 | 18 |
| GB | United Kingdom | 13 | 18 |
| GR | Grécia | 16 | 18 |
| HR | Croácia | 16 | 18 |
| HU | Hungria | 16 | 18 |
| IE | Irlanda | 13 | 18 |
| IT | Itália | 16 | 18 |
| KR | Coreia, República da | 14 | 18 |
| LT | Lituânia | 16 | 18 |
| LU | Luxemburgo | 16 | 18 |
| LV | Letônia | 16 | 18 |
| MT | Malta | 16 | 18 |
| NA | Namíbia | Nenhum | 21 |
| NL | Países Baixos | 16 | 18 |
| PL | Polônia | 13 | 18 |
| PT | Portugal | 16 | 18 |
| RO | Romênia | 16 | 18 |
| SE | Suécia | 13 | 18 |
| SG | Singapura | Nenhum | 21 |
| SI | Eslovênia | 16 | 18 |
| SK | Eslováquia | 16 | 18 |
| TD | Chade | Nenhum | 21 |
| TH | Tailândia | Nenhum | 20 |
| TW | Taiwan | Nenhum | 20 |
| EUA | Estados Unidos | 13 | 18 |

## <a name="age-gating-options"></a>Opções de restrição de idade

### <a name="allowing-minors-without-parental-consent"></a>Permitindo menores sem consentimento dos pais

Para fluxos de usuário que permitem inscrição, entrada ou ambas, você pode optar por permitir a entrada de menores sem consentimento dos pais em seu aplicativo. Para menores sem o consentimento dos pais, eles têm permissão para entrar ou se inscrever normalmente, e o Active Directory B2C emite um token com a declaração **legalAgeGroupClassification**. Essa declaração define a experiência que os usuários têm, como o consentimento dos pais para coletar e atualizar o campo **consentProvidedForMinor**.

### <a name="blocking-minors-without-parental-consent"></a>Bloqueando menores sem consentimento dos pais

Para fluxos de usuário que permitem inscrição, entrada ou ambas, você pode optar por bloquear a entrada de menores sem consentimento dos pais no aplicativo. As seguintes opções estão disponíveis para lidar com os usuários bloqueados no Azure Active Directory B2C:

- Enviar um JSON de volta ao aplicativo – esta opção enviará uma resposta de volta para o aplicativo informando que um foi bloqueado.
- Mostrar uma página de erro – o usuário verá uma página informando que ele não pode acessar o aplicativo.

## <a name="set-up-your-tenant-for-age-gating"></a>Configurar seu locatário para restrição etária

Para usar a restrição etária em um fluxo do usuário, você precisa configurar seu locatário para ter propriedades adicionais.

1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior. Selecione o diretório que contém seu locatário.
2. Selecione **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
3. Selecione **Propriedades** para seu locatário no menu à esquerda.
2. Na seção **Restrição etária**, clique no botão **Configurar**.
3. Aguarde a conclusão da operação e seu diretório estará configurado para a restrição etária.

## <a name="enable-age-gating-in-your-user-flow"></a>Habilitando a restrição etária em seu fluxo de usuário

Depois que seu locatário estiver configurado para usar a restrição etária, você pode usar esse recurso nos [fluxos de usuário](user-flow-versions.md) onde ele está habilitado. Habilite a restrição de idade seguindo estas etapas:

1. Crie um fluxo de usuário que tenha a restrição etária habilitada.
2. Depois de criar o fluxo de usuário, selecione **Propriedades** no menu.
3. Na seção **Restrição etária**, selecione **Habilitado**.
4. Você pode, então, decidir como deseja gerenciar os usuários identificados como menores. Para **Inscrever-se ou entrar**, selecione `Allow minors to access your application` ou `Block minors from accessing your application`. Se os menores de bloqueio for selecionado, selecione `Send a JSON back to the application` ou `Show an error message`.




