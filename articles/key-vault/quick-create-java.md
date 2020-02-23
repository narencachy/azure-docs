---
title: Quickstart -  Azure Key Vault client library for Java
description: Provides format and content criteria for writing Quickstarts for Azure SDK client libraries.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.topic: quickstart

---

# Quickstart: Azure Key Vault client library for Java

Get started with the Azure Key Vault client library for Java. Follow the steps below to install the package and try out example code for basic tasks.

Azure Key Vault helps safeguard cryptographic keys and secrets used by cloud applications and services. Use the Key Vault client library for Java to:

- Increase security and control over keys and passwords.
- Create and import encryption keys in minutes.
- Reduce latency with cloud scale and global redundancy.
- Simplify and automate tasks for TLS/SSL certificates.
- Use FIPS 140-2 Level 2 validated HSMs.

[Source code](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault) | [API reference documentation](https://azure.github.io/azure-sdk-for-java) | [Product documentation](index.yml) | [Samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets/src/samples/java/com/azure/security/keyvault/secrets)

## Prerequisites

- An Azure subscription - [create one for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) version 8 or above
- [Apache Maven](https://maven.apache.org)
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)

This quickstart assumes you are running [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest), [Apache Maven](https://maven.apache.org) and bash in a Linux terminal window.

## Setting up

### Create a new Java console app

In a console window, use the `mvn` command to create a new Java console app with the name `akv-java`.

```bash
mvn archetype:generate -DgroupId=com.keyvault.quickstart \
                       -DartifactId=akv-java \
                       -DarchetypeArtifactId=maven-archetype-quickstart \
                       -DarchetypeVersion=1.4 \
                       -Dversion=0.1.0 \
                       -DinteractiveMode=false
```

The output from generating the project will look something like this:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.quickstart
[INFO] Parameter: artifactId, Value: akv-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.quickstart
[INFO] Parameter: artifactId, Value: akv-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Change your directory to the newly created akv-java folder.

```bash
cd akv-java
```

### Install the packages

Open the *pom.xml* file in your text editor. Add the following dependency elements to the group of dependencies.

```xml

  <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-client-authentication</artifactId>
    <version>1.7.2</version>
  </dependency>

  <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-mgmt-keyvault</artifactId>
    <version>1.31.0</version>
  </dependency>

  <dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-nop</artifactId>
    <version>1.7.30</version>
  </dependency>

```

### Create a resource group and key vault

This quickstart uses a pre-created Azure key vault. You can create a key vault by following the steps in the [Azure CLI quickstart](quick-create-cli.md), [Azure PowerShell quickstart](quick-create-powershell.md), or [Azure portal quickstart](quick-create-portal.md). Alternatively, you can run the Azure CLI commands below.

> [!Important]
> Each key vault must have a unique name. Replace `your-unique-keyvault-name` with the name of your key vault in the following examples.

```bash

# your keyvault name must be unique
export jkv_Name=your-unique-keyvault-name

export jkv_RG=JavaKeyVaultQuickStart
export jkv_Location=EastUS

# create the resource group
az group create -n $jkv_RG -l jkv_Location

# create the key vault
az keyvault create -n $jkv_Name -g jkv_RG

# add a secret
az keyvault secret set --vault-name $jkv_Name --name mySecret --value "Hello from Azure Key Vault!"

```

### Access Key Vault Securely

The most secure way to authenticate a cloud-based application is with a managed identity; see [Use an App Service managed identity to access Azure Key Vault](managed-identity.md) for details. For the sake of simplicity, this quickstart creates a desktop application and uses the Azure CLI cached credentials to access Azure Key Vault.

## Object model

The Azure Key Vault client library for Java allows you to manage keys and related assets such as certificates and secrets. The code samples below will show you how to read the secret we set earlier.

The entire console app is [below](#sample-code).

## Code examples

### Add directives

Add the following directives to the top of your code:

```java

import java.io.IOException;
import com.microsoft.azure.credentials.*;
import com.microsoft.azure.keyvault.*;
import com.microsoft.azure.keyvault.models.*;

```

### Authenticate and create a client

Authenticating to your key vault and creating a key vault client depends on the jkv_Name environment variable set in the [Create a resource group and key vault](#Create-a-resource-group-and-key-vault) step above. The name of your key vault is expanded to the key vault URL, in the format `https://<your-key-vault-name>.vault.azure.net`.

```java

String keyVaultName = System.getenv("jkv_Name");
String kvUri = "https://" + keyVaultName.trim() + ".vault.azure.net";

AzureTokenCredentials cred = AzureCliCredentials.create();
KeyVaultClient kvClient = new KeyVaultClient(cred);

```

### Retrieve a secret

You can get `mySecret` set during setup with the `kvClient.getSecret` method.

```java

SecretBundle secret = kvClient.getSecret(kvUri, "mySecret");

System.out.println(secret.value());

 ```

You can access the value of the secret with `secret.value()`.

## Clean up resources

When no longer needed, you can use the Azure CLI to remove your key vault and the corresponding resource group.

### Make sure there is nothing else in the resource group as it will be deleted

```bash

az group delete -g $jkv_RG

```

## Sample code

```java

package com.keyvault.quickstart;

import java.io.IOException;
import com.microsoft.azure.credentials.*;
import com.microsoft.azure.keyvault.*;
import com.microsoft.azure.keyvault.models.*;

public class App {

    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {

        String keyVaultName = System.getenv("jkv_Name");

        if (keyVaultName == null || keyVaultName.isBlank()) {
            System.out.println("Set the jkv_Name environment variable");
            System.exit(-1);
        }

        String kvUri = "https://" + keyVaultName.trim() + ".vault.azure.net";

        try {
            // uses the Azure CLI credentials
            AzureTokenCredentials cred = AzureCliCredentials.create();

            KeyVaultClient kvClient = new KeyVaultClient(cred);

            SecretBundle secret = kvClient.getSecret(kvUri, "mySecret");

            System.out.println(secret.value());

        } catch (IOException ex) {
            System.out.println(ex.getMessage());
        }
    }
}

```

## Next steps

In this quickstart you created a key vault, set a secret and retrieved that secret. To learn more about Key Vault and how to integrate it with your applications, continue on to the articles below.

- Read an [Overview of Azure Key Vault](key-vault-overview.md)
- See the [Azure Key Vault developer's guide](key-vault-developers-guide.md)
- Learn about [keys, secrets, and certificates](about-keys-secrets-and-certificates.md)
- Review [Azure Key Vault best practices](key-vault-best-practices.md)
