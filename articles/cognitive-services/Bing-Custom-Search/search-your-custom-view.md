---
title: Pesquisar uma exibição personalizada – Pesquisa Personalizada do Bing
titleSuffix: Azure Cognitive Services
description: Depois de configurar sua experiência de pesquisa personalizada, você pode testar a partir do portal de Pesquisa Personalizada do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: aahi
ms.openlocfilehash: f00ffee47e3eb6366d632d8b6ee9beb01f048442
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "76983105"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>Chame sua instância de Pesquisa Personalizada do Bing no Portal

Depois de configurar sua experiência de pesquisa personalizada, você pode testar a partir do [portal](https://customsearch.ai) de Pesquisa Personalizada do Bing. 

![uma captura de tela do portal de pesquisa personalizada do Bing](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Criar uma consulta de pesquisa 

Depois de entrar no [portal](https://customsearch.ai)de pesquisa personalizada do Bing, selecione sua instância de pesquisa e clique na guia **produção** . Em **ponto**de extremidade, selecione um ponto de extremidade de API (por exemplo, API Web). Sua assinatura determina quais pontos de extremidade são mostrados.

Para criar uma consulta de pesquisa, insira os valores de parâmetro para seu ponto de extremidade. Observe que os parâmetros exibidos no portal podem mudar dependendo do ponto de extremidade queescolher. Consulte a [referência de API de pesquisa personalizada](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) para obter mais informações. Para alterar a assinatura que sua instância de pesquisa usa, adicione a chave de assinatura apropriada e atualize os parâmetros de mercado e/ou de idioma apropriados.

Alguns parâmetros importantes estão abaixo:


|Parâmetro  |Descrição  |
|---------|---------|
|Consulta     | O termo de pesquisa a ser pesquisado. Disponível somente para pontos de extremidade da Web, de Imagem, de Vídeo e da Sugestão Automática |
|ID de configuração personalizada | O ID de configuração da instância selecionada de Pesquisa Personalizada. Este campo é somente leitura. |
|Market     | O mercado que os resultados serão originados. Disponível somente para Web, Imagem, Vídeo e ponto de extremidade da Interface do Usuário Hospedada.        |
|Chave de Assinatura | A chave de assinatura para o teste. Selecione uma chave na lista suspensa ou inserir uma manualmente.          |

Clicar em **Parâmetros adicionais** revela os seguintes parâmetros:  

|Parâmetro  |Descrição  |
|---------|---------|
|Salvar pesquisa     | Um filtro usado para filtrar as páginas da Web para conteúdo adulto. Disponível somente para Web, Imagem, Vídeo e ponto de extremidade da Interface do Usuário Hospedada. Observe que o Pesquisa de Vídeo personalizado do Bing dá suporte apenas `moderate` a `strict`dois valores: e.        |
|Linguagens de Interface do Usuário    | A linguagem a ser usada para cadeias de caracteres de interface do usuário. Por exemplo, se você habilitar imagens e vídeos na interface do usuário hospedada, as guias **Imagem** e **Vídeo** usarão o idioma especificado.        |
|Contagem     | O número de resultados da pesquisa a ser retornado na resposta. Disponível somente para pontos de extremidade da Web, de Imagem e de Vídeo.         |
|Deslocamento    | O número de resultados da pesquisa a ignorar antes de retornar resultados. Disponível somente para pontos de extremidade da Web, de Imagem e de Vídeo.        |
    
Depois de especificar todas as opções necessárias, clique em **Chamar** para exibir a resposta JSON no painel direito. Se você selecionar o ponto de extremidade da Interface do Usuário Hospedada, poderá testar a experiência de pesquisa no painel inferior.

## <a name="change-your-bing-custom-search-subscription"></a>Alterar sua assinatura do Pesquisa Personalizada do Bing

Você pode alterar a assinatura associada à sua instância de Pesquisa Personalizada do Bing sem criar uma nova instância. Para que as chamadas à API sejam enviadas e cobradas para uma nova assinatura, crie um novo recurso de Pesquisa Personalizada do Bing no portal do Azure. Use a nova chave de assinatura em suas solicitações de API, juntamente com a ID de configuração personalizada da sua instância.

## <a name="next-steps"></a>Próximas etapas

- [Chamar o modo de exibição personalizado com C#](./call-endpoint-csharp.md)
- [Chamar o modo de exibição personalizado com Java](./call-endpoint-java.md)
- [Chamar o modo de exibição personalizado com NodeJs](./call-endpoint-nodejs.md)
- [Chamar o modo de exibição personalizado com Python](./call-endpoint-python.md)

- [Chamar a exibição personalizada com o SDK do C#](./sdk-csharp-quick-start.md)
