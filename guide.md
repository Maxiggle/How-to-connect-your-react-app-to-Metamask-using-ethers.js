<h1>How to Connect Your Dapp With Metamask Using Ethers.js</h1>

<h3>OverView</h3>

In our dApp, we will have a simple react user interface that has a material button asking the user to connect to metamask and if  they do not have an account,  they can create one or better still login to their account and they will be able to view their wallet balance and address displayed on the UI.


<h3>Prerequisites</h3>

- Basic knowledge of React.js
- Basic knowledge of Ethereum and Blockchain
- NPM
- MetaMask browser extension
- Ethers.js library

<h3>What are dApps</h3>

A dApp is an application that is built on a decentralized network. It has a user interface and a [smart contract](https://ethereum.org/en/developers/docs/smart-contracts/). Also, it can run on a peer-to-peer network.

dApps run on blockchain which are open-source, decentralized environments which are free from the control of any single superior that is, a developer can create a Facebook-like app and make it run on the Ethereum blockchain and when any user makes a post, it cannot be deleted by anybody, user data is not fully managed as well by one person on decentralized apps

A dApp can have a frontend that is built using any language. It can run on decentralized servers like [IPFS](https://ipfs.io/) or [Swarm](https://www.ethswarm.org/).


<h3>What is MetaMask</h3>

[MetaMask](https://metamask.io/) is a popular browser extension that serves as a [cryptocurrency wallet](https://www.bankrate.com/glossary/c/cryptocurrency-wallet/) that connects to Ethereum blockchain. MetaMask is a secure Ethereum wallet that enables users to interact with Ethereum's decentralized applications, it allows users to store Ether and other ERC-20 tokens, it enables users to spend on games, stake on gambling apps and trade them on exchanges. MetaMask do not only store ERC-20 tokens but can also store ERC-721 tokens as well.
In this article, we will be connecting our dApp with MetaMask so our app can connect with the Ethereum blockchain.

<h3>Install MetaMask</h3>

To complete this tutorial, we will need to install the MetaMask extension in our browser.
To add MetaMask to your browser for Chrome, follow this [link](https://chrome.google.com/webstore/detail/metamask/nkbihfbeogaeaoehlefnkodbefgpgknn?hl=en) but if you are using a different browser from Chrome, you can search for the extension for the browser you are using.


<h3>Why Ethers.js</h3>

Ethers.js is a light weight javascript library that allows developers to connect and interact with the Ethereum blockchain. The library includes utility functions in Javascript and Typescript, and has all the capabilities of an Ethereum wallet and it is mostly used for client side applications.
Ethers.js makes it easier to connect with the Ethereum blockchain with very less amount of code as compared to web3.js.
One major difference between web3 and ethers.js is that web3 apps typically have a local node that is responsible for storing keys and signing transactions. However, in reality most users do not run [geth(Go Ethereum)](https://geth.ethereum.org/) locally, MetaMask emulates that environment through a browser application, and so most web3 apps require MetaMask to hold keys, sign transactions, and interact with the Ethereum mainnet network.
Furthermore, Ethers.js does have a poles apart approach which is believed to give users more flexibility plus it has very good and extensive documentation and is less in size( 284kb uncompressed and 88kb when compressed.

The above explanations are well organized and explained to help you get familiar with the tools we will be using in this tutorial so you can understand why we are using them and for what purpose they are used for.


<h3>Build dApp frontend using React</h3>

With this application, we will explore the basic concepts of the react framework and the blockchain as we will create paths to communicate with Ethereum blockchain, query it and fetch data to render in our dApp. 
At the end of this tutorial, we should get this result

![](https://paper-attachments.dropbox.com/s_3715D6DB6DF07792306B1607ED50F65FCAB5BBC2AE5040B6D4BEDAEA71D8C685_1638431614853_wellTouched.png)


Run the command `npx create-react-app ethersjs_meta` to create a new react app.

Next run the command `npm install` `--``save ethers` to install the Ethers.js library and every dependency we will be needing.

Next, delete the dummy code in `App.js` file, copy-paste the code below

    function App() {
      return (
        <div className="App-header">
        <div className="centerCard">
        <div className = "card">
        <div className="App">
          <WalletCard/>
        </div>
        </div>
        </div>
        </div>
      );
    }
    export default App;

Next, we want to create a new file called `WalletCard.js` in our `src` folder. `WalletCard.js` will contain all the the UI for our project and the logic for it.


    import React, { useState } from 'react';
    import { Button } from '@material-ui/core';
    import Ethereum from './Ethereum.png'
    import { ethers } from 'ethers';
    const provider = new ethers.providers.Web3Provider(window.Ethereum)
    const WalletCard = () => {
        const [errorMessage, setErrorMessage] = useState(null);
        const [defaultAccount, setDefaultAccount] = useState(null);
        const [userBalance, setUserBalance] = useState(null);
        const connectwalletHandler = () => {
            if (window.Ethereum) {
                provider.send("eth_requestAccounts", []).then(async () => {
                    await accountChangedHandler(provider.getSigner());
                })
            } else {
                setErrorMessage("Please Install Metamask!!!");
            }
        }
        const accountChangedHandler = async (newAccount) => {
            const address = await newAccount.getAddress();
            setDefaultAccount(address);
            const balance = await newAccount.getBalance()
            setUserBalance(ethers.utils.formatEther(balance));
            await getuserBalance(address)
        }
        const getuserBalance = async (address) => {
            const balance = await provider.getBalance(address, "latest")
        }
        return (
            <div className="WalletCard">
                <img src={Ethereum} className="App-logo" alt="logo" />
                <h3 className="h4">
                    Welcome to a decentralized Application
                </h3>
                <Button
                    style={{ background: defaultAccount ? "#A5CC82" : "white" }}
                    onClick={connectwalletHandler}>
                    {defaultAccount ? "Connected!!" : "Connect"}
                </Button>
                <div className="displayAccount">
                    <h4 className="walletAddress">Address:{defaultAccount}</h4>
                    <div className="balanceDisplay">
                        <h3>
                            Wallet Amount: {userBalance}
                        </h3>
                    </div>
                </div>
                {errorMessage}
            </div>
        )
    }
    export default WalletCard;

In the code above, we created a constant variable called `provider` and we assigned it to `new ethers.providers.Web3Provider(window.ethereum)` which is a read-only abstraction for accessing blockchain data. 
We can access the whole Ethereum Api using `window.ethereum` and that is precisely the reason why we passed it as a parameter to Ethers web3 provider.

Inside the WalletCard component, we created three states to mutate the app when there is an error, on the account display and the user balance.


      const [errorMessage, setErrorMessage] = useState(null);
        const [defaultAccount, setDefaultAccount] = useState(null);
        const [userBalance, setUserBalance] = useState(null);

The variable `errorMessage` is what takes the state setter `setErrorMessage` as it is the only way to mutate the app state, there are other ways of doing it but the code above is less complex to use.
This method is called [state hooks](https://reactjs.org/docs/hooks-overview.html) in React. We are setting states for three(3) events to occur in our app.

<h3>Create a connection with MetaMask</h3>

Next, we will create a function called `connectwalletHandler` that will contain a send request to ask for permission to connect to MetaMask. The function includes the following


     const connectwalletHandler = () => {
            if (window.ethereum) {
                provider.send("eth_requestAccounts", []).then(async () => {
                    await accountChangedHandler(provider.getSigner());
                })
            } else {
                setErrorMessage("Please Install MetaMask!!!");
            }
        }

In the code above, we did a little check to assert that the user has MetaMask installed, if the user doesn’t, have it installed, then it should ask the user to install MetaMask and if it is installed, it should connect to MetaMask.

Build a connect Button

We will create a Text button with connect as a button hint and we will call this function in the `onClick` function of the button. When this button is clicked, we get we get connected to MetaMask.

      <Button
                    style={{ background: defaultAccount ? "#A5CC82" : "white" }}
                    onClick={connectwalletHandler}>
                    {defaultAccount ? "Connected!!" : "Connect"}
                </Button>

The defaultAccount displays the wallet address and we use the wallet address to show `connected` when the the address is available else it should just show `connect`.

It should look like this when connected


![](https://paper-attachments.dropbox.com/s_3715D6DB6DF07792306B1607ED50F65FCAB5BBC2AE5040B6D4BEDAEA71D8C685_1638441581212_connected+1.png)


<h3>Login MetaMask</h3>

After getting connected to MetaMask, we are prompted to login our account, login the account to access your account

![](https://paper-attachments.dropbox.com/s_3715D6DB6DF07792306B1607ED50F65FCAB5BBC2AE5040B6D4BEDAEA71D8C685_1638448272369_metamask.png)


<h3>Display User Wallet Address</h3>

To handle the wallet address of the account and balance we signed up for or any other newly created account, we created an async function that awaits the the `provider.getSigner()` function passed as a parameter to the `accountChnagedHandler` function. The `accountChangedHandler`  contains the following:


     const accountChangedHandler = async (newAccount) => {
            const address = await newAccount.getAddress();
            setDefaultAccount(address);
        }
    


Inside this function from the code above, we pass a new parameter `newAccount` which is assigned to `provider.signer()` , so we can access the `getAddress()` function and pass the address to our state variable using the account setter `setDefaultAccount(address)`.
To display the Wallet Address, we will call the defaultAccount variable.


     <h4 className="walletAddress">Address:{defaultAccount}</h4>


![Wallet Address](https://paper-attachments.dropbox.com/s_3715D6DB6DF07792306B1607ED50F65FCAB5BBC2AE5040B6D4BEDAEA71D8C685_1638443924173_walletAddress.png)


<h3>Display User Wallet Address</h3> 

This section will  show us how to get the user balance from the blockchain.


     const accountChangedHandler = async (newAccount) => {
            const address = await newAccount.getAddress();
            setDefaultAccount(address);
            const balance = await newAccount.getBalance()
            setUserBalance(ethers.utils.formatEther(balance));
            await getuserBalance(address)
        }

Based on the above code, we can access the balance of the wallet using the `provider.signer()` passed as `newAccount` , using the `getBalance()` function but this would return a hexadecimal value and that would be very large and unreadable, so we have to covert it from Wei to ether ( Wei is the smallest denomination of ether). To do this, we will use the do the following 


    setUserBalance(ethers.utils.formatEther(balance));

Next, we will pass the address into the `getuserBalance()` to be specific as to what wallet we will be needing its balance.


    const getuserBalance = async (address) => {
            const balance = await provider.getBalance(address, "latest")
        }

Inside the `getuserBalance()` function, we will pass the address to the `provider.getBalance()` that takes the address and blockTag as parameters. To render it in our UI, use the code below


     <div className="balanceDisplay">
                        <h3>
                            Wallet Amount: {userBalance}
                        </h3>

<h3>Conclusion</h3>

In this tutorial, we demonstrated how to build a decentralized application using the Ethers.js library and react framework.

You can also extend the blockchain concept to build your own DeFi project where you can create a wallet that allows you to send and receive Ethereum or other tokens on the Ethereum blockchain.

You can take a look at the [Ethers.js library](https://docs.ethers.io/v5/) to see how to use other functions of the library to build even more advanced projects on the blockchain.


Resources
- Github

