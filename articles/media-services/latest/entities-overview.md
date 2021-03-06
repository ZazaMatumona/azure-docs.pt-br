---
title: Filtragem, ordenação e paginação de entidades de serviços de mídia
titleSuffix: Azure Media Services
description: Saiba mais sobre filtragem, ordenação e paginação de entidades dos serviços de mídia do Azure v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/21/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: a9f9463cd1cac49e36ea52cafaf4d07f4e709ca7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87053248"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>Filtragem, ordenação e paginação de entidades de serviços de mídia

Este tópico discute as opções de consulta OData e o suporte à paginação disponíveis quando você está listando entidades dos serviços de mídia do Azure v3.

## <a name="considerations"></a>Considerações

* As propriedades de entidades que são do `Datetime` tipo estão sempre no formato UTC.
* O espaço em branco na cadeia de caracteres de consulta deve ser codificado por URL antes de enviar uma solicitação.

## <a name="comparison-operators"></a>Operadores de comparação

Você pode usar os seguintes operadores para comparar um campo com um valor constante:

Operadores de igualdade:

- `eq`: Testar se um campo é *igual a* um valor constante.
- `ne`: Testar se um campo *não é igual a* um valor constante.

Operadores de intervalo:

- `gt`: Testar se um campo é *maior que* um valor constante.
- `lt`: Testar se um campo é *menor que* um valor constante.
- `ge`: Testar se um campo é *maior ou igual a* um valor constante.
- `le`: Testar se um campo é *menor ou igual a* um valor constante.

## <a name="filter"></a>Filtrar

Use `$filter` para fornecer um parâmetro de filtro OData para localizar somente os objetos nos quais você está interessado.

O exemplo de REST a seguir filtra o `alternateId` valor de um ativo:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

O exemplo de C# a seguir filtra na data de criação do ativo:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

## <a name="order-by"></a>Ordenar por

Use `$orderby` para classificar os objetos retornados pelo parâmetro especificado. Por exemplo:  

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

Para classificar os resultados em ordem crescente ou decrescente, acrescente `asc` ou `desc` ao nome do campo, separado por um espaço. Por exemplo: `$orderby properties/created desc`.

## <a name="skip-token"></a>Ignorar token

Se uma resposta de consulta contiver muitos itens, o serviço retornará um `$skiptoken` `@odata.nextLink` valor () que você usará para obter a próxima página de resultados. Use-a para paginar todo o conjunto de resultados.

No Media Services V3, não é possível configurar o tamanho da página. O tamanho da página varia de acordo com o tipo de entidade. Leia as seções individuais a seguir para obter detalhes.

Se as entidades forem criadas ou excluídas enquanto você estiver paginando pela coleção, as alterações serão refletidas nos resultados retornados (se essas alterações estiverem na parte da coleção que não foi baixada).

> [!TIP]
> Sempre use `nextLink` para enumerar a coleção e não depender de um tamanho de página específico.
>
> O `nextLink` valor estará presente somente se houver mais de uma página de entidades.

Considere o exemplo a seguir de onde `$skiptoken` é usado. Certifique-se de substituir *amstestaccount* pelo seu nome de conta e defina o valor *api-version* valor para a versão mais recente.

Se você solicitar uma lista de ativos como esta:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Você receberá uma resposta semelhante a esta:

```
HTTP/1.1 200 OK

{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-00000000-0000-0000-0000-0000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-00000000-0000-0000-0000-000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Em seguida, você pode solicitar a próxima página, enviando uma solicitação get para:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

O exemplo de C# a seguir mostra como enumerar por todos os localizadores de streaming na conta.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>Usando operadores lógicos para combinar opções de consulta

Os serviços de mídia v3 oferecem suporte a operadores lógicos **or** e **and** . 

O exemplo de REST a seguir verifica o estado do trabalho:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

Você constrói a mesma consulta em C# da seguinte maneira: 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>Opções de filtragem e ordenação de entidades

A tabela a seguir mostra como você pode aplicar as opções de filtragem e ordenação a diferentes entidades:

|Nome da entidade|Nome da propriedade|Filtrar|Order|
|---|---|---|---|
|[Ativos](/rest/api/media/assets/)|name|`eq`, `gt`, `lt`, `ge`, `le`|`asc` e `desc`|
||properties.alternateId |`eq`||
||properties.alternateId |`eq`||
||properties.created| `eq`, `gt`, `lt`| `asc` e `desc`|
|[Políticas de chave de conteúdo](/rest/api/media/contentkeypolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.description    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||properties.lastModified|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.policyId|`eq`, `ne`||
|[Trabalhos](/rest/api/media/jobs)| name  | `eq`            | `asc` e `desc`|
||properties.state        | `eq`, `ne`        |                         |
||properties.created      | `gt`, `ge`, `lt`, `le`| `asc` e `desc`|
||properties.lastModified | `gt`, `ge`, `lt`, `le` | `asc` e `desc`| 
|[Localizadores de streaming](/rest/api/media/streaminglocators)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`,  `gt`, `lt`|`asc` e `desc`|
||properties.endTime    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
|[Políticas de streaming](/rest/api/media/streamingpolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
|[Transformações](/rest/api/media/transforms)| name | `eq`            | `asc` e `desc`|
|| properties.created      | `gt`, `ge`, `lt`, `le`| `asc` e `desc`|
|| properties.lastModified | `gt`, `ge`, `lt`, `le`| `asc` e `desc`|

## <a name="next-steps"></a>Próximas etapas

* [Listar ativos](/rest/api/media/assets/list)
* [Listar políticas de conteúdo de chave](/rest/api/media/contentkeypolicies/list)
* [Listar trabalhos](/rest/api/media/jobs/list)
* [Listar políticas de streaming](/rest/api/media/streamingpolicies/list)
* [Listar localizadores de streaming](/rest/api/media/streaminglocators/list)
* [Transmitir um arquivo por streaming](stream-files-dotnet-quickstart.md)
* [Cotas e limites](limits-quotas-constraints.md)
