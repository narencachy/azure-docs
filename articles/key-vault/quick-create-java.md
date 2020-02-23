---
title: Quickstart -  Azure Key Vault client library for Java
description: Provides format and content criteria for writing Quickstarts for Azure SDK client libraries.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 02/25/2020
ms.service: key-vault
ms.topic: quickstart

---

# Quickstart: Azure Key Vault client library for Java

Get started with the Azure Key Vault client library for Java. Follow the steps below to install the packages and try out example code for basic tasks.

Azure Key Vault helps safeguard cryptographic keys and secrets used by cloud applications and services. Use the Key Vault client library for Java to:

- Increase security and control over keys and passwords.
- Create and import encryption keys in minutes.
- Reduce latency with cloud scale and global redundancy.
- Simplify and automate tasks for TLS/SSL certificates.
- Use FIPS 140-2 Level 2 validated HSMs.

[Source code](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault) | [API reference documentation](https://azure.github.io/azure-sdk-for-java) | [Product documentation](index.yml) | [Samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets/src/samples/java/com/azure/security/keyvault/secrets)

## Prerequisites

- An Azure subscription - [create one for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](https://jdk.java.net/13/) version 13 or above
- [Apache Maven](https://maven.apache.org)
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Visual Studio Code](https://code.visualstudio.com/Download) (optional but recommended)

> This quickstart assumes you are running [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest), [Apache Maven](https://maven.apache.org) and either bash or Windows command prompt in a terminal.

## Setting up

### Create a new Java console app

In a terminal, use the `mvn` command to create a new Java console app with the name `akv-java`.

Windows

```console

mvn archetype:generate -DgroupId=com.keyvault.quickstart ^
                       -DartifactId=akv-java ^
                       -DarchetypeArtifactId=maven-archetype-quickstart ^
                       -DarchetypeVersion=1.4 ^
                       -Dversion=0.1.0 ^
                       -DinteractiveMode=false

```

Bash

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
[INFO] Parameter: version, Value: 0.1.0
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Change your directory to the newly created akv-java folder.

```console

cd akv-java

```

### Files we care about

> Assumes starting from the akv-java directory

- pom.xml
  - Maven project file
- src/main/java/com/keyvault/quickstart/App.java
  - Java source code

### Install the packages

Open the *pom.xml* file in [Visual Studio Code](https://code.visualstudio.com/Download) (or your favorite text editor). Add the following dependency elements to the group of dependencies.

> Note that the slf4j-nop prevents warning messages and isn't part of the solution

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

This quickstart includes basic commands to create an Azure key vault. More detailed instructions are available at [Azure CLI quickstart](quick-create-cli.md), [Azure PowerShell quickstart](quick-create-powershell.md), or [Azure portal quickstart](quick-create-portal.md)

> [!Important]
> Each key vault must have a unique name. Replace `your-unique-keyvault-name` with the name of your key vault in the following examples.
>
> If you get an error on the az keyvault create command, change the environment variable value and try again.

Windows Command Prompt Commands

```console

# your keyvault name must be unique
set jqs_KeyVaultName=your-unique-keyvault-name

# you can change these if you want
set jqs_ResourceGroup=JavaKeyVaultQuickStart
set jqs_Location=EastUS

# create the resource group
az group create -n %jqs_ResourceGroup% -l %jqs_Location%

# create the key vault
az keyvault create -n %jqs_KeyVaultName% -g %jqs_ResourceGroup%

# add a secret
az keyvault secret set --vault-name %jqs_KeyVaultName% --name mySecret --value "Hello from Azure Key Vault!"

# check the value
az keyvault secret show --vault-name %jqs_KeyVaultName% --name mySecret

```

Bash Commands

```bash

# your keyvault name must be unique
export jqs_KeyVaultName=your-unique-keyvault-name

# you can change these if you want
export jqs_ResourceGroup=JavaKeyVaultQuickStart
export jqs_Location=EastUS

# create the resource group
az group create -n $jqs_ResourceGroup -l $jqs_Location

# create the key vault
az keyvault create -n $jqs_KeyVaultName -g $jqs_ResourceGroup

# add a secret
az keyvault secret set --vault-name $jqs_KeyVaultName --name mySecret --value "Hello from Azure Key Vault!"

# check the value
az keyvault secret show --vault-name $jqs_KeyVaultName --name mySecret

```

## Code excerpts

The Azure Key Vault client library for Java allows you to manage secrets and related assets such as keys and certificates.

The code excerpts below will show you how to read the secret we set earlier as well as set, read and delete a new secret.

> The entire console app is [below](#sample-code)

### Add directives

Add the following directives to the top of your code:

```java

import com.microsoft.azure.credentials.*;
import com.microsoft.azure.keyvault.*;
import com.microsoft.azure.keyvault.models.*;
import com.microsoft.azure.keyvault.requests.*;

```

### Access Key Vault securely

The most secure way to authenticate a cloud-based application is with a managed identity; see [Use an App Service managed identity to access Azure Key Vault](managed-identity.md) for more details.

> This quickstart supports using a Managed Identity or using the Azure CLI cached credentials to access Azure Key Vault securely
>
> Once Managed Identity is setup and functioning, set the `jqs_AuthType` environment variable to `MSI` to use Managed Identity

### Authenticate and create a client

Authenticating to your key vault and creating a key vault client depends on the `jqs_KeyVaultName` environment variable set in the [initial setup](#Create-a-resource-group-and-key-vault) above.

> The name of your key vault is expanded to the key vault URL, in the format `https://<your-key-vault-name>.vault.azure.net`

```java

// build the Key Vault URL from the env var
String keyVaultName = System.getenv("jqs_KeyVaultName");
String kvUri = "https://" + keyVaultName.trim() + ".vault.azure.net";

AzureTokenCredentials cred = null;

// check for jqs_AuthType env var
String isMsi = System.getenv("jqs_AuthType");
if (isMsi != null && isMsi.equals("MSI")) {
    // use Managed Identity
    cred = new MSICredentials(AzureEnvironment.AZURE);
} else {
    // use Azure CLI cache
    cred = AzureCliCredentials.create();
}

```

### Retrieve a secret

You can get `mySecret` set during setup with the `kvClient.getSecret` method. You can access the value of the secret with `secret.value()`

```java

SecretBundle secret = kvClient.getSecret(kvUri, "mySecret");

System.out.println(secret.value());

 ```

### Create a secret

You can create (set) `myNewSecret` using the `kvClient.setSecret` method after building the `SetSecretRequest`

```java

SetSecretRequest req = new SetSecretRequest.Builder(kvUrl, "myNewSecret", "My new secret value").build();
kvClient.setSecret(req);


```

### Delete a secret

You can delete `myNewSecret` using the `kvClient.deleteSecret` method

```java

kvClient.deleteSecret(kvUrl, "myNewSecret");

```

### Verify myNewSecret was deleted

You can verify that `myNewSecret` was deleted using `kvClient.getSecret` and checking `secret == null`

```java

// myNewSecret should be null
secret = kvClient.getSecret(kvUrl, "myNewSecret");
System.out.println(secret == null);

```

## Running the code

Open src/main/java/com/keyvault/quickstart/App.java with VS Code and replace all contents with the code below.

> Save and press F5 to run the code

```java

package com.keyvault.quickstart;

import com.microsoft.azure.AzureEnvironment;
import com.microsoft.azure.credentials.*;
import com.microsoft.azure.keyvault.*;
import com.microsoft.azure.keyvault.models.*;
import com.microsoft.azure.keyvault.requests.*;

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {

        String keyVaultName = System.getenv("jqs_KeyVaultName");

        if (keyVaultName == null || keyVaultName.isBlank()) {
            System.out.println("Set the jqs_KeyVaultName environment variable");
            System.exit(-1);
        }

        // build key vault URL
        String kvUrl = "https://" + keyVaultName.trim() + ".vault.azure.net";
        System.out.println(kvUrl);

        try {
            AzureTokenCredentials cred = null;

            // check for jqs_AuthType env var
            String isMsi = System.getenv("jqs_AuthType");
            if (isMsi != null && isMsi.equals("MSI")) {
                // use Managed Identity
                cred = new MSICredentials(AzureEnvironment.AZURE);
            } else {
                // use Azure CLI cache
                cred = AzureCliCredentials.create();
            }

            // can't continue
            if (cred == null || cred.domain() == null){
                System.out.println("Unable to get credentials");
                System.exit(-1);
            }
            System.out.println(cred.domain());

            // create Key Vault client
            KeyVaultClient kvClient = new KeyVaultClient(cred);

            // get mySecret
            // this will fail without proper permissions so make sure to check results
            SecretBundle secret = kvClient.getSecret(kvUrl, "mySecret");
            System.out.println(secret.value());

            // create myNewSecret
            // this will fail without proper permissions so make sure to check results
            SetSecretRequest req = new SetSecretRequest.Builder(kvUrl, "myNewSecret", "My new secret value").build();
            secret = kvClient.setSecret(req);
            System.out.println(secret.id());

            // get myNewSecret
            // this will fail without proper permissions so make sure to check results
            secret = kvClient.getSecret(kvUrl, "myNewSecret");
            System.out.println(secret.value());

            // delete myNewSecret
            // this will fail without proper permissions so make sure to check results
            secret = kvClient.deleteSecret(kvUrl, "myNewSecret");
            System.out.println(secret.id());

            // myNewSecret should be null
            secret = kvClient.getSecret(kvUrl, "myNewSecret");
            System.out.println(secret == null);

        } catch (Exception ex) {
            System.out.println(ex.getMessage());
        }

        // keeps mvn exec:java from waiting for ctl-c
        System.exit(0);
    }
}

```

## Delete Key Vault and Resource Group

When no longer needed, you can use the Azure CLI to remove your key vault and the corresponding resource group.

> **Make sure there are no resources in the Resource Group you want to retain as they will be deleted!**

```console

### Make sure there is nothing else in the resource group as it will be deleted
az group delete -g $jqs_ResourceGroup

```

## Next steps

In this quickstart you created a key vault, set a secret, retrieved secrets and deleted a secret. To learn more about Key Vault and how to integrate it with your applications, continue on to the articles below.

- Read an [Overview of Azure Key Vault](key-vault-overview.md)
- See the [Azure Key Vault developer's guide](key-vault-developers-guide.md)
- Learn about [keys, secrets, and certificates](about-keys-secrets-and-certificates.md)
- Review [Azure Key Vault best practices](key-vault-best-practices.md)
