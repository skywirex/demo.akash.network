---
title: Static Site Deployment on Akash Network
description: Example of Static Site Deployment on Akash Network
date: 2021-06-28
categories:
  - "Development"
tags:
  - "Akash"
  - "Hugo"
  - "AKT"
---

### I. Introduction

[Akash Network](http://akash.network/) has been introduced as “The world’s first decentralized and open-source cloud, accelerates deployment, scale, efficiency, and price performance for high-growth industries like blockchain and machine learning/AI. Known as the “Airbnb for Cloud,” Akash Network provides a fast, efficient, and low-cost application deployment.”


[Hugo](https://gohugo.io/) is a static site generator written in Go. Hugo takes a directory with content and templates and renders them into a full HTML website. [A hundred Hugo themes](https://themes.gohugo.io/) are provided free to help you start more easily. You only need to focus on writing the content.

You can host your website on Akash network very tiny amount. At the time of writing the payment of Akash network can be done only with uAKT (1ukat = 10^-6AKT), but soon the network will support the payment in the other currency as stated in the white paper. You can see more detail about the current price in $USD monthly [here](Https://www.akashlytics.com/price-compare).

Due to the long story, this article mainly focuses on section `IV. Deploy your website on Akash network`

### II. Prerequisites

Basic knowledge of:

- Command Line

- [Git](https://git-scm.com/)

- [Docker](https://www.docker.com/)

with reference to the following documents:

- [Akash documents](https://docs.akash.network/)

### II. Create your static site using Hugo

You will use Github to store your content and templates. Therefore, to start you should create your repository there.

**Step 1: Create a new repository and clone your repository to create a local copy on your computer**

<center>![create-github-repo](https://user-images.githubusercontent.com/9553811/123507260-a95dd400-d692-11eb-931b-6fef788383a4.jpg)</center>

In this case, Public repo is chosen. You can choose Public or Private repo depending on your purpose.

Clone your repository to create a local copy on your computer to build a static site using Hugo.

```
$ git clone https://github.com/skywirex/demo.akash.network.git
```

**Step 2: Install Hugo and generate new site**

You can install Hugo based on their [github guide](https://github.com/gohugoio/hugo) and generate a new site on you local computer.

```
$ hugo new site demo-akash
```

After executing the command above you will see the structure of your new website look like this:

<center>![hugo-folder-structure](https://user-images.githubusercontent.com/9553811/123507266-ab279780-d692-11eb-8888-238824c55477.jpg)</center>

**Step 3: Build your site locally**

- Move all generated files and folders inside `demo-akash` folder to `demo.akash.network`

- Choose a [Hugo theme](https://themes.gohugo.io/) for your static site

- Download and unzip in the theme folder. Rename it for configuration more easily. In this case, I choose [`hugo-dpsg`](https://github.com/pfadfinder-konstanz/hugo-dpsg) theme. After that you can copy all contents in `themes\hugo-dpsg\exampleSite` and overwrite the contents in the `demo.akash.network` folder.

<center>![download-hugo-theme](https://user-images.githubusercontent.com/9553811/123507265-aa8f0100-d692-11eb-8929-863999a75327.jpg)</center>

- Open the `.gitignore` file and delete the character `themes` 

- Open `config.toml` and modify the content based on your information. Run hugo site locally using command

```
$ hugo server
```    

Open your browser with address the `localhost:1313`. After satisfied with the results, you commit the first version of website content to your Github repo.

**Step 4: Push your site to Github**

- Run the following command

```
$ git add .
$ git commit -m "1st version of demo static site on akash network"
$ git push origin
$ git push origin main
```

- Check your files on Github repo. You can see example at my [github repo](https://github.com/skywirex/demo.akash.network)

### III. Package your website automatically using Docker Hub

To package your website to Docker image, you need to add [`Dockerfile`](https://github.com/skywirex/demo.akash.network/blob/main/Dockerfile) to the root folder or your site and push it to Github repo.

Login to Dockerhub and create new repo.

<center>![docker-hub-create-repo](https://user-images.githubusercontent.com/9553811/123507263-a9f66a80-d692-11eb-8110-bf63327bb642.jpg)</center>

Chose build image from Github source code as shown in the below image

<center>![build-image-from-github-source](https://user-images.githubusercontent.com/9553811/123507258-a6fb7a00-d692-11eb-9e1e-a67c00571883.jpg)</center>

Make sure that you choose the right branch for building

<center>![save-and-build](https://user-images.githubusercontent.com/9553811/123507267-abc02e00-d692-11eb-8d64-e41ef000de3a.jpg)</center>

If everything works correctly, you will see your website image in Dockerhub

<center>![docker-automated-build](https://user-images.githubusercontent.com/9553811/123507262-a9f66a80-d692-11eb-9581-6af141c0f5bf.jpg)</center>

Ensure your docker image work as expected by running a container on localhost to check 

```
# docker run --rm --name demo-akash -d -p 8080:80 skywirex/demo.akash.network:latest
```

Open your browser and type `<IP>:8080` to check your website.


### IV. Deloy your website on Akash network

The most important and interesting part is here.

##### STEP 1: Buy $AKT

You can buy `$AKT` directly on the [supported exchanges here](https://www.coingecko.com/en/coins/akash-network#markets) or buy `$ATOM` and swap to `$AKT` on [OSMO DEX](https://app.osmosis.zone/)

##### STEP 2: Install Akash tool

Akash is built using Go and requires Go version 1.16+. You can install Go using [this guide.](https://golang.org/doc/install)

To verify that Go is installed:

```
# go version
```

There are many ways to install the Akash tool. The easiest way is to download and install it from a binary file. In this example, we will be installing the Akash tool on an `arm64` single-board computer (SBC) like Orange Pi3. You can head over [Akash github](https://github.com/ovrclk/akash) for more information.

```
# wget https://github.com/ovrclk/akash/releases/download/v0.12.1/akash_0.12.1_linux_arm64.deb
# dpkg -i akash_0.12.1_linux_arm64.deb
```

Confirm `akash` installation:

```
# akash version
v0.12.1
```

Set enviroment variables

```
cat <<'EOF' >>$HOME/.profile
export AKASH_NET="https://raw.githubusercontent.com/ovrclk/net/master/mainnet"
export AKASH_VERSION="$(curl -s "$AKASH_NET/version.txt")"
export AKASH_CHAIN_ID="$(curl -s "$AKASH_NET/chain-id.txt")"
export AKASH_NODE="$(curl -s "$AKASH_NET/rpc-nodes.txt" | head -1)"
export AKASH_KEYRING_BACKEND="os"
export AKASH_KEY_NAME=skywirex ### you can change <skywirex> with your desired name
EOF
```

Load enviroment variables:

```
source $HOME/.profile
```

Verify your settings:

```
# echo $AKASH_CHAIN_ID $AKASH_NODE $AKASH_KEYRING_BACKEND
```

##### STEP 3: Create Akash wallet and deposit

From [Akash' guide](https://docs.akash.network/guides/wallet), you can setup new wallet using:

```
$ akash keys add "$AKASH_KEY_NAME" \
    --keyring-backend "$AKASH_KEYRING_BACKEND"
```

You'll see a response similar to below:

```
- name: skywirex
  type: local
  address: akash1he4e66pt83cx4vk4ecyr0vnwpg8k8jnlxtw2dh
  pubkey: akashpub1addwnpepq2emfht2frwzejy0vsse8k6g68573c57w4wdea0502wgk86c9en7603vuwe
  mnemonic: ""
  threshold: 0
  pubkeys: []


**Important** write this mnemonic phrase in a safe place.
It is the only way to recover your account if you ever forget your password.

over inform script account piece paper lion junk what pretty couch prevent cupboard captain cage cluster simple merit area cargo super mushroom mosquito grab
```

Get deposit address and set it to a variable.

```
# akash keys show "$AKASH_KEY_NAME" -a \
    --keyring-backend "$AKASH_KEYRING_BACKEND"

# AKASH_ACCOUNT_ADDRESS=akash1he4e66pt83cx4vk4ecyr0vnwpg8k8jnlxtw2dh
```

You have to fund your wallet at least 5 `$AKT` (for escrow) + a little amount that you will need for lease bidding. In my case, I deposited 6 `$AKT`.

Verify balance before making new deployment

```
# akash query bank balances "$AKASH_ACCOUNT_ADDRESS" \
  --node "$AKASH_NODE"
```

will output a response similar this:

```
balances:
- amount: "6000000"
  denom: uakt
pagination:
  next_key: null
  total: "0"
```

Note that the above balance indicated is denominated in uAKT, the account has a balance of 6 AKT. We’re now ready to deploy.

##### STEP 4: Prepare your deloy.yml and create certificate

You need a `deploy.yml` to deploy your website to Akash network. You can just copy and modify my [deploy.yml file](https://github.com/skywirex/demo.akash.network/blob/main/deploy.yml) or refer to [Akash guide](https://docs.akash.network/guides/deploy)

Just make sure, you update the docker image reference to your dockerhub image name.

Before you can create a deployment, a certificate must first be created. Your certificate needs to be created only once per account and can be used across all deployments.

```
# akash tx cert create client \
    --chain-id $AKASH_CHAIN_ID \
    --keyring-backend $AKASH_KEYRING_BACKEND \
    --from $AKASH_KEY_NAME \
    --node $AKASH_NODE \
    --fees 2000uakt
```

will output a response similar this:

```
{
  "body": {
    "messages": [
      {
        "@type": "/akash.cert.v1beta1.MsgCreateCertificate",
        "owner": "akash1he4e66pt83cx4vk4ecyr0vnwpg8k8jnlxtw2dh",
        "cert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUJ3RENDQVdXZ0F3SUJBZ0lJRm1pcUJWcWZDVmt3Q2dZSUtvWkl6ajBFQXdJd1NqRTFNRE1HQTFVRUF4TXMKWVd0aGMyZ3hkbTV6Tld0aE0zZzJPV1ZyYlRObFkzQTRiWGs0WkRWNlpuVTRhakl6Y0RWeFpYY3dkek14RVRBUApCZ1ZuZ1FVQ0JoTUdkakF1TUM0eE1CNFhEVEl4TURNd01qSXpNak15TmxvWERUSXlNRE13TWpJek1qTXlObG93ClNqRTFNRE1HQTFVRUF4TXNZV3RoYzJneGRtNXpOV3RoTTNnMk9XVnJiVE5sWTNBNGJYazRaRFY2Wm5VNGFqSXoKY0RWeFpYY3dkek14RVRBUEJnVm5nUVVDQmhNR2RqQXVNQzR4TUZrd0V3WUhLb1pJemowQ0FRWUlLb1pJemowRApBUWNEUWdBRUtaSTlmWGVPVzRCYXRwcU1mb1VTekx2b01lWGlpbEZTMnJhZlhKdUNObUlMVjJMaWhIZW5JdjJTCjV5Uzh1Zkh5QmNMSUI5aFE1VE81THRHSUpPdzIvYU0xTURNd0RnWURWUjBQQVFIL0JBUURBZ1F3TUJNR0ExVWQKSlFRTU1Bb0dDQ3NHQVFVRkJ3TUNNQXdHQTFVZEV3RUIvd1FDTUFBd0NnWUlLb1pJemowRUF3SURTUUF3UmdJaApBSjJzQ3ZodGNzWkRXUkQ2MU03ZkVCRUk5eEt5Z0UzRkd3K2tIYVhZYXl0TUFpRUE4cUZtb3FEc1Z0ZzhPSHc1Ck5iOEljd0hiNHVkc0RpTzRxaWhoL0owNWZKaz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=",
        "pubkey": "LS0tLS1CRUdJTiBFQyBQVUJMSUMgS0VZLS0tLS0KTUZrd0V3WUhLb1pJemowQ0FRWUlLb1pJemowREFRY0RRZ0FFS1pJOWZYZU9XNEJhdHBxTWZvVVN6THZvTWVYaQppbEZTMnJhZlhKdUNObUlMVjJMaWhIZW5JdjJTNXlTOHVmSHlCY0xJQjloUTVUTzVMdEdJSk93Mi9RPT0KLS0tLS1FTkQgRUMgUFVCTElDIEtFWS0tLS0tCg=="
      }
    ],
    "memo": "",
    "timeout_height": "0",
    "extension_options": [],
    "non_critical_extension_options": []
  },
  "auth_info": {
    "signer_infos": [],
    "fee": {
      "amount": [],
      "gas_limit": "200000",
      "payer": "",
      "granter": ""
    }
  },
  "signatures": []
}

confirm transaction before signing and broadcasting [y/N]:
```

Enter `y` to confirm.


##### STEP 5: Deploy your website

Deploy to Akash network:

```
# akash tx deployment create deploy.yml \
    --from $AKASH_KEY_NAME \
    --chain-id $AKASH_CHAIN_ID \
    --keyring-backend $AKASH_KEYRING_BACKEND \
    --node $AKASH_NODE \
    --fees 2000uakt -b sync -y
```

With option `-b sync`, a tx broadcast option, the response will return the transaction hash without waiting for confirmation. You can get transaction hash as soon as the command is executed.

if OK, your terminal will output a response similar this:

```
{"height":"0","txhash":"23ED8EC164013B199CAA0762DD0E97CCEA86524C588F2F4DFCE92F03E941510C","codespace":"","code":0,"data":"","raw_log":"[]","logs":[],"info":"","gas_wanted":"0","gas_used":"0","tx":null,"timestamp":""}
```

This command posts your deployment and triggers the Akash marketplace to try to match you with a provider via auction. This can take a few seconds. If it [fails](https://pastebin.com/0RDQZrf6), you should to close the deployment to get back your `$AKT` from the escrow before making the new one.

```
# akash tx deployment close \
    --node $AKASH_NODE \
    --chain-id $AKASH_CHAIN_ID \
    --dseq $AKASH_DSEQ \
    --owner $AKASH_ACCOUNT_ADDRESS \
    --from $AKASH_KEY_NAME \
    --keyring-backend $AKASH_KEYRING_BACKEND \
    -y --fees 2000uakt
```

Query the transaction:

```
# akash query tx 23ED8EC164013B199CAA0762DD0E97CCEA86524C588F2F4DFCE92F03E941510C \
    --node $AKASH_NODE
```

Copy DSEQ, OSEQ, GSEQ values from the response of the above command and set variables

```
AKASH_DSEQ=1496160
AKASH_OSEQ=1
AKASH_GSEQ=1
```

Query market bid list:

```
# akash query market bid list \
    --owner=$AKASH_ACCOUNT_ADDRESS \
    --node $AKASH_NODE \
    --dseq $AKASH_DSEQ
```

Copy provider address from responses above and set variable

```
AKASH_PROVIDER=akash1f6gmtjpx4r8qda9nxjwq26fp5mcjyqmaq5m6j7
```


You can combine environment variables as follow

```
cat <<'EOF' >>$HOME/.profile
export AKASH_DSEQ=1496160
export AKASH_OSEQ=1
export AKASH_GSEQ=1
export AKASH_PROVIDER=akash1f6gmtjpx4r8qda9nxjwq26fp5mcjyqmaq5m6j7
EOF
```

Load variables

```
source $HOME/.profile
```

Create transaction for your market lease

```
# akash tx market lease create \
    --chain-id $AKASH_CHAIN_ID \
    --node $AKASH_NODE \
    --owner $AKASH_ACCOUNT_ADDRESS \
    --dseq $AKASH_DSEQ \
    --gseq $AKASH_GSEQ \
    --oseq $AKASH_OSEQ \
    --provider $AKASH_PROVIDER \
    --from $AKASH_KEY_NAME --fees 2000uakt
```

Check the status of your lease:

```
# akash query market lease list \
    --owner $AKASH_ACCOUNT_ADDRESS \
    --node $AKASH_NODE \
    --state active
```

https://gist.github.com/magicstone1412/81ca13986158f8dd71ddb36f5f9e6868

To kick off image building on the provider machine, you need to upload manifest:

```
# akash provider send-manifest deploy.yml \
    --node $AKASH_NODE \
    --dseq $AKASH_DSEQ \
    --provider $AKASH_PROVIDER \
    --from $AKASH_KEY_NAME
```

If it is okay, you will get an empty response.

Check your lease status

```
# akash provider lease-status \
    --node $AKASH_NODE \
    --dseq $AKASH_DSEQ \
    --provider $AKASH_PROVIDER \
    --from $AKASH_KEY_NAME
```

will output a response similar this:

```
{
  "services": {
    "web": {
      "name": "web",
      "available": 1,
      "total": 1,
      "uris": [
        "prio8dt27hc9r9luc416ttu4g0.ingress.ewr1p0.mainnet.akashian.io"
      ],
      "observed_generation": 1,
      "replicas": 1,
      "updated_replicas": 1,
      "ready_replicas": 1,
      "available_replicas": 1
    }
  },
  "forwarded_ports": {}
}

```

Get `uris` above and config it with your domain


When you create a transaction for your market lease, you have only 5 minutes to accept a bid, otherwise, bids will close. Therefore, the additional commands below you may need to: 

  - Verify created deployment to make sure `state: open`

```
# akash query deployment get \
    --owner $AKASH_ACCOUNT_ADDRESS \
    --node $AKASH_NODE \
    --dseq $AKASH_DSEQ
```

  - View your order to make sure `state: open`

```
# akash query market order get \
    --node $AKASH_NODE \
    --owner $AKASH_ACCOUNT_ADDRESS \
    --dseq $AKASH_DSEQ \
    --oseq $AKASH_OSEQ \
    --gseq $AKASH_GSEQ
```

##### STEP 5: Update your deployment (if any)

You need to do 2 commands to update your deployment every time you want to publish a post to your website.

- First, update deployment

```
# akash tx deployment update deploy.yml \
    --dseq $AKASH_DSEQ \
    --from $AKASH_KEY_NAME \
    --chain-id $AKASH_CHAIN_ID \
    --node $AKASH_NODE \
    --fees=2000uakt
```

- Second, upload modified manifest. You shall modify it to change the hash

```
# akash provider send-manifest deploy.yml \
    --node $AKASH_NODE \
    --dseq $AKASH_DSEQ \
    --provider $AKASH_PROVIDER \
    --from $AKASH_KEY_NAME
```

You need to add funds to the escrow account associated with your deployment to keep it running, avoid interruption.

View escrow balance

```
akash query deployment get \
    --node $AKASH_NODE \
    --dseq $AKASH_DSEQ \
    --owner $AKASH_ACCOUNT_ADDRESS \
    -o text
```

Deposit to the escrow address

```
# akash tx deployment deposit 10000uakt \
    --from $AKASH_KEY_NAME \
    --chain-id $AKASH_CHAIN_ID \
    --node $AKASH_NODE \
    --dseq $AKASH_DSEQ \
    --fees=2000uakt
```

### V. Custom domain configuration

After getting your uris, you can configure it with your own domain and Cloudflare based on [this guide.](https://teeyeeyang.medium.com/how-to-use-a-custom-domain-with-your-akash-deployment-5916585734a2)

If you want to use your own domain name instead of the default Akash link `(*.ingress.ewr1p0.mainnet.akashian.io)` you have to insert your domain name in [depoy.yml](https://github.com/skywirex/demo.akash.network/blob/main/deploy.yml). Otherwise you have to delete.

Moreover, the [Handshake domain](https://handshake.org/) also work well according to Akash's CEO blog at address . Very exciting!

### VI. Limitation and next steps

Now, every time I want to publish a post, I have to do `tx deployment update`  and `provider send-manifest` to update my post manually.

I have had a plan to publish my new blog post with only one command `$ git push origin main`. My website on Akash network will be updated automatically by using [Watchtower Docker image.](https://github.com/containrrr/watchtower). Unfortunately, Akash network did not support `-volume` parameter at the moment. Therefore, I am still thinking about the way to update my website automatically by using [Docker Hub Webhooks] (https://docs.docker.com/docker-hub/webhooks/). When Docker Hub has a new image, it will trigger an automated update to my deployment and send the modified manifest to the Akash provider. 

### VII. Conclusion

With the early stage of development, the Akash network team has created the amazing platform with a [significant lower cost](https://www.akashlytics.com/price-compare) than the giant big tech companies like Amazon, Google Cloud Platform, Azure. Holding the `$AKT` coin you will get the passive income in the future when the network growing bigger. With the current APY over 50%, you can buy some `$AKT`, stake them, and hosted your site without any hidden cost.

You have a big chance to accumulate the `$AKT` token and waiting for the fly to take off. Good luck and have fun!

<center>Buy Me a Coffee: **akash1yq8j7u0h4u4rxac6agsc0gkzznyp7vmgwklksl**</center>