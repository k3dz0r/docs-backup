---
id: "providers_offers"
title: "Providers and Offers"
slug: "/cli_guides/providers_offers"
sidebar_position: 4
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# Create and manage providers and offers

## About

This guide explains how to register a _provider_ on Super Protocol and create a _solution_ and _data offer_. As a provider, you can monetize your offers by earning TEE tokens from leasing your products to clients.

Confidentiality and decentralization ensure that clients can only use your offers in their orders on your terms. In other words, users cannot download, modify, or otherwise access your solution or data without your permission even when they use it in their orders. Confidentiality protects both providers and customers.

:::info Testnet limitations

As of Testnet 5, you can only create solution and data offers. The ability to create _storage_ and _compute offers_ will be available in future releases. Monetization is only possible with test TEE tokens until the project goes to Mainnet.

:::

## Prerequisites

This guide requires a general understanding of Super Protocol basic concepts such as _Marketplace_, _orders_, _offers_, _slots_, etc. Learn about [Super Protocol fundamentals](/developers/fundamentals) before proceeding.

To set up a provider and create offers, you need:

- Computer with Linux or macOS operating system. On Windows, install [WSL](https://learn.microsoft.com/en-us/windows/wsl/install) (Windows Subsystem for Linux).

- Super Protocol Testnet access. If you do not have it yet, [apply to join](/testnet/). The Super Protocol team sends out invites daily, but it may take several days if the number of requests is high. You can ask any Community Manager on the [Super Protocol Discord server](https://discord.gg/superprotocol) about the status of your request. When your access is ready, you will receive an email with your Testnet credentials:
  + _Testnet Account address_ – the public key of Super testnet wallet;
  + _Private Key_ – the private key of the Super Protocol testnet wallet;
  + _Access Token_ – necessary to receive free test TEE and MATIC tokens.

- [SPCTL](/developers/cli_guides/) – Super Protocol CLI tool. You will need it to upload your offers and create an Offer Provisioner order. Refer to the [SPCTL guide](/developers/cli_guides/configuring) to install and configure it.

- (optional) [Storj](https://www.storj.io/) account, either Free Trial or Pro.

### Set up Provider Tools

_Provider Tools_ is a special application that is necessary to set up providers and create offers.

To install Provider Tools, create a separate directory, open Terminal there, and run the following command:

<Tabs>
  <TabItem value="linux" label="Linux" default>
```
curl -L https://github.com/Super-Protocol/provider-tools/releases/latest/download/provider-tools-linux-x64 -o provider-tools
chmod +x ./provider-tools
```

  </TabItem>
  <TabItem value="macos" label="macOS">
```
curl -L https://github.com/Super-Protocol/provider-tools/releases/latest/download/provider-tools-macos-x64 -o provider-tools
chmod +x ./provider-tools
```

  </TabItem>
</Tabs>

:::note

To avoid confusion, do not put Provider Tools in the same directory as your SPCTL. In the later steps, you will create a separate copy of SPCTL in this directory to manage your offers.

:::

Provider Tools and SPCTL is a work in progress. Super Protocol keeps optimizing the application and the offer creation and management process.

## Main steps

To set up a provider and create an active offer, you must complete the following steps:

1. Set up _provider accounts_. Besides the Testnet Account, there are three provider accounts you have to create using Provider Tools.

2. Prepare and upload content. Use SPCTL to prepare your solution or data, upload it to a remote storage.

3. Configure the offer. You need to provide a general description of the offer, its properties, restrictions, and requirements.

4. Create the provider and offer on the blockchain.

5. Deploy the offer on Super Protocol. _Offer Provisioner_ is an application that regularly checks for new orders that use your active offer and allows such orders to be processed.

6. Pass the Marketplace moderation.

7. Keep your offer alive.

## Step 1. Set up accounts

### Types of accounts

_User Account_ is the main wallet you should use to create and manage orders. This is your Testnet Account provided by Super Protocol.

But to become a provider, you also need three other accounts:

1. _Authority Account_ – to create and manage your provider. This is your main provider account on Super Protocol.

2. _Action Account_ – to create and manage offers. This account executes actions on behalf of the Authority account.

3. _Token Receiver Account_ – to receive rewards in TEE tokens for providing offers on Super Protocol.

---

### Create accounts

Use Provider Tools to generate the three provider accounts. Go to the directory where you downloaded Provider Tools and run the `./provider-tools setup` command.

The tool will request your Testnet Access Token and then ask you questions regarding creation of the provider accounts:

`Do you need to generate a new Authority account?`<br/>
Select `Yes`.

`Do you need to generate a new Action account?`<br/>
Select `Yes`.

`Do you need to generate a new TokenReceiver account?`<br/>
Select `Yes`.

Alternatively, you can use your own previously created Polygon accounts. Create them through Metamask and then follow [this guide](https://docs.polygon.technology/tools/wallets/metamask/add-polygon-network/) to add the Polygon Amoy Testnet network. Select `No` in the menu when prompted to generate new accounts and enter their private keys.

---

**Expected step result:**

* Provider Tools has created `provider-tools-config.json` with private keys to your provider accounts.

## Step 2. Prepare content

 Before clients can use your application or dataset in their orders, you have to prepare, pack, and upload it to a storage. _Offer content_ is prepared and packed solution or data. 
 
 Go to your SPCTL directory and create the offer content using your User Account.

### Prepare the offer content

**For solution offers**

Solutions have to be packed and signed with [Gramine](https://gramineproject.io/) (aka "graminized") to be executed inside an Intel SGX confidential enclave (_Trusted Execution Environment_ or _TEE_). To prepare and pack your solution, use the [`solutions prepare`](/developers/cli_commands/solutions/prepare) command. It will create a TAR.GZ archive with your solution and a JSON file with necessary metadata.

You can find a detailed example of how to prepare a Python solution in the [Python deployment guide](/developers/deployment_guides/python/solution_prep).

**For data offers**

To prepare Data, put it into a TAR.GZ archive. Use the following command:

```bash
tar -czvf OFFER_CONTENT.tar.gz -C ./DATA .
```

Where:
- `OFFER_CONTENT.tar.gz` is the desired name of the output archive;
- `./DATA` is the relative path to the directory with your data; ensure no hidden and system files end up in the archive.

### Upload offer content

You need to upload your offer content to a remote storage, from where TEE can download it and use in clients' orders. Use the following [`files upload`](/developers/cli_commands/files/upload) command to create a storage order on Super Protocol with your offer content:

```bash
./spctl files upload OFFER_CONTENT.tar.gz --storage 25,33 --min-rent-minutes 43200
```

Where:
- `OFFER_CONTENT.tar.gz` is the name of the archive with your prepared solution or data;
- `--storage 25,33` - slot 33 of storage offer 25. Maximum disk capacity for this slot is 0.977 GB; you may choose [another slot](https://marketplace.superprotocol.com/storage?offer=offerId%3D25&tab=pricing) that suits your offer content better;
- `--min-rent-minutes 43200` - the lease time is 30 days, because the offer content must be available on demand. You can make the lease shorter or longer. You can also [replenish the balance](/developers/cli_commands/orders/replenish-deposit) later to prolong the storage order.

As a result, SPCTL will generate the `resource.json` file with information for TEE on how to access your uploaded offer content. Copy this file to the Provider Tools directory.

You can check all your orders, including storage orders, using the [`orders list`](/developers/cli_commands/orders/list) command.

---

**Expected step result:**
* You have prepared, packed, and uploaded your offer content to a long term storage;
* `resource.json` created by SPCTL is in the Provider Tools directory.

## Step 3. Configure the offer

In this step, you need create two JSON files in the Provider Tools directory. The first one should contain a general description and properties of the offer. The second one should specify the required slots and options for your solution or data offer.

### Offer description

Create the first JSON file in the Provider Tools directory. In this guide, it will be called `offer-info.json`, but you can choose any name you want.

:::note

It is important that your offer is well-documented, operational, and not containing anything illegal. Refer to the [moderation guidelines](/developers/marketplace/moderation/) for more details.

:::

Copy and add the format below into this JSON file:

```json title="offer-info.json"
{
  "name": "Name of your offer",
  "group": "0",
  "offerType": "OFFER_TYPE",
  "cancelable": false,
  "description": "Description of your offer; it may include HTML",
  "restrictions": {
    "offers": [
      "REQUIRED_OFFER_ID"
    ],
    "types": [
      "REQUIRED_OFFER_TYPE"
    ]
  },
  "metadata": "",
  "input": "",
  "output": "",
  "allowedArgs": "",
  "allowedAccounts": [],
  "argsPublicKey": "",
  "resultResource": "",
  "linkage": "",
  "hash": ""
}
```

Fill in the following fields:

- `name` – the name of your offer;
- `group` – leave it as `0`;
- `cancelable` – leave it as `False`;
- `offerType` – change `OFFER_TYPE` to `2` for a solution offer or `3` for a data offer;
- `description` – the description of your offer, it may contain HTML;
- `restrictions` – if your offer depends on existing offers (for example, on [Python Base Image](https://marketplace.superprotocol.com/solutions?offer=offerId%3D5) or [Node.js Base Image](https://marketplace.superprotocol.com/solutions?offer=offerId%3D6)), specify them here:
  + `offers` – IDs of such required offers, including IDs of their own dependencies. Change `REQUIRED_OFFER_ID` to the ID of an existing offer that your offer depends upon. If there are several required offers, put each ID in quotation marks and separate with a comma. See the examples below;
  + `types` – offer types for every required offer. Change `REQUIRED_OFFER_TYPE` to either `2` for a solution offer in `offers` or `3` for a data offer in `offers`. If there are several IDs in `offers`, state the type for each of them. Put each type in quotation marks and separate with a comma. See the examples below.

**Example 1**: For a solution offer identical to [Image Classification](/developers/offers/python-image), the part of `offer-info.json` that you have to fill out should look like this:

```json
"name": "Image Classification",
"group": "0",
"offerType": "2",
"cancelable": false,
"description": "Machine-trained Python model that recognizes and classifies objects in an image<br/><br/>This demo solution is compatible with Image Classification Dataset #1 and #2.<br/><br/>It is also possible to use custom datasets. Refer to the documentation for detailed instructions.",
"restrictions": {
  "offers": [
    "5"
  ],
  "types": [
    "2"
  ]
}
```

Image Classification is a solution offer, hence `"offerType": "2"`. It depends on Python Base Image (ID 5), hence there is `"offers": ["5"]` in `restrictions`. Base Image is a solution offer, hence `"types": ["2"]`.

**Example 2**: For a data offer identical to [Image Classification Dataset #1](https://marketplace.superprotocol.com/data?offer=offerId%3D18), the part of `offer-info.json` that you have to fill out should look like this:

```json
"name": "Image Classification Dataset #1",
"group": "0",
"offerType": "3",
"cancelable": false,
"description": "Dataset with images of various breeds of dogs<br/><br/>This demo dataset is compatible with the Image Classification solution. Refer to the documentation for detailed instructions.",
"restrictions": {
  "offers": [
    "8",
    "5"
  ],
  "types": [
    "2",
    "2"
  ]
}
```

Image Classification Dataset #1 is a data offer, hence `"offerType": "3"`.

Users must use it with the Image Classification solution offer (ID 8), so there is `"8"` in the requirements. Image Classification solution, in turn, requres Python Base Image solution offer (ID 5), so you must include its ID as well, put each ID in quotation marks, and separate with a comma: `"offers": ["8","5"]`.

Further, you must specify the type for every offer in the `restrictions`. Both Image Classification and Python Base Image are solution offers, hence `"types": ["2","2"]`.

To get more examples, check existing offers via the Marketplace GUI:

<img src={require('./../images/cli_guides_providers_offers_1.png').default} width="auto" height="auto"/>

<br/>

<img src={require('./../images/cli_guides_providers_offers_2.png').default} width="auto" height="auto"/>

<br/>

Or via CLI using the SPCTL [`offers get`](/developers/cli_commands/offers/offers/get) command:

```
./spctl offers get value 8
```

<img src={require('./../images/cli_guides_providers_offers_3.png').default} width="auto" height="auto"/>

<br/>

Refer to the [`offers update`](/developers/cli_commands/offers/offers/update) command's documentation to learn more about other fields.

---

### Offer requirements

You must specify the values of the required slots and options for your offer. Each slot has its price, either Fixed or Per Hour. Depending on the requirements, the client will select a compute offer configuration, which cannot be lower than your requirements. Read more about the slots and options [here](/developers/fundamentals/slots).

Create... In this guide, it will be called `slot-info.json`, but you can choose any name you want.

Copy and add the following example format to your `slot-info.json` file:

```json title="slot-info.json"
{
  "info": {
    "cpuCores": 1.15,
    "gpuCores": 0,
    "diskUsage": 1073741824,
    "ram": 1073741824
  },
  "usage": {
    "maxTimeMinutes": 0,
    "minTimeMinutes": 0,
    "price": "1000000000000000000",
    "priceType": "1" 
  },
  "option": {
    "bandwidth": 1000000,
    "traffic": 1000000000,
    "externalPort": 0
  }
}
```

Modify all these fields as necessary. Since this is your offer, only you know the [pricing terms](/developers/fundamentals/orders#cost-and-pricing) and compute configuration your solution or data needs to run.

Refer to the [`offers add-slot`](/developers/cli_commands/offers/slots/add-slot) command's documentation to learn more about these fields.

Every offer you create may have multiple requirements slots. You must create a separate JSON file for each slot.

To get examples, check existing offers via the Marketplace GUI:

<img src={require('./../images/cli_guides_providers_offers_4.png').default} width="auto" height="auto"/>

<br/>

Or via CLI using the SPCTL [offers get-slot](/developers/cli_commands/offers/slots/get-slot) command:

```
./spctl offers get-slot value --offer 8 --slot 4
```

<img src={require('./../images/cli_guides_providers_offers_5.png').default} width="300" height="auto"/>

<br/>

---

**Expected step result:**
* `offer-info.json` with offer description;
* `slot-info.json` with offer requirements (slots and options). There may be more than one such file.

## **Step 4. Create the provider and offer**

At this point, you should have the following files in the Provider Tools directory:
* `provider-tools-config.json` with the credentials for Authority, Action, and Token Receiver Accounts;
* `offer-info.json` with the description of the offer;
* `slot-info.json`, one or several, with the required slots for the offer;
* `resource.json` with information for TEE on how to access your uploaded offer content.

### Create provider and offer

Run this command in the Provider Tools directory to register your offer:

```
./provider-tools register TYPE --result ./resource.json
```

Change `TYPE` to either `data` or `solution`.

The tool will take you through the following steps:

**First**, Provider Tools downloads a service copy of SPCTL into a temporary directory. Do not use this copy of SPCTL to run commands.

**Second**, the Provider Tools checks whether a provider with the Authority Account specified in `provider-tools-config.json` is already registered on blockchain. If provider exists, the prompt will go to the next step.

If provider does not exist, you will be prompted to create one. Specify the desired provider name and write a short description. Provider Tools will also prompt you to save the provider info to a JSON in case you need to [update](/developers/cli_guides/providers_offers#updating-provider-info) the provider description later.

**Third**, Provider Tools asks if this provider already has a solution or data offer created on blockchain. If you want to create a new offer, select `No`. Provider Tools will ask for the `offer-info.json` and `slot-info.json` from Step 3.

When done, Provider Tools creates a new `execution-controller` directory (either `solution-execution-controller` or `data-execution-controller`, depending on the type of your order). This directory contains `.env` and `config.json` with all the necessary artifacts to launch an Offer Provisioner order in the [next step](/developers/cli_guides/providers_offers#step-5-run-offer-provisioner).

### Action Account SPCTL

Provider Tools also creates `spctl-config-ACTION_ACCOUNT_PUBLIC_KEY.json` in its root directory, where `ACTION_ACCOUNT_PUBLIC_KEY` is your Action Account address. This file is an SPCTL configuration file that allows you to manage your provider and offers.

Rename this file to `config.json` so SPCTL can recognize it as its configuration file. Copy or [download](/developers/cli_guides/configuring) SPCTL to the Provider Tools root directory.

Alternatively, you can copy this Action Account SPCTL config to your SPCTL directory and manage you provider and orders using the `--config` option with [SPCTL commands](/developers/cli_commands).

---

**Expected step result:**
- Your provider is created on blockchain. The provider address is its Authority Account public key. To get it, find the Authority Account private key in `provider-tools-config.json` and import your Authority Account into MetaMask.
- Your solution or data offer is created on blockchain. You can find its ID and private key in `provider-tools-config.json`.
- `execution-controller` directory contains all necessary files to run Offer Provisioner.
- `spctl-config-ACTION_ACCOUNT_PUBLIC_KEY.json` contains SPCTL configuration for your Action Account.

## **Step 5. Run Offer Provisioner**

### About Offer Provisioner

Offer Provisioner is a utility application for solution and data providers. It is a critical part of running and maintaining an active offer. It enables clients to launch orders using the provider's solution or data offers.

Every five minutes, Offer Provisioner checks for new orders that use your active solution or data offer. When found, the application decrypts the order with its private key and allows it to be processed securely. If the order does not receive a response from your Offer Provisioner, it will not process and will hang indefinitely. If your offer fails to respond for 15 minutes, the system marks it as potentially abandoned by moving it into the Inactive category.

If your offer becomes Inactive, you can reactivate it by contacting the Super Protocol team on [Discord](https://discord.gg/superprotocol) in the [#developers](https://discord.com/channels/951018794590023681/1177643255198920737) channel. Check the [moderation guidelines](/developers/marketplace/moderation/).

### Create an Offer Provisioner order

Execute the following command to launch an Offer Provisioner order:

```bash
./provider-tools deploy OFFER_TYPE --path "/ABSOLUTE/PATH/TO/PROVIDER_TOOLS_DIRECTORY/OFFER_TYPE-execution-controller"
```

Change the placeholders to the absolute path to your execution-controller directory and the type of your offer, for example:

```bash
./provider-tools deploy solution --path "/mnt/drive/provider-tools/solution-execution-controller"
```

And then you are done! Check that your offer is on blockchain using the [offers get](/developers/cli_commands/offers/offers/get) command:

```
./spctl offers get value OFFER_ID
```

Where:
- `OFFER_ID` is the ID of your offer. You can find it in the `provider-tools-config.json` in the Provider Tools root directory.

<img src={require('./../images/cli_guides_providers_offers_6.png').default} width="500" height="auto"/>

<br/>

## **Step 6. Marketplace GUI moderation**

Every new offer appears in the **Unmoderated** category in the Marketplace GUI. This category is for the offers that the Super Protocol team has not yet reviewed. It is not recommended to run offers from this category because they might not work. However, Super Protocol is a permissionless cloud, so anyone can create an order using any offer. 

If you want the Super Protocol team to review your offer and move it to the **Approved** category in the Marketplace GUI, then follow the [moderation guidelines](/developers/marketplace/moderation/).

## **Step 7. Keep your offer active**

As a provider, you must keep track of the  things to ensure your offer stays active.

### Lease on uploaded offer content

:::warning Important!

Ensure your storage order does not end. If you created a [storage order](/developers/cli_commands/files/upload) for your offer content in [Step 2](/developers/cli_guides/providers_offers#upload-offer-content), keep track of the balance and replenish it in time.

:::

If the storage expires, then the TEE will not be able to access your uploaded offer content, and the customer order will fail. Use the [`orders replenish-deposit`](/developers/cli_commands/orders/replenish-deposit) command to add tokens to the balance of the storage order.

If an order fails due to a provider fault, then the offer becomes Inactive in the Marketplace GUI.

### Lease on Offer Provisioner

:::warning Important!

Ensure your Offer Provisioner order does not end. Keep track of the balance and replenish it in time.

:::

Your [Offer Provisioner order](/developers/cli_guides/providers_offers#create-an-offer-provisioner-order) has to remain active and be ready to authorize your offer. Use the [`orders replenish-deposit`](/developers/cli_commands/orders/replenish-deposit) command to add tokens to the Offer Provisioner order.

Be a responsible provider. If you no longer wish to provide products and services on Super Protocol, [disable your offer](/developers/cli_commands/offers/offers/disable).

## **FAQ**

### Update provider info

To update provider information (name, description, associated Action and Token Receiver Accounts), make changes in `provider.json` you saved in [Step 4](/developers/cli_guides/providers_offers#create-provider-and-offer). Then, use the [`providers update`](/developers/cli_commands/providers/update) SPCTL command.

### Update offer info

**Offer description:**

Modify `offer-info.json` you have prepared in [Step 3](/developers/cli_guides/providers_offers#offer-description) and then run the [`offers update`](/developers/cli_commands/offers/offers/update) SPCTL command.

**Offer requirements (slots):**

Modify `slot-info.json` you have prepared in [Step 3](/developers/cli_guides/providers_offers#offer-requirements) and then run the [`offers update-slot`](/developers/cli_commands/offers/slots/update-slot) SPCTL command.

Similarly, you can use the [`offers add-slot`](/developers/cli_commands/offers/slots/add-slot) command to add another slot and the [`offers delete-slot`](/developers/cli_commands/offers/slots/delete-slot) command to remove a slot.

### Create additional offers

To create a new offer in addition to an existing one on the same provider, you need to go through the process again.

1. Rename or otherwise save the `solution-execution-controller` or `data-execution-controller` folder. The new offer will overwrite the `.env` file and you might still need it to manage the Offer Provisioner order for your previous offer.

2. Go through Steps 2-6.

If for some reason you need to create a Offer Provisioner order for an eisting offer, use `.env` of that offer. Every active offer must have its Offer Provisioner order.

### Enable/disable offers

If you no longer want to provide products and services on Super Protocol, disable your offer using the [`offers disable`](/developers/cli_commands/offers/offers/disable) SPCTL command. This will not delete your offer, but it will make it unavailable to use in orders. Similarly, if you want to reinstate it back to active status, use the [`offers enable`](/developers/cli_commands/offers/offers/enable) command.

### Inactive orders

Offers may be flagged as Inactive in the Marketplace GUI for two reasons:

1. If the offer content uploaded to Storj is no longer accessible because the lease has expired. In this case, recreate your offer. Due to confidentiality and security, the Super Protocol team cannot change the resource link in an active offer.

2. If the lease on the order with your Offer Provisioner has expired. In this case, create a new Offer Provisioner order, contact Super Protocol Community Managers on [Discord](https://discord.gg/superprotocol), and they will reactivate your offer.

### How to troubleshoot

If any issue occurs while creating an offer or its slot, you can always check the error details in the `error.log` file located in `tool` directory in Provider Tools directory and take action or contact our support.

## Support

If you have any issues or questions, contact Super Protocol on [Discord](https://discord.gg/superprotocol). The Community Managers will be happy to help you.