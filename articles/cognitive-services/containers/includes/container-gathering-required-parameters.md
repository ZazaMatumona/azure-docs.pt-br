---
title: Coletando parâmetros necessários
services: cognitive-services
author: aahill
manager: nitinme
description: Os parâmetros para todos os contêineres de serviços cognitivas
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 85c67384c38376dc6f5701cf26e879d6e1b76c7f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80875020"
---
## <a name="gathering-required-parameters"></a>Coletando parâmetros necessários

Há três parâmetros principais para todos os contêineres de serviços cognitivas que são necessários. O contrato de licença de usuário final (EULA) deve estar presente com um valor de `accept` . Além disso, uma URL de ponto de extremidade e uma chave de API são necessárias.

### <a name="endpoint-uri-endpoint_uri"></a>URI do ponto de extremidade`{ENDPOINT_URI}`

O valor do URI do **ponto de extremidade** está disponível na página de *visão geral* portal do Azure do recurso de serviço cognitiva correspondente. Navegue até a página *visão geral* , focalize o ponto de extremidade e um `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> ícone será exibido. Copie e use onde for necessário.

![Coletar o URI do ponto de extremidade para uso posterior](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Novas`{API_KEY}`

Essa chave é usada para iniciar o contêiner e está disponível na página chaves do portal do Azure do recurso de serviço cognitiva correspondente. Navegue até a página *chaves* e clique no `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> ícone.

![Obter uma das duas chaves para uso posterior](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Essas chaves de assinatura são usadas para acessar sua API de serviço cognitiva. Não compartilhe suas chaves. Armazene-os com segurança, por exemplo, usando Azure Key Vault. Também recomendamos regenerar essas chaves regularmente. Apenas uma chave é necessária para fazer uma chamada à API. Ao regenerar a primeira chave, você pode usar a segunda chave para obter acesso contínuo ao serviço.