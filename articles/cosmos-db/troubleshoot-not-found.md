---
title: Solucionar problemas Azure Cosmos DB exceções não encontradas
description: Saiba como diagnosticar e corrigir exceções não encontradas.
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 1d778b4330389d23b0fe7179a005abfbd7d66d5c
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871098"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found-exceptions"></a>Diagnosticar e solucionar problemas Azure Cosmos DB exceções não encontradas
O código de status HTTP 404 representa que o recurso não existe mais.

## <a name="expected-behavior"></a>Comportamento esperado
Há muitos cenários válidos em que um aplicativo espera um código 404 e manipula corretamente o cenário.

## <a name="a-not-found-exception-was-returned-for-an-item-that-should-exist-or-does-exist"></a>Uma exceção não encontrada foi retornada para um item que deveria existir ou existe
Aqui estão os possíveis motivos para um código de status 404 ser retornado se o item deve existir ou existir.

### <a name="race-condition"></a>Condição de corrida
Há várias instâncias de cliente SDK e a leitura aconteceu antes da gravação.

#### <a name="solution"></a>Solução:
1. A consistência de conta padrão para Azure Cosmos DB é a consistência da sessão. Quando um item é criado ou atualizado, a resposta retorna um token de sessão que pode ser passado entre as instâncias do SDK para garantir que a solicitação de leitura esteja lendo de uma réplica com essa alteração.
1. Altere o [nível de consistência](consistency-levels-choosing.md) para um [nível mais forte](consistency-levels-tradeoffs.md).

### <a name="invalid-partition-key-and-id-combination"></a>Combinação de chave de partição e ID inválida
A combinação de chave de partição e ID não é válida.

#### <a name="solution"></a>Solução:
Corrija a lógica do aplicativo que está causando a combinação incorreta. 

### <a name="invalid-character-in-an-item-id"></a>Caractere inválido em uma ID de item
Um item é inserido em Azure Cosmos DB com um [caractere inválido](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks) na ID do item.

#### <a name="solution"></a>Solução:
Altere a ID para um valor diferente que não contenha os caracteres especiais. Se a alteração da ID não for uma opção, você poderá codificar a ID em base64 para escapar os caracteres especiais.

Os itens já inseridos no contêiner para a ID podem ser substituídos usando valores de RID em vez de referências baseadas em nome.
```c#
// Get a container reference that uses RID values.
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// Invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // Choose a new ID that doesn't contain special characters.
        // If that isn't possible, then Base64 encode the ID to escape the special characters.
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes);

        // Update the item with the new ID value by using the RID-based container reference.
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validating the new ID can be read by using the original name-based container reference.
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="time-to-live-purge"></a>Limpeza de vida útil
O item tinha a propriedade [TTL (vida útil)](https://docs.microsoft.com/azure/cosmos-db/time-to-live) definida. O item foi limpo porque a propriedade TTL expirou.

#### <a name="solution"></a>Solução:
Altere a propriedade TTL para impedir que o item seja limpo.

### <a name="lazy-indexing"></a>Indexação lenta
A [indexação lenta](index-policy.md#indexing-mode) não foi detectada.

#### <a name="solution"></a>Solução:
Aguarde até que a indexação acompanhe ou altere a política de indexação.

### <a name="parent-resource-deleted"></a>Recurso pai excluído
O banco de dados ou o contêiner no qual o item existe foi excluído.

#### <a name="solution"></a>Solução:
1. [Restaure](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore#backup-retention-period) o recurso pai ou recrie os recursos.
1. Crie um novo recurso para substituir o recurso excluído.

## <a name="next-steps"></a>Próximas etapas
* [Diagnostique e solucione](troubleshoot-dot-net-sdk.md) problemas ao usar o SDK do .net Azure Cosmos DB.
* Saiba mais sobre as diretrizes de desempenho para o [.net v3](performance-tips-dotnet-sdk-v3-sql.md) e o [.net v2](performance-tips.md).