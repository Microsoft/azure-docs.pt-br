---
title: Restringir o conteúdo da CDN do Azure por país/região
description: Saiba como restringir o acesso por país/região ao conteúdo da CDN do Azure usando o recurso de filtragem geográfica.
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 01/16/2021
ms.author: allensu
ms.openlocfilehash: 8901dffb752409acd7fb08a2025bed9a4cc70132
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539501"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>Restringir o conteúdo da CDN do Azure por país/região

## <a name="overview"></a>Visão geral
Quando um usuário solicita seu conteúdo, o conteúdo é servido para os usuários em todos os locais. Talvez você queira restringir o acesso ao seu conteúdo por país/região. 

Com o recurso de *filtragem geográfica* , você pode criar regras em caminhos específicos no ponto de extremidade da CDN. Você pode definir as regras para permitir ou bloquear o conteúdo em países/regiões selecionados.

> [!IMPORTANT]
> **A CDN Standard do Azure dos perfis da Microsoft** não oferecem suporte baseado no caminho da filtragem geográfica.
> 

## <a name="standard-profiles"></a>Perfis Standard

Estas instruções são para os perfis da **CDN standard do Azure da Akamai** e do **Azure CDN Standard da Verizon** .

Para perfis **CDN Premium do Azure da Verizon**, você deve usar o portal **Gerenciar** para ativar a filtragem geográfica. Para obter mais informações, consulte os [perfis CDN Premium do Azure da Verizon](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>Definir o caminho do diretório
Para acessar o recurso de filtragem geográfica, selecione o ponto de extremidade da CDN no portal e, em seguida, selecione **Filtragem Geográfica** em CONFIGURAÇÕES no menu esquerdo. 

![Captura de tela mostrando a filtragem geográfica selecionada no menu de um ponto de extremidade.](./media/cdn-filtering/cdn-geo-filtering-standard.png)

Na caixa **PATH**, especifique o caminho relativo para o local ao qual os usuários terão permissão ou acesso negado. 

É possível aplicar filtragem geográfica a todos os arquivos com uma barra (/) ou selecionar pastas específicas, especificando caminhos de diretórios (por exemplo, */imagens/*). Adicionalmente, é possível aplicar filtragem geográfica a um único arquivo (por exemplo */imagens/city.png*). Várias regras são permitidas. Depois de inserir uma regra, uma linha em branco é exibida para que você insira a próxima regra.

Por exemplo, todos os filtros de caminho de diretório a seguir são válidos:   
*/*                                 
*Contida*     
*/Photos/Strasbourg/*     
*/Fotos/Estrasburgo/city.png*

### <a name="define-the-type-of-action"></a>Definir o tipo de ação

Na lista **AÇÃO**, selecione **Permitir** ou **Bloquear**: 

- **Permitir**: somente os usuários dos países/regiões especificados têm acesso permitido aos ativos solicitados do caminho recursivo.

- **Bloquear**: os usuários dos países/regiões especificados têm acesso negado aos ativos solicitados do caminho recursivo. Se nenhuma outra opção de filtragem de país/região tiver sido configurada para esse local, todos os outros usuários terão acesso permitido.

Por exemplo, uma regra de filtragem geográfica para bloquear o caminho */Fotos/Estrasburgo/* filtra os arquivos a seguir:     
*http: \/ / \<endpoint> . azureedge.net/photos/Strasbourg/1000.jpg* 
 *http: \/ / \<endpoint> . azureedge.net/photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countriesregions"></a>Definir os países/regiões

Na lista **códigos de país** , selecione os países/regiões que você deseja bloquear ou permitir para o caminho. 

Depois de concluir a seleção dos países/regiões, selecione **salvar** para ativar a nova regra de filtragem geográfica. 

![Captura de tela mostra os códigos de país a serem usados para bloquear ou permitir países ou regiões.](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Limpar os recursos

Para excluir uma regra, selecione-a na lista da página **Filtragem Geográfica** e, em seguida escolha **Excluir**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Perfis CDN Premium do Azure da Verizon

Para perfis **da CDN Premium do Azure da Verizon** , a interface do usuário para criar uma regra de filtragem geográfica é diferente:

1. No menu superior do perfil da CDN do Azure, selecione **Gerenciar**.

2. No portal da Verizon, selecione **HTTP Grande** e, em seguida, selecione **Filtragem por País/Região**.

    :::image type="content" source="./media/cdn-filtering/cdn-geo-filtering-premium.png" alt-text="Captura de tela mostra como selecionar a filtragem de país na CDN do Azure" border="true":::
  
3. Selecione **Adicionar Filtro por País/Região**.

4. Na **etapa um:**, insira o caminho do diretório. Selecione **Bloquear** ou **Adicionar** e, em seguida, selecione **Avançar**.

    > [!IMPORTANT]
    > O nome do ponto de extremidade deve estar no caminho.  Exemplo: **/myendpoint8675/MyFolder**.  Substitua **myendpoint8675** pelo nome do ponto de extremidade.
    > 
    
5. Na **etapa dois**, selecione um ou mais países/regiões na lista. Selecione **concluir** para ativar a regra. 
    
    A nova regra aparece na tabela na página **Filtragem por País/Região**.
    
    :::image type="content" source="./media/cdn-filtering/cdn-geo-filtering-premium-rules.png" alt-text="Captura de tela mostra onde a regra aparece na filtragem de país." border="true":::
 
### <a name="clean-up-resources"></a>Limpar os recursos
Na tabela regras de filtragem de país/região, selecione o ícone excluir ao lado de uma regra para excluí-la ou o ícone Editar para modificá-la.

## <a name="considerations"></a>Considerações
* As alterações na sua configuração de filtragem geográfica não entram em vigor imediatamente:
   * Para perfis da **CDN Standard do Azure da Microsoft**, a propagação geralmente é concluída em dez minutos. 
   * Para perfis da **CDN Standard do Azure da Akamai**, a propagação normalmente é concluída em um minuto. 
   * Para perfis da **CDN Standard do Azure da Verizon** e da **CDN Premium do Azure da Verizon**, a propagação geralmente é concluída em 10 minutos. 
 
* Este recurso não dá suporte a caracteres curinga (por exemplo, *).

* A configuração de filtragem geográfica associada ao caminho relativo é aplicada recursivamente a esse caminho.

* Apenas uma regra pode ser aplicada para o mesmo caminho relativo. Ou seja, você não pode criar vários filtros de país/região que apontam para o mesmo caminho relativo. No entanto, como os filtros de país/região são recursivos, uma pasta pode ter vários filtros de país/região. Em outras palavras, uma subpasta de uma pasta configurada anteriormente pode ser atribuída a um filtro de país/região diferente.

* O recurso de filtragem geográfica usa códigos de país para definir os países/regiões dos quais uma solicitação é permitida ou bloqueada para um diretório protegido. Embora os perfis da Akamai e da Verizon deem suporte para a maioria dos códigos do país, existem algumas diferenças. Para obter mais informações, consulte [códigos de país da CDN do Azure](/previous-versions/azure/mt761717(v=azure.100)). 

