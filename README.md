# hardhat
# Hardhat Tutorial: From Beginner to Master

## Table of Contents

1. [Introduction](#introduction)
2. [Installation and Setup](#installation-and-setup)
3. [Getting Started](#getting-started)
4. [Tutorials and Topics](#tutorials-and-topics)
    - [1. Writing Smart Contracts](#writing-smart-contracts)
    - [2. Testing](#testing)
    - [3. Deployments and Networks](#deployments-and-networks)
    - [4. Tasks and Plugins](#tasks-and-plugins)
    - [5. Debugging and Tracing](#debugging-and-tracing)
    - [6. Advanced Topics](#advanced-topics)
5. [Usage](#usage)
6. [Contributing](#contributing)
7. [License](#license)
8. [Acknowledgments](#acknowledgments)
9. [Contact Information](#contact-information)
10. [Additional Resources](#additional-resources)

# Introduction

Hardhat is an open-source ethereum development framework used for building, testing and deploying smart contracts and dApps on the Ethereum blockchain.

As the Ethereum ecosystem continues to grow rapidly, developers face the challenge of creating robust and secure smart contracts. Hardhat rises to this challenge by offering a powerful toolkit, enabling developers to write smart contracts in Solidity, the predominant programming language for Ethereum smart contracts. With its extensive capabilities, Hardhat equips developers with the necessary tools to ensure their smart contracts are both efficient and secure.

## Key Features of Hardhat:

1. **Developer-Friendly Tooling:** Hardhat provides a comprehensive suite of developer tools, including a built-in testing environment, debugging features, and an interactive console. 

2. **Automated Testing:**  Hardhat supports various testing frameworks, making it effortless to write and execute tests for smart contracts. Through automated testing, developers can ensure their contracts behave as expected under different scenarios.

3. **Network Management:** Deploying smart contracts to Ethereum networks can be a complex process. Hardhat simplifies network management with an intuitive interface, allowing developers to deploy and interact with contracts on multiple networks with ease.

4. **Extensibility with Plugins:** Hardhat's plugin system enables developers to extend its functionality effortlessly. With a rich ecosystem of community-contributed plugins, developers can easily integrate additional features, custom tasks, or even connect with other blockchain networks.

5. **Security and Auditing Tools:** Hardhat promotes best practices for smart contract security. It integrates with various security tools to analyze contract code, detect vulnerabilities, and reduce the likelihood of exploitable weaknesses.

6. **Ethereum Improvement Proposal (EIP) Support:** Staying up-to-date with the latest Ethereum improvements is essential for developers. Hardhat offers compatibility with EIPs, ensuring smooth transitions when adopting new Ethereum upgrades and changes.

# Installation and Setup

## Prerequisites

Before installing Hardhat, ensure you have the following prerequisites installed on your system:

- **Node.js**: Hardhat requires Node.js version 14 or higher. You can download it from the official Node.js website: [https://nodejs.org](https://nodejs.org).

- **npm**: npm is the Node.js package manager and comes bundled with Node.js. Make sure you have the latest version of npm.

## Step 1: Create a New Project Directory

Start by creating a new directory for your Hardhat project. You can do this using the following command in your terminal:

mkdir my-hardhat-project
cd my-hardhat-project

## Step 2:  Initialize a New npm Project

Inside your project directory, initialize a new npm project by running the following command:

`npm init -y `

## Step 3:  Install hardhat

To install Hardhat, run the following npm command in your project directory:
`npm install --save-dev hardhat`

## Step 4: Project structure 

After the installation, your project directory should have the following structure:
```
my-hardhat-project/
├── node_modules/
├── hardhat.config.js
├── package.json
└── [...other files and directories]

```
## Step 4:  Npx hardhat

on same folder, write following command:
`npx hardhat`
after running that command, it ask us what do we want. we see three options:   
 Create a JavaScript project    
 Create a TypeScript project     
 Create an empty hardhat.config.js

 choose " Create an empty hardhat.config.js" using arrow down key and enter. 
 We also could choose JavaScript or TypeScript project if we want.


## Plugin Installation

To install the plugin for Hardhat, use npm by running the following command in your project directory:

`npm install <plugin-name> `   

In this tutorial, we are going to use @nomicfoundation/hardhat-toolbox that has everthing we need to develop smart contracts.   
Use following command to install this plugin:   
`npm install --save-dev @nomicfoundation/hardhat-toolbox`   

Then, add following code to your hardhat.config.js file.   

`require('@nomicfoundation/hardhat-toolbox'); `


# Tutorials and Topics

## 1. Writing Smart Contracts

With Hardhat installed and configured, you are ready to start writing your first smart contract. Create a new directory named contracts in your project, and inside it, create a file name whatever you want. In this tutorial, I named Token.sol.
In newly created file, paste the code below; 
```
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.0;


contract Token {
    string public name = "Hardhat Token";
    string public symbol = "HT";
    uint256 public totalSupply = 1000000;
    address public owner;

    mapping(address => uint256) balances;

    event Transfer(address indexed _from, address indexed _to, uint256 _value);

 
    constructor() {
        balances[msg.sender] = totalSupply;
        owner = msg.sender;
    }

    function transfer(address to, uint256 amount) external {
        require(balances[msg.sender] >= amount, "Not enough tokens");

        balances[msg.sender] -= amount;
        balances[to] += amount;

        emit Transfer(msg.sender, to, amount);
    }

    function balanceOf(address account) external view returns (uint256) {
        return balances[account];
    }
}
```

##  2. compiling contracts 
To compile your Solidity smart contracts, run the following Hardhat command in your terminal:   
`npx hardhat compile`   
Hardhat will compile your contracts and generate the compiled artifacts in the artifacts/ directory.

## 3. Testing contracts    
Writing automated tests when building smart contracts is of crucial, as your user's money is what's at stake.

we are going to use Hardhat Network which comes built-in with Hardhat, and it's used as the default network. You don't need to setup anything to use it.

In our tests we're going to use ethers.js to interact with the Ethereum contract we built in the previous section, and we'll use Mocha as our test runner.

### Writing Tests
Create a new directory called test inside our project root directory and create a new JavaScript file and you can name anything, but in this tutorial, file name is Token.js. Then paste code below into Token.js file. 
```
const { expect } = require("chai");

describe("Token contract", function () {
  it("Deployment should assign the total supply of tokens to the owner", async function () {
    const [owner] = await ethers.getSigners();

    const hardhatToken = await ethers.deployContract("Token");

    const ownerBalance = await hardhatToken.balanceOf(owner.address);
    expect(await hardhatToken.totalSupply()).to.equal(ownerBalance);
  });
});
```

install solidity extenstion on vs code editor, 

notes:   
1. A Signer  is an object that represents an Ethereum account. It's used to send transactions to contracts and other accounts. Here we're getting a list of the accounts in the node we're connected to, which in this case is Hardhat Network, and we're only keeping the first one.
2. The ethers variable is available in the global scope. Add the following line of code at the top If you like your code always being explicit:
   `const { ethers } = require("hardhat");`

3. Calling ethers.deployContract("Token") will start the deployment of our token contract, and return a Promise that resolves to a Contract. This is the object that has a method for each of your smart contract functions.
   `const hardhatToken = await ethers.deployContract("Token");`
   After contract is deployed, we can call any function on our contract. The variable hardhatToken is promise object which has access to all methods of our contract. 

### Run test 
Run following command to test the contract: 
`npx hardhat test`

## 4. Debugging with hardhat network    
 Hardhat allows you to deploy your contracts, run your tests and debug your code, all within the confines of your local machine. We don't need to set up anything for it as it has already default network setup. 

### console.log in solidity   
We can console message or variables in Solidity code using hardhat/console.sol on top of the contract.   
This is how it looks like using console.log in solidity: 
```
//SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.0;
import "hardhat/console.sol";

contract Token {
    string public name = "Hardhat Token";
    string public symbol = "HT";
    uint256 public totalSupply = 1000000;
    address public owner;

    mapping(address => uint256) balances;

   

    event Transfer(address indexed _from, address indexed _to, uint256 _value);

 
    constructor() {
        balances[msg.sender] = totalSupply;
        owner = msg.sender;
    }

    function transfer(address to, uint256 amount) external {
        require(balances[msg.sender] >= amount, "Not enough tokens");

        console.log("test if console works");
        balances[msg.sender] -= amount;
        balances[to] += amount;

        emit Transfer(msg.sender, to, amount);
    }

    function balanceOf(address account) external view returns (uint256) {
        return balances[account];
    }
}
```

## 5. Deploying to live network   
We can deploy to live networks like Sepolia and Goerli which does not cost money and still do all work we need to learn real world scenario.    
Let's create a new directory called scripts inside the project root's directory, and then create new file name deploy.js and paste the following code into it:   
```
async function main() {
  const [deployer] = await ethers.getSigners();

  console.log("Deploying contracts with the account:", deployer.address);

  const token = await ethers.deployContract("Token");

  console.log("Token address:", await token.getAddress());
}

main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```
We need to use --network parameter when running any task to connect to connect any ethereum network.    Use following syntax:    
`npx hardhat run scripts/deploy.js --network <network-name>`   

Note:   
We can also deploy without specifyig any network if we just want to test if deployment code works. In this case, the deployment get lost when hardhat finishes running. The result will look like:   
```
PS C:\work\web3\metana\my-hardhat-project> npx hardhat run scripts/deploy.js
Deploying contracts with the account: 0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266
Token address: 0x5FbDB2315678afecb367f032d93F642f64180aa3
```
### Deploying to local network   
We can deploy to remote network such as mainnet or any other test networks by adding network entry in our hardhat.config file. For example,   
```
require("@nomicfoundation/hardhat-toolbox");
const INFURA_API_KEY = "KEY";

const SEPOLIA_PRIVATE_KEY = "YOUR SEPOLIA PRIVATE KEY";

module.exports = {
  solidity: "0.8.19",
  networks: {
    sepolia: {
      url: `https://sepolia.infura.io/v3/${INFURA_API_KEY}`,
      accounts: [SEPOLIA_PRIVATE_KEY]
    }
  }
};
```
Notes: 
#### 1. Replace KEY   
Replace with your own which you can get by visiting [Infura](https://infura.io), sign up, create a new API key
#### 2. Import private key from coin base
If you want to export your private key from coinbase Wallet, go to Settings > Developer Settings > Show private key   

#### 2. Import private key from metamask
If you want to export your private key from Meta Wallet,   
-> go to Account details by selecing account   
-> Click on 3 dots which is appeared on right    
-> Click on show private key   
-> Enter password to access private key   

Finally run following code to deploy contract:   
`npx hardhat run scripts/deploy.js --network sepolia`   
After running this command, you will be able to see deployed contract address. 
   
## 6. Useful Commands

- **npx hardhat compile**: Compiles the Solidity smart contracts and generates artifacts.
- **npx hardhat test**: Executes the tests in the `test/` directory using the configured testing framework.
- **npx hardhat node**: Lists the accounts available on the specified network.
- **npx hardhat accounts --network <NETWORK_NAME>**: Runs a custom script located at `<script_path>` within the project.
- **npx hardhat run scripts/myScript.js**: Runs a custom script located at `<script_path>` within the project.




