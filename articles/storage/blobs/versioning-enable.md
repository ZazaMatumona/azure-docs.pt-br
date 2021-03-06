---
title: Habilitar e gerenciar o controle de versão de BLOB (visualização)
titleSuffix: Azure Storage
description: Saiba como habilitar o controle de versão de BLOB (visualização) no portal do Azure ou usando um modelo de Azure Resource Manager.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/10/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 85e8ccd03bd20ed9bb572d482dbc7a06b8af725c
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067266"
---
# <a name="enable-and-manage-blob-versioning-preview"></a>Habilitar e gerenciar o controle de versão de BLOB (visualização)

Você pode habilitar o controle de versão do armazenamento de BLOBs (visualização) para manter automaticamente as versões anteriores de um objeto.  Quando o controle de versão de blob estiver habilitado, você poderá restaurar uma versão anterior de um blob para recuperar seus dados se eles forem modificados ou excluídos erroneamente.

Este artigo mostra como habilitar ou desabilitar o controle de versão de BLOB para a conta de armazenamento usando o portal do Azure ou um modelo de Azure Resource Manager.

Você deve se registrar para a visualização antes de habilitar o controle de versão de BLOB. Para saber mais sobre o controle de versão de BLOB, incluindo como registrar-se para a versão prévia, consulte [versionamento de BLOB (visualização)](versioning-overview.md).

## <a name="enable-blob-versioning"></a>Habilitar controle de versão de blob

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Para habilitar o controle de versão de blob no portal do Azure:

1. Navegue até sua conta de armazenamento no Portal.
1. Em **serviço blob**, escolha **proteção de dados**.
1. Na seção **controle de versão** , selecione **habilitado**.

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Captura de tela mostrando como habilitar o controle de versão de blob no portal do Azure":::

# <a name="template"></a>[Modelo](#tab/template)

Para habilitar o controle de versão de blob com um modelo, crie um modelo com a propriedade **IsVersioningEnabled** como **true**. As etapas a seguir descrevem como criar um modelo no portal do Azure.

1. Na portal do Azure, escolha **criar um recurso**.
1. Em **Pesquisar no Marketplace**, digite **implantação de modelo**e pressione **Enter**.
1. Escolha **implantação de modelo**, escolha **criar**e, em seguida, escolha **criar seu próprio modelo no editor**.
1. No editor de modelo, Cole o JSON a seguir. Substitua o espaço reservado `<accountName>` pelo nome da sua conta de armazenamento.
1. Salve o modelo.
1. Especifique o grupo de recursos da conta e, em seguida, escolha o botão **comprar** para implantar o modelo e habilitar o controle de versão de BLOB.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts/blobServices",
                "apiVersion": "2019-06-01",
                "name": "<accountName>/default",
                "properties": {
                    "IsVersioningEnabled": true
                }
            }
        ]
    }
    ```

Para obter mais informações sobre como implantar recursos com modelos no portal do Azure, consulte [implantar recursos com portal do Azure](../../azure-resource-manager/templates/deploy-portal.md).

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>Modificar um blob para disparar uma nova versão

O exemplo de código a seguir mostra como disparar a criação de uma nova versão com a biblioteca de cliente de armazenamento do Azure para .NET, versão [12.5.0-Preview. 5](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.0-preview.5) ou posterior. Antes de executar este exemplo, verifique se você habilitou o controle de versão para sua conta de armazenamento.

O exemplo cria um blob de blocos e, em seguida, atualiza os metadados do blob. Atualizar os metadados do blob dispara a criação de uma nova versão. O exemplo recupera a versão inicial e a versão atual e mostra que apenas a versão atual inclui os metadados.

```csharp
public static async Task UpdateVersionedBlobMetadata(string containerName, string blobName)
{
    // Create a new service client from the connection string.
    BlobServiceClient blobServiceClient = new BlobServiceClient(ConnectionString);

    // Create a new container client.
    BlobContainerClient containerClient = blobServiceClient.GetBlobContainerClient(containerName);

    try
    {
        // Create the container.
        await containerClient.CreateIfNotExistsAsync();

        // Upload a block blob.
        BlockBlobClient blockBlobClient = containerClient.GetBlockBlobClient(blobName);

        string blobContents = string.Format("Block blob created at {0}.", DateTime.Now);
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        string initalVersionId;
        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            Response<BlobContentInfo> uploadResponse = await blockBlobClient.UploadAsync(stream, null, default);

            // Get the version ID for the current version.
            initalVersionId = uploadResponse.Value.VersionId;
        }

        // Update the blob's metadata to trigger the creation of a new version.
        Dictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "key", "value" },
            { "key1", "value1" }
        };

        Response<BlobInfo> metadataResponse = await blockBlobClient.SetMetadataAsync(metadata);

        // Get the version ID for the new current version.
        string newVersionId = metadataResponse.Value.VersionId;

        // Request metadata on the previous version.
        BlockBlobClient initalVersionBlob = blockBlobClient.WithVersion(initalVersionId);
        Response<BlobProperties> propertiesResponse = await initalVersionBlob.GetPropertiesAsync();
        PrintMetadata(propertiesResponse);

        // Request metadata on the current version.
        BlockBlobClient newVersionBlob = blockBlobClient.WithVersion(newVersionId);
        Response<BlobProperties> newPropertiesResponse = await newVersionBlob.GetPropertiesAsync();
        PrintMetadata(newPropertiesResponse);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        await containerClient.DeleteAsync();
    }
}

static void PrintMetadata(Response<BlobProperties> propertiesResponse)
{
    if (propertiesResponse.Value.Metadata.Count > 0)
    {
        Console.WriteLine("Metadata values for version {0}:", propertiesResponse.Value.VersionId);
        foreach (var item in propertiesResponse.Value.Metadata)
        {
            Console.WriteLine("Key:{0}  Value:{1}", item.Key, item.Value);
        }
    }
    else
    {
        Console.WriteLine("Version {0} has no metadata.", propertiesResponse.Value.VersionId);
    }
}
```

## <a name="next-steps"></a>Próximas etapas

- [Controle de versão de blob (versão prévia)](versioning-overview.md)
- [Exclusão reversível para blobs do Armazenamento do Azure ](soft-delete-overview.md)
