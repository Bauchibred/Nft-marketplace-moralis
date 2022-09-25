
- [Full-Stack Setup](#full-stack-setup)
  - [1. Git clone the contracts repo](#1-git-clone-the-contracts-repo)
  - [2. Start your node](#2-start-your-node)
  - [3. Connect your codebase to your moralis server](#3-connect-your-codebase-to-your-moralis-server)
  - [4. Globally install the `moralis-admin-cli`](#4-globally-install-the-moralis-admin-cli)
  - [5. Setup your Moralis reverse proxy](#5-setup-your-moralis-reverse-proxy)
    - [IMPORTANT](#important)
  - [6. Setup your Cloud functions](#6-setup-your-cloud-functions)
  - [7. Add your event listeners](#7-add-your-event-listeners)
    - [You can do this programatically by running:](#you-can-do-this-programatically-by-running)
    - [Or, if you want to do it manually](#or-if-you-want-to-do-it-manually)
  - [8. Mint and List your NFT](#8-mint-and-list-your-nft)
  - [9. Start your front end](#9-start-your-front-end)
- [Minimal Quickstart](#minimal-quickstart)

# Full-Stack Setup

## 1. Git clone the contracts repo

In it's own terminal / command line, run: 

```
git clone https://github.com/Bauchibred/Nft-marketplace-moralis
cd hardhat-nextjs-nft-marketplace-fcc
yarn
```

## 2. Start your node 

After installing dependencies, start a node on it's own terminal with:

```
yarn hardhat node
```

## 3. Connect your codebase to your moralis server

Setup your event [moralis](https://moralis.io/). You'll need a new moralis server to get started. 

Sign up for a [free account here](https://moralis.io/).

Once setup, update / create your `.env` file. You can use `.env.example` as a boilerplate. 

```
NEXT_PUBLIC_APP_ID=XXXX
NEXT_PUBLIC_SERVER_URL=XXXX
moralisApiKey=XXX
moralisSubdomain=XXX
masterKey=XXX
chainId=31337
```

With the values from your account. 

Then, in your `./package.json` update the following lines:
```
"moralis:sync": "moralis-admin-cli connect-local-devchain --chain hardhat --moralisSubdomain XXX.usemoralis.com --frpcPath ./frp/frpc",
"moralis:cloud": "moralis-admin-cli watch-cloud-folder  --moralisSubdomain XXX.usemoralis.com --autoSave 1 --moralisCloudfolder ./cloudFunctions",
"moralis:logs": "moralis-admin-cli get-logs --moralisSubdomain XXX.usemoralis.com"
```

Replace the `XXX.usemoralis.com` with your subdomain, like `4444acatycat.usemoralis.com` and update the `moralis:sync` script's path to your instance of `frp` (downloaded as part of the Moralis "Devchain Proxy Server" instructions mentioned above)

## 4. Globally install the `moralis-admin-cli`

```
yarn global add moralis-admin-cli
```

## 5. Setup your Moralis reverse proxy 

> Optionally: On your server, click on "View Details" and then "Devchain Proxy Server" and follow the instructions. You'll want to use the `hardhat` connection. 

- Download the latest reverse proxy code from [FRP](https://github.com/fatedier/frp/releases) and add the binary to `./frp/frpc`. 
- Replace your content in `frpc.ini`, based on your devchain. You can find the information on the `DevChain Proxy Server` tab of your moralis server. 

In some Windows Versions, FRP could be blocked by firewall, just use a older release, for example frp_0.34.3_windows_386

Mac / Windows Troubleshooting: https://docs.moralis.io/faq#frpc

Once you've got all this, you can run: 

```
yarn moralis:sync
``` 

You'll know you've done it right when you can see a green `connected` button after hitting the refresh symbol next to `DISCONNECTED`. *You'll want to keep this connection running*.

<img src="./img/connected.png" width="200" alt="Connected to Moralis Reverse Proxy">

### IMPORTANT

Anytime you reset your hardhat node, you'll need to press the `RESET LOCAL CHAIN` button on your UI!

## 6. Setup your Cloud functions

In a separate terminal (you'll have a few up throughout these steps)

Run `yarn moralis:cloud` in one terminal, and run `yarn moralis:logs` in another. If you don't have `moralis-admin-cli` installed already, install it globally with `yarn global add moralis-admin-cli`.

> Note: You can stop these after running them once if your server is at max CPU capactity. 

If you hit the little down arrow in your server, then hit `Cloud Functions` you should see text in there. 

<img src="./img/down-arrow.png" width="500" alt="Cloud Functions Up">
<img src="./img/functions.png" width="250" alt="Cloud Functions Up">

Make sure you've run `yarn moralis:sync` from the previous step to connect your local Hardhat devchain with your Moralis instance. You'll need these 3 moralis commands running at the same time. 

## 7. Add your event listeners

### You can do this programatically by running:

```
node watchEvents.js
```

### Or, if you want to do it manually

Finally, go to `View Details` -> `Sync` and hit `Add New Sync` -> `Sync and Watch Contract Events`

Add all 3 events by adding it's information, like so: 

1. ItemListed:
   1. Description: ItemListed
   2. Sync_historical: True
   3. Topic: ItemListed(address,address,uint256,uint256)
   4. Abi: 
```
{
  "anonymous": false,
  "inputs": [
    {
      "indexed": true,
      "internalType": "address",
      "name": "seller",
      "type": "address"
    },
    {
      "indexed": true,
      "internalType": "address",
      "name": "nftAddress",
      "type": "address"
    },
    {
      "indexed": true,
      "internalType": "uint256",
      "name": "tokenId",
      "type": "uint256"
    },
    {
      "indexed": false,
      "internalType": "uint256",
      "name": "price",
      "type": "uint256"
    }
  ],
  "name": "ItemListed",
  "type": "event"
}
```
    5. Address: <YOUR_NFT_MARKETPLACE_DEPLOYED_ADDRESS_FROM_HARDHAT>
    6. TableName: ItemListed

You can add the canceled and bought events later. 

## 8. Mint and List your NFT

Back in the main directory, run:

```
yarn hardhat run scripts/mint-and-list-item.js --network localhost
```

And you'll now have an NFT listed on your marketplace. 

## 9. Start your front end

At this point, you'll have a few terminals running:

- Your Hardhat Node
- Your Hardhat Node syncing with your moralis server
- Your Cloud Functions syncing
- Your Moralis Logging

And you're about to have one more for your front end. 

```
yarn run dev
```

And you'll have your front end, indexing service running, and blockchain running.

# Minimal Quickstart

1. Clone the backend repo

```
git clone https://github.com/PatrickAlphaC/hardhat-nft-marketplace-fcc
cd hardhat-nextjs-nft-marketplace-fcc
yarn
yarn hardhat node
```
Leave that terminal running^

2. Clone the frontend

```
git clone https://github.com/PatrickAlphaC/nextjs-nft-marketplace-moralis-fcc
cd nextjs-nft-marketplace-moralis-fcc
yarn
```

Setup your `.env` with moralis info and update your `package.json` with moralis subdomain.

3. Sync your hardhat node with moralis

Update your `frpc.ini` file with what you see in the moralis UI.

Leave this terminal running: 

```
yarn moralis:sync
```

4. Watch for events && Update cloud functions

Run once:
```
yarn moralis:cloud
```

```
node watchEvents.js
```

5. Emit an event

Back in your hardhat project, run:

```
yarn hardhat run scripts/mint-and-list-item.js --network localhost
```

And you should see it updated in the database


Doing this so we can allow normal people to be able to interact with our code on our website

NEXTJS SETUP
yarn create next-app
remove the eslint

Then we go to pages remove everythign but the head tag

!. We want to make a home page
  -Home page shpws recently listed NFTs and if you own the NFTs we can update the listing if not we can buy
  2. Sell page
    -where we can sell our NFTS
    so we create our sell page

    Index page is our home page, so the first thing is our  connect button so our users can connect to our sit
    So we create our component folder and add a new file
    yarn add web3uikit moralis react-moralis, run in the command line not as dev dependencies.
    The moralis package is really important and even when we are not going to host on the moralis server all the hooks and tools from Moralis really helps

    so in app.js we 
    import {moralisprovider} from "react-moralis" 
    this is so we can use our web3uikit
    Then we wrap our whole component in a moralis provider
    We export our default function from headers where we just return our connect button
    AAnd then add it to our app.js just above our component
    In our headers now since we need a nav bar and other stuffs we return a lot of stuffs and then would have to put them in parentheses and to use links we import link, from next/Link
    Now we can create our links and then add them
    We do our formatting with tailwind on nextjs
    We can use the tailwind nextjs documentary to follow up
    we yarn add dev tailwind and then run tailwindcss init-p
    Then we can add our stylings for our nav and div in thr headers
  then lastly we remove the head from our index page snd add it to our header, that way no matter what page we are on we are always going to have the head information and there's no need to add it to every page.
    INDEXING IN WEB3
How do we get our most recent listings to show on our home page, we can't loop through the mapping of addresses cause that's a whole large number of data one can't go through and it can't be an array too cause that's going to be too gas expensive, so this is one of the reason why emitting events are very important and off chain services can access these events, so the best thing to do is actually create an offline server that's also going to keep an ear out to listen to these events and then we add them to a database, and this is not necessarily centralised using the graph oes it in a decentralised way but moralis those it in a centralised way but is much more faster, but to keep in mind that most part of our contracts is still in a decentralised setting in the sense that our smart contracts and all that and some centralised servers are realy important in the spac

MORALIS
Moralis also comes optionally with a server back in to help us with our development
Moralis is really helpful in the sense that it's a single workflow, and it's becoming one of the most adopted tech in the web3space

Connecting Moralis to our Local Hardhat Node so we create a server on moralis, after doing that we can then go on the moralis documentation to see the infos we need so for example we can search smart contract events to see what comes up

so now we want to use the moralis providers so instead of initialisingmount on false we change it we are going to give the moralisprovider our app id and server url as that way it get a direect acces to our server thst has been created on moralis, so we just get the info from our moralis database, grab our app id and the server url to add to our moralis provider and we should add it as environment variables
And in our env file we have to prefix our variables with NEXT_PUBLIC so that nextJs can know that this is a variable that's needed to be integrated in our nextAPP so then we stick them in to our moralis provider so now
We connect it to our contracts and then inform it on which events to listen out for and all that
So we now then run our backend code locally and then use the Devchain proxy server from moralis as how we connect moralis to our code that's been produced locally on hardhat, but first we have to download a reverse proxy
After downloading the reverse proxy, all we need is the frpc and frpc.ini so we can just import them to a folder in our project
So then we can go to the more opions on our moralis to get the hardhat cofig to use and add it to our frpc_ini file.
Then we run the command to set it uo in a different terminal, remember that i use a mac and we can run linux commands on mac so in the new terminkal we just cd into 
Then we run the linux command 
./frpc -c frpc.ini
There's another way to do this
This is using the moralis admin CLI
we run yarn global add moralis-admin-cli
and now if we run moralis-admin-cli
we'd have a lot of functions we can chose from
so we can just add a new command and script to our package.json file, so we can easily access it from our command line
so now we can then add our cli api to our env file so we don't need to add it ourselves to on the terminal, also we need to add the moralisapisecret

Moralis Event Sync
The first way to tell our moralis to listen for events is by using the UI and adding a sync, so while adding the new sync we chose the option where we watch contracts and listen to events
And then manually add everything, or wqe can do everything programmatically, 

Now we do everything programmatically but first we have to conect our moralis through sdk, i.e not just therough react 
so we use sdk and to do this we need to import moralis/node and our dotenv file check addEvents.js
And asides that we need to get the contract address our hardhat was deployed to, so to do that we are going to need to create a deploy script from our backend which is the Nft market place itself and then update to our front end.
We need to then read the fiile onto our front end code so for that we create our networkMapping.json under our constants file this file just keeps track of all of our deployment
then under our update front end from the backend we attach this json file that keeps track of our updates in relation to the backend file  then we also get the chain id  
so we update the contract address of the mapped file and then we need to write it back to our front end by using the line below
    fs.writeFileSync(frontEndContractsFile, JSON.stringify(contractAddresses))

    so we now have our network mapping file and then we add it in to our addEvents.js
    Then we create a new async function, but for this we need to get our server url and app id

    We are going to be using the Add new Event sync from code using moralis
        sync_historical: true, this allows us to get the history of all the events listed on our blockchain, 
        the topic is the name of the event and the type of our params
thrn the abi for just the event, so we just go to our nftmarketplace.json and get the abi for that particular event, so all we have to do is search the name of the event and then copy from where the name is at up until the anonymouse false
Then the tableName which is going to be the name of the table that gets updated on our moralis server so we do same for all events
So now we've indexed these events and whenever moralis hears anything about the listed events it attaches them to the server8
so now to send them up to our server we use
    const listedResponse = await Moralis.Cloud.run("watchContractEvent", itemListedOptions, {
        useMasterKey: true,
    })
for our item listing event and then do the same thing for the rest of the events
The moralis.cloud.com returns our response

Then we run node addEvents.js
Back in our backend there is a mint and list scripts, so we can list an Nft and see if our moralis is going to hear that

Event sync status on our moralis dashboard is how our server knows that it's supposed to be listening for events/mint-and
Some troubleshooting:
-Resetting the local chain
if we stop our hardhat node we need to reset the local chain on moralis so moralis knows that something has been done and to start listening for events again
Moralis Cloud Functions
This allows us to add anything we want our front end to do on our front end, so we can just add the long quote for calling moralis cloud to our package.json and from on there we can just run the short code to get our cloud updated  so we create an updateActiveitems.js

Updating our active items list
So now we need to create a cloud function and we call this whenever we want to update, and we use the aftersave keyword and it takes 2 parameters, what table we want to run something on and also the function we want to run
And we also create a logger and use logger so we can see it on our moralis framework, also since we are running this for now on our local network we can move blocks, so advisably we create a move block utils which we use to manually mine nodes adn moralis Knows that we've done this and moralis catches up
Remember that this is added to our backend solidity code, and under this we create an async function with 2 parameters of amount and sleepamount, so sleepAmount is just how long we want our blockchain to wait before mining so it resembles a real chain
We make a raw call using the method evm mine, and also use some conditional statements to mine our blocks

function sleep(timeInMs) {
    return new Promise((resolve) => setTimeout(resolve, timeInMs))
}

this is the function that's been used when we want to sleep so we just return a promise
After creating it we add it to our mint and list and we also import network from ethers
And then add to the mint and list an if conditional statement regarding if we are on a local development chain or not, so if we are on this we move bll
Then now back to our updateActiveItem
we use our logger to find the active item, so if it exists we get it if not, we create it
So the event saved come with all the parameters of our events 
so we get the properties we want and set them to what our aftersave provides us with
we can use logger info to add some things to our logger
so now if we call mint and list, it shows in our databases, cause any time an item listed event happens our active item gets updated
Even after restting our blockchain, we are still going to have entries from the other time the block was running, so best practise is to delete the rows from our databses
 
 Cloud Functions ||
Now we update our cloud functions, so thar anytime an object is both it's been removed from the list of our active items and to do this we use another afterSave trigger for  ItemCanceled, pretty similar with what we have for Itemlisted
So firstly we are going to query our activeitem to search for the item that's the same with the request that wants to be canceled
So we query all the properties to make sure it's the same object we have 
we use this to log our info
logger.info(`Marketplace | Query: ${query}`)
then we have this run so we can cehk for the first item that matches our description
  logger.info(`Marketplace | CanceledItem: ${JSON.stringify(canceledItem)}`)
  Then we get logger.info marketplace type cancel item then we do so string intropulation and then we print out this canceled item
  here if the query does not find anything it returns undefined but then if cacnel item is true
  after that we can then run yarn moralis:cloud to update our database
  and now if we run our cancel-item.js scripts on our backend we now would see it on our moralis database that it gets deleted 

  And then on our updateActiveItem we add our aftersave for itemBought, that's almost same as that fo itemCanceled
   else {
            logger.info(
                `No item bought with address: ${request.object.get(
                    "address"
                )} and tokenId: ${request.object.get("tokenId")} found`
            )
        }
This is incase we don't find it, we use this 
Then on our backend we create a buy-item scripts and run this and from on here if we check our activeItem on our moralis database we can see that the bought item is no longer on the table, cause of the afterSave we added to our cloud functions 
But do not forget that in our Nftmarketplace.sol we have a function to update listing and cause of that on our updateActiveItem under the afterSave for itemListed we have to check first to see if Nft exists or is already listed and from there, and to do that we use our query, so if the item has already been listed we delete it, and then resave it with our new price

Querying the moralis database
You might wonder how do we show the recetly listed NFTs?
Here we useMoralisQuery
this query is heavily inspired by react-query, so this is back in our index page
and this returns data, error, isLoading and all that
    const { data: listedNfts, isFetching: fetchingListedNfts } = useMoralisQuery(
Here we are setting the data name to lisredNfts and also checking if the data is fetching and under here we indicate the name of the table and also the function of the equery
so the ActiveItem is the table we need to get
        "ActiveItem",
        (query) => query.limit(10).descending("tokenId")
    )
We use this to get our ActiveItem table and then get the first ten token ids, as these are the tokenIds of the Nfts we are going to show our users on the front end
Then we check  if web#isEnabled, if it is we also check if we are fetchingListedNfts, if we are, if we are not we then map or listedNfts and then log our Nft attributes on our terminal which we chose all the properties from the attributes we would like then we return this in a tag to our front end, and if we refresh our front end now we get the list of items that have already been listed and, but they would be without the images and for us to, ao for us to show the image we have to create a new component where we indicate everythign we would like to show on our front end as our Nft, so here we have it pretty similar to what we've been doing befor with our exports, on which we would just indicate the properties we would like to export
Here we import usestate from react to help with our imageUri
Also we import useWeb3Contract from react-moralis in order for us to use the image tag from the imageURI to get the image
    const { runContractFunction: getTokenURI } = useWeb3Contract({
        abi: nftAbi,
        contractAddress: nftAddress,
        functionName: "tokenURI",
        params: {
            tokenId: tokenId,
        },
    })
And then we use this to get the tokenUri but we also have to add abi to our updatefrontend in our backend code for both our nft and the nftmarketplace, and we pass in the location since it's going to override the file whenever we call it, then we also add the updateAbi to our module.exports, so then if we run the updateFrontEnd scripts and go to our next app we can nnow see 2 new files Nftmarketplace.json and also BasicNft.json, ad then we can import them into our NFTBox
Then we create our async function to updateUi and we only want to run this if web3isEnabled
Import NftBox back in our index
Now under our UpdateUI function
we use an ipfs gateway so we can get our image URI from https istead of ipfs as not all our users would have browserrs that are ipfs compatible
then we replace it using the below line
const requestURL = tokenURI.replace("ipfs://", "https://ipfs.io/ipfs/")
  const tokenURIResponse = await (await fetch(requestURL)).json()
so fetch is like a keyword for us to get
then we set the imageUri to the image property that we get from the ipfs url and replace it if there's need for that and also set the properties that need to be set like the image name uri and description
There are also othe rways we cou;d do this indicated in our code,
then below that we return under which we check if we have our imageuri or not
If imageUri exists
Now to show the images we can just use the next/image component that comes with nextjs, but since we are using this we can no longer deploy this to a static site like ipfs, technically it shouldn't even be possible since we are using moralis which is a server
 After imprting our image from next/image 
    <Image
 loader={() => imageURI}
 src={imageURI}
 height="200"
 width="200"
/>
then we use this to get the images on our front end, then we can now use the web3Uikit to add ome sort of styling to this and we can use the card from web3uikit and then we wrap our image in a card, and then we can label it, we use usestate  to get the name and description and then add this to our card, then we add some divs to indicate who owns this and also the price, but we also import ethers so we can parse eth and then have our price in eth and not wei, then we jsut style it and then we have the UIs show them beautifully, and then we are done with showing the most recently NFTs.
so we only want to show this if web3 is enabled so we update that on our index page, we get isweb3enabled from use moralis and then we get usemoralis from react-moralis so now if we are connected we see the marketplace if not, we get t he info taht web3 is currently not enabled
Then we check now if Nft is owned by the conected account
    const isOwnedByUser = seller === account || seller === undefined 
So here we are checking if the seller is same as the account that's connected that means that there is no seller but if not then we show who the seller is
const formattedSellerAddress = isOwnedByUser ? "you" : truncateStr(seller || "", 15)
we use the truncatestr function just to reduce how long our string of the adress 
Update Listing Modal
Now if it's owned by us, we wamt to able to be able to update it and to do this we need to create a new component which is going to be updatelistingmodal.js
and modal is like a pop-up.

so we pass the params so the modal can know what function to call
we import our modal and input from web3uikit
then we add a label, name and a new type, and then we can add it to our front end, so now whenever we own an nft we get the pop up if we would like to update the listing or not

now we can add an onclick function, to show incase it's owned by the user we have our modal pop up to update, if not it doesn't pop up. 
const handleCardClick = () => {
        isOwnedByUser
            ? setShowModal(true)
            : buyItem({
                  onError: (error) => console.log(error),
                  onSuccess: handleBuyItemSuccess,
              })
    }
so we also get the set and show modal from usestate(false) and by default we want it as false so it only shows if the user owns the nft
Then under the input in we add an onchange, so we want to keep track of the changes that occur

    const [priceToUpdateListingWith, setPriceToUpdateListingWith] = useState(0)

so now whatever is in our event.target.price gets updated as new price, then we can create a different action for when ok is clicked
which is going to be under the modal
onOk={() => {
                updateListing({
                    onError: (error) => {
                        console.log(error)
                    },
                    onSuccess: handleUpdateListingSuccess,
                })
            }}
but to do this we have to useWeb3Contract, which we get from web3Moralis and then import our nftmarketplace addresss, so now 
const { runContractFunction: updateListing } = useWeb3Contract({
        abi: nftMarketplaceAbi,
        contractAddress: marketplaceAddress,
        functionName: "updateListing",
        params: {
            nftAddress: nftAddress,
            tokenId: tokenId,
            newPrice: ethers.utils.parseEther(priceToUpdateListingWith || "0"),
        },
    })
we then use this to set the new price, parse ether and 
So now if we were to get our modal and close it, when we click the cards nothing pips up and that is cause our use modal is still true and to change this now we have to create an onClose which is going to have a hideMOdal
and hidemodal is going to be a function that just changes setmodal to false and then we add it to our onClose
And then we add the error to our onOk and and success to when it's successsful
then we get useNotification from web3uikit and import it also to our app.js
then under our handleListingSuccess function we add await cause it's an async and we want to be sure the tx has gone through and now we get the notification if our tx goes through

we can create a mine script so we can use to move blocks

Buying the NFT
we add the runContract for our buyItem on the NFTbox file
and the hadleonSuccess just like we had before and that's it for our home page, we can now work on our sell page

SELL PAGE
We can grab forms from the web3uikit which we do
Then we can check what parameters we want to add for our forms 
then we can now add the objects properties like name of the form and also what type of form it is the inputWidth, the value is an empty string,  and key, and add the titlew we want
And then we create a new function that we attach to onSubmit which is approveAndList
This function takes a data as param, we pass in all the details we want to get the function execute and then we also get our nftAbi, we get our chainId using usemoralis()
Then we get our nftmarketplace from the networkMapping
So now once our runcontract function gets successful we call handleApproveSuccess function which is an async function
After creating all our functions and making use of our imports we can now go ahead and create a mint file on our backend, we are not going to list it programmatically but rather use the ui from our sell page to do it
after minting, so we should also add the address in the mint script in the sense that we see it on our terminal after it's been listed, we can now take the address and the token id to add using our sell-page
And then we can now add the withdrawa aspect.  
And that's it for using the moralis server
