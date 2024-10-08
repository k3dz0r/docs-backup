---
id: "web3-access"
title: "Set Up Web3 User Access"
slug: "/web3-access"
sidebar_position: 3
---

This guide provides step-by-step instructions on how to log in to the Super Protocol AI Marketplace via the [web app](https://beta.marketplace.superprotocol.com/) as a Web3 user.

:::note

The AI Marketplace web app does not support mobile devices. Use a desktop or laptop with a screen resolution of at least 1280x720.

:::

## Step 1. Install MetaMask

If you already have MetaMask installed, consider creating a new wallet specifically for the Marketplace instead of using an existing one.

If you do not have MetaMask installed, add the MetaMask extension to your browser from its [official website](https://metamask.io/) or directly from your browser's extension store. During the installation, create a new wallet.

## Step 2. Add a new network

Open the MetaMask extension window and then the **Select a network** menu in the top-left corner.

<img src={require('./images/ai-marketplace-02.png').default} width="400" height="auto" border="1"/>
<br/>
<br/>

There, press the **+ Add network** button.

<img src={require('./images/ai-marketplace-03.png').default} width="400" height="auto" border="1"/>
<br/>
<br/>

Then, click **Add a network manually** at the bottom of the page.

<img src={require('./images/ai-marketplace-04.png').default} width="800" height="auto" border="1"/>
<br/>
<br/>

Set the following parameters and press **Save**.

<img src={require('./images/ai-marketplace-05.png').default} width="400" height="auto" border="1"/>
<br/>
<br/>

| **Field** | **Value** |
| :- | :- |
| Network name | Super Protocol Polygon Amoy Testnet |
| New RPC URL | https://amoy.polygon.superprotocol.com/hesoyam |
| Chain ID | 80002 |
| Currency Symbol | MATIC |
| Block Explorer URL | [https://www.oklink.com/amoy](https://www.oklink.com/amoy) |

You can change the network name if you would like.

Switch to the newly added network.

<img src={require('./images/ai-marketplace-06.png').default} width="400" height="auto" border="1"/>
<br/>
<br/>

## Step 3. Connect the account

On the main Marketplace screen, press the **Enter Marketplace** button.

<img src={require('./images/ai-marketplace-07.png').default} width="800" height="auto" border="1"/>
<br/>
<br/>

Select **MetaMask**.

<img src={require('./images/ai-marketplace-08.png').default} width="400" height="auto" border="1"/>
<br/>
<br/>

In the MetaMask windows, select the account you want to connect and press **Next**. Then, **Confirm** the permissions and the signature request to log in.

<img src={require('./images/ai-marketplace-09.png').default} width="800" height="auto" border="1"/>
<br/>
<br/>

If you connect successfully, you will see your account address in the top-right corner of the main AI Marketplace screen.

<img src={require('./images/ai-marketplace-11.png').default} width="400" height="auto" border="1"/>
<br/>
<br/>

## Step 4. Set up personal storage

This step is optional—you can use Super Protocol as a storage provider. However, for additional control, set up and use your Storj account.

### Register and set up Storj

Register a [Storj](https://www.storj.io/) account if you do not have one yet. You can use either a free Trial or a Pro Storj account. However, with a Trial account, your files will become unavailable after the end of the trial period.

Create a new project and a bucket. Refer to the [Storj documentation](https://docs.storj.io/dcs/getting-started/quickstart-objectbrowser/).

Create two S3 Access Keys for this bucket. One should provide the **Write** and **Delete** permissions, and the other one the **Read** and **List** permissions. Alternatively, you can create a single key with **Full Access** (**All permissions**). Refer to the [Storj documentation](https://storj.dev/dcs/getting-started#generate-s3-compatible-credentials) to generate S3 credentials.

### Add to the Marketplace account

In AI Marketplace, click on your account address in the top-right corner and select **Account**.

<img src={require('./images/ai-marketplace-12.png').default} width="400" height="auto" border="1"/>
<br/>
<br/>

Select the **Your Storj account** radio button, fill in all the fields, and press **Save**.

| **Field** | **Value** |
| :- | :- |
| Bucket | Name of the Storj bucket |
| Prefix | Path to the directory in the bucket; it can be empty |
| WriteAccessKeyId | Access Key with the **Write** and **Delete** permissions |
| WriteSecretAccessKey | Secret Key with the **Write** and **Delete** permissions |
| ReadAccessKeyId | Access Key with the **Read** and **List** permissions |
| ReadSecretAccessKey | Secret Key with the **Read** and **List** permissions |

<img src={require('./images/ai-marketplace-13.png').default} width="400" height="auto" border="1"/>
<br/>
<br/>